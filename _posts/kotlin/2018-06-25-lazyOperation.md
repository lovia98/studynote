---
title: 지연 컬렉션 연산
layout: post
icon: fa-angle-right
categories: Kotlin
permalink: kotlin-lazyOperation.html
img: k1.png
---

### 시퀀스<sup>sequence</sup>를 이용한 컬렉션 지연 연산(lazy operation)

<br>map이나 filter같은 함수는 연산 결과를 새로운 컬렉션에 담아 반환한다.

여러 연산을 연쇄하여 실행하는 경우 매 단계마다 새로운 임시 컬렉션을 만드는 것은 비효율 적이라 할수 있다.

코틀린에서는 이런 부분을 효율적으로 만들기 위해 Sequence 라는 자료형을 제공한다.
<br>ref. https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/index.html

* sequence 사용 방법 : asSequece()함수를 이용 하여 컬렉션을 시컨스로 전환한 후 연산을 시행한다.
{% highlight Kotlin%}
people.asSequence()                
    .map(Person::name)              | Sequnence
    .filter{ it.startsWith("A") }       |  인터페이스의 함수로 연산된다.
    .toList()   //<--- 다시 리스트로 반환    
{% endhighlight %}


sequce를 통한 연산이 일때와 아닐때를 비교하기 위해 map과 filter 람다 본문에 println으로 element를 print해보자.

[예 1-1]
{% highlight Kotlin%}
val peoples = listOf(Person("A", 15)
        , Person("B", 15)
        , Person("C", 30))

fun nonSequeceTest() {
    val a = peoples
            .map{                                   //직접 연산
                println("map : "+it.name)
                it.name
            }
            .filter {                                  //직접 연산
                println("filter : $it")
                it.startsWith("A")
            }
            .toList()
}

//****  nonSequeceTest() 호출시 출력 결과
map : A
map : B
map : C
filter : A
filter : B
filter : C
{% endhighlight %}

{% highlight Kotlin%}
fun sequenceTest() {
    val a = peoples.asSequence()
            .map{                                  //중간 연산1  (지연)
                println("map : "+it.name)
                it.name
            }
            .filter {                                 //중간 연산2  (지연)
                println("filter : $it")
                it.startsWith("A")
            }
            .toList()                               //최종 연산
}

//****  sequeceTest() 호출시 출력 결과
map : A
filter : A
map : B
filter : B
map : C
filter : C
{% endhighlight %}

* 시퀀스 인터페이스내의 연산은 중간 처리를 저장하지 않고 연쇄적으로 적용해서 필요한 시점(.toList()) 에 계산된다. : 연산 지연(lazy operation)
* <span class="fontHighlight2">만약 .toList()를 코드에서 삭제 한다면 어떤 결과가 나타날까?
<br>연산결과가 필요한 시점(최종연산)까지는 중간 연산을 시행하지 않으므로 아무 내용도 출력되지 않는다. </span>
<br>[예 1-2]
{% highlight Kotlin%}
fun nonOperation() {

    val a = peoples.asSequence()
            .map{                               //중간 연산(지연)
                println(it.name)
                it.name
            }
            .filter {                               //중간 연산(지연)
                println(it)
                it.startsWith("A")
            }
}

//****  nonOperation() 호출시 출력 결과       

//아무내용도 출력되지 않는다.  
{% endhighlight %}

* 또한 [예제 1-1]에서 볼수 있듯이, Sequence에서의 연산은 중간 연산의 결과에서 다음 연산이 이루어 지는 것이 아니라
하나의 element마다 모든 연산 처리 하는 것을 알 수 있다.
이런 처리 방법의 효율성은 필요하지 않는 연산을 하지 않게 해줌으로서, 성능을 향상 시키는 효과를 가져온다.

* <span class="fontHighlight2">아래 예제를 보면 sequence를 통한 연산은 2번째 element(2)의 map연산 결과(4)가 3보다 크기 때문에
다음 element의 연산을 진행 하지 않는다는 것을 알 수 있다.</span>

{% highlight Kotlin%}

fun normalTest() {
    listOf(1,2,3,4)
            .map { println("map $it"); it * it }
            .find { println("filter $it");it > 3 }

}

//****   출력결과
map 1
map 2
map 3
map 4
filter 1
filter 4

fun lazyTest() {

    listOf(1,2,3,4).asSequence()
            .map { println("map $it"); it * it }
            .find { println("filter $it");it > 3 }

}

//****  출력결과
map 1
filter 1
map 2
filter 4
{% endhighlight %}


>컬렉션 연산시 Sequence로 변환하여 처리 하면 다음과 같은 장점을 가져 올 수 있다.
* 중간 연산의 결과를 따로 저장하지 않기 때문에 메모리 낭비를 줄일 수 있다.
* 연산처리가 지연되어 필요한 시점에만 최종 실행 되기 때문에 효율적이다.
* 연산 처리시 중간 연산 결과를 가지고 다음 연산을 실행하지 않고 element마다 모든 연산을 한번에 실행 하기 때문에
* 불필요한 연산 처리를 시행 하지 않음으로서 연산 횟수의 효율성을 가져 올 수 있다.
* 이와 같은 장점은 데이터양이 많은 컬렉션을 처리 할 시 성능의 효과를 많이 느낄 수 있다.

> 자바 스트림과 비교
* 시퀀스는 자바8의 Stream과 매우 유사하다.
* 코틀린에도 동일한 개념을 도입한 이유는 안드로이드에서 예전 자바를 사용하는 경우 스트림을 이용할 수 없기 때문이었다.
* 코틀린에서는 자바8에서 제공하는 '병렬처리'(parrallStream)를 제공하지 않는다.


<br>
*시퀀스 만들기*{: .fontHighlight1}
<br>asSequence() 함수는 주어진 컬렉션을 변환해주는 함수라면 처음 원소를 인자로 받아 시퀀스를 생성해 주는 generateSequence 함수도 있다.
* 아래 예는 generateSequence를 이용하여 0부터 100까지 합을 구하는 알고리즘이다.
{% highlight Kotlin%}
val naturalNumbers = generateSequence(0) { it +1 }
val numbersTo100 = naturalNumbers.takeWhile { it <= 100 }


>>> println(numbersTo100.sum())
5050
{% endhighlight %}
* 상위(조상)객체로 이루어진 시퀀스를 만들고 탐색하는 용도로 주로 쓰인다.
<br>아래 예는 hidden속성을 가진 상위 디렉터리가 있는지 검사하는 알고리즘이다.
{% highlight Kotlin%}
fun File.isInsideHiddenDirectory() = generateSequece(this) { it.parentFile }.any { it.isHidden }

>>> val file = File("/Users/svtk/.HiddenDir/a.txt")
>>> println(file.isInsideHiddenDirectory())
true
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
