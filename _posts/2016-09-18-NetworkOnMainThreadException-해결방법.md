---
layout: post
title: "NetworkOnMainThreadException 해결방법"
image: '/assets/img/'
description: 'Network 관련 기능은 MainThread와 분리해야합니다'
tags:
- Android
categories:
- Android
---

### NetworkOnMainThreadException 해결방법
`android.os.NetworkOnMainThreadException`이 발생할때 해결방법 관련해서 일어나는 원인은
허니컴 이후에 네트워크 관련 처리를 메인 쓰레드에서 처리할 경우에 발생합니다.

해결방법으로는 스레드를 별도로 만들어서 처리를 하거나 아래와 같이 스레드 정책을 허용하도록 설정해줍니다.

```java
if(android.os.Build.VERSION.SDK_INT > 9) {
    StrictMode.ThreadPolicy policy
= new StrictMode.ThreadPolicy.Builder().permitAll().build();
    StrictMode.setThreadPolicy(policy);
  }
}
StrictMode.setThreadPolicy(policy);
}
```
