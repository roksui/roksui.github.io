---
title: "Gradle Basics"
date: 2021-11-25 06:10:32
---

## 개요
프로젝트가 커질수록 컴파일, 테스트, jar 파일 생성 등등의 작업을 수행하기 위해서 수많은 커맨드들을 실행해야 할 수 있다. 소스코드 변경이 있을 때 마다 이러한 커맨드들을 수동으로 실행하는 것은 금방 힘들고 에러가 많이 발생하는 작업이 될 것이다. 이렇게 하지 않고 소프트웨어 프로젝트들은 빌드 툴을 사용하여 실행할 커맨드들을 기록하고 자동화한다. 빌드 툴의 예시로 Make, Ant, Ivy, Maven, Rake, Gant, Scons, SBT, Leinengen, Buildr가 있다.

이 글에서는 *Gradle*에 대해 알아볼 것이다. Gradle은 JVM 기반 빌드 툴로써 위에 언급된 다른 빌드 툴들을 개선하여 만들어 졌다. 빌드 파일은 *Groovy*라는 언어로 작성되었다. Groovy는 JVM의 동적 언어로써 Java와 많은 부분이 비슷하지만 몇 가지 중요한 차이점도 있다. 모든 Gradle 빌드 파일은 실행가능한(executable) Groovy 스크립트이다.

## Core Concepts
Gradle에서 쓰이는 핵심 용어가 있다.
- **Project**: 프로젝트란 전형적으로 우리가 빌드하고자 하는 소프트웨어이다.
- **Task**: 태스크란 프로젝트를 빌드하기 위해 필요한 액션이다. 예를 들어 소스코드를 컴파일하는 것, JavaDoc을 생성하는 것, 컴파일된 클래스들을 JAR 파일로 묶는 것 등이 있다.
- **Build Script**: 프로젝트는 전형적으로 태스크들이 정의된 빌드 스크립트를 가지고 있다. 빌드 스크립트는 `gradle` 커맨드에 의해 사용된다 - 그렇게 함으로써 Gradle은 프로젝트를 위해 정의된 태스크들을 알 수 있다.

Gradle 프로젝트는 프로젝트를 빌드하기 위해 실행할 수 있는 한 개 이상의 태스크를 가진다. 통상 빌드 스크립트는 `build.gradle`라고 불리고 빌드하고자 하는 프로젝트의 루트 디렉토리에 위치하고 있다. `gradle` 커맨드가 실행되면 Gradle은 커맨드가 실행되는 디렉토리에서 빌드 스크립트를 찾는다.

Gradle은 다음과 같은 것들을 할 수 있다.
- 컴파일링, jar 파일 생성, 테스트 실행과 같은 흔한 태스크들을 자동화시킨다.
- 플러그인 사용을 통해 여러가지 다른 언어를 지원한다. 예를 들어 Java 플러그인이 있다.
- Incremental 빌드가 가능하다. 즉, 이미 가장 최신화되어 있는 것들을 빌드하지 않는다. 예를 들어 가장 최근 빌드 후에 소스 파일을 변경하지 않았는데 빌드를 수행하면, 컴파일이나 테스트가 실행되지 않는다.
- 디펜던시를 자동으로 다운로드 한다. 즉, 우리의 프로젝트가 의존하는 라이브러리 파일들의 적절한 버전들을 Maven과 Ivy와 같은 글로벌 레포지토리에서 다운로드 해준다.

## Gradle 설치
Gradle은 모든 메이저 운영체제에서 다 실행되고 Java JDK 버전 7 이상을 요구한다.

