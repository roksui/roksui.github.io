---
title: "Coroutines: Under the hood"
---

## 개요
프로그래밍에서 코루틴이라는 개념은 생각보다 오래되었다. 그러나 필자는 비교적 최근 Kotlin Coroutines를 사용하며 처음 코루틴이라는 개념을 알게되었고, 기존 Java의 Thread보다 가볍고 비용 효율적으로 사용할 수 있다는 장점으로 많이 사용해왔다. 본 글에서는 코루틴의 역사와 어떠한 장점을 위해 구상되고 사용되어 왔는지 좀 더 자세히 알아보고자 한다.

## 역사
코루틴이라는 용어는 1958년 Melvin Edward Conway에 의해 처음 발표되었으며, 그는 어셈블리 프로그램에 이를 최초로 적용한 사람이다. 그 후 1963년 "Design of a Separable Transition-diagram Compiler"라는 글을 출판하여 분리가능성(separability)의 설계 속성을 가지는 Cobol 컴파일러에 대해 얘기하였다.

Conway가 말하길, 

> "프로그램이 분리가능하다는 것은 특정한 제약에 의거하여 서로 소통하는 프로세싱 모듈로 분리될 수 있다는 것을 의미한다 - 각 모듈은 코루틴이 될 수 있다."

라고 서술한다.

그럼 코루틴이 된다는 것은 정확히 무슨 뜻일까?

각 모듈은 그것의 인접한 모듈들을 인풋 및 아웃풋 서브루틴(subroutine) 처럼 여기며 그것들과 소통한다는 것을 뜻한다.

예를 들어, 함수 A 안에서 함수 B가 호출되면, A는 B가 끝날 때 까지 기다려야한다. 이 함수들은 비협력적(non-cooperative)이다. OS가 통제권을 B에게 주는 것이고, B가 끝나면 OS는 다시 A에게 통제권을 준다. 이러한 기다리는 상황을 없애기 위해서는 B를 다른 스레드에서 실행시킬 수 있을 것이다.

이러한 경우에 코루틴이 어떻게 도움이 될 수 있을까?

코루틴은 협력적인(cooperative) 함수들을 의미한다 - Coroutine의 "Co"가 Cooperative를 의미한다. 그리고 함수들의 통제권은 OS가아닌 개발자에 의해 결정되고, 여러 개의 다른 스레드를 사용하는 효과를 단 하나의 스레드로도 낼 수도 있을 것이다. 그렇게 하기 위해, 통제권을 주는 함수의 exit point와 통제권을 받는 함수의 entry point가 기억된다. 코틀린 코루틴의 예시로 더 자세히 알아보자.

## Kotlin Coroutines
Hello World 예시를 보자.

```kotlin
import kotlinx.coroutines.*

fun main() {
    GlobalScope.launch {
        delay(1000L)
        println("World")
    }

    println("Hello")
    Thread.sleep(2000L)
}
```

이 코드는 `GlobalScope.launch{}`를 사용하여 코루틴을 생성한다. 그리고 해당 코루틴 안에서 실행을 1초간 `delay`하고 "World"를 출력한다.

코루틴 밖에서, 프로그램은 "Hello"를 출력하고 스레드는 2초간 잠재운다.

아웃풋은 다음과 같다.

```
Hello
World
```

코드를 좀 더 자세히 설명하자면, `GlobalScope`는 하나의 코루틴 스코프이고, `launch` 함수를 사용하여 코루틴을 생성하였다. 그리고 이 `launch` 함수는 하나의 익명 suspend 함수 `{}`를 필요로 한다. 코루틴을 생성한 후, 우리는 `delay` 함수를 호출하였다 - `delay` 함수 또한 suspend function이고, 1초의 delay 후, `delay` 함수는 종료되고 익명 suspend 함수가 재실행된다.

코드 마지막에 `Thread.sleep()`이 없었다면 프로그램은 "Hello"를 출력하고 종료되었을 것이다. 왜냐하면 `delay` 함수는 메인 스레드를 block하지 않고, 함수를 suspend하기 떄문이다.

스레드만 사용해서 위 코드를 구현하면 다음과 같이 할 수 있다.

```kotlin
import kotlin.concurrent.thread

fun main() {
    thread {
        Thread.sleep(1000L)
        println("World")
    }

    println("Hello")
    Thread.sleep(2000L)
}
```

