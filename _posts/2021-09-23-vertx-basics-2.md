---
title: Vert.x Basics(2) - Verticles and Event Bus
categories: 
  - Vert.x
---

우리의 첫번째 Vert.x 애플리케이션을 만들어 보자.

## Create Vertx Instance
우리의 Java 애플리케이션에 들어갈 Vert.x를 사용하는 가장 첫 단계는 Vertx 인스턴스를 만드는 것이다.

{% highlight java %}
import io.vertx.core.Vertx;

public class VertxApp {
    public static void main(String[] args) {
        Vertx vertx = Vertx.vertx();
    }
}
{% endhighlight %}

위와 같이 `Vertx.vertx()`를 호출하여 인스턴스를 만든다. Vertx 인스턴스는 Verticle들 간의 메시지 교환을 처리하기 위해 내부적으로 여러 개의 스레드를 생성한다.
**NOTE**: 이 스레드들은 daemon thread가 아니기 때문에 Vertx 인스턴스를 생성한 메인 스레드가 종료되어도 JVM을 셧다운시키지 않는다. (daemon thread는 Garbage Collecting과 같은 태스크를 수행하는 백그라운드 스레드로써 메인 스레드가 종료되면 JVM도 셧다운 되는 것을 막지 못한다.)

## Creating a Verticle
Vertx 인스턴스는 스레드 관리, event bus 생성 등과 같이 통신 및 인프라 태스크 빼고는 많은 일을 하지는 않는다. 애플리케이션을 유용하게 만들려면 Vertx 인스턴스 안에 한 개 이상의 Verticle 컴포넌트를 배포(deploy)해야한다.

Verticle을 배포하기에 앞서 생성을 먼저 해야 한다. 다음과 같이 AbstractVerticle을 상속받는 클래스를 만들면 된다.

{% highlight java %}
import io.vertx.core.AbstractVerticle;
import io.vertx.core.Future;

public class MyVerticle extends AbstractVerticle {
    @Override
    public void start(Future<Void> startFuture) {
        System.out.println("MyVerticle started!");
    }

    @Override
    public void stop(Future stopFuture) throws Exception {
        System.out.println("MyVerticle stopped!");
    }
}
{% endhighlight %}

Verticle은 `start()`와 `stop()` 메소드를 가지고, 배포되었을 때 그리고 해제(undeploy)되었을 때 호출된다. 따라서 `start()`안에서는 적절한 초기화 작업 (주로 HTTP/TCP 서버 생성, event bus의 event handler 등록, 다른 Verticle 배포 등), `stop()`에서는 클린업 작업을 하면 된다.

## Deploying a Verticle
Verticle을 생성했다면 Vertx 인스턴스로 배포해야 할 차례이다. 이는 Vertx 인스턴스에 `deployVerticle()`메소드를 호출하면 된다. 예를 들어 다음과 같다.

{% highlight java %}
import io.vertx.core.Vertx;

public class VertxVerticleMain {
    public static void main(String[] args) {
        Vertx vertx = Vertx.vertx();

        vertx.deployVerticle(new MyVerticle());
    }
}
{% endhighlight %}

`vertx.deplyVerticle(new MyVerticle())`이 호출되면 Verticle의 `start()`메소드가 호출된다. (Vert.x가 종료되면 `stop()`이 호출됨)

이 때, Verticle은 비동기적으로 배포되기 때문에 `deployVerticle()`메소드가 리턴하는 시점에 배포되어 있지 않을 수도 있다. 만약 Verticle이 언제 완전히 배포되는지 알아야 한다면, `deployVerticle()`에 Handler를 구현하면 된다.

{% highlight java %}
vertx.deployVerticle(new MyVerticle(), new Handler<AsyncResult<String>>() {
    @Override
    public void handle(AsyncResult<String> stringAsyncResult) {
        System.out.println("MyVerticle deployment complete!");
    }
});
{% endhighlight %}

좀 더 간단하게 람다식을 쓴다면,

{% highlight java %}
vertx.deployVerticle(new MyVerticle(), stringAsyncResult -> {
    System.out.println("MyVerticle deployment complete!");
});
{% endhighlight %}

