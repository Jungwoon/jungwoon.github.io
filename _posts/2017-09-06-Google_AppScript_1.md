---
layout: post
title: "Google AppScript를 이용하여 Firebase Database로부터 데이터 가져오기 #1"
image: '/assets/img/'
description: 'Android에서 Firebase Database로 데이터 전송하기'
tags:
- Firebase
- Android
categories:
- Firebase
---

요즘 계속 느끼고 있는건데, 구글이 정말 미친거 같습니다 자기네 서비스들끼리, 왠만한건 다 연결할 수 있도록 하고 있습니다

오늘 포스팅할 내용은 Android에서 실시간으로 전송하기 입니다

우선 안드로이드에 Firebase Database를 이용하여 데이터를 실시간으로 전달 하는 부분부터 진행하도록 하겠습니다

### 사전 준비

우선 Firebase Database와 연결해주기 위해서 Android Studio에서

Tools - Firebase

를 클릭합니다

![](https://cdn-images-1.medium.com/max/1600/1*uSEXz-_Z63b5UiAq3fc40g.png)


그럼 다음과 같이 오른쪽에 `Assistant`라고 나오는데 여기서 필요한 Firebase 기능을 클릭하면

정말 손쉽게 연결이 됩니다


![](https://cdn-images-1.medium.com/max/1200/1*JwRTN6IsSqHUam4fI5Lyww.png)

우선 저희 `Realtime Database`가 필요하니 찾아서 아래 `Save and retrieve data`를 클릭합니다


![](https://cdn-images-1.medium.com/max/2000/1*QCPV3Bwy4iiC3UJqN3dojw.png)

그럼 아래와 같이 나오는데 `1번`과 `2번`을 차례로 눌러서 관련 연결 및 설정을 진행합니다 

![](https://cdn-images-1.medium.com/max/1200/1*YVtOOTybg6IQ5i__SjUq9g.png)

`1번`을 누르면 아래와 같이 나오는데 기존의 Firebase 프로젝트를 이용할건지, 아니면 새로 만들건지 정할 수 있습니다

정한 다음 하단에 `Connect to Firebase`를 눌러서 연결을 합니다

![](https://cdn-images-1.medium.com/max/2000/1*Ads5uUchspOAkgOoxYAurA.png)


`2번`을 누르면 다음과 같이 나타나는데 하단에 `Accept Changes` 버튼을 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*AB0dvhMfaL2HCchUfuV7sA.png)

그럼 다음과 같이 Gradle 파일에 Firebase에 필요한 설정이 자동 등록된걸 확인할 수 있습니다

![](https://cdn-images-1.medium.com/max/2000/1*ge-r47nFratrRLQPdh1qdg.png)


[Firebase Console](https://console.firebase.google.com)에 들어가서

Realtime Database에 들어가 `규칙`을 누르고 

다음과 같이 `read`와 `write` 권한을 둘다 `true`로 바꿔줍니다

(이렇게 해야지 데이터가 들어갈 수 있습니다)

![](https://cdn-images-1.medium.com/max/2000/1*GdEQ1k5kQDC3wJP4d_7eTw.png)



### 실습

우선 `DatabaseReference`의 인스턴스를 만듭니다

저 같은 경우는 `생성자`에서 `initFirebaseDatabase()`라는 메서드를 호출해서 관련 초기 설정을 진행하였습니다

![](https://cdn-images-1.medium.com/max/2000/1*R9bKNbGcDaCs_XowkkhXFw.png)

`initFirebaseDatabase()`에는 아래와 같이 

`FirebaseDatabase firebaseDatabase = FirebaseDatabase.getInstance();`

를 이용하여 인스턴스를 만듭니다

그 다음 `getReference("이름")`를 이용하여 databaseReference에 넣어줍니다 

> 여기서 getReference에 들어가는 이름은 Table 같은 개념이라고 생각하면 됩니다
여기서는 Test라는 이름으로 만들어서 Test라는 곳 아래에 데이터들이 들어가게 됩니다


![](https://cdn-images-1.medium.com/max/2000/1*Ym6XYDiVRh8uQ1YUW4_1pw.png)

들어가는 데이터는 다양하게 들어갈 수 있는데, 저같은 경우는 Test라는 Object를 만들어서 생성자를 통해 초기화 하게끔 만들었습니다. 

![](https://cdn-images-1.medium.com/max/2000/1*8xJGZ1Gn3OI-rE8XQFvqGw.png)

데이터 삽입은 `insertFirebaseDatabase()`라는 메서드를 통해서 넣게끔 되어있는데,

이 안에서

`databaseReference.push().setValue(들어갈 데이터);` 를 통해서 데이터를 넣어주게 됩니다

`setValue(들어갈 데이터)`가 호출되기만 해도 실시간으로 Firebase로 데이터가 들어가게 됩니다

---

#### (Tip) 중간에 push()를 하는 이유

Firebase Database는 Key-Value 형태로 들어가는데 동일한 키로 데이터가 들어가면 추가가 아니라 수정이 됩니다.

결론부터 말씀 드리면 데이터 수정이 아닌 추가를 원하면 push()를 넣어줘야 합니다

만약에 push()가 없이 들어가게 되면, 위에서 선언한 Test란 곳 아래에 데이터가 들어가는데

Test
- battery
- deviceName
- endTime
- flag
- startTime
- url
- versionCode

가 들어가는데 다음 데이터가 들어갈때 똑같은 Test 라는 곳 아래에 들어가기 때문에 그 사이에 `난수를 발생` 시키기 위해서 다음과 같이
중간에 `push()`를 넣습니다. `push()`가 들어가면 아래와 같이 바뀌게 됩니다

Test
- sK2sdoei-sdfoIk(=난수) 
    - battery
    - deviceName
    - endTime
    - flag
    - startTime
    - url
    - versionCode

---

![](https://cdn-images-1.medium.com/max/2000/1*6Ka-3s0jNlR73QDBFC4BCQ.png)

여기서는 webView가 Page Loading이 끝날때 호출되는 함수에서 데이터를 집어 넣었습니다

![](https://cdn-images-1.medium.com/max/2000/1*vWg5p7jFT47gmuT46w8ugA.png)

그럼 다음과 같이 Firebase Database에 실시간으로 데이터가 들어오게 됩니다

![](https://cdn-images-1.medium.com/max/1600/1*w-jp3_4mNv-uCSoDQ9be2A.png)
