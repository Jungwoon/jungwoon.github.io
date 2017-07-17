---
layout: post
title: "Node.js와 MySQL 연동시키기"
image: '/assets/img/'
description: 'Node.js와 MySQL 연동시키기'
tags:
- Web
- Node.js
- MySQL
- Database
categories:
- Node.js
---

## Node.js와 MySQL 연동시키기
Node.js와 MySQL 연동하기 위해서 우선 MySQL 서버를 준비

Mac에서는 MAMP를 이용하는게 편리하여 MAMP를 이용하겠습니다.

### MAMP로 MySQL 서버 만들기
[MAMP](https://www.mamp.info/en/) 사이트로 들어가서 MAMP를 다운 받아 설치하여 실행합니다

![](https://cdn-images-1.medium.com/max/2000/1*djTADBup4tzLh8hkeUKf4A.png)

그럼 다음과 같은 화면을 볼 수 있는데, `Start Server` 버튼을 눌러서 실행시킵니다.
(만약, 자동으로 실행이 되지 않으면 `Open WebStart page` 버튼을 누릅니다.)

![](https://cdn-images-1.medium.com/max/2000/1*9NsamIOgD4jLsYH9J6MbQw.png)

그럼 다음과 같이 웹페이지가 실행되면서 서버에 대한 정보를 알 수 있습니다.

![](https://cdn-images-1.medium.com/max/2000/1*MSEQfhVAiKFZTxKlkTFNFw.png)

MAMP의 기본 경로는 아래와 같습니다, 터미널을 열고 아래와 같이 명령어를 입력하여, MySQL 서버로 들어갑니다.

```
$ ./Applications/MAMP/Library/bin/mysql -u root -p
```

그러면 password를 누르라고 하는데, 기본 password인 `root`를 입력합니다, 그럼 프롬프트 모양이 다음과 같이 변하게 됩니다. 여기서 query를 입력합니다.

```
$
  to
mysql>
```
#### Database 만들기

```
mysql> create database jsman; # 예제와 동일
mysql> use jsman; # 해당 database를 사용하겠다는 명령어
```

#### Table 만들기

```
mysql> CREATE TABLE user (
    ->      email varchar(30) not null,
    ->      name varchar(30),
    ->      pw varchar(30)
    -> );
Query OK, 0 rows affected (0.03 sec) # 성공하면 다음과 같은 메시지 확인
```

#### Data 넣어주기

```
mysql> insert into user (email, name, pw) values('byjungwoon@gmail.com', 'byjungwoon', 'byjungwoon');
Query OK, 1 row affected (0.00 sec)
```

#### Data 확인하기

```
mysql> select * from user;
+----------------------+------------+------------+
| email                | name       | pw         |
+----------------------+------------+------------+
| byjungwoon@gmail.com | byjungwoon | byjungwoon |
+----------------------+------------+------------+
1 row in set (0.00 sec)
```

### Node에서 mysql 설치하기
MySQL 서버 구동은 끝났습니다. 프로젝트 디렉토리로 와서, mysql 관련 패키지를 설치를 해줍니다.

```
$ npm install mysql --save
```

### Node.js에서 MySQL 기본 사용법

```javascript
var mysql = require('mysql'); // 선언

var connection = mysql.createConnection({
    host     : 'localhost',
    port     : 3306,
    user     : 'root',
    password : 'root',
    database : 'jsman',
    socketPath : '/Applications/MAMP/tmp/mysql/mysql.sock' // 중요!! MAMP로 해서 소켓 경로를 따로 잡아주어야 함
});

connection.connect(); // MySQL에 연결하기

connection.query('select name from user where email="' + email + '"', function (err, rows) {
      if (err) throw err;
      // email로 조회해서 받은 경우
      if(rows[0]) {
          responseData.result = "ok"; // 성공
          responseData.name = rows[0].name; // 이름 받기
      }
      else {
          responseData.result = "none"; // 실패
          responseData.name = "";
      }

      res.json(responseData); // 비동기라 이 블록 안에서 줘야 함
  })
```

---

### 예제 전체 소스 및 테스트 화면

```javascript
//app.js
var express = require('express'); // express 모듈 사용하기 위함
var app = express(); // express 모듈을 app이라는 변수명으로 사용
var bodyParser = require('body-parser'); // body-parser를 bodyParser란 변수로 사용
var mysql = require('mysql');

var connection = mysql.createConnection({
    host     : 'localhost',
    port     : 3306,
    user     : 'root',
    password : 'root',
    database : 'jsman',
    socketPath : '/Applications/MAMP/tmp/mysql/mysql.sock' // MAMP로 해서 소켓 경로를 따로 잡아주어야 함
});

connection.connect();

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
  var email = (req.body.email);
  var responseData = {};

  connection.query('select name from user where email="' + email + '"', function (err, rows) {
      if (err) throw err;
      if(rows[0]) {
          responseData.result = "ok";
          responseData.name = rows[0].name;
      }
      else {
          responseData.result = "none";
          responseData.name = "";
      }

      res.json(responseData); // 비동기라 이 블록 안에서 줘야 함
  })

});

```

```html
<!-- public/main.html -->
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
<head>
  <meta http-equiv="Content-Type" content="text/html;charset=UTF-8">
  <title>Document</title>
</head>
<body>
  <h1>main page</h1>
  <p>
    Lorem ipsum dolor sit amet,
    consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
  </p>

  <script src="main.js"></script>

</body>
</html>

```

```html
<!-- public/form.html -->
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
<head>
  <meta http-equiv="Content-Type" content="text/html;charset=UTF-8">
  <title>email form</title>
</head>
<body>

  <form action="email_post" method="post">
    email : <input type="text" name="email"><br/>
    <input type="submit">
  </form>

  <button class="ajaxSend">Ajax Send</button>

  <div class="result"></div>

  <script>

    document.querySelector('.ajaxSend').addEventListener('click', function() {
      var inputData = document.forms[0].elements[0].value;
      sendAjax('http://127.0.0.1:3000/ajax_send_email', inputData);
    });

    // Ajax로 전달
    function sendAjax(url, data) {
      var data = {'email' : data};
      data = JSON.stringify(data); // json -> string

      var xmlHttpRequest = new XMLHttpRequest();
      xmlHttpRequest.open('POST', url);
      xmlHttpRequest.setRequestHeader('Content-Type', "application/json");
      xmlHttpRequest.send(data);

      xmlHttpRequest.addEventListener('load', function() {
        var result = JSON.parse(xmlHttpRequest.responseText);
        var resultDiv = document.querySelector(".result");
        if (result.result !== "ok")  resultDiv.innerHTML = "Not Found";
        else resultDiv.innerHTML = result.name;
      });

    }

  </script>

</body>
</html>

```

```javascript
// views/email.ejs

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
<head>
  <meta http-equiv="Content-Type" content="text/html;charset=UTF-8">
  <title>email ejs template</title>
</head>
<body>
  <h1>Welcome !! <%= email %> </h1>

  <p>
    Happy to meet you!!
  </p>
</body>
</html>

```

### 테스트 결과 화면
![](https://cdn-images-1.medium.com/max/1600/1*6259IfYFFHPVrr4veB1JsQ.png)