## Deploying a Verticle From Another Verticle
Verticle 클래스 안에서 또 다른 Verticle을 배포하는 것도 가능하다.

{% highlight java %}
public class MyVerticle extends AbstractVerticle {
    @Override
    public void start() throws Exception {
        System.out.println("MyVerticle started");

        vertx.deployVerticle(new SecondVerticle());
    }

    @Override
    public void stop() throws Exception {
        System.out.println("MyVerticle stopped");
    }
}
{% endhighlight %}

## Using the Event Bus
Verticle들은 event bus를 통해 메시지를 listen하고 event bus를 통해 서로에게 메시지를 전달한다.

Event bus에서 오는 메시지를 listen하기 위해 Verticle은 특정한 address에서 listen하는데, address는 단지 임의의 스트링 값이다.

여러개의 Verticle들이 같은 address에서 메시지를 listen할 수 있다. 따라서 address는 하나의 Verticle에 대해 유니크하지 않다.

Verticle은 event bus에 대한 참조를 vertx 인스턴스를 통해 할 수 있다.

다음은 특정 address에서 메시지를 리스닝하는 방법이다.

{% highlight java %}
public class EventBusReceiverVerticle extends AbstractVerticle {
    
    public void start(Future<Void> startFuture) {
        vertx.eventBus().consumer("anAddress", message -> {
            System.out.println("1 received message.body() = "
                + message.body());
        });
    }
}
{% endhighlight %}

위 예제를 보면, Verticle이 메시지 consumer(listener)를 Vert.x event bus에 등록하는 것을 알 수 있다. 해당 consumer는 `anAddress`라는 address로 등록되어 있다 - event bus를 통해 이 address로 전송되는 메시지를 consume(listen)할 수 있다.

## Sending Messages
Event bus를 통해 메시지를 보내기 위해서 event bus의 `send()`나 `publish()`를 사용하면 된다.

`publish()` 메소드는 특정 address에 리스닝하는 모든 Verticle들에게 메시지를 보낸다.

`send()` 메소드는 리스닝하고 있는 단 하나의 Verticle에게만 메시지를 보낸다. 어떤 Verticle이 메시지를 받을지는 Vert.x에 의해 결정된다. 공식 문서에 의하면 "non-strict round robin algorithm"에 의해 Verticle이 정해지는데, 리스닝하고 있는 Verticle들에게 최대한 균등하게 메시지를 배포하는 것을 목표로 하는 것 같다.

다음 예제에서는 두 개의 event bus consumer(listener)와 한 개의 event bus sender를 배포한다. Sender는 특정 address로 두 개의 메시지를 보낸다. 첫 번째 메시지는 `publish()` 메소드를 사용하여 보내지므로 두 consumer 모두 메시지를 받게 되고, 두 번째 메시지는 `send()` 메소드를 통해 보내지므로 한 개의 consumer만 메시지를 받게된다.

{% highlight java %}
Vertx vertx = Vertx.vertx();

vertx.deployVerticle(new EventBusReceiverVerticle("R1"));
vertx.deployVerticle(new EventBusReceiverVerticle("R2"));

Thread.sleep(3000);
vertx.deployVerticle(new EventBusSenderVerticle());
{% endhighlight %}

{% highlight java %}
public class EventBusSenderVerticle extends AbstractVerticle {
    
    public void start(Future<Void> startFuture) {
        vertx.eventBus().publish("anAddress", "message 2");
        vertx.eventBus().send("anAddress", "message 1");
    }
}
{% endhighlight %}

{% highlight java %}
public class EventBusReceiverVerticle extends AbstractVerticle {
    private String name = null;

    public EventBusReceiverVerticle(String name) {
        this.name = name;
    }

    public void start(Future<Void> startFuture) {
        vertx.eventBus().consumer("anAddress", message -> {
            System.out.println(this.name + " received emssage: " + message.body());
        });
    }
}
{% endhighlight %}

## 출처
http://tutorials.jenkov.com/vert.x/verticles.html