---
title: Returns and Jumps
author: 한주희
layout: post
icon: fa-angle-right
categories: Kotlin
permalink: kotlin-returnsAndJumps
img: k1.png
---
* 코틀린은 3가지 jump expressions를 가지고 있다. - returns. break. continue.

---
### Lable로 Break와 Continue 하기

* 코틀린에서는 어떤 식이던 <code>라벨</code>로 표시할수 있다.
* 라벨은 식별자 다음에 <code>@</code>를 붙여 사용한다. : `abc@`, `fooBar@`
* 특정 조건절앞에 라벨을 붙이기만 하면 label expressions을 사용할 수 있다.
<pre><code>
  myLabel@ for(i in 1..100) {
      for(j in 1..100) {
          println(j)
          if(j == 3) break@myLabel
      }
  }

  //*****
  1
  2
  3
</code></pre>

---
### Lable로 Return
코틀린에서는 아래와 같이 return 식별자 하나만 사용이 가능한데, 공식문서에 의하면 [Nothing Type](https://kotlinlang.org/docs/reference/exceptions.html#the-nothing-type){:target="_blank"}을 넘기는거라고 한다.
<pre><code>
  val s = person.name ?: return
</code></pre>
이를 이용해서 loop를 빠져 나가는 용도로 쓸 수 있다.
<pre><code>
fun foo() {
  listOf(1, 2, 3, 4, 5).forEach {
      if (it == 3) return // non-local return directly to the caller of foo()
      println(it)
  }
  println("this point is unreachable")
}

>>> foo()
1
2
</code></pre>
주의할 점은 return-expression은 가장 가까운 함수를 return한다는 점이다.
<br>위 코드의 경우 .forEach 람다식의 결과가 return되는 것이 아니라 함수 foo()의 리턴
으로 인식하고 함수를 빠져 나가게 된다.

* 람다식 내에서만 return 하고 싶다면 label을 이용해야 한다.
<pre><code>
fun fooLabel() {
    listOf(1, 2, 3, 4, 5).forEach lit@{
        if (it == 3) return@lit // local return to the caller of the lambda, i.e. the forEach loop
        print(it)
    }
    print(" done with explicit label")
}


>>> fooLabel()
1
2
4
5
 done with explicit label
</code></pre>

다음과 같이 앞단에 라벨을 선언하지 않고도 암시적인 Lable을 사용할 수도 있다.
<pre><code>
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach {
        if (it == 3) return@forEach // local return to the caller of the lambda, i.e. the forEach loop
        println(it)
    }
    println(" done with implicit label")
}

>>> foo()
1
2
4
5
 done with implicit label
</code></pre>

* Label왜에 익명함수를 이용하여 위와 똑같은 결과를 도출 할 수도 있다.
<pre><code>
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach(fun(value: Int) {
        if (value == 3) return  // local return to the caller of the anonymous fun, i.e. the forEach loop
        println(value)
    })
    println(" done with anonymous function")
}

>>> foo()
1
2
4
5
 done with anonymous function
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
