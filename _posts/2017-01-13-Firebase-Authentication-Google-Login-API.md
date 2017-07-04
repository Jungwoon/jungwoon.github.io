---
layout: post
title: "Firebase Authentication을 이용한 Google Login API 연동하기"
image: '/assets/img/'
description: 'Google Login API 연동하기(feat. Firebase)'
tags:
- Firebase
- Authentication
- Google
categories:
- Android
---


## Firebase Authentication을 이용한 Google Login API 연동하기
> Butterknife를 이용해서 Dependency Injection을 주어서 코드가 좀 더 간결 합니다, 해당 기능을 이용하기 위해서는 Firebase 연동이 되어 있어야 합니다. Firebase 연동은 포스팅된 내용을 확인 바랍니다.


#### [Firebase Console](www.console.firebase.google.com) 에 들어가서 Google 사용 설정을 합니다.

![img](https://cdn-images-1.medium.com/max/2000/1*PIfHGVb4B2G-ihg5wqzDkA.png)

#### Gradle Settings

![img](https://cdn-images-1.medium.com/max/2000/1*AaLW7PlPOxmWEM5SBwiVKw.png)

#### Google Login Button

![img](https://cdn-images-1.medium.com/max/1600/1*uZ1blFyAsSte7wsqT16m7w.png)

#### Activity 설정

![img](https://cdn-images-1.medium.com/max/2000/1*M4OdtxSrWxCb2Zub6Iz49A.png)

#### Firebase Authentication을 위한 객체 및 인스턴스

![img](https://cdn-images-1.medium.com/max/2000/1*TcjS4FzrBrU0k_LoW2OZHQ.png)

#### onCreate() 소스

![img](https://cdn-images-1.medium.com/max/2000/1*UCFunmVShsDwGR6Pch-pvw.png)

#### 로그인 연동 메소드(이걸로 끝)

![img](https://cdn-images-1.medium.com/max/2000/1*yMy3TETcHlO2kOrL3ON-AA.png)

#### Google 연동을 위한 onActivityResult() 세팅

![img](https://cdn-images-1.medium.com/max/2000/1*RZqRUdgWCAjLEEkWaViUfg.png)

#### 실제 Google 로그인을 하는 부분

![img](https://cdn-images-1.medium.com/max/2000/1*66r4UhizDlZwNxhJRX4r-g.png)
