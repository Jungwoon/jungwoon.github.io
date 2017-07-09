---
layout: post
title: "Node.js 내용 정리"
image: '/assets/img/'
description: 'Node.js 내용 정리'
tags:
- Node.js
- Web
categories:
- Node.js
---

## Node.js 정리

### Node 프로젝트 만들기
```
$ mkdir MyProject
$ cd MyProject
$ npm init # 해당 디렉토리를 node로 사용하기 위해 초기화
```

### 모듈 설치 하기

```
$ npm install [모듈명] -[옵션]

(Example)
$ npm install express --save # package.json에 명시하여 패키지에서 사용할 수 있도록 설치
$ npm install express -g # 시스템 전체에 설치를 하여서 언제 어디서든 독립적으로 사용할 수 있도록 설치
```

### 연습 소스

```javascript
var express = require('express');
var app = express();
var bodyParser = require('body-parser');

app.listen(3000, function() {
    console.log("start!! express server port on 3000!!");
});

// 이렇게 하면 public 아래에 있는 부분에 대해서 따로 디렉토리 설정을 하지 않아도 된다.
app.use(express.static('public'));

// express에 아래 모듈을 사용하겠다고 호출하는 부분
app.use(bodyParser.json()); // Json Parser
app.use(bodyParser.urlencoded({extended:true}));

// view engine은 ejs를 사용한다.
// template는 views 디렉토리 안에 들어가게 된다.
app.set('view engine', 'ejs');

// '/'로 들어오면 /public/main.html을 열어준다.
app.get('/', function(req, res) {
    res.sendFile(__dirname + "/public/main.html");
});

// '/main'로 들어오면 /public/main.html을 열어준다.
app.get('/main', function(req, res) {
    res.sendFile(__dirname + "/public/main.html");
});

// email_post는 포맷에 받음
app.post('/email_post', function(req, res) {
  // get : req.param('email')
  // res.send("welcome : " + req.body.email);
  // email.ejs를 여는데, 이 때 email에는 req.body.email의 값을 넣어서 반환한다.
  res.render('email.ejs', {'email' : req.body.email})
});

app.post('/ajax_send_email', function(req, res) {
  var responseData = {'result' : 'ok', 'email' : req.body.email};
  res.json(responseData);
});

```

### 내용 정리

아래와 같이 사용할 모듈명은 require()를 통해서 import를 해준다.
```javascript
require('모듈명');
```

```javascript
var express = require('express'); // express 모듈 사용하기 위함
var app = express(); // express 모듈을 app이라는 변수명으로 사용
var bodyParser = require('body-parser'); // body-parser를 bodyParser란 변수로 사용
```

신호를 기다리고 있다가, 3000번 포트로 요청이 들어오면 아래와 같이 console.log()를 이용하여 열어줌

```javascript
app.listen(3000, function() {
    console.log("start!! express server port on 3000!!");
});
```

아래와 같이 express.static으로 경로를 잡아주면 해당 부분에 대해서 절대 경로를 매번 써주지 않아도 된다.

```javascript
// 이렇게 하면 public 아래에 있는 부분에 대해서 따로 디렉토리 설정을 하지 않아도 된다.
app.use(express.static('public'));
```

다음과 같이 express에서 사용할때는 `use()`를 사용하여 express에서 사용하겠다고 정의를 해준다.

```javascript
// express에 아래 모듈을 사용하겠다고 호출하는 부분
app.use(bodyParser.json()); // Json Parser
app.use(bodyParser.urlencoded({extended:true}));
```

view engine으로 ejs를 사용한다, template는 기본경로로 /views 디렉토리 안에 들어가게 된다.

```javascript
// view engine은 ejs를 사용한다.
// template는 views 디렉토리 안에 들어가게 된다.
app.set('view engine', 'ejs');
```

get으로 요청이 오는 경우에 '/' 가 들어오면 `/public/main.html`을 열어준다.

예를 들어
`http://localhost:3000/` 으로 들어오면 `http://localhost:3000/public/main.html`로 라우팅을 해준다.

```javascript
// '/'로 들어오면 /public/main.html을 열어준다.
app.get('/', function(req, res) {
    res.sendFile(__dirname + "/public/main.html");
});
```

post로 요청이 오는 경우에 '/email_post' 가 들어오면, views에 미리 만들어 옪은 email.ejs로 라우팅을 해주는데, 이때 email이란 값에 `req.body.email` 값을 전달 한다.

```javascript
// email_post는 포맷에 받음
app.post('/email_post', function(req, res) {
  // get : req.param('email')
  // res.send("welcome : " + req.body.email);
  // email.ejs를 여는데, 이 때 email에는 req.body.email의 값을 넣어서 반환한다.
  res.render('email.ejs', {'email' : req.body.email})
});
```

post로 요청이 오는 경우 '/ajax_send_email'가 들어오면 `{'result' : 'ok', 'email' : req.body.email}` 형태로 JSON 형태로 만들어 전달한다.

```javascript
app.post('/ajax_send_email', function(req, res) {
  var responseData = {'result' : 'ok', 'email' : req.body.email};
  res.json(responseData);
});
```
