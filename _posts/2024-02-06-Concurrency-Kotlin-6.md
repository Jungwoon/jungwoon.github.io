---
layout: post
title: "코틀린 동시성 프로그래밍 #6 - 원자성 위반, 스레드 한정, 액터 그리고 뮤텍스"
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

## 원자성 위반

원자성 위반은동기화 없이 객체의 상태를 동시에 수정할 때 발생합니다. 동시성 어플리케이션을 실행하게 되면 공유 상태를 수정하는 코드 블로깅 다른 스레드의
변경 시도와 겹치면서 이런 문제가 발생합니다.

예를 들어서 아래와 같은 코드로 테스트 했을때 나타납니다.

```kotlin
var counter = 0

fun main() = runBlocking {

    val context = Dispatchers.Default

    fun asyncIncrement(by: Int) = async(context) {
        for (i in 0 until by) {
            counter++
        }
    }

    repeat(10) {
        val workerA = asyncIncrement(2000)
        val workerB = asyncIncrement(100)

        workerA.await()
        workerB.await()

        println("counter : $counter")

    }

}

```

결과

```
counter : 2100
counter : 4200
counter : 6219
counter : 8229
counter : 10244
counter : 12247
counter : 14250
counter : 16255
counter : 18263
counter : 20274
```

위에 결과에서 보듯이 항상은 아니지만 어느순간부터 숫자 증감분이 누락되는걸 볼 수 있습니다. 숫자가 보장이 되었다면 2100씩 증가가 되어야 하는데 세번째부터
누락이 된 것을 확인할 수 있습니다. 이는 여러 스레드가 하나의 변수에 대해서 읽기와 쓰기를 하는 경우 중복이 되어서 증가분 중 하나씩 유실이 되기 때문입니다.

코드 블록을 원자적으로 하려면 블록 안에서 발생하는 어떤 메모리 액세스도 동시에 실행되지 않도록 해야 합니다.

다음에서는 이를 해결하는 방법들에 대해서 알아보도록 하겠습니다.

---

## 스레드 한정 ()

이름 그대로 공유 상태에 접근하는 모든 코루틴을 단일 스레드에서 실행되도록 한정하는 것을 의미합니다. 상태가 더 이상 스레드 간에 공유되지 않으며 하나의 스레드에서만
상태를 수정함을 의미합니다.

코루틴에서는 `CoroutineContext`을 `SingleThreadContext`로 변경하는 것만으로도 손쉽게 변경할 수 있습니다.

```kotlin
var counter = 0

fun main() = runBlocking {

    val context = newSingleThreadContext("test") // <-- 여기만 변경

    fun asyncIncrement(by: Int) = async(context) {
        for (i in 0 until by) {
            counter++
        }
    }

    repeat(10) {
        val workerA = asyncIncrement(2000)
        val workerB = asyncIncrement(100)

        workerA.await()
        workerB.await()

        println("counter : $counter")
    }
}
```

결과

```
counter : 2100
counter : 4200
counter : 6300
counter : 8400
counter : 10500
counter : 12600
counter : 14700
counter : 16800
counter : 18900
counter : 21000
```

---

## 액터 (Actor)

위의 스레드 한정은 가장 심플한 방법이지만 앱의 여러 다른 부분에서 공유 상태를 수정해야 하거나 더 높은 유연성을 원하는 경우에는 제한이 될 수 있습니다.
이때 스레드 한정이라는 개념을 기반으로 동시성 기본 요소인 채널을 사용해 솔루션을 개선할 수 있습니다. 이 두가지를 혼합해 액터를 만들 수 있습니다.

### 액터의 역할

상태 액세스를 단일 스레드로 한정하고 다른 스레드가 채널로 상태 수정을 요청합니다. 이를 통해 안전하게 업데이트 할 수 있습니다.

아래에서는 비동기 작업은 `Dispaters.Default`를 사용해 여러 스레드에서 호출이 되지만 실제 내부에서는 `actor`를 통해 하나의 스레드에서만 채널을 전달하게끔하였습니다.

여기서 액터의 역할은 단순히 송신 채널입니다.

아래 코드에서는 실제 메시지를 사용하지 않기 때문에 메시지를 null 로 전달했습니다.

```kotlin
var counter = 0

fun main() = runBlocking {

    val context = newSingleThreadContext("contextActor")

    val actorCounter = actor<Void?>(context) {
        for (msg in channel) {
            counter++
        }
    }

    fun asyncIncrement(by: Int) = async(Dispatchers.Default) {
        for (i in 0 until by) {
            actorCounter.send(null)
        }
    }

    repeat(10) {
        val workerA = asyncIncrement(2000)
        val workerB = asyncIncrement(100)

        workerA.await()
        workerB.await()

        println("counter : $counter")
    }
}
```

결과

```
counter : 2100
counter : 4200
counter : 6300
counter : 8400
counter : 10500
counter : 12600
counter : 14700
counter : 16800
counter : 18900
counter : 21000
```

