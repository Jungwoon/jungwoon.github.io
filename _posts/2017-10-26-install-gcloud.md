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

## Google Cloud SDK(=gcloud) 설치하기

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

