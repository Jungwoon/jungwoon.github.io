---
layout: post
title: "Google Cloud의 Traslation API 사용해보기"
image: '/assets/img/'
description: 'Google Cloud의 Traslation API 사용해보기'
tags:
- Google Cloud
categories:
- Google Cloud
---


## Google Cloud의 Traslation API 사용해보기

개인적으로 구글에서 가장 많이 쓰는 서비스중에 하나인 구글 번역기 API를 이용하는 방법에 대해서 공부해보려고 합니다.

자세한 부분은 [공식 문서](https://cloud.google.com/translate/docs/)를 확인해주세요 

---

### 지원 언어 (2018.01.03 기준)

| 언어 | 코드
| :--- | :--- 
| 갈라시아어 | gl
| 구자라트어 | gu
| 그리스어 | el
| 네덜란드어 | nl
| 네팔어 | ne
| 노르웨이어 | no
| 덴마크어 | da
| 독일어 | de
| 라오어 | lo
| 라트비아어 | lv
| 라틴어 | la
| 러시아어 | ru
| 루마니아어 | ro
| 룩셈부르크어 | lb
| 리투아니아어 | lt
| 마라티어 | mr
| 마오리어 | mi
| 마케도니아어 | mk
| 말라가시어 | mg
| 말라얄람어 | ml
| 말레이어 | ms
| 몰타어 | mt
| 몽골어 | mn
| 몽어 | hmn
| 미얀마어 | my
| 바스크어 | eu
| 베트남어 | vi
| 벨라루스어 | be
| 벵골어 | bn
| 보스니아어 | bs
| 불가리아어 | bg
| 사모아어 | sm
| 세르비아어 | sr
| 세부아노 | ceb
| 세소토어 | st
| 소말리아어 | so
| 쇼나어 | sn
| 순다어 | su
| 스와힐리어 | sw
| 스웨덴어 | sv
| 스코틀랜드 게일어 | gd
| 스페인어 | es
| 슬로바키아어 | sk
| 슬로베니아어 | sl
| 신디어 | sd
| 신할라어 | si
| 아랍어 | ar
| 아르메니아어 | hy
| 아이슬란드어 | is
| 아이티 크리올어 | ht
| 아제르바이잔어 | az
| 아프리칸스어 | af
| 알바니아어 | sq
| 암하라어 | am
| 에스토니아어 | et
| 에스페란토어 | eo
| 영어 | en
| 요루바어 | yo
| 우르두어 | ur
| 우즈베크어 | uz
| 우크라이나어 | uk
| 웨일즈어 | cy
| 이그보어 | ig
| 이디시어 | yi
| 이탈리아어 | it
| 인도네시아어 | id
| 일본어 | ja
| 자바어 | jw
| 조지아어 | ka
| 줄루어 | zu
| 중국어(간체) | zh
| 중국어(번체) | zh-TW
| 체와어 | ny
| 체코어 | cs
| 카자흐어 | kk
| 카탈로니아어 | ca
| 칸나다어 | kn
| 코르시카어 | co
| 코사어 | xh
| 쿠르드어 | ku
| 크로아티아어 | hr
| 크메르어 | km
| 키르기스어 | ky
| 타갈로그어 | tl
| 타밀어 | ta
| 타지크어 | tg
| 태국어 | th
| 터키어 | tr
| 텔루구어 | te
| 파슈토어 | ps
| 펀자브어 | pa
| 페르시아어 | fa
| 포르투갈어 | pt
| 폴란드어 | pl
| 프랑스어 | fr
| 프리지아어 | fy
| 핀란드어 | fi
| 하와이어 | haw
| 하우사어 | ha
| 한국어 | ko
| 헝가리어 | hu
| 히브리어 | iw
| 힌디어 | hi

---

### 사전 준비

이제 개발을 하기 전에 필요한 사전 설정부분에 대해서 설명 드립니다.

#### Cloud Translation API 사용 허용해주기

먼저 Cloud Translation API의 사용 설정을 해줍니다.

[Google Cloud Console](https://console.cloud.google.com)에서 아래와 같이 `Translation`으로 검색을 해줍니다.

![](https://cdn-images-1.medium.com/max/1600/1*UvFbXAGokpKDY3i1lxcnVg.png)

아래와 같이 나오면 `사용 설정`을 눌러서 API를 Enable 해줍니다.

![](https://cdn-images-1.medium.com/max/1600/1*ZawE3EGcVX3oQaNeKecjBA.png)

#### gcloud sdk 설치 후 인증받기

우선 [Google Cloud SDK(=gcloud) 설치하기](https://jungwoon.github.io/google%20cloud/2017/10/26/install-gcloud/)를
참고하여 gcloud 설치를 먼저 해줍니다


그 다음 `gcloud auth login`을 터미널에 입력을 해줍니다 

![](https://cdn-images-1.medium.com/max/600/1*O1KuxZdZw_KBfmxunh2yWg.png)

그럼 아래와 같이 브라우저가 뜨면서 구글 계정을 선택하는 화면이 나오는데, 해당 프로젝트 계정을 클릭합니다

![](https://cdn-images-1.medium.com/max/2000/1*7RKsET0X_n7kK6kWQ1CYQQ.png)

그럼 다음과 같이 권한 요청이 나오는데, `허용`을 누릅니다

![](https://cdn-images-1.medium.com/max/1600/1*vcKxHlphpBcUkA6BuvzRBg.png)

---

#### 서비스 계정키를 다운 받아서 .bashrc에 등록하기 및 사용설정하기

GCP 서비스를 이용하기 위한 서비스 계정 키를 얻기 위해 [Google Cloud Console](http://console.cloud.google.com)에서
 `메뉴 - API 및 서비스 - 서비스 계정`을 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*4-ZoTgS8yilA_0Gskcg_Yg.png)

아래와 같이 나오면 우측에 메뉴를 누르고 `키 만들기`를 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*VsgBvxgCVepRa4JuxOyo9A.png)

그럼 아래와 같이 `JSON` 또는 `P12`를 선택하라고 하는데 여기서는 JSON을 선택하고 `생성` 버튼을 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*PDkxgzEXHL2Dczop6thgqw.png)

그럼 자동으로 Local로 다운로드 받아지게 됩니다

`(한번밖에 다운로드 되지 않기 때문에 잘 보관해야 합니다)`

![](https://cdn-images-1.medium.com/max/1200/1*9J42lsbpxj98C2lS3MOPtQ.png)

그 다음 `vi ~/.bashrc`를 입력을 합니다. (다른 쉘을 쓰면 거기에 맞는걸 열어줍니다)

![](https://cdn-images-1.medium.com/max/2000/1*QumrcEfuwN2NPo_nnxM_Yw.png)

아래의 105번 라인과 같이 위에서 다운받은 서비스 계정 키의 경로를 `GOOGLE_APPLICATION_CREDENTIALS`에 넣어줍니다

![](https://cdn-images-1.medium.com/max/2000/1*E0fpt06SLamFdXSakReqnQ.png)

```
## Google Cloud
export GOOGLE_CLOUD_SDK_PATH=/Users/jungwoon/google-cloud-sdk
export PATH=$PATH:$GOOGLE_CLOUD_SDK_PATH/bin
export GOOGLE_APPLICATION_CREDENTIALS=/Users/jungwoon/GoogleCredential/bigquerybyjw.json
```

그 다음 터미널에서 `$ gcloud auth application-default login`을 해주어야 합니다.

![](https://cdn-images-1.medium.com/max/800/1*WdGVW0xeXmda4UijQGPSPg.png)

그럼 아래와 같이 나오는데 여기서 `Y`를 눌러서 진행을 합니다.

![](https://cdn-images-1.medium.com/max/1200/1*JP_MMkFHJA0tMuu1PBjfuA.png)

그 다음 브로우저가 열리면서 어떤 계정에 연결을 할건지에 대해서 나오는데, 연결하고자 하는 계정을 클릭합니다.

![](https://cdn-images-1.medium.com/max/1200/1*iG56mP8w2EKHFiVbzqOiOg.png)

마지막으로 아래와 같은 화면에서 허용을 해주어야 사용할 수 있게 됩니다.

![](https://cdn-images-1.medium.com/max/1000/1*DiQZv3P_SrVSn0mGZnFgdw.png)  

---

### 실습

진행은 JAVA로 진행하려고 하고, 먼저 Client Library를 가져오는 방법에 대해서 봐보도록 하겠습니다.

---

#### Maven 설정

```xml
<dependency>
  <groupId>com.google.cloud</groupId>
  <artifactId>google-cloud-translate</artifactId>
  <version>1.14.0</version>
</dependency>
```

---

#### Gradle 설정

```
compile 'com.google.cloud:google-cloud-translate:1.14.0'
```

---

#### 소스

사용방법이 생각보다 간단해서 소스의 주석으로 설명 드리도록 하겠습니다.

소스는 메소드로 만들었습니다.

1. translate(String text, String source, String target) : 번역하고자 하는 텍스트와, 원본언어코드, 바꾸고자 하는 언어 코드를 넣어서 번역결과를 얻습니다.
2. detectLanguage(String text) : 어떤 언어인지 감지하여 언어 코드 반환 예) 영어 -> en 
3. autoDetectTranslate(String text, String target) : 위의 두 메소드의 조합으로 바꾸고자 하는 텍스트와 바꿀 언어코드를 넣으면 번역한 결과를 반환합니다.

```java
import com.google.cloud.translate.Detection;
import com.google.cloud.translate.Translate;
import com.google.cloud.translate.TranslateOptions;
import com.google.cloud.translate.Translation;
import com.google.common.collect.ImmutableList;
import java.util.List;

public class TranslationExample {

    public static void main(String[] args) {
        String text = "This document describes how to use the Google Translation API to list supported languages.";

        System.out.println("Text Origin : " + text); // 원본 텍스트
        System.out.println("Translation : " + autoDetectTranslate(text, "ja")); // 일본어로 번역
    }

    private static Translate getTranslateService() {
        return TranslateOptions.getDefaultInstance().getService();
    }

    private static String translate(String text, String source, String target) {
        Translate translate = getTranslateService();

        Translation translation = translate.translate(text,        // 바꾸고자 하는 텍스트
                Translate.TranslateOption.sourceLanguage(source),  // 원본 언어
                Translate.TranslateOption.targetLanguage(target)); // 번역할 언어

        return translation.getTranslatedText();
    }

    // 어떤 언어인지 감지를 해서 언어 코드를 반환합니다 예) 한국어 -> ko
    private static String detectLanguage(String text) {
        Translate translate = getTranslateService();
        List<Detection> detections = translate.detect(ImmutableList.of(text));

        String sourceLanguage = null;
        for (Detection detection : detections) {
            sourceLanguage = detection.getLanguage();
        }

        return sourceLanguage;
    }

    private static String autoDetectTranslate(String text, String target) {
        return translate(text, detectLanguage(text), target);
    }

}
```

#### 결과

```
Text Origin : This document describes how to use the Google Translation API to list supported languages.
Translation : このドキュメントでは、Google翻訳APIを使用してサポートされている言語を一覧表示する方法について説明します。
```

---

### 마무리

최근 구글 클라우드 API들을 보면 문서가 영어라서 그렇지 막상 하면 사용방법은 엄청 쉬운경우가 대부분이었습니다, 만약 다른 API가 필요하더라도
겁먹지 말고 천천히 공식문서의 샘플을 보면, 왠만한 사용방법은 알 수 있을겁니다. 