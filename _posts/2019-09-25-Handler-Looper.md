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

`Handler`를 생성하면 호출한 스레드의 `Message Queue`와 `Looper`에 자동으로 연결됩니다.

| 메서드 | 리턴타입 | 설명
| :---: | :--- | :---
| obtainMessage() | Message | 핸들러 자신으로 지정된 메시지 객체 리턴
| sendMessage(msg: Message) | boolean | 메시지 큐에 `message`를 만들어서 전달을 합니다.
| post(runnable: Runnable) | void | 메시지 큐에 `runnable` 전달 
| sendEmptyMessage(what: Int) | boolean | 메시지 큐에 메시지를 만들어서 전달을 합니다.
| removeMessages(what: Int) | void | 전달한 `what` 코드 메시지를 메시지 큐에서 삭제합니다. 
| handleMessage(msg: Message) | void | 루퍼를 통해 메시지 큐에서 꺼낸  `Message`나 `Runnable` 처리 

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

### 호출 순서

```
sendMessage() -> handleMessage()
```

---

### 루퍼(Looper)

루퍼는 스레드당 하나씩 밖에 가질 수 없고, 루퍼는 `Message Queue`가 비어 있는 동안 아무 행동도 하지 않고, 메시지가 들어오면 해당 메시지를 꺼내 적절한 `Handler`로 전달합니다.
기본적으로 새로 생성한 스레드는 루퍼를 가지지 않고 `Looper.prepare()` 메서드를 호출해야지 `Looper`가 생성이 됩니다.

루퍼는 무한히 실행되는 메시지 루프를 통해 큐에 메시지가 들어오는지 감시하며 들어온 메시지를 처리할 핸들러를 찾아서 handleMessage()를 호출합니다.

| 메서드 | 리턴타입 | 설명
| :---: | :--- | :---
| prepare() | void | 루퍼를 생성
| loop() | void | 무한히 루프를 돌며 Message Queue에 쌓인 Message나 Runnable객체를 핸들러에 전달
| quit() | void | 루프 종

---

### 예제 (전체소스)

![](https://miro.medium.com/max/1080/1*Yy_9wFSx6RhQqbDcm222-Q.png)

[실행화면]


```java
package com.byjw.handlerthread

import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.widget.TextView

class MainActivity : AppCompatActivity() {

    private val countTextView by lazy {
        findViewById<TextView>(R.id.txt)
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val myHandler = MyHandler(countTextView)
        val workerThread = WorkerThread(myHandler)
        workerThread.start()


    }
}
```
[MainActivity.kt]

```java
package com.byjw.handlerthread

import android.os.Handler
import android.os.Message
import android.widget.TextView
import com.byjw.handlerthread.Contract.Companion.COUNT_DATA_KEY
import com.byjw.handlerthread.Contract.Companion.MSG_CODE

class MyHandler(private val countTextView: TextView) : Handler() {
    override fun handleMessage(msg: Message) {
        when(msg.what) {
            MSG_CODE -> countTextView.text = msg.data.getInt(COUNT_DATA_KEY).toString()
        }
    }
}
```
[MyHandler.kt]

```java
package com.byjw.handlerthread

interface Contract {

    companion object {
        const val MSG_CODE = 100
        const val COUNT_DATA_KEY = "count"
    }
    
}
```
[Contract.kt]

```java
package com.byjw.handlerthread

import android.os.Bundle
import android.os.Handler
import android.os.Message
import com.byjw.handlerthread.Contract.Companion.COUNT_DATA_KEY
import com.byjw.handlerthread.Contract.Companion.MSG_CODE

class WorkerThread(private val handler: Handler) : Thread() {

    override fun run() {
        for (i in 0..100) {
            val bundle = Bundle()
            bundle.putInt(COUNT_DATA_KEY, i)

            val message = Message()
            message.what = MSG_CODE
            message.data = bundle

            handler.sendMessage(message)
            sleep(100)
        }
    }
}
```
[WorkerThread.kt]

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/txt"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```
[activity_main.xml]

---

### 핸들러 스레드(Handler Thread)

메인 Thread는 기본적으로 Looper가 생성되어 있지만, 일반적으로 새로 생성된 Thread는 Looper를 가지고 있지 않아 메시지를 받을 수 없습니다. 이때 메시지를 받기 위해서는 `Looper.prepare()`메서드와 `Looper.loop()`에서드를 통해
Looper를 생성하고 메시지를 받을 수 있습니다. 이를 쉽게 이용하기 위한 `HandlerThread`는 `Thread`를 상속하고 자동으로 `Looper.prepare()`와 `Looper.loop()`를 실행하여 `개별 Looper`를 가지는 내부에서 무한루프를 도는 백그라운드 스레드입니다.

백그라운드에서 어떤 작업을 처리해야하는데, 순차적으로 처리를 해야한다면 이용할 수 있는 클래스입니다.

간단하게는 아래와 같이 `HandlerThread` 객체를 만들어서 `start()` 하고 만들어진 `HandlerThread`의 `Looper`를 이용하여 `Handler`로 사용할 수 있습니다.
`quit()`함수를 호출하기 전까지는 계속 루프를 돌고 `quit()`함수가 호출이 되면 `Looper`가 종료가 됩니다. 액티비티 LifeCycle()에 따라서 Destroy()때 호출하는 식으로
처리할 수 있습니다.

```java
// 별도의 스레드에 Looper와 MessageQueue가 자동으로 생성됨
val handlerThread = HandlerThread("Jungwoon")
handlerThread.start()

// 위에서 만든 HandlerThread의 Looper를 가져와서 Handler를 만듬
Handler(handlerThread.looper).post {
    // 새로 만들어진 스레드에서 해야할 작업 명시
}
```

아니면 직접 HandlerThread를 상속받아 처리할 수 있습니다.

```java
package com.byjw.handlerthread

import android.os.Handler
import android.os.HandlerThread
import android.os.Message
import android.util.Log

class MyHandlerThread : HandlerThread("MyHandlerThread") {
    // HandlerThread안에서 사용할 Handler 호출
    private lateinit var handler: Handler
    private val ACTION_1 = 1
    private val ACTION_2 = 2
    private val ACTION_3 = 3

    // Looper가 생성이 될때 호출되는 메소드
    override fun onLooperPrepared() {
        super.onLooperPrepared()

        handler = object : Handler(looper) {
            override fun handleMessage(msg: Message) {
                super.handleMessage(msg)

                when (msg.what) {
                    ACTION_1 -> doAction1()
                    ACTION_2 -> doAction2()
                    ACTION_3 -> doAction3()
                }

            }
        }
    }

    fun action1() {
        val message = getMessage(ACTION_1)
        handler.sendMessage(message)
    }

    fun action2() {
        val message = getMessage(ACTION_2)
        handler.sendMessage(message)
    }

    fun action3() {
        val message = getMessage(ACTION_3)
        handler.sendMessage(message)
    }
    
    private fun getMessage(what: Int): Message {
        val message = Message()
        message.what = what
        return message
    }

    private fun doAction1() {
        // todo something
    }

    private fun doAction2() {
        // todo something
    }

    private fun doAction3() {
        // todo something
    }
}
```