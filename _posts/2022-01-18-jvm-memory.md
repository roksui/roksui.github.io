---
title: "JVM and Memory Areas"
---

## JVM 안에 있는 Memory Areas
Java 가상 머신이 프로그램을 실행할 때는 여러가지의 것들을 저장하기 위해 메모리가 필요하다. 예를 들어, 로딩된 클래스 파일들로부터 추출한 바이트 코드 및 그 외 정보, 프로그램에 의해 인스턴스화되는 객체들, 메소드 파라미터, 리턴 값, 지역 변수, 계산 중간 값 등의 여러 가지 데이터 말이다. JVM은 프로그램을 실행하기 위해 필요한 메모리를 여러개의 런타임 데이터 구역(area)으로 체계화한다.

JVM의 메모리 구역은 다음과 같다.
1. Method Area
2. Heap Area
3. Stack Area
4. PC Registers
5. Native Method Stack

각각 살펴보자.

### Method Area
- 한 개의 JVM에는 한 개의 메소드 구역이 있다.
- 메소드 구역은 JVM이 가동될 때 생성된다.
- 메소드 구역 안에는, 정적 변수와 같은 클래스 레벨의 바이너리 데이터가 저장된다.
- 클래스에 대한 constant pool들이 메소드 구역에 저장된다.
- 메소드 구역은 여러 개의 스레드에 의해 동시에 접근될 수 있다.
- 메소드 구역의 크기는 고정될 필요가 없다. Java 애플리케이션이 실행하는 동안 가상 머신은 니즈에 따라 메소드 구역을 확장시키거나 축소시킬 수 있다.
- 모든 스레드들은 동일한 메소드 구역을 공유한다. 따라서 메소드 구역의 데이터 구조에 접근하는 것을 thread-safe하게 설계해야 한다.

### Heap Area
- 한 개의 JVM에는 한 개의 힙 구역이 있다.
- 힙 구역은 JVM이 가동될 때 생성된다.
- 객체와 그것과 매핑되는 인스턴스 변수들이 힙 구역에 저장된다.
- Java에서 배열은 객체이므로, 이 또한 힙 구역에 저장된다.
- 힙 구역은 여러 개의 스레드에 의해 접근될 수 있으므로, thread-safe하지 않을 수 있다.

### Heap Memory Statistics
Java 애플리케이션은 `Runtime` 클래스의 싱글톤 객체를 사용하여 JVM과 통신할 수 있다. 
`getRuntime` 메소드를 통해 `Runtime` 객체를 얻을 수 있다.

```java
Runtime run = Runtime.getRunner();

runtime.maxMemory() // 힙에 할당 가능한 최대 메모리의 바이트 수를 리턴함
runtime.totalMemory() // 힙에 할당된 메모리의 총 바이트 수를 리턴함
runtime.freeMemory() // 힙에 남아있는 사용 가능한 바이트 수를 리턴함
```

위를 출력해보면, 아웃풋이 다음과 같이 나올 수 있다.

```
889192448 // Max
60283120  // Total
58719832  // Free
```

### Set Maximum and Minimum heap size
힙 메모리는 우리의 요구사항에 따라 한정된 메모리로 늘리거나 줄일 수 있다.

Max heap size를 설정하기 위해 다음 커맨드를 사용한다.

```bash
java -Xmx512m HeapSpaceDemo
```

여기서, mx는 maximum size, 512m는 512 MB, HeapSpaceDemo는 Java 클래스 이름이다.

```bash
java -Xms65m HeapSpaceDemo
```

minimum heap size를 설정할 때는 ms(minimum size)로 명시한다.

혹은, 다음과 같이 한 번에 할 수도 있다.

```bash
java -Xms256m -Xmx1024m HeapSpaceDemo
```

### Stack Memory
각 스레드가 생성될 시 JVM은 런타임 스택을 생성한다. 스레드에 의한 모든 메소드 호출과 그에 해당하는 로컬 변수가 스택에 저장된다.

메소드 호출이 있을 때 마다, 새로운 엔트리가 스택에 추가되며, 이러한 엔트리를 스택 프레임(stack frame) 혹은 activation record라고 부른다.

호출한 메소드가 종료되면, 그에 해당하는 엔트리는 스택에서 제거된다. 모든 메소드가 촐요되어 스택이 비게되면, JVM은 스레드를 종료시키기 전에 스택을 소멸시킨다.

