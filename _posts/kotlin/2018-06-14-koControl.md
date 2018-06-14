---
title: 조건절 if, when, for, while
author: 한주희
layout: post
icon: fa-angle-right
categories: kotlin
permalink: kotlin-control.html
comments: true
---

### If Expression
코틀린에서 if 조건절을 value를 리턴할수 있다.
<br>따라서 다음과 같은 삼항 조건 연산자가 존재하지 않는다. 예) (x=1)? "true" : "false"
<pre><code>
  //전통적인 방식
  var max: Int
  if (a<b) {
    max = a
  }else {
    max = b
  }

  //코틀린에선 다음과 같이 바로 if에서
  //return value 값을 변수에 할당해 줄수 있다.
  val max = if(a > b) a else b

  //if 블럭안에 다른 코드가 있는 경우 마지막 구문이 반환 값이 된다.
  val max = if(a > b) {
    print("Choose a")
    a
  } else {
    print("Choose b")
    b
  }
</code></pre>

 위와 같이 if결과값을 변수에 할당해야 하는 경우(if절로 부터 리턴받아야 할 값이 있는 경우)라면 반드시 else절이
 존재 해야 한다.
 <pre><code>
 //오류 발생 없음
  if(a > b) {
    println("It's Ok")
    a
  }

 //else가 없어서 syntax 오류 발생
 val max = if(a > b) {
   println("It's Ok")
   a
 }
 </code></pre>

---
### When Expression
 * C계열 언어의 switch문과 같음.
 * when문은 각각의 branches의 조건문이 만족할 때 까지 위에서 부터 순차적으로 인자를 비교함.
<pre><code>
when(x) {
    1 -> print("x == 1")
    2 -> print("x == 2")
    else -> {
        print("x is neither 1 nor 2")
    }
}
</code></pre>
 * if절과 마찬가지로 반환값을 넘길 수 있다.
 <pre><code>
 fun whenTest5(x : Int) {
    val result:String = when(x) {
         3 -> "3"
         4 -> "4"
        else -> "else"
    }
}
 </code></pre>
 * `is` or `!` 연산자를 이용하여 객체 타입 체크
<pre><code>
fun hasPrefix(x: Any) = when(x) {
  is String -> x.startsWith("prefix")
  else -> false
}
</code></pre>
 * 인자 없이 branch에 조건절을 지정하여 true인 branch 실행
<pre><code>
 when {
     x.startsWith("0") -> print("start 0")
     x.startsWith("1") -> print("start 1")
     else -> print("x is funny")
 }
</code></pre>
 * 그 밖의 다양한 사용법
<pre><code>
  when(x) {
    //x가 0,1 중 하나인지 체크 가능
      0, 1 -> print("x == 0 or x == 1")
      else -> print("otherwise")
  }

  when(x) {
    //임의의 특정값을 branch 조건식에 넣을 수 있다.
    parseInt(s) -> print("s encodes x")
    else -> print("s does not encode x")
  }

 //범위 체크
  val validNumbers = 50..100
  when(x) {
    in 1..10 -> print("x is in the rage")
    in validNumbers -> print("x is valid")
    !in 10..20 -> print("x is outside the range")
    else -> print("none of the above")
  }
</code></pre>

---
### For Loops
* for문은 iterator를 제공하는 모든 것을 반복 할수 있다.
<br>( - has a member- or extension-function iterator(), whose return type
<br> - has a member- or extension-function next(), and
<br> - has a member- or extension-function hasNext() that returns Boolean.)
<pre><code>
  for(item in collection) print(item)

  for(item: Int in ints) {
    //...
  }
</code></pre>

* 범위 표현식을 이용하여 loop의 범위를 설정 할 수 있다.
<pre><code>
  for(i in 1..3) {
    println(i)
  }
  >>>
  1
  2
  3  

  for(i in 6 downTo 0 step 2) {
    println(i)
  }
  >>>
  6
  4
  2
  0
</code></pre>

* index를 사용하고 싶다면 indices를 이용하면 된다.
<pre><code>
  val array = arrayOf(1,2,3,4)
  for (i in array.indices) {
      println(array[i])
  }
  >>>
  1
  2
  3
  4
</code></pre>
---
### While Loops
while문은 do..while과 유사하게 작동한다.
<pre><code>
while (x > 0) {
  x--
}

do {
  val y = retrieveData()
} while (y != null) // y is visible here!

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
