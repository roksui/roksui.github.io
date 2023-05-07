---
title: "Git의 checkout에 대하여"
categories: 
  - Git Basics
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

## 특정 commit 방문하기

`git log`를 통해 방문하고 싶은 상태를 가리키는 커밋을 찾았다면 `git checkout` 명령어를 써서 해당 커밋을 방문할 수 있다. 디폴트로 `git log`는 현재 선택된 브랜치의 커밋만 보여준다. 그러나 찾고자 하는 커밋이 다른 브랜치에 있을 수도 있다. `git log --branches=*`를 실행하여 모든 브랜치에 있는 모든 커밋을 볼 수 있다.

로그 히스토리에서 가고자하는 부분을 가리키는 커밋 레퍼런스를 찾았다면, `git checkout` 커맨드를 사용하여 해당 커밋에 방문할 수 있다.

깃으로 버전 관리를 할 때, HEAD는 주로 로컬 브랜치를 가리키고 있는데, 이전 커밋으로 checkout을 하면 HEAD는 직접적으로 그 커밋을 가리키게 된다. 따라서 HEAD는 더이상 브랜치를 가리키지 않으므로 "detached HEAD" 상태가 된다.

먼저, HEAD가 정확이 무엇인지 알아보자.

먼저, 깃 레포지토리는 **objects**와 **references**의 컬렉션이다. Object들은 서로 관계를 가지고 있고, reference들은 object나 다른 reference를 가리킨다. 깃에서 주 object는 커밋이지만 blob나 tree도 있다. 깃에서 가장 중요한 reference는 브랜치라고 볼 수 있다 - 브랜치를 커밋에 붙이는 레이블이라고 생각할 수 있다.

HEAD는 중요한 종류의 reference이다. HEAD의 목적은 깃 레포지토리의 현재 시점을 추적하기 위해 존재한다 - "내가 지금 어디에 있지?"라는 것에 대한 질문에 답해주는 것이다. 예를 들어, 우리가 `git log`를 하면 깃은 어느 커밋부터 보여줄지 어떻게 결정할까? 바로 HEAD로 인해 알 수 있는 것이다. 새로운 커밋을 하면, 해당 커밋의 부모는 현재 HEAD가 가리키는 커밋이 된다.

이제, 다시 detached HEAD를 알아보자. 대부분의 경우 HEAD는 브랜치 이름을 가리킨다. 새롭게 커밋을 할 경우, 브랜치 reference는 그 새로운 커밋을 가리키도록 업데이트되지만, HEAD는 동일하게 유지된다 - 즉, HEAD는 브랜치 reference를 가리키고 있는 것이다. 브랜치를 변경할 경우, HEAD는 변경한 브랜치를 가리키도록 업데이트된다. 다르게 말해, HEAD는 현재 브랜치의 마지막 커밋을 가리킨다고 생각할 수 있다. 이러한 상태가 HEAD가 브랜치에 attached된 normal state(일반적인 상태)이다.

![attached-head](/assets/img/attached_head.png)

위 사진의 상태를 보면 HEAD가 master 브랜치를 가리키고, master 브랜치는 마지막 커밋을 가리키고 있다. 모든게 깔끔하다.

만약 `git checkout 87ec91d`를 하면, 아래그림과 같이 HEAD는 해당 커밋을 직접적으로 가리키게 되어 detached HEAD state가 된다.

![detached-head](/assets/img/detached_head.png)

### Detached HEAD의 장점?

이런식으로 detached HEAD가 되는 것에는 이유가 있기 마련이다. 어떠한 장점이 있을까?

일단, 프로젝트 히스토리의 특정한 이전 시점으로 돌아가게 해준다는 점은 분명히 장점이다. 예를 들어, 저번 주 수요일에 찾고자 하는 버그가 프로젝트에 존재했는지 확인하려 한다고 생각해보자. `git log` 커맨드를 써서 날짜로 필터링하여 관련 커밋을 찾을 것이다. 그러면 해당 커밋으로 checkout을 한 후에 애플리케이션을 테스트해볼 수 있다.

근데, detached HEAD 상태에서는 이러한 이전 커밋을 확인할 뿐만 아니라, 변경을 할 수도 있다. Detached HEAD 메시지가 이를 알려준다.

