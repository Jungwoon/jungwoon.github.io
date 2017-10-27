---
layout: post
title: "Google Cloud Storage의 gsutil 명령어 살펴보기"
image: '/assets/img/'
description: 'Google Cloud Storage의 gsutil 명령어 살펴보기'
tags:
- BigQuery
categories:
- BigQuery
---


## Google Cloud Storage의 gsutil 명령어 살펴보기

Google Cloud의 Storage인 GCS(=Google Cloud Storage)는 AWS(Amazon Web Service)의 S3에
해당하는 서비스입니다 

별로 잘 안쓰고 있었는데, GCP(=Google Cloud Platform)을 계속 사용하다가 보니까 사용하면 정말 편할거 같아서
공부할겸 내용을 정리해보려고 합니다

기본적으로 제가 사용하는 환경이 MacOS이기 때문에 터미널에서 명령을 입력하는 것만으로 업로드 및 다운로드가 된다는게
크게 좋게 다가왔습니다


> 자세한 사항은 [구글 공식 가이드](https://cloud.google.com/storage/docs/how-to)를 참고하세요

---

### gsutil을 위한 사전 준비

만약 gcloud가 설치되어있지 않다면 [Google Cloud SDK(=gcloud) 설치하기](https://jungwoon.github.io/google%20cloud/2017/10/26/install-gcloud/)를
참고하여 설치를 먼저 진행합니다

그 다음 아래 명령어를 통해서 `gsutil` 컴포넌트를 설치하면 사용준비가 끝이 납니다

```
$ gcloud components install gsutil      # gsutil 명령어를 다운받아보겠습니다
```

---

### gsutil 명령어 모음

```
$ gsutil list                           # 나의 버킷 리스트 보기
$ gsutil ls -r gs://버킷이름             # 버킷 안에 들어있는 파일 확인
$ gsutil du -s gs://버킷이름             # 버킷 용량 확인
$ gsutil mb gs://버킷이름                # 버킷 생성
$ gsutil rb gs://버킷이름                # 버킷 삭제
$ gsutil cp 로컬 파일 위치 gs://버킷이름   # 로컬 -> 버킷 복사
$ gsutil cp gs://버킷이름 로컬 파일 위치   # 버킷 -> 로컬 복사
$ gsutil mv 로컬 파일 위치 gs://버킷이름   # 로컬 -> 버킷 이동
$ gsutil mv gs://버킷이름 로컬 파일 위치   # 버킷 -> 로컬 이동
$ gsutil rm gs://버킷이름/파일이름        # 파일 삭제
$ gsutil ls -L gs://버킷이름/파일이름     # 파일 정보 보기
```

---

### 실습

#### 나의 버킷 리스트 보기

```
$ gsutil list
```

![](https://cdn-images-1.medium.com/max/1200/1*M4Xovp7gycycXshf5sal5g.png)

#### 버킷 안에 들어있는 파일 확인

```
$ gsutil ls -r gs://버킷이름
```

![](https://cdn-images-1.medium.com/max/800/1*jzByVSDvdUKGSB9z16vsRg.png)

#### 버킷 용량 확인

```
$ gsutil du -s gs://버킷이름
```

![](https://cdn-images-1.medium.com/max/600/1*S2FUkwmmMu3r64zPYZ2izQ.png)

#### 버킷 생성

```
$ gsutil mb gs://버킷이름
```

![](https://cdn-images-1.medium.com/max/1600/1*19lGJVIY7teUPJsqfjTMVQ.png)

#### 버킷 삭제

```
$ gsutil rb gs://버킷이름
```

![](https://cdn-images-1.medium.com/max/1600/1*UWz4lHlfRAa6iKiX3kQEyA.png)

#### 로컬 -> 버킷으로 파일 복사(=업로드)

```
$ gsutil cp 로컬 파일 위치 gs://버킷이름
```

![](https://cdn-images-1.medium.com/max/2000/1*jqZd1jgDZg3_zRAuwcaGkw.png)

#### 버킷 -> 로컬로 파일 복사(=다운로드)

```
$ gsutil cp gs://버킷이름 로컬 파일 위치
```

![](https://cdn-images-1.medium.com/max/2000/1*Z6_S69VBJRNzlYYyW5m4Vg.png)

#### 로컬 -> 버킷으로 파일 이동(=업로드)

```
$ gsutil mv 로컬 파일 위치 gs://버킷이름
```

![](https://cdn-images-1.medium.com/max/2000/1*mGb6JnPv_lJ7u6bqByODOA.png)

#### 버킷 -> 로컬로 파일 이동(=다운로드)

```
$ gsutil mv gs://버킷이름 로컬 파일 위치
```

![](https://cdn-images-1.medium.com/max/1600/1*_fMJzk9N93e_KZAbsBdVDg.png)

#### 버킷안에 파일 삭제하기

```
$ gsutil rm gs://버킷이름/파일이름
```

![](https://cdn-images-1.medium.com/max/2000/1*r9-kL2HmUAj2I6bYl0XSxQ.png)

#### 버킷안에 파일 정보 보기

```
$ gsutil ls -L gs://버킷이름/파일이름
```

![](https://cdn-images-1.medium.com/max/2000/1*ut6GQ4yqmZQSiwdlLvUbUw.png)
