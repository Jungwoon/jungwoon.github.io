---
layout: post
title: "Android Decompile 및 ADB로 APK파일 추출하기"
image: '/assets/img/'
description: 'APK 파일을 ADB로 추출하여 디컴파일링 해보기'
tags:
- Android
- Decompile
categories:
- Android
---


# Android Decompile 및 ADB로 APK파일 추출하기

### 준비물
- apk 파일 : 디컴파일할 apk 파일
- dex2jar : dex 파일을 jar 포맷으로 바꿔준다.
- JD-GUI : Java Decompiler

#### apk 파일
adb를 이용해서 설치되어 있는 apk 파일을 추출하는 방법

```
$ adb shell  # ADB Shell 로 진입(안드로이드 기기를 USB로 먼저 연결해야 함)
$ pm list packages -f  # 기기에 설치되어있는 packages 확인
```
![img](https://cdn-images-1.medium.com/max/2000/1*WDsdbpon7BZenOx6maaQwg.png)

list를 보면 아래와 같이 나오는데 아래의 예로 앞의
/system/app/BeamService/BeamService.apk 부분만 복사를 한다.

```
package:/system/app/BeamService/BeamService.apk=com.mobeam.barcodeService
```

그 다음 새로운 터미널을 열고, 로컬의 저장하고자 하는 디렉토리로 이동해서 아래 명령어를
실행하면, 다운이 된다.

```
$ adb pull /system/app/BeamService/BeamService.apk .
```

![img](https://cdn-images-1.medium.com/max/800/1*-9mnuXDpFfEbVQ-wyBEUwQ.png)


#### dex2ja

[https://github.com/pxb1988/dex2jar](https://github.com/pxb1988/dex2jar) 에 들어가서 다운을 받고 압축을 풀어준 뒤에 해당 디렉토리에 들어간뒤에 다음 명령어를 입력합니다.

![img](https://cdn-images-1.medium.com/max/2000/1*R16ApdU75y-FOPEuzrMQ-Q.png)

```
$ sh d2j-dex2jar.sh [APK 파일 경로] [jar파일을 저장할 경로]
$ sh d2j-dex2jar.sh ../BeamService.apk . (예제)
```

만약 다음과 같은 에러가 나오면

```
2j-dex2jar.sh: line 36: ./d2j_invoke.sh: Permission denied
```

해당 디렉토리(dex2jar-2.0)에 들어가서 다음 명령어를 입력합니다.

```
$ chmod a+x *.sh
```

그럼 다음과 같이 jar 파일이 생성이 됩니다.

![img](https://cdn-images-1.medium.com/max/2000/1*sq-xrYarkfw5HKXerRyiag.png)

#### JD-GUI
위에서 생성한 jar 파일을 JD-GUI에 넣어주면 끝
(다운로드는 [http://jd.benow.ca](http://jd.benow.ca) 다음 사이트에서 받습니다.)

다운로드 후 압축을 풀고 실행을 하면 아래와 같은 창이 열리고

![img](https://cdn-images-1.medium.com/max/2000/1*YqrLUa5HNTJ1fZMeBsxGpQ.png)

위에서 생성한 jar파일을 넣고 [File] - [Save All Sources] 를 통해서 압축을 해주면,
디컴파일된 파일이 생성이 됩니다.

![img](https://cdn-images-1.medium.com/max/2000/1*j6Q2B3Rl4W1fPVdpWQdFzQ.png)
