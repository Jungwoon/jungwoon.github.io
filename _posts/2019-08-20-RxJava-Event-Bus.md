---
layout: post
title: "RxJava로 EventBus 만들기"
image: '/assets/img/'
description: 'RxJava로 Otto 대체하기'
tags:
- Android
categories:
- Android
---

최근에 `카카오`에서 제공하는 `REST API`를 이용하여 `Retrofit2`과 `MVP 패턴`을 이용하여 토이 프로젝트를 했었는데, Fragment 간에 이벤트를 넘겨주기 위해서
처음에 `Square사`의 `Otto(Deprecated)`를 사용하여 만들었다가 최근에 다시 공부했던 `RxJava`를 이용하여 `Otto`를 대체하면서 그 부분에 대한 부분을 
정리해보고자 합니다.

앱은 카카오의 REST API를 통해 키워드를 검색하면 이미지와 비디오의 검색 결과를 보여주는 앱입니다.

자세한 소스는 [KakaoREST](https://github.com/Jungwoon/KakaoREST.git)를 통해서 확인하실 수 있습니다.

![](https://camo.githubusercontent.com/a15e69100573a5bb06d4ee46d798b2b120c842e1/68747470733a2f2f6d69726f2e6d656469756d2e636f6d2f6d61782f323030302f312a3559367864766631384d5634794b4a4979486b5167772e706e67)

---

### PublishSubject 클래스

먼저 `뜨거운 Observable`인 `PublishSubject`를 사용하여 `subscribe`를 호출하지 않아도 데이터가 들어오면 바로바로 발행하도록 해야 하기 때문에
개념부터 먼저 살펴보도록 하겠습니다.


오직 해당 시간에 발생한 데이터를 그대로 구독자에게 전달

![](http://reactivex.io/documentation/operators/images/S.PublishSubject.png)

```kotlin
val subject = PublishSubject.create<String>()
subject.subscribe { data -> println("Subscriber #1 => $data") }
subject.onNext("1")
subject.onNext("2")
subject.subscribe { data -> println("Subscriber #2 => $data") }
subject.onNext("3")
subject.onComplete()

//------------------------------------------
            
결과)

Subscriber #1 => 1
Subscriber #1 => 2
Subscriber #1 => 3
Subscriber #2 => 3

```
---

### 실제 구현

실제 소스에서는 더 많은 부분을 구현했는데, 여기서는 `MainActivity`에서 검색 키워드를 가지고 클릭 이벤트가 발생했을때, `SearchFragment`에서 
이벤트가 발생하여 검색 결과를 보여주는 부분을 가지고 설명하도록 하겠습니다. 

먼저 `싱글톤`으로 `SubjectPublish`를 만들어 다음 차례로 구현합니다. 

1. `싱글톤` 클래스 구현
2. 개별 이벤트별로 `PublishSubject` 생성
3. 위에서 생성한 `PublishSubject`의 데이터를 발행하는 `onNext 함수`를 선언

```kotlin
package com.byjw.jungwoon.util

import com.byjw.jungwoon.util.retrofit.scheme.BaseContent
import io.reactivex.subjects.PublishSubject

object RxEventBus {

    val subjectSearchKeyword = PublishSubject.create<String>() // 키워드를 검색하기 위한 PublishSubject

    fun searchKeyword(keyword: String) {
        subjectSearchKeyword.onNext(keyword)
    }

}
```

그 다음 호출하는 곳은 아래와 같습니다. 해당 이벤트가 발생해야 하는 곳에 `RxEventBus.searchKeyword(it)`와 같이 호출을 합니다.

```kotlin
searchView.setOnQueryTextListener(object : SearchView.OnQueryTextListener {
    override fun onQueryTextSubmit(query: String?): Boolean {
        query?.let {
            RxEventBus.searchKeyword(it) // <- 검색 키워드를 받으면 이 부분에서 키워드를 넘겨줍니다. 
        } ?: Toast.makeText(baseContext, "키워드를 입력해주세요", Toast.LENGTH_SHORT).show()

        return false
    }

    override fun onQueryTextChange(newText: String?): Boolean {
        return false
    }

})
```

이벤트가 처리되어야 하는 쪽에는 `생명주기`에 맞도록 넣어줍니다. 아래 예제는 `Fragment`의 `onCreateView()`에서 `searchPresenter()`를 생성해줬기
때문에, 그 다음인 `onResume()`에서 위의 소스에서 호출했을때, 동작해야할 부분을 구현합니다.

```kotlin
override fun onResume() {
    super.onResume()

    searchPresenter.addDisposable(
        RxEventBus.subjectSearchKeyword.subscribe {
            // 여기서부터 이벤트를 받으면 처리해야 하는 부분
            searchPresenter.clear()
            searchPresenter.addSearchResponseByKeyword(keyword = it, page = 1)
        }
    )

}
```

여기서 `searchPresenter.addDisposable()`를 통해서 Disposable 객체를 넣어줬는데, 그 이유는 `Activity`나 `Fragment`가 비정상적으로
종료되게되면 등록된 `Disposable 객체`가 계속 잡혀 있어서 `Memory Leak`이 발생할 수 있기 때문에 `CompositeDisposable`에 담아서 생명주기가 끝나는
`onDestory()`에서 등록된 `Disposable 객체`들을 해제 해주기 위함입니다.

`MVP Pattern`에 맞도록 명세를 한 `SearchContract`에는 다음과 같이 선언했습니다.

```kotlin
package com.byjw.jungwoon.searchPage

interface SearchContract {

    interface Model {

        ...

    }

    interface View : BaseContract.BaseView {

        ...

    }

    interface Presenter : BaseContract.BasePresenter {

        val compositeDisposable: CompositeDisposable

        fun addDisposable(disposable: Disposable)

        fun dispose()

        ...

    }
}
```

그리고 위의 `SearchContract`를 구현하는 `SearchPresenter`에서는 아래와 같이 만들어놨습니다.

```kotlin
package com.byjw.jungwoon.searchPage.presenter

class SearchPresenter(
    private val model: SearchContract.Model,
    val view: SearchContract.View
) : SearchContract.Presenter, Serializable {

    override val compositeDisposable = CompositeDisposable()

    ...

    override fun addDisposable(disposable: Disposable) {
        compositeDisposable.add(disposable)
    }

    override fun dispose() {
        compositeDisposable.dispose()
    }

}

```

그리고 이를 사용하는 `SearchFragment`의 `onDestroy()` 부분에서 해제합니다.

```kotlin
package com.byjw.jungwoon.searchPage

class SearchFragment : Fragment() {

    private lateinit var searchPresenter: SearchPresenter

    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        val view = inflater.inflate(R.layout.fragment_search, container, false)

        searchPresenter = SearchPresenter(searchModel, searchViewAdapter)

        ...

        return view
    }

    override fun onResume() {
        super.onResume()
        
        // 여기서 EventBus 부분 등록
        searchPresenter.addDisposable(
            RxEventBus.subjectSearchKeyword.subscribe {
                searchPresenter.clear()
                searchPresenter.addSearchResponseByKeyword(keyword = it, page = 1)
            }
        )

        ...

    }

    override fun onDestroy() {
        super.onDestroy()

        searchPresenter.dispose() // 이 onDestroy될때 CompositeDisposble의 등록된 disposable 객체를 해제합니다.
    }

}

```
