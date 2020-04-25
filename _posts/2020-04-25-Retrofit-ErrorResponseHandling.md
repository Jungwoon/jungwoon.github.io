---
layout: post
title: "Retrofit2에서 Error Message 받아오기"
image: '/assets/img/'
description: 'Error Message Handling for Retrofit2'
tags:
- Retrofit2
categories:
- Android
---


# Retrofit2에서 Error Message 받아오기


최근에 개발을 하다가 서버에서 아래와 같이 특정 에러 메시지를 보내주는데 해당 에러 메시지를 받아서 토스트로 보여주어야 하는 경우가 있었습니다.
이 부분에서 어떻게 `throwable`에서 타입 캐스팅을 해서 가져와야 하는지 삽질을 해서 정리해서 올리고자 합니다.

---

## 예제

아래와 비슷한 느낌으로 보내주는데 아래와 같은 데이터 형태로 오는 경우에 아래 데이터 타입에 맞게 받아와서 `errorMessage` 부분만 토스트로 보여줍니다


```json
{
  "errorCode": "404_00_0_00",
  "errorMessage": "페이지를 찾을 수 없습니다"
}
```

위와같이 서버에서 내려주는 `json`을 이용하여 `data class`를 만들어주었습니다.

```kotlin
import com.google.gson.annotations.SerializedName

data class ErrorResponse(
    @SerializedName("errorCode") val errorCode: Int,
    @SerializedName("errorMessage") val errorMessage: String
)
```

그 다음 실제 throwable을 받아서 throwable에서 메시지를 가져와서 토스트로 보여주는 부분입니다.
위에서 만들어 놓은 ErrorResponse 형태로 타입 캐스팅 하여 반환합니다.

```kotlin
fun getErrorResponse(throwable: Throwable): ErrorResponse? {
    val httpException = throwable as HttpException
    val errorBody = httpException.response()?.errorBody()!!
    
    // 제가 Koin을 쓰고 있어서 Retrofit을 이렇게 사용 했는데, 사용하는 Retorift 객체를 넘겨주면 됩니다.
    val retrofit: Retrofit = GlobalContext.get().koin.get()
    val converter = retrofit.responseBodyConverter<ErrorResponse>(
        ErrorResponse::class.java, ErrorResponse::class.java.annotations)

    return converter.convert(errorBody)
}
```

저는 `RxAndroid`를 써서 다음과 같이 처리를 해주었지만 `Retorift` 통신을 하고 에러가 발생하는 곳에서
`getErrorResponse(throwable).errorMessage`를 던지면 서버에서 내려준 `errorMessage`를 받아올 수 있습니다.

```kotrlin
fun getMyList(myApi: MyApi) {
    addToDisposable(
        myApi.getMyList()
            .subscribeOn(Schedulers.io())
            .observeOn(AndroidSchedulers.mainThread())
            .subscribe(
                {
                    // 성공했을 때
                }, {
                    // 에러가 발생하여 서버에서 내려준 errorMessage를 토스트로 보여주기 위한 부분
                    Toast.makeText(contet, getErrorResponse(it).errorMessage, Toast.LENGTH_LONG).show()
                }
            )
    )
}
```

삽질한 부분)

동일한 `throwable`을 두번 부르면 두번째에 아래와 같은 에러가 발생합니다.

```
End of input at line 1 column 1 ... 
```

발생 상황 - 아래와 같이 동일한 `throwable`을 통해서 두 번 `getErrorResponse()`를 호출하는 경우 첫번째는 가져오는데 두번째 부분에서 에러가 발생합니다.

```kotlin
fun errorHandle(throwable: Throwable) {
    when (getErrorResponse(throwable)?.errorCode) { // 성공
        404_00_0_00 -> Toast.makeText(contet, getErrorResponse(throwable).errorMessage, Toast.LENGTH_LONG).show() // 실패
    }
}
```

수정 코드

```kotlin
fun errorHandle(throwable: Throwable) {
    val errorResponse = getErrorResponse(throwable) // 한번에 받아와서 이걸 가지고 재사용

    when (errorResponse.errorCode) {
        404_00_0_00 -> Toast.makeText(contet, errorResponse.errorMessage, Toast.LENGTH_LONG).show() // 실패
    }
}
```