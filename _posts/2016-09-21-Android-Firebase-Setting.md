---
layout: post
title: "Android Firebase 세팅하기"
image: '/assets/img/'
description: '내가 만든 앱을 Firebase에 연동시키기'
tags:
- Android
- Firebase
categories:
- Android
---

16년 Google I/O에서 발표해서 핫했던, Firebase 설정하는 방법을 공유하고자 합니다.

1.`console.firebase.google.com`에 들어가서 로그인 하고 새 프로젝트 만들기를 클릭
![img](https://cdn-images-1.medium.com/max/2000/1*bOZJmZigVtSE_AY87PNM9A.png)

2.프로젝트 이름과 국가를 선택
![img](https://cdn-images-1.medium.com/max/2000/1*WI5jlv_6QT8CYmtB5WML1g.png)

3.‘Android 앱에 Firebase 추가’ 를 누른다.
![img](https://cdn-images-1.medium.com/max/800/1*sX_rZU6tK4Z51v_VpLkvWA.png)

4.패키지 네임과 디버그 서명 인증서를 넣는다, 해당 SHA-1키를 얻고 싶으면 ? 를 클릭하면 링크페이지로 연결이 된다.
![img](https://cdn-images-1.medium.com/max/1600/1*kmLlf-PBd6yjUlFnMWb-bQ.png)

4-1.APK 생성때 썼던 keystore의 ‘path’와 ‘key alias’가 필요
![img](https://cdn-images-1.medium.com/max/2000/1*mpJcRndM4oruqhHjCo6_og.png)

4-2.터미널을 열고 다음과 같이 입력하면 인증서 키를 받을 수 있다. 여기서 SHA1 부분의 키를 복사하여 붙여준다.
![img](https://cdn-images-1.medium.com/max/2000/1*GpdxoXzCSJ8KdkjQRXB4iQ.png)

5.위에서 SHA-1까지 넣어주고 앱 추가를 누르면 ‘google-services.json’ 이라는 파일이 다운로드가 되는데 이걸 app 밑에 프로젝트에 넣어준다.

![img](https://cdn-images-1.medium.com/max/1600/1*vG6PK6_kXmYagcm_G5ItqA.png)

6.다음 위치에 넣어주면 된다.
![img](https://cdn-images-1.medium.com/max/800/1*Z086JEKej0y-_yfpDXwscQ.png)

7.계속을 누르면 build.gradle에 다음 설정을 하라고 한다.(build.gradle의 총 2개가 생성이 됨, Project 수준, App 수준), 설정을 해주고 완료 버튼을 누른다.
![img](https://cdn-images-1.medium.com/max/1600/1*Kk3et399n3_z7OnDMo3Dew.png)

8.우선 프로젝트 수준의 설정 부터 해준다. 다음 위치에 있는 build.gradle이 프로젝트 수준의 build.gradle
![img](https://cdn-images-1.medium.com/max/2000/1*sSWPUoh3ltDkvD5evwRjKw.png)

9.그 다음 app 수준의 gradle 설정을 해준다.
![img](https://cdn-images-1.medium.com/max/2000/1*V9J3PIi_BB60L8Sl_qHc6g.png)

10.대쉬보드에 다음과 같은 카드뷰가 생기면 끝
![img](https://cdn-images-1.medium.com/max/1600/1*uNa-1SQCSvqeZ_DKsId0Iw.png)
