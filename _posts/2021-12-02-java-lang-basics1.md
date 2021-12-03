---
title: "Java - Language Basics: Primitive Data Types"
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
- **byte**: byte 데이터 타입은 8-bit signed two's complement integer이다. 값 범위는 $-128 \text{ ~ } 127$이다. byte 데이터 타입은 큰 배열을 사용할 때 메모리를 낭비하지 않기 위해 사용될 수 있다 - 실제로 메모리는 아끼는 것이 중요한 상황에서. 또한, int 대신 사용하여 그 제한된 범위로 인해 우리의 코드가 좀 더 명확해지면 사용할 수도 있다. 변수의 값 범위가 제한된다는 사실은 하나의 documentation 역할을 할 수도 있다. 

- **short**: short 데이터 타입은 16-bit signed two's complement integer이다. 값 범위는 $-32,768 \text{ ~ } 32,767$이다. byte와 비슷하게 똑같은 가이드라인이 적용된다 - 큰 배열에서 메모리 낭비를 줄이기 위해 사용된다.

- **int**: int 데이터 타입은 32-bit signed two's complement integer이다. 값 범위는 $-2^{31} \text{ ~ } 2^{31} - 1$이다. Java SE8 이후에서는 int 데이터 타입을 unsinged 32-bit integer (값 범위: $0 \text{ ~ } 2^{64} - 1$)를 나타내기 위해 사용할 수 있다. Integer 클래스를 사용하여 int 데이터 타입을 unsigned integer로 사용할 수 있다.

- **long**: long 데이터 타입은 64-bit two's complement integer이다. signed long의 값 범위는 $-2^{63} \text{ ~ } 2^{63} - 1$이다. 

