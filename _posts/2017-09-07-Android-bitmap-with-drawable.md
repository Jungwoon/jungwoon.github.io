---
layout: post
title: "Android Bitmap을 Drawable로 또는 Drawable을 Bitmap으로 변경"
image: '/assets/img/'
description: 'Android Bitmap을 Drawable로 또는 Drawable을 Bitmap으로 변경'
tags:
- Android
categories:
- Android
---

이번엔 생각보다 간단한데 매번 찾게 되는 부분 정리를 하려고 합니다

만들어지는 View 종류에 따라서 Drawable(int)가 들어갈수도 있고 Bitmap(bitmap) 이 들어갈 수도 있어서
이때 경우에 맞게 바꿔서 넣어주면 됩니다


```java
Context context = getApplicationContext();
Drawable drawable = getResources().getDrawable(R.drawable.my_image);

// drawable 타입을 bitmap으로 변경
Bitmap bitmap = ((BitmapDrawable)drawable).getBitmap();

// bitmap 타입을 drawable로 변경
Drawable drawable = new BitmapDrawable(bitmap); 
```
