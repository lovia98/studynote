---
title: 병렬 스트림
layout: post
date: 2018-05-17
categories: JAVA
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
3 msecs //loop
100 msecs //스트림
78 msecs  //병렬 스트림
```
 기대한 바와는 다르게 for 루프가 가장 빠르다. for 루프는 기본값을 박싱하거나 언박싱할 필요가 없으므로 수행속도가 빠르다.
 <br>책(자바인액션)의 결과와는 다르게 나의 경우 병렬 스트림 처리가 일반 스트림 처리 보다 더 수행속도가 빠른 것으로 나왔다.
 <br>하지만 이런 경우 병렬 스트림처리가 느리다고 소개되어 있으니 그 이유를 정리 하고 가겠다.
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
<br>병렬화를 이용하려면 스트림을 재귀적으로 분할해야 하고, 각 서브스트림의 연산결과를 하나의 값으로 합쳐야 한다.
<br>멀티코어간의 데이터 이동은 우리 생각보다 비싸서 데이터 전송 시간보다 훨씬 오래 걸리는 작업만 병렬화 하는 것이 좋다.

* 확신이 서지 않는다면 직접 측정하라.
* 박싱 : 자동 박싱과 언박싱은 성능을 크게 저하시킬 수 있는 요소다. 되도록이면 기본형 특화 스트림을 사용하는 것이 좋다.
* 순차 스트림보다 병렬 스트림에서 성능이 떨어지는 연산이 있다. - 특히 limit이나 findFirst처럼 요소의 순서에 의존하는
<br>연산을 병렬 스트림에서 수행하려면 비싼 비용을 치러야 한다. 예를 들어 findAny는 요소의 순서와 상관 없이 연산하므로
<br>findFisrt보다 성능이 좋다.
* 스트림에서 수행하는 전체 파이프라인 연산비용을 고려해라. 하나의 요소를 처리하는데 드는 비용이 커지면 커질 수록 병렬 스트림
으로 성능을 개선할 수 있는 가능성이 있음을 의미한다.
* 소량의 데이터에서는 병렬 스트림이 도움 되지 않는다. 소량의 데이터를 처리하는 상황에서는 병렬화 과정에서 생기는 부가 비용을
상쇄할 수 있을 만큼의 이득을 얻지 못하기 때문이다.
* 스트림을 구성하는 자료구조가 적절한지 확인하라.
<br>예를 들면 ArrayList가 LinkedList보다 분할하기 낫고, range팩토리 메서드로 만든 기본형 스트림을 쓰는 것이 좋다.
* 스트림의 중간연산이 스트림의 특성을 어떻게 바꾸는지도 생각해봐야 한다. filter연산이 있는 경우 스트림의 길이를 예측 할
수 없으므로 효과적으로 스트림을 병렬 처리 할 수 있을지 알 수 없게 된다.
* 최종 연산의 병합과정(예를 들면 Collector의 combiner 메서드) 비용을 살펴 보라. 병합 과정의 비용이 비싸다면
<br>병렬 스트림으로 얻은 성능의 이익이 서브스트림의 부분결과를 합치는 과정에서 상쇄될 수 있다.


