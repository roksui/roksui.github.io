---
title: "Java - Language Basics: Expressions, Statements, and Blocks"
categories: 
  - Java Basics
---

코딩을 하며 너무나 당연하게 우리가 써왔던 요소들이지만 정확히 어떤 이름을 갖고 어떻게 구분되는지 알아보자.

**Expression(식)**은 변수, 연산자 및 메소드 호출로 언어의 syntax에 맞게 구성되어 하나의 값으로 evaluate(연산)되는 것이다.

<u>Example of an Expression</u>

```java
int cadence = 0;
anArray[0] = 100;
System.out.println("Element 1 at index 0: " + anArray[0]);

int result = 2 + 3;
if (value1 == value2) // evaluates to a boolean type value
  System.out.println("value1 == value2");
```

**Statement(구문)**은 일반 언어의 문장과 비슷하다. 구문은 하나의 완전한 실행 단위이다. 다음과 같은 식들은 ;를 붙여 구문으로 만들 수 있다.

- 대입 식 (assignment operations)
- ++이나 -- 연산자
- 메소드 호출
- 객체 생성 식

이러한 구문들을 expression statement(식 구문)이라고 부른다.

<u>Example of an Expression Statement</u>

```java
aValue = 12;
aValue++;
System.out.println("Hello World!");
Bicycle bike = new Bicycle();
```

또, declaration statement(선언 구문)과 control flow statment(흐름 제어 구문)이 있다.

```java
// declaration statement
double aValue = 823.423;

// if statement (control flow statement)
if (isMoving)
  stop();
```

**Block(블록)**은 0개 이상의 구문을 밸런스된 중괄호 사이에 포함한 것을 말한다.

<u>Example of a Block</u>

```java
class BlockDemo {
  public static void main(String[] args) {
    boolean condition = true;
    
    if (condition) { // begin block 1
      System.out.println("Condition is true");
    } // end block 1
    else { // begin block 2
      System.out.println("Condition is false");
    } // end block 2
  }
}
```

## Control Flow Statements

Java 언어에서 소스 코드의 구문들은 위에서 아래로 차례대로 실행된다. 하지만 흐름 제어 구문을 통해 decision making (의사결정), looping (반복), branching (브랜칭)을 적용하여 "conditionally(조건적으로)" 동작할 수 있다.

위 세 가지 흐름 제어 구문은 코딩의 필수적인 요소들이라고 생각된다. 그 중에 branching statement(브랜칭 구문)에 대해 조금 살펴보자.

### The break Statement

break 구문은 두 가지 형태를 가진다: labeled와 unlabeled. 다음 예제를 보자.

```java
class BreakDemo {
  public static void main(String[] args) {
    int[] = arrayOfInts = {
      32, 87, 3, 589,
      12, 1076, 2000,
      8, 622, 127
    };

    int searchFor = 12;
    int i;
    boolean foundIt = false;

    for (i = 0; i < arrayOfInts.length; i++) {
      if (arrayOfInts[i] == searchFor) {
        foundIt = true;
        break;
      }
    }

    if (foundIt) {
      System.out.println("Found " + searchFor + " at index " + i);
    } else {
      System.out.println(searchFor + " not in the array");
    }
  }
}
```

이 프로그램은 배열에서 12를 찾는다. break 구문을 통해 값을 찾으면 for 구문을 나온다. 흐름 제어는 for 구문 밑으로 진행한다. 따라서 아웃풋은,

```text
Found 12 at index 4
```

가 된다.

이런식으로 unlabeled break 구문은 많이 써봤다. 이는 가장 안에 있는 innermost한 switch, for, while, 혹은 do-while 구문을 탈출한다. 그러나 labeled break 구문으로 더 밖에 있는 구문을 탈출할 수 있다. 예를 들어, 중첩 for문을 쓰는데, 안에서 조건을 주어 outer for문을 탈출하고 싶을 수도 있다.

```java
class BreakDemo {
  public static void main(String[] args) {
    int[][] arrayOfInts = { 
            { 32, 87, 3, 589 },
            { 12, 1076, 2000, 8 },
            { 622, 127, 77, 955 }
    };

    int searchFor = 12;
    int i;
    boolean foundIt = false;

    search:
      for (i = 0; i < arrayOfInts.length; i++) {
        for (j = 0; j < arrayOfInts[i].length; j++) {
          if (arrayOfInts[i][j] == searchFor) {
            foundIt = true;
            break search;
          }
        } 
      }

    if (foundIt) {
      System.out.println("Found " + searchFor + " at " + i + ", " + j);
    } else {
      System.out.println(searchFor + " not in the array");
    }
  }
}
```

아웃풋:

```text
Found 12 at 1, 0
```

`break <labeled statement name>`을 통해 명시된 outer 구문을 탈출 할 수 있는 것이다.

labeled break 구문처럼, labeled continue 구문을 사용하면 주어진 label을 가진 outer 반복문의 현재 iteration을 건너뛸 수 있다.
