---
title: "Java - Language Basics: Operators"
categories: 
  - Java Basics
---

자, 이제 변수를 선언하고 초기화하는 것에 대해서는 알겠으니, 이것들로 무엇을 할지를 알아보자.

*Operator(연산자)*는 1개, 2개, 혹은 3개의 *operand(피연산자)*를 대상으로 특정 연산을 수행하여 결과를 리턴하는 특수 심볼이다.

연산자들 사이에서 연산 우선순위가 존재한다. 다음 테이블은 위에서부터 아래로 높은 우선순위를 가진 연산자 순서로 나열되어있다.

같은 우선순위를 가질 경우, 대입 연산자는 right to left로 연산되고, 나머지는 left to right으로 연산된다.

| Operators                | Precedence                              |
| ------------------------ | --------------------------------------- |
| postfix                  | expr++ expr--                           |
| unary                    | ++expr --expr +expr -expr ~ !           |
| multiplicative           | * / %                                   |
| additive                 | + -                                     | 
| shift                    | << >> >>>                               |
| relational               | < > <= >= instanceof                    |
| equality                 | == !=                                   |
| bitwise AND              | &                                       |
| bitwise exclusive OR     | ^                                       |
| bitwise inclusive OR     | \|                                      |
| logical AND              | &&                                      |
| logical OR               | \|\|                                    |
| ternary                  | ? :                                     |
| assignment               | = += -= *= /= %= &= ^= \|= <<= >>= >>>= |

연산자 우선순위에 대해 공부하면서 한 가지 의문이 들었다. 본 테이블을 참조한 오라클 공식 문서에서는 postfix 연산자가 prefix 연산자보다 우선순위가 높다고 말한다.

그럼, postfix 연산자와 prefix 연산자가 하나의 식에 있을 때는 무엇이 먼저 연산(evaluate)되어야 하는가?

라는 의문이 생겼는데, 문서에서는 분명히 "우선순위가 높은 연산자가 먼저 연산된다"고 말한다. 그럼 다음과 같은 예시를 생각해보자.

```java
int n = 1;
System.out.println(--n + n++);
```

물론, 서비스를 개발할 때 이런식으로 코딩할 일은 없어야 겠지만, 아카데믹적인 이유로 한 번은 생각해볼만 한 것 같다.

내가 생각한 부분은 이렇다:
- postfix 증감 연산자가 우선순위가 높으므로, 먼저 식은 `--n + 1`로 연산되고, n은 2로 증가한다.

- 그 후, prefix 증감 연산자가 우선순위가 높으므로, 식은 `1 + 1`이 되므로 2가 출력될 것이다.

- 그러나 출력 결과는 0으로 나온다.

어떠한 부분에서 내가 잘못 생각하고 있을까? 생각해보았다.

Java Language Specification(JLS)에 의하면 일단 확실한 부분은, 식이 주어졌을 때, 각 피연산자(operand)들은 left to right으로 evaluate된다. 그래서 위와 같은 경우도 `--n`이 먼저 연산되고, 그후에 `n++`이 되고 더해지는 순서인 것이다 - 즉, 위 식은 `0 + 0`으로 연산되는 것이다.

그럼 우선순위라는건 왜 존재하는걸까? 위에서 말하는 연산자 우선순위는 left to right으로 계산을 하기 전에, 식을 파싱하여(괄호를 매기는 것) 계산될 것을 준비하는 과정으로 볼 수 있다.

예를 들어, `i = 2`이면, `i+i++`는 연산자 우선순위에 의해 `(i+i)++`가 아닌 `i+(i++)`로 파싱되어 `2 + 2 = 4`가 될 것이다.

또, `i+++i`는 `(i++)+i`가 되어 `2 + 3 = 5`가 될 것이다. postfix가 prefix보다 우선순위가 높기 때문에 `i+(++i)`로 파싱되지 않고 말이다.

`i+++++i`의 경우에, 우선순위를 고려하면 `((i++)++)+i`가 된다. 하지만 이는 컴파일 에러를 발생시킨다. 왜냐하면 postfix 연산자는 변수에 적용되는데 값에 적용하고 있기 때문이다. 만약 postfix와 prefix가 동일한 우선순위를 가졌다면, `(i++)+(++i)`로 파싱되어 컴파일 에러가 안나고 `2 + 4 = 6`가 연산될 것이다.

