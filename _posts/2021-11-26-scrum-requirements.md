---
title: "Scrum - Requirements (User Stories)"
date: 2021-11-26 04:08:20
---

## 개요
소프트웨어 개발에 있어서 제품 기능(product features)은 결정적인 역할을 한다. 이는 유저가 최종 제품에서 궁극적으로 사용하길 원하는 기능을 말한다. 일반적인 용어로 "요구사항"이라고 알려져있다.

소프트웨어 개발 프로젝트의 성공은 유저의 요구사항을 정확하게 그리고 올바르게 이해하여 최종 제품에 구현하는 것에 달려있다. 따라서 제품 기능의 요구사항들은 개발 프로젝트 팀 또한 잘 알고있어야 한다. 애자일 프로젝트에서 가장 일반적으로 요구사항을 표현하는 방법은 **유저 스토리 (User Stories)**를 통해서이다.

**유저 스토리**는 시스템이 사용자를 위해 할 수 있는 것보다, 사용자의 관점에서 그 사람이 어떤 것을 원하는지로부터 나온다. 유저 스토리는 대체로 새로운 기능을 원하는 시스템 유저 및 고객의 관점에서 나오는 해당 기능에 대한 간결하고 간단한 설명이다.

### 유저 스토리 형식
유저 스토리는 전형적으로 간단한 템플릿을 따른다.
As a <*Type of User*>,<br>
I want <*To Perform Some Task*>,<br>
So that <*I can achieve some goal/benefit/value*>.

예시.<br>
은행 고객이 ATM에서 돈을 인출하는 시나리오에 대한 유저 스토리를 구성하고자 한다.
**As a** Customer,<br>
**I want** to withdraw cash from an ATM,<br>
**So that** I don't have to wait in the line at the Bank.

## 유저스토리 관리 및 작성
### 유저 스토리 관리
유저 스토리는 Product Owner에 의해 제품 백로그에 관리된다. 유저 스토리들은 우선순위에 따라 정렬된다. 가장 높은 우선순위를 가진 스토리들은 세분화시키고 가장 낮은 우선순위를 가진 스토리들은 디테일 수준이 좀 더 낮게 유지한다.

각 스프린트에 대해 가장 우선순위가 높고, 따라서 가장 세분화된 유저 스토리들은 스프린트 백로그로 옮긴다. 유저 스토리가 제품 백로그로 추가될 때, 그것의 우선순위가 가장 먼저 결정되고 그 순위에 맞게 위치한다. 유저 스토리들은 언제든지 우선순위가 다시 매겨질 수 있다. 그리고 필요 시, 유저 스토리를 제거할 수도 있다.

### 유저 스토리 작성
Product Owner가 제품 백로그, 그리고 유저 스토리들을 책임지지만, 그렇다고 해서 product owner만 유저 스토리를 작성하는 것은 아니다. 스크럼 팀원 그 누구나 유저 스토리를 작성할 수 있고, 프로젝트가 진행되면서 요구사항이 다듬어지고 새로운 기능이 추가될 때마다 계속해서 작성할 수 있다.

## 유저 스토리 Acceptance 평가 기준
각 유저 스토리는 평가 기준(Acceptance Critera)을 가지고 있다. 평가 기준을 통해 유저 스토리가 올바르게 구현되었는지 확인할 수 있고, 이 평가 기준을 기반으로 하는 Acceptance Test를 통과해야 한다.

위 시나리오의 예시로, "은행 고객이 ATM에서 현금을 인출한다"에 대한 acceptance 평가 기준은 다음과 같다.
- 고객이 현금을 인출하기 전에 로그인 되어있어야 한다.
- 시스템은 요청 금액이 잔액을 초과하는지 확인한다.
- 만약 그렇다면 시스템은 잔액을 표시하고 유저에게 새로운 금액을 입력하라고 요구한다.
- 만약 잔액보다 적은 금액이 입력되면 출금이 되고 새로운 잔액이 표시된다.

## 유저 스토리 설계
1. User Interfaces (UI)

**Low Fidelity (Lo Fi) UI**: Lo Fi UI는 단순하고 덜 기술적이다. 시작하기 위해서는 펜과 종이만 있으면 된다. 이것의 목표는 우리의 아이디어를 테스트 가능한 아티팩트로 바꾸는 것이다. 테스트 가능한 아티팩트를 사용하여 초기 단계에 피드백을 수집 및 분석할 수 있다.

위의 현금 인출 예시에 대한 Lo Fi UI 예시는 다음과 같다.

![Lo Fi UI Example](/assets/img/lofi_ui_example.png)