- **float**: float 데이터 타입은 single-precision 32-bit IEEE 754 floating point이다. 값 범위는 Java Language Specification의 [Floating-Point Types, Formats, and Values](https://docs.oracle.com/javase/specs/jls/se7/html/jls-4.html#jls-4.2.3)에 명시되어 있다.

- **double**: double 데이터 타입은 double-precision 64-bit IEEE 754 floating point이다. 값 범위는 float와 같이 위 링크에 명시되어있다. 소수점을 사용할 때 이 데이터 타입을 일반적으로 선택한다.

- **boolean**: boolean 데이터 타입은 두 개의 값이 가능하다: true 혹은 false. 이 데이터 타입은 한 개의 bit의 정보를 나타낸다.

- **char**: char 데이터 타입은 single 16-bit Unicode 캐릭터이다. 값 범위는 '\u0000'(or 0) ~ '\uffff' (or 65,535)이다.

위의 8가지 primitive 데이터 타입에 더하여 Java 언어는 character string을 위한 특별한 지원을 해준다 - java.lang.String 클래스. Character string을 ""로 감싸면 자동으로 새로운 String 객체를 생성한다. String 객체는 *immutable(불변)*하다 - 한 번 생성되면 그 값이 바뀔 수 없다. String 클래스는 primitive 데이터 타입이 아니다. 하지만 언어차원에서 이런식으로 character string을 String 클래스로 만들 수 있는 특별 지원을 하기 때문에 primitive 데이터 타입으로 오해하기 쉽다.

### Default Values
필드를 선언할 시 항상 값을 부여해야하는 것은 아니다. 선언만 하고 초기화하지 않으면 컴파일러에 의해 적절한 디폴트 값이 부여된다. 일반적으로 말해 데이터 타입에 따라 0 혹은 null이 들어간다. 그러나 이러한 디폴트 값에 의존하는 것은 나쁜 프로그래밍 스타일이다.

그러나, local variable에게는 컴파일러가 디폴트 값으로 자동 초기화해주지 않는다. Local variable을 선언할 때 초기화할 수 없다면, 그것을 사용하기 전에 꼭 값을 부여해줘야 한다. 초기화되지 않은 local variable을 사용하면 컴파일 타임 에러가 발생할 것이다.

### Literals
Primitive 데이터 타입을 초기화할 때는 new 키워드를 사용하지 않는 것을 알 것이다. **Primitive 타입은 언어의 빌트인된 특수 데이터 타입이다 - 클래스로부터 생성되는 객체가 아니다**. *Literal*은 고정 값에 대한 소스 코드 표현법이다. 리터럴은 계산 필요 없이 직접적으로 코드에 나타낼 수 있다. 다음과 같이 리터럴을 primitive 데이터 타입 변수에 부여할 수 있다.

```java
boolean result = true;
char capitalC = 'C';
byte b = 100;
short s = 10000;
int i = 100000;
```

### Integer Literals
Integer literal은 L이나 l로 끝나면 long 타입이고, 그렇지 않으면 int 타입이다. 소문자 l 보다는, 대문자인 L을 쓰는 것을 추천한다 - 1과 l이 구분이 어려우니.

byte, short, int, long 타입의 값들은 int 리터럴로 생성될 수 있다. int 값의 범위를 넘는 long 타입의 값은 long 리터럴로 만들 수 있다. Integer 리터럴은 다음의 넘버 시스템을 통해 나타낼 수 있다:
- Decimal(10진법): Base 10 (0 ~ 9), 우리가 매일 쓰는 숫자
- Hexadecimal(16진법): Base 16 (0 ~ 9, A ~ F)
- Binary(2진법): Base 2 (0, 1), Java SE 7 이후부터 가능

범용 프로그래밍에서는 거의 decimal system만 볼 것이다. 그러나 만약 다른 넘버 시스템을 사용한다면 다음과 같은 syntax를 사용한다. Prefix 0x는 hexadecimal, 0b는 binary를 나타낸다.

```java
// The number 26, in decimal
int decVal = 26;
// The number 26, in hexadecimal
int hexVal = 0x1a;
// The number 26, in binary
int binVal = 0b11010;
```

### Floating-Point Literals
Floating-point literal은 F나 f로 끝날 경우 float 타입이고, 만약 D나 d로 끝나면 double 타입이다.

이러한 floating-point 타입은 E나 e (scientific notation), F나 f(32-bit float literal), 혹은 D나 d(64-bit double literal)를 사용하여 나타낼 수 있다. D나 d가 디폴트이고, 컨벤션에의해 따로 표시하지 않는다.

```java
double d1 = 123.4;
// same value as d1, but in scientific notation
double d2 = 1.234e2;
float f1 = 123.4f;
```

### Character and String Literals
char와 String 타입의 리터럴은 Unicode (UTF-16) 캐릭터를 포함할 수도 있다. 에디터나 파일 시스템이 이를 허용한다면, 그 캐릭터를 코드에 바로 사용할 수 있다. 그렇지 않다면 '\u0108' (capital C with circumflex), "S\u00ED Se\u00F1or" (스페인어 Si Señor)와 같이 "Unicode escape"를 사용할 수 있다. char 리터럴에 대해서는 'single quote'를 쓰고, String 리터럴에 대해서는 "double quotes"를 쓴다. Unicode escape 시퀀스는 char이나 String 리터럴 외에도 필드 이름과 같이 프로그램의 다른 부분에서도 사용될 수 있다.

Java 언어에서도 char와 String 리터럴에 대한 몇 가지 특수 escape 시퀀스를 제공한다: \b (backspace), \n (line feed), \f (form feed), \r (carriage return), \\" (double quote), \\' (single quote), 그리고 \\\\ (backslash)

또, reference 타입에 대한 값으로 특수하게 사용할 수 있는 null 리터럴이 있다. null은 primitive 타입 변수를 제외한 그 어떠한 변수에도 부여 가능하다. null 값의 존재를 테스트하는 것 외에는 null을 크게 사용할 곳은 없다. 따라서 null은 주로 어떠한 객체가 사용될 수 없음을 나타내는 용도로 많이 쓰인다.

마지막으로 *class literal*이라는 특수한 리터럴 종류가 있다. 이는 type 이름에 ".class"를 붙여서 만들 수 있다. 예를 들어 String.class. 이 때, String.class 리터럴은 String 타입을 나타내는 Class 타입의 인스턴스이다.

### Using Underscore Characters in Numeric Literals
Java SE 7 이후 부터는 숫자 리터럴에서 언더스코어 캐릭터가 숫자들 사이에 몇개든지 존재할 수 있다. 이 기능을 통해 숫자 리터럴에서 숫자를 그룹으로 나눌 수도 있어 코드의 가독성이 좋아진다.

예를 들어 만약 우리의 코드가 긴 숫자를 포함한다면 언더스코어를 사용하여 숫자를 3개의 그룹으로 나눌 수 있을 것이다 - 콤마, 스페이스 등의 분리자를 사용하듯이.

다음의 여러가지 같은 예시를 보자.

```java
long creditCardNumber = 1234_5678_9043_2313L;
long socialSecurityNumber = 999_99_9928L;
float pi = 3.14_15F;
long hexBytes = 0xFF_EC_DE_5E;
long hexWords = 0xCAFE_BABE;
long maxLong = 0x7fff_ffff_ffff_ffffL;
byte nybbles = 0b0010_0101;
long bytes = 0b11010010_01101001_10010100_10010010;
```

**NOTE**: 언더스코어는 다음과 같은 경우로는 사용될 수 없다.

```java
// Invalid: cannot put underscores adjacent to a decimal point
float pi1 = 3_.1415F;
float pi2 = 3._1415F;
// Invalid: cannot put underscores prior to an L suffix
long socialSecurityNumber1 = 999_99_9999_L;
// Invalid: cannot put underscores at the end of a literal
int x2 = 52_;
// Invalid: cannot put underscores in the 0x radix prefix
int x4 = 0_x52;
// Invalid: cannot put underscores at the beginning of a number
int x 5 = 0x_52;
// Invalid: cannot put underscores at the end of a number
int x7 = 0x52_;
```

다음과 같이 언더스코어를 여러개 붙여도 decimal literal이며 허용된다.
```java
int x3 = 5_____2;
System.out.println(x3 / 2); // prints 26 because x3 is 52
```