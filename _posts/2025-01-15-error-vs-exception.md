---
title: Error vs Exception
date: 2025-01-15 16:00:00 +09:00
last_modified_at: 2025-08-31 22:50:00 +09:00
categories: [Programming, Kotlin]
tags:
  [
    kotlin,
    코틀린,
    error,
    exception,
    throwable,
    java,
    자바,
  ]
image: "/assets/img/title/kotlin-logo.png"
---

> 최근 Exception과 Error의 차이에 대한 글을 보고 한번은 정리하고 가면 좋겠다는 생각이 들어 작성하게 되었습니다.
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

## Throwable Class Hierarchy

![throwable-class-hierarchy](/assets/img/posts/throwable-class-hierarchy.png)

Throwable class와 Exception & Error class의 계층 구조를 살펴보자.

Throwable class는 모든 Exception과 Error 클래스들의 상위 클래스이며, Throwable 클래스를 상속받는 클래스만 throw 될 수 있다.
Exception 클래스의 경우 크게 runtime exception과 checked exception으로 나누어지며 두가지 모두 "catch" 되어야할 대상이다.
다음은 runtime exception과 checked exception의 대표적인 예시들이다.

### Runtime Exception
![runtime-exception](/assets/img/posts/runtime-exception.webp)

### Checked Exception
![checked-exception](/assets/img/posts/checked-exception.webp)

## Java에서 Exception 처리
자바에서는 <mark>throws</mark>, <mark>throw</mark>, <mark>try</mark>, <mark>catch</mark>, <mark>finally</mark> 와 같은 키워드들로 처리할 수 있다.
하나씩 살펴보자.

### throws & throw
Exception은 객체로 개발자가 직접 커스텀하여 처리하거나 어플리케이션 레벨에서 처리될 수 있다.

```java
static void catchException() throws Exception {
  throw new Exception();
}
```

* throw : throw 키워드는 메소드 안에서 사용되며 메소드 안에서 실제로 Exception 인스턴스를 호출하는 역할을 한다.
* throws : throws 키워드는 함수를 정의하는 부분에 같이 사용되며 해당 메소드를 호출할 때 호출하는 부분에서 해당 Exception이 
발생할 것이라는 것을 인지하여 처리할 수 있도록 한다.

### try, catch, finally

세개의 키워드는 세트로 사용되며 <mark>try...catch</mark>, <mark>try...finally</mark>, <mark>try...catch...finally</mark> 와 같이 조합되어 사용된다.

```java
static void catchException() throws Exception {
  try {
    // try to do something
  } catch (Exception e) {
    // catch excepton and do something
  } finally {
    // do what you want
  }
}
```

할당된 리소스를 제거하는데 사용하면 좋으며 그렇지 않으며 메모리 누수가 일어날 수 있다.
return을 하든 exception을 던지든 finally 부분의 호출이 보장된다.

```java
static String firstLineOfFile(String path) throws IOException {
  BufferedReader br = new BufferedReader(new FileReader(path));
  try {
    return br.readLine();
  } finally {
    br.close();
  }
}
```

### try with resources

이 구문은 자바7부터 도입되었으며 위의 예제와 같이 리소스를 명시적으로 닫아주는 번거로움을 줄일 수 있다.
AutoCloseable 인터페이스를 구현한 모든 객체에 사용가능하며 해당 인터페이스 안의 close 메소드가 있고
try-with-resources 구문이 종료될때 close 메소드가 자동으로 호출된다.

```java
try (FileReader fr = new FileReader("path/file.txt")) {
  int i;
  while ((i = fr.read()) != -1) {
    System.out.print((char) i );
  }
} catch (IOException e) {
  e.printStackTrace();
}
```

FileReader 객체를 try문 안에서 선언하고 있고 블록이 종료되면 자동으로 close() 메소드를 호출하여 
finally문과 같은 것을 통해 해당 리소스를 닫아줄 필요가 없다. 주의할 점은 당연히 사용하려는 자원이 AutoCloseable
인터페이스를 구현했는지 체크하고, 여러 자원을 동시에 사용하는 경우 각각의 자원을 별도의 try-with-resources 구문으로
처리하여 자원 간의 의존성 문제 같은 것을 발생시키지 않도록 주의해야 한다.

