---
layout: post
title: "Android 키보드 입력시 Bottom Navigation 아이콘 올라오는 문제 해결 방법"
image: '/assets/img/'
description: 'Android 키보드 입력시 Bottom Navigation 아이콘 올라오는 문제 해결 방법'
tags:
- Android
- Material Design
categories:
- Android
---

## Android 키보드 입력시 Bottom Navigation 아이콘 올라오는 문제 해결 방법

Bottom Navigation이 공식적으로 지원이 되면서 사용하고 있는데 Keyboard 입력을 받을때,

Bottom Navigation의 아이콘도 같이 올라가는 문제가 있습니다. 이를 해결하는 방법을 포스팅 해보려고 합니다

---

보통때의 화면은 다음과 같습니다

![](https://cdn-images-1.medium.com/max/600/1*XkemaqKddFbwABS0t04qrw.png)

EditText에 포커스가 잡히면서 키보드를 입력시 Bottom Navigation의 아이콘들도 같이 올라오는 문제가 발생합니다

![](https://cdn-images-1.medium.com/max/800/1*GXSk20hFGIfKjvJIKjzmAw.png)

해결법) AndroidManifest.xml의 안에 `activity` 안에 다음 코드를 넣어줍니다

`android:windowSoftInputMode="adjustPen"`

![](https://cdn-images-1.medium.com/max/1000/1*nteRZY18GEjf2QADezBYIg.png)

그럼 다음과 같이 Bottom Navigation 아이콘들이 같이 올라오는 문제가 해결됩니다

![](https://cdn-images-1.medium.com/max/600/1*kqn6PeQTgnfiPoydbYEvgQ.png)