따라서 postfix와 prefix의 우선순위가 다른 것에 대한 증명이 된 것이다. 그리고, 기억해야 할 것은, **연산자 우선순위는 파싱을 어떻게 할지를 결정해주는 룰이고, 파싱이 된 후에 실제로 evaluation(연산)은 왼쪽에서 오른쪽 방향으로 수행된다**는 점이다.

정확한 것은 Java 설계자들한테 물어봐야겠지만, JLS에서는 연산자 우선순위를 직접적으로 명시하진 않고, "Precedence among operators is managed by a hierarchy of grammar productions"라고 말한다.

### Conditional 연산자
&&와 || 연산자는 두 개의 boolean 피연산자에게 Conditional-AND와 Conditional-OR 연산을 수행한다. 이 연산자들은 "short-circuiting" 특성을 갖는다 - 필요시에만 두 번째 피연산를 연산한다.

### instanceof 연산자
Parent라는 클래스, MyInterface라는 인터페이스, 그리고 Parent를 상속받고 MyInterface를 구현하는 Child 클래스가 있다고 해보자.<br>

```java
class InstanceofDemo {
    public static void main(String[] args) {
        Parent obj1 = new Child();

        System.out.println(obj1 instanceof Parent); // true
        System.out.println(obj1 instanceof Child); // true
        System.out.println(obj1 instanceof MyInterface); // true

    }
}
```

코드 스니펫을 보면 알 수 있듯이, Child 인스턴스인 obj1은 Parent와 MyInterface의 인스턴스이기도 하다.

**NOTE**: instanceof 연산자를 사용할 때, null은 그 어떠한 것의 인스턴스도 아니라는 점의 유의하자.

### Bitwise와 Bit Shift 연산자
Java 언어에서는 integral 타입(integer 값을 갖는 타입): byte, short, int, long, char에 대해 bitwise 및 bit shift 연산을 할 수 있다. 덜 사용되긴 하지만 알면 매우 유용하다.

- Unary bitwise complement 연산자 '~'는 bit 패턴을 반대로 바꾼다. (0 <-> 1)
  - 예를 들어 8 bits를 가진 byte 값 00000000에 이 연산자를 적용하면 11111111이 된다.

- Signed left shift 연산자 "<<"는 한 개의 bit 패턴을 왼쪽으로 밀고, signed right shift 연산자 ">>"는 오른쪽으로 민다. 
  - 예를 들어, 0010 << 1을 하면 0100이 된다. 빈 자리는 0으로 채워진다.
  - 8의 2진법은 1000이다. 8 >> 2를 하면 0010이 된다. 이는 10진법의 2이다. Right shift 연산을 할 때, 빈 자리는 sign bit로 채워진다. 이 경우 8은 positive sign을 가지기 때문에 positive sign bit를 나타내는 0으로 채워진다.
  - -8의 경우 negative sign을 가지기 떄문에, 빈 자리는 negative sign bit를 나타내는 1로 채워진다. -8 >> 2을 하면 1100이 되어 10진법으로 -2가 된다.

- Unsigned right shift 연산자 ">>>"는 가장 왼쪽 자리에 sign bit 대신에 0을 밀어 넣는다.

```java
class Main {
  public static void main(String[] args) {
    
    int number1 = 8;
    int number2 = -8;
    
    System.out.println(Integer.toBinaryString(number1)); // 1000
    System.out.println(Integer.toBinaryString(number2)); // 11111111111111111111111111111000
    
    // 2 bit signed right shift
    System.out.println(Integer.toBinaryString(number1 >>> 2));    // 0010
    System.out.println(Integer.toBinaryString(number2 >>> 2));    // 00111111111111111111111111111110
  }
}
```

- Bitwise & 연산자는 bitwise AND 연산을 수행한다.

- Bitwise ^ 연산자는 bitwise exclusive OR 연산을 수행한다.

- Bitwise \| 연산자는 bitwise inclusive OR 연산을 수행한다.

다음 예제는 bitwise AND 연산자를 사용하여 2를 standard output에 출력한다.

```java
class BitDemo {
    public static void main(String[] args) {
        int bitmask = 0x000F;
        int val = 0x2222;

        System.out.println(val & bitmask); // prints 2
    }
}
```

아래와 같은 bitwise AND 연산이 수행된다.

```
   00000000001111
&  10001000100010
__________________
   00000000000010  == 2
```