---
title: "Java Basics - Modifier"
categories: 
  - Java Basics
---

## What is a modifier?
자바에는 다양한 제어자(modifier)를 제공하는데, 여기서 제어자는 접근 메커니즘을 통제하고 JVM에게 클래스 기능성에 대해 정보를 제공하기 위해 사용된다.

제어자는 접근 제어자(Access modifiers)와 비접근 제어자(Non-access modifiers)로 분류된다.

### Access modifiers
자바의 접근 제어자는 클래스, 생성자, 변수, 메소드, 혹은 데이터 멤버의 범위(scope)를 정해준다.
자바의 접근 제어자로 public, private, protected가 존재한다.\
또, package-private이라 불리는 default access level또한 정의되어 있다 (키워드 없음).

좀 더 자세히 알아보자.

**Default**: 클래스, 메소드 등의 접근 제어자가 따로 명시되지 않은 경우, 기본적으로 default 접근제어자를 가지게 된다. 즉, 해당 클래스, 메소드 등은 같은 패키지 내에서만 접근이 가능하다.

**Private**: 메소드나 데이터 멤버가 private으로 선언되면, 선언된 클래스 내에서만 접근 가능하다. 따라서 같은 패키지내의 다른 클래스는 이 멤버들에게 접근할 수 없다. Top-level 클래스나 인터페이스는 private으로 선언될 수 없다. 왜냐하면 private은 해당 컴포넌트를 감싸는 클래스 내에서만 보인다는 의미를 가지기 때문이다.

**Protected**: 메소드나 데이터 멤버가 protected로 선언되면, 같은 패키지내 혹은 다른 패키지의 subclass(상속의 경우)에서 접근 가능하다.
예를 들어 다음과 같은 상황을 가정할 수 있다: p1과 p2 패키지를 각각 생성하고, 클래스 A는 p1에서 public으로 만들고 p2에서 이 클래스에 접근하고자 한다.

{% highlight java %}
package p1;

public class A {
    protected void display() {
        System.out.println("hello world");
    }
}
{% endhighlight %}

display 메소드는 protected로 선언되었고 다음과 같이 클래스 B를 클래스 A로 부터 상속받게 만든다.

{% highlight java %}
package p2;
import p1.*;

class B extends A {
    public static void main(String[] args) {
        B obj = new B();
        obj.display();
    }
}
{% endhighlight %}
클래스 B에 대한 객체를 만들어서 부모 클래스인 A에 선언된 display 메소드에 접근이 가능해진다.

**Public**: public 접근 제어자는 모든 접근 제어자 중에 가장 넓은 범위를 가지고 있다. 해당 제어자를 통해 선언된 클래스, 메소드 혹은 데이터 멤버는 프로그램상에 어떠한 곳에서도 접근이 가능하다.

### Non-access modifiers
비접근 제어자로는 7가지 종류가 있다. 이들은 클래스, 메소드, 변수, 생성자 등에 쓰이며 해당 요소들의 행동에 대한 정보를 JVM에게 제공하기 위해 쓰인다.

**static**: static 제어자는 블록, 변수, 메소드, 중첩 클래스(nested class)에 사용 가능하다.
static으로 선언된 멤버는 해당 클래스의 객체가 생성되기 전에, 어떤 객체에 대한 참조 없이도 접근 가능하다.

{% highlight java %}
class Test {
    static void m1() {
        System.out.println("from m1");
    }

    public static void main(String[] args) {
        m1();
    }
}
{% endhighlight %}

위 코드의 경우 Test 클래스의 객체를 생성하지 않고서도 m1 메소드를 호출할 수 있다.

1) Static Block\
만약 static 변수를 초기화해야 한다면, 클래스가 제일 처음 로딩될 때 단 한번 실행되는 static 블록을 선언할 수 있다. 

{% highlight java %}
class Test {
    static int a = 10;
    static int b;

    static {
        System.out.println("Static block initialized.");
        b = a * 4;
    }

    public static void main(String[] args) {
        System.out.println("from main");
        System.out.println("Value of a : " + a);
        System.out.println("Value of b : " + b);
    }
}
{% endhighlight %}