**NOTE**: 스택에 저장되는 데이터는, 그것을 가지는 스레드에게 private하다.

스택 프레임에 대해 좀 더 자세히 들여보자.

스택 프레임은 3가지 부분으로 구성되어 있다.
1. 로컬 변수 배열
2. 피연산자 스택
3. 프레임 데이터

#### 로컬 변수 배열
- 메소드의 모든 매개변수와 로컬 변수를 담고 있다.
- 배열의 각 슬롯은 4 바이트이다.
- int, float, 그리고 참조 변수는 한 개의 슬롯을 차지한다.
- long, double은 두 개의 연결된 슬롯을 차지한다.
- byte, short, char은 저장되기 전 int로 변환되어 한 개의 슬롯에 저장된다.
- boolean 타입 저장 방법은 JVM마다 다르지만, 대부분의 JVM은 한 개의 슬롯에 저장한다.

#### 피연산자 스택
- JVM은 피연산자 스택을 하나의 작업 공간으로 사용한다.
- 명령에 따라 값들이 피연산자 스택으로 올라갈 수도 있고, 연산이 수행될 수 있고, 결과 값이 저장될 수도 있다.
- 피연선자 스택 또한 스택이므로 LIFO 방법론을 따른다.
- 예를 들어, iadd 명령은 피연산자 스택에서 두 개의 int를 pop하여 서로 더한 후, int 결과 값을 push한다. JVM이 두 개의 int형 로컬 변수를 더한 뒤 다른 새로운 로컬 변수에 결과 값을 저장하는 예시는 다음과 같다.

```
iload_0    // push the int in local variable 0
iload_1    // push the int in local variable 1
iadd       // pop two ints, add them, push result
istore_2   // pop int, store into local variable 2
```

![operand-stack-process](/assets/img/operand_stack_process.png)

#### 프레임 데이터
로컬 변수 배열과 피연산자 스택 외에도 Java 스택 프레임에는 constant pool resolution, 메소드와 관련된 모든 symbolic reference, normal method return 및 exception dispatch를 지원하기 위한 데이터를 포함하고 있다.

이러한 데이터는 스택 프레임의 프레임 데이터에 저장된다. 프레임 데이터는 exception 테이블에 대한 참조값을 갖고 있는데, 여기서 exception 테이블은 이에 해당하는 예외 발생 시의 catch 블록 정보를 담고 있다. 메소드가 예외를 발생시키면 JVM은 프레임 데이터가 참조하는 exception 테이블을 사용하여 어떻게 예외를 처리할지를 결정하게 되는 것이다.

JVM이 constant pool에 있는 엔트리를 필요로하는 명령을 만나게 되면, 프레임 데이터가 가진 constant pool에 대한 포인터를 통해 해당 정보에 접근한다.

### PC Registers (Program Counter Registers)
각 스레드가 생성될 때, 스레드 마다 별도의 PC 레지스터가 생성된다. PC 레지스터는 현재 실행 중인 명령의 주소를 가지고 있다. 현재 명령이 완료되면, PC 레지스터는 자동으로 바로 다음 명령의 주소를 갖게된다. 여기서 말하는 주소(address)는 네이티브 포인터나 메소드의 바이트 코드의 시작점으로부터의 offset이 될 수 있다.

### Native Method Stacks
각 스레드 마다 각자의 런타임 스택이 생성된다. 여기에는 애플리케이션에 있는 모든 네이티브 메소드들이 포함된다. 네이티브 메소드라함은 Java 언어 외의 언어로 작성된 메소드를 말한다. 달리 말해, JNI (Java Native Interface)를 통해 호출되는 C/C++ 코드를 실행하기 위해 사용되는 스택이다. 언어에 따라 C, 혹은 C++ 스택이 생성된다.

스레드가 Java 메소드를 호출하면, JVM은 새로운 프레임을 만들어 Java 스택으로 push한다. 그러나 스레드가 네이티브 메소드를 호출하면, 잠시 Java 스택을 뒤로한다. 새로운 프레임을 Java 스택에 올리지 않고, JVM은 단순히 동적으로 네이티브 메소드를 링킹하여 바로 이를 호출한다.

TBD...