---
title: "Android - Coroutines"
---

본 글은 Google의 Android 개발자 Sean McQuillan님의 글을 참고하여 정리한 내용이다.

## Coroutine이 무엇인가?
코루틴의 개념은 최근의 것이 아니고 1967년도에 Simula라는 언어에서 처음 등장하였다. 그만큼 오래된 개념이지만, Kotlin 1.3에서 처음 소개되었고 코틀린 코루틴은 concurrency를 다를 수 있는 새로운 기법을 제공한다.

안드로이드에서 코루틴은 두 가지 문제를 해결해준다.

1. Long running tasks 방지

2. Main-safety 제공

각각의 이슈를 좀 더 자세히 살펴보겠다.

### Long running tasks
웹페이지를 가져오거나 API를 호출하는 것은 네트워크 요청을 하는 것을 의미한다. 또, 데이터베이스에서 데이터를 가져오는 것도 파일을 읽는 작업을 거친다. 이러한 과정들을 소위 말하는 long running tasks라고 한다. 즉, 앱을 잠시 멈춘 후 이러한 작업들을 진행하기에는 너무나 오래 걸리게 된다.

현대의 휴대폰이 네트워크 요청에 비해 얼마나 빠르게 작동하는지 가늠하기 어려울 수 있다. 예를 들어, Pixel 2에서는 단 한번의 CPU 사이클은 약 0.0000000004초가 걸리는데, 비교하자면 네트워크 요청은 0.4초 정도 걸린다고 보면 된다.

안드로이드에서는 모든 앱에 메인 스레드가 존재하고, 이는 UI(예. View를 그리는 것)를 담당한다. 만약 이 스레드에서 너무나 많은 일들이 일어나면 앱이 매우 느리게 동작하는 것 처럼 보여, 안 좋은 UX를 제공할 것이다.

따라서, long running task는 메인 스레드를 block하지 않고 진행되는게 이상적일 것이다.



















## 출처
https://medium.com/androiddevelopers/coroutines-on-android-part-i-getting-the-background-3e0e54d20bb