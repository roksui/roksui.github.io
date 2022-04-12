---
title: "Git의 checkout에 대하여"
---

## 개요
지금까지 Git을 VCS으로 사용해왔고, 자주 사용되는 커밋, 브랜치, 푸쉬, 풀 기능을 많이 사용해왔지만 항상 checkout 커맨드는 branch 변경의 개념으로만 알고 사용해왔다.

그러나 실제로 checkout의 용도는 브랜치 변경보다 더 큰, 커밋 변경의 용도로도 사용된다는 것을 알았고, 이에 대해 어떤식으로 사용될 수 있는지, checkout의 더 많은 용도를 알아보기 위해 글을 작성하게 되었다.

## Checkout의 뜻이 정확히 무엇일까?
모든 VCS의 중요한 목적 중 하나는 프로젝트의 복사본을 안전하게 저장하여 코드 베이스에 돌이킬 수 없는 변경이 생기더라도 이전 상태로 돌아가기 위함이라 할 수 있다. 이러한 코드의 히스토리를 Git에서는 커밋을 통해 기록을 하고 있고, 필요 시 특정 커밋을 통해 프로젝트의 이전 상태로 돌아갈 수 있는 것이다.

먼저 영어로 "check out"은 떠난다는 뜻이다. 이와 비슷하게 Git 용어로 "checkout"한다는 것은 타깃 개체(target entity)를 그것의 다른 버전으로 바꾼다는 뜻이다.

`git checkout` 커맨드는 세 개의 개체에 동작한다 - 파일, 커밋, 그리고 브랜치

Git에서 "checking out"을 한다고 하면 대체로 `git checkout` 커맨드를 실행한다는 것을 의미한다.

결국 checkout을 통해 프로젝트의 상태를 변경할 수 있다라는 점을 생각해보면 위에서 말한 프로젝트를 다른 상태로 되돌리기(undo)를 할 수 있다는 의미이다.

`git log`를 통해 방문하고 싶은 상태를 가리키는 커밋을 찾았다면 `git checkout` 명령어를 써서 해당 커밋을 방문할 수 있다. 디폴트로 `git log`는 현재 선택된 브랜치의 커밋만 보여준다. 그러나 찾고자 하는 커밋이 다른 브랜치에 있을 수도 있다. `git log --branches=*`를 실행하여 모든 브랜치에 있는 모든 커밋을 볼 수 있다.

로그 히스토리에서 가고자하는 부분을 가리키는 커밋 레퍼런스를 찾았다면, `git checkout` 커맨드를 사용하여 해당 커밋에 방문할 수 있다. 

깃으로 버전 관리를 할 때, HEAD는 주로 로컬 브랜치를 가리키고 있는데, 이전 커밋으로 checkout을 하면 HEAD는 직접적으로 그 커밋을 가리키게 된다. 따라서 HEAD는 더이상 브랜치를 가리키지 않으므로 "detached HEAD" 상태가 된다.

이 때, HEAD가 정확이 무엇인지 알아보자.

먼저, 깃 레포지토리는 **objects**와 **references**의 컬렉션이다.


## 출처
https://git-scm.com/docs/git-checkout
https://www.atlassian.com/git/tutorials/undoing-changes
https://www.bryanbraun.com/2019/06/11/why-we-use-git-checkout-to-discard-file-changes/
https://www.cloudbees.com/blog/git-detached-head
