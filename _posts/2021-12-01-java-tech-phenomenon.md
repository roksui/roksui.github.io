---
title: "The Java Technology Phenomenon"
---

## Java 기초 시리즈를 시작하며
Java를 공부하고 사용한지 1년쯤이 되어간다. 그러나 항상 필요한 부분만 검색해서 구현하고 찾아보고하였지, 제대로 언어를 ground-up하게 배우지 않은 것 같아, 초심으로 돌아가 Java라는 언어를 학습하고자 이 글을 시작으로 Java 시리즈를 작성하게 되었다. 그리고 이를 위해 가장 최고의 참고자료는 오라클 공식 문서라고 생각하였다. Java로 개발을 하면서 그동안 당연시 여겨왔던 것들이 실제로 왜 그렇고 어떻게 동작하는지를 중점으로 더 깊이있게 알아보고자 한다.

## Java라는 기술에 대해서
Java 기술은 프로그래밍 언어이자 하나의 플랫폼이다.

### Java 프로그래밍 언어
Java 프로그래밍 언어는 high-level 언어로써 다음과 같은 용어들로 설명되곤 한다.
- 단순한 (Simple)
- 객체지향적인 (Object oriented)
- 분산된 (Distributed)
- 멀티쓰레드 (Multithreaded)
- 동적인 (Dynamic)
- 플랫폼 독립적 (Architecture neutral)
- 휴대성 높은 (Portable)
- 높은 성능 (High Performance)
- 안전한 (Secure)
- 견고한 (Robust)

위에 대한 특징들은 James Gosling과 Henry McGilton이 작성한 white paper [The Java Language Environment](https://www.oracle.com/java/technologies/language-environment.html)에 설명되어 있다.

Java 언어에서는 모든 소스코드가 `.java` 확장자를 갖는 plain 텍스트 파일안에 먼저 작성된다. 그리고 해당 소스 파일은 javac 컴파일러에 의해 `.class` 파일로 *컴파일*된다. 이 때, `.class` 파일은 우리의 프로세서가 이해할 수 있는 코드로 구성되어있지 않고 *바이트코드*로 이루어져 있다. 여기서 바이트코드(bytecode)는 JVM의 기계어이다. 그 후, java 런처 툴이 JVM의 인스턴스를 통해 우리의 어플리케이션을 실행해준다.

![Java 소스코드 실행 프로세스](/assets/img/java-run-process.gif)

JVM은 여러 다른 운영체제에서 사용 가능하기 때문에, 똑같은 `.class`파일을 마이크로소프트 윈도우, 솔라리스 OS, 리눅스, 혹은 Mac OS에서 다 실행가능하다. 이러한 JVM 중에서도 몇몇 VM, 예를 들어 Java SE HotSpot at a Glance는 런타임 시 추가적인 단계를 수행하여 어플리케이션 성능을 개선시켜준다 - 성능 병목현상을 찾거나 자주 사용되는 코드를 네이티브 코드로 리컴파일하는 태스크를 수행한다.

![JVM 가용성](/assets/img/jvm-availability.gif)

위 그림처럼 JVM을 통해 동일한 어플리케이션을 여러 개의 플랫폼에서 실행할 수 있다. 여기서 Architecture neutral의 특징이 나오는 것 같다.

### Java 플랫폼
*플랫폼*이란 프로그램이 실행되는 하드웨어 및 소프트웨어 환경이다. 위에 언급한 마이크로소프트 윈도우, 리눅스, 솔라리스 OS 등도 가장 인기있는 플랫폼들이다. 대체로 플랫폼이라 함은, 운영체제(OS)와 그 밑에 있는 하드웨어의 조합이라 말할 수 있다.

Java 플랫폼은 두 개의 구성요소가 있다.
- Java Virtual Machine (JVM)
- Java Application Programming Interface (API)

이미 언급된 JVM은 Java 플랫폼의 기반이고 다양한 하드웨어-기반 플랫폼 위에 올라탄다.

![Java Platform](/assets/img/java-platform.gif)

API와 JVM, 즉 Java 플랫폼이 프로그램과 기반 하드웨어 사이에 위치한다.

API란 여러가지 유용한 기능을 제공하는 바로 사용가능한(ready-made) 소프트웨어 컴포넌트들의 거대한 컬렉션이다. API에는 서로 관련된 클래스 및 인터페이스들을 라이브러리로 그룹화되어 있다. 이러한 라이브러리를 *패키지(package)*라고 부른다. API는 Java 언어의 핵심 기능을 제공하고, 코어 API는 매우 방대하다.

플랫폼 독립적 환경이기 때문에 Java 플랫폼은 네이티브 코드보다 조금 느릴 수는 있다. 그러나 컴파일러와 VM 기술의 발전으로 인해 휴대성에 위협을 가하지 않고 네이티브 코드와 비슷할 정도의 성능이 실현 가능하다.

## 출처
The Java Tutorials Trail, Oracle Java Documentation