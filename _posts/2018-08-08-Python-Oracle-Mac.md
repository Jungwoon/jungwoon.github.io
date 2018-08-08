---
layout: post
title: "파이썬에서 오라클 사용하기(맥OS)"
image: '/assets/img/'
description: 'Python에서 Oracle 사용하기 for Mac'
tags:
- Google Cloud
categories:
- Google Cloud
---

## 파이썬에서 오라클 사용하기(맥OS)

엄청 오랜만에 포스팅을 하네요, 최근에 파이썬에서 Oracle에 연결을 해야 했었는데 관련해서
설정해줘야 하는 부분들이 조금 있어서 관련 내용으로 포스팅을 해보려고 합니다.

(** 현재 제가 MacOS를 사용하기 때문에 MacOS 위에서 설정하는 방법으로 진행할 예정입니다)

### 환경 설정하기

우선 [다운로드 페이지](http://www.oracle.com/technetwork/topics/intel-macsoft-096467.html)에 들어갑니다.
그럼 아래와 같은 사이트가 보이는데요.

![[](https://cdn-images-1.medium.com/max/1600/1*AxgGcip2btUMPZLA_CuAaA.png)

여기서 아래 파일들을 다운로드 받아줍니다.

- `instantclient-basic-macos.x64–12.2.0.1.0.zip`
- `instantclient-sqlplus-macos.x64–12.2.0.1.0.zip`
- `instantclient-sdk-macos.x64–12.2.0.1.0.zip`

다운로드가 다 되면 아래와 같이 나오는데, 여기서 `터미널을 이용`하여 압축을 풀어줍니다.

(** 터미널을 이용하지 않으면 귀찮아지니 터미널의 `unzip`을 쓰도록 하겠습니다)

![](https://cdn-images-1.medium.com/max/2000/1*B5Lt3lCklm84oUbBR32xcw.png)

아래 명령어를 입력해줍니다.

```
$ unzip instantclient-basic-macos.x64–12.2.0.1.0.zip
$ unzip instantclient-sqlplus-macos.x64–12.2.0.1.0.zip
$ unzip instantclient-sdk-macos.x64-12.2.0.1.0-2.zip
```

![](https://cdn-images-1.medium.com/max/2000/1*jTZo7R6wN2vFI1BE4C-KnQ.png)

![](https://cdn-images-1.medium.com/max/2000/1*bSVN_kVfvDRFzcOfPz6gYg.png)

![](https://cdn-images-1.medium.com/max/2000/1*IBqlziCQrWitTI-4-iayQg.png)

그럼 `개별 디렉토리`가 아닌 `instantclient_12_2`와 같은 `하나의 디렉토리`에 파일들이 들어가게 됩니다.

![](https://cdn-images-1.medium.com/max/2000/1*d9CtpIWOs4ydgBgxzsTQOw.png)

그 다음 아래의 명령어를 입력해서 `/usr/local/oracle` 이란 디렉토리를 만들고 방금 압축이 풀린
`instantclient12_2` 디렉토리를 이동시켜줍니다.

(권한 때문에 `sudo` 명령어를 입력해주셔야 합니다)

![](https://cdn-images-1.medium.com/max/2000/1*nzP9S-gQJqHaOjTT92FJEQ.png)

```
$ sudo mkdir /usr/local/oracle
$ sudo mv instantclient_11_2 /usr/local/oracle
```

그 다음이 중요한데 아래 심볼릭 링크를 잡아주셔야 합니다.

`(주의 : 여기서 /usr/local/oracle/instantclient_12_2/ 는 위에서 설정한 부분입니다)`

![](https://cdn-images-1.medium.com/max/2000/1*ETLrCvcX0anze4N16G5PyA.png)

```
$ ln -sf /usr/local/oracle/instantclient_12_2/sdk/include/*.h /usr/local/include/
$ ln -sf /usr/local/oracle/instantclient_12_2/sqlplus /usr/local/bin/
$ ln -sf /usr/local/oracle/instantclient_12_2/*.dylib /usr/local/lib/
$ ln -sf /usr/local/oracle/instantclient_12_2/*.dylib.12.1 /usr/local/lib/
$ ln -sf /usr/local/oracle/instantclient_12_2/libclntsh.dylib.12.1 /usr/local/lib/libclntsh.dylib
```

`$ ls -al` 명령어로 확인해보면 잘 걸린걸 확인하실 수 있습니다.

![](https://cdn-images-1.medium.com/max/2000/1*_aBbgpYmNyMu9tAuu0LFAg.png)

```
total 454032
drwxrwxrwx@ 24 jungwoon  staff        768  8  8 09:06 .
drwxr-xr-x   3 root      wheel         96  8  8 09:12 ..
-rw-rw-rw-@  1 jungwoon  staff        400  1 10  2018 BASIC_README
-rw-rw-rw-@  1 jungwoon  staff        404  1 10  2018 SQLPLUS_README
-rwxrwxrwx@  1 jungwoon  staff      10036  1 10  2018 adrci
-rwxrwxrwx@  1 jungwoon  staff      40360  1 10  2018 genezi
-r-xr-xr-x@  1 jungwoon  staff        342  4 25  2015 glogin.sql
lrwxrwxrwx   1 jungwoon  staff         20  8  8 09:05 libclntsh.dylib -> libclntsh.dylib.12.1
-rwxrwxrwx@  1 jungwoon  staff   85621532  1 10  2018 libclntsh.dylib.12.1
-rwxrwxrwx@  1 jungwoon  staff    4685408  1 10  2018 libclntshcore.dylib.12.1
-r-xr-xr-x@  1 jungwoon  staff    8179072  6 29  2017 libnnz12.dylib
lrwxrwxrwx   1 jungwoon  staff         18  8  8 09:05 libocci.dylib -> libocci.dylib.12.1
-rwxrwxrwx@  1 jungwoon  staff    1465312  1 10  2018 libocci.dylib.12.1
-rwxrwxrwx@  1 jungwoon  staff  124769196  1 10  2018 libociei.dylib
-r-xr-xr-x@  1 jungwoon  staff     151748  8  8  2017 libocijdbc12.dylib
-r-xr-xr-x@  1 jungwoon  staff     237780  1  8  2018 libons.dylib
-rwxrwxrwx@  1 jungwoon  staff      84988  1 10  2018 liboramysql12.dylib
-rwxrwxrwx@  1 jungwoon  staff    1267088  1 10  2018 libsqlplus.dylib
-r-xr-xr-x@  1 jungwoon  staff    1639388  7 12  2017 libsqlplusic.dylib
-r--r--r--@  1 jungwoon  staff    4036257 12 14  2016 ojdbc8.jar
drwxrwxrwx@  8 jungwoon  staff        256  1 10  2018 sdk
-rwxr-xr-x@  1 jungwoon  staff       8480  1 10  2018 sqlplus
-rwxrwxrwx@  1 jungwoon  staff     146084  1 10  2018 uidrvci
-r--r--r--@  1 jungwoon  staff      74230  1 26  2017 xstreams.jar
```

마지막으로 설정한 경로를 `~/.bashrc`에 설정해줍니다.

(저는 `bash`말고 `zsh`를 사용하기 있어서 `zshrc`로 들어갔습니다.)

![](https://cdn-images-1.medium.com/max/2000/1*Xa9hyvs9ObNc4gxZiaz1QA.png)

```
# Oracle
export ORACLE_HOME=/usr/local/oracle/instantclient_12_2
export OCI_INC_DIR=/usr/local/oracle/instantclient_12_2/sdk/include/
export OCI_LIB_DIR=/usr/local/oracle/instantclient_12_2
export LD_LIBRARY_PATH=/usr/local/oracle/instantclient_12_2
export DYLD_LIBRARY_PATH=/usr/local/oracle/instantclient_12_2:$DYLD_LIBRARY_PATH
```

설정을 해주었으면 적용을 위해 `source` 명령어로 변경된 내용을 적용 시킵니다

(저는 `bash`말고 `zsh`를 사용하기 있어서 `zshrc`를 적용했습니다.)

![](https://cdn-images-1.medium.com/max/400/1*9PpSZiZKuhthngT-QPsCPQ.png)

```
$ source ~/.bashrc
```

여기까지 해주시면 Oracle을 사용하기 위한 설정이 끝이 납니다.

---

### 파이썬에서 Oracle 연결하기

사실 위에 환경설정하는 부분이 힘들어서 그렇지 소스를 만드는건 간단합니다.

이 부분은 아래 예제 소스와 주석으로 대체하도록 하겠습니다.

```python
# coding=utf-8
import cx_Oracle

# [아이디]/[비밀번호]@[서버 주소]/[SID] 형태로 들어가야 합니다.
connection = cx_Oracle.connect("jungwoon/1111@111.111.111.111/JUNGWOON")

# cursor를 만들어줍니다
cursor = connection.cursor()

startDate = '20180801'
endDate = '20180831'

query = '''
select date, name, content
from sample_table
where date between %s and %s
''' % (startDate, endDate)

# 실행을 시킵니다.
cursor.execute(query)

# 아래와 같이 차례대로 불러온 컬럼명르 잡아주게 되면 개별 변수에 차례대로 값이 들어가게 됩니다.
for date, name, content in cursor:
    print("Values : ", date, name, content)

```

---

### 마무리

처음 시작할때는 엄청 금방 될지 알았는데, 환경 설정 부분때문에 삽질을 많이해서 해당 내용 정리도 할겸 포스팅 했습니다.

혹시 MacOS에서 OCI8 설정을 하기 위한 부분이 궁금하시면 [맥에서 PHP OCI8 설정하기](https://medium.com/@byJW/php-oci8-installation-on-macos-b42df1e8441b)
를 참고해주시면 됩니다.

(추가 설정을 해야하는 부분이 있네요)