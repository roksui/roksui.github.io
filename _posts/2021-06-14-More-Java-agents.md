---
title: "Java Agents (ASM에서의 관점)"
---

ASM 라이브러리에 있는 ClassReader는 byte 배열로 주어지는 컴파일된 자바 클래스를 파싱하고, 그것의 accept 메소드의 매개변수로 전달되는 ClassVisitor의 메소드들을 호출한다.

참고: https://www.proquest.com/openview/ffaee555994c5e1e23cb257bb0976260/1?pq-origsite=gscholar&cbl=18750&diss=y