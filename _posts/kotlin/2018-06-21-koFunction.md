---
title: 코틀린의 컬렉션
layout: post
icon: fa-angle-right
categories: Kotlin
permalink: kotlin-collection.html
img: k1.png
---

코틀린에서는 컬렉션을 만들어내는 여러 표준 라이브러리를 제공한다.
```Kotiln
val set = hashSetOf(1, 7, 53)       //set

val list = arrayListOf(1, 7, 53)    //list
val map = hashMapOf(1 to "one", 7 to "seven", 53 to "fifty-three")   // map
```

코틀린에서 제공하는 특별한 키워드가 아니라 함수를 통해 컬렉션을 생성하는데, println 으로 타입을 찍어 보면,
<br>모두 자바 컬렉션인 것을 알 수 있다.(javaClass는 getClass()에 해당하는 코틀린 코드다.)
```Kotiln

>>> val set = hashSetOf(1,7,53)
>>> println(set.javaClass)
class java.util.HashSet

>>> val list = arrayListOf(1,2,3)
>>> println(list.javaClass)
class java.util.ArrayList

>>> val map = hashMapOf(1 to "one", 7 to "seven", 53 to "fifty-three")
>>> println(map.javaClass)
class java.util.HashMap

```
 코틀린이 자체 컬렉션을 제공하지 않고 표준 자바 컬렉션을 활용하면 자바 함수를 호출 할때 자바와 코틀린 컬렉션을 서로 변환할 필요가
 <br>없으므로 자바와 상호작용하기가 훨씬 더 쉽다.




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
