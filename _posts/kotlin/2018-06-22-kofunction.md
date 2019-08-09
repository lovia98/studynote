---
title: 최상위 함수와 프로퍼티
author: 한주희
layout: post
icon: fa-angle-right
categories: kotlin
permalink: kotlin-static.html
comments: true
---

자바에서는 모든 코드를 클래스 기반에서 작성해야 한다. 하지만 특정 도메인 영역에는 포함되기 어려운 유틸에 관련된 변수나
메소드를 생성해야 할 경우가 있는데, 클래스를 하나 생성하고 그 클래스에 static 멤버로 선언하여 사용하는 것이 보통이다.

* 코틀린에서는 이런 무의미한 클래스가 필요 없다.
* 코틀린에서는 코틀린파일(.kt)의 최상위 수준, 모든 다른 클래스의 밖에 선언된 메소드와 프로퍼티는 static 멤버로 처리한다.
* 이런 함수나 프로퍼티는 패키지의 멤버이므로 다른 패키지에서 사용하고자 한다면 해당 패키지를 import해야 한다.

아래와 같이 'strings'라는 이름의 패키지에 'prefix.kt'이라는 파일을 생성하여,함수 하나를 선언해 보자.
```Kotiln
package strings

fun prefixAddFirst(s: String, prefix: String): String {
    return prefix+s
}

>>> print(prefixAddFirst("hello", "Juhee! "))
Juhee! hello
```

코틀린도 JVM에서 실행 되기 때문에, 코틀린 컴파일러는 이파일을 <bold>컴파일 할때</bold> 코틀린 파일명으로 `새로운 클래스를 정의`해준다.
<br>예제와 같은 경우 파일명이 'prefix.kt'이므로 'PrefixKt'라는 이름으로 클래스가 만들어진다.
<br>컴파일 후에는 자바에서는 다음과 같은 코드로 인식 될 것이다.
```Kotiln
package strings

public class PrefixKt {
  public static String prefixAddFirst(String s, String prefix) {
    return prefix+s
  }
}
```

함수와 마찬가지로 프로퍼티도 파일의 최상위 수준에 놓일 수 있다.
```Kotiln
var opCount = 0

fun performOperation() {
    opCount++
}

fun reportOperationCount() {
    print("Operation performed $opCount times")
}
```
함수와 마찬가지로 최상위 프로퍼티도 정적 필드에 저장된다.
<br>최상위 프로퍼티를 활용해 <code>val</code>로 선언하면 상수로 사용할 수 있다.
```Kotiln
  val UNIX_LINE_SEPARATOR = "\n"
```
* 기본적으로 최상위 프로퍼티도 일반적인 프로퍼티처럼 접근자 메소드(getter, setter)를 통해 자바코드에 노출 된다.
<br>코틀린 코드 상에서 상수처럼 보이지만, 자바에서 위에 UNIX_LINE_SEPARATOR같은 변수에 접근 하려면 getter를 이용해야 한다.
* 자바에서와 같은 상수를 선언하기 위해서는 `const` 키워드를 사용하여야 한다.
```Kotiln
  const val UNIX_LINE_SEPARATOR = "\n"
```
위 코드는 다음 자바 코드와 동일한 바이트 코드를 만들어 낸다.
```Kotiln
  public static final String UNIX_LINE_SEPARATOR = "\n"
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
