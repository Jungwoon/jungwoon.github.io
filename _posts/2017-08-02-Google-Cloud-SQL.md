---
layout: post
title: "Google Cloud에 SQL 서버 만들기"
image: '/assets/img/'
description: 'Google Cloud에 SQL 서버 만들기'
tags:
- SQL
- Cloud
categories:
- Google Cloud
---

최근에 Node.js를 공부하다가 데이터베이스 연동을 위해서 Local에 MySQL 서버를 만들 일이 있었는데,

차라리 Cloud에 만들어서사용하는게 좋을거 같아서 이용하고 있던 Google Cloud에 서버를 만들어봤습니다.

그 때 내용을 공유하고자 이렇게 포스팅을 합니다.

---

우선 [Google Cloud](https://cloud.google.com) 에 가입을 하고 콘솔로 들어갑니다

![](https://cdn-images-1.medium.com/max/2000/1*5fP_FIq2vOkACj0C-7UU6Q.png)

좌측 메뉴 버튼을 누르고 `SQL`을 누릅니다
 
![](https://cdn-images-1.medium.com/max/1600/1*QykM4y0PRqzPFHlRGFe0Vw.png)
 
그럼 다음과 같은 화면을 볼 수 있는데 여기서 상단에 `인스턴스 생성`을 누릅니다
 
![](https://cdn-images-1.medium.com/max/2000/1*8GSRGTojO3cjbyYdQ0k-vw.png)

데이터베이스 엔진 선택에서 `MySQL`을 선택합니다

![](https://cdn-images-1.medium.com/max/2000/1*l39qCWoBkay4-RoptdnTqw.png)

그 다음 MySQL 세대를 선택합니다 (여기서는 기본인 2세대를 선택합니다)

![](https://cdn-images-1.medium.com/max/1600/1*OSqiv4LvuVM-WMp9M7blYA.png)

인스턴스 ID를 만들고 root 비밀번호를 입력하고, 생성합니다 (여기서는 테스트를 위해서 root로 줬습니다)
 
![](https://cdn-images-1.medium.com/max/1600/1*pm4m0hXNbtm_4Iqt3WWKlA.png)

그럼 다음과 같이 생성이 되는 모습을 확인할 수 있습니다
 
![](https://cdn-images-1.medium.com/max/1200/1*uvJMJIpDEA-dG4guoe843g.png)

생성이 된 후에 해당 인스턴스로 들어가면 다음과 같은 화면을 볼 수 있습니다

외부 접근을 위해 `액세스 제어`에 들어가서 `네트워크 추가`를 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*g5mVzxBzO7cBIXrJkJvPSQ.png)

여기서는 외부의 모든 접근을 허용하기 위해 `0.0.0.0/0` 을 입력하고 완료를 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*dVUNHma8JOC-rpK-S9PIQA.png)

그럼 다음과 같이 네트워크가 추가된 것을 확인할 수 있습니다

![](https://cdn-images-1.medium.com/max/2000/1*eXIH9d8d5ExHK8uEUtZ-iQ.png)

테스트용 데이터베이스를 생성하기 위해서 `데이터베이스` 메뉴로 들어가서 `데이터베이스 만들기`를 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*yzDAn6S7GRXI3gZA8V98UQ.png)

데이터베이스 이름을 넣고 `만들기`를 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*4ztWCA7zhn4e8OKf3r_H2A.png)

그럼 다음과 같이 `test`로 생성된것을 확인할 수 있습니다

![](https://cdn-images-1.medium.com/max/2000/1*ixnnmJhwDIzkV891EFM-hA.png)

`개요` 메뉴에 들어가면 사용량과 개략적인 정보를 확인할 수 있습니다

서버의 IP를 알기 위해 스크롤을 조금 내립니다

![](https://cdn-images-1.medium.com/max/2000/1*6lKNqGMBqYgiNd60JHXnRg.png)

`속성`에 보면 다음과 같이 관련 정보를 확인할 수 있습니다

`35.192.35.0`를 이용해서 외부에서 DB에 접근할 수 있습니다

![](https://cdn-images-1.medium.com/max/2000/1*4oEzI13QivNxbKp0gqF3yg.png)
 
