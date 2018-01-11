---
layout: post
title: "Google Application Default Credentials 사용하기"
image: '/assets/img/'
description: 'Google Application Default Credentials 사용하기'
tags:
- Google Cloud
categories:
- Google Cloud
---


## Google Application Default Credentials 사용하기

이번에는 구글 클라우드를 이용하여 Client Library를 이용하는 서비스키를 default로 설정하는 방법에 대해서
공유 드리려고 합니다.

예전에 이거때문에 삽질을 많이했었던 기억이 있어서 도움이 되지 싶네요 자세한 부분은 
[공식 문서](https://developers.google.com/identity/protocols/application-default-credentials)를 참고해주세요

---

### gcloud sdk 설치 후 인증받기

우선 [Google Cloud SDK(=gcloud) 설치하기](https://jungwoon.github.io/google%20cloud/2017/10/26/install-gcloud/)를
참고하여 gcloud 설치를 먼저 해줍니다


그 다음 `gcloud auth login`을 터미널에 입력을 해줍니다 

![](https://cdn-images-1.medium.com/max/600/1*O1KuxZdZw_KBfmxunh2yWg.png)

그럼 아래와 같이 브라우저가 뜨면서 구글 계정을 선택하는 화면이 나오는데, 해당 프로젝트 계정을 클릭합니다

![](https://cdn-images-1.medium.com/max/2000/1*7RKsET0X_n7kK6kWQ1CYQQ.png)

그럼 다음과 같이 권한 요청이 나오는데, `허용`을 누릅니다

![](https://cdn-images-1.medium.com/max/1600/1*vcKxHlphpBcUkA6BuvzRBg.png)

---

#### 사용할 서비스 계정키 얻기

GCP 서비스를 이용하기 위한 서비스 계정 키를 얻기 위해 [Google Cloud Console](http://console.cloud.google.com)에서
 `메뉴 - API 및 서비스 - 서비스 계정`을 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*4-ZoTgS8yilA_0Gskcg_Yg.png)

아래와 같이 나오면 우측에 메뉴를 누르고 `키 만들기`를 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*VsgBvxgCVepRa4JuxOyo9A.png)

그럼 아래와 같이 `JSON` 또는 `P12`를 선택하라고 하는데 여기서는 JSON을 선택하고 `생성` 버튼을 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*PDkxgzEXHL2Dczop6thgqw.png)

그럼 자동으로 Local로 다운로드 받아지게 됩니다

`(한번밖에 다운로드 되지 않기 때문에 잘 보관해야 합니다)`

![](https://cdn-images-1.medium.com/max/1200/1*9J42lsbpxj98C2lS3MOPtQ.png)

---

### 서비스 계정키를 다운 받아서 .bashrc에 등록하기 및 사용설정하기

GCP 서비스를 이용하기 위한 서비스 계정 키를 얻기 위해 [Google Cloud Console](http://console.cloud.google.com)에서
 `메뉴 - API 및 서비스 - 서비스 계정`을 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*4-ZoTgS8yilA_0Gskcg_Yg.png)

아래와 같이 나오면 우측에 메뉴를 누르고 `키 만들기`를 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*VsgBvxgCVepRa4JuxOyo9A.png)

그럼 아래와 같이 `JSON` 또는 `P12`를 선택하라고 하는데 여기서는 JSON을 선택하고 `생성` 버튼을 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*PDkxgzEXHL2Dczop6thgqw.png)

그럼 자동으로 Local로 다운로드 받아지게 됩니다

`(한번밖에 다운로드 되지 않기 때문에 잘 보관해야 합니다)`

![](https://cdn-images-1.medium.com/max/1200/1*9J42lsbpxj98C2lS3MOPtQ.png)

그 다음 `vi ~/.bashrc`를 입력을 합니다. (다른 쉘을 쓰면 거기에 맞는걸 열어줍니다)

![](https://cdn-images-1.medium.com/max/2000/1*QumrcEfuwN2NPo_nnxM_Yw.png)

아래의 105번 라인과 같이 위에서 다운받은 서비스 계정 키의 경로를 `GOOGLE_APPLICATION_CREDENTIALS`에 넣어줍니다

![](https://cdn-images-1.medium.com/max/2000/1*E0fpt06SLamFdXSakReqnQ.png)

```
## Google Cloud
export GOOGLE_CLOUD_SDK_PATH=/Users/jungwoon/google-cloud-sdk
export PATH=$PATH:$GOOGLE_CLOUD_SDK_PATH/bin
export GOOGLE_APPLICATION_CREDENTIALS=/Users/jungwoon/GoogleCredential/bigquerybyjw.json
```

그 다음 터미널에서 `$ gcloud auth application-default login`을 해주어야 합니다.

![](https://cdn-images-1.medium.com/max/800/1*WdGVW0xeXmda4UijQGPSPg.png)

그럼 아래와 같이 나오는데 여기서 `Y`를 눌러서 진행을 합니다.

![](https://cdn-images-1.medium.com/max/1200/1*JP_MMkFHJA0tMuu1PBjfuA.png)

그 다음 브로우저가 열리면서 어떤 계정에 연결을 할건지에 대해서 나오는데, 연결하고자 하는 계정을 클릭합니다.

![](https://cdn-images-1.medium.com/max/1200/1*iG56mP8w2EKHFiVbzqOiOg.png)

마지막으로 아래와 같은 화면에서 허용을 해주어야 사용할 수 있게 됩니다.

![](https://cdn-images-1.medium.com/max/1000/1*DiQZv3P_SrVSn0mGZnFgdw.png)

---