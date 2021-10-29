---
title: JMX Technology (1)
---

**NOTE**: 해당 글에 있는 JMX Technology 내용은 오라클의 Java Documentation을 출처로 하고, 튜토리얼은 JDK8을 기반으로 작성된 것입니다.  

## 개요
Java Management Extensions (JMX) technology는 Java Platform, Standard Edition (J2SE) 5.0 버전에서 처음 추가된 구성요소이다.

JMX는 애플리케이션, 디바이스 및 서비스와 같은 자원을 관리할 수 있는 간단하고 기본적인 툴을 제공해준다. JMX는 동적이기 때문에 자원이 생성되고, 배치되고 구현되는 순간에 바로바로 모니터링하고 관리할 수 있다. JVM 또한 모니터링 및 관리를 할 수 있다.

JMX Specification은 애플리케이션 및 네트워크를 Java 언어로 모니터링/관리하기 위한 아키텍쳐, 디자인 패턴, API 및 서비스들을 정의한다.

JMX 기술을 통해 주어진 자원은 한 개 이상의 Managed Bean(or MBean)이라는 자바 객체로 instrument된다. 이러한 MBean들은 코어에서 관리되는 객체 서버인 MBean server에 등록된다. MBean 서버는 관리 에이전트로 동작하여 자바 언어가 실행될 수 있는 대부분의 디바이스에서 작동할 수 있다.

JMX Specification은 자원을 관리하기 위해 사용되는 JMX 에이전트를 정의해주고, 이 에이전트는 MBean들이 등록되어 있는 MBean 서버와 MBean을 핸들링하는 여러개의 서비스들로 구성되어 있다. 이러한 방식을 통해 JMX 에이전트는 자원을 직접적으로 통제하고 리모트 관리 애플리케이션에 전달을 해줄 수 있다.

자원이 instrument되는 방식은 관리 인프라에 종속되지 않는다. 즉, 자원은 관리 애플리케이션이 어떻게 구현되어 있는지와 상관없이 관리 가능하도록 만들어질 수 있다.

JMX 기술은 표준 커넥터(JMX connectors)라는 것을 정의하는데, 이는 리모트 관리 애플리케이션에서 JMX 에이전트에 접근할 수 있도록 해준다. JMX 커넥터들이 다른 프로토콜을 사용하더라도 동일한 관리 인터페이스를 제공한다. 따라서 관리 애플리케이션은 통신 프로토콜과 상관없이 자원을 투명적으로 관리할 수 있다.

또, 특정 시스템이나 애플리케이션이 JMX Specification에 따르지 않아도 JMX 에이전트를 지원한다면, JMX 에이전트를 사용할 수 있다.

### Why use JMX?
여러가지의 장점이 있겠지만, JMX는 Java 애플리케이션을 유연하게 instrument할 수 있게 해주는 가장 큰 장점이 있다.

즉, JMX는 큰 부담 없이 Java 애플리케이션을 관리할 수 있게 해준다.<br>
JMX 기반 에이전트 (JMX 에이전트)는 대부분의 Java가 활성화된 디바이스에서 실행될 수 있다. 따라서 Java 애플리케이션은 그것의 디자인에 별 큰 영향을 받지 않고 관리될 수 있다. 애플리케이션은 managed object server만 추가시켜, 애플리케이션의 기능들을 한개 혹은 여러개의 MBean들의 형태로 이 object server에 등록하면 된다.
 
## JMX의 아키텍쳐
JMX 기술은 세 개의 레벨로 나뉠 수 있다.
- Instrumentation
- JMX agent
- Remote management

### Instrumentation
JMX를 통해 자원을 관리하기 위해서는 해당 자원을 Java 언어로 instrument해야한다. 이를 구현하기 위해 MBeans라는 Java 객체를 사용한다. 그리고 MBean은 JMX specification에 정의된 디자인 패턴과 인터페이스를 따라야 한다. 이렇게 함으로써 모든 MBean들이 표준화된 방식으로 자원 instrumentation을 할 수 있다.

자원이 MBean들을 통해 instrument되었다면, 그 자원은 JMX agent를 통해 관리될 수 있다. 이 때, MBean은 JMX agent에 대한 내용을 알 필요가 없다. MBean은 유연하고, 간단하고 쉽게 구현할 수 있도록 설계되었다. 추가로, JMX specification의 instrumentation 레벨에서는 notification 메커니즘을 제공한다. 이 메커니즘은 MBean들이 다른 레벨의 구성요소들에게 notification 이벤트를 전달할 수 있게 해준다.

### JMX Agent
JMX Agent는 표준 관리 에이전트로써 자원을 직접적으로 관리하여 remote 관리 애플리케이션이 이를 사용할 수 있도록 해준다. JMX agent는 대체로 그것이 관리하는 자원들이 있는 같은 기기에 위치해 있지만, 이게 필수 조건은 아니다.

JMX agent의 핵심 구성요소는 MBean server이다. 이는 MBean들이 등록되는 managed object server이다. 또, JMX agent에는 MBeans들을 관리하는 여러개의 service들, 그리고 원격 애플리케이션과 통신할 수 있는 최소한 하나의 통신 어댑터, 혹은 connector가 포함되어 있다.

JMX agent를 구현할 때, 우리는(개발자들은) 그것이 관리하게 될 자원의 semantics나 functions를 알 필요가 없다. 사실 JMX agent는 자신이 다루게 될 것이 어떤 자원인지도 알 필요가 없다. 왜냐하면 JMX specification에 따라 instrument된 자원은 그 어떠한 JMX agent를 사용해도 잘 동작 할 것이기 떄문이다. 또, JMX agent는 자신이 접근하게 될 관리 애플리케이션의 기능에 대해 알 필요가 없다.

### Remote Management
JMX 기술의 instrumentation은 여러가지 방법으로 접근될 수 있다. 이미 구현된 관리 프로토콜 (SNMP - Simple Network Management Protocol), 혹은 proprietary protocols를 통해 접근 될 수 있다. MBean server는 관리 애플리케이션이 JMX agent의 JVM 밖에서도 agent에 접근할 수 있도록 프로토콜 어댑터나 커넥터에 의존한다.

각 어댑터는 MBean server에 등록된 모든 MBean들에 대한 특정 프로토콜을 제공한다. 예를 들어 HTML 어댑터는 MBean을 브라우저에 표시할 수 있을 것이다.

커넥터는 관리자와 JMX agent 사이의 통신을 핸들링하는 관리자 측 인터페이스를 제공한다. 각 커넥터는 서로 다른 프로토콜을 통해 동일한 remote 관리 인터페이스를 제공한다. Remote 관리 애플리케이션이 이 인터페이스를 사용할 때, 그 애플리케이션은 프로토콜과 상관 없이 네트워크를 통해 투명하게 JMX agent에 연결할 수 있다. JMX 기술은 Java Remote Method Invocation (Java RMI)을 바탕으로 JMX 기술 instrumentation을 remote 애플리케이션에 내보낼 수 있는 표준 솔루션을 제공한다.

## 출처
Overview of the JMX Technology, The Java Tutorials, Java Documentation, Oracle