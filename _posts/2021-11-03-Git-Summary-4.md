---
layout: post
title: "Git 정리 #4 - 병합"
image: '/assets/img/'
description: 'Git Summary #3 - Merge & Rebase'
tags:
- Git
categories:
- Git
---

최근에 이직을 하고 지금까지 혼자 개발한것과 다르게 팀으로 개발을 하다가 보니까 Git을 제대로(?) 사용해야하는 상황이 많아졌습니다. 그래서
최근에 다시한번 Git 관련 책을 읽으면서 공부한 내용을 정리해보고자 합니다.

---

## 병합

예전에는 개발이 끝나면 일일이 수작업으로 변경된 파일을 병합을 하였는데, 이는 프로젝트가 커지면 커질수록 점점 더 난이도가 높아지고 잦은 실수가 발생하였습니다.

Git 의 머지를 이용하면 브랜치를 사용하여 원본 코드에 영향을 주지 않고 분리해서 개발하고 다시 원본 코드에 합쳐야 하는 작업을 자동으로 해줍니다.

---

## 병합 종류

Git의 병합 종류에는 크게 2가지로 나눠집니다.

### Fast-Forward 병합

가장 간단한 병합 방법 중 하나로 순차적으로 작업이 진행될때 사용하는 방법입니다.

![](https://miro.medium.com/max/1400/1*0St5in6-WX11CrKG6ORTeQ.png)

위의 예에서는 `feature` 라는 하나의 브랜치가 `master` 에서 파생되었지만 순차적으로 커밋을 했기 때문에 일직선으로 보입니다. 
이런 상황에서는 `Fast-Forward 병합` 을 사용하여 병합을 하게 됩니다. 이렇게 되면 단순히 커밋 위치를 최신으로 옮기것봐 비슷하게 됩니다.

```shell
$ git checkout master
(master) $ git merge feature

업데이트 중 d90f0d7..07fb7d6
Fast-forward        # Fast-forward 알고리즘으로 병합했기 때문에 다음과 같이 나옵니다.
 sample.txt | 1 +
 1 file changed, 1 insertion(+)

```


### 3-way 병합

또 다른 병합 알고리즘 방법으로 작업이 병렬로 진행될때 사용하는 방법입니다.

![](https://miro.medium.com/max/1400/1*CcKdQuqpsMGsqHSA9CmBJA.png)

위의 예에서는 `Feature 브랜치` 의 마지막 변경인 `수정3`과 공통 조상인 원본을 기준으로 병합한 `Temp1`을 먼저 만들고,
그 다음 `Master 브랜치`의 마지막 변경인 `수정2`와 공통 조상인 원본을 기준으로 병합한 `Temp2`를 만들어서
`Temp1`과 `Temp2`를 가지고 병합을 합니다.


```shell
$ git checkout master
(master) $ git merge feature

Merge made by the 'recursive' strategy.     # 3-Way 알고리즘으로 병합했기 때문에 메시지가 다르게 나옵니다.
 a.txt | 1 +
 1 file changed, 1 insertion(+)

```

### 충돌

`Fast-Forward 병합`일때는 순차적으로 변화하기 때문에 충돌이 날일이 없지만 `3-Way 병합`일때는 동일한 파일의 동일한 코드를
서로 다른 사람이 수정할 수 있기 때문에 이런 경우에는 `Git`도 어느게 맞는건지 알 수 없기 때문에 충돌이 발생합니다. 이럴 경우에는
직접 코드를 수정해주어야 합니다.

```shell
(master) $ git merge feature

자동 병합: sample.txt
충돌 (내용): sample.txt에 병합 충돌
자동 병합이 실패했습니다. 충돌을 바로잡고 결과물을 커밋하십시오.
```

이럴 경우 `git status` 명령어로 어느 파일에서 문제가 발생했는지 알 수가 있습니다.

```shell
(master) $ git status
현재 브랜치 master
병합하지 않은 경로가 있습니다.
  (충돌을 바로잡고 "git commit"을 실행하십시오)
  (병합을 중단하려면 "git merge --abort"를 사용하십시오)

병합하지 않은 경로:
  (해결했다고 표시하려면 "git add <파일>..."을 사용하십시오)
        양쪽에서 수정:  sample.txt

```

해당 `sample.txt`로 가면 아래와 같이 나타납니다.

```shell
  1 a
  2 <<<<<<< HEAD        # 현재 변경 사항
  3 c
  4 =======
  5 b
  6 >>>>>>> feature     # 병합하고자 하는 브랜치의 변경 사항
```

이 경우 `c`가 맞는지 `b`가 맞는지 확인하여 수정하고 파일을 저장해줍니다.
그 다음 `git commit` 명령어를 다시 해줍니다.

```shell
(master) $ git add .
(master) $ git commit
[master a9fb8cc] Merge branch 'feature'
```