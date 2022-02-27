---
title: "Java - Language Basics: Classes and Objects"
categories: 
  - Java Basics
---

이번 글에서는 Java의 핵심 요소인 클래스와 객체에 대해 알아보자. 압축시켜 노트를 하였으나, 그래도 여전히 내용은 많다.

클래스와 객체에 대한 기초 내용은 제대로 익히지 않으면 나중에 발목을 잡는 것들이니 중요하므로 천천히 알아보겠다.

## Classes
우리는 다음과 같은 형태로 클래스를 정의한다.

```java
class MyClass {
    // field, constructor, and
    // method declarations
}
```

이를 *class declaration(클래스 선언)*이라고 한다.

*class body*는 중괄호 사이에 있는 내용으로써 클래스로부터 생성되는 객체들의 라이프사이클을 책임지는 코드를 담고 있다: 새로운 객체를 생성하기 위한 constructor(생성자), 클래스와 그것의 객체들의 상태를 제공하는 (field declaration)필드 선언, 행위를 구현하는 (method declaration)메소드 선언.

제어자인 *public*과 *private*을 클래스 선언에 붙일 수도 있다. 이는 어떤 다른 클래스들이 MyClass를 접근할 수 있는지 결정한다.

요약하자면 클래스 선언에는 다음과 같은 구성요소들이 순서대로 온다:
1. public, private 등과 같은 제어자 (**NOTE**: private은 중첨 클래스에만 적용가능함)
2. 클래스 이름 (첫 문자는 컨벤션에 의해 대문자)
3. 클래스가 부모가 있다면 extend 키워드를 사용하여 명시
4. 클래스가 구현하는 인터페이스들이 있다면 implement 키워드로 명시
5. 클래스 바디

**NOTE**: 클래스의 필드는 다른 이름으로 member variable(멤버 변수)라고도 부른다.

Field declaration(필드 선언)에는 세 가지 구성요소가 다음과 같은 순서로 온다:
1. 0개 이상의 제어자 (public or private 등)
2. 필드 타입
3. 필드 이름

예를 들어, 이전의 Bicycle 클래스는 다음과 같은 필드 선언을 한다.

```java
public int cadence;
public int gear;
public int speed;
```

위 세 가지 필드는 모두 데이터 타입이 int이고, public 키워드는 이 필드들은 이 클래스를 접근할 수 있는 모든 객체들에 의해 접근 가능한 퍼블릭 멤버라는 것을 식별해준다.

### Access Modifiers(접근 제어자)
가장 왼쪽에 명시되는 제어자가 접근 제어자로써 다른 클래스들이 멤버 필드의 접근할 수 있는지를 명시한다. 현재로써는 public과 private만 생각해보자.

- public 제어자: 필드는 모든 클래스들로부터 접근 가능하다.
- private 제어자: 필드는 자기 클래스 안에서만 접근 가능하다.

Encapsulation(캡슐화)을 위해 필드를 private으로 만드는 것이 통상적이다. 따라서 필드가 Bicycle 클래스 안에서만 직접적으로 접근가능하다는 것이다. 외부에서 접근을 하려면 간접적으로 public 메소드를 통해 필드 값을 얻는다.

```java
public class Bicycle {
    private int cadence;
    private int gear;
    private int speed;

    public Bicycle(int startCadence, int startSpeed, int startGear) {
        gear = startGear;
        cadence = startCadence;
        speed = startSpeed;
    }
        
    public int getCadence() {
        return cadence;
    }
        
    public void setCadence(int newValue) {
        cadence = newValue;
    }
        
    public int getGear() {
        return gear;
    }
        
    public void setGear(int newValue) {
        gear = newValue;
    }
        
    public int getSpeed() {
        return speed;
    }
        
    public void applyBrake(int decrement) {
        speed -= decrement;
    }
        
    public void speedUp(int increment) {
        speed += increment;
    }
}
```

**NOTE**: 통상적으로 메소드 이름의 첫 단어(하나만 있다면 그 단어)는 동사이다.

### Defining Methods
메소드 정의에 대해서 좀 더 자세히 알아보자.

전형적인 메소드 선언은 다음과 같다.

