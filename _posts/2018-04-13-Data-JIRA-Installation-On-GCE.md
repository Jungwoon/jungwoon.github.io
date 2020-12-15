---
layout: post
title: "Compute Engine에 JIRA 설치하기"
image: '/assets/img/'
description: 'Compute Engine에 JIRA 설치하기'
tags:
- JIRA
- Google Cloud
categories:
- JIRA
- Google Cloud
---

이번에 회사에서 JIRA를 사용하게 되면서 서버에 만드는 방법을 한번 정리해보려고 합니다.

Atlassian 자체적으로 클라우드 서비스를 제공해서 굳이 자신의 서버에 설치를 하지 않아도 되지만,
저는 회사 서버에 설치를 위해서 설치 방법을 진행해보도록 하겠습니다.

### 실습

우선 [구글 클라우드 콘솔](https://console.cloud.google.com)에 들어가서
인스턴스를 만들어 보도록 하겠습니다.

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
$ sudo apt-get update
$ sudo apt-get install build-essential
$ sudo apt-get install libssl-dev
```

![](https://cdn-images-1.medium.com/max/1600/1*O6ciw6qau1royXzx8sx0qg.png)

![](https://cdn-images-1.medium.com/max/2000/1*WiJRLfG3cqkl3BmzcVXQRA.png)

![](https://cdn-images-1.medium.com/max/2000/1*SKkrT67EGBXrIHZWoi3UIA.png)

그 다음 아래 명령어를 입력해서 JIRA를 다운로드 받습니다.

```bash
# 2018.04.13. 기준 최신
$ wget http://www.atlassian.com/software/jira/downloads/binary/atlassian-jira-software-7.8.2-x64.bin
```

![](https://cdn-images-1.medium.com/max/1000/1*RtPbZVHVBzlVfdI5loF2WQ.png)

아래 명령어를 입력해서 권한을 줍니다

```bash
$ chmod a+x atlassian-jira-software-7.8.2-x64.bin
```

![](https://cdn-images-1.medium.com/max/1000/1*HAugA-7Bm00MrPK--FFp3w.png)

아래 명령어로 설치를 진행합니다

```bash
$ sudo ./atlassian-jira-software-7.8.2-x64.bin
```

![](https://cdn-images-1.medium.com/max/1000/1*eOkUuO9Kg00mbshsoFc3dA.png)

그럼 다음과 같은 화면을 볼 수 있습니다.

`o`를 누르고 엔터를 눌러서 설치를 진행합니다.

![](https://cdn-images-1.medium.com/max/1000/1*Qnq0BWGHk6s8KZR-ezbEqw.png)

그 이외에도 아래와 같이 다양한 옵션을 물어보는데, 최종적으로 `i`를 누르고 엔터를 누르면 설치가
진행됩니다.

![](https://cdn-images-1.medium.com/max/1000/1*EGzl5yTczKQfqiisjfnE8w.png)

완료가 되면 아래와 같이 나타납니다.

![](https://cdn-images-1.medium.com/max/1000/1*XdM0pPqiTM2H0BgSgP0slg.png)

이번에는 JIRA 기본 포트인 `8080`포트를 열어주기 위해서
[구글 클라우드 콘솔](https://console.cloud.google.com)로 돌아와서
상단에 `Google Cloud Shell 활성화` 버튼을 눌러줍니다.

![](https://cdn-images-1.medium.com/max/1000/1*OuAi8m2vDcN_gHPCoKDeTQ.png)

그럼 아래에 다음과 같이 `터미널`이 나타나는데, 아래 명령어를 입력해서 방화벽 규칙을 만듭니다.

그럼 `allow-tcp-8080`이란 이름으로 방화벽 규칙이 생성됩니다.

```bash
$ gcloud compute firewall-rules create rule-allow-tcp-8080 --source-ranges 0.0.0.0/0 --target-tags allow-tcp-8080 --allow tcp:8080
```

![](https://cdn-images-1.medium.com/max/1000/1*h_9W_DYlF-FQy8r74ZvT1w.png)

그 다음 자신의 `GCE instance 이름`을 `[VM_NAME]` 넣어서 아래 명령어를 입력해 인스턴스에 방화벽 규칙을 적용합니다.

```bash
$ gcloud compute instances add-tags [VM_NAME] --tags allow-tcp-8080
```

![](https://cdn-images-1.medium.com/max/1000/1*tT9kbF0EzJ2Y1tOjw3JICw.png)

그 다음 브라우저에서 `http://자신의 서버 주소:8080`으로 들어오면 다음과 같이 JIRA 설치 마법사가 보입니다.

`사용자를 위해 설정하십시오.`를 누르고 `My Atlassian으로 계속` 버튼을 누릅니다.

![](https://cdn-images-1.medium.com/max/1000/1*ql-VHwbIlop2b5Pg4HRy1A.png)

그럼 아래와 같이 로그인하라고 나오는데 로그인을 진행합니다.

![](https://cdn-images-1.medium.com/max/1000/1*1VfSmx5UCCOiKlHf0EPOcg.png)

그 다음 아래 화면이 나오면 `JIRA Software (Server)`를 선택해줍니다.

![](https://cdn-images-1.medium.com/max/1000/1*fHJS17DwyvsSUonb0QQ6jg.png)

그 다음 자신에 환경에 맞게 설정을 진행하고 `Generate License`를 누릅니다.

![](https://cdn-images-1.medium.com/max/1000/1*w_kmXqSXLFgAXYc8vwl6BA.png)

그럼 마지막으로 해당 주소가 맞는지 확인을 하는데 `Yes`를 눌러 줍니다.

![](https://cdn-images-1.medium.com/max/1000/1*N4x_g-hNSs4-cHEgumxHvg.png)

그 다음은 관리자 계정을 등록을 하는데, 등록하고자 하는 계정을 등록하고 `다음`을 누릅니다.

![](https://cdn-images-1.medium.com/max/1000/1*gLB-UpHzRf3XsA7my5bdog.png)

그 다음은 다음과 같이 설치가 완료 됩니다.

![](https://cdn-images-1.medium.com/max/1000/1*yeqEmYgTtaB_XRnX8VbNBw.png)

완료가 되면 아래와 같이 나옵니다. `시작해봅시다.`를 누릅니다.

![](https://cdn-images-1.medium.com/max/1000/1*eES-FCCLYNSRYr4gRkcTqA.png)

주로 사용할 언어를 선택합니다.

![](https://cdn-images-1.medium.com/max/1000/1*_RGwD9LE0cefscXNQ5pp1A.png)

그 다음 `Continue` 버튼을 눌러서 계속 진행합니다.

![](https://cdn-images-1.medium.com/max/1000/1*8rfpgPHLBtESw0X2xyA2Gw.png)

다음 화면이 나오면 `아바타 선택`을 눌러서 원하는 아바타를 선택하고 `다음` 버튼을 누릅니다.

![](https://cdn-images-1.medium.com/max/1000/1*2bNf2uZoFPc0p5WbFCs9Jg.png)

그럼 다음과 같은 화면이 나오면서 JIRA 설치가 완료됩니다.

![](https://cdn-images-1.medium.com/max/1000/1*8oTctmR60gLHhwf1yqG_Fw.png)

최종적으로 `스크럼`, `칸반` 등 자신의 필요에 의해서 선택을 하고 대쉬보드를 보면
다음과 같은 화면이 나타납니다.

![](https://cdn-images-1.medium.com/max/1000/1*0XKdcX_o2Z_o18TiLnSBBQ.png)