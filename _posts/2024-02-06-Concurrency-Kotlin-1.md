---
layout: post
title: "코틀린 동시성 프로그래밍 - Job"
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

## Job

- 결과가 없는 비동기 함수로 결과를 갖지 않는 백그라운드 작업이 속합니다.
- `launch()`를 사용해 `Job`을 생성합니다.
- `fire and forget` 방식으로 한번 시작된 작업은 예외가 발생하지 않는한 대기하지 않습니다.

> `fire and forget scenario` : 이베트나 메시지 기반 시스템에서 널리 사용되는 패턴으로, 미사일을 발사하고 나면 그 후 미사일은 알아서 표적을 향해 날아가는데, 미사일에 대해 잊고 있어도 알아서 표적에 명중한다는 의미로 실행 후 결과에 대해서 신경 쓸 필요가 없는 경우와 같은 시나리오를 의미합니다.

---

### Job 생성

기본적으로 Job은 생성되는 즉시 시작됩니다. 만약 바로 시작하지 않게 하려면 `GlobalScope.launch(start = CoroutineStart.LAZY) { }`와 같이 
옵션을 주어야 합니다.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    val job = GlobalScope.launch {
        // Do background task here
    }
}
```

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    val job = Job()
}
```

---

### Job 에외

Job 내부에서 발생한 예외는 Job을 생성한 곳까지 전파됩니다. Job이 완료되기를 기다리지 않아도 예외가 발생합니다.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    println("before")
    val job = GlobalScope.launch {
        throw Exception("Exception Test")
    }

    delay(500)
    println("after")
}
```

결과
```
before
Exception in thread "DefaultDispatcher-worker-1" java.lang.Exception: Exception Test
at org.example.MainKt$main$1$job$1.invokeSuspend(Main.kt:8)
at kotlin.coroutines.jvm.internal.BaseContinuationImpl.resumeWith(ContinuationImpl.kt:33)
at kotlinx.coroutines.DispatchedTask.run(DispatchedTask.kt:108)
at kotlinx.coroutines.scheduling.CoroutineScheduler.runSafely(CoroutineScheduler.kt:589)
at kotlinx.coroutines.scheduling.CoroutineScheduler$Worker.executeTask(CoroutineScheduler.kt:806)
at kotlinx.coroutines.scheduling.CoroutineScheduler$Worker.runWorker(CoroutineScheduler.kt:710)
at kotlinx.coroutines.scheduling.CoroutineScheduler$Worker.run(CoroutineScheduler.kt:697)
Suppressed: kotlinx.coroutines.internal.DiagnosticCoroutineContextException: [StandaloneCoroutine{Cancelling}@4be6c1a5, Dispatchers.Default]
after
```

---

### Job 라이프 사이클

```
                                          wait children
    +-----+ start  +--------+ complete   +-------------+  finish  +-----------+
    | New | -----> | Active | ---------> | Completing  | -------> | Completed |
    +-----+        +--------+            +-------------+          +-----------+
                     |  cancel / fail       |
                     |     +----------------+
                     |     |
                     V     V
                 +------------+                           finish  +-----------+
                 | Cancelling | --------------------------------> | Cancelled |
                 +------------+                                   +-----------+

```

- New : 존재하지만 아직 실행되지 않는 Job
- Active : 실행 중인 Job. 일시 중단된 Job도 활성으로 간주
- Completing(임시 상태) : 실행 중인 Job에서 완료가 되기 직전까지의 상태
- Completed : Job이 더 이상 실행되지 않는 경우
- Canceling(임시 상태) : 실행 중인 Job에서 cancel()이 호출되면 취소가 완료될때까지의 상태
- Cancelled : 취소로 인해서 실행이 완료된 Job, 취소된 Job도 완료로 간주

| 상태        | isActive | isCompleted |isCancelled
|:----------|:---------|:------------|:---
| New       | false    | false       | false
| Active    | true     | false       | false
| Completing| true     | false       | false
| Completed| true     | true        | false
| Canceling| false    | false       | true
| Cancelled| false    | true        | true

---

### Active

Job은 일반적으로 `start()`나 `join()`을 호출해서 실행하는데 아래와 같은 차이가 있습니다.

- start() : 잡이 완료될때까지 기다리 않고 잡을 시작
- join() : 잡이 완료될때까지 실행을 일시 중단

`start()`로 호출한 경우

```kotlin
import kotlinx.coroutines.*
import java.time.Instant
import java.time.LocalDateTime
import java.time.ZoneId
import java.time.format.DateTimeFormatter

