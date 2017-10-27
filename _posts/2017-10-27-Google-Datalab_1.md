---
layout: post
title: "Google Cloud에서 Datalab 사용하기 #1"
image: '/assets/img/'
description: 'Google Cloud에서 Datalab 생성하기'
tags:
- Google cloud
- Datalab
categories:
- Google Cloud
---


## Google Cloud에서 Datalab 사용하기

Google Cloud 환경에서 제공하는 Datalab은 Jupyter Notebook과 같은 솔루션을 제공합니다

이제 더이상 Jupyter Notebook을 사용하기 위해서 Python 환경 구축을 할 필요 없이 간단하게 Datalab을
사용할 수 있습니다

Jupyter는 웹 어플리케이션으로 라이브 코드, 등식, 시각화와 같은 기능들을 제공하며 주로 데이터 클리닝,
변형 및 수치 시뮬레이션, 통계 모델링 및 머신 러닝에 사용할 수 있습니다
   
---

### 명령어

```
$ datalab create [datalab-instance-name]  # Datalab을 위한 VM 생성
$ datalab connect [datalab-instance-name] # VM이 생성이 되어 있을때 Datalab을 실행시키는 명령어
$ datalab delete [datalab-instance-name]  # VM 지우기
```

---

### 실습

먼저 [Google Cloud](https://console.cloud.google.com) 에 들어갑니다

상단의 `>_` 모양의 아이콘을 열어서 Cloud Shell을 엽니다

![](https://cdn-images-1.medium.com/max/1600/1*qA_wlV6KNhKoOC-qiW31hg.png)

그럼 아래 이미지와 같이 하단(=default)에 쉘을 뜹니다 

![](https://cdn-images-1.medium.com/max/1400/1*FgVgdZm1ESee6p8-Jhm-fg.png)

그럼 이제 Datalab을 실행시키기 위한 VM을 생성하겠습니다

아래 명령어를 입력하고, 엔터를 누릅니다 

```
$ datalab create my-datalab # 여기서는 인스턴스 이름을 my-datalab을 줬습니다
```

![](https://cdn-images-1.medium.com/max/1200/1*hY3xUM-MSQkgywtQCEcL5g.png)

그럼 아래 그림과 같이 관련된 설명이 나오고 `y`를 눌러서 진행합니다

![](https://cdn-images-1.medium.com/max/1800/1*q7N_qs0-TVfIuk4WUEDNYA.png)

그럼 VM을 만들 Zone을 선택하라고 하는데 원하는 위치의 Zone을 선택하고 진행합니다 

![](https://cdn-images-1.medium.com/max/800/1*1C5dTQ3EU-X3F9CiBmQ3ng.png)

그리고 좀 지나면 아래와 같이 `my-datalab`이라는 이름으로 새로운 VM 이 생긴걸 확인할 수 있습니다

여기서 좀 더 기다립니다

![](https://cdn-images-1.medium.com/max/1400/1*r1HgIJ9LJyfLiKy52gsVOw.png)

그럼 아래와 같이 메시지가 나오고 여기서 `http://localhost:8081/` 를 누릅니다

![](https://cdn-images-1.medium.com/max/1600/1*Kamd5YyN1T-pJM31GdXYRg.png)

그럼 아래와 같이 새로운 탭이 열리면서 Datalab이 실행되는걸 확인할 수 있습니다

![](https://cdn-images-1.medium.com/max/1600/1*mr86C4C5EcIIEFRB9Rs0lg.png)

---

### 마무리

이 이후에 이제 실제로 Datalab을 이용하는 방법에 대해서도 포스팅할 예정입니다.

만약 위의 내용 중 틀린 부분이 있으면 언제든지 아래 댓글 남겨주세요 감사합니다