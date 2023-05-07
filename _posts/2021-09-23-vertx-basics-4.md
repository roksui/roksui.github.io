---
title: Vert.x Basics(4) - HTTP Server
categories: 
  - Vert.x
---

우리의 애플리케이션이 HTTP request를 받을 수 있도록 Vert.x를 통해 HTTP 서버를 만들 수 있다. 필요에 따라 한 개 이상의 HTTP 서버를 만들 수 있다.

여기서는 Verticle로 HTTP 서버를 만들고 request를 처리하는 예제를 다루어 보겠다.

### Creating an HTTP Server
HTTP 서버를 만들기 위해서는 Vertx 인스턴스의 `createHTTPServer()`메소드를 사용한다.

{% highlight java %}
HttpServer httpServer = vertx.createHttpServer();
{% endhighlight %}

통상 Verticle 안에서 HTTP 서버를 만든다. 그렇게 해야 Verticle을 시작한 같은 스레드에서 HTTP 서버에 등록된 핸들러들을 실행할 수 있다.

{% highlight java %}
import io.vertx.core.AbstractVerticle;
import io.vertx.core.http.HttpServer;

public class VertxHttpServer extends AbstractVerticle {
    private HttpServer httpServer = null;

    @Override
    public void start() throws Exception {
        httpServer = vertx.createHttpServer;
    }
}
{% endhighlight %}

### Starting the HTTP Server
HTTP 서버를 생성한 후 `listen()` 메소드를 사용하여 서버를 시작할 수 있다.

{% highlight java %}
public class VertxHttpServerVerticle extends AbstractVerticle {
    private HttpServer httpServer = null;

    @Override
    public void start() throws Exception {
        httpServer = vertx.createHttpServer();

        httpServer.listen(9999);
    }
}
{% endhighlight %}
NOTE: HttpServer 클래스에 있는 `listen()`메소드는 여러 옵션을 주어서 호출할 수도 있다.

### Sending a Request Handler on the HTTP Server
들어오는 HTTP request를 처리하기 위해서 HTTP Server에 request handler를 설정해야 한다. 이는 주로 서버를 시작하기 전에 수행한다.

{% highlight java %}
public class VertxHttpServerVerticle extends AbstractVerticle {
    private HttpServer httpServer = null;

    @Override
    public void start() throws Exception {
        httpServer = vertx.createHttpServer();

        httpServer.requestHandler(new Handler<HttpServerRequest>() {
            @Override
            public void handle(HttpServerRequest request) {
                System.out.println("incoming request!");
            }
        });
    }
}
{% endhighlight %}

HTTP 서버에 request가 도착할 때마다 Handler 객체의 `handle()`메소드가 호출된다. `handle()`메소드 안에서는 HTTP request를 처리하기 위해 필요한 코드를 작성하면 된다.

### Request Headers and Parameters
`handle()`메소드의 매개변수로 넘긴 HttpServerRequest 객체로부터 HTTP 헤더와 파라미터값들에 접근할 수 있다.
다음 예시는 하나의 HTTP request의 몇 가지 속성에 접근하는 것을 보여준다.

{% highlight java %}
httpServer.requestHandler(new Handler<HttpServerRequest>() {
    @Override
    public void handle(HttpServerRequest request) {
        System.out.println("incoming request!");

        request.uri();
        request.path();
        request.getParam("p1");
    }
});
{% endhighlight %}

### Handling POST Requests
만약 HTTP request가 POST request라면 조금 다르게 처리해줘야 한다. HTTP request에 body handler를 붙여줘야 한다. request body 데이터가 도착할 때마다 body handler가 호출된다.

{% highlight java %}
httpServer.requestHandler(new Handler<HttpServerRequest>() {
    @Override
    public void handle(HttpServerRequest request) {
        System.out.println("incoming request!");

        if (request.method() == HttpMethod.POST) {
            request.handler(new Handler<Buffer>() {
                @Override
                public void handle(Buffer buffer) {

                }
            });
        }
    }
});
{% endhighlight %}

만약 HTTP POST body의 전체가 도착할 때까지 기다리고 싶다면 end handler를 붙이면 된다. end handler는 HTTP POST body 전체가 도착하기 전까지 호출되지 않는다. 그러나 end handler는 HTTP POST body 전체에 대한 직접적인 접근을 하지 못하므로 이는 request handler에서 수집해야 한다. 다음 예시를 살펴보자.

{% highlight java %}
httpServer.requestHandler(new Handler<HttpServerRequest>() {
    @Override
    public void handle(HttpServerRequest request) {
        System.out.println("incoming request!");

        Buffer fullRequestBody = Buffer.buffer();
        if (request.method() == HttpMethod.POST) {
            request.handler(new Handler<Buffer>() {
                @Override
                public void handle(Buffer buffer) {
                    fullRequestBody.appendBuffer(buffer);
                }
            });

            request.endHandler(new Handler<Buffer>() {
                @Override
                public void handle(Buffer buffer) {
                    // here you can access the fullRequestBody Buffer instance
                }
            });
        }
    }
})
{% endhighlight %}

### Sending Back an HTTP Response
당연하게도 들어오는 HTTP request에 대한 HTTP response를 돌려보낼 수도 있다. 그러기 위해서는 request 객체로부터 HttpServerResponse 인스턴스를 얻어야 한다.
다음과 같이 할 수 있다.

{% highlight java %}
HttpServerResponse response = request.response();
{% endhighlight %}

HttpServerResponse를 얻은 후, HTTP 응답 status code와 헤더를 다음과 같이 설정할 수 있다.

{% highlight java %}
response.setStatusCode(200);
response.headers()
    .add("Content-Length", String.valueOf(57))
    .add("Content-Type", "text/html")
;
{% endhighlight %}

그리고나서 `write()` 메소드를 통해 response body를 적을 수 있다.

{% highlight java %}
response.write("Vert.x is alive!");
response.end();
{% endhighlight %}

이 떄, `write()` 메소드를 여러번 호출해서 response body에 데이터를 더 추가할 수 있다. 또, `write()` 메소드에 Vert.x Buffer 인스턴스를 매개변수로 넘겨 Buffer에 있는 데이터를 HTTP response에 적을 수도 있다.

`write()` 메소드는 비동기적이고 적고자 하는 스트링이나 버퍼를 queue up한 후 바로 리턴한다.

HTTP response body를 다 적은 후에는 HTTP response를 위와 같이 end해줘야 한다. 혹은 위 과정을 하나로 합쳐서,
{% highlight java %}
response.end("Vert.x is alive!");   // end()는 스트링이나 Buffer를 매개변수로 받을 수 있다.
{% endhighlight %}
로 적을 수도 있다.

### Closing the HTTP Server
HTTP 서버를 닫기 위해서는 간단하게 `close()` 메소드를 호출하면 된다.

{% highlight java %}
httpServer.close();
{% endhighlight %}

`close()` 메소드는 비동기적으로 실행하여 해당 메소드가 리턴하는 시점에 HTTP 서버가 완전히 닫히지 않았을 수도 있다. 따라서 close handler를 `close()` 메소드의 매개변수로 전달하여 HTTP 서버가 완전히 닫혔을 때 notify받을 수 있다.

## 출처
http://tutorials.jenkov.com/vert.x/http-server.html