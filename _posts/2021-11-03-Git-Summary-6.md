---
layout: post
title: "Git 정리 #6 - 되돌리기"
image: '/assets/img/'
description: 'Git Summary #6 - Revert'
tags:
- Git
categories:
- Git
---

최근에 이직을 하고 지금까지 혼자 개발한것과 다르게 팀으로 개발을 하다가 보니까 Git을 제대로(?) 사용해야하는 상황이 많아졌습니다. 그래서
최근에 다시한번 Git 관련 책을 읽으면서 공부한 내용을 정리해보고자 합니다.

---

## 되돌리기

깃을 사용하다가 문제가 발생할 경우 되돌려야 하는 경우가 발생하는데, 지금까지 기록한 커밋을 기준으로 되돌릴 수 있습니다.

이때 사용할 수 있는게 `Reset`과 `Revert` 입니다.

테스트 와 `테스트`

---

### Reset

리셋은 커밋을 기준으로 이전 코드로 되돌리는 방법으로 기록한 커밋을 취소합니다.

먼저 아래와 같은 커밋 기록을 가지고 있습니다.

```shell
$ git log --oneline
5efbf52 (HEAD -> master) add menu5
b39917b add menu4
b120291 add menu3
5c7f27b add menu2
1e2012f init
```

여기서 두번째 전으로 리셋을 하겠습니다.(여기서는 hard를 사용하였습니다.)

> '~'를 사용하면 HEAD로부터 몇번째 전으로 돌아가는지 나타납니다. HEAD~2는 HEAD 기준으로 두번째 전이라는 의미입니다.

```shell
$ git reset --hard HEAD^2
HEAD의 현재 위치는 b120291입니다 add menu3
$ git log --oneline
b120291 (HEAD -> master) add menu3    # menu5의 두번째 전인 menu3로 돌아갔습니다.
5c7f27b add menu2
1e2012f init
```

여기서 reset의 3가지 옵션이 있는데 해당 옵션에 대해서 알아보도록 하겠습니다.

- soft : 스테이지 영역을 포함한 상태로 복원합니다. (= 변경된 내용을 커밋하기 전 까지의 내용입니다)
- mixed : 기본값
- hard : 커밋 이후에 모든 기록을 삭제해버립니다.

---

#### soft (수정 -> add -> (commit))