코루틴 버전과 다른점은 단지

> `GlobalScope.launch{}` -> `thread{}` \
  `delay()` -> `Thread.sleep()`

의 변경 뿐이다.

그렇다면, 코루틴을 사용해서 얻는 장점은 무엇일까?

먼저, 스레드는 무겁기 때문에 여러 개의 스레드를 사용하게 될 경우, 메모리 사용량이 커질 수 있다. 이는 애플리케이션의 확장성에 제한을 줄 수 밖에 없다. 예를 들어, 100,000개의 스레드를 생성하는 프로그램은 OutOfMemory Exception이 발생하지만, 동일한 개수의 코루틴은 잘 동작할 것이다.

**NOTE**: 코루틴은 스레드와 동등하지 않다. 코루틴은 스레드 위에서 동작한다.

### Suspend 함수
위에서 `launch`를 호출할 때 넘기는 익명 함수나 `delay` 함수를 suspend 함수라고 하였다. 코틀린에서는 함수를 suspendable하게 만드려면 fun 앞에 suspend 제어자만 붙여주면 된다. 이를 적음으로써 컴파일러에게 해당 함수는 suspend될 수 있고 나중에 resume될 수 있다는 것을 알려준다 - 즉, suspendable한 것이다.

그럼 왜 굳이 suspend 함수를 쓰는걸까?

다음의 예시를 보며 알아보자. 이는 RxJava나 다른 라이브러리를 쓰지않고 순수히 콜백함수를 통해 로직이 작성되었다.

```kotlin
fun makeLogin(id: String, password: String, callback: (Token) -> Unit) {
    // request login
    callback(token)
}

fun loadMovies(callback: (List<Movie>) -> Unit) {
    makeLogin("abc123", "qwerty") { token ->
        // request movies with token
        callback(movies)
    }
}

fun someFunction() {
    loadMovies { movies ->
        printMovies(movies)
    }
}
```

위 코드에서 우리는 어떠한 API로부터 영화 데이터를 가져오려고 한다. 그러나 그러기 전에, 요청을 승인해주는 토큰이 필요하다. 안드로이드 애플리케이션의 맥락으로 위 코드가 Retrofit의 enqueue로 request를 전송한다고 가정해보자. Request의 응답은 콜백 함수로 처리된다. 위 로직에 따르면, `makeLogin()`의 응답을 알려면 콜백 함수가 필요하고, `loadMovies()`의 응답을 알려고 해도 콜백 함수가 필요하다. 만약 추가로 다른 request가 필요할 경우, 또 다른 콜백 함수가 필요할 것이다. 더욱 더 복잡한 로직이 필요할 경우, 우리는 흔히 말하는 콜백 헬(Callback Hell)로 접어들 수 있다.

이러한 코드를 코루틴으로 변경해보자.

먼저, `makeLogin()`과 `loadMovies()`를 suspend 함수로 만든다.

`someFunction()`은 suspend 함수가 아니므로 `loadMovies()`를 호출할 수 없다고 불만을 표시할 것이다. 이를 해결하기 위해, `someFunction()`안에 코루틴을 생성하여 `loadMovies()`를 그 코루틴 안에서 호출할 것이다.

이제 콜백은 더 이상 필요하지 않다. 이를 없애고 토큰과 영화를 일반적인 함수에서 리턴하듯이 할 수 있다.

```kotlin
suspend fun makeLogin(id: String, password: String) {
    // request login
    return token
}

suspend fun loadMovies(token: Token) : List<Movies> {
    // request movies with token
    return movies
}

fun someFunction() {
    GlobalScope.launch {
        val token = makeLogin("abc123", "qwerty")
        val movies = loadMovies(token)
        printMovies(movies)
    }
}
```

Suspend 함수를 쓰도록 코드를 변경하였으니, 이전의 Retrofit의 Call과 enqueue대신 Deferred<> 혹은 suspend 구현체를 사용할 수 있다. 콜백 대신 suspend 함수로 코딩하니 더욱 깔끔하고 이해하기 쉬워졌다. `makeLogin()`, `loadMovies()`는 비동기 함수이고 몇 초 걸릴 수 있으나 동기 함수처럼 작성되있는 것이 특징이다.

