---
layout: post
title: "쿠버네티스 정리 #2 - 쿠버네티스 설치"
image: '/assets/img/'
description: 'Kubernetes Installing'
tags:
- Kubernetes
categories:
- Kubernetes
---

# 쿠버네티스 설치

최근 개인적으로 이직을 하고 연말, 연초다 보니 포스팅이 소홀해졌는데, 새해 새마음으로 다시 포스팅을 시작하려고 합니다, 새해에 사내 스터디로
쿠버네티스를 공부하기로 했는데, 관련 내용을 공부하며, 같이 블로그에 정리를 해보고자 합니다. 
학습할 때 참고한 책은 [쿠버네티스 입문](https://book.naver.com/bookdb/book_detail.nhn?bid=16030803) 입니다.

---

## 도커 및 쿠버네티스 설치

쿠버네티스 설치를 위해서 도커 허브의 도커 데스크탑을 이용하도록 하겠습니다.

먼저 [Docker Hub](https://hub.docker.com)에 들어갑니다. 우측 상단의 `Sign In`을 누릅니다.

![](https://miro.medium.com/max/4244/1*ZEiklR3TjUiLRELFAcX1TQ.png)

로그인을 하고 계정이 없으면 회원가입을 합니다.

![](https://miro.medium.com/max/2148/1*BRbvGUC5EDtWmGdYBpKSUw.png)

그럼 아래와 같은 화면이 나타나는데, 우측 중간에 있는 `Download Docker Desktop`을 눌러서 다운로드를 합니다. 

![](https://miro.medium.com/max/4272/1*rqRG16RNWwNWvxRgJJIBVA.png)

다운이 받아지면 아래와 같이 나타나는데, 실행을 합니다.

![](https://miro.medium.com/max/3516/1*h3n7O-kskzbsi5KL_zlpdg.png)

Docker 아이콘을 드래그 & 드랍을 합니다.

![](https://miro.medium.com/max/3020/1*8RpA-DBU4RoVGEafvCpECg.png)

설치가 완료되면 Docker를 실행합니다.

![](https://miro.medium.com/max/736/1*7BraKoD84ML6_jkFZkSzww.png)

그럼 다음과 같이 나타나면 설치가 잘 된 것입니다.

![](https://miro.medium.com/max/1480/1*pZf9cD71Kn7h5KQKw6v55g.png)

정말 잘 되었는지 확인하기 위해서 터미널을 열고 `$ docker version`을 눌러서 아래와 같이 나타나는지 확인합니다.

> 버전 정보가 잘 나타난다면 설치가 잘 된 것입니다.  

![](https://miro.medium.com/max/2464/1*kkospPmV4iTZ3-RzIUhQ7g.png)

이번에는 `Kubernetes`를 설치하도록 하겠습니다. `Docker -> Preferences`를 누르도록 하겠습니다.

![](https://miro.medium.com/max/1112/1*O--SNYurZsRzsGlvdlHWHQ.png)

Kubernetes를 선택하고 `Enable Kubernetes`와 `Deploy Docker Stacks to Kubernetes by default`를 누르고
`Apply`를 누릅니다.

![](https://miro.medium.com/max/2184/1*Hoc8CjVgILxHAzXZenfYpQ.png)

그럼 아래와 같이 나타나는데, `Install` 을 눌러서 설치를 진행합니다.

![](https://miro.medium.com/max/2092/1*GOK7B6Gmv6TVmJxTbVfgIQ.png)

그럼 다음과 같이 설치가 진행이 됩니다.

![](https://miro.medium.com/max/2084/1*YhHn6MRmFmGTPSz3Wa_9tQ.png)

설치가 다 끝나면, 아래와 같이 나타나는데 저 같은 경우는 재부팅을 했어야 했습니다. 만약 실행이 안된다면, 재부팅을 합니다. 

![](https://miro.medium.com/max/1100/1*Qp6TlDwR3yF9FP27wPLI7g.png)

잘 설치가 되었는지 확인하기 위해서 터미널을 열고 `$ kubectl version` 눌러서 버전정보가 잘 나타나는지 확인합니다.
버전 저보가 잘 나타나면 설치가 잘 된 것입니다.

![](https://miro.medium.com/max/2504/1*I_4z4fA7fd5illLmcjwXxw.png)

---

## Google Cloud Platform 에서 Kubernetes Engine 설정하기

쿠버네티스를 처음으로 만들어서 공개한 회사가 구글이나 보니 구글 클라우드인 GCP에서도 Kubernetes Engine란 이름으로
쿠버네티스를 사용할 수 있는 서비스를 제공하고 있습니다. 

Compute Engine에 Kubespray 를 설치하여 환경 설정을 하도록 하겠습니다.

[GCP Console](https://console.cloud.google.com)에 접속합니다.

![](https://miro.medium.com/max/4196/1*hRC_HhIVbu_P-Px_Yg6pRQ.png)

메뉴 - Compute Engine - VM 인스턴스를 선택합니다.

![](https://miro.medium.com/max/1976/1*ysrAlkokp7NkGEtJ3PaZNg.png)

그럼 다음과 같이 나타나는데 `만들기`를 선택합니다.

![](https://miro.medium.com/max/1776/1*rW-4W226VoFm60eHt41myg.png)

그럼 다음과 같이 인스턴스를 만들 수 있는 환경이 나타납니다.

![](https://miro.medium.com/max/4760/1*mosiNf2iX7w7DRCxgZlH4Q.png)

부팅 디스크 설정만 바꿔 주기 위해서 `변경`을 누릅니다.

![](https://miro.medium.com/max/1924/1*vmwKxExJmmESuGKMtSVkHg.png)

그럼 아래와 같이 나타나는데 `Ubuntu 16.04 LTS` 버전을 선택합니다. 그 다음 `만들기`를 눌러서
인스턴스를 만듭니다.

(이미지만 변경 합니다)

![](https://miro.medium.com/max/2012/1*EStEm7hbs3z7y_40erjFqQ.png)

동일한 설정(이미지만 Ubuntu 16.04 LTS)으로 해서 총 인스턴스 5개를 만듭니다.

![](https://miro.medium.com/max/3472/1*kzMUBpkXg_HKS1cVTUNAiQ.png)

공개키 설정을 위해서 SSH 버튼을 눌러서 instance-1에 접속니다. 
(instance-1을 마스터로 사용할 예정)

![](https://miro.medium.com/max/3524/1*RHqFS5e8Wo-trQxOGWeMNQ.png)

그럼 다음과 같이 터미널 환경이 나타납니다.

![](https://miro.medium.com/max/3684/1*uehs4953dh44JDurEfDSrQ.png)

공개키를 먼저 만들기 위해서 `ssh-keygen -t rsa`를 입력합니다.

(그 외에 옵션은 Enter를 눌러서 기본값을 넣어줍니다.)

![](https://miro.medium.com/max/2524/1*dzmcpQ6vwnQZ9dz-_3fEJg.png)

키가 잘 만들어 있는지 확인하기 위해서 아래 명령어로 확인합니다.

```bash
$ cd .ssh
$ ls -al
```

![](https://miro.medium.com/max/2492/1*pYIm-u5YDBmShR_nEj6WVw.png)

아래 명령어를 통해서 공개키를 복사하고, 나온 명령어를 복사합니다.

```bash
$ cat id_rsa.pub
```

![](https://miro.medium.com/max/3696/1*teBFFkC7AsV1DPS_LalISw.png)

GCP에 공개키를 등록하기 위해서 `Compute Engine - 메타데이터`에 들어갑니다.

![](https://miro.medium.com/max/3504/1*5dkE60Evgv1vtSjur3okpw.png)

`메타데이터 - SSH 키`를 눌러서 `수정`을 누릅니다.

![](https://miro.medium.com/max/2704/1*kWUT0kI6R0kj2u2dH74qTQ.png)

아래와 같이 나타나면 `항목 추가`를 누릅니다.

![](https://miro.medium.com/max/2808/1*I14EYYOAtxXcOWxS9kXVJw.png)

복사한 키를 아래와 같이 넣으면 자동으로 왼쪽에 아이디(여기서는 byjungwoon)이 나타납니다. 그 다음에 `저장`을 누릅니다.

`(만약에 나타나지 않았다면 공개키 코드에 띄어쓰기 때문일수도 있으니 그 부분도 확인해야 합니다)`

![](https://miro.medium.com/max/2964/1*MkIUJHR9Jf8vOH1oI4-wnw.png)

잘 등록이 되었으면 메타데이터에 추가된 것을 확인할 수 있습니다.

![](https://miro.medium.com/max/2708/1*RSeZRtKIjIJSA_uqKEhWxg.png)

잘 되었는지 보기 위해서 instance-2에 접속합니다.

![](https://miro.medium.com/max/4232/1*kXZaqCF_J6LqSNMjlXxnRQ.png)

터미널에 `cat .ssh/authorized_keys`를 눌러보면 아래와 같이 등록된 키를 확인할 수 있습니다.

![](https://miro.medium.com/max/3712/1*2VB2KlBFan0KpUNK3anWYw.png)

---

## Kubespray 설치하기

![](https://mblogthumb-phinf.pstatic.net/MjAxODA3MTBfNjMg/MDAxNTMxMTU1NTExOTQy.du0V1dfUracZixe-KlZCQ35fd9LdU9pC2_VKgDi-OX0g.kveGiB_FTtMXgKIjLa7qG3kYqbZMU-zBXYc2d82of5cg.PNG.alice_k106/ycmjhkbbxvrabrdn66xlunt5rta.png?type=w2)

이번에는 Kubespray를 설치해보도록 하겠습니다. Kubespray는 Ansible을 기반으로 Kubernetes를 설치합니다.
이를 이용하면 Kubenetes를 손쉽게 설치할 수 있게 도와줍니다. 

---

일단 Master 인스턴스인 `instance-1`의 터미널을 열고 `$ sudo apt update`를 눌러 `apt`를 업데이트 합니다.

![](https://miro.medium.com/max/1524/1*9dOnF9r5nq_beUwvrFCUcg.png)

그 다음은 pip를 설치하기 위해 아래 명령어를 입력합니다.

```bash
$ sudo apt -y install python-pip
```

![](https://miro.medium.com/max/1920/1*EA2Pe9pLCJp1-b-Xb-1t5Q.png)

설치가 잘되었는지 확인하기 위해 아래 명령어를 입력합니다. 아래 이미지와 같이 버전 정보가 잘 나타나면 설치가 잘 된것입니다.

```bash
$ pip --version
```

![](https://miro.medium.com/max/2468/1*7raWwxfjdqU0IKMTC-Dssg.png)

이제 본격적으로 `kubesparay`를 설치할 것입니다. `git clone` 명령어를 통해서 다운로를 진행합니다.

```bash
$ git clone https://github.com/kubernetes-sigs/kubespray.git
```

![](https://miro.medium.com/max/2912/1*OVjrDy3vC57FbTOhdoxuwg.png)

다운받은 디렉토리에 아래 명령어로 들어오면 다음과 같이 필요한 파일들이 잘 있는것을 확인 할 수 있습니다.

```bash
$ cd kubespray/
```

![](https://miro.medium.com/max/3552/1*irsgbTsoOm18Dr3nz7obhw.png)

안에 있는 `requirements.txt`에 필요한 패키지들이 명시되어 있는데, 이를 이용해 설치를 하기 위해 아래 명령어를 입력합니다.

```bash
$ sudo pip install -r requirements.txt
```

![](https://miro.medium.com/max/2448/1*2hDGgP6FRSUPSRF_FXql1Q.png)

설치가 자 되면 ansible이 설치가 되는데 잘 설치되어 있는지 보기 위해 다음 명령어를 입력해봅니다.

```bash
$ ansible --version
```

![](https://miro.medium.com/max/2740/1*hNIq2u9Y577oFOF1DAIimg.png)

아래 명령어를 통해서 기본적으로 `inventory/sample`을 `inventory/mycluster` 로 복사합니다.

```bash
$ cp -rfp inventory/sample inventory/mycluster
```

![](https://miro.medium.com/max/2596/1*ihskNw3Mn6_dNYhRaKaFYw.png)

복사한 `/inventory/mycluster 디렉토리`를 보면 아래와 같이 두개의 파일이 있습니다.

![](https://miro.medium.com/max/2376/1*575ocYMMggESeIR3BoL9kg.png)

디렉토리의 `tree 구조`를 보기 위해서 tree 패키지를 설치합니다.

```bash
$ sudo apt install tree
```

![](https://miro.medium.com/max/2008/1*Datc2uQk-fsO_d27Rzv7Cg.png)

tree를 이용해 `group_vars 디렉토리`에 보면 설치에 필요한 yml 파일들이 있는걸 확인할 수 있습니다.

```bash
$ tree inventory/mycluster/group_vars
```

![](https://miro.medium.com/max/2440/1*0GsqoCrfRABCr-1KSq33Ew.png)

이제 설치를 위한 설정을 하기 위해 `inventory.ini`를 수정하겠습니다. 아래 명령어를 입력합니다.

```bash
$ vi inventory/mycluster/inventory.ini
```

![](https://miro.medium.com/max/2484/1*yW-V8vVJPBXpA7fXPqUwoQ.png)

그럼 아래와 같이 나타납니다. 

![](https://miro.medium.com/max/3548/1*n3ZP1msBrQVAOkU5UZp94A.png)

여기서 아래와 같이 설정을 해줍니다. 여기서 나타나는 IP는 GCP의 내부 IP 입니다.

![](https://miro.medium.com/max/3548/1*Z8rgMwjt79zKfW18NpUQrQ.png)

Compute Engine 콘솔에 나와 있는 인스턴스의 내부 IP 정보 및 호스트 이름으로 설정을 합니다.

![](https://miro.medium.com/max/2952/1*a9w7L12_pB5n_c5sJkz2fQ.png)

설정이 끝나고 ansible의 아래 명령어를 통해서 필요로 하는 의존성 및 설정을 세팅합니다.

```bash
$ ansible-playbook -i inventory/mycluster/inventory.ini -v --become --become-user=root cluster.yml
```

![](https://miro.medium.com/max/3520/1*4YmGz2kUnGMXhH6bHhux-w.png)

그럼 다음과 같이 필요로 하는 패키지 및 설정이 되는데 시간이 대충 20~30분 정도 걸렸었습니다.

(커피라도 한잔 하시고 오면 좋을거 같아요)

![](https://miro.medium.com/max/4004/1*3Q8IGTuzFo8IYKlIeGN0Aw.png)

설치가 끝나면 아래 명령어를 통해서 설정이 잘 되어 있는지를 확인합니다. 여기까지 잘 따라왔으면 설치가 잘 된 것입니다.

```bash
$ sudo -i   # user에서 root 권한을 변경
# kubectl get node
```

![](https://miro.medium.com/max/2588/1*8ErqRQcMeQu8PakGxhX-nw.png)

