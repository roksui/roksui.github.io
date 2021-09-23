---
title: Vert.x Basics(3) - Buffers
---

Vert.x에서 실행되는 Verticle들은 때때로 데이터 블록을 처리해야 한다. 예를 들어, HTTP request를 받으면 그 안에 있는 데이터를 처리해야 할 수도 있고, 디스크로부터 로딩하는 데이터, 또는 HTTP request에 응답을 하기 위해 생성된 데이터 등등이 있을 것이다. Vert.x는 `Buffer` 인터페이스를 제공하여 이러한 데이터 블록 처리를 돕는다.

`Buffer`는 binary data를 저장할 수 있다. 따라서 Buffer는 byte array와 비슷한데, Buffer는 데이터를 저장함과 동시에 동적으로 용량을 늘릴 수 있다.

## Creating a Buffer
Buffer를 생성하기 위해서는 `Buffer` 인터페이스에 있는 static 메소드인 `buffer()`를 호출한다.

{% highlight java %}
Buffer buffer = Buffer.buffer();
{% endhighlight %}

초기 데이터와 함께 Buffer를 생성할 수도 있다. 예를 들어, byte arry로 초기화한다면 다음과 같이 할 수 있다.

{% highlight java %}
byte[] initialData = new byte[]{1, 2, 3};
Buffer buffer2 = Buffer.buffer(initialData);
{% endhighlight %}

## Writing to a Buffer
Buffer의 특정 포지션에 데이터를 저장하기 위해 `set...()` 메소드를 사용할 수 있다.

{% highlight java %}
Buffer buffer = Buffer.buffer();

System.out.println("buffer.length() = " + buffer.length());

buffer.setByte(0, (byte)127);
buffer.setShort(2, (short)127);
buffer.setInt(4, 127);
buffer.setLong(8, 127);
buffer.setFloat(16, 127.0F);
buffer.setDouble(20, 127.0D);

System.out.println("buffer.length() = " + buffer.length());
{% endhighlight %}

Buffer의 길이는 0으로 시작해, 데이터가 추가될 수록 동적으로 늘어난다. 위 코드를 실행하면 최종 Buffer 길이는 28이 된다.

특정 포지션을 지정하지 않고 `append...()`메소드를 써서 데이터를 추가할 수도 있다. (항상 Buffer의 마지막 부분에 연결해서 추가함)

{% highlight java %}
Buffer buffer = Buffer.buffer();

System.out.println("buffer.length() = " + buffer.length());

buffer.appendByte  ((byte)  127);
buffer.appendShort ((short) 127);
buffer.appendInt   (        127);
buffer.appendLong  (        127);
buffer.appendFloat (     127.0F);
buffer.appendDouble(     127.0D);

System.out.println("buffer.length() = " + buffer.length());
{% endhighlight %}

단, `set...()`에서는 첫번째 추가된 byte다음에 index 2에 short가 추가되었지만 여기서는 index 1에 short가 추가됨으로, 최종 Buffer 길이는 27이다.

## Reading From a Buffer
Buffer에 저장된 데이터를 `get...()`메소드를 통해 읽을 수 있다.

{% highlight java %}
byte aByte = buffer.getByte(0);
byte aShort = buffer.getShort(2);
int anInt = buffer.getInt(4);
long aLong = buffer.getLong(8);
float aFloat = buffer.getFloat(16);
double aDouble = buffer.getDouble(20);
{% endhighlight %}

## 출처
http://tutorials.jenkov.com/vert.x/buffers.html