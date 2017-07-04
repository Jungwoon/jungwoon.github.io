---
layout: post
title: "Android Support Annotation 정리"
image: '/assets/img/'
description: 'Annotation을 활용하여 버그를 줄이자!!'
tags:
- Android
- Annotation
categories:
- Android
---

## Android Support Annotaions를 활용하여 Debug를 줄이자

Android에서는 기본적으로 [Android Support Annotations](http://tools.android.com/tech-docs/support-annotations)를 제공합니다. 이를 이용하여 코드 상에 문제를 찾아낼 수 있는데, Android Studio와 통합되면서 좀 더 편하게 Annotation을 활용할 수 있게되었습니다.

### Support Annotations 필요 환경
- Android Plugin for Gradle 1.3 버전 이상
- Support Annotations 라이브러리 22.2.0 버전 이상
- Android SDK Platform-tools 23 버전 이상
- Android Studio 1.3 버전 이상

### Support Annotations 설정
build.gradle에 다음과 같이 설정해야합니다.

```
dependencies {  
    compile 'com.android.support:support-annotations:23.4.0'
}
```

### Android Support Annotations 정리

#### @NonNull

@NonNull이 붙은 변수에 null 값을 대입하면 경고가 나타납니다.

```java
@NonNull
final Context mContext; // null이 들어가면 경고

mContext = null; // 에러 발생
```

#### @Nullable

@Nullable이 붙은 변수는 null 일 수도 있다는 것을 나타냅니다. @Nullable 애너테이션이 붙은 변수를 null 검사 없이 사용하면 경고가 나타납니다.

@Nullable 애너테이션을 사용할 때는, 참조하거나 호출하는 쪽에서 null 검사를 하지 않으면 반드시 경고가 나타나기 때문에, 사용에 주의하여야 합니다.

```java
void setText(@Nullable CharSequence text) {
  this.text = text; // Warning

  // @Nullable이 붙으면 null 검사를 해주어야 함.
  if (text != null) {
    this.text = text;
  }
  else {
    this.text = "";
  }
}
```

#### @NonNull 과 @Nullable 사용에 적절한 예제
```java
class TestActivity extends Activity {  
    // 클래스 인스턴스 생성 시 null로 초기화되므로 @NonNull 애너테이션을 붙일 수 없다.
    View mView;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.test_activity);
        mView = findViewById(R.id.test_view);
    }

    @Override
    protected void onResume() {
        super.onResume();
        // mView에 @Nullable 애너테이션을 붙이면 불필요한 null 검사를 해야 한다.
        mView.setText(getCurrentText());
    }
}
```

#### @StringRes, @DrawableRes, @ColorRes
Android의 모든 리소스는 R 클래스에 의해 int 타입으로 관리가 되기 때문에, 서로 다른 리소스 아이디를 넣을 수 있는 가능성이 있는데, 이때 다음과 같은 리소스 애너테이션을 붙여주면 실수를 방지할 수 있습니다.

```java
@StringRes
int resId = R.string.text;

setText(resId); // 지정한 애너테이션에 맞게 수정하여 OK
setDrawable(resId); // Drawable 리소스를 넣어줘야 하기 때문에 Error
```

#### @ColorInt
@ColorInt 애너테이션은 0xff99ff99와 같은 ARGB 컬러 정수입니다. @ColorRes와는 다르기때문에, 잘못 썼을 경우에 오류가 나타납니다.

```java
@ColorInt
int color = 0xff99ff99;

setColor(color); // OK
setColorRes(color); // 여기에는 ColorRes 타입을 넣어주어야 해서 Error
```

#### @Dimension, @Px
@Dimension 애너테이션은 단위가 dp, sp, px임을 나타냅니다, 단위를 명시해주어야 하기때문에, 명시하지 않으면 기본은 px로 설정이 됩니다.

```java
@Dimension(unit = Dimension.SP) // sp로 설정을 하기 위해서는 다음과 같이 설정해야함
int resSp;

@Dimension(unit = Dimension.DP) // dp로 설정을 하기 위해서는 다음과 같이 설정해야함
int resDp;

@Dimensiton // 설정을 하지 않으면 기본 px로 설정이 됩니다.
int resPx;
```

#### @IntRange, @FloatRange
@intRange와 @FloatRange는 숫자형 값의 범위를 지정합니다. 지정한 범위를 나타내면 에러가 발생합니다.

```java
setAlpha(@FloatRange(from=0.0, to=1.0) float alpha) {
  // 값의 범위는 0.0 ~ 1.0
  // code
}
setAlpha(0.5f); // OK
setAlpha(127); // OutOfRange Error


setRgb(@IntRange(from=0, to=255) int rgb) {
  // 값의 범위는 0 ~ 255
  // code
}
setRgb(208); // OK
setRgb(277); // OutOfRange Error
```

#### @Size
@Size 애너테이션은 배열의 크기나 컬렉션의 크기, 문자열의 길이를 한정합니다.
@IntRange의 from 파라미터와 to 파라미터와 대응되는 min 파라미터와 max 파라미터로 범위를 잡을 수 있습니다. multiple 파라미터를 사용하면 크기가 입력한 값의 배수로 제한됩니다.

```java
void setVertex2d(@size(2) int[] vertext) {
  // code
}
setVertex2d(new int[2]); // OK
setVertex2d(new int[3]); // Error


void setUsername(@Size(min=8, max=12) String username) {
  // code
}
setUsername("abcde"); // Error(5자리라서)
setUsername("abcdefghi") // OK(9자리라 범위에 포함이 됨)
```

#### @IntDef, @StringDef
@IntDef 애너테이션과 @StringDef 애너테이션은 int 타입이나 String 타입으로 이루어진 열거형 값을 Enum이나 EnumSet을 이용할 때처럼 안전하게 사용할 수 있도록 제약 합니다.

사용법
1. @interface로 별도의 애너테이션을 선언
2. 선언된 애너테이션에 @IntDef 나 @StringDef 를 붙이고 허용 가능한 값을 파라미터로 열거
3. 실제로 열거형 값을 사용하는 부분에 앞서 @interface로 선언한 애너테이션을 붙임

```java
static final int INITIALIZED = 0;  
static final int STARTED = 1;  
static final int ENDED = 2;  
static final int CANCELED = 3;

@IntDef({INITIALIZED, STARTED, ENDED, CANCELED})
@Retention(RetentionPolicy.SOURCE)
@interface SharingState {}

@SharingState
int getState() { ... }  
void setState(@SharingState int state) { ... }

// ...

setState(STARTED);         // OK  
setState(STARTED | ENDED); // ERROR: Must be one of: Example.INITIALIZED, Example.STARTED,  
                           // Example.ENDED, Example.CANCELED
setState(0);               // ERROR: Must be one of: Example.INITIALIZED, Example.STARTED,  
                           // Example.ENDED, Example.CANCELED
setState(100);             // ERROR: Must be one of: Example.INITIALIZED, Eample.STARTED,  
                           // Example.ENDED, Example.CANCELED
```

#### @UiThread, @MainThread, @WorkerThread, @BinderThread
다음은 스레드 관련 애너테이션들로 해당 애너테이션들이 의미하는 스레드와 동일한 유형의 스레들에서만 해당 메서드를 호출할 수 있도록 제약합니다.(@UiThread와 @MainThread는 실질적으로 동일)

```java
@WorkerThread
void backgroundTask() {
  // code
}

@UiThread
void uiTask() {
  // code
}

@WorkerThread
void backgroundJob() {
  backgroundTask(); // OK
  uiTask() // Error
}

```

주의) 위 애너테이션들은 호출 스택까지 분석하지 않기때문에, @UiThread가 붙은 애너테이션에서 호출한 메서드에 애너테이션이 없으면 오류가 발생하지 않습니다, 해당 메서드를 사용하면 모든 메서드에 관련 애너테이션을 붙여주어야 정상적으로 사용하실 수 있게 됩니다.

