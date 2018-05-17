---
title: 함수형 프로그래밍 기법#1
author: 한주희
layout: post
icon: fa-angle-right
categories: java
permalink: functional2.html
---
<div class="font17 darkGray">
  함수형을 지향하기 위한 프로그래밍 기법을 알아보자.
  <ul>
    <li>일급 시민, 고차원 함수, 커링, 부분 적용</li>
    <li>영속 자료구조</li>
    <li>자바 스트림을 일반화하는 게으른 평가와 게으른 리스트</li>
    <li>패턴 매칭, 자바에서 패턴 매칭을 흉내 내는 방법</li>
    <li>참조 투명성과 캐싱</li>
  </ul>
  <hr>
  <header>
    <h3><i>#일급 시민</i></h3>
    <p>함수를 일급 객체 시민으로(fisrt-class object citizen)</p>
  </header>
  <p>
    이 단어는 크리스토퍼 스트래치(Christopher Strachey)라는 영국 컴퓨터 과학자가 60년대에 만들어 낸 단어로,
    함수를 일반 객체처럼 사용해서 인수로 전달하거나, 결과로 반환받거나, 자료구조에 저장할 수 있음을 의미한다.
  </p>
  <ul>
    <li>변수나 데이터 구조안에 담을 수 있다.</li>
    <li>파라미터로 전달 할 수 있다.</li>
    <li>반환값(return value)으로 사용할 수 있다.</li>
    <li>할당에 사용된 이름과 관계없이 고유한 구별이 가능하다.</li>
    <li>동적으로 프로퍼티 할당이 가능하다.</li>
  </ul>
  <p>
    <code class="highlighter-rouge"><i>고차원 함수(고계함수 higher-order functions)</i></code> &nbsp;:&nbsp;
    이와 같이 함수를 일급 시민으로 사용하여 하나 이상의 동작을 수행하는 함수를 라고 한다.
  </p>
<hr>
  <header>
    <h3><i>#커링(currying)</i></h3>
    <p>함수를 일급 객체로 쓰기 위해 사용되는 커링이라는 기법에 대해 알아보자.</p>
  </header>
  <p><code>Currying</code>은 수학자 하스켈 커리로 부터 유래된 이름으로 함수를 변형하는 과정을 말한다.
    <br>함수의 전달인자 몇개를 미리 채움으로써 더 간단한 함수를 만드는 방법이다.
    <br>참고 링크 : <a href="https://www.linkedin.com/pulse/functional-programming-applying-currying-java-figueras" target="_blank">https://www.linkedin.com/pulse/functional-programming-applying-currying-java-figueras</a>
    <br>예제를 통해 알아보자.
    {%highlight java %}
//아래와 같이 섭씨를 화씨로 바꿔주는 함수가 있다.
public int tempConverter(int x, int y, int z) {
  return x*y+z;
}
//--> 넘겨받는 인자중 x를 리턴해 주는 함수로 교체해본다면 어떨까?
public IntUnaryOperator curryConverter(int y, int z) {
  return (int x)->x*y+z;
}
//수정한 함수 curry를 이용해서 섭씨를 화씨로
//바꾸는 여러가지 기준의 함수를 정의 할수 있다.
IntUnaryOperator convertA = curryConverter(3,5);
IntUnaryOperator convertB = curryConverter(2,6);
int resulta = convertA.applyAsInt(10); //10*3+5
int resultb = convertB.applyAsInt(10); //10*2+6;
    {% endhighlight %}
    <br>함수 curryConverter는 x, y, z를 넘기지 않고 y,z만을 넘겨 받고, 다시 x라는 값을 받아 연산하는 함수를 리턴한다.
    <br>하지만 원래 우리가 도달하고자 했던 x*y+z를 한번의 호출로 반환하는 함수는 아니다.
    <br>이와 같이 여러 과정이 끝까지 완료 되지 않은 상태를 가리켜 <code>함수가 부분적으로<sup>partially</sup>적용 되었다</code>라고 말한다.
    <br>이런 방식으로 변환 로직을 재활용할 수 있으며 다양한 변환 요소로 다양한 함수를 만들 수 있다.
  </p>

<hr>
  <h3><i>#영속 자료구조</i></h3>
  <ul>
    <li>자료의 불변성(Immutable)을 유지 하는 것을 말한다.</li>
    <li>오해하지 말하야 할것은 함수형 언어가 근본적인 문제를 해결해 주지는 않는다.</li>
    <li>다만 코딩하는 사람에게 제약을 조금 더 주어 변경을 난발하게 못하게 할 뿐이다.</li>
    <li>클로저에서 함수는 변경 불가능한 자료를 다루기 때문에 함수의 결과가 새로운 값을 리턴한다.</li>
    <li>내부적으로는 공유 데이터를 사용하기 때문에 중복 데이터에 대한 성능 문제는 발생하지 않는다.</li>
    참고 링크 : <a href="https://github.com/eunmin/clojure-study/wiki/%5B%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%81%B4%EB%A1%9C%EC%A0%80%5D-5%EC%9E%A5-%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D" target="_blank">[프로그래밍 클로저] 5장 함수형 프로그래밍</a>
  </ul>
  예) 다음과 같은 linkedList가 있다.
{%highlight java %}
class TrainJourney {
  public int price;
  public TraiinJourney onward;
  public TrainJourney(int p, TrainJourney t) {
    price = p;
    onward = t;
  }
}
{% endhighlight %}
      인자의 갱신이 일어나는 함수
      {%highlight java %}
public TrainJourney link(TrainJourney a, TrainJourney b) {
    if (a == null) {
        return b;
    }
    TrainJourney t = a;
    while (t.onward != null) {
      //반복적으로 인자 a의 값의 변형이 발생한다.
        t = t.onward;
    }
    t.onward = b;
    return a;
}      
      {% endhighlight %}

			함수형 프로그래밍
      {%highlight java %}
public TrainJourney append(TrainJourney a, TrainJourney b) {
    //기존의 자료구조를 갱신하지 않도록 중단 단계의 계산결과를 새로운 자료구조로 반환
    return a == null ? b : new TrainJourney(a.price, append(a.onward, b));
}     
      {% endhighlight %}
</div>
