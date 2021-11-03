---
layout: post
title: "Git 정리 #2 - 원격 저장소"
image: '/assets/img/'
description: 'Git Summary #2 - Remote'
tags:
- Git
categories:
- Git
---

최근에 이직을 하고 지금까지 혼자 개발한것과 다르게 팀으로 개발을 하다가 보니까 Git을 제대로(?) 사용해야하는 상황이 많아졌습니다. 그래서
최근에 다시한번 Git 관련 책을 읽으면서 공부한 내용을 정리해보고자 합니다.

---

## 원격 저장소 (=Remote Repository)

Git은 Local(내 컴퓨터)에 저장하는 공간 외에도 Remote(서버)에 저장을 할 수 있습니다.

근데 생각해보면 모두가 같이 코드를 수정해서 합칠때 개별 컴퓨터가 아닌 공통된 공간이 필요하죠 그 공간이 원격 저장소입니다.

원격 공간은 다양한 서비스들을 이용할 수 있는데 보통은 `Github`이나 `Gitlab`등의 서비스를 이용할 것입니다.
(위의 서비스들을 통해서 Repository를 생성하면 자동으로 원격 저장소가 생성됩니다.)

여기에서 각각의 프로젝트 공간을 `Repository`라고 하는데, `Local(내 컴퓨터)`에서 사용할때는 `Local Repository`라고 하고,
`Remote(서버)`에 있다면 `Remote Repository`라고 합니다.

---

### Github의 내 원격 저장소 주소 알기 

`Github`을 기준으로 해서 설명하겠습니다. 각각의 `Repository`에 들어가면 `Code -> HTTPS` 쪽에 보면 주소를 알 수 있습니다.

![](https://miro.medium.com/max/4800/1*uiwqXVkM2u3ejnV855fvZA.png)

이 주소가 원격 주소가 되고 해당 주소를 가지고 `Git`에 원격으로 등록하는 방법에 대해서 알아보도록 하겠습니다.
(물론 아무나 다 등록할 수 있는건 아니고 권한이 필요합니다.)

### 원격 저장소 등록

위에서 알게된 원격 저장소 주소를 `origin`란 별칭으로 등록합니다.

```shell
# 원격 저장소 별칭 = origin
# 복사한 주소 = https://github.com/Jungwoon/jungwoon.github.io.git
$ git remote add origin https://github.com/Jungwoon/jungwoon.github.io.git
```

### 설정된 원격 저장소 확인

원격 저장소가 잘 등록되었는지 확인합니다.

```shell
$ git remote -v
origin  https://github.com/Jungwoon/jungwoon.github.io.git (fetch)
origin  https://github.com/Jungwoon/jungwoon.github.io.git (push)
```

### 원격 저장소 삭제

필요가 없어진 원격 저장소를 제거합니다.

```shell
# 원격 저장소 별칭 = origin
$ git remote rm origin
```

#### 원격 저장소로 전송

`Local Repository` 의 커밋된 내용들은 `push`를 통해서 `Remote Repository`로 보낼 수 있습니다. 

```shell
# 원격 저장소 별칭 = remote
# 브랜치명 = develop
$ git push remote develop
```

#### Remote Repository 복사하기

`clone` 명령어를 이용하면 원격 저장소에 정보와 함께 기본적인 git 세팅까지 된 상태로 다운받을 수 있습니다.

```shell
$ git clone https://github.com/Jungwoon/jungwoon.github.io.git
'jungwoon.github.io'에 복제합니다...
remote: Enumerating objects: 2427, done.
remote: Counting objects: 100% (245/245), done.
remote: Compressing objects: 100% (181/181), done.
remote: Total 2427 (delta 145), reused 153 (delta 64), pack-reused 2182
오브젝트를 받는 중: 100% (2427/2427), 4.96 MiB | 7.22 MiB/s, 완료.
델타를 알아내는 중: 100% (1397/1397), 완료.

# 잘 복사된걸 확인할 수 있습니다.
$ ls -al
total 0
drwxr-xr-x   3 jungwoon  staff    96 Nov  3 21:14 .
drwxr-xr-x+ 59 jungwoon  staff  1888 Nov  3 21:14 ..
drwxr-xr-x  21 jungwoon  staff   672 Nov  3 21:14 jungwoon.github.io

# 복사된 디렉토리를 확인해보면 복사한 remote의 정보도 이미 설정되어 있는걸 확인할 수 있습니다.
$ cd jungwoon.github.io
$ git remote -v
origin  https://github.com/Jungwoon/jungwoon.github.io.git (fetch)
origin  https://github.com/Jungwoon/jungwoon.github.io.git (push)
```

#### 원격 저장소 변경된 내용 최신화 하기

`fetch` 명령어를 통해서 최신화 할 수 있습니다.

```shell
$ git fetch origin
remote: Enumerating objects: 7, done.
remote: Counting objects: 100% (7/7), done.
remote: Compressing objects: 100% (1/1), done.
remote: Total 4 (delta 3), reused 4 (delta 3), pack-reused 0
오브젝트 묶음 푸는 중: 100% (4/4), 983 bytes | 327.00 KiB/s, 완료.
https://github.com/Jungwoon/jungwoon.github.io URL에서
   5b47eaf..01a7a08  master     -> origin/master
```

#### 원격 저장소의 내용으로 로컬 저장소 내용과 합치기

`merge` 명령어는 서로다른 저장소의 내용을 하나로 합칠때 사용합니다.

아래 과정은 로컬의 develop 브랜치에 내용을 원격의 develop 브랜치 내용을 가져와 합치는 작업입니다. (보통은 최신화)

> 로컬에서 가지고 있는 develop 내용이 최신화 되어 있지 않으면 변화하지 않기 때문에 fetch를 통해서 먼저 최신화 합니다.

```shell
# 로컬의 develop 브랜치로 이동
$ git checkout develop

# 원격 저장소 별칭 = origin
# 합치고자 하는 저장소 이름 = develop
$ git merge origin/develop
```


#### 원격 저장소의 내용으로 최신화 하기

`pull` 명령어를 사용하면 원격에 최신화된 정보를 로컬에 반영할 수 있습니다.

가져오는 정보는 로컬의 정보와 비교하여 변경된 부분만 가져옵니다.

실제로 `pull` 명령어는 아래 명령어 두개를 합한 동작을 합니다.  

> `pull` 명령어는 = `fetch` 명령어(최신화) + `merge` 명령어(로컬과 합치기)

```shell
$ git pull
remote: Enumerating objects: 6, done.
remote: Counting objects: 100% (6/6), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 4 (delta 2), reused 4 (delta 2), pack-reused 0
오브젝트 묶음 푸는 중: 100% (4/4), 2.01 KiB | 685.00 KiB/s, 완료.
https://github.com/Jungwoon/jungwoon.github.io URL에서
   de1ecc9..5b47eaf  master     -> origin/master
업데이트 중 de1ecc9..5b47eaf
Fast-forward
 _posts/2021-11-03-Git-Summary-2.md | 112 +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
 1 file changed, 112 insertions(+)
 create mode 100644 _posts/2021-11-03-Git-Summary-2.md
```
