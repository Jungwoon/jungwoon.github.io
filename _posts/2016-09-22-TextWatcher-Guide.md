---
layout: post
title: "TextWatcher 예제"
image: '/assets/img/'
description: '값을 입력할때마다 바로바로 변화된 값을 보여주기'
tags:
- Android
- TextWatcher
categories:
- Android
---

안드로이드에서 뭔가를 입력할때마다 변화된 값을 보여줘야 하는 경우가 있습니다.
예를 들어서 환율 정보 같은 경우에, 제가 한국의 화폐를 입력하면,
실시간으로 달러로 변경된 값을 보여줘야 하는 경우가 그렇습니다.


이렇게 실시간으로 변화를 해주기 위해서는 TextWatcher란 기능을 이용하면 됩니다.

아래는 예제입니다.

### ScreenShot

예제는 숫자를 입력하면 100씩 곱해서 상단에 연산된 값을 보여주는 간단한 예제입니다.

![img](https://cdn-images-1.medium.com/max/1600/1*pTSuy_IVdj8D5rAl-bl2Kg.png)


### activity_main.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    android:id="@+id/activity_main"
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context="com.byjw.textwatcher.MainActivity">

    <TextView
        android:text="TextView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/textView"
        android:layout_marginTop="127dp" android:layout_alignParentTop="true"
        android:layout_centerHorizontal="true"/>
    <EditText
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:inputType="number"
        android:ems="10"
        android:layout_below="@+id/textView" android:layout_centerHorizontal="true"
        android:layout_marginTop="30dp" android:id="@+id/editText"/>
</RelativeLayout>

```



### MainActivity
```java
public class MainActivity extends AppCompatActivity {
    TextView textView;
    EditText editText;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        textView = (TextView)findViewById(R.id.textView); // 결과를 보여줄 TextView
        editText = (EditText)findViewById(R.id.editText); // 값을 입력할 EditText

        editText.addTextChangedListener(textWatcher); // TextWatcher 리스너 등록
    }

    // EidtText가 눌릴때마다 감지하는 부분
    TextWatcher textWatcher = new TextWatcher() {
        @Override
        public void afterTextChanged(Editable edit) {
            // Text가 바뀌고 동작할 코드
        }

        @Override
        public void beforeTextChanged(CharSequence s, int start, int count, int after) {
            // Text가 바뀌기 전 동작할 코드
        }

        //
        @Override
        public void onTextChanged(CharSequence s, int start, int before, int count) {
            // 입력받은 값에 100을 곱한다
            int changeValue = Integer.parseInt(editText.getText().toString()) * 100;
            textView.setText(changeValue+""); // 위에서 얻은 변경된값을 textView에 표시한다
        }
    };
}
```

예제는 [github](https://github.com/Jungwoon/TextWatcher)에 올라가 있습니다.

주석으로 큰 설명은 다 달아놨지만 궁금하신 부분이 있으시면 코멘트 남겨주세요!!
