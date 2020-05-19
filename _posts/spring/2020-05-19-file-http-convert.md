---
layout: post
title: "MultipartFile restemplate 전송시 오류"
date: 2020-05-19
description: 로그백 관련 정보 정리. # Add post description (optional)
categories: Spring
img:  # Add image post (optional)
---

MultipartFile을 Spring RestTemplate 으로 전송할 경우 아래와 같은 오류가 발생했다.  

* MultipartFile을 전송한 로직 부분
```
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

* 오류 메시지
```
    Type definition error: [simple type, class java.io.FileDescriptor]; 
    nested exception is com.fasterxml.jackson.databind.exc.InvalidDefinitionException: 
    No serializer found for class java.io.FileDescriptor and no properties discovered to create BeanSerializer (to avoid exception, disable SerializationFeature.FAIL_ON_EMPTY_BEANS) (through reference chain: 
    org.springframework.web.multipart.support.StandardMultipartHttpServletRequest$StandardMultipartFile["inputStream"]->java.io.FileInputStream["fd"])] with root cause
```
 <br><br>
  에러 메시지에서 보듯이, java.io.FileDescriptor 에 대한 직렬화를 할수 없다고 한다.  
  구글링을 해보니 RestTemplate의 **기본 MessageConverters**가 MultipartFile 파일에 포함 된 InputStream을 직렬화하는 방법을 알지 못하기 때문에 예외가 발생하는 것이라고 한다.  
  해결을 위해서는 request 전송시 **MultipartFile 자체 대신 MultipartMap에 MultipartFile의 바이트를 추가** 하여 해결 가능하다.  
  (https://cnpnote.tistory.com/entry/SPRING-restTemplate%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EC%97%AC-Multipart-%ED%8F%BC-%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%A5%BC-%EB%B3%B4%EB%82%B4%EB%8A%94-%EB%B0%A9%EB%B2%95-Spring-mvc)  
    
* 위 링크를 보고 적용 했더니 잘된다. 한가지 알아 둘것이, 파일리스트를 전달하고자 할시 MultipartMap에 리스트 자체를 set해주면 안되고 **add**해줘야 한다.  

* 변경 코드
  ```
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

    /**
     * RestTemplate 기본 messageConverters가 multipartFile에 포함된 InputStream을 직렬화하지 못함.
     * 아래와 같이 map에 multipartFile의 byte를 넘겨야 함.
     *
     * httpClient로 교체 하면 이 이슈는 없어질 수 있음.
     */
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