---
layout: post
title: "구글 Cloud SQL 에 이모티콘 들어가게 설정하기"
image: '/assets/img/'
description: 'Enable Emojis in MySQL on Google Cloud'
tags:
- Google Cloud, MySQL
categories:
- Google Cloud, MySQL
---

정말 오랜만에 포스팅을 하는거 같은데요, 최근에 SNS에서 크롤링을 하여 SQL에 넣는 작업을 하다가 이모티콘때문에, 에러가 난적이 있어서
관련해서 이모티콘이 들어갈 수 있도록 설정하는 법을 포스팅하려고 합니다.

---

## 에러 내용

크롤링을 돌리다가 보니 다음과 같은 에러가 발생하였습니다.

```
1366, "Incorrect string value:
```

---

## 원인

원인은 이모티콘을 지원하게 하려면 `utf8mb4`로 설정해야 하는데, `euc-kr`이나 `utf-8`로 설정이 되어 있어서 그렇습니다.

---


## 해결방법 - 쿼리로 설정하기

심플하게 `utf8mb4`로 설정을 하면 됩니다

우선 아래 내용을 쿼리로 보냅니다.

```sql
# Database에 설정 바꾸기
ALTER DATABASE <database_name> 
CHARACTER SET = utf8mb4 
COLLATE = utf8mb4_unicode_ci;


# Table에 설정 바꾸기
ALTER TABLE <table_name> CONVERT TO 
CHARACTER SET utf8mb4 
COLLATE utf8mb4_unicode_ci;


# 개별 Column에 설정 바꾸기
# 모든 varchar 컬럼은 일관성 유지를 위해서 다 바꿔야 합니다.
ALTER TABLE <table_name>
  CHANGE <varchar_col1> <varchar_col1> VARCHAR(1024) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci,
  CHANGE <varchar_col2> <varchar_col2> VARCHAR(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```
---

## 해결방법 - Cloud SQL의 characterset 변경하기

먼저 [구글 클라우드 콘솔](https://console.cloud.google.com/) 에 들어가서 `메뉴 - SQL`을 누릅니다.

![](https://cdn-images-1.medium.com/max/800/1*CuJsYCO2h4QWSNhlvtz-pw.png)

바꾸고자 하는 인스턴스를 클릭하여 들어갑니다.

![](https://cdn-images-1.medium.com/max/800/1*3rJzSc-DpbAE2ZnquQRYmw.png)

상단의 `수정` 버튼을 누릅니다.

![](https://cdn-images-1.medium.com/max/800/1*s458O5yA64jo1NEo5CXKJw.png)

그럼 여러가지 설정 메뉴가 나오는데 스크롤을 내려서 `구성 옵션 - 데이터베이스 플래그 추가`를 누릅니다.

![](https://cdn-images-1.medium.com/max/800/1*Nr_dRbJRSX4Cg2IN0FtkQQ.png)

그럼 아래와 같이 나오는데 `+ 항목 추가`를 누릅니다.

![](https://cdn-images-1.medium.com/max/800/1*EUMzoVFdZiid-L1Q1jos8g.png)

아래 그림과 같이 `character_set_server`를 `utf8mb4`로 변경하고 하단의 `저장`을 누릅니다.

![](https://cdn-images-1.medium.com/max/800/1*EzPlLEeoRC7KhxJYMkNYEg.png)

그 다음 `다시 시작` 버튼을 눌러서 인스턴스를 다시 시작하면 적용이 됩니다.

![](https://cdn-images-1.medium.com/max/800/1*qs_ojIxCQVRTlCxtL6SpJA.png)

그럼 아래와 같이 이모티콘들이 잘 들어가는걸 확인할 수 있습니다.

![](https://cdn-images-1.medium.com/max/1200/1*HKBlZLDjiinLeK5QR6xHqw.png)

