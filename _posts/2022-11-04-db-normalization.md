---
title: "데이터베이스 정규화"
---

## What is it?

데이터베이스 정규화(Database Normalization)는 기존의 데이터베이스의 데이터 중복성(redundancy)을 제거함으로써 데이터 보관의 효율성(efficiency), 무결성(integrity), 그리고 확장성(scalability)를 개선시키는 프로세스/기법이다.

관계형 모델(Relational model)에서, 어떠한 데이터베이스가 얼마나 효율적인지 수치화할 수 있는 방법으로 *normal form (NF)*의 카테고리를 고려할 수 있다. 대체로, 정규화는 기존의 테이블을 여러 개로 쪼개는 과정을 거쳐, 특정한 정규형(normal form)으로 나타낼 수 있다.

### History

Edgar F. Codd가 그의 *A Relational Model of Data for Large Shared Data Banks* 논문에서 처음으로 정규화 프로세스를 제안하였으며, 이것이 곧 제1 정규형(1st Normal Form1, NF)이다. Codd는 1NF, 2NF, 3NF 및 BCNF(Boyce-Codd Normal Form) 정규형을 확립하였다. 더 높은 정규형, 즉 정규화의 정도가 존재하지만, 대부분의 실용적인 응용 분야에서는 3NF에서 정규화로 충분하다고 여겨진다.

## Case Study

정규화의 과정을 하나의 예시로 알아보자.

|Title|Author|ISBN|Subject|Pages|Publisher|
|---|---|---|---|---|---|
|Database System Concepts|Abraham Silberschatz|0072958862|MySQL, Computers|1160|McGraw-Hill|
|Operating System Concepts|Abraham Silberschatz, Henry F. Korth|0471684665|Computers|932|McGraw-Hill|

이 테이블에는 몇 가지 문제점들이 있다.

1. 저장 방법이 효율적이지 않다.
2. 이 디자인은 데이터 무결성을 보장하지 않는다.
3. 확장성이 좋지 않다.

이 테이블은 정규화되지 않은 형태, 즉 Unnormalized form (UNF)이다. 한 개 이상의 반복 그룹(repeating group)을 가지는 테이블을 UNF라고 한다. 여기서 반복 그룹이란, 테이블의 열에 해당되는 속성(attribute)이 여러 개의 값을 가지는 것을 말한다.

이 예시에서는, Author와 Subject 속성이 한 개 보다 많은 정보를 담고 있기 때문에 반복 그룹이다.

UNF를 1NF로 정규화해보자.

### 1NF

|Title|Author|ISBN|Subject|Pages|Publisher|
|---|---|---|---|---|---|
|Database System Concepts|Abraham Silberschatz|0072958862|MySQL|1160|McGraw-Hill|
|Database System Concepts|Abraham Silberschatz|0072958862|Computers|1160|McGraw-Hill|
|Operating System Concepts|Abraham Silberschatz|0471684665|Computers|932|McGraw-Hill|
|Operating System Concepts|Henry F. Korth|0471684665|Computers|932|McGraw-Hill|

즉, 반복 그룹을 제거하여 각 행과 열의 교집합(intersection)이 오로지 한 개의 값을 갖는 관계가 1NF이다.

// TBD: UNF -> 1NF...
