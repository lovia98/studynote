---
title: 인터페이스에 선언된 프로퍼티 구현
layout: post
icon: fa-angle-right
categories: Kotlin
permalink: kotlin-property2.html
img: k1.png
---
 코틀린에서는 인터페이스에 추상 프로퍼티 선언을 넣을 수 있다.
 ```Kotlin
 interface User {
   val nickname: String
 }
````
인터페이스에 있는 프로퍼티 선언에는 뒷받침하는 필드나 세터등의 정보가 들어 있지 않다.
<br>인터페이스를 구현한 하위 클래스에서 상태 저장을 위한 프로퍼티 등을 만들어야 한다.
<br>이 프로퍼티는 추상프로퍼티임으로 `override`를 표시해야 한다.

 ```Kotlin
 interface User {
    val nickname: String
}

class PrivateUser(override val nickname: String) :User

class SubscribingUser(val email: String) :User {
    override val nickname: String
        get() = email.substringBefore('@')
}

class FacebookUser(val accountId: Int) :User {
    override val nickname = "https://www.facebook.com/$accountId"
}

>>>println(PrivateUser("test@daum.net").nickname)
test@daum.net

>>>println(SubscribingUser("test@daum.net").nickname)
test

>>>println(FacebookUser(3244532).nickname)
https://www.facebook.com/3244532
````

인터페이스에는 추상 프로퍼티뿐 아니라 게터와 세터가 있는 프로퍼티를 선언할 수도 있다.
<br>이런 경우 getter에 대한 기본 구현이 커스텀 되어 있는 상태이므로 backing field를 참조할 수 없다.
```Kotlin

interface UserInfo {
    //추상 프로퍼티이기 때문에 반드시 override해야 함.
    val email: String
    //get에 대한 구현이 있기 때문에 override하지 않아도 됨.
    val nickname: String
        get() = email.substringBefore('@')
}

````
---
### 접근자(getter, setter)의 가시성(visibility) 변경
<br>
* 접근자의 가시성은 기본적으로 프로퍼티의 가시성과 같다.(프로퍼티가 public이면 getter, setter도 public)
* 원한다면 접근자의 가시성을 변경 할수 있다.

```Kotlin
class LengthCounter {
  var counter: Int = 0
    private set

  fun addWord(word: String) {
    counter += word.length
  }  
}
````
```Kotlin
>>> val len = LengthCounter()

// private setter기 때문에 외부에서 직접 값을 바꿀 수 없다.
>>> len = "test"    

error: val cannot be reassigned
len = "test"
^
error: type mismatch: inferred type is String but Line_40.LengthCounter was expected
len = "test"
      ^
````
```Kotlin
>>> len.addWord("Hi")
>>> println(len.counter)
2
````




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
