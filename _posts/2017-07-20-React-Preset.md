---
layout: post
title: "React.js를 위한 툴 설치"
image: '/assets/img/'
description: 'React.js를 위한 툴 설치'
tags:
- Web
- React.js
- Tools
categories:
- React.js
---

React.js 툴 설치방법에 대해서 알아보겠습니다.

## React.js를 위한 툴 설치
Udemy에서 제공되는 React.js 강좌에서 사전세팅에 필요한 것들로 browser 환경에서 Node 모듈을 사용할 수 있게끔 함

>  [참고하면 좋은 사이트](http://shiren.github.io/2015-06-23-동적인-자바스크립트-앱을-위한-ES6-사용법/)

### 1. Browserify - [http://browserify.org](http://browserify.org)
browser에서 NPM 모듁을 사용할 수 있도록 도와주는 툴

#### Browserify 설치

```
$ npm install -g browserify
```


### 2. Babel - [https://babeljs.io](https://babeljs.io)
ECMAScript6/JSX 문법으로 작성된 파일을 변환 컴파일 할수 있는 trans-compiler


#### Babel 설치

```
$ npm install --save-dev babel-cli babel-preset-env
```

### 3. Babelify - [https://github.com/babel/babelify](https://github.com/babel/babelify)
Babel과 Browserify를 같이 사용할 수 있게 도와주는 툴


#### Babelify 설치

```
$ npm install --save-dev babelify
```


### 4. babel-preset-react - [https://www.npmjs.com/package/babel-preset-react](https://www.npmjs.com/package/babel-preset-react)
Babel한테 어떻게 JSX를 컴파일 해야 하는지 알려주는 도구


#### babel-preset-react 설치

```
$ npm install --save-dev babel-cli babel-preset-react
```

### 5. watchify - [https://github.com/substack/watchify](https://github.com/substack/watchify)
즉시 컴파일이 될때마다 반영을 해주는 툴


#### watchify 설치

```
$ npm install watchify
```