---
layout: post
title: "Google Cloud Platform 제품들 살펴보기"
image: '/assets/img/'
description: 'Google Cloud Platform 제품들 살펴보기'
tags:
- Google Cloud
categories:
- Google Cloud
---

## Google Cloud Platform 제품들 살펴보기

이번 5월부터 [Google Cloud Professional Data Engineer](https://cloud.google.com/certification/data-engineer)을
취득하기 위해 준비하면서 공부하는 내용들을 틈틈히 포스팅 해보려고 합니다.

오늘 포스팅할 내용은 [Coursera의 Google Cloud Platform Fundamentals: Core Infrastructure](https://www.coursera.org/learn/gcp-fundamentals/home/welcome)
의 내용으로 전반적으로 `Google Cloud Platform 제품들`에 대한 간단한 소개를 하려고 합니다.

예전에 포스팅한 내용도 있고 새로 포스팅하는 내용도 있을 예정입니다.

---

### GCP 계층 구조

![](https://cdn-images-1.medium.com/max/800/1*_I8qdTjR7FlEWaBiH9QJSg.png)

![](https://cloud.google.com/resource-manager/img/cloud-folders-hierarchy.png)

GCP는 다음과 같은 계층 구조를 가질 수 있습니다.

기본적으로 권한이나 정책등은 계층구조에서 위에서 아래로 상속됩니다.

- Organization Node : 회사 단위
    - Folders : 부서별, 팀별 단위
        - Projects : 실제 프로젝트 및 서비스에 해당 (`과금 단위`)
            - Resources : 프로젝트에 속해있는 GCP의 서비스들


기본적으로 GCP는 `Project` 단위로 작업을 하게 됩니다.

| 속성 | 특징 | 지정 | 변경 가능성
| :--- | :--- | :--- | :---
| `Project ID` | 반드시 유니크 해야함	| 직접 선택 가능 | 변경 불가
| `Project name` | 반드시 유니크 할 필요없음 | 직접 선택 가능 | 변경 가능
| `Project number` | 반드시 유니크 해야 함	| GCP가 지정 | 변경 불가


### IAM

![](https://cdn-images-1.medium.com/max/800/1*VHaUVh7-BEd4_kLfAduXxg.png)

구글 내부의 자원에 대해서 권한을 줄 수 있는 기능입니다.


IAM에서 정할 수 있는 것들
- Who
    - 구글 계정
    - 구글 그룹
    - 서비스 계정
    - G Suite 도메인
- can do What
- on which resource

---

IAM의 역할

- Owner
    - 결제
    - 멤버 초대
    - 멤버 삭제
    - 프로젝트 삭제
    - 어플리케이션 배포
    - 코드 수정
    - 서비스 수정
    - 읽기 권한
- Editor
    - 어플리케이션 배포
    - 코드 수정
    - 서비스 수정
    - 읽기 권한
- Viewer
    - 읽기 권한


---

### GCP와 상호 작용 가능한 방법들

![](https://cdn-images-1.medium.com/max/800/1*AJvrK69t_gT_YSwakhmbrg.png)

1. Cloud Platform Console (`console.cloud.google.com`)
2. Cloud Shell and Cloud SDK (`gcloud명령어`)
3. Mobile App
4. Rest-based api

---

### Cloud Launcher

![](https://cdn-images-1.medium.com/max/1600/1*jb2shy3807JcMfFZDaZArA.png)

미리 설정이 된 이미지들을 바로 이용할 수 있는 서비스

---

### VPC

![](https://cdn-images-1.medium.com/max/1200/1*EEJi_eeP8YtUuP948bJRmg.png)

GCP 리소스들을 서로 연결하고 인터넷에 연결을 할 수 있도록 도와주며, 심지어, 서로 다른 zone
이라도 논리적으로 묶을 수 있습니다.

---

### Compute Engine

![](https://cdn-images-1.medium.com/max/800/1*1yscw5fw8YVm6FfH8bv04w.png)

GCP의 대표적인 VM 서비스

- 가상 CPU를 달 수 있는 최대 개수 : 96개
- 최대 메모리 크기 : 624 GB

---

### Cloud Load Balancing

![](https://cdn-images-1.medium.com/max/800/1*yTn1kPh1lNWWsvSJ74QY-w.png)

GCP이 로드밸런싱 서비스

---

### Cloud DNS

![](https://cdn-images-1.medium.com/max/800/1*UulFWIukPs1ZGjkVjuoYgQ.png)

구글의 대표적인 DNS  tjqltm

---

### Cloud Storage

![](https://cdn-images-1.medium.com/max/800/1*PdQ84MapbS783g275h1SZg.png)

구글 클라우드의의 대표적인 Block Store 형태의 저장소로, 크게 아래 4가지 데이터 형태에 맞춰서 정할 수 있습니다.

| | Multi-regional | Regional | Nearline | Coldline
| :--- | :--- | :--- | :--- | :---
| `데이터 특징` | 가장 자주 접근하는 데이터에 유용	| 지역내 자주 접근하는 데이터에 유용| 한달에 한 번 미만으로 접근하는 데이터에 유용 | 연간 한 번 미만으로 접근하는 데이터에 유용
| `SLA 이용성` | 99.95% | 99.90% | 99.00% | 99.00%
| `저장 비용` | 가장 큼	| 큼 | 보통 | 저렴
| `검색 비용` | 저렴 | 보통 | 큼 | 가장 큼
| `적합한 케이스` | 컨텐츠 저장 및 전달 | 지역내 분석 및 변경 | 장기 컨텐츠, 백업 | 아카이빙, 재난 방지용

---

### Cloud Bigtable

![](https://cdn-images-1.medium.com/max/800/1*SsYwpkfPEuY_UdTIl1WQrA.png)

GCP의 대표적인 NoSQL with wide column 서비스

- HBase와 호환됩니다.
- 전 세게적인 가용성을 지원합니다.
- 오토 스케일링 및 중복 저장소를 지원합니다.

---

### Cloud Datastore

![](https://cdn-images-1.medium.com/max/800/1*ALPmOcGfSj5IuZUCv-vpMQ.png)

GCP의 대표적인 NoSQL 서비스

- 정수, 부동 소수점 수, 문자열, 날짜, 바이너리 데이터등 다양한 데이터 유형을 지원합니다.
- ACID 트랜잭션을 지원하여 무결성을 보장합니다.

---

### Cloud SQL

![](https://cdn-images-1.medium.com/max/800/1*LHNuNmXyQP9JAl8GFYLOIw.png)

GCP에서의 MySQL과 PostgreSQL을 지원

---

### Cloud Spanner

![](https://cdn-images-1.medium.com/max/800/1*ctm1yi4tvIUFYAvg2c0X4Q.png)

글로벌 분산형 데이터베이스 서비스로, 관계형 데이터베이스 구조와 비관계형 수평적 확장의 이점을 결합한 서비스입니다.

- 대륙 단위를 넘어서는 확장성을 지원합니다.
- 모든 규모 및 단계에서 쉽게 배포할 수 있습니다.

---

### BigQuery

![](https://cdn-images-1.medium.com/max/800/1*OiI1R8HudMeWaX6HIUWnrw.png)

- 표준SQL을 지원합니다.
- 장기간 이용시 자동으로 할인이 됩니다
- 스트리밍 API를 이용하여 실시간 분석이 가능합니다.
- 페타바이터급 규모의 데이터를 처리할 수 있습니다.
- 자동 백업 및 간편한 복원이 가능합니다.
- 다른 GCP 서비스들과 쉽게 연동이 됩니다.

---

### 개별 스토리지 비교 (technical details)

| | Cloud Datastore | Bigtable | Cloud Storage | Cloud SQL | Cloud Spanner | Big Query
| :--- | :--- | :--- | :--- | :--- | :---
| Type | NoSQL | NoSQL with column | Blockstore | 관계형 SQL for OLTP | 관계형 SQL for OLTP | 관계형 SQL for OLAP
| Transactions | Yes | Single-row | No | Yes | Yes | Yes
| Complex queries | No | No | No | Yes | Yes | Yes
| Capacity | TB+ | PB+ | PB+ | TB | PB | PB+
| Unit Size | 1MB/entity | ~10MB/cell, ~100MB/row | 5TB/object | DB Engine에 따라 달라짐 | 10,240MiB/row | 10MB/row

---

### 개별 스토리지 비교 (use cases)

| | Cloud Datastore | Bigtable | Cloud Storage | Cloud SQL | Cloud Spanner | Big Query
| :--- | :--- | :--- | :--- | :--- | :---
| Type | NoSQL | NoSQL with column | Blockstore | 관계형 SQL for OLTP | 관계형 SQL for OLTP | 관계형 SQL for OLAP
| Best for | 반 정형화된 어프리케이션 데이터, 영속성 있는 키-밸류 데이터 | `Flat` 데이터, 큰 규모의 읽기/쓰기, 이벤트, 분석 데이터 | 여러 형태의 파일들 | 웹 프레임워크, 어플리케이션 | 큰 규모의 데이터베이스 어플리케이션 | 쿼리를 이용한 데이터 분석
| Use cases | App Engine 어플리케이션 | 광고, 금융 및 IoT 데이터 | 이미지, 미디어 파일, 백업 | 사용자 신용정보, 고객 주문 | 글로벌 규모의 큰 데이터 처리 | 데이터 웨어하우스

---

### Container Engine

![](https://cdn-images-1.medium.com/max/800/1*G-WLqqRZ1_WsqfNzwXCvVA.png)

GCP의 대표적인 컨테이너 서비스


---

### Kubernetes

![](https://cdn-images-1.medium.com/max/2000/1*f6RGkBXdwtU0L4NJmUXpYA.png)

컨테이너 관리 시스템

---

### App Engine

![](https://cdn-images-1.medium.com/max/800/1*bFUt980HM9yw4fJLsLCyiw.png)

웹 어플리케이션 서비

---

### Cloud Dataproc

![](https://cdn-images-1.medium.com/max/800/1*Iw4Iggm5s1qVaKaZeIpPBQ.png)

GCP에서 돌아가는 Hadoop 서비스

- 빠르고 쉽게 Hadoop / Spark / Pig를 GCP 위에서 관리할 수 있습니다.
- Cluster를 생성하는데 평균 90초 안으로 걸립니다.
- Cluster의 스케일 업/다운이 job이 돌아가면서도 가능합니다.

---

### Cloud Dataflow

![](https://cdn-images-1.medium.com/max/800/1*bmtCMeQ4VsP6Ng5z1P2J8Q.png)

파이프를 이용하여 스트리밍 및 배치 데이터를 처리할 수 있는 서비스

- Compute Engine 인스턴스를 이용하여 데이터를 처리
- 오토 스케일링
- ETL(Extract/Transform/Load) 파이프라인을 이용하여 쉽게 이동, 필터등의 작업을 할 수 있습니다.
- 스트리밍 데이터 처리시 유용하게 사용할 수 있습니다.
- 다른 GCP 서비스들과의 통합하여 사용이 가능합니다.

---

### Cloud Pub/Sub

![](https://cdn-images-1.medium.com/max/800/1*J8X6glMHbNufk_ry9VCa6Q.png)

Kafka와 같은 대표적인 메세지 큐잉 서비스

- 수많은 비동기 메시지들을 지원
- 오프라인 소비자들도 포함하여 지원

---

### Cloud Datalab

![](https://cdn-images-1.medium.com/max/800/1*2eR2xC2dswrokXapS3s6DA.png)

GCP의 대표적인 Jupyter 서비스

- 큰 데이터의 처리, 변형, 분석 및 시각화 등에 사용
- 오픈소스인 Jupyter 빌트인 서비스
- 별다른 환경 설정없이 쉽게 구글 서비스들과 연동하여 사용할 수 있습니다.

---

### Cloud Vision API

![](https://cdn-images-1.medium.com/max/800/1*oRViU_5mfuI_j1FUVBCP0A.png)

이미지 분석 서비스

- 이미지 내의 객체를 감지할 수 있습니다.
- 부적절한 컨텐츠(성인물, 폭력물 등)를 감지할 수 있습니다
- OCR(이미지내의 텍스트 추출)이 지원됩니다
- 라벨을 감지할 수 있습니다.
- 로고를 감지할 수 있습니다.
- 랜드마크를 감지할 수 있습니다.

---

### Cloud Speech API

![](https://cdn-images-1.medium.com/max/800/1*XXNpJJUXY4NFCXcrW8X3Qw.png)

음성 분석 서비스

- 음성을 텍스트로 변환할 수 있습니다.
- 110가지 이상의 언어와 방언을 인식합니다.
- 사용자가 말하는 중에 인식 결과를 반환할 수 있습니다.
- 노이즈 캔슬링 처리를 합니다.

---

### Cloud Natural Language API

![](https://cdn-images-1.medium.com/max/800/1*bmFMNqkgwuR5EHxdiD9FIg.png)

자연어 분석 서비스

- 구문을 분석할 수 있습니다.
- 감정을 분석할 수 있습니다.
- 콘텐츠를 분류할 수 있습니다.

---

### Cloud Translation API

![](https://cdn-images-1.medium.com/max/800/1*pDHSgbDeWGQWN-66eHJoYg.png)

구글의 대표적인 번역 서비스

- 100개가 너믄ㄴ 언어 번역을 지원합니다
- 언어를 감지할 수 있습니다.
