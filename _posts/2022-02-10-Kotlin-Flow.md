---
layout: post
title: "코틀린 Flow"
image: '/assets/img/'
description: 'Kotlin Flow Summary'
tags:
- Kotlin
- Flow
categories:
- Kotlin
---

코틀린의 Flow에 대해서 한번 알아보고자 합니다.

---

## 코틀린 Flow

Flow는 코틀린 내부에서 리액티브 프로그래밍을 지원하기 위한 구현체이며 아래의 3가지로 구성이 됩니다.

1. 생산자(Producer)
   1. 데이터 생성 & 발행
2. 중간 연산자(Intermediary)
   1. 데이터 변환
3. 소비자(Consumer)
   1. 데이터 소비(=처리)

### 생산자(Producer)

데이터를 발행하는 역할을 합니다. Flow를 생성하기 위해서는 `flow { }` 안에서 `emit` 함수를 통해 데이터를 내보낼 수 있습니다.
이를 이용해 비동기로 API를 이용할 수 있지만 아래와 같은 제한사항이 있습니다.

- 순차적입니다. suspend function 을 호출하면 생산자는 정지 함수가 반환될때까지 정지 상태로 유지됩니다.
- flow 빌더 안에서는 생성자가 다른 CoroutineContext의 값을 emit  할 수 없습니다. 사용하려면 `callbackFlow` 를 이용할 수 있습니다.

```kotlin
class NewsRemoteDataSource(
    private val newsApi: NewsApi,
    private val refreshIntervalMs: Long = 5000
) {
    val latestNews: Flow<List<ArticleHeadline>> = flow { // flow 블록 호출
        while(true) {
            val latestNews = newsApi.fetchLatestNews() // API로부터 데이터 가져오기
            emit(latestNews) // API로부터 전달받은 데이터 발행
            delay(refreshIntervalMs) // 딜레이를 가지고 반복
        }
    }
}

// Interface that provides a way to make network requests with suspend functions
interface NewsApi {
    suspend fun fetchLatestNews(): List<ArticleHeadline>
}
```

### 중간 연산자(Intermediary) - Optional

데이터가 흐르는 중간에서 데이터를 변경하거나 필터링 하는 등의 조작을 합니다. 필요없으면 사용하지 않아도 됩니다.
연산자들 종류는 많지만 크게 아래 연산자들을 통해 데이터를 수정합니다.

- map : 데이터 변형
- filter : 데이터 필터링

```kotlin
class NewsRepository(
    private val newsRemoteDataSource: NewsRemoteDataSource,
    private val userData: UserData
) {
    val favoriteLatestNews: Flow<List<ArticleHeadline>> =
        newsRemoteDataSource.latestNews
            .map { news -> news.filter { userData.isFavoriteTopic(it) } }
            .onEach { news -> saveInCache(news) }
}
```

### 소비자(Consumer)

데이터를 소비(=처리)를 합니다. 처리를 위해서는 `collect` 를 이용하여 발행한 데이터를 받아옵니다.

```kotlin
class LatestNewsViewModel(
    private val newsRepository: NewsRepository
) : ViewModel() {

    init {
        viewModelScope.launch {
            newsRepository.favoriteLatestNews.collect { favoriteNews ->
                // 새로 발행된 데이터를 받아서 처리 하는 부분
            }
        }
    }
}
```