Gradle Installation: [Gradle Download](https://gradle.org/install/)

위에서 Gradle은 다운로드하여 새 디렉토리 C:\Gradle에 zip을 해제한 *gradle-7.2* 폴더를 옮겨준다.

Gradle을 실행하기 위해서 환경 변수 GRADLE_HOME을 추가하고 이는 언패킹한 파일들을 포인팅해야 한다. (i.e. C:\Gradle\gradle-7.2)<br>
그리고 GRADLE_HOME/bin을 PATH 환경 변수에 추가하면 충분하다. (i.e. C:\Gradle\gradle-7.2\bin)

```
gradle --version
```
을 실행해서 잘 설치되었는지 확인할 수 있다.

## Gradle Commands
커맨드 라인에서 Gradle을 실행하기 위해 빌드 스크립트가 있는 디렉토리에 있어야 한다.

### Run a Task
Gradle 빌드 스크립트는 우리가 실행할 수 있는 한 개 이상의 태스크를 주로 가지고 있다. 태스크를 실행하기 위해서는 `gradle` 커맨드 다음에 태스크 이름을 나열하여 실행할 수 있다.

```
gradle <task>
```

이 커맨드는 커맨드가 실행되는 빌드 스크립트 안에 <task>의 이름을 갖는 태스크를 실행한다.

### Run multiple Tasks
Gradle은 한 개의 커맨드에 여러 개의 태스크 이름을 적어서 그 태스크들을 모두 실행할 수 있다. 태스크들의 이름은 공백으로 분리되어야 한다.

```
gradle clean build
```

이 커맨드를 실행하면 Gradle은 *clean*이라는 이름은 갖는 태스크를 먼저 실행하고 그 다음에 *build*라는 이름은 갖는 태스크를 실행한다. 또, 해당 태스크들이 의존하는 태스크도(만약 존재한다면) 실행한다.

각각의 태스크는 여러번 적혀도 단 한 번만 실행된다. 따라서 다음 명령어를 실행해도 *clean* 태스크는 딱 한 번만 실행될 것이다.

```
gradle clean clean
```

### Task Name Abbreviation
태스크를 실행하기 위해 이름 전체를 적을 필요도 없다. Gradle이 다른 태스크들과 구분할 수 있을 정도가 되면 충분하다. 예를 들어 *build* 태스크를 다음과 같이 실행할 수 있다.

```
gradle b
```

단, 빌드 스크립트에 `b`로 시작하는 다른 태스크가 없다는 가정하에 말이다. 만약 다른 태스크가 `b`로 시작한다면 태스크 이름에 대한 정보를 더 줘야 할 것이다.
i.e.
```
gradle bu
gradle bui
gradle buil
...
```

### Excluding Tasks
`-x` flag를 사용하여 실행으로부터 태스크를 제외시킬 수 있다. 주로 태스크가 의존하는 다른 테스틑 제외시킬 때 유용하게 쓰인다 - 실행시키지 않아도 되는 태스크를 제외함으로써 빌드 과정을 더 빠르게 만든다.

예를 들어, Gradle Java plugin의 *build* 태스크는 *test*와 *testClasses* 태스크들에 의존한다. 만약 방금 전에 테스트를 실행했고 다 패스했다는 것을 안다면 *test*와 *testClasses*를 다시 실행할 필요가 없을 것이다. 그래서 그냥 프로젝트 빌드만 하고자 한다면 다음과 같이 할 수 있다.

```
gradle build -x test
```

위와 같이 하면 *test* 태스크를 실행 과정에서 제외시킬 것이다. *test* 태스크가 *testClasses* 태스크에 의존하고 *test* 태스크가 제외되었기 때문에 *testClasses* 태스크 또한 암묵적으로(implicitly) 제외된다.

### Quiet Mode
Gradle을 "quiet mode"로 실행할 수도 있는데, 이는 주로 실행하면 커맨드 라인에 출력하는 많은 양의 status 메시지를 생략해준다. 다음과 같이 할 수 있다.

```
gradle -q
gradle -q <task>
```

### Listing Tasks in Build Script
빌드 스크립트의 모든 태스크들을 다음과 같이 나열할 수 있다.

```
gradle tasks
```

위 커맨드는 커맨드를 실행하는 디렉토리에 있는 빌드 스크립트에서 찾은 모든 태스크들을 나열한다.

만약 `--all` flag를 추가하면 각 태스크에 대한 정보를 더 얻을 수 있다.

### Specifying Build Script
디폴트로 Gradle은 `gradle` 커맨드가 실행되는 곳의 디렉토리에 있는 빌드 스크립트를 실행한다. 그러나 다른 빌드 스크립트 또한 빌드할 수 있다.

다음과 같이 `-b` flag를 사용하면 된다.

```
gradle -b subproject-dir/build.gradle build
```

또는, 빌드하고자 하는 프로젝트 디렉토리를 명시해도 된다.

```
gradle -p subproject-dir build
```

Gradle은 subproject-dir 디렉토리에 있는 build.gradle 파일을 사용하여 *build* 태스크를 실행한다.

### Listing Subprojects
Gradle은 프로젝트의 모든 서브프로젝트들(프로젝트의 빌드 스크립트에 명시된 대로)을 나열할 수 있다.
이는 *projects* 태스크를 통해 할 수 있다.

```
gradle projects
```

### Gradle Help
`gradle` 커맨드 옵션 리스트를 `-h` flag를 통해 얻을 수 있다.

```
gradle -h
```

### Build Failures
만약 빌드 스크립트의 태스크가 실행 중 실패하면 Gradle은 빌드 전체를 중단한다. 우리의 시간을 아껴주기 위해 이렇게 한다. 대체로 빌드 스크립트의 나중 태스크들은 그 전의 태스크들이 실패하면 그들의 의미를 잃게 된다.

만약, 태스크가 실패해도 빌드를 계속해서 하고 싶다면 `--continue` flag를 사용하면 된다.

```
gradle build --continue
```

단, 태스크가 의존하는 다른 태스크들이 실패하면 Gradle은 그 태스크는 실행하지 않는다. 즉, 태스크 B가 태스크 A에 의존하는데 태스크 A가 실패하면, --continue flag에도 불구하고 태스크 B는 실행되지 않는다.

### Dry Run
"Dry Run"이라는 것은 우리가 실행하라고 하는 태스크들을 Gradle이 실제로 전부 다 실행하지 않는 것을 말한다. 그 대신 Gradle은 만약 Gradle을 일반적으로 실행했을 경우 실행되었을 태스크들에 대한 정보를 출력한다.

```
gradle -m build
```
위와 같이 `-m` flag로 dry run을 하면 아웃풋이 다음과 같이 나올 수 있다.

```
D:\data\projects\gradle-experiments>gradle -m build :compileJava SKIPPED :processResources SKIPPED :classes SKIPPED :jar SKIPPED :assemble SKIPPED :compileTestJava SKIPPED :processTestResources SKIPPED :testClasses SKIPPED :test SKIPPED :check SKIPPED :build SKIPPED BUILD SUCCESSFUL Total time: 14.869 secs D:\data\projects\gradle-experiments>
```

### Verbose Output
Gradle 태스크 실행에 대한 좀 더 verbose(상세)한 아웃풋을 원하면 다음과 같이 쓸 수 있다.

```
gradle <task> --console verbose
```