fun main() = runBlocking {
    val job = GlobalScope.launch(start = CoroutineStart.LAZY) {
        delay(3000)
    }

    println("before : ${getCurrentTime()}")
    job.start()
    println("after : ${getCurrentTime()}")

}

fun getCurrentTime(): String {
    // 현재 시간의 밀리초 값을 얻기
    val currentTimeMillis = System.currentTimeMillis()

    // 밀리초 값을 Instant 객체로 변환
    val instant = Instant.ofEpochMilli(currentTimeMillis)

    // Instant를 LocalDateTime으로 변환 (원하는 시간대로)
    val localDateTime = LocalDateTime.ofInstant(instant, ZoneId.systemDefault())

    // LocalDateTime을 특정 형식으로 포맷팅
    val formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss.SSS")
    return localDateTime.format(formatter)
}
```

결과

```
before : 2024-02-06 17:46:14.531
after : 2024-02-06 17:46:14.561
```

`join()`으로 호출한 경우

```kotlin
import kotlinx.coroutines.*
import java.time.Instant
import java.time.LocalDateTime
import java.time.ZoneId
import java.time.format.DateTimeFormatter

fun main() = runBlocking {
    val job = GlobalScope.launch(start = CoroutineStart.LAZY) {
        delay(3000)
    }

    println("before : ${getCurrentTime()}")
    job.join()
    println("after : ${getCurrentTime()}")

}

fun getCurrentTime(): String {
    // 현재 시간의 밀리초 값을 얻기
    val currentTimeMillis = System.currentTimeMillis()

    // 밀리초 값을 Instant 객체로 변환
    val instant = Instant.ofEpochMilli(currentTimeMillis)

    // Instant를 LocalDateTime으로 변환 (원하는 시간대로)
    val localDateTime = LocalDateTime.ofInstant(instant, ZoneId.systemDefault())

    // LocalDateTime을 특정 형식으로 포맷팅
    val formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss.SSS")
    return localDateTime.format(formatter)
}
```

결과

```
before : 2024-02-06 17:50:00.627
after : 2024-02-06 17:50:03.676
```

---

### Canceling

`cancel()`이나 `cancelAndJoin()` 함수를 호출하면 잡은 `Canceling` 상태로 들어갑니다.

- cancel() : 취소를 합니다, cause를 사용하여 왜 취소가 발생했는지 남길 수 있습니다.
- cancelAndJoin() : 취소가 완료될때까지 현재 코루틴을 일시 중단합니다, cause를 사용할 수 없습니다.

하지만 둘 다 거의 바로 취소가 되기때문에 아래 예제에서는 큰 차이가 보이지는 않았습니다.


`cancel()`를 호출했을한 경우

```kotlin
import kotlinx.coroutines.*
import java.time.Instant
import java.time.LocalDateTime
import java.time.ZoneId
import java.time.format.DateTimeFormatter

fun main() = runBlocking {
    println("before : ${getCurrentTime()}")
    val job = GlobalScope.launch {
        delay(10_000)
    }

    delay(2_000)
    job.cancel() // or job.cancel(cause = CancellationException("Test Exception"))
    println("after : ${getCurrentTime()}")
}

