---
layout: post
title: "Embulk 설치 및 기본 사용법"
image: '/assets/img/'
description: 'Embulk 설치 및 기본 사용법'
tags:
- Embulk
categories:
- BigData
---

Embulk 설치 및 기본 사용법에 대해서 알아보겠습니다.

### Embulk?

![](https://cdn-images-1.medium.com/max/2000/1*L1y27SO415-mIpf3TxeM7A.png)

Embulk는 Sqoop과 거의 같은 기능을 하는 솔루션으로 여러 데이터 소스로 읽어와서 부어주는 기능을 하는 솔루션입니다 

### Embulk 특징

- 플러그인 형태로 여러 데이터 소스를 이용할 수 있습니다(BigQuery, Oracle, MySQL, PostgreSQL, CSV, JSON...)
- 병렬로 로딩이 가능합니다
- 스키마를 예측해서 해당 스키마가 없으면 알아서 만들어 줍니다


### Embulk 설치

설치 방법은 MacOS에서 실습을 진행하였습니다(Linux도 동일합니다) 설치 전에 자바가 미리 설치되어 있어야 합니다

> MacOS외의 환경에서 설치방법 및 자세한 설명은 [https://github.com/embulk](https://github.com/embulk) 에서 확인 가능합니다


단지 아래 커맨드 4개만 입력하면 설치가 됩니다

```
$ curl --create-dirs -o ~/.embulk/bin/embulk -L "https://dl.embulk.org/embulk-latest.jar"
$ chmod +x ~/.embulk/bin/embulk
$ echo 'export PATH="$HOME/.embulk/bin:$PATH"' >> ~/.bashrc
$ source ~/.bashrc
```

설치 완료 후 아래 커맨드를 치면 다음과 같이 설치된것을 확인할 수 있습니다

```
$ embulk -version
embulk 0.8.31
```

### Embulk plug-in 설치

```
$ embulk gem install [plug-in] # 필요한 플러그인은 [plug-in] 부분에 넣어서 설치를 합니다
$ embulk gem list # 설치할 수 있는 플러그-인 리스트를 확인합니다
```

Embulk는 input과 output 2개의 각각 개별적으로 플러그인을 설치해야 합니다

아래는 mysql 관련 플러그인 설치를 진행해보겠습니다

```
$ embulk gem install embulk-input-mysql
$ embulk gem install embulk-output-mysql
```

### Embulk Example (CSV -> MySQL)

Embulk는 기본적으로 예제를 지원합니다 예제로 생성되는 csv 파일로부터 데이터를 읽어와을 MySQL에 넣는 작업을 진행하겠습니다

우선 다음 명령어로 Sample을 만듭니다

```
$ embulk example
2017-08-31 09:32:03.449 +0900: Embulk v0.8.31
Creating embulk-example directory...
    Creating embulk-example/
    Creating embulk-example/csv/
    Creating embulk-example/csv/sample_01.csv.gz
    Creating embulk-example/seed.yml

Run following subcommands to try embulk:

     1. embulk guess embulk-example/seed.yml -o config.yml
     2. embulk preview config.yml
     3. embulk run config.yml
``` 

그럼 디렉토리에 embulk-example이 생성이 됩니다, 다음 명령어를 통해서 해당 디렉토리로 들어갑니다, 그럼 csv와 seed.yml이 생성되어 있는걸 확인할 수 있습니다

```
$ cd embulk-example
csv      seed.yml
```


Embulk는 기본적으로 설정에 대한 부분은 `yml파일`에 명시를 해놓고 실행하는 식으로 되어 있습니다


기본적으로 생성이 되는 seed.yml을 열어보면 다음과 같습니다

- `in:` - 읽어들이는쪽의 설정 (플러그인에 따라 달라짐)
- `out:` - 로딩하는쪽의 설정 (플러그인에 따라 달라짐)
 

```
in:
  type: file # file 타입의 파일 여기서는 csv 파일
  path_prefix: "/Users/jungwoon/embulk-example/csv/sample_" # csv 파일 경로
out:
  type: stdout # stdout은 그냥 화면 출력
```

yml이 만들어졌으면 `embulk guess`란 명령으로 해당 데이터에 대한 추론이 가능할뿐아니라 자동으로 설정까지 만들어 줍니다
일단 아래 명령어를 통해 seed.yml에 대해서 확인을 해봅니다

```
$ embulk guess seed.yml
``` 


![](https://cdn-images-1.medium.com/max/2000/1*aiyva36152nXVfpy_4OgZw.png)

그럼 위와 같이 결과가 나옵니다. 이 추론을 이용해서 -o 옵셥을 통해 설정 파일을 만듭니다

```
$ embulk guess seed.yml -o config.yml
```

그럼 config.yml이란 파일이 생성이 되면서 위에서 추론했던 결과가 그대로 들어가게 됩니다

그럼 이제 `out:` 부분을 수정해서 MySQL에 들어가도록 `config.yml`을 수정합니다.

> 원래 guess를 하면 time, purchase 둘 다 timestamp인데 MySQL에 두개가 한번에 들어가면 에러가 나서 purchase를 string으로 바꿔줌

```
in:
  type: file
  path_prefix: /Users/jungwoon/embulk-example/csv/sample_
  decoders:
  - {type: gzip}
  parser:
    charset: UTF-8
    newline: LF
    type: csv
    delimiter: ','
    quote: '"'
    escape: '"'
    null_string: 'NULL'
    trim_if_not_quoted: false
    skip_header_lines: 1
    allow_extra_columns: false
    allow_optional_columns: false
    columns:
    - {name: id, type: long}
    - {name: account, type: long}
    - {name: time, type: timestamp, format: '%Y-%m-%d %H:%M:%S'}
    - {name: purchase, type: string} # timestamp가 들어가면 에러가 나서 String으로 바꿔줌
    - {name: comment, type: string}
out:
    type: mysql
    host: [Database IP]
    port: 3306
    user: [Databse ID]
    password: [Database PW]
    database: [Database Name]
    table: [Table Name]
    mode: replace
```

저장을 하고 다음 명령어로 실행을 시킵니다

```
$ embulk run config.yml
```

그럼 다음과 같이 진행 상항이 나옵니다

![](https://cdn-images-1.medium.com/max/2000/1*tYwwojw12XZehb4Ti1KGXg.png)

그 다음 데이터가 잘 들어갔는지 확인을 해보면 다음과 같이 잘 들어간걸 확인할 수 있습니다

![](https://cdn-images-1.medium.com/max/2000/1*3GvMRqQsfLAc8xnin_tBzA.png)






