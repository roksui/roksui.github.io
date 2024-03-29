---
title: "GitHub Basics"
categories: 
  - Git Basics
date: 2021-11-25 04:10:12
---

## What is GitHub?

깃헙이란 버전 관리 및 협업을 위한 코드 호스팅 플랫폼(code hosting platform)이다. 쉽게 말해 어디서든 다른 사람들과 프로젝트를 같이 수행할 수 있게 해준다.

## Github에서의 Workflow

깃헙을 통해 개발 과정에서 협업을 할 수 있다. 작업물은 레포지토리들로 구성되고, 개발자들은 여기에 요구사항이나 방향성을 명시하고, 팀 멤버들에 대한 기대치를 설정할 수 있다. 그리고 깃헙 플로우를 사용하여 개발자들은 브랜치를 생성하여 업데이트를 만들고, 변경 사항을 커밋하고 pull request를 만들어 변경 사항들을 제안 및 토의하고, 모두 동의하였을 때 pull request를 머지하는 과정을 거친다.

위에 언급한 깃헙 플로우(GitHub flow)는 가볍고(lightweight), 브랜치 기반 워크플로우로써, 전 세계에서 사용되는 깃의 코어 커맨드들을 사용한다.

깃헙 플로우는 6개의 단계이다.

- **브랜치 생성**: 배포 브랜치(주로 `master`)로부터 생성된 토픽 브랜치들은 여러 개의 평행(parallel)적인 협업을 가능케 한다. 짧은 수명은 가진(short-lived) 토픽 브랜치들은 특히 팀의 집중도를 높여주고 빠른 출시를 가능하게 해준다.

- **커밋 추가**: 브랜치안에서의 개발 작업의 스냅샷은 프로젝트 히스토리에 안전하고, 돌아갈 수 있는 시점을 생성해준다.

- **풀 리퀘스트 보내기**: 풀 리퀘스트는 프로젝트에서 각자 진행중인 작업을 팀원들에게 공개하여 투명적인 개발 과정을 돕는다.

- **코드 리뷰 및 디스커션**: 오픈되어 있는 풀 리퀘스트에 댓글을 쓰거나, 테스트하거나 검토함으로써 팀원들은 코드 리뷰에 참여한다. 오픈하고 참가주의(participatory) 문화에서 코드 리뷰는 핵심 요소이다.

- **머지**: 머지를 클릭하면 깃헙은 자동으로 로컬에서의 `git merge`와 동일한 작업을 수행한다. 또, 깃헙은 머지된 풀 리퀘스트에 브랜치의 개발 히스토리 전체를 유지한다.

- **배포**: 팀은 가장 최적의 릴리즈 사이클을 정하거나 연속적 통합(continuous integration) 툴을 사용하여 배포 브랜치의 코드가 탄탄한 워크플로우를 거쳤다는 것에 대한 확신을 갖을 수 있다.

## 협업 개발을 위한 모델

사람들이 깃헙에서 협업하는 방식에는 크게 두 가지가 있다.

- 공유된 레포지토리
- Fork & Pull

공유 레포지토리에서는 개개인과 팀이 컨트리뷰터로 지정되어 read, write, 혹은 administrator 접근 권한을 갖는다. 이러한 간단한 권한 구조와 protected branch(보호 브랜치), Marketplace와 같은 기능들을 통해 깃헙은 팀들이 빠르게 작업을 수행할 수 있도록 해준다.

오픈소스 프로젝트나 누구나 컨트리뷰트를 할 수 있는 프로젝트에 대해서는 개개인의 권한을 관리하는 것은 어렵지만, *fork and pull* 모델을 통해 프로젝트를 보는 그 누구나 컨트리뷰트할 수 있다.
Fork를 하면 개발자의 개인 계정 아래에 프로젝트의 카피가 생긴다. 모든 개발자는 그들의 fork에 대해서는 full 권한을 가지고 수정이나 새로운 기능을 구현할 수 있다. Fork에서 완료된 작업은 그냥 따로 보관되거나 풀 리퀘스트를 통해 원래 프로젝트로 합쳐질 수도 있다. 그러면 그 프로젝트의 메인테이너들이 제안된 변경 사항들을 보고 머지를 할지 말지를 정할 수 있는 것이다.
