---
title: JMX Mbeans
---

## MBeans 소개
이 글에서는 JMX API의 핵심 개념인 managed beans (MBeans)에 대해 소개해보고자 한다.

MBean이란 관리되는(managed) Java 객체로써 JMX specification에 정의된 디자인 패턴을 따르는 구성요소이다. (JavaBeans 구성요소와 비슷하다)

MBean은 하나의 디바이스, 애플리케이션, 혹은 관리되어야 할 자원을 나타낼 수 있다.

MBean들은 다음을 포함하는 관리 인터페이스를 가진다.
- A set of readable or writable attributes, or both
- A set of invokable operations
- A self-description

관리 인터페이스는 MBean 인스턴스의 라이프 사이클 동안 바뀌지 않는다. 또, MBean은 특정하게 미리 정의된 이벤트가 발생할 때 notification을 내보낼 수(emit) 있다.

JMX specification에서는 다섯 가지 종류의 MBean을 정의한다:
- Standard MBeans
- Dynamic MBeans
- Open MBeans
- Model MBeans
- MXBeans

이 글에서는 가장 단순한 종류의 MBean인 standard MBeans와 MXBeans를 살펴볼 것이다.

## Standard MBeans
Standard MBean은 `SomethingMBean`이라는 Java 인터페이스와, 그것을 구현하는  `Something`이라는 Java 클래스를 만듦으로써 정의된다.

이 인터페이스에서 정의되는 모든 메소드는 MBean 상에 있는 속성(attribute)이나 연산(operation)을 정의한다. 기본적으로 모든 메소드는 연산을 정의한다. 속성과 연산은 특정한 디자인 패턴을 따르는 메소드이다.

Standard MBean은 MBean 인터페이스와 클래스로 구성되어있으며, 인터페이스는 가용한 속성 및 연산들의 메소드를 리스트하고, 클래스는 인터페이스를 구현하고 instrument되는 자원의 functionality를 제공한다.

다음은 standard MBean과 이를 관리하는 JMX agent에 대한 예시이다.

**MBean Interface**
```java
public interface HelloMBean {
    public void sayHello();
    public int add(int x, int y);

    public String getName();

    public int getCacheSize();
    public void setCacheSize(int size);
}
```

이 인터페이스에서는 두 개의 연산: `add()`와 `sayHello()`를 선언하고 있다.
또, `Name`이라는 read-only 스트링과 `CacheSize`라는 read and write가 가능한 integer를 속성으로 선언하고 있다.

관리되는 애플리케이션이 속성 값에 접근하고 필요시 변경할 수 있도록 getter 및 setter 메소드들이 인터페이스에 정의된다. JMX specification에 정의되어 있듯이, *getter*는 void를 리턴하지 않고 이름이 get으로 시작하는 public 메소드이다. Getter는 관리자가 속성의 값을 읽을 수 있게 해준다. *Setter*는 한 개의 매개변수를 받고 이름이 set으로 시작하는 public 메소드이다. Setter는 관리자가 속성에 새로운 값을 write할 수 있게 해준다.

**MBean Implementation**
```java
public class Hello implements HelloBean {
    public void sayHello() {
        System.out.println("hello world");
    }

    public int add(int x, int y) {
        return x + y;
    }

    public String getName() {
        return this.name;
    }

    public int getCacheSize() {
        return this.cacheSize;
    }

    public synchronized void setCacheSize(int size) {
        ...
        this.cacheSize = size;
        System.out.println("Cache size now " + this.cacheSize);
    }
    ...

    private final String name = "Reginald";
    private int cacheSize = DEFAULT_CACHE_SIZE;
    private static final int DEFAULT_CACHE_SIZE = 200;
}
```

구현체이므로 `HelloMBean`에서 선언된 연산 및 속성들을 정의하고 있다.
여기서 `Name`이라는 속성 값은 바뀌지 않지만, 실제로는 관리되는 자원이 실행되면서 속성 값이 바뀔 수도 있다. 예를 들어 uptime 및 메모리 사용량 등을 나타내는 속성 값은 바뀌기 마련이다.

이제 MBean 인터페이스와 구현체가 정의되었으므로, 그것들이 나타내는 자원을 관리해보자.

### 자원을 관리하기 위해 JMX Agent 생성하기
자원이 MBean에 의해 instrument되었다면, 자원의 관리 자체는 JMX agent가 맡는다.

JMX agent의 핵심 구성요소는 MBean 서버이다. MBean 서버는 managed object server로써 MBean들이 등록되는 곳이라는 것을 기억하자. 또, JMX agent에는 MBean들을 관리하기 위한 여러 service들이 포함된다. 

다음은 기본 JMX agent 구현 방법이다.

```java
package com.example;

import java.lang.management.*;
import javax.management.*;

public class Main {
    public static void main(String[] args) throws Exception {
        MBeanServer mbs = ManagementFactory.getPlatformMBeanServer();
        ObjectName name = new ObjectName("com.example:type=Hello");
        Hello mbean = new Hello();
        mbs.registerMBean(mbean, name);

        ...

        System.out.println("Waiting forever...");
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

첫번째로 JMX agent `Main`은 `java.lang.management.ManagementFactory`클래스에 있는 `getPlatformMBeanServer()`메소드를 통해 플랫폼에 의해 생성되고 초기화된 MBean server를 가져온다. 만약 플랫폼에 의해 만들어진 MBean server가 없을 경우, 해당 메소드는 JMX 메소드인 `MBeanServerFactory.createMBeanServer()`를 호출하여 MBean server를 만든다.

그 다음에, `Main`은 만들어질 MBean instance의 name을 정의한다. 모든 JMX MBean은 object name을 가져야 한다. Object name은 JMX 클래스 `ObjectName`의 인스턴스이고, JMX specification에 정의된 syntax를 지켜야 한다 - object name은 domain과 key-properties 리스트를 포함해야 한다. 위 예시에서는 domain이 com.example (MBean이 포함된 패키지)이고 key-property에는 object의 type이 Hello라고 선언하고 있다.

`Hello` 인스턴스가 생성되고 `mbean`이라는 이름을 가진다. 그 후, JMX 메소드인 `MBeanServer.registerMBean()`을 통해 MBean server `mbs`에 `name`이라는 object 이름으로 등록된다.

이제 `Hello` MBean이 MBean server에 등록되었으므로 `Main`은 단순히 `Hello`에서 관리 연산이 수행되기를 기다린다. 이 예시에서 관리 연산은 `sayHello()`나 `add()`, 혹은 속성 값에 대한 getter 및 setter 메소드들이 호출되는 것이다.