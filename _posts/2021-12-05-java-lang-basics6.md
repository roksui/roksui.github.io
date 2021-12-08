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
Local class는 block안에 정의된 클래스를 말한다. 전형적으로 메소드 바디안에 정의되지만, for문이나 if문 안에서도 정의가 가능하다.

다음 예시는 두 개의 휴대폰 번호를 검증하는 코드이다. Local class인 PhoneNumber가 validatePhoneNumber 메소드 안에 정의된다.

```java
public class LocalClassExample {
    static String regularExpression = "[^0-9]";

    public static void validatePhoneNumber(String phoneNumber1, String phoneNumber2) {
        int numberLength = 10;

        class PhoneNumber {
            String formattedPhoneNumber = null;

            PhoneNumber(String phoneNumber) {
                String currentNumber = phoneNumber.replaceAll(regularExpression, "");

                if (currentNumber.length() == numberLength)
                    formattedPhoneNumber = currentNumber;
                else
                    formattedPhoneNumber = null;
            }

            public String getNumber() {
                return formattedPhoneNumber;
            }

            public void printOriginalNumbers() {
                System.out.println("Original numbers are " + phoneNumber1 + " and " + phoneNumber2);
            }
        }

        PhoneNumber myNumber1 = new PhoneNumber(phoneNumber1);
        PhoneNumber myNumber2 = new PhoneNumber(phoneNumber2);

        myNumber1.printOriginalNumbers();

        if (myNumber1.getNumber() == null)
            System.out.println("First number is invalid");
        else
            System.out.println("First number is " + myNumber2.getNumber());
        if (myNumber2.getNumber() == null)
            System.out.println("Second number is invalid");
        else
            System.out.println("Second number is " + myNumber2.getNumber());
    }

    public static void main(String... args) {
        validatePhoneNumber("123-456-7890", "456-7890");
    }
}
```

위 코드를 보면 전화번호에서 0-9의 문자를 빼고는 모두 제거한 후, 정확히 10자리 인지 확인하여 검증을 한다.

Output:

```
Original numbers are 123-456-7890 and 456-7890

First number is 1234567890
Second number is invalid
```

위에서 볼 수 있듯이 로컬 클래스에서는 outer class의 멤버에 접근할 수 있다. PhoneNumber 생성자는 LocalClassExample.regularExpression에 접근한다. 또, 로컬 클래스는 로컬 변수에도 접근할 수 있는데, final로 선언된 로컬 변수에만 접근이 가능하다. 로컬 클래스가 enclosing/outer block의 로컬 변수나 파라미터에 접근할 때는 그 변수/파라미터를 capture한다.

Java SE 8부터는 로컬 클래스가 enclosing block의 final이거나 *effectively final*한 로컬 변수 및 파라미터에 접근할 수 있다. Effectively final이라함은 변수나 파라미터의 값이 초기화되고나서 바뀌지 않는 것이다. 예를 들어, numberLength가 final로 선언되지 않고 로컬 클래스에서 값을 재할당한다고 생각해보자.

```java
// 로컬 클래스 생성자
PhoneNumber(String phoneNumber) {
    numberLength = 7; // 10으로 초기화된 것을 7로 변경
    String currentNumber = phoneNumber.replaceAll(regularExpression, "");
    ...
}
```

이 대입 구문으로 인해 numberLength는 더 이상 effectively final하지 않다. 따라서 Java 컴파일러는 "local variables referenced from an inner class must be final or effectively final"과 같은 에러 메시지를 보일 것이다.

또, Java SE 8부터는 로컬 클래스를 메소드 안에서 선언하면, 그 클래스는 메소드의 파라미터에 접근할 수 있다. 위 예시에서 printOriginalNumbers 메소드에서 enclosing 메소드의 파라미터인 phoneNumber1과 phoneNumber2에 접근하고 있다.

**NOTE**: 이전에 알아본 섀도잉처럼, 로컬 클래스 안에서 변수를 선언하면 enclosing 스코프에 있는 동일한 이름의 변수 선언을 섀도잉한다.

로컬 클래스에서는 static 멤버를 정의할 수 없다는 점에서 inner 클래스와 비슷하다. Static 메소드 안에 있는 로컬 클래스는 enclosing 클래스의 static 멤버들만 참조할 수 있다. 예를 들어, 위 예시에서 멤버 변수 regularExpression을 static으로 하지 않았다면 컴파일 에러가 날 것이다. 로컬 클래스는 enclosing block의 인스턴스 멤버에 접근할 수 있으므로 non-static이다. 따라서 로컬 클래스에는 대부분의 static 선언이 불가능하다.

