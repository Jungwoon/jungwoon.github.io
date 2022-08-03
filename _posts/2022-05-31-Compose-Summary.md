---
layout: post
title: "Jetpack Compose"
image: '/assets/img/'
description: 'Jetpack Compose'
tags:
- Jetpack
- Compose
categories:
- Jetpack
---

`Jetpack Compose` 는 `선언형 UI`를 만들어주는 도구로 기존에 `XML`에서 작업하던 `Layout`이나 `Widget` 작업들을 명령을 통하여 
좀 더 쉽게 작성할 수 있게 도와줍니다. 이번에 회사에서 `Compose`를 점진적으로 도입한다고 하여 공부하면서 조금씩 정리를 해보고자 합니다.

해당 내용은 [구글 공식 문서](https://developer.android.com/jetpack/compose/mental-model)를 통해서 학습하였습니다.

---

## 지금까지의 방식

지금까지 `Android`의 `뷰`와 `레이아웃`은 XML을 통해서 `UI 계층 트리`를 구성하여 만들었었고 이로 인해서 UI를 변경해야하는 이벤트가 발생하면
`findViewById()`로 트리를 찾고 `setter()`나 `getter()`를 통해서 위젯의 내부 상태를 변경하였습니다.
이렇게 수동으로 뷰를 조작할 경우 `복잡도`가 올라가서 오류가 발생할 가능성이 커지고 그로 인해 유지 관리가 힘들어지게 됩니다.

---

## Compose

이때 `Compose`를 사용하게 되면 특정 시점에 UI의 어떤 부분을 다시 그리는지 지능적으로 선택을 할 수 있고, 
이로 인해서 컴퓨팅 파워등의 리소스를 줄일 수 있게 됩니다.

`Compose`는 아래와 같이 `@Composable` 어노테이션을 통해 UI로 변환가능한 함수라는 걸 `Compose 컴파일러`에게 알립니다. 
이렇게 `구성 가능한 함수(=Composable Function)`를 만들어서 간단하게 `Widget`을 구성할 수 있습니다. 

```kotlin
@Composable
fun Greeting(name: String) {
   Text("Hello $name")
}
```

- 해당 함수는 UI로 표시하기 때문에 별도의 `반환값이 없습니다`.
- `Kotlin`으로 작성되기 때문에 `동적`일 수 있습니다.
- 입력이 변경될 때 `구성 가능한 함수를 다시 호출(=재구성)`하여 `필요한 부분만 변경`을 하는데, 이걸 지능적으로 처리하기 때문에 리소스를 줄일 수 있습니다.
- 상대적으로 `Stateless`로 `setter`와 `getter`를 노출하지 않습니다.

---

## 재구성

재구성은 입력이 변경될 때 구성 가능한 함수를 다시 호출하는 프로세스입니다. 이때, 변경되는 값에 종속되지 않은 다른 함수는 재구성되지 않습니다.

```kotlin
@Composable
fun ClickCounter(clicks: Int, onClick: () -> Unit) {
    Button(onClick = onClick) {
        Text("I've been clicked $clicks times")
    }
}
```
위의 예에서는 클릭을 할때마다 `clicks`의 값이 증가하는데, 이때에는 변화된 clicks 부분만 재구성하고 그 외에는 유지합니다. 

- `재구성`은 `바뀐 부분만 변경`하고 `나머지는 건너뛰기` 때문에 효율적입니다.
- `재구성`은 `취소`될 수 있습니다.
- `재구성`은 `낙관적`이라 매개변수가 `다시 변경되기 전`에 `재구성`을 `완료`할 것으로 예상합니다.
- `구성 가능한 함수`는 `순서에 상관없이` 실행될 수 있습니다.
- `구성 가능한 함수`는 `동시에` 실행될 수 있습니다.
- `구성 가능한 함수`는 `매우 자주` 실행될 수 있습니다.
- `구성 가능한 함수`는 `빨라야` 합니다.

---

## 상태 및 컴포지션

`Compose`를 `업데이트` 하기 위해선 `새 인수`로 동일한 컴포저블을 호출됩니다.
이러한 인수는 UI의 상태를 표현하고 `상태가 업데이트`될 때마다 `재구성`이 발생합니다.

`remember` : 구성 가능한 함수는 `remember`를 이용하여 아래와 같이 `메모리`에 `상태를 저장`할 수 있습니다.
> remember는 객체를 컴포지션에 저장하고, remember를 호출한 컴포저블이 컴포지션에서 삭제되면 그 객체를 잊습니다.

```kotlin
val mutableState = remember { mutableStateOf(default) }
var value by remember { mutableStateOf(default) }               // 델리게이트 패턴을 통해, 매번 *.value를 사용하지 않아도 됨
val (value, setValue) = remember { mutableStateOf(default) }
```

> `remember`가 재구성 과정 전체에서 상태를 유지하는데 도움은 주지만 구성 변경 전반에서는 상태가 유지되지 않기 때문에 이 경우에는 `rememberSaveable`을
> 사용해야 합니다. 이를 이용하면 `Bundle`에 저장할 수 있는 모든 값을 자동으로 저장합니다.


`Compose`는 `State<T> 객체`를 읽어오면서 자동으로 `재구성`됩니다.

`Observable`한 데이터 타입을 이용해서 `Compose`에서 사용가능하게 하기 위해서는 `State<T>` 또는 `MutableState<T>`로 변환해야 하는데
아래 세 가지 유형에서 `State<T>`로 변환할 수 있는 함수를 가지고 있습니다.

- LiveData
- Flow
- RxJava2

> `Compose`는 `State<T>` 객체를 읽어오면서 자동으로 재구성합니다. 그렇기 때문에 `LiveData`의 관찰을 사용해서 `Compose`를 구성할 경우
> `LiveData<T>.observeAsState()` 같은 구성 가능한 확장 함수를 사용하여 `State<T>`로 변환해야 합니다.


> 주의: Compose에서 ArrayList<T> 또는 mutableListOf() 같은 변경 가능 객체를 상태로 사용하면 앱에 잘못되거나 오래된 데이터가 표시될 수 있습니다.


- `Stateful` : `remember`를 사용하여 상태를 가지는 컴포저블입니다.
- `Stateless` : 상태를 갖지 않는 컴포저블로, 보통 `상태 호이스팅`을 사용합니다.

---

## 상태 호이스팅

`Compose`에서 컴포저블을 `Stateless`로 만들기 위해 `상태`를 `컴포저블의 호출자`로 옮기는 패턴

```kotlin
@Composable
fun HelloScreen() {
    // 상태는 바깥에서 관리
    var name by rememberSaveable { mutableStateOf("") }
    
    // 상태를 직접 가지지 않는 함수에 값만 전달
    HelloContent(name = name, onNameChange = { name = it })
}

@Composable
fun HelloContent(name: String, onNameChange: (String) -> Unit) {
    Column(modifier = Modifier.padding(16.dp)) {
        Text(
            text = "Hello, $name",
            modifier = Modifier.padding(bottom = 8.dp),
            style = MaterialTheme.typography.h5
        )
        OutlinedTextField(
            value = name,
            onValueChange = onNameChange,
            label = { Text("Name") }
        )
    }
}
```

이렇게 `HelloContent()`를 사용하면 여러 상황에서 재사용하고 테스트할 수 있습니다.

---

## 단방향 데이터 흐름

단방향 데이터 흐름은 `상태는 내려가고` `이벤트는 올라가는` 형태입니다.

![](https://developer.android.com/static/images/jetpack/compose/udf-hello-screen.png?hl=ko)

앱의 UI 업데이트 루프

- 이벤트 : UI 일부가 이벤트를 생성하여 위로 전달
- 상태 업데이트 : 이벤트 핸들러가 상태를 변경
- 상태 표시 : 상태 홀더가 상태를 아래로 전달하고 UI가 상태를 표시

아래 3가지 규칙을 통해서 상태를 끌어 올릴 수 있습니다.

1. 상태는 적어도 그 상태를 사용하는 모든 컴포저블의 가장 낮은 공통 상위 요소로 끌어올려야 합니다(읽기). 
2. 상태는 최소한 변경될 수 있는 가장 높은 수준으로 끌어올려야 합니다(쓰기). 
3. 동일한 이벤트에 대한 응답으로 두 상태가 변경되는 경우 두 상태를 함께 끌어올려야 합니다.

---

## 상태 복원

`Activity` 또는 `Process`가 다시 생성된 경우에는 `rememberSaveable`을 사용하여 `UI 상태`를 `복원`합니다.

`Bundle`에 저장되는 모든 데이터 유형은 자동으로 저장됩니다.

만약 `Bundle`에 추가할 수 없는 데이터 타입인 경우 아래 방법들을 사용할 수 있습니다.

### Parcelize

간단히 `@Parcelize 어노테이션`을 통해 번들로 저장할 수 있습니다.

```kotlin
@Parcelize
data class City(val name: String, val country: String) : Parcelable

@Composable
fun CityScreen() {
    var selectedCity = rememberSaveable {
        mutableStateOf(City("Madrid", "Spain"))
    }
}
```

### MapSaver

`@Parcelize`를 사용 못하는 경우에 `mapSaver`도 이용 가능합니다.

```kotlin
data class City(val name: String, val country: String)

val CitySaver = run {
    val nameKey = "Name"
    val countryKey = "Country"
    mapSaver(
        save = { mapOf(nameKey to it.name, countryKey to it.country) },
        restore = { City(it[nameKey] as String, it[countryKey] as String) }
    )
}

@Composable
fun CityScreen() {
    var selectedCity = rememberSaveable(stateSaver = CitySaver) {
        mutableStateOf(City("Madrid", "Spain"))
    }
}
```

### ListSaver

`index`를 키로 사용하는 방법입니다.

```kotlin
data class City(val name: String, val country: String)

val CitySaver = listSaver<City, Any>(
    save = { listOf(it.name, it.country) },
    restore = { City(it[0] as String, it[1] as String) }
)

@Composable
fun CityScreen() {
    var selectedCity = rememberSaveable(stateSaver = CitySaver) {
        mutableStateOf(City("Madrid", "Spain"))
    }
}
```

---

## 상태 관리

관리하는 컴포저블의 크기가 커지는 경우에는, 관심사 분리를 위해서 상태 관리는 `상태 홀더`에 위임하는 것이 좋습니다.

> `상태 홀더`는 컴포저블의 로직과 상태를 관리합니다.

- 컴포저블 : 간단한 UI 요소 상태 관리 목적
- 상태 홀더 : 복잡한 UI 요소 상태 관리 목적 (UI 상태 + UI 로직), 단일 위젯 또는 전체 화면 같이 UI 요소의; 범위에 따라 다양한 크기로 제공이 가
- ViewModel : 비즈니스 로직 및 화면에 따라 UI를 관리하는 상태 홀더 (그냥 ViewModel)

![Compose 상태 관리와 관련된 각 항목의 종속 항목(선택사항) 요약.](https://developer.android.com/images/jetpack/compose/state-dependencies.svg?authuser=1&hl=ko)

아래에서는 위의 각각의 예제를 살펴 봅니다.

### 컴포저블

상태와 로직이 간단한 경우, 이런 경우 `변화할 수 있는 상태(여기서는 ScaffoldState)`는 하나의 컴포저블에서 관리를 해야
단일 정보 소스 원칙을 유지할 수 있습니다.

```kotlin
@Composable
fun MyApp() {
    MyTheme {
        val scaffoldState = rememberScaffoldState()
        val coroutineScope = rememberCoroutineScope()

        Scaffold(scaffoldState = scaffoldState) {
            MyContent(
                showSnackbar = { message ->
                    coroutineScope.launch {
                        scaffoldState.snackbarHostState.showSnackbar(message)
                    }
                }
            )
        }
    }
}
```

### 상태 홀더

여러 UI 요소의 상태가 관련되어 있는 `복잡한 UI 로직이 포함된 컴포저블`은 책임을 `상태 홀더에 위임`해야 합니다.
이렇게 하면 `컴포저블`이 `UI 요소`를 책임지고 `상태홀더`가 `UI 로직`과 `UI 상태`를 관리합니다.

용어 정리
- UI 요소 상태 : UI 요소를 호이스팅한 상태
- 화면 상태 및 UI 상태 : 화면에 표시되어야 하는 요소 ex) HomeUi, CartUi 등의 Ui 객체
- UI 동작 로직 및 UI 로직 : 화면에 상태 변경을 표시하는 방법
- 비즈니스 로직 : 상태 변경에 따라 진행해야할 작업


```kotlin
// Plain class that manages App's UI logic and UI elements' state
class MyAppState(
    val scaffoldState: ScaffoldState,
    val navController: NavHostController,
    private val resources: Resources,
    /* ... */
) {
    val bottomBarTabs = /* State */

    // Logic to decide when to show the bottom bar
    val shouldShowBottomBar: Boolean
        get() = /* ... */

    // Navigation logic, which is a type of UI logic
    fun navigateToBottomBarRoute(route: String) { /* ... */ }

    // Show snackbar using Resources
    fun showSnackbar(message: String) { /* ... */ }
}


// 상태 관리를 위한 부분
@Composable
fun rememberMyAppState(
    scaffoldState: ScaffoldState = rememberScaffoldState(),
    navController: NavHostController = rememberNavController(),
    resources: Resources = LocalContext.current.resources,
    /* ... */
) = remember(scaffoldState, navController, resources, /* ... */) {
    MyAppState(scaffoldState, navController, resources, /* ... */)
}
```

`MyApp`은 `UI 요소를 방출`하는데 집중하고 `UI 로직`과 `UI 상태`는 `MyAppState`에 위임합니다.

위에서 만든 `rememberMyAppState()`에 `UI로직`과 `UI상태`에 대한 모든걸 위임

```kotlin
@Composable
fun MyApp() {
    MyTheme {
        val myAppState = rememberMyAppState()
        Scaffold(
            scaffoldState = myAppState.scaffoldState,
            bottomBar = {
                if (myAppState.shouldShowBottomBar) {
                    BottomBar(
                        tabs = myAppState.bottomBarTabs,
                        navigateToRoute = {
                            myAppState.navigateToBottomBarRoute(it)
                        }
                    )
                }
            }
        ) {
            NavHost(navController = myAppState.navController, "initial") { /* ... */ }
        }
    }
}
```

### ViewModel

`ViewModel`은 `비즈니스 레이어`와 `데이터 영역` 같이 `계층 구조가 다르게 구성`되어 있는 상태에서 사용합니다. 

- `ViewModel`은 `컴포지션보다 수명이 길기 때문`에 구성 변경 후에도 유지가 됩니다.
- `화면 수준 컴포저블`에서 `ViewModel`을 통해 `UI 상태`와 `정보 소스`로 사용하는게 좋습니다.
> `ViewModel`은 `장기 지속 참조`를 보유하면 메모리 누수가 발생할 수 있습니다


아래에는 `ViewModel`이 생명주기가 더 길기 때문에 `ViewModel`이 `상태홀더(=uiState)`를 직접 갖는 예제입니다.

```kotlin
data class ExampleUiState(
    val dataToDisplayOnScreen: List<Example> = emptyList(),
    val userMessages: List<Message> = emptyList(),
    val loading: Boolean = false
)

class ExampleViewModel(
    private val repository: MyRepository,
    private val savedState: SavedStateHandle
) : ViewModel() {

    var uiState by mutableStateOf(ExampleUiState())
        private set

    // Business logic
    fun somethingRelatedToBusinessLogic() { /* ... */ }
}

@Composable
fun ExampleScreen(viewModel: ExampleViewModel = viewModel()) {

    val uiState = viewModel.uiState
    /* ... */

    ExampleReusableComponent(
        someData = uiState.dataToDisplayOnScreen,
        onDoSomething = { viewModel.somethingRelatedToBusinessLogic() }
    )
}

@Composable
fun ExampleReusableComponent(someData: Any, onDoSomething: () -> Unit) {
    /* ... */
    Button(onClick = onDoSomething) {
        Text("Do something")
    }
}
```

---

## 수명 주기

컴포지션은 UI를 기술하는 컴포저블의 트리 구조입니다. 

- 생성 : `초기 컴포지션을 통해서`만 `생성` 
- 업데이트 : `리컴포지션을통해서`만 `업데이트`
> 핵심 사항: 컴포저블의 수명 주기는 컴포지션 시작, 0회 이상 재구성 및 컴포지션 종료 이벤트로 정의됩니다.

![](https://developer.android.com/images/jetpack/compose/lifecycle-composition.png?authuser=1&hl=ko)


아래와 같은 소스에서는 다음과 같이 구성이 됩니다. 
> 아래 색상이 다른 요소는 별도의 인스턴스입니다.

```kotlin
@Composable
fun MyComposable() {
    Column {
        Text("Hello")
        Text("World")
    }
}
```

![](https://developer.android.com/images/jetpack/compose/lifecycle-hierarchy.png?authuser=1&hl=ko)

`State에 변화가 있는 경우`에만 `재구성`을 합니다. 

아래 경우에도 `LoginInput()`은 변화가 없기 때문에 기존의 인스턴스를 그대로 사용합니다.

```kotlin
@Composable
fun LoginScreen(showError: Boolean) {
    if (showError) {
        LoginError()
    }
    LoginInput() // This call site affects where LoginInput is placed in Composition
}

@Composable
fun LoginInput() { /* ... */ }
```

![](https://developer.android.com/images/jetpack/compose/lifecycle-showerror.png?authuser=1&hl=ko)


만약 `MovieOverview`가 마지막에 추가되어서 `순서에 변화가 없는 경우` `기존에 인스턴스들`을 `재사용`합니다.

![](https://developer.android.com/static/images/jetpack/compose/lifecycle-newelement-bottom.png?hl=ko)

하지만 목록의 상단이나 가운데 등에 추가한거나 삭제하여 순서에 변화가 있는 경우에는 `순서가 바뀐` 모든 `MovieOverview`에서 `재구성`이 발생합니다.

![](https://developer.android.com/static/images/jetpack/compose/lifecycle-newelement-top-all-recompose.png?hl=ko)

이런 경우에는 `특정 식별자`를 통해서 변화를 감지하면 `식별자`를 통해 `재사용`이 가능합니다.

그러기 위해서 `Key`를 사용합니다.

```
@Composable
fun MoviesScreen(movies: List<Movie>) {
    Column {
        for (movie in movies) {
            key(movie.id) { // Unique ID for this movie
                MovieOverview(movie)
            }
        }
    }
}
```

![](https://developer.android.com/images/jetpack/compose/lifecycle-newelement-top-keys.png?authuser=1&hl=ko)

### 안정적인 유형

모든 입력이 안정적이고 변경되지 않았으면 리컴포지션을 건너뛸 수 있습니다.

안정적인 유형은 아래 규칙을 만족해야 합니다.

- 두 인스턴스의 `equals 결과가 동일`한 두 인스턴스의 경우 항상 동일합니다.
- 유형의 `공개 속성이 변경`되면 컴포지션에 알림이 전송됩니다.
- 모든 공개 속성 유형도 안정적입니다.

만약 안정적이지 않은 유형은 `@Stable 주석`을 통해 안정적인 것으로 간주할 수 있습니다.

```kotlin
// Marking the type as stable to favor skipping and smart recompositions.
@Stable
interface UiState<T : Result<T>> {
    val value: T?
    val exception: Throwable?

    val hasError: Boolean
        get() = exception != null
}
```


---

## 수정자

수정자를 이용하면 레이아웃, 크기, 동작 및 모양 변경 등의 작업들을 처리할 수 있습니다.

```kotlin
@Composable
private fun Greeting(name: String) {
  Column(modifier = Modifier
    .padding(24.dp)
    .fillMaxWidth()
  ) {
    Text(text = "Hello,")
    Text(text = name)
  }
}
```

수정자는 `순서`가 매우 중요합니다.

먼저 아래 코드에서는 `clickable`이 `padding` 적용 전에 적용되었기 때문에 클릭시에 `padding이 적용되기 전 영역`이 전부 클릭이 됩니다.

```kotlin
@Composable
fun Card(onClick: () -> Unit) {
    Column(
        Modifier
            .clickable(onClick = onClick)
            .padding(16.dp)
            .fillMaxWidth()
    ) {
        // rest of the implementation
    }
}
```
반대로 아래 코드는 `padding`을 먼저 적용을 하고 `clickable`이 적용이 되었기 때문에 `padding이 적용된 영역`이 클릭이 됩니다.

```kotlin
@Composable
fun Card(onClick: () -> Unit) {
    Column(
        Modifier
            .padding(16.dp)
            .clickable(onClick = onClick)
            .fillMaxWidth()
    ) {
        // rest of the implementation
    }
}
```

컴포즈는 기본적으로 `상위 레이아웃의 특성(크기, 너비, 색 등등)`을 그대로 하위 레이아웃이 가져옵니다.
하지만 `상위 레이아웃의 특성을 무시`하고 `size`를 정하고 싶으면 `requireSize`를 이용합니다.

```kotlin
@Composable
fun ArtistCard(/*...*/) {
    Row(
        modifier = Modifier.size(width = 400.dp, height = 100.dp)
    ) {
        Image(
            // 위의 높이는 100dp 이지만 requiredSized에 의해서 150dp가 됨
            modifier = Modifier.requiredSize(150.dp) 
        )
        Column { /*...*/ }
    }
}
```

> 수정자에 대한 자세한 내용은 (다음 링크)[https://developer.android.com/jetpack/compose/modifiers-list?authuser=1&hl=ko]에서 확인 가능합니다.

---

## Compose의 부수 효과

컴포저블에서는 `부수 효과`가 없어야 하지만 케이스에 따라서 필요한 경우에는 컴포저블의 수명 주기를 고려해서 `부수 효과`를 호출해야 합니다. 그래서
이러한 부수 효과를 예측 가능하게 실행하기 위해서 `Effect API`를 사용합니다.

> 부수 효과 : 구성 가능한 함수의 범위 밖에서 발생하는 앱 상태에 관한 변경사항


### LaunchedEffect

`컴포저블 함수내`에서 `Suspend Function`을 사용하기 위해서는 `LaunchedEffect`를 사용해야 합니다.

```kotlin

// key는 하나 이상으로 구성할 수 있습니다.
LaunchedEffect(key) {
    // ...
}
```

`LaunchedEffect`가 컴포지션을 종료하면 코루틴이 취소됩니다.

`LaunchedEffect`가 `다른 키`로 재구성되면 `기존 코루틴`이 `취소`되고 `새 코루틴`에서 `새 정지 함수`가 실행됩니다.

```kotlin
@Composable
fun MyScreen(
    state: UiState<List<Movie>>,
    scaffoldState: ScaffoldState = rememberScaffoldState()
) {

    if (state.hasError) {
        // `scaffoldState.snackbarHostState`의 상태변화에 따라 실행됩니다.
        LaunchedEffect(scaffoldState.snackbarHostState) {
            // 아래 함수는 코루틴을 이용한 정지함수로 `LauchedEffect` 블록 안에 있어야 합니다.
            scaffoldState.snackbarHostState.showSnackbar(
                message = "Error message",
                actionLabel = "Retry message"
            )
        }
    }

    Scaffold(scaffoldState = scaffoldState) {
        /* ... */
    }
}
```


### rememberCoroutineScope

`LaunchedEffect`는 컴포저블 함수내에서만 사용할 수 있기 때문에 밖에서 사용하면서, 
컴포지션 종료시에 자동으로 취소되도록 하려면 `rememberCoroutineScope`를 사용해야 합니다.

```kotlin
@Composable
fun MoviesScreen(scaffoldState: ScaffoldState = rememberScaffoldState()) {

    // scope를 컴포저블 함수 바깥에서 정의
    val scope = rememberCoroutineScope()

    Scaffold(scaffoldState = scaffoldState) {
        Column {
            /* ... */
            Button(
                onClick = {
                    scope.launch {
                        // 정지함수 호출
                        scaffoldState.snackbarHostState.showSnackbar("Something happened!")
                    }
                }
            ) {
                Text("Press me")
            }
        }
    }
}
```
---

### rememberUpdatedState

주요 매개변수의 상태가 바뀌면 `LauchedEffect`가 다시 시작되는데, 이때, 다시 시작하지 않게 하기 위해서는 `rememberUpdatedState`를 사용하여야 합니다.

```kotlin
@Composable
fun LandingScreen(onTimeout: () -> Unit) {
    
    // 여기서 이렇게 선언을 해줘야지 아래에서 상태변경시 다시 시작하지 않습니다.
    val currentOnTimeout by rememberUpdatedState(onTimeout)

    LaunchedEffect(true) {
        delay(SplashWaitTimeMillis)
        currentOnTimeout() // 이건 LaunchedEffect가 다시 호출이 되더라도 재호출되지 않음
    }

    /* Landing screen content */
}
```


---

### DisposableEffect

`변화`가 일어나거나 `컴포지션이 종료`가 되었을때 직접 `정리`가 필요한 경우에는 `DisposableEffect`를 사용합니다.
`DisposableEffect`의 `키`가 `변경`되면 컴포저블이 현재 효과를 삭제(정리)하고 효과를 다시 호출하여 재설정해야 합니다.

```kotlin
@Composable
fun HomeScreen(
    lifecycleOwner: LifecycleOwner = LocalLifecycleOwner.current,
    onStart: () -> Unit,
    onStop: () -> Unit
) {
    val currentOnStart by rememberUpdatedState(onStart)
    val currentOnStop by rememberUpdatedState(onStop)

    // `lifecycleOwner`의 상태 변화에 따라서 호출됩니다.
    DisposableEffect(lifecycleOwner) {
        val observer = LifecycleEventObserver { _, event ->
            if (event == Lifecycle.Event.ON_START) {
                currentOnStart()
            } else if (event == Lifecycle.Event.ON_STOP) {
                currentOnStop()
            }
        }

        lifecycleOwner.lifecycle.addObserver(observer)
        
        // Dispose 시에 Observer를 제거해줍니다.
        // 빈 블록을 포함하는 것은 좋은 방법이 아닙니다.
        onDispose {
            lifecycleOwner.lifecycle.removeObserver(observer)
        }
    }

    /* Home screen content */
}
```

---

### SideEffect

Compose 상태를 `Compose에서 관리하지 않는 객체와 공유`하려면 `리컴포지션 성공` 시마다 호출되는 `SideEffect`를 사용합니다.

```kotlin
@Composable
fun rememberAnalytics(user: User): FirebaseAnalytics {
    val analytics: FirebaseAnalytics = remember {
        /* ... */
    }
    
    // 리컴포지션이 성공할때마다 애널리틱스에 로그를 세팅
    SideEffect {
        analytics.setUserProperty("userType", user.userType)
    }
    return analytics
}
```

---

### produceState

`State`를 `리턴`해야하는 경우에는 `produceState`를 사용합니다.

`Flow`, `LiveData`, `RxJava` 같은 `비 Compose상태 -> Compose 상태`로 반환하기 위해서는 `produceState`를 사용해야 합니다.
컴포지션이 시작하면 프로듀서가 실행되고, 컴포지션을 종료하면 취소됩니다.

`반환된 State`는 합성되며 `동일한 값`을 설정해도 `리컴포지션`이 일어나지 않습니다.

`구독을 삭제`할때에는 `awaitDispose 함수`를 사용해야 합니다.

```kotlin
@Composable
fun loadNetworkImage(
    url: String,
    imageRepository: ImageRepository
): State<Result<Image>> {

    return produceState<Result<Image>>(initialValue = Result.Loading, url, imageRepository) {
        val image = imageRepository.load(url)

        value = if (image == null) {
            Result.Error
        } else {
            Result.Success(image)
        }
    }
}
```


---

### derivedStateOf

여러개의 상태를 바라보다가 하나라도 변경되면 호출이 필요할때는 `derivedStateOf`를 사용합니다.

```kotlin
@Composable
fun TodoList(highPriorityKeywords: List<String> = listOf("Review", "Unblock", "Compose")) {

    val todoTasks = remember { mutableStateListOf<String>() }

    // highPriorityKeywords의 상태를 따라 재구성됩니다.
    val highPriorityTasks by remember(highPriorityKeywords) {
        // 하지만 여기에 todoTasks의 상태도 따라가게끔 추가됩니다.
        derivedStateOf { 
            todoTasks.filter { 
                it.containsWord(highPriorityKeywords) 
            } 
        }
    }

    Box(Modifier.fillMaxSize()) {
        LazyColumn {
            items(highPriorityTasks) { /* ... */ }
            items(todoTasks) { /* ... */ }
        }
        /* Rest of the UI where users can add elements to the list */
    }
}
```

---

### snapshotFlow

`State<T> 객체`를 `콜드 Flow`로 변환합니다.
`snapshotFlow` 블록 내의 `State 객체의 변경`이 되었을때 새 값이 이전에 내보낸 값과 다른 경우 `Flow`에서 `새 값을 수집기에 내보냅니다`

```kotlin
val listState = rememberLazyListState()

LazyColumn(state = listState) {
    // ...
}

LaunchedEffect(listState) {
    snapshotFlow { listState.firstVisibleItemIndex }
        .map { index -> index > 0 }
        .distinctUntilChanged()
        .filter { it == true }
        .collect {
            MyAnalyticsService.sendScrolledPastFirstItemEvent()
        }
}
```

---

## Compose의 단계

![](https://developer.android.com/images/jetpack/compose/phases-3-phases.svg)

1. Composition : Composable 함수를 실행하여 UI를 만듭니다.
2. Layout : UI를 측정하고 배치합니다.
   1. Measure : 측정 단계에서는 Layout 컴포저블에 전달된 측정 람다와 LayoutModifier 인터페이스의 MeasureScope.measure 메서드 등을 실행
   2. Layout : 배치 단계에서는 layout 함수의 배치 블록과 Modifier.offset { … }의 람다 블록 등을 실행
3. Drawing : UI가 실제 캠퍼스에 그려집니다.

> 일반적으로 위의 순서는 동일하지만, `BoxWithConstraints`, `LazyColumn`, `LazyRow`는 중요한 예외로, 
> 하위 요소의 컴포지션이 상위 요소의 레이아웃 단계에 따라 달라집니다


---

## Compose의 기본 레이어

![](https://developer.android.com/static/images/jetpack/compose/layering-major-layers.svg?hl=ko)

각 레이어는 하위 수준에 기반하고, 상위 수준의 구성요소를 만들기 위해 기능을 결합합니다

- Material
  - 이 모듈은 Compose UI에 머티리얼 디자인 시스템의 구현을 제공하고 테마 설정 시스템, 스타일 적용된 구성요소, 물결 표시, 아이콘도 제공합니다. 앱에 머티리얼 디자인을 사용할 때는 이 레이어를 기반으로 빌드합니다.
- Foundation
  - 이 모듈은 Compose UI에 Row, Column, LazyColumn, 특정 동작 인식 같은 디자인 시스템에 구속되지 않는 구성요소를 제공합니다. 자체 디자인 시스템을 만들 때는 기초 레이어를 기반으로 빌드하는 것이 좋습니다.
- UI
  - UI 레이어는 여러 개의 모듈(ui-text, ui-graphics, ui-tooling)로 구성됩니다. 그러한 모듈은 LayoutNode, Modifier, 입력 핸들러, 맞춤 레이아웃, 그리기 같은 UI 툴킷의 기본 사항을 구현 합니다. UI 툴킷의 기본 개념만 필요한 경우 이 레이어를 기반으로 빌드하는 것이 좋습니다.
- 런타임
  - 이 모듈은 remember, mutableStateOf, @Composable 주석, SideEffect 같은 Compose 런타임의 기초를 제공합니다. UI가 아닌 Compose의 트리 관리 기능만 필요한 경우 이 레이어에 바로 빌드하는 것이 좋습니다.

---

## 성능

Compose에서 성능을 올릴 수 있는 몇가지 원칙에 대해서 정리해보겠습니다.

1. 가능하면 구성 가능한 함수 외부로 계산을 이동합니다 
   1. 구성 가능한 함수는 UI가 변경될 때마다 다시 실행해야 할 수 있습니다. 
   2. 컴포저블에 넣은 모든 코드는 잠재적으로 애니메이션의 모든 프레임에서 다시 실행됩니다.
   3. 따라서 컴포저블의 코드를 `UI를 빌드하는 데 실제로 필요한 것으로만 제한`해야 합니다. 
2. 최대한 오랫동안 상태 읽기를 연기합니다. 
   1. 상태 읽기를 하위 컴포저블 또는 이후 단계로 이동하면 `재구성을 최소화하거나 컴포지션 단계를 완전히 건너뛸 수 있습니다.` 
   2. 자주 변경되는 상태의 `상태 값 대신 람다 함수를 전달`하고, 자주 변경되는 상태를 전달할 때 람다 기반 수정자를 기본으로 선택하여 이를 실행할 수 있습니다. 
   3. 이 기법의 예는 최대한 읽기 연기 섹션을 참고하세요. 다음 섹션에서는 이러한 종류의 문제를 일으킬 수 있는 구체적인 코드 오류를 설명합니다.
3. remember를 사용하여 비용이 많이 드는 계산 최소화
   1. 중요한 기법은 remember를 사용하여 계산 결과를 저장하는 것
4. 지연 레이아웃 키 사용
   1. `LazyColumn`이나 `LazyRow`같은 지연 레이아웃에 `Key`를 사용 함으로써 재구성 스킵에 도움을 줄 수 있습니다.
5. `derivedStateOf`를 사용하여 재구성 제한
   1. 상태가 빠르게 변경되는(ex - 스크롤링) 상황에서는 UI가 필요 이상으로 재구성될 수 있습니다.
   2. 파생 상태를 사용하면 실제로 재구성을 트리거해야 하는 상태 변경을 Compose에 알릴 수 있습니다.
6. 역방향 쓰기 방지
   1. 컴포지션에서 상태에 쓰지 않음으로써 역방향 쓰기를 완전히 방지할 수 있습니다.
   2. 람다에서 상태를 쓰도록 합니다.


---

## CompositionLocal

`CompositionLocal`은 암시적으로 `컴포지션을 통해 데이터를 전달`하는 도구입니다.

암시적 방법으로 사용할 수 있는 트리 범위의 명명된 객체를 만들 수 있습니다.
`CompositionLocal`은 `머티리얼 테마`에서 `내부적으로 사용`합니다.

`MaterialTheme`은 나중에 컴포지션의 하위 부분에서 가져올 수 있는 세 개의 `CompositionLocal 인스턴스(색상, 서체, 도형)를 제공`하는 객체 입니다. 
이러한 인스턴스는 구체적으로 `LocalColors`, `LocalShapes`, `LocalTypography` 속성으로, `MaterialTheme` `colors`, `shapes`, `typography` 속성을 통해 액세스할 수 있습니다.

```kotlin
@Composable
fun MyApp() {
    MaterialTheme {
        SomeTextLabel("Hello World")
    }
}

@Composable
fun SomeTextLabel(labelText: String) {
    Text(
        text = labelText,
        color = MaterialTheme.colors.primary // color를 직접 받지 않았지만 가져올 수 있음
    )
}
```

`CompositionLocalProvider`를 사용해서 `CompositionLocal`을 가져올 수 있습니다.

```kotlin
@Composable
fun CompositionLocalExample() {
    MaterialTheme { // MaterialTheme sets ContentAlpha.high as default
        Column {
            Text("Uses MaterialTheme's provided alpha")
            CompositionLocalProvider(LocalContentAlpha provides ContentAlpha.medium) {
                Text("Medium value provided for LocalContentAlpha")
                Text("This Text also uses the medium value")
                CompositionLocalProvider(LocalContentAlpha provides ContentAlpha.disabled) {
                    DescendantExample()
                }
            }
        }
    }
}

@Composable
fun DescendantExample() {
    // CompositionLocalProviders also work across composable functions
    Text("This Text uses the disabled alpha now")
}
```

### 직접 CompositionLocal 만들기

- `compositionLocalOf` : 재구성 중에 제공된 값을 변경하면 current 값을 읽는 콘텐츠만 무효화됩니다.
- `staticCompositionLocalOf` : `compositionLocalOf`와 달리 `staticCompositionLocalOf` 읽기는 `Compose`에서 추적하지 않습니다. 값을 변경하면 컴포지션에서 current 값을 읽는 위치만이 아니라 `CompositionLocal`이 제공된 content 람다 전체가 재구성됩니다.

> CompositionLocal에 제공된 값이 변경될 가능성이 거의 없거나 변경되지 않는다면 staticCompositionLocalOf를 사용하여 성능 이점을 얻으세요.

`CompositionLocal` 생성

```kotlin
data class Elevations(val card: Dp = 0.dp, val default: Dp = 0.dp)
val LocalElevations = compositionLocalOf { Elevations() }
```

`CompositionLocal`에 값 제공

```kotlin
// MyActivity.kt file

class MyActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        setContent {
            val elevations = if (isSystemInDarkTheme()) {
                Elevations(card = 1.dp, default = 1.dp)
            } else {
                Elevations(card = 0.dp, default = 0.dp)
            }

            // Bind elevation as the value for LocalElevations
            CompositionLocalProvider(LocalElevations provides elevations) {
                // ... Content goes here ...
                // This part of Composition will see the `elevations` instance
                // when accessing LocalElevations.current
            }
        }
    }
}
```

`CompositionLocal` 사용

```kotlin
@Composable
fun SomeComposable() {
    // Access the globally defined LocalElevations variable to get the
    // current Elevations in this part of the Composition
    Card(elevation = LocalElevations.current.card) {
        // Content
    }
}
```