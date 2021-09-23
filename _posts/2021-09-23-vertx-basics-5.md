---
title: Vert.x Basics(5) - HTTP Client
---

Vert.x에는 HTTP client가 포함되어있어 HTTP request를 비동기적으로 만드는 것이 수월하다. Vert.x에서의 모든 IO 액션은 이벤트 루프(event loop)를 block하지 않기 위해 비동기적으로 수행되어야 한다는 점을 기억하자. 이벤트 루프는 Verticle들과 그것들의 핸들러들을 관리하는 스레드(들)이다. Java의 built-in URL 및 URLConnection은 비동기가 아니라서 Vert.x 애플리케이션에서는 이 클래스들을 사용하지 않는 것이 좋다.

### Createing an HTTP Client
다음과 같이 Vert.x HTTP client를 생성할 수 있다.

{% highlight java %}
HttpClient httpClient = vertx.createHttpClient();
{% endhighlight %}

Verticle안에서 HTTP client를 만들면 다음과 같다.

{% highlight java %}
public class VertxHttpClientVerticle extends AbstractVerticle {

    @Override
    public void start() throws Exception {
        HttpClient httpClient  vertx.createHttpClient();
    }
}
{% endhighlight %}

### Sending a GET Request
Vert.x HTTP client를 만든 후, `getNow()` 메소드를 통해 GET request를 날릴 수 있다. 예를 들어 다음과 같다.

{% highlight java %}
httpClient.getNow(80, "tutorials.vertx.com", "/", new Handler<HttpClientResponse>() {

    @Override
    public void handle(HttpClientResponse httpClientResponse) {
        System.out.println("Response received");
    }
});
{% endhighlight %}

`getNow()` 메소드의 첫번째 매개변수는 remote HTTP 서버에 연결하기 위한 TCP 포트이다. 이 매개변수를 안적어도 되며, 그럴 경우 HTTP 프로토콜의 default TCP 포트 번호인 port 80으로 지정된다.

두번째 매개변수는 remote HTTP 서버의 도메인 이름(domain name)이다. "http://"가 앞에 붙지 않는 점을 유의하자. Vert.x HTTP client는 이것이 HTTP request라는 것을 알기 때문에 프로토콜을 명시할 필요가 없다.

세번째 매개변수는 갖고오고자 하는 리소스의 URI이다. 위 예시의 경우 웹사이트의 첫 페이지("/")를 가져온다. 다른 URI 예시로 "/vert.x/index.html"이 될 수도 있다.

네번째 매개변수는 Handler 구현체이다. 이는 HTTP request에 대한 응답이 도착했을 때 호출된다. 이러한 HTTP 응답을 어떻게 처리하는지 좀 더 알아보자.

### Handling the HTTP Response
`getNow()` 메소드로 전달된 Handler 구현체는 HTTP response의 헤더가 도착하면 호출된다. 만약 response body에 접근할 필요가 없다면 이 handler안에서 response를 처리하면 될 것이다.
하지만 response body에 접근해야 한다면, HttpClientResponse의 또 다른 handler를 등록하여 첫 번째 Handler의 `handle()` 메소드의 매개변수로 전달해야 한다. 코드는 다음과 같다.

{% highlight java %}
httpClient.getNow(80, "tutorials.vertx.com", "/", new Handler<HttpClientResponse>() {

    @Override
    public void handle(HttpClientResponse httpClientResponse) {
        httpClientResponse.bodyHandler(new Handler<Buffer>() {
            @Override
            public void handle(Buffer buffer) {
                System.out.println("Response (" + buffer.length() + "): ");
                System.out.println(buffer.getString(0, buffer.length()));
            }
        });
    }
});
{% endhighlight %}

HttpClientResponse의 `bodyHandler()` 메소드로 전달된 Handler 구현체는 HTTP response body 전체가 도착했을 때 호출된다.

body Handler의 매개변수로 전달된 Buffer가 full HTTP response를 담고 있다.

**NOTE**: HttpClient에는 GET, POST, PUT, DELETE 및 HEAD request와 관련된 더 많은 메소드들이 포함되어 있다. 또, HttpClient를 설정하는 방법, HTTP response를 비동기적으로, 점증적으로 처리하기 위한 많은 옵션들이 존재한다. 더 많은 옵션을 보려면 Vert.x JavaDoc을 참조하자.

## 출처
http://tutorials.jenkov.com/vert.x/http-client.html