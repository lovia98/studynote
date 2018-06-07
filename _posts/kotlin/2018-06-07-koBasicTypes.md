---
title: Basic Types
author: 한주희
layout: post
icon: fa-angle-right
categories: kotlin
permalink: kotlin-basicTypes.html
---
이 글은 코틀린 공식 홈페이지를 보고 발번역한 내용입니다.
<br/>[Kotlin Refrence 링크](https://kotlinlang.org/docs/reference/basic-types.html){:target="_blank"}


코틀린에서는 모든 변수는 객체이다. 따라서 java의 *Integer*와 같은 wrapper 객체가 존재하지 않는다.
런타임시에 primitive 변수로 처리 되는 몇개의 타입들이 있는데, 다음과 같은 타입들이다. ```numbers, characters, booleans, arrays, strings```
- - -
## Numbers
JAVA와 아주 비슷하게 numbers를 다루는데 다른점은,
* 크기가 큰 타입으로의 암시적인 타입변환이 없다.(명시적으로 타입 변환을 해주어야 한다.)
* 몇몇의 경우에 [literals(변수값)](https://ko.wikipedia.org/wiki/%EB%A6%AC%ED%84%B0%EB%9F%B4){:target="_blank"}이 미세하게 다른점이 있다.


<span class="font14">숫자형 타입들</span>
<br>![typesOfNumbers]({{ site.baseurl }}/assets/images/kotlin/types_of_numbers.jpg)

숫자 리터럴안에 underline을 이용하여 좀더 알아보기 쉽게 표현할 수 있다.
<pre><code class="kotlin">
  val oneMillion = 1_000_000
  val creditCardNumber = 1234_5678_9012_3456L
  val socialSecurityNumber = 999_99_9999L

  println(oneMillion)
  println(creditCardNumber)
  println(socialSecurityNumber)

//********* 출력결과
  1000000
  1234567890123456
  999999999    
</code></pre>


* <bold class="colorBlueLight">명시적 타입 변환</bold> 코틀린에서는 크기가 작은 타입이 크기가 큰 타입으로 변환되지 않는다.
<br><span class="font15">(나와 같은 경우 공식문서의 예와는 다르게 print 결과가 나오기 전에 syntax오류 발생하였다.)</span>
<pre><code>
    val a: Int? = 1
    val b: Long? = a //intellij에서 해보니 이 부분에서 syntax오류가 발생
    print(b == a)

    val b: Byte = 1
    val i: Int = b //Error
</code></pre>
<span class="font15">다음과 같이 크기가 큰 타입을 명시적으로 변환할수 있다.
<pre><code>
  val i: Int = b.toInt()
</code></pre>

모든 number type은 다음과 같은 형변환 처리 함수를 제공한다.
<pre><code>
//Primitives.kt안의 코드에서 발췌
    public override fun toByte(): Byte
    public override fun toChar(): Char
    public override fun toShort(): Short
    public override fun toInt(): Int
    public override fun toLong(): Long
    public override fun toFloat(): Float
    public override fun toDouble(): Double
</code></pre>

 <bold class="font19">비교연산</bold>
- <code>sh1(bits)</code> : java에서의 <code><<</code>
- <code>shr(bits)</code> : java에서의 <code>>></code>
- <code>ushr(bits)</code> : java에서의 <code>>>></code>
- <code>and(bits)</code> : bitwise and
- <code>or(bits)</code> : bitwise or
- <code>xor(bits)</code> : bitwise xor
- <code>inv()</code> : bitwise inversion

- - -
## Characters
문자열들은 <code>Char</code>으로 표현된다.
Char 타입의 변수들은 숫자로 바로 대치 될 수 없다.&nbsp;(위에서 언급한 '명시적인 타입변환'특징의 하나인 것 같다.)
<pre><code>
fun check(c: Char) {
  if (c == 1) { // ERROR: incompatible types
  // ...
  }
}
</code></pre>
Char 변수는 single quotes(')로 표현한다: <code>'1'</code>
<br>특수문자들은 백슬러시를 이용하면 single auotes사용없이 표현 가능하다.
<code>\t</code>, <code>\b</code>, <code>\n</code>, <code>\r</code>, <code>\'</code>
<code>\"t"</code>, <code>\\</code>, <code>$</code>

역시 명시적인 형변화를 통해 문자열을 Int형으로 변환 가능하다.
<pre><code>
val c:Char = 'A'
val n:Int = c.toInt() - '0'.toInt() //Explicit conversions to numbers
</code></pre>

- - -
## Booleans

- - -
## Arrays

- - -
## Strings