```java
public double calculateAnswer(double wingSpan, int numberOfEngines, double length, double grossTons) {
    // do calculation here
}
```

더 일반적으로 메소드 선언은 다음과 같이 6개의 구성요소를 순서대로 가진다:
1. 제어자 - public, private 등
2. 리턴 타입
3. 메소드 이름
4. 파라미터 리스트 (괄호 안에)
5. 예외 리스트 (Exception list)
6. 메소드 바디

<u>Definition</u>

메소드 선언에서 두 개의 구성 요소 **메소드 이름 & 파라미터 타입**은 *method signature(메소드 시그니쳐)*가 된다.

위 예시의 메소드 시그니쳐는 `calculateAnswer(double, int, double, double)`이다.

### Naming a Method
메소드의 이름을 지을 때 컨벤션에 의해 동사로 시작해야 한다.

### Overloading Methods
Java 언어는 *method overloading(메소드 오버로딩)*을 지원하여 서로 다른 메소드 시그니쳐를 가진 메소드들을 구분할 수 있다. 즉, 한 클래스 안에서 서로 다른 파라미터 리스트를 가진다면 동일한 이름을 가질 수 있다는 말이다.

예를 들어 다양한 데이터 (string, integer, 등)를 그릴 수 있는 메소드를 가진 캘리그래피에 대한 클래스가 있다고 가정하자. 각 데이터 타입에 대해 새로운 메소드 이름을 사용하는 것은 번거로울 것이다 - drawString, drawInteger, 등. 따라서 이름은 동일하게 하되, 다른 파라미터 리스트를 가지게 하면 된다.

```java
public class DataArtist {
    ...
    public void draw(String s) {
        ...
    }

    public void draw(int i) {
        ...
    }

    public void draw(int i, double f) {
        ...
    }
}
```

**NOTE**: Java Tutorial에는 explicitly하게 명시하지는 않는 부분이 파라미터 개수/종류가 같을 경우 메소드 파라미터 순서에 대해서이다. 컴파일러는 메소드 오버로딩을 시그니쳐가 다른 것으로 구분하기 때문에, 위 클래스에 `public void draw(double f, int i)`를 추가해도 이 또한 메소드 오버로딩이 될 것이다.

### Constructors
클래스는 객체를 생성할 때 호출되는 생성자를 가진다. 생성자 선언은 메소드 선언과 비슷하다, 그러나 클래스 이름을 사용하고 리턴 타입이 없다.

```java
public Bicycle(int startCadence, int startSpeed, int startGear) {
    gear = startGear;
    cadence = startCadence;
    speed = startSpeed;
}
```

이와 같은 생성자가 있을 때, 새로운 Bicycle 객체를 만들려면, new 연산자를 통해 생성자를 호출한다.

```
Bicycle myBike = new Bicycle(30, 0, 8);
```

`new Bicycle(30, 0, 8)`은 객체를 위한 새로운 공간을 메모리에 생성하고 필드들을 초기화한다.

현재로써는 Bicycle은 하나의 생성자만 가지고 있지만, no-argument 생성자를 포함한 여러 개의 생성자를 가질 수도 있다.

```java
public Bicycle() {
    gear = 1;
    cadence = 10;
    speed = 0;
}
```

`new Bicycle()`은 no-argument 생성자를 호출하게 된다.

메소드 선언처럼 Java 플랫폼은 파라미터 리스트에 따라 생성자를 구분할 수 있다. 또, 클래스에 생성자를 명시하지 않아도 된다. 컴파일러는 자동으로 no-argument 생성자를 만들어준다. 이 디폴트 생성자는 superclass의 no-argument 생성자를 호출한다. 이 경우, 만약 superclass가 no-argument 생성자가 없으면 컴파일 에러가 발생한다. 만약 우리의 클래스가 explicit한 superclass가 없다면, 이는 implicit superclass인 Object를 상속 받고, 이 Object 클래스는 no-argument 생성자가 있다.

**NOTE**: 생성자 선언에서 접근 제어자를 사용하여 어떤 다른 클래스들이 생성자를 호출할 수 있는지를 제한할 수 있다. 즉, 다른 클래스가 MyClass 생성자를 부를 수 없다면, 그것은 직접적으로 MyClass 객체를 생성할 수 없다는 의미이다.

