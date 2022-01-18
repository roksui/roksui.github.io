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
- Bootstrap 클래스 로더의 자식 클래스이다.
- Extension class path (jdk\jre\lib\ext)에 있는 클래스들을 로딩해준다.
- Java 언어로 구현되었고, 이것의 .class 파일은 sun.misc.Launcher$ExtClassLoader.class이다.

### Application Class Loader / System Class Loader
- Extension 클래스 로더의 자식 클래스이다.
- Application class path로부터 클래스들을 로딩해준다.
- 내부적으로 환경 변수 class path를 사용한다.
- Java 언어로 구현되었고, 이것의 .class 파일은 sun.misc.Launcher$AppClassLoader.class이다.

클래스 로더는 **delegation hierarchy** 알고리즘을 따른다.

![class-loader-hierarchy](/assets/img/class-loader-hierarchy.png)

단계별로 보면 다음과 같다.

- JVM이 특정한 클래스를 마주하면, 먼저 .class 파일이 로딩되었는지를 확인한다.
- 만약 method area로 이미 로딩되었다면, JVM은 로딩된 클래스로 계속 진행한다.
- 만약 로딩되지 않았다면, JVM은 Class Loader sub-system에게 해당 클래스를 로딩하라고 요청을 보낸다.
- Class Loader sub-system은 그 요청을 Application 클래스 로더에게 포워딩한다.
- Application 클래스 로더는 그 요청을 Extension 클래스 로더에게 delegate(위임)하고, Extension 클래스 로더는 또 한 번 Bootstrap 클래스 로더에게 delegate한다.
- Bootstrap 클래스 로더는 Bootstrap class path를 탐색하여 해당 클래스가 있으면, 그 .class 파일을 로딩한다.
- 만약 .class 파일이 없다면, Bootstrap 클래스 로더는 요청을 Extension 클래스 로더에게 다시 delegate한다.
- Extension 클래스 로더는 Extension class path에서 해당 클래스를 탐색하고, 있다면 로딩하고, 없다면 Application 클래스 로더에게 요청을 delegate한다.
- Application 클래스 로더 또한 Application class path에서 클래스를 탐색하고, 있다면 로딩하고, 없다면 ClassNotFoundException의 런타임 exception이 발생한다.

이러한 단계를 거치는 알고리즘이 *Delegation Hierarchy Algorithm*이다.

**NOTE**: 자식 클래스 로더에의해 로딩된 클래스들은 부모 클래스 로더에의해 로딩된 클래스들에 접근할 수 있다. 즉, Application 클래스 로더에의해 로딩된 클래스들은 Extension/Bootstrap 클래스 로더에의해 로딩된 클래스들에 접근할 수 있는 것이다.

예시를 하나 작성해보자.

`Student.java`와 `School.java` 파일을 생성하여 컴파일하는데, `School.class` 파일은 Application Classpath와 Extension Classpath에 한 카피씩 존재한다고 가정하자.

그리고 다음과 같은 코드를 실행하면 어떤 결과가 나올까?

```java
public class Student {
    public static void main(String[] args) {
        
        System.out.println(String.class.getClassLoader());
        System.out.println(Student.class.getClassLoader());
        System.out.println(School.class.getClassLoader());
    }
}
```

```bash
$> javac Student.java
$> java Student
```
로 실행을 해보면, 

Output:
```
null
sun.misc.Launcher$AppClassLoader@1985a96
sun.misc.Launcher$ExtClassLoader@1085a85
```
이런 결과가 나온다.

`String.class`는 Bootstrap 클래스 로더에의해 로딩되고, Bootstrap 클래스 로더는 Java로 구현되지 않았기 때문에 객체가 생성되지 않아 null이 된다.

하지만, 나머지 클래스 로더들은 Java 객체로써 클래스이름@hashcodeInHexadecimalForm으로 출력된다.

또, Delegation Hierarchy Algorithm에 의해 Extension classpath가 Application classpath보다 더 높은 우선 순위를 가지기 때문에, `School.class`는 Extension 클래스 로더에의해 먼저 로딩된다.

## Customizing Class Loaders

이러한 디폴트 클래스 로딩 메커니즘은 클래스가 한 번 method area로 로딩되고, 만약 .class 파일이 외부에서 변경되어도 이미 로딩된 .class 파일을 사용할 것이다. 만약 서버 애플리케이션을 중단하고 재시작하지않고 (그럴 경우 변경된 클래스 파일이 적용되겠지만), 실시간으로 업데이트된 .class 파일을 적용해줘야할 경우, 커스터마이징된 클래스 로더를 만드는 것도 하나의 방법이다. 다음과 같이 만들 수 있을 것이다.

```java
public class CustomizedClassLoader extends ClassLoader {
    public Class loadClass(String cname) throws ClassNotFoundException {
        // Check whether updated version of class is available or not.
        // If it is available, then load the updated version and return the corresponding class "Class" object.
        // Otherwise return Class object of already loaded .class
    }

    class CustomClassLoaderTest {
        public static void main(String[] args) {
            Student s1 = new Student(); // Default class loader loads Student.class

            CustomizedClassLoader c = new CustomizedClassLoader();
            c.loadClass("Student"); // Customized class loader checks for updates and loads updated version of Student class
            ...
            c.loadClass("Student"); // Customized class loader checks for updates and loads updated version of Student class
        }
    }
}
```