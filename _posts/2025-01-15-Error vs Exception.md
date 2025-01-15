---
title: Error vs Exception
date: 2025-01-15 16:00:00 +09:00
last_modified_at: 2025-01-15 17:00:00 +09:00
categories: [Programming, Kotlin]
tags:
  [
    Kotlin,
    코틀린,
    Error,
    Exception,
    Throwable,
  ]
image: "/assets/img/title/kotlin-logo.png"
---

> 최근 Exeption과 Error의 차이에 대한 글을 보고 한번은 정리하고 가면 좋겠다는 생각이 들어 작성하게 되었습니다.
> [해당 글](https://medium.com/@junfeng0828/b44b07823f72)

## Exception vs Error in Kotlin
---
기본적으로 Kotlin에서 Exception과 Error는 Java의 예외 처리를 기반으로 한다.

```kotlin
@SinceKotlin("1.1") public actual typealias Error = java.lang.Error
@SinceKotlin("1.1") public actual typealias Exception = java.lang.Exception
```

이 둘의 차이를 이해하기 위해 Java의 소스코드를 봐보자.

## Exception vs Error in Java
---
Java에서의 Exception과 Error는 둘다 Throwable 클래스를 상속한다는 공통점이 있다.

```java
public class Exception extends Throwable 
public class Error extends Throwable
```

그러면 어떻게 서로 다를까. Exception와 Error 클래스들의 소스코드 주석을 살펴보자.

### Exception Class 주석

> The class Exception and its subclasses are a form of Throwable that indicates conditions that a reasonable application might want to catch.
> The class Exception and any subclasses that are not also subclasses of RuntimeException are checked exceptions. 
> Checked exceptions need to be declared in a method or constructor's throws clause if they can be thrown by the execution of the method or constructor and propagate outside the method or constructor boundary

> 해석을 해보면 "Exception과 그것의 서브 클래스들은 Throwable의 형태로 어플리케이션이 잡고자하는 경우이다.
> Exception과 그것의 서브 클래스들 중 RuntimeException의 서브클래스가 아닌 것들은 checked exceptions이다.
> Checked exceptions는 메소드 또는 생성자의 실행에 의해 던져질 여지가 있는 경우 메소드나 생성자에 throws 절을 통해 선언해주어야한다."

### Error Class 주석

> An Error is a subclass of Throwable that indicates serious problems that a reasonable application should not try to catch. 
> Most such errors are abnormal conditions. 
> The ThreadDeath error, though a "normal" condition, is also a subclass of Error because most applications should not try to catch it.
> A method is not required to declare in its throws clause any subclasses of Error that might be thrown during the execution of the method but not caught, since these errors are abnormal conditions that should never occur. That is, Error and its subclasses are regarded as unchecked exceptions for the purposes of compile-time checking of exceptions.

> 마찬가지로 해석을 해보면 "Error는 Throwable의 서브 클래스로 심각한 문제가 발생했음을 알리며 어플리케이션이 잡고자하면 안된다.
> 대부분의 Errors는 비정상적인 경우이다.
> 비록 "정상" 경우인, ThreadDeath 에러 또한 Error의 서브클래스이며 어플리케이션 레벨에서 잡고자 시도하면 안된다.
> 메소드의 실행을 통해 던져질 여지가 있더라도 이 경우의 Error는 비정상적인 경우로 절대로 발생해서는 안되기에 Error의 서브클래들은 throws 절을 통해 선언할 필요도 없다.
> 즉, Error와 이것의 서브 클래스들은 unchecked exceptions로 컴파일 단계에서 검사하는 것에 그 목적이 있다."

Exception과 Error 모두 Throwable Class를 상속한다는 공통점은 있으나 둘의 목적성은 정반대이다. 
Exception은 try-catch를 통해 예외처리를 하는 것이 권장되어지는 반면, Error의 경우는 try-catch를 통해 예외처리를 하지 않는 것이 권장된다.

![throwable-class-hierarchy](/assets/img/posts/throwable-class-hierarchy.png)

...ing