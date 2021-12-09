---
title: "Java Language Basics: Enum Types"
---

*Enum type*은 특수한 데이터 타입으로써, 해당 타입의 변수는 미리 정의된 constant들의 집합의 값들 중 하나가 될 수 있다.

나침반 방향 (NORTH, SOUTH, EAST, WEST 값)이나 요일 등이 Enum 타입으로 많이 정의된다.

Enum 타입은 Java 언어에서는 enum 키워드를 사용하여 정의한다. 요일 enum 타입은 다음과 같이 적을 수 있다:

```java
public enum Day {
    SUNDAY, MONDAY, TUESDAY, WEDNESDAY,
    THURSDAY, FRIDAY, SATURDAY
}
```

고정된 constant를 나타낼 필요가 있을 때는 enum 타입을 사용하는 것이 좋다. 고정된 값이라함은 태양계의 행성들, 음식 메뉴, 커맨드 라인 flag 등 처럼 컴파일 타임때 모든 가능한 값들을 알고 있는 데이터셋을 의미한다.

위에 정의한 enum 타입인 Day를 사용해보자.

```java
public class EnumTest {
    Day day;

    public EnumTest(Day day) {
        this.day = day;
    }

    public void tellItLikeItis() {
        switch (day) {
            case MONDAY:
                System.out.println("Mondays are bad.");
                break;
            
            case FRIDAY:
                System.out.println("Fridays are better.");
                break;
            
            case SATURDAY: case SUNDAY:
                System.out.println("Weekends are the best.");
                break;
            
            default:
                System.out.println("Midweek days are so-so.");
                break;
        }
    }

    public static void main(String[] args) {
        EnumTest firstDay = new EnumTest(Day.MONDAY);
        firstDay.tellItLikeItis();
        EnumTest thirdDay = new EnumTest(Day.WEDNESDAY);
        thirdDay.tellItLikeItis();
        EnumTest fifthDay = new EnumTest(Day.FRIDAY);
        fifthDay.tellItLikeItis();
        EnumTest sixthDay = new EnumTest(Day.SATURDAY);
        sixthDay.tellItLikeItis();
        EnumTest seventhDay = new EnumTest(Day.SUNDAY);
        seventhDay.tellItLikeItis();
    }
}
```

Output:

```
Mondays are bad.
Midweek days are so-so.
Fridays are better.
Weekends are best.
Weekends are best.
```

Java 언어의 enum 타입은 다른 언어들의 enum 보다 훨씬 더 파워풀하다. enum 선언은 enum type으로 부르는 클래스를 정의한다. enum 클래스 바디는 메소드 및 필드를 가질 수 있다. 컴파일러는 enum을 생성할 때 몇 가지 특수 메소드를 자동으로 추가한다. 예를 들어, static한 `values` 메소드가 추가되는데, 이는 선언된 순서대로 enum의 모든 값들을 포함하는 배열을 리턴한다. 이 메소드와 for-each construct를 같이 사용하여 enum 타입의 값들을 iterate할 수 있다.

```java
for (Planet p : Planet.values()) {
    System.out.printf("Your weight on %s is %f\n", p, p.surfaceWeight(mass));
}
```

**NOTE**: 모든 enum들은 implicit하게 java.lang.Enum을 상속 받는다. 한 클래스는 한 개의 부모로부터만 상속 받을 수 있으므로, enum은 다른 것들을 상속받을 수 없다.

다음 예제는 enum 타입으로 Planet을 만들어 태양계에 있는 행성들을 나타낸다. 각 enum constant는 질량과 반경 파라미터 값으로 선언된다. Constant가 생성될 시 이 질량 및 반경 값은 생성자로 전달된다. Java는 constant들이 필드나 메소드 전에 먼저 정의되는 것을 요구한다. 그리고 필드와 메소드가 있다면 enum constant 리스트는 세미콜론으로 끝마침을 표시해줘야 한다.

**NOTE**: enum 생성자는 우리가 직접 호출할 수 없다. Java는 알아서 제일 처음 정의된 constant들을 자동으로 생성하기 때문에 생성자는 package-private이나 private으로 선언되어야 한다.

또 아래 코드를 보면 Planet은 각 행성 표면에서의 중력과 무게를 구할 수 있는 메소드를 가지고 있다.

```java
public enum Planet {
    MERCURY (3.303e+23, 2.4397e6),
    VENUS   (4.869e+24, 6.0518e6),
    EARTH   (5.976e+24, 6.37814e6),
    MARS    (6.421e+23, 3.3972e6),
    JUPITER (1.9e+27,   7.1492e7),
    SATURN  (5.688e+26, 6.0268e7),
    URANUS  (8.686e+25, 2.5559e7),
    NEPTUNE (1.024e+26, 2.4746e7);

    private final double mass;
    private final double radius;
    
    Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
    }

    private double mass() { return mass; }
    private double radius() { return radius; }

    public static final double G = 6.67300E-11;

    double surfaceGravity() {
        return G * mass / (radius * radius);
    }

    double surfaceWeight(double otherMass) {
        return otherMass * surfaceGravity();
    }

    public static void main(String[] args) {
        if (args.length != 1) {
            System.err.println("Usage: java Planet <earth_weight>");
            System.exit(-1);
        }

        double earthWeight = Double.parseDouble(args[0]);
        double mass = earthWeight / EARTH.surfaceGravity();

        for (Planet p : Planet.values())
            System.out.printf("Your weight on %s is %f\n", p, p.surfaceWeight(mass));
    }
}
```

Command line로 인자 175를 주고 실행하면 다음과 같이 나온다.

```bash
$ java Planet 175
Your weight on MERCURY is 66.107583
Your weight on VENUS is 158.374842
Your weight on EARTH is 175.000000
Your weight on MARS is 66.279007
Your weight on JUPITER is 442.847567
Your weight on SATURN is 186.552719
Your weight on URANUS is 158.397260
Your weight on NEPTUNE is 199.207413
```