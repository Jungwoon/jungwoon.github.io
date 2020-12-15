---
layout: post
title: "SharedPreference 동기화 문제"
image: '/assets/img/'
description: 'SharedPreference가 runtime에 바뀌지 않을때'
tags:
- Android
categories:
- Android
---

예전에는 SharedPreference 동기화가 자동으로 되었는데, 이제는 따로 동기화 설정을 해주어야 합니다.
만약 설정을 하지 않으면 어플리케이션을 새로 불러올때는 동기화가 잘 되지만 실행 중에는 동기화가 되지 않는 문제가 발생합니다.

```java
SharedPreferences pref = getSharedPreferences(“PREF_CHECK”, Context.MODE_MULTI_PROCESS);
SharedPreferences.Editor edit = pref.edit();
edit.putInt(“NEW_VERSION”, availableVersionCode);
edit.commit();
```

위와 같이 옵션 부분에 `MODE_MULTI_PROCESS`를 설정해주어야지만 실행중에도 바로 바로 변경된 값을 가져올 수 있습니다.
