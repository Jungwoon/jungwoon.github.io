---
layout: post
title: "빌드 타입별로 파이어베이스키 설정하기"
image: '/assets/img/'
description: 'Setting Firebase Key by Build Type '
tags:
- Android
- Firebase
categories:
- Android
- Firebase
---

프로젝트를 진행하다가 debug, stage, release 별로 파이어베이스 프로젝트 설정을 하고 싶었는데, 빌드 타입별로 파이어베이스키 설정하는 자료를 찾다가  
해당내용이 정리된 자료를 찾기 어려웠었던 경험이 있어서 이렇게 정리를 한번 해보고자 이렇게 포스팅을 하였습니다.

---

## 빌드 타입 설정하기

파일은 build.gradle(app) 에 해야하고 기본적으로 debug, release 가 있는데, 저는 여기에 stage 서버용으로 stage를 추가하도록 하겠습니다.
들어가는 위치는 android 블럭 안에 아래와 같이 들어갑니다.

```
buildTypes {
    debug {
    applicationIdSuffix '.dev'                                                  // 패키지 네임 뒤에 구분을 위해서 분이는 부분
    manifestPlaceholders = [appName : "@string/app_name_debug"]                 // 이름 다르게 설정 하기 위한 부분
    buildConfigField "String", "BASE_URL", "\"https://dev-api.jungwoon.com\""   // 설정에 따라 BASE_URL 다르게 하기
    }
    staging {
        signingConfig signingConfigs.release
        applicationIdSuffix '.stg'
        manifestPlaceholders = [appName : "@string/app_name_staging"]
        buildConfigField "String", "BASE_URL", "\"https://stg-api.jungwoon.com\""
    }
    release {
        signingConfig signingConfigs.release
        minifyEnabled false
        proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        manifestPlaceholders = [appName : "@string/app_name"]
        buildConfigField "String", "BASE_URL", "\"https://api.jungwoon.com\""
    }
}
```

위에서 저는 아래와 같이 다르게 설정을 줬습니다.

- debug
    - 패키지 네임 : 패키지네임.dev (여러개 설치가 가능하게 패키지 네임 뒤에 .dev 추가) 
    - 앱 네임 : @string/app_name_debug
    - 서버 주소 : https://dev-api.jungwoon.com
- staging
    - 패키지 네임 : 패키지네임.stg (여러개 설치가 가능하게 패키지 네임 뒤에 .stg 추가)
    - 앱 네임 : @string/app_name_staging
    - 서버 주소 : https://stg-api.jungwoon.com
- release
    - 패키지 네임 : 패키지네임
    - 앱 네임 :  @string/app_name
    - 서버 주소 : https://api.jungwoon.com
    
위와 같이 buildType에 맞춰서 다르게 보이게끔 하였습니다. 위에서 지정한 BASE_URL 부분은 아래와 같이 호출할 수 있습니다.

```kotlin
import 패키지 네임.BuildConfig // 주의할 점 여기서 패키지네임의 BuildConfig가 import 되어 있어야 합니다. 

Retrofit.Builder()
    .baseUrl(BuildConfig.BASE_URL) // 여기서 현재 설정된 BuildType에 맞춰서 가져오게 됨
    .addConverterFactory(GsonConverterFactory.create(get()))
    .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
    .client(get())
    .build()
```

---

## 실제 Firebase 키 넣기

위의 BuildType 설정을 하였으면 BuildType별 서로다른 프로젝트의 Firebase Key를 설정할 수 있는데 아래와 같은 구조로 하면 됩니다.

![](https://miro.medium.com/max/2400/1*33jJjob5gIVJburKj1U1NA.png)

우선 Project로 변경을 하고 src 밑에 설정한 BuildType 이름하고 동일한 디렉토리를 생성하고 각각의 Firebase Key를 넣으면 설정이 가능합니다.
(여기서 release는 별도의 디렉토리 생성없이 src 바로 밑에 넣으면 됩니다.)