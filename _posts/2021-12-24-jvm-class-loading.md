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
등이 있고, 하드웨어 기반 VM의 주 장점은 하드웨어 자원을 효과적으로 활용할 수 있다는 점이다.

소프트웨어 기반 혹은 애플리케이션 기반 VM은 특정 프로그래밍 언어로 작성된 애플리케이션을 실행하기 위한 런타임 엔진 역할을 한다.

예시로,<br>
- JVM은 Java 애플리케이션을 위한 런타임 엔진 역할을 한다.
- PVM은 PERL과 같은 스트립트 언어를 위한 런타임 엔진 역할을 한다.
- CLR (Common Language RUNTIME)은 .NET 기반 애플리케이션을 실행하기 위한 런타임 엔진 역할을 한다.

그럼 JVM을 알아보자. 먼저,<br>
1. JVM은 JRE의 일부분이다.
2. JVM은 Java 애플리케이션을 로딩하고 실행하는 것을 담당한다.

### JVM Architecture

JVM 아키텍처는 다음 그림과 같다.

![jvm-architecture](/assets/img/jvm-architecture.png)

각 구성요소를 하나하나 살펴보자.

#### ClassLoader sub-system
ClassLoader sub-system은 JVM 아키텍처의 중요한 구성요소로써 다음 3가지 활동을 책임진다.

1. Loading
2. Linking
    1. Veritifaction
    2. Preparation
    3. Resolution
3. Initialization

1. **Loading**
Loading이란 클래스 파일들을 읽어서 그에 대응되는 바이너리 데이터를 method area에 저장하는 것을 의미한다.

각 클래스에 대해 JVM은 다음과 같은 정보를 method area에 저장한다:
1. 로딩된 클래스, 인터페이스, 혹은 Enum의 fully qualified name
2. 그 클래스의 부모 클래스의 fully qualified name
3. .class 파일이 Class, Interface, Enum 중 어떤 것과 관련이 있는지
4. 제어자 정보
5. 변수 혹은 필드 정보
6. 메소드 정보
7. Constant pool 정보 등등

.class 파일을 로딩한 후, JVM은 바로 type Class의 객체(주어진 클래스에 대한 메타 데이터를 가지는 Class 타입의 객체)를 생성할 것이다. 클래스 레벨 바이너리 정보를 가지는 이 Class 객체를 Heap Memory에 저장한다.

![jvm-class-loading](/assets/img/jvm-class-loading.png)

이 Class 객체를 통해 개발자들은 클래스 레벨 정보 (FQN, 메소드, 필드 정보 등)를 얻을 수 있다. 다음 예시를 보자.

```java
import java.lang.reflect.Field;
import java.lang.reflect.Method;

class Student {
    private String firstName;
    private String lastName;
    private int rollNumber;

    public String getFirstName() {
        return firstName;
    }

    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }

    public String getLastName() {
		return lastName;
	}

	public void setLastName(String lastName) {
		this.lastName = lastName;
	}

	public int getRollNumber() {
		return rollNumber;
	}

	public void setRollNumber(int rollNumber) {
		this.rollNumber = rollNumber;
	}
}

public class StudentInformation {

    public static void main(String[] args) {
        
        Student student = new Student();
        
        Class c = student.getClass();
        System.out.println(c.getName());

        Method[] m = c.getDeclaredMethods();
        for (int i = 0; i < m.length; i++) {
            System.out.println(m[i]);
        }

        Field[] f = c.getDeclaredFields();
        for (int i = 0; i < f.length; i++) {
            System.out.println(f[i]);
        }
    }
}
```

Output:
```
com.example.Student
public void com.example.Student.setFirstName(java.lang.String)
public void com.example.Student.setRollNumber(int)
public void com.example.Student.setLastName(java.lang.String)
public java.lang.String com.example.Student.getLastName()   
public java.lang.String com.example.Student.getFirstName()   
public int com.example.Student.getRollNumber()   
private java.lang.String com.example.Student.firstName   
private java.lang.String com.example.Student.lastName   
private int com.example.Student.rollNumber
```

