---
layout: post
title: "Android Annotations Guide"
image: '/assets/img/'
description: 'Android Annotations로 코드를 줄이자'
tags:
- Android
- Android Annotation
categories:
- Android
---

Android Annotations 사용법에 대해서 알아보겠습니다.

### @EActivity

```java
@EActivity(R.layout.activity_main) // layout binding
public class MainActivity extends Activity {
  // Code
}
```

### @EService
```java
@EService
public class TestService extends Service {
  @Override
  public IBinder onBind(Intent intent) {
    return null;
  }
  // code
}
```

### @EFragment
```java
@EFragment(R.layout.my_fragment_layout)
public class MyFragment extends Fragment {
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        return null;
    }
}
```

### @ViewById
```java
@ViewById(R.id.image_view)
ImageView testImage;

// Auto Binding
@ViewById
ImageView imageView;
```

### @Click
```java
@Click
void btnClick() {
  Toast.makeText(getApplicationContext(), "TOAST TEST", Toast.LENTGH_SHORT).show();
}
```

### @LongClick
```java
@LongClick
void btnLongClick() {
  Toast.makeText(getApplicationContext(), "TOAST TEST", Toast.LENTGH_SHORT).show();
}
```

### @Receiver
```java
@Receiver(actions - ACTION_TO_ACTIVITY)
protected void onActionToActivity() {
  Toast.makeText(getApplicationContext(), "RECEIVER TEST", Toast.LENTGH_SHORT).show();
}
```

### (Tip) AutoBinding

#### xml
```xml
<Button
  android:id="@+id/btnFirst"
  android:layout_width="wrap_content"
  android:layout_height="wrap_content"
  android:text="First Button" />
```

#### java
```java
@Click(R.id.btnFirst)
void btnFirst() {
  Toast.makeText(getApplicationContext(), "First Button", Toast.LENTGH_SHORT).show();
}
```
위는 @Click에 리소스 ID를 직접 이벤트 메소드에 바인딩 시켜서 이벤트를 만드는 예제

#### xml
```xml
<Button
  android:id="@+id/btnSecond"
  android:layout_width="wrap_content"
  android:layout_height="wrap_content"
  android:text="Second Button" />
```

#### java
```java
@Click
void btnSecond() {
  Toast.makeText(getApplicationContext(), "Second Button", Toast.LENTGH_SHORT).show();
}
```
위는 @Click에 리소스 ID가 없지만 리소스 ID와 이벤트 메소드의 이름이 같아서 AndroidAnnotations가 자동으로 바인딩을 해주는 예제입니다. 이를 `Android Annotations에서는 InferringIDFromMethodName`이라고 부르고 있습니다.

#### xml
```xml
<Button
  android:id="@+id/btn_third"
  android:layout_width="wrap_content"
  android:layout_height="wrap_content"
  android:text="Third Button" />
```

#### java
```java
@Click
void btnThird() {
  Toast.makeText(getApplicationContext(), "Third Button", Toast.LENTGH_SHORT).show();
}
```

위의 예제에서는 리소스 ID는 btn_third이고, 이벤트 메소드의 이름은 btnThird이지만, 자동으로 바인딩이 됩니다.

그 이유는 기본적으로 Resource ID에서는 snake_case를 이용하고 메소드의 이름에는 camelCase를 사용하기 때문에 이와 같은 경우에도 자동으로 바인딩이 됩니다.

#### xml
```xml
<Button
  android:id="@+id/btn_fourth"
  android:layout_width="wrap_content"
  android:layout_height="wrap_content"
  android:text="Fourth Button" />
```

#### java
```java
@Click
void btnFourthClicked() {
  Toast.makeText(getApplicationContext(), "Fourth Button", Toast.LENTGH_SHORT).show();
}
```
위의 예제에서는 메소드 명뒤에 Clicked가 붙었지만, 이 경우에도 자동으로 바인딩이 됩니다. 그 이유는 Annotation으로 @Click 이기 때문입니다. 그래서 @LongClick 인 경우에는 메소드 명에 LongClicked가 붙어도 자동으로 바인딩이 됩니다.

