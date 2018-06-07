---
title: 코틀린 소개
author: 한주희
layout: post
icon: fa-angle-right
categories: kotlin
permalink: kotlin-basic.html
---
최근 회사에서 일부 신규 프로젝트를 코틀린으로 하겠다고 하여, 공부한 내용을 정리해 보고자 한다.

* 각 IDE별 설정 방법 링크 : [https://kotlinlang.org/docs/tutorials/](https://kotlinlang.org/docs/tutorials/){:target="_blank"}
* Refrence 링크 : [https://kotlinlang.org/docs/reference/server-overview.html](https://kotlinlang.org/docs/reference/server-overview.html){:target="_blank"}

<hr>
위키를 참고 하여 코틀린에 대한 정보를 간략하게 정리해 보았다.
([나무위키](https://namu.wiki/w/Kotlin){:target="_blank"})

1. 소개
* IntelliJ IDEA의 개발사 JetBrains에서 2011년에 공개한 프로그래밍 언어.
* JVM 기반의 언어이며, JAVA와의 상호 운용이 100% 지원된다.
* JVM 바이트코드가 기본이나 *`Kotlin/Native`*컴파일러를 사용하여 최종 컴파일
* 2017년 구글에서 안드로이드의 공식 언어로 채택하였다.

2. 특징
* 간결한 문법과 런타임 오버헤드가 거의 없다.
* NUll Safety - 코틀린의 변수는 Nullable Types과 Non-Null Types 나뉘고, operator '?'등을 통해 Nullable로 만들어
                Null발생에 대한 안정성을 제공한다.
* 예외 처리를 강제하지 않는다.
* Java의 'Integer'와 같은 별도의 wrapper class가 존재하지 않는다. 모든 primitive type은 객체 취급을 받는다.

그외 더 설명되어 있지만 아직은 이정도까지 파악하는 걸로...^----^;
