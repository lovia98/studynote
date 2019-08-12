---
title: 컬렉션 함수형 API
layout: post
icon: fa-angle-right
categories: Kotlin
permalink: kotlin-lamdaCollection.html
img: k1.png
---

### filter와 map
* filter
<br>Ref. : https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter.html
<br>컬렉션에서 주어진 람다가 true를 반환하는 element를 추출하는 함수이다.
{% highlight Kotlin %}
val list = listOf(1,2,3,4)
println(list.filter { it % 2 == 0 }) //prints [2, 4]
{% endhighlight %}

* map
<br>Ref. : https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-map/index.html
<br>컬렉션에서 원하는 다른 형태의 element의 조합을 만들어 낼 수 있다.
{% highlight Kotlin %}
  val list = listOf(1,2,3,4)
  println(list.map{it * it}) //prints [1, 4, 9, 16]

  //객체의 필드만 추출
  data class Person(val name:String, val age:Int)
  val people = listOf(Person("Alice", 20), Person("juhee", 30))
  println(people.map { it.name })  // prints [Alice, juhee]

  //멤버참조를 이용한 표현
  people.map(Person::name)
{% endhighlight %}

* filter와 map을 연결(join)하여 원하는 컬렉션을 추출 할수도 있다.
{% highlight Kotlin %}
//예) 25세 이상인 사람의 이름을 추출
println(
      people.filter { it.age > 25 }
              .map(Person::name)
)
{% endhighlight %}


---
### all, any, count, find
* all
<br>컬렉션의 모든 element가 특정 조건을 만족한다면 true, 한개라도 만족하지 않는 것이 있다면 false를 반환한다.
<br>Ref. : https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/all.html

* any
<br>특정 조건을 만족하는 element가 한개라도 존재한다면 true, 아니라면 false를 반환한다.
<br>Ref. : https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/any.html
{% highlight Kotlin %}
al people = listOf(Person("철수", 15)
        , Person("영희", 15)
        , Person("주희", 30)
        , Person("영자", 31)
        , Person("금자", 32))

//27이하에 해당하는 조건식
val canBelInClub27 =  {p:Person->p.age <=27}

println("모두가 27세 미만인가? : "+ people.all(canBelInClub27))
println("27세 미만인 사람이 있는가? : "+ people.any(canBelInClub27))


//**** 출력결과
모두가 27세 미만인가? : false
27세 미만인 사람이 있는가? : true
{% endhighlight %}

*!all(condition)과 any(!condition)*{: .fontHighlight}
<br>!all(condition) 과 any(!condition)의 결과는 같다. 반대로 !any(condition)과 all(!condition)도 같다.
<br>가독성을 위해 !all  이나 !any와 같은 함수를 쓰기 보다는 함수안의 조건을 변경하여 사용하는 것을 추천한다.(지은이 왈)

{% highlight Kotlin %}
val list = listOf(1, 2, 3)

//둘다 같은 true를 반환한다.
println(!list.all { it == 3 })
println(list.any{ it!=3 })  //<-- 가독성을 위해 이코드를 쓰도록 하자.
{% endhighlight %}


* find
<br>특정 조건을 만족하는 첫번째 element를 반환한다.
<br>Ref. : https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/find.html

* count
<br>결과 컬렉션의 element의 count수를 반환한다.
<br>Ref. : https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/count.html

{% highlight Kotlin %}
println(people.count(canBelInClub27))
println(people.find(canBelInClub27))

//****  출력결과
2
Person(name=철수, age=15)
{% endhighlight %}

*함수를 적재적소에 사용하라: count와 size*{: .fontHighlight}
<br>컬렉션을 필터링한 결과의 크기를 가져 올때 .size보다는 count함수를 이용하는 편이 좋다.
<br>예) people.fileter(canBelInClub27).size

<span class="fontHighlight2"><br>lazy 연산과 관련된 것 같은데(개인적인 추측) size를 이용하는 경우 실질적인 collection 결과가 필요하므로
<br>size를 구하기전 조건에 맞는 collection을 만들게 된다.
<br>하지만 count함수를 이용할 경우 최종 조건에 맞는 element의 수를 추척할 뿐 만족하는 요소들을 따로 저장하지 않는다.</span>

---
### groupBy
<br>groupBy는 컬렉션의 요소들을 어떤 특성에 따라 분류하고자 할때 사용한다.
<br>조건(람다식)을 파라미터로 넘기면 그에 따른 collection을 분리하여 map으로 반환해 준다.   
Ref. : https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/group-by.html
{% highlight Kotlin %}
val people = listOf(Person("철수", 15)
        , Person("영희", 15)
        , Person("주희", 30)
        , Person("영자", 30)
        , Person("금자", 32))

println(people.groupBy{it.age})


//****  출력결과
{15=[Person(name=철수, age=15), Person(name=영희, age=15)],
30=[Person(name=주희, age=30), Person(name=영자, age=30)],
32=[Person(name=금자, age=32)]}
{% endhighlight %}

---
### flatMap, flatten
* flatMap
<br>개별 element가 지니고 있는 list(or Array)의 요소들을 1개의 리스트로 병합하여 반환한다.
<br>글을 봐서는 이해가 안되니 예제와 그림으로..
<br>Ref. : https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/flat-map.html
{% highlight Kotlin %}
val books = listOf(
        Book("bookA", listOf("이지용", "김순모", "한원석"))
        ,Book("bookB", listOf("한유신", "윤주빈", "한주희"))
        ,Book("bookC", listOf("김정수", "조기택", "김현진"))
)
println(books.flatMap { it.authors })
println(books.flatMap { it.authors }.toSet()) //toSet() 은 결과리스트에서 중복을 없애준다.

//****  출력결과
[이지용, 김순모, 한원석, 한유신, 윤주빈, 한주희, 김정수, 조기택, 김현진, 한주희]
[이지용, 김순모, 한원석, 한유신, 윤주빈, 한주희, 김정수, 조기택, 김현진]


val strings = listOf("abc", "def")
println(strings.flatMap{it.toList()})

//****  출력결과
[a, b, c, d, e, f]
{% endhighlight %}

![flatMap]({{site.baseurl}}/assets/images/kotlin/flatMap.jpg)

* flatten
<br>개별 element가 list(or Array)자체인 경우 flatten을 이용해 1개의 리스트로 병합할 수 있다.
<br>Ref. : https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/flatten.html
{% highlight Kotlin %}
val deepList = listOf(listOf(1), listOf(2, 3), listOf(4, 5, 6))
println(deepList.flatten()) // [1, 2, 3, 4, 5, 6]
{% endhighlight %}

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
