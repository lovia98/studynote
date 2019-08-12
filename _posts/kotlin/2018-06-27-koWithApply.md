---
title: 수신 객체 지정 람다 - with와 apply
layout: post
icon: fa-angle-right
categories: Kotlin
permalink: kotlin-withApply.html
img: k1.png
---

## with
with는 코틀린 표준 라이브러리 함수로 람다식 안에서 다른 객체의 메소드를 호출 할 수 있게 해준다.

아래와 같은 알파벳을 만드는 함수가 with를 사용하였을때 어떻게 변경 되는지 보자.
{% highlight Kotlin %}
//A부터 Z까지 print하는 흔한 함수
fun alphabet(): String {

    val result = StringBuilder()

    for(letter in 'A'..'Z') {
        result.append(letter)
    }

    result.append("\nNow I know the alphabet!")

    return result.toString()
}

//with를 이용한 함수
fun alphabetWith(): String {

    val stringBuilder = StringBuilder()

    return with(stringBuilder) {

        for(letter in 'A'..'Z') {
            append(letter)
        }

        append("\nNow I know the alphabet!")
        this.toString()
    }
}
{% endhighlight %}

*with를 이용한 함수에서..*{: .fontHighlight1}
* with 괄호() 안의 객체 stringBuilder는 수신객체이다.
* with 중괄호{} 안에서 this는 바로 수신객체인 stringBuilder를 가르킨다.
* 중괄호{} 안에서 this를 호출하지 않아도 수신객체의 메소드를 바로 호출 할 수 있다.
* with는 인자1개와 블럭으로 이루어진 statement같지만, 사실 파라미터가 2개인 함수이다.
<br>   파라미터1 : 수신객체인 stringBuilder
<br>   파라미터2 : 람다식 {}


* 수신 객체 지정 람다(lamda with receiver) : 이렇게 수신 객체를 람다안에서 명시 하지 않아도 다른 객체의 메소드를 호출 할 수 있는 람다를 '수신 객체 지정 람다' 라고 부른다.


<span class="fontHighlight2">with식을 본문으로 하는 함수로 만들기</span>
<br>StringBuilder 참조 변수를 굳이 만들 필요 없이 바로 with식을 본문으로 사용할 수 있다.
{% highlight Kotlin %}
fun alphabetWithExpress() = with(StringBuilder()) {

    for(letter in 'A'..'Z') {
        append(letter)
    }

    append("\nNow I know the alphabet!")

    toString()
}
{% endhighlight %}


---
## apply

apply  함수는 with와 거의 같다. 유일하게 다른점은 apply는 항상 자신에게 전달된 객체(수신 객체)를 반환한다는 것이다.
{% highlight Kotlin %}
fun alphabetApply() = StringBuilder().apply {

    for(letter in 'A'..'Z') {
        append(letter)
    }

    append("\nNow I know the alphabet!")

}.toString()
{% endhighlight %}

* apply는 확장함수로 정의돼 있다. (Ref.https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/apply.html)
![apply]({{site.baseurl}}/assets/images/kotlin/apply.jpg)
* 위 식에서 알수 있듯이 apply의 반환객체는 수신객체(StringBuilder()) 의 자신이다. (apply{} == Stringbuilder())
* 따라서 apply의 결과값의 toString을 호출 해서 값을 얻어 낼 수 있다.


* apply 함수는 객체를 초기화하는 용도로 쓰이기 좋다.(자바의 builder와 같은)
{% highlight Kotlin %}
class TextView(context: Context) {

    var text = ""
    var textSize = 0
}

fun createViewWithCustomAttribuetes(context: Context) =
        TextView(context).apply{
            text = "sample Text"
            textSize = 40
        }
{% endhighlight %}

* 위 예와 같은 [ StringBuilder().appy{}.toString() ] 역할을 해주는 표준 라이브러리 함수가 별도로 있다.
<br>buildString 함수인데, 내부를 살펴 보면 StringBuilder를 확장하여 String을 반환하고 있다.
<br>(Ref. https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/build-string.html)
![buildString]({{site.baseurl}}/assets/images/kotlin/buildString.jpg)
{% highlight Kotlin %}
fun alphabetBuildString() = buildString{

    for(letter in 'A'..'Z') {
        append(letter)
    }

    append("\nNow I know the alphabet!")
}
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
