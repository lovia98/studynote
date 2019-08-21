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

그 중 오늘 살펴볼 부분은 바로 이 부분  
<img src="{{site.baseurl}}/assets/img/spring/security-contextHolder.png"/>  

* **SecurityContextHolder**  
  * 사용자의 인증 처리(로그인 성공) 후 현재의 보안 세부 정보를 저장하는 가장 기본적인 개체  
  * ThreadLocal을 사용하여 이러한 세부 사항을 저장한다.
  * 이를 이용하여 한번 로그인에 성공하면 인증 객체를 securitycontext에 저장해 두고  
    매번 인증 처리 할 필요 없이 동일한 실행 스레드에서 사용자의 인증 처리 결과를 가져올 수 있다.  
<br>
* **SecurityContext**
  * 현재 application을 사용하고 있는 보안 주체(사용자에 대한 인증 정보)를 담고 있음.  
<br>
* **Authentication**
  * 스프링 시큐리티는 Authentication객체에 보안 주체(사용자)의 세부 정보를 저장한다.
  * Authentication에는 Principal과 GrantAuthority 두가지 객체가 있다.  
<br>
* **Principal**
  * 사용자 인증 정보(아이디, 이름, 권한등)를 담는 객체
  * Principal을 가져오는 역할은 직접 만들어도 되지만, 스프링 시큐리티에서는 UserDetailsService  
    인터페이스를 제공한다.
  * UserDetailsService 를 구현하여 스프링시큐리티 설정시 등록해주면 유져 정보를 가져오는 부분에
    사용된다.  
    <br>
    **UserDetailsService interface**
    ~~~
    public interface UserDetailsService {
      UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;
      }
    ~~~  
    <br>
  * UserDetailsService 는 사용자 정보(인증정보)를 리턴하는 하나의 메소드를 가지고 있으며,  
    리턴 객체는 UserDetails 타입이다.
  * 결론적으로 Principal로 쓰이는 객체는 UserDetails 타입으로 우리가 application내에  
    인증정보를 담는 객체를 만들때는 UserDetails 인터페이스를 구현한 객체여야 한다는 것..

<br>
* **GrantAuthority**
  * "ROLE_USER", "ROLE_ADMIN"과 같은 Principal이 가지고 있는 "권한"정보들
  * 인증 이후, 인가 및 권한 확인 할때 이 정보를 참조한다.

<i class="fas fa-box"></i>정리   
