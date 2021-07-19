---
title: "Singleton Pattern"
---

## 싱글톤 패턴(Singleton) 이란?
소프트웨어 디자인 패턴 중 하나인 싱글톤 패턴을 따르는 클래스는, 생성자가 여러 차례 호출되더라도 실제로 생성되는 객체는 하나이고, 최초 생성 이후에 호출된 생성자는 최초의 생성자가 생성한 객체를 리턴한다.\

즉, 하나의 객체만을 생성하여 프로그램 전반에서 하나의 인스턴스만 사용하는 것이 싱글톤 패턴이다.

Java에서 많이 사용되며, 보통 다음과 같이 코딩할 수 있다.

{% highlight java %}
public class ExampleClass {
    private static ExampleClass instance = new ExampleClass();

    private ExampleClass() {}

    public static ExampleClass getInstance() {
        return instance;
    }
}
{% endhighlight %}

위 코드를 보면, instance라는 static 변수를 선언하여 인스턴스화 하지 않고도 접근할 수 있도록 하였지만, 접근 제어자가 private이기 때문에 직접적인 접근은 불가능하다.\
또, 생성자도 private이기 떄문에 new를 통한 객체 생성이 불가능하다.\
따라서, public으로 선언된 getInstance 메소드를 통해 해당 인스턴스를 얻을 수 있다.

## 왜 싱글톤 패턴을 사용하는가?
예를 들어, 레지스트리 같은 설정 파일의 경우 객체가 여러개 생성되면 설정 값이 변경될 위험이 생길 수 있다.\
또, 하나의 인스턴스를 메모리에 등록해서 여러 스레드가 동시에 해당 인스턴스를 공유하므로 요청이 많은 곳에서 사용하면 효율성을 높일 수 있다.

언뜻 보면 싱글톤이 매우 좋아보이지만 문제점도 존재한다.

## 싱글톤의 문제점
싱글톤으로 만든 객체의 역할이 간단하지 않고 복잡하다면, 해당 싱글톤 객체를 사용하는 다른 객체간의 결함도가 높아져서 객체 지향 설계 원칙에 어긋나게 된다. (개방-폐쇄)

또한, 해당 싱글톤 객체를 수정할 경우, 싱글톤 객체를 사용하는 곳에서 영향을 받기 쉬우며, 멀티쓰레드환경에서 동기화 처리 문제가 있다.

## 출처
https://elfinlas.github.io/2019/09/23/java-singleton/