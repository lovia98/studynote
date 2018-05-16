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

    <br>함수 curryConverter는 x, y, z를 넘기지 않고 y,z만을 넘겨 받고, 다시 x라는 값을 받아 연산하는 함수를 리턴한다.
    <br>하지만 원래 우리가 도달하고자 했던 x*y+z를 한번의 호출로 반환하는 함수는 아니다.
    <br>이와 같이 여러 과정이 끝까지 완료 되지 않은 상태를 가리켜 <code>함수가 부분적으로<sup>partially</sup>적용 되었다</code>라고 말한다.
    <br>이런 방식으로 변환 로직을 재활용할 수 있으며 다양한 변환 요소로 다양한 함수를 만들 수 있다.
  </p>
</div>
