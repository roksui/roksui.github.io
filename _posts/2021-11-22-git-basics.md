---
title: "Git Basics"
date: 2021-11-22 12:20:00
---

## What is Git?
Git은 무료 오픈소스 distributed version control system으로써 소스 코드의 변경을 추적하기 위해 사용된다. 매우 활발하게 관리되고 있는 프로젝트이고, 소프트웨어 개발 프로세스에서 없어서는 안되는 편리한 must-have 툴이다.

다음과 같은 특징을 가지고 있다.
- 오픈소스/무료 소프트웨어
- 사용하기 쉽고 매우 빠름
- Branching 기능: 새로운 아이디어를 시도해보기 위해 branch를 생성하고, 변경 사항을 잃지 않고 이전 상태의 코드로 돌아 올 수 있다. 즉, 실험하기 위해 브랜치를 만들고, 잘 안될거 같으면 그냥 삭제하고 돌아 올 수 있다 - 아무도 모르게
- 나의 프로젝트의 모든 부분의 암호화 관련의 무결성을 보장해줌 - 강한 보안
- 탈중앙화(decentralized)되어 있고 분배(distributed)되어 있음 - 많은 개발자들과 협업하는 것이 매우 원활함

이는 Git의 몇 가지 장점을 나열한 것이고, 이보다 더 많은 기능과 편리함을 가져다 준다.

Git을 사용하면서 다음과 같은 용어들을 많이 접한다.

- Commit: 코드 히스토리에서의 하나의 시점. 커밋은 직관적으로(formal하지 않지만) 보면 코드 베이스의 특정 변경 사항(들)로 구성된 스냅샷으로써 언제든지 다시 가져올 수 있는 것이라고 이해할 수 있다.
- Branch: 활동 중인 개발 라인(line)
- Repository: 커밋들(+그것의 메타데이터)의 컬렉션

## Installation
Mac OS나 리눅스 시스템에서는 Git이 디폴트로 설치되어 있다. 터미널에 `git`을 치면 확인할 수 있다.

윈도우에서는 [WSL (Windows Subsystem for Linux)](https://www.microsoft.com/ko-kr/p/ubuntu-1804-lts/9n9tngvndl3q?activetab=pivot:overviewtab)와 같은 command-line Linux in Windows를 사용하는 것이 좋다. 혹은 Windows CLI를 잘 안다면 윈도우용 Git을 사용해도 된다.