**NOTE**: Parameter와 Argument(인자)가 헷갈리는 경우가 많다. 정확히 말하면 파라미터는 메소드 선언에서의 변수들의 리스트를 말하고, 인자는 메소드가 호출될 때 전달되는 실제 값들을 말한다. 즉, 런타임 시, 메소드 바디에서 사용되는 파라미터들이 전달된 인자들의 값이 되는 것이다.

### Arbitrary Number of Arguments
*varargs*라는 construct를 사용하여 한 메소드에게 임의의 개수의 값들을 전달할 수 있다. 메소드에게 몇 개의 특정 타입의 인자가 전달될지 모를 때 사용할 수 있다. 어떻게 보면 수동으로 배열을 만드는 것의 숏컷이라고 볼 수 있다.

varargs를 사용하려면 타입 뒤에 ellipsis(...)를 붙여 주고, 공백 하나, 그리고 파라미터 이름을 적어주면 된다. 그러면 메소드는 0개 이상의 파라미터 개수로 호출될 수 있다.

```java
public Polygon polygonFrom(Point... corners) {
    int numberOfSides = corners.length;
    double squareOfSide1, lengthOfside1;
    squareOfSide1 = (corners[1].x - corners[0].x)
                    * (corners[1].x - corners[0].x)
                    * (corners[1].y - corners[0].y)
                    * (corners[1].y - corners[0].y);
    lengthOfSide1 = Math.sqrt(squareOfSide1);
}
```

위 코드를 보면, corners는 메소드 바디에서 배열처럼 다루어지는 것을 볼 수 있다.

주로 출력 메소드에서 varargs 쓰는 것을 많이 볼 것이다. 예를 들어 printf 메소드를 보면:

```java
public PrintStream printf(String format, Object... args)
```

이는 임의의 개수의 객체를 출력하게 해준다. 다음과 같이 호출할 수 있다.

```java
System.out.printf("%s: %d, %s\n", name, idnum, address);
// or
System.out.printf("%s: %d, %s, %s, %s\n", name, idnum, address, phone, email);
```

### Parameter Names
클래스의 메소드나 생성자에서 파라미터를 명시할 때, 이름을 정해야 한다. 이 이름은 메소드 바디에서 전달된 인자를 참조할 때 사용된다.

파라미터의 이름은 이 스코프안에서 고유해야 한다. 즉, 동일한 메소드에서 두 개의 같은 파라미터 이름을 사용할 수 없고, 메소드 안에 있는 로컬 변수의 이름과도 같을 수 없다.

단, 파라미터는 클래스 필드와 같은 이름을 가질 수 있다. 이런 경우, 파라미터가 필드를 *shadow*(섀도잉)한다고 한다. 필드를 섀도잉하면 코드의 가독성이 떨어지고, 컨벤션을 따르면 생성자나 메소드에서 특정 필드를 set할 때만 사용된다. 예를 들면 다음과 같다.

```java
public class Circle {
    private int x, y, radius;
    public void setOrigin(int x, int y) {
        ...
    }
}
```

### Passing Primitive Data Type Arguments
int나 double같은 primitive 인자들은 메소드에게 *value(값)*으로 전달된다. 즉, 파라미터의 값의 변경은 메소드 스코프 내에서만 일어나고, 메소드가 리턴하면 파라미터들은 없어지고 변경 또한 손실된다.

```java
public class PassPrimitiveByValue {
    public static void main(String[] args) {
        int x = 3;

        // invoke passMethod() with x as argument
        passMethod(x);

        // print x to see if its value has changed
        System.out.println("After invoking passMethod, x = " + x);
    }

    // change parameter in passMethod
    public static void passMethod(int p) {
        p = 10;
    }
}
```

아웃풋:

```
After invoking passMethod, x = 3
```

따라서 x의 값은 변하지 않은 것을 볼 수 있다.

### Passing Reference Data Type Arguments
객체와 같이 reference 데이터 타입 인자들 또한 메소드에게 값으로 전달된다. 그리고 메소드가 리턴하면 전달된 참조값은 전과 동일한 객체를 참조하게 된다. 그러나, 객체의 필드 값은 메소드 안에서 바뀔 수 있다.

