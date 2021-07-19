---
title: "Java Generics"
---

## 개요
Java Generics는 버그를 줄이고 타입에 대한 추상화를 한 층 강화하기 위해 J2SE 5.0에 소개되었다.

## 왜 Generic이 필요한가?
Integer형 데이터를 보관하기 위해 리스트를 생성한다고 가정해보자. 우리는 다음과 같이 쓸 수 있다.
{% highlight java %}
List list = new LinkedList();
list.add(new Integer(1));
Integer i = list.iterator().next();
{% endhighlight %}

하지만 컴파일러는 마지막 줄에 대해 불만을 표할 것이다. 어떤 데이터 타입이 리턴되는지 컴파일러는 알 수 없기 때문이다. 따라서 다음과 같은 explicit한 캐스팅이 필요하다.
{% highlight java %}
Integer i = (Integer) list.iterator.next();
{% endhighlight %}

위 리스트의 리턴 타입이 Integer일 것이란 보장이 없다 - 정의된 리스트는 아무런 객체를 가질 수 있다. 그래서 리턴하는 타입이 safe한지를 보장하기 위해 explicit 캐스팅이 필요하다.\
하지만 이런 캐스팅은 성가시고, 잘못쓰면 런타임 에러를 야기할 수도 있다. 따라서 제너릭을 통해 컴파일 타임에 버그를 발견할 수 있는 장점이 있다.\
개발자가 특정한 타입을 쓰겠다는 의도를 표현하고 컴파일러가 이를 미리 알 수 있다면 훨씬 더 간편할 것이다 - 이것이 제너릭의 핵심 모티브이다.

위 코드 snippet을 다음과 같이 고쳐보자.

{% highlight java %}
List<Integer> list = new LinkedList<>();
{% endhighlight %}
'<>'에 타입을 명시해줌으로써 리스트가 Integer 타입만 가질 수 있다는 것을 컴파일러가 알 수 있다.

## Generic Methods
제너릭 메소드란 하나의 메소드 정의로 여러 종류의 타입을 가진 매개변수로 호출될 수 있는 메소드이다.
예를 들어 배열을 리스트로 변환하는 제너릭 메소드를 다음과 같이 작성할 수 있다.

{% highlight java %}
public <T> List<T> fromArrayToList(T[] a) {
    return Arrays.stream(a).collect(Collectors.toList());
}
{% endhighlight %}

메소드 시그니쳐를 보면 리턴 타입을 명시하기 전에 제너릭 타입 T를 먼저 써준다. 콤마를 사용하여 한 개 이상의 제너릭 타입도 명시가 가능하다. 

{% highlight java %}
public <T, G> List<G> fromArrayToList(T[] a, Function<T, G> mapperFunction) {
    return Arrays.stream(a)
      .map(mapperFunction)
      .collect(Collectors.toList());
}
{% endhighlight %}

여기서 public 제어자 다음에 쓰는 제너릭 <T>나 <T, G>는 타입 매개변수(type parameter)라고 부르며 이는 해당 메소드가 제너릭이고, 명시된 타입의 매개변수를 받는다는 의미를 가진다.

## Bounded Generics
타입 매개변수는 bounded될 수 있다. 즉, 메소드가 받는 매개변수의 타입에 제한을 둘 수 있다는 말이다.

예를 들어, upper bound를 두어, 메소드가 특정한 타입과 그것의 subclass들만 받을 수 있도록 하거나, 반대로 특정한 타입과 그것의 superclass들만 받는 lower bound를 둘 수 있다.

upper bound를 두기 위해, 다음과 같이 extends 키워드를 사용할 수 있다. (lower bound는 super 키워드)

{% highlight java %}
public <T extends Number> List<T> fromArrayToList(T[] a) {
    ...
}
{% endhighlight %}

기억하기 쉽게, 타입 매개변수 T는 Number를 상속 받는(extend 하는) 타입이라고 생각하면 되겠다.

More on Multiple Bounds and wildcards to be continued...

### 출처
https://www.baeldung.com/java-generics
