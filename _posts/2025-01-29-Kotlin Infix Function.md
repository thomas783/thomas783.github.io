---
title: Kotlin Infix Function
date: 2025-01-29 21:38:00 +09:00
last_modified_at: 2025-01-29 21:38:00 +09:00
categories: [Programming, Kotlin]
tags:
  [
    Kotlin,
    코틀린,
    Infix
  ]
image: "/assets/img/title/kotlin-logo.png"
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

### 사용 예시 1 - String 확장함수
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

### 사용 예시 2 - Builder DSL
Builder 패턴도 DSL로 적용해보자.

```kotlin
class PrincessMaker(
  private val name: String,
  private val age: Int,
) {

  companion object {
    infix fun build(block: Builder.() -> Unit) = Builder().apply(block).build()

    class Builder {
      var name: String = ""
      var age: Int = 0

      fun build() = PrincessMaker(name, age)
    }
  }

  override fun toString(): String {
    return "PrincessMaker(name=$name, age=$age)"
  }
}

// 사용 예시
PrincessMaker.build {
    name = "Ariel"
    age = 16
  }.also { println(it) }
```
```
PrincessMaker(name=Ariel, age=16)
```

## 참고

* [https://kotlinlang.org/docs/functions.html#infix-notation](https://kotlinlang.org/docs/functions.html#infix-notation)
* [https://velog.io/@cksgodl/kotlin-kotlin%EC%97%90%EC%84%9C%EC%9D%98-infix-%EB%B0%8F-Builder-%ED%8C%A8%ED%84%B4](https://velog.io/@cksgodl/kotlin-kotlin%EC%97%90%EC%84%9C%EC%9D%98-infix-%EB%B0%8F-Builder-%ED%8C%A8%ED%84%B4)
* [소스코드](https://github.com/thomas783/kotlin-practice/tree/main/src/main/kotlin/infixfunction)