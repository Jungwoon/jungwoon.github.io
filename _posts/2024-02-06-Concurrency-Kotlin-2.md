---
layout: post
title: "코틀린 동시성 프로그래밍 - Deferred"
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

## Deferred

- `결과를 갖는` 비동기 작업을 수행합니다.
- `Deferred`는 `Job`을 상속합니다.
- 다른 언어의 Future, Promises 와 동일합니다.
- 객체를 반환하며, 객체는 비동기 작업이 완료될 때까지 비어 있습니다.
- `Async()`를 사용해 `Deferred`를 생성합니다.

---

### Deferred 생성

`async{}`를 통해서 `Deferred`를 생성하고 `await()`을 통해서 결과를 받을 수 있습니다.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    val deferred = GlobalScope.async {
        10
    }

    val result = deferred.await()
    println("Result : $result")
}
```

`CompletableDeferred<타입>()`을 통해서도 생성할 수 있습니다.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    val deferred = CompletableDeferred<Int>()
}
```

---

### Deferred 예외

`Deferred`는 `Job`과 달리 처리되지 않은 예외를 자동으로 전파하지 않습니다. 왜냐하면 `Deferred`는 결과를 대기할것으로 예상하였기 때문입니다.
실행이 성공했는지 확인하는 것은 결국 사용자의 역할입니다.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    val deferred = GlobalScope.async {
        throw Exception("Exception Test")
    }

    delay(2000)
}
```

결과
```
정상 실행
```

만약에 결과를 받기 위해 `await()`을 사용한다면 예외가 전파됩니다.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking<Unit> {
    val deferred = GlobalScope.async {
        throw Exception("Exception Test")
    }

    delay(2000)

    deferred.await()
}

```

결과
```
Exception in thread "main" java.lang.Exception: Exception Test
	at org.example.MainKt$main$1$deferred$1.invokeSuspend(Main.kt:11)
	at kotlin.coroutines.jvm.internal.BaseContinuationImpl.resumeWith(ContinuationImpl.kt:33)
	at kotlinx.coroutines.DispatchedTask.run(DispatchedTask.kt:108)
	at kotlinx.coroutines.scheduling.CoroutineScheduler.runSafely(CoroutineScheduler.kt:589)
	at kotlinx.coroutines.scheduling.CoroutineScheduler$Worker.executeTask(CoroutineScheduler.kt:806)
	at kotlinx.coroutines.scheduling.CoroutineScheduler$Worker.runWorker(CoroutineScheduler.kt:710)
	at kotlinx.coroutines.scheduling.CoroutineScheduler$Worker.run(CoroutineScheduler.kt:697)
```

아래와 같이 `try-catch` 블록을 사용해 예외를 처리할 수 있습니다.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking<Unit> {
    val deferred = GlobalScope.async {
        throw Exception("Exception Test")
    }

    delay(2000)
    
    try {
        deferred.await()
    } catch (throwable: Throwable) {
        println("Exception : ${throwable.message}")
    }
    
}
```

결과

```
Exception : Exception Test
```

---