fun getCurrentTime(): String {
    // 현재 시간의 밀리초 값을 얻기
    val currentTimeMillis = System.currentTimeMillis()

    // 밀리초 값을 Instant 객체로 변환
    val instant = Instant.ofEpochMilli(currentTimeMillis)

    // Instant를 LocalDateTime으로 변환 (원하는 시간대로)
    val localDateTime = LocalDateTime.ofInstant(instant, ZoneId.systemDefault())

    // LocalDateTime을 특정 형식으로 포맷팅
    val formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss.SSS")
    return localDateTime.format(formatter)
}
```

결과

```
before : 2024-02-06 17:58:49.693
after : 2024-02-06 17:58:51.739
```

`cancelAndJoin()` 함수 호출

```kotlin
import kotlinx.coroutines.*
import java.time.Instant
import java.time.LocalDateTime
import java.time.ZoneId
import java.time.format.DateTimeFormatter

fun main() = runBlocking {
    println("before : ${getCurrentTime()}")
    val job = GlobalScope.launch {
        delay(10_000)
    }

    delay(2_000)
    job.cancelAndJoin()
    println("after : ${getCurrentTime()}")
}

fun getCurrentTime(): String {
    // 현재 시간의 밀리초 값을 얻기
    val currentTimeMillis = System.currentTimeMillis()

    // 밀리초 값을 Instant 객체로 변환
    val instant = Instant.ofEpochMilli(currentTimeMillis)

    // Instant를 LocalDateTime으로 변환 (원하는 시간대로)
    val localDateTime = LocalDateTime.ofInstant(instant, ZoneId.systemDefault())

    // LocalDateTime을 특정 형식으로 포맷팅
    val formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss.SSS")
    return localDateTime.format(formatter)
}
```

결과
```
before : 2024-02-06 18:13:00.711
after : 2024-02-06 18:13:02.759
```

---

### Cancelled

취소나 예외로 인해 시행이 종료된 상태입니다.

- `getCancellationException()`을 통해 취소 이유에 대해서 정보를 얻을 수 있습니다.

```kotlin
import kotlinx.coroutines.*
import java.time.Instant
import java.time.LocalDateTime
import java.time.ZoneId
import java.time.format.DateTimeFormatter

@OptIn(InternalCoroutinesApi::class)
fun main() = runBlocking {
    println("before : ${getCurrentTime()}")
    val job = GlobalScope.launch {
        delay(10_000)
    }

    delay(2_000)
    job.cancel(cause = CancellationException("Test Exception"))
    println("after : ${getCurrentTime()}")

    val cancellation = job.getCancellationException()
    println("message : ${cancellation.message}")

}

fun getCurrentTime(): String {
    // 현재 시간의 밀리초 값을 얻기
    val currentTimeMillis = System.currentTimeMillis()

    // 밀리초 값을 Instant 객체로 변환
    val instant = Instant.ofEpochMilli(currentTimeMillis)

    // Instant를 LocalDateTime으로 변환 (원하는 시간대로)
    val localDateTime = LocalDateTime.ofInstant(instant, ZoneId.systemDefault())

    // LocalDateTime을 특정 형식으로 포맷팅
    val formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss.SSS")
    return localDateTime.format(formatter)
}
```

결과
```
before : 2024-02-06 16:17:58.120
after : 2024-02-06 16:18:00.175
message : Test Exception
```

---

### Completed

실행이 중지된 잡은 완료된으로 간주됩니다. 이때, `실행이 정상적으로 종료됐거나 취소됐는지 또는 예외 때문에 종료됐는지 여부는 관계없이 적용`되기때문에
`취소된 항목도 완료된 항목으로 간주`됩니다.

---

### 상태는 한 방향으로만 이동

잡의 상태는 앞으로만 이동할 수 있고, 이전 상태로 되돌릴 수 없습니다. 최종 상태는 잡이 이동할 수 없는 상태 중 하나 입니다.

```kotlin
fun main() = runBlocking {
    val job = GlobalScope.launch {
        delay(1000)
        println("Test")
    }

    job.join()
    
    // 한번 완료가 된 후에는 상태가 변하지 않는다. 
    job.start()
    job.join()
}
```

결과

```
Test
```