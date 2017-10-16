---
layout: post
title: "Embulk 공통 설정 외부로 분리하기"
image: '/assets/img/'
description: 'Embulk 공통 설정 외부로 분리하기'
tags:
- BigQuery
categories:
- BigQuery
---


## Embulk 공통 설정 외부로 분리하기

Embulk 설정을 하다가 보면, 데이터베이터 설정이나, 보안 관련 키 같은 공통 된 설정들이, 자주 보일겁니다

비슷한 YML 파일들을 계속 만들다 보면 공통된 부분을 빼고 싶다고 생각이 들텐데요, YML에서 Liquid란 기능을 이용해서,
해당 부분을 분리할 수 있습니다

---

### 실습

아래는 MySQL -> BigQuery로 데이터를 로드해주는 yml 설정 파일입니다. 보면 빨간색 선으로 만든 상자 부분은, 설정 부분이라
나중에 동일한 MySQL에서 동일한 BigQuery로 넘겨준다면 매번 반복을 하게 됩니다, 그래서 빨간색 부분을 따로 파일로 빼도록 하겠습니다

![](https://cdn-images-1.medium.com/max/2000/1*kwj-ieFTFQeLY00sm7RskA.png)

Source)

```
in:
    type: mysql
    host: 111.111.111.111
    port: 3306
    user: 아이디
    password: 비밀번호
    database: study_data
    table: customers
    select: "cid,
        end_at,
        id,
        date_add(start_at, interval 9 hour) as start_at,
        state,
        used_at,
        zid"
    where: "start_at >= '2017-02-01 00:00:00'
        and start_at < '__range_stop__ 00:00:00'"
out:
    type: bigquery
    mode: replace
    auth_method: private_key
    service_account_email: byjungwoon@jayden-project.iam.gserviceaccount.com
    p12_keyfile: /Users/jungwoon/GoogleCredential/key.p12
    project: jayden-project
    gcs_bucket: byjungwoon-byjw
    dataset: ods_partition
    table: customers$20170201
    auto_create_gcs_bucket: true
    auto_create_table: true
    auto_create_dataset: true
    ignore_unknown_values: true
    allow_quoted_newlines: true
```

먼저 `in`이란 디렉토리를 만들고 그 안에 `in:`에 해당하는 공통 설정을 빼도록 하겠습니다
`in` 부분에 해당하는 공통된 설정파일은 이 안에 넣도록 하겠습니다

```
    형태 : _이름.yml.liquid        # 앞에 '_'와 뒤에 '.yml.liquid' 부분이 중요합니다!! 
    예제 파일 이름 : _mysql.yml.liquid
```

![](https://cdn-images-1.medium.com/max/2000/1*7BCd0qrerLczlAqtp2mPGw.png)

Source)

```
    type: mysql
    host: 111.111.111.111
    port: 3306
    user: 아이디
    password: 비밀번호
    database: study_data
```

그 다음 `out`이란 디렉토리를 만들고 그 안에 `in:`에 해당하는 공통 설정을 빼도록 하겠습니다
`out` 부분에 해당하는 공통된 설정파일은 이 안에 넣도록 하겠습니다

```
    형태 : _이름.yml.liquid        # 앞에 '_'와 뒤에 '.yml.liquid' 부분이 중요합니다!! 
    예제 파일 이름 : _bigquery.yml.liquid
```

![](https://cdn-images-1.medium.com/max/2000/1*ZzMyUL1BCkI-Ps8aoMfTVg.png)

Source)

```
    type: bigquery
    mode: replace
    auth_method: private_key
    service_account_email: byjungwoon@jayden-project.iam.gserviceaccount.com
    p12_keyfile: /Users/jungwoon/GoogleCredential/key.p12
    project: jayden-project
    gcs_bucket: byjungwoon-byjw
    dataset: ods_partition
    auto_create_gcs_bucket: true
    auto_create_table: true
    auto_create_dataset: true
    ignore_unknown_values: true
    allow_quoted_newlines: true
```


그럼 원래 설정 파일의 공통으로 빠진 부분을 지우고 `{% include '경로 %}` 를 아래와 같이 넣어줍니다

(** 대신 include 하는 파일명이 `_mysql.yml.liquid`이면 `_`뒤에 `이름(=mysql)`만 넣어줍니다. `_`와 `.yml.liquid` 생략 합니다)

![](https://cdn-images-1.medium.com/max/2000/1*XSTzYH7ZakxTZdYHwqHecw.png)

그렇게 한 다음 같이 명령어를 똑같이 써서 실행 하면 됩니다.

```
$ embulk run liquid 파일명

ex) embulk run cfg_transform.yml.liquid
``` 

---

### 마무리

이렇게 공통된 부분을 빼게 됨으로써 비슷한 형태의 새로운 *.yml 을 만들때 많은 부분을 생략할 수도 있고,
비밀번호 같은 부분을 노출시키지 않을수도 있습니다. 

관련해서 틀린 부분이 있으시면 언제든지 말씀 부탁 드리겠습니다.