인터페이스는 본질적으로 static하기 때문에 block안에 작성될 수 없다. 다음 코드 스니펫을 보면 인터페이스 HelloThere가 메소드 안에 정의되어서 컴파일 되지않는다.

```java
public void greetInEnglish() {
    interface HelloThere {
        public void greet();
    }

    class EnglishHelloThere implements HelloThere {
        public void greet() {
            System.out.println("Hello " + name);
        }
    }

    HelloThere myGreeting = new EnglishHelloThere();
    myGreeting.greet();
}
```

Static initializer나 멤버 인터페이스를 로컬 클래스 안에서 선언할 수 없다. 다음 코드 스니펫을 보면 메소드 EnglishGoodbye.sayGoodbye가 static으로 선언되어서 컴파일 에러가 발생한다.

```java
public void sayGoodbyeInEnglish() {
    class EnglishGoodbye {
        public static void sayGoodbye() {
            System.out.println("Bye bye");
        }
    }
    EnglishGoodbye.sayGoodbye();
}
```

만약 Constant 변수라면 로컬 클래스의 static member가 될 수 있다. Constant 변수는 primitive 타입이나 String 타입의 final로 선언되고 초기화되는 변수이다. 이를 compile-time constant expression이라고 하는데 이는 대체로 컴파일때 연산될 수 있는 문자열이거나 산수 식이다. 다음 코드 스니펫의 경우는 잘 컴파일된다.

```java
public void sayGoodbyeInEnglish() {
    class EnglishGoodbye {
        public static final String farewell = "Bye bye";
        public void sayGoodbye() {
            System.out.println(farewell);
        }
    }
    EnglishGoodbye myEnglishGoodbye = new EnglishGoodbye();
    myEnglishGoodbye.sayGoodbye();
}
```

### Anonymous Classes
익명 클래스는 코드를 좀 더 간결하게 만들어준다. 익명 클래스를 사용하여 클래스를 선언함과 동시에 인스턴스화할 수 있다. 이름 없는 로컬 클래스이기 떄문에, 일회용으로 필요할 때 용이하다. 

로컬 클래스는 클래스 선언이지만 익명 클래스는 하나의 식(expression)이다. 다음 예시를 보자.

```java
public class HelloWorldAnonymousClasses {

    interface HelloWorld {
        public void greet();
        public void greetSomeone(String someone);
    }

    public void sayHello() {

        class EnglishGreeting implements HelloWorld {
            String name = "world";
            public void greet() {
                greetSomeone("world");
            }
            public void greetSomeone(String someone) {
                name = someone;
                System.out.println("Hello " + name);
            }
        }

        HelloWorld englishGreeting = new EnglishGreeting();

        HelloWorld frenchGreeting = new HelloWorld() {
            String name = "tout le monde";
            public void greet() {
                greetSomeone("tout le monde");
            }
            public void greetSomeone(String someone) {
                name = someone;
                System.out.println("Salut " + name);
            }
        };

        HelloWorld spanishGreeting = new HelloWorld() {
            String name = "mundo";
            public void greet() {
                greetSomeone("mundo");
            }
            public void greetSomeone(String someone) {
                name = someone;
                System.out.println("Hola, " + name);
            }
        };
        englishGreeting.greet();
        frenchGreeting.greetSomeone("Fred");
        spanishGreeting.greet();
    }

    public static void main(String... args) {
        HelloWorldAnonymousClasses myApp = new HelloWorldAnonymousClasses();
        myApp.sayHello();
    }
}
```

Syntax를 보면, 생성자 호출과 비슷한데 거기에 클래스 정의를 block에 넣어준다. 요약하자면 다음으로 구성된다:
- new 연산자
- 구현하고자 하는 인터페이스 혹은 상속받고자 하는 클래스의 이름. 위 예시에서는 HelloWorld 인터페이스를 구현하는 익명 함수를 만들었다.
- 보통 클래스 인스턴스 생성할 때 처럼 생성자 인자를 괄호안에 넣어준다. **NOTE**: 인터페이스를 구현할 때는 생성자가 없으므로 위 예시처럼 그냥 빈 괄호를 쓴다.
- 클래스 선언 바디

익명 클래스 정의가 하나의 식이기 때문에, 구문의 일부분이 되어야 한다. 위 예시에서는 익명 클래스 식이 frenchGreeting 객체를 인스턴스화하는 구문의 일부분이다.

로컬 클래스와 같이 익명 클래스도 변수를 capture할 수 있다. 즉, enclosing 스코프의 로컬 변수에 접근할 수 있다.
- 익명 클래스는 enclosing 클래스의 멤버에 접근할 수 있다.
- 익명 클래스는 enclosing 클래스의 final이나 effectively final하지 않은 로컬 변수에 접근할 수 없다.
- 중첩 클래스의 경우와 같이 익명 클래스 내의 변수 선언이 enclosing 스코프의 변수를 섀도잉 할 수 있다.

