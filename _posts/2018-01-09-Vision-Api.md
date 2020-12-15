---
layout: post
title: "Google Cloud의 Vision API 사용해보기"
image: '/assets/img/'
description: 'Google Cloud의 Vision API 사용해보기'
tags:
- Google Cloud
categories:
- Google Cloud
---

이번에는 구글의 Vision API를 한번 정리해보도록 하겠습니다. 

Vision API는 구글의 머신러닝 기술을 이용하여 이미지를 분석해주는 기술입니다.

크게 아래와 같은 기능들을 이용할 수 있습니다.

1. 사물 탐지 (자동차, 개, 고양이...)
2. 로고 탐지 (구글 로고, 나이키 로고, 벤츠 로고 등등)
3. 텍스트 추출(OCR)
4. 랜드마크 탐지
5. 얼굴 탐지 (표정을 통한 심리 상태까지도)
6. 웹에서 유사 이미지 감지
7. 불건전한 컨텐츠 감지 (성인 컨텐츠나, 폭력적인 컨텐츠등)


자세한 부분은 [공식 문서](https://cloud.google.com/vision/)를 확인해주세요 

---

### 사전 준비

이제 개발을 하기 전에 필요한 사전 설정부분에 대해서 설명 드립니다.

---

#### Cloud Translation API 사용 허용해주기

먼저 Cloud Translation API의 사용 설정을 해줍니다.

[Google Cloud Console](https://console.cloud.google.com)에서 아래와 같이 `cloud vision`로 검색을 해줍니다.

![](https://cdn-images-1.medium.com/max/2000/1*_9NH5YJOl-WrUye8LXMUIA.png)

아래와 같이 나오면 `사용 설정`을 눌러서 API를 Enable 해줍니다.

![](https://cdn-images-1.medium.com/max/1000/1*03tukVRgq2XLj_5AC8xH1w.png)

---

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
  <artifactId>google-cloud-vision</artifactId>
  <version>1.14.0</version>
</dependency>
```

---

#### Gradle 설정

```
compile 'com.google.cloud:google-cloud-vision:1.14.0'
```

---

#### 소스

각각의 기능을 메소드로 구현하고 거기에 대한 소스와 결과를 공유 드리도록 하겠습니다.

우선 공통적으로 로컬의 이미지와 GCS에서 이미지를 각각 가져와서 Image 파일을 만드는 부분인 `getImage(String filePath)`부터
확인하도록 하겠습니다.

```java
private static Image getImage(String filePath) throws IOException {
        Image image;

        if (filePath.startsWith("gs://")) { // GCS에서 이미지를 가져올때 image 생성
            ImageSource imgSource = ImageSource.newBuilder().setGcsImageUri(filePath).build();
            image = Image.newBuilder().setSource(imgSource).build();
        }
        else { // 로컬에서 이미지를 가져올때 image 생성
            ByteString imgBytes = ByteString.readFrom(new FileInputStream(filePath));
            image = Image.newBuilder().setContent(imgBytes).build();
        }

        return image;
    }
```


#### 글자 인식

샘플 이미지

![](https://cdn-images-1.medium.com/max/1400/1*Zbpjad26FqtDu0df5jFSmw.jpeg)

소스

```java
private static void detectText(String filePath) throws Exception {
    List<AnnotateImageRequest> requests = new ArrayList<>();

    Image image = getImage(filePath);

    Feature feature = Feature.newBuilder().setType(Feature.Type.TEXT_DETECTION).build();
    AnnotateImageRequest request = AnnotateImageRequest.newBuilder().addFeatures(feature).setImage(image).build();
    requests.add(request);

    try (ImageAnnotatorClient client = ImageAnnotatorClient.create()) {
        BatchAnnotateImagesResponse response = client.batchAnnotateImages(requests);
        List<AnnotateImageResponse> responses = response.getResponsesList();

        for (AnnotateImageResponse res : responses) {
            if (res.hasError()) {
                System.out.println("Error: " + res.getError().getMessage());
                return;
            }

            // For full list of available annotations, see http://g.co/cloud/vision/docs
            for (EntityAnnotation annotation : res.getTextAnnotationsList()) {
                System.out.println("Text: " + annotation.getDescription());
                System.out.println("Position : " + annotation.getBoundingPoly());
            }
        }
    }
}
```

결과

```
Text: 에그 맥머핀
1900
오직
NAVER
맥모닝
11월 2일 ~ 11월 20일까지 4:00 AM ~ 10:30 AM *일부매장 제외

Position : vertices {
  x: 99
  y: 95
}
vertices {
  x: 731
  y: 95
}
vertices {
  x: 731
  y: 682
}
vertices {
  x: 99
  y: 682
}

Text: 에그
Position : vertices {
  x: 222
  y: 96
}
vertices {
  x: 360
  y: 102
}
vertices {
  x: 357
  y: 174
}
vertices {
  x: 219
  y: 168
}

Text: 맥
Position : vertices {
  x: 374
  y: 96
}
vertices {
  x: 441
  y: 99
}
vertices {
  x: 438
  y: 163
}
vertices {
  x: 371
  y: 160
}

Text: 머핀
Position : vertices {
  x: 441
  y: 95
}
vertices {
  x: 573
  y: 101
}
vertices {
  x: 570
  y: 178
}
vertices {
  x: 438
  y: 172
}

Text: 1900
Position : vertices {
  x: 307
  y: 291
}
vertices {
  x: 570
  y: 280
}
vertices {
  x: 574
  y: 387
}
vertices {
  x: 312
  y: 399
}

Text: 오직
Position : vertices {
  x: 203
  y: 302
}
vertices {
  x: 294
  y: 298
}
vertices {
  x: 296
  y: 345
}
vertices {
  x: 205
  y: 349
}

Text: NAVER
Position : vertices {
  x: 468
  y: 613
}
vertices {
  x: 568
  y: 613
}
vertices {
  x: 568
  y: 632
}
vertices {
  x: 468
  y: 632
}

Text: 맥모닝
Position : vertices {
  x: 656
  y: 609
}
vertices {
  x: 731
  y: 608
}
vertices {
  x: 731
  y: 636
}
vertices {
  x: 656
  y: 637
}

Text: 11
Position : vertices {
  x: 99
  y: 666
}
vertices {
  x: 108
  y: 666
}
vertices {
  x: 108
  y: 679
}
vertices {
  x: 99
  y: 679
}

Text: 월
Position : vertices {
  x: 110
  y: 665
}
vertices {
  x: 123
  y: 665
}
vertices {
  x: 123
  y: 682
}
vertices {
  x: 110
  y: 682
}

Text: 2
Position : vertices {
  x: 129
  y: 665
}
vertices {
  x: 136
  y: 665
}
vertices {
  x: 136
  y: 680
}
vertices {
  x: 129
  y: 680
}

Text: 일
Position : vertices {
  x: 137
  y: 665
}
vertices {
  x: 150
  y: 665
}
vertices {
  x: 150
  y: 678
}
vertices {
  x: 137
  y: 678
}

Text: ~
Position : vertices {
  x: 156
  y: 670
}
vertices {
  x: 167
  y: 670
}
vertices {
  x: 167
  y: 675
}
vertices {
  x: 156
  y: 675
}

Text: 11
Position : vertices {
  x: 173
  y: 665
}
vertices {
  x: 182
  y: 665
}
vertices {
  x: 182
  y: 680
}
vertices {
  x: 173
  y: 680
}

Text: 월
Position : vertices {
  x: 184
  y: 665
}
vertices {
  x: 197
  y: 665
}
vertices {
  x: 197
  y: 682
}
vertices {
  x: 184
  y: 682
}

Text: 20
Position : vertices {
  x: 203
  y: 665
}
vertices {
  x: 220
  y: 665
}
vertices {
  x: 220
  y: 680
}
vertices {
  x: 203
  y: 680
}

Text: 일
Position : vertices {
  x: 221
  y: 665
}
vertices {
  x: 234
  y: 665
}
vertices {
  x: 234
  y: 678
}
vertices {
  x: 221
  y: 678
}

Text: 까지
Position : vertices {
  x: 236
  y: 665
}
vertices {
  x: 263
  y: 665
}
vertices {
  x: 263
  y: 681
}
vertices {
  x: 236
  y: 681
}

Text: 4:00
Position : vertices {
  x: 269
  y: 665
}
vertices {
  x: 305
  y: 665
}
vertices {
  x: 305
  y: 680
}
vertices {
  x: 269
  y: 680
}

Text: AM
Position : vertices {
  x: 311
  y: 666
}
vertices {
  x: 332
  y: 666
}
vertices {
  x: 332
  y: 680
}
vertices {
  x: 311
  y: 680
}

Text: ~
Position : vertices {
  x: 338
  y: 670
}
vertices {
  x: 349
  y: 670
}
vertices {
  x: 349
  y: 675
}
vertices {
  x: 338
  y: 675
}

Text: 10:30
Position : vertices {
  x: 355
  y: 665
}
vertices {
  x: 397
  y: 665
}
vertices {
  x: 397
  y: 680
}
vertices {
  x: 355
  y: 680
}

Text: AM
Position : vertices {
  x: 402
  y: 666
}
vertices {
  x: 423
  y: 666
}
vertices {
  x: 423
  y: 680
}
vertices {
  x: 402
  y: 680
}

Text: *
Position : vertices {
  x: 430
  y: 669
}
vertices {
  x: 435
  y: 669
}
vertices {
  x: 435
  y: 676
}
vertices {
  x: 430
  y: 676
}

Text: 일부
Position : vertices {
  x: 437
  y: 665
}
vertices {
  x: 465
  y: 665
}
vertices {
  x: 465
  y: 681
}
vertices {
  x: 437
  y: 681
}

Text: 매장
Position : vertices {
  x: 466
  y: 665
}
vertices {
  x: 494
  y: 665
}
vertices {
  x: 494
  y: 682
}
vertices {
  x: 466
  y: 682
}

Text: 제외
Position : vertices {
  x: 500
  y: 665
}
vertices {
  x: 527
  y: 665
}
vertices {
  x: 527
  y: 680
}
vertices {
  x: 500
  y: 680
}

```

---

#### 문서 인식

샘플 이미지

![](https://cdn-images-1.medium.com/max/2000/1*fIHBLwyThpHchoF5G548xg.png)

소스

```java
public static void detectDocumentText(String filePath) throws Exception {
    List<AnnotateImageRequest> requests = new ArrayList<>();

    Image image = getImage(filePath);

    Feature feature = Feature.newBuilder().setType(Feature.Type.DOCUMENT_TEXT_DETECTION).build();
    AnnotateImageRequest request = AnnotateImageRequest.newBuilder().addFeatures(feature).setImage(image).build();
    requests.add(request);

    try (ImageAnnotatorClient client = ImageAnnotatorClient.create()) {
        BatchAnnotateImagesResponse response = client.batchAnnotateImages(requests);
        List<AnnotateImageResponse> responses = response.getResponsesList();
        client.close();

        for (AnnotateImageResponse res : responses) {
            if (res.hasError()) {
                System.out.println("Error: " + res.getError().getMessage());
                return;
            }

            // For full list of available annotations, see http://g.co/cloud/vision/docs
            TextAnnotation annotation = res.getFullTextAnnotation();
            for (Page page: annotation.getPagesList()) {
                String pageText = "";

                for (Block block : page.getBlocksList()) {
                    String blockText = "";
                    for (Paragraph para : block.getParagraphsList()) {
                        String paraText = "";
                        for (Word word: para.getWordsList()) {
                            String wordText = "";
                            for (Symbol symbol: word.getSymbolsList()) {
                                wordText = wordText + symbol.getText();
                            }
                            paraText = paraText + wordText;
                        }
                        // Output Example using Paragraph:
                        System.out.println("Paragraph: \n" + paraText);
                        System.out.println("Bounds: \n" + para.getBoundingBox() + "\n");
                        blockText = blockText + paraText;
                    }
                    pageText = pageText + blockText;
                }
            }

            System.out.println(annotation.getText());
        }
    }
}
```

결과

```
Paragraph: 
TouchBarTouchID
Bounds: 
vertices {
  x: 760
  y: 78
}
vertices {
  x: 1182
  y: 78
}
vertices {
  x: 1182
  y: 129
}
vertices {
  x: 760
  y: 129
}


Paragraph: 
Mac을다루는혁신적인방법,
Bounds: 
vertices {
  x: 443
  y: 168
}
vertices {
  x: 1555
  y: 168
}
vertices {
  x: 1555
  y: 271
}
vertices {
  x: 443
  y: 271
}


Paragraph: 
TouchBar는기존에키보드상단을차지하고있었던기능키를대신하여훨씬더다재다능하고강력한기능을선보입니다.당신이하고있는일에맞춰자동으로모습을바꾸며당신이주로쓰는관련도구들을띄워주죠.볼륨이나밝기를조절하는시스템컨트롤,인터랙티브방식의콘텐츠정리및탐색,이모티콘등더스마트한타이핑을가능하게해줍니다.이뿐아니라
Bounds: 
vertices {
  x: 76
  y: 335
}
vertices {
  x: 1901
  y: 335
}
vertices {
  x: 1901
  y: 517
}
vertices {
  x: 76
  y: 517
}


Paragraph: 
이제TouchID가Mac에적용되어순식간에로그인할수도있죠.
Bounds: 
vertices {
  x: 512
  y: 526
}
vertices {
  x: 1461
  y: 526
}
vertices {
  x: 1461
  y: 573
}
vertices {
  x: 512
  y: 573
}


Paragraph: 
LEt|
Bounds: 
vertices {
  x: 926
  y: 788
}
vertices {
  x: 944
  y: 788
}
vertices {
  x: 944
  y: 796
}
vertices {
  x: 926
  y: 796
}


Paragraph: 
FIForCrop|
Bounds: 
vertices {
  x: 977
  y: 788
}
vertices {
  x: 1081
  y: 788
}
vertices {
  x: 1081
  y: 797
}
vertices {
  x: 977
  y: 797
}


Paragraph: 
|purnel
Bounds: 
vertices {
  x: 1556
  y: 788
}
vertices {
  x: 1593
  y: 788
}
vertices {
  x: 1593
  y: 796
}
vertices {
  x: 1556
  y: 796
}


Paragraph: 
|
Bounds: 
vertices {
  x: 1385
  y: 788
}
vertices {
  x: 1387
  y: 788
}
vertices {
  x: 1387
  y: 797
}
vertices {
  x: 1385
  y: 797
}


Paragraph: 
|ADLUEST-GURFHistogram는Lagraps
Bounds: 
vertices {
  x: 1395
  y: 802
}
vertices {
  x: 1480
  y: 802
}
vertices {
  x: 1480
  y: 844
}
vertices {
  x: 1395
  y: 844
}


Paragraph: 
OColorEOBlack&White-ORetouch는당perdayer-EwhiteBalance-ALevels-Curvos-ADefinition-SelectiveColor-NoiseReduction
Bounds: 
vertices {
  x: 1371
  y: 845
}
vertices {
  x: 1488
  y: 846
}
vertices {
  x: 1487
  y: 964
}
vertices {
  x: 1370
  y: 963
}


Paragraph: 
ETFTOVignette
Bounds: 
vertices {
  x: 1381
  y: 966
}
vertices {
  x: 1440
  y: 966
}
vertices {
  x: 1440
  y: 986
}
vertices {
  x: 1381
  y: 986
}


Paragraph: 
MacBookPro
Bounds: 
vertices {
  x: 939
  y: 1141
}
vertices {
  x: 1029
  y: 1141
}
vertices {
  x: 1029
  y: 1156
}
vertices {
  x: 939
  y: 1156
}


Paragraph: 
Adjust
Bounds: 
vertices {
  x: 714
  y: 1231
}
vertices {
  x: 748
  y: 1231
}
vertices {
  x: 748
  y: 1245
}
vertices {
  x: 714
  y: 1245
}


Paragraph: 
Filters
Bounds: 
vertices {
  x: 838
  y: 1232
}
vertices {
  x: 870
  y: 1232
}
vertices {
  x: 870
  y: 1244
}
vertices {
  x: 838
  y: 1244
}


Paragraph: 
〈종6)있
Bounds: 
vertices {
  x: 1184
  y: 1226
}
vertices {
  x: 1353
  y: 1226
}
vertices {
  x: 1353
  y: 1249
}
vertices {
  x: 1184
  y: 1249
}


Paragraph: 
@2
Bounds: 
vertices {
  x: 642
  y: 1282
}
vertices {
  x: 656
  y: 1283
}
vertices {
  x: 654
  y: 1313
}
vertices {
  x: 640
  y: 1312
}


Paragraph: 
#3
Bounds: 
vertices {
  x: 711
  y: 1282
}
vertices {
  x: 726
  y: 1282
}
vertices {
  x: 726
  y: 1311
}
vertices {
  x: 711
  y: 1311
}


Paragraph: 
99당
Bounds: 
vertices {
  x: 799
  y: 1278
}
vertices {
  x: 799
  y: 1322
}
vertices {
  x: 781
  y: 1322
}
vertices {
  x: 781
  y: 1278
}


Paragraph: 
%5
Bounds: 
vertices {
  x: 848
  y: 1283
}
vertices {
  x: 869
  y: 1283
}
vertices {
  x: 869
  y: 1314
}
vertices {
  x: 848
  y: 1314
}


Paragraph: 
delete
Bounds: 
vertices {
  x: 1431
  y: 1302
}
vertices {
  x: 1462
  y: 1302
}
vertices {
  x: 1462
  y: 1317
}
vertices {
  x: 1431
  y: 1317
}


Paragraph: 
{|}|
Bounds: 
vertices {
  x: 1305
  y: 1335
}
vertices {
  x: 1419
  y: 1335
}
vertices {
  x: 1419
  y: 1361
}
vertices {
  x: 1305
  y: 1361
}


Touch Bar Touch ID
Mac을 다루는 혁신적인 방법,
Touch Bar는 기존에 키보드 상단을 차지하고 있었던 기능키를 대신하여 훨씬 더 다재다능하고 강력한 기능을 선보입니다.
당신이 하고 있는 일에 맞춰 자동으로 모습을 바꾸며 당신이 주로 쓰는 관련 도구들을 띄워주죠. 볼륨이나 밝기를 조절하는
시스템 컨트롤, 인터랙티브 방식의 콘텐츠 정리 및 탐색, 이모티콘 등 더 스마트한 타이핑을 가능하게 해줍니다. 이뿐 아니라
이제 Touch ID가 Mac에 적용되어 순식간에 로그인할 수도 있죠.
LEt|
FIFor Crop|
| purnel
| | AD LUEST
- GUR FHistogram
는 Lagraps
O Color
E O Black & White
- O Retouch
는 당perdayer
- E white Balance
- A Levels
- Curvos
- A Definition
-Selective Color
- Noise Reduction
ETFT
O Vignette
MacBook Pro
Adjust
Filters
〈종 6) 있
@ 2
#3
99당
%5
delete
{|}|
```

---

#### 얼굴 인식

샘플 이미지

![](https://cdn-images-1.medium.com/max/600/1*gfCu-V7_12Ds-spfu1wJ2A.jpeg)

소스

```java
public static void detectFaces(String filePath) throws Exception {
    List<AnnotateImageRequest> requests = new ArrayList<>();

    Image image = getImage(filePath);

    Feature feature = Feature.newBuilder().setType(Feature.Type.FACE_DETECTION).build();
    AnnotateImageRequest request =
            AnnotateImageRequest.newBuilder().addFeatures(feature).setImage(image).build();
    requests.add(request);

    try (ImageAnnotatorClient client = ImageAnnotatorClient.create()) {
        BatchAnnotateImagesResponse response = client.batchAnnotateImages(requests);
        List<AnnotateImageResponse> responses = response.getResponsesList();

        for (AnnotateImageResponse res : responses) {
            if (res.hasError()) {
                System.out.println("Error: " + res.getError().getMessage());
                return;
            }

            // For full list of available annotations, see http://g.co/cloud/vision/docs
            for (FaceAnnotation annotation : res.getFaceAnnotationsList()) {
                System.out.println(
                        "anger: " + annotation.getAngerLikelihood() + "\n" +
                        "joy: " + annotation.getJoyLikelihood() + "\n" +
                        "surprise: " + annotation.getSurpriseLikelihood() + "\n" +
                        "position: %s" + annotation.getBoundingPoly() + "\n");
            }
        }
    }
}
```

결과

```
anger: VERY_UNLIKELY
joy: VERY_LIKELY
surprise: VERY_UNLIKELY
position: %svertices {
  x: 249
}
vertices {
  x: 564
}
vertices {
  x: 564
  y: 329
}
vertices {
  x: 249
  y: 329
}
```

---

#### 로고 인식

샘플 이미지 

![](https://cdn-images-1.medium.com/max/2000/1*-3Zbh1LVDeUM-hUgP5p6zQ.png)

소스

```java
public static void detectLogos(String filePath) throws Exception {
    List<AnnotateImageRequest> requests = new ArrayList<>();

    Image image = getImage(filePath);

    Feature feature = Feature.newBuilder().setType(Feature.Type.LOGO_DETECTION).build();
    AnnotateImageRequest request = AnnotateImageRequest.newBuilder().addFeatures(feature).setImage(image).build();
    requests.add(request);

    try (ImageAnnotatorClient client = ImageAnnotatorClient.create()) {
        BatchAnnotateImagesResponse response = client.batchAnnotateImages(requests);
        List<AnnotateImageResponse> responses = response.getResponsesList();

        for (AnnotateImageResponse res : responses) {
            if (res.hasError()) {
                System.out.println("Error: " + res.getError().getMessage());
                return;
            }

            // For full list of available annotations, see http://g.co/cloud/vision/docs
            for (EntityAnnotation annotation : res.getLogoAnnotationsList()) {
                System.out.println(annotation.getDescription());
            }
        }
    }
}
```

결과

```
PlayStation
```

---

#### 라벨 인식

샘플 이미지

![](https://cdn-images-1.medium.com/max/2000/1*XbBCvUdVhwwHUCQhyg3IkQ.jpeg)

소스

```java
public static void detectLabels(String filePath) throws Exception {
    List<AnnotateImageRequest> requests = new ArrayList<>();

    Image image = getImage(filePath);

    Feature feature = Feature.newBuilder().setType(Feature.Type.LABEL_DETECTION).build();
    AnnotateImageRequest request = AnnotateImageRequest.newBuilder().addFeatures(feature).setImage(image).build();
    requests.add(request);

    try (ImageAnnotatorClient client = ImageAnnotatorClient.create()) {
        BatchAnnotateImagesResponse response = client.batchAnnotateImages(requests);
        List<AnnotateImageResponse> responses = response.getResponsesList();

        for (AnnotateImageResponse res : responses) {
            if (res.hasError()) {
                System.out.println("Error: " + res.getError().getMessage());
                return;
            }

            // For full list of available annotations, see http://g.co/cloud/vision/docs
            for (EntityAnnotation annotation : res.getLabelAnnotationsList()) {
                annotation.getAllFields().forEach((k, v) -> System.out.println(k + " : " + v.toString()));
            }
        }
    }
}
```

결과

```
google.cloud.vision.v1.EntityAnnotation.mid : /m/01lxd
google.cloud.vision.v1.EntityAnnotation.description : coast
google.cloud.vision.v1.EntityAnnotation.score : 0.97922075
google.cloud.vision.v1.EntityAnnotation.mid : /m/06npx
google.cloud.vision.v1.EntityAnnotation.description : sea
google.cloud.vision.v1.EntityAnnotation.score : 0.9546406
google.cloud.vision.v1.EntityAnnotation.mid : /m/02c9_23
google.cloud.vision.v1.EntityAnnotation.description : coastal and oceanic landforms
google.cloud.vision.v1.EntityAnnotation.score : 0.95107996
google.cloud.vision.v1.EntityAnnotation.mid : /m/01bqvp
google.cloud.vision.v1.EntityAnnotation.description : sky
google.cloud.vision.v1.EntityAnnotation.score : 0.93153024
google.cloud.vision.v1.EntityAnnotation.mid : /m/018vpr
google.cloud.vision.v1.EntityAnnotation.description : cliff
google.cloud.vision.v1.EntityAnnotation.score : 0.9131947
google.cloud.vision.v1.EntityAnnotation.mid : /m/04gv79h
google.cloud.vision.v1.EntityAnnotation.description : headland
google.cloud.vision.v1.EntityAnnotation.score : 0.90662897
google.cloud.vision.v1.EntityAnnotation.mid : /m/02r3_7_
google.cloud.vision.v1.EntityAnnotation.description : promontory
google.cloud.vision.v1.EntityAnnotation.score : 0.8520773
google.cloud.vision.v1.EntityAnnotation.mid : /m/02fm9k
google.cloud.vision.v1.EntityAnnotation.description : shore
google.cloud.vision.v1.EntityAnnotation.score : 0.8442393
google.cloud.vision.v1.EntityAnnotation.mid : /m/05kq4
google.cloud.vision.v1.EntityAnnotation.description : ocean
google.cloud.vision.v1.EntityAnnotation.score : 0.83395606
google.cloud.vision.v1.EntityAnnotation.mid : /m/080byt0
google.cloud.vision.v1.EntityAnnotation.description : cape
google.cloud.vision.v1.EntityAnnotation.score : 0.81899774
```

---

#### 랜드마크 인식

샘플 이미지

![](https://cdn-images-1.medium.com/max/2000/1*XvLBG_j6JKVoq0fY_ONNvw.jpeg)

소스

```java
public static void detectLandmarks(String filePath) throws Exception {
        List<AnnotateImageRequest> requests = new ArrayList<>();

        Image image = getImage(filePath);

        Feature feature = Feature.newBuilder().setType(Feature.Type.LANDMARK_DETECTION).build();
        AnnotateImageRequest request = AnnotateImageRequest.newBuilder().addFeatures(feature).setImage(image).build();
        requests.add(request);

        try (ImageAnnotatorClient client = ImageAnnotatorClient.create()) {
            BatchAnnotateImagesResponse response = client.batchAnnotateImages(requests);
            List<AnnotateImageResponse> responses = response.getResponsesList();

            for (AnnotateImageResponse res : responses) {
                if (res.hasError()) {
                    System.out.println("Error: " + res.getError().getMessage());
                    return;
                }

                // For full list of available annotations, see http://g.co/cloud/vision/docs
                for (EntityAnnotation annotation : res.getLandmarkAnnotationsList()) {
                    LocationInfo info = annotation.getLocationsList().listIterator().next();
                    System.out.println("Landmark: " + annotation.getDescription() + "\n" + "LatLng: " + info.getLatLng());
                }
            }
        }
    }
```

결과

```
Landmark: Statue of Liberty
LatLng: latitude: 40.689261
longitude: -74.044482
```

---

#### 유사 이미지 감지

샘플 이미지

![](https://cdn-images-1.medium.com/max/2000/1*cpbu2MfFscUX7B3dGEEApA.jpeg)

소스

```java
public static void detectWebDetections(String filePath) throws Exception {
    List<AnnotateImageRequest> requests = new ArrayList<>();

    Image image = getImage(filePath);

    Feature feat = Feature.newBuilder().setType(Feature.Type.WEB_DETECTION).build();
    AnnotateImageRequest request = AnnotateImageRequest.newBuilder().addFeatures(feat).setImage(image).build();
    requests.add(request);

    try (ImageAnnotatorClient client = ImageAnnotatorClient.create()) {
        BatchAnnotateImagesResponse response = client.batchAnnotateImages(requests);
        List<AnnotateImageResponse> responses = response.getResponsesList();

        for (AnnotateImageResponse res : responses) {
            if (res.hasError()) {
                System.out.println("Error: " + res.getError().getMessage());
                return;
            }

            // Search the web for usages of the image. You could use these signals later
            // for user input moderation or linking external references.
            // For a full list of available annotations, see http://g.co/cloud/vision/docs
            WebDetection annotation = res.getWebDetection();
            System.out.println("Entity:Id:Score");
            System.out.println("===============");

            for (WebDetection.WebEntity entity : annotation.getWebEntitiesList()) {
                System.out.println(entity.getDescription() + " : " + entity.getEntityId() + " : "
                        + entity.getScore());
            }

            System.out.println("\nPages with matching images: Score\n==");
            for (WebDetection.WebPage page : annotation.getPagesWithMatchingImagesList()) {
                System.out.println(page.getUrl() + " : " + page.getScore());
            }

            System.out.println("\nPages with partially matching images: Score\n==");
            for (WebDetection.WebImage webImage : annotation.getPartialMatchingImagesList()) {
                System.out.println(webImage.getUrl() + " : " + webImage.getScore());
            }

            System.out.println("\nPages with fully matching images: Score\n==");
            for (WebDetection.WebImage webImage : annotation.getFullMatchingImagesList()) {
                System.out.println(webImage.getUrl() + " : " + webImage.getScore());
            }
        }
    }
}
```

결과

```
Entity:Id:Score
===============
Abbey Road : /m/03qmlw : 2.2396
Abbey Road : /m/03qm8k : 0.9933
The Beatles : /m/07c0j : 0.703
Album : /m/02lx2r : 0.5974
Album cover : /m/067408 : 0.5776
The Beatles : /m/01hg4bh : 0.4968
Paul is dead : /m/0x_52 : 0.4904
Let It Be : /m/01hg45m : 0.3262
McCartney : /m/01j7tc9 : 0.3216
Fifth Beatle : /m/0fw1l : 0.294

Pages with matching images: Score
==
https://www.biography.com/news/beatles-abbey-road-album-cover-anniversary : 0.0
https://en.wikipedia.org/wiki/Abbey_Road : 0.0
http://www.whizzpast.com/cover-story-11-fascinating-facts-about-the-beatles-abbey-road-album-cover/ : 0.0
https://www.beatlesbible.com/1969/08/08/the-abbey-road-cover-photography-session/ : 0.0
http://www.thebeatles.com/album/abbey-road : 0.0
http://turnmeondeadman.com/paul-is-dead-clues-on-abbey-road/ : 0.0
http://teamrock.com/feature/2016-11-06/the-beatles-abbey-road-artwork-paul-mccartney-john-lennon-george-harrison-ringo-starr-conspiracy-theory : 0.0
http://mentalfloss.com/article/84326/why-was-paul-mccartney-barefoot-cover-abbey-road : 0.0
http://www.dailymail.co.uk/news/article-1205310/Pictured-The-Beatles-album-cover-started-decades-long-conspiracy-theory.html : 0.0
http://captaingino.com/the-beatles-abbey-road-album-cover-20-interesting-facts/ : 0.0

Pages with partially matching images: Score
==
http://assets.podomatic.net/ts/2c/dc/e3/alterna/1400x1400_644029.jpg : 0.0
https://www.ara.cat/2010/12/23/cultura/Abbey-Road_395370618_712451_1500x1000.jpg : 0.0
https://media.nbcwashington.com/images/1200*900/abbey+road-640.jpg : 0.0
https://pbs.twimg.com/media/DQSdsfMU8AAyQZy.jpg : 0.0
http://2.bp.blogspot.com/-WmuRiaPBltU/Tz1Gy74bDGI/AAAAAAAAN94/Sr_1k3u1RFM/s1600/COLE_01.jpg : 0.0
http://cfile201.uf.daum.net/attach/20618236504DA3C21458CA : 0.0
http://4.bp.blogspot.com/-Ew2EfuqUoe4/TWVRThFej4I/AAAAAAAAAIw/HXZbb3GTmiU/s1600/AbbeyRoad.jpg : 0.0
http://www.freakingnews.com/pictures/20000/Old-Paul-McCartney-and-Beatles-Crosing-Road-20263.jpg : 0.0
http://www.gereksizgercek.com/wp-content/uploads/2011/07/beatles-.jpg : 0.0
http://1.bp.blogspot.com/-IzajIz1sHss/VQYQyUfNnUI/AAAAAAAArs0/sRffipmQpfo/s1600/Slide85.JPG : 0.0

Pages with fully matching images: Score
==
https://ugc.kn3.net/i/origin/http://i924.photobucket.com/albums/ad81/xPADER/abbeyroadac5.jpg : 0.0
http://2.fimagenes.com/i/2/9/bb/am_79213_5504102_171373.jpg : 0.0
https://lacuriosidadquematoalgato.files.wordpress.com/2012/05/abbeyroad.jpg : 0.0
http://duck_and_duck.zip.net/images/abbey.JPG : 0.0
https://orig00.deviantart.net/1946/f/2007/242/f/2/abbey_rasta_road_by_elfiodor.jpg : 0.0
https://www.ara.cat/2010/12/23/cultura/Abbey-Road_395370618_711967_1024x768.jpg : 0.0
http://1.bp.blogspot.com/-NmVNzJKquiw/UKPkrjmTANI/AAAAAAAACXg/TzU1pZpozuM/s1600/abbey+road1.jpg : 0.0
https://activatemusic.academy/wp-content/uploads/2015/02/The_Beatles__Abbey_Road.jpg : 0.0
https://i.stack.imgur.com/NezRe.jpg : 0.0
http://gildsociety.com/wp-content/uploads/2014/11/Abbey-Road.jpg : 0.0
```