## Java에서 Error 처리??

에러는 말 그대로 어플리케이션 레벨에서 자체적으로 처리하기 힘든 문제가 발생한 상황이라고 볼 수 있다. 대부분 JVM 내에서 일어
나는 에러가 많으며 보통은 코드레벨에서는 바로 발견하기 힘들다. 대표적인 에러를 살펴보자.

### OutOfMemoryError

해당 에러가 발생하면 더 이상 프로그램에 정상적으로 메모리를 할당할 수 없다는 뜻이다. 가령 모종의 이유로 인스턴스가 라이프 사이클이 끝나도 
종료되지 않고 계속해서 무한히 생성된다면 힙 메모리가 터져 OutOfMemoryError가 발생할 수 있다. 해당 에러를 줄이기 위해 메모리 사용량을
모니터링하는 툴을 사용하거나 캐싱을 하여 같은 객체의 생성을 줄이거나 코드의 로직을 향상시키는 등의 방법을 찾아볼 수 있다.

### StackOverflowError

stack space가 부족하여 새로운 메소드를 호출할 수 없는 경우 발생한다. stack은 지역 변수 같은 것을 저장하는 메모리 공간이다.
우리가 메소드를 호출하여 변수에 어떤 값을 할당한다면 스택 안에 새로운 공간을 차지한다. JVM에서 허락하는 최대 depth를 초과하여 
stack이 쌓인 경우 StackOverflowError가 발생한다. 가령 끝나지 않는 while문이 무한 루프에 빠진 경우 발생할 수 있다.
해당 에러를 줄이기 위해 최대한 while문이나 재귀의 사용을 지양하고 불가피한 경우에는 해당 부분의 종료가 보장되도록 코드를 짜는 것이 좋다.

## NoClassDefFoundError vs ClassNotFoundException

### NoClassDefFoundError
해당 에러는 JVM에서 발생하며 런타임 단계에서 특정 클래스를 찾을 수 없을 때 발생한다. 보통 컴파일 단계에서는 해당 클래스가 존재했으나 모종의 이유로
JVM이 해당 클래스를 호출할 수 없을 때 발생한다.

### ClassNotFoundException
특정 class를 호출하고자 할때 해당 클래스를 찾지 못하면 발생한다. 보통 reflection을 사용할 때 발생하기 쉽다.

```java
try {
  Class.forName("com.example.NonExistentClass");
} catch (ClassNotFoundException e) {
  e.printStackTrace();
}
```

## Kotlin에서 Exception 처리

이제 코틀린에서의 예외 처리 방법을 조금 살펴보자.
그 전에 먼저 Result를 알고 넘어가야한다. 

### Result

```kotlin
@SinceKotlin("1.3")
@JvmInline
public value class Result<out T> @PublishedApi internal constructor(
    @PublishedApi
    internal val value: Any?
) : Serializable {
    public val isSuccess: Boolean get() = value !is Failure

    public val isFailure: Boolean get() = value is Failure

    @InlineOnly
    public inline fun getOrNull(): T? =
        when {
            isFailure -> null
            else -> value as T
        }

    public fun exceptionOrNull(): Throwable? =
        when (value) {
            is Failure -> value.exception
            else -> null
        }

    public override fun toString(): String =
        when (value) {
            is Failure -> value.toString() // "Failure($exception)"
            else -> "Success($value)"
        }

    public companion object {
        @Suppress("INAPPLICABLE_JVM_NAME")
        @InlineOnly
        @JvmName("success")
        public inline fun <T> success(value: T): Result<T> =
            Result(value)

        @Suppress("INAPPLICABLE_JVM_NAME")
        @InlineOnly
        @JvmName("failure")
        public inline fun <T> failure(exception: Throwable): Result<T> =
            Result(createFailure(exception))
    }

    internal class Failure(
        @JvmField
        val exception: Throwable
    ) : Serializable {
        override fun equals(other: Any?): Boolean = other is Failure && exception == other.exception
        override fun hashCode(): Int = exception.hashCode()
        override fun toString(): String = "Failure($exception)"
    }
}
```

