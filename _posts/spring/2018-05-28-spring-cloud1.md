---
title: 스프링 클라우드
author: 한주희
layout: post
categories: spring
comments: true
---

Spring Cloud 홈페이지에 따르면, MSA와 같은 분산 시스템의 규모가 점차 커지면서 중간에 조정할
수 있는 별도의 서비스가 필요하고, 이 서비스를 쉽게 구축 할 수 있는 프레임웍이라고 소개 되고 있다.
(참고 링크 : <a href="http://projects.spring.io/spring-cloud/" target="_blank">http://projects.spring.io/spring-cloud/</a>)

일단 내가 이해 된 Spring Cloud의 기능 중 하나는 Application에서 필요한 설정 정보(db접속 정보, api도메인 정보등..)를
담은 properties를 배포 없이 수정 가능하다는 것이다.

다른 블로거님이 올려 놓은 정보를 토대로 내가 만든 예제를 정리해본다.
<span class="font14">
<br>( 참고 링크 : <a href="http://blog.leekyoungil.com/?p=352">http://blog.leekyoungil.com/?p=352</a>
<br><a href="http://jeroenbellen.com/manage-and-reload-spring-application-properties-on-the-fly">
http://jeroenbellen.com/manage-and-reload-spring-application-properties-on-the-fly</a>
<br><a href="https://projects.spring.io/spring-cloud/spring-cloud.html#customizing-bootstrap-properties">https://projects.spring.io/spring-cloud/spring-cloud.html#customizing-bootstrap-properties</a></span> )

<hr>
### 공통 config properties 만들기 (gitHub에 생성)
<br><span class="font14">* 샘플 코드 : <a href="https://github.com/lovia98/spring-cloud-config" target="_blank">https://github.com/lovia98/spring-cloud-config</a></span>
<br>&nbsp;&nbsp;2개의 product, payment서비스를 위한 2개의 설정이 필요하다고 가정하고 아래 2개의 파일을
만든다.
<br><span class="font14"><bold>payment-service.yml</bold></span>
<br><img src="/studynote/assets/images/spring/spring_cloud_1.jpg">
<br><span class="font14"><bold>product-service.yml</bold> (profile별로 나누어진 상태)</span>
<br><img src="/studynote/assets/images/spring/spring_cloud_2.jpg">


<hr>
### Config Server 설정
 1. Cloud Config Server 만들기 (Intellj, maven기준)
<br><span class="font14">참고 링크 : <a href="https://github.com/lovia98/spring-cloud-example" target="_blank">https://github.com/lovia98/spring-cloud-example</a></span>
  * Spring boot project 생성
  <br><img src="/studynote/assets/images/spring/spring_cloud_3.jpg">
  * Dependencies 에서 <bold>Cloud Config > config Server<bold>선택
  <br><img src="/studynote/assets/images/spring/spring_cloud_4.jpg">
  * Application.java에 @EnableConfigServer추가
  <br><img src="/studynote/assets/images/spring/spring_cloud_5.jpg">
  * application.yml 설정
  <br>Spring Cloud guide에서는 bootstrap.yml을 생성하라고 가이드 되어 있는데, bootstrap.yml파일은
  spring cloud application에서 application.yml파일보다 먼저 실행이 된다고 한다.
  <br><span class="font14">spring.cloud.config.server.git.uri 설정</span>
  <br><img src="/studynote/assets/images/spring/spring_cloud_6.jpg">
  <br>구글링을 해보니 config설정 properties는 git이 아니여도 application내부의 class path, local file, http uri
  모두 설정이 가능 한것으로 보인다.&nbsp;&nbsp;상황(회사 방침)에 따라 맞추어 설정하면 될 것 같다.

 2. config server 확인
  * uri 패턴 : {config-server-address}/{service-name}/{profiles}
  <br>github의 payment-service.yml은 profile이 나뉘어져 있지 않으므로, {profiles}부분을 default로 하여 호출 한다.
  <br><img src="/studynote/assets/images/spring/spring_cloud_7.jpg">
  * product-service의 config 정보 (profile을 구분하여서 dev, stg로 호출이 가능하다.)
  <br><img src="/studynote/assets/images/spring/spring_cloud_8.jpg">
  <br><img src="/studynote/assets/images/spring/spring_cloud_9.jpg">

<hr>
### Client Server 설정
1. Config Server를 참조할 client인 일반적인 Rest Api application을 생성한다.
* pom.xml에 spring-cloud-config-client, spring-boot-starter-actuator 를 추가한다.
<br><img src="/studynote/assets/images/spring/spring_cloud_10.jpg">
<br>spring-cloud-config-client는 config server로 부터 config정보를 얻어 오기 위해 필요하고,
actuator는 배포없이 config설정 변경이 적용되기 위해 필요하다. &nbsp;&nbsp;자세한 것은 뒤에 config 수정 테스트를 통해
알게 되니 우선 config정보를 가져오는 부분을 따라가 보자~!
* bootstrap.yml(or application.yml) 생성 및 설정
<br><img src="/studynote/assets/images/spring/spring_cloud_11.jpg">
<br>1) spring.application.name 을 설정하면 해당 서비스명의 config정보가 적용된다.
<br>2) spring.management.security.enable = false 액츄에이터 보안 처리 false로 설정
<br>3) spring.endpoints.acurator.enabled = true 액츄에이터 endpoints 사용

