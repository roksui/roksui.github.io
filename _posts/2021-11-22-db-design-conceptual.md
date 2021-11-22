---
title: "Database Design - Conceptual Design (3)"
date: 2021-11-22 11:20:00
---

설계 단계에서는 이전 단계에서 식별된 요구사항들을 바탕으로 새로운 시스템을 개발된다. 다르게 말해, 데이터 구조에 대한 비즈니스적 이해를 기술적 이해로 변환하는 단계이다.

*무엇*의 질문("무슨 데이터가 필요한가?")에서 *어떻게*의 질문("어떻게 데이터를 구축하고 접근할 것인가?")으로 바뀐다.

설계 단계는 세 개의 단계로 나눌 수 있다.
1. Conceptual Design (개념적 설계)
2. Logical Design (논리적 설계)
3. Physical Design (물리적 설계)

**NOTE** 몇몇 방법론에서는 논리적 설계를 나머지 두개의 단계에 병합시킨다.

## Conceptual Design
개념적 설계 단계의 목적은 이전에 식별된 요구사항을 바탕으로 개념적인 모델을 구상하여, 최종 물리적 모델에 보다 가까이 다가가는 것이다. 자주 쓰이는 개념적 모델은 *entity-relationship* 모델이다.

### Entities and attributes
Entitiy는 데이터베이스의 가장 기본이 되는 것으로써, 사람, 장소, 등의 우리가 관련 정보를 저장하고자 하는 어떠한 개체를 말한다.

예를 들어, 도서관 시스템은 책, 도서관 및 대여자 개체를 갖을 수 있다.

예를 들어, 다음과 같이 대학교와 관련된 개체들 그것들의 속성(attribute)과 함께 나열할 수 있다.
- Course(name, code, course_prerequisites)
- Student(first_name, last_name, address, age)
- Book(title, ISBN, price, quantity_in_stock)

개체의 *인스턴스*란 해당 개체의 한 개의 특정 '예시'라고 볼 수 있다. 예를 들어, 학생이라는 개체의 인스턴스로 James Hugson이 있을 수 있다.

### Relationships
개체들은 서로 특정한 방식으로 관계를 가진다. 예를 대여자는 어떠한 도서관에 소속되어 책을 대여할 수 있을 것이다. 책은 특정한 도서관에서 찾을 수 있을 것이다.

가능한 관계는 여러가지가 있다.

- Mandatory
개체 A의 각 인스턴스에 대해서 한 개 이상의 개체 B 인스턴스가 존재해야만 한다. 이는 반대로 개체 B의 각 인스턴스에 대해서 한 개 이상의 개체 A가 존재해야 한다는 의미는 아니다. Optional이나 mandatory 관계는 각각 일방향으로만 가능하다. 따라서, A-to-B 관계는 optional하고, B-to-A 관계는 mandatory한 것이다.

- Optional
개체 A의 각 인스턴스에 대해 개체 B의 인스턴스가 존재할 수도 있고 존재 안 할 수도 있다.

- One-to-one
개체 A의 각 인스턴스에 대해, 오로지 한 개의 개체 B 인스턴스가 존재하고, 반대로도 동일하다. 만약 이 관계가 optional이라면, 0개 혹은 1개의 인스턴스가 존재할 수 있고, 만약 mandatory라면, 관계를 가지는 개체의 인스턴스가 무조건 한 개가 존재한다.

- One-to-many
개체 A의 각 인스턴스에 대해, 여러 개의 개체 B 인스턴스가 존재할 수 있고, 각 개체 B 인스턴스에 대해 오로지 한 개의 개체 A 인스턴스가 존재한다. One-to-one과 같이, optional 혹은 mandatory 관계가 될 수 있다.

- Many-to-many
개체 A의 각 인스턴스에 대해, 여러 개의 개체 B 인스턴스가 존재할 수 있고, 반대로도 동일하다. Optional 혹은 mandatory 관계가 될 수 있다.

이러한 관계들을 나타내는 방법은 여러가지가 있다. 아래 그림은 *student*와 *course* 개체를 나타낸다. 이 경우, 각 student는 최소 한 개의 course에 등록해야하지만, course는 등록된 student들을 가질 필요는 없다. 따라서 many-to-many이면서 student-to-course 관계는 mandatory이지만 course-to-student는 optional 관계이다.

-ADD IMAGE-

아래 그림은 *invoice_line*과 *product* 개체를 가지고 있다. 각 invoice line은 최소한 한 개(오로지 한개)의 product를 가져야 한다. 그러나 각 product는 여러 개의 invoice line, 혹은 0개의 invoice line에서 보여질 수 있다. invoice_line-to-product 관계는 mandatory이지만 product-to-invoice_line은 optional이다.

