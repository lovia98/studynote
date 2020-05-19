---
layout: post
title: "MultipartFile Restemplate 전송시 오류"
date: 2020-05-19
description: 로그백 관련 정보 정리. # Add post description (optional)
categories: Spring
img:  # Add image post (optional)
---

### 파일을 api로 전송하는 로직을 구현 중에 발생한 오류에 대한 기록
* 구현중인 로직은 MultipartFile 리스트를 Spring RestTemplate을 이용하여 전송하는 로직이었다.

* 코드는 다음과 같다.  

``` java  
  private ResUploadFileVO postMultiPartRequest(String url, ReqFileUploadFileVO param) throws Exception {

    HttpHeaders httpHeaders = new HttpHeaders();
    httpHeaders.set("Content-Type", MediaType.MULTIPART_FORM_DATA_VALUE);

    //param, header set
    MultiValueMap<String, Object> body = getMultiPartParam(param);
    ResponseEntity<String> result
            = apiRestTemplate.exchange(url, HttpMethod.POST, new HttpEntity<>(body, httpHeaders), String.class);

    return this.responseResultProcess(result, ResUploadFileVO.class);
}

private MultiValueMap<String, Object> getMultiPartParam(ReqFileUploadFileVO param) throws IOException {

    MultiValueMap<String, Object> body = new LinkedMultiValueMap<>();
    body.add("svcCd", param.getSvcCd());
    body.add("savePath", param.getSavePath());
    body.add("upFile", param.getUpFile());
    body.add("upFiles", param.getUpFiles());
    return body;
}
    
```
<br>
* 오류 메시지
``` text  
    Type definition error: [simple type, class java.io.FileDescriptor]; 
    nested exception is com.fasterxml.jackson.databind.exc.InvalidDefinitionException: 
    No serializer found for class java.io.FileDescriptor and no properties discovered to create BeanSerializer (to avoid exception, disable SerializationFeature.FAIL_ON_EMPTY_BEANS) (through reference chain: 
    org.springframework.web.multipart.support.StandardMultipartHttpServletRequest$StandardMultipartFile["inputStream"]->java.io.FileInputStream["fd"])] with root cause
```
 <br><br>
  에러 메시지를 보니, multipartFile 형태의 필드를 convert하지 못하는 것으로 보이는데, 통 감이 잡히지 않았다.  
  <br>구글링을 해보니 RestTemplate의 **기본 MessageConverters**가 MultipartFile 파일에 포함 된 InputStream을 직렬화하는 방법을 알지 못하기 때문에 예외가 발생하는 것이라고 한다.  
  해결을 위해서는 request 전송시 **MultipartFile 자체 대신 MultipartMap에 MultipartFile의 바이트를 추가** 하여 해결 가능하다.  
* [참고링크](https://cnpnote.tistory.com/entry/SPRING-restTemplate%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EC%97%AC-Multipart-%ED%8F%BC-%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%A5%BC-%EB%B3%B4%EB%82%B4%EB%8A%94-%EB%B0%A9%EB%B2%95-Spring-mvc)  
    
* 위 링크를 보고 적용 했더니 잘된다. 한가지 알아 둘것이, 파일리스트를 전달하고자 할시 MultipartMap에 리스트 자체를 set해주면 안되고 **add**해줘야 한다.  

* 변경 코드
  ``` java  
    private MultiValueMap<String, Object> getMultiPartParam(ReqFileUploadFileVO param) throws IOException {
        MultiValueMap<String, Object> body = new LinkedMultiValueMap<>();
        body.add("svcCd", param.getSvcCd() == null ? this.svcCode : param.getSvcCd());
        body.add("savePath", param.getSavePath());

        if (param.getUpFile() != null) {
            body.add("upFile",
                    new MultipartInputStreamFileResource(param.getUpFile().getInputStream(), param.getUpFile().getOriginalFilename()));
        }

        if (param.getUpFiles() != null) {
            for (MultipartFile file : param.getUpFiles()) {
                body.add("upFiles", new MultipartInputStreamFileResource(file.getInputStream(), file.getOriginalFilename()));
            }
        }

        return body;
    }

    class MultipartInputStreamFileResource extends InputStreamResource {

        private final String filename;

        MultipartInputStreamFileResource(InputStream inputStream, String filename) {
            super(inputStream);
            this.filename = filename;
        }

        @Override
        public String getFilename() {
            return this.filename;
        }

        @Override
        public long contentLength() throws IOException {
            return -1; // we do not want to generally read the whole stream into memory ...
        }
    }
  ```