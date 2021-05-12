---
layout: post
title:  "Java Agents and Instrumentation"
date:   2021-05-12 10:10:56 +0900
categories: jekyll update
---

## Instrumentation이란?

소프트웨어 맥락에서 instrumentation이란, 존재하는 어플리케이션에 코드를 삽입하여 변화를 주는 기술을 말한다. 이는 수동/자동으로 컴파일/런타임 시에 가능하다.
즉, 소스코드를 변경하지 않고도 어플리케이션의 코드를 수정하여 동작방식을 바꿀 수 있다. 특히, Java에서의 instrumentation을 이를 이해하기 위해서는 Agent에 대한 개념이 필요하다.

## Java Agent란?

Java Agent는 Java Instrumentation API의 일부분이다.
가장 low level에서 동작하는 클래스로써 Java Instrumentation API를 사용하여 JVM 상에서 실행되는 어플리케이션의 bytecode를 수정하게 된다.

JVM이 지원하는 Java 프로세스에 대한 두 개의 entry point (premain, agentmain) 메서드가 존재하는데, 이를 통해 Java agent는 별도의 jar파일로 존재하면서 메인 어플리케이션 프로세스에서 실행될 수 있다.

가장 단순한 Java agent는 메인 어플리케이션이 실행되기 전에 실행되는 것이다. (eg. dynamic setup) 하지만 현실적으로 보면 Java agent는 entry point method에 Instrumentation형의 두번째 매개변수를 더함으로써 유용해진다.

컴파일된 Java 클래스는 .class 파일로 저장되고 이는 byte array 형태로 Java agent에게 전달된다.
Agent는 Instrumentation API에 한 개 이상의 ClassFileTransformer를 등록함으로써 현재 JVM 프로세스에서 로딩되는 class에 대한 notification을 받는다.

Code Snippet 1:

{% highlight java %}
public class ClassLoadingAgent {
   public static void premain(String argument, Instrumentation instrumentation) {
      instrumentation.addTransformer(new ClassFileTransformer() {
         @Override
         public byte[] transform(Module module,
                                 ClassLoader loader,
                                 String name,
                                 Class<?> typeIfLoaded,
                                 ProtectionDomain domain,
                                 byte[] buffer) {
            System.out.println("Class was loaded: " + name);
            return null;               
         }
      });
   }
}
{% endhighlight %}

위 예시를 보면 agent는 어떤 클래스가 로딩되었는지를 출력하고 null을 return함으로써 transformation 프로세스를 중단시킨다. 그러나 buffer 바이트 배열에 의해 주어진 byte array를 변환시킴으로써 로딩된 그 어떠한 클래스도 transform시킬 수 있다.

### 그럼 컴파일된 Java class를 어떻게 transform 시킬까?

다행스럽게도 Java Virtual Machine Specification (JVMS)를 보면 class 파일을 나타내는 모든 byte에 대한 의미가 명시되어 있다. 따라서 메서드의 동작방식을 바꾸기 위해서는 Java byte code를 메서드 코드에 삽입하게 된다. 이러한 작업은 일반적으로 수동으로 하지 않고 bytecode processor (eg. ASM library)를 사용하여 수행된다. 이 프로세서는 class 파일을 컴포넌트별로 분해하여 field, method, annotation을 분리해서 볼 수 있게 해준다.

ASM을 기반으로 한 라이브러리 중에서도 Byte Buddy는 bytecode transformation을 기존의 Java 개념과 연결시켜 agent 개발을 용이하게 해준다.
Byte Buddy의 AgentBuilder API는 ClassFileTransformer를 암묵적으로 생성하고 등록한다. 직접적으로 ClassFileTransformer를 등록하는 대신, Byte Buddy는 ElementMatcher를 명시하여 대상의 타입을 식별하고, 각각의 매칭된 타입에 대해 한 개 이상의 transformation이 명시된다. 그 후, Byte Buddy는 transformer를 instrumenttation API에 적용시킨다.

Code Snippet 2:

{% highlight java %}
public class ByteBuddySampleAgent {
   public static void premain(String argument, Instrumentation instrumentation) {
      new AgentBuilder.Default()
      .type(ElementMatchers.any())
      .transform((DynamicType.Builder<?> builder,
                  TypeDescription type,
                  ClassLoader loader,
                  JavaModule module) -> {
         System.out.println("Class was loaded: " + name);
         return builder;
      }).installOn(instrumentation);
   }
}
{% endhighlight %}

Code Snippet 1을 Byte Buddy의 API로 변환한 코드가 Code Snippet 2이다.

## Dynamic Agent Attachment

premain과는 달리 메인 Java 클래스가 실행되는 도중에 agent가 실행될 수 있는데, 이는 agent의 agentmain 메서드에 의해 가능하다. JDK에 있는 tooling API를 사용하여 agent jar 파일을 JVM에 attach할 수 있다. 코드는 다음과 같다.

Code Snippet 3:

{% highlight java %}
VirtualMachine vm = VirtualMachine.attach(processId);
try {
   vm.loadAgent("/location/of/agent.jar");
} finally {
   vm.detach();
}
{% endhighlight %}

위 API가 호출되면 JVM은 주어진 processId를 가진 프로세스를 찾아서 agent의 agentmain 메서드를 실행한다. 이때, agent는 이미 로딩된 class들에 대해 retransform을 수행할 수 있는데 이는 이전의 ClassFileTransformer을 등록할 때 boolean 매개변수를 하나 더 추가해서 할 수 있다. 즉, 이 boolean 매개변수는 retransformation의 시도가 있을 경우 notification을 줄지의 여부를 알려준다. agentmain 메서드는 다음과 같다.

Code Snippet 4:

{% highlight java %}
package sample;
public class ClassReloadingAgent {
  public static void agentmain(String argument, 
                               Instrumentation instrumentation) {
    instrumentation.addTransformer(new ClassFileTransformer() {
      @Override
       public byte[] transform(Module module, 
                               ClassLoader loader, 
                               String name, 
                               Class<?> typeIfLoaded, 
                               ProtectionDomain domain, 
                               byte[] buffer) {
          if (typeIfLoaded == null) {
           System.out.println("Class was loaded: " + name);
         } else {
           System.out.println("Class was re-loaded: " + name);
         }
         return null;
       }
    }, true);
    instrumentation.retransformClasses(
        instrumentation.getAllLoadedClasses());
  }
}
{% endhighlight %}


출처: https://www.javaadvent.com/2019/12/a-beginners-guide-to-java-agents.html