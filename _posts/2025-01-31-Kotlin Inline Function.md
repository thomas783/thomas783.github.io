---
title: Inline Function
date: 2025-02-03 11:25:00 +09:00
last_modified_at: 2025-02-03 11:25:00 +09:00
categories: [Programming, Kotlin]
tags:
  [
    kotlin,
    코틀린,
    inline,
    noinline,
    crossinline,
    reified
  ]
image: "/assets/img/posts/inline-skate.jpg"
---

> 오늘은 inline function에 대해 알아보자.
> [공식 documentation](https://kotlinlang.org/docs/inline-functions.html)

## Inline Function in Kotlin
---

들어가기에 앞서 공식 documentation의 설명을 살펴보자.
> Using higher-order functions imposes certain runtime penalties: each function is an object, and it captures a closure. A closure is a scope of variables that can be accessed in the body of the function. Memory allocations (both for function objects and classes) and virtual calls introduce runtime overhead.

대충 해석을 해보면 "고차함수를 사용하는 것은 런타임 시 패널티가 발생한다. 각 함수는 객체이며 클로저를 생성한다. 클로저란
함수의 바디로 접근할 수 있는 변수의 스코프를 의미한다. 함수 객체 및 클래스의 메모리 할당과 가상 호출은 런타임 오버헤드를 발생시킨다." (~~거의 뭐 조사빼고 다 영어네~~)

해당 글만 읽어보면 무슨 말인지 잘 이해가 가지 않는다. 예시를 통해 살펴보자.

### Inline이 없을 때 무슨 일이??

inline 키워드 없이 고차함수를 만들어보자.
```kotlin
fun doWithoutInline(lambda: () -> Unit) {
  println("Hi!! I'm doing something without inline")
  lambda()
}
```

해당 코드를 컴파일해보면 Function 객체를 파라미터로 받고 invoke 메소드를 실행하는 모습이다.
```java
public static final void doWithoutInline(@NotNull Function0 lambda) {
  Intrinsics.checkNotNullParameter(lambda, "lambda");
  String var1 = "Hi!! I'm doing something without inline";
  System.out.println(var1);
  lambda.invoke();
}
```

이제 공식 documentation이 말했던 "패널티"가 눈에 보인다. 고차함수의 사용으로 인해 느자구 없는(의미 없는) 객체 
(Function)가 생성되어 메모리에 할당되었고 내부적으로 invoke 메소드의 호출이 생겨 오버헤드가 생긴다는 것을 알 수 있다.

### Inline과 함께

그렇다면 이번에는 inline 키워드를 앞에 붙여보자.
```kotlin
inline fun doWithInline(lambda: () -> Unit) {
  println("Hi!! I'm doing something with inline")
  lambda()
}

fun main() {
  doWithoutInline { println("without inline") }
  doWithInline { println("with inline") }
}
```

컴파일도 해서 해당 함수를 호출하는 부분을 비교해보자.
```java
public static final void doWithInline(@NotNull Function0 lambda) {
  Intrinsics.checkNotNullParameter(lambda, "lambda");
  int $i$f$doWithInline = false;
  String var2 = "Hi!! I'm doing something with inline";
  System.out.println(var2);
  lambda.invoke();
}

public static final void main() {
  doWithoutInline((Function0)null.INSTANCE);
  int $i$f$doWithInline = false;
  String var1 = "Hi!! I'm doing something with inline";
  System.out.println(var1);
  int var2 = false;
  String var3 = "with inline";
  System.out.println(var3);
}
```

먼저 inline 키워드가 없는 함수는 새로운 객체를 생성하여 호출하는 반면 inline 키워드가 있는 함수는
람다식 내부의 코드가 통으로 복사되었다.

### 장단점

- 장점: 객체를 생성하거나 함수를 또 호출하는 등의 오버헤드가 사라진다. (성능의 향상을 기대할 수 있음)
- 단점: 컴파일되는 바이트 코드의 양이 늘어난다. (2~3줄 정도만 사용하는 것을 권장하는 듯하다 남용은 금물)

## noinline

그렇다면 여기서 우리는 이런식으로 inline으로 전달 받은 함수를 다른 함수의 인자로 넘겨주는 상상을 해볼 수 있다.
```kotlin
inline fun doWithoutNoinline(lambda1: () -> Unit, lambda2: () -> Unit) {
  println("Hi!! I'm doing something without noinline")
  lambda1()
  doSomething { lambda2() }
}

fun doSomething(lambda: () -> Unit) {
  println("Hi!! I'm doing something")
  lambda()
}
```

하지만 유감스럽게도 `doSomething { lambda2() }` 부분에서 에러가 난다.

```
Can't inline 'lambda2' here: it may contain non-local returns. Add 'crossinline' modifier to parameter declaration 'lambda2'
```

`inline`은 람다식 안의 내용을 그대로 가져오는 식으로 동작하기에 인자로 넘겨주는 것은 작동하지 않는다. 이런 경우 `noinline` 키워드를 통해 inline fun 안에서 inline으로 작동하지 않을 부분만 따로 선언해줄 수 있다.

```kotlin
inline fun doWithNoinline(lambda1: () -> Unit, noinline lambda2: () -> Unit) {
  println("Hi!! I'm doing something with noinline")
  lambda1()
  doSomething { lambda2() }
}

fun main() {
  doWithNoinline({ println("with noinline") }, { println("noinline") })
}
```

```java
public static final void main() {
  Function0 lambda2$iv = (Function0)null.INSTANCE;
  int $i$f$doWithNoinline = false;
  String var2 = "Hi!! I'm doing something with noinline";
  System.out.println(var2);
  int var3 = false;
  String var4 = "with noinline";
  System.out.println(var4);
  doSomething((Function0)(new Function0(lambda2$iv) {
      final Function0 $lambda2;

      public {
        this.$lambda2 = $lambda2;
      }

      public final void invoke() {
        this.$lambda2.invoke();
      }

      public Object invoke() {
        this.invoke();
        return Unit.INSTANCE;
      }
  }));
}
```

자바로 컴파일해서 보면 `inline`으로 선언한 부분은 앞에서 봤던 것처럼 람다식 안의 코드를 그대로 가져오고
 `noinline`으로 선언된 부분은 `inline`이 없는 일반적인 고차함수처럼 객체를 생성하는 형식으로 작동이 되는 것을 볼 수 있다.

## crossinline

`inline` 함수 안에서 lambda parameter로 `inline`이 아닌 고차함수를 호출하는 경우를 자세히 보자. 
`noinline`으로 먼저 선언해보자.

```kotlin
fun doSomething(lambda: () -> Unit) {
  println("start doing something")
  lambda()
  println("end doing something")
}

inline fun doWithoutCrossinline(noinline block: () -> Unit) {
  println("start without crossinline")
  doSomething {
    println("start without crossinline lambda")
    block()
    println("end without crossinline lambda")
  }
  println("end without crossinline")
}

fun main() {
  doWithoutCrossinline {
    println("start main")
  }
}
```

다음과 같은 형태로 바뀐다.

```kotlin
fun main() {
  println("start without crossinline")
  val block = {
    println("start main")
  }
  doSomething {
    println("start without crossinline lambda")
    block()
    println("end without crossinline lambda")
  }
  println("end without crossinline")
}
```

여기서 우리는 `val block = {...}` 부분이 없어지고 바로 아래와 같은 형태로 바뀌면 좋겠다고 상상해볼 수 있다.

```kotlin
fun main() {
  println("start without crossinline")
  doSomething {
    println("start without crossinline lambda")
    println("start main")
    println("end without crossinline lambda")
  }
  println("end without crossinline")
}
```

이럴 때 `crossinline` 키워드를 사용하면 가능하다.

```kotlin
inline fun doWithCrossinline(crossinline block: () -> Unit) {
  println("start with crossinline")
  doSomething {
    println("start with crossinline lambda")
    block()
    println("end with crossinline lambda")
  }
  println("end with crossinline")
}
```

`crossinline` 키워드를 사용하면 다음과 같은 형태로 바뀌고 이렇게 되면 non local return을 사용할 수 없게 된다.

```kotlin
fun main() {
  // 컴파일 O
  doWithInline {
    return
  }
  // 컴파일 X
  doWithNoinline({ println("hi") }) {
    return
  }
  // 컴파일 X
  doWithCrossinline {
    return
  }
}
```

일반적인 `inline` 함수의 경우 람다식 안에서 `return`을 통해 main 함수를 종료시킬 수 있고 이러한 것을 
non local return 이라고 한다. 하지만 `noinline`이나 `crossinline` 키워드가 있는 경우 
inline이 아닌 함수가 들어있을 수 있어 return이 main을 종료시켜야할지 아니면 람다식 안에서 호출하는 또 다른
함수를 종료해야할지 명시해 줄 수 없어 위와 같은 형태로 쓸수 없다.

## reified

마지막으로 reified type에 대해 알아보자. 먼저 어떤 클래스의 타입을 파라미터로 넘겨주고 싶을 때 우리는 보통 이런식으로
작성할 수 있다.

```kotlin
class TreeNode {
  var parent: TreeNode? = null

  companion object {
    fun <T> TreeNode.findParentOfType(clazz: Class<T>): T? {
      var p = parent
      while (p != null && !clazz.isInstance(p)) {
        p = p.parent
      }
      @Suppress("UNCHECKED_CAST")
      return p as T?
    }
  }
}

fun main() {
  val grandFather = TreeNode()
  val father = TreeNode()
  val child = TreeNode()
  father.parent = grandFather
  child.parent = father
  println(child.findParentOfType(TreeNode::class.java))
}
```

위와 같은 경우 우리는 해당 클래스를 명시적으로 넘겨주어야 인자의 타입에 접근할 수 있다. 하지만 아래와 같은 방법으로
바꿔준다면 reflection이 필요 없어지고 `is`나 `as` 같은 연산자도 바로 사용할 수 있다.

```kotlin
inline fun <reified T> TreeNode.findParentOfType(): T? {
  var p = parent
  while (p != null && p !is T) {
    p = p.parent
  }
  return p as T?
}

fun main() {
  ...
  println(child.findParentOfType<TreeNode>())
}

```

일반적인 함수에서는 사용할 수 없고 오직 `inline` 함수 안에서만 reified를 사용할 수 있다는 것에 유의하자.

## 참고

* [https://kotlinlang.org/docs/inline-functions.html](https://kotlinlang.org/docs/inline-functions.html)
* [소스코드](https://github.com/thomas783/kotlin-practice/tree/main/src/main/kotlin/inlinefunction)