---
layout: post
title: "Git 정리 #3 - 브랜치"
image: '/assets/img/'
description: 'Git Summary #3 - Branch'
tags:
- Git
categories:
- Git
---

최근에 이직을 하고 지금까지 혼자 개발한것과 다르게 팀으로 개발을 하다가 보니까 Git을 제대로(?) 사용해야하는 상황이 많아졌습니다. 그래서
최근에 다시한번 Git 관련 책을 읽으면서 공부한 내용을 정리해보고자 합니다.

---

## 브랜치

브랜치는 기존 내용을 복사해서 새로운 저장소를 만드는 Git의 기능으로 브랜치를 사용하면 프로젝트를 독립적으로 관리할 수 있습니다.

### 브랜치 사용 이유

보통 기존의 코드에서 수정이나 새로운 기능이 필요할때 기존 코드에 영향이 가지 않도록 기존 코드에서 별도의 브랜치를 생성해서 작업하고 
기존 코드에 합치는 식으로 작업을 하게 되며 이렇게 하면 기존 코드에 영향을 주지 않고 독립적으로 개발할 수 있습니다.

```
기존 코드를 가진 브랜치 -> 새로운 기능을 위한 브랜치 -> Merge -> 최신화 -> 새로운 기능을 위한 브랜치 삭제
    (master)                (feature)              (master)       (feature 삭제) 
```

### 깃 브랜치 특징

1. 가상 폴더 : 깃의 브랜치는 작업 폴더를 실제로 복사하지 않고, 가상 폴더를 생성합니다. (=외부에서는 물리적인 파일이 하나만 있음)
2. 독립적인 동작 : 브랜치를 이용하면 원본 폴더와 분리하여 독립적으로 개발 작업을 수행할 수 있습니다.
3. 빠른 동작 : Blob(=포인트)를 이동하여 브랜치간 빠르게 전환합니다.

### 현재 브랜치 확인하기

아래는 현재 브랜치가 master로 되어 있음을 확인할 수 있습니다. 

> 별도의 브랜치 생성전에는 `mater` 가 기본 브랜치입니다.

```shell
$ git status
현재 브랜치 master
브랜치가 'origin/master'에 맞게 업데이트된 상태입니다.

커밋할 사항 없음, 작업 폴더 깨끗함
```

그리고 보통 터미널에서는 아래와 같이 현재 브랜치 명을 바로 확인할 수 있습니다.

![](https://miro.medium.com/max/1400/1*Ij-bsZPQ16lF1-BpXCfrag.png)

저장소의 전체 브랜치를 보려면 아래 명령어를 사용합니다.

```shell
# -v = 브랜치 목록을 좀 더 자세히 볼 수 있습니다. (Optional) 
$ git branch -v 
  develop c69b87a Posting
* master  c69b87a Posting
```


### 브랜치간 이동하기

`checkout`명령어를 통해서 이동할 수 있습니다.

```shell
# 브랜치명 = develop, master
(master) $ git checkout develop
(develop) $ git checkout master
(master) $
```

### 브랜치 생성하기

생성은 `branch` 명령어를 통해서 생성할 수 있습니다. (생성된 브랜치로 이동 X)

복사하고자 하는 브랜치로 이동해서 `branch` 명령어로 복사합니다.

> 아래에서는 develop 브랜치로부터 feature 브랜치를 만들었습니다.

```shell
(master) $ git checkout develop
# 새로 만들 브랜치명 = feature
(develop) $ git brnach feature 
(develop) $ git branch -v

  master c69b87a Posting
  feature c69b87a Posting
* develop  c69b87a Posting
```

만약 만들고 바로 이동하고 싶으면 `checkout -b` 명령어를 사용합니다.

```shell
(develop) $ git checkout -b feature
(feature) $ 
```

> #### 브랜치 명 규칙
> - 기호(-)로 시작할 수 없습니다.
> - 마침표(.)로 시작할 수 업습니다.
> - 연속적인 마침표(..)를 포함할 수 없습니다.
> - 빈칸, 공백 문자, 물결(~), 캐럿(^), 물음표(?), 별표(*), 대괄호([]) 등의 특수문자를 포함할 수 없습니다.
> - 아스키 제어 문자는 포함할 수 없습니다.

### 브랜치 삭제하기

`branch -d` 명령어를 통해서 삭제할 수 있습니다.

> 삭제할때는 삭제하고자 하는 브랜치에 있으면 안됩니다.

```shell
(master) $ git branch -d develop
develop 브랜치 삭제 (과거 c69b87a).
```

#### 업스트림 트래킹

브랜치를 원격 저장소의 특정 브랜치를 바라보게 설정하고 싶을때 아래와 같이 사용할 수 있습니다.

`-u` 옵션을 사용하면 자동으로 원격 저장소가 설정이 되기 때문에 추후에 `git push` 만으로 원격에 업로드 할 수 있습니다.

```shell
# 원격 저장소 = upstream
# 선택한 브랜치 = develop
# 아래 develop을 upstream에 업로드 한다는 의미입니다.
$ git push -u upstream develop
```

각각의 브랜치의 트래킹 브랜치를 확인하고 싶으면 아래 명령어를 사용합니다.

```shell
(master) $ git barnch -vv
* master 771ff76 [origin/master] Posting
```

기존에 있는 브랜치를 업스트림에 연결하려면

```shell
# 로컬의 develop 브랜치를 upstream에 있는 develop에 연결한다는 의미입니다.
# --set-upstream-to 는 -u 로 생략 가능
(develop) $ git barnch --set-upstream-to upstream/develop 
```

푸시를 하면서 연결을 하려면 아래 명령어를 사용할 수 있습니다.

```shell
(develop) $ git push --set-upstream upstream develop 
```