-ADD IMAGE-

아래 그림은 *husband*와 *wife* 개체를 가지고 있다. 각각의 husband는 단 한명의 wife를 가질 수 있고, 반대도 동일하다. One-to-one이고 양쪽으로 mandatory한 관계이다.

개체는 자기자신과 관계를 가질 수도 있다. 이러한 개체를 *recursive entity*라고 부른다. 예를 들어 *person* 개체가 있다고 하자. 만약 어떠한 사람들이 형인지에 대한 데이터를 저장하고 싶다면, "is brother to" 관계가 있을 것이다. 이럴 경우, 이 관계는 M:N(many-to-many) 관계이다.

반대로, *weak entity*는 다른 개체 없이는 존재할 수 없는 개체를 말한다. 예를 들어, 학교에서 *scholar* 개체는 weak entity인 *parent* 개체와 관계가 있다. Scholar가 없다면 parent는 시스템에 존재할 수 없다. Weak entity는 대게 부분적으로, 혹은 전체적으로 관련된 개체로부터 primary key를 도출한다. Parent 개체는 scholar 테이블로부터 primary key를 가져와 자신의 primary key의 일부분으로 사용하거나, 혹은 만약 시스템이 한 명의 scholar당 한명의 parent만 저장한다면, scholar primary key 전체를 사용할 수 있다.

*Connectivity*는 관계 분류를 말한다.

*Cardinality*는 관계에 대해서 가능한 특정 인스턴스의 개수를 말한다. *Cardinality limits*는 관련 개체의 가능한 최소/최대 개수를 나타낸다. 위 husband와 wife 예시에서, cardinality limit은 (1, 1)이고, student가 1-8개 사이의 course를 등록할 수 있다면 cardinality limit은 (1, 8)이 된다.

## Developing an entity-relationship diagram(ERD)
Entity-relationship diagram(ERD)는 개체들이 서로 어떠한 관계를 가지는지를 모델링 한다. 추후에 이러한 개체들이 데이터베이스 테이블이 되는 것이다.

ERD를 만들기 위한 첫 단계는 시스템에 존재하는 모든 개체들을 식별하는 것이다. 초기 단계에서는 모든 속성(attribute)를 식별할 필요는 없다. 하지만 특정 개체에 대해 불확실하다면 속성을 생각해서 좀 더 명확하게 설계할 수는 있을 것이다.

개체들을 나열하였다면, 이 개체들 사이에 존재하는 관계들을 식별하고, 관계들의 종류(one-to-one, 등)에 따라 모델링한다. 이를 시각적으로 그릴 수 있는 프로그램을 쓰면 좋다. 나는 현재 draw.io를 사용하고 있다.

초기 ERD가 완성되었다면, 대체로 이쯤에서 이해 관계자들에게 한 번 보여준다. ERD는 기술직이 아닌 사람들도 이해하기 쉽다. 이를 통해 우리도 모르게 생긴 오해나 오류를 발견할 수 있다. 모델링을 하는 이유 중에 하나가 글씨로 전달하는 것보다 모델을 통해 이해하기가 더 쉽고 이해 관계자들도 관심을 더 쉽게 가지기 때문이다. 즉, 오류가 발생하는 확률 또한 줄일 수 있어 미래에 고쳐야 하는 비용을 절감할 수 있다.

ERD가 검증된 후에, 다음 단계로 many-to-many 관계를 두 개의 one-to-many 관계로 교체한다. DBMS는 직접적으로 many-to-many 관계를 구현할 수 없기 때문에 두 개의 작은 관계로 분해한다. 이를 하기 위해서 우리는 *intersection*, 혹은 *composite* 개체 타입을 만들어야 한다. Intersection 개체는 보통 개체들보다 덜 "현실적"이기 떄문에 이름을 짓기가 어려울 수 있다. 이럴 경우, intersect하고자 하는 두 개의 개체들에 따라 이름을 지으면 된다. 예를 들어, student와 course 개체 사이의 many-to-many 관계를 student-course 개체로 intersect할 수 있다.

-ADD IMAGE-

개체가 recursive하더라도 같은 방식을 적용할 수 있다. M:N의 "is brother to" 관계를 갖는 person 개체 또한 intersection 개체가 필요하다. 이 경우, 우리는 *brother*이라는 좋은 intersection 개체 이름을 지을 수 있다. 이 개체는 두 개의 필드를 갖는다 - brother 관계에 대한 각 person에 대한 필드, 즉, 첫 brother의 primary key와 나머지 brother의 primary key 이다.

-ADD IMAGE-

## 출처
Database Design, Mariadb