**NOTE**: `.class`를 사용하면, 예를 들어 `String.class` or `Student.class`, 이는 주어진 클래스에 대한 메타 데이터를 가지고 있는 클래스 리터럴, 즉 `java.lang.Class` 객체를 참조한다. 이는 위 예시에서 사용한 `getClass()`와 같은 객체를 리턴한다. 다른점은 `getClass()`는 주어진 클래스의 인스턴스에 호출할 수 있고, `.class`는 인스턴스가 없어도 사용할 수 있다. 결국에 이로 인해 리턴되는 타입은 `Class<?>`인 것이다.

로딩된 각 .class 파일에 대해서는 단 하나의 Class 객체만 생성된다.

2. **Linking**
ClassLoder sub-system의 두 번째 활동은 Linking이다. Linking에는 세 가지 활동이 있다.

    1. Verification
    Verification은 클래스의 바이너리 형태가 구조적으로 올바른지를 확인한다. 따라서 JVM은 .class file이 올바른 컴파일러로부터 생성되었는지, 올바른 포맷을 갖고 있는지를 확인한다. 내부적으로 바이트코드 검증기가 이를 수행한다.

    그럼 만약 검증을 통과하지 못한다면? - Runtime Exception이 발생한다 (java.lang.Verify.Error).

    2. Preparation
    Preparation 단계에서 JVM은 class-level static 변수에 대한 메모리를 할당하고 디폴트 값을 부여한다.

    3. Resolution
    Resolution 단계에서는 로딩된 타입에 의해 사용되는 symbolic reference를 원래의 reference로 바꾸는 과정을 거친다. Symbolic reference에 대응하는 direct reference를 Method area를 탐색하여 찾아낸다. 예를 들어, 다음과 같은 코드 스니펫이 있다고 가정하자.

    ```java
    class Demo {
        public static void main(String... args) {
            String name = new String("John");
            Student s1 = new Student();
        }
    }
    ```

    ClassLoader sub-system은 위 코드를 읽으며 `Demo.class`, `String.class`, 'Student.class`, 그리고 `Object.class`(parent class)를 로딩할 것이다. 그리고 이 클래스들의 이름은 이 특정 클래스 "Demo"의 Constant Pool에 저장된다. Constant Pool은 .class 파일의 일부분으로써 해당 클래스의 코드를 실행하기 위해 필요한 constant들을 포함한다.

    여기서 constant는 개발자에 의해 명시된 리터럴과 컴파일러에 의해 생성된 symbolic reference를 말한다. Symbolic reference는 다름이 아닌 코드에 있는 클래스, 메소드 및 필드를 가리키는 하나의 reference값이고, 이러한 reference값을 통해 JVM이 코드 부분 부분이 어떤 다른 클래스에 의존하는지를 찾아내는 Linking-Resolution 단계를 수행하는 것이다.

    즉, 모든 symbolic reference는 런타임 constant pool에 있고 이는 method area에 있는 것이다 (사실 runtime constant pool의 구조는 implementation-specific하다). 그래서 이 Resolution 단계에서는 이러한 symbolic reference를 JVM에 의해 로딩된 실제 타입으로 변환하는 것이다. 즉, 프로그램 코드 상의 symbolic reference가 메모리 상의 실제 레퍼런스로 변환되는 것이다.

    예를 들어, 다음과 같은 간단한 예시를 보자.

    ```java
    System.out.println("Hello World!");
    ```
    
    `javap -verbose`를 실행하면 다음과 같은 아웃풋이 나온다.

    ```
    0:   getstatic        #2; //Field java/lang/System.out:Ljava/io/PrintStream
    3:   ldc              #3; //String Hello, World!
    5:   invokevirtual    #4; //Method java/io/PrintStream.println:(Ljava/lang/String;)V
    ```

    `#n`이 constant pool에 대한 reference이다. `#2`는 `System.out` 필드, `#3`은 `Hello World!` 스트링, 그리고 `#4`는 `PrintStream.println(String)` 메소드에 대한 symbolic reference이다. 

3. **Initialization**
ClassLoader sub-system의 다음 단계는 Initialization이다.

이 단계에서 모든 static 변수들은 코드 상에 적힌 원래의 값들로 초기화되고, static 블록들은 위에서 아래로 실행된다.

## 출처
https://codezup.com/java-virtual-machine-jvm-architecture/