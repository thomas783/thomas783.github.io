---
title: StringBuilder And StringBuffer
date: 2025-02-26 23:36:00 +09:00
last_modified_at: 2025-02-26 23:36:00 +09:00
categories: [ Programming, Kotlin ]
tags:
  [
    kotlin,
    코틀린,
    string,
    string builder,
    string buffer,
  ]
image: "/assets/img/posts/string-thread-safe.jpg"
---

> 오늘은 StringBuilder와 StringBuffer에 대해 알아보자.

## String, StringBuilder, StringBuffer?

- `String`: 불변(immutable)이다. 한 번 생성된 문자열은 변경할 수 없다.
- `StringBuilder`: 가변(mutable)이다. 문자열을 변경할 수 있으며 단일 스레드 환경에서 사용한다.
- `StringBuffer`: 가변(mutable)이다. 문자열을 변경할 수 있으며 멀티 스레드 환경에서 사용한다.

하나씩 살펴보자.

### String

String 객체는 불변이기 때문에 한 번 생성되면 변경할 수 없다. 이러한 불변성 덕분에 String 객체 간의 공유를 통해
메모리 사용을 최적화할 수 있다. 

예를 들어, 같은 문자열로 `String` 객체를 생성하면 JVM 상에서 이미 존재하는 String 객체를 참조하여 재사용하고
이는 String Pool이라는 메모리 영역에서 관리된다. 

그러나 문자열의 연산이 빈번하게 발생한다면 변경할 때마다 새로운 String 객체가 생성되어 메모리 낭비로 이어질 수 있다. 

```kotlin
var str: String = "Hello"
str += " World"
```

위 코드에서 `str`은 `Hello`와 `World`를 합친 `Hello World`가 된다. 이때 `str`은 새로운 String 객체를 참조하게 된다.
기존에 참조하던 `Hello` 객체는 GC(Garbage Collection)의 대상이 된다.

이러한 경우 StringBuilder나 StringBuffer를 사용하는 것이 좋다.

### StringBuilder && StringBuffer

`StringBuilder`와 `StringBuffer`는 API가 거의 동일하다. `StringBuffer`의 경우 동기화(synchronized) 키워드를 사용하여
동기화를 지원하여 멀티 쓰레드 환경에서 사용하면 좋고 `StringBuilder`는 동기화를 지원하지 않아 단일 스레드 환경에서 사용하는 것이 좋다.

동기화는 멀티 쓰레드 환경에서 데이터의 일관성을 유지시켜 주나 성능 저하를 일으킬 수 있기 때문에 단일 쓰레드 환경인 경우 `StringBuilder`를 사용하는 것이 좋다.

### String vs StringBuilder vs StringBuffer

|              | String      | StringBuilder | StringBuffer |
|--------------|-------------|---------------|--------------|
| Storage      | String Pool | Heap          | Heap         |
| Modifiable   | Immutable   | Mutable       | Mutable      |
| Thread Safe  | Yes         | No            | Yes          |
| Synchronized | Yes         | No            | Yes          |
| Performance  | High        | High          | Low          |

** JDK 7 이후로는 `Heap` 영역으로 변경되었고 이전에는 `PermGen` 영역에 저장되었다고 한다.
