---
title: 클래스와 Object:위임에 의한 구현
author: 한주희
layout: post
icon: fa-angle-right
categories: Kotlin
permalink: kotlin-delegation.html
---

Ref 번역 : https://kotlinlang.org/docs/reference/delegation.html

<code>Delegation 패턴</code>은 구현 상속에 대한 좋은 대안으로 입증되었으며, Kotlin은 native로
보일러 플레이트 코드가 필요하지 않도록 지원합니다. <code>Derived</code> 클래스는 모든 public 멤버를 지정된
객체에 위임하여 인터페이스 Base를 구현할 수 있습니다.

```Kotlin
interface Base {
    fun print()
}

class BaseImpl(val x: Int) : Base {
    override fun print() { print(x) }
}

class Derived(b: Base) : Base by b

fun main(args: Array<String>) {
    val b = BaseImpl(10)
    Derived(b).print()
}
```

<code>Derived</code>의 상위 유형 목록에있는 <span class="highlighter-rouge">by</span>절은 b가 <code>Derived</code>의 오브젝트에 내부적으로 저장되고 컴파일러가 b로 전달하는 <code>Base</code>의 모든 메소드를 생성 함을 나타냅니다.


## 위임에 의해 구현 된 인터페이스의 멤버 오버라이딩(Overriding)
예상대로 작업을 재정의합니다. 컴파일러는 델리게이트 객체 대신 오버라이드 구현을 사용합니다. override fun print () {print ( "abc")}를 Derived에 추가하면 프로그램은 print가 호출 될 때 "10"대신 "abc"를 출력합니다.
```Kotlin
interface Base {
    fun printMessage()
    fun printMessageLine()
}

class BaseImpl(val x: Int) : Base {
    override fun printMessage() { print(x) }
    override fun printMessageLine() { println(x) }
}

class Derived(b: Base) : Base by b {
    override fun printMessage() { print("abc") }
}

fun main(args: Array<String>) {
    val b = BaseImpl(10)
    Derived(b).printMessage()
    Derived(b).printMessageLine()
}
```

그러나 이 방법으로 재정의 된 멤버는 인터페이스 멤버의 자체 구현에만 액세스 할 수있는 대리자 객체의 멤버에서 호출되지 않습니다.
```Kotlin
interface Base {
    val message: String
    fun print()
}

class BaseImpl(val x: Int) : Base {
    override val message = "BaseImpl: x = $x"
    override fun print() { println(message) }
}

class Derived(b: Base) : Base by b {
    // This property is not accessed from b's implementation of `print`
    override val message = "Message of Derived"
}

fun main(args: Array<String>) {
    val b = BaseImpl(10)
    val derived = Derived(b)
    derived.print()
    println(derived.message)
}
```


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
