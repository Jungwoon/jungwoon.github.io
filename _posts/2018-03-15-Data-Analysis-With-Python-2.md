---
layout: post
title: "파이썬으로 데이터 분석하기 #2"
image: '/assets/img/'
description: '파이썬에서 정규 표현식 사용하기'
tags:
- Python
categories:
- Python
---


## 파이썬에서 정규표현식 사용하기

정규 표현식은 패턴 매칭에 기반해서 문자열을 검색하고 자르고 교체하는 강력한 기능입니다.

파이썬에서는 `re 모듈`을 이용하여 정규 표현식을 이용할 수 있습니다.

만약 정규표현식을 잘 모른다면 
[정규 표현식(Regular Expression) 정리하기](https://jungwoon.github.io/regex/2018/03/14/Regular-Expression/)를 참고하시기 바랍니다

---

### 기본 이용 방법

```python
import re

pattern = re.compile('정규표현식 패턴')
result = pattern.match('텍스트)
```

위와 동일한 방법

```python
import re

result = re.match('정규표현식 패턴', '텍스트')
```

---

### 예제

```python
# coding=utf-8

import re

pattern = re.compile('[a-z]+')  # re.compile('정규표현식 패턴')을 하면 해당 '정규표현식 패턴' 해당하는 패턴 객체가 생성된다
match = pattern.match("python")  # 위에서 생성된 패턴 객체로 특정 텍스트에서 결과를 가져올 수 있다.

print match.group() # 결과는 group()에 저장이 된다.

>>> python
```

다음은 위와 동일한데 한번에 이용하는 방법입니다.

```python
# coding=utf-8

import re

match = re.match('[a-z]+', 'python')

print match.group()

>>> python
```

---

### re 모듈의 메서드

| 메서드 | 설명
| :--- | :---
| compile(`정규 표현식 패턴`) | `정규 표현식 패턴`에 해당하는 패턴 객체 생성
| match(`정규 표현식 패턴`, `텍스트`) | `텍스트`에서 `정규 표현식 패턴`에 해당하는 값을 찾음, 만약 처음부터 일치하지 않으면 `None`
| search(`정규 표현식 패턴`, `텍스트`) | `텍스트`에서 `정규 표현식 패턴`에 해당하는 값을 찾음, `전체`를 찾아봄
| findall(`정규 표현식 패턴`, `텍스트`) | `텍스트`에서 `정규 표현식 패턴`에 해당하는 값을 찾아서 `리스트`로 반환
| split(`정규 표현식 패턴`, `텍스트`) | `텍스트`에서 `정규 표현식 패턴`에 해당하는 값을 찾아서 `부분 문자열`로 자르고 `리스트`로 반환한다
| sub(`정규 표현식 패턴`, `바꿀 글자`, `텍스트`) | `텍스트`에서 `정규 표현식 패턴`에 해당하는 값을 찾아서 `바꿀 글자`로 바꾼다

---

### compile() 메서드

`compile()` 자체만으로는 뭔가를 찾을 수 없지만 이렇게 패턴 객체를 만들어 놓고 사용하게 되면 `재사용`을 할 수 있습니다.

```python
# coding=utf-8

import re

pattern = re.compile('[a-z]+') # 패턴 객체를 가져옴
match = pattern.match('hello') # 위에서 얻은 패턴 객체로 매칭함

print match.group()

>>> hello
```

---

### match()와 search() 메서드

match()와 search()는 비슷한 기능을 하기 때문에, 비교를 위해서 같이 실습을 해보도록 하겠습니다.

`match()`는 `처음`부터 패턴이 일치 하지 않으면 None을 반환합니다.

`search()`는 `전체`를 찾아서 일치하는 것을 찾습니다 

`중요) 두 메서드 모두 자신이 찾는 패턴을 찾으면 검색을 멈춥니다` 

```python
# coding=utf-8

import re

match = re.match('World', 'Hello World') # 처음 부터 패턴이 매칭되지 않으면 None을 가져옴
search = re.search('World', 'Hello World') # 끝까지 패턴을 찾아봄


if match:
    print 'match : ' + match.group()

if search:
    print 'search : ' + search.group()


>>>
search : World
```

---

### findall() 메서드

`findall()`은 전체를 탐색하여 `패턴에 해당 하는 모든 값`을 `리스트`로 반환합니다


```python
# coding=utf-8

import re

findall = re.findall('World', 'Hello World Hello World')  # 끝까지 패턴을 찾아봄

print findall

>>> ['World', 'World']
```

---

### split() 메서드

`패턴`에 `구분자`를 넣으면 구분자 기준으로 나눠서 `리스트`로 반환합니다

```python
# coding=utf-8

import re

split = re.split('-', 'Hello-World-Hello-World')  # 끝까지 패턴을 찾아봄

print split

>>> ['Hello', 'World', 'Hello', 'World']
```

---

### sub() 메서드

`패턴`에 해당하는 텍스트를 찾아서 `바꿀 글자`로 바꿔서 반환합니다

```python
# coding=utf-8

import re

sub = re.sub(r'[a-z ]', '[...]', '0010010 has at least one 010 letter 0010010')

print sub

>>> 
0010010[...][...][...][...][...][...][...][...][...][...][...][...][...][...][...][...][...][...]010[...][...][...][...][...][...][...][...]0010010
```

---

### Tip) 파이썬에서는 ` \ `를 써서 특수문자를 사용하지 않아도 되게끔 아래와 같이 지원을 해줍니다.

```
"\\n" 와 r"\n"는 동일함
``` 