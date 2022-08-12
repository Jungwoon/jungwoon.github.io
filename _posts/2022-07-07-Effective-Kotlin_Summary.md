---
layout: post
title: "이펙티브 코틀린 내용 정리"
image: '/assets/img/'
description: 'Effective Kotlin Summary'
tags:
- Kotlin
categories:
- Kotlin
---

이번에 [이펙티브 코틀린](https://search.shopping.naver.com/book/catalog/32487702885?cat_id=50010920&frm=PBOKPRO&query=%25EC%259D%25B4%25ED%258E%2599%25ED%258B%25B0%25EB%25B8%258C%2B%25EC%25BD%2594%25ED%258B%2580%25EB%25A6%25B0&NaPm=ct%3Dl6dp3oxk%7Cci%3D440c96f390a44ad69554b66d5bd531fed16dd1b9%7Ctr%3Dboknx%7Csn%3D95694%7Chk%3D631337f2c3a1e77d236c5ece70c872fd5089daea)이라는 책을 읽으면서 봤던 안에 있던 내용들을 조금 정리해보고자 합니다.
이렇게 정리를 해놓으면 틈틈히 다시 확인하면서 안티패턴이나 좋지 않은 습관들을 고칠 수 있을거 같아서요

--

## 1. 가변성을 제한하라

- `var`보다는 `val`을 사용하는 것이 좋습니다.
- `mutable` 프로퍼티보다는 `immutable` 프로퍼티를 사용하는 것이 좋습니다.
- 변경이 필요하다면 `immutable` 데이터 클래스로 만들고 `copy`를 활용하는 것이 좋습니다.

```kotlin
data class User(
    val name: String,
    val surname: String
)

val user = User("Jungwoon", "Park")
val chanedUser = user.copy(surname = "Lee")
print(chanedUser) // User(name=Jungwoon, surname=Lee)
```
- 컬렉션에 상태를 저장해야 한다면, `mutable 컬렉션`보다는 `immutable 컬렉션`을 사용하는 것이 좋습니다.
- 변경 지점을 적절하게 설계하고, 불필요한 변경 지점은 만들지 않는 것이 좋습니다. (예상하지 못하게 데이터가 변경될 수 있습니다)
- mutable 객체를 외부에 노출하지 않는 것이 좋습니다. (외부 노출때에는 immutable 객체를 노출)

```kotlin
private val _someData = mutableLiveData<Any>()
val someData: LiveData<Any>
    get() = _someData
```

## 2. 변수의 스코프를 최소화하라

- 프로퍼티보다는 지역 변수를 사용하는 것이 좋습니다.
- 최대한 좁은 스코프를 갖게 변수를 사용합니다. 예를 들어 내부에서만 변수가 사용된다면, 변수를 반복문 내부에 작성하는 것이 좋습니다.

```kotlin
// Bad case
var user: User
for (i in users.indices) {
    user = users[i]
    print("User at $i is $user")
}

// Better Case
for (i in users.indices) {
    val user = users[i]
    print("User at $i is $user")
}

// Best Case
for ((i, user) in users.withIndex()) {
    print("User at $i is $user")
}

```

## 플랫폼 타입을 사용하지 말라

- 플랫폼 타입 : 코틀린은 자바 등의 다른 프로그래밍 언어에서 넘어온 타입 (=플랫폼 타입은 String!처럼 타입 이름 뒤에 `!기호`를 붙여서 표기합니다)
- 자바를 코틀린과 함꼐 사용할 때, 자바 코드를 직접 조작할 수 있다면, 가능한 `@Nullable`과 `@NotNull` 어노테이션을 붙여서 사용합니다.

## 4. inferrd 타입으로 리턴하지 말라

- `inferred 타입` : `타입 추론`을 통해서 추론된 타입
- 타입을 확실하게 지정해야 하는 경우에는 명시적으로 타입을 지정해야 한다는 원칙만 가지면 됩니다.

## 5. 예외를 활용해 코드에 제한을 걸어라

- 코틀린에서 코드의 동작을 제한할때 방법들
    - require 블록 : `아규먼트`를 제한할 수 있습니다.
    - check 블록 : `상태`와 관련된 동작을 제한할 수 있습니다.
    - assert 블록 : 어떤것이 `true인`지 확인할 수 있고 `테스트 모드`에서만 동작합니다.
    - return 과 throw와 함께 활용하는 Elvis 연산자


- require 블록 : 제한을 걸고 제한을 만족하지 못하면 `IllegalArgumentException`을 throw 합니다.

```kotlin
fun factorial(n: Int): Long {
    require(n >= 0) // n이 0보다 크게끔 제한할 수 있습니다.
    return if (n <= 1) 1 else factorial (n - 1) * n
}
```

- check 블록 : `check`는 `require`와 비슷하지만, 지정된 예측을 만족하지 못할 때, `IllegalStateException`을 `throw` 합니다.

```kotlin
fun getUserInfo(token: Token): UserInfo {
    checkNotNull(token)
    // ...
}

```

- assert 블록 : 코드 자체를 점검하며, 테스트 모드에서만 돌아가기 때문에 더 효율적으로 테스트를 할 수 있습니다.

```kotlin
@Test
fun `Stack pops correct number of elements`() {
    val stack = Stack(20) { it }
    val ret = stack.pop(10)
    assertEquals(10, ret.size)
}
```

- return 과 throw와 함께 활용하는 Elvis 연산자

```kotlin
fun getEmail(member: Member): String {
    return member?.mail ?: "unknown"
}
```

## 6. 사용자 정의 오류보다는 표준 오류를 사용하라

- 표준 라이브러리에 오류들도 잘 정의되어있기 때문에, 만들어진 오류를 사용하는게 좋습니다.
- 많이 사용하는 오류들
    - `IllegalArgumentException`과 `IllegalStateException` : `require`와 `check`를 사용해 throw 할 수 있는 예외입니다.
    - `IndexOutOfBoundsException` : 인덱스 파라미터의 `값이 범위를 벗어났을때` 나타냅니다. 보통 ArrayList.get(Int)를 사용할 때 throw 됩니다.
    - `ConcurrentModificationException` : `동시 수정`을 금지했는데, 발생했을때 throw 됩니다.
    - `UnSupportedOperationException` : 사용자가 사용하고자 하는 `메서드가 현재 객체에서는 사용할 수 없다는 것`을 나타냅니다.
    - `NoSuchElementException` : 사용자가 사용하려고 했던 `요소가 존재하지 않음`을 나타닙니다.

## 7. 결과 부족이 발생할 경우 null과 Failure를 사용하라

- 특정 함수가 원하는 결과를 만들어 낼 수 없을 때 처리 방법
    - null 또는 '실패를 나타내는 sealed 클래스(일반적으로 Failure라는 이름을 붙입니다)'를 리턴한다
    - 예외를 throw 한다.

> 예외는 정보를 전달하는 방법으로 사용해서는 안 됩니다, 예외는 잘못된 특별한 상황을 나타내야 하며, 처리되어야 합니다.

```kotlin
sealed class Result<out T>
class Success<out T>(val result: T): Result<T>()
class Failure<out T>(val throwable: Throwable): Result<Nothing>()

fun getSign() {
    if (iscorrectSign) {
        return null
    }
    // ...
}

fun getJsonest() {
    if (incorrectJSon) {
        return Failure(JsonParsingException())
    }
    // ...
}

```

## 8. 적절하게 null을 처리하라

- null은 최대한 명확한 의미를 갖는 것이 좋습니다. (아래 예시 참고)
    - String.toIntOrNull()은 String을 Int로 적절하게 변환할 수 없을 경우 null을 리턴합니다.
    - Interable<T>.firstOrNull(() -> Boolean)은 주어진 조건에 맞는 요소가 없는 경우 null을 리턴합니다.

-  null 안전하게 처리하기

```kotlin
printer?.print() // 안전 호출

if (printer != null) printer.print() // 스마트 캐스팅

val printerName1 = printer?.name ?: "Unnamed"
val printerName2 = printer?.name ?: return
val printerName3 = printer?.name ?: throw Error("Error Messages")
```

- `!!은 타입`은 nullable이지만, `null이 나오지 않는다는 것이 거의 확실한 상황`에서 많이 사용됩니다. 그렇기 때문에 좋은 해결 방법은 아닙니다.


## 9. use를 사용하여 리소스를 닫아라

- 사용이 끝났을때 `명시적으로 close()`를 통해서 다당야 하는 리소스들이 있습니다. (아래 예시), use를 사용하면 명시적으로 close()를 안해도 됩니다.
    - InputStream과 OutputStream
    - java.sql.Connection
    - java.io.Reader(FileReader, BufferReader, CSSParser)
    - java.new.Socket과 java.util.Scanner


```kotlin

// use를 사용하지 않은 방법
fun countCharactersInFile(path: String): Int {
    val reader = BufferedReader(FileReader(path))
    try {
        return reader.lineSequence().sumBy { it.length }
    } {
        finally {
            reader.close()
        }
    }
}

// use를 사용하지한 방법
fun countCharactersInFile(path: String): Int {
    val reader = BufferedReader(FileReader(path))
    reader.user {
        return reader.lineSequence().sumBy { it.length }
    }
}

```

## 10. 단위 테스트를 만들어라

- 아래와 같은 코드들에 대해서는 `단위 테스트`를 만드는 것이 좋습니다.
    - 복잡한 부분
    - 계속해서 수정이 일어나고 리팩토링이 일어날 수 있는 부분
    - 비즈니스 로직 부분
    - 공용 API 부분
    - 문제가 자주 발생하는 부분
    - 수정해야 하는 프로덕션 버그

## 11. 가독성을 목표로 설계하라

- 개발자는 코드를 작성하는 데는 1분 걸리지만, 이를 읽는 데는 10분이 걸린다
- 즉, 개발자는 어떤 코드를 작성하는 것보다 읽는 데 많은 시간을 소모한다
- 가독성이란 코드를 읽고 얼마나 빠르게 이해할 수 있는지를 의미합니다
- 인식 부하 감소
    - 아래 코드의 경우 `구현 A`는 일반적인 관용구를 사용하고 있어서 초보자도 쉽게 이해합니다.
    - `구현 B`는 코틀린에서 사용되는 관용구들이 사용되는데, 코드가 짧아지긴 하지만 숙련된 개발자만을 위한 코드는 좋은 코드가 아닙니다.

```kotlin
// 구현 A
if (person != null && person.isAdult) {
    view.showPerson(person)
} else {
    view.showError()
}

// 구현 B
person?.takeIf { it.isAdult }
    ?.let(view::showPerson)
    ?: view.showError()

```

## 12. 연산자 오버로드를 할 때는 의미에 맞게 사용하라

- 연산자 오버로딩은 의미에 맞게 사용하고, 의미가 명확하지 않으면 사용하지 않는게 좋습니다.
- 코틀린에서 지원되는 연산자 대응 함수

| 연산자    | 대응되는 함수             |
|--------|---------------------|
| +a     | a.unaryPlus()       |
| -a     | a.unaryMinus()      |
| !a     | a.not()             |
| ++a    | a.inc()             |
| --a    | a.dec()             |
| a+b    | a.plus(b)           |
| a-b    | a.minus(b)          |
| a*b    | a.times(b)          |
| a/b    | a.plus(b)           |
| a..b   | a.rangeTo(b)        |
| a in b | b.contains(a)       |
| a+=b   | a.plusAssign(b)     |
| a-=b   | a.minusAssign(b)    |
| a*=b   | a.timesAssign(b)    |
| a/=b   | a.divAssign(b)      |
| a==b   | a.equals(b)         |
| a>b    | a.compareTo(b) > 0  |
| a<b    | a.compareTo(b) < 0  |
| a>=b   | a.compareTo(b) >= 0 |
| a<=b   | a.compareTo(b) <= 0 |

## 13. Unit?을 리턴하지 말라

- Unit을 리턴하는 함수는 별도의 return을 하지 않아도 됩니다.
- Unit?을 리턴하는 함수는 Unit이나 null을 리턴해주어야 합니다.
- 이를 이용해서 Boolean을 대신해서 사용할 수 있겠지만 굳이 사용해야 싶습니다.

```kotlin
fun test(): Unit {
    print("test")
    return Unit // 생략 가능
}

fun test2(): Unit? {
    print("test2")

    // Unit이나 null이나 반드시 return을 해주어야 합니다.
    return Unit
    return null
}

// 만약 사용한다면 다음과 같이 사용할 수 있습니다.

fun test3() {
    // test2()의 리턴값이 null 이면 early return
    test2() ?: return
}

```

## 14. 변수 타입이 명확하지 않은 경우 확실하게 지정하라

- 코틀린은 타입추론이 가능하지만 가독성 향상 외에 안전을 위해서도 타입을 지정하는 것이 좋습니다.


## 15. 리시버를 명시적으로 참조하라

- 동일한 이름의 리시버를 받을 때에는 리시버를 명시적으로 참조해야합니다.

```kotlin 
class Node(val name: String) {

    fun makeChild(childName: String) {
        create("$name.$childName").apply {
            print("Created ${this?.name} in ${this@Node.name}") // 여기서 this와 this@Node로 명시
        }
    }

    fun create(name: String): Node? = Node(name)

}

fun main() {
    val node = Node("parent")
    node.makeChild("child") // Created parent.child in parent
}

```

## 16. 프로퍼티는 동작이 아니라 상태를 나타내야 한다

- 코틀린의 프로퍼티는 사용자 저의 게터와 세터를 가질 수 있습니다.

```kotlin
var name: String? = null
    get() = field?.toUpperCase()
    set(value) {
        if (!value.isNullOrBlank()) {
            field = value
        }
    }

// val 일때는 field가 만들어지지 않습니다.
val fullName: String
    get() = "$name $surname"
```

- 프로퍼티에 계산식을 사용하지 않습니다.

```kotlin 
// 이렇게 하지 마세요! 이러한 처리는 프로퍼티가 아니라 함수로 구현해야 합니다.
val Tree<Int>.sum: Int
    get() = when (this) {
        is Leaf -> value
        is Node -> left.sum + right.sum
    }
```

- 프로퍼티는 상태 집합을 나타내고, 함수는 행동을 나타냅니다.
- 프로퍼티에서는 특별한 이유가 없다면 함수를 사용하면 안됩니다

## 17. 이름 있는 아규먼트를 사용하라

- 아규먼트에 이름을 명시하여 실수를 방지하고 가독성을 높이자

```kotlin
fun sendMail(
    to = "byjungwoon@gmail.com",
    message = "Hello World"
)
```

## 18. 코딩 컨벤션을 지켜라

- 기본적인 코딩 컨벤션을 지키자 (조직에 따라 다를 수 있음)
- [공식 코딩컨벤션](https://kotlinlang.org/docs/coding-conventions.html)

## 19. knowledge를 반복하여 사용하지 말라

- 프로그래밍에서 `knowledge`는 `'의도적인 정보'`를 의미합니다.
- 크게 아래 두 가지가 있습니다.
    - 로직 : 프로그램이 어떠한 식으로 동작하는지와 프로그램이 어떻게 보이는지
    - 공통 알고리즘 : 원하는 동작을 하기 위한 알고리즘
- 결론 : 코드를 복사해서 사용하지 말고 추출해서 같이 사용하자!

## 20. 일반적인 알고리즘을 반복해서 구현하지 말라

- [표준 라이브러리(stdlib)](https://kotlinlang.org/api/latest/jvm/stdlib/)에 있는 기능을 굳이 구현하지 말고 가져다 사용하라

## 21. 일반적인 프로퍼티 패턴은 프로퍼티 위임으로 만들어라

`프로퍼티 위임`은 `다른 객체의 메서드`를 활용해서 `프로퍼티의 접근자(getter, setter)`를 만드는 방식입니다.

일반적으로 `by` 를 통해서 위임을 합니다.

```kotlin
// 코틀린에서 많이 사용하는 프로퍼티 위임
val value by lazy { getValue() }
var items: List<Item> by
  Delegates.observable(listOf()) { _, _, _ ->
    notifyDataSetChanged()
}

// 안드로이드에서 뷰와 리소스 바인딩
private val button: Button by bindView(R.id.button)
private val textSize: Button by bindDimention(R.dimen.font_size)

// Koin에서 종속성 주입
private val viewModel: MainViewModel by inject()

// 데이터 바인딩
private val port by bindConfiguration("port")

```

코틀린 stdlib에 있는 유용한 델리게이터

- lazy
- Delegates.observable
- Delegates.vetoable // observe 하다가 특정조건에서 변경을 취소할 수 있음
- Delegates.notNull

실제 구현시 아래와 같이 `getValue()`와 `setValue()`를 직접 구현합니다.

```kotlin
var token String? by LoggingProperty(null)

private class LoggingProperty<T>(var value: T) {
  
  operator fun getValue(
    thisRef: Any?,
    prop: KProperty<*>
  ): T {
    print("${prop.name} returned value $value")
    return value
  }
  
  operator fun setValue(
    thisRef: Any?,
    prop: KProperty<*>,
    newValue: T
  ) {
    val name = prop.name
    print("$name changed from $value to $newValue")
    value = newValue
  }
  
}
```

실제 by가 어떻게 컴파일 되는지 아래와 같이 확인할 수도 있습니다.

값 처리뿐 아니라 컨텍스트(this)와 프로퍼티 레퍼런스의 경계도 함께 사용합니다.

```kotlin
private val 'token$delegate' =
  LoggingProperty<String?>(null)

var token: String?
  get() = 'token$delegate'.getValue(this, ::token)
  set(value) {
    'token$delegate'.setValue(this, ::token, value)
  }
```

## 22. 일반적인 알고리즘을 구현할 때 제네릭을 사용하라

타입 파라미터는 `구체적인 자료형의 서브타입`을 제한할 수 있습니다. 이렇게 하면 특정 자료형이 제공하는 메서드를 `안전하게` 사용할 수 있습니다.

아래는 `Iterable`의 서브타입으로 제한을 걸었습니다.

```kotlin
inline fun <T, R : Any> Iterable<T>.mapNotNull(
  transform: (T) -> R?
): List<R> {
  return mapNotNullTo(ArrayList<R>(), transform)
}
```

## 23. 타입 파라미터의 섀도잉을 피하라

아래와 같이 `동일한 파라미터의 이름(=name)`을 쓰지않는게 좋습니다.

```kotlin
class Forest(val name: String) {
  fun addTree(name: String) {
    // ...
  }
}
```

## 24. 제네릭 타입과 variance 한정자를 활용하라

`variance 한정자(ex - in, out)`은 제네릭 타입에 관계를 주기 위해서 사용합니다.

- 불공변성(invariant)
- 공변성(covariant) : A'가 A의 서브타입이라면 `C<A'>`는 `C<out A>`의 서브타입입니다.  
- 반공변성(contravariant) : A'가 A의 서브타입이라면 `C<A>`는 `C<in A'>`의 서브타입입니다.


## 25. 공통 모듈을 추출해서 여러 플랫폼에서 재사용하라

코틀린을 사용하면 `다양한 장치`와 `플랫폼`을 대상으로 개발할 수 있어서 `모듈`로 빼서 `재사용성`을 늘리는게 좋습니다.

## 26. 함수 내부의 추상화 레벨을 통일하라

추상화 레벨을 통일함으로써 `세부사항`을 숨길수 있습니다.


- 추상화 레벨이 통일 되지 않은 케이스

```kotlin
class CoffeeMachine {
  
  fun makeCoffee() {
    // 수백 개의 변수를 선언
    // 복잡한 로직을 처리
    //  낮은 수준의 최적화도 여기에서 처리 
  }
  
}
```

- 추상화 레벨이 통일된 케이스

```kotlin
class CoffeeMachine {
  
  // 높은 레벨
  fun makeCoffee() {
    boilWater()
    brewCoffee()
    pourCoffee()
    pourMilk()
  }
  
  // 낮은 레벨
  private fun boilWater() { // ... }
  private fun brewCoffee() { // ... }
  private fun pourCoffee() { // ... }
  private fun pourMilk() { // ... }
  
}
```

## 27. 변화로부터 코드를 보호하려면 추상화를 사용하라

함수와 클래스 등의 추상화로 `실질적인 코드를 숨기면`, 사용자가 `세부 사항을 알지 못해도 괜찮다`는 장점이 있습니다.

- 추상화를 하는 방법
  - `상수`로 추출한다.
    - 이름을 붙일 수 있음
    - 나중에 해당 값을 쉽게 변경할 수 있음
  - `동작`을 `함수`로 래핑한다.
    - 이름을 붙일 수 있음
  - `함수`를 `클래스`로 래핑한다.
    - 상태를 가질 수 있음
    - 많은 함수를 가질 수 있음
  - `인터페이스` 뒤에 클래스를 숨긴다
    - `추상화의 꽃`
    - 인터페이스 뒤에 `객체를 숨김`으로써 `실질적인 구현`을 추상화
    - 사용자가 `추상화된 것`에만 `의존`하게 만들 수 있음
  - `보편적인 객체`를 `특수한 객체`로 래핑한다.

## 28. API 안정성을 확인하라

안정적인 API를 사용하는 것이 좋습니다.

## 29. 외부 API를 랩(wrap)해서 사용하라

외부 API를 래핑함으로써 얻게되는 장 단점

- 장점
  - 문제가 있으면 래퍼만 변경하면 되므로, API 변경에 쉽게 대응할 수 있음
  - 프로젝트 스타일에 맞춰서 API의 형태를 조정할 수 있습니다.
  - 특정 라이브러리에서 문제가 발생하면, 래퍼를 수정해서 다른 라이브러리를 사용하도록 코드를 쉽게 변경할 수 있습니다.
  - 필요한 경우 쉽게 동작을 추가하거나 수정할 수 있습니다.
- 단점
  - 래퍼를 따로 정의해야 합니다.
  - 다른 개발자가 프로젝트를 다룰 때, 어떤 래퍼들이 있는지 따로 확인해야 합니다.
  - 래퍼들은 프로젝트 내부에서만 유효하므로, 스택오버플로에 질문을 못합니다.

## 30. 요소의 가시성을 최소화하라

접근자의 가시성을 제한해서 캡슐화 하는게 좋습니다.

- 클래스 멤버
  - public : 어디에서나 볼 수 있습니다.
  - private : 클래스 내부에서만 볼 수 있습니다.
  - protected : 클래스와 서브클래스 내부에서만 볼 수 있습니다.
  - internal : 모듈 내부에서만 볼 수 있습니다.
- 톱 레벨
  - public : 어디에서나 볼 수 있습니다.
  - private : 같은 파일 내부에서만 볼 수 있습니다.
  - internal : 모듈 내부에서만 볼 수 있습니다.

## 31. 문서로 규약을 정의하라

일반적인 문제는 행위가 문서화되지 않고, 요소의 이름이 명확하지 않다면 이를 사용하는 사용자는 우리가 만들려고 했던 추상화 목표가 아닌, 현재 구현에만
의존하게 된다는 점입니다. 이러한 문제는 예상되는 행위를 문서로 설명함으로써 해결합니다.

규약

어떤 행위를 설명하면 사용자를 이를 일종의 약속으로 취급하며 이를 규약이라고 합니다.

- 이름 : 일반적인 개념과 관련된 메서드는 이름만으로 동작을 예측할 수 있습니다.
- 주석 : 모든 규약을 적을 수 있는 강력한 방법 
- 타입 : 타입은 객체에 댛나 많은 것을 알려줄 수 있습니다.

> KDoc 형식을 이용하면 주석으로 함수를 문서화 할때 도움이 됩니다.

## 32. 추상화 규약을 지켜라

이건 말 그대로...

## 33. 생성자 대신 팩토리 함수를 사용하라

```kotlin
val list = ArrayList<String>()      // 생성자
val list2 = mutableListOf<String>() // 팩토리 함수
```

팩토리 함수는 생성자의 역할을 대신 해 주는 함수로 다음과 같은 장점이 있습니다.

- 생성자와 다르게, 함수에 `이름`을 붙일 수 있습니다.
- 생성자와 다르게, 함수가 `원하는 형태의 타입`을 `리턴`할 수 있습니다.
- 생성자와 다르게, 호출할 때마다 `새로운 객체`를 만들 필요가 없습니다.
- 팩토리 함수는 `아직 존재하지 않는 객체를 리턴`할 수도 있습니다.
- 객체 `외부에` 팩토리 함수를 만들면, 그 `가시성`을 원하는 대로 제어할 수 있습니다.
- 팩토리 함수는 `인라인`으로 만들 수 있으며, 그 파라미터들을 `reified`로 만들 수 있습니다.
- 팩토리 함수는 생성자로 만들기 `복잡한 객체`도 만들어 낼 수 있습니다.
- 생성자는 `즉시` `슈퍼클래스` 또는 `기본 생성자`를 호출해야 합니다. 하지만 팩토리 함수를 사용하면, `원하는 때`에 생성자를 호출할 수 있습니다.

> 대신 `서브클래스 생성`에는 슈퍼클래스의 생성자가 필요하기 때문에 서브클래스를 만들 수 없습니다.

팩토리 함수의 종류

#### companion 객체 팩토리 함수

```kotlin
class MyLinkedList<T>(
  val head: T,
  val tail: MyLinkedList<T>?
) {

  companion object {
    fun <T> of(vararg elements: T): MyLinkedList<T>? {
      // ...
    }
  }
}

// 사용
val list = MyLinkedList.of(1, 2)
```

이러 경우는 이름을 가진 생성자라고 부르며 생성자와 같은 역할을 하면서 다른 이름을 가지고 있습니다.

- from : 파라미터를 하나 받고, 같은 타입의 인스턴스 하나를 리턴
```kotlin
val date: Date = Date.from(instant)
```
- of : 파라미터를 여러 개 받고, 이를 통합해서 인스턴스를 만들어 줌
```kotlin
val faceCards: Set<Rank> = EnumSet.of(JACK, QUEEN, KING)
```
- valueOf : 의미를 조금 더 쉽게 읽을 수 있게 이름 붙인 함수(from과 of와 비슷)
```kotlin
val prime: BigInteger = BigInteger.valueOf(Integer.MAX_VALUE)
```
- instance or getInstance : 싱글턴으로 인스턴스 하나를 리턴
```kotlin
val luke: StackWalker = StackWalker.getInstance(options)
```
- createInstance or newInstance : 인스턴스 하나를 생성하지만 싱글턴이 적용되지 않음
```kotlin
val enwArray = Array.newInstance(classObject, arrayLength)
```
- get[Type] : 싱글톤 인스턴스를 반환하지만, 팩토리 함수가 다른 클래스에 있을 경우 팩토리 함수 타입을 지정 
```kotlin
val fileStore: FileStore = Files.getFileStore(path)
```
- new[Type] : 인스턴스 하나를 반환하지만, 팩토리 함수가 다른 클래스에 있을 경우 팩토리 함수 타입을 지정
```kotlin
val bufferedReader = BufferedReader = Files.newBufferedReader(path)
```

#### 확장 팩토리 함수

`companion 객체`를 `직접 수정할 수 없고` 다른 파일에 함수를 만들어야 하는 경우

```kotlin
// 교체할 수 없는 인터페이스
interface Tool {
    companion object {
        // ...
    }
}

// 확장 함수로 정의
fun Tool.Companion.createBigTool() : BigTool { }

// 호출
Tool.createBigTool()
```

#### 톱레벨 팩토리 함수

아래는 톱레벨 팩토리 함수의 예제로 대표적인 예로는 `listOf`, `setOf`, `mapOf` 등이 있습니다.

```kotlin
class MainActivity: Activity {
  companion object {
    fun getIntent(context: Context = Intent(context, MainActivity::class.java) 
  }
}
```

#### 가짜 생성자

`List`과 `MutableList`는 `인터페이스` 입니다. 따라서 생성자를 가질 수 없지만 
아래와 같이 생성자처럼 사용하는 방법이 있는데 이런 방식을 가짜 생성자라 합니다.

보통 아래와 같은 이유로 만듭니다.
- `인터페이스`를 위한 `생성자`를 만들고 싶을때
- `refied 타입` 아규먼트를 갖게 하고 싶을때

```kotlin
List(4) { "User$it" } // [User0, User1, User2 ...]

public inline fun <T> List(
    size: Int,
    init: (index: Int) -> T
): List<T> = MutableList(size, init)

public inline fun <T> MutableList(
    size: Int,
    init: (index: Int) -> T
): MutableList<T> {
    val list = ArrayList<T>(size)
    repeat(size) { index -> list.add(init(index) }
    return list
}
```

> `reified 타입`은 제네릭으로 `런타임`에 `inline 함수`내의 `파라미터 타입` 정보를 알고 싶을때 사용합니다.

```kotlin
inline fun <reified T> printGenerics(value: T) {
    when (T::class) {
        String::class -> {
            println("String : $value")
        }
        Int::class -> {
            println("Int : $value")
        }
    }
}

printGenerics1("print generics function")
printGenerics1(1000)
```

#### 팩토리 클래스의 메서드

팩토리 클래스는 클래스의 `상태`를 가질 수 있다는 특징 때문에 팩토리 함수보다 다양한 기능을 갖습니다.

```kotlin
data class Student(
  val id: Int,
  val name: String,
  val surname: String
)

class StudentsFactory {
  var nextId = 0  // 여기가 상태
  fun next(name: String, surname: String = Student(nextId++, surname)
}

val factory = StudentsFactory()
val s1 = factory.next("Hello", "World") // Student(id=0, name="Hello", surname="World")
val s2 = factory.next("Hi", "Kotlin") // Student(id=1, name="Hi", surname="Kotlin")
```

## 34. 기본 생성자에 이름 있는 옵션 아규먼트를 사용하라

간단히 말하면 코틀린에서는 아규먼트에 이름과 디폴트 값을 지정할 수 있기 때문에, 
이를 이용하면 `점층적 생성자 패턴`과 `빌더 패턴`을 사용하지 않아도 됩니다.

#### 점층적 생성자 패턴

생성자에 여러 아규먼트가 있을때 기본값을 주어 생략이 가능한 방법

```kotlin
class Pizza {
    
    private val size: String
    private val cheese: Int
    private val olives: Int
    private val bacon: Int

    constructor(size: String, cheese: Int, olives: Int, bacon: Int) {
        this.size = size
        this.cheese = cheese
        this.olives = olives
        this.bacon = bacon
    }
    
    constructor(size: String, cheese: Int, olives: Int): this(size, cheese, olives, 0)
    
    constructor(size: String, cheese: Int): this(size, cheese, 0, 0)
    
    constructor(size: String): this(size, 0, 0, 0)
    
}

// 각각의 아규먼트가 뭔지 알기가 힘듦
val a = Pizza("L", 3, 4, 5)
val b = Pizza("L", 3, 4)
val c = Pizza("L", 3, )
val d = Pizza("L")
```

하지만 코틀린에서는 아래와 같이 더 간단히 대체 될 수 있습니다.

```kotlin
class Pizza(
    val size: String,
    val cheese: Int = 0,
    val olives: Int = 0,
    val bacon: Int = 0,
)

// 아규먼트에 이름까지 지정 가능해서 더 한눈에 다가옵니다.
val a = Pizza(
  size = "L",
  cheese = 3,
  olives = 4,
  bacon = 5,
)

val b = Pizza(
  size = "L",
  cheese = 3,
  olives = 4,
)

val c = Pizza(
  size = "L",
  cheese = 3,
)

val d = Pizza(
  size = "L",
)

```

#### 빌더 패턴

생성자에 여러 아규먼트가 있을대 기본값을 주어 생략이 가능하고 직접 이름을 명시

```kotlin
class Pizza(
    val size: String,
    val cheese: Int = 0,
    val olives: Int = 0,
    val bacon: Int = 0
) {
    class Builder(private val size: String) {
        private var cheese: Int = 0
        private var olives: Int = 0
        private var bacon: Int = 0
        
        fun setCheese(value: Int): Builder = apply {
            cheese = value
        }

        fun setOlives(value: Int): Builder = apply {
            olives = value
        }

        fun setBacon(value: Int): Builder = apply {
            bacon = value
        }
        
        fun build() = Pizza(size, cheese, olives, bacon)
    }
}

// 점층적 생성자 패턴보다 이름이 명시가 되어서 훨씬 보기 좋아졌습니다.
val pizza = Pizza.Builder("L")
  .setCheese(3)
  .setOlives(4)
  .setBacon(5)
  .build()

```

그래도 코틀린만으로 만드는게 훨씬 짧고 가독성도 좋습니다.

```kotlin
class Pizza(
    val size: String,
    val cheese: Int = 0,
    val olives: Int = 0,
    val bacon: Int = 0,
)

val pizza = Pizza(
  size = "L",
  cheese = 3,
  olives = 4,
  bacon = 5,
)
```


## 35. 복잡한 객체를 생성하기 위한 DSL을 정의하라

코틀린은 사용자 정의 DSL(Domain Specific Language)을 만들 수 있습니다. 아래와 같은 조건이 있는 경우에는 만들어서 사용하면 좋습니다.

- 복잡한 자료 구조
- 계측적인 구조
- 거대한 양의 데이터

## 36. 상속보다는 컴포지션을 사용하라

상속은 관계가 명확하지 않을 때 사용하면, 여러 가지 문제가 발생할 수 있습니다. ('is-a' 관계에서 사용)

```kotlin
class ProfilerLoader {
    fun load() {
        // 프로그래스 바를 보여 줌
        // 프로파일을 읽어 들임
        // 프로그레스 바를 숨김
    }
}

class ImageLoader {
    fun load() {
        // 프로그래스 바를 보여 줌
        // 이미지를 읽어 들임
        // 프로그레스 바를 숨김
    }
}
```

상속을 이용하면 위의 코드를 아래와 같이 시용할 수 있습니다.

```kotlin
abstract class LoaderWithProgress {
    fun load() {
        // 프로그래스 바를 보여 줌
        innerLoad()
        // 프로그래스 바를 숨김
    }

    abstract fun innerLoad()
}


class ProfilerLoader: LoaderWithProgress() {
    override fun innerLoad() {
        // 프로파일을 읽어 들임
    }
}

class ImageLoader: LoaderWithProgress() {
    override fun innerLoad() {
        // 이미지를 읽어 들임
    }
}
```

하지만 몇 가지 단점이 있습니다. 그렇기 때문에 명확한 'is-a' 관계일때 사용하는것이 좋습니다.

- 상속은 하나의 클래스만을 대상으로 할 수 있습니다. 상속을 사용해서 행위를 추출하다 보면, 많은 함수를 갖는 거대한 BaseXXX 클래스가 만들어지고 복잡해집니다.
- 상속은 클래스의 모든 것을 가져오게 됩니다. 따라서 불필요한 함수를 갖는 클래스가 만들어질 수 있습니다. (인터페이스 분리 원칙(ISP) 위반)
- 상속은 어렵습니다. 슈퍼클래스를 여러 번 확인해야 합니다.

이런한 이유로 `컴포지션`을 사용하면 대안이 가능합니다. 컴포지션을 사용한다는 것은 `객체를 프로퍼티로 갖고`, `함수를 호출`하는 형태로 재사용하기 때문에
`추가 코드`만을 통해서 해결이 가능합니다.

```kotlin
class Progress {
    
    fun showProgress() {
        // ...
    }
    
    fun hideProgress() {
        // ...
    }
}


class ProfilerLoader {
    private val progress = Progress()
    
    fun load() {
        progress.showProgress()
        // 프로파일을 읽어 들임        
        progress.hideProgress()
    }
}

class ImageLoader {
    private val progress = Progress()

    fun load() {
        progress.showProgress()
        // 이미지를 읽어 들임        
        progress.hideProgress()
    }
}
```

컴포지션을 사용하면 다음과 같은 장점을 같습니다.

- 컴포지션은 다른 클래스의 내부 구현에 의존하지 않기 때문에 더 `안전`합니다.
- 컴포지션은 여러 클래스를 대상으로 할 수 있기 때문에 더 `유연`합니다.
- 컴포지션은 `this 리시버`를 사용할 수 없기 때문에 리시버를 명시적으로 활용해야 해야 더 `명시적`입니다.

## 37. 데이터 집합 표현에 data 한정자를 사용하라

코틀린에는 `data class`가 있기 때문에 `데이터 집합`을 생성할때에는 `data class`를 이용하는게 좋습니다.

```kotlin
data class Player(
    val id: Int,
    val name: String,
    val points: Int,
)
```

`data class`는 자동으로 아래 함수들이 생성이 되는 강점이 있습니다.

- toString()
- equals()와 hashCode()
- copy()
- componentN()

## 38. 연산 또는 액션을 전달할 때는 인터페이스 대신 함수 타입을 사용하라

대부분의 프로그래밍 언어에는 함수 타입이라는 개념이 없기 때문에 `메서드가 하나만 있는 인터페이스(=SAM)`을 이용합니다.

안드로이드 개발자들은 클릭 리스너때문에 아래와 같은 코드를 많이 봤을 겁니다.

```kotlin

//  하나의 메서드만 가지는 인터페이스 SAM(Single Abstract Method)
interface OnClick {
    fun clicked(view: View)
}

fun setOnClickListener(listener: OnClick) {
    // ...
}

setOnClickListener(object : OnClick {
    override fun clicked(view: View) {
        // ...
    }
})
```

이를 `함수 타입으로 변경`하면 다음과 같이 사용할 수 있습니다.

```kotlin
fun setOnClickListener(listener: (View) -> Unit) {
  // ...
}
```

그리고 함수 타입으로 변경 시 다음과 같은 기능들을 사용할 수 있습니다.

- 람다 표현식 또는 익명 함수로 전달

```kotlin
setOnClickListener { //... }
setOnClickListener(fun(view) { // ... } )
```

- 함수 레퍼런스 또는 제한된 함수 레퍼런스로 전달

```kotlin
setOnClickListener(::println)
setOnClickListener(this::showUsers)
```

- 선언된 함수 타입을 구현한 객체로 전달

```kotlin
class ClickListener: (View) -> Unit {
    override fun invoke(view: View) {
        // ...
    }
}

setOnClickListener(ClickListener())
```


> SAM을 사용할 때에는 코틀린이 다른 언어에서 사용할 클래스를 설계할 때 입니다.

## 39. 태그 클래스보다는 클래스 계층을 사용하라

상수 모드를 태그라고 부르며 태그를 가진 클래스를 태그 클래스라고 합니다. 
하지만 태그 클래스는 서로 다른 책임을 한 클래스에 태그로 구분해서 넣음으로써 다양한 문제를 내포하게 됩니다.

- 한 클래스에 여러 모드를 처리하기 위한 `보일러 플레이트 코드`가 추가 됩니다.
- 여러 목적으로 사용해야 하므로 프로퍼티가 일관적이지 않게 사용될 수 있습니다.
- 여러 목적을 가지고 요소를 여러 방법으로 설정할 수 있는 경우, 상태의 `일관성`과 `정확성`을 지키기 어렵습니다.
- `팩토리 메서드`를 사용하지 않으면 객체가 제대로 생성되었는지 확인하기 어렵습니다.

아래는 태그 클래스의 예제입니다.

```kotlin
class ValueMatcher<T> private constructor(
    private val value: T? = null,
    private val matcher: Matcher
) {
    
    companion object {
        fun <T> equal(value: T)
            = ValueMatcher<T>(value = value, matcher = Matcher.EQUAL)

        fun <T> notEqual(value: T)
                = ValueMatcher<T>(value = value, matcher = Matcher.NOT_EQUAL)

        fun <T> emptyList(value: T)
                = ValueMatcher<T>(value = value, matcher = Matcher.LIST_EMPTY)

        fun <T> notEmptyList(value: T)
                = ValueMatcher<T>(value = value, matcher = Matcher.LIST_NOT_EMPTY)
    }
    
    enum class Matcher {
        EQUAL,
        NOT_EQUAL,
        LIST_EMPTY,
        LIST_NOT_EMPTY
    }
    
    fun match(value: T?) = when(matcher) {
        Matcher.EQUAL -> value == this.value
        Matcher.NOT_EQUAL -> value != this.value
        Matcher.LIST_EMPTY -> value is List<*> && value.isEmpty()
        Matcher.LIST_NOT_EMPTY -> value is List<*> && value.isNotEmpty()
    }
    
}
```

`태그 클래스`는 `sealed 클래스`로 변경이 가능하고 이를 통해` 한 클래스에 여러 모드를 만드는 방법` 대신에, 
`각각의 모드를 여러 클래스`로 만들고 `타입 시스템`과 `다형성`을 활용하는 것입니다.

이를 `sealed class`로 변경하면 다음과 같습니다.

```kotlin
sealed class ValueMatcher<T> {
   abstract fun match(value: T): Boolean
   
   class Equal<T>(private val value: T) : ValueMatcher<T>() {
       override fun match(value: T): Boolean {
           return value == this.value
       }
   }

    class NotEqual<T>(private val value: T) : ValueMatcher<T>() {
        override fun match(value: T): Boolean {
            return value != this.value
        }
    }

    class EmptyList<T>(private val value: T) : ValueMatcher<T>() {
        override fun match(value: T): Boolean {
            return value is List<*> && value.isEmpty()
        }
    }

    class NotEmptyList<T>(private val value: T) : ValueMatcher<T>() {
        override fun match(value: T): Boolean {
            return value is List<*> && value.isNotEmpty()
        }
    }
}
```

sealed 한정자를 사용하면 아래와 같은 장점이 있습니다.
- 외부 파일에서 서브클래스를 만드는 행위 자체를 모두 제한
- 외부에서 추가적인 서브클래스를 만들 수 없으므로, 타입이 추가되지 않을 거라는게 보장이라 `when()`절에서 `else 브랜치`를 만들 필요가 없습니다.


## 40. equals의 규약을 지켜라

직접 구현시에 규약을 지켜야 합니다. (생략)

## 41. hashCode의 규약을 지켜라

직접 구현시에 규약을 지켜야 합니다. (생략)

## 42. compareTo의 규약을 지켜라

직접 구현시에 규약을 지켜야 합니다. (생략)

## 43. API의 필수적이지 않는 부분을 확장 함수로 추출하라

클래스의 메서드를 정의할 때에는 메서드를 멤버로 정의할 건지 확장으로 정의할건지 결정해야 하는데, 아래와 같은 특징이 있기 때문에
상황에 맞춰서 잘 선택하여야 합니다.

- 확장 함수는 읽어 들여야 합니다. (클래스의 public API로 import)
  - 확장 함수는 일반적으로 다른 패키지에 위치
  - 데이터와 행위를 분리하도록 설계된 프로젝트에 사용할 수 있습니다.
- 확장함수는 `virtual`이 아닙니다.
  - 파생 클래스에서 오버라이드 할 수 없습니다. 그래서 상속을 목적으로 하는 설계에서는 확장 함수로 만들면 안됩니다.
- 멤버는 높은 우선 순위를 갖습니다.
- 확장 함수는 클래스 위가 아니라 타입 위에 만들어집니다.
- 확장 함수는 `클래스 레퍼런스`에 나오지 않습니다.
  - 확장은 클래스 레퍼런스에서 `멤버`로 표시되지 않기 때문에 `어노테이션 프로세서가 따로 처리하지 않습니다.`
  - 필수적이지 않은 요소를 확장 함수로 추출하면, `어노테이션 프로세스로`부터 숨겨집니다.

## 44. 멤버 확장 함수의 사용을 피하라

확장 함수는 다음과 같이 클래스 멤버로 정의할 수 있습니다.

```kotlin
interface PhoneBook {
    fun String.isPhoneNumber(): Boolean
}

class Fizz: PhoneBook {
    override fun String.isPhoneNumber(): Boolean {
        return length == 7 && all { it.isDigit() }
    }
}
```

위와 같은 코드는 DSL을 만들 때를 제외하면 피하는게 좋습니다. 이유는 아래와 같습니다

- 가시성을 제한하지 못합니다.
- 레퍼런스를 지원하지 않습니다.
- 암묵적 접근을 할 때, 두 리시버 중 어떤 리시버를 선택할지 혼동됩니다.
- 확장 함수가 외부에 있느 다른 클래스를 리시버로 받을 때, 해당 함수가 어떤 동작을 하는지 명확하지 않습니다.
- 경험이 적은 개발자의 경우 확장 함수를 보면, 직관적이지 않을 수 있습니다.

## 45. 불필요한 객체 생성을 피하라

당연히 불필요한 객체 생성은 피해야 하는데, 언어적 특성을 이용해서 다양한 레벨에서 객체 생성ㅇ르 피할 수 있습니다.

- JVM에서는 동일한 문자열을 처리하는 코드가 여러개 있다면, 기존의 문자열을 재사용합니다.

```kotlin
val str1 = "Hello World"
val str2 = "Hello World"
print(str1 == str2)  // true
print(str1 === str2) // true, str2를 캐시로부터 읽어들이기 때문에
```

- Integer와 Long처럼 박스화한 기본 자료형도 작은 경우에는 재사용합니다. (`Int기준 -128 ~ 127`)

```kotlin
val i1 = 1
val i2 = 1
print(i1 == i2)  // true
print(i1 === i2)  // true, i2를 캐시로부터 읽어 들이기 때문에

val j1 = 1234
val j2 = 1234
print(j1 == j2)  // true
print(j1 === j2)  // false, 범위를 벗어나서 캐시를 하지 않습니다.

```

- 객체를 wrap 하면 크게 발생하는 비용
  - 객체는 더 많은 용량을 차지합니다. (최소 16바이트)
  - 요소가 캡슐화되어 있다면 접근에 추가적인 함수 호출이 필요합니다.
  - 객체는 생성되어야 하고, 메모리에 할당하고, 레퍼런스를 만드는 등의 작업을 필요로 합니다.

- 객체에 비용 줄이기
  - `싱글톤` 사용하기 : 매번 생성하는게 아니라 만들어놓은 객체를 사용합니다.
  - `캐시`를 활용하는 팩토리 함수 : 팩토리 함수내에 캐시를 활용
  - 무거운 객체 `외부 스코프`로 보내기
  - `지연 초기화` : 실제 사용할때까지 객체 생성 미루기
  - `Primitive` 자료형 사용하기

> 레퍼런스 종류
> WeakReference : 사용안할때 GC가 정리
> SoftReference : 메모리가 부족할 때 GC가 정리

## 46. 함수 타입 파라미터를 갖는 함수에 inline 한정자를 붙여라

inline 한정자의 역할은 컴파일 시점에 `함수를 호출하는 부분`을 `함수의 본문`으로 대체하는 것입니다.

```kotlin
inline fun repeat(times: Int, action: (Int) -> Unit) {
    for (index in 0 until times) {
        action(index)
    }
}

// 함수 호출하는 부분
repeat(10) {
    print(it)
}

// 컴파일 당시
for (index in 0 until 10) {
    print(index)
}
```

inline을 사용하면 아래와 같은 장점이 있습니다.

1. 타입 아규먼트에 refied 한정자를 붙여서 사용할 수 있습니다 : 제네릭에 대해서 컴파일 타임에 자료형을 줄 수 있습니다.
2. 함수 타입 파라미터를 가진 함수가 훨씬 빠르게 동작합니다 : 함수 호출과 리턴을 위해 점프하는 과정의 백스택을 추적하는 과정이 없기 때문에 빠릅니다. 
3. 비지역리턴을 사용할 수 있습니다 : 함수가 직접 main 함수에 박히기 때문에 return이 사용 가능합니다.

### crossinline vs noinline

- `crossinline` : 아규먼트를 인라인 함수로 받지만, 비지역적 리턴을 하는 함수는 받을 수 없습니다. 인라인으로 만들지 않은 다른 람다 표현식과 조합해서 사용할 때
- 문제가 발생하는 경우 활용합니다.
- `noinline` : 아규먼트로 인라인 함수를 받을 수 없게 만듭니다. 인라인 함수가 아닌 함수를 아규먼트로 사용하고 싶을때 사용합니다.


## 47. 인라인 클래스의 사용을 고려하라

생성자 프로퍼티가 하나인 클래스 앞에 `inline`을 붙이면, 해당 객체를 사용하는 위치가 모두 해당 프로퍼티로 교체됩니다.
인라인 클래스는 다른 자료형을 래핑해서 새로운 자료형을 만들 때 많이 사용됩니다.

```kotlin
inline class Name(private val value: String) {
    fun greet() {
        print("Hello, I am $value")
    }
}

val name: Name = Name("Marcin")
name.greet()
        
// 컴파일 때 다음과 같은 형태로 변경
val name: Name = Name("Marcin")
Name.'greet-impl'(name)
```

- 측정 단위를 표현할 때

```kotlin
// time이 Int이므로 어떤 시간이 올지 혼동이 올 수 있습니다.
interface Timer {
    fun callAfter(time: Int, callback: () -> Unit)
}


// 직접 inline class를 통해서 타입에 재한을 줌
inline class Millis(val mills: Int)

interface Timer {
    fun callAfter(time: Millis, callback: () -> Unit)
}
```

- 타입 오용으로 발생하는 문제를 막을 때

```kotlin
// 모두 Int 타입이므로 실수로 잘못된 값을 넣을 수 있습니다.
@Entity(tableName = "grades")
class Grades(
    @ColumnInfo(name = "studentId")
    val studentId: Int,
    @ColumnInfo(name = "teacherId")
    val teacherId: Int,
    @ColumnInfo(name = "schoolId")
    val schoolId: Int,
)

// 인라인 클래스로 대체
inline class StudentId(val studentId: Int)
inline class TeacherId(val teacherId: Int)
inline class SchoolId(val schoolId: Int)

@Entity(tableName = "grades")
class Grades(
    @ColumnInfo(name = "studentId")
    val studentId: StudentId,
    @ColumnInfo(name = "teacherId")
    val teacherId: TeacherId,
    @ColumnInfo(name = "schoolId")
    val schoolId: SchoolId,
)
```

## 48. 더 이상 사용하지 않는 객체의 레퍼런스를 제거하라

패스

## 49. 하나 이상의 처리 단계를 가진 경우에는 시퀀스를 사용하라

거의 비슷하게 사용할 수 있지만 
`Sequence`는 `toList`, `count 등의 최종 연산이 이루어질때 수행이됩니다.
`Iterable`은 처리 함수를 사용할 때마다 연산이 이루어져 `List가 만들어집니다`.

```kotlin
// 매번 연산
interface Iterable<out T> {
    operator fun iterator(): Iterator<T>
}

// 결과가 불릴때까지 연산이 지연
interface Sequence<out T> {
    operator fun iterator(): Iterator<T>
}
```

시퀀스의 지연 처리의 장점

- 자연스러운 처리 순서를 유지합니다. : 순서에 따라 `Iterable`과 `Sequence`의 결과가 달라질 수 있습니다.
- 최소한만 연산합니다. : 시퀀스는 `중간 연산`을 사용할 수 있어서 원하는 개수만큼만 원하는 처리를 할 수 있습니다.
- 무한 시퀀스 형태로 사용할 수 있습니다. : 최종 연산자를 만났을때 연산이 가능하기 때문에 무한 시퀀스를 만들 수 있습니다.
- 각각의 단계에서 컬렉션을 만들어 내지 않습니다.


## 50. 컬랙션 처리 단계 수를 제한하라

컬렉션의 처리 메서드는 비용이 많이 들기 때문에 단계수를 줄이면 좋습니다.

| good                                                                                                    | best                                                                |
|---------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------|
| .filter { it != null }<br/>.map { it }                                                                  | .filterNotNull()                                                    |
| .map { Transformation }<br/>.filterNotNull()                                                            | .mapNotNull { Transformation }                                      |
| .map { Transformation }<br/>.joinToString()                                                             | .joinToString { Transformation }                                    |
| .filter { Predicate1 }<br/>.filter{ Predicate2 }                                                        | .filter { Predicate1 && Predicate2 }                                |
| .filter { it is Type }<br/>.map { it as Type }                                                          | .filterIsInstance<Type>()                                           |
| .sortedBy { Key2 }<br/>.sortedBy { Key1 }                                                               | .sortedWith(<br/>compareBy({ Key1 }, { Key2 })<br/>)                |
| .listOf(...)<br/>.filterNotNull()                                                                       | .listOfNotNull(...)                                                 |
| .withIndex()<br/>.filter { (index, element) -> <br/> Predicate using index <br/>}<br/>.map { it.value } | .filterIndexed { index, element -><br/>Predicate using index <br/>} |

## 51. 성능이 중요한 부분에는 기본 자료형 배열을 사용하라

기본 자료형은 아래와 같은 특징이 있기 때문에 성능이 중요한 부분에서는 고려하면 좋습니다.

- 가볍습니다.
- 빠릅니다.

## 52. mutable 컬렉션 사용을 고려하라

`immutable 컬렉션`보다 `mutable 컬렉션`이 성능적인 측면에서 더 빠릅니다.

이유는 `immutable 컬렉션은`은 컬렉션을 복제하는 처리 비용이 많이 들기 때문입니다.


