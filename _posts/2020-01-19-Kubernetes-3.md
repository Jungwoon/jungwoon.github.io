---
layout: post
title: "쿠버네티스 정리 #3 - 쿠버네티스 개념 및 명령어 정리"
image: '/assets/img/'
description: 'Kubernetes Commands'
tags:
- Kubernetes
categories:
- Kubernetes
---


# 쿠버네티스 개념 및 명령어 정리


최근 개인적으로 이직을 하고 연말, 연초다 보니 포스팅이 소홀해졌는데, 새해 새마음으로 다시 포스팅을 시작하려고 합니다, 새해에 사내 스터디로
쿠버네티스를 공부하기로 했는데, 관련 내용을 공부하며, 같이 블로그에 정리를 해보고자 합니다. 
학습할 때 참고한 책은 [쿠버네티스 입문](https://book.naver.com/bookdb/book_detail.nhn?bid=16030803) 입니다.


이번 포스팅은 [조대협의 블로그 :: 쿠버네티스 #2 - 개념 이해](https://bcho.tistory.com/1256) 부분도 함께 참고 하였습니다.


---


## kubectl 명령어


쿠버네티스는 kubectl 이라는 CLI 명령어를 통해서 쿠버네티스 및 클러스터 관리, 디버그 및 트러블 슈팅들을 할 수 있습니다.
자세한 내용을 알고 싶으면 [kubectl 치트 시트](https://kubernetes.io/ko/docs/reference/kubectl/cheatsheet/)를 참고하세요


`kubectl 명령어`는 기본적으로 아래와 같은 형태로 커맨드 라인에 입력하여 사용할 수 있습니다. 


```bash
$ kubectl [command] [type] [name] [flags]
``` 

- `command` : 자원에 실행하려는 동작
    - `create` : 생성
    - `get` : 정보 가져오기
    - `describe` : 자세한 상태 정보
    - `delete` : 삭제
- `type` : 자원 타입
    - `pod` : Pod
    - `service` : 서비스
- `name` : 자원 이름
- `flag` : 옵션


## kubectl 기본 사용법

간단히 에코 서버(=클라이언트가 전송해주는 데이터를 그대로 되돌려 전송하는 서버)를 동작시키는 kubectl를 다뤄보도록 하겠습니다.


먼저 아래 명령어를 통해서 `echoserver`라는 pod를 하나 생성하겠습니다.

```bash
$ kubectl run echoserver --generator=run-pod/v1 --image="k8s.gcr.io/echoserver:1.10" --port=8080
```

각각의 내용은 좀 더 자세히 다루겠지만 간단히 설명하면 `run 명령어`는 클러스터에 특정 이미지를 가지고 pod를 생성하는 명령어 입니다.

```
$ kubectl run [생성할 POD 이름] --generator=[Repolication Controller 지정] --image=[사용할 이미지] --port=[포트정보]
```

> k8s.gcr.io는 구글의 [Container Registry](https://cloud.google.com/container-registry/)에서 가져오겠다는 의미입니다.
> --generator : Replication Controller를 지정한다고 하는데 좀 더 알아보고 다음 포스팅에 다시 말씀 드리도록 하겠습니다.

![](https://miro.medium.com/max/2360/1*uyjbTEE5GsMU3o6MdoLNnw.png)

아래 명령어를 통해서 만들어진 `echoserver` pod의 서비스를 생성하도록 하겠습니다.

```bash
$ kubectl expose pod echoserver --type=NodePort
```

![](https://miro.medium.com/max/1912/1*5o14a0IgYM2nDXyX9XePvA.png)


아래 명령어를 입력하면 pod 들의 정보를 볼 수 있습니다.

```bash
$ kubectl get pods
```

각각의 내용은

- `NAME` : Pod 이름
- `READY` : 0/1(생성되었지만 사용 준비 X) / 1/1(생성되었지만 사용 준비 O) 
- `STATUS` : Running (실행) / Terminating / ContainerCreating
- `RESTARTS` : 재시작 횟수
- `AGE` : 생성 후 지난 시간


![](https://miro.medium.com/max/1552/1*jubgZFmej--myRFTEb0Qiw.png)


현재 만들어진 서비스 정보들을 확인할 수 있습니다.

```bash
$ kubectl get services
```

각각의 내용은

- `NAME` : 서비스 이름
- `TYPE` : 서비스 타입
    - `Cluster IP` : 서비스에 클러스터 IP (내부 IP)를 할당합니다.
    - `Load Balancer` : 외부 IP를 가진 로드밸런서를 할당합니다.
    - `Node Port` : 클러스터 IP 뿐만 아니라 노드의 IP 및 포트를 통해서 접근을 할 수 있습니다.
    - `External Name` : 외부 서비스를 쿠버네티스 내부에서 호출하고자 할 때 사용할 수 있습니다.
- `CLUSTER-IP` : 클러스터 안에서 사용하는 IP
- `EXTERNAL-IP` : 외부 IP
- `PORT(S)` : 서비스에 접속하는 포트
- `AGE` : 생성 후 지난 시간 

![](https://miro.medium.com/max/2208/1*ONZ8u8ogxBriCWcO5T4HGg.png)

로컬서버의 8080 포트를 에코 서버의 8080포트로 포트 포워딩 해주기 위한 명령어

```bash
$ kubectl port-forward svc/echoserver 8080:8080
```

![](https://miro.medium.com/max/1608/1*3KtX-kUKmfieCAIr0_nloQ.png)


브라우저를 열고 아래 주소를 입력하면 잘 동작하는걸 확인할 수 있습니다.

```
http://localhost:8080
```

![](https://miro.medium.com/max/4064/1*wIxlA7D2ewpeFhjxhLOl_Q.png)

에코 서버의 실행 중 로그를 수집하려면 아래 명령어를 입력하면 확인할 수 있습니다.

```bash
$ kubectl logs -f echoserver
```

![](https://miro.medium.com/max/3280/1*N7dkm9QV8Kjn1f_z70459Q.png)

만들어진 echoserver pod를 지워보도록 하겠습니다.

```bash
$ kubectl delete pod echoserver
```

![](https://miro.medium.com/max/1812/1*AbfGTf_JJ2i6yFHK2bA6hw.png)

잘 지워졌는지 확인하기 위해서 다시한번 아래 명령어로 확인해보겠습니다. 아까는 있었던 `echoserver`가 사라진걸 확인할 수 있습니다.

```bash
$ kubectl get pods
```

![](https://miro.medium.com/max/1832/1*Mg3OmrYyhHGDTeKWR8GXVg.png)

서비스도 지워졌는지 확인하기 위해서 아래 명령어로 확인해보겠습니다. 보면 아까는 있었던 `echoserver`가 사라진걸 확인할 수 있습니다.

```bash
$ kubectl get services
```

![](https://miro.medium.com/max/2020/1*8UIZl_2FEdEgTJvRhClAsA.png)

