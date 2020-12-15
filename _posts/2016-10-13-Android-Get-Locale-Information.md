---
layout: post
title: "Android 설정된 언어/지역 정보 가져오기"
image: '/assets/img/'
description: '안드로이드 시스템에 설정된 지역 언어/지역 정보 가져오기'
tags:
- Android
categories:
- Android
---

안드로이드 시스템에 설정된 지역 언어/지역 정보 가져오기

```java
Locale systemLocale = getApplicationContext().getResources().getConfiguration().locale;
String strDisplayCountry = systemLocale.getDisplayCountry(); // 대한민국
String strCountry = systemLocale.getCountry(); // KR
String strLanguage = systemLocale.getLanguage(); // ko
```
