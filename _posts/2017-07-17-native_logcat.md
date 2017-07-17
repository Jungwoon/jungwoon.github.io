---
layout: post
title: "Native Source에서 Log를 찍어서 Logcat 이용하기"
image: '/assets/img/'
description: 'Native Source에서 Log를 찍어서 Logcat 이용하기'
tags:
- Android
- NDK
- Logcat
categories:
- NDK
---

## Native Source에서 Log를 찍어서 Logcat 이용하기
아래와 같이 선언을 해준다.(cpp 파일 위쪽이나 header 파일로 빼준다.)

### 선언 하기

```c
//
// Created by Jungwoon on 2017. 7. 17..
// for Android LogCat byJW
//

#include <android/log.h>
#define LOG_TAG "NDK_TEST"
#define LOGV(...) __android_log_print(ANDROID_LOG_VERBOSE, LOG_TAG, __VA_ARGS__)
#define LOGD(...) __android_log_print(ANDROID_LOG_DEBUG, LOG_TAG, __VA_ARGS__)
#define LOGI(...) __android_log_print(ANDROID_LOG_INFO, LOG_TAG, __VA_ARGS__)
#define LOGW(...) __android_log_print(ANDROID_LOG_WARN, LOG_TAG, __VA_ARGS__)
#define LOGE(...) __android_log_print(ANDROID_LOG_ERROR, LOG_TAG, __VA_ARGS__)
#define LOGF(...) __android_log_print(ANDROID_FATAL_ERROR, LOG_TAG, __VA_ARGS__)
#define LOGS(...) __android_log_print(ANDROID_SILENT_ERROR, LOG_TAG, __VA_ARGS__)
```

### 사용 방법

```c
ProxyWorker::ProxyWorker() {
  LOGE("ProxyWorker::Constructor()"); // 다음과 같이 사용
	pthread_mutex_init(&writeQueueMutex, NULL);
	pthread_cond_init(&writeCond, NULL);
    tunInputBuf = (uint8_t*)malloc(TUN_INPUT_BUF_SIZE);
}
```

### Logcat 모니터링

![](https://cdn-images-1.medium.com/max/2000/1*iA5-O6CH2tmO3MYptPJNWA.png)
