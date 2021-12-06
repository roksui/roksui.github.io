---
title: "Java - Language Basics: Nested Classes"
---

원래는 이전 글에 Nested Class까지 다룰려고 하였으나, 해당 글의 양이 너무 많아질 것 같아, 따로 나누었다.

Java로 클래스를 다루다 보면 중첩으로 만들 경우가 꽤나 많은데, 왜 중첩으로 쓰는지, 어떻게 쓰는지 또한 매우 중요한 내용이라고 생각된다.

## Nested Classes
Java 언어는 클래스를 다른 클래스 안에 정의하는 것을 허용한다. 이런 클래스를 *nested class(중첩 클래스)*라고 부른다.

```java
class OuterClass {
    ...
    class InnerClass {
        ...
    }
}
```

Nested class는 두 개의 부류로 나뉜다: non-static / static
이때, non-static nested class는 흔히 *inner class*라고 부른다.

```java
class OuterClass {
    ...
    class InnerClass {
        ...
    }

    static class StaticNestedClass {
        ...
    }
}
```

Nested class는 그것을 감싸는 클래스(enclosing class)의 멤버라고 볼 수 있다. Non-static nested class, 즉 inner class는 그것을 감싸는 클래스의 다른 멤버들에게 접근할 수 있다, 그것들이 private으로 선언될지라도. Static nested class는 그것을 감싸는 클래스의 다른 멤버들에게 접근할 수 없다.

OuterClass의 멤버로써, nested class는 private, public, protected, 혹은 package private로 선언될 수 있다.

### Why Use Nested Classes?
맨 처음에, 왜 굳이 어렵게 클래스들을 중첩시키나?라고 생각했다. Nested class를 쓰는 중요한 이유들은 다음과 같다.

- **오로지 한 곳에서만 쓰이는 클래스를 논리적으로 그룹화할 수 있다**: 만약 한 클래스가 오로지 다른 하나의 클래스에게만 유용하다면, 그 클래스를 다른 하나의 클래스 안에 넣어서 두 개를 같이 유지하는 것이 논리적일 것이다. 이러한 "helper class"를 중첩시켜 패키지를 더욱 간소화시킬 수 있다.

- **Encapsulation을 개선시킨다**: 두 개의 top-level 클래스 A, B가 있고, B는 A의 private으로 선언된 멤버들에게 접근해야 한다고 가정하자. 클래스 B를 클래스 A에 숨김으로써 A의 멤버들을 private으로 선언함과 동시에 B가 이에 접근을 할 수가 있다. 또, B 자체도 외부로부터 숨길 수 있다.

- **가독성 높고 유지하기 쉬운 코드가 된다**: Top-level 클래스 내에 작은 클래스들을 중첩시켜 클래스들이 사용되는 곳 가까이에 보관할 수 있다. 

### Inner Classes
Inner class는 Non-static nested class인 것을 확실히 기억하자. 근데 나는 처음에 이게 확실하게 와닿거나 기억이 안되었다.

인스턴스 변수 및 메소드처럼 inner class는 그것을 감싸는 클래스의 인스턴스와 연관이 있고, 그 객체의 필드/메소드에 바로 접근할 수 있다. Inner class는 한 인스턴스와 관련이 있기 떄문에, 그 클래스 안에서는 static 멤버를 정의할 수 없다. 다시 말해, **inner class의 인스턴스들은 outer class의 인스턴스 내에서만 존재할 수 있다.**

Inner의 의미가 Instance와 바로 연결되는 느낌이 안들어 기억이 좀 어려운데, Instance와 Inner Class는 둘 다 'I'로 시작하니 이렇게 기억해도 될듯하다.

다음의 예제를 생각해보자.

```java
class OuterClass {
    ...
    class InnerClass {
        ...
    }
}
```

InnerClass의 인스턴스는 OuterClass의 인스턴스 내에서만 존재할 수 있고, outerClass의 필드/메소드에 바로 접근할 수 있다.

Inner class를 인스턴스화하기 위해서는, 먼저 outer class를 인스턴스화 해야한다.

```java
OuterClass outerObject = new OuterClass();
OuterClass.InnerClass innerObject = outerObject.new InnerClass();
```

### Static Nested Classes
클래스 변수/메소드처럼 static nested class는 outer class와 연관이 있다. static 클래스 메소드와 같이 static nested class는 outer class의 인스턴스 변수/메소드에 바로 접근할 수 없고, 객체 reference를 통해서만 가능하다. Static nested class는 사실상 하나의 top-level 클래스로써, 패키징 편의성을 위해 또 하나의 top-level 클래스 안에 보관하는 것으로 생각하면 이해하기 쉽다. 따라서, 두 개의 top-level 클래스들이 상호작용하는 것과 똑같다.

Static nested class는 top-level 클래스를 인스턴스화하듯 하면된다.

```java
StaticNestedClass staticNestedObject = new StaticNestedClass();
```

Nested class들에 대해 한꺼번에 하나의 예시로 알아보자.

```java
public class OuterClass {
    String outerField = "Outer field";
    static String staticOuterField = "Static outer field";

    class InnerClass {
        void accessMembers() {
            System.out.println(outerField);
            System.out.println(staticOuterField);
        }
    }

    static class StaticNestedClass {
        void accessMembers(OuterClass outer) {
            
            // Compile-time error
            // Cannot make a static reference to the non-static field outerField 
            System.out.println(outerField);

            System.out.println(outer.outerField);
            System.out.println(staticOuterField);
        }
    }

    public static void main(String[] args) {
        System.out.println("Inner class:");
        System.out.println("------------");
        
        OuterClass outerObject = new OuterClass();
        outerClass.InnerClass = outerObject.new InnerClass();
        innerObject.accessMembers();

        System.out.println("\n Static nested class:");
        System.out.println("-----------------------");

        StaticNestedClass staticNestedObject = new StaticNestedClass();
        staticNestedObject.accessMembers(outerObject);

        System.out.println("\n Top-level class:");
        System.out.println("-------------------");

        TopLevelClass topLevelObject = new TopLevelClass();
        topLevelObject.accessMembers(outerObject);
    }
}
```

