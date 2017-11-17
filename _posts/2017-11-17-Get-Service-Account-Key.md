---
layout: post
title: "Google Cloud 서비스 계정키 얻기 및 GCS 공유하기"
image: '/assets/img/'
description: 'Google Cloud 서비스 계정키 얻기 및 GCS 공유하기'
tags:
- Google Cloud
categories:
- Google Cloud
---


## Google Cloud 서비스 계정키 얻기

Google Cloud API를 이용하려면 `서비스 계정 키`가 필요하기 때문에, 이를 생성하는 방법과 공유 하는 방법에 대해서
공유하려고 합니다

바로 실습으로 가도록 하겠습니다

### 실습

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

#### 로컬로 다운받아진 서비스 계정 키 GCS를 통해서 Compute Engine에서 받기

위에서 서비스 계정키를 받으면 로컬로 다운로드가 되는데, 이를 GCS를 이용하여 처음에 만든 Compute Engine으로 넘기도록 하겠습니다

[Google Cloud Console](http://console.cloud.google.com)에서 `메뉴 - Storage`을 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*8BSISu9jyfVNfgcVyF32dw.png)

아래와 가팅 나오면 `버킷 생성`을 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*oDj9sG0nEJQUKxfr-DD71Q.png)

임의의 이름을 넣고 `Regional`을 누른뒤에 `만들기` 버튼을 누릅니다

![](https://cdn-images-1.medium.com/max/1600/1*Ui45VI3WQDDACB4H39zCRg.png)

그럼 다음과 같이 버킷이 생성이 됩니다

버킷의 형태는 `gs://my-key-byjw/`와 같이 됩니다

![](https://cdn-images-1.medium.com/max/1200/1*WbM_xMi_8j1e3ZUfy6qmww.png)

이제 아까 다운로드 받아진 디렉토리로 가서 아래 명령어를 통해 `서비스 계정 키`를 위의 버킷(=`gs://my-key-byjw/`)로 보냅니다

`$ gsutil cp beer-coding-b001cdb46cef.json gs://my-key-byjw/`

(gsutil에 대한 자세한 내용을 알고 싶으면 [Google Cloud Storage의 gsutil 명령어 살펴보기](https://jungwoon.github.io/bigquery/2017/10/28/gcs-gsutil/)를 참고하세요)

![](https://cdn-images-1.medium.com/max/2000/1*xlhLTbcQ1Paj8KHufqwWyA.png)

위의 명령어가 완료가 되면 좀전에 만들어진 버킷에 키가 복사가 된것을 확인할 수 있습니다

![](https://cdn-images-1.medium.com/max/2000/1*j0cQ5Om5ZO5CZh5LC64TJA.png)

이번에는 Compute Engine의 인스턴스의 `홈 디렉토리`에서 아래 명령어를 입력하여 버킷에 있는 키를 받습니다

`$ gsutil cp gs://my-key-byjw/beer-coding-b001cdb46cef.json .`

![](https://cdn-images-1.medium.com/max/2000/1*mxKbd5sowOYxnwNT9jZoPg.png)

그 다음 해당 키의 경로를 확인해뒀다가 아래 `td-agent.conf` 파일에 입력하도록 합니다

![](https://cdn-images-1.medium.com/max/1200/1*ZORWDug3gCiOUIZlv5ApXw.png)