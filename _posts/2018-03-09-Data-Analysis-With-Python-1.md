---
layout: post
title: "파이썬으로 데이터 분석하기 #1"
image: '/assets/img/'
description: '파이썬으로 데이터 분석하기 #1'
tags:
- Python
categories:
- Python
---


## 파이썬으로 데이터 분석하기 #1

데이터 분석을 할때, 파이썬을 이용하여 분석할 수 있는 부분에 대해서 알아보겠습니다.

---

## 유용한 함수

먼저 파이썬에서 사용할때 유용한 함수들 부터 살펴보도록 하겠습니다

### lower() : 모든 문자를 소문자로 변환

```python

sample_text = 'Hello World'

print sample_text.lower()

>>> hello world
```

### upper() : 모든 문자를 대문자로 변환

```python

sample_text = 'Hello World'

print sample_text.upper()

>>> HELLO WORLD
```

### capitalize() : 첫 글자가 대문자로 변환하고 나머지는 소문자로 변환

```python

sample_text = 'Hello World'

print sample_text.capitalize()

>>> Hello world # 주의 'Hello world'를 하나의 묶음으로 보기때문에 'world'에서 대문자로 바꿔주지 않음
```

### islower() : 모든 알파벳 문자가 소문자인지 판단 (True / False)

```python

sample_text = 'Hello World'
sample_text2 = 'hello world'

print sample_text.islower()
print sample_text2.islower()

>>>
False
True
```

### isupper() : 모든 알파벳 문자가 대문자인지 판단 (True / False)

```python

sample_text = 'Hello World'
sample_text2 = 'HELLO WORLD'

print sample_text.islower()
print sample_text2.islower()

>>>
False
True
```

### isspace() : 모든 문자가 공백인지 판단 (True / False)

```python

sample_text = 'Hello World'
sample_text2 = ' '

print sample_text.isspace()
print sample_text2.isspace()

>>>
False
True
```

### isdigit() : 모든 문자가 0 ~ 9 사이의 숫자인지 판단 (True / False)

```python

sample_text = 'Hello World 2'
sample_text2 = '123456789'

print sample_text.isdigit()
print sample_text2.isdigit()

>>>
False
True
```

### isalpha() : 모든 문자가 알파벳인지 판단 (True / False)

```python

sample_text = 'HelloWorld'
sample_text2 = 'Hello World'
sample_text3 = 'Hello World3'

print sample_text.isalpha()
print sample_text2.isalpha()
print sample_text3.isalpha()

>>>
True
False # 공백이 있어서 False
False
```

### lstrip() : 왼쪽 공백 제거

```python

sample_text = '    HelloWorld'

print sample_text
print sample_text.lstrip()

>>>
    HelloWorld
HelloWorld
```

### rstip() : 오른쪽 공백 제거

```python

sample_text = 'HelloWorld    '

print sample_text
print sample_text.rstrip()

>>>
HelloWorld    
HelloWorld
```

### strip() : 양 옆의 공백 제거 (가운데 공백 제외)

```python

sample_text = '   HelloWorld    '

print sample_text
print sample_text.strip()

>>>
   HelloWorld    
HelloWorld
```

### split(`구분자`) : `구분자`로 구분, 기본값은 공백

```python

sample_text = 'my-data-with-ptyhon'

print sample_text.split('-')

>>>
['my', 'data', 'with', 'ptyhon']
```

### `구분자`.join(`리스트`) : split 함수와 반대로 `구분자`로 붙인다

```python

sample_text = ['my', 'data', 'with', 'ptyhon']

print sample_text
print '-'.join(sample_text)

>>>
['my', 'data', 'with', 'ptyhon']
my-data-with-ptyhon
```

### `split과 join의 응용`

```python

print '-'.join('1.123.234.345'.split('.'))

>>>
1-123-234-345
```

### find(`찾고자 하는 값`) : `찾고자 하는 값`의 위치를 반환, 없으면 `-1`을 반환

