---
title: "Java - Lambda Expression"
---

## What is it?
Java에서 람다식(Lambda/Lambda Expression)은 Java 8에 추가되었고 Java가 함수형 언어로 들어가는 첫 단계였다고 볼 수 있다.
Java 람다식은 어떠한 클래스에 속하지 않고도 생성될 수 있는 함수이다. 또, 람다식은 객체로 여겨져 전달될 수 있다.

Java 람다식은 보통 이벤트 리스너/콜백을 구현할 때, 혹은 Java Streams API를 사용하는 함수형 프로그래밍에 사용된다.

- Java Lambdas and Single Method Interface
Java에서 이벤트 리스너는 주로 한 개의 메소드를 가진 인터페이스로 정의된다. 예를 들어 다음과 같이 StateChangeListener를 정의할 수 있다.
{% highlight java %}
public interface StateChangeListener {
    public void onStateChange(State oldState, State newState);
}
{% endhighlight %}

이 인터페이스는 어떠한 객체의 상태가 변화할 때 호출되는 한 개의 메소드를 가지고 있다.

Java 7에서는 이러한 인터페이스를 구현하여 상태 변화에 대해 listen한다.
예를 들어, 상태 이벤트 리스너를 등록할 수 있는 StateOwner 클래스를 만들자.

{% highlight java %}
public class StateOwner {
    public void addStateListener(StateChangeListener listener) { ... }
}
{% endhighlight %}

익명 인터페이스 구현을 사용하여 이벤트 리스너를 추가할 수 있다.
{% highlight java %}
StateOwner stateOwner = new StateOwner();

stateOwner.addStateListener(new StateChangeListener() {
    public void onStateChange(State oldState, State new State) {
        // do something
    }
});
{% endhighlight %}

위 코드를 보면, 먼저 StateOwner 인스턴스가 생성되고, StateChangeListener 인터페이스를 익명으로 구현해 StateOwner 인스턴스의 리스너로 추가된다.

Java 8에서는 다음과 같이 람다식을 이용하여 이벤트 리스너를 추가할 수 있다.

{% highlight java %}
StateOwner stateOwner = new StateOwner();

stateOwner.addStateListener(
    (oldState, newState) -> System.out.println("State changed");
);
{% endhighlight %}

위 람다식은 addStateListener() 메소드의 매개변수로 전달된다. 만약 람다식이 이 매개변수 타입(위의 경우는 StateChangeListener 인터페이스)과 같으면, 람다식은 매개변수에 해당하는 인터페이스를 구현하는 함수로 변환된다.

Java 람다식은 매칭되는 타입이 single method interface일 경우만 사용할 수 있다. 위의 예시에서, 매칭되는 매개변수 타입이 StateChangeListener 인터페이스였는데, 이 인터페이스는 한 개의 메소드만 가지고 있기 때문에 람다식이 사용 가능하였다.

- Matching Lambdas to Interfaces
위에서 언급한 single method interface는 functional interface라고 부르기도 한다. 람다식을 functional interface와 매칭시키는 과정을 세 가지 단계로 나눌 수 있다.
1) 인터페이스가 오로지 한 개의 추상 (구현되지 않은) 메소드를 가지고 있는가?
2) 람다식의 매개변수가 해당 메소드의 매개변수와 일치하는가?
3) 람다식의 리턴 타입이 해당 메소드의 리턴 타입과 일치하는가?

위 세 가지에 대한 대답이 모두 "yes"일 경우, 주어진 람다식은 인터페이스와 성공적으로 매칭이 된다.

- Interfaces with Default and Static Methods
Java 8부터 인터페이스는 default 메소드와 static 메소드 둘다 가질 수 있게 되었다. default와 static 메소드는 인터페이스 선언에 바로 구현될 수 있다. 이 말은 즉, 람다식은 한 개 이상의 메소드를 가진 인터페이스를 구현할 수 있다는 얘기가 된다 - 인터페이스가 오로지 하나의 구현되지 않은 추상 메소드를 갖고 있다는 전제 하에.

**정리하자면, 인터페이스는 default와 static 메소드를 가지고 있더라도, 단 하나의 구현되지 않은 추상 메소드를 가지면 여전히 functional interface이다.**

다음의 예시를 보자.
{% highlight java %}
import java.io.IOException;
import java.io.OutputStream;

public interface MyInterface {
    void printIt(String text);

    default public void printUtf8To(String text, OutputStream outputStream) {
        try {
            outputStream.write(text.getBytes("UTF-8"));
        } catch (IOException e) {
            throw new RuntimeException("Error writing String as UTF-8 to OutputStream", e);
        }
    }

