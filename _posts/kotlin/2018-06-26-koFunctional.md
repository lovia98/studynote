---
title: 자바 함수형 인터페이스 활용
author: 한주희
layout: post
icon: fa-angle-right
categories: Kotlin
permalink: kotlin-funtionalInterface.html
---

### 함수형 인터페이스
<br>
* java8에서 동작(함수)를 파라미터화 하기 위해서 클래스의 선언과 인스턴스화를 동시에 수행할 수 있도록 익명 클래스라는 기법을 사용하였다.
* 익명 클래스를 전달 할 시 코드의 장황함을 해결하기 위해서 람다 표현식이 활용 된다.


{% highlight Kotlin %}
//자바의 Onclick인터페이스
public interface OnClickListener {
    void onClick(View v);
}

//Onclick타입의 변수를 인자로 받는 메소드
public class Button {
    public void setOnClickListener(OnClickListener l) {
            //......
    }
}

//익명클래스를 이용해 파라미터 전달
Button button = new Button();
button.setOnClickListener(new OnClickListener() {
        @override
        public void onClick(View v) {
            System.out.println("button view");
        }

    }) {
        //.....
}


//익명 클래스 대신 람다식을 인자로 넘김
button.setOnClickListener(v->{ System.out.println("button view"); })

{% endhighlight %}




* 위와 같이 람다식을 인자로 넘기는 코드가 가능한 이유는 OnclickListener에 추상 메소드가 단 하나만 있기 때문이다.
* 이렇게 하나의 추상 메소드를 가진 인터페이스를 <span class="highlighter-rouge">함수형 인터페이스(functional interface)</span> 또는 <span class="highlighter-rouge">SAM인터페이스-single abstract method</span>라고 한다.

<br>코틀린은 함수형 인터페이스를 인자로 취하는 자바 메소드를 호출할 때 람다를 넘길 수 있게 해준다.
{% highlight Kotlin %}
//자바
public class FunctionalTest {

        public String setFunctional(int num, IntFunction<Integer> f) {
            return "function Result : " + f.apply(num);
        }

        public void setFunctionalCustom(Runnable r) {
        }
}

> > > val ft = FunctionalTest()
>>> println(ft.setFunctional(5,{i->i*i}))
function Result : 25

>>> println(ft.setFunctional(5){i->i*i})
function Result : 25

{% endhighlight %}

<br>익명클래스 전달코드를 간소화 하기 위해 람다식을 이용하지만, <code>내부적으로 람다와 익명클래스 사이에는 차이</code>가 있다.
* 익명클래스를 이용할 시에는 매번 새로운 익명 클래스의 인스턴스가 생성 되지만,
<br><code>람다는 단 하나의 인스턴스만 만들고 그 무명 객체를 메소드를 호출할 때마다 반복 사용한다.</code>
{% highlight Kotlin %}
//람다를 이용할시 IntFunction타입의 인스턴스 한번만 생성됨.

//example1
ft.setFunctional(5){i->i*i}



//example2
val intFunc = IntFunction { i->i*i }
println(ft.setFunctional(5, intFunc))
{% endhighlight %}

* 매번 같은 인스턴스를 사용한다면, 란다가 주변 영역의 변수는 어떻게 포획(capture)하는걸까? 이런 경우 컴파일러는 <code>매번 주변 영역의 변수를 포획한 새로운 인스턴스를 생성해 준다.</code>
{% highlight Kotlin %}
//자바 (In FunctionalTest class)
public void setFunctionalCustom(Runnable r) {
}

//코틀린
fun callCustom(id: String) {

    // "id"변수 포획
     setFunctionalCustom{ println(id) } // <-- 새로운 "id" 참조를 위해 매번 새로운 Runnable 인스턴스를 만들어 낸다.
}
{% endhighlight %}

---
### SAM생성자를 통한 람다 반환
<br>
컴파일러가 자동으로 람다를 함수형 인터페이스의 익명 클래스로 바꾸지 못하는 경우 SAM생성자를 사용해야 하는데,
<br>바로 함수형 인터페이스의 인스턴스를 반환하는 메소드가 있을 경우 그렇다.
{% highlight Kotlin %}
//오류 발생!
fun createAllDoneFunnable() : Runnable {
    return { println("All Done!") }
}

//Runnable생성자를 통해 리턴
fun createAllDoneFunnable() : Runnable {
    return Runnable { println("All Done!") }
}

//혹은 리턴타입을 람다식으로 바꾸면 가능
fun createAllDoneFunnabl1e() : () -> Unit {
    return { println("All Done!") }
}

fun createFuntional(num : Int) : IntFunction<Int> {
    return IntFunction { num*num }
}
{% endhighlight %}

* 자바는 생성자 없이 되던데..
{% highlight Kotlin %}
public Runnable returnRunnable() {

    return ()->{
        System.out.println("Runnable");
    };
}

public IntFunction<Integer> returnIntFunc() {
    return i->i*i;
}
{% endhighlight %}

> <span class="fontHighlight1">람다안의 this</span>
<br>람다 안에서 this는 그 람다를 둘러싼 클래스의 인스턴스를 가리킨다.
<br>만약 이벤트 리스너가 이벤트를 처리하다가 자기 자신의 리스너 등록을 해제해야 한다면, (람다 안에서 익명 클래스의 인스턴스 자체를 호출해야 한다면) 람다를 사용 할 수 없다.
<br>이런 경우 람다 대신 익명 객체를 사용해서 리스너를 구현해야 한다. 익명 객체 안에서는 this가 그 익명 객체 인스턴스 자신을 가리킨다.

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
