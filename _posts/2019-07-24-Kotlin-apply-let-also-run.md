---
layout: post
title: "코틀린(Kotlin)에서 apply, with, let, also, run 차이"
image: '/assets/img/'
description: '코틀린(Kotlin)에서 apply, with, let, also, run 차이'
tags:
- Kotlin
categories:
- Kotlin
---

개인적으로 코틀린을 사용하다가 apply, with, let, also, run이 언제 어떤 케이스에 사용하는지 햇갈려서 해당 부분에 대해서 정리를 한번 해보고자 합니다.

아래 예제에서의 data class는 아래와 같습니다.

```java
data class Customer (
    var name: String? = null,
    var age: Int? = null,
    var phone: String? = null,
    var address: String? = null
)
```

---

### apply

프로퍼티에 값을 할당할때 유용합니다.

apply 적용 O

```java
val jungwoon = Customer().apply {
    name="Jungwoon"
    age=31
    phone="01012341234"
    address="Seoul"
}
```

apply 적용 X

```java
val jungwoon = Customer()
jungwoon.name = "Jungwoon"
jungwoon.age = 31
jungwoon.phone = "01012341234"
jungwoon.address = "Seoul"
```

---

### with

수신 객체이며 결과가 필요하지 않은 경우 유용합니다.

with 적용 O

```java
val jungwoon = Customer(
    name = "Jungwoon",
    age = 31,
    phone = "01012341234",
    address = "Seoul"
)

with(jungwoon) {
    print(name)
    print(age)
    print(phone)
    print(address)
}
```

with 적용 X

```java
val jungwoon = Customer(
    name = "Jungwoon",
    age = 31,
    phone = "01012341234",
    address = "Seoul"
)

print(jungwoon.name)
print(jungwoon.age)
print(jungwoon.phone)
print(jungwoon.address)
```

---

### let

null 검사를 하고 null 이 아닐때만 코드를 실행할때 유용합니다.

let 적용 O

```java
val jungwoon = Customer(
    name = "Jungwoon",
    age = 31,
    phone = "01012341234",
    address = "Seoul"
)

jungwoon?.let {
    print(it.name)
    print(it.age)
    print(it.phone)
    print(it.address)
}
```

let 적용 X

```java
val jungwoon = Customer(
    name = "Jungwoon",
    age = 31,
    phone = "01012341234",
    address = "Seoul"
)


if (jungwoon != null) {
    print(jungwoon.name)
    print(jungwoon.age)
    print(jungwoon.phone)
    print(jungwoon.address)
}
```

---

### also

데이터를 할당하기 전에 유효성 검사등을 할 때 유용합니다.

also 적용 O

```java
val validData = jungwoon.also {
    requireNotNull(it.name)
    requireNotNull(it.age)
    requireNotNull(it.phone)
    requireNotNull(it.address)
}
```

also 적용 X

```java
var validData: Customer? = 
    if (jungwoon.name != null &&
        jungwoon.age != null && 
        jungwoon.phone != null && 
        jungwoon.address != null) {
        jungwoon
    } else {
        null
    }
```

---

### run

블록 내에서 여러 작업을 할때 사용합니다.

run 적용 O - #1

```java
fun printCustomerName(customer: Customer) = customer.run { 
    print(name)
}
```

run 적용 X - #1

```java
fun printCustomerName(customer: Customer) {
    print(customer.name)
}
```

run 적용 O - #2

```java
val isAllFill: Boolean = jungwoon.run {
    val isFillName = name != null 
    val isFillAge = age != null 
    val isFillPhone = phone != null 
    val isFillAddress = address != null
    
    isFillName and isFillAge and isFillPhone and isFillAddress
}
```

run 적용 X - #2

```java
val isAllFill: Boolean = jungwoon.name != null &&
        jungwoon.age != null &&
        jungwoon.phone != null &&
        jungwoon.address != null
```

