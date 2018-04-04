---
layout: post
title: "Linux 서버에 JIRA 설치하기"
image: '/assets/img/'
description: 'Linux 서버에 JIRA 설치하기'
tags:
- JIRA
categories:
- JIRA
---


## JIRA 설치하기

이번에 JIRA를 설치할일이 있어서 한번 설치 과정을 저장해보려고 합니다

[JIRA 아카이브 다운로드 페이지](https://ko.atlassian.com/software/jira/download-archives)에서
해당하는 부분을 다운로드 받습니다.

---

### JIRA 다운로드 (*.bin)

```bash
# 2018.04.04. 기준 최신
$ wget http://www.atlassian.com/software/jira/downloads/binary/atlassian-jira-software-7.8.2-x64.bin
```

![](https://cdn-images-1.medium.com/max/1000/1*-qhfMsASpxbXSBURdlTj4w.png)

![](https://cdn-images-1.medium.com/max/1000/1*cN-GNGP9rPaUr6aeGeaH8Q.png)

---

### 권한 변경

```bash
$ chmod a+x atlassian-jira-software-7.8.2-x64.bin
```

![](https://cdn-images-1.medium.com/max/1000/1*HSYDJjUZlG2sJ5r2LA_bpg.png)

### ** chmod 명령어 사용법

```bash
$ chmod [레퍼런스] [연산자] [모드] 파일

ex) chmod u+x my_file
```

| 레퍼런스 | 클래스 | 설명
| :---: | :---: | :---
| u	| 사용자	|파일의 소유자
| g	| 그룹	 | 그 파일의 그룹 멤버인 사용자
| o	| 다른 사람들 | 그 파일의 소유자나 혹은 그 그룹의 멤버가 아닌 사용자
| a | 모든 사람 | 위의 셋 모두, "ugo"와 같다

| 연산자 | 설명
| :---: | :---
|+	| 지정된 모드들은 지정된 클래스들에 더한다
|-	| 지정된 클래스들로부터 지정된 모드들은 지운다
|=	| 지정된 클래스들을 위해서 지정된 모드들이 정확한 모드들로 만들어지게 된다

| 모드 | 이름 | 설명
| :---: | :--- | :---
|r | 읽기 (read) | 파일을 읽거나 디렉터리 안 내용물의 리스트를 보여준다
|w | 쓰기 (write) | 파일이나 디렉터리에 쓴다
|x | 실행하기 (excute) | 파일을 실행하거나 디렉터리 트리로 되돌아간다

---

### 설치

```bash
$ ./atlassian-jira-software-7.8.2-x64.bin
```

![](https://cdn-images-1.medium.com/max/1000/1*9A3Si_WXtukoI1qz1WBQxg.png)

![](https://cdn-images-1.medium.com/max/1000/1*AB55H-An32TbN7X4_QfORQ.png)

---

### 설정

`서버 주소:8080`으로 들어가시면 아래와 같은 화면을 볼 수 있습니다.

`사용자를 위해 설정하십시오`를 선택하고 `MyAtlassian으로 계속` 버튼을 누릅니다

![](https://cdn-images-1.medium.com/max/1000/1*oxn_E4u3IyIM6wOd-vd_gg.png)

다음 화면에서 결제한 본인의 계정으로 로그인을 합니다

![](https://cdn-images-1.medium.com/max/1000/1*WT7eheK8jSHutnkc_pIn9Q.png)

아래와 같은 화면이 나옵니다.

![](https://cdn-images-1.medium.com/max/1000/1*hS92vL24Aq7K5CBGQFhFlQ.png)

여기서는 `JIRA Software(Server)`를 선택하고 `Generate License`를 누르도록 하겠습니다.

![](https://cdn-images-1.medium.com/max/1000/1*KX0C8YKu55tUjox2kk-9YQ.png)

그 다음 `관리자 계정 설정`이 나오는데 관리자로 추가할 계정을 넣고 `다음`을 누릅니다

![](https://cdn-images-1.medium.com/max/1000/1*2utMOGXgMXu7b1-_s04L5A.png)

언어를 선택하고 `Continue`를 누릅니다.

![](https://cdn-images-1.medium.com/max/1000/1*ua88dorH_pQGy77UUNPFAQ.png)

그럼 아래와 같이 설치가 진행이 됩니다.

![](https://cdn-images-1.medium.com/max/1000/1*Gk-RK2yKQ_L-CvRjuIrdZQ.png)

완료가 되면 아래와 같은 화면이 나오는데, `시작해봅시다.`를 눌러서 계속 진행합니다.

![](https://cdn-images-1.medium.com/max/1000/1*Z0GpULlFKGpflEUakBp3uA.png)

이렇게 나오면 `아바타 선택`을 누릅니다

![](https://cdn-images-1.medium.com/max/1000/1*7Bpk3w_GCtjlm4tlpKsNMQ.png)

원하는 아바타를 고르고 `닫기`를 누릅니다

![](https://cdn-images-1.medium.com/max/1000/1*oo2p94qgY6Mo5Op3Qu7-ag.png)

그럼 아바타가 선택된 것을 확인하실 수 있습니다. `다음`을 누릅니다.

![](https://cdn-images-1.medium.com/max/1000/1*VyRaGhkBie3Bmsn1DybJkw.png)

그럼 다음과 같은 화면이 나오면서 세팅이 완료가 됩니다.

![](https://cdn-images-1.medium.com/max/1000/1*lSBR7SKgF-WWCdPR-7RuRA.png)


## 마무리

다음 포스팅에는 `Bitbucket 설치하는 법`과 `JIRA와 Bitbucket 연동하기` 부분을 포스팅할 예정입니다.