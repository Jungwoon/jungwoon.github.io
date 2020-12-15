---
layout: post
title: "안드로이드에서 뷰가 그려지는 과정"
image: '/assets/img/'
description: 'How to draw view in Android'
tags:
- Android
categories:
- Android
---

최근에 안드로이드에서 뷰가 그려지는 과정에 대해서 질문을 받았던 적이 있는데 그 부분에 대답을 
잘 못해서 공부도 할겸 포스팅을 작성합니다.

---

## 개념

안드로이드에서 UI를 렌더링하기 위한 여러가지 개념들이 있는 해당 개념들 보통 살펴보도록 하겠습니다.

### 크기 순서

```
Window > Surface > Canvas > View 
```

### Window

![](https://miro.medium.com/max/2160/1*GqBKZVXpY-0dqwoY24PzyQ.png)

위의 그림에서 네모로 표시한 하나하나가 `Window` 입니다. 위와 같이 하나의 화면 안에는 여러개의
`Window`를 가질 수 있고 이러한 `Window`들은 `WindowManager`가 관리를 합니다.

`Window`는 뭔가를 그릴 수 있는 창이라고 생각하면 됩니다. 보통 하나의 `Surface`를 가지고 있으며,
`Application`은 `Window Manager`와 상호작용하여 `Window`를 만듭니다. 
`Window Manager`는 각각의 Window 표면에 `Component`를 그리기 위해 `Surface`를 만듭니다.

`Application`은 `Surface`에 원하는 것을 그릴 수 있습니다.

일반적으로 `Activity`가 `Window`를 가지게 됩니다.

---

### Surface

![](https://miro.medium.com/max/2160/1*z15f9yf-mMXlC_dyFZc9Ig.png)

위의 그림처럼 각각의 `Window`는 `Surface`를 가집니다.

`Surface`는 화면에 합성되는 픽셀을 보유한 객체입니다. 화면에 표시되는 모든 `Window`는 
자신만의 `Surface`가 포함되어 있으며, `Surface Flinger`가 여러 소스로부터 그래픽 데이터 버퍼를 받고,
그것들을 합성해서 Display로 보냅니다.

개별 `Surface`는 **이중 버퍼 렌더링을 위한 1개 이상(보통 2개)의 버퍼를 가집니다.

```
** 이중 버퍼 렌더링

스크린의 출력될 화면 데이터를 프레임 버퍼(Frame Buffer)라고 하는데, 렌더링을 하면 그 결과가
프레임 버퍼에 저장이 됩니다. 근데 하나의 버퍼만 가진다면, 이미지가 다 그려지지 않아도 화면
주사율때문에 렌더링을 해야할때 다 그려지지 않은 프레임 버퍼가 렌더링이 되어서 그려져야 할 이미지가
나타났다 사라졌다 하면서 깜빡이는 현상(Flicker)가 발생할 수 있습니다.

이를 해결하기 위해서 프레임 버퍼에 바로 렌더링 하지 않고, 다른 버퍼를 만들어서 그 버퍼에 렌더링을
완료하고 다음 프레임 버퍼로 옮기는 방식을 사용하여 위와 같은 현상을 해결할 수 있습니다. 
```

---

### Canvas

실제 UI를 그리기 위한 공간으로 비트맵이 그려지는 공간이라고 생각합니다.

---

### View

`View`는 `Window` 내부의 대화식 UI 요소입니다. 창에는 단일 뷰 계층 구조가 연결되어 있으며 모든 창의 동작을 제공합니다. 

`Window`가 다시 뭔가를 그려야할때마다 `Window`의 `Surface`에서 작업이 수행됩니다. 만약 `Surface`가 잠기면
그리는데 사용할 수 있는 `Canvas`가 반환이 됩니다. 그럼 `Draw Traversal`가 계층적으로 각 뷰를 `Canvas`에 전달하여
UI를 그리기 시작합니다.

완료가 되면 `Surface`가 잠기고 방금 그린 `Buffer`가 포그라운드 상태로 바뀌고 `Surface Flinger`에 의해서 화면에 합성됩니다.

---

### SurfaceView

일반적인 `View`는 `Main Thread`에서 캔버스를 그리기 때문에, 그리기를 하는 동안에는 사용자의
입력을 받을 수 없고 그로 인해 반응성이 좋지 못합니다. 그렇다고 그리는 작업을 별도의 작업 스레드에서
처리하고 싶어도 안드로이드 정책 상 `Main Thread`가 아닌 별도의 `Thread`에서는 UI 관련 작업을
할 수도 없습니다. 이럴때 사용할 수 있는게 `SurfaceView`입니다. 

`SurfaceView`는 `Canvas` 아닌 Surface(=가상 메모리 화면)에 그리고 그려진 `Surface`를 화면에 뿌리기 때문에
게임이나, 카메라 같은 높은 반응성이 필요한 UI 작업이 필요한 경우 사용할 수 있습니다.

![](https://miro.medium.com/max/3860/1*lkIO9z94CxTvgqPJGKLcMQ.png)

구조는 위의 이미지와 같으며, `SurfaceHolder`가 `Surface`에 접근하여 화면을 처리해주는 구조입니다.

---

### View와 ViewGroup

![](http://cdn.learncomputer.com/wp-content/uploads/2012/01/image0033.png)

안드로이드에서 `UI요소`들은 크게 `View`와 `ViewGroup`으로 이루어져 있으며 아래와 같은 특징들을 가지고 있습니다.

- `View`는 화면상의 UI를 구성합니다.
- `ViewGroup`은 `ViewGroup`과 `View`를 포함합니다.
-  `TextView`, `Button`등 기본적인 화면 구성 요소들은 `View`에 상속되어 구현됩니다.
- 개별적으로 속성을 가지며 개별 속성은 `Parent`로부터 상속을 받을 수 있습니다.
- View는 Window의 Surface를 이용하여 화면을 그리고, 이벤트를 어떻게 처리할지에 대한 구현을 하는 객체입니다. 

---

## Layout이 그려지는 과정

안드로이드에서는 액티비티가 `Focus`를 얻게되면 자신의 `Layout`을 그리게 됩니다. 이때 그리고자 하는
`Layout`의 `Root Node`를 요청하게 되는데, 우리가 `setContentView()`를 호출할때 `Root Node`가
정해집니다. `Root Node`가 결정이 되면 `Root Node`를 따라 `Child Node`를 찾아가면서 차례대로 `View`를 그리게 됩니다.

`Layout`을 그리는건 아래 과정을 통해 `측정`과 `순서`란 과정을 거치게됩니다.

```
measure() -> onMeasure() -> layout() -> onLayout()
```

- `measure(widthMeasureSpec: Int, heightMeasureSpec: Int)` : 뷰의 크기를 알아내기 위해 호출되며, 실제 크기 측정을 위해 `onMeasure()`를 호출합니다.
    - widthMeasureSpec : `Parent`가 부여한 필요한 `가로` 공간
    - heightMeasureSpec : `Parent`가 부여한 필요한 `세로` 공간
- `onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int)` : 실제 뷰의 크기를 측정합니다.
    - widthMeasureSpec : `Parent`가 부여한 필요한 `가로` 공간
    - heightMeasureSpec : `Parent`가 부여한 필요한 `세로` 공간
- `layout(left: Int, top: Int, right: Int, bottom: Int)` : 뷰의 위치를 할당하기 위해 호출되며, 실제 할당을 위해 `onLayout()`을 호출합니다.
    - left : Parent에 대한 왼쪽 포지션
    - top : Parent에 대한 위쪽 포지션
    - right : Parent에 대한 오른쪽 포지션
    - bottom : Parent에 대한 하단 포지션    
- `onLayout(changed: Boolean, left: Int, top: Int, right: Int, bottom: Int)` : 실제 뷰의 할당을 합니다.
    - changed : 새로운 사이즈나 위치인지 맞으면 True 아니면 False 
    - left : Parent에 대한 왼쪽 포지션
    - top : Parent에 대한 위쪽 포지션
    - right : Parent에 대한 오른쪽 포지션
    - bottom : Parent에 대한 하단 포지션

---

### Custom View LifeCycle

뷰가 생성이 되면 다음 순서대로 메소드들이 호출이 됩니다. `View의 LifeCycle`는 여러가지 이유로 
공식적으로 제공이 되지 않습니다. 아래 이미지는 널리 퍼져 있는 `View LifeCycle`입니다.

![](https://miro.medium.com/max/692/1*abc0UlGj1myFD0eph4pZjQ.png)

1. Constructor : 
    - CustomView(context: Context) : `코드`로 생성하면 호출
    - CustomView(context: Context, attributeSet: AttributeSet) : `xml`로 생성하면 호출
2. onAttachedToWindow : `Parent View`가 addView(view: View)를 호출하면 해당 `View`가 `Window`에 연결됩니다, 이 단계부터 `id`를 통해 접근할 수 있습니다.
3. onMeasure : `View`의 사이즈 측정
    - MeasureSpec : Parent `View`에서 `Child View`로 요구조건을 보내기 위해 사용
        - MeasureSpec.EXACTLY : 직접적으로 `width`와 `height`에 대해 하드코딩 할때
        - MeasureSpec.AT_MOST : `Parent`가 `Child`의 `Maximum Size`를 설정하기 위해 사용합니다.
        - MeasureSpec.UNSPECIFIED : `Parent`에 의해 사용되며 제한이 없기 때문에 Child `View`가 원하는 사이즈를 가질 수 있습니다. 
4. onLayout : 개별 `Child View`들의 사이즈 및 위치 할당
5. onDraw : `View` 그리기 시작, `Canvas`와 `Paint`를 사용하여 그리기 시작하며 `Canvas`는 `모양`을 그리고 `Paint`는 `색`을 칠합니다.
(리소스를 많이 사용하기 때문에 할당된 객체를 재사용할 수 있도록 해야합니다.)

### View Update

View의 업데이트는 위에서 보이는거 같이 아래 두 함수를 통해서 이루어집니다.

- `invalidate()` : `View`에 변화가 생겨서 다시 그려야 할때 사용합니다.
- `requestLayout()` : `View`를 처음부터 그려야 할때 사용합니다.

### Animation

`View`의 `Animation`은 `frame 단위`로 `View`에 애니메이션으로 변화가 있을때마다 `invalidate()`를
호출하여 뷰를 그립니다. 대표적으로 애니메이션을 사용하는 클래스는 `ValueAnimator` 입니다.
