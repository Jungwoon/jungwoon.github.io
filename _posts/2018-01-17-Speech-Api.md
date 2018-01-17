---
layout: post
title: "Google Cloud의 Speech API 사용해보기"
image: '/assets/img/'
description: 'Google Cloud의 Speech API 사용해보기'
tags:
- Google Cloud
categories:
- Google Cloud
---


## Google Cloud의 Speech API 사용해보기

이번에는 구글의 Speech API를 한번 정리해보도록 하겠습니다. 

Speech API는 구글의 머신러닝 기술을 이용하여 음성을 분석해주는 기술입니다.

크게 아래와 같은 기능들을 이용할 수 있습니다.

1. 오디오를 텍스트로 변경
2. 노이즈 켄슬링
3. 스트리밍 제공
4. 전 세계 80가지 이상의 언어와 방언을 인식


자세한 부분은 [공식 문서](https://cloud.google.com/speech/docs/)를 확인해주세요 

---

### 사전 준비

이제 개발을 하기 전에 필요한 사전 설정부분에 대해서 설명 드립니다.

#### Cloud Translation API 사용 허용해주기

먼저 Cloud Translation API의 사용 설정을 해줍니다.

[Google Cloud Console](https://console.cloud.google.com)에서 아래와 같이 `speech`로 검색을 해줍니다.

![](https://cdn-images-1.medium.com/max/2000/1*LNhNcJo8TclXr3lgO4GRzA.png)

아래와 같이 나오면 `사용 설정`을 눌러서 API를 Enable 해줍니다.

![](https://cdn-images-1.medium.com/max/2000/1*mpTQuvjXz5oQ5YSmT8fbVA.png)

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
  <artifactId>google-cloud-speech</artifactId>
  <version>0.32.0-alpha</version>
</dependency>
```

---

#### Gradle 설정

```
compile 'com.google.cloud:google-cloud-speech:0.32.0-alpha'
```

---

### 실습

소스 자체가 크게 어렵지는 않기 때문에, 소스내의 주석을 통해서 설명을 하도록 하겠습니다.

크게 1분 이하의 오디오 파일을 분석하는 소스와 1분 이상의 오디오 파일을 분석하는 소스로 구분하였습니다. 


```java
import com.google.cloud.speech.v1.*;
import com.google.protobuf.ByteString;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.util.List;

public class SpeechApiTest {
    public static void main(String[] args) {
        String filePath = "./audio.raw";

        recognitionSpeech(filePath);
    }
    
    // 1분 미만의 오디오 파일일
    public static void recognitionSpeech(String filePath) {

        try {
            SpeechClient speech = SpeechClient.create(); // Client 생성
            
            // 오디오 파일에 대한 설정부분
            RecognitionConfig config = RecognitionConfig.newBuilder()
                    .setEncoding(RecognitionConfig.AudioEncoding.LINEAR16)
                    .setSampleRateHertz(16000)
                    .setLanguageCode("en-US")
                    .build();

            RecognitionAudio audio = getRecognitionAudio(filePath); // Audio 파일에 대한 RecognitionAudio 인스턴스 생성
            RecognizeResponse response = speech.recognize(config, audio); // 요청에 대한 응답
            List<SpeechRecognitionResult> results = response.getResultsList(); // 응답 결과들

            for (SpeechRecognitionResult result: results) {
                SpeechRecognitionAlternative alternative = result.getAlternativesList().get(0);
                System.out.printf("Transcription: %s%n", alternative.getTranscript());
            }

            speech.close();
        }
        catch (Exception e) {
            e.printStackTrace();
        }
    }

    // Local 이나 Remote이거나 구분해서 RecognitionAudio 만들어 주는 부분
    public static RecognitionAudio getRecognitionAudio(String filePath) throws IOException {
        RecognitionAudio recognitionAudio;

        // 파일이 GCS에 있는 경우
        if (filePath.startsWith("gs://")) {
            recognitionAudio = RecognitionAudio.newBuilder()
                    .setUri(filePath)
                    .build();
        }
        else { // 파일이 로컬에 있는 경우
            Path path = Paths.get(filePath);
            byte[] data = Files.readAllBytes(path);
            ByteString audioBytes = ByteString.copyFrom(data);

            recognitionAudio = RecognitionAudio.newBuilder()
                    .setContent(audioBytes)
                    .build();
        }

        return recognitionAudio;
    }

}

```

---

### 결과

```

Transcription: how old is the Brooklyn Bridge

```

---

### 소스


```java
import com.google.api.gax.longrunning.OperationFuture;
import com.google.cloud.speech.v1.*;
import com.google.protobuf.ByteString;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.util.List;

public class SpeechApiTest {
    public static void main(String[] args) {
        String filePath = "gs://myfiles-byjw/vr.flac";

        longRecognitionSpeech(filePath);
    }

    // 1분 이상의 오디오 파일일때, (** GCS에 올려서 이용해야 함)
    public static void longRecognitionSpeech(String filePath) {

        try {
            SpeechClient speech = SpeechClient.create();


            // 오디오 파일에 대한 설정부분
            RecognitionConfig config = RecognitionConfig.newBuilder()
                    .setEncoding(RecognitionConfig.AudioEncoding.FLAC)
                    .setSampleRateHertz(16000)
                    .setLanguageCode("en-US")
                    .build();

            RecognitionAudio audio = getRecognitionAudio(filePath); // Audio 파일에 대한 RecognitionAudio 인스턴스 생성

            // Non-Blocking 으로 호출을 하며 긴 파일 일때는 LongRunningRecognizeResponse를 사용함
            OperationFuture<LongRunningRecognizeResponse, LongRunningRecognizeMetadata> response =
                    speech.longRunningRecognizeAsync(config, audio);

            while (!response.isDone()) {
                System.out.println("Waiting for response...");
                Thread.sleep(10000);
            }

            List<SpeechRecognitionResult> results = response.get().getResultsList();

            for (SpeechRecognitionResult result: results) {
                SpeechRecognitionAlternative alternative = result.getAlternativesList().get(0);
                System.out.printf("Transcription: %s\n",alternative.getTranscript());
            }
            speech.close();
        }
        catch (Exception e) {
            e.printStackTrace();
        }
    }

    // Local 이나 Remote이거나 구분해서 RecognitionAudio 만들어 주는 부분
    public static RecognitionAudio getRecognitionAudio(String filePath) throws IOException {
        RecognitionAudio recognitionAudio;

        // 파일이 GCS에 있는 경우
        if (filePath.startsWith("gs://")) {
            recognitionAudio = RecognitionAudio.newBuilder()
                    .setUri(filePath)
                    .build();
        }
        else { // 파일이 로컬에 있는 경우
            Path path = Paths.get(filePath);
            byte[] data = Files.readAllBytes(path);
            ByteString audioBytes = ByteString.copyFrom(data);

            recognitionAudio = RecognitionAudio.newBuilder()
                    .setContent(audioBytes)
                    .build();
        }

        return recognitionAudio;
    }

}

```

---

### 결과

```
Waiting for response...
Waiting for response...
Waiting for response...
Waiting for response...
Waiting for response...
Waiting for response...
Transcription: it's okay so what am I doing here why am I here at GDC talking about VR video it's because I believe my favorite games I love games I believe in games my favorite games are the ones that are all about the stories I love narrative game design I love narrative based games and I think that when it comes to telling stories in VR bring together capturing the world with narrative based games and narrative based game design is going to unlock some of the killer apps and killer stories of the medium
Transcription: so I'm really here looking for people who are interested in telling us or two stories that are planning projects around telling those types of stories and I would love to talk to you so if this sounds like your project if you're looking at blending VR video and interactivity to tell a story I want to talk to you I want to help you so if this sounds like you please get in touch please come find me I'll be here all week I have pink hair I work for Google and I would love to talk with you further about VR video interactivity and storytelling
```

