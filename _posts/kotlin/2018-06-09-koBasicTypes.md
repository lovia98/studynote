---
title: 기본 타입
layout: post
date: 2018-06-09
categories: kotlin
---

이 글은 코틀린 공식 홈페이지를 보고 발번역한 내용입니다.
<br/>[Kotlin Refrence 링크](https://kotlinlang.org/docs/reference/basic-types.html){:target="_blank"}


코틀린에서는 모든 변수는 객체이다. 따라서 java의 *Integer*와 같은 wrapper 객체가 존재하지 않는다.
런타임시에 primitive 변수로 처리 되는 몇개의 타입들이 있는데, 다음과 같은 타입들이다. ```numbers, characters, booleans, arrays, strings```
- - -
## Numbers
JAVA와 아주 비슷하게 numbers를 다루는데 다른점은,
* 크기가 큰 타입으로의 암시적인 타입변환이 없다.(명시적으로 타입 변환을 해주어야 한다.)
* 몇몇의 경우에 [literals(변수값)](https://ko.wikipedia.org/wiki/%EB%A6%AC%ED%84%B0%EB%9F%B4){:target="_blank"}할당시 약간 다른점이 있다.


<span class="font14">숫자형 타입들</span>
<br>![typesOfNumbers]({{ site.baseurl }}/assets/img/kotlin/types_of_numbers.jpg)

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


* <bold class="colorBlueLight">명시적 타입 변환</bold> 코틀린에서는 크기가 작은 타입이 크기가 큰 타입으로 자동(암묵적인)변환되지 않는다.
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
    pub      
     override fun toFloat(): Float
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

<bold class="font19">소수점숫자들의 비교</bold>
- equality Check : <code>a == b</code>, <code>a != b</code>
- 비교연산자들 : <code>a < b</code>, <code>a > b</code>, <code>a <= b</code>, <code>a >= b</code>
- <code>범위설정 표현과 범위 체크</code> : <code>a..b</code>, <code>x in a..b</code>, <code>x !in a..b</code>

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
<pre><code class="kotlin">
val c:Char = 'A'
val n:Int = c.toInt() - '0'.toInt() //Explicit conversions to numbers
</code></pre>

- - -
## Booleans
true나 false 둘 중 하나의 값을 가지는 타입, java와 별반 다를게 없다.
- - -
## Arrays
* Arrays class로 존재한다.&nbsp;&nbsp;get, set 함수, size 프로퍼티를 가지고 있습니다.
([refer링크](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-array/index.html){:target="_blank"})
* 배열을 생성하는 방법은 아래와 같습니다.
<br>1) 표준 라이브러리 함수를 사용 - arrayOf(), arrayOfNulls(), emptyArray()
<br>2) Array Contructor 사용 - controctor는 배열size와 초기화할 element를 만드는 람다식을 파라미터로 받는 형식.
{% highlight kotlin%}
val t:Array<Int> = arrayOf(1,2,3) // [1,2,3]
val s: Array<Int?> = arrayOfNulls(3) //size: 3, null로 이루어진 array 생성

//빈 Array선언
val a: Array<String> = emptyArray();

//size : 5, (i*i)값으로 이루어진 Array<String>생성 ["0", "1", "4", "9", "16"]
val asc = Array(5, {i->(i*i).toString()})

//Any타입으로 선언하면 여러 타입의 element을 가질수 있네요.
val c:Array<Any> = arrayOf("1",2,3.3)
{% endhighlight %}

* 코틀린은 자바와 같은 자동 boxing 처리 없이 각 원시타입을 위한 특화된 클래스가 존재합니다.
<br><code>ByteArray</code>, <code>ShortArray</code>, <code>IntArray</code> 등등..
<br>일반 배열과 마찬가지로, 원시타입을 위한 배열을 생성하는 함수 또한 표준 라이브러리에 포함 되어 있습니다.
{% highlight kotlin%}
val intArr : IntArray = IntArray(1,2,3,4,5)
{% endhighlight %}

* 한가지 알아둘 점은 자바와 달리 코틀린에서 배열은 타입의 변환을 허용하지 않는다는 점입니다.(Arrays are invariant)
즉 <code>Array< String ></code> 타입의 변수를 <code>Array< Any ></code>타입으로 배당할수 없습니다.
&nbsp;예를 들어 java에서는 다음과 같은 코드가 허용 되지만,
{% highlight kotlin%}
Number[] numbers = new Number[1];
Object[] objects = numbers;
objects[0] = "hello";
{% endhighlight %}
다음과 같이 코틀린에서는 <code>Array< Int > ->Array< Any ></code>로의 할당이 불가능합니다.
{% highlight kotlin%}
fun copy(from: Array<Any>, to: Array<Any>) {
    assert(from.size == to.size)
    for(i in from.indices)
        to[i] = from[i]
}

val ints: Array<Int> = arrayOf(1,2,3)
    val any = Array<Any>(3){ "" }
    copy(ints, any) //오류 발생
{% endhighlight %}

- - -
## Strings
java와 같이 코틀린에서도 String은 불변객체입니다.(immutable)
string의 각 요소는 characters로 이루어져 있으며 indexing operation을 통해 접근 가능합니다. : <code>s[i]</code>
<pre><code>
for(c in str) {
  println(c)
}
</code></pre>
코틀린에서 triple quote(` """ `)를 사용하면 개행문자(` \n `)를 쓰지 않고도 줄바꿈이나 공백등 어떤 문자든 자유롭게 표현 할 수 있습니다.
<pre><code>
var text = """
  for (c in "foo")
    print(c)
"""
</code></pre>
또한 각줄의 시작을 구분하는 문자(default `|`)로 채운 후 `trimMargin()`을 이용하여 앞의 공백의 제거 할 수 있습니다.
<pre><code>
val text = """
    |Tell me and I forget.
    |Teach me and I remember.
    |Involve me and I learn.
    |(Benjamin Franklin)
    """.trimMargin()
</code></pre>
공백 구분값으로 사용되는 문자 `|`는 default입니다. 다른 문자를 사용할 경우 함수에 해당 구분문자를 파라미터로 넘겨주어 사용 가능합니다.
`trimmargin(">")`

*문자열 템플릿*
문자열 템플릿을 이용하여 문자내 일부 단어를 다른 값으로 치환할 수 있습니다.
<pre><code>
val i = 10
println("i = $i") //prints "i = 10"

val s = "abc"
println("$s.length is ${s.length}") //prints "abc.length is 3"
</code></pre>


{% if page.comments %}
<div id="disqus_thread"></div>
<script>

/**
*  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
*  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
/*
var disqus_config = function () {
this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};
*/
(function() { // DON'T EDIT BELOW THIS LINE
var d = document, s = d.createElement('script');
s.src = 'https://juhee-studynote.disqus.com/embed.js';
s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
{% endif %}