* Rest Controller 생성
<br>&nbsp;1) @Value를 통한 config 정보 가져오기
<br><img src="/studynote/assets/images/spring/spring_cloud_12.jpg">
<br>&nbsp;2)@ConfigurationProperties를 통해 config정보를 객체에 담기
<br><img src="/studynote/assets/images/spring/spring_cloud_14.jpg">
<br><img src="/studynote/assets/images/spring/spring_cloud_15.jpg">

* Client Application 실행시 -Dspring.profiles.active=dev로 vm options 추가.
<br>application의 profile을 dev 로 설정 했으니 dev환경의 yml값을 가져오게 된다.
<br><img src="/studynote/assets/images/spring/spring_cloud_17.jpg" width="700">

* @Value와 Property객체 설정을 통한 config값 모두 정상적으로 노출 되는 것을 확인 할 수 있다.
<div class="row">
  <div class="4u 12u$(small)">
    <span class="font14">- @Value를 통한 config값 테스트</span>
    <br><img src="/studynote/assets/images/spring/spring_cloud_13.jpg">
  </div>
  <div class="6u 12u$(small)">
    <span class="font14">- 객체를 통한 config값 테스트</span>
    <br><img src="/studynote/assets/images/spring/spring_cloud_16.jpg">
  </div>
</div>

<hr>
### yml(or property) 파일 변경시 자동 반영
<br>여기까지 gitHub에 push되어 있는 yml파일을 제공해주는 cloud config server 을 만들고 client server에서 읽어 오는 부분을
확인 하였다. 그럼 궁극적으로 확인해 보려고 하는 배포 없는 config 파일 수정을 테스트 해보자.
<br><span class="font14">( host.domain값을 <bold>item-dev-api1</bold>.myhost.com에서 <bold>item-dev-api</bold>.myhost.com로 변경 )</span>
<br><img src="/studynote/assets/images/spring/spring_cloud_18.jpg">

* <span class="colorBlueLight">기대하는 것과 다르게 client Server에서는 변경이 반영되지 않는데,
<br>이는 @RefeshScope를 적용한 영역에서만 build없이 적용되기 때문이다.</span>

* 확인을 위해 새로운 @RefreshScope를 적용한 Controller를 생성해보자.
<br><img src="/studynote/assets/images/spring/spring_cloud_19.jpg">

* <span class="colorBlueLight">@RefreshScope가 적용된 controller를 호출해 보지만, 여전히 자동 반영되지 않다. ㅡ.ㅡ;;
<br><img src="/studynote/assets/images/spring/spring_cloud_20.jpg">
</span>
<br>여기서 client api server를 생성할때 왜 actuator dependency를 추가해준 이유가 나오는데,
boot application을 배포하지 않은 상태에서 build를 하기 위해서는 'refresh'라는 endpoint를 통해서만 가능 하기 때문이다.

* 아래와 같이 <i>{client server address}/refresh</i> uri를 POST로 호출하면,
<br><img src="/studynote/assets/images/spring/spring_cloud_21.jpg">
<br>yml 파일의 변경된 값이 host.domain이라는 것을 응답으로 알 수 있다.

* @RefreshScope가 적용된 controller를 호출 하면 값이 변경된 것을 확인 할수 있다.
하지만 @RefreshScope가 적용되지 않은 controller는 반영이 되지 않는다.
<br><img src="/studynote/assets/images/spring/spring_cloud_22.jpg">
<br><img src="/studynote/assets/images/spring/spring_cloud_20.jpg">


Spring Cloud는 배포없는 변경을 지원해 주기도 하지만 다른 기능도 여러개 있는 것으로 보이는데, 좀 더 공부가 필요할 것 같다.

{% if page.comments %}
<div id="disqus_thread"></div>
<script>

/**
*  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
*  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
/*
var disqus_config = function () {
this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};
*/
(function() { // DON'T EDIT BELOW THIS LINE
var d = document, s = d.createElement('script');
s.src = 'https://juhee-studynote.disqus.com/embed.js';
s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
{% endif %}