```text
You are in 'detached HEAD' state. You can look around, make experimental changes and commit them, and you can discard any commits you make in this state without impacting any branches by switching back to a branch.
```

이 메시지대로라면 checkout한 커밋에서 변경 사항을 만들고 커밋을 할 수 있다 - 또 다른 히스토리 라인을 만드는 것이다. 실험해보자.

```bash
echo "Hello, another timeline!" > new_file.txt
git add .
git commit -m "Create new file"
echo "blah blah" >> new_file.txt
git commit -a -m "Add a new line to the file"
```

위 커맨드를 실행하면 다음과 같은 상태가 된다.

![detached-head-new-commit](/assets/img/detached_head_new_commit.png)

`git log --oneline`을 실행해보면 두번 째 커밋으로부터 새로운 커밋들이 파생된 것을 볼 수 있다. 그리고 `git log`는 이러한 커밋들의 이력을 커밋 시간 순서대로 보여준다.

**NOTE**: 디폴트로 `git log`를 하면 현재 브랜치를 기준으로 보여준다고 하였다. Detached HEAD 상태에서는 HEAD가 브랜치를 가리키는 상황이 아니지만, 커밋 이력은 보여진다.

![git-log-detached-head](/assets/img/git_log_detached_head.png)

요약하자면 detached HEAD 상태는 깃에서 충분히 있을 수 있고 valid한 상태이다. 따라서 이 상태를 사용하여 커밋을 확인만 하거나 혹은 변경 사항을 만들어 적용할 수도 있다. 다음과 같은 상황 케이스로 나눠보자.

**시나리오 1: 실수로 왔다.**
만약 실수로 detached HEAD 상태로 왔다면, 즉, 특정 커밋으로 checkout을 잘못 했다면, 단순히 원래 있던 브랜치로 checkout을 해주면 된다. - `git checkout <branch-name>`

**시나리오 2: 테스트를 위한 변경 사항을 만들었지만 저장하고 싶지않다.**
Detached HEAD 상태가 된 후 몇 가지 커밋을 했다. 그러나 테스트 내용이 진전이나 가치가 없어 저장하고 싶지 않다. 그러면 시나리오1에서 한 것 처럼 원래의 브랜치로 checkout을 하면된다.

새로 뻗어나가는 타임라인의 변경 사항들은 현재 브랜치에 영향을 주지 않는다. 깃은 알아서 이러한 orphan이 된 커밋들을 garbage collect해준다.

**시나리오 3: 테스트를 위한 변경 사항을 만들었고 저장하고 싶다.**
만약 detached HEAD 상태에서 만든 변경 사항들을 유지하고 싶다면, 새로운 브랜치를 만들어서 checkout하면 된다. 새로운 브랜치는 detached HEAD에 들어가자마자 생성해도 되고, 커밋을 한 뒤에 생성해도 된다. 단, 원래 브랜치로 돌아가기 전에 새로운 브랜치를 만들어야만 변경 사항이 유지가 된다.

위에 가장 마지막 그림이 나타내는 상태에서 실험을 해보자.

```bash
git branch alt-history
git checkout alt-history
```

**NOTE**: 위 두 개의 커맨드는 `git checkout -b alt-history`로 한번에 가능하다.

![git-log-attached-head](/assets/img/git_log_attached_head.png)

`git log --oneline`을 해보면 위에 로그랑 동일하다. 한 가지 다른 점은 브랜치의 이름이 마지막 커밋에 보여지는 것이다 - HEAD가 alt-history 브랜치를 가리키고 있다.

최종 레포지토리의 상태는 다음과 같다.

![attached-head2](/assets/img/attached_head2.png)

Detached HEAD에 대해서 알아봤다. 특정 커밋으로 돌아감으로 인해 HEAD가 detach되는 것은 절대 레포지토리가 잘못된 상태는 아니고, 조금 덜 일반적인(less usual) 상태에 있는 것이라고 생각할 수 있다. 심지어는 위에서 보았듯이 이러한 detached HEAD가 유용할 수도 있다.

## 출처

<https://git-scm.com/docs/git-checkout>

<https://www.atlassian.com/git/tutorials/undoing-changes>

<https://www.bryanbraun.com/2019/06/11/why-we-use-git-checkout-to-discard-file-changes/>

<https://www.cloudbees.com/blog/git-detached-head>
