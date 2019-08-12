---
title: 위임 프로퍼티1
layout: post
icon: fa-angle-right
categories: Kotlin
permalink: delegatedProp.html
img: k1.png
---

[Ref번역 - 의역 살짝(https://kotlinlang.org/docs/reference/delegated-properties.html#lazy)](https://kotlinlang.org/docs/reference/delegated-properties.html#lazy){: target="_blank"}

위임 프로퍼티는 말그대로 위임 패턴을 적용한 프로퍼티이다.
<br>우리는 보통 필요할 경우에 수동으로 accessor를 구현할 필요가 있는데 이 로직이 자주 쓰인다면 매번 구현할 것이 아니라 라이브러리에 넣고
쓴다면 좋을 것이다.

- Lazy 프로퍼티 : 첫 번째 acces시에만 값이 계산된다.
- 관찰 가능한 프로퍼티 : listeners들이 ㅍ로퍼티에 대한 변경 사항에 대한 알림을 받을수 있다.
- 각 프로퍼티에 대한 별도의 필드 대신 map에 각프로퍼티들을 저장한다.

이러한 (및 기타) 사례를 다루기 위해 Kotlin은 위임 된 속성을 지원합니다.
```Kotlin
class Example {
    var p: String by Delegate()
}
```
구문은 다음과 같다.
<br>` val / var <property name> : <Type> by <expression>`
<br> getValue () 및 setValue () 메서드에 위임 될 프로퍼티에 해당하는 get () (및 set ())이 by delegate이므로
`by by expression`이 대리자이다.

프로퍼티 대리자는 인터페이스를 구현할 필요는 없지만 getValue () 함수 (및 vars의 경우 setValue () -)를 제공해야합니다. 예 :

```Kotlin
class Delegate {
    operator fun getValue(thisRef: Any?, property: KProperty<*>): String {
        return "$thisRef, thank you for delegating '${property.name}' to me!"
    }

    operator fun setValue(thisRef: Any?, property: KProperty<*>, value: String) {
        println("$value has been assigned to '${property.name}' in $thisRef.")
    }
}
```

Delegate의 인스턴스에 delegate하는 <code>p</code>를 읽을 때 <code>Delegate</code>의 <code>getValue()</code>함수가 호출되므로
첫 번째 매개 변수는 p에서 읽은 객체이고 두 번째 매개 변수는 p 자체에 대한 설명을 포함합니다 (예 : 이름을 가져 올 수 있습니다.) 예 :
```Kotlin
val e = Example()
println(e.p)

출력 결과 :
com.kotlin.web.delegate.DelegateTest$Example@2752f6e2, thank you for delegating 'p' to me!
```
마찬가지로 p에 대입하면 setValue () 함수가 호출됩니다. 처음 두 매개 변수는 동일하고 세 번째 매개 변수는 할당 할 값을 보유합니다.

```Kotlin
e.p = "NEW"

//노출 결과
NEW has been assigned to 'p' in com.kotlin.web.delegate.DelegateTest$Example@2752f6e2.
```

Kotlin 1.1에서는 함수 또는 코드 블록 내에 위임 된 속성을 선언 할 수 있으므로 반드시 클래스의 멤버가 아니어야합니다. 아래에서 예제를 찾을 수 있습니다.

### Standard Delegates
<br>Kotlin 표준 라이브러리는 몇 가지 유용한 대리인을위한 팩토리 메서드를 제공합니다.

Lazy
<br>lazy()는 lamby를 취하고 lazy <T>의 인스턴스를 반환하는 함수로, lazy 속성을 구현하는 위임자 역할을 할 수 있습니다. get ()의 첫 번째 호출은 lazy ()에 전달 된 lambda를 실행하고 결과를 기억합니다 이후에 get ()을 호출하면 단순히 기억 된 결과가 반환됩니다.
```Kotlin
val lazyValue: String by lazy {
    println("computed!")
    "Hello"
}

fun main(args: Array<String>) {
    println(lazyValue)
    println(lazyValue)
}
```
This example prints:

computed!
Hello
Hello

기본적으로 게으른 속성의 평가는 동기화됩니다. 값은 하나의 스레드에서만 계산되며 모든 스레드는 동일한 값을 볼 수 있습니다. 초기화 대리자의 동기화가 필요하지 않아 여러 스레드가 동시에 실행할 수 있도록 LazyThreadSafetyMode.PUBLICATION을 매개 변수로 lazy () 함수에 전달합니다. 초기화가 항상 단일 스레드에서 발생한다고 확신하는 경우 스레드 안전성 보장 및 관련 오버 헤드가 발생하지 않는 LazyThreadSafetyMode.NONE 모드를 사용할 수 있습니다.


Observable
<br>Delegates.observable ()은 초기 값과 수정을위한 핸들러라는 두 개의 인수를 취합니다. 처리기는 속성에 할당 할 때마다 (할당이 수행 된 후에) 호출됩니다. 세 개의 매개 변수가 있습니다 : 속성이 할당되고 이전 값과 새 값이 지정됩니다.
```Kotlin
import kotlin.properties.Delegates

class User {
    var name: String by Delegates.observable("<no name>") {
        prop, old, new ->
        println("$old -> $new")
    }
}

fun main(args: Array<String>) {
    val user = User()
    user.name = "first"
    user.name = "second"
}
```
This example prints:

```
<no name> -> first
first -> second
```

할당을 가로 채서 "거부"하려면 observable () 대신 vetoable ()을 사용하십시오. 거부권에 전달 된 핸들러는 새로운 속성 값의 할당이 수행되기 전에 호출됩니다.

Storing Properties in a Map
<br>하나의 일반적인 사용 사례는 속성 값을지도에 저장하는 경우입니다. 이것은 JSON을 파싱하거나 다른 "동적 인"일을하는 것과 같은 애플리케이션에서 자주 발생합니다. 이 경우 위임 된 속성의 위임자로지도 인스턴스 자체를 사용할 수 있습니다.


```Kotlin
class User(val map: Map<String, Any?>) {
    val name: String by map
    val age: Int     by map
}
```

In this example, the constructor takes a map:
```Kotlin
val user = User(mapOf(
    "name" to "John Doe",
    "age"  to 25
))
```
위임 된 속성은 이 맵에서 값을 가져옵니다 (문자열 키 - 속성 이름).
```Kotlin
println(user.name) // Prints "John Doe"
println(user.age)  // Prints 25
```

읽기 전용 Map 대신 MutableMap을 사용하면 var의 속성에도 적용됩니다.
```Kotlin
class MutableUser(val map: MutableMap<String, Any?>) {
    var name: String by map
    var age: Int     by map
}
```

Local Delegated Properties (since 1.1)

You can declare local variables as delegated properties. For instance, you can make a local variable lazy:
```Kotlin
fun example(computeFoo: () -> Foo) {
    val memoizedFoo by lazy(computeFoo)

    if (someCondition && memoizedFoo.isValid()) {
        memoizedFoo.doSomething()
    }
}
```
The memoizedFoo variable will be computed on the first access only. If someCondition fails, the variable won't be computed at all.


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
