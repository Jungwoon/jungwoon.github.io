---
layout: post
title: "Bottom Navigation"
image: '/assets/img/'
description: 'Material Design #1'
tags:
- Android
- Design
categories:
- Material Design
---

## 만들려고 하는 Design

![Bottom Navigation](https://material-design.storage.googleapis.com/publish/material_v_9/0B3321sZLoP_HZHA1UVAyRFpMVDQ/components_bottomnavigation_usage5.png)

> [from Google](https://material.google.com/components/bottom-navigation.html)

## Step
> Github에 올라온 tyzlmjj님이 만든 라이브러리와 샘플소스를 참고해서 만들었습니다.
> <https://github.com/tyzlmjj/PagerBottomTabStrip>

1. AndroidStudio에서 새로운 프로젝트를 생성하고 `Empty Activity`를 선택 합니다.
![image](https://github.com/Jungwoon/jungwoon.github.img/blob/master/bottom_navigation/1.png?raw=true)

2. build.gradle(`Module: app`)의 `dependencies` 부분에 다음을 추가합니다.
`compile 'me.majiajie:pager-bottom-tab-strip:1.0.0'`
![image](https://github.com/Jungwoon/jungwoon.github.img/blob/master/bottom_navigation/2.png?raw=true)

3. Project File Tree
![image](https://github.com/Jungwoon/jungwoon.github.img/blob/master/bottom_navigation/3.png?raw=true)

## activity_main.xml
Layout에 `Framelayout`과 `me.majiajie.pagerbottomtabstrip.PagerBottomTablayout`
(실제 Bottom Layout이 되는 부분)을 추가합니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    tools:context="com.byjw.material.bottomnavigation.MainActivity">

    <FrameLayout
        android:id="@+id/frameLayout"
        android:layout_above="@+id/tab"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>

    <!-- Library로부터 BottomNavigation 가져오는 부분(gradle에 library가 등록되어야 함) -->
    <me.majiajie.pagerbottomtabstrip.PagerBottomTabLayout
        android:id="@+id/tab"
        app:elevation="8dp"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"/>

</RelativeLayout>
```

## MainActivity

```java
package com.byjw.material.bottomnavigation;

import android.os.Bundle;
import android.support.v4.app.Fragment;
import android.support.v4.app.FragmentTransaction;
import android.support.v7.app.AppCompatActivity;
import java.util.ArrayList;
import java.util.List;
import me.majiajie.pagerbottomtabstrip.Controller;
import me.majiajie.pagerbottomtabstrip.PagerBottomTabLayout;
import me.majiajie.pagerbottomtabstrip.TabItemBuilder;
import me.majiajie.pagerbottomtabstrip.TabLayoutMode;
import me.majiajie.pagerbottomtabstrip.listener.OnTabItemSelectListener;

public class MainActivity extends AppCompatActivity {

    Controller controller;
    List<Fragment> mFragments;

    // BottomNavigation의 배경색 부분
    int[] tabColors = {0xFF607D8B, 0xFF00796B, 0xFFF57C00, 0xFF5B4947};

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        initFragment(); // Fragment 초기화 해주는 부분
        initBottomNavigation(); // Bottom Navigation 초기화 해주는 부분
    }

    private void initFragment() {
        mFragments = new ArrayList<>();

        // Fragment View 만들어주는 부분
        mFragments.add(createFragment("Movies & TV"));
        mFragments.add(createFragment("Music"));
        mFragments.add(createFragment("Books"));
        mFragments.add(createFragment("Newsstand"));

        FragmentTransaction transaction = getSupportFragmentManager().beginTransaction();
        transaction.add(R.id.frameLayout,mFragments.get(0));
        transaction.commit();
    }

    private void initBottomNavigation() {
        PagerBottomTabLayout pagerBottomTabLayout = (PagerBottomTabLayout) findViewById(R.id.tab);

        // Tab 추가해주는 부분
        TabItemBuilder tabItemBuilder = new TabItemBuilder(this).create()
                .setDefaultIcon(R.drawable.ic_ondemand_video_white_24dp)
                .setText("Movies & TV")
                .setSelectedColor(tabColors[0])
                .setTag("Movies & TV")
                .build();

        // Tab에 들어갈 아이템들 넣어주는 부분
        controller = pagerBottomTabLayout.builder()
                .addTabItem(tabItemBuilder)
                .addTabItem(R.drawable.ic_music_note_white_24dp, "Music", tabColors[1])
                .addTabItem(R.drawable.ic_book_white_24dp, "Books", tabColors[2])
                .addTabItem(R.drawable.ic_chrome_reader_mode_white_24dp, "Newsstand", tabColors[3])
                .setMode(TabLayoutMode.HIDE_TEXT | TabLayoutMode.CHANGE_BACKGROUND_COLOR)
                .build();

        // pagerBottomTabLayout에 리스너 등록해주는 부분
        controller.addTabItemClickListener(listener);
    }

    // Bottom Navigation 눌렀을때 리스너 부분
    OnTabItemSelectListener listener = new OnTabItemSelectListener() {
        @Override
        public void onSelected(int index, Object tag) {
            FragmentTransaction transaction = getSupportFragmentManager().beginTransaction();
            transaction.replace(R.id.frameLayout, mFragments.get(index));
            transaction.commit();
        }

        @Override
        public void onRepeatClick(int index, Object tag) { }
    };

    // Fragment를 생성해주는 부분
    private Fragment createFragment(String content) {
        AFragment fragment = new AFragment();
        Bundle bundle = new Bundle();
        bundle.putString("content", content);
        fragment.setArguments(bundle);

        return fragment;
    }
}

```

## AFragment

```java
package com.byjw.material.bottomnavigation;

import android.content.Context;
import android.os.Bundle;
import android.support.annotation.Nullable;
import android.support.v4.app.Fragment;
import android.view.Gravity;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.TextView;

public class AFragment extends Fragment
{
    private Context mContext;

    @Override
    public void onAttach(Context context) {
        super.onAttach(context);
        mContext = context;
    }

    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        String content = getArguments().getString("content");
        TextView textView = new TextView(mContext);
        textView.setTextSize(30);
        textView.setGravity(Gravity.CENTER);
        textView.setText(content);
        textView.setBackgroundColor(0xFFececec);
        return textView;
    }
}
```

7. 결과
![image](https://github.com/Jungwoon/jungwoon.github.img/blob/master/bottom_navigation/result.png?raw=true)
