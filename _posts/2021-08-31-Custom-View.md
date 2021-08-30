---
layout: post
title: "안드로이드 커스텀뷰, 커스텀 뷰그룹 만들기"
image: '/assets/img/'
description: 'Develop Custom View and Custom ViewGroup in Android'
tags:
- Android
categories:
- Android
---

최근에 직접 커스텀뷰 및 커스텀 뷰 그룹을 만들어야 하는 업무를 맡으면서 그 부분에 대해서 다시 정리할겸 이렇게 포스팅을 하게 되었습니다.

---

## View가 그려지는 과정

우리가 화면에서 보는 View들도 특정한 과정을 거쳐서 화면에 그려지게 되는데 아래와 같이 그려지게 됩니다.

![](https://blog.kakaocdn.net/dn/wTeVw/btqzrX4bJNK/E5jRkkVHN13kkZUVEFFBkK/img.png)

크게 보면 아래 3단계를 통해서 그려집니다.

```
크기 알아내자! (onMeasure) -> 위치 알아내자! (onLayout) -> 그리자! (onDraw) 
```

보면 아래와 같이 나타나는데 `on`이 붙지 않은 메서드는 반드시 실행이 되어야 하는 메서드이고,
`on`이 붙은 메서드는 `override`해서 재활용할 수 있는 메서드라고 생각하시면 됩니다. 그래서
보통 커스텀 뷰나 뷰그룹을 만들때에는 `onXXX()`메서드를 확장하여 만듭니다.

```
measure() -> onMeasure()
layout() -> onLayout()
draw() -> onDraw()
```

위의 과정에 대해서 하나 하나 살펴보도록 하겠습니다.

### 1. Constructor

뷰가 생성될때의 생성자 입니다. 개발 언어를 이용해서 직접 할때와 XML을 통해서 속성들을 받을때 등 여러가지 상황에
대한 다양한 생성자를 정의해주어야 합니다. 총 3개의 생성자를 정의 해주어야 하는데 그 부분에 대해서 이야기를 해보도록 하겠습니다.

```kotlin
// 코틀린이나 자바를 이용해서 동적으로 생성할때의 생성자
View(context: Context)
```

```kotlin
// XML에서 사용하려면 이 부분까지 생성자를 지정해주어야 합니다.
View(context: Context, attrs: AttributeSet?)
```

```kotlin
// 별도의 지정된 스타일을 적용할 수 있게 할 때 사용합니다.
View(context: Context, attrs: AttributeSet?, defStyleAttr: Int)
```

그래서 실제 구현 했을때의 모습은 아래와 같습니다.

```kotlin
class MyView() : View {
    constructor(context: Context) : super(context)
    constructor(context: Context, attrs: AttributeSet?) : super(context, attrs)
    constructor(context: Context, attrs: AttributeSet?, defStyleAttr: Int) : super(
        context,
        attrs,
        defStyleAttr
    )
    
    // Todo
}
```

또는 

```kotlin
class MyView @JvmOverloads constructor(
    context: Context,
    attrs: AttributeSet? = null,
    defStyleAttr: Int = 0
) : View(context, attrs, defStyleAttr) {
    
    // Todo

}
```

#### onFinishInflate()

XML로부터 ViewGroup과 View들이 전부 Inflate 한 후에 호출되는 콜백입니다.


### 2. onAttachedToWindow()

View가 Window에 붙었을때 호출되는 콜백 입니다.

### 3. onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int)

```
Parameter)

widthMeasureSpec : 부모뷰의 의해 적용된 너비 요구 사항 (너비와 MeasureSpec 정보를 가진다.)
heightMeasureSpec : 부모뷰의 의해 적용된 높이 요구 사항 (높이와 MeasureSpec 정보를 가진다.)

***) MeasureSpec
MeasureSpec.EXACTLY : 보통 XML에서 match_parent 또는 직접 크기를 설정된 경우에 이 모드가 내려오고 이 경우 속해있는 부모 ViewGroup에서
크기를 지정해서 width와 height를 넘겨주므로 MeasureSpec.getSize()를 통해 얻게 된 값을 사용하면 됩니다.
MeasureSpec.AT_MOST : 보통 XML에서 wrap_content로 설정된 경우에 이 모드가 내려오고 MeasureSpec.getSize()를 통해 나온 값은 View가
최대로 가질 수 있는 크기입니다.
MeasureSpec.UNSPECIFIED : 소스에서 View를 생성한 경우에 크기가 정해지지 않아서 해당 MeasureSpec이 넘어옵니다.
```

실질적으로 함수가 호출되는 과정은 아래와 같습니다. measure 부분은 필수적으로 불리는 부분이고 onMeasure() 부분은 따로 오버라이드를 하거나 할 수 있습니다.

```
View.measure() -> View.onMeasure() -> View.setMeasuredDimention()
```

View : 크기를 확인하기 위해 호출
ViewGroup : Child View 들에 대한 측정을 하고 자신의 크기를 결정합니다.

모든 뷰는 반드시 뷰 그룹 안에 담겨야 합니다. 그래서 자신의 크기를 결정하기 위해서 자신이 속한 뷰그룹의 크기를 알아야 
하고 뷰그룹에서 사용할 수 있는 영역의 크기도 알아야 합니다.

`MeasureSpec`으로부터 `mode`와 `size`를 얻으려면 아래와 같이 사용할 수 있습니다.

```kotlin
// 너비
val widthMode = MeasureSpec.getMode(widthMeasureSpec)
val widthSize = MeasureSpec.getSize(widthMeasureSpec)

// 높이
val heightMode = MeasureSpec.getMode(heightMeasureSpec)
val heightSize = MeasureSpec.getSize(heightMeasureSpec)
```

실제 테스트를 위해서 아래 테스트코드로 테스트를 해봤습니다.

```xml
<!-- 부모 뷰 -->
<LinearLayout
    android:layout_width="너비 설정"
    android:layout_height="높이 설정"
    android:orientation="vertical">
    
    <!-- 자식 뷰 -->
    <com.byjw.myapplication.MyTestView
        android:layout_width="너비 설정"
        android:layout_height="높이 설정"
        android:text="Hello World"/>

</LinearLayout>
```

```kotlin
package com.byjw.myapplication

import android.content.Context
import android.util.AttributeSet
import android.util.Log
import androidx.appcompat.widget.AppCompatTextView

class MyTestView : AppCompatTextView {

    constructor(context: Context) : super(context)
    constructor(context: Context, attrs: AttributeSet?) : super(context, attrs)
    constructor(context: Context, attrs: AttributeSet?, defStyleAttr: Int) : super(
        context,
        attrs,
        defStyleAttr
    )

    override fun onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec)

        Log.e("JW_TEST", "Width MeasureSpec : ${getMode(MeasureSpec.getMode(widthMeasureSpec))} / ${MeasureSpec.getSize(widthMeasureSpec)}")
        Log.e("JW_TEST", "Height MeasureSpec : ${getMode(MeasureSpec.getMode(heightMeasureSpec))} / ${MeasureSpec.getSize(heightMeasureSpec)}")

    }

    private fun getMode(measureSpecMode: Int): String {
        return when (measureSpecMode) {
            MeasureSpec.AT_MOST -> "AT_MOST"
            MeasureSpec.EXACTLY -> "EXACTLY"
            MeasureSpec.UNSPECIFIED -> "UNSPECIFIED"
            else -> "NONE"
        }

    }

}
```
위와 같이 해서 부모 뷰와 자식 뷰의 너비와 높이의 설정을 바꿔가면서 테스트했을때 결과는
아래와 같습니다. 

| |<pre>부모 뷰</br>width : 100dp</br>height : 200dp</pre>|<pre>부모 뷰</br>width : match_parent</br>height : match_parent</pre>|<pre>부모 뷰</br>width : wrap_content</br>height : wrap_content</pre>
|:---|:---|:---|:---
|<pre>자식 뷰</br>width : match_parent</br>height : match_parent</pre>|<pre>EXACTLY / 275</br>EXACTLY / 550</pre>|<pre>EXACTLY / 1080</br>EXACTLY / 1791</pre>|<pre>AT_MOST / 1080</br>AT_MOST / 1791</br>EXACTLY / 200</br>EXACTLY / 53</pre>
|<pre>자식 뷰</br>width : wrap_content</br>height : wrap_content</pre>|<pre>EXACTLY / 275</br>EXACTLY / 550</pre>|<pre>EXACTLY / 1080</br>EXACTLY / 1791</pre>|<pre>AT_MOST / 1080</br>AT_MOST / 1791</pre>
|<pre>자식 뷰</br>width : 10dp</br>height : 20dp</pre>|<pre>EXACTLY / 28</br>EXACTLY / 55</pre>|<pre>EXACTLY / 28</br>EXACTLY / 55</pre>|<pre>EXACTLY / 28</br>EXACTLY / 55</pre>
|<pre>자식 뷰</br>width : 0dp</br>height : 0dp</pre>|<pre>EXACTLY / 0</br>EXACTLY / 0</pre>|<pre>EXACTLY / 0</br>EXACTLY / 0</pre>|<pre>EXACTLY / 0</br>EXACTLY / 0</pre>

위의 표를 보면 크기가 결정되어 있을 경우(=match_parent, 크기 결정) EXACTLY에 부모 뷰의 크기 안에서 크기가 결정되고,
부모 뷰의 크기가 정해져 있지 않은 경우(=wrap_content) AT_MOST가 결정되는걸 확인할 수 있습니다. 그리고 두 번에 걸쳐서 결과가 나왔는데,
그 이유는 아직 자식뷰의 크기가 정해지지 완전히 정해지지 않아서 자식뷰의 크기가 결정이 되고 부모 뷰의 크기가 결정되기 때문입니다.

`setMeasuredDimension()` 은 자신의 크기를 설정하는 함수입니다. 이 함수에 `width`와 `height`를
넣게되면 미리 지정한 `view`의 `width`와 `height`에 상관없이 이 함수에 넣은 값으로 크기가 설정됩니다.

`super.onMeasure(widthMeasureSpec, heightMeasureSpec)`를 사용하면 부모뷰에서 `setMeasuredDimension()`메서드를 호출하여 
부모 공간의 크기를 결정하는걸 확인할 수 있습니다.

만약에 `super.onMeasure(widthMeasureSpec, heightMeasureSpec)`를 없애고 싶으면
직접 `setMeasuredDimension(가로 크기, 세로 크기)` 메서드를 호출해주면 됩니다. 그럼 `MeasureSpec`을 통해 얻게 된
측정 크기는 무시되고 직접 지정한 가로 크기와 세로 크기로 설정됩니다. 
(** 만약 부모 뷰의 크기보다 큰 경우는 부모뷰에 가득 채워지지만 부모 뷰를 벗어나지는 않습니다.)

만약 `onMeasure()`에서 `setMeasuredDimension()`을 호출하지 않으면 `java.lang.IllegalStateException`이 발생합니다.


#### 내부에서 많이 쓰이는 함수

```
- measureChild() : 자식 뷰의 크기를 계산시킨다.
- getChildMeasureSpec() : 특정 자식 view의 MeasureSpec을 만들 수 있습니다.
- measureChildWithMargins(childView: View, widthMeasureSpec: Int, widthConstraint: Int, heightMeasureSpec: Int, heightMeasureConstraint) : childView의 너비 높이 측정
- View.getMeasuredWidth() : measureChildWithMargins()가 먼저 호출 된 후 측정된 너비를 반환합니다.   
- View.getMeasuredHeight() : measureChildWithMargins()가 먼저 호출 된 후 측정된 높이를 반환합니다.   
```

### 4. onLayout(changed: Boolean, left: Int, top: Int, right: Int, bottom: Int)

```
Parameter)

changed : 사이즈나 포지션이 새로 바뀌었으면 true 아니면 false
left : 부모의 상대적 왼쪽 포지션
top : 부모의 상대적 위쪽 포지션
right : 부모의 상대적 오른쪽 포지션
bottom : 부모의 상대적 아래쪽 포지션
```

View의 위치를 잡아주는 역할을 하며, 이 위치는 장비 디스플레이의 절대적 위치로 부몰르 기준으로한
상대적인 위치는 아닙니다.

#### 내부에서 많이 쓰이는 함수

```
- getPaddingLeft() : 자신의 왼쪽 패딩값
- getPaddingTop() : 자신의 위쪽 패딩값
- getPaddingRight() : 자신의 오른쪽 패딩값
- getPaddingBottom() : 자신의 아래쪽 패딩값
- getMeasuredWidth() : 자신의 크기 계산 후 나온 넓이
- getMeasuredHeight() : 자신의 크기 계산 후 나온 높이
- getChildCount() : 자신뷰의 개수를 반환합니다.
- child.getMeasuredWidth() : 자식 뷰의 넓이
- child.getMeasuredHeight() : 자식 뷰의 높이
- child.layout() : 자식 뷰의 위치를 지정
```

onMeasure()를 통해 알게된 크기를 가지고 적절한 위치에 배치 시킵니다. 대신에 

### 5. dispatchDraw(canvas: Canvas?)

```
Parameter)

canvas : 배경이 그려질 캔버스
```

자신을 다 그리고 자식뷰들을 그리기 전에 호출이 됩니다. ViewGroup 의 경우에 자식뷰들을 그릴때 호출이 됩니다. 

### 6. onDraw(canvas: Canvas?)

```
Parameter)

canvas : 배경이 그려질 캔버스
```

위의 onMeasure()와 onLayout()을 거쳐 크기와 그려질 위치를 알게되면 여기서 직접 그립니다.
ViewGroup일 경우에는 자신을 직접 그릴 필요가 없는 경우 이 과정이 생략이 되기도 합니다.
(*** setWillNotDraw(false)를 호출하면 onDraw() 생략을 막을 수 있ㅅ브니다.)

### 7. invalidate()

뷰가 변경될 경우 invalidate()를 호출하여 dispatchDraw() 과정부터 다시 그립니다. 이전에 onMeasure()와
onLayout()을 통해 알게 된 크기와 위치 정보는 그대로 이용합니다.

### 8. requestLayout()

레이아웃이 변경되어 이전에 onMeasure()와 onLayout()을 통해 알게 된 크기와 위치 정보는 그대로 이용하지 못할 경우
requestLayout()을 호출하여 onMeasure()와 onLayout()을 통해 변경된 크기와 위치 정보를 얻습니다.


## 좀 더 빠르게 하기

ViewGroup의 Depth가 깊어지면 연산에 더 많은 비용이 발생하기 때문에 왠만하면 깊은 Depth를 만들지 않기 위해
ConstraintLayout을 이용하면 퍼포먼스 상에서도 좋은 결과를 가져올 수 있습니다.