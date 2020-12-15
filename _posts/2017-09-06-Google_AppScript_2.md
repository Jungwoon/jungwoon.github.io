---
layout: post
title: "Google AppScript를 이용하여 Firebase Database로부터 데이터 가져오기 #2"
image: '/assets/img/'
description: 'Firebase Database에서 Google Sheets로 데이터 가져오기'
tags:
- Firebase
- App Script
categories:
- Firebase
---

회사내에서 테스트 결과를 매번 산출해서 정리해서 매니저한테 보고를 했어야 했는데 이 과정이 너무 귀찮아서

다음 포스팅 내용을 진행했었습니다 실시간으로 데이터를 읽어와서 매니저한테 Google Sheets만 공유하고

확인하고 싶을때 Google Sheets에서 내용만 확인하게끔 하여 중간에 지속적으로 보고하는 과정을 줄였습니다

> 제가 다니는 회사에서는 사내에서 구글 제품들을 사용하고 있어서 내부적으로 Google Office를 많이 사용하고 있습니다.
그래서 다음과 같이 사내에서 이용할 수 있었습니다


### 사전 세팅

먼저 [Firebase Console](https://console.firebase.google.com) 에 들어가서 해당 프로젝트의 우측 상단의 메뉴를 누르고 설정을 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*i5PQq-cUu2jA_nnuPBh2vQ.png)

그 다음 아래와 같은 화면이 나오면

`서비스 계정 - 데이터베이스 비밀번호`

에 들어가서 하단에 있는 비밀번호를 복사합니다

![](https://cdn-images-1.medium.com/max/2000/1*Gs-KVBOiMlfVkmJhaNt8Qg.png)

파이어베이스 콘솔에 Realtime Database 에 들어가면 아래와 같이 URL정보를 가져올 수 있는데, 이 정보도 필요하니 저장해 둡니다

![](https://cdn-images-1.medium.com/max/2000/1*1NpYaC7g2PVkAetDAhVN6A.png)

### 실습

우선 만들고자 하는 Google Sheet를 만들어서 들어갑니다. 그 다음

`메뉴 - 도구 - 스크립트 편집기`  

를 클릭하여 들어갑니다

![](https://cdn-images-1.medium.com/max/2000/1*Vh5D609mSFy1CYnGJZMg9A.png)

그럼 다음과 같이 나오는데 여기에 Google App Script를 작성하면 됩니다.

(자세한 정보는 [https://developers.google.com/apps-script/](https://developers.google.com/apps-script/)에서 확인하도록 합니다)

![](https://cdn-images-1.medium.com/max/2000/1*PpQDffslxcOy1iirHsRREw.png)

```javascript

function getFirebaseDatabase() {
  var response = UrlFetchApp.fetch(getFirebaseUrl()); // json 형태의 데이터를 받고
  var jsonObject = JSON.parse(response.getContentText()); // JSON Object 로 변환
  var jsonArray = getArrayForJson(jsonObject); // JSON Object -> Array로 변환
  
  var spreadSheets = SpreadsheetApp.getActiveSpreadsheet();
  var sheet = spreadSheets.getSheets()[0]; // 첫번째 시트 가져오기
  
  sheet.clear(); // Clear Sheet
  setTitle(sheet);
  
  for (var i=0; i<jsonArray.length; i++) {
    // 행렬이 1부터 시작하므로 1을 더하고 타이틀이 있어서 1을 더함
    sheet.getRange(i + 2, 1).setValue(jsonArray[i].date);
    sheet.getRange(i + 2, 2).setValue(jsonArray[i].deviceName);
    sheet.getRange(i + 2, 3).setValue(jsonArray[i].versionCode);
    sheet.getRange(i + 2, 4).setValue(jsonArray[i].url);
    sheet.getRange(i + 2, 5).setValue(jsonArray[i].startTime);
    sheet.getRange(i + 2, 6).setValue(jsonArray[i].endTime);
    sheet.getRange(i + 2, 7).setValue(jsonArray[i].endTime - jsonArray[i].startTime);
    sheet.getRange(i + 2, 8).setValue(jsonArray[i].battery);
    sheet.getRange(i + 2, 9).setValue(jsonArray[i].flag);
  }
}

// JSON URL 가져오는 부분
function getFirebaseUrl() {
  var firebase = 'https://nielsenbrowser.firebaseio.com/'; // 파이어베이스 - 데이터베이스 상단에 있는 주소
  var child = 'Test'; // Reference 정보를 넣습니다
  var secret = '123123123123123'; // 아까전에 복사한 데이터베이스 비밀번호를 넣습니다
  
  // return value : https://nielsenbrowser.firebaseio.com/Test.json?auth=123123123123123
  
  return firebase + child + '.json?auth=' + secret;
}

function setTitle(sheet) {
  var titles = [["Date", "Devie Name", "Version", "URL", "Start Time", "End Time", "Time Gap", "Battery", "Flag"]]
  var titleRange = sheet.getRange("A1:I1");
  titleRange.setValues(titles);
  titleRange.setFontWeight("bold"); // Bold
  titleRange.setHorizontalAlignment("center"); // Align Center
}

/*
  JSONArray가 지원하지 않아서, 배열을 만들어서 넣어줘야 함
  그럼 개별 jsonObject 가 하나씩 배열에 들어가게 됨
*/
function getArrayForJson(jsonObject) {
  var array = [];
  
  for (var i in jsonObject) {
    array.push(jsonObject[i]);
  }
  
  return array;
}

```

우선 여기서 포인트는 아래 함수인데, 이를 통해서 URL을 만들게 되면 아래와 같이 JSON 형태로 REST API를 받을 수 있습니다  

결국 이 데이터를 파싱해서 Google Sheets에 넣어주는 겁니다

```javascript
function getFirebaseUrl() {
  var firebase = 'https://nielsenbrowser.firebaseio.com/'; // 파이어베이스 - 데이터베이스 상단에 있는 주소
  var child = 'Test'; // Reference 정보를 넣습니다
  var secret = '123123123123123'; // 아까전에 복사한 데이터베이스 비밀번호를 넣습니다
  
  // return value : https://nielsenbrowser.firebaseio.com/Test.json?auth=123123123123123
  
  return firebase + child + '.json?auth=' + secret;
}
```

![](https://cdn-images-1.medium.com/max/2000/1*cS0U2NnQYJFaQZiIcIT9BQ.png)


이 스크립트를 실행 시키기 위해

`메뉴 - 삽입 - 그림` 을 누릅니다


![](https://cdn-images-1.medium.com/max/1600/1*K1jqwLukBrTUtxLbFiy2ng.png)

그 다음 알맞은 도형을 선택하여 버튼을 만듭니다

![](https://cdn-images-1.medium.com/max/2000/1*luR5G07jzJlaqyvToCqW6g.png)

다음과 같이 만들었으면 우측 상단에 `저장 후 닫기` 를 눌러 나갑니다

![](https://cdn-images-1.medium.com/max/2000/1*l4eb0x9OOUkD2ggQgQ3iJg.png)

그럼 만들어진 버튼이 추가되는데 여기서 우측 상단의

`메뉴 - 스크립트 할당`을 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*wsCxeIDMBwoqzLO69QJijw.png)

그럼 아래와 같은 화면이 나타나는데, 여기다가 App Script에서 만들었던,

Function 이름을 넣은 다음에 확인을 누르면 해당 스크립트가 실행이 됩니다.

![](https://cdn-images-1.medium.com/max/2000/1*XTPpz2JPosy5pdEVCb-qVg.png)


### 마무리

만들었었던 큰 기능들은 첨부한 App Script 소스에 다 있고, 왠만한건 주석도 써놔서, 한번 읽어보고 이해가 안되시면 댓글 남겨주시면 확인 후 답변 드리도록 하겠습니다.

여기서는 Google Sheets와의 연동을 했지만 App Script를 이용하면 거의 모든 구글 서비스들을 연결할 수 있습니다.

자세한 정보는 [https://developers.google.com/apps-script/](https://developers.google.com/apps-script/)에 들어가면 각 서비스별 지원하는 API를 확인할 수 있습니다.