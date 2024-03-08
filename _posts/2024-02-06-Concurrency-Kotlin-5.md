---
layout: post
title: "코틀린 동시성 프로그래밍 - Channel"
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

## Channel

동시성 관련된 오류는 스레드간 메모리를 공유할 때 주로 발생을 합니다. 코루틴은 `Channel`을 제공하여 스레드가 서로 `상태를 공유하는 대신 메시지를 통해
통신`을 함으로써 동시성 코드 작성하는데 도움을 줍니다.

`Channel`은 `실행 중인 스레드에 상관없이` 서로 다른 코루틴 간에 메시지를 안전하게 주고 받을 수 있습니다.

`Channel`의 `send()`는 일시 중단 함수입니다. 이는 `Backpressure`라고 불리는 처리속도가 공급속도를 따라가지 못할때 발생하는 문제를 도와줍니다.
코루틴은 채널 안의 요소가 버퍼 크기에 도달하며 일시 중단이 되고 채널에서 요소가 제거되는 즉시 Producer는 다시 재개됩니다.

채널은 다음과 같이 손쉽게 만들 수 있습니다.

```kotlin
val channel = Channel<Int>()
```

`Channel` 내부는 다음과 같이 되어 있어서 `capacity` 설정을 통해서 버퍼를 둘 수도 있고 두지 않을 수도 있습니다. 일반적으로는 버퍼가 없는 `RENDEZVOUS(=0)`가
기본값입니다.

```kotlin
public fun <E> Channel(
    capacity: Int = RENDEZVOUS,
    onBufferOverflow: BufferOverflow = BufferOverflow.SUSPEND,
    onUndeliveredElement: ((E) -> Unit)? = null
): Channel<E>
```

버퍼가 없는 채널은 send()를 호출하면 리시버가 Receive()를 호출할 때까지 일시 중단됩니다.

```kotlin
fun main() = runBlocking {

    val unbufferedChannel = Channel<Int>()

    GlobalScope.launch {
        repeat(10) {
            unbufferedChannel.send(it)
            println("Send Data : $it")
            delay(10)
        }
        unbufferedChannel.close()
    }

    delay(500)

    unbufferedChannel.consumeEach {
        println("Receive Data : $it")
    }

}
```

결과

```
Receive Data : 0
Send Data : 0
Send Data : 1
Receive Data : 1
Send Data : 2
Receive Data : 2
Send Data : 3
Receive Data : 3
Send Data : 4
Receive Data : 4
Send Data : 5
Receive Data : 5
Send Data : 6
Receive Data : 6
Send Data : 7
Receive Data : 7
Send Data : 8
Receive Data : 8
Send Data : 9
Receive Data : 9
```

채널 내 요소의 수가 버퍼의 크기와 같을 때마다 송신자의 실행을 중지 하는데 중단 없이 무한의 요소를 전송할 수 있는 채널을 원하면 아래와 같이 만들 수 있습니다.

```kotlin
val channel = Channel<Int>(Channel.UNLIMITED) 
```

예제는 다음과 같습니다

```kotlin
fun main() = runBlocking {

    val bufferedChannel = Channel<Int>(Channel.UNLIMITED)

    GlobalScope.launch {
        repeat(10) {
            bufferedChannel.send(it)
            println("Send Data : $it")
            delay(10)
        }
        bufferedChannel.close()
    }

    delay(500)


    bufferedChannel.consumeEach {
        println("Receive Data : $it")
    }


}
```

결과

```
Send Data : 0
Send Data : 1
Send Data : 2
Send Data : 3
Send Data : 4
Send Data : 5
Send Data : 6
Send Data : 7
Send Data : 8
Send Data : 9
Receive Data : 0
Receive Data : 1
Receive Data : 2
Receive Data : 3
Receive Data : 4
Receive Data : 5
Receive Data : 6
Receive Data : 7
Receive Data : 8
Receive Data : 9
```

이번 예제에서는 버퍼가 있기 때문에 receive()가 호출되기전까지 일시 중단되지 않습니다.

임의의 버퍼를 주고 싶은 경우는 아래와 같이 직접 `capacity` 값을 넣어도 됩니다. 이 경우에는 버퍼가 가득 차면 멈췄다가 `recieve()`가 호출되면 다시 재개합니다.

```kotlin
val channel = Channel<Int>(50) 
```

다음은 ConflatedChannel에 대해서 알아보도록 하겠습니다. 이 채널은 내보낸 요소가 유실돼도 괜찮다는 전제가 있습니다. 하나의 요소의 버퍼만 가지고 새로운 
요소가 보내질 때마다 이전 요소는 유실되어 최신 요소를 가져오게 됩니다.

```kotlin
fun main() = runBlocking {

    val conflatedChannel = Channel<Int>(Channel.CONFLATED)

    GlobalScope.launch {
        repeat(10) {
            conflatedChannel.send(it)
            println("Send Data : $it")
            delay(10)
        }
        conflatedChannel.close()
    }

    delay(500)

    conflatedChannel.consumeEach {
        println("Receive Data : $it")
    }

}
```

결과

```
Send Data : 0
Send Data : 1
Send Data : 2
Send Data : 3
Send Data : 4
Send Data : 5
Send Data : 6
Send Data : 7
Send Data : 8
Send Data : 9
Receive Data : 9
```

다음은 `Channel`의 유효성 검사하는 방법에 대해서 알아보도록 하겠습니다.

- SendChannel
  - isCloseForSend : 송신에 대해 닫혀 있는지 여부를 확인합니다.
  - isFull : 채널이 가득 찼는지 여부를 확인합니다.
- ReceiveChannel
  - isCloseForReceive : 수신에 대해 닫혀 있는지 여부를 확인합니다.
  - isEmpty : 채널이 비어 있는지 여부를 확인합니다.