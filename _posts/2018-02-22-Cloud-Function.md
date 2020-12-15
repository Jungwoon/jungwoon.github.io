---
layout: post
title: "Google Cloud Function 이용하기"
image: '/assets/img/'
description: 'Cloud Function을 이용해서 이미지 파일들 자동으로 jpg로 변경하기'
tags:
- Google Cloud
categories:
- Google Cloud
---

Google Cloud 중에 별도의 서버 없이 이벤트 처리를 할 수 있는 Cloud Function을 소개하려고 합니다. (* 아직 베타 서비스)

아쉽지만 아직은 Node.js만 지원하고 있습니다. 그래서 사전에 Node.js 설치가 필 수 입니다.

공식 페이지에서 소개하고 있는 기능으로는 아래와 같습니다.

1. 모바일 백엔드 : Firebase를 사용해서 모바일 백엔드가 가능해 집니다
2. Webhook : HTTP 트리거를 통해 이벤트 처리가 가능합니다
3. API 및 초소형 서비스 : 작은 규모의 어플리케이션 개발이 가능합니다
4. IoT : Pub/Sub으로 실시간 데이터를 스트리밍하여 자동으로 실행하고, 데이터를 실시간으로 처리할 수 있습니다.
5. 데이터 처리 / ETL : GCS의 이벤트를 확인하고 처리가 가능합니다.