    static void printItToSystemOut(String text) {
        System.out.println(text);
    }
}
{% endhighlight %}

이 인터페이스는 3개의 메소드를 가지고 있지만, 오로지 한 개의 메소드가 구현되지 않았기 때문에 람다식으로 구현할 수 있다.

{% highlight java %}
MyInterface myInterface = (String text) -> {
    System.out.print(text);
};
{% endhighlight %}

- Lambda Expression vs Anonymous Interface Implementations
람다식과 익명 인터페이스 구현과 비슷하여도, 차이점이 있다.
가장 큰 차이점은 익명 인터페이스 구현은 상태(멤버 변수)를 가질 수 있지만 람다식은 가질 수 없다는 것이다. 다음의 예를 보자.

{% highlight java %}
public interface MyEventConsumer {
    public void consume(Object event);
}
{% endhighlight %}

이 인터페이스는 익명 인터페이스 구현으로 구현될 수 있다.

{% highlight java %}
MyEventConsumer consumer = new MyEventConsumer() {
    public void consume(Object event) {
        System.out.println(event.toString() + " consumed");
    }
};
{% endhighlight %}

이 익명 MyEventConsumer 구현은 그것만의 내부 상태를 가질 수 있다.

{% highlight java %}
MyEventConsumer myEventConsumer = new MyEventConsumer() {
    private int eventCount = 0;
    public void consume(Object event) {
        System.out.println(event.toString() + " consumed" + this.eventCount++ + " times.");
    }
};
{% endhighlight %}

위와 같이 익명 인터페이스 구현법은 eventCount 멤버변수를 가질 수 있다.
하지만 람다식은 이러한 필드값을 가질 수 없으므로 stateless하다고 부른다.

- Lambda Type Inference
Java 8전에는 익명 인터페이스 구현을 하려면 어떠한 인터페이스를 구현할지를 명시해야 하였다. 위의 예시를 다시 보자.

{% highlight java %}
stateOwner.addStateListener(new StateChangeListener() {
    public void onStateChange(State oldState, State newState) {
        // do something
    }
});
{% endhighlight %}

람다식에서는 주변 코드로부터 타입이 추론될 수 있다. 예를 들어, 매개변수의 인터페이스 타입은 addStateListener() 메소드 (StateChangeListener 인터페이스가 가진 single method) 선언으로부터 추론될 수 있다. 이를 타입 추론(Type Inference)이라고 한다. 컴파일러는 코드의 다른 부분을 확인하여 매개변수의 타입을 추론한다 - 위의 경우에는 메소드 정의 부분을 본다. 다음과 같이 람다식으로 변형된 것을 보면, StateChangeListener 인터페이스는 안적는다.

{% highlight java %}
stateOwner.addStateListener(
    (oldState, newState) -> System.out.println("State changed")
);
{% endhighlight %}

또한, 람다식에서의 매개변수들의 타입도 추론될 수 있다. 컴파일러는 onStateChange() 메소드 선언부에서 oldState과 newState의 타입을 추론한다.