예를 들어, Circle 객체를 움직이는 메소드를 살펴보자.

```java
public void moveCircle(Circle circle, int deltaX, int deltaY) {
    // move origin of cicle to x+deltaX, y+deltaY
    circle.setX(circle.getX() + deltaX);
    circle.setY(circle.getY() + deltaY);

    // assign a new reference to circle
    circle = new Circle(0, 0);
}
...
// invoke above method
moveCircle(myCircle, 23, 56);
```

위 코드를 보면, 메소드 안에서 circle은 처음에 myCircle을 가리킨다(refer to). 메소드에서 circle이 가리키는 myCircle의 x 및 y 좌표에 23, 56씩 더해준다. 이 변경은 메소드가 리턴하고도 유지될 것이다. 그 후, circle은 새로운 Circle 객체(x = y = 0)에 대한 reference를 할당 받는다. 메소드 내에서 circle이 가리키는 객체는 변경하였지만 메소드가 리턴하면 myCircle은 메소드가 호출되었을 때 가리키던 Circle 객체를 아직 가리키고 있을 것이다. 다시 말해, 파라미터가 메소드 내에서 새로운 객체를 가리키더라도, 그로 인해 전달된 객체 인자가 가리키는 객체는 변하지 않는다. 포인터 개념으로 생각하면 당연한 결과인 것 같다.

### Creating Objects
```java
Point OrigineOne = new Point(23, 94);
```

위와 같은 객체를 생성하는 구문이 있다. 그러면 우리는 3가지 과정으로 이를 나눈다.
1. **Declaration(선언)**: `Point OriginOne` 부분으로, 변수 이름과 객체 타입을 연관시키는 과정
2. **Instantiation(인스턴스화)**: new 연산자를 통해 객체를 생성하는 과정
3. **Initialization(초기화)**: new 연산자 후에 생성자를 호출하여 새로운 객체를 초기화하는 과정

### Declaring a Variable to Refer to an Object
우리는 변수를 선언할 때, `type name`으로 한다.

이는 컴파일러에게 "우리는 *name*을 사용하여 *type*의 데이터 타입을 가진 데이터를 참조하겠다."라고 말하는 것이었다.

Primitive 변수에 대해서 이 선언문에 의해 해당 변수를 위해 적절한 양의 메모리가 할당된다.

Reference 변수 또한 `Point originOne`처럼 선언할 수 있다.

이런식으로 originOne을 선언하면, 객체가 실제로 생성되고 할당될 때 까지 그것의 값은 결정되지 않는다. 즉, 단순히 reference 변수를 선언하는 것은 객체를 생성해주지 않는다. 따라서 new 연산자를 사용하여 객체를 생성하고 originOne에 할당을 해야 사용할 수 있다.

현재는 originOne 변수는 어떠한 객체도 참조하지 않는다. 다음 그림을 보면, 아무것도 가리키지 않는 reference를 가진, 즉, null을 가리키는 originOne변수를 위한 공간이 만들어진 것을 볼 수 있다.

![Object references Nothing](/assets/img/object_ref_nothing.gif)

### Instantiating a Class
new 연산자를 사용하면 새로운 객체를 위한 메모리를 할당하고 그 메모리 공간에 대한 reference를 리턴함으로써 클래스를 인스턴스화할 수 있다.

또, new 연산자는 객체 생성자를 호출한다.

**NOTE**: 클래스 인스턴스화와 객체 생성은 같은 의미를 가진다. 객체를 생성한다는 것은 클래스의 인스턴스를 생성한다는 것이기 때문이다.

new 연산자는 postfix 인자로 생성자를 갖고, 생성되는 객체에 대한 reference를 리턴한다. 이 reference는 주로 적합한 타입의 변수에 할당된다. 다음 예시는 Point 타입의 변수 originOne에게 할당된다.

```java
Point originOne = new Point(23, 94);
```

new 연산자에 의해 리턴된 reference는 변수에 할당되지 않아도 된다. 식 안에서 바로 쓰일 수도 있다.

```java
int height = new Rectangle().height;
```

### Intializing an Object
Point 클래스 코드는 다음과 같다.

