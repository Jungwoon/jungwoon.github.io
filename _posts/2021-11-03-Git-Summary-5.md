---
layout: post
title: "Git 정리 #5 - 리베이스"
image: '/assets/img/'
description: 'Git Summary #5 - Rebase'
tags:
- Git
categories:
- Git
---

최근에 이직을 하고 지금까지 혼자 개발한것과 다르게 팀으로 개발을 하다가 보니까 Git을 제대로(?) 사용해야하는 상황이 많아졌습니다. 그래서
최근에 다시한번 Git 관련 책을 읽으면서 공부한 내용을 정리해보고자 합니다.

---

## 리베이스

브랜치를 합치는 방법에는 크게 `Merge`와 `Rebase`가 있는데 이번에는 `Rebase`라고 하는 커밋 순서를 재배열하여 병합하는 방법에 대해서 포스팅해보고자 합니다.

`Rebase`는 커밋의 트리 구조를 재배열하여 병합합니다. 이렇게 함으로써 많아진 브랜치를 정리하고 단순화 할 수 있습니다.

---

### 리베이스와 병합의 차이

일단 둘의 차이는 새로운 커밋을 만드는지 안만드는지에 따라 달라집니다.

![](https://miro.medium.com/max/2848/1*EyqP92Xl9uDR5Mi0SsObrg.png)

머지 같은 경우엔 공통 조상을 찾아서 `3-way 방식`으로 병합을 하여 최종적으로 새로운 커밋을 생성합니다. 

![](https://miro.medium.com/max/3060/1*dwR1w4W0AJAPB3Z09yVEiw.png)

리베이스는 두 브랜치를 비교하지 않고 순차적으로 커밋 병합을 합니다. 위의 예에서 파생된 브랜치의 `변화점(=diff)`을 임시공간에 보관해놓고 베이스를 커밋6으로
병경하고 임시 저장된 브랜치의 커밋(커밋3, 커밋4)를 그 뒤에 붙입니다. 이렇게 함으로써 새로운 커밋을 만들지도 않고 커밋트리가 단순해집니다.

### 명령어

리베이스는 병합되는 브랜치 방향이 머지와 반대입니다. 그래서 먼저 리베이스 하고자 하는 파생 브랜치로 checkout을 하고
그 다음 원본브랜치인 master 브랜치로 리베이스를 합니다.

```shell
$ git checkout feature
(feature) $ git rebase master
```

만약 리베이스 중간에 에러가 발생하는 경우에는 아래와 같이 에러가 나는데

```shell
(feature) $ git rebase master
자동 병합: test.txt
충돌 (내용): test.txt에 병합 충돌
error: 다음을 적용할(apply) 수 없습니다: 1da1170... edit1
Resolve all conflicts manually, mark them as resolved with
"git add/rm <conflicted_files>", then run "git rebase --continue".
You can instead skip this commit: run "git rebase --skip".
To abort and get back to the state before "git rebase", run "git rebase --abort".
Could not apply 1da1170... edit1
```

해당 부분을 수정을 한다음에 아래 명령어로 수정을 합니다.

```shell
$ git add .
$ git rebase --continue
[HEAD 분리됨 ef8d57e] edit1
 1 file changed, 1 insertion(+)
Successfully rebased and updated refs/heads/feature.
```

대신 만약에 리베이스를 중단하고 싶으면 아래 명령어로 중단할 수도 있습니다.

```shell
$ git rebase --abort
```

> 리베이스 할 때 주의 점 : 리베이스는 커밋 위치와 해시 값을 변경 하기 때문에 저장소를 외부에 공개하면 리베이스를 사용하지 않는 것이 원칙입니다. 