---
layout: post
title: "Background Processing with AsyncTask"
image: '/assets/img/'
description: 'AsyncTask를 이용한 MainThread 분리'
tags:
- Android
categories:
- Android
---

안드로이드 개발을 하다보면 MainThread와 별도로 처리해야하는 API들이 있습니다.
대표적인 예로 Network 관련 API들은 반드시 MainThread와 분리해야 하는데,
Thread를 생성하면 별도로 생성되기때문에 리턴등을 통한 동기화 작업이 까다롭습니다.
그래서 다음과 같이 처리는 백그라운드에서 처리하고 리턴을 받는 예제를 공유합니다.

다양한 방법이 있겠지만, 여기선 `AsyncTask`를 사용하였습니다.

```java
// 별도로 return값을 받을때까지 대기
final List<ProcessManager.Process> list = new AsyncGetRunningApps().execute().get();
// 백그라운드에서 처리해주는 부분
private class AsyncGetRunningApps extends AsyncTask<Void, Void, List> {
 protected List<ProcessManager.Process> doInBackground(Void… arg0) {
 List<ProcessManager.Process> list = ProcessManager.getRunningApps();
 return list; // 백그라운드에서 처리된 결과를 리턴(Point!!)
 }
}
```
