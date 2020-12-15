---
layout: post
title: "SingleLiveEvent와 Event 정리"
image: '/assets/img/'
description: 'SingleLiveEvent and Event Summary'
tags:
- Android
- LiveData
categories:
- Android
- LiveData
---

최근 프로젝트에서 LiveData 이용해 MVVM을 만드는 경험을 하였습니다. 프로젝트를 만들다 보니 ViewModel에서 특정 이벤트가 발생을 하였는데, 
딱히 값을 넘겨줄 필요가 없는 경우가 있었습니다. 이러한 경우 `null`을 넣어주거나 사용하지 않는 `불필요한 값`을 넣어주거나 하는 방법을 사용할 수 있는데
뭔가 섞연치 않아서 찾아보다 보니 이에 대한 해답으로 [구글]((https://medium.com/androiddevelopers/livedata-with-snackbar-navigation-and-other-events-the-singleliveevent-case-ac2622673150)) 에서 `SingleLiveEvent` 란 예시를 주었습니다.


예를 들어서 `ViewModel`에서 `startMyActivity()`란 메소드 호출이 발생하면 `View`에서 `감지`하고 있다가 `MyActivity::class.java`를 
실행해야한다고 가정했을때 `LiveData`를 이용해서 구현하려면 다음과 같이 구현해야 할것입니다.

```java
class MyViewModel : BaseViewModel() {

    val eventMyActivity: MutableLiveData<Boolean> = MutableLiveData() // 타입은 상관없지만 여기선 Boolean으로 하겠습니다.

    fun startMyActivity() {
        eventMyActivity.value = true
    }
    
}
``` 
[MyViewModel.kt]


이제 `View`에서는 다음 `LiveData`를 감지하고 있어야 할것입니다.

```java
import android.content.Intent
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val viewModel = MyViewModel()

        viewModel.eventMyActivity.observe(this, {
            Intent(this, MyActivity::class.java).apply { 
                startActivity(this)
            }
        })

    }
}
```
[MainActivity.kt]

여기서 큰 문제가 있는데 `eventMyActivity`란 `LiveData`에 사용하지도 않는 `불필요 값`을 넣어야 합니다.
심지어 여기서 아래 두 케이스 전부 동작 합니다.

```java

eventMyActivity.value = true
eventMyActivity.value = false

```

이에 구글에서는 `SingleLiveEvent`를 예시로 줬습니다.
우선 아래의 `SingleLiveEvent.kt`를 만듭니다.

```java
import androidx.annotation.MainThread
import androidx.annotation.Nullable
import androidx.lifecycle.LifecycleOwner
import androidx.lifecycle.MutableLiveData
import androidx.lifecycle.Observer
import java.util.concurrent.atomic.AtomicBoolean;


class SingleLiveEvent<T> : MutableLiveData<T>() {

    companion object {
        private const val TAG = "SingleLiveEvent"
    }

    val mPending: AtomicBoolean = AtomicBoolean(false)

    override fun observe(owner: LifecycleOwner, observer: Observer<in T>) {
        if (hasActiveObservers()) {
            Log.w(TAG,"Multiple observers registered but only one will be notified of changes.")
        }

        // Observe the internal MutableLiveData
        super.observe(owner, Observer { t ->
            if (mPending.compareAndSet(true, false)) {
                observer.onChanged(t)
            }
        })
    }

    @MainThread
    override fun setValue(@Nullable t: T?) {
        mPending.set(true)
        super.setValue(t)
    }

    /**
     * Used for cases where T is Void, to make calls cleaner.
     */
    @MainThread
    fun call() {
        value = null
    }

}
```
[SingleLiveEvent.kt]

그 다음에 `ViewModel`에서 다음과 같이 사용할 수 있습니다.

```java
import androidx.lifecycle.LiveData

class MyViewModel : BaseViewModel() {

    private val _eventMyActivity = SingleLiveEvent<Any>()
    val eventMyActivity: LiveData<Any>
        get() = _eventMyActivity
            
            
    fun startMyActivity() {
        _eventMyActivity.call()
    }

}
```
[MyViewModel.kt]

그 다음 `View`에서는 다음과 같이 사용할 수 있습니다.

```java
import android.content.Intent
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val viewModel = MyViewModel()

        viewModel.eventMyActivity.observe(this, {
            Intent(this, MyActivity::class.java).apply { 
                startActivity(this)
            }
        })

    }
}
```
[MainActivity.kt]

이렇게 하게 되면 불필요한 값을 넘겨주지 않고 `call()` 이란 메소드를 호출하는 것만으로도 View에서 감지하여 이벤트를 실행할 수 있습니다.

하지만 여기에 또다른 `문제(?)`가 있습니다. 모든 이벤트마다 매번 `SingleLiveData` 객체를 만들어줘야 하는 문제가 발생합니다.
여기서 위에 글에서는 `Event.kt`를 사용하라고 하였습니다.

```java
/**
 * Used as a wrapper for data that is exposed via a LiveData that represents an event.
 */
open class Event<out T>(private val content: T) {

    var hasBeenHandled = false
        private set // Allow external read but not write

    /**
     * Returns the content and prevents its use again.
     */
    fun getContentIfNotHandled(): T? {
        return if (hasBeenHandled) {
            null
        } else {
            hasBeenHandled = true
            content
        }
    }

    /**
     * Returns the content, even if it's already been handled.
     */
    fun peekContent(): T = content
}
```
[Event.kt]

저는 여기서 만든 `Event`를 `BaseViewModel`이란 모든 `ViewModel`이 상속받는 `SuperClass`에 다음과 같이 넣어 주었습니다.

```java
import androidx.lifecycle.LiveData
import androidx.lifecycle.MutableLiveData
import androidx.lifecycle.ViewModel

open class BaseViewModel : ViewModel() {

    private val _viewEvent = MutableLiveData<Event<Any>>()
    val viewEvent: LiveData<Event<Any>>
        get() = _viewEvent

    fun viewEvent(content: Any) {
        _viewEvent.value = Event(content)
    }

}
```
[BaseViewModel.kt]

이렇게 한 다음에 `ViewModel`에서 다음과 같이 사용할 수 있습니다.

```java
class MyViewModel : BaseViewModel() {

    companion object {
        const val EVENT_START_MY_ACTIVITY = 22212
    }

    fun startMyActivity() = viewEvent(EVENT_START_MY_ACTIVITY)

}
```
[MyViewModel.kt]

그 다음 `View`에서는 이렇게 사용합니다.

```
import android.content.Intent
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val viewModel = MyViewModel()

        viewModel.viewEvent.observe(this, {
            it.getContentIfNotHandled()?.let { event -> 
                when (event) {
                    MyViewModel.EVENT_START_MY_ACTIVITY -> {
                        Intent(this, MyActivity::class.java).apply {
                            startActivity(this)
                        }
                    }
                }
            }
        })

    }
}
```
[MainActivity.kt]

이렇게 하게 되면 매번 `SingleLiveEvent`를 만들 필요도 없고, `여러 이벤트`에 대해서 `한번에` 처리할 수도 있습니다. 여기서 `MyActivity2`란 액티비티를
호출하는 이벤트를 하나 더 `추가`해보겠습니다. 

```java
class MyViewModel : BaseViewModel() {

    companion object {
        const val EVENT_START_MY_ACTIVITY = 22212
        const val EVENT_START_MY_ACTIVITY_2 = 22213
    }

    fun startMyActivity() = viewEvent(EVENT_START_MY_ACTIVITY)
    
    fun startMyActivity2() = viewEvent(EVENT_START_MY_ACTIVITY_2)

}
```
[MyViewModel.kt]

그 다음에 `View`에서도 `MyActivity2` 만 호출하는 부분을 추가합니다.

```java
import android.content.Intent
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import com.global.exoplayer.MyViewModel.Companion.EVENT_START_MY_ACTIVITY_2

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val viewModel = MyViewModel()

        viewModel.viewEvent.observe(this, {
            it.getContentIfNotHandled()?.let { event ->
                when (event) {
                    MyViewModel.EVENT_START_MY_ACTIVITY -> {
                        Intent(this, MyActivity::class.java).apply {
                            startActivity(this)
                        }
                    }
                    EVENT_START_MY_ACTIVITY_2 -> {
                        Intent(this, MyActivity2::class.java).apply {
                            startActivity(this)
                        }
                    }
                }
            }
        })

    }
}
```
[MainActivity.kt]

이렇게 하게되면 굳이 데이터를 넘기지 않아도 되는 이벤트들을 좀 더 수월하게 처리할 수 있게 됩니다. 마지막 팁으로 자주 사용되는 기능들을
`BaseViewModel`과 같은 `SuperClass`에 넣어놓고 사용하면 코드 재활용을 수월하게 할 수 있습니다.