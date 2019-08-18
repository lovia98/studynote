---
  layout: post
  date: 2019-08-19
  title : 스프링 시큐리티 구조
  categories: Spring
---

시큐리티를 제대로 이해해 보아요~

## 인증처리 편 - SecurityContextHolder와 Authentication
* 참고자료 :
  * [인프런 강의](https://www.inflearn.com/course/백기선-스프링-시큐리티){:target="_blank"}
  * [https://springbootdev.com](https://springbootdev.com/2017/08/23/spring-security-authentication-architecture/){:target="_blank"}  
  * [docs.spring.io](https://docs.spring.io/spring-security/site/docs/current/reference/html/overall-architecture.html#securitycontextholder-securitycontext-and-authentication-objects){:target="_blank"}  
<br>
스프링 시큐리티 인증처리에 대한 전체적인 처리 흐름은 다음과 같다.
<img src="{{site.baseurl}}/assets/img/spring/blogpost-spring-security-architecture.png" height="400px;"/>  
<br><br>

그중 오늘 살펴볼 부분은 바로 이 부분  
<img src="{{site.baseurl}}/assets/img/spring/security-contextHolder.png"/>  

* **SecurityContextHolder**  
  * 사용자의 인증 처리(로그인 성공) 후 현재의 보안 세부 정보를 저장하는 가장 기본적인 개체  
  * ThreadLocal을 사용하여 이러한 세부 사항을 저장한다.
  * 이를 이용하여 한번 로그인에 성공하면 인증 객체를 securitycontext에 저장해 두고  
    매번 인증 처리 할 필요 없이 동일한 실행 스레드에서 사용자의 인증 처리 결과를 가져올 수 있다.
