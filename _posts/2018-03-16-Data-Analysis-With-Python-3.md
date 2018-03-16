---
layout: post
title: "파이썬으로 데이터 분석하기 #3"
image: '/assets/img/'
description: 'BeautifulSoup를 이용하여 html파일 읽어오기'
tags:
- Python
categories:
- Python
---


## BeautifulSoup를 이용하여 html파일 읽어오기

이번에는 BeautifulSoup 이라는 강력한 파이썬 모듈을 이용하여 HTML 문서를 일고 파싱하는 방법에 대해서
알아보도록 하겠습니다.

---

### 설치

일단은 아래 명령어를 통해서 `BeautifulSoup`의 설치를 하도록 하겠습니다.

```bash
$ pip install BeautifulSoup4
```

그리고 URL을 열려면 `urlopen`이란 메소드도 필요합니다.
아래 명령어를 통해서 설치를 하도록 하겠습니다.

```bash
$ pip install urlopen
```

---

### HTML이나 XML을 읽어서 BeautifulSoup 인스턴스 만들기

아래와 같이 `BeautifulSoup()`안에 `urlopen(웹 사이트 주소)`를 입력하면 `웹 사이트 주소`의 HTML을 읽어와서 
`BeautifulSoup의 인스턴스`로 만들어 줍니다. 

가장 기본 형태는 아래와 같습니다.

```python
soup = BeautifulSoup(urlopen("웹 사이트 주소").read(), "Parser 종류")
``` 

또 다른 사용법은 아래와 같이 `open()`을 통해서 로컬 파일도 읽어올 수 있습니다. 두번째 매개변수는 `Parser 종류`입니다.

```python
soup = BeautifulSoup(open("로컬 파일 경로"), "Parser 종류")
```

---

### 지원하는 Parser 종류

- `"html.parser"` : 빠르지만 유연하지 않기 때문에 단순한 HTML문서에 사용합니다. 
- `"lxml"` : 매우 빠르고 유연합니다.
- `"xml"` : XML 파일에만 사용합니다.
- `"html5lib"` : 복잡한 구조의 HTML에 대해서 사용합니다.

---

### 기본적인 사용 방법

`BeautifulSoup 인스턴스`가 만들어지면 아래와 같이 사용할 수 있습니다.

아래 예제에서 `soup`는 `BeautifulSoup 인스턴스`입니다. 그 다음 원하는 `태그`를 넣으면 해당 `태그`에 해당하는 것만 가져옵니다.

```python
soup.태그
```

예제)

```python
soup = BeautifulSoup(urlopen("http://naver.com").read(), "html.parser")

soup.body # 위에서 가져온 문서의 body 태그만 가져옵니다.
soup.title # 위에서 가져온 문서의 title 태그만 가져옵니다.
```

---

findall()을 이용하면 특정 태그에 해당하는 것만 모두 가져와서 리스트로 반환을 합니다.

```python
soup.findAll("태그") # 이렇게 하면 "태그"에 해당하는 모든 글자를 가져와서 리스트로 반환합니다.

soup.findAll("a") # 이렇게 하면 a태그에 해당하는걸 모두 가져와서 리스트로 반환합니다.
```

다음과 같이 조건을 좀 더 넣어서 응용 할 수도 있습니다.

```python
soup.findAll("태그", "조건") # 이렇게 하면 "태그"에 해당하는 모든 글자를 찾아서 그 중에 "조건"에 해당하는 것을 리스트로 반환합니다.

soup.findAll("a", {"class":"hello"}) # 이렇게 하면 a태그에 해당하는걸 모두 가져와서 class가 hello인것만 가져옵니다.
```

---

### 사용 예제


```python
from urllib import urlopen
from bs4 import BeautifulSoup

url = "http://pythonscraping.com/pages/page1.html"

html = urlopen(url)
bsObj = BeautifulSoup(html.read(), "html.parser")

print bsObj.body
```