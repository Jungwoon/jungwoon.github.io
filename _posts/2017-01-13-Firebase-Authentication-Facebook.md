---
layout: post
title: "Firebase Authentication을 이용한 Facebook 로그인 연동하기"
image: '/assets/img/'
description: 'Facebook Login API 연동하기(feat. Firebase)'
tags:
- Firebase
- Authentication
- Facebook
categories:
- Android
---


Butterknife를 이용해서 Dependency Injection을 주어서 코드가 좀 더 간결 합니다, 해당 기능을 이용하기 위해서는 Firebase 연동이 되어 있어야 합니다. Firebase 연동은 포스팅된 내용을 확인 바랍니다.

#### [Facebook 개발자 사이트](http://developers.facebook.com) 에 들어가서 가입을 하고 개인 프로젝트를 만들어 줍니다.

![img](https://cdn-images-1.medium.com/max/2000/1*5-UMAH3Yq3eT_TFStjhEPQ.png)

![img](https://cdn-images-1.medium.com/max/1200/1*QAY_qxStiiRx9QgrR40EGw.png)

![img](https://cdn-images-1.medium.com/max/2000/1*A_vr1r7AM43tcDWLFqrQ6A.png)

![img](https://cdn-images-1.medium.com/max/2000/1*Qc1PuZG0IhBzZkKcvX-OYw.png)

#### 이 부분에 안드로이드에서 생성한 '키 해시'를 넣어줍니다.(중요!!)

![img](https://cdn-images-1.medium.com/max/1200/1*akColNKjlECprwWAHZ_mIQ.png)


#### Tips) 안드로이드에서 '키 해시' 구하는 소스
(아래의 생성된 키 해시를 복사하여 위의 사이트에 붙여넣기 합니다)

![img](https://cdn-images-1.medium.com/max/2000/1*-pOo6SH1js0wtKnXlusiXA.png)

#### [Firebase Console](http://console.firebase.google.com) 으로 가서 Facebook의 사용설정을 합니다.

![img](https://cdn-images-1.medium.com/max/2000/1*5cIu5amVqo9KgJ5G3pxS4A.png)

#### Gradle 설정

![img](https://cdn-images-1.medium.com/max/2000/1*AaLW7PlPOxmWEM5SBwiVKw.png)

#### [Firebase Console](http://console.firebase.google.com) 에서 가져온 앱 아이디를 strings.xml에 넣어줍니다.

![img](https://cdn-images-1.medium.com/max/2000/1*lS4cnQ5Mb4Bpe9O3JftqUA.png)

#### AndroidManifest에 strings.xml에서 설정한 facebook_app_id를 설정합니다.

![img](https://cdn-images-1.medium.com/max/2000/1*_2wUWltDk5pHmJOz_JohEg.png)

---

## Android Source(아래 소스를 따라 하시면 됩니다)

#### 주의) FacebookSdk.sdkInitialize(this)는 반드시 setContentView() 호출 전에 넣어줘야 합니다.

![img](https://cdn-images-1.medium.com/max/800/1*2zwH2pcSHhxHsXXh3vmRWw.png)

#### Firebase 관련 설정 소스

![img](https://cdn-images-1.medium.com/max/2000/1*TcjS4FzrBrU0k_LoW2OZHQ.png)

![img](https://cdn-images-1.medium.com/max/2000/1*50wewzjPIVWvzVz3n3q9sw.png)

![img](https://cdn-images-1.medium.com/max/1200/1*Ujt8g6XkK77E14sB55FcHA.png)

![img](https://cdn-images-1.medium.com/max/2000/1*hmnkCLJDpGKqVZ5ypyAIUw.png)

![img](https://cdn-images-1.medium.com/max/400/1*xZ8_AjsojMakizOtOOsLQA.png)

![img](https://cdn-images-1.medium.com/max/400/1*NsKZ_UQwJyL3s-uX3gjtJA.png)