익명 클래스는 멤버와 관련해서 로컬 클래스와 똑같은 제한사항을 가진다.
- 익명 클래스에서 static initializer나 멤버 인터페이스를 선언할 수 없다.
- 익명 클래스는 constant 변수의 static 멤버를 가질 수 있다.

익명 클래스에는 필드, supertype의 메소드 구현 외에 추가 메소드, 인스턴스 initializers 및 로컬 클래스를 포함할 수 있다. 하지만 생성자는 포함할 수 없다.

익명 클래스는 GUI 어플리케이션에서 많이 쓰인다. 다음 예시는 JavaFX를 사용하여 "Hello World"버튼이 있는 프레임을 생성한다.

```java
import javafx.event.ActionEvent;
import javafx.event.EventHandler;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.layout.StackPane;
import javafx.stage.Stage;

public class HelloWorld extends Application {
    public static void main(String[] args) {
        launch(args);
    }

    @Override
    public void start(Stage primaryStage) {
        primaryStage.setTitle("Hello World!");
        Button btn = new Button();
        btn.setText("Say 'Hello World'");
        btn.setOnAction(new EventHandler<ActionEvent>() {

            @Override
            public void handle(ActionEvent event) {
                System.out.println("Hello World!");
            }
        });

        StackPane root = new StackPane();
        root.getChildren().add(btn);
        primaryStage.setScene(new Scene(root, 300, 250));
        primaryStage.show();
    }
}
```

`btn.setOnAction` 메소드 호출이 Say 'Hello World' 버튼을 선택했을 시 일어나는 이벤트를 담당한다. 이 메소드는 `EventHandler<ActionEvent>` 타입 객체를 받는다. 이 메소드의 인자로 익명 클래스를 넘겨주고 있다.

**NOTE**: `EventHandler<ActionEvent>` 인터페이스는 한 개의 메소드 (i.e. handle)만 가지고 있다. 따라서 익명 클래스 대신 람다식을 써도 된다.

익명 클래스는 두 개 이상의 메소드를 가진 인터페이스를 구현하기 위해 유용하게 사용된다. 다음 JavaFX 예제는 숫자값만 받는 텍스트 필드를 생성한다. `TextField` 클래스의 디폴트 구현을 재정의한다 - `TextInputControl` 클래스로부터 상속받는 `replaceText`와 `replaceSelection` 메소드를 오버라이딩한다.

```java
public class CustomTextFieldSample extends Application {

    final static Label label = new Label();

    @Override
    public void start(Stage stage) {
        Group root = new Group();
        Scene scene = new Scene(root, 300, 150);
        stage.setScene(scene);
        stage.setTitle("Text Field Sample");

        GridPane grid = new GridPane();
        grid.setPadding(new Insets(10, 10, 10, 10));
        grid.setVgap(5);
        grid.setHgap(5);

        scene.setRoot(grid);
        final Label dollar = new Label("$");
        GridPane.setConstraints(dollar, 0, 0);
        grid.getChildren().add(dollar);

        final TextField sum = new TextField() {
            @Override
            public void replaceText(int start, int end, String text) {
                if (!text.matches("[a-z, A-Z]")) {
                    super.replaceText(start, end, text);
                }
                label.setText("Enter a numeric value");
            }

            @Override
            public void replaceSelection(String text) {
                if (!text.matches("[a-z, A-Z]")) {
                    super.replaceSelection(text);
                }
            }
        };

        sum.setPromptText("Enter the total");
        sum.setPrefColumnCount(10);
        GridPane.setConstraints(sum, 1, 0);
        grid.getChildren().add(sum);

        Button submit = new Button("Submit");
        GridPane.setConstraints(submit, 2, 0);
        grid.getChildren().add(submit);

        submit.setOnAction(new EventHandler<ActionEvent>() {
            @Override
            public void handle(ActionEvent e) {
                label.setText(null);
            }
        });

        GridPane.setConstraints(label, 0, 1);
        GridPane.setColumnSpan(label, 3);
        grid.getChildren().add(label);
        
        scene.setRoot(grid);
        stage.show();
    }

    public static void main(String[] args) {
        launch(args);
    }
}
```

### Lambda Expressions
익명 클래스의 이슈 중 하나가, 만약 익명 클래스 구현이 매우 간단한 경우, 예를 들어, 한 개의 메소드만 가진 인터페이스를 사용하는 경우, 익명 클래스의 syntax가 불명확해져보일 수도 있고, 배보다 배꼽이 더 큰 불필요한 경우가 될 수 있다. 이러한 경우는 주로 다른 메소드에 인자로 functionality를 넘기려고 할 때이다, 예를 들어 버튼을 눌렀을 때 어떤 액션이 취해질지. 람다식을 통해 하나의 functionality를 메소드 인자로 사용할 수 있다. 혹은 코드를 데이터화시킨다고 생각할 수 있다.

