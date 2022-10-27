---
title: "객체지향 프로그래밍의 개념"
---

## 객체지향 프로그래밍 (Object-Oriented Programming) 개념

Java는 객체지향적 프로그래밍 언어이고, 해당 언어로 프로그래밍을 하려면 OOP 개념을 잘 알아야 하는 것은 기본이다. OOP의 기본 개념을 짚고 넘어가자.

## Object란?

객체(object)란 서로 관련된 상태 및 행위들을 포함하는 소프트웨어 묶음(software bundle)이다. 소프트웨어 객체는 우리가 현실 세계에서 매일 볼 수 있는 것들을 모델링하기 위해 주로 사용된다.

객체지향 기술을 이해하는데 있어 객체는 핵심 요소이다. 지금 주위에 있는 모든 것들, 강아지, 책상, 텔레비젼, 컴퓨터, 이 모든 것들이 현실 세계의 객체들이다. 현실 세계의 객체들은 두 가지 특징을 가지고 있다: 상태(state)와 행위(behavior)을 가진다. 강아지들은 이름, 색깔, 종, 배고픔 등의 상태와 짖기, 물어오기, 꼬리흔들기 등의 행동을 가질 수 있다. 자전거도 현재 기어, 현재 페달 회전 속도, 주행 속도 등의 상태와 기어 변경하기, 페달 속도 변경하기, 브레이크 잡기의 행위를 가질 수 있다. 현실 세계 객체들의 상태와 행위를 식별하는 것은 객체지향적으로 생각하는 시작점이다.

잠시 동안 주위를 둘러보자. 보이는 각 물체(객체)에 대하여 두 가지 질문을 해보자.

1. 이 물체가 현재 어떤 상태에 있는가?
2. 이 물체가 어떠한 행위를 할 수 있는가?

관찰한 것들을 적어보자. 하면서 느끼겠지만 현실 세계의 객체들은 다양한 복잡성을 가진다. 램프의 경우 두 가지 상태(on & off)와 두 가지 행위(켜기, 끄기)를 가지지만 라디오는 현재 볼륨, 현재 주파수 등의 추가적 상태와 볼륨 높이기/줄이기, 탐색, 스캔, 튜닝 등의 추가적인 행위를 가질 것이다. 또한, 몇몇 객체들은 다른 객체들을 포함하는 형태일 것이다. 이러한 현실 세계의 관찰들이 객체지향 프로그래밍의 세계로 변환된다.

소프트웨어 객체는 개념적으로 현실 세계 객체들과 비슷하다. 소프트웨어 객체도 상태와 행위를 갖고 있다. 객체는 필드(field)에 그것의 상태를 저장하고 메소드(method)를 통해 행위를 노출한다.

**NOTE**: 몇몇 프로그래밍 언어에서는 field를 variable(변수), method를 function(함수)이라고 칭한다.

메소드는 객체의 내부 상태에 대해 동작하고, 객체 대 객체 소통을 위한 주요 메커니즘이 된다. 내부 상태를 숨기고 객체의 메소드를 통해 상호작용을 수행하는 것을 요구하는 개념을 데이터 캡슐화 (data encapsulation)라고 한다 - 객체지향 프로그래밍의 근본적인 원칙 중 하나이다.

예를 들어 자전거 객체를 생각해보자. 현재 속도, 현재 페달 속도, 현재 기어와 같은 상태를 주고, 상태를 변경할 수 있는 메소드를 줌으로써 현실 세계에서 자전거에게 허용되는 제한 사항들을 소프트웨어상의 자전거 객체에 적용할 수 있다. 예를 들어 자전거가 기어가 6단까지 있으면 기어를 변경하는 메소드에서 1보다 작은, 그리고 6보다 큰 기어는 거부할 수 있다.

각각의 소프트웨어 객체에 코드를 번들링(묶음)함으로써 다음과 같은 여러 이점이 생긴다:

- Modularity(모듈화): 어떠한 객체에 대한 소스 코드는 다른 객체들의 소스코드와 독립적으로 작성되고 관리될 수 있다. 한 번 생성되면 객체는 시스템 안에서 손쉽게 전달될 수 있다.
- Information-hiding(정보 은닉화): 객체의 메소드와 상호작용함으로써 내부 구현에 대한 세부사항을 바깥세계로부터 숨길 수 있다.
- Code re-use(코드 재사용): 객체가 이미 존재한다면 (내가 쓰거나 혹은 다른 개발자에 의한), 그 객체를 내 프로그램에서 사용할 수 있다. 이렇게 재사용이 가능하여 기술자들이 구현/테스트/디버그한 복잡하고 특정 태스크 용도의 객체를 내 코드에서 실행할 수 있다.
- Pluggability(착탈식) & Debugging ease(디버깅 용이성): 특정 객체에 문제가 있으면 단순히 그거를 내 어플리케이션에서 제거하고, 그 대신에 다른 객체를 넣을 수 있다. 이는 현실 세계에서 기계적 문제를 고치는 것과 유사하다. 볼트가 부서지면, 전체 기계가 아닌 해당 볼트만 교체하는 것이다.

