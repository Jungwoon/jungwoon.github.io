---
layout: post
title: "Android Fragment 정리"
image: '/assets/img/'
description: 'Android Fragment 정리'
tags:
- Android
- Fragment
categories:
- Anroid
---

## Android Fragment 정리


### Fragment? 

엄청 오랜만에 안드로이드 포스팅을 하네요, 최근에 개인적으로 안드로이드 개발을 하다가 다시 정리할겸 이렇게 포스팅을 합니다.

Fragment는 보통 하나의 Activity안에 여러 UI를 구성할 때 이용할 수 있습니다. 그래서 Fragment는 항상 Activity내에 포함되어 있어야 하며
해당 Fragment의 수명 주기는 호스트 Activity의 생명 주기에 영향을 받습니다

예를 들어 Activity가 일시정지가 되면 그 안에 모든 Fragment도 일시정지되며 Activity가 소멸될때 Fragment들도 소멸됩니다, 하지만 Activity가
실행 중인 동안에는 각 Fragment들은 독립적으로 조작할 수 있습니다


> [Fragment Guide](https://developer.android.com/guide/components/fragments.html) : 좀더 자세한 정보를 얻고 싶으면 확인해보세요

> 사실 Fragment가 안드로이드 어플리케이션을 개발할때 좋긴 하지만 고질적인 문제들도 몇 개 가지고 있어서 관련해서 좀 더 알고 싶으면
  [프래그먼트: 안드로이드의 모든 문제의 해결책이자 원인](https://academy.realm.io/kr/posts/michael-yotive-state-of-fragments-2017/) 다음 자료를 참고 해보세요

### Fragment Life Cycle

![](https://developer.android.com/images/fragment_lifecycle.png)

위의 그림이 Google에서 제공하고 있는 Fragment의 생명주기입니다

엄청 많은데, 가장 많이 쓰이는 콜백 메서드만 일단 설명 드리도록 하겠습니다. 각각의 대한 설명은 다음과 같습니다

`onCreate()` : 시스템은 Fragment가 생성될 때 이것을 호출합니다

`onCreateView()` : Fragment가 자신의 UI를 그릴 때 호출합니다, UI를 그리기 위해 메서드에서는 View를 Return 해야 합니다.
만약 UI를 제공하지 않으면 null을 반환합니다.

`onPause()` : onPause()일때 즉 사용자가 해당 Fragemnt를 떠났을때 호출이 됩니다

### Fragment Manager

Activity 내의 Fragment를 관리하기 위해서는 FragmentManager를 사용해야 합니다

FragmentManager가 할 수 있는 일
- `findFragmentById()` or `findFragmentByTag()`로 Fragment 가져오기
- popBackStack()을 사용하여 Fragment를 BackStack에서 꺼내기
- FragmentTransaction을 가져오기

```java

FragmentManager fragmentManager = getFragmentManager(); // 쉽게 가져올 수 있습니다

```

### Fragment Transaction

FragmentTransaction의 API를 사용하면 Fragment의 추가, 제거, 변경 등의 작업을 할 수 있습니다.

FragmentTransaction을 가져오는 방법

```java
FragmentManager fragmentManager = getFragmentManager();
FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();
```

위에서 가져온 FragmentTransaction을 이용하여 아래와 같은 작업을 할 수 있습니다

- add() : Fragment 추가
- remove() : Fragment 제거
- replace() : Fragment 변경

```java
FragmentTransaction fragmentTransaction = getSupportFragmentManager().beginTransaction();
fragmentTransaction.add(R.id.fragment_container, Movies.newInstance()).commit();
```
Transaction 작업 후 마지막에는 반드시 commit()을 호출해야 적용이 됩니다.


### Basic Usage

![](https://cdn-images-1.medium.com/max/1600/1*XG1aGAzN8PYJVhxEWTgMUA.png)


Books.class

```java

public class Books extends Fragment {

    public static Books newInstance() {
        return new Books();
    }

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        return inflater.inflate(R.layout.fragment_books, container, false); // 여기서 UI를 생성해서 View를 return
    }

}

```

fragment_books.xml

```xml
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.byjw.materialdesignandroid.Books.Books">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:text="Books Fragment" />

</FrameLayout>

```


MainActivity.class

```java

public class MainActivity extends AppCompatActivity {

    @BindView(R.id.navigation)
    BottomNavigationView navigation;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ButterKnife.bind(this);

        navigation.setOnNavigationItemSelectedListener(mOnNavigationItemSelectedListener);

        FragmentTransaction fragmentTransaction = getSupportFragmentManager().beginTransaction();
        fragmentTransaction.add(R.id.fragment_container, Movies.newInstance()).commit();

    }

    private BottomNavigationView.OnNavigationItemSelectedListener mOnNavigationItemSelectedListener
            = new BottomNavigationView.OnNavigationItemSelectedListener() {

        @Override
        public boolean onNavigationItemSelected(@NonNull MenuItem item) {
            switch (item.getItemId()) {
                case R.id.navigation_movies:
                    replaceFragment(Movies.newInstance());
                    return true;
                case R.id.navigation_music:
                    replaceFragment(Music.newInstance());
                    return true;
                case R.id.navigation_books:
                    replaceFragment(Books.newInstance());
                    return true;
                case R.id.navigation_shopping:
                    replaceFragment(Shopping.newInstance());
                    return true;
            }
            return false;
        }

    };

    // Fragment 변환을 해주기 위한 부분, Fragment의 Instance를 받아서 변경
    private void replaceFragment(Fragment fragment) {
        FragmentManager fragmentManager = getSupportFragmentManager();
        FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();
        fragmentTransaction.replace(R.id.fragment_container, fragment).commit();
    }

}

```


activity_main.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/container"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context="com.byjw.materialdesignandroid.MainActivity">

    <FrameLayout
        android:id="@+id/fragment_container"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"/>


    <android.support.design.widget.BottomNavigationView
        android:id="@+id/navigation"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        android:background="?android:attr/windowBackground"
        app:menu="@menu/navigation" />

</LinearLayout>

```
