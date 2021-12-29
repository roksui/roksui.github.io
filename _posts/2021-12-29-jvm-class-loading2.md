---
title: "JVM and Class Loading (2)"
---

이전 글에서는 JVM의 구조와 그것의 구성요소 중 하나인 Class Loader sub-system의 역할을 알아보았다.

이번 글에서는 클래스 로더(Class Loader)의 종류에 대해 알아보자. 

## 개요
Java에서 동적 로딩을 가능하게 해주는 메커니즘은 클래스 로더인데, 이는 Java dynamism의 토대 중 하나이다.

클래스 로더는 실행 중인 Java 환경에 클래스들을 언제, 그리고 어떻게 추가될 수 있는지를 결정해주고, Java 런타임 환경의 중요한 부분들이 악의적인 코드로 대체되지 않도록 해준다. 신뢰하지 못하는 외부 코드를 실행하기 전, 디지털 시그니처를 확인하는 커스텀 클래스 로더를 작성할 수도 있다.

## 클래스 로더의 종류
Class Loader sub-system에는 다음의 세 가지 종류의 클래스 로더를 가지고 있다.
1. Bootstrap Class Loader or primordial Class Loader
2. Extension Class Loader
3. Application Class Loader or System Class Loader

### Bootstrap Class Loader
- Core Java API 클래스들을 로딩해준다. i.e. rt.jar (jdk\jre\lib\rt.jar)에 있는 클래스들
- 이 위치를 bootstrap class path라고 부른다. i.e. Bootstrap 클래스 로더는 bootstrap class path에 있는 클래스들을 로딩해준다.
- Bootstrap 클래스 로더는 디폴트로 모든 JVM에서 가용하다.
- Bootstrap 클래스 로더는 Java 언어가 아닌 네이티브 언어인 C/C++ 언어로 구현되었다.

### Extension Class Loader
