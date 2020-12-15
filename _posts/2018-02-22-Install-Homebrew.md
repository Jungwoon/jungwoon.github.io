---
layout: post
title: "Homebrew 설치하기"
image: '/assets/img/'
description: 'Homebrew 설치하기'
tags:
- Google Cloud
categories:
- Google Cloud
---

제가 Mac을 사면 가장 먼저 설치하는게, Homebrew 입니다.
`Homebrew`는 맥에서 사용할 수 있는 리눅스의 `yum`이나 `apt-get`같은 패키지 관리자
입니다.  

먼저 [Homebrew 홈페이지](https://brew.sh/index_ko.html)에 들어갑니다 

그럼 아래와 같은 화면이 나오는데 `Homebrew 설치하기` 아래에 있는 명령어를 복사합니다.

![](https://cdn-images-1.medium.com/max/2000/1*FjJ7U1PUaZYJ6ZVeu2wrlQ.png)

그 다음 아래 명령어를 터미널을 열어서 붙여넣기 하고 엔터를 누르면 Homebrew가 설치가 됩니다

```bash
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

![](https://cdn-images-1.medium.com/max/2000/1*A7u_jLGSpkWuSjFaVL69Jg.png)


### 사용법

간단히 Homebrew 사용방법에 대해서 알아보도록 하겠습니다.

```bash
$ brew install [패키지 명]   # 필요한 패키지 설치
$ brew uninstall [패키지 명] # 설치한 패키지 삭제
$ brew search [패키지 명]    # 설치하고자 하는 패키지명 검색
$ brew list                # Homebrew로 설치된 리스트 확인
$ brew update              # Homebrew 업데이트
```  

다음은 Node.js를 설치해 보도록 하겠습니다. 아래 명령어를 입력하고 엔터를 치면 node 설치가 됩니다.

```bash
$ brew install node
```

![](https://cdn-images-1.medium.com/max/2000/1*0Hg1zCci_quPqfG66sc0Aw.png)

다음과 같이 위의 사용방법 대로 손쉽게 패키지들을 관리할 수 있는 아주 고마운 툴입니다.