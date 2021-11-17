---
title: [Python] Quitting Gracefully
---

## 개요
Python으로 소켓 통신을 구현하며 프로그램이 종료될 때, graceful한 방식으로 종료한다고 얘기를 많이 한다. 그런데 처음 이 표현을 봤을 때, 의미가 잘 와닿지 않았다.

직역을 하자면 '우아하게 종료'? 라는 의미인데, 프로그래밍 맥락에서 쓰이는 의미가 있을 것 같아서 더 찾아봤다.

Wikipedia에 의하면 *graceful exit*혹은 *graceful handling*은 하나의 프로그래밍 이디엄으로써, 프로그램이 치명적인 에러를 감지하여 통제된 방식으로 문제 없이 종료되는 것을 말한다. 하지만 많은 한국 번역본에서는 우아한 종료라고 명시되어 그 의미가 불분명한 경우가 많은 것 같다. Graceful의 또 다른 뜻을 보면 "(유예를 두는) 깔끔한/부드러운"의 의미를 가진다. 따라서 graceful exit은 "유예를 두어 깔끔하게 정상 종료를 한다"라고 이해하면 될 것 같다. 실제로 TCP 패킷 전송 이론에서 시작된 개념이고, graceful하게 exit하는 것은 패킷 전송에 있어서 유예 기간, 일종의 타임아웃을 두어 앞서 전송된 패킷들이 모두 정상적으로 전송된 뒤에 깔끔하게 끊는 것을 말한다.

Graceful exit을 할 경우 대체로 프로그램은 에러 메시지를 로그로 출력한다.

Python에서는 signal 모듈을 써서 다음과 같은 handler를 만들 수 있다.

Class based로 만들 수도 있지만 Top-level 메소드로도 만들 수 있다.

```python
import signal

daemon_quit = False

def quit_gracefully(signum, frame):
    global daemon_quit
    daemon_quit = True

# Register the signals
signal.signal(signal.SIGINT, quit_gracefully)
signal.signal(singal.SIGTERM, quit_gracefully)

while 1:
    # do stuff
    pass
```

여기서 system signals를 사용하여 프로그램이 종료되기 전에 유용한 정보나 로그를 얻을 수 있고, 또 리소스를 clean up할 수도 있다.

위 코드에서 `SIGINT`는 프로세스를 즉시 interrupt하는 시그널이다. 이 시그널은 유저가 ctrl+c를 누르면 호출되어 graceful exit을 하게된다.
`SIGTERM`은 프로세스를 즉시 terminate시킨다. 이 또한 프로그램이 graceful exit을 하게하고, 다른점은 쉘 커맨드 `kill`로 호출된다는 점이다.

## 출처
Graceful Exit, Wikipedia
https://sunyzero.tistory.com/269