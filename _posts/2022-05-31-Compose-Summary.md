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
이렇게 수동으로 뷰를 조작할 경우 복잡도가 올라가서 오류가 발생할 가능성이 커지고 그로 인해 유지 관리가 힘들어지게 됩니다.

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
- `재구성`은 `낙관적`이라 변경되었을 수 있다고 생각할때마다 재구성이 시작됩니다.
- `구성 가능한 함수`는 `순서에 상관없이` 실행될 수 있습니다.
- `구성 가능한 함수`는 `동시에` 실행될 수 있습니다.
- `구성 가능한 함수`는 `매우 자주` 실행될 수 있습니다.
- `구성 가능한 함수`는 `빨라야` 합니다.

---

## 상태 및 컴포지션

`Compose`를 업데이트 하기 위해선 새 인수로 동일한 컴포저블을 호출됩니다. 이러한 인수는 UI의 상태를 표현하고 상태가 업데이트될 때마다 재구성이 발생합니다.

`remember` : 구성 가능한 함수는 `remember`를 이용하여 아래와 같이 상태를 저장할 수 있습니다. 

```kotlin
val mutableState = remember { mutableStateOf(default) }
var value by remember { mutableStateOf(default) }
val (value, setValue) = remember { mutableStateOf(default) }
```

> `remember`가 재구성 과정 전체에서 상태를 유지하는데 도움은 주지만 구성 변경 전반에서는 상태가 유지되지 않기 때문에 이 경우에는 `rememberSaveable`을
> 사용해야 합니다. 이를 이용하면 `Bundle`에 저장할 수 있는 모든 값을 자동으로 저장합니다.

`Observable`한 데이터 타입을 이용해서 `Compose`에서 사용가능하게 하기 위해서는 `State<T>` 또는 `MutableState<T>`로 변환해야 하는데
아래 세 가지 유형에서 `State<T>`로 변환할 수 있는 함수를 가지고 있습니다.

- LiveData
- Flow
- RxJava2

> `Compose`는 `State<T>` 객체를 읽어오면서 자동으로 재구성합니다. 그렇기 때문에 `LiveData`의 관찰을 사용해서 `Compose`를 구성할 경우
> `LiveData<T>.observeAsState()` 같은 구성 가능한 확장 함수를 사용하여 `State<T>`로 변환해야 합니다.

- `Stateful` : `remember`를 사용하여 상태를 가지는 컴포저블입니다.
- `Stateless` : 상태를 갖지 않는 컴포저블로, 보통 `상태 호이스팅`을 사용합니다.

---

## 상태 호이스팅

`Compose`에서 컴포저블을 `Stateless`로 만들기 위한 패턴, 보통 `XXXScreen()`이 내부 위젯의 상태를 갖는 구

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

## 상태 복원

`Activity` 또는 `Process`가 다시 생성된 경우에는 `rememberSaveable`을 사용하여 `UI 상태`를 `복원`합니다.

`Bundle`에 저장되는 모든 데이터 유형은 자동으로 저장됩니다. 아래 방법들을 사용할 수 있습니다.

### Parcelize

간단히 @Parcelize 어노테이션을 통해 번들로 저장할 수 있습니다.

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
- 상태 홀더 : 복잡한 UI 요소 상태 관리 목적 (UI 상태 + UI 로직)
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

컴포지션은 UI를 기술하는 컴포저블의 트리 구조입니다. 컴포지션은 초기 컴포지션을 통해서만 생성되고 `리컴포지션을
통해서만` 업데이트 될 수 있습니다.
> 핵심 사항: 컴포저블의 수명 주기는 컴포지션 시작, 0회 이상 재구성 및 컴포지션 종료 이벤트로 정의됩니다.

![](https://developer.android.com/images/jetpack/compose/lifecycle-composition.png?authuser=1&hl=ko)


아래와 같은 소스에서는 다음과 같이 구성이 됩니다. 
>아래 색상이 다른 요소는 별도의 인스턴스입니다.

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

리컴포지션할때 변화가 있는 경우에만 재구성을 합니다. 아래 경우에도 LoginInput()은 변화가 없기 때문에 기존의 인스턴스를 그대로 사용합니다.

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


만약 특정 식별자를 통해서 변화를 감지하려면 `Key`를 사용합니다.

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

---

### LaunchedEffect

`컴포저블 함수내`에서 `Suspend Function`을 사용하기 위해서는 `LaunchedEffect`를 사용해야 합니다.

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

---

### rememberCoroutineScope

`LaunchedEffect`는 컴포저블 함수내에서만 사용할 수 있기 때문에 밖에서 사용하면서, 컴포지션 종료시에 자동으로 취소되도록 하려면 `rememberCoroutineScope`를
사용해야 합니다.

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
        currentOnTimeout()
    }

    /* Landing screen content */
}
```


---

### DisposableEffect

변화가 일어났을때 직접 `정리`가 필요한 경우에는 `DisposableEffect`를 사용합니다.
`DisposableEffect`dml `키`가 `변경`되면 컴포저블이 현재 효과를 삭제(정리)하고 효과를 다시 호출하여 재설정해야 합니다.

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
        onDispose {
            lifecycleOwner.lifecycle.removeObserver(observer)
        }
    }

    /* Home screen content */
}
```

---

### SideEffect

Compose 상태를 Compose에서 관리하지 않는 객체와 공유하려면 리컴포지션 성공 시마다 호출되는 `SideEffect`를 사용합니다.

```kotlin
@Composable
fun rememberAnalytics(user: User): FirebaseAnalytics {
    val analytics: FirebaseAnalytics = remember {
        /* ... */
    }

    SideEffect {
        analytics.setUserProperty("userType", user.userType)
    }
    return analytics
}
```

---

### produceState

`State`를 해야하는 경우에는 `produceState`를 사용합니다.

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
`snapshotFlow` 블록 내의 State 객체의 변경이 되었을때 새 값이 이전에 내보낸 값과 같지 않은 경우 Flow에서 새 값을 수집기에 내보냅니다

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

1. Composition : Composable 함수를 싱행하여 UI를 만듭니다.
2. Layout : UI를 측정하고 배치합니다.
3. Drawing : UI가 실제 캠퍼스에 그려집니다.