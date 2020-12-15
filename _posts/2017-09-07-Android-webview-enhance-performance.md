---
layout: post
title: "Android Webview 성능향상을 위한 설정"
image: '/assets/img/'
description: 'Android Webview 성능향상을 위한 설정'
tags:
- Android
- Webview
categories:
- Android
---

기본적으로 안드로에서 제공하는 웹뷰를 바로 사용하면,
기대했던대로 동작하지 않거나 퍼포먼스가 나오지 않는 경우가 많습니다

이때 아래와 같이 설정을 하면 좀 더 나은 성능을 가져올 수 있게 됩니다


### AndroidManifest

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

### Source
```java
WebView mWebView = new WebView(this);
WebSettings settings = mWebView.getSettings();
settings.setJavaScriptEnabled(true);
settings.setLoadWithOverviewMode(true);
settings.setUseWideViewPort(true);
settings.setSupportZoom(true);
settings.setBuiltInZoomControls(false);
settings.setLayoutAlgorithm(WebSettings.LayoutAlgorithm.SINGLE_COLUMN);
settings.setCacheMode(WebSettings.LOAD_NO_CACHE);
settings.setDomStorageEnabled(true);
mWebView.setScrollBarStyle(WebView.SCROLLBARS_OUTSIDE_OVERLAY);
mWebView.setScrollbarFadingEnabled(true);

if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
  mWebView.setLayerType(View.LAYER_TYPE_HARDWARE, null);
} else {
  mWebView.setLayerType(View.LAYER_TYPE_SOFTWARE, null);
}
 
```
