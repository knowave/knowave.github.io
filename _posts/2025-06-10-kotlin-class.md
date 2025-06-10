---
layout: post
title: "[Kotlin] Class"
description: "Kotlin 문법 중 Class에 대해서"
data: 2025-06-10 10:30:00 +0900
categories: [TIL, Kotlin]
tags: [Kotlin, Syntax]
---

## Kotlin

`kotlin`은 `Java` 를 기반으로 생겨난 **프로그래밍 언어**로, `JVM` 위에서 실행된다.많이 알다시피 `Java`보다 간결하고 `null` 체크에 유리하다.  
공식 문서에서는 `kotlin`을 아래와 같이 정의한다.

> `kotlin`은 개발자를 더욱 더 행복하게 설계된 현대적이면서 성숙한 프로그래밍 언어다.  
간결하면서 안전하고, `Java` 및 기타 언어와 상호 운용이 가능하며,  
생산성 있는 프로그래밍을 위하여 여러 플랫폼 간에 코드를 재사용할 수 있는 다양한 방법을 제시한다.

대충 보면 `java`를 기반으로 하는 언어로 `java`보다 더 간단하고 생산성이 향상된 언어라고 볼 수 있을 거 같다.

## Class와 Instance 생성

먼저, class 정의를 할 때 `class` 키워드를 사용한다.

```kotlin
class Shape
```

`class`의 속성은 `class` 선언할 때와 `class body` 안에서 사용할 수 있다.

```kotlin
class Rectangle(val height: Double, val length: Double) {
    val perimeter = (height + length) * 2
}
```

`class` 선언 시 선언한 생성자 (constructor)는 `this` 키워드 없이 사용이 가능하다.

```kotlin
class Rectangle(val height: Double, val length: Double) {
    val perimeter = (height + length) * 2
}

fun main() {
    val rectangle = Rectangle(5.0, 2.0)
    println("The Perimeter is ${rectangle.perimeter}")
}
```

`instance` 생성 시 `Java`와 다르게 `new` 연산자를 사용하지 않는다.

`class` 간 상속을 할 때 `:` 를 사용하여 상속을 진행한다. `kotlin`의 `class`는 기본적으로 `final` 이기 때문에  
상속이 가능한 `class`를 만들기 위해서는 `open` 키워드를 붙여야 한다.

```kotlin
class Shape

class Rectangle(val height: Double, val length: Double) : Shape {
    val perimeter = (height + length) * 2
}
```