따라서, 람다식은 단 한 개의 메소드를 가진 클래스 및 인터페이스를 좀 더 간결하게 표현할 수 있게 해준다.

소셜 네트워킹 어플리케이션을 만든다고 가정해보자. 관리자는 특정 기준에 의거하여 회원들에게 메시지를 전송하는 등의 어떠한 액션이든 취할 수 있도록 하는 기능을 만들고자 한다. 이러한 유스 케이스를 다음과 같이 요약할 수 있다.

| field | Description |
| ----- | ----------- |
| Name  | Perform action on selected members |
| Primary Actor | Administrator |
| Preconditions | Administrator is logged in to the system |
| Postconditions | Action is performed only on members that fit the specified criteria |
| Main Success Scenario | 1. Administrator specifies criteria of members on which to perform a certain action.<br>2. Administrator specifies an action to perform on those selected members.<br>3. Administrator selects the Submit button.<br>4. The system finds all members that match the specified criteria.<br>5. The system performs the specified action on all matching members. |
| Extensions | 1a. Aministrator has an option to preview those members who match the specified criteria before he or she specifies the action to be performed or before selecting the Submit button |
| Frequency of Occurrence | Many times during the day |

이 소셜 네트워킹 어플리케이션의 회원은 다음 Person 클래스로 나타낸다.

```java
public class Person {

    public enum Sex {
        MALE, FEMALE
    }

    String name;
    LocalDate birthday;
    Sex gender;
    String emailAddress;

    public int getAge() {
        ...
    }

    public void printPerson() {
        ...
    }
}
```

그리고 회원들은 `List<Person>` 인스턴스에 저장된다.

이 휴스케이스에 대한 나이브한 방식부터 구현하여, 로컬 및 익명 클래스를 사용하여 개선하고, 마지막에 람다식을 통해 간결하고 효율적으로 바꿔보자.

**Approach 1 (Naive Approach): Create methods that search for members that match one characteristic**

단순한 방법으로는 여러 개의 메소드를 만들 수 있다. 각 메소드는 하나의 특징(예. gender or age)에 매칭되는 멤버를 탐색한다. 다음 메소드는 특정 나이보다 더 나이가 많은 회원을 출력한다.

```java
public static void printPersonsOlderThan(List<Person> roster, int age) {
    for (Person p : roster) {
        if (p.getAge() >= age) {
            p.printPerson();
        }
    }
}
```

이러한 방법을 사용하면 우리의 어플리케이션이 "변경에 취약한"(brittle) 상태가 된다. 예를 들어, 어플리케이션이 업데이트 되어 새로운 데이터 타입이 추가되는 경우, 즉 Person 클래스에 멤버 변수가 추가되거나 나이를 측정 및 기록하는 데이터 타입이나 알고리즘이 변경될 경우, 많은 양의 API를 새로 작성해야 할 것이다. 또한, 이 방법은 불필요하게 제한적이다. 만약 특정 나이보다 어린 회원들을 출력하고 싶다면?

**Approach 2: Create more generalized search methods**

위 방법을 좀 더 일반화하여 특정 나이 범위에 있는 회원들을 출력해보자.

```java
public static void printPersonsWithinAgeRange(List<Person> roster, int low, int high) {
    for (Person p : roster) {
        if (low <= p.getAge() && p.getAge() < high) {
            p.printPerson();
        }
    }
}
```

이전보다는 낫지만, 만약 특정 성별, 혹은 성별 및 나이의 조합을 가진 회원을 출력하고 싶다면? Person 클래스에 이성관계 상태 혹은 위치정보와 같은 attribute을 추가하고 싶다면? 그러면 각 검색 쿼리에 대한 메소드를 일일이 다 만들어줘야 하므로 여전히 brittle한 코드가 된다. 이렇게 하지말고, 검색하고자 하는 기준(criteria)을 명시하는 코드를 다른 클래스에 분리할 수 있다.

**Approach 3: Specify search criteria code in a local class**

다음 메소드는 명시하는 검색 기준에 매칭되는 회원들을 출력한다.

