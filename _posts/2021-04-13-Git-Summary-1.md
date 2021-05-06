---
layout: post
title: "Git 정리 #1 - 로컬"
image: '/assets/img/'
description: 'Git Summary #1 - Local'
tags:
- Git
categories:
- Git
---

최근에 이직을 하고 지금까지 혼자 개발한것과 다르게 팀으로 개발을 하다가 보니까 Git을 제대로(?) 사용해야하는 상황이 많아졌습니다. 그래서
최근에 다시한번 Git 관련 책을 읽으면서 공부한 내용을 정리해보고자 합니다.

---

## Git 이란?

2005년에 리누스 토발즈에 의해서 개발된 대표적인 버전 관리 시스템입니다. (아마 현재는 가장 많이 쓰고 있지 않을까 합니다)

크게 Local과 Remote로 이루어진 Repository를 별도로 가지고 작업을 할 수 있기 때문에 네트워크가 연결되어 있지 않은 상태에서도 자신의 
컴퓨터에서 작업을 할 수 있습니다.


## Git 설치

Mac을 사용한다면 기본적으로 Git은 설치가 되어 있어서 바로 사용이 가능합니다. 

Windows 환경에서는 깃 [공식사이트](https://git-scm.com)에서 직접 다운받아 이용할 수 있습니다. 

## Git 명령어

Git의 버전을 확인하는 명령어

```shell
$ git --version
git version 2.31.0
```
Git에 있는 명령어들을 확인하는 명령어

```shell
$ git help

사용법: git [--version] [--help] [-C <path>] [-c <name>=<value>]
           [--exec-path[=<path>]] [--html-path] [--man-path] [--info-path]
           [-p | --paginate | -P | --no-pager] [--no-replace-objects] [--bare]
           [--git-dir=<path>] [--work-tree=<path>] [--namespace=<name>]
           [--super-prefix=<path>] [--config-env=<name>=<envvar>]
           <command> [<args>]

다음은 여러가지 상황에서 자주 사용하는 깃 명령입니다:

작업 공간 시작 (참고: git help tutorial)
   clone             저장소를 복제해 새 디렉터리로 가져옵니다
   init              빈 깃 저장소를 만들거나 기존 저장소를 다시 초기화합니다

변경 사항에 대한 작업 (참고: git help everyday)
   add               파일 내용을 인덱스에 추가합니다
   mv                파일, 디렉터리, 심볼릭 링크를 옮기거나 이름을 바꿉니다
   restore           Restore working tree files
   rm                파일을 작업 폴더에서 제거하고 인덱스에서도 제거합니다
   sparse-checkout   Initialize and modify the sparse-checkout

커밋 내역과 상태 보기 (참고: git help revisions)
   bisect            이진 탐색으로 버그를 만들어낸 커밋을 찾습니다
   diff              커밋과 커밋 사이, 커밋과 작업 내용 사이 등의 바뀐 점을 봅니다
   grep              패턴과 일치하는 줄을 표시합니다
   log               커밋 기록을 표시합니다
   show              여러가지 종류의 오브젝트를 표시합니다
   status            작업 폴더 상태를 표시합니다

커밋 내역을 키우고, 표시하고, 조작하기
   branch            브랜치를 만들거나, 삭제하거나, 목록을 출력합니다
   commit            바뀐 사항을 저장소에 기록합니다
   merge             여러 개의 개발 내역을 하나로 합칩니다
   rebase            커밋을 다른 베이스 끝의 최상위에서 적용합니다
   reset             현재 HEAD를 지정한 상태로 재설정화합니다
   switch            Switch branches
   tag               태그를 만들거나, 표시하거나, 삭제하거나, GPG 서명을 검증합니다

협동 작업 (참고: git help workflows)
   fetch             다른 저장소에서 오브젝트와 레퍼런스를 다운로드합니다
   pull              다른 저장소 또는 다른 로컬 브랜치에서 가져오거나 통합합니다
   push              원격 레퍼런스 및 그와 관련된 오브젝트를 업데이트합니다

'git help -a' and 'git help -g' list available subcommands and some
concept guides. See 'git help <command>' or 'git help <concept>'
to read about a specific subcommand or concept.
See 'git help git' for an overview of the system.
```

---

## Git 환경설정하기

가장 먼저 Git의 기본적인 사용자 등록 방법입니다. (Local)

```shell
$ git config user.name "Jungwoon"              # 사용자 이름
$ git config user.email "byjungwoon@gmail.com" # 사용자 메일
```

만약에 설정한걸 취소하고 싶다면 `--unset`을 아래와 같이 사용합니다.

```shell
$ git config --unset user.name
$ git config --unset user.email
```

Git의 글로벌 사용자 등록 방법입니다. (Global)

```shell
$ git config --global user.name "Jungwoon"
$ git config --global user.email "byjungwoon@gmail.com"
```

이것도 역시 취소하고 싶다면 `--unset`을 사용합니다.

```shell
$ git config --unset --global user.name
$ git config --unset --global user.email
```

Git에 설정된 내용을 확인하고 싶으면 아래 명령어로 확인할 수 있습니다.

```shell
$ git config --list
```

아래와 같이 출력이 됩니다.

```text
credential.helper=osxkeychain
core.excludesfile=/Users/jungwoon/.gitignore_global
difftool.sourcetree.cmd=opendiff "$LOCAL" "$REMOTE"
difftool.sourcetree.path=
mergetool.sourcetree.cmd=/Applications/Sourcetree.app/Contents/Resources/opendiff-w.sh "$LOCAL" "$REMOTE" -ancestor "$BASE" -merge "$MERGED"
mergetool.sourcetree.trustexitcode=true
user.name=Jungwoon
user.email=byjungwoon.gmail.com
core.repositoryformatversion=0
```

---

## Git 세팅하기

Git을 사용하고 싶다면 아래 명령어로 관리하고 싶은 디렉토리 안에서 Git을 초기화 해야 합니다.

```shell
$ git init
```

그럼 내부적으로 `.git`이란 숨겨진 디렉토리가 생기면서 설정이 완료됩니다.
(`.git` 디렉토리를 삭제하면 설정도 다 지워지게 됩니다.)

```shell
$ ls -al

total 0
drwxr-xr-x   3 jungwoon  staff    96 May  6 22:33 .
drwxr-xr-x+ 47 jungwoon  staff  1504 May  6 22:34 ..
drwxr-xr-x  12 jungwoon  staff   384 Apr 14 21:28 .git
```

이렇게 되면 `워킹 디렉토리`란 영역으로 바뀌며 Git이 관리를 하는 영역으로 변경됩니다.

```shell
$ mkdir test # test 디렉토리를 만듭니다.
$ cd test    # test 디렉토리로 이동합니다.
$ git init   # test 디렉토리를 git으로 관리하기 위해 초기화 합니다.
/Users/jungwoon/test/.git/ 안의 빈 깃 저장소를 다시 초기화했습니다
$ ls -al     # 디렉토리 안을 보면 .git 이 생긴걸 확인할 수 있습니다.
total 0
drwxr-xr-x   3 jungwoon  staff    96 Apr 14 00:16 .
drwxr-xr-x+ 40 jungwoon  staff  1280 Apr 14 00:23 ..
drwxr-xr-x   9 jungwoon  staff   288 Apr 14 00:16 .git
```
`git status 명령어`를 사용하면 아래와 같이 현재 git의 상태를 알 수 있습니다.

```shell
$ echo "Hello World" > test.txt   # "Hello World"란 글자를 가진 test.txt 파일을 만듭니다.
$ ls                              # 잘 생성될걸 확인할 수 있습니다.
test.txt
$ cat text.txt                    # 확인해보니 글자가 잘 들어가있죠?
Hello World
$ git status                      # Git의 상태를 보니 새로 만들어져 추적하지 않은 test.txt 파일이 있는걸 알 수 있습니다.

현재 브랜치 master

아직 커밋이 없습니다

추적하지 않는 파일:                    # 이 부분을 보면 아직 tracked 되어있지 않았다고 나옵니다.
  (커밋할 사항에 포함하려면 "git add <파일>..."을 사용하십시오)
        test.txt

커밋할 사항을 추가하지 않았지만 추적하지 않는 파일이 있습니다 (추적하려면 "git
add"를 사용하십시오)
```

---

## Git의 상태

위에서 나온 상태는 git이 변화를 추적하는 파일인지 아닌지의 상태입니다.

- tracked(=추적) : Git이 파일 내부의 변화를 감지
- untracked(=추적하지 않는) : Git이 파일 내부의 변화를 감지하지 않음 

기본적으로 파일이 생성될때 바로 추적하지는 않고 `add 명령어`를 통해서 특정 파일을 추적하겠다고 등록하면 추적을 하게 됩니다.

```shell
$ git add test.txt  # 추적하고자 하는 파일 경로
$ git status        # 잘 변경이 되었는지 Git의 상태를 확인해봅니다.

현재 브랜치 master

아직 커밋이 없습니다

커밋할 변경 사항:        # 위에서 add를 했기 대문에 tracked 되고 있다고 나옵니다.
  (스테이지 해제하려면 "git rm --cached <파일>..."을 사용하십시오)
        새 파일:       test.txt
```

Git에는 또 다른 아래 두 가지 영역이 있습니다.

- STAGE 영역 : 작업이 끝난 파일이 저장되는 임시 영역 (생성/수정 완료 후 등록된 상태 = 수정 후 Commit 된 상태)
- UNSTAGE 영역 : 작업이 끝나기 전의 영역 (등록되지 않거나 등록 후 수정된 상태 = 수정 후 Commit 안 된 상태) 

일단 위의 개념은 저런게 있구나 하고 넘어가도록 하겠습니다.

왜냐면 아래 두가지 상태가 더 있기 때문입니다.

- modified(=수정된 상태) : Commit 후 수정이 된 상태
- unmodified(=수정되지 않은 상태) : Commit 후 수정이 없는 상태

위에서 이미 사용했지만 Git의 상태를 확인하는 명령어는 아래 명령어입니다.

```shell
$ git status

현재 브랜치 master

아직 커밋이 없습니다

커밋할 변경 사항:      # add를 해서 tracked 되었기 때문에 commit을 하라고 나옵니다.
  (스테이지 해제하려면 "git rm --cached <파일>..."을 사용하십시오)
        새 파일:       test.txt
```

추적하기 전 상태(tracked -> untracked 상태)로 변경하기 위해서는 `--cached 옵션`을 사용해야 합니다.

그럼 스테이지 영역에 등록되어서 tracked된 파일을 untracked 상태로 변경할 수 있습니다.

```shell
$ git rm --cached test.txt # git rm 명령어에 --cached 옵션을 추가합니다.
rm 'test.txt'
$ git status

현재 브랜치 master
커밋할 변경 사항:
  (use "git restore --staged <file>..." to unstage)
        삭제함:        test.txt

추적하지 않는 파일:
  (커밋할 사항에 포함하려면 "git add <파일>..."을 사용하십시오)
        test.txt

$ ls -al
drwxr-xr-x   4 jungwoon  staff   128 Apr 14 00:47 .
drwxr-xr-x+ 40 jungwoon  staff  1280 Apr 14 21:29 ..
drwxr-xr-x  12 jungwoon  staff   384 Apr 14 21:28 .git
-rw-r--r--   1 jungwoon  staff    16 Apr 14 00:47 test.txt
```

단 한 번 이라도 `커밋을 했다면` `rm`이 아니라 `reset` 명령어를 사용해야 합니다

```shell
$ git reset test.txt  # 추적을 취소하고자 하는 파일 경로
```

---

## Commit(=작업 완료)

작업을 하고 작업이 완료된 시점을 저장해야 하는데 이 부분을 Commit으로 이해하면 좋습니다.
그럼 그때 시점으로 저장을 하게 됩니다.

- Commit : 변경된 상태를 기록하는 상태

각각의 커밋 시점에서 얼마만큼 변경되었는지 기록이 되며 파일이 tracked 된 상태에서만 Commit을
할 수 있습니다. 커밋 명령어는 아래와 같습니다.

```shell
$ git commit
```

이렇게 하면 Vi Editor가 열리면서 Commit Message를 쓰라고 합니다.

만약에 Vi Editor를 열지않고 바로 메시지를 넣으려면 `-m` 옵션을 같이 사용합니다.

```shell
$ git commit -m "First Commit" # 따옴표 안에 커밋 메시지를 넣습니다.
```

커밋을 하기 위해서는 반드시 수정된 파일들이 Stage 영역에 있어야 하는데 그러기 위해서는
매번 `git add 명령어`를 입력해야 합니다. 이 부분을 한번에 해결해주는 옵션이 `-a` 옵션입니다.

```shell
$ git commit -a # 스테이지 영역에 등록되지 않은 파일들을 자동으로 등록하고 커밋
```

그럼 위의 두 개를 조합해보겠습니다. 아래와 같이 명령어를 입력하면 수정된 파일들을 자동으로 등록하면서
커밋 메시지도 한번에 넣어서 커밋을 할 수 있게 됩니다.

```shell
$ git commit -am "First Commit"
```

커밋의 기록을 보고 싶으면 아래 메세지를 이용합니다.

```shell
$ git log
commit 47aa28b69059418f05acc9e2cb72197f3cc25558 (HEAD -> master, origin/master, origin/HEAD)
Author: 박정운 <byjungwoon@gmail.com>
Date:   Tue Apr 13 00:37:47 2021 +0900

    posting

commit c5f7be0132c2e0f00232d6ca73de329737749b42
Author: 박정운 <byjungwoon@gmail.com>
Date:   Mon Apr 12 22:13:43 2021 +0900
...
```

만약 커밋 기록을 좀 더 간략히 보고 싶으면 아래와 같이 `--oneline` 옵션을 사용합니다.

나갈때는 `q`를 누릅니다.

```shell
$ git log --oneline

80866bd (HEAD -> master) posting
c5f7be0 change syntax css
6761de4 change syntax css
62e3b5c change syntax css
0c0c66f change syntax css
```

만약 커밋 메시지를 잘못 입력했을때에는 아래 명령어를 입력하여 `Vi Editor`를 이용하여 수정할 수 있습니다.

```shell
$ git commit --amend
```
---
## Git의 포인터

이번에는 Git의 포인터에 대해서 알아보도록 하겠습니다.

- HEAD : HEAD는 커밋을 가리키는 포인터

Git을 설치하고 처음 커밋할때는 HEAD의 포인터가 없습니다. 최소한 한 번 이상 커밋을 해야만 HEAD가 존재합니다.

그리고 HEAD는 커밋할 때 마다 한 단계씩 이동합니다. 결과적으로 `HEAD는 커밋이 변화한 최신 포인터`입니다.