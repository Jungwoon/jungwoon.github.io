---
layout: post
title: "Mac에서 sudo 없이 npm 이용하기"
image: '/assets/img/'
description: 'Mac에서 sudo 없이 npm 이용하기'
tags:
- NPM
- Node.js
categories:
- Node.js
---

## Mac에서 sudo 없이 npm 이용하기

>Mac에서 보통 패키지 설치할때 [Homebrew](https://brew.sh/index_ko.html)를 이용하는데
Homebrew를 이용하여 npm을 설치하게 되면 모듈 설치시 매번 sudo를 요청합니다.
이게 설치에서만 sudo를 이용하면 괜찮은데 나중에 되면 권한때문에, 예상치 못한 문제들이 발생합니다
아래의 방법을 이용하면 위의 문제를 해결할 수 있습니다

---
### Solution

#### 기존의 Node.js 제거

```
$ sudo rm -rf ~/.npm
$ sudo rm -rf /usr/local/bin/node
$ sudo rm -rf /usr/local/bin/npm
$ sudo rm -rf /usr/local/include/node
$ sudo rm -rf /usr/local/lib/node_modules
$ brew uninstall node
```

#### Node.js 설치

```
$ brew install node --without-npm # node 설치때 npm은 설치 하지 않습니다.
$ echo prefix=~/.npm-packages >> ~/.npmrc
$ curl -L https://www.npmjs.com/install.sh | sh
```


#### 새로 설치된 Node의 경로 추가
```
$ vi ~/.bash_profile # 사용하고 있는 SHELL 종류에 따라 달라집니다.

# 아래와 같이 Node경로를 추가해줍니다.
export PATH="$HOME/.npm-packages/bin:$PATH"
```


#### 참고사이트
> [https://gist.github.com/DanHerbert/9520689#file-fix-homebrew-npm-md](https://gist.github.com/DanHerbert/9520689#file-fix-homebrew-npm-md)
[http://hochulshin.com/node-install-osx/](http://hochulshin.com/node-install-osx/)