```java
public class Point {
    public int x = 0;
    public int y = 0;

    public Point(int a, int b) {
        x = a;
        y = b;
    }
}
```

이 클래스는 하나의 생성자를 갖고있다.

`Point originOne = new Point(23, 94);`구문의 실행은 다음 그림을 보며 이해해보자.

![Object Intialization](/assets/img/object_initialize.gif)

new Point(23, 94)에 의해 x = 23, 94 = y 필드를 가진 새로운 Point 객체가 생성되어, originOne 변수가 이를 가리키고 있다. 즉, originOne이 해당 객체에 대한 reference를 저장하고 있다고 보면 된다.

다음은 4개의 생성자를 갖고 있는 Rectangle 클래스 코드이다.

```java
public class Rectangle {
    public int width = 0;
    public int height = 0;
    public Point origin;

    public Rectangle() {
        origin = new Point(0, 0);
    }

    public Rectangle(Point p) {
        origin = p;
    }

    public Rectangle(int w, int h) {
        origin = new Point(0, 0);
        width = w;
        height = h;
    }

    public Rectangle(Point p, int w, int h) {
        origin = p;
        width = w;
        height = h;
    }

    // moves the rectangle
    public void move(int x, int y) {
        origin.x = x;
        origin.y = y;
    }

    // computes the area of the rectangle
    public int getArea() {
        return width * height;
    }
}
```

각 생성자는 특정 초기 값(primitive 타입과 reference 타입)들을 받는다.

만약 `Rectangle rectOne = new Rectangle(originOne, 100, 200);` 구문이 실행되면, Java 컴파일러는 생성자 시그니쳐를 통해 정확히 어떤 생성자를 호출할지 안다. 자, 이제 똑같은 Point 객체에 대해 두 개의 reference가 존재한다 - 한 객체에 대해 여러 개의 reference가 있을 수 있다. 다음 그림을 참고하자.

![Object Multiple References](/assets/img/objects_multiple_refs.gif)

**NOTE**: `int height = new Rectangle().height;`과 같이 객체를 생성하고 이에 대한 reference를 저장하지 않는다면, 이 Rectangle 인스턴스에 대한 reference는 프로그램 그 어디에도 저장되지 않는다. 따라서 이 객체는 unreferenced하고 그것의 자원은 JVM에 의해 재활용될 수 있다.

### A bit about the Garbage Collector
몇몇 객체지향 언어는 우리가 생성하는 모든 객체들을 추적하고, 그것들이 더이상 필요하지 않을 때, 직접 소멸시키는 것을 요구한다. 메모리를 explicit하게 직접적으로 관리하는 것은 번거롭고 에러가 쉽게 발생할 수 있다. Java 플랫폼은 우리가 원하는만큼의 객체를 생성할 수 있게 해주고, 우리는 객체들을 소멸시키는 것은 걱정안해도 된다. Java 런타임 환경(JRE)은, 사용되고 있지 않은 객체들을 알아서 찾아서 삭제시켜준다. 이 프로세스를 *garbage collection*이라고 한다.

어떠한 객체에 대한 reference가 더 이상 존재하지 않는다면, 그것은 garbage collection의 대상이 될 자격이 있다. 변수가 담고 있는 reference들은 주로 변수의 스코프를 벗어날 경우 없어진다. 혹은, 변수에 null을 할당함으로써 객체 reference를 explicit하게 없앨 수 있다.

JRE는 garbage collector를 가지고 있어, 주기적으로 reference를 가지지 않는 객체들에 의해 사용되는 메모리를 free시켜준다. Garbage collector는 적절한 시간을 판단하여 자신의 프로세스를 수행한다. Java가 가지는 매우 편리한 기능이라고 생각된다.

### Returning a Value from a Method
메소드는 다음 중 하나의 경우 리턴한다.
- 메소드의 모든 구문을 완료했을 때
- return 구문에 도달하였을 때
- exception(예외)를 날릴 때

모든 메소드 (void 메소드 제외)는 return 구문을 포함해야 하고, 선언한 리턴 타입을 가지는 값을 리턴해야 한다.