```java
public class RosterTest {
    interface CheckPerson {
        boolean test(Person p);
    }

    public static void printPersons(List<Person> roster, CheckPerson tester) {
        for (Person p : roster) {
            if (tester.test(p)) {
                p.printPerson();
            }
        }
    }

    public static void main(String... args) {
        List<Person> roster = Person.createRoster();

        class CheckPersonEligibleForSelectiveService implements CheckPerson {
            public boolean test(Person p) {
                return p.getGender() == Person.Sex.MALE
                    && p.getAge() >= 18
                    && p.getAge() <= 25;
            }
        }

        printPersons(roster, new CheckPersonEligibleForSelectiveService());
    }
}
```

`printPersons`메소드는 roster 리스트에서 `CheckPerson tester`에 명시된 기준을 만족하는 회원들을 `tester.test` 메소드를 통해 탐색하여 출력한다.

탐색 기준을 명시하기 위해 `CheckPersonEligibleForSelectiveService` 클래스는 `CheckPerson` 인터페이스를 구현한다 - `test` 메소드 구현체를 담고 있다. 이 메소드는 나이 18-25 사이의 남자를 탐색해준다. 그리고 메인 메소드에서 이 클래스를 인스턴스화하여 `printPersons` 메소드를 호출한다.

이 방법은 Person의 구조가 변경되어도 메소드를 다시 작성해야하는 부담은 줄어 덜 brittle하지만, 각 탐색에 대한 새로운 인터페이스 및 로컬 클래스를 추가해줘야 한다. `CheckPersonsEligibleForSelectiveService` 클래스가 인터페이스를 구현하기 때문에, 로컬 클래스 대신 익명 클래스를 사용하여 각 탐색에 대해 새로운 클래스를 선언하는 것을 피할 수 있다.

**Approach 4: Specify search criteria code in an anonymous class**

`printPersons` 메소드 호출의 인자로 회원을 필터링 해주는 익명 클래스를 넘기자.

```java
printPersons(roster, new CheckPerson() {
    public boolean test(Person p) {
        return p.getGender() == Person.Sex.MALE
                    && p.getAge() >= 18
                    && p.getAge() <= 25;
    }
});
```

이는 각 탐색에 대해 새로운 클래스를 선언하지 않아도되므로 코드 양을 줄여준다. 그러나 익명 클래스의 syntax로 인해 코드가 빵빵(?)해 보인다. `CheckPerson` 인터페이스는 한 개의 메소드만 가지고 있으니 람다식을 사용할 수 있다.

**Approach 5: Specify search criteria code with a lambda expression**

`CheckPerson` 인터페이스는 *functional interface*이다. Functional interface는 한 개만의 abstract method(추상 메소드)를 가진 인터페이스를 말한다. 단, functional interface는 한 개 이상의 default 메소드나 static 메소드는 가질 수 있다. 단 한 개의 추상 메소드만을 가지고 있기 때문에, 이름을 궂이 넣을 필요가 없는 것이다. 다음과 같이 람다식으로 바꿔보자.

```java
printPersons(roster, (Person p) -> 
    p.getGender() == Person.Sex.MALE
    && p.getAge() >= 18
    && p.getAge() <= 25
);
```

지금까지 개선한 내용을 보면, 나이브하게 모든 탐색 쿼리에 대한 메소드를 선언하지 않고, 출력해주는 메소드 `printPersons` 하나에 `CheckPerson` 인터페이스 구현체를 인자로 넘겨주므로 코드 양이 줄고, 코드 변경 시 수정해야 하는 부분이 줄어든다. 로컬 클래스로 선언할 시, 그 클래스를 수정해야하는데, 익명 클래스로 작성하면, 탐색을 수행할 때만 그 클래스가 선언되므로, 수정 양 또한 줄일 수 있다.

**Approach 6: Use standard functional interface with lambda expressions**

`CheckPerson` 인터페이스는 다음과 같았다.

```java
interface CheckPerson {
    boolean test(Person p);
}
```

이는 매우 간단한 인터페이스이다. 한 개의 추상 메소드만 가지고 있어서 functional interface이다. 이 메소드는 한 개의 파라미터만 받고 boolean 값을 리턴한다. 너무나 간단해서 우리의 어플리케이션에 정의자체를 하는 것이 투머치일 수도 있다. 따라서 JDK는 여러 개의 standard functional interface를 정의한다. (java.util.function 에서 찾을 수 있음)

예를 들어, `Predicate<T>` 인터페이스로 `CheckPerson`를 대체할 수 있다. 이 인터페이스는 `boolean test(T t)` 메소드를 가지고 있다.

```java
interface Predicate<T> {
    boolean test(T t);
}
```

`Predicate<T>`는 generic interface(제너릭 인터페이스)의 예시이다. 제너릭 타입은 한 개 이상의 타입 파라미터를 '<>'로 명시한다. 이 인터페이스는 한 개의 파라미터 T를 가지고 있는 것이다. 그리고 제너릭 타입을 실제 타입 인자로 선언하거나 인스턴스화하면, parameterized type이 되는 것이다. 예를 들어 `Predicate<Person>`의 parameterized type은 다음과 같다:

