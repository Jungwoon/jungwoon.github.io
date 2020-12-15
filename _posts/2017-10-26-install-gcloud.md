---
layout: post
title: "Google Cloud SDK(=gcloud) 설치하기"
image: '/assets/img/'
description: 'Google Cloud SDK(=gcloud) 설치하기'
tags:
- Google Cloud
categories:
- Google Cloud
---

MacOS에서 gcloud 명령어를 사용하기 위해서 세팅하는 방법을 포스팅하려고 합니다

다른 운영체제의 경우에는 아래 공식 가이드를 참고하시기 바랍니다

> 자세한 사항은 [구글 공식 가이드](https://cloud.google.com/sdk/)를 참고하세요

---

### 실습

먼저 [구글 공식 가이드](https://cloud.google.com/sdk/)를 들어갑니다

그럼 아래와 같은 화면을 볼 수 있는데 `INSTALL FOR MAC OS X`를 선택합니다

![](https://cdn-images-1.medium.com/max/2000/1*hTtSdeyaUJ_3BC_mdL0q4g.png)

그럼 넘어간 페이지에서 스크롤을 조금만 내리면 아래와 같이 sdk를 다운로드 할 수 있게 됩니다

요즘은 보통 64비트 머신이므로  `x86_64`를 다운로드 받습니다

![](https://cdn-images-1.medium.com/max/2000/1*I_3ofGuqimkK5Y3LzWnUww.png)

그럼 아래와 같이 tar.gz 파일이 다운로드되는데 압축을 풉니다

그럼 디렉토리가 나오는데 전 이걸 홈 디렉토리(제 경우엔 /Users/jungwoon)로 옮길려고 합니다

![](https://cdn-images-1.medium.com/max/2000/1*xU1Uag_b5lb7Jm8Xe7KfwQ.png)

홈 디렉토리로 이동을 시킵니다

![](https://cdn-images-1.medium.com/max/2000/1*64XMIBnrG4gTSE9n_GyHpA.png)

일단 본인의 shell이 뭔지 확인을 하기 위해 아래 명령어를 입력하고 엔터를 칩니다

```
$ echo $SHELL
```

저는 zsh를 쓰고 있어서 `/bin/zsh` 라고 나오는데, 설정을 따로 안하셨다면 기본이 bash라 `/bin/bash` 가 나올겁니다

![](https://cdn-images-1.medium.com/max/1200/1*7P_Q04t2xKRQIy_F6uJsyw.png)

본인의 shell에 맡게 아래 명령어로 들어갑니다

```
$ vi ~/.bashrc # bash를 쓰고 있는 경우
or
$ vi ~/.zshrc  # zsh를 쓰고 있는 경우
```

![](https://cdn-images-1.medium.com/max/1600/1*7iI410J8L89zIhDshG32Aw.png)

그 다음 아래 설정을 넣고 저장하고 나옵니다

(여기서는 vi 사용법을 알고 있다고 가정하고 진행하도록 하겠습니다)

```
## Google Cloud
export GOOGLE_CLOUD_SDK_PATH=[google-cloud-sdk를 넣어둔 경로]
export PATH=$PATH:$GOOGLE_CLOUD_SDK_PATH/bin
```

저는 `/Users/jungwoon/google-cloud-sdk`가 경로라 아래 이미지와 같이 설정하였습니다

![](https://cdn-images-1.medium.com/max/2000/1*jQ_zZHic4LHOBL_jN8MTIw.png)

변경된 사항을 적용하기 위해서 `source` 명령어로 변경사항을 적용합니다

```
$ source .bashrc # bash를 쓰고 있는 경우
or
$ source .zshrc # zsh를 쓰고 있는 경우
```

![](https://cdn-images-1.medium.com/max/1200/1*OnAg_I9OKDjBqlZM3qRHwg.png)

그 다음 아래 명령어를 입력해서 아래 화면과 같이 나오면 설치가 끝난겁니다

```
$ gcloud --version
```

![](https://cdn-images-1.medium.com/max/1600/1*jnH6GpVf74W1icnoP2pCBA.png)

---

### gcloud 초기화 하기

위에서 명령어를 사용할 수 있게 했으면 이제 본인의 계정과 프로젝트를 연결시켜야 하는데요

그 명령어는 아래와 같습니다

```
$ gcloud init
```

그럼 이제 스크린 샷을 보면서 진행내용을 보도록 하겠습니다

(저는 이미 설정이 되어있는 상태라 처음 하시는 분들은 약간 다를 수 있습니다)

---

콘솔에 `gcloud init`를 입력합니다, 저는 이미 설정을 해놓아서 또 다시 설정을 할건지 물어보는 화면이 나왔습니다

![](https://cdn-images-1.medium.com/max/2000/1*6NV3AKKcVou-d1z6hy9zdQ.png)

저는 default로 재설정을 위해서 `1`을 입력하여 진행하도록 하겠습니다

그럼 다음에 어떤 구글 계정하고 연결을 할 건지 물어보는데 새 연결을 위해서 `2`를 누르도록 하겠습니다


![](https://cdn-images-1.medium.com/max/2000/1*bWxYSGqciNEgcttJ0KMYgw.png)

그럼 Safari나 Chrome이 자동으로 실행되면서 어떤 계정과 연결할건지 아래 화면이 나오는데, 여기서 본인이 연결할 계정을 선택합니다

![](https://cdn-images-1.medium.com/max/1600/1*clnyX-etjwBCA94Ac_4ptw.png)

그럼 권한 관련해서 요청이 오는데 여기서 `허용`을 눌러서 GCP에서 필요한 권한을 허용합니다

![](https://cdn-images-1.medium.com/max/1200/1*If6P3usfhUxhvHMMSJparw.png)

그 다음은 어떤 프로젝트와 연결을 할건지 물어봅니다 보면, GCP 프로젝트 뿐 아니라 Firebase 프로젝트까지 전부 잡히게 됩니다

여기서 본인이 연결하고자 하는 프로젝트를 선택합니다

![](https://cdn-images-1.medium.com/max/2000/1*bWxYSGqciNEgcttJ0KMYgw.png)

그 다음 Compute Engine 설정을 할건지 물어보는데 이 부분은 넘어가도록 하겠습니다

![](https://cdn-images-1.medium.com/max/2000/1*iTQa3xD2IlJTf_UFUi--8g.png)

이렇게 하면 아래와 같이 문구가 나오면서 설정이 끝나게 됩니다

![](https://cdn-images-1.medium.com/max/2000/1*X3_-dL1c9fQMVKEqRbULDQ.png)

---

### 마무리

생각보다 간단하게 Google Cloud SDK 설치가 끝났는데요, 웹 콘솔에서 이용하셔도 되지만 몇가지 명령어를 익히면
생각보다 더 나은 생산성이 나올 수도 있기때문에 기회가 되면 한번 설치하는걸 추천합니다

관련해서 잘못된 정보가 있다면 언제든지 아래 댓글로 말씀 부탁드리겠습니다