```java
@UiThread
void uiJob() {  
}

@UiThread
void annotatedJob() {  
    uiJob();
}

void unannotatedJob() {  
    uiJob();
}

@WorkerThread
void backgroundJob() {  
    uiJob();          // 정상: 오류 발생
    annotatedJob();   // 정상: 오류 발생
    unannotatedJob(); // 비정상: 오류 발생 안 함
}
```

#### @CallSuper
@CallSuper 애너테이션이 붙은 메서드를 하위 클래스에서 오버라이드할 때는 반드시 상위 클래스드를 호출하도록 강제합니다.

```java
class Test {
  @CallSuper
  void helloWorld() {
    // code
  }

  void helloWorld2() {
    // code
  }
}

class Example extends Test {
  @Override
  void helloWorld() {} // Super를 넣어주지 않아서 ERROR

  @Override
  void helloWorld() {
    super.helloWorld(); // OK
  }

  @Override
  void helloWorld2() {} // @CallSuper를 지정하지 않아서 super를 넣어주지 않아도 OK
}
```

#### @CheckResult
@CheckResult 가 붙은 메서드를 호출했을 때는 반드시 메서드의 반환값을 사용하도록 강제합니다.

```java
@CheckResult
boolean isFlag() {
  boolean flag;
  // code
  return boolean;
}


isFlag();                           // Error
boolean flag = isFlag();           // OK
```