## Class란?

현실 세계에서는 각각의 객체들이 서로 똑같은 종류인 경우가 많다. 수천, 수만 개의 같은 모델의 자전거가 있을 것이다. 각 자전거는 똑같은 블루프린트(도면)를 기반으로 만들어졌을 것이고, 따라서 똑같은 구성요소들을 가질 것이다. 객체지향적 용어로 하나의 자전거는 자전거라는 객체들의 *클래스*의 하나의 *인스턴스*라고 말한다. 즉, 각각의 객체는 클래스라는 블루프린트로부터 생성되는 것이다.

다음과 같이 Bicycle 클래스를 구현할 수 있을 것이다.

```java
class Bicycle {
    int cadence = 0;
    int speed = 0;
    int gear = 1;

    void changeCadence(int newValue) {
        cadence = newValue;
    }

    void changeGear(int newValue) {
        gear = newValue;
    }

    void speedUp(int increment) {
        speed = speed + increment;
    }

    void applyBrakes(int decrement) {
        speed = speed - decrement;
    }

    void printStates() {
        System.out.println("cadence:" +
            cadence + " speed:" +
            speed + " gear:" + gear);
    }
}
```

**NOTE**: 모든 Java 어플리케이션에는 main함수가 존재해야 한다. 근데 Bicycle 클래스에는 main 메소드가 없다. 왜? 이 클래스로가 전체 어플리케이션이 아니기 때문이다. 이 클래스는 단지 어플리케이션에서 사용될 수 있는 자전거를 위한 블루프린트일 뿐이다. 새로운 자전거를 생성하여 사용하는 것은 우리 어플리케이션의 다른 부분의 몫이다.
예를 들어, BicycleDemo 클래스에서 다음과 같이 쓰일 수 있다.

```java
class BicycleDemo {
    public static void main(String[] args) {
        // Create two different
        // Bicycle objects
        Bicycle bike1 = new Bicycle();
        Bicycle bike2 = new Bicycle();

        // Invoke methods on
        // those objects
        bike1.changeCadence(50);
        bike1.speedUp(10);
        bike1.changeGear(2);
        bike1.printStates();

        bike2.speedUp(20);
        bike2.changeGear(3);
        bike2.applyBrakes(10);
        bike2.printStates();
    }
}
```

아웃풋은 다음과 같을 것이다.
cadence: 50 speed: 10 gear: 2
cadence: 0 speed: 10 gear: 1

## Inheritance란?

서로 다른 종류의 객체들도 때때로 공통적인 부분을 가질 수 있다. 산악 자전거, 로드 자전거, 2인용 자전거는 모두 자전거의 특징(현재 속도, 현재 페달 속도, 현재 기어)을 갖는다. 그러나 각 자전거는 자신만의 추가적인 기능 또한 가질 것이다. 2인용 자전거는 두 개의 좌석과 핸들바가 있을 것이고, 로드 자전거는 드롭핸들바를 가지고, 산악 자전거는 체인 링을 가질 수도 있다.

객체지향 프로그래밍에서는 클래스가 공통적으로 사용되는 상태 및 행위를 다른 클래스로부터 *상속*받을 수 있다. 예를 들어 Bicycle이 *superclass*가 되고, MountainBike, RoadBike, TandemBike는 그것의 *subclass*들이 된다. Java 언어에서는 각 클래스가 한 개의 직접적인 superclass를 가질 수 있고, superclass는 무제한의 subclass들을 가질 수 있다.

```java
class MountainBike extends Bicycle {

    // new fields and methods defining
    // a mountain bike goes here
}
```

위와 같이 `extends` 키워드를 통해 상속을 받을 수 있고, 이로 인해 MountainBike는 Bicycle과 동일한 필드 및 메소드를 갖음과 동시에, 산악 자전거만의 고유 기능에 집중해서 구현할 수 있게 해준다. 이로 인해 subclass의 코드 가독성이 더 좋아진다. 단, subclass에서는 superclass의 상태 및 행위가 나타나지 않으므로, superclass에서 정의하는 상태 및 행위를 superclass에서 확실하고 올바르게 document하여야 한다.

## Interface란?