`makeLogin()`이 호출되면 launch의 익명 suspend 함수인 `{}`는 suspend될 것이고 `makeLogin()`이 끝나면 익명 함수는 다시 이어간다. `loadMovies()`의 경우도 마찬가지다. 만약 `loadMovies()`안에 또 다른 suspend 함수가 있다면, 비슷한 원리로 해당 함수가 끝날 때 까지 `loadMovies()`는 suspend될 것이다.

### 코루틴 스코프
코루틴을 실행시키기 위해서는 코루틴 스코프라는게 존재해야한다는 것을 위 예시를 통해 볼 수 있었다. 근데 코루틴 스코프가 정확히 무엇인가? 이름 그대로 코루틴의 스코프(범위)이다.

가령, 수식 계산을 위한 프로젝트를 생각해보자. 수식을 계산하는 것을 UI 스레드/메인 스레드에서 하기는 비용이 비싸기 때문에 계산/연산을 하기 위해 코루틴을 사용할 수 있을 것이다. `mathScope`이라는 코루틴 스코프를 생성하여 이 스코프를 사용하여 수식 계산을 수행할 모든 코루틴을 생성해보자.

```kotlin
mathScope.launch {
    // calculate PI
}
```

이러한 특정 스코프를 사용함으로써 프로젝트에서 수행되는 모든 계산을 멈추고 싶다면 단지,

```kotlin
mathScope.cancel()
```

을 실행하면 된다. `mathScope`은 cancel되어 이 스코프 내에서 새로운 코루틴을 실행하려해도 아무런 코루틴이 실행되지 않을 것이다. 스코프를 사용하여 특정 코루틴들이 필요하지 않을 때 해당 스코프를 cancel하여 코루틴을 관리할 수 있다. 예를 들어, 안드로이드에서는 lifecycleScope와 viewModelScope가 있는데, 전자에서는 Activity/Fragment에서 작업을 수행하기 위한 스코프이고, 후자는 ViewModel에서 연산을 수행하기 위한 스코프이다.

### How does it work under the hood?
함수가 suspend되고 resume되는 것은 실제로 내부적으로 어떻게 가능한 것일까? 컴파일러가 suspendable한 함수를 어떻게 아는 것인가?에 대한 질문에 대해 알아보겠다.

다음과 같은 간단의 함수의 바이트코드와 디컴파일을 통한 Java 코드를 보자.

```kotlin
suspend fun loadMovies() {
}
```

위 코드를 바이트코드로 변환 후, 다시 Java 코드로 디컴파일 하면 다음과 같은 코드를 얻을 수 있다.

```java
@Metadata(
    mv = {1, 5, 1},
    k = 2,
    d1 = {"\u0000\n\n..."},
    d2 = {"loadMovies", "", "(Lkotlin/coroutines/Continuation;)Ljava/lang/Object;", ...}
)
public final class CoroutineKt {
    @Nullable
    public static final Object loadMovies(@NotNull Continuation $completion) {
        return Unit.INSTANCE;
    }
}
```

`loadMovies` 함수의 매개 변수로 `Continuation` 타입 변수를 볼 수 있다. 이는 일반 함수에는 없고, suspend 함수에서만 볼 수 있다. 코틀린 컴파일러는 suspend 제어자를 Continuation 타입의 변수 completion으로 대체하였다. 이 Continuation 타입을 통해 함수는 연산의 결과를 자신을 호출시킨 코루틴과 소통할 수 있게 된다. 따라서 이 Continuation 타입 변수를 통해 suspend 함수가 suspend되고 resume될 수 있는 것이다. 

결국에, 개발자가 콜백을 호출하는 것이 아닌, 컴파일러가 내부적으로 콜백을 호출하게 되는 것이다 - 컴파일러는 State Machine을 사용하여 이를 효율적인 방식으로 수행한다.

Continuation은 다름이 아닌 콜백에 대한 제너릭 인터페이스(generic interface)이다 - `CoroutineContext`와 함수 `resumeWith`를 가진 인터페이스이다.

```kotlin
interface Continuation<in T> {
    val context: CoroutineContext // continuation에서 사용될 CoroutineContext
    fun resumeWith(result: Result<T>) // suspend 함수를 재게하는 함수
}
```

다음과 같이 `setupMovies` 함수는 suspend 함수로써 3개의 다른 suspend 함수를 실행한다고 생각해보자.

```kotlin
suspend fun setupMovies() {
    val token = makeLogin("abc123", "qwerty")
    val movies = loadMovies(token)
    printMovies(movies)
}
```

