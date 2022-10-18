---
title: "Introduction to Python Bytecode"
---

## 개요

파이썬으로 코딩을 하면 주로 `.py` 확장자의 소스 코드 파일들을 만들고 실행한다. 또한, 바이트코드인 `.pyc` 파일 또한 생성되는 것을 본적이 있을 것이다. (Python3 부터는 이 파일이 `.py` 파일과 같은 디렉토리가 아닌 `__pycache__` 서브디렉토리에 생성되긴 한다)

본 글에서는 바이트코드 파일 안에 어떠한 코드가 있고 Python이 이를 어떻게 실행하는지를 알아볼 것이다.

## How Python works

파이썬은 주로 interpreted 언어(프로그램이 실행되면서 소스 코드가 한줄 한줄 네이티브 CPU 명령어로 변환되는 언어)로 설명된다. 그러나 이는 부분적으로만 맞다. 파이썬은 먼저 가상 머신이 이해할 수 있는 명령어로 소스 코드를 컴파일한다 - 파이썬 Interpreter가 이러한 가상 머신의 구현체이다. 이렇게 컴파일된 것을 '바이트코드'라고 한다. 즉, `.pyc`파일은 우리의 프로그램이 실행되면서 파이썬의 가상 머신이 실행하게 될 바이트코드 명령어들이다.

예를 들어 클래식한 "Hello, World!" 예시를 보자.

```python
def hello():
    print("Hello, World!")
```

이는 다음과 같은 바이트코드로 변환된다 - 인간이 읽을 수 있는 형태로 보면 다음과 같다.

```asm
2        0 LOAD_GLOBAL         0 (print)
         2 LOAD_CONST          1 ('Hello, World!')
         4 CALL_FUNCTION       1
```

위의 `hello()` 함수를 작성하여 CPython 인터프리터를 통해 실행하면 Python이 실행하는 것은 위 바이트코드에 있는 명령어들이 실행되는 것이다.

## Inside the Python virtual machine

CPython은 Python 언어의 구현체이다. 여기서 주의해야 할 것이, Python은 프로그래밍 언어이고, 구현체라함은 컴파일러, 인터프리터 등이 된다.

CPython은 스택 기반의 가상 머신을 사용한다. 즉, 완전히 스택 데이터 구조를 사용하는 것이다. CPython은 세 가지 종류의 스택을 사용한다.

1. **Call Stack** - 실행되는 파이썬 프로그램의 주요 구조가 된다. 콜 스택은 현재 active한 각 함수 호출에 대해 하나의 아이템인 '프레임'을 가진다. 즉, 프로그램의 entry point는 스택의 가장 밑에 있게 된다. 함수 호출이 생길 때 마다, 새로운 프레임이 콜 스택에 들어가고, 함수가 리턴할 때 마다 프레임이 pop된다.

2. 각 프레임 안에는 **evaluation stack**(also called **data stack**)이라는 것이 있다. 이 스택은 파이썬 함수의 실행이 일어나는 공간이다. 파이썬 코드를 실행하는 것이라함은 주로 이 스택에 아이템들을 push하고, 조작하고 다시 pop off하는 것을 말한다.

3. 각 프레임 안에는 **block stack**이라는 것도 있다. 이 스택은 파이썬이 특정 제어 구조들을 추적하기 위해 사용된다 - 루프, try/except 블록, with 블록들에 의해 블록 스택에 아이템들이 push되고, 이러한 블록들이 종료되면 pop된다. 이를 통해 파이썬은 특정 시점에 어떠한 블록이 active한지 알 수 있어, 예를 들어 continue나 break 구문이 올바른 블록에 적용될 수 있다.

대부분의 파이썬 바이트코드 명령어는 현재 콜 스택 프레임의 evaluation stack을 조작한다. 그러나 특정 명령어로 jump하거나 블록 스택을 조작하는 다른 명령어도 있긴 하다.

예를 들어, `my_function(myvariable, 2)` 함수를 호출하는 코드가 있다고 가정하자. 파이썬은 이를 네 개의 바이트코드 명령어들의 나열로 변환한다.

1. `LOAD_NAME` 명령어는 `my_function` 함수 객체를 찾아 evaluation stack의 맨 위로 push한다.
2. 또 다른 `LOAD_NAME` 명령어가 `my_variable` 변수를 찾아 evaluation stack의 맨 위로 push한다.
3. `LOAD_CONST` 명령어가 리터럴 정수 값 `2`를 evaluation stack의 맨 위로 push한다.
4. `CALL_FUNCTION` 명령어

`CALL_FUNCTION` 명령어는 2개의 인수를 가질 것이고, 따라서 파이썬은 스택의 두 개의 positional argument들을 pop한다. 그리고 나서 제일 위에 아이템은 호출해야할 함수가 될것이고, 이 또한 pop된다. (keyword arguments를 포함한 함수에는 `CALL_FUNCTION_KW`가 적용되긴 하지만 비슷한 연산 방식이 사용된다. 또, *나 **로 argument unpacking이 있는 함수 호출에는 `CALL_FUNCTION_EX`가 사용된다.)

이러한 프로세스가 끝나면 파이썬은 콜 스택에 새로운 프레임을 할당하여 함수 호출에 대한 지역 변수를 초기화한 후, 해당 프레임 안에서 `my_function`의 바이트코드를 실행한다. 그 후, 해당 프레임은 콜 스택에서 pop되고 원래의 프레임에서 `my_function`의 리턴 값이 evaluation stack으로 push된다.

