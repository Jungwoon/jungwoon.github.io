---
layout: post
title: "Google BigQuery에 CSV 파일 업로드 하기"
image: '/assets/img/'
description: 'Google BigQuery에 CSV 파일 업로드 하기'
tags:
- Google Cloud
- BigQuery
categories:
- BigQuery
---


## BigQuery

이번에는 BigQuery에 CSV 파일을 업로드 하는 방법을 살펴 보도록 하겠습니다 

---

### 실습

[구글 클라우드 콘솔](https://console.cloud.google.com)에 들어갑니다

![](https://cdn-images-1.medium.com/max/2000/1*zjj5jR9EuSsuUB1vanUDlw.png)

메뉴에서 `BigQuery`를 찾아 클릭합니다

![](https://cdn-images-1.medium.com/max/800/1*WogRtV2q6c1oyzviP6cG1A.png)

우선 테스트를 위해서 일반 `database`에 해당하는 `dataset`을 만듭니다

(화살표를 누르면 다음과 같이 뜹니다)

![](https://cdn-images-1.medium.com/max/2000/1*nxR1aeyhQKsg9QTAySYEUg.png)

이름을 넣고, `Data expiration` 등도 선택할 수 있습니다.

![](https://cdn-images-1.medium.com/max/2000/1*nQLS5La7vRPFtWP7XqL3xQ.png)

`ecommerce`라는 `dataset`이 만들어 졌으니 테이블을 만들기 위해서 `+`버튼을 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*uLdcmkuBbR2gh5jjWBVy8w.png)

그럼 다음과 같은 화면이 나오는데 아래 상자안에 있는 설정을 통해서 CSV 파일을 읽어올 수 있습니다 

`choose file`을 눌러서 컴퓨터에 저장된 `csv파일`을 가져오겠습니다

![](https://cdn-images-1.medium.com/max/2000/1*vO8PcWPozVCnIhLCkLLDqQ.png)

여기서는 purchase_data를 가져와 보겠습니다

![](https://cdn-images-1.medium.com/max/2000/1*XbEiqQgzqYEycPGO-lJOIA.png)

사용할 데이터는 다음과 같이 생겼습니다

![](https://cdn-images-1.medium.com/max/2000/1*P5J8bDEW9juU_-JcWoERig.png)

먼저 `Table name`을 넣어주고 여기서는 `purchase`로 넣어주겠습니다

그 다음 스키마를 지정해주는 부분이 나오는데, 여기서는 `Automatically detect`를 이용한 자동으로 스키마 가져오는 방법도 있고,

직접 넣어주는 방법도 있습니다. 자신의 데이터에 맞는 방법으로 넣어줍니다

![](https://cdn-images-1.medium.com/max/2000/1*2awWKN04R5GZ2bOZRmbxiQ.png)

만약 첫줄에 컬럼명이 있는경우 무시하기 위해서 `Header rows to skip`을 설정해줍니다

그 다음 `Create Table`을 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*anH5JJ1rlSnVyHjGmPPbZw.png)

그럼 다음과 같이 Table 생성을 위한 `job`이 나옵니다

(저는 초기 스키마 설정을 몇번 잘못해 아래에 실패 `job`들이 보입니다)

![](https://cdn-images-1.medium.com/max/2000/1*ZjmhS87NQ2J876n-E1H8GA.png)

그 다음 쿼리를 날려서 실제 데이터가 잘 들어갔는지 확인을 해 봅니다

![](https://cdn-images-1.medium.com/max/2000/1*kbDqABgCO535MHaL5bzNDw.png)

---

### 마무리

실제 `CSV 파일`로 데이터를 가지고 있거나 한 경우에 엑셀로 돌리기 무거운 경우 이렇게 손쉽게 `BigQuery`에 올려서 데이터를 손쉽게 핸들링 할 수 있습니다.

