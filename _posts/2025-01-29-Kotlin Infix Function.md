---
title: Infix Function(중위함수)
date: 2025-01-29 21:38:00 +09:00
last_modified_at: 2025-01-29 22:57:00 +09:00
categories: [Programming, Kotlin]
tags:
  [
    kotlin,
    코틀린,
    infix
  ]
image: "/assets/img/posts/infix-last-days.jpg"
---

> 오늘은 infix notation에 대해 알아보자.
> [공식 documentation](https://kotlinlang.org/docs/functions.html#infix-notation)

## Infix Notation in Kotlin
---

공식 documentation을 보면 infix notation을 사용할 때 3가지의 유의할 사항들이 적혀있다. 
> * They must be member functions or extension functions.
> * They must have a single parameter.
> * The parameter must not accept variable number of arguments and must have no default value.

* 멤버 함수나 확장함수의 형태이어야 한다.
* 반드시 하나의 파라미터를 가져야 한다.
* 파라미터는 가변인자를 사용할 수 없으며, 기본값이 없어야 한다.

### 사용 예시 1 - 확장함수
간단하게 String을 확장하여 같은 단어를 반복하여 출력하는 함수를 만들어보자.

```kotlin
infix fun String.multiply(n: Int): String {
  return this.repeat(n)
}

println("Hello World!\n" multiply 3)
```
```
Hello World!
Hello World!
Hello World!
```

### 사용 예시 2 - 멤버함수
Class 내에서 멤버 함수로 정의하게 되면 class 자체(this)가 dispatcher기 때문에 굳이 선언해줄 필요가 없다.

```kotlin
class Assertion<T>(private val target: T) {
  infix fun isEqualTo(other: T) {
    if (target != other) {
      throw AssertionError("Expected $target to be equal to $other")
    }
  }

  infix fun isDifferentFrom(other: T) {
    if (target == other) {
      throw AssertionError("Expected $target to be different from $other")
    }
  }
}

val result = Assertion(5)
result isEqualTo 4
result isDifferentFrom 5
```

```
Exception in thread "main" java.lang.AssertionError: Expected 5 to be equal to 4 
    at infixfunction.Assertion.isEqualTo(Assertion.kt:6)

Exception in thread "main" java.lang.AssertionError: Expected 5 to be different from 5
	at infixfunction.Assertion.isDifferentFrom(Assertion.kt:12)
```

## 참고

* [https://kotlinlang.org/docs/functions.html#infix-notation](https://kotlinlang.org/docs/functions.html#infix-notation)
* [소스코드](https://github.com/thomas783/kotlin-practice/tree/main/src/main/kotlin/infixfunction)