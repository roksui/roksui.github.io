---
#layout: post
title:  "ASM - A Java bytecode Engineering Library (1)"
#date:   2021-05-14 15:10:56 +0900
#categories: jekyll update
---

## Introduction
ASM 라이브러리의 목적은 byte array 형태의 컴파일된 Java 클래스들을 생성, 변환, 분석하는 것이다. 따라서 ASM은 이러한 byte array를 읽고, 쓰고, 변환시킬 수 있는 byte보다는 high level의 개념(numeric constant, strings, Java identifiers, Java types Java class structure elements, etc)을 사용한다.

ASM 라이브러리는 컴파일된 클래스를 생성하고 변화하기 위한 두 가지의 API를 제공한다.
1. Core API - event based representation of classes
2. Tree API - object based representation of classes

## Part I. Core API
컴파일된 클래스는 소스코드가 갖는 구조적 정보와 대부분의 심볼을 그대로 가진다. 섹션 별로 다음과 같이 나뉜다.
- 클래스의 modifiers (public or private), 이름, 수퍼 클래스, 인터페이스, 어노테이션를 나타내는 섹션

- 클래스의 각 field마다 하나의 섹션: 각 섹션은 field의 modifiers, 이름, 타입, 어노테이션을 나타낸다.

- 클래스의 각 메서드와 생성자에 대한 섹션: 각 섹션은 메서드의 modifiers, 이름, 리턴/매개변수 타입, 어노테이션을 나타낸다. 또, 메서드의 컴파일 코드를 bytecode instruction의 나열로 나타낸다.

NOTE: 소스코드와 컴파일된 클래스의 다른점은 다음과 같다.
- 컴파일된 클래스는 오로지 하나의 클래스만 나타내지만 소스파일은 여러 개의 클래스를 나타낼 수 있다. 예를 들어 소스파일에서 한 클래스 안에 inner 클래스가 컴파일 될 경우, 두 개의 클래스 파일로 컴파인 된다: 하나는 main 클래스, 하나는 inner 클래스. 여기서 main 클래스 파일은 inner 클래스에 대한 reference를 가지고, 메서드 안에서 정의된 inner 클래스는 상위 메서드에 대한 reference를 가진다.

- 컴파일된 클래스는 주석을 포함하지 않는다.

- 컴파일된 클래스는 package와 import 섹션을 포함하지 않는다.

또 한가지 매우 중요한 구조적 차이점은 컴파일된 클래스는 constant pool 섹션을 가진다는 점이다. 이 pool은 클래스에서 나타나는 numeric, string 및 타입 constant들을 가지는 배열이다.
Constant들은 constant pool 섹션에서 한 번 정의되고 그 후부터 클래스의 다른 섹션에서 불러올 때는 인덱스를 통해 reference된다.

{:refdef: style="text-align: center;"}
![Compiled JavaClass](/assets/img/compiled_javaclass.png)

<U>Figure 1. Compiled Java Class (*는 0 혹은 그 이상을 뜻함) </U>
{: refdef}


### 컴파일된 클래스의 Java types
1. Internal Names
많은 경우에서 타입은 class나 interface type로 제한된다. 이러한 경우 <i>internal names</i>로 나타낸다. 이는 클래스의 fully qualified name으로써 예를 들어 String은 java/lang/String이 된다.

2. Type Descriptors
class나 interface type일 경우 internal name을 사용하지만 그 외 다른 경우(field 타입, Java 타입)은 <i>type descriptors</i>로 나타낸다.

{:refdef: style="text-align: center;"}
![Compiled JavaClass](/assets/img/type_descriptors.png)

<U>Figure 2. Java type들의 Type descriptors </U>
{: refdef}

Primitive type의 경우 (boolean, char, byte, short, int, float, long, double)와 class type(internal name앞에 'L'이 붙는다)로 나눌 수 있다. 배열의 경우 '['가 앞에 붙는다.

3. Method Descriptors
<i>method descriptor</i>는 메서드의 매개변수 타입과 리턴 타입을 나타내는 type descriptor들을 하나의 string으로 표현한 것이다.

몇 가지 예시를 들면 다음과 같다.
{% highlight java %}
void m(int i, float f) // ==> (IF)V
int m(Object o) // ==> (Ljava/lang/Object;)I
int[] m(int i, String s) // ==> (ILjava/lang/String;)[I
Object m(int[] i) // ==> ([I)Ljava/lang/Object;
{% endhighlight %}

### Interfaces and Components
컴파일된 클래스를 생성 및 변환하기 위한 ASM API는 ClassVisitor라는 추상 클래스를 기반으로 한다.