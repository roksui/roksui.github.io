---
title: JVM Overview with HotSpot JVM
categories: 
  - Java Basics
---

## 개요
Java는 1995년에 출시된 이후부터 계속해서 많은 발전을 해왔다. JVM 또한 많은 발전을 해왔다. Java의 초창기 때 많은 애플리케이션들에게 개발 생산성 향상 및 메모리 관리의 장점을 가져다 주었지만 성능은 큰 걸림돌이었다. 따라서 성능 관련 요구사항을 충족시키기 위해, JIT 컴파일러, garbage collector, JVM runtime environment 개선을 진행해왔다. 현대 JVM의 가장 큰 challenge 중 하나는 많은 JVM 사용자들이 JVM을 블랙박스로 여기기 때문에 Java 애플리케이션의 성능 이나 확장성을 향상시키는 것이 어려워지는 것이다. 따라서 현대 JVM에 대한 기본 지식을 가지는 것은 Java 애플리케이션 성능을 향상시키는 능력을 갖기 위한 필수 요소라고 볼 수 있다.

## HotSpot
Java Virtual Machine(JVM)은 실제 머신이 하는 것을 시뮬레이션하는 소프트웨어 머신으로서 Java Virtual Machine Specification에 정의되어 있다. 일종의 추상적인, 상상속의 컴퓨터의 개념이 JVM이라면 그것을 실제로 구현한 예시 중의 하나가 HotSpot이다. HotSpot JVM은 원래 Sun에 의해 개발되어 현재 Oracle이 소유하고 있다. 이 외에도 JRockit, IBM J9 등과 같은 JVM 구현체들이 있다.

**NOTE**: HotSpot은 OpenJDK 프로젝트의 일부분으로써 보관, 유지, 개발되고 있다.

HotSpot VM은 크게 새 개의 요소들로 구성되어 있다: VM Runtime, JIT compiler, memory manager

### High Level Architecture
HotSpot VM은 강력한 기능 및 역량을 지원하는 구조를 갖고 있다. 이러한 구조는 높은 성능과 확장성을 실현할 수 있게 해준다.<br>
예를 들어 HotSpot VM JIT 컴파일러는 동적 최적화를 수행한다. 즉, Java 애플리케이션이 실행 중일 때 최적화 결정을 내려 기반 시스템 구조를 대상으로 성능이 높은 네이티브 기계어 명령을 생성한다.

High level의 HotSpot VM 구조는 다음 그림과 같다.

![HotSpot VM High Level Architecture](/assets/img/hotspot_architecture_high_level.png)

그림과 같이, JIT 컴파일러는 클라이언트, 혹은 서버로 추가될 수 있고, garbage collector는 Serial GC, Throughput, Concurrent, 혹은 G1으로 존재한다. HotSpot VM Runtime은 JIT 컴파일러와 garbage collector에게 services와 common API를 제공한다. 또, VM 자체에게 launcher, thread management, Java Native Interface 등등의 기본적인 기능을 제공해준다.

초기에 출시된 HotSpot VM은 32-bit JVM으로 제한되었기 떄문에 메모리 주소가 4GB로 제한되었었다. 심지어 32-bit HotSpot VM에서 가용한 실제 Java heap 공간은 기반 OS에 따라 또 더 제한되었다. 예를 들어 Microsoft Windows OS에서는 HotSpot VM에서 가용한 최대 Java heap 공간은 1.5GB이다. 최근의 리눅스 커널은 2.5-3.0GB, 옛날 리눅스 커널은 2GB 정도가 된다. 실제 최대 값은 주어진 Java 애플리케이션과 JVM 버전이 컨슘하는 메모리 주소 공간에 따라서도 달라진다.

더욱 큰 메모리를 가진 서버 시스템들이 나오기 시작하면서 64-bit HotSpot VM이 소개되었다. 64-bit HotSpot JVM은 증가된 Java heap 공간 사용을 통해 이러한 시스템들이 추가적인 메모리를 활용할 수 있도록 해주었다. 64-bit 주소를 사용하는 것이 매우 유용한 애플리케이션들도 있었지만, 64-bit VM을 사용하여 성능 저하가 있기도 하다. *Ordinary object pointers (oop)*라고 불리는 HotSpot VM이 내부적으로 Java 객체를 나타내는 방식의 사이즈가 커지면서 발생하는 성능 저하이다. Width가 32 bits에서 64 bits로 늘어나면서 CPU cache line에서 가용한 oop의 개수가 줄고, 따라서 CPU cache 효율성을 떨어트린다.

Java 6 HotSpot VM 이후 부터는 compressed oop라는 기능이 추가되어 (`-XX:+UseCompressedOops` VM command line option) 64-bit Java heap을 사용함과 동시에 32-bit JVM 성능을 가져올 수 있다. 실제로 몇몇 Java 애플리케이션은 32-bit JVM을 사용하는 것보다 compressed oops를 적용한 64-bit HotSpot VM을 사용하는 것이 더 좋은 성능을 보여줬다. 이러한 compressed oop를 통한 성능 개선은 alignment와 offset에 의존하여 64-bit 포인터를 32 bits에 넣음으로써 가능하다. 다르게 말해, full width의 64-bit 포인터를 사용하는 것보다, 더 작고 공간 효율적인 압축된 포인터를 사용함으로써 CPU cache 활용도를 높였다는 말이다. 개선된 CPU cache 활용법을 적용한 애플리케이션은 더 빠르게 동작한다.

추가로, Intel이나 AMD x64와 같은 플랫폼에서는 64-bit JVm은 추가적인 CPU 레지스터를 사용할 수 있어서 애플리케이션 성능이 향상될 수 있다. 추가 레지스터가 가용하다면 레지스터 스필링(register spilling)을 막아줄 수 있다. 레지스터 스필링은 CPU의 레지스터 수 보다 애플리케이션에 live state(i.e. 변수)가 더 많을 경우에 발생한다. 레지스터 스필링이 발생하면 몇몇의 live state는 CPU 레지스터에서 메모리로 'spilled' 되어야 한다. 따라서 레지스터 스필링을 피하면 애플리케이션이 더 빠르게 동작할 수 있는 것이다.

TBD...

## 출처
Java Performance, Charlie Hunt, Binu John, 2012. 