#### @RequiresPermission
@RequiresPermission 애너테이션이 나타내는 권한이 AndroidManifest.xml 에 설정되어 있지 않으면 오류가 발생합니다.

```java
@RequiresPermission(CALL_PHONE);
void callHome() { ... }

@RequiresPermission(anyOf = { ACCESS_COARSE_LOCATION, ACCESS_FINE_LOCATION })
Location getLocation() { ... }

@RequiresPermission(allOf = { READ_SMS, WRITE_SMS })
void deleteAllSpams() { ... }

@RequiresPermission(BLUETOOTH)
String ACTION = "android.bluetooth.adapter.action.REQUEST_DISCOVERABLE";

@RequiresPermission.Read(@RequiresPermission(READ_HISTORY_BOOKMARKS))
@RequiresPermission.Write(@RequiresPermission(WRITE_HISTORY_BOOKMARKS))
Uri BOOKMARKS_URI = Uri.parse("content://browser/bookmarks");
```

#### @RequiresApi
@RequiresApi 애너테이션은 호출에 필요한 최소 API 레벨을 강제하기때문에, 어플리케이션의 minSdkVersion 속성의 값이 @RequiresApi 애너테이션에 명시된 API 레벨보다 낮으면 오류가 발생합니다.

```java
@RequireApi(Build.VERSION_CODES.LOLLIPOP)
void setImage(Bitmap bitmap) {
  // code
}

setImage(bitmap); // 만약 minSdkVersion이 21 이상이면 OK 아니면 Error
```

#### @Keep

@Keep 애너테이션이 붙은 요소는 소스 코드 최적화 때, 제거되거나 이름이 변경되면 안됨을 나타냅니다. ProGuard를 이용할 때 일일이 -keep 규칙을 지정하지 않아도 @Keep 애너테이션이 붙은 요소는 바로 사용할 수 있습니다.(대신 ProGuard설정을 추가해야 합니다)

```java
-keep @android.support.annotation.Keep class *

-keep @android.support.annotation.Keep class * {
    *;
}

-keep class * {
    @android.support.annotation.Keep *;
}
```

### 마무리하며...

해당 자료는 예전부터 궁금하던 부분이었는데, [Android Support Annotations 라이브러리를 활용한 결함 탐지(NaverD2)](http://d2.naver.com/helloworld/8725603)에서 좋은 글을 올려주셔서 개인적으로 공부도 할겸 정리도 할겸 해서 좀 더 짧고 간략하게, 정리한 내용 입니다. 예제 코드를 줄일 수 있는 부분은 줄였고, 아닌 부분은 위 사이트에서 그대로 가져왔습니다. 좀 더 자세한 내용을 알고 싶으시면, 위의 링크를 따라 가시면 더 정확하고 깊은 내용을 얻으실 수 있습니다.
