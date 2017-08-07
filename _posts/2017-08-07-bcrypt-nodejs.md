---
layout: post
title: "bcrypt를 이용하여 로그인 암호 해싱하기"
image: '/assets/img/'
description: 'bcrypt를 이용하여 로그인 암호 해싱하기'
tags:
- bcrypt
- Node.js
- Authentication
categories:
- Node.js
---

## bcrypt를 이용하여 로그인 암호 해싱하기

### bcrypt

bcrypt는 애초부터 패스워드 저장을 목적으로 설계되었다. Niels Provos와 David Mazières가 1999년 발표했고,
현재까지 사용되는 가장 강력한 해시 메커니즘 중 하나이다.

bcrypt는 보안에 집착하기로 유명한 OpenBSD에서 기본 암호 인증 메커니즘으로 사용되고 있고 미래에 PBKDF2보다 더 경쟁력이 있다고 여겨진다.
bcrypt에서 "work factor" 인자는 하나의 해시 다이제스트를 생성하는 데 얼마만큼의 처리 과정을 수행할지 결정한다.

"work factor"를 조정하는 것만으로 간단하게 시스템의 보안성을 높일 수 있다.
다만 PBKDF2나 scrypt와는 달리 bcrypt는 입력 값으로 72 bytes character를 사용해야 하는 제약이 있다.

> 출처 : d2.naver.com

#### 필요한 사전 설치 패키지

```
$ npm install bcrypt-nodejs --save
```

#### 기본 사용법

```javascript

// 평문 -> 해싱 
bcrypt.hash("keyword", null, null, function(err, hash) {
    // 성공하면 hash로 변환된 코드를 받을 수 있습니다
});

// 비교
bcrypt.compare("keyword", hash, function(err, res) {
    // "keyword"와 hash(해싱된 코드)를 비교하여 같으면 true 아니면 false를 반환합니다
});
```

#### 실제 예제

회원가입

```javascript
var express = require('express');
var router = express.Router();
var path = require('path');
var mysql = require('mysql');
var passport = require('passport');
var LocalStrategy = require('passport-local').Strategy;
var bcrypt = require('bcrypt-nodejs');

// DATABASE SETTING (Google Cloud SQL)
var connection = mysql.createConnection({
    host     : 'SQL DB IP',
    port     : 3306,
    user     : 'id',
    password : 'pw',
    database : 'db name'
});

connection.connect();

// Routing
router.get('/', function(req, res) {
    var msg;
    var errMsg = req.flash('error');

    if (errMsg) msg = errMsg;

    res.render('join.ejs', {'message' : msg});
});

passport.serializeUser(function(user, done) {
    done(null, user.id);
});

passport.deserializeUser(function(id, done) {
    done(null, id);
});

// 성공했을때 리다이렉트 시키는 부분
router.post('/', passport.authenticate('join-local', {
    successRedirect: '/main',
    failureRedirect: '/join',
    failureFlash: true
}));

passport.use('join-local', new LocalStrategy({
        usernameField: 'email',
        passwordField: 'password',
        passReqToCallback: true
    },
    function(req, email, password, done) {

        connection.query('select * from user where email=?', [email], function (err, rows) {
            if (err) { return done(err); }

            if (rows.length) {
                return done(null, false, {message: 'your email is already used'});
            }
            else {
                bcrypt.hash(password, null, null, function(err, hash) {
                    var sql = {email: email, password: hash}; // 입력받은 평문을 hash로 바꿔서 넣어준다
                    connection.query('insert into user set ?', sql, function (err, rows) {
                        if (err) throw err;
                        return done(null, { 'email' : email, 'id' : rows.insertId });
                    });
                });
            }
        })
    }
));

module.exports = router;

```

그럼 다음과 같이 평문으로 설정한 부분이, 해싱이 되어서 DB에 들어가게 된다
![](https://cdn-images-1.medium.com/max/1600/1*dUZWjFY5mmCKQoWL4l3sQA.png) 


로그인

```javascript
var express = require('express'); // express 모듈 사용하기 위함
var app = express();
var router = express.Router();
var path = require('path');
var mysql = require('mysql');
var passport = require('passport');
var LocalStrategy = require('passport-local').Strategy;
var bcrypt = require('bcrypt-nodejs');

// DATABASE SETTING (Google Cloud SQL)
var connection = mysql.createConnection({
    host     : 'SQL DB IP',
    port     : 3306,
    user     : 'id',
    password : 'pw',
    database : 'db name'
});

connection.connect();

// Routing
router.get('/', function(req, res) {
    var msg;
    var errMsg = req.flash('error');

    if (errMsg) msg = errMsg;

    res.render('login.ejs', {'message' : msg}); // login.ejs 호출
});

passport.serializeUser(function(user, done) {
    done(null, user.id);
});

passport.deserializeUser(function(id, done) {
    done(null, id);
});

passport.use('login-local', new LocalStrategy({
        usernameField: 'email',
        passwordField: 'password',
        passReqToCallback: true
    },
    function(req, email, password, done) {
        connection.query('select * from user where email=?', [email], function (err, rows) {

            if (err) return done(err);

            if (rows.length) {
                // 여기서 DB로부터 가져온 해싱된 부분과 입력받은 평문을 비교
                bcrypt.compare(password, rows[0].password, function(err, res) {
                    if (res) {
                        return done(null, { 'email' : email, 'id' : rows[0].uid });
                    }
                    else {
                        return done(null, false, {'message' : 'Your password is incorrect'});
                    }
                });
            }
            else {
                return done(null, false, {'message' : 'Your login info is not found'});
            }
        })
    }
));

router.post('/', function(req, res, next) {
    console.log('login local');
    passport.authenticate('login-local', function(err, user, info) {
        if (err) {
            res.status(500).json(err); // 500 : Server Error
        }

        if (!user) {
            return res.status(401).json(info.message); // 401 : 권한없음
        }

        req.logIn(user, function(err) {
            if (err) return next(err);

            return res.json(user);
        });
    }) (req, res, next);
});


router.get('/kakao', passport.authenticate('login-kakao'));
router.get('/oauth/kakao/callback', passport.authenticate('login-kakao', {
    successRedirect: '/main',
    failureRedirect: '/'
}));

module.exports = router;
```


