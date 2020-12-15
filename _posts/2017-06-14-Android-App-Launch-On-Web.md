---
layout: post
title: "웹에서 네이티브 앱 실행시키기"
image: '/assets/img/'
description: '웹에서 안드로이드 앱 실행시키기'
tags:
- Android
- Web
categories:
- Android
---

웹에서 버튼이나 링크를 눌렀을때, 안드로이드 앱을 실행시킬 수 있습니다.

### Android Manifest 설정

```xml

<activity
  android:name=".TestActivity">

  <!-- 이 부분이 다 들어가 있어야 동작 -->
  <intent-filter>
    <action android:name="android.intent.action.VIEW"/>
    <category android:name="android.intent.category.DEFAULT"/>
    <category android:name="android.intent.category.BROWSABLE"/>
    <data android:scheme="schemeName" android:host="hostName"/>
  </intent-filter>
</activity>

```

### 설명
- android.intent.action.VIEW : 이 작업은 액티비티가 사용자에게 표시할 수 있는 어떤 정보를 가지고 있을 때 startActivity()가 있는 인텐트에서 사용합니다. 예를 들어 갤러리 앱에서 볼 사진이나 지도 앱에서 볼 주소 등이 이에 해당됩니다.
- android.intent.category.DEFAULT : 암시적 인텐트를 수신하려면 인텐트 필터 안에 반드시 포함되어 있어야 합니다.
- android.intent.category.BROWSABLE : 대상 액티비티가 스스로 웹 브라우저가 자신을 시작해도 되도록 허용하여 링크로 참조된 데이터를 표시하게 합니다. 예컨대 이미지나 이메일 메시지 등이 이에 해당합니다.
- data : 이 부분에서 scheme이름과 host이름을 지정해주어야 실행시킬 수 있습니다.

### <data> 포맷

```xml
<scheme>://<host>:<port>/<path>
```


### Web에서 설정

```html

<a href="schemeName://hostName"> 실행 하기 </a>

```


위와 같이 설정이 끝난 상태에서 Web에서 해당 링크를 누르게 되면 설치되어 있는 경우에 지정한 Activity가 실행이 됩니다.
