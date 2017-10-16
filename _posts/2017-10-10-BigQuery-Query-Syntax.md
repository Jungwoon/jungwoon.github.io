---
layout: post
title: "BigQuery 기본 사용법"
image: '/assets/img/'
description: 'BigQuery 기본 사용법'
tags:
- BigQuery
- Google Cloud
categories:
- BigQuery
---


## Big Query 기본 사용법

Big Query에서 사용하는 문법은 크게 Legacy SQL와 Standard SQL이 있는데,
LegacySQL은 Big Query 초기부터 사용되던 문법이나 2.0 이후에 Standard SQL이 지원되었기 때문에,
여기서는 `Standard SQL` 기준으로 포스팅하도록 하겠습니다.

기본적으로 우리가 얄고있는 ANSI SQL과 엄청 크게 다르진 않아서, SQL에 대해 기본적으로 알고 있는 분들이라고 가정하고,
다른 부분 위주로 진행하도록 하겠습니다

(좀더 자세히 알고 싶으신 분들은 [구글 문서](https://cloud.google.com/bigquery/docs/reference/standard-sql/query-syntax#select-list)를 참조하시기 바랍니다)

Legacy SQL자세한 설명은 [변성윤님의 Github](https://github.com/zzsza/bigquery-tutorial/blob/master/tutorials/01-Basic/02.%20Query%20syntax%20(legacy%2C%20standard).ipynb)에
매우 자세히 나와있기에, 궁금하신 분은 한번 참고해보시면 좋을거 같습니다

---

### BigQuery Mate 설치

먼저 추천드리는 Chrome Extension이 있습니다

`Chrome`으로 [BigQuery Mate](https://chrome.google.com/webstore/detail/bigquery-mate/nepgdloeceldecnoaaegljlichnfognh)에 들어가서 설치를 해줍니다
그럼 자동으로 자동완성같은 부분이 지원이 되어서 문법적으로 틀릴일이 많이 줄어들 수 있습니다 

![](https://cdn-images-1.medium.com/max/2000/1*nbEXCeOEeX9_1rLBrpIwnA.png)

---

### 기본 구성

BigQuery를 열면 다음과 같은 화면을 볼 수 있습니다

여기서 좌측 상단의 `COMPOSE QUERY`를 열면 오른쪽에 Query 입력창을 볼 수 있습니다 이제 여기다가, 쿼리를 입력해서 실행을 시키면 됩니다

![](https://cdn-images-1.medium.com/max/1800/1*XAf8993NpfiQHTyoz33KVA.png)

- Project : 데이터를 담는 최상위 개념이며, 하나의 프로젝트에 여러개의 데이터셋이 들어갈 수 있습니다
- Dataset : RDB에서 Database의 개념이며, 하나의 Dataset에 여러개의 테이블이 들어갈 수 있습니다
- Table : RDB의 테이블과 동일한 개념입니다.
- Job : BigQuery에 내리는 모든 명령을 Job이라 합니다

---

### Standard SQL설정 하기

저희는 `Standard SQL`로 설정을 하기 위해 우측 하단의 `Show Options`를 클릭하여 옵션을 봅니다
그럼 아래와 같이 화면이 나올텐데, 아래 표시한 `Use Legacy SQL`의 체크를 해제해주면 `Standard SQL`을 사용할 수 있게 됩니다
밑에 공간을 차지하니 우측 하단에 `Hide Options`를 눌러서 옵션 화면을 숨깁니다

![](https://cdn-images-1.medium.com/max/2000/1*2je5NEOpT6YT0LtlFywcxQ.png)

여기까지 잘 따라왔으면 아래에 `Standard SQL Dialect` 이란 박스가 생긴것을 확인할 수 있습니다, 이제부터 `Standard SQL`를 사용할 수 있습니다

![](https://cdn-images-1.medium.com/max/2000/1*1tg1KkdTF0PMECDiIrdUKw.png)

---

### 쿼리를 날릴때의 기본 구성

예제로 대략적인 형태를 보여드리고 설명을 하도록 하겠습니다

![](https://cdn-images-1.medium.com/max/2000/1*v5IJjBXRsjrLvZBw3fbPRQ.png)

제가 제일 햇갈렸던 부분은 `FROM` 다음에 `사이에 프로젝트명, 데이터셋 테이블이름이 들어가는 문법이었습니다

아래는 Legacy SQL과 Standard SQL의 문법이 달라서 넣어봤습니다

| | Legacy SQL | Standard SQL
| :--- | :--- | :---
| 문법 | [프로젝트명:데이터셋.테이블명] | \`프로젝트명.데이터셋.테이블명` 
| 예시 | [jayden-project:bq_test.access_log] | \`jayden-project.bq_test.access_log`


```sql
SELECT *
FROM `프로젝트명.데이터셋.테이블이름` -- 프로젝트명 생략 가능
WHERE 조건
ORDER BY 컬럼
```

### BigQuery에서 지원하는 자료형

| 타입 | 이름 | 설명
| :---: | :---: | :---
| INTEGER | INT64 | 정수
| FLOAT | FLOAT64 | 실수
| BOOL | BOOL | 불 대수
| STRING | STRING | 문자열
| BYTES | BYTES | 바이트
| DATE | DATE | 포맷 : YYYY-[M]M-[D]D
| DATETIME | DATETIME | 포맷 : YYYY-[M]M-[D]D[( T)[H]H:[M]M:[S]S[.DDDDDD]]
| TIME | TIME | 포맷 : [H]H:[M]M:[S]S[.DDDDDD]
| TIMESTAMP | TIMESTAMP | 포맷 : YYYY-[M]M-[D]D[( T)[H]H:[M]M:[S]S[.DDDDDD]][time zone]
| ARRAY | ARRAY | 배열
| STRUCT | STRUCT | 포맷 : STRUCT\<T>


### 기본 문법

