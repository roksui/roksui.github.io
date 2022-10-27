---
title: "Java - Language Basics: Arrays"
categories: 
  - Java Basics
---

## Arrays

*Array(배열)*는 한 가지 타입의 고정된 개수의 값들을 저장하는 컨테이너 객체이다. 배열의 길이는 그것이 생성될 때 정해진다. 생성된 후로는 크기가 바뀔 수 없다.

배열을 선언할 때는 다음과 같이 할 것이다.

```java
// declares an array of integers
int[] an Array;
```

다른 타입의 변수들을 선언할 때 처럼 배열 선언에는 두 가지 요소가 있다: 배열 타입, 그리고 배열 이름. 배열의 타입은 type[]으로 하는데, 여기서 type은 저장되는 element(원소)의 데이터 타입이 된다. 또, **다른 타입의 변수들과 같이, 선언을 하는 것은 실제로 배열을 생성하지는 않는다**. 이는 단순히 컴파일러에게 "이 변수는 이러한 타입의 배열을 저장할 것이다"라는 것을 말해주는 것 뿐이다.

배열을 생성하기 위한 한 가지 방법으로 new 연산자를 쓸 수 있다. 위 코드 스니펫에 추가로 적으면 다음과 같다.

```java
// create an array of integers
anArray = new int[10];
```

만약 이 구문을 안쓰면 선언한 배열이 초기화되지 않았을 수도 있다고 컴파일러가 불만을 표시한다.

```bash
// compile-time error
ArrayDemo.java:4: Variable anArray may not have been initialized.
```

C와 같이 배열의 인덱스로 접근해서 값을 대입해주면 된다.

또는, shortcut syntax를 사용하여 선언과 동시에 초기화할 수도 있다.

```java
int[] anArray = {
    100, 200, 300,
    400, 500, 600, 700
};
```

이 때, 배열의 길이는 초기화된 값의 개수로 결정된다.

C와는 다르게 Java 언어에서 multidimensional 배열의 요소들은 배열이다. 따라서 행들이 서로 길이가 다를 수 있다.

```java
class MultiDimArrayDemo {
    public static void main(String[] args) {
        String[][] names = {
            {"Mr. ", "Mrs. ", "Ms. "},
            {"Smith", "Jones"}
        };
        // Mr. Smith
        System.out.println(names[0][0] + names[1][0]);
        // Ms. Jones
        System.out.println(names[0][2] + names[1][1]);
    }
}
```

아웃풋:

```
Mr. Smith
Ms. Jones
```

### Copying Arrays

System 클래스의 arraycopy 메소드를 사용하여 효율적으로 한 배열의 데이터를 다른 배열로 복사할 수 있다.

```java
public static void arraycopy(Object src, int srcPost, Object dest, int destPos, int length)
```

두 개의 Object 인자는 어느 '배열'에서 어느 '배열로' 가는지를 나타내고, 세 개의 int 인자들은 src 배열의 시작점, dest 배열의 시작점, 그리고 복사할 요소들의 개수를 의미한다. 다음의 예시로 확인하자.

```java
class ArrayCopyDemo {
    public static void main(String[] args) {
        String[] copyFrom = {
            "Affogato", "Americano", "Cappuccino", "Corretto", "Cortado",
            "Doppio", "Espresso", "Frappucino", "Freddo", "Lungo", "Macchiato",
            "Marocchino", "Ristretto"
        };

        String[] copyTo = new String[7];
        System.arraycopy(copyFrom, 2, copyTo, 0, 7);
        for (String coffee : copyTo) {
            System.out.print(coffee + " ");
        }
    }
}
```

아웃풋:

```bash
Cappuccino Corretto Cortado Doppio Espresso Frappucino Freddo
```

### Array Manipulations

배열은 프로그래밍에서 사용되는 파워풀하고 유용한 개념이다. Java SE는 배열과 관련된 가장 흔한 조작에 대한 메소드들을 제공한다. 예를 들어, 방금과 같은 System 클래스의 arraycopy 메소드를 사용하여 src 배열의 원소를 일일이 iterate하여 하나씩 dest 배열에 넣어주는 일을 안해도 된다. 이러한 일은 behind the scene으로 진행되어 개발자는 메소드를 한 줄로 호출하기만 하면된다.

