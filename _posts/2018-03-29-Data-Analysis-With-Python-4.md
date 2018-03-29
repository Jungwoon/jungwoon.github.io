---
layout: post
title: "파이썬으로 데이터 분석하기 #4"
image: '/assets/img/'
description: 'PyMySql이용해서 데이터 베이스 서버와 통신하기'
tags:
- Python
categories:
- Python
---


## PyMySql이용해서 데이터 베이스 서버와 통신하기

이번에는 PyMySql 모듈을 이용해서 Python에서 데이터베이스와 연결을 해보도록 하겠습니다.

[PyMYSql의 공식 사이트](https://github.com/PyMySQL/PyMySQL)에서 좀 더 자세한
내용을 볼 수 있습니다.

---

### 실습

Python 인터프리터 버전 확인 `(아래 지정 버전에서 정상적으로 동작합니다)`
- CPython >= 2.6 or >= 3.3
- PyPy >= 4.0
- IronPython 2.7


일단 PIP를 이용하여 PyMySql 설치하도록 하겠습니다.

```bash
$ pip install PyMySQL
```

---

### 기본 소스

그 다음은 바로 소스를 보면서 간단히 내용을 살펴 보도록 하겠습니다.

```python
import pymysql

# 우선 database 정보를 입력하여 데이터를 가져오도록 하겠습니다.
connection = pymysql.connect(
    host='데이터 베이스 IP',
    port=3306, # MySQL기본 포트 = 3306
    user='아이디',
    passwd='비밀번호',
    db='사용하고자 하는 데이터 베이스 이름',
    charset='utf8mb4')

cursor = connection.cursor()

# 동작시킬 쿼리 입력
query = '''
SELECT *
FROM employees
LIMIT 20
'''

# execute(쿼리) 명령어를 이용하여 쿼리를 실행시킵니다, 실행된 결과 레코드 수를 반환 합니다.
number_of_rows = cursor.execute(query)

# 몇개의 데이터를 가져왔는지 레코드 수 확인
print(number_of_rows)

# cursor.fetchall()을 이용하여 데이터를 가져옵니다
rows = cursor.fetchall()

for row in rows:
    print(row) # 가져온 row를 출력합니다

connection.close() # 다 썼으면 닫아줍니다
```

결과

```
20
(10001, datetime.date(1953, 9, 2), u'Georgi', u'Facello', u'M', datetime.date(1986, 6, 26))
(10002, datetime.date(1964, 6, 2), u'Bezalel', u'Simmel', u'F', datetime.date(1985, 11, 21))
(10003, datetime.date(1959, 12, 3), u'Parto', u'Bamford', u'M', datetime.date(1986, 8, 28))
(10004, datetime.date(1954, 5, 1), u'Chirstian', u'Koblick', u'M', datetime.date(1986, 12, 1))
(10005, datetime.date(1955, 1, 21), u'Kyoichi', u'Maliniak', u'M', datetime.date(1989, 9, 12))
(10006, datetime.date(1953, 4, 20), u'Anneke', u'Preusig', u'F', datetime.date(1989, 6, 2))
(10007, datetime.date(1957, 5, 23), u'Tzvetan', u'Zielinski', u'F', datetime.date(1989, 2, 10))
(10008, datetime.date(1958, 2, 19), u'Saniya', u'Kalloufi', u'M', datetime.date(1994, 9, 15))
(10009, datetime.date(1952, 4, 19), u'Sumant', u'Peac', u'F', datetime.date(1985, 2, 18))
(10010, datetime.date(1963, 6, 1), u'Duangkaew', u'Piveteau', u'F', datetime.date(1989, 8, 24))
(10011, datetime.date(1953, 11, 7), u'Mary', u'Sluis', u'F', datetime.date(1990, 1, 22))
(10012, datetime.date(1960, 10, 4), u'Patricio', u'Bridgland', u'M', datetime.date(1992, 12, 18))
(10013, datetime.date(1963, 6, 7), u'Eberhardt', u'Terkki', u'M', datetime.date(1985, 10, 20))
(10014, datetime.date(1956, 2, 12), u'Berni', u'Genin', u'M', datetime.date(1987, 3, 11))
(10015, datetime.date(1959, 8, 19), u'Guoxiang', u'Nooteboom', u'M', datetime.date(1987, 7, 2))
(10016, datetime.date(1961, 5, 2), u'Kazuhito', u'Cappelletti', u'M', datetime.date(1995, 1, 27))
(10017, datetime.date(1958, 7, 6), u'Cristinel', u'Bouloucos', u'F', datetime.date(1993, 8, 3))
(10018, datetime.date(1954, 6, 19), u'Kazuhide', u'Peha', u'F', datetime.date(1987, 4, 3))
(10019, datetime.date(1953, 1, 23), u'Lillian', u'Haddadi', u'M', datetime.date(1999, 4, 30))
(10020, datetime.date(1952, 12, 24), u'Mayuko', u'Warwick', u'M', datetime.date(1991, 1, 26))
```

---

### 데이터 삽입하기

```python
import pymysql

# 우선 database 정보를 입력하여 데이터를 가져오도록 하겠습니다.
connection = pymysql.connect(
    host='35.225.116.42',
    port=3306,
    user='root',
    passwd='frip1!',
    db='employees',
    charset='utf8mb4')

cursor = connection.cursor()

query = '''
INSERT INTO customer (name, age, region)
VALUES (%s, %s, %s)
'''

# 문제가 생길 수도 있기 때문에 예외처리를 해줍니다
try:
    # insert query를 실행
    cursor.execute(query, ('박정운', 1, '경기'))
    cursor.execute(query, ('이보경', 2, '서울'))

    # 성공하면 commit() 명령어로 변화를 저장
    connection.commit()
except:
    # 문제가 생겼으면 롤백
   connection.rollback()

# 다 썼으면 닫아줍니다
connection.close()
```

---

### 데이터 업데이트 하기

```python
import pymysql

# 우선 database 정보를 입력하여 데이터를 가져오도록 하겠습니다.
connection = pymysql.connect(
    host='35.225.116.42',
    port=3306,
    user='root',
    passwd='frip1!',
    db='employees',
    charset='utf8mb4')

cursor = connection.cursor()

query = '''
UPDATE customer SET age = 20
WHERE name = '박정운'
'''

# 문제가 생길 수도 있기 때문에 예외처리를 해줍니다
try:
    # insert query를 실행
    cursor.execute(query)

    # 성공하면 commit() 명령어로 변화를 저장
    connection.commit()
except:
    # 문제가 생겼으면 롤백
   connection.rollback()

# 다 썼으면 닫아줍니다
connection.close()
```

---

### 데이터 삭제하기

```python
import pymysql

# 우선 database 정보를 입력하여 데이터를 가져오도록 하겠습니다.
connection = pymysql.connect(
    host='35.225.116.42',
    port=3306,
    user='root',
    passwd='frip1!',
    db='employees',
    charset='utf8mb4')

cursor = connection.cursor()

query = '''
DELETE FROM customer
WHERE name = '박정운'
'''

# 문제가 생길 수도 있기 때문에 예외처리를 해줍니다
try:
    # insert query를 실행
    cursor.execute(query)

    # 성공하면 commit() 명령어로 변화를 저장
    connection.commit()
except:
    # 문제가 생겼으면 롤백
   connection.rollback()

# 다 썼으면 닫아줍니다
connection.close()
```