- Lambda Parameters
람다식은 어떻게 보면 메소드이기 떄문에, 매개변수를 전달 받아서 어떠한 결과를 내놓는다. 이때, 매개변수들은 single method interface에 있는 메소드의 매개변수와 매칭되어야 한다. 위의 경우, 람다식의 매개변수는 onStateChange() 메소드의 매개변수와 매칭되어야 한다.

    Zero Parameters
    만약의 람다식과 매칭하려는 메소드가 매개변수를 받지 않는다면 다음과 같이 적을 수 있다.
    {% highlight java %}
    () -> System.out.println("Zero parameter lambda");
    {% endhighlight %}

    One Parameter
    {% highlight java %}
    param -> System.out.println("One parameter: " + param");
    //(param) -> System.out.println("One parameter: " + param"); 매개변수가 한 개일 경우 괄호는 optional함
    {% endhighlight %}

    Multiple Parameters
    {% highlight java %}
    (p1, p2) -> System.out.println("Multiple parameters: " + p1 + ", " + p2);
    {% endhighlight %}

    var Parameter Types from Java 11
    Java 10에서 지역변수 타입 추론을 위해 소개된 var 키워드가 Java 11부터는 람다식에서 매개변수의 타입으로 쓸 수 있다.
    
    {% highlight %}
    Function<String, String> toLowerCase = (var input) -> input.toLowerCase();
    {% endhighlight %}
    
    여기서 input에 대한 타입은 String으로 추론될 것이다. 왜냐하면 변수의 타입 선언은 Function<String, String>으로 되어있기 때문에 Function의 매개변수 타입과 리턴 타입이 String이다.

- Lambda Function Body
람다식의 바디 부분은 -> 오른쪽에 위치한다. 또, 여러 줄이면 {}안에 넣어주면 된다.

{% highlight java %}
(oldState, newState) -> {
    System.out.println("Old state: " + oldState);
    System.out.println("New state: " + newState);
}
{% endhighlight %}

- Returning a value from a Lambda Expression
메소드에서 값을 리턴하듯이, 람다식에서도 값을 리턴할 수 있다. 바디 부분에 return문을 추가해주면 된다.
    
{% highlight java %}
(param) -> {
    System.out.println("param: " + param);
    return "return value";
}
{% endhighlight %}

**NOTE**: 만약 람다식이 단지 어떠한 값을 계산하고 리턴만 한다면 다음과 같이 줄일 수도 있다.
    
{% highlight java %}
(a1, a2) -> { return a1 > a2; } // 이를 다음과 같이 줄일 수 있다.
(a1, a2) -> a1 > a2;
{% endhighlight %}

컴파일러는 a1 > a2 식이 람다식의 리턴 값이라는 것을 알아챈다. 람다식은 말그대로 하나의 식(Statement)이기 때문에 어떠한 종류의 '값'을 리턴하는 것이 당연하기도 하다.

- Lambda as Objects
자바에서 람다식은 본질적으로는 하나의 객체이다. 객체를 전달하고 이리저리 넘기듯이, 어떠한 변수에 람다식을 대입하여 전달할 수 있다.

{% highlight java %}
public interface MyComparator {
    public boolean compare(int a1, int a2);
}
{% endhighlight %}

{% highlight java %}
MyComparator myComparator = (a1, a2) -> a2 > a2;
boolean result = myComparator.compare(2, 5);
{% endhighlight %}

첫 코드 블럭은 람다식이 구현하는 인터페이스이다. 두번째 코드 블럭에서는 람다식을 정의하였고, 람다식이 변수에 대입되어 인터페이스 메소드를 호출함으로써 람다식이 호출되는 것을 보여준다.

- Variable Capture

    Local Variable Capture
    자바 람다식은 람다식 바디 밖에 선언된 지역변수 값을 캡쳐(참조)할 수 있다. 다음과 같은 single method interface를 보자.

    {% highlight java %}
    public interface MyFactory {
        public String create(char[] chars);
    }
    {% endhighlight %}

    MyFactory 인터페이스를 구현하는 람다식이 있다.

    {% highlight java %}
    MyFactory myFactory = (chars) -> {
        return new String(chars);
    };
    {% endhighlight %}

    현재, 위 람다식은 chars만을 참조하고 있다. 하지만, 다음과 같이 바디 밖의 변수 또한 참조 가능하다.

    {% highlight java %}
    String myString = "Test";

    MyFactory myFactory = (chars) -> {
        return myString + ":" + new String(chars);
    };
    {% endhighlight %}

    이러한 참조는 참조하려는 변수가 "effectively final"할 때만 가능하다. 즉, 변수값이 한 번 대입되면 바뀌지 않는 다는 전제 하에만 가능하다. 만약 myString 값이 나중에 바꼈다면, 컴파일러가 불만을 표시할 것이다.

    Instance Variable Capture
    
    {% highlight java %}
    public class EventConsumerImpl {
        private String name = "MyConsumer";

        public void attach(MyEvent Producer eventProducer) {
            eventProducer.listen(e -> {
                System.out.println(this.name);
            });
        }
    }
    {% endhighlight %}

    람다식에서 this.name을 참조하고 있다. 이는 람다식을 감싸고 있는 EventConsumerImpl 객체의 인스턴스 변수 name을 캡쳐한다. 또한, 캡쳐 후에 인스턴스 변수 값을 변경하는 것도 가능하다 - 이 변경은 람다식에도 반영된다.

    this의 시멘틱과 관련해서 람다식과 익명 인터페이스 구현의 차이점이 존재한다. 익명 인터페이스 구현은 자신의 인스턴스 변수를 가질 수 있고 이는 this를 통해 참조할 수 있다. 하지만, 람다식은 자신의 인스턴스 변수를 가질 수 었다. 따라서 this는 항상 람다식을 감싸는 객체를 가르킨다.

    Static Variable Capture
    람다식은 static 변수도 캡쳐할 수 있다. static 변수는 package scoped거나 public이면 자바 어플리케이션 어디에서나 접근할 수 있기 떄문에 놀랍지 않은 사실이긴하다.

    {% highlight java %}
    public class EventConsumerImpl {
        private static String someStaticVar = "Some text";

        public void attach(MyEventProducer eventProducer) {
            eventProducer.listen(e -> {
                System.out.println(someStaticVar);
            });
        }
    }
    {% endhighlight %}
    인스턴스 변수처럼 static 변수값도 람다가 캡쳐한 후에 바뀔 수 있다.

- Method References as Lambdas
람다식이 오로지 전달받은 매개변수를 사용하여 다른 메소드를 부르는 일만 한다면 좀 더 간결하게 적을 수 있는 방법이 있다.
예를 들어 MyPrinter 인터페이스를 다음과 같이 람다식으로 구현한 예를 보자.

{% highlight java %}
public interface MyPrinter {
    public void print(String s);
}
{% endhighlight %}

{% highlight java %}
MyPrinter myPrinter = s -> System.out.println(s);
{% endhighlight %}

이 람다식을 method reference(메소드 참조)로 바꿀 수 있다.

{% highlight java %}
MyPrinter myPrinter = System.out::println;
{% endhighlight %}

컴파일러는 '::'를 메소드 참조로 인식하여 '::' 이후에 오는 메소드를 참조하게 된다. 그 전에 오는 것들은 그 메소드를 가지고 있는 클래스/객체가 될 것이다.

참조할 수 있는 메소드 종류를 알아보자.

    Static Method References
    참조할 수 있는 가장 쉬운 메소드는 static 메소드이다.
        
    {% highlight java %}
    public interface Finder {
        public int find(String s1, String s2);
    }
    {% endhighlight %}

    다음은 우리가 참조하고자 하는 static 메소드이다.

    {% highlight java %}
    public class MyClass {
        public static int doFind(String s1, String s2) {
            return s1.lastIndexOf(s2);
        }
    }
    {% endhighlight %}

    그리고 우리는 람다식을 통해 다음과 같이 static 메소드를 참조할 수 있다.

    {% highlight java %}
    Finder finder = MyClass::doFind;
    {% endhighlight %}

    NOTE: Finder.find()의 매개변수와 MyClass.doFind()의 매개변수가 매칭이 되기 때문에 Finder.find()를 구현하는 람다식을 만들어 MyClass.doFind()를 참조가 가능하게 되는 것이다.

    Parameter Method Reference
    람다식 매개변수의 메소드를 참조하는 것도 가능하다.

        
    {% highlight java %}
    public interface Finder {
        public int find(String s1, String s2) {
        }
    }
    {% endhighlight %}

    위 인터페이스는 s1에서 s2를 검색할 수 있는 기능을 가지고, 다음과 같은 람다식 인터페이스 구현을 할 수 있다.
        
    {% highlight java %}
    // 아래 두개 식은 동일한 결과를 가진다.
    Finder finder = String::indexOf;
    Finder finder = (s1, s2) -> s1.indexOf(s2); 
    {% endhighlight %}

    NOTE: 컴파일러는 참조한 메소드를 첫번째 매개변수에 대하여 참조하고, 두번째 매개변수를 참조 메소드의 매개변수로 사용한다.

    Instance Method References
    인스턴스 메소드 참조 또한 가능하다.
        
    {% highlight java %}
    public interface Deserializer {
        public int deserialize(String v1);
    }
    {% endhighlight %}

    이 인터페이스는 String을 int로 deserialize해주는 컴포넌트다.
    다음과 같은 StringConverter 클래스를 보자.

        
    {% highlight java %}
    public class StringConverter {
        public int convertToInt(String v1) {
            return Integer.valueOf(v1);
        }
    }
    {% endhighlight %}

    convertToInt()는 Deserializer.deserialize와 같은 메소드 시그니쳐를 갖고 있다. 그렇기 때문에 StringConverter 인스턴스를 만들어 람다식을 통해 해당 인스턴스의 convertToInt() 메소드를 참조할 수 있다.
        
    {% highlight java %}
    StringConverter stringConverter = new StringConverter();
    Deserializer des = stringConverter::convertToInt;
    {% endhighlight %}

    Constructor References
    마지막으로 클래스의 생성자도 참조할 수 있다. '클래스명::new'를 통해 가능하다.
        
    {% highlight java %}
    public interface Factory {
        public String create(char[] val);
    }
    {% endhighlight %}

    이 인터페이스의 create 메소드는 String 클래스의 생성자 중 하나의 시그니쳐와 매칭된다. 따라서 해당 생성자는 람다식으로 사용 가능하다.
        
    {% highlight java %}
    // 아래 두개의 식이 같은 결과를 같는다.
    Factory factory = String::new;
    Factory factory = chars -> new String(chars);
    {% endhighlight %}

출처
- http://tutorials.jenkov.com/java/lambda-expressions.html