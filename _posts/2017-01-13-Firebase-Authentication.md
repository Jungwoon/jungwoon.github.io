---
layout: post
title: "Firebase Authentication을 이용한 로그인 및 회원가입 연동하기"
image: '/assets/img/'
description: '1시간 안에 백사이드 개발없이 로그인 서버 만들기(feat. Firebase, Butterknife)'
tags:
- Firebase
- Authentication
categories:
- Android
---


Butterknife를 이용해서 Dependency Injection을 주어서 코드가 좀 더 간결 합니다, 해당 기능을 이용하기 위해서는 Firebase 연동이 되어 있어야 합니다. Firebase 연동은 포스팅된 내용을 확인 바랍니다.


#### [Firebase Console](www.console.firebase.google.com) 에 들어가서 이메일/비밀번호 설정을 합니다.

![img](https://cdn-images-1.medium.com/max/2000/1*Yx4Y4NkdeOzCx4TODZ1jRQ.png)

#### Firebase를 위한 Gradle 세팅

![img](https://cdn-images-1.medium.com/max/2000/1*AaLW7PlPOxmWEM5SBwiVKw.png)

#### 로그인 메서드(아래 소스로 끝)

![img](https://cdn-images-1.medium.com/max/2000/1*4LYnCb9pYHADGYWLJpb_QA.png)

#### 회원가입 메서드(아래 소스로 끝)

![img](https://cdn-images-1.medium.com/max/2000/1*7eV4RDffTVnQjWmgibVMZQ.png)

#### 비밀번호 찾기 매서드(아래 소스로 끝)

![img](https://cdn-images-1.medium.com/max/2000/1*B6N9alnoQsz5xH-qoU_GAA.png)

#### onStart()와 onStop()을 다음과 같이 오버라이딩 해줘야 합니다.

![img](https://cdn-images-1.medium.com/max/2000/1*47nAmpbe-qXZA8H9R5h82g.png)

#### onCreate에 다음과 같이 Firebase 로그인 관련 리스너 등록을 해줘야 합니다.

![img](https://cdn-images-1.medium.com/max/2000/1*lY4rCrHPECBnUJuaZsRQQQ.png)

#### 그럼 파이어베이스 콘솔쪽에 자동으로 회원정보들이 연동이 됩니다.

![img](https://cdn-images-1.medium.com/max/2000/1*z4Z-QKcV_Gq_RI6KJydGUg.png)
