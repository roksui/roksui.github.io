---
title: A dive into Gradle
---

## History of Gradle
Java 시장에서 Ant와 Maven이 지배적이었다. Ant는 절차적 프로그래밍 아이디어를 기반으로 2000년도에 처음 출시된 빌드 툴이다. 그 후에도 네트워크를 통한 플러그인과 디펜던시 관리를 적용함으로써 개선되었다.

Ant의 주 단점은 다음과 같다:
- 빌드 스크립트가 XML 형식을 사용한다.
- 절차적 프로그래밍에서 hierarchical한 구조는 적합하지 않다.
- XML은 비교적 관리하기 힘들다.

2004년에 Maven이 소개되었다. Ant에서 많은 개선점을 가진 툴이었다. Maven은 구조 변경이 가능하였고 빌드 specification을 쓰기 위해 XML을 사용할 수 있었다. Maven 또한 네트워크를 통해 디펜던시를 다운로드 할 수 있는 방식이었다.

Maven의 주 장점은 라이프 사이클이 있고, 여러 개의 프로젝트의 라이프 사이클을 계속적으로 같이 따르고 있다는 점이다.
하지만 디펜던시 관리와 관련하여 몇 가지 단점이 있다면, 동일한 라이브러리간의 충돌을 handle해주지 않는다. 또, Maven은 ANT에 비해 복잡하고 커스터마이징된 빌드 스크립트를 작성하기 힘들다.

시간이 더 지나, 2012년에 위 두개의 툴의 효율적인 속성을 다 가진 Gradle이 세상에 나왔다.

## Features of Gradle
### Declarative builds and build-by-convention
- Gradle의 빌드 스크립트는 Groovy 언어로 만들어 졌으며 선언형 언어(declarative language) 요소들을 제공한다. 이러한 요소들은 build-by-convention을 지원한다.<br>
여기서 declarative language와 build-by-convention은 중요한 내용이니 짚고 넘어가자.

*declarative*라는 것은 *imperative*와 비교하여 설명하면 좀 더 쉽다.

Declarative Programming에서는 우리가 **무엇을** 하고 싶은지 선언할 수 있게 해준다.
Imperative Programming에서는 우리가 어떠한 것을 **어떻게** 할 지 적어야 한다.

따라서, declarative 언어에서는 모든 태스크를 구현/선언/설정하는 로우 레벨 작업을 하지 않고, high 레벨에서 "선언"을 해주면 되는 것이다. 예를 들어, Gradle에서는 "이거는 Java 프로젝트이다" (`apply plugin: "java"`), "이거는 내 레포지토리들이다" (`repositories { ... }`), "이거는 내 소스들이다" (`sourceSets { ... }`), "이거는 내 디펜던시들이다" (`dependencies { ... }`)라고 선언만 해주면 된다. 이러한 선언을 바탕으로 Gradle은 어떠한 태스크가 필요한지, 그것들을 위한 디펜던시가 무엇인지, 어떻게 설정되어야 하는지를 스스로 알아내준다. 

이와 달리 Make와 같이 imperative한 특성을 가진 빌드 툴을 살펴보자.
간단한 Make rule 예제이다.
```
foo.o : foo.c foo.h
        cc -c -g foo.c
```
이 룰을 보면 C 소스 파일과 헤더 파일로부터 object 파일인 foo.o를 **어떻게** 빌드해야하는지를 설명하고 있다.<br>
즉, 첫번째 줄의 의미는, foo.o 파일은 foo.c와 foo.h에 의존(depend)한다는 것이다. 사실 이 줄은 declarative한 느낌이 있는 것이, Make는 foo.o 파일의 타임스탬프를 확인하여, foo.c와 foo.h보다 오래되었는지 확인하고, 만약 그렇다면 다음 줄을 실행하는 방법을 알고 있다.

두 번째 줄은 imperative하다.<br>
만약 foo.o가 foo.c나 foo.h보다 오래되었다면, 정확하게 어떠한 커맨드를 실행해야하는지(cc 컴파일러)를 명시하고 있다. 또, 해당 Make rule을 작성하는 개발자는 cc 커맨드에 어떠한 flag 값을 주어야 할지도 알고 있어야 한다.

자, 그럼 build-by-convention이라는 것은 간단하다.
디폴트 컨벤션을 따르면 빌드가 훨씬 간단해진다는 아이디어이다.<br>
예를 들어 소스 파일을 src/main/java에 넣고 테스트 파일들을 src/text/java에 넣고 빌드하면 Gradle이 알아서 빌드를 해주고 테스트도 실행해주고 jar 파일도 만들어 준다. 하지만 Ant같은 경우를 보면 어디에 소스가 있는지, 어떠한 태스크가 돌아가는지, 어떻게 묶어야 하는지 등을 다 명시해줘야 한다.

## Gradle - Build Script
Gradle은 **프로젝트**와 **태스크**를 핸들링하기 위해 스크립트 파일을 빌드한다.<br>
모든 Gradle 빌드는 한 개 이상의 프로젝트를 나타낸다.

하나의 프로젝트는 라이브러리 JAR, 혹은 웹 애플리케이션, 아니면 다른 프로젝트들로부터 생성된 JAR들을 모아놓은 ZIP파일을 나타낼 수 있다. 간단히 말해, 하나의 프로젝트는 서로 다른 태스크들로 구성되어 있다.

