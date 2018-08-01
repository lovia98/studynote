---
title: 스프링 클라우드 버스
author: 한주희
layout: post
categories: spring
comments: true
---

 스프링 클라우드 환경에서 환경설정 정보(깃등에 저장한 property)를 변경을 하면,
 <br>config server는 바로 반영 하지만 config server를 바라보는 마이스크로 서비스는
 자동으로 갱신된 정보를 가져가지 못한다.
<br><br>
환경설정 정보를 최신화하려면 각 마이크로서비스의 /refresh 엔드포인트를 호출 해야 한다.
<br>마이스크로 서비스가 한 두개가 아닌 경우 일일히 호출해주는 것도 만만한 일이 아닐 것이다.
<br>스프링 클라우드 버스는 이런 문제를 해결해 주기 위한 서비스이다.
<br><img src="/studynote/assets/images/spring/sp-clound-bus.jpg">