### Returning a Class or Interface
메소드의 리턴 타입이 클래스 이름일 경우, 리턴되는 객체의 타입의 클래스는 리턴 타입의 클래스, 혹은 subclass여야 한다. 다음과 같이 Object의 subclass인 java.lang.Number의 subclass인 ImaginaryNumber 계층이 있다고 해보자.

![ImaginaryNum class Hierarchy](/assets/img/imgnum_class_hier.gif)

그리고, Number를 리턴하는 메소드를 선언한다.

```java
public Number returnANumber() {
    ...
}
```

`returnANumber` 메소드는 ImaginaryNumber를 리턴할 수 있지만 Object를 리턴할 수는 없다.

왜? ImaginaryNumber는 Number이다 - Number의 subclass이기 떄문에. 그러나 Object는 Number가 아닐 수도 있다 - String이나 다른 타입일 수도 있다.

메소드 오버라이딩을하여 원래 메소드의 subclass를 리턴하도록 할 수도 있다.

```java
public ImaginaryNumber returnANumber() {
    ...
}
```

이런식으로 원래 메소드의 subclass 방향으로 리턴 타입에 변경을 주어 메소드를 오버라이딩 하는 기법을 *covariant return type*이라고 한다.

**NOTE**: 리턴 타입이 인터페이스 이름일 수도 있다. 그럴 경우, 리턴되는 객체는 명시된 인터페이스를 구현해야만 한다.

### Recall the 'this' Keyword
인스턴스 메소드나 생성자에서 this는 *current object(현재 객체)*에 대한 reference이다. 현재 객체라함은 현재 호출되고 있는 메소드/생성자의 주인이다. 이 this를 사용하여 현재 객체의 멤버들에 접근할 수 있다.

this를 사용하는 가장 흔한 이유는 필드가 메소드/생성자 파라미터에 의해 섀도잉되기 떄문이다.

```java
public class Point {
    public int x = 0;
    public int y = 0;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```

생성자 내에서 this 키워드를 사용하여 같은 클래스에 있는 다른 생성자를 호출할 수 있다. 이를 *explicit constructor invoation*이라고 부른다. 다음 예시를 보자.

```java
public class Rectangle {
    private int x, y;
    private int width, height;

    public Rectangle() {
        this(0, 0, 1, 1);
    }

    public Rectangle(int width, int height) {
        this(0, 0, width, height);
    }

    public Rectangle(int x, int y, int width, int height) {
        this.x = x;
        this.y = y;
        this.width = width;
        this.height = height;
    }
    ...
}
```

### Controlling Access to Members of a Class
Access level modifiers(접근 제어자)는 다른 클래스들이 특정 필드나 메소드에 접근할 수 있는지를 결정한다. 접근 제어에는 두 개의 레벨이 있다.
- Top level - public / package-private (no explicit modifier)
- Member level - public / private / protected / package-private (no explicit modifier)

클래스는 public으로 선언될 수 있다. 그럴 경우, 그 클래스는 모든 곳에서 모든 클래스들에게 보일 수 있다. 만약 클래스가 디폴트로 제어자가 없으면, package-private인 것이고, 자신의 패키지안에서만 보인다.

멤버 레벨에서는 탑 레벨처럼 public 제어자, 혹은 아무런 제어자를 안쓰면 package-private인 디폴트 제어자를 사용할 수 있다. 또, private 및 protected 제어자도 가능한데, private으로 선언되면 해당 멤버가 자신의 클래스 안에서만 접근될 수 있다. protected로 선언되면 멤버는 자신의 패키지에 더해, 다른 패키지에 있는 자기 클래스의 subclass에 의해 접근될 수 있다.

접근 레벨은 우리에게 두 가지 영향을 준다.
1. Java 플랫폼안의 클래스들과 같이 다른 소스로부터 온 클래스들을 사용할 때, 우리의 클래스가 그 클래스들의 어떤 멤버들을 사용할 수 있는지를 접근 레벨이 결정해준다.
2. 클래스를 작성할 때, 우리는 멤버 변수 및 멤버 메소드들이 어떤 접근 레벨을 가질지를 결정해야 한다.

