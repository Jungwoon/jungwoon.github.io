---
layout: post
title: "Retrofit 으로 파일 업로드 하기"
image: '/assets/img/'
description: 'Retrofit File Upload'
tags:
- Android
- Retrofit
categories:
- Android
- Retrofit
---

Retrofit을 이용해서 파일 업로드 하는 방법에 대해서 알아보도록 하겠습니다. 

---

## 이론

Retrofit을 통해서 파일을 업로드 할때 Multipart를 사용하는데 Multipart에 대해서 간단히 알아보고 실제 코드를 통해 구현 하는 방법에 대해서 알아보도록
하겠습니다.

### Multipart란?

`Multipart`는 HTTP를 통해 `File을 Server로 전송하기 위해` 사용되는 `Content-type` 입니다. HTTP 프로토콜은 크게 `Header`와 `Body`로 구분이 되고
데이터는 Body에 들어가서 전송이 되는데, Body에 들어가는 데이터 타입을 명시해주는게 `Content-type`입니다. 이때 타입으로 지정해주는 형태를 MIME 타입으로
지정해줄 수 있는데, `Multipart(=multipart/form-data)`는 MIME 타입 중 하나입니다.

Multipart 말 그대로 메시지(=파일)를 여러 파트로 나누어서 메세지를 전달하는 방식이라고 이해하면 편할거 같습니다.

---

## 실제 코드

아래는 API 부분입니다. Retrofit에서 Multipart를 사용하기 위해서는 가장 바깥쪽에 `@Multipart` 를 지정하고
앞쪽에 `@Part`를 지정해하고 데이터 타입은 `MultipartBody.Part`로 지정하여야 합니다. 

```java
interface ReviewApi {

    @Multipart // <- 이 부분이 중요 
    @POST("/reviews")
    suspend fun writeReview(
        @Part id: MultipartBody.Part,
        @Part rating: MultipartBody.Part,
        @Part content: MultipartBody.Part,
        @Part file: MultipartBody.Part?,
        @Part type: MultipartBody.Part?
    ): ReviewResponse
    
}
```

아래 코드는 파일이나 데이터를 `MultipartBody`로 변경하는 부분입니다. 매번 호출해서 사용하기 귀찮아서 `object class`를 만들어 재활용하여 사용하였습니다.

```java
import android.net.Uri
import okhttp3.MediaType.Companion.toMediaType
import okhttp3.MultipartBody
import okhttp3.RequestBody.Companion.asRequestBody
import java.io.File

object FormDataUtil {

    fun getBody(key: String, value: Any): MultipartBody.Part {
        return MultipartBody.Part.createFormData(key, value.toString())
    }

    fun getImageBody(key: String, file: File): MultipartBody.Part {
        return MultipartBody.Part.createFormData(
            name = key,
            filename = file.name,
            body = file.asRequestBody("image/*".toMediaType())
        )
    }

    fun getVideoBody(key: String, file: File): MultipartBody.Part {
        return MultipartBody.Part.createFormData(
            name = key,
            filename = file.name,
            body = file.asRequestBody("video/*".toMediaType())
        )
    }
}
```

실제로 api를 호출하여 파일을 업로드 하는 부분입니다.
api에 들어가야 하는 데이터 타입이 `MultipartBody.Part`이기 때문에 `FormDataUtil` 클래스를 이용하여 `데이터를 변환`하여 전달 해주었습니다.

```java
fun writeReview(id: Long, reviewImage: File, reviewType: String) {

    val formId = FormDataUtil.getBody("productId", id)
    val formRating = FormDataUtil.getBody("rating", rating.value)       // 2-way binding 되어 있는 LiveData
    val formContent = FormDataUtil.getBody("content", content.value)    // 2-way binding 되어 있는 LiveData
    val formFile = FormDataUtil.getImageBody("media", reviewImage)
    val formType = FormDataUtil.getImageBody("mediaType", reviewType)

    viewModelScope.launch {
        reviewApi.writeReview(
            formId,
            formRating,
            formContent,
            formFile,
            formType
        ).apply {
            if (this.resultCode == SUCCESS) {
                // Todo 성공했을 때 처리
            }
        }
    }
}
```
