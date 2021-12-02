---
title: "Java - Language Basics"
use_math: true
---

본 글에서는 Java 프로그래밍 언어 자체의 요소들을 살펴볼 것이다. 하나도 빠트리지 않고 모든 디테일을 설명하기보다는, 그동안 헷갈렸지만 그냥 썼던 부분들, 그리고 그렇게 쓰이는 이유를 중점적으로 살펴보고자 한다. 

## Variables
Java 언어에서 "field"(필드)와 "variable"(변수)는 둘 다 사용되는 용어인데, 두 용어가 같은 것을 말하기 때문에 때때로 헷갈릴 수 있다.

Java에서는 다음과 같은 종류의 변수들을 정의한다:
- **Instance Variables (Non-Static Fields)**: 엄밀히 말해, 객체들은 자신의 상태를 "non-static field"에 저장한다. 이는 static 키워드가 없이 선언된 필드를 말한다. Non-static field는 *instance variable*이라고도 불린다. 왜냐하면 그것들의 값이 클래스의 각 인스턴스, 즉 객체가 가지는 고유의 값이기 떄문이다. 한 자전거의 currentSpeed는 다른 자전거의 currentSpeed와 다르다.

- **Class Variables (Static Fields)**: *Class variable*은 static 제어자와 함께 선언된 필드를 말한다. 이는 컴파일러에게 클래스가 몇 번 인스턴스화되었는지 상관 없이, 이 변수는 정확히 한 개의 카피만 존재한다는 것을 말해준다. 특정 종류의 자전거의 기어의 개수를 정의하는 필드는 static으로 선언할 수 있을 것이다. 왜냐하면 상식적으로 그 자전거에 대한 모든 인스턴스들에게 똑같은 개수의 기어를 적용할 것이기 때문이다. `static int numGears = 6`와 같이 static field를 선언할 수 있다. 또한, final 키워드를 추가하여 기어의 개수는 변하지 않는다는 것을 표시할 수 있다.

- **Local Variables**: 객체가 자신의 상태를 필드에 저장하듯이, 메소드는 자신의 임시 상태를 *local variable (지역 변수)*에 저장한다. Local variable을 선언하는 syntax는 필드를 선언하는 것과 비슷하다 - `int count = 0`. 변수를 local하다고 지정하는 특별한 키워드는 없고, 단지 그 변수가 어디에 선언되었는지로부터 결정된다 - 메소드의 { } 괄호 사이. 따라서 local variable은 자신이 선언된 메소드에게만 보이고 클래스의 나머지 부분에서는 접근이 불가능하다.

- **Parameters**: 메인 메소드의 시그니쳐는 `public static void main(String[] args)`이다. 여기서 args 변수가 이 메소드의 parameter(매개변수)이다. 한 가지 기억할 것은, parameter는 항상 field가 아닌 variable로 분류된다는 점이다.

따라서, 필드와 변수를 다룰 때 따르는 일반적인 가이드라인은, local variable과 parameter를 제외한 일반적인 필드(fields in general)를 얘기할 때는 단순히 필드라고 칭한다. 만약 위에 나열한 모든 것들을 포함하여 얘기할 때는 변수라고 칭한다. 또, *member*라는 용어도 쓰이는데, 이는 한 타입의 필드, 메소드 및 중첩 타입들을 모두 일컫는 용어이다.

## Naming
모든 프로그래밍 언어가 사용할 수 있는 이름에 대한 규칙 및 컨벤션이 있고 Java 또한 예외가 아니다. 다음과 같이 요약할 수 있다.
- 변수 이름은 case-sensitive하다. 변수의 이름은 길이 제한이 없는 Unicode 문자 및 숫자의 시퀀스이다. (문자 or '$' or '_'로 시작해야 함) 그러나 컨벤션을 따르면 달러 표시는 절대 안쓰이고 변수 이름은 항상 문자로 시작하는 것이다. 몇몇 상황에서는 자동으로 생성된 이름이 달러 표시가 들어갈 때도 있지만 이는 피하는 것이 좋다. 언더스코어로 시작하는 것도 추천하지 않는 프렉티스이다. 공백(whitespace)은 허용되지 않는다.

- 뒤에 붙는 글자들은 문자, 숫자, 달러 사인 및 언더스코어가 될 수 있다. 컨벤션에 의하면 변수의 이름을 선택할 때, 쉽게 의미를 알 수 없는 약어를 사용하기 보다는 전체 단어를 사용하자. 그래야 코드도 읽고 이해하기 더 쉬워진다. 또, self-documenting 코드가 될 가능성이 높다. cadence, speed, gear 이름을 가진 필드들은 s, c, g 보다 훨씬 더 직관적이다. 당연하겠지만 Java에서의 키워드나 이미 Java 프로그래밍 언어에서 사용하는 단어(reserved word)를 사용할 수 없다.

- 만약 선택한 이름이 한 개의 단어라면, 그냥 소문자로 적으면되고, 두 개 이상일 경우는 각 단어의 첫 문자를 대문자로 적는다 - eg. gearRatio, currentGear. 만약 변수가 constant 값을 저장한다면, eg. `static final int NUM_GEARS = 6`와 같이 모든 문자를 대문자로 하고 단어들을 언더스코어로 분리한다. 컨벤션에 의해 언더스코어는 다른 곳에서는 절대 안쓰인다.

## Primitive Data Types
Java 언어는 statically-typed(정적 타입)이다. 즉, 모든 변수는 사용되기 전에 먼저 선언되어야 한다는 의미이다. 변수의 타입, 이름을 다음과 같이 지정해줘야 한다.

```
int gear = 1;
```

이렇게 함으로써 우리의 프로그램에게 gear라는 이름을 가진 필드가 존재하고, 숫자 데이터를 담고있고, 초기 값이 1이라는 것을 알려준다.

변수의 데이터 타입은 그것이 가질 수 있는 값과 그것을 대상으로 할 수 있는 연산을 결정해준다.

int와 함께 Java 프로그래밍 언어는 7개의 다른 *primitive data type(원시적 데이터 타입)*을 지원한다:
- **byte**: byte 데이터 타입은 8-bit signed two's complement integer이다. 값 범위는 -128 ~ 127이다.

- **short**: short 데이터 타입은 16-bit signed two's complement integer이다. 값 범위는 -32,768 ~ 32,767이다.

- **int**: int 데이터 타입은 32-bit signed two's complement integer이다. 값 범위는 $-2^{31} ~ 2^{31} - 1$이다.

- **long**: long 데이터 타입은 64-bit two's complement integer이다. signed long은 

- **float**:

- **double**:

- **boolean**:

- **char**:
