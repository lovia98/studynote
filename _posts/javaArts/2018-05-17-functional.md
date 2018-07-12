---
title: 병렬 스트림
author: 한주희
layout: post
icon: fa-angle-right
categories: java
permalink: parrelStream.html
comments: true
---

컬렉션에 paralleStream을 호출하면 <bold>병렬 스트림</bold>이 생성된다. 병렬 스트림이란 각각의 스레드에서
처리할 수 있도록 스트림 요소를 여러 청크로 분할한 스트림이다. 따라서 병렬 스트림을 이용하면 모든 멀티코어 프로세서가
각각의 청크를 처리하도록 할당할 수 있다.

```JAVA
//일반 스트림 처리
public long sequentialSum(long n) {
    return Stream.iterate(1L, i -> i+1)
            .limit(n)
            .reduce(0L, Long::sum);
}

//병렬 처리
public long parallelSum(long n) {
    return Stream.iterate(1L, i -> i+1)
            .limit(n)
            .parallel() //스트림을 병렬 스트림으로 변환
            .reduce(0L, Long::sum);
}
```
---
### 스트림 성능 측정
<br>병렬화를 이용하면 순차나 반복 형식에 비해 성능이 좋아질 것이라 추축되지만, 실제로는 그렇지 않다.
<br>아래와 같이 1부터 n까지 합을 구하는 함수를 만들었다.
```JAVA
    //전통적인 처리 : loop
    public static long iterativeSum(long n) {
        long result = 0;
        for(long i = 1L; i<=n; i++) {
            result += i;
        }
        return result;
    }

    //스트림 처리
    public static long sequentialSum(long n) {
        return Stream.iterate(1L, i -> i+1)
                .limit(n)
                .reduce(0L, Long::sum);
    }

    //병렬 처리
    public static long parallelSum(long n) {
        return Stream.iterate(1L, i -> i+1)
                .limit(n)
                .parallel() //스트림을 병렬 스트림으로 변환
                .reduce(0L, Long::sum);
    }
```
그리고 아래는 각 함수의 처리 속도를 측정하는 함수이다.
```JAVA
  //성능 측정
  public long mesaure(Function<Long, Long> adder, long n) {
      long fastest = Long.MAX_VALUE;

      for(int i = 0; i<10; i++) {
          long start = System.nanoTime();
          long sum = adder.apply(n);
          long duration = (System.nanoTime()-start) / 1_000_000;
         // System.out.println("Result: "+sum);
          if(duration < fastest) fastest = duration;
      }

      return fastest;
  }

  @Test
  public void test() {
      System.out.println(mesaure(ParallTest::iterativeSum, 10_000_000)+" msecs");
      System.out.println(mesaure(ParallTest::sequentialSum, 10_000_000)+" msecs");
      System.out.println(mesaure(ParallTest::parallelSum, 10_000_000)+" msecs");
  }
```
실행해 본 결과는 다음과 같다.
```
//처리 결과
3 msecs //loop
100 msecs //스트림
78 msecs  //병렬 스트림
```
 기대한 바와는 다르게 for 루프가 가장 빠르다. for 루프는 기본값을 박싱하거나 언박싱할 필요가 없으므로 수행속도가 빠르다.
 책(자바인액션)의 결과와는 다르게 나의 경우 병렬 스트림 처리가 일반 스트림 처리 보다 더 수행속도가 빠른 것으로 나왔다.
 하지만 이런 경우 병렬 스트림처리가 느리다고 소개되어 있으니 그 이유를 정리 하고 가겠다.
  * Stream.iterate가 박싱된 객체를 생성하므로 이를 다시 언박싱하는 과정이 필요했다.
  * Stream.iterate는 병렬로 실행될 수 있도록 독립적인 청크로 분할하기가 어렵다.

 두번째 이유를 자세히 설명하자면,
   * 스트림 연산은 최종 연산이 진행되기 전까지는 연산을 미루는 Lazy 연산을 한다.
   * 따라서 위 병렬처리 함수(parallelSum)로직에서 iterate함수가 stream내에서 이루어 짐으로 .reduce가 실행되기 전까지 전체 숫자 리스트가 준비 되지 않는다.
   * 전체 숫자 리스트가 준비되지 않았으므로 스트림을 병렬로 처리할 수 있도록 청크로 분할할 수 없다.

   스트림이 병렬로 처리되도록 지시했고 각각의 합계가 다른 스레드에서 수행되었지만 결국 순차처리 방식과 크게 다른 점이 없으므로
   스레드를 할당하는 오버헤드만 증가하게 된다.

   다음은 이러한 문제점을 개선한 함수이다.
```JAVA
//개선된 처리
public static long paralleRangeSum(long n) {
    return LongStream.rangeClosed(1, n)
            .parallel()
            .reduce(0L, Long::sum);
}
```   
처리 결과는 다음과 같이 가장 빨랐다.
```
@Test
public void test1() {
    System.out.println(mesaure(ParallTest::paralleRangeSum, 10_000_000)+" msecs");
}

1 msecs
```
  빨라진 이유는 다음과 같은 이유에서다.
  * long타입에 특화된 LongStream을 사용함으로서 박싱과 언박싱 오버헤드가 사라진다.
  * LongStream.rangeClosed는 쉽게 청크로 분할할 수 있도록 숫자 범위가 정해진다.

### 결론
 병렬스트림 뿐 아니라 스트림을 사용하고자 할시 수행속도를 위한 적절한 자료구조 선택이 필요하다.
<br>병렬화를 이용하려면 스트림을 재귀적으로 분할해야 하고, 각 서브스트림의 연산결과를 하나의 값으로 합쳐야 한다.
<br>멀티코어간의 데이터 이동은 우리 생각보다 비싸서 데이터 전송 시간보다 훨씬 오래 걸리는 작업만 병렬화 하는 것이 좋다.


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