```java
interface Predicate<Person> {
    boolean test(Person t);
}
```

이 parameterized 타입은 기존의 `CheckPerson.boolean test(Person p)`의 메소드의 리턴 타입과 파라미터와 동일하다. 따라서 다음과 같이 대체할 수 있다.

```java
public static void printPersonsWithPredicate(List<Person> roster, Predicate<Person> tester) {
    for (Person p : roster) {
        if (tester.test(p)) {
            p.printPerson();
        }
    }
}
```

그리고 나서 Approach 3과 같이 메소드를 호출할 수 있다.

```java
printPersonsWithPredicate(roster, p ->
    p.getGender() == Person.Sex.MALE
    && p.getAge() >= 18
    && p.getAge() <= 25
);
```

**Approach 7: Use lambda expressions throughout your application**

또 다른 곳에서도 람다식을 사용할 수 있는지 알아보자.

`printPersonsWithPredicate` 메소드를 보자.

```java
public static void printPersonsWithPredicate(List<Person> roster, Predicate<Person> tester) {
    for (Person p : roster) {
        if (tester.test(p)) {
            p.printPerson();
        }
    }
}
```

이 메소드는 roster 리스트에 있는 각 Person 인스턴스가 Predicate 파라미터 tester의 기준을 만족하는지 확인하고, 그렇다면 `printPerson` 메소드를 Person 객체를 대상으로 호출한다.

`printPerson` 메소드를 호출하는 것 대신에 람다식을 써서 다른 액션을 명시할 수도 있다. `printPerson`과 비슷한 람다식을 원한다고 가정해보자. 그 람다식은 `Person` 타입의 객체를 인자로 받아들여 void를 리턴한다. 람다식을 사용하기 위해서는 functional interface가 필요하므로, 우리는 그 인터페이스의 추상 메소드가 Person 타입을 인자로 받고 void를 리턴하게 해야한다. `Consumer<T>` 인터페이스는 이러한 특징을 가진 `void accept(T t)` 메소드를 가지고 있다.

```java
public static void processPersons(List<Person> roster, Predicate<Person> tester, Consumer<Person> block) {
    for (Person p : roster) {
        if (tester.test(p)) {
            block.accept(p);
        }
    }
}
```

그리고 이 메소드를 다음과 같이 호출한다.

```java
processPersons(roster,
    p -> p.getGender() == Person.Sex.MALE
    && p.getAge() >= 18
    && p.getAge() <= 25,
    p -> p.printPerson()
);
```

바뀐점은 우리는 회원을 출력하기 위해 람다식을 사용했다는 것이다.

자, 이로인해 우리는 회원 출력 외에도 여러 액션을 할 수 있는 유연함을 얻었다. 그래서 메소드 이름도 좀 더 일반적인 processPersons로 변경하였다. 만약 회원 프로필을 검증하거나 연락처를 얻고 싶다면 어떨까? 그러면 어떠한 값을 리턴하는 추상 메소드를 포함하는 functional interface가 필요할 것이다. `Function<T, R>` 인터페이스는 `R apply(T t)` 메소드를 가지고 있다. 이 메소드는 mapper 파라미터에 명시된 데이터를 가져와 block 파라미터에 명시된 액션을 취한다.

```java
public static void processPersonsWithFunction(List<Person> roster, Predicate<Person> tester, Function<Person, String> mapper, Consumer<String> block) {
    for (Person p : roster) {
        if (tester.test(p)) {
            String data = mapper.apply(p);
            block.accept(data);
        }
    }
}
```

다음과 같이 호출할 수 있다. 이는 roster 리스트에 있는 각 회원들 중에 기준을 만족하는 사람들의 이메일 주소를 가져온다.

```java
processPersonsWithFunction(roster,
    p -> p.getGender() == Person.Sex.MALE
    && p.getAge() >= 18
    && p.getAge() <= 25,
    p -> p.getEmailAddress(),
    email -> System.out.println(email);
);
```

**Approach 8: Use generics more extensively**

방금 메소드 `processPersonsWithFunction`을 제너릭을 사용하여 좀 더 일반적으로 만들어보자.

```java
public static <X, Y> void processElements(Iterable<X> source, Predicate<X> tester, Function<X, Y> mapper, Consumer<Y> block) {
    for (X p : source) {
        if (tester.test(p)) {
            Y data = mapper.apply(p);
            block.accept(data);
        }
    }
}
```

그리고 이전 예시와 같이 호출하면 된다.

