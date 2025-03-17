---
title: Data Class in Kotlin
date: 2025-02-21 11:35:00 +09:00
last_modified_at: 2025-02-21 11:35:00 +09:00
categories: [Programming, Kotlin]
tags:
  [
    kotlin,
    코틀린,
    data class,
  ]
image: "/assets/img/posts/kotlinvsjava.webp"
---

> 오늘은 data class에 대해 알아보자.
> [공식 documentation](https://kotlinlang.org/docs/data-classes.html)

## data class?

일반 클래스와 달리 여러가지 메소드를 자동으로 생성해주는 클래스이다. `data class`를 선언해주면 아래와 같은 메소드들이 자동으로 같이 생성된다.
- equals()
- hashCode()
- toString()
- componentN()
- copy()

또한 선언시 다음과 같은 조건들을 만족시켜 주어야 한다.
- 기본 생성자(primary constructor)는 최소한 하나 이상의 파라미터가 있어야한다.
- 모든 기본 생성자의 파라미터는 `val` 또는 `var`로 선언해 주어야한다.
- `data class`는 `abstract`, `open`, `sealed`, `inner` 키워드를 붙일 수 없다.

### Why data class?

자바의 경우 아래와 같이 선언해줄 수 있을 것이다.

```java
class Person {
    String name;
    int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge(int age) {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @NotNull
    @Override
    public String toString() {
        return this.getClass().getName() + 
            "(name=" + getName() + ", " +
            "(age=" + getAge() + ")";
    }

    @Override
    public boolean equals() {
        ~~~
    }

    @Override
    public int hasCode() {
        ~~~
    }
}
```

자바의 경우 boilerplate code가 상당히 많다고 볼 수 있다. 하지만 코틀린은?? 아래가 끝이다.

```kotlin
data class Person(
    val name: String,
    val age: String
)
```

자동으로 생성되는 메소드들도 알아보자.

### equals()

두 객체의 모든 프로퍼티가 같은 지 비교한다.

```kotlin
val person1 = Person("John", 21)
val person2 = Person("John", 21)

println(person1 == person2)
// true
```

### hashCode()

두 객체의 메모리 주소가 같은 지 비교한다.

```kotlin
println(person1 === person2)
// false
```

아 여기서 java와 kotlin에서 동등성 비교를 위한 연산자(혹은 메소드)를 알아보고 가자.

|java|kotlin|설명|
|---|---|---|
|equals|==|두 객체의 프로퍼티가 모두 같음|
|==|===|두 객체의 메모리 주소가 같음|

### toString()

다음과 같은 인간이 알기 쉬운 형태로 바꿔준다.

```kotlin
println(person1)
// Person(name=John, age=21)
```

### componentN()

destructuring declarations이 가능해진다.

```kotlin
val (name, age) = person1

println("Destructuring Declarations: $name, $age")
// Destructuring Declarations: John, 21
```

기본 생성자에서 선언한 순서대로 (1- name, 2- age) component1() 메소드에 name 필드가 대응되고 
component2() 메소드에 age 필드가 대응된다.
compiler에 의해 아래와 같이 destructuring delcarations가 된다.

```kotlin 
val name = person1.component1()
val age = person2.component2()
```

### copy()

해당 객체를 복사하며, 특정 필드 값만 바꿔서 복사하는 것이 간편하다.

```kotlin
val person3 = person1.copy(age = 100)

println(person3)
// Person(name=John, age=100)
```

## 클래스 바디 안에 선언된 프로퍼티

기본 생성자 안에는 `name` 프로퍼티를 선언해주고 `age` 프로퍼티의 경우 클래스의 바디 안에 선언을 해주면 어떻게 될까?

```kotlin
data class Person(val name: String) {
    var age: Int = 0
}
```

```kotlin
val person1 = Person("John")
val person2 = Person("John")
person1.age = 10
person2.age = 20

println("person1 == person2: ${person1 == person2}")
println("person1 with age ${person1.age}: ${person1}")
println("person2 with age ${person2.age}: ${person2}")
```

```
person1 == person2: true
person1 with age 10: Person(name=John)
person2 with age 20: Person(name=John)
```

age는 어디다 팔아먹은 모습을 볼 수 있다. `data class`는 기본 생성자의 프로퍼티에 대해서만 toString(), equals(), hashCode(), copy() 메소드를 자동으로 생성해 주어서 클래스 바디의 프로퍼티도 넣어주고 싶다면 `override`해서 구현해주는 것을 잊지 말자.

## 참고

* [https://kotlinlang.org/docs/data-classes.html](https://kotlinlang.org/docs/data-classes.html)