하나의 태스크는 한 단위의 작업이고 이것이 각각 빌드 된다. 태스크는 예를 들어 클래스를 컴파일 하는 것, JAR를 만드는 것, Javadoc을 만드는 것, 혹은 레포지토리에 아카이브를 퍼블리슁하는 등의 작업이 될 수 있다.

Gradle은 빌드 스크립트를 작성하기 위해 Groovy 언어를 사용한다.

Gradle은 빌드를 작성하기 위해 Domain Specific Language(DSL)를 제공한다. 그리고 이러한 빌드를 좀 더 쉽게 나타내기 위해 Groovy 언어를 사용한다. 모든 Gradle 빌드 스크립트는 UTF-8로 인코딩되고, 오프라인에 저장되며 build.gradle의 이름을 가진다.

다음 예제는 helloworld를 출력하는 스크립트이다.
```
task hello {
    doLast {
        println 'helloworld'
    }
}
```
이 스크립트를 build.gradle 파일에 저장해보자. 이 빌드 스크립트는 hello라는 태스크를 포함하여, 해당 태스크는 helloworld 스트링을 출력한다.

터미널에서 다음과 같은 커맨드를 실행해보자. 위 스크립트를 실행할 것이다.
```
$> gradle -q hello
```
**OUTPUT**
```
helloworld
```

Gradle 태크스는 ANT의 target과 동일하다. doLast문의 shortcut으로 "<<" 심볼을 사용하여 위 hello 태스크를 단순화 할 수 있다.
```
task hello << {
    println 'helloworld'
}
```
Gradle 스크립트는 주로 두 개의 실(real) 객체를 사용한다: Project Object & Script Object.
- Project Object - 각 스크립트는 한 개 이상의 프로젝트를 나타낸다. 실행 도중에 이 스크립트는 Project Object를 설정한다. 특정 메소드를 실행시켜 빌드 스크립트에 속성을 적용할 수 있고, 이는 Project Object로 위임된다.

- Script Object - Gradle은 스크립트 코드를 클래스로 넘기고 이 클래스들은 Script Interface를 구현하고, 실행된다. 이는 즉, 스크립트 인터페이스에 선언된 모든 속성과 메소드들은 스크립트 안에 존재한다는 뜻이다.

빌드 스크립트에 있을 수 있는 기본 프로젝트 속성 중에 다음과 같은 것들이 있다.
Name: project, Type: Project, Default Value: The Project instance<br>
Name: name, Type: String, Default Value: The name of the project directory<br>
Name: path, Type: String, Default Value: The absolute path of the project<br>
Name: description, Type: String, Default Value: A description of the project<br>
Name: projectDir, Type: File, Default Value: The directory containing the build script<br>
Name: buidDir, Type: File, Default Value: projectDir/build<br>
Name: group, Type: Object, Default Value: unspecified<br>
Name: version, Type: Object, Default Value: unspecified<br>
Name: ant, Type: AntBuilder, Default Value: An AntBuilder instance<br>

### Groovy Basics
Gradle 빌드 스크립트는 Groovy API 전체를 사용한다.
간단한 예시로 시작하자.

**Example 1**
다음 build.gradle 파일은 string을 대문자로 변환한다.
```
task upper << {
    String expString = 'HELLO world'
    println "Original: " + expString
    println "Upper case: " + expString.toUpperCase()
}
```
`gradle -q upper`를 실행하면, 다음과 같이 OUTPUT이 출력된다.
```
Original: HELLO world
Upper case: HELLO WORLD
```

**Example 2**
다음 build.gradle 파일은 implicit parameter ($it)를 네 번 출력한다.
```
task count << {
    4.times {
        print "$it "
    }
}
```
`gradle -q count`를 실행하면, `0 1 2 3`이 출력된다.

### Groovy Features
Groovy 언어는 여러가지 feature를 제공하는데 몇몇 중요한 아이들이 있다.

Groovy는 standard Java classes에 더 많은 유용한 메소드들을 추가하는데, 예를 들어 JDK의 Iterable API는 `each()` 메소드를 구현한다.

```
task groovyJDK << {
    String myName = "Marc";
    myName.each() {
        println "${it}"
    };
}
```
`gradle -q groovyJDK`를 실행하면,
```
M
a
r
c
```
가 출력된다.

## Reference

또, 속성에 대한 getter 및 setter 메소드에 바로 접근할 수 있다.
예를 들어 밑에 코드는 buildDir 속성의 getter/setter 메소드의 syntax를 정의한다.

```
// Using a getter method
println project.buildDir
println getProject().getBuildDir()

// Using a setter method
project.buildDir = 'target'
getProject().setBuildDir('target')
```

메소드 호출에 있어서 Groovy는 괄호를 생략할 수 있기 때문에 Gradle scripting에도 적용이 된다.
다음 두 개의 syntax 모두 허용된다.
```
test.systemProperty 'some.prop', 'value'
test.systemProperty('some.prop', 'value)
```

Gradle DSL에서는 메소드의 마지막 매개변수가 closure인 closure를 많이 사용한다. 예를 들어 다음 세 가지 syntax는 모두 `repositories()` 메소드의 마지막 매개변수가 closure가 된다.
```
repositories {
    println "in a closure"
}

repositories() {
    println "in a closure"
}

repositories({ println "in a closure" })
```

추가로, Gradle은 Gradle 스크립트에 여러개의 디폴트 import 패키지(org.gradle packages)를 자동으로 추가해준다.

TBD...