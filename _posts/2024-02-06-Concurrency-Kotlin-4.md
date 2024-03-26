---
layout: post
title: "코틀린 동시성 프로그래밍 #4 - Iterator, Sequence, Producer"
image: '/assets/img/'
description: 'Kotlin Concurrency'
tags:
- Kotlin
categories:
- Programming
- Kotlin
---

이번에 [코틀린 동시성 프로그래밍](https://search.shopping.naver.com/book/catalog/32502382356)이란 책을 읽고 공부하면서 알게 된 부분에 대해서 정리해 보고자 합니다.

이번에는 일시 중단되는 여러 유형의 일시 중단 함수에 대해서 알아보도록 하겠습니다.

---

## Iterator

- `Index`가 없기 때문에 `순서대로`만 액세스 할 수 있습니다.
- 다음 요소가 있는지는 `hasNext()`를 통해서 알 수 있습니다.
- 한 방향으로만 검색이 가능합니다. 이전 요소를 검색할 수 없습니다.
- `Reset`이 불가능해서 한 번만 반복할 수 있습니다.

기본적인 사용방법은 아래와 같이 `iterator<타입> { }`를 통해서 사용할 수 있고 이때 `<타입>`은 생략 가능합니다.

값을 호출할때는 `next()`를 통해서 가져올 수 있습니다.

```kotlin
val iterator = iterator<String> {
    yield("Hello World")
}

println(iterator.next())
```

결과는 다음과 같습니다.

```
Hello World
```

아래와 같이 여러 타입을 넣을 수도 있습니다.


```kotlin
val iterator = iterator<Any> {
    yield("Hello World")
    yield(1)
    yield(10L)
}

iterator.forEach {
    println(it)
}
```

결과는 다음과 같습니다.

```
Hello World
1
10
```

만약 가지고 있는 값 이상으로 next()를 호출하면 예외가 발생합니다.

```kotlin
val iterator = iterator<Any> {
    yield("Hello World")
}

println(iterator.next())
println(iterator.next())
```

결과

```
Hello World
Exception in thread "main" java.util.NoSuchElementException
	at kotlin.sequences.SequenceBuilderIterator.nextNotReady(SequenceBuilder.kt:152)
	at kotlin.sequences.SequenceBuilderIterator.next(SequenceBuilder.kt:135)
	at org.example.MainKt.main(Main.kt:11)
	at org.example.MainKt.main(Main.kt)
```

그렇기 때문에 다음 값이 있는지 확인하기 위해 `hasNext()`란 검사를 위한 함수가 제공됩니다.

```kotlin
val iterator = iterator<Any> {
    yield("Hello World")
}

println(iterator.next())

if (iterator.hasNext()) {
    println(iterator.next())
} else {
    println("값이 없습니다.")
}
```

결과

```
Hello World
값이 없습니다.
```

---

## Sequence

- `Index`를 통해서 값을 가져올 수 있습니다.
- 상태가 저장되지 않으므로 상호 작용한 후 자동으로 Reset 됩니다.

### 사용방법

사용방법은 `sequence<타입> { }`을 통해서 사용할 수 있으며 `<타입>`은 생략 가능합니다.

`elementAt(인덱스)`를 통해서 원하는 인덱스의 값을 가져올 수 있습니다.

```kotlin
val sequence = sequence<Int> {
    yield(1)
    yield(2)
}

println(sequence.elementAt(0))
```

결과는 다음과 같습니다.

```
1
```

또 `take()`를 통해서 한번에 여러개의 값을 포함한 그룹을 가져올 수도 있습니다.

```kotlin
val sequence = sequence {
    yield(1)
    yield(2)
    yield(3)
    yield(4)
    yield(5)
    yield(6)
    yield(7)
    yield(8)
    yield(9)
    yield(10)
}

val element = sequence.take(5)
println("element : ${element.joinToString()}")
```

결과

```
element : 1, 2, 3, 4, 5
```


`Sequence`는 호출한 `index`가 없을때를 대비해서 아래 두 개의 함수를 제공합니다.

### elementAtOrElse

`elementAtElse`는 요청하는 인덱스의 값이 없을때 호출할 수 있는 후행람다가 있습니다. 람다가 받는 값은 앞에서 파라미터로 받는 인덱스 값입니다.
아래 예제에서는 3을 요청했기 때문에 3을 받습니다.

```kotlin
val sequence = sequence {
    yield(1)
    yield(2)
}

val element = sequence.elementAtOrElse(3) { "$it 인덱스에는 값이 없습니다." }
println("element : $element")
```

결과

```
element : 3 인덱스에는 값이 없습니다.
```

### elementAtOrNull

요건 값이 없을때 null을 받는 방법입니다.

```kotlin
val sequence = sequence {
    yield(1)
    yield(2)
}

val element = sequence.elementAtOrNull(3)
println("element : $element")
```

결과

```
element : null
```

---

## Producer

`Sequence`와 `Iterator`는 `실행 중` 일시 중단할 수 없다는 제약이 있습니다. 이때 일시 중단을 위해서는 `Producer`를 사용해야 하며 `Producer`의 사용법은
    `Sequence`와 `Iterator`와 비슷합니다. `Producer`의 특징은 다음과 같습니다.

- Producer는 값이 생성된 후 일시 중단되며, 새로운 값이 요청되면 재개됩니다.
- 특정 `CoroutineContext`로 생성될 수 있습니다.
- 전달되는 일시 중단 람다의 본문은 언제든지 일시 중단될 수 있습니다.
- `Producer`의 값은 일시 중단 연산에서만 수신될 수 있습니다.
- `Channel`을 사용해 작동하므로 `Data`를 `Stream`처럼 다룰 수 있습니다.

기본적인 사용 방법은 다음과 같습니다. `send()`를 통해서 값을 주고 `receive()`를 통해서 값을 가져올 수 있습니다.

```kotlin
fun main() = runBlocking{

    val producer = GlobalScope.produce {
        send(1)
    }

    println(producer.receive())

}
```

결과는 아래와 같습니다.

```
1
```

아래와 같이 직접 `CoroutineContext`를 지정할 수 있습니다.

```kotlin
val context = newSingleThreadContext("byJW")

val producer = GlobalScope.produce(context) {
    send(1)
}
```

consumeEach()를 사용하면 producer값을 전부 가져올 수 있습니다.

```kotlin
fun main() = runBlocking {

    val producer = GlobalScope.produce {
        send(1)
        send(2)
        send(3)
        send(4)
        send(5)
    }

    producer.consumeEach {
        println(it)
    }

}
```

```
1
2
3
4
5
```

---

## 결론

- `Iterator`는 상태가 있고 한 방향으로만 읽을 수 있기 때문에 이전 요소를 검색할 수 없고 인덱스가 없습니다.
- `Sequence`는 상태가 없고 각 호출 후에는 자체적으로 Reset 됩니다. 인덱스를 가지고 한번에 여러 그룹을 얻을 수 있습니다.
- `Iterator`와 `Sequence`는 생성한 후에는 일시 중단할 수 있지만 실행 중에는 일시 중단할 수 없기 때문에 비동기 작업이 필요 없는 경우에 적합합니다.
- `Producer`는 생성 후, 실행 후 언제든지 중단할 수 있습니다.
- `Producer`는 `suspend 연산`이나 Coroutine에서만 호출할 수 있습니다.