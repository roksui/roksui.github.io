---
title: "Jenkins Basics"
date: 2021-11-25 09:10:32
---

## What is Jenkins?

젠킨스는 자동화된 서버로써 소프트웨어 개발 프로세스를 돕는다. 예를 들어, 소스 코드 레포지토리에 변경이 생길 경우 코드 빌드를 trigger하기 위해 사용될 수 있다.

## Jenkins 설치 - Using Docker

젠킨스를 셋업하는 가장 쉬운 방법 중 하나가 Docker를 통해서 하는 것이다. 이 글에서 도커를 깊이 있게 다루진 않겠지만 도커는 소프트웨어를 컨테이너(container) 형태로 전달하게 해주는 소프트웨어라는 것을 알고있자.

먼저 도커를 설치해야 한다. [Docker 설치](https://docs.docker.com/get-docker/)를 따라서 설치하자.

도커가 설치되었다면 다음 커맨드를 실행시킨다.

```bash
docker run -d -v jenkins_home:/var/jenkins_home -p 8080:8080 -p 50000:50000 --name jenkins jenkins/jenkins:lts
```

unlock password를 얻기 위해 다음을 실행한다.

```bash
docker exec -it jenkins /bin/bash
jenkins@c537b805f333:/$ cat /var/jenkins_home/secrets/initialAdminPassword
```

그리고 패스워드를 기억하자.

컨테이너들은 중지시키려면,

```bash
docker stop jenkins
```

를 실행한다.

localhost:8080에 접속하면 젠킨스 포탈이 나온다.

## Jenkins 설치 - Fresh Install

만약 VM이 매우 느리고 도커도 사용할 수 없다면 PC에 젠킨스 소프트웨어를 설치해도 된다.

이 글에서는 윈도우 OS 기준 설치 방법만 다뤄보겠다.

젠킨스 설치 전, Java (JDK)가 설치되어 있어야 한다.

```bash
java -version
```

으로 간단히 확인해주자.

젠킨스는 [Jenkins Download](https://www.jenkins.io/download/)에서 다운로드 할 수 있다.
브라우저가 installer를 다운받으면 다운로드된 파일을 압축해제하고 jenkins.exe를 실행한다.

Next를 누르고 원하는 경로에 젠킨스를 설치한다.

설치 후, 3-5분 정도는 기다려야 Windows의 service로 젠킨스 서비스가 백그라운드에서 준비가 된다. 그 후, <http://localhost:8080>으로 접속한다.

패스워드를 입력하라고 할텐데, 명시된 파일에서 패스워드를 복붙해준다. 그 후, Install suggested plugins를 하면 플러그인 설치가 시작된다.
해당 설치가 끝나면, admin 유저를 생성하라고 하는데, 시키는대로 한다. 그리고 url을 설정하라고 하는데 디폴트로 유지하자. Save and Finish를 하고 Start using Jenkins를 클릭한다.

이제 사용할 준비가 되었다.

## Accessing Jenkins using Ngrok

PC에 젠킨스를 설치하였다면 localhost:8080에 접속하면 되고, VM에서는 파이어폭스를 통해 젠킨스에 접속할 수 있다.

젠킨스가 localhost 포트 8080에서 실행되고 있기 때문에 협업을 하기 위해서는 인터넷에 노출시켜야 한다. 우리는 무료 소프트웨어인 ngrok을 통해 이를 가능하게 할 것이다.

Ngrok은 [Ngrok Download](https://ngrok.com/download)에서 다운로드 받을 수 있다. 가이드를 따라서 ngrok을 unzip한다. 그리고 ngrok 프로그램이 있는 디렉토리로 cd한 다음 다음 커맨드를 실행한다.

```bash
ngrok http 8080
```

Output:

```bash
@inconshreveable                              (Ctrl+C to quit)

Session Status online
Account <Name> 
Version 2.3.34
Region United States (us)
Web Interface http://127.0.0.1:4040
Forwarding http://f9e633c8.ngrok.io -> http://localhost:8080
Forwarding https://f9e633c8.ngrok.io -> http://localhost:8080

Connections ttl opn rt1 rt5 p50 p90
0 0 0.00 0.00 0.00 0.00
```

8시간 후에 만료되는 public IP 주소를 얻을 수 있다 (<https://f9e633c8.ngrok.io>). Ngrok 웹사이트에 계정을 만들어 이 시간을 늘릴 수는 있지만, 현재 세션 만료 후 위 커맨드를 다시 실행하여 새로운 도메인을 얻을 수도 있다.

## Integrate Jenkins with Github using web-hooks

젠킨스와 깃헙을 통합하면 새로운 코드 빌드를 자동으로 확인할 수 있다. 새로운 코드 빌드가 성공인지 실패인지를 쉽게 알 수 있다. 이를 통해 최종, 혹은 마스터 레포지토리에 커밋되었을 때 에러가 있는 코드를 빠르게 식별할 수 있다.

젠킨스를 사용하기 위해 새로운 job 혹은 item을 생성해야한다.

item name을 jenkins-github으로 하고 Freestyle project를 선택하자.

General에서 item에 대한 상세 내용을 더 적어도된다. 이거는 선택 사항이지만 Source Code Management는 적어줘야 한다.
Git을 선택하고 레포지토리 URL도 적어준다.

빌드 에러를 확인하기 위해 젠킨스를 사용하는 것이기 때문에 Github hook trigger for GITScm polling을 선택해준다. 이 기능은 git event(예. push)가 수행되었을 때 젠킨스에서 빌드를 trigger한다.

Save를 클릭하고 대시보드로 돌아오면 github-jenkins item이 추가된 것을 볼 수 있을 것이다.

깃헙으로부터 젠킨스가 trigger를 받는 것을 활성화하기 위해 젠킨스에서 플러그인 설정을 해야한다.

Manage Jenkins -> Manage Plugins -> Available 탭 클릭 -> Filter option에 key로 'Github' -> GitHub Integration Plugin

이 플러그인을 선택하고 Download now and install after the restart를 클릭한다. 그러면 젠킨스가 해당 플러그인을 설치하고 서버를 재시작 시킬 것이다.

젠킨스가 재시작하는 동안 깃헙으로 가서 몇 가지 설정을 해줘야 한다.

Working 레포지토리의 Settings 탭 -> Hooks로 간다. 이를 통해 git event가 발생했을 때 젠킨스를 notify해줄 service를 생성할 것이다.

Add webhook을 클릭하면 폼을 작성해야 하는데, Payload URL에 이전에 ngrok을 통해 얻은 ip주소/github-webhook/을 적어준다. 레포지토리에서 push 이벤트가 발생할 때 깃헙에 의해 trigger가 될 URL이 이것이다.

URL이 제대로 설정되었다면 초록색 체크표시가 생성될 것이다.

젠킨스-깃헙 연동이 잘 작동하는지 테스트하기 위해 깃 레포지토리에 몇 가지 변경 사항을 만들고 젠킨스에게 webhook을 trigger해본다.

젠킨스 대시보드에 가서 빌드 히스토리를 보자. 빌드가 성공적이면 동그라이 아이콘이 파란색이 될 것이다.

## Continuous Integration using Jenkins

연동 내용: GitHub -> Jenkins -> Gradle

Gradle을 젠킨스에 연동함으로써 깃헙에 코드가 푸쉬될 때 마다 코드 빌드를 자동화할 수 있다. 이를 활성화하기 위해 Manage Jenkins -> Global Tool Configuration에 들어간다.

Gradle Installations 아래에 Add Gradle을 클릭한다. Gradle name을 gradle_build로 입력해주고 Install automatically를 선택한다. 그 다음, 이 gradle_build을 호출해야하는데 그러기 위해서 대시보드에서 github-jenkins를 선택하고 Configure를 선택하면 github-jenkins item에 대한 설정이 나올 것이다. Build 탭에 들어가서 Add build step을 누르고 Invoke Gradle script를 선택해준다.

Invoke Gradle script안에서는 Invoke Gradle을 선택하고, Gradle version name은 좀 전에 Gradle installations에서 생성한 이름 (gradle_build)으로 선택해준다. 다 되면 Save를 누른다.

적절한 Gradle 태스크를 추가한다. eg. clean build

또한, 적절한 post-build action도 추가할 수 있다.

**NOTE**: Jacoco와 젠킨스를 연동하여 성공적인 빌드가 이루어질때 마다 Jacoco 리포트를 생성하도록 만드는 것도 실험해보면 좋을듯 하다.
