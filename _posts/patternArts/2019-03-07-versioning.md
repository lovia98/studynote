
---
title: 버전에 따른 class 디자인
author: 한주희
layout: post
icon: fa-angle-right
categories: patternArts
permalink: /pattern/versioning.html
---

  어떤 회사 면접시에 받은 질문인데, api 버젼에 따른 메소드내 코드 변경을 어떻게 디자인 하는 것이 좋을까?는 질문을 받은 적이 있다.  

  version에 따른 if, case문들이 생겨 날테니, 전략 패턴이 답이라고 생각이 들어 전략패턴을 써야 한다고 답했는데, 올바른 답인지는 확신이 없다.
  사실 사내 서비스에서 코딩할때 패턴을 고민하는 분들이 많지 않은 편이고, 검색을 해봐도 모르겠고.. 
    
  그나마 궁금한 질문에 가까운 문서를 찾아서 기록해 둔다.  
  
  [http://www.springboottutorial.com/spring-boot-versioning-for-rest-services](http://www.springboottutorial.com/spring-boot-versioning-for-rest-services){:target="blank"}  
  
  내용은 버젼별로 클래스를 만들어 version에 따른 매핑 클래스에서 버젼별 클래스를 주입시킨다는 내용..    
    
  결국 전략 패턴이나 wrapper클래스(결국엔 버젼별 클래스가 생김)를 두는식 말고는 딱히 답이 없는 것 같다. 
