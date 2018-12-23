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
  <img src="{{site.baseurl}}/assets/images/pattern/deco.png" width="400">
 
 위와 같이 커피의 종류가 4개가 있는데, 우유, 휘핑크림, 모카등이 첨가된 커피가 필요한
 경우 그때 마다 새로운 클래스를 만들어 낸다면 클래스 갯수가 너무 많아진다는 문제가 있다.  
 
 <img src="{{site.baseurl}}/assets/images/pattern/deco1.png" width="400">

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
커피에 모카를 장식하는 과정을 그려보자.
  1. 커피 객체를 가져온다.
  2. 모카 객체로 장식한다.
  3. 휘핑 객체로 장식한다.

그렇다면, 장식을 어떻게 디자인해야 할까? (변화에 닫혀있으면서..)  
이는 기본 객체인 <code>확장</code>을 통해 가능하다.  
아래 클래스 다이어그램을 보면서 이해해 보자.  
 <img src="{{site.baseurl}}/assets/images/pattern/deco2.png" width="400">

* Component : 장식이 추가될 될 기본 객체  
* ConcreteComponent : 인터페이스를 구현하고 더 많은 기능(장식)을 추가하려는 속성을 가진 실제 클래스.  
* Decorator : ConcreateComponent와 추가될 장식 사이에 접착제 역할을하는 인터페이스.  
* Concrete Decorator : 그 동작을 추가하는 실제 데코레이터.  

**"커피 주문 음료"**에 데코레이터 패턴을 적용하면 다음과 같다.  
1. **음료** (Component)객체와 **첨가물** (Decorator)객체 생성  
~~~
  public abstract class Beverage {
    String description = "no subject";

    public String getDescription() {
        return description;
    }

    public abstract double cost();
  }

  public abstract class CondimentDecorator extends Beverage {
    public abstract String getDescription();
  }
~~~
2. 실제로 장식이 추가될 대상자인 **커피음료**(ConcreteComponent) 선언
~~~
  //에스프레소
  public class Espresso extends Beverage {

      public Espresso() {
          description = "Espresso";
      }

      @Override
      public double cost() {
          return 1.99;
      }
  }
  //하우스블렌드
  public class HouseBlend extends Beverage {

      public HouseBlend() {
          description = "House Blend Coffee";
      }

      @Override
      public double cost() {
          return .99;
      }
  }
~~~
3. **첨가물**(ConcreteDecorator)클래스 선언
~~~
  public class Mocha extends CondimentDecorator {

      Beverage beverage;

      public Mocha(Beverage beverage) {
          this.beverage = beverage;
      }

      @Override
      public String getDescription() {
          return beverage.getDescription() + " + Mocha";
      }

      @Override
      public double cost() {
          return .20 + beverage.cost();
      }
  }

  public class SteamMilk extends CondimentDecorator {

      Beverage beverage;

      public SteamMilk(Beverage beverage) {
          this.beverage = beverage;
      }

      @Override
      public double cost() {
          return .10 + beverage.cost();
      }

      @Override
      public String getDescription() {
          return beverage.getDescription() + " + steam milk";
      }
  }
~~~
4. 테스트
~~~
  //에스프레소
  Beverage espresso = new Espresso();
  espresso = new SteamMilk(espresso); //우유를 첨가하여 까페라떼가 되었음.

  System.out.println(espresso.getDescription() + " $" + espresso.cost());

  //다크로스트
  Beverage darkRoast = new DarkRoast();
  darkRoast = new Mocha(darkRoast);
  darkRoast = new Mocha(darkRoast);
  Beverage darkRoastMochaWhip = new WhipingCream(darkRoast);

  //모카를 두번, 휘핑크림을 얻은 darkRoast
  System.out.println(darkRoast.getDescription() + " $" + darkRoast.cost());
~~~
  
  * 실행결과 : 각 첨가물의 가격이 본래 가격에 추가되었다.
  ~~~
   Espresso + steam milk $2.09
   dark roast + Mocha + Mocha $1.39
  ~~~
  
***결론***
  * 데코레이터 패턴을 적용하면 객체에 추가 요소가 발생하여도 코드를 변경하지 않고 추가적인 요소(장식)을  
  동적으로 더할 수 있다.  
  * 데코레이터 패턴을 적용하면 서브 클래스를 만드는 경우에 비해 훨씬 유연하게 기능을 확장할 수 있다.(OCP)
  