```python

sample_text = 'abcd-e'

print sample_text.find('-')
print sample_text.find('*')

>>>
4
-1
```

### enumerate(`리스트`) : 인덱스와 값을 함께 반환

```python

for i, name in enumerate(['a', 'b', 'c', 'd']):
    print i, name
    
>>>
0 a
1 b
2 c
3 d
```

---

## 컬렉션

| | 리스트 | 튜플 | 셋(=집단자료형) | 딕셔너리
|:---: |:---: |:---: |:---: |:---:
| 블록 | `[ ]` | `( )` | `{ }` | `{ }`
| 인덱스 | `O` | `O` | `X` | `X`
| 특징 | `일반적인 배열` | `변형이 불가능` | `중복 X, 순서 X` | `Key-Value 형태`
| 샘플 | `['a', 'b', 'c']` | `('a', 'b', 'c')` | `{'a', 'b', 'c'}` | `{0: 'a', 1: 'b', 2: 'c'}`
| 검색 | `크기와 함께 검색시간도 증가` | `크기와 함께 검색시간도 증가` | `적합` | `키를 이용한 접근`
 
### 컬렉션 응용

```python
myList = list(set(mylist)) # 빠르게 중복 제거
```

```python
seq = ['alpha', 'bravo', 'charlie', 'delta']
print dict(enumerate(seq))

>>> {0: 'alpha', 1: 'bravo', 2: 'charlie', 3: 'delta'}
```

```python
kseq = 'abcd'
vseq = ['alpha', 'bravo', 'charlie', 'delta']
print zip(kseq, vseq)

>>> [('a', 'alpha'), ('b', 'bravo'), ('c', 'charlie'), ('d', 'delta’)]
```

```python
kseq = 'abcd'
vseq = ['alpha', 'bravo', 'charlie', 'delta']
print dict(zip(kseq, vseq))

>>> {'a': 'alpha', 'c': 'charlie', 'b': 'bravo', 'd': 'delta'}
```

---

## 리스트 내포

컬렉션을 바로 리스트 변환하는 표현식으로 진짜 유용한 기능

바로 예제로 확인

```python
myList = ['alpha', 'bravo', 'charlie', 'delta']

print [x for x in myList]

>>> ['alpha', 'bravo', 'charlie', 'delta']
```

```python
myList = [21, -23, 44, 23, 123, -55, -1, 2]

print [x for x in myList if x >= 0]

>>> [21, 44, 23, 123, 2]
```

```python
myList = [21, -23, 44, 23, 123, -55, -1, 2]

print [x**2 for x in myList if x >= 0]

>>> [441, 1936, 529, 15129, 4]
```

---

## Counter를 이용한 카운팅

Count는 컬렉션 안에 아이템의 개수를 자동으로 카운팅 합니다 

```python
from collections import Counter

phrase = 'a man a plan a canal panama'
cntr = Counter(phrase.split())
print cntr

>>> Counter({'a': 3, 'canal': 1, 'panama': 1, 'plan': 1, 'man': 1})
```

`Counter의 인스턴스`.most_common()을 이용하면 리스트 형태로 받을 수 있습니다

```python
from collections import Counter

phrase = 'a man a plan a canal panama'
cntr = Counter(phrase.split())
print cntr.most_common()

>>> [('a', 3), ('canal', 1), ('panama', 1), ('plan', 1), ('man', 1)]
```

여기서 딕셔너리로 한번 더 묶으면 딕셔너리 형태로 보기 좋게 가져올 수 있습니다

```python
from collections import Counter

phrase = 'a man a plan a canal panama'
cntr = Counter(phrase.split())
dict_cntr = dict(cntr.most_common())

print dict_cntr
print dict_cntr['a']

>>> {'a': 3, 'canal': 1, 'panama': 1, 'plan': 1, 'man': 1}
>>> 3
```