코틀린 컴파일러는 suspend 제어자를 Continuation 타입의 completion 매개변수로 대체한다.
또한, `setupMovies`가 언제 suspend될 수 있는지 식별하고 그러한 각각의 suspension point는 Finite State Machine의 state(상태)로 나타낸다.

```kotlin
fun setupMovies(completion: Continuation<Any?>) {
    // Label 0: First execution
    val token = makeLogin("abc123", "qwerty")

    // Label 1: Resumes from makeLogin
    val movies = loadMovies(token)

    // Label 2: Resume from loadMovies
    printMovies(movies)

    // Label 3: Resumes from printMovies
    completion.resume(Unit)
}
```

`setupMovies()` 함수의 바이트코드를 디컴파일 해보면 다음과 같은 코드를 확인할 수 있다. (가독성을 위해 코틀린으로 작성하였다)

```kotlin
suspend fun setupMovies(completion: Continuation<Any?>) {
    when (label) {
        0 -> {
            val token = makeLogin("abc123", "qwerty")
        }
        1 -> {
            val movies = loadMovies(token)
        }
        2 -> {
            printMovies(movies)
        }
        3 -> {
            completion.resume(Unit)
        }
        else -> throw IllegalStateException(...)
    }
}
```

`setupMovies` 함수에 있는 각 suspend 함수는 when문의 케이스가 되었다. 또, `setupMovies` 함수를 재게하기 위한 하나의 케이스가 더 있고, 이 외의 값을 가진 label이 들어오는 것을 처리하기 위한 else 케이스가 있다.

label이 0일 경우, `makeLogin`이 실행되고 리턴 값을 token 변수에 대입한다.

label이 1일 경우, token 변수를 사용하여 `loadMovies`의 인자로 넘겨준다.

하지만, token 변수는 첫번 째 브랜치에서만 존재하므로 이는 불가능해야한다. 그럼 두번 째 브랜치에서 token 값에 어떻게 접근해야하는 걸까? - 해답은 Continuation 인터페이스에 있다.

When문의 각 브랜치는 State Machine의 state이다. 그럼 State Machine은 어떻게 생긴 것일까? 다음 코드를 보자.

```kotlin
fun setupMovies(completion: Continuation<Any?>) {
    class SetupMoviesStateMachine(completion: Continuation<Any?>) : CoroutineImpl(completion) {
        var token: Token? = null
        var movies: List<Movie>? = null

        var result: Any? = null
        var label: Int = 0

        override fun invokeSuspend(result: Any?) {
            this.result = result
            setupMovies(this)
        }
    }
}
```

State Machine은 token, movies, result, label과 같이 저장되어야 할 변수들은 가질 것이다.

* label 변수는 State Machine의 상태(state)를 나타낸다.
* token 및 movies 변수는 State Machine의 상태 중 하나의 결과를 갖는다.
* result 변수는 이전 상태의 결과를 나타내고, 최초에 null로 시작한다.

또한, State Machine은 `invokeSuspend` 함수를 가지는데, 이 함수는 현재 상태의 result 값으로 다음 suspend 함수를 호출한다.

위 정의된 State Machine은 다음과 같이 suspend 함수인 setupMovies에 적용될 것이다.

```kotlin
fun setupMovies(completion: Continuation<Any?>) {
    val continuation = completion as? SetupMoviesStateMachine
        ?: SetupMoviesStateMachine(completion)

    when (continuation.label) {
        0 -> {
            makeLogin("abc123", "qwerty", continuation)
            continuation.label = 1
        }
        1 -> {
            continuation.token = continuation.result as Token
            loadMovies(continuation.token, continuation)
            continuation.label = 2
        }
        2 -> {
            continuation.movies = continuation.result as List<Movie>
            printMovies(continuation.movies as List<Movie>, continuation)
            continuation.label = 3
        }
        3 -> {
            continuation.resume(Unit)
        }
        else -> throw IllegalStateException(...)
    }
}
```

`setupMovies`가 시작되고, 만약 completion 인자가 `SetupMoviesStateMachine`이 아니라면, completion 인자를 넘겨 새로운 `SetupMoviesStateMachine` 객체를 생성하고, 만약 맞다면 그 completion 인자를 쓴다 - State Machine을 continuation 변수에 저장한다.

