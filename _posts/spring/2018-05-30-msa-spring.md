---
title: 스프링 클라우드(책 정리)
author: 한주희
layout: post
categories: spring
comments: true
---

이전에 스프링 클라우드 config를 활용한 rest서비스 구축 과정을 정리 하였는데,
이번에는 책에서 읽은 스프링 클라우드에 대한 개념과 필요성에 대해 정리 하고자 한다.

### 스프링 클라우드란?

* 스프링팀에서 만든 일종의 포괄적 상위 프로젝트로, 분산 시스템 개발에 필요한 공통적인 패턴들을 모아 사용하기 쉬운
스프링 라이브러리 형태로 구현해서 제공함.

* 클라우드라는 이름과 달리 클라우드 솔루션이 아니고, 클라우드에 배포될 시스템을 개발할 때 필수 적인 기능들을 제공하는
의미임.

* 스프링 클라우드를 사용하면 클라우드 벤더 서비스에 높은 결합도를 형성하게 되지 않게 해준다.

---
### 스프링 클라우드 config

* Spring Cloud Config 서버는 어플리케이션과 서비스의 도느 환경설정 속성 정보를 저장하고, 조회하고
관리할 수 있게 해주는 외부화된 환경설정 서버다. Spring Config는 환경설정 정보의 버전 관리 기능도 지원한다.

* 그동안의 보통의 환경설정 파라미터는 프로젝트에 함께 패키징되는 properties 파일이나 yml 파일에서 읽어왔다.
이런 경우 서비스의 환경이 변경 될때 마다 어플리케이션 전체를 다시 빌드해야 한다.

* 이는 12 요소 어플리케이션의 원칙 중 하나인 환경설정 외부화 원칙을 위반하게 된다.
<br>[참고:https://12factor.net/ko/](https://12factor.net/ko/){: target="_blank"}

* 환경설정 정보를 배포 패키지에서 분리해서 외부화하는 방법은 JNDI, PropertySource, 어플리케이션 실행시 argument(-D)
이용등이 있지만, 대규모 시스템에서 유연함을 보장하지 못한다.

<br><img src="/studynote/assets/images/spring/sp-clound.jpg">
* 위 그림에서 볼수 있는 것처럼 모든 마이크로서비스가 필요한 환경설정 파라미터를 읽기 위해 중앙에 있는 하나의 서버에 접근한다.
* config서버는 환경설정 정보를 git이나 svn같은 버전관리 도구에 저장한다.
git 저장소는 로컬에 둘 수도 있고 원격에 둘 수도 있다.
<br>
* 스프링 부트와는 다르게 스프링 클라우드는 bootstrap context를 이용한다. 부트스트랩 컨텍스트는 메인 어플리케이션의
부모컨텍스트 역할을 담당하며, config 서버에서 환경설정 정보를 읽어오는 책임을 갖는다.

* BootStrap Context는 초기 환경설정 정보를 bootstrap.yml 혹은 bootstrap.properties파일에서 읽어온다.

* config서버 url 포맷
```
  http://{uri}/{application-name}/{profile}/{label}
```
  ㄴ label은 필수가 아닌 옵션 정보이며, 깃 브랜치 이름이 레이블로 사용된다.(default : master)
