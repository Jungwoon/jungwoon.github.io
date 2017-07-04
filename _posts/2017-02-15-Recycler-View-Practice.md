---
layout: post
title: "Recycler View 연습"
image: '/assets/img/'
description: 'Recycler를 뷰를 활용한 간단한 앱 만들기'
tags:
- Android
- RecyclerView
categories:
- Android
---

# RecyclerView
: 기존의 ListView 보다 유연하고 향상된 성능을 가진 RecyclerView는 안드로이드 버전 5.0에서 발표되었습니다. 기존의 ListView와의 가장 큰 차이점으로는 Layout Manager와 View Holder 패턴의 의무화가 있습니다.

### Adapter
: 기존의 ListView에서 사용하는 Adapter와 같은 개념으로, 데이터와 아이템에 대한 View 생성

### LayoutManager
: 아이템의 배치를 담당하며, 기존의 ListView에서 제공하던 수직 스크롤뿐만 아니라, 수평, 격자 배열도 제공을 합니다, 아래는 RecyclerView의 LayoutManager가 제공하는 기능입니다.

- LinearLayoutMaanger : 가로 또는 세로 스크롤 목록
- GridLayoutManager : 그리드 형식의 목록
- StaggeredGridLayoutManager : 지그재그형의 그리드 형식 목록

### ViewHolder
: ViewHolder 패턴을 이용하여, 매번 아이템을 생성할때 하던 뷰 바인딩을 한번만 하고, 바인딩된 객체를 가져다 쓰는 형식으로 리소스를 줄인 부분으로, RecyclerView에서는 ViewHolder를 강제해놓았습니다.


### 예제

![img](https://cdn-images-1.medium.com/max/800/1*0lMV254s3L5W7rEaL9oJ8w.png)

#### MainActivity

```java
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.support.v7.widget.RecyclerView;
import android.support.v7.widget.StaggeredGridLayoutManager;

import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {
    private RecyclerView mRecyclerView;
    private RecyclerView.Adapter mAdapter;
    private RecyclerView.LayoutManager mLayoutManager;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mRecyclerView = (RecyclerView) findViewById(R.id.my_recycler_view);
        mRecyclerView.setHasFixedSize(true);

        ArrayList<Item> items = new ArrayList<>();

        items.add(new Item(R.drawable.a, "Micky Mouse1"));
        items.add(new Item(R.drawable.b, "Micky Mouse2"));
        items.add(new Item(R.drawable.c, "Micky Mouse3"));
        items.add(new Item(R.drawable.d, "Micky Mouse4"));
        items.add(new Item(R.drawable.a, "Micky Mouse1"));
        items.add(new Item(R.drawable.b, "Micky Mouse2"));
        items.add(new Item(R.drawable.c, "Micky Mouse3"));
        items.add(new Item(R.drawable.d, "Micky Mouse4"));

        // 이 부분에서 정렬 방식을 설정합니다.
        mLayoutManager = new StaggeredGridLayoutManager(2, StaggeredGridLayoutManager.VERTICAL);
        mRecyclerView.setLayoutManager(mLayoutManager);

        mAdapter = new MyAdapter(items, getApplicationContext());
        mRecyclerView.setAdapter(mAdapter);

    }
}
```


#### MyAdapter

```java
import android.content.Context;
import android.support.v7.widget.RecyclerView;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.view.animation.Animation;
import android.view.animation.AnimationUtils;
import android.widget.ImageView;
import android.widget.TextView;

import java.util.ArrayList;


/**
* Created by jungwoon on 2017. 1. 18..
*/

public class MyAdapter extends RecyclerView.Adapter<MyAdapter.ViewHolder> {
    private Context context;
    private ArrayList<Item> items;
    private int lastPosition = -1;


    public MyAdapter(ArrayList<Item> items, Context context) {
        this.items = items;
        this.context = context;
    }

    // 뷰 바인딩 부분을 한번만 하도록, ViewHolder 패턴 의무화
    public static class ViewHolder extends RecyclerView.ViewHolder {
        ImageView imageView;
        TextView textView;

        public ViewHolder(View view) {
            super(view);

            imageView = (ImageView) view.findViewById(R.id.image_view);
            textView = (TextView) view.findViewById(R.id.text_view);
        }
    }

    // 새로운 뷰 생성
    @Override
    public MyAdapter.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.item_cardview, parent, false);
        ViewHolder viewHolder = new ViewHolder(view);

        return viewHolder;
    }

    // RecyclerView의 getView 부분을 담당하는 부분
    @Override
    public void onBindViewHolder(ViewHolder holder, int position) {
        holder.imageView.setImageResource(items.get(position).getImage());
        holder.textView.setText(items.get(position).getImageTitle());

        setAnimation(holder.imageView, position);
    }

    // Item 개수를 반환하는 부분
    @Override
    public int getItemCount() {
        return items.size();
    }

		// View가 나올때 Animation을 주는 부분
    private void setAnimation(View viewToAnimate, int position) {
        if (position > lastPosition) {
            Animation animation = AnimationUtils.loadAnimation(context, android.R.anim.slide_in_left);
            viewToAnimate.setAnimation(animation);
            lastPosition = position;
        }
    }
}
```

#### Item

```java
/**
* Created by jungwoon on 2017. 1. 18..
*/

public class Item {

    private int image;
    private String imageTitle;

    public Item(int image, String imageTitle) {
        this.image = image;
        this.imageTitle = imageTitle;
    }

    public int getImage() {
        return image;
    }

    public String getImageTitle() {
        return imageTitle;
    }
}
```

#### activity_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context="com.nkc.recyclerviewtest.MainActivity">

    <android.support.v7.widget.RecyclerView
        android:id="@+id/my_recycler_view"
        android:scrollbars="vertical"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>

</RelativeLayout>
```

#### item_cardview.xml

```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:card_view="http://schemas.android.com/apk/res-auto"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" >

    <!-- A CardView that contains a TextView -->
    <android.support.v7.widget.CardView
        xmlns:card_view="http://schemas.android.com/apk/res-auto"
        android:id="@+id/card_view"
        android:layout_gravity="center"
        android:layout_width="150dp"
        android:layout_height="wrap_content"
        android:layout_margin="5dp"
        card_view:cardCornerRadius="8dp">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical">

            <ImageView
                android:id="@+id/image_view"
                android:layout_width="match_parent"
                android:layout_height="wrap_content" />

            <TextView
                android:id="@+id/text_view"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_gravity="center"/>

        </LinearLayout>


    </android.support.v7.widget.CardView>

</LinearLayout>
```
