---
layout: post
title: "Passport를 이용하여 Kakao 인증 개발하기"
image: '/assets/img/'
description: 'Passport를 이용하여 Kakao 인증 개발하기'
tags:
- Passport
- Node.js
- Authentication
- Kakao
categories:
- Node.js
---

Passport를 이용하여 Kakao 인증 개발하기

### passport-kakao

passport기능 중에 다른 계정을 이용하여 인증하는 기능을 제공하는데, 여기서는 Kakao 계정을 통한 인증을 보도록 하겠습니다

전체 소스를 올리지 않아서 관련 부분 확인을 하려면 [Github](https://github.com/Jungwoon/Node.js-Login)에서 확인 가능합니다 


#### 필요한 사전 설치 패키지

```
$ npm install passport
$ npm install passport-kakao --save
```

#### 사전 작업

카카오의 인증 기능을 사용하기 위해서는 [https://developers.kakao.com](https://developers.kakao.com)에서 해당 기능을 사용하겠다고 등록을 해야합니다

![](https://cdn-images-1.medium.com/max/2000/1*K_ETtY8ZjYWyYuZ2i-HDgg.png)

회원 가입을 하고 우측 상단에 계정 이름을 누르고 `내 애플리케이션`를 눌러야 합니다

![](https://cdn-images-1.medium.com/max/1200/1*ob1MYVyhJe6OzbVB15gkXQ.png)

그럼 다음과 같은 화면을 볼 수 있는데, 왼쪽에 `앱 만들기` 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*-jNvtGY3bAkXBvEIpA9ePA.png)

그럼 아래와 같이 나오는데 이름을 넣고 `앱 만들기` 버튼을 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*uKYUV2zeOuq80GHugo-PUA.png)

이런 화면이 나타나면 그냥 `계속 진행`을 누릅니다

![](https://cdn-images-1.medium.com/max/1200/1*SmVTozYbPJelrQjTr0Ckqw.png)

완료가 되면 아래와 같이 나오는데 여기서 나온 Key들이 우리가 사용할 Client Key 입니다. 우리는 이 중에 `REST API 키`를 사용할겁니다 

![](https://cdn-images-1.medium.com/max/2000/1*TI5OrhzrAHAWrQnnAQviEw.png)

좌측의 `설정-일반`을 누른다음에 `플랫폼 추가` 버튼을 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*rUBvG5H-51Kc_H06rqt9SQ.png)

웹을 선택하고 사이트 도메인을 넣고(로컬에서 테스트를 할거기 때문에 여기서는 localhost:3000을 넣었습니다) `추가` 버튼을 누릅니다

![](https://cdn-images-1.medium.com/max/2000/1*yD-bscfWPc2Af2_iu4jzKA.png)

그럼 아래와 같은 화면이 나옵니다

![](https://cdn-images-1.medium.com/max/2000/1*YLqxjMJstlThfHea6IMD3g.png)

이번에는 `설정-사용자 관리`를 눌러서 사용 부분을 `ON` 시키고 저장을 하면 사전 작업이 끝납니다

![](https://cdn-images-1.medium.com/max/2000/1*C5WCTHNCaqA7tIIZXbkIpw.png)


#### 기본 사용법

```javascript

var passport = require('passport');
var KakaoStrategy = require('passport-kakao').Strategy;
 
passport.use(new KakaoStrategy({
    clientID : clientID, // REST API 키를 넣어줍니다
    callbackURL : callbackURL // 개발자 사이트에서 설정한 리다이렉트 주소인 /oauth를 입력합니다
  },
  function(accessToken, refreshToken, profile, done){
    // 사용자의 정보는 profile에 들어있다. 
    User.findOrCreate(..., function(err, user) {
      if (err) { return done(err); }
      done(null, user);
    });
  }
));

```

> 출처 : [https://www.npmjs.com/package/passport-kakao](https://www.npmjs.com/package/passport-kakao)


#### 예제

HTML 소스

```html
<!-- 아래 링크를 누르면 javascript로 넘어갑니다 -->
<a href="http://localhost:3000/login/kakao"> 카카오 계정 </a>
```

Javascript 소스

```javascript
var passport = require('passport');
var KakaoStrategy = require('passport-kakao').Strategy;

// localhost:3000/login/kakao로 들어오면(get으로 들어오면) passport.authenticate를 실행(여기서는 임의로 login-kakao로 이름을 줌)
router.get('/kakao', passport.authenticate('login-kakao'));

// 이름을 login-kakao로 임의로 주었습니다 그래서 /kakao로 들어오면 아래가 실행이 됩니다
passport.use('login-kakao', new KakaoStrategy({
        clientID : 'REST API KEY',
        callbackURL : 'http://localhost:3000/login/oauth' // 카카오 개발자 사이트에서 지정한 리다이렉트 URL 
    },
    function(accessToken, refreshToken, profile, done) {
        console.log(profile);
        return done(null, profile);
    }
));


router.get('/oauth/kakao/callback', passport.authenticate('login-kakao', {
    successRedirect: '/main', // 성공하면 /main으로 가도록
    failureRedirect: '/'
}));

```