만약에 메시지를 직접 사용하려면 아래와 같이 코드를 변경할 수 있습니다.

```kotlin
var counter = 0

fun main() = runBlocking {

    val context = newSingleThreadContext("contextActor")
    
    val actorCounter = actor<Action>(context) {
        for (msg in channel) {
            when (msg) {
                Action.INCREASE -> counter++
                Action.DECREASE -> counter--
            }
        }
    }

    fun asyncIncrement(by: Int) = async(Dispatchers.Default) {
        for (i in 0 until by) {
            actorCounter.send(Action.INCREASE)
        }
    }

    repeat(10) {
        val workerA = asyncIncrement(2000)
        val workerB = asyncIncrement(100)

        workerA.await()
        workerB.await()

        println("counter : $counter")
    }
}

enum class Action {
    INCREASE,
    DECREASE,
}
```

액터는 채널이기 때문에 앞에서 포스팅했던거처럼 `capacity`를 직접 가질 수 있습니다. 그래서 기본적으로는 버퍼가 없지만 `capacity`에 값을 줌으로써 버퍼를 가지게
할 수 있습니다.

```kotlin
val actor = actor<String>(capacity = 10)
```

또 위의 예제처럼 직접 `CoroutineContext`를 전달 할 수 있습니다.

```kotlin
val dispatcher = newFixedThreadPoolContext(3, "pool")
val actor = actor<String>(dispatcher)
```

---

## 상호 배제 (Mutual Exclusions)

상호 배제는 한 번에 하나의 코루틴만 코드 블록을 실행할 수 있도록 하는 동기화 메커니즘으로 일반적으로 뮤텍스라고 합니다.

코틀린 뮤텍스의 가장 중요한 특징은 블록되지 않는 점입니다. 실행 대기 중인 코루틴은 잠금을 획득하고 코드 블록을 실행할 수 있을 때까지 일시 중단됩니다.
코루틴은 일시 중단되지만 일시 중단 함수를 사용하지 않고 뮤텍스를 잠글 수 있습니다.

아래 코드에서 처럼 `Mutex()`를 통해서 mutex 객체를 만들고 `withLock 블록`안에서 잠금 수 있습니다.

```kotlin
var counter = 0

fun main() = runBlocking {

    val mutex = Mutex()
    
    fun asyncIncrement(by: Int) = async(Dispatchers.Default) {
        for (i in 0 until by) {
            mutex.withLock {
                counter++
            }
        }
    }

    repeat(10) {
        val workerA = asyncIncrement(2000)
        val workerB = asyncIncrement(100)

        workerA.await()
        workerB.await()

        println("counter : $counter")
    }
}
```

결과

```
counter : 2100
counter : 4200
counter : 6300
counter : 8400
counter : 10500
counter : 12600
counter : 14700
counter : 16800
counter : 18900
counter : 21000
```

---

## 휘발성 변수 (Volatile Variables)

JVM에서 각 스레드는 비휘발성 변수의 캐시된 사본을 가질 수 있으며 이 캐시는 항상 변수의 실제 값과 동기화되지는 않습니다. 한 스레드에서 공유 상태를
변경하면 캐시가 업데이트될 때까지 다른 스레드에서는 볼 수 없습니다.

이를 해결하기 위해서는 @Volatile 어노테이션을 변수에 사용하면 다른 스레드에서 값이 변경되자마자 변경사항을 바로 업데이트할 수 있습니다.

예를 들어 아래 코드에서는 `스레드 A`에서 `shutdownRequested`의 값을 변경할 경우
바로 변경점이 업데이트되기 때문에 `스레드 B`에서 변경사항을 즉시 볼 수 있습니다. 

```kotlin
@Volatile
var shutdownRequested = false
```

휘발성 변수를 통해서 안전하게 원자성을 보장하려면 아래 조건이 둘 다 만족되어야 합니다.

- 변수 값의 변경은 현재 상테에 의존하지 않습니다.
- 휘발성 변수는 다른 변수에 의존하지 않으며, 다른 변수도 휘발성 변수에 의존하지 않습니다.

---

## 원자적 데이터 구조

원자적 데이터 구조는 기본적으로 원자적 연산을 제공하는 데이터 구조로 코틀린에서 제공하는 표준 라이브러리가 아니라 JVM에서 제공합니다.

```kotlin
var counter = AtomicInteger()

fun main() = runBlocking {

    fun asyncIncrement(by: Int) = async(Dispatchers.Default) {
        for (i in 0 until by) {
            counter.incrementAndGet()
        }
    }

    repeat(10) {
        val workerA = asyncIncrement(2000)
        val workerB = asyncIncrement(100)

        workerA.await()
        workerB.await()

        println("counter : $counter")
    }
}
```

결과

```
counter : 2100
counter : 4200
counter : 6300
counter : 8400
counter : 10500
counter : 12600
counter : 14700
counter : 16800
counter : 18900
counter : 21000
```