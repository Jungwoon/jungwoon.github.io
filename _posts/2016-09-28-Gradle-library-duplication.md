---
layout: post
title: "Gradle에 라이브러리 중복 선언"
image: '/assets/img/'
description: 'com.android.build.transform.api.TransformException 발생시 해결법'
tags:
- Android
- Gradle
categories:
- Android
---

안드로이드에서는 기본적으로 하나의 라이브러리만 선언할 수 있고 중복 선언이 불가합니다
만약 중복으로 선언하려고 하면 'com.android.build.transform.api.TransformException' 에러가
발생합니다.

이때 Gradle에서 아래와 같이 설정을 해주면 해결이 가능합니다.

![img](https://cdn-images-1.medium.com/max/800/1*KKJFwViBv-DBzuZM9TpOEg.png)
