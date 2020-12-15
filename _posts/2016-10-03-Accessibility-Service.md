---
layout: post
title: "Accessibility Service 사용방법"
image: '/assets/img/'
description: '접근성 서비스를 활용하는 방법'
tags:
- Android
- Accessibility
categories:
- Android
---

안드로이드의 접근성 서비스를 사용하는 방법에 대해서 알아보겠습니다.

### 설정해야하는 순서
1. android.accessibilityservice.AccessibilityService에서 상속받는 클래스 만들기
2. AndroidManifest.xml에 접근성 서비스 등록하기
3. res/xml/accessibility_service_config.xml 만들기
4. res/values/strings.xml 에 accessibility_description 추가하기


### MainActivity
해당 앱의 패키지 네임을 가지고 접근성 권한을 가지고 있는지
비교하여, 권한을 가지고 있지 않으면 다이얼로그를 띄워서 설정을 하도록 설정화면을 띄워줍니다.

```java
package com.byjw.accessibilityservice;

import android.accessibilityservice.AccessibilityServiceInfo;
import android.app.AlertDialog;
import android.content.Context;
import android.content.DialogInterface;
import android.content.Intent;
import android.os.Bundle;
import android.provider.Settings;
import android.support.v7.app.AppCompatActivity;
import android.view.accessibility.AccessibilityManager;

import java.util.List;

public class MainActivity extends AppCompatActivity {
    private static final String TAG = "MainActivity";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 접근성 권한이 없으면 접근성 권한 설정하는 다이얼로그 띄워주는 부분
        if(!checkAccessibilityPermissions()) {
            setAccessibilityPermissions();
        }
    }

    // 접근성 권한이 있는지 없는지 확인하는 부분
    // 있으면 true, 없으면 false
    public boolean checkAccessibilityPermissions() {
        AccessibilityManager accessibilityManager = (AccessibilityManager) getSystemService(Context.ACCESSIBILITY_SERVICE);

        // getEnabledAccessibilityServiceList는 현재 접근성 권한을 가진 리스트를 가져오게 된다
        List<AccessibilityServiceInfo> list = accessibilityManager.getEnabledAccessibilityServiceList(AccessibilityServiceInfo.DEFAULT);

        for (int i = 0; i < list.size(); i++) {
            AccessibilityServiceInfo info = list.get(i);

            // 접근성 권한을 가진 앱의 패키지 네임과 패키지 네임이 같으면 현재앱이 접근성 권한을 가지고 있다고 판단함
            if (info.getResolveInfo().serviceInfo.packageName.equals(getApplication().getPackageName())) {
                return true;
            }
        }
        return false;
    }

    // 접근성 설정화면으로 넘겨주는 부분
    public void setAccessibilityPermissions() {
        AlertDialog.Builder gsDialog = new AlertDialog.Builder(this);
        gsDialog.setTitle("접근성 권한 설정");
        gsDialog.setMessage("접근성 권한을 필요로 합니다");
        gsDialog.setPositiveButton("확인", new DialogInterface.OnClickListener() {
            public void onClick(DialogInterface dialog, int which) {
                // 설정화면으로 보내는 부분
                startActivity(new Intent(Settings.ACTION_ACCESSIBILITY_SETTINGS));
                return;
            }
        }).create().show();
    }
}
```


### AccessibilityService
접근성 권한을 가지게 되면 동작하게 되는 부분으로 여기서 연결과 이벤트 발생시 콜백을 받을 수 있습니다.

```java
package com.byjw.accessibilityservice;

import android.accessibilityservice.AccessibilityServiceInfo;
import android.util.Log;
import android.view.accessibility.AccessibilityEvent;

/**
 * Created by JW on 16. 10. 3..
 */
public class AccessibilityService extends android.accessibilityservice.AccessibilityService {
    private static final String TAG = "AccessibilityService";

    // 이벤트가 발생할때마다 실행되는 부분
    @Override
    public void onAccessibilityEvent(AccessibilityEvent event) {
//        Log.e(TAG, "Catch Event : " + event.toString());
        Log.e(TAG, "Catch Event Package Name : " + event.getPackageName());
        Log.e(TAG, "Catch Event TEXT : " + event.getText());
        Log.e(TAG, "Catch Event ContentDescription  : " + event.getContentDescription());
        Log.e(TAG, "Catch Event getSource : " + event.getSource());
        Log.e(TAG, "=========================================================================");
    }

    // 접근성 권한을 가지고, 연결이 되면 호출되는 함수
    public void onServiceConnected() {
        AccessibilityServiceInfo info = new AccessibilityServiceInfo();

        info.eventTypes = AccessibilityEvent.TYPES_ALL_MASK; // 전체 이벤트 가져오기
        info.feedbackType = AccessibilityServiceInfo.DEFAULT | AccessibilityServiceInfo.FEEDBACK_HAPTIC;
        info.notificationTimeout = 100; // millisecond

        setServiceInfo(info);
    }

    @Override
    public void onInterrupt() {
        // TODO Auto-generated method stub
        Log.e("TEST", "OnInterrupt");
    }
}
```

### AndroidManifest.xml
application 태그 안에 accessibilityservice 등록을 합니다

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest package="com.byjw.accessibilityservice"
          xmlns:android="http://schemas.android.com/apk/res/android">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>

                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>

        <!-- 접근성 관련 서비스 부분 -->
        <service
            android:name="AccessibilityService"
            android:permission="android.permission.BIND_ACCESSIBILITY_SERVICE" >
            <intent-filter>
                <action android:name="android.accessibilityservice.AccessibilityService" />
            </intent-filter>

            <meta-data
                android:name="android.accessibilityservice"
                android:resource="@xml/accessibility_service_config" />
        </service>

    </application>

</manifest>
```

### accessibility_service_config.xml
res밑에 xml이란 디렉토리를 만들고 해당 xml 파일을 새로 생성해야 합니다.

```xml
<?xml version="1.0" encoding="utf-8"?>

<accessibility-service
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:accessibilityEventTypes="typeAllMask"
    android:accessibilityFeedbackType="feedbackAllMask"
    android:notificationTimeout="100"
    android:description="@string/accessibility_description"
    android:canRetrieveWindowContent="true" />
```

### string.xml
접근성 권한 설정하는 화면에서 왜 해당 권한이 필요한지에 대한 설명을 넣어줍니다.

```xml
<resources>
    <string name="app_name">AccessibilityService</string>
    <!-- Accessibility -->
    <string name="accessibility_description">접근성 권한을 필요로 합니다.</string>
</resources>

```

### 결과

![img](https://cdn-images-1.medium.com/max/2000/1*OnMNgLpNwP4QHzKP12aukA.png)

![img](https://cdn-images-1.medium.com/max/400/1*mgdlBJZNIpF2VovvCOBuQw.png)

![img](https://cdn-images-1.medium.com/max/1200/1*p-vzB9Q9UCl4sSNh1VEEpA.png)


***

예제는 [github](https://github.com/Jungwoon/AccessibilityService)에 올라가 있습니다.

주석으로 큰 설명은 다 달아놨지만 궁금하신 부분이 있으시면 코멘트 남겨주세요!!