`setupMovies`가 최초로 호출될 때, label은 0이다 (State Machine 정의 상 label 초기값). 따라서 switch문의 첫번 째 브랜치로 빠질 것이고, `makeLogin`함수가 호출될 것이다.
모든 suspend 함수에 대해 컴파일러는 Continuation 타입 인자를 추가하기 때문에, continuation을 `makeLogin`함수 실행 때 넘겨준다. 왜 continuation을 넘기냐면 `makeLogin`함수가 재게될 때, `invokeSuspend` 함수를 호출하여 자신의 result를 저장할 것인데, 이 result가 저장된 continuation으로 `setupMovies`를 실행할 수 있어야 하기 때문이다.

첫 번째 브랜치에서 label을 1로 바꾸기 때문에 `invokeSuspend`가 `setupMovies`를 다시 호출하게 되면 두 번째 브랜치로 빠지게 될 것이다. 이 브랜치에서는 State Machine의 token 변수에 result 변수(Token)을 저장하고 이 token값과 continuation을 넘겨 `loadMovies`를 호출한다. 이 때 continuation을 다시 넘김으로써 `loadMovies`는 `invokeSuspend` 함수를 호출하여 자신의 result를 저장한 continuation으로 `setupMovies`의 다음 브랜치로 넘어갈 수 있게 된다. 세 번째 브랜치도 마찬가지인 방식으로 진행된다.

네 번째 브랜치에서 우리는 `setupMovies` 함수를 재게한다. 만약 `setupMovies`에서 `printMovies` 함수 호출 후에 non-suspend 함수가 있었다면 그것 또한 이 네 번째 브랜치에 있었을 것이다.

예를 들어, 다음과 같이 `setupMovies`가 코딩되었다고 생각해보자.

```kotlin
suspend fun setupMovies() {
    val token = makeLogin("someValue", "someValue")
    val movies = loadMovies(token)
    printMovies(movies)
    println("Done")
}
```

여기서 `println`함수는 suspend 함수가 아니기 때문에 네 번째 브랜치는 다음과 같이 된다.

```kotlin
suspend fun setupMovies(completion: Continuation<Any?>) {
    val continuation = ...

    when (continuation.label) {
        ...
        3 -> {
            println("Done)
            continuation.resume(Unit)
        }
        ...
    }
}
```

`continuation.resume()`이 호출되면 suspend 함수는 재게된다. `loadMovies`가 재게되어 `setupMovies`를 다시 호출한 것 처럼, `setupMovies` 또한 재게되어 다른 suspend 함수를 부를 수 있다.

만약 위 전체 과정 중에 에러가 발생한다면 어떻게 처리될까? 단순하다. 우리는 단지 switch문의 각 브랜치에 한 줄의 에러 처리 코드만 추가해주면 된다.

```kotlin
suspend fun setupMovies(completion: Continuation<Any?>) {
    val continuation = completion as? SetupMoviesStateMachine 
	    ?: SetupMoviesStateMachine(completion)

    when(continuation.label) {
       0 -> {
          throwOnFailure(continuation.result)
          makeLogin("someValue", "someValue", continuation)
          continuation.label = 1
       }
       1 -> {
          throwOnFailure(continuation.result)
	  continuation.token = continuation.result as Token
          loadMovies(continuation.token, continuation)
          continuation.label = 2
       }
       2 -> {
          throwOnFailure(continuation.result)
	  continuation.movies = continuation.result as as List<Movie>
          printMovies(continuation.movies as List<Movie>, continuation)
          continuation.label = 3
       }
       3 -> {
          throwOnFailure(continuation.result)
          continuation.resume(Unit)
       }
       else -> throw IllegalStateException(...)
   } 
}
```

## 결론
함수가 suspend되었다가 resume되는 것은 마치 마법처럼 자연스럽게 수행되는 것 처럼 보인다. 하지만 내부적으로 보면 컴파일러가 suspension을 구현하기 위해 Finite State Machine을 통해 최적화된 콜백을 사용하는 것을 알 수 있다. 이러한 이유로 개발자가 일반적인 동기적인 코드를 적는데도 비동기적인 결과를 만들어낼 수 있다.


## 출처
https://medium.com/@viniciusviana_61216/a-deep-dive-into-kotlin-coroutines-a621d2978451

http://www.melconway.com/Home/pdf/compiler.pdf