이미 배웠듯이 객체는 자신이 노출하는 메소드를 통해 바깥 세상과 상호작용한다. 즉, 메소드는 바깥세상과 객체의 *인터페이스* 역할을 한다. 예를 들어, 텔레비전에 있는 버튼들은 사람과 플라스틱 껍데기 안에 있는 전기 선 사이의 인터페이스다. 전원버튼을 눌러서 텔레비전을 켰다 껐다 하는 것이다.

가장 흔한 형태로, 인터페이스는 빈 바디(body)를 가진 서로 관련된 메소드들의 그룹이다.

자전거의 행위가 만약 인터페이스로 명시되면 다음과 같을 수 있다.

```java
interface Bicycle {
    void changeCadence(int newValue);

    void changeGear(int newValue);

    void speedUp(int increment);

    void applyBrakes(int decrement);
}
```

이 인터페이스를 구현하기 위해 우리의 클래스 이름이 특정 브랜드의 자전거 이름 (예. ACMEBicycle)으로 바뀌고 `implements` 키워드를 클래스 선언에 쓴다.

```java
class ACMEBicycle implements Bicycle {
    
    int cadence = 0;
    int speed = 0;
    int gear = 1;

    // The compiler will now require that methods
    // changeCadence, changeGear, speedUp, and applyBrakes
    // all be implemented. Compilation will fail if those
    // methods are missing from this class.

    void changeCadence(int new Value) {
        cadence = newValue;
    }

    void changeGear(int newValue) {
         gear = newValue;
    }

    void speedUp(int increment) {
         speed = speed + increment;   
    }

    void applyBrakes(int decrement) {
         speed = speed - decrement;
    }

    void printStates() {
         System.out.println("cadence:" +
             cadence + " speed:" + 
             speed + " gear:" + gear);
    }
}
```

인터페이스를 구현함으로써 클래스는 제공하고자 약속한 행위들에 대하여 좀 더 formal(격식 있게)해진다. 인터페이스는 클래스와 바깥 세상 사이의 일종의 "계약서"가 되고, 이러한 계약은 컴파일러에 의해 빌드 시 강요된다. 만약 우리의 클래스가 인터페이스를 구현한다고 주장한다면, 그 인터페이스에 정의된 모든 메소드들은 클래스 코드에 모두 포함되어야 성공적으로 컴파일될 것이다.

**NOTE**: ACMEBicycle 클래스를 실제로 컴파일하기 위해서는 `public` 키워드가 구현된 인터페이스 메소드 선언 앞에 필요하다. 이에 대한 이유는 추후에 알아보겠다.

## Package란?

패키지는 서로 관련된 클래스 및 인터페이스들을 관리하는 namespace(네임스페이스)이다. 개념적으로, 컴퓨터에 있는 폴더와 비슷하다고 생각하면 된다. HTML 페이지들은 하나의 폴더에, 이미지들은 다른 폴더에 각각 저장하듯이 말이다. Java 언어로 작성된 소프트웨어는 몇 백, 혹은 천개 이상의 클래스들로 구성되기 때문에 패키지를 사용하여 관리하는 것이 상식적으로도 좋은 방법이 아닐 수 없다.

Java 플랫폼은 우리의 어플리케이션에서 사용할 수 있는 거대한 클래스 라이브러리 (패키지들의 집합)를 제공한다. 이 라이브러리를 "Application Programming Interface (API)"라고 부른다. 이 라이브러리의 패키지들은 범용(general-purpose) 프로그래밍과 관련된 태스크들을 나타낸다.
예를 들어,

- `String` 객체는 character 스트링에 대한 상태 및 행위를 포함하고 있다.
- `File` 객체는 프로그래머가 손쉽게 파일시스템에 파일을 생성, 삭제, 확인, 비교 및 수정을 할 수 있게 해준다.
- `Socket` 객체는 네트워크 소켓 생성 및 사용을 가능하게 해준다.
- 다양한 GUI 객체들은 버튼 및 체크박스 혹은 graphical user interface와 관련된 다른 것들을 제어한다.

이 외에도 실제로 몇 천개의 클래스들로부터 선택해서 사용할 수 있다.

이를 통해 우리는 프로그래머로써, 기반 인프라를 구축하지 않고 우리의 특정 어플리케이션 설계에 집중할 수 있다.

[Java Platform API Specification](https://docs.oracle.com/javase/8/docs/api/index.html)은 Java SE 플랫폼이 공급하는 모든 패키지, 인터페이스, 클래스, 필드, 메소드들의 전체 리스트를 가지고 있다. 이를 북마킹하자. 이 공식 문서는 프로그래머로써 자주 필요할 것이다.