### @Background

```java
@EActivity(R.layout.activity_main)
public class MainActivity extends Activity {
  private static final String ID = "background_id";
  private static int count = 0;

  @Override
  public void onCreate() {
    super.onCreate();

    backgroundTask();
  }

  // id를 통해서 접근
  @Background(id = ID)
  private void backgroundTask() {
    boolean flag = true;

    while(flag) {
      try {
        count++;
        Thread.sleep(100);
        // 증가한 count를 로그로 찍어준다.
        Log.e(TAG, "count : " + count);
      }
      catch (Exception e) {
        // btnStopTask()가 호출되면 예외 발생
        flag = false;
      }
    }

  }

  @Click
  void btnStopTask() {
    // InterruptedException을 발생시킴
    BackgroundExecutor.cancelAll(TASK_ID, true);
  }

}
```

### @UiThread
```java
@EActivity(R.layout.activity_main)
public class MainActivity extends Activity {
  private static final String ID = "background_id";
  private static int count = 0;

  @Override
  public void onCreate() {
    super.onCreate();

    backgroundTask();
  }

  @Background(id = ID)
  private void backgroundTask() {
    boolean flag = true;

    while(flag) {
      try {
        count++;
        Thread.sleep(100);
        // 값을 바꾸고 UiThread를 호출하여 TextView에 변경된 값을 찍어준다.
        uiTask();

      }
      catch (Exception e) {
        // btnStopTask()가 호출되면 예외 발생
        flag = false;
      }
    }
  }

  @Click
  void btnStopTask() {
    // InterruptedException을 발생시킴
    BackgroundExecutor.cancelAll(TASK_ID, true);
  }

  @UiThread
  void uiTask() {
    textView.setText("count : " + count);
  }
}
```

### @StringRes
```java
@StringRes(R.string.sample)
String strSample;

// Auto Binding
@StringRes(R.string.sample)
String sample;
```

### @ColorRes
```java
@ColorRes(R.color.color)
int mainColor;

// Auto Binding
@ColorRes
int color;
```

### @AnimationRes
```java
@ColorRes(R.anim.zoom_in)
XmlResourceParser zoomInAnimation;

// Auto Binding
@ColorRes
XmlResourceParser zoomIn;
```

### @Rest
```java
@Rest("http://www.bookmarks.com")
public interface BookmarkClient {

  @Get("/bookmarks/{userId}?search={search}")
  Bookmarks getBookmarks(@Path String search, @Path String userId);

}
```

### Rest Example

#### MainActivity
```java
@EActivity(R.layout.activity_main)
public class MainActivity extends Activity {
  private int requestCount = 0;

  @ViewByID
  TextView TextView;

  @RestService
  RestProtocol restProtocol;

  @Click
  void btnRequestToServerClicked() {
    requestToServer();
  }

  @Backgroundvoid requestToServer() {
    requestCount++;
    Message message = restProtocol.echoProtocol(new Message("Root", "Hello?" + requestCount));
    setTextView(message.getData());
  }

  @UiThread
  void setTextView(String param) {
    textView.setText("Server Echo : " + param);
  }
}
```

#### RestProtocol

```java
@Rest(rootUrl = "http://test.com", converters = { MappingJackson2HttpMessageConverter.class})
public interface RestProtocol {
  @Post("/send")
  Message echoProtocol(Message message);
}
```

#### Message

```java
public class Message {
  private String name;
  private String data;

  public Message(String name, String data) {
    this.name = name;
    this.data = data;
  }

  public String getName() {
    return name;
  }

  public String getData() {
    return data;
  }

}
```

### 마무리하며...
거의 설명없이 각각의 Annotations에 대한 샘플 소스만 보이는걸로 정리를 하였습니다. 이외에도 훨씬 많은 기능들이 있으므로 [Android Annotations Cookbook](https://github.com/androidannotations/androidannotations/wiki/Cookbook) 에서 확인할 수 있습니다. 추후 자주 사용될만한 기능들은 추가하도록 하겠습니다.
