---
layout: post
title: "정규 표현식 정리하기"
image: '/assets/img/'
description: '정규 표현식 정리하기'
tags:
- Regex
categories:
- Regex
---


## 정규 표현식 정리하기

이번에는 정규 표현식을 한번 정리해보도록 하겠습니다.
정규 표현식은 텍스트에서 특정 패턴만 골라서 가져올 수 있는 기술로
파싱이나 크롤링때 많이 이용합니다

---

### 기본 구성

기본적으로 정규표현식은 `/`사이에 들어가며 바깥쪽에는 `다양한 옵션`을 가진 `플래그`를 설정할 수 있습니다

![](https://cdn-images-1.medium.com/max/2000/1*xMKMeMP-PvE_yy8oEH4IhQ.png)

---

### 실습

```regexp
/A/
```

결과

```
`A`A BB Aa Bb 80%
```

추출 글자

```
["A"]
```

---

```regexp
/A/g
```

결과

```
`A``A` BB `A`a Bb 80%
```

추출 글자

```
["A", "A", "A"]
```

---

```regexp
/A/gi
```

결과

```
`A``A` BB `A``a` Bb 80%
```

추출 글자

```
["A", "A", "A", "a"]
```

---

```regexp
/AA/gi
```

결과

```
`AA` BB `Aa` Bb 80%
```

추출 글자

```
["AA", "Aa"]
```

---