TopLevelClass.java

```java
public class TopLevelClass {
    void accessMembers(OuterClass outer) {

        // Compile-time error
        // Cannot make a static reference to the non-static field OuterClass.outerField
        System.out.println(OuterClass.outerField);
    
        System.out.println(outer.outerField);
        System.out.println(OuterClass.staticOuterField);
    }
}
```

Output:

```
Inner class:
------------
Outer field
Static outer field

Static nested class:
--------------------
Outer field
Static outer field

Top-level class:
--------------------
Outer field
Static outer field
```

**NOTE**: Static nested class는 여느 top-level 클래스처럼 outer class의 인스턴스 멤버와 상호작용한다 - StaticNestedClass나 TopLevelClass나 outerField에 바로 접근하지는 못한다. 왜냐하면 outerField는 OuterClass의 인스턴스 변수이기 때문이다.

### Shadowing
다음과 같이 변수 x가 섀도잉이 된다고 생각해보자.

```java
public class ShadowTest {

    public int x = 0;

    class FirstLevel {

        public int x = 1;

        void methodInFirstLevel(int x) {
            System.out.println("x = " + x);
            System.out.println("this.x = " + this.x);
            System.out.println("ShadowTest.this.x = " + ShadowTest.this.x);
        }
    }

    public static void main(String... args) {
        ShadowTest st = new ShadowTest();
        ShadowTest.FirstLevel fl = st.new FirstLevel();
        fl.methodInFirstLevel(23);
    }
}
```

Output:

```
x = 23
this.x = 1
ShadowTest.this.x = 0
```

보다싶이 `this.x`를 통해 FirstLevel 클래스의 x에 접근할 수 있고, `ShadowTest.this.x`를 통해 outer class의 x에 접근하였다. this의 의미는 현재 객체였다. 따라서 `this.x`는 현재 대상 객체의 x 필드인데, 메인 메소드에서 f1은 FirstLevel이 인스턴스화되었으므로 FirstLevel 객체의 x가 맞고, `ShadowTest.this.x`는 ShadowTest 객체(ShadowTest.this)의 x에 접근한다는 의미이다.

### Inner Class Example
Inner class의 예시를 조금 자세히 다뤄보자.

다음 예제에서는 배열을 생성하여 integer 값으로 채우고, 오름차순으로 짝수 인덱스의 배열 값만 출력한다.

```java
public class DataStructure {

    private final static int SIZE = 15;
    private int[] arr = new int[SIZE];

    public DataStructure() {
        for (int i = 0; i < SIZE; i++) {
            arr[i] = i;
        }
    }

    public void printEven() {
        DataStructureIterator iterator = this.new EventIterator();
        while (hasNext()) {
            System.out.print(iterator.next() + " ");
        }
        System.out.println();
    }

    interface DataStructureIterator extends java.util.Iterator<Integer>() {
    }

    // Inner class implements the DataStructureIterator interface,
    // which extends the Iterator<Integer> interface

    class EventIterator implements DataStructureIterator {
        // Start stepping through the array from the beginning
        private int nextIndex = 0;

        // Check if the current element is the last in the array
        public boolean hasNext() {
            return (nextIndex <= SIZE - 1);
        }

        // Return the next integer
        public Integer next() {
            // Record a value of an even index of the array
            Integer retValue = Integer.valueOf(arr[nextIndex]);

            // Get the next even element
            nextIndex += 2;

            return retValue;
        }
    }

    public static void main(String[] args) {
        DataStructure ds = new DataStructure();
        ds.printEven();
    }
}
```

Output:

```
0 2 4 6 8 10 12 14
```

**NOTE**: EventIterator 클래스는 DataStructure 객체의 arr 인스턴스에 바로 접근하고 있다. Non-static nested class, 즉 inner class이기에 가능한 것이다.
이와 같이, inner class들을 사용하여 helper class를 구현할 수 있다. 배열을 반복적으로 iterate하는 EventIterator가 outer class의 helper class역할을 하는 것이다. 유저 인터페이스 이벤트를 핸들링할 때, 이벤트 핸들링 메커니즘으로 inner class를 많이 사용하기 때문에 이는 필수적으로 알아야 한다.

예전 같으면, 인터페이스로 구현하지 않고, 단순하게 for문으로 출력을 하였을 것이다. 상황에 맞게 하는 것이 베스트지만, 이를 통해 streamlined되고 유연성있는 Java의 interface 및 nested class를 통해 기능을 구현할 수 있다는 점을 배울 수 있었다.

**NOTE**: Outer class의 멤버들에게 제어자를 적용하듯이 inner class에게도 똑같이 적용 가능하다. 즉, public, private, protected를 사용하여 inner class에 대한 접근을 제어할 수 있다.

### Local and Anonymous Classes
Inner class에는 두 가지 종류가 더 있다.

Inner class를 메소드의 바디 내에 선언할 수도 있는데, 이것이 local class이다.

또, inner class를 메소드 내에서 클래스 이름을 주지 않고 선언할 수 있는데, 이것이 anonymous class이다.

### Local Classes