[ATM Process Lo Fi UI 예시](https://vimeo.com/255470270)를 참고해도 좋다.

**High Fidelity (Hi Fi) UI**: Hi Fi UI는 매우 실용적이고 인터렉티브하다. 최종 제품과 매우 유사하고 대부분의 필요한 디자인 에셋 및 컴포넌트들이 개발되고 통합되어 있다. Hi Fi UI는 사용상을 테스트하여 워크 플로우상의 이슈를 식별하기 위해 나중 단계에서 주로 쓰인다.

똑같이 현금 인출 예시에 대한 Hi Fi UI 예시는 다음과 같다.

![Hi Fi UI Example](/assets/img/hifi_ui_example.png)

[ATM Process Hi Fi UI 예시](https://www.youtube.com/watch?v=UVhbjAXPdRs)를 참고해도 좋다.

2. Storyboard (스토리보드)
스토리보드는 high-level 인터랙션 플로우 그림으로써 주로 시간의 흐름을 따르는 그림과 설명 텍스트로 구성되어 있다. 즉, 사람들이 일상 생활에서 소프트웨어를 사용하는 것을 나타내기 위함이다.

현금 인출 예시에 대한 스토리보드의 예시이다.

![Storyboard Example](/assets/img/storyboard_example.png)

## 유저 스토리 관리 툴
### Scrum Task Board
스크럼 보드 (혹은 스크럽 태스크 보드)는 팀이 스프린트 백로그 아이템을 노출시킬 수 있도록 도와주는 툴이다. 이 보드를 통해 많은 물리적 형태(화이트보드, 스티커 등) 및 가상 형태(JIRA Agile, Trello, GitHub 등의 소프트웨어 툴)가 될 수 있는데 어떻게 보이는가와 상관 없이 동일한 기능을 수행한다. 스크럼 보드는 모든 애자일 프로젝트의 초점이고, 스탠드업 미팅을 하기 좋은 시작점이다. 이 보드는 팀에 의해 업데이트되고 참고되며, 일일 스크럼(Daily Scrum) 마다 아이템들을 봄으로써 팀은 남은 업무와 우선순위에 집중할 수 있다.

스크럼 보드의 예시는 다음과 같다.

![Scrum Board Example](/assets/img/scrumboard_example.png)

다음 Exercise를 진행해보자.

1. 위에서 제공된 유저 스토리 형식을 사용하여 ATM과 고객의 인터렉션에 대한 5개의 추가적인 유저 스토리 및 acceptance 평가 기준을 만들어보자. 로그인, 현금 입금 등의 시나리오를 생각해볼 수 있다. 유저 스토리들을 완료하였으면 SMART 방법을 써서 평가해보자.
  - 유저 스토리가 timeboxed되었는가?
  - "5 whys"를 사용하여 유저 스토리의 가치/니즈를 파악하자.

2. 깃헙이나 Trello에 프로젝트 보드를 만들어서 위에서 만든 ATM 유저 스토리들을 추가하자. 깃헙에서 [프로젝트 보드 생성](https://docs.github.com/en/issues/organizing-your-work-with-project-boards/managing-project-boards/creating-a-project-board) 문서를 참고하자.

## Agile Estimation & Tracking Progress
이터레이션에서 발생하는 다음 스크럼 이벤트들을 알아보자.
- Estimating Size
  * Story Points (https://www.planitpoker.com)
- Tracking Progress
  * Task Board
  * Burndown charts

## Estimating Size
다음 Exercise를 진행해보자.

아래에 있는 유저 스토리들에 대해 몇 번의 이터레이션이 수행될지를 알아내는 작업을 할 것이다.

먼저 각각의 유저 스토리에 대해 story point를 알아내야 한다. 그리고 팀의 velocity가 25라는 가정하에 story point의 개수의 합을 velocity로 나눈다. 이 숫자가 수행해야 할 이터레이션의 개수의 추정값이 된다.

위에서 사용한 ATM 유저 스토리들을 사용하여 각각의 story point의 견적을 낼 것이다.

Story point를 알아내기 위해 온라인 툴인 [PlanIT Poker](https://www.planitpoker.com/)를 사용할 수 있다. 이 툴은 팀이 투표하여 각 유저 스토리의 스토리 포인트 값을 얻을 수 있게 해준다.

스크럼 마스터는 이 온라인 툴을 다음과 같이 사용할 수 있다.
- PlanIT Poker 사이트에 들어간다.
- "Start a quick play"를 클릭한다.
- 툴에서 자신을 식별하기 위해 'Scrum Master'라고 이름을 입력한다.
- 새로운 룸 이름을 생성한다.
- 스크럼 마스터는 제공되는 카드 값을 사용할지 커스터마이징된 값을 사용할지 정한다.
- "Request confirmation when skipping stories?" 체크를 해제한다.
- "Create"를 클릭한다.
- 룸 URL을 공유하여 팀원들을 초대한다.
- 스크럼 마스터는 "+ New" 버튼을 클릭하여 룸에서 유저 스토리를 생성한다.

팀원들은
- 스크럼 마스터가 전달한 URL로 접속한다.
- 각자의 이름을 입력하고 Enter를 클릭한다.
- 모든 팀원들이 룸에 참여할 때까지 기다린다.

모든 팀원이 룸에 들어오면 스크럼 마스터는 유저 스토리 견적을 시작한다.
- 스크럼 마스터가 "Start"를 누른다.
- 스크럼 마스터와 팀원들은 value card를 사용하여 선택된 유저 스토리에 점수를 투표한다.
- 스크럼 마스터를 포함한 모든 인원리 투표를 했으면 스크럼 마스터는 "Finish Voting"을 누른다.
- 스크럼 마스터가 "Next Story"를 누르고 팀은 위와 같은 과정을 반복한다, 모든 유저 스토리들이 투표될 때 까지.
- 투표가 모두 끝나면 스크럼 마스터는 "Completed Stories"를 눌러 유저 포인트을 검토하여 각각의 유저 스토리에 대한 포인트를 적는다.
- 스크럼 마스터는 모든 유저 푕ㄴ트의 합을 계산하고 이 값이 모든 유저 스토리들에 대한 전체 유저 포인트가 된다.
- 그 다음, 팀은 유저 스터리들을 완료하기 위해 필요한 이터레이션 횟수를 알아내야 한다. 각 팀은 투표된 유저 포인트에 따라 다 다른 이터레이션 횟수를 얻는다.

TBD - Tracking Progress