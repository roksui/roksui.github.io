---
title: Vert.x Basics(1) - Vert.x Introduction
---

**Recall**: Vert.x는 reactive 애플리케이션을 JVM에서 구현하도록 해주는 툴킷/플랫폼이다.

그렇다면, 이게 정확히 무슨 뜻이고, Vert.x의 내부 아키텍쳐는 어떻게 생겼는지 알아보자.

### Verticles
Vert.x는 Verticle이라는 컴포넌트들을 배포하고 실행할 수 있다. Java EE 모델에서 서블릿이나 message driven EJB와 비슷하다고 보면된다. 다음 그림은 Vert.x 플랫폼 안에 4개의 Verticle이 있는 간단한 구조이다.

![Simple Vertx](/assets/img/vertx_simple.png)

### The Event Bus
Verticle은 event driven하다. 즉, 메시지를 받지 않는 이상 실행하지 않는다는 말이다. Verticle들은 Vert.x의 Event Bus를 통해 서로와 통신할 수 있다.

![Event Bus](/assets/img/vertx_eventbus.png)

여기서 말하는 메시지는 필요에 맞게 Java 객체, 스트링, CSV, JSON, binary data 등이 될 수가 있다.

Verticle들은 address를 전송하고 listen할 수 있다. 여기서 말하는 address란 이름을 가진 채널과 같다. 메시지가 어떠한 address에 전송되면, 해당 address를 리슨하고 있는 모든 Verticle들은 메시지를 받게 된다. Verticle들은 발신자가 모르게 address를 구독 및 구독취소 할 수 있다. 따라서 메시지 발신자와 수신자 간의 결합도가 매우 루즈하게 형성된다.

모든 메시지 처리는 비동기적이다. 만약 한 Verticle이 다른 한 Verticle에게 메시지를 보낸다면, 그 메시지는 먼저 event bus에 올라가고, 발신자 Verticle은 통제권을 얻는다. 나중에 메시지가 디큐되고 보낸 메시지의 address를 리슨하고 있는 Verticle에게 전송된다.

### The Vert.x Thread Model
Verticle은 싱글 스레드 모드로 실행된다. 다시 말해, 한 Verticle은 언제나 싱글 스레드로 실행되고, 항상 그 똑같은 스레드에서 동작한다. 따라서 Verticle 안에서는 멀티스레딩을 할 생각을 안해도 된다는 말이된다.

Vert.x는 컴퓨터의 모든 CPU 코어를 사용할 수 있는 능력이 있다 - 이는 Vert.x가 각 CPU마다 하나의 스레드를 생성할 수 있기 때문이다. 각 스레드는 여러 개의 Verticle들에게 메시지를 보낼 수 있다. Verticle은 event driven이어서 메시지를 받을 때만 실행하기 때문에 자신만의 스레드를 가질 필요가 없다. 싱글 스레드가 여러 개의 Verticle들에게 메시지를 배분할 수 있다.

스레드가 Verticle에게 메시지를 전달할 때, Verticle의 해당 메시지 처리 코드는 스레드에 의해 실행된다. 메시지 전송 및 처리 로직은 Verticle이 register한 handler(listener 객체)안에 있는 메소드를 호출함으로써 실행된다. Verticle의 메시지 처리 로직이 끝나면 스레드는 또 다른 Verticle에게 미시지를 전송 할 수 있다.

쓰레드 기반의 그림은 다음과 같다.

![Thread Model](/assets/img/vertx_thread.png)

### Vert.x Services
Vert.x는 여러가지의 built-in 서비스(functionality)를 포함하고 있다. 예를 들어,
- HTTP server
- JDBC connector
- SMTP Mail
- Message queue connectors
등이 있다.

## 출처
http://tutorials.jenkov.com/vert.x/overview.html