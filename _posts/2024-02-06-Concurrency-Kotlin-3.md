---
layout: post
title: "코틀린 동시성 프로그래밍 - Dispatcher"
image: '/assets/img/'
description: 'Kotlin Concurrency'
tags:
- Kotlin
categories:
- Programming
- Kotlin
---

이번에 [코틀린 동시성 프로그래밍](https://search.shopping.naver.com/book/catalog/32502382356)이란 책을 읽고 공부하면서 알게 된 부분에 대해서 정리해 보고자 합니다.

---

## Dispatcher

- 코루틴이 실행될 스레드를 결정
- 시작될 곳과 중단 후 재개될 곳을 모두 포함

### Default

Default는 CPU 바운드 작업을 위해서 프레임워크에 의해 자동으로 생성되는 스레드 풀로 최대 크기는 `시스템의 코어 수 -1` 입니다.
이전에는 CommonPool로 사용되었으나 변경되었습니다.

```kotlin
GlobalScope.launch {
    // TODO 
}

GlobalScope.launch(Dispatchers.Default) {
    // TODO
}
```

---

### Unconfined(=자유로운, 구속받지 않는)

일시 중지된 후, 일시 중단 연산에서 사용된 기존 스레드에서 다시 시작됩니다.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    GlobalScope.launch(Dispatchers.Unconfined) {
        println("Starting in ${Thread.currentThread().name}")
        delay(500)
        println("Resuming in ${Thread.currentThread().name}")
    }.join()
}
```

결과는 다음과 같이 처음에는 main에서 시작했지만 일시 중단 연산이 실행된 DefaultExecutor 스레드로 이동했습니다.

```
Starting in main
Resuming in kotlinx.coroutines.DefaultExecutor
```

---

### SingleThreadContext

지정한 특정 스레드 안에서 실행을 합니다.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    val dispatcher = newSingleThreadContext("threadByJW")
    GlobalScope.launch(dispatcher) {
        println("Starting in ${Thread.currentThread().name}")
        delay(500)
        println("Resuming in ${Thread.currentThread().name}")
    }.join()
}
```

결과는 다음과 같이 같은 스레드를 호출합니다.

```
Starting in threadByJW
Resuming in threadByJW
```

---

### ThreadPool

스레드 풀을 갖고 해당 풀에서 가용한 스레드에서 코루틴을 시작하고 재개합니다. 알아서 분산을 하기 때문에 별도로 처리할 필요는 없습니다.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    val dispatcher = newFixedThreadPoolContext(5, "threadPool")
    GlobalScope.launch(dispatcher) {
        println("Starting in ${Thread.currentThread().name}")
        delay(500)
        println("Resuming in ${Thread.currentThread().name}")
    }.join()
}
```

결과는 다음과 같이 숫자가 붙으며 해당 Pool 내에 스레드로 배정합니다.

```
Starting in threadPool-1
Resuming in threadPool-2
```

---

### ExceptionHandler

특정 예외에 대한 동작을 정의할 수 있습니다.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    val handler = CoroutineExceptionHandler({ context, throwable ->
        println("Error captured in $context")
        println("Message : ${throwable.message}")
    })

    GlobalScope.launch(handler) {
        TODO("Yet")
    }

    delay(500)
}
```

결과는 코루틴 내부에 아직 implement 를 안했기 때문에 예외가 발생하고 예외 발생시 위에 handler에서 정의한 대로 로그가 찍힙니다.

```
Error captured in [org.example.MainKt$main$1$invokeSuspend$$inlined$CoroutineExceptionHandler$1@283be0cd, StandaloneCoroutine{Cancelling}@486e94e8, Dispatchers.Default]
Message : An operation is not implemented: Yet
```

---

### Context 조합

여러 종류의 컨텍스트를 `+` 연산자를 통해서 쉽게 조합할 수 있습니다.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    val dispatcher = newSingleThreadContext("threadByJW")
    val handler = CoroutineExceptionHandler({ context, throwable ->
        println("Error captured in $context")
        println("Message : ${throwable.message}")
    })

    GlobalScope.launch(dispatcher + handler) {
        println("Running in ${Thread.currentThread().name}")
        TODO("Yet")
    }

    delay(500)
}
```

결과는 다음과 같습니다.

```
Running in threadByJW
Error captured in [org.example.MainKt$main$1$invokeSuspend$$inlined$CoroutineExceptionHandler$1@62857888, StandaloneCoroutine{Cancelling}@4bd5ddc5, java.util.concurrent.ScheduledThreadPoolExecutor@6ae37906[Running, pool size = 1, active threads = 1, queued tasks = 0, completed tasks = 0]]
Message : An operation is not implemented: Yet
```

---

### Context 분리

위의 조합과 반대로 조합된 Context에서 `-` 연산자를 통해 손쉽게 분리할 수 있습니다.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    val dispatcher = newSingleThreadContext("threadByJW")
    val handler = CoroutineExceptionHandler({ context, throwable ->
        println("Error captured in $context")
        println("Message : ${throwable.message}")
    })

    val newContext = dispatcher + handler

    val tempContext = newContext.minusKey(handler.key)

    GlobalScope.launch(tempContext) {
        println("Running in ${Thread.currentThread().name}")
        TODO("Yet")
    }

    delay(500)
}
```

결과는 다음과 같이 예외 발생시 위에서 정의한 `handler`를 통하지 않게 됩니다.

```
Running in threadByJW
Exception in thread "threadByJW" kotlin.NotImplementedError: An operation is not implemented: Yet
	at org.example.MainKt$main$1$1.invokeSuspend(Main.kt:18)
	at kotlin.coroutines.jvm.internal.BaseContinuationImpl.resumeWith(ContinuationImpl.kt:33)
	at kotlinx.coroutines.DispatchedTask.run(DispatchedTask.kt:108)
	at java.base/java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:515)
	at java.base/java.util.concurrent.FutureTask.run(FutureTask.java:264)
	at java.base/java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(ScheduledThreadPoolExecutor.java:304)
	at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1128)
	at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:628)
	at java.base/java.lang.Thread.run(Thread.java:834)
	Suppressed: kotlinx.coroutines.internal.DiagnosticCoroutineContextException: [StandaloneCoroutine{Cancelling}@5fd90a52, java.util.concurrent.ScheduledThreadPoolExecutor@719a955[Running, pool size = 1, active threads = 1, queued tasks = 0, completed tasks = 0]]
```

---

### Context Switch

`withContext()`란 함수를 사용하면 코드 블럭내에서 특정 콘텍스트로 변경할 수 있습니다. 다른 스레드에서 작업을 실행해야 할 필요가 있다면 다음과 같이 
사용할 수 있습니다.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    val dispatcher = newSingleThreadContext("threadByJW")
    val dispatcher2 = newSingleThreadContext("threadByJW2")

    GlobalScope.async(dispatcher) {
        println("Running in ${Thread.currentThread().name}")
        withContext(dispatcher2) {
            println("Running2 in ${Thread.currentThread().name}")
        }
    }

    delay(500)
}
```

결과는 다음과 같습니다.

```
Running in threadByJW
Running2 in threadByJW2
```