## Experimenting with Python bytecode

파이썬 스탠더드 라이브러리에 있는 `dis` 모듈을 사용하여 파이썬 바이트코드를 들여다보고 이해할 수 있다. `dis` 모듈은 파이썬 바이트코드에 대한 'disassembler' 역할을 하여 바이트코드 명령어를 human-readable한 형태로 보여준다.

예를 들어, 위에 있는 `hello()` 함수의 바이트코드 목록을 보려면 다음과 같은 코드를 파이썬 인터프리터로 실행한다.

```python
import dis
dis.dis(hello)
```

`dis.dis()` 함수는 함수, 메소드, 클래스, 모듈, 컴파일된 파이썬 코드 객체, 혹은 소스 코드를 포함하는 스트링 리터럴을 disassemble하여 human-readable 형태를 출력해준다. `dis` 모듈에서 또 유용한 함수는 `distb()` 함수이다. 파이썬 traceback 객체를 넘겨주거나 예외가 발생한 후 호출하면, 예외 발생 시점에 콜 스택의 가장 위에 있는 함수를 disassemble하여 그것의 바이트코드를 출력하고 예외를 발생시킨 명령어에 포인터를 가르키게한다.

또한, 모든 함수에 대해 파이썬이 빌드하는 컴파일된 코드 객체를 보는 것도 코드를 추적하고 이해하는데 많은 도움이 된다 - 함수를 실행하는 것은 결국 이러한 코드 객체의 속성들은 사용하는 것이다. `hello()` 함수를 다음과 같이 분석할 수 있다.

```bash
>>> hello.__code__
<code object hello at 0x104e46930, file "<stdin>", line 1>
>>> hello.__code__.co_consts
(None, 'Hello, World!')
>>> hello.__codoe__.co_varnames
()
>>> hello.__code__.co_names
('print',)
```

코드 객체는 함수의 `__code__` 속성으로 접근할 수 있고 몇 가지 중요한 속성들을 가지고 있다.

- `co_consts`는 함수 바디에서 나오는 리터럴들의 튜플이다.
- `co_varnames`는 함수 바디에서 사용되는 지역 변수 이름의 튜플이다.
- `co_names`는 함수 바디에서 참조되는 비지역(non-local) 이름의 튜플이다.

따라서, 이제 우리는 `hello()` 함수의 바이트코드 리스트를 이해할 수 있다.

1. `LOAD_GLOBAL 0`: 파이썬에게 `co_names`의 0번째 인덱스의 이름으로 참조되는 전역 객체 (`print` 함수)를 찾아 evaluation stack에 push하라고 말한다.

2. `LOAD_CONST 1`: `co_consts`의 1번째 인덱스에 있는 리터럴 값을 push한다. (0번째 인덱스에 있는 값은 `None` 리터럴이다. 이는 파이썬 함수 호출은 explicit한 return 구문이 없으면 implicit하게 `None`을 리턴하기 때문에 `co_consts`에 존재한다.)

3. `CALL_FUNCTION 1`: 파이썬에게 함수를 호출하라고 말한다. 스택에서 한 개의 positional argument를 pop을 해야하므로, 스택의 맨 위에는 호출해야할 함수가 된다.

Raw 바이트코드(non-human-readable 형태)는 코드 객체의 `co_code` 속성을 통해 접근할 수 있다. 함수를 수동으로 disassemble하고자 한다면 `dis.opname`의 리스트를 사용하여 decimal 바이트 값으로 바이트코드 명령어를 찾을 수 있다.

## Why go through this Python bytecode?

이제 파이썬 바이트 코드를 보거나 이해하는 건 알겠는데, 이거를 알면 뭐가 실질적으로 좋을까? 싶기도 하다. 단순히 궁금해서 알아보는 것도 있지만 또 다른 장점이 있긴하다.

1. 파이썬의 실행 모델을 이해하면 우리의 코드의 로직에 대해서 좀 더 깊이 이해할 수 있다. 사람들은 C 언어가 일종의 '휴대용 어셈블러'여서 특정 C 소스 코드가 어떠한 머신 명령어로 변환될지 쉽게 추측할 수 있다고는 한다. 바이트코드를 이해한다면 파이썬으로도 비슷한 능력을 가지게 된다. 우리의 파이썬 소스 코드가 어떤 바이트코드로 바뀔지 예측할 수 있다면, 코드 작성 및 최적화에 대해 더 좋은 의사결정을 내릴 수 있다.

2. 바이트코드를 이해하면 파이썬에 대한 질문들에 답하기 좋다. 예를 들어, 뉴비 파이썬 프로그래머들이 왜 특정 방식이 다른 방식보다 빠른지 궁금해할 때가 많다. 예를 들어 왜 `{}`가 `dict()`보다 빠른지. 파이썬 바이트코드에 접근하여 읽을 수 있다면 이에 대한 답을 찾을 수 있다. `dis.dis("{}")`와 `dis.dis("dict()")`를 비교해보자.

3. 바이트코드를 이해하고 파이썬이 이를 어떻게 실행하는지를 이해하는 것은 파이썬 프로그래머들이 대체로 접하지 않는 종류의 프로그래밍에 대한 분야인 스택지향 프로그래밍(stack-oriented programming)을 엿볼 수 있다. FORTH나 Factor와 같은 스택지향 언어를 사용해봤다면 이미 이러한 프로그래밍 모델에 대해 알수도 있다.

## 출처

<https://opensource.com/article/18/4/introduction-python-bytecode>
