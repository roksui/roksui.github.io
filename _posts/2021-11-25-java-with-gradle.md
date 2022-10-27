---
title: "Building Java Projects with Gradle"
date: 2021-11-25 08:10:32
---

Gradle을 사용해 Java 프로젝트를 빌드 해보자.

### 디렉토리 구조 생성

*demo*라는 이름으로 디렉토리를 생성한다.

```bash
mkdir demo
cd demo
```

### Java 프로젝트 빌드

프로젝트를 빌드하기 위해서 다음 단계들을 수행한다.

- demo 디렉토리에서 `gradle init`을 한다.
- "application", "java", "Groovy", "JUnit Jupiter"를 선택한다.
- *init* 태스크가 실행 된 후 새로 생성된 파일과 폴더를 확인한다. `build.gradle` 파일이 보일 것이다.
- `gradle build`를 실행한다.

빌드 커맨드가 실행된 후, 디렉토리 구조를 확인한다. build 폴더 아래에 다음과 같은 디렉토리 구조가 보일 것이다.

- classes: 프로젝트의 컴파일 된 .class 파일들
- libs: 어셈블된 프로젝트 라이브러리 (주로 JAR/WAR 파일들)

이제 `gradle run`을 실행해본다.
다음과 같은 결과가 나올 것이다.

```bash
> Task :app:run
Hello World!
```

### Exploring Dependencies

통상, 복잡한 Java 프로젝트는 여러 외부 JAR 파일들에 의존할 것이다. 이러한 JAR 파일들을 프로젝트에서 참조하기 위해서는 build.gradle 파일에 그것들을 정의해줘야 한다.

Gradle에서 JAR 파일과 같은 아티팩트들은 레포지토리에 위치해있다. 레포지토리는 프로젝트의 디펜던시들을 가져오기 위해 사용될 수 있고, 혹은 아티팩트들을 공개하기 위해 사용될 수도 있다.

다음은 공개 Maven 레포지토리를 추가하는 예제이다.

*build.gradle*

```groovy
repositories {
    mavenCentral()
}
```

또, 다음과 같이 디펜던시들을 프로젝트에 추가할 수도 있다.

*build.gradle*

```groovy
dependencies {
    compile group: 'commons-collections', name: 'commons-collections', version: '3.2.2'
    testCompile group: 'org.junit.jupiter', name: 'junit-jupiter-api', version: '5.5.1'
}
```

위 코드의 syntax는 production 클래스들이 commons collections에 대해 compile-time 디펜던시를 가지고, test 클래스들이 JUnit에 대해 compile-time 디펜던시를 갖는다는 것을 선언한다.

**NOTE**: commons-collection은 java collections framework를 확장한 패키지이다.

다음과 같은 자바 파일을 만들어보자.

*HelloWorld.java*

```java
import com.google.common.base.Joiner;
import com.google.common.collect.Lists;
import java.util.List;

public class HelloWorld {

    public static void main(String[] args) {
        List newList = Lists.newArrayList("Hello", "World");
        String result = Joiner.on(",").join(newList);
        System.out.println(result);
    }
}
```

위 코드를 보면 Google Guava의 Joiner 클래스를 사용하여 문자열을 붙이고 있다.

build.gradle 스크립트에서 mainClassName attribute을 "HelloWorld"로 바꿔 gradle이 자신이 실행해야할 메인 클래스가 "HelloWorld"라는 것을 알게 해주어야 한다.

또, 다음 디펜던시가 있어야 한다.

```groovy
implementation 'com.google.guava:guava:29.0-jre'
```

이제 `gradle build`를 실행하면 "Hello,World"가 아웃풋으로 나올 것이다.

## Gradle Wrapper

지금까지 gradle을 어떻게 설치하고 사용하는지 알아봤다.

Gradle wrapper는 프로젝트가 사용하는 gradle 버전 설치를 자동화해준다.

Wrapper는 스크립트이고 선언된 버전의 Gradle를 불러온다. (필요할 경우 미리 다운로드 해준다)

따라서 개발자들은 수동 설치 과정을 따르지 않아도 Gradle 프로젝트를 빠르게 진행할 수 있다.

Gradle wrapper를 사용하는 또 다른 장점은 주어진 Gradle 버전에 대해 프로젝트를 표준화한다는 점이다 - 더 신뢰가고 탄탄한 빌드가 가능해진다. 여러 다른 사용자들에게 다른 버전의 gradle을 줘야 할수도 있는데, 이 때, wrapper 정의만 바꿔주면 된다.

이를 사용하기 위해서는 먼저 필요한 gradle wrapper 파일들을 생성해야한다. 다음 커맨드를 실행하여 생성해주자.

```bash
gradle wrapper
```

자, 이제 사용하기 위해서는 gradle/wrapper/gradle-wrapper.properties 파일을 다음과 같이 수정해야 한다. 예를 들어 협업하는 다른 사람들의 워크스테이션과 호환되는 이전 버전 (4.8)을 사용해야 한다고 가정하자.

```groovy
distributionUrl=https://services.gradle.org/distributions/gradle-4.8-bin.zip
```

이렇게 함으로써 gradle 패키지를 어디로부터 설치할지를 정의한 것이다. 설치 위치는 "distributionPath"와 "distributionBase"에 명시된다.

또는, 위와 같이 줄을 바꿀 필요 없이 다음과 같이 자동으로 바꿀 수도 있다.

```bash
./gradlew wrapper --gradle-version 4.8
```

이제 우리는 이 새롭게 명시된 버전으로 태스크들을 빌드하기 위해 현재 폴더에서 "gradlew"를 사용해야 한다.