![](https://miro.medium.com/max/2560/1*bp_bttN5fjmRstvvI3_h-Q.png)

soft 옵션은 실제로 변경된 상태는 유지하고 커밋 포인터만 바꾸는 옵션입니다.

우선 현재 커밋 상태를 보도록 하겠습니다. 

```shell
$ git log --oneline 

aac79bd (HEAD -> master) add menu5
690ee67 add menu4
b120291 add menu3
5c7f27b add menu2
1e2012f init
```

여기서 `HEAD`의 하나 전인 `menu4`로 `SOFT 리셋`을 해보도록 하겠습니다.

```shell
$ git reset --soft HEAD~1
$ git log --oneline

690ee67 (HEAD -> master) add menu4
b120291 add menu3
5c7f27b add menu2
1e2012f init
```

그럼 뭐가 다른지 조면 변경된 상태까지는 유지 하는데 최종 커밋의 포인터만 바뀐걸 알 수 있습니다.

여기서 새로 커밋을 하게 되면 reset 하기 전과 동일한 상태가 됩니다.

```shell
$ git diff HEAD     # HEAD와 현재 스테이지 상태가 어떻게 다른지 확인 해보겠습니다.

diff --git a/test.txt b/test.txt
index e8ce8f3..b110584 100644
--- a/test.txt
+++ b/test.txt
@@ -2,3 +2,4 @@ menu1
 menu2
 menu3
 menu4
+menu5
```

---

#### mixed (수정 -> (add) -> (commit))

![](https://miro.medium.com/max/2376/1*scia9X1hN-L9A2GEpeb0sQ.png)

mixed는 변경된건 가지고 있지만 add 하기 전 상태로 놔둡니다. 그래서 commit을 하려면 add 부터 해야합니다.

우선 현재 커밋 상태를 보도록 하겠습니다.

```shell
$ git log --oneline 

aac79bd (HEAD -> master) add menu5
690ee67 add menu4
b120291 add menu3
5c7f27b add menu2
1e2012f init
```

`mixed 옵션`은 생략이 가능합니다.(=디폴트)

```shell
$ git reset HEAD~1

리셋 뒤에 스테이징하지 않은 변경 사항:
M       test.txt
```

`reset`을 한 다음에 상태를 보면 아래와 같이 수정된 파일은 남아있는데 스테이지 영역에 아직 추가되기 전 상태인걸 확인할 수 있습니다.
다시 커밋을 하기 위해선 `git add` 명령어를 통해 스테이지 영역에 넣고 커밋을 해주어야 합니다.

```shell
$ git status

현재 브랜치 master
커밋하도록 정하지 않은 변경 사항:
  (무엇을 커밋할지 바꾸려면 "git add <파일>..."을 사용하십시오)
  (use "git restore <file>..." to discard changes in working directory)
        수정함:        test.txt

커밋할 변경 사항을 추가하지 않았습니다 ("git add" 및/또는 "git commit -a"를
사용하십시오)
```

---

#### hard ((수정) -> (add) -> (commit))

![](https://miro.medium.com/max/2512/1*ixP648BrD9TOSEK-Z4wrRw.png)

hard는 아예 마지막 커밋 상태로 되돌려서 새로 만든 파일은 지우고, 수정 사항도 다 없애버립니다.

우선 현재 커밋 상태를 보도록 하겠습니다. 현재 "add menu5" 까지 되어 있는 상태입니다.

```shell
$ git log --oneline

330e1b7 (HEAD -> master) add menu5
690ee67 add menu4
b120291 add menu3
5c7f27b add menu2
1e2012f init
```

여기서 hard로 reset 해보도록 하겠습니다.

```shell
$ git reset --hard HEAD~1

HEAD의 현재 위치는 690ee67입니다 add menu4
```

그 다음 상태를 보면 다음과 같이 변화된것도 없이 마지막으로 "add menu4"로 커밋한 그 상태 그대로 되어 있는걸 알 수 있습니다.

```shell
$ git status

현재 브랜치 master
커밋할 사항 없음, 작업 폴더 깨끗함
```

---

### Revert

공개된 커밋은 보통 리셋 작업을 하지 않습니다. 이럴때는 리버트를 사용하는데 그 차이는 아래와 같습니다.

- Reset : 기존 커밋 정보 삭제
- Revert : 기존 커밋 정보 유지 & 새로운 커밋 생성

우선 현재 상태를 보도록 하겠습니다.

```shell
$ git log --oneline

0b5b4cf (HEAD -> master) add menu5
690ee67 add menu4
b120291 add menu3
5c7f27b add menu2
1e2012f init
```

여기서 마지막 커밋인 menu5를 revert 하겠습니다.

```shell
$ git revert HEAD     # HEAD를 이용한거 말고 직접 커밋ID를 써도 됩니다. ex) git revert 690ee67

# 그럼 아래와 같이 vi editor가 열리면서 나타납니다. :wq 를 눌러서 저장하고 나옵니다.
  1 Revert "add menu5"
  2 
  3 This reverts commit 0b5b4cfe29e8205734b32b30abff9149a7f405fa.
  4 
  5 # 변경 사항에 대한 커밋 메시지를 입력하십시오. '#' 문자로 시작하는
  6 # 줄은 무시되고, 메시지를 입력하지 않으면 커밋이 중지됩니다.
  7 #
  8 # 현재 브랜치 master
  9 # 커밋할 변경 사항:
 10 #   수정함:        test.txt
 11 #
:wq

[master 9383a17] Revert "add menu5"
 1 file changed, 1 deletion(-)
```

그럼 다시 커밋 트리의 상태를 보겠습니다. 그럼 아래와 같이 `0b5b4cf add menu5` 가 사라지는게 아니라 그 위로
`새로운 커밋`이 생성된걸 확인할 수 있습니다.

```shell
$ git log --oneline

9383a17 (HEAD -> master) Revert "add menu5"
0b5b4cf add menu5   # 삭제가 되지 않음
690ee67 add menu4
b120291 add menu3
5c7f27b add menu2
1e2012f init
```

여기서 파일의 상태를 보면 menu4 까지만 있는걸 확인할 수 있습니다.

```shell
$ vi test.txt

  1 menu1
  2 menu2
  3 menu3
  4 menu4
```

