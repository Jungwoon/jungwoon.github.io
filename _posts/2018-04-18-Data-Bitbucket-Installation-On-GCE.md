---
layout: post
title: "Compute Engine에 Bitbucket 설치하기"
image: '/assets/img/'
description: 'Compute Engine에 Bitbucket 설치하기'
tags:
- Bitbucket
- Google Cloud
categories:
- Bitbucket
- Google Cloud
---

지난 시간에 이어서 이번에는 Bitbucket을 설치해보겠습니다.
Bitbucket은 Atlassian에서 제공하는 git 솔루션 입니다.

`스크린샷 주소가 계속 바뀌는건, 설치하다가 실패해서 몇 번의 인스턴스를 재시작하거나 다시 만드는
과정에서 주소가 바뀌어서 그렇습니다.`

---

### 실습

우선 [구글 클라우드 콘솔](https://console.cloud.google.com)에 들어가서
인스턴스를 만들어 보도록 하겠습니다.

`만약 앞에서 만든 인스턴스가 있으면 건너뛰겠습니다`

![](https://cdn-images-1.medium.com/max/1000/1*_Fie7H1s8-jbvWN0DePiIQ.png)

- 이미지 : `Ubuntu 14.04 LTS`
- 액세스 범위 : `모든 Cloud API에 대한 전체 액세스 허용`
- 방화벽 : `HTTP 트래픽 허용`, `HTTPS 트래픽 허용`

![](https://cdn-images-1.medium.com/max/1000/1*MDusDYJIKoWL5zNBMfT2lg.png)

설치가 되면 다음과 같이 `외부 IP`가 생기는걸 확인할 수 있습니다.
이제 접속해 보도록 하겠습니다.

![](https://cdn-images-1.medium.com/max/1000/1*C9zwdnWM6hKHhJaYEMuJwA.png)

아래 명령어로 서버에 접속합니다.

```bash
$ ssh -i [개인키 경로] [계정]@[주소]
```

![](https://cdn-images-1.medium.com/max/1000/1*G_AmubhbBNvaKqw76NmC6Q.png)

아래 명령어를 입력해서 기본적인 패키지들 설치를 진행합니다

```bash
$ sudo add-apt-repository ppa:git-core/ppa -y # 최신 git을 설치하기 위함
$ sudo apt-get update # 위에 작업을 먼저 한 다음에 apt-get을 업데이트 해야함
$ sudo apt-get install build-essential # 만약 설치 안했으면 설치
$ sudo apt-get install libssl-dev # 만약 설치 안했으면 설치
$ sudo apt-get install git
```

![](https://cdn-images-1.medium.com/max/1000/1*eI-w3UsKPX4YnVg-dbun6A.png)

![](https://cdn-images-1.medium.com/max/1000/1*Gf6bHa5oEzmtDdpYSN7tig.png)

다 되었으면 아래 명령어를 입력해서 git 버전을 확인합니다.

```bash
$ git --version
```

![](https://cdn-images-1.medium.com/max/1000/1*BDBR0_84-mdWJkxsCmn7dA.png)


그 다음 아래 명령어를 입력해서 Bitbucket을 다운로드 받습니다.

```bash
# 2018.04.18. 기준 최신
$ wget https://www.atlassian.com/software/stash/downloads/binary/atlassian-bitbucket-5.9.1-x64.bin
```

![](https://cdn-images-1.medium.com/max/1000/1*LFjZn13ThF8NhpbXzum-og.png)

아래 명령어를 입력해서 권한을 줍니다

```bash
$ chmod a+x atlassian-bitbucket-5.9.1-x64.bin
```

![](https://cdn-images-1.medium.com/max/1000/1*HAugA-7Bm00MrPK--FFp3w.png)

아래 명령어로 설치를 진행합니다

```bash
$ sudo ./atlassian-bitbucket-5.9.1-x64.bin
```

![](https://cdn-images-1.medium.com/max/1000/1*rtn-CsC-Id8X8NxDsY5uTA.png)

그럼 다음과 같은 화면을 볼 수 있습니다.

설명에 따라서 설치를 진행합니다.

![](https://cdn-images-1.medium.com/max/1000/1*adUG7OCyjOKHjthPFF-vUw.png)

완료가 되면 아래와 같이 나타납니다.

![](https://cdn-images-1.medium.com/max/1000/1*Rco77ozj44_sOWKj_63auA.png)

이번에는 JIRA 기본 포트인 `7990`포트를 열어주기 위해서
[구글 클라우드 콘솔](https://console.cloud.google.com)로 돌아와서
상단에 `Google Cloud Shell 활성화` 버튼을 눌러줍니다.

![](https://cdn-images-1.medium.com/max/1000/1*OuAi8m2vDcN_gHPCoKDeTQ.png)

그럼 아래에 다음과 같이 `터미널`이 나타나는데, 아래 명령어를 입력해서 방화벽 규칙을 만듭니다.

그럼 `allow-tcp-7990`이란 이름으로 방화벽 규칙이 생성됩니다.

```bash
$ gcloud compute firewall-rules create rule-allow-tcp-7990 --source-ranges 0.0.0.0/0 --target-tags allow-tcp-7990 --allow tcp:7990
```

![](https://cdn-images-1.medium.com/max/1000/1*0wcgHm3HYmR-6xejFeLr4w.png)

그 다음 자신의 `GCE instance 이름`을 `[VM_NAME]` 넣어서 아래 명령어를 입력해 인스턴스에 방화벽 규칙을 적용합니다.

```bash
$ gcloud compute instances add-tags [VM_NAME] --tags allow-tcp-7990
```

![](https://cdn-images-1.medium.com/max/1000/1*ZXmBC4k9BRHM9llOEoCIMQ.png)

그 다음 브라우저에서 `http://자신의 서버 주소:7990`으로 들어오면 다음과 같이 Bitbucket 설치 마법사가 보입니다.

![](https://cdn-images-1.medium.com/max/1000/1*ieqMqxLXsbsFf5azoI2JdQ.png)

아래와 같이 나타나면 자신에 맞게 설정을 하고 `Next`를 누릅니다.

![](https://cdn-images-1.medium.com/max/1000/1*HRrRolKu4Uw_nX1cdyxBEg.png)

그럼 다음과 같은 화면이 나타나는데, 여기서는 먼저 로그인을 하기 위해서 `I have an account`를
클릭합니다.

![](https://cdn-images-1.medium.com/max/1000/1*kDbGOCjL4PU4As-V9X9R4g.png)

그 다음 필요한 부분 설정을 다 해주고 나면 `license key`가 자동으로 나타납니다.

그 다음 `Next` 버튼을 누릅니다.

![](https://cdn-images-1.medium.com/max/1000/1*otA4iUMtys2i8eQxh8uxlQ.png)

그 다음 관리자도 추가합니다.

![](https://cdn-images-1.medium.com/max/1000/1*o0NbbIVNv4FQaViv8AIwdQ.png)

그 다음 앞에서 만든 `Jira 서버주소`와 `id` 그리고 `pw` 를 입력하고 `Connect` 버튼을
누릅니다.

![](https://cdn-images-1.medium.com/max/1000/1*NRfvAJadW-a7BWyE2tancg.png)

그럼 다음과 같이 로그인 화면이 나타나고 좀 전에 설정한 `관리자`로 로그인을 합니다

![](https://cdn-images-1.medium.com/max/1000/1*ax4QfR7QAGgE6j7_uPjG8A.png)

그럼 다음 화면과 같이 Bitbuckt 설치가 완료된걸 확인할 수 있습니다.

![](https://cdn-images-1.medium.com/max/1000/1*NZIN7JfM4w7WRXrgmZzoJQ.png)


---

### JIRA와 연결된거 확인

JIRA에 들어가서 `프로젝트 - 설정 - 개발 도구`에 들어가면 다음과 같이 Bitbucket과 잘 연결된것을
확인할 수 있습니다.

![](https://cdn-images-1.medium.com/max/1000/1*5vW0yjzXUPZfEAb-OPIjcg.png)
