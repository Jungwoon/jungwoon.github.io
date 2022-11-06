---
layout: post
title: "Jetpack Compose"
image: '/assets/img/'
description: 'Jetpack Compose'
tags:
- Jetpack
- Compose
categories:
- Jetpack
---

`Jetpack Compose` 를 공부하면서 좀 더 심화된 내용들 알게 된 부분에 대해서 정리해 보고자 합니다.

해당 내용은 다음 포스팅들을 참고하였습니다. 

> [Jetpack Compose 데이터 저장 시스템](https://sungbin.land/jetpack-composes-data-storage-system-slot-table-change-list-82e92d274c32)

---

## Compose의 데이터 저장 시스템

Compose에는 핵심 데이터를 저장하기 위함 2가지 저장소가 있습니다.

### Slot Table

슬롯 테이블은 리컴퍼지션이 되기 위해 필요한 정보를 추적합니다.

ex) 
- 컴포지션동안 발생한 일
- 사용한 인자
- CompositionLocal
- 호출된 다른 함수등이 저장되는 공간

슬롯 테이블 내의 두가지 메모리 구조

- group : 컴포저블 그룹에 대한 메타데이터 저장 (IntArray)
- slot : 실제 컴포지션 관련 데이터가 저장 (Array<Any?>)


### Change List

- Change : 슬롯 테이블에 기록된 정보를 가지고 컴포저블 트리를 업데이트 하는 작업
- Change List : 컴포지션이 일어날때, 변경 사항들을 Chagne로 모델링해 Chagne List에 추가

---

### 컴포즈에서 액티비티를 가져오기

크게 아래 3가지 방법이 있습니다.

#### 1. LocalContext.current as Activity

Context를 Activity로 캐스팅하는 방법

모든 컴포저블은 `ComponentActivity.setContent`에서 시작되며, 이곳에서
`LocalContext`를 포함한 `데이터들이 초기화` 됩니다.

```kotlin
public fun ComponentActivity.setContent(
    parent: CompositionContext? = null,
    content: @Composable () -> Unit
) {
    val existingComposeView = window.decorView
        .findViewById<ViewGroup>(android.R.id.content)
        .getChildAt(0) as? ComposeView

    if (existingComposeView != null) with(existingComposeView) {
        setParentCompositionContext(parent)
        setContent(content)
    } else ComposeView(this).apply {
        setParentCompositionContext(parent)
        setContent(content)
        setOwners()
        setContentView(this, DefaultActivityContentLayoutParams)
    }
}
```


#### 2. LocalActivity.current

#### 3. Context.findActivity




---

## Compose가 그려지는 과정

컴포즈의 분리
- 런타임 : 
- UI 의존성 : 