```java
processElements(roster,
    p -> p.getGender() == Person.Sex.MALE
    && p.getAge() >= 18
    && p.getAge() <= 25,
    p -> p.getEmailAddress(),
    email -> System.out.println(email);
);
```

메소드명이 `processElements`로 한 층 더 일반화된 것을 확인할 수 있다.

메소드 호출은 다음과 같은 액션을 수행한다.
1. roster 컬렉션(List 타입은 Collection의 한 종류이자 Iterable 타입의 객체이기도 함)에서 Person 객체 source를 얻는다.
2. 람다식인 Predicate 객체를 통해 객체를 필터링한다.
3. 필터링된 객체들을 람다식인 Function 객체를 통해 하나의 값으로 매핑해주어 리턴한다.
4. 매핑된 각 객체에 액션을 취하는 람다식인 Consumer 객체를 사용한다. 이 예시의 경우 문자열을 출력해준다.

자, 이제 이 프로세스들을 aggregate operation을 사용하여 나타낼 수 있다.

**Approach 9: Use aggregate operations that accept lambda expressions as parameters**

```java
roster
    .stream()
    .filter(
        p -> p.getGender() == Person.Sex.MALE
            && p.getAge() >= 18
            && p.getAge() <= 25)
    .map(p -> p.getEmailAddress())
    .forEach(email -> System.out.println(email));
```

다음 테이블은 `processElements` 메소드의 각 연산을 그에 대응하는 aggregate operation과 매핑한다.

| processElements Action | Aggregate Operation |
| ---------------------- | ------------------- |
| Obtain a source of objects | `Stream<E> stream()` |
| Filter objects that match a `Predicate` object | `Stream<T> filter(Predicate<? super T> predicate` |
| Map objects to another value as specified by a `Function` object | `<R> Stream<R> map(Function<? super T, ? extends R> mapper)` |
| Perform an action as specified by a `Consumer` object | `void forEach (Consumer<? super T> action)` |

여기서 filter, map, 그리고 forEach 연산자는 *aggregate operation*이다. Aggregate operation은 stream으로부터 나오는 요소들을 처리한다. 즉, 컬렉션에 있는 요소들을 바로 처리하지 않고 첫 호출된 메소드인 `stream`을 먼저 호출한다. *stream*은 요소의 시퀀스이다. 컬렉션과는 다르게 요소들을 저장하는 데이터 구조가 아니고, 컬렉션과 같은 source로부터 값들을 pipeline을 통해 전달한다. 여기서 *pipeline*이란 stream 연산의 시퀀스다. 이 예시에서는 이 시퀀스가 filter-map-forEach가 된다. 또, aggregate operations는 전형적으로 파라미터로 람다식을 받아서 우리가 해당 연산이 어떤식으로 행동할지를 커스터마이징할 수 있게 해준다.

### Lambda Expressions in GUI Applications
GUI 어플리케이션에서 키보드 액션, 마우스 액션 및 스크롤 액션과 같은 이벤트 처리를 위해 전형적으로 event handler(이벤트 핸들러)를 만드는데, 이 때 특정 인터페이스를 구현하는 과정을 거친다.
주로, 이벤트 핸들러 인터페이스는 functional interface이다.

이전에 익명 클래스를 다룰 때 JavaFX 예제를 봤는데, 다음과 같은 익명 클래스를 람다식으로 바꿀 수 있다.

```java
btn.setOnAction(new EventHandler<ActionEvent> {
    @Override
    public void handle(ActionEvent event) {
        System.out.println("Hello World!");
    }
});
```

```java
btn.setOnAction(
    event -> System.out.println("Hello World!")
);
```

`btn.setOnAction` 메소드 호출은 btn 객체를 선택할 시 어떤 이벤트가 발생할지를 셋팅해준다. 이 메소드는 `EventHandler<ActionEvent>` 타입의 객체를 요구한다. 그리고 `EventHandler<ActionEvent>` 인터페이스는 하나의 메소드만 가지고 있다 - `void handle(T event)`. 따라서 이 인터페이스는 functional interface이므로 람다식으로 바꿀 수 있는 것이다.

### Accessing Local Variables of the Enclosing Scope

로컬 클래스나 익명 클래스처럼 람다식은 변수를 capture할 수 있다. 람다식은 enclosing 스코프의 로컬 변수에 대해 로컬/익명 클래스와 동일한 접근성을 가진다.

그러나 로컬/익명 클래스와는 달리 람다식은 섀도잉과 관련된 이슈를 갖지 않는다. 람다식은 lexical하게 스코프(lexically scoped)된다. 이는 supertype으로부터 이름을 상속받거나 새로운 스코프 레벨을 만들지 않는다는 뜻이다. 람다식안에서의 선언은 enclosing 스코프에서 해석되는 것과 동일하게 처리된다. 다음 예시를 보자.

