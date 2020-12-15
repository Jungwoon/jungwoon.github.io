---
layout: post
title: "Setting 화면 띄우기"
image: '/assets/img/'
description: '안드로이드 Setting 화면 띄우기'
tags:
- Android
categories:
- Android
---

안드로이드의 보안이 강화되면서 설정에서 권한을 줘야지만, 이용할 수 있는 기능들이 많아졌습니다.
다음 소스는 설정 화면을 띄워주는 소스입니다.

```java
Intent intent = new Intent(android.provider.Settings.ACTION_ACCESSIBILITY_SETTINGS);
startActivityForResult(intent, 0);
```

위의 소스는 접근성 설정화면으로 넘겨주는 소스이지만

```java
android.provider.Settings.필요한 설정
```

이렇게 하면 자신이 필요로 하는 설정 화면으로 이동시킬 수 있습니다.
