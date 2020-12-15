---
layout: post
title: "터미널로 구글 클라우드 접속하기"
image: '/assets/img/'
description: '터미널로 구글 클라우드 접속하기'
tags:
- Google Cloud
categories:
- Google Cloud
---

터미널을 이용해서 구글 클라우드에 접속하느 방법에 대해서 알아보겠습니다.

1. SSH Key 생성

![](https://cdn-images-1.medium.com/max/2000/1*CIa5FaT6dYU2bUNZHPxfWA.png)

2. 복사할 pub(공개키)를 복사

![](https://cdn-images-1.medium.com/max/2000/1*-h6MX0J0GCBFLfiUmMM2LQ.png)

3. [Google Cloud](http://cloud.google.com)에 들어가서 메타데이터로 들어간다

![](https://cdn-images-1.medium.com/max/2000/1*cUtYaSAfyrg5weM1j_KSHQ.png)

4. 2번에서 복사한 공개키를 붙여넣고 저장을 누른다

![](https://cdn-images-1.medium.com/max/2000/1*DeHOR0FNbdw5sup7AbzhCw.png)

5. 그럼 아래와 같이 들어간것을 확인할 수 있다

![](https://cdn-images-1.medium.com/max/2000/1*XghRGfWGHEAGG54_w82_Iw.png)

6. 그 다음 터미널로 들어가서 `ssh -i [SSH Key 위치] [Google Cloud ID]@[Google Cloud IP]` 를 입력하면 접속이 됩니다

![](https://cdn-images-1.medium.com/max/2000/1*Ej0GfI7L_EctuCX4MAqaTA.png)

