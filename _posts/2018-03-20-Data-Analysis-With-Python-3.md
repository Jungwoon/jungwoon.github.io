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

자세한 내용은 [공식 문서](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)에서 확인 바랍니다.

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
# 웹에서 가져오기
soup = BeautifulSoup(urlopen("웹 사이트 주소").read(), "Parser 종류")
``` 

또 다른 사용법은 아래와 같이 `open()`을 통해서 `로컬 파일`도 읽어올 수 있습니다. 두 번째 매개변수는 `Parser 종류`입니다.

```python
# 로컬 파일에서 가져오기
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

아래와 같이 사용할 수 있습니다.

```python
soup = BeautifulSoup(urlopen("http://naver.com").read(), "html.parser")

soup.body # 위에서 가져온 문서의 body 태그만 가져옵니다.
soup.title # 위에서 가져온 문서의 title 태그만 가져옵니다.
```

---

`soup.태그`를 하면 해당 하는 태그를 가져올수 있습니다

(** 대신 여러개 있는 경우에는 맨 처음 발견한 하나만 가져옵니다.)

```python
soup.head # <head> ... </head>에 해당하는 것들 가져옴
soup.body # <body> ... </body>에 해당하는 것들 가져옴
soup.a # <a> ... </a>에 해당하는 것들 가져오는데, 첫 발견 하나만 가져옴
```

---

그래서 여러개를 가져오고 싶을때, `findall()`을 이용하면 특정 태그에 해당하는 것만 모두 가져와서 `리스트`로 반환을 합니다.

```python
soup.findAll("태그") # 이렇게 하면 "태그"에 해당하는 모든 글자를 가져와서 리스트로 반환합니다.

soup.findAll("a") # 이렇게 하면 a태그에 해당하는걸 모두 가져와서 리스트로 반환합니다.
```

---

아래와 같이 제한을 줄 수도 있습니다.

```python
soup.find_all("a", limit=2) # a 태그에서 2개만 가져옴
```

---

title에서 String만 가져옵니다.`(태그 제외)`

```python
soup.title.find_all(string=True)
```

---


`limit`를 이용해서 개수 제한을 둘 수도 있습니다.

```python
soup.find_all("a", limit=2) # a 태그를 두개만 가져옴
```

---

`get_text()`를 이용해서 태그를 제외하고 글자만 가져오기

```python
name_list = soup.findAll("span", {"class": "green"})

for name in name_list:
    print(name.get_text()) # get_text()는 현재 문서에서 모든 태그를 제거하고 텍스트만 들어 있는 문자열을 반환합니다

```

---

p 태그와 속성 값이 title 이 있는거
```python
soup.find_all("p", "title")

예) <p class="title"></p>
```

---

아래와 같이 `조건`을 좀 더 넣어서 응용 할 수도 있습니다.

```python
soup.findAll("태그", "조건") # 이렇게 하면 "태그"에 해당하는 모든 글자를 찾아서 그 중에 "조건"에 해당하는 것을 리스트로 반환합니다.

soup.findAll("a", {"class":"hello"}) # 이렇게 하면 a태그에 해당하는걸 모두 가져와서 class가 hello인것만 가져옵니다.
```

---

`['태그1', '태그2']`을 이용해서 여러개의 태그를 동시에 검색할 수도 있습니다. (or 조건)

```python
soup.find_all(["a", "b"]) # a 태그와 b 태그 찾기
```

---

속성 값 가져오기

```python
soup.p['class']
soup.p['id']
```

---

보기 좋게 출력

```python
soup.b.prettify()
```

---

간단한 검색

```python
soup.body.b # body 태그 아래의 첫번째 b 태그
soup.a # 첫번째 a 태그
```

---

### 사용 예제

아래 예제는 `NYT`기사에서 `p 태그` 중에 `class : story=body-text`인 것들만 읽어서 text로 뽑는 예제입니다.

위의 내용들과 아래 예제를 참고하면 여러가지 응용을 할 수 있습니다.

```python
# coding=utf-8
from bs4 import BeautifulSoup
from urllib import urlopen

# 웹 페이지를 읽고 soup을 만든다.
url = "https://www.nytimes.com/2018/03/15/world/europe/germany-food-bank-migrant-ban.html?hp&action=click&pgtype=Homepage&clickSource=image&module=photo-spot-region&region=top-news&WT.nav=top-news"
html = urlopen(url)
soup = BeautifulSoup(html.read(), "html.parser")

# 본문만 읽어오기
paragraph_list = soup.find_all("p", {"class":"story-body-text"})

for a in paragraph_list:
    print a.get_text()
```