Result의 값은 
* 성공인 경우 T를 타입으로 하는 값을 가지며
* 실패한 경우 Failure를 wrapper class로 하는 exception를 갖는다.


### RunCatching

```kotlin
public inline fun <R> runCatching(block: () -> R): Result<R> {
    return try {
        Result.success(block())
    } catch (e: Throwable) {
        Result.failure(e)
    }
}
```

자바와 같이 try..catch 로직을 사용하나 
* 성공한 경우 Result.success 반환
* 실패한 경우 Result.faliure를 반환

Result가 제공하는 함수들은 다음과 같다. 하나씩 예시를 통해 살펴보자.

```kotlin
inline fun <T> Result<T>.getOrThrow(): T

inline fun <R, T : R> Result<T>.getOrElse(
  onFailure: (exception: Throwable) -> R
): R

inline fun <R, T : R> Result<T>.getOrDefault(defaultValue: R): R

inline fun <R, T> Result<T>.fold(
  onSuccess: (value: T) -> R,
  onFailure: (exception: Throwable) -> R
): R

inline fun <R, T> Result<T>.map(transform: (value: T) -> R): Result<R>

fun <R, T> Result<T>.mapCatching(transform: (value: T) -> R): Result<R>

inline fun <R, T : R> Result<T>.recover(transform: (exception: Throwable) -> R): Result<R>

inline fun <T> Result<T>.onFailure(action: (exception: Throwable) -> Unit): Result<T>

inline fun <T> Result<T>.onSuccess(action: (value: T) -> Unit): Result<T>
```

runCatching은 Result<T>를 반환하고, 위의 메소드를 사용하여 다양하게 활용할 수 있다.

### getOrNull()

```kotlin
val response = runCatching {
  doSomething()
}.getOrNull()
```

성공 시 doSomething의 값, 실패 시 exception을 무시하고 null 반환

### getOrDefault()

```kotlin
val response = runCatching {
  doSomething()
}.getOrDefault(defaultValue)
```

성공 시 doSomething()의 값, 실패 시 exception 대신 defaultValue의 값 반환

### getOrElse()

```kotlin
val response = runCatching {
  doSomething()
}.getOrElse { ex ->
  logger.warn(ex) { "Exception has occured!" }

  throw ex
}
```

성공 시 doSomething()의 값, 실패 시 logger.log() 부분을 호출하고 에러 발생

### exceptionOrNull()

```kotlin
val exception = runCatching {
  doSomething()
}.exceptionOrNull()
```

에러가 발생한 경우에만 에러객체를 반환.
에러가 발생했는지만 확인하고 싶을 때 사용하면 유용하다.

```kotlin
val isValid = runCatching { doSomething() }.exceptionOrNull == null
```

### onSuccess, onFailure

```kotlin
val response = runCatching {
  doSomething()
}.onSuccess {
  logger.info("Success!!")
}.onFailure {
  logger.info("Failure!!")
}.getOrThrow()
```

성공/실패 시 각각 로그를 남기고 에러 발생시 호출

### try..catch..finally

try catch finally와 같이 사용하고 싶다면 

```kotlin
runCatching {
  doSomething()
}.also {
  doSomethingFinally()
}.getOrThrow()
```

## 마무리하며..

평소 Exception이 Error고 Error가 Exception 아닌가?? 그노미가 그노미 아님??? 라고 했던 내 자신을 반성한다.

## 참고

* [https://toss.tech/article/21010](https://toss.tech/article/21010)
* [https://medium.com/@junfeng0828/b44b07823f72](https://medium.com/@junfeng0828/b44b07823f72)