Java SE의 java.util.Arrays 클래스에서 복사, 정렬, 탐색 등과 같은 배열 조작관련 메소드를 제공한다. 예를 들어, 이전 예시는 java.util.Arrays 클래스의 copyOfRange 메소드를 사용하여 똑같이 재현할 수 있다. 차이점은 copyOfRange 메소드는 메소드를 호출하기 전에 dest 배열을 생성할 필요가 없다는 점이다 - 메소드가 dest 배열을 리턴한다.

```java
class ArrayCopyOfDemo {
    public static void main(String[] args) {
        String[] copyFrom = {
            "Affogato", "Americano", "Cappuccino", "Corretto", "Cortado",
            "Doppio", "Espresso", "Frappucino", "Freddo", "Lungo", "Macchiato",
            "Marocchino", "Ristretto"
        };

        String[] copyTo = java.util.Arrays.copyOfRange(copyFrom, 2, 9);
        for (String coffee : copyTo) {
            System.out.print(coffee + " ");
        }
    }
}
```

아웃풋은 이전 예시와 똑같지만 코드 줄의 개수가 줄었다.

**NOTE**: copyOfRange 메소드의 두번째 파라미터가 나타내는 인덱스는 포함되고, 세번째 파라미터가 나타내는 인덱스는 포함되지 않는다 - 인덱스 9의 원소 "Lungo"는 복사되지 않았다.

java.util.Arrays가 제공하는 또 다른 유용한 메소드들은 다음과 같다:

- binarySearch 메소드: 배열에서 특정 값을 탐색하여 인덱스를 얻는다.
- equals 메소드: 두 개의 배열이 동일한지 비교한다.
- fill 메소드: 배열의 각 인덱스에 특정한 값을 넣는다.
- sort 메소드: 배열을 순차적으로(sequentially) ascending 순서로 정렬한다.
- parallelSort 메소드: 배열을 동시에(concurrently) ascending 순서로 정렬한다. (Java SE 8에서 소개됨)
- stream 메소드: 배열을 소스로 사용하는 stream을 생성한다. 예를 들어 다음 구문은 copyTo 배열을 이전 예시와 같이 출력한다.

```java
java.util.Arrays.stream(copyTo).map(coffee -> coffee + " ").forEach(System.out::print);
```

- toString 메소드: 배열을 문자열로 변환한다. 배열의 각 원소를 문자열로 변환하고 콤마로 분리한 후 대괄호로 감싼다.

```java
System.out.println(java.util.Arrays.toString(copyTo));
```

아웃풋:

```bash
[Cappuccino, Corretto, Cortado, Doppio, Espresso, Frappucino, Freddo] 
```

이렇게 하여 Java 언어의 **Variables**와 관련된 내용은 모두 살펴봤다.

### Summary

- 필드(field)와 변수(variable)은 둘 다 사용되는 용어이다.
- Instance variable (non-static field)은 클래스의 각 인스턴스 마다 갖는 고유한 변수이다.
- Class variable (static field)은 static 제어자를 통해 선언하고, 클래스가 몇 번 인스턴스화되었는지와 상관없이 단 하나의 카피만 존재한다.
- Local variable은 메소드 안에서 임시 상태를 저장한다.
- Local variable과 parameter 둘다 항상 "field"가 아닌 "variable"로 분류된다.
- 필드와 변수의 이름에 대한 룰과 컨벤션을 숙지하자.

- 8개의 primitive 데이터 타입은: byte, short, int, long, float, double, boolean, char이다.
- java.lang.String 클래스는 character strings를 나타낸다.
- 위 타입들의 필드에 대해서 컴파일러는 디폴트 값을 할당한다.
- Local variable에 대해서 컴파일러는 디폴트 값을 할당하지 않는다.
- Literal은 고정된 값의 소스코드 표현법이다.
- 배열은 고정된 개수의 한 개의 타입의 값들을 저장하는 컨테이너 객체이다.
- 배열의 길이는 생성 때 길이가 정해지고, 그 후 변경이 불가능하다.