**접근 레벨을 정하는 팁?**
다른 프로그래머들이 나의 클래스를 사용할 때, 오용으로 인해 에러가 발생하면 안된다. 접근 제어로 이를 어느정도 방지할 수 있다.
- Constant를 제외하고는 public field를 피하는 것이 좋다.

### Understanding Class Members
이전에 class variable를 다시 기억해보자. static 키워드를 사용하여 필드 및 메소드를 만드는 것이었다.

여러 개의 객체들이 동일한 클래스 블루프린트로부터 생성된다면, 각 객체는 자신만의 instance variable 카피를 갖게 된다. 이전 예시에서 Bicycle 객체들이 서로 다른 cadence, gear, speed 인스턴스 변수 값을 서로 다른 메모리 위치에 갖듯이 말이다.

때때로는 모든 객체들이 공통으로 가지는 변수들이 필요할 수도 있다. 이를 static 제어자를 통해 만들 수 있다. static 제어자를 선언 시에 갖는 필드들을 *static field*혹은 *class variable*이라고 한다. 그 필드는 객체보다는 클래스와 연관이 있다. 그 클래스에 대한 모든 인스턴스는 클래스 변수를 공유하고, 그 클래스 변수는 고정된 메모리 위치에 존재한다. 모든 객체는 클래스 변수의 값을 변경할 수 있고, 또, 클래스를 인스턴스화하지 않고도 그 값이 변경될 수도 있다.

예를 들어, 여러 개의 Bicycle 객체를 만들어 1부터 시작하여 각각 시리얼 넘버를 매긴다고 해보자. 각 객체는 고유한 ID 넘버를 갖게되므로 이는 instance variable이다. 동시에, 몇 개의 Bicycle 객체가 생성되었는지를 기록하는 필드도 필요하다 - 다음 객체의 ID가 뭐가 되야할지 알아야하기 때문에. 이 필드는 특정 하나의 객체와 관련 있는 필드가 아니고, 전체적인 클래스와 연관이 있다. 따라서 이를 위해서는 클래스 변수 numberOfBicycles가 필요하다.

```java
public class Bicycle {
    private int cadence;
    private int gear;
    private int speed;

    // adds an instance variable for the object ID
    private int id;

    // adds a class variable for the number of Bicycle objects instantiated
    private static int numberOfBicycles = 0;

    public Bicycle(int cadence, int gear, int speed) {
        this.cadence = cadence;
        this.gear = gear;
        this.speed = speed;
        id = ++numberOfBicycles; // increment number of bikes and assign the id number in constructor
    }
}
```

**NOTE**: 클래스 변수는 클래스 이름에 의해 reference된다 => `Bicycle.numberOfBicycles`. 클래스 변수로써는 당연하고 자연스러운 reference 저장 방법인 것 같다.<br>
`myBike.numberOfBicycles`처럼 객체 reference를 통해서도 static 필드에 접근할 수는 있다. 하지만 클래스 변수라는 것이 불명확히지기 때문에 이런 방식은 추천하지 않는다.

자, 그럼 class method(클래스 메소드)는 어떨까?

Java 언어는 static 메소드도 지원한다. Static 메소드는 클래스 이름으로 호출되어 인스턴스를 생성할 필요가 없다. 필드와 같이 객체로 호출도 가능하지만 동일한 이유로 추천하지 않는다.

Static 메소드를 사용하는 흔한 이유는, 당연하고 자연스럽게도 static 필드에 접근하기 위해서이다. 다음과 같이 Bicycle 클래스에 static method를 추가하여 numberOfBicycles에 접근할 수 있다.

```java
public static int getNumberOfBicycles() {
    return numberOfBicycles;
}
```

인스턴스 변수/메소드와 클래스 변수/메소드를 혼합하여 사용할 때 제한 사항은 다음과 같다:
- **인스턴스 메소드**는 **인스턴스 변수**와 **인스턴스 메소드**에 바로 접근할 수 있다.
- **인스턴스 메소드**는 **클래스 변수**와 **클래스 메소드**에 바로 접근할 수 있다.
- **클래스 메소드**는 **클래스 변수**와 **클래스 메소드**에 바로 접근할 수 있다.
- **클래스 메소드**는 **인스턴스 변수**와 **인스턴스 메소드**에 **바로 접근할 수 없다** - 객체 reference를 사용해야만 한다. 또, 클래스 메소드는 this가 가리킬 수 있는 인스턴스가 없기 때문에 this 키워드를 사용할 수 없다.

예를 들어, Java에 처음 접할 때 다음과 같은 컴파일 에러가 항상 있었다.

```java
public class Main {
    public static void main(String[] args) {
        // some code
        ...
        // invoke instance method Foo
        foo();
    }

    public void foo() {
        return;
    }
}
```

위와 같이 클래스 메소드인 main에서 인스턴스 메소드인 foo를 호출하려하면 `error: non-static method foo() cannot be referenced from a static context`라는 컴파일 에러가 발생한다. 따라서, `new Main().foo()`처럼 객체 reference를 만들거나, 혹은 foo를 static으로 선언하여 에러를 피할 수 있다. 물론 에러를 피하는 것은 둘째치고 필요에 따라 잘 결정해야 한다.

### Constants
static과 final 제어자를 같이 써서 constant를 정의할 수 있다. final 제어자는 필드 값이 변경될 수 없다는 것을 명시한다.

예를 들어, 다음 변수 선언은 PI라는 constant를 정의한다.

```java
static final double PI = 3.141592653589793;
```

이런식으로 constant를 정의하면 값이 재할당될 수 없고, 시도를 한다면 컴파일 에러가 날 것이다.

**NOTE**: Primitive 타입이나 string이 constant로 정의되고, 그 값을 컴파일 타임때 알고 있다면, 컴파일러는 코드에 있는 모든 해당 constant 이름을 그 값으로 대체한다. 이를 *compile-time constant*라고 부른다. 만약 바깥에서 constant의 값이 바뀌면, 새로운 값을 쓰기 위해 해당 constant를 쓰는 클래스를 recompile해야 한다.

### Initializing Fields
필드 선언 시, 다음과 같이 초기값을 줄 수 있다.

```java
public class BedAndBreakfast {
    public static int capacity = 10;
    private boolean full = false;
}
```

이는 간단한 초기화의 경우 한 줄로 가능한거지만, 만약 더 복잡한 초기화 과정이 필요하다면 간단한 대입으로 끝나지 않는다. 인스턴스 변수는 생성자에서 초기화될 수 있고, 클래스 변수는 Java에서는 *static initialization block*에서 초기화될 수 있다.

Static initialization block은 중괄호로 감싸지는 코드이다. 다음과 같이 적는다.

```java
static {
    // whatever code is needed for initialization goes here
}
```

한 클래스는 여러 개의 static initialization block을 가질 수 있고, 클래스 바디 어디에 있든 상관 없다. 런타임 시스템은 소스코드에 나타나는 순서대로 static 초기화 블록을 호출한다.

static 블록 대신, private static method를 사용할 수도 있다.

```java
class Whatever {
    public static varType myVar = initializeClassVariable();

    private static varType initializeClassVariable() {
        
        // initialization code goes here

    }
}
```

private static 메소드의 장점은, 클래스 변수를 재초기화해야할 경우 나중에 다시 사용할 수 있다는 점이다.

아까 말했듯이 인스턴스 변수는 생성자안에서 초기화될 수 있다. 그 외에도 인스턴스 변수를 초기화할 수 있는 방법으로 initializer block과 final method가 있다.

- Initializer block은 static initializer block과 비슷하다. 다음과 같이 사용한다.

```java
{
    // whatever code is needed for initialization goes here
}
```

Java 컴파일러는 initializer block을 모든 생성자에 복사한다. 따라서 위 코드 블록을 한 번 적어서 생성자들에게 공유할 수 있는 효과가 있다.

final 메소드는 subclass에서 오버라이딩할 수 없다. 다음 예제는 final 메소드를 통해 인스턴스 변수를 초기화한다.

```java
class Whatever {
    private varType myVar = initializeInstanceVariable();

    protected final varType initializeInstanceVariable() {

        // initialization code goes here

    }
}
```

만약 subclass가 initialization method를 재사용하고 싶을 경우 이는 유용하다. Non-final 메소드를 인스턴스 초기화 중에 호출하는 것은 문제를 일으킬 수 있어서 이 메소드는 final이다.