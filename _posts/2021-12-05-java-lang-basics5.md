---
title: "Java - Language Basics: Classes and Objects"
---

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