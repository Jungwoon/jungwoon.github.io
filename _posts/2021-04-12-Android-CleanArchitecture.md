---
layout: post
title: "안드로이드에서 클린 아키텍처 구현하기"
image: '/assets/img/'
description: 'Implement Clean Architecture on Android'
tags:
- Android
categories:
- Android
---

지난 3월부터 새로운 회사로 이직을 하게 되었고 해당 회사에서는 Clean Architecture를 사용하고 있었습니다. 처음 접하는 Clean Architecture라 해당 부분
학습하면서 정리한 내용입니다.

---

## 클린 아키텍처

클린 아키텍처의 개념은 우리가 잘 알고 있는 Uncle Bob인 로버트.C 마틴의 Clean Architecture에서 시작되었습니다.
책에서는 서버에 대한 내용이라 안드로이드에 맞게끔 변경된 부분으로 정리를 해보고자 합니다.

![](https://miro.medium.com/max/4800/1*qCJKf3qh53dGvo4p4etpwQ.jpeg)
> 로버트 C. 마틴의 클린 아키텍처 구조

![](https://miro.medium.com/max/4800/1*3F7Wg1-TBqkhib7O-hXRIQ.png)
> 레이어간 의존성을 생각했을 때 구조

![](https://miro.medium.com/max/4800/1*4Fz9-oG-KSUzGizIwqoHlQ.png)
> 그래서 내가 이해한 아키텍처 구조 

### 패키지 구조

패키지 구조는 아래와 같이 크게 3개의 Module로 나눠집니다.

1. domain
2. data
3. presentation

![](https://miro.medium.com/max/600/1*KO82aKDvc7UC6mWuvm8x4w.png)

---

### 패키지 의존성

각 레이어마다 모듈을 분리하면 아래와 같이 개별 build.gradle이 생기는데 
여기에 각각에 계층에서 필요한 레이어만 의존성 주입해주면 
Clean Architecture와 한방향으로만 의존성을 가지게 됩니다.

![](https://miro.medium.com/max/4800/1*-FlpFdZpLHkvVfDD9g2tgg.png)

Domain Layer는 의존성을 가지지 않습니다.
![](https://miro.medium.com/max/4800/1*rITsP6CgWOtUBiZkdclh-Q.png)

Data Layer는 Domain에 대한 의존성만 가집니다.
![](https://miro.medium.com/max/4800/1*HBVRdEfd7wFiMZC_AE6fug.png)

Presentation Layer는 Data Layer와 Domain Layer 두개의 의존성을 가집니다
![](https://miro.medium.com/max/4800/1*0LAPF_MwMkx5DBWJcIyz5A.png)

---

### Domain 레이어 (순수 코틀린 or 순수 자바 코드)

![](https://miro.medium.com/max/600/1*E4ap5tk60XOd0NOwNifrUg.png)

- 비즈니스 로직을 포함하며 비즈니스 로직에 필요한 Entity(=Model)와 UseCase와 Repository를 포함합니다.
- UseCase
    - 행동들의 최소 단위
        - ex)
            - 사용자를 가져오는 UseCase
            - Token을 가져오는 UseCase
            - 로그인을 하는 UseCase
        - 장점
            - 전체적인 코드 파악과 의존성이 낮아지므로 유지보수에 용이
        - 단점
            - 클래스의 양이 많아짐
    - 개별 기능 or 비즈니스 로직 단위 (=앱에서 일어날 동작)
    - 보통 한 개의 행동을 담당하고 UseCase의 이름만 보고 이게 무슨 기능을 가졌을지 짐작하고 구분할 수 있어야 합니다. (ex - LoginUseCase)
    - Domain 레이어는 Presentation 레이어와 Data 레이어와 어떤 의존성도 맺지 않고 독립적이어야 합니다.
    - UseCase는 Presentation 레이어의 UI에서 어떠한 이벤트나 동작에 의하여 호출되는 방향으로 설계
    - UseCase 구현은 Data 레이어에서 실제로 어떻게 데이터를 가져올지에 대한 정의는 하지 않고 해당 메서드를 호출하는 방식으로 정의
    - Domain 레이어에서 Repository Interface 정의
    - Usecase 구성시 어떤 데이터베이스를 사용했는지에 대한 고민을 하지 않고 적당한 Domain레이어에서 정의한 Repository를 주입
- Repository
    - Domain 레이어에서 User와 관련된 행동을 Interface로 정의한 UserRepository 입니다.
- Model(=Entity)
    - 프레임워크와 의존성을 가지면 안됩니다.
    - 다른 프로젝트에서도 동일하게 사용할 수 있는 클래스를 작성합니다.

Domain은 안드로이드 프레임워크에 의존성이 없는 순수한 코틀린 또는 자바 코드만을 사용합니다. (어떤 프레임워크에서도 같이 사용할 수 있습니다.)
우선 Game이란 데이터 클래스를 만들어보도록 하겠습니다. 

```kotlin
package com.byjw.domain.model
 
import java.math.BigDecimal
 
data class Game(
    val name: String,
    val genre: String,
    val manufacture: String,
    val imageUrl: String,
    val price: BigDecimal
)
```

그리고 이에대한 비즈니스 로직인 Repository를 선언하도록 하겠습니다. 여기서 선언된 Repository는 data 계층에서 concrete해서 사용합니다.

```kotlin
package com.byjw.domain.repository
 
import com.byjw.domain.model.Game
 
interface GameRepository {
 
    suspend fun getGameInfo(query: String): Game
 
    suspend fun getGameList(): List<Game>
 
}
```

이제 위의 Repository를 이용하여 실제 사용자들이 하는 동작이나 행동인 UseCase를 만들어 보도록 하겠습니다. 이 UseCase는 presenter 계층에서 ViewModel이나 Presenter에 주입하여 사용합니다.

UseCase에는 위에서 만든 repository를 주입하는데 실제 주입되는건 domain에서 만드는 concrete된 repositoryImpl 클래스입니다.

```kotlin
package com.byjw.domain.usecase
 
import com.byjw.domain.model.Game
import com.byjw.domain.repository.GameRepository
 
class GetGameListUseCase(private val repository: GameRepository) {
 
    suspend fun invoke(): List<Game> {
        return repository.getGameList()
    }
}
```

### Data 레이어 (Domain 모듈 포함)

![](https://miro.medium.com/max/600/1*wvhbovor_NYeS_58OqN_uQ.png)

- Domain레이어에서 설계한 Repository 구현체
- Data Model
    - Repository에서 사용하는 DTO
- Repository
    - Local (Room or SharedPreference…)
    - Remote (Server API)
- DataSource
    - Local (Room or SharedPreference…)
        - Remote (Server API)
- Mapper
    - 데이터모델에 의해 가져온 데이터를 Entity에 맞게 바꿔주는 매핑 작업을 하여 UseCase 로 반환
    - Mapper 클래스를 통해 DB로부터 받아온 데이터모델과 UI에 맞는 데이터 모델을 매핑

Model 은 Api 통신을 하거나 DB 통신을 할때, Data Layer에서 사용하는 DTO들을 선언합니다. 여기서는 API 통신을 통해서 받게되는 Response 로 이용하였습니다.

```kotlin
package com.byjw.data.model
 
import com.google.gson.annotations.SerializedName
import java.math.BigDecimal
 
data class GameResponse(
    @SerializedName("name") val name: String,
    @SerializedName("genre") val genre: String,
    @SerializedName("manufacture") val manufacture: String,
    @SerializedName("imageUrl") val imageUrl: String,
    @SerializedName("price") val price: BigDecimal
)
```

여기서는 api 통신을 통해서 가져온 데이터를 위에서 선언한 model에서 가져온다고 해보겠습니다.

```koltin
package com.byjw.data.api
 
import com.byjw.data.model.GameResponse
import retrofit2.http.GET
 
interface GameApi {
 
    @GET("/game")
    suspend fun getGame(): GameResponse
 
    @GET("/games")
    suspend fun getGameList(): List<GameResponse>
 
}
```

이를 이용하여 datasource를 만들어줍니다. interface로 필요한 기능들을 선언하고 이에 api(=retrofit)나 dao(=room)을 주입하여 concrete 하는 클래스를 만들어 사용합니다.

```kotlin
package com.byjw.data.datasource
 
import com.byjw.data.model.GameResponse
 
interface GameRemoteDataSource {
 
    suspend fun getGame(): GameResponse
 
    suspend fun getGameList(): List<GameResponse>
 
}
 
class GameRemoteDataSourceImpl(private val api: GameApi): GameRemoteDataSource {
 
    override suspend fun getGame(): GameResponse {
        return api.getGame()
    }
 
    override suspend fun getGameList(): List<GameResponse> {
        return api.getGameList()
    }
 
}
```

이렇게 만들어진 Datasource를 이용하기 위해서 repository를 만듭니다. 여기서 concrete 하는 repository는 domain에 선언한 repository interface를 구현합니다.

```kotlin
package com.byjw.data.repository
 
import com.byjw.data.datasource.GameRemoteDataSource
import com.byjw.data.mapper.GameMapper
import com.byjw.domain.model.Game
import com.byjw.domain.repository.GameRepository
 
class GameRepositoryImpl(
    private val dataSource: GameRemoteDataSource
): GameRepository {
 
    override suspend fun getGameInfo(query: String): Game {
        // GameMapper를 이용하여 GameResponse -> Game으로 바꿔줍니다.
        return GameMapper.mapperToGame(dataSource.getGame())
    }
 
    override suspend fun getGameList(): List<Game> {
        // GameMapper를 이용하여 GameResponseList -> GameList로 바꿔줍니다.
        return dataSource.getGameList().map {
            GameMapper.mapperToGame(it)
        }
    }
}
```

여기서 주의할 점은 domain에 있는 Game 데이터로 바꿔주기 위한 Mapper를 만들어주어야 하는 겁니다. 서로다른 계층간 데이터를 변경하기 위해 Mapper Class를 만들어줍니다.

```kotlin
package com.byjw.data.mapper
 
import com.byjw.data.model.GameResponse
import com.byjw.domain.model.Game
 
object GameMapper {
 
    fun mapperToGame(gameResponse: GameResponse): Game {
        return Game(
            name = gameResponse.name,
            genre = gameResponse.genre,
            manufacture = gameResponse.manufacture,
            imageUrl = gameResponse.imageUrl,
            price = gameResponse.price
        )
    }
 
    fun mapperToGameResponse(game: Game): GameResponse {
        return  GameResponse(
            name = game.name,
            genre = game.genre,
            manufacture = game.manufacture,
            imageUrl = game.imageUrl,
            price = game.price
        )
    }
 
}
```

### Presentation 레이어 (Domain 모듈, Data 모듈 포함)

![](https://miro.medium.com/max/744/1*eiPKWc-Gth8-DlT22cxkPw.png)

- UI (Activity, Fragment, View..), Presenter 및 ViewModel을 포함
- 화면표시와 사용자 액션(=입력)에 대한 처리 등 UI와 직접적으로 관련된 부분을 담당합니다.
- 내부적으로 프레임워크 의존성을 처리하기 위해 MVP 패턴이나 MVVM 패턴 사용

Presentation Layer에서의 주요 기능은 UI(=Android Framework)와의 상호작용이므로 기존에 많이들 사용하는 MVP 패턴이나 MVVM 패턴을 이용하여 처리할 수 있습니다. 여기서는 AAC의 MVVM 패턴을 적용한 예제를 볼 예정이며, ViewModel에 Domain의 UseCase를 주입하는 형태로 만들어줍니다.

여기서 중요한점은 Domain Layer의 UseCase를 직접 주입해서 비즈니스 모델을 만든다는 점입니다.
(의존성 주입은 DI Tool인 Degger나 Hilt, Koin등으로 주입을 합니다)

```kotlin
package com.byjw.cleanarchitecture.viewmodel
 
import androidx.lifecycle.LiveData
import androidx.lifecycle.MutableLiveData
import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewModelScope
import com.byjw.domain.model.Game
import com.byjw.domain.usecase.GetGameListUseCase
import com.byjw.domain.usecase.GetGameUseCase
import kotlinx.coroutines.launch
 
class GameViewModel(
    private val getGameUseCase: GetGameUseCase,
    private val getGameListUseCase: GetGameListUseCase
) : ViewModel() {
 
    private val _game: MutableLiveData<Game> = MutableLiveData()
    val game: LiveData<Game> = _game
 
    private val _gameList: MutableLiveData<List<Game>> = MutableLiveData()
    val gameList: LiveData<List<Game>> = _gameList
 
 
    fun getGame(query: String) {
        viewModelScope.launch {
            _game.value = getGameUseCase.invoke(query)
        }
    }
 
    fun getGameList() {
        viewModelScope.launch {
            _gameList.value = getGameListUseCase.invoke()
        }
    }
}
```

위에서 만든 viewModel은 Activity에서 주입을 시켜서 사용합니다. 아래 코드에서는
Koin을 이용하여 주입을 시켰고 이를 DataBinding을 사용하여 처리합니다.

여기서부터는 우리가 일반적으로 많이 사용하는 MVP, MVVM, AAC MVVM 등 다양한 디자인 아키텍처를
이용하여 설계를 하면 됩니다.

```kotlin
package com.byjw.cleanarchitecture.view

import android.os.Bundle
import androidx.annotation.LayoutRes
import com.byjw.cleanarchitecture.R
import com.byjw.cleanarchitecture.base.BindingActivity
import com.byjw.cleanarchitecture.databinding.ActivityMainBinding
import org.koin.androidx.viewmodel.ext.android.getViewModel

class MainActivity : BindingActivity<ActivityMainBinding>() {

    @LayoutRes
    override fun getLayoutResId() = R.layout.activity_main

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        binding.viewModel = getViewModel()
        binding.lifecycleOwner = this

    }

}
```

---

### 마무리

처음 클린 아키텍처에 대한 내용을 확인하고 흐름을 따라가는게 힘들었습니다. 일단은 제가 생각했을때는
맨 위쪽에 다이어그램을 확인해보고 이해를 하고자 하면 어느정도 이해가 되실거라 생각합니다. 물론
처음이라 잘못된 부분도 있을텐데 그럼 편하게 댓글 남겨주시면 확인후에 다시 수정하도록 하겠습니다.