2) Static Variables\
변수가 static으로 선언되면, 한 개의 변수가 생성되어 클래스 레벨에서 모든 객체들 간에 공유된다. 따라서 static 변수는 근본적으로 전역 변수인 셈이다. 클래스의 모든 인스턴스들이 동일한 static 변수를 공유한다.

**중요**
- static variable은 클래스 레벨에서만 만들 수 있다.
- static 블록과 static 변수는 프로그램에서 나타나는 순서대로 실행된다.

3) Static Methods\
메소드가 static으로 선언되면 static 변수와 마찬가지로 클래스의 객체에 대한 참조 없이도 접근이 가능하지만 몇 가지 제한사항이 다음과 같이 존재한다.
- static 메소드는 다른 static 메소드만 직접 호출할 수 있다.
- static 메소드는 다른 static 데이터에만 직접 접근할 수 있다.
- static 메소드는 this나 super를 참조할 수 없다.

{% highlight java %}
class Test {
    static int a = 10;
    int b = 20;

    static void m1() {
        a = 20;
        System.out.println("from m1");

        b = 10; // compilation error; cannot make a static reference to non-static field

        m2(); // compilation error; cannot make a static reference to non-static method

        System.out.println(super.a) // compilation error; cannot user super in a static context
    }

    void m2() {
        System.out.println("from m2");
    }

    public static void main(String[] args) {
        // main method
    }
}
{% endhighlight %}

### static 변수/메소드는 언제 사용하는가?
모든 객체에게 동일하게 적용되는 속성이 있을 경우 static 변수를 사용한다. 예를 들어, Student 클래스에서, 모든 학생이 같은 대학교 이름을 공유한다면 static 변수를 사용할 것이다. 그리고 static 변수를 변경하기 위해 static 메소드를 사용한다.

다음과 같은 예시를 들어보자.

{% highlight java %}
class Student {
    String name;
    int rollNo;

    static String collegeName;
    static int counter = 0;

    public Student(String name) {
        this.name = name;
        this.rollNo = setRollNo();
    }

    static int setRollNo() {
        counter++;
        return counter;
    }

    static void setCollegeName(String name) {
        collegeName = name;
    }

    void getStudentInfo() {
        System.out.println("name: " + this.name);
        System.out.println("rollNo: " + this.rollNo);

        System.out.println("collegeName: " + collegeName);
    }
}

public class StaticDemo {
    public static void main(String[] args) {
        Student.setCollegeName("XYZ");

        Student s1 = new Student("Alice");
        Student s2 = new Student("Bob");

        s1.getStudentInfo(); // Alice, 1, XYZ
        s2.getStudentInfo(); // Bob, 2, XYZ
    }
}
{% endhighlight %}

4) Static Class\
Java에서 클래스를 static으로 만들 수 있다.
Java에서는 클래스 안에 클래스를 정의할 수 있는데, 이를 중첩 클래스(nested classes)라고 한다. Top-level 클래스와 다르게, 그 안에 중첩 클래스는 static이 될 수 있다. Non-static 중첩 클래스는 inner class라고 부른다.

**static nested class와 inner class의 차이점**
1. static nested class는 그 클래스의 outer class를 인스턴스화하지 않고도 인스턴스화할 수 있다.
2. Inner class는 outer class의 static 및 non-static 멤버들에게 접근할 수 있다. Static class는 outer class의 static 멤버들에게만 접근할 수 있다.

{% highlight java %}
class OuterClass {
    private static String msg = "Hello";

    public static clss NestedStaticClass {

        public void printMessage() {
            System.out.println(msg); // if msg was non-static, there will be compilation error
        }
    }

    public class InnerClass {
        public void display() {
            System.out.println(msg); // both static and non-static members of Outer class are accessible
        }
    }
}

class Main {
    public static void main(String args[]) {
        // Create instance of nested static class
        OuterClass.NestedStaticClass printer = new OuterClass.NestedStaticClass();

        // Call non-static method of nested static class
        printer.printMessage();

        // Create instance of inner class
        OuterClass outer = new OuterClass();
        OuterClass.InnerClass inner = outer.new InnerClass();

        // Call non-static method of inner class
        inner.display();

        // Combine above steps in one
        OuterClass.InnerClass innerObject = new OuterClass().new InnerClass();

        innerObject.display();
    }
}
{% endhighlight %}

출처
- https://www.geeksforgeeks.org/access-and-non-access-modifiers-in-java/