이 외의 더 자세한 정보를 얻고 싶으면 [공식 문서](https://cloud.google.com/functions/docs/)를 참고하길 바랍니다.

---

### 사전 준비

이제 개발을 하기 전에 필요한 사전 설정부분에 대해서 설명 드립니다.
평소와 다르게 실습을 위해 Firebase와 Node.js도 필요하기 때문에 관련된 부분도 진행 해주어야 합니다

---

#### Cloud Function API 사용 허용해주기

먼저 Cloud Function API의 사용 설정을 해줍니다.

[Google Cloud Console](https://console.cloud.google.com)에서 아래와 같이 `function`로 검색을 해줍니다.

![](https://cdn-images-1.medium.com/max/2000/1*tCdN1oeacbjw1EmRxRIIfg.png)

아래와 같이 나오면 `사용 설정`을 눌러서 API를 Enable 해줍니다.

![](https://cdn-images-1.medium.com/max/2000/1*xOQYeVMhOADZVflFfazwmQ.png)

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

### Node.js 설치하기 (Mac 기준)

Firebase가 현재 Node.js만 지원을 하고 있기 때문에 Node.js를 설치해야 합니다.
현재 제가 사용하는게 Mac이기 때문에, Mac기준으로 진행하겠습니다.

Node.js를 설치하기 위해 Homebrew란 Mac용 패키지 관리 프로그램 부터 설치하겠습니다.
그러기 위해서 먼저 아래 사이트로 가겠습니다.

[Homebrew](https://brew.sh/index_ko.html) 

그럼 아래와 같은 화면이 나오는데 `Homebrew 설치하기` 아래에 있는 명령어를 복사합니다.

![](https://cdn-images-1.medium.com/max/2000/1*FjJ7U1PUaZYJ6ZVeu2wrlQ.png)

그 다음 아래 명령어를 터미널을 열어서 붙여넣기 하고 엔터를 누르면 Homebrew가 설치가 됩니다

```bash
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

![](https://cdn-images-1.medium.com/max/2000/1*A7u_jLGSpkWuSjFaVL69Jg.png)


간단한 Homebrew 사용방법에 대해서 알아보도록 하겠습니다.

```bash
$ brew install [패키지 명]   # 필요한 패키지 설치
$ brew uninstall [패키지 명] # 설치한 패키지 삭제
$ brew search [패키지 명]    # 설치하고자 하는 패키지명 검색
$ brew list                # Homebrew로 설치된 리스트 확인
$ brew update              # Homebrew 업데이트
```  

이제 Node.js를 설치해 보도록 하겠습니다. 아래 명령어를 입력하고 엔터를 치면 node 설치가 됩니다.

```bash
$ brew install node
```

![](https://cdn-images-1.medium.com/max/2000/1*0Hg1zCci_quPqfG66sc0Aw.png)


---

### Firebase 에서 프로젝트 만들어 주기

이번 실습에서는 Firebase도 사용해야 하기 때문에, [Firebase Console](https://console.firebase.google.com/)에 들어갑니다

![](https://cdn-images-1.medium.com/max/2000/1*aUsGf1pfLABgnJnumhKhYQ.png)

그 다음 `프로젝트 추가` 버튼을 누르면, 아래와 같은 창이 나오는데, 저는 Google Cloud에 있는 선택하고 `FIREBASE 추가` 버튼을 누릅니다.

(처음이라면 원하는 프로젝트 이름을 넣고 아래 `FIREBASE 추가` 버튼을 누릅니다.)

![](https://cdn-images-1.medium.com/max/2000/1*IaM9cwqlrObRwGcsxwB1hA.png)


보통은 이 화면은 안나오는데, 저는 Google Cloud 프로젝트를 연결했기 때문에 나옵니다, 요금을 확인한 후에 `요금제 확인` 버튼을 누릅니다.

(하지만 Google Cloud Function을 이용하려면 아래 `Blaze 요금제`를 선택해야 합니다.)

![](https://cdn-images-1.medium.com/max/2000/1*-sr6N0QMJhLE0LC4VdmRaA.png)

그럼 아래와 같이 프로젝트가 만들어지면서 다음과 같은 Console 화면이 나옵니다.

![](https://cdn-images-1.medium.com/max/2000/1*Au-QmNIzdN5oiqvXHDNiZQ.png)

---

### Firebase Command툴 설치하기

드디어 여기까지 왔네요 이제 `firebase-tool`을 설치하겠습니다.
아래 명령어를 입력하게 엔터를 눌러 주세요 (`npm은 node 패키지를 관리해주는 툴입니다.`)

npm에 대한 자세한 정보는 예전 포스팅 자료인 [NPM(Node Package Manager)](https://jungwoon.github.io/node.js/2017/08/01/Node-NPM/)를 참고해주세요

(만약 예전에 Node.js를 설치했는데, `sudo` 없이 npm 설치가 안되시는 분들은 [Mac에서 sudo 없이 npm 이용하기](https://jungwoon.github.io/node.js/2017/08/01/NPM-without-sudo-on-mac/)를 참고해주세요)

```bash
$ npm install -g firebase-tools
```

![](https://cdn-images-1.medium.com/max/2000/1*mZ0kt9NFKgOxtBWsq1MieA.png)

그 다음 firebase 계정을 연결해주시 위해서 `firebase login`을 입력해줍니다.

그럼 웹 페이지가 뜨면서 어떤 Google 아이디를 입력할건지 나오는데, 자신이 쓰고자 하는것을 선택해 줍니다.

```bash
$ firebase login
```

![](https://cdn-images-1.medium.com/max/2000/1*BgThoU5E8fGZ0Z6tvuR9wA.png)

---

## 실습

[Cloud Function Sample Github](https://github.com/firebase/functions-samples)에 가면 다양한 샘플들이 있습니다. 그 중에, 이미지를 올리면
자동으로 JPEG 파일로 변경해주는 샘플을 실행해 보도록 하겠습니다.

`예제 소스로 진행을 할거기 때문에, 해당 소스는 맨 마지막에 명시하도록 하겠습니다`

아래 명령어를 터미널에서 입력하여 해당 프로젝트의 `git clone`을 가져옵니다.

```bash
$ git clone https://github.com/firebase/functions-samples.git
```

그 다음 해당 경로로 들어가면 다양한 샘플 프로젝트가 있는 것을 볼 수 있습니다.

![](https://cdn-images-1.medium.com/max/2000/1*YJ_3KnOh8HiCzR75uxRWRg.png)

그 다음 저희가 진행하고자 하는 샘플인 `convert-images`로 이동합니다.

![](https://cdn-images-1.medium.com/max/2000/1*rzkqJCC_p7ODVYXy0wPacw.png)

그 다음 `firebase use --add`를 입력합니다

그럼 아래와 같이 어떤 프로젝트를 쓸건지 물어보는데, 방향키로 선택하고 엔터를 누릅니다.

```bash
$ firebase use --add
```

![](https://cdn-images-1.medium.com/max/2000/1*IjWCK6fZzufSnVVFC_Amgw.png)

그 다음은 `alias`에 관련된걸 물어보는데, 저는 그냥 프로젝트와 동일한 이름을 주도록 하겠습니다.

![](https://cdn-images-1.medium.com/max/2000/1*qt8Bt_GynCoGGmMv17QrzQ.png)

그리고 `functions` 디렉토리에 들어갑니다

그리고 `npm install`을 눌러서 해당 프로젝트에 필요한 패키지들을 설치해줍니다.

```bash
$ npm install
```

![](https://cdn-images-1.medium.com/max/2000/1*hsKQ7Q9v6DYEnp6fBHNliA.png)

다 되었으니, Firebase Hosting에 프로젝트를 올리기 위해 `firebase deploy`를 `functions 디렉토리 안`에서 입력합니다

```bash
$ firebase deploy
``` 

![](https://cdn-images-1.medium.com/max/2000/1*TN0oS8BLGAIjAn3kod60zw.png)

근데 에러가 나네요, `Error` 부분에 보면 `functions` 디렉토리 안에서 `npm i --save firebase-functions@latest`를 해주라고 나오네요
일단 시키는 대로 해보도록 하겠습니다.

```bash
$ npm i --save firebase-functions@latest
```

![](https://cdn-images-1.medium.com/max/2000/1*HCnCfjrGhZmnsPwqYnp9JA.png)

그 다음 다시 `firebase deploy`를 해주면 성공합니다.

```bash
$ firebase deploy
```

![](https://cdn-images-1.medium.com/max/2000/1*mN9PbMcdB5uSrQlD6nuPJQ.png)

---

### 테스트

[Firebase Console](https://console.firebase.google.com)에 들어가서 좌측 메뉴 중 `Functions`를 눌러봅니다 그럼 좀 전에 업데이트한 걸
볼 수 있습니다.

![](https://cdn-images-1.medium.com/max/2000/1*gcCKil1aYnbB0xNu8BObjQ.png)

이제 메뉴에서 `Storage`를 클릭하고, `시작하기`를 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*HKFsCsGmS3oNk94YZubh2Q.png)

그리고 `파일 업로드`를 눌러보겠습니다

![](https://cdn-images-1.medium.com/max/2000/1*VJXeVz4y65HcoXIKvwEvEQ.png)

준비해 뒀던 `png 이미지`를 업로드 하겠습니다. 

![](https://cdn-images-1.medium.com/max/2000/1*uIIXzZhWf1W7JtZzBjv6UA.png)

그럼 아래와 같이 업로드 된걸 확인할 수 있습니다.

![](https://cdn-images-1.medium.com/max/2000/1*R5jpT00hM1vbgMoyFSm1YA.png)

그리고 몇 초 후 `새로고침`을 해보면 `jpg` 파일이 생긴걸 확인할 수 있습니다.

![](https://cdn-images-1.medium.com/max/2000/1*zyStu64t3cgz7ma3J2QwOg.png)

---

### 소스

프로젝트 내부를 보면 실제로는 `index.js`밖에 파일이 없어서 해당 소스를 봐보도록 하겠습니다.

```javascript
'use strict';

const functions = require('firebase-functions');
const mkdirp = require('mkdirp-promise');
const gcs = require('@google-cloud/storage')();
const spawn = require('child-process-promise').spawn;
const path = require('path');
const os = require('os');
const fs = require('fs');

// File extension for the created JPEG files.
const JPEG_EXTENSION = '.jpg';

/**
 * When an image is uploaded in the Storage bucket it is converted to JPEG automatically using
 * ImageMagick.
 */
exports.imageToJPG = functions.storage.object().onChange(event => {
  const object = event.data;
  const filePath = object.name;
  const baseFileName = path.basename(filePath, path.extname(filePath));
  const fileDir = path.dirname(filePath);
  const JPEGFilePath = path.normalize(path.format({dir: fileDir, name: baseFileName, ext: JPEG_EXTENSION}));
  const tempLocalFile = path.join(os.tmpdir(), filePath);
  const tempLocalDir = path.dirname(tempLocalFile);
  const tempLocalJPEGFile = path.join(os.tmpdir(), JPEGFilePath);

  // 타입이 업로드된 파일이 image가 아니면 나갑니다
  if (!object.contentType.startsWith('image/')) {
    console.log('This is not an image.');
    return;
  }

  // 만약 업로드 된 파일이 jpg이면 중단합니다
  if (object.contentType.startsWith('image/jpeg')) {
    console.log('Already a JPEG.');
    return;
  }

  // Exit if this is a move or deletion event.
  if (object.resourceState === 'not_exists') {
    console.log('This is a deletion event.');
    return;
  }

  const bucket = gcs.bucket(object.bucket);

  // 파일이 다운로드 될 임시 디렉토리를 만듭니다.
  return mkdirp(tempLocalDir).then(() => {
    // Download file from bucket.
    return bucket.file(filePath).download({destination: tempLocalFile});
  }).then(() => {
    console.log('The file has been downloaded to', tempLocalFile);
    // ImageMagick을 이용해서 이미지를 JPEG로 바꿉니다
    return spawn('convert', [tempLocalFile, tempLocalJPEGFile]);
  }).then(() => {
    console.log('JPEG image created at', tempLocalJPEGFile);
    // 해당 Storage로 업로드를 합니다
    return bucket.upload(tempLocalJPEGFile, {destination: JPEGFilePath});
  }).then(() => {
    console.log('JPEG image uploaded to Storage at', JPEGFilePath);
    // 이미지 업로드가 완료가 되면 로컬에 저장된 파일을 지웁니다
    fs.unlinkSync(tempLocalJPEGFile);
    fs.unlinkSync(tempLocalFile);
  });
});

```

---

### 번외) Firebase Service Account가 필요할 때

패키지에 따라서 Firebase의 권한을 얻기 위해 아래와 같이 `Service Account Key`가 필요할때가 있는데,
Service Account Key 구하는 방법을 소개하겠습니다

```javascript
const gcs = require('@google-cloud/storage')({keyFilename: '서비스 계정 키.json'});
```

Firebase 콘솔 메뉴 맨 위에 `Project Overview`옆에 `설정` 버튼을 누르고 `프로젝트 설정`을 클릭합니다. 

![](https://cdn-images-1.medium.com/max/2000/1*TwnppZI4eIKigOg45o2MhA.png)

상단의 `서비스 계정`을 누릅니다.

![](https://cdn-images-1.medium.com/max/2000/1*Nz4JSIbfds34i4ETgnN0Xg.png)

그리고 스크롤을 조금 내려서 `새 비공개 키 생성` 버튼을 누릅니다.

![](https://cdn-images-1.medium.com/max/2000/1*5T0X7a7NFRDl8G9OJ6bDcg.png)

그럼 다음과 같은 경고가 나오는데, 여기서 `키 생성`을 누르면 자동으로 키가 다운로드가 됩니다

![](https://cdn-images-1.medium.com/max/2000/1*PtIvW6cJxXsxY9FHeHjAMg.png)

다음과 같이 `*.json` 형태로 파일이 다운로드가 되는데, 해당 키의 이름을 자유롭게 바꿔서 이용하시면 됩니다.

![](https://cdn-images-1.medium.com/max/2000/1*zctvyB-uJtsXCFBVHg-SIw.png)

---

### 마무리

이번에는 API 설명이 아니라 샘플 소스를 이용해서 한번 사용하는 방법을 배웠는데, 정말 무궁 무진하게 사용할 수 있을걸로 보입니다.

위에 소개한 [Github 샘플 페이지](https://github.com/firebase/functions-samples)에 좋은 자료가 많으니 참고해보시면 좋을거 같습니다.