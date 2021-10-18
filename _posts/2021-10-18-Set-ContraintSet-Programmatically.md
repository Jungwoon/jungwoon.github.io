---
layout: post
title: "ConstraintSet 직접 설정하기"
image: '/assets/img/'
description: 'Set ConstraintSet Programmatically'
tags:
- Android
categories:
- Android
---

이번에는 `ConstraintLayout`의  `ConstraintSet`을 코드로 설정하는 부분에 대해서 예제로 알아보고자 합니다. 

---

## ConstraintSet?

안드로이드의 `ConstraintLayout` 을 사용하면 다음과 같이 서로의 관계를 `XML` 에서 
`app:layout_constraintXXX_toXXXOf` 와 같이 직접 지정을 해줍니다.


```xml
<androidx.constraintlayout.widget.ConstraintLayout
      android:layout_width="match_parent"
      android:layout_height="match_parent">
  
  <TextView
        android:id="@+id/txt_title"
        android:layout_width="0dp"
        android:layout_height="0dp"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHeight_min="450dp" />
  
</androidx.constraintlayout.widget.ConstraintLayout>

```

이를 직접 하는 방법에 대해서 확인 해 보도록 하겠습니다. 아마 처음부터 만드는 경우는 거의 없을거고 기존에 있는 `ContraintSet`을
동적으로 바꾸고 싶을때 이용할거기 때문에 아래 예제는 기존의 `Constraint 정보`를 가져와서 동적으로 변경하는 예제입니다.

```kotlin
val textView = findViewById<TextView>(R.id.txt_title)

(textView.parent as? ConstraintLayout)?.let { parent ->
    val constraintSet = ConstraintSet() // ConstraintSet 객체를 만듭니다.
    constraintSet.clone(parent)         // 부모 뷰의 Constraint 정보를 복사합니다. 
    constraintSet.constrainMaxHeight(textView.id, 512) // 새로운 Constraint 정보를 등록합니다.
    constraintSet.applyTo(parent)       // 새로 만들어진 ConstraintSet을 부모의 ContraintSet에 적용합니다.
}
```