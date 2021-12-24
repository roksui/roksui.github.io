---
title: "JVM and Class Loading (1)"
---

## 개요
Dynamic Instrumentation 코어 모듈을 개발하는 과정에서 WebGoat를 대상으로 instrumentation를 시도해보았다. Javassist 라이브러리를 통해 메소드를 transform하는 방식으로 진행하는데, Spring Boot에서 cglib로 동적으로 생성되는 바이트코드에 대한 classpath를 Javassist가 찾지 못하는 것 같았다 - NotFoundException 발생. 그렇게 원인을 찾던 중, JVM Class loading 메커니즘은 물론이고 Spring Boot가 돌아가는 방식에 대해 잘 모르고 있기 떄문에 디버깅이 오래 걸리고 있다. 따라서 이 글에서는 JVM 클래스 로딩에 대해 한 번 자세히 알아보려고 한다.

## JVM Revision

클래스 로딩에 대해 파고들기 전에 먼저 Virtual Machine과 Java Virtual Machine의 아키텍처에 대해 다시 짚고 넘어가자.

### Virtual Machine (VM)이 무엇인가?
가상 머신은 말 그대로 물리적인 기계처럼 연산을 수행할 수 있는 기계를 simulate하는 소프트웨어이다.

크게 두 가지 종류의 VM이 있다.<br>
1. 하드웨어 기반 or 시스템 기반 VM
2. 소프트웨어 기반 or 애플리케이션 기반 or 프로세스 기반 VM

하드웨어 기반 VM의 예시로,<br>
- KVM (Kernel-based virtual machine) for Linux systems
- VMware
- Xen
- Cloud computing
등이 있고, 