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
 ![deco]({{site.baseurl}}/assets/images/pattern/deco.png){width="400"}
 
 위와 같이 커피의 종류가 4개가 있는데, 우유, 휘핑크림, 모카등이 첨가된 커피가 필요한
 경우 그때 마다 새로운 클래스를 만들어 낸다면 클래스 갯수가 너무 많아진다는 문제가 있다.  
 
 ![deco]({{site.baseurl}}/assets/images/pattern/deco1.png){width="400"}

 첨가물들을 추상클래스인 Beverage에 추가하는 것을 생각해 볼 수 있다.
~~~
  public class abstract Beverage {
    String description;
    Milk milk;
    Soy soy;
    Mocha mocha;
    Whip whip;
    
    ....
  }
~~~
 이 방법은 아래와 같은 문제가 있다.
 * 첨가물의 가격이 바뀌거나 추가가 될때마다 Beverage의 코드를 수정해야 한다.  
   -> 추상클래스인 Beverage가 변화에 열려 있어 상속받는 클래스에 영향도가 커진다.
 * 이는 OCP(open-close principle) 원칙에 어긋나게 된다.
 
 > OCP
   클래스는 확장에 대해서는 열려 있어야 하지만 코드 변경에 대해서는 닫혀 있어야 한다.
   
데코레이터 패턴은 기본 객체에 추가 장식을 한다는 개념으로 아래와 같이 문제를 해결한다.

**모카커피 주문**
  1. 커피 객체를 가져온다.
  2. 모카 객체로 장식한다.
  3. 휘핑 객체로 장식한다.

위에서 언급한 "장식"은 기본 객체인 커피 객체를 <code>확장</code>을 통해 가능하다.  
아래 클래스 다이어그램을 보면서 이해해 보자.  
 ![deco]({{site.baseurl}}/assets/images/pattern/deco2.png){width="400"}

 