```java
import java.util.function.Consumer;

public class LambdaScopeTest {
    public int x = 0;

    class FirstLevel {
        public int x = 1;

        void methodInFirstLevel(int x) {
            int z = 2;

            Consumer<Integer> myConsumer = (y) ->
            {
                // The following statement causes the compiler to generate the error "Local variable z defined in an enclosing scope must be final or effectively final"
                // z = 99;

                System.out.println("x = " + x);
                System.out.println("y = " + y);
                System.out.println("z = " + z);
                System.out.println("this.x = " + this.x);
                System.out.println("LambdaScopeTest.this.x = " + LambdaScopeTest.this.x);
            };

            myConsumer.accept(x);
        }

        public static void main(String[] args) {
            LambdaScopeTest st = new LambdaScopeTest();
            LambdaScopeTest.FirstLevel f1 = st.new FirstLevel();
            f1.methodInFirstLevel(23);
        }
    }
}
```

Output:

```
x = 23
y = 23
z = 2
this.x = 1
LambdaScopeTest.this.x = 0
```

만약 `myConsumer` 람다식 선언에 파라미터로 y대신 x를 대입하면 컴파일 에러가 발생할 것이다 - "Lambda expression's parameter x cannot redeclare another local variable defined in an enclosing scope". 왜냐하면 람다식은 새로운 스코프 레벨을 만들지 않기 떄문이다. 따라서 enclosing 스코프에 있는 필드, 메소드 및 로컬 변수에 접근할 수 있다. 위 예시에서 람다식은 `methodInFirstLevel` 메소드의 파라미터 x에 바로 접근할 수 있는 것이다. Enclosing 클래스의 변수에 접근하기 위해서는 this 키워드를 쓰면된다 - `this.x`는 `FirstLevel.x` 멤버 변수이다.

그러나 로컬/익명 클래스처럼 람다식은 enclosing block에서 final 혹은 effectively final한 로컬 변수 및 파라미터에만 접근할 수 있다. 위에 예시에서는 z가 effectively final하지만 만약 주석을 풀어서 람다식 안에서 z 변수에 값을 대입하면 더 이상 effectively final하지 않게된다. 따라서 Java 컴파일러는 주석에 있는 컴파일 에러를 발생시킬 것이다.

### Target Typing
람다식의 타입을 어떻게 알까?

이전 예시에서 `public static void printPersons(List<Person> roster, CheckPerson tester)` 메소드와 `public void printPersonsWithPredicate(List<Person> roster, Predicate<Person> tester)`에서 람다식이 사용되었다.

Java runtime이 `printPersons` 메소드를 호출할 때, `CheckPerson` 데이터 타입을 예상하기 때문에 람다식은 그 타입이 되어야 한다. 또, Java runtime이 `printPersonsWithPredicate` 메소드를 호출할 때는 `Predicate<Person>` 타입을 예상하므로 람다식은 그 타입이 된다. 이런식으로 이 메소드들이 예상하는 데이터타입을 *target type*이라고 부른다. 람다식의 타입을 알아내기 위하여 Java 컴파일러는 context나 상황의 target type을 사용한다 - 그리고 람다식을 발견하게 되는 것이다.

따라서 Java 컴파일러가 target type을 알아낼 수 있는 상황에서만 람다식을 사용할 수 있다. 다음과 같은 상황에서 알아낼 수 있다:
- 변수 선언
- 대입
- 리턴 구문
- 배열 initializers
- 메소드 or 생성자 인자
- 람다식 바디
- 조건식 ?:
- 캐스트 식 (cast expression)

### Target Types and Method Arguments
메소드 인자에 대해서 Java 컴파일러는 두 가지 언어적 기능의 도움을 통해 target type을 알아낸다: overload resolution & type argument interface

다음 두 개의 function interface를 고려하자.

```java
public interface Runnable {
    void run();
}

public interface Callable<V> {
    V call();
}
```

`Runnable.run` 메소드는 값을 리턴하지 않고, `Callable<V>.call`은 값을 리턴한다.

`invoke` 메소드를 다음과 같이 오버로딩했다고 가정하자.

```java
void invoke(Runnable r) {
    r.run();
}

<T> T invoke(Callable<T> c) {
    return c.call();
}
```

다음 구문에서 어떤 메소드가 호출될까?

```java
String s = invoke(() -> "done");
```

`invoke(Callable<T>)`가 호출될 것이다. 왜냐하면 이 메소드가 값을 리턴하기 떄문이다. 따라서 람다식 `() -> "done"`의 타입은 `Callable<T>`가 되는 것이다.