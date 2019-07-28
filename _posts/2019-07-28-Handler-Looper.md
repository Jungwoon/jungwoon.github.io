---
layout: post
title: "핸들러와 루퍼(Handler & Looper)"
image: '/assets/img/'
description: '핸들러와 루퍼(Handler & Looper)'
tags:
- Android
categories:
- Android
---

## 핸들러와 루퍼(Handler & Looper)

이번 포스팅에는 안드로이드의 핸들러와 루퍼에 대해서 알아보도록 하겠습니다.

안드로이드 시스템은 기본적으로 하나의 `Main Thread(=UI Thread)`만을 가지고 있고 보통 이 스레드 안에서 작업을 하는데, 
UI 관련 작업은 반드시 `Main Thread(=UI Thread)`에서 처리를 해야하고 시간이 오래 걸릴 수 있는
`네트워크 작업`이나 `데이터베이스 작업`들은 `Main Thread(=UI Thread)`가 아닌 별도의 스레드에서만 작업을 해야합니다.

이러다가 보니 `다운로드`나 `업로드`와 같이 `네트워크 작업`은 `별도의 스레드`에서 하고 해당 스레드에서 작업한 결과를 `UI`에 보여줄때는 `Main Thread(=UI Thread)`에
표시를 해야하는 경우가 자주 발생합니다.

이렇게 되면 자연스럽게 멀티 스레드 환경에서 작업을 해야하는데, 이 때 서로다른 스레드간 통신을 하기 위해 고려해야할 부분이 많아집니다. 이때 안드로이드에서 스레드간
통신을 도와주는 도구가 바로 `핸들러와 루퍼(=Handler & Looper)`입니다.

---

### 핸들러를 통한 스레드간 통신

핸들러를 통한 스레드간 통신은 다음과 같습니다. 

![](https://miro.medium.com/max/1000/1*cPvR6xzW8oSMhcUCaJNZ4w.png)

1. 핸들러에 있는 `sendMessage()`를 통해서 `Message(=작업)`를 전달합니다.
2. 그럼 핸들러는 `Message Queue`에 차례대로 넣습니다.
3. 그럼 `Looper`가 `Message Queue`로부터 하나씩 `Message`를 뽑아서 핸들러로 전달합니다.
4. `Looper`로부터 전달받은 메시지는 `handleMessage()`를 통해서 작업을 하게 됩니다.

---

### 핸들러(Handler)

안드로이드에서 사용할 수 있는 스레드 통신 방법은 여러 가지가 있는데, 대표적인 방법 중 하나가 `Hadler`를 통해 `Message`를 전달하는 방법입니다.
기본 생성자를 통해서 `Handler`를 생성하면 호출한 스레드의 `Message Queue`와 `Looper`에 자동으로 연결됩니다.

| 메서드 | 리턴타입 | 설명
| :---: | :--- | :---
| obtainMessage() | Message | 핸들러 자신으로 지정된 메시지 객체 리턴
| sendMessage() | boolean | 메시지를 만들어서 전달을 합니다.

```java
handler = object : Handler() {
    override fun handleMessage(msg: Message) {
        super.handleMessage(msg)

        if (msg.what === START_CODE) {
            thread.start()
        } else if (msg.what === PROGRESS_CODE) {
            textView.text = "Count : ${msg.arg1}"
        }
    }
}
```

---

### 메시지(Message)

스레드 통신에서 핸들러에 데이터를 보내기 위한 클래스로 데이터를 `Message`에 담아서 핸들러로 보내면 해당 객체는 핸들러를 통해
`Message Queue`에 쌓이게 됩니다.

| 변수 | 설명
| :---: | :---
| what | 메시지 종류를 식별하기 위한 메시지 코드
| arg1 | 메시지를 통해 전달되는 정수 값1
| arg2 | 메시지를 통해 전달되는 정수 값2
| obj | 메시지를 통해 전달되는 객체

```java
val message = Message()
message.what = PROGRESS_CODE
message.arg1 = count

handler.sendMessage(message)
```

---

### 메시지 큐(Message Queue)

`Message Queue`는 이름 그대로 `Message 객체`를 `Queue 형태`로 관리하는 자료 구조를 말합니다. Queue 구조는 기본적으로
들어온 순서대로 차례대로 처리하게 됩니다. 

---

### 루퍼(Looper)

루퍼는 스레드당 하나씩 밖에 가질 수 없고, 루퍼는 `Message Queue`가 비어 있는 동안 아무 행동도 하지 않고, 메시지가 들어오면 
해당 메시지를 꺼내 적절한 `Handler`로 전달합니다.


---

### 예제 (전체소스)

![](https://miro.medium.com/max/700/1*6viIFycFoaInx_pWfJuzjQ.png)

[실행화면]


```java
package com.byjw.myapplication

import android.support.v7.app.AppCompatActivity
import android.os.Bundle
import android.os.Handler
import android.os.Message
import kotlinx.android.synthetic.main.activity_main.*

class MainActivity : AppCompatActivity() {

    lateinit var handler: Handler
    lateinit var thread: Thread

    companion object {
        private const val START_CODE = 100
        private const val PROGRESS_CODE = 101
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        progressBar.max = 100

        thread = Thread(Runnable {
            for (count in 0..100) {
                progressBar.progress = count
                Thread.sleep(100)

                val message = Message()
                message.what = PROGRESS_CODE
                message.arg1 = count

                handler.sendMessage(message)
            }
        })

        start_progress.setOnClickListener {
            if (!thread.isAlive) {
                progressBar.progress
                handler.sendEmptyMessage(START_CODE)
            }
        }
    }

    override fun onResume() {
        super.onResume()

        // msg.what : 메시지의 종류를 식별하기 위한 사용자 정의 메시지 코드
        // msg.arg1 : 메시지를 통해 전달되는 정수 값
        // msg.arg2 : 메시지를 통해 전달되는 정수 값2
        // msg.obj : 메시지를 통해 전달할 객체

        handler = object : Handler() {
            override fun handleMessage(msg: Message) {
                super.handleMessage(msg)

                if (msg.what === START_CODE) {
                    thread.start()
                } else if (msg.what === PROGRESS_CODE) {
                    textView.text = "Count : ${msg.arg1}"
                }
            }
        }


    }
}
```
[MainActivity.kt]


```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".MainActivity">

    <ProgressBar
            android:id="@+id/progressBar"
            style="?android:attr/progressBarStyleHorizontal"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_alignParentLeft="true"
            android:layout_alignParentStart="true"
            android:layout_alignParentTop="true"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent" />

    <TextView
            android:id="@+id/textView"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_alignParentTop="true"
            android:layout_centerHorizontal="true"
            android:layout_margin="8dp"
            android:layout_marginTop="120dp"
            android:text="Count"
            android:textSize="36sp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/progressBar" />

    <Button
            android:id="@+id/start_progress"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerVertical="true"
            android:layout_margin="16dp"
            android:layout_marginTop="156dp"
            android:text="Start"
            android:textSize="18sp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/textView" />

</android.support.constraint.ConstraintLayout>
```
[activity_main.xml]