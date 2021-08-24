---
title: Reactive Programming 및 Vert.x 소개
---

## Reactive Programming이 뭐야?

**Reactive Programming is programming with asynchronous data streams.**
즉, Reactive Programming은 비동기적 데이터 스트림으로 프로그래밍하는 것을 말한다. 

따지고 보면, 새로운 것도 아니다. Event bus나 click event는 비동기 이벤트 스트림이고, 이를 관찰하고 특정한 액션을 취할 수 있다.

자, 이제 Reactive는 이러한 아이디어를 한 층 업그레이드 시킨다.

click이나 hover 이벤트 외에도 그 어떠한 것에 대한 데이터 스트림을 만들 수 있다. 변수, 유저 입력값, 속성, 캐시, 데이터 구조, 등등 다양한 것들이 데이터 스트림이 된다.

더 나아가, 이러한 스트림들을 합치고, 만들고, 필터링할 수 있는 다양한 함수들이 주어진다.

자, 이제 여기서 "함수형"(functional)이란 마법이 들어오게 된다. 하나의 스트림은 다른 스트림의 input이 될 수 있고, 여러개의 스트림이 input이 될 수도 있다. 

두 개의 스트림을 병합할 수도 있고, 특정 스트림을 필터링하여 관심 있는 이벤트들로만 구성된 스트림으로 만들 수 있고, 한 개의 스트림의 데이터 값들을 다른 하나의 스트림을 매핑할 수도 있다.

그렇다면, 이 스트림이라는 것이 Reactive의 핵심인데, 좀 더 자세히 살펴보자.

버튼 클릭 이벤트 스트림을 예로 들어보자.

![Button Click Event Stream](/assets/img/reactive_btn_click.png)

스트림이란 시간순으로 나열된 이벤트들의 시퀀스이다.

스트림은 세 가지 다른 것들을 내보낼 수 있다.\
1) 어떠한 타입의 값
2) 에러
3) 완료 시그널

여기서, 완료된다는 것은 예를 들어 현재 윈도우 창이나 버튼을 포함하고 있는 뷰가 종료될 때이다.

위 세 가지가 내보내지는 것에 대한 함수를 각각 정의하여 내보내진 이벤트들은 **비동기적**으로 캡쳐된다.

이런식으로 스트림을 listen하는 것을 **subscribing**이라고 한다. 그리고 함수들은 **observer**가 된다. 스트림은 관찰당하는 주체 이므로 **observable**이라고 칭한다. 이것이 Observer Design Pattern이다.

자, 이제 위 클릭 이벤트 스트림을 변환하여 새로운 클릭 이벤트 스트림을 만들어보자.

먼저, 버튼이 몇 번 클릭되었는지를 의미하는 counterStream을 만들어보자. 대부분의 Reactive 라이브러리에는 스트림에 대해 여러 개의 함수들이 있다 (map, filter, scan, 등). 만약 `clickStream.map(f)`를 호출하면 clickStream을 기반으로 하는 새로운 스트림을 리턴한다. 여기서 clickStream은 변경되지 않는다.

이러한 immutability 속성은 Reactive 스트림과 잘 어울려 사용된다.

예를 들어 `clickStream.map(f).scan(g)`와 같은 체인 함수를 사용할 수도 있다.

```
  clickStream: ---c----c--c----c------c-->
               vvvvv map(c becomes 1) vvvv
               ---1----1--1----1------1-->
               vvvvvvvvv scan(+) vvvvvvvvv
counterStream: ---1----2--3----4------5-->
```

`map(f)`는 내보내진 값들을 함수 f에 적용하여 새로운 스트림을 만든다. 위의 경우 모든 내보내진 값을 1로 매핑하였다.

그리고 `scan(g)`는 스트림에서의 모든 이전 값들을 가져와서 `x = g(accumulated, current)`를 수행한다. 위의 경우 `g`는 단순히 더하는 함수이다. 이로 인해, counterStream은 클릭이 발생했을 때, 총 클릭 수를 내보낸다.

Reactive의 힘을 더 알아보기 위해, 더블 클릭 이벤트 스트림을 가지고 싶다고 가정하자. 그리고 새로운 스트림이 더블클릭 or 그 이상을 동일하게 처리하고 싶다고 여겨보자. 즉, 트리플클릭도 더블클릭으로 인식되어야 한다.











위 내용은 Adre Staltz가 Github에 올린 내용을 기반으로 한다. 그는 자신이 그동안 읽은 Reactive Programming에 대한 자료를 보며 신입 개발자들이 접근하기 힘든 부분이 많은 것을 느껴, 이를 좀 더 간결하고 명확하게 풀어서 설명을 해준다. 원문을 읽고 싶다면 출처를 참고해도 좋겠다.










출처: https://gist.github.com/staltz/868e7e9bc2a7b8c1f754