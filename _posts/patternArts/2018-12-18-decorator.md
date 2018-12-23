---
title : 데코레이터 패턴
author: 한주희
layout: post
icon: fa-angle-right
categories: patternArts
permalink: decorator.html
---

### 데코레이터 패턴 
  객체에 추가적인 요건을 <code>동적으로 첨가</code>한다. 데코레이터는 서브클래스를 만드는 것을 통해서  
  기능을 유연하게 확장할 수 있는 방법을 제공한다.
 
  음료 주문의 예
 ![]({{site.baseurl/assets/images/pattern/deco.png}})
 
 위와 같이 커피의 종류가 4개가 있는데, 우유, 휘핑크림, 모카등이 첨가된 커피가 필요한
 경우 그때 마다 새로운 클래스를 만들어 낸다면 클래스 갯수가 너무 많아진다는 문제가 있다.  
 
 ![]({{site.baseurl/assets/images/pattern/deco1.png}})

 
