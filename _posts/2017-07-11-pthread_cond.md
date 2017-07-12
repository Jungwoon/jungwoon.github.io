---
layout: post
title: "PTHREAD_COND 내용 정리"
image: '/assets/img/'
description: '스레드 동기화 함수'
tags:
- Thread
- Linux
- Synchronize
categories:
- Linux
---

## PTHREAD_COND 내용 정리

### 사용방법

```c
#include <pthread.h>
pthread_cond_t cond = PTHREAD_COND_INITIALIZER;

int pthread_cond_init(pthread_cond_t *cond, pthread_condattr_t *cond_attr);
int pthread_cond_signal(pthread_cond_t *cond);
int pthread_cond_broadcast(pthread_cond_t *cond);
int pthread_cond_wait(pthread_cond_t *cond, pthread_mutex_t *mutex);
int pthread_cond_timedwait(pthread_cond_t *cond, pthread_mutex_t *mutex, const struct timespec *abstime);
int pthread_cond_destroy(pthread_cond_t *cond);
```

### 설명

`pthread_cond_t(조건변수)` : 공유 데이터에 대한 특정 조건에 따라 스레드의 실행을 중지하거나 다시 실행시키는 역할을 하는 동기화 장치

### 기본적인 연산
조건변수에 시그널을 보낸다(특정 조건이 만족된 경우), 조건변수를 기다린다(다른 스레드가 해당 조건 변수에 시그널을 보낼 때까지 스레드의 실행이 중지됨)

조건변수는 한 스레드가 조건 변수를 기다릴 준비를 하는 동안 (실제로 대기하기 전에) 다른 스레드가 해당 조건변수에 시그널을 보내는 것과 같은 경쟁 상태를 방지하기 위해 항상 뮤텍스와 함께 사용되야 한다.

### 메서드 설명

```c
int pthread_cond_init(pthread_cond_t *cond, pthread_condattr_t *cond_attr)
```
: pthread_cond_init는 조견변수 (condition variable)cond를 초기화하기 위해서 사용한다. attr 를 이용해서 조건변수의 특성을 변경할수 있으며, NULL 을 줄경우 기본특성으로 초기화된다.

```c
pthread_cond_t cond = PTHREAD_COND_INITIALIZER;
or
pthread_cond_init(&cond, NULL);
```
: 조건변수 cond는 상수 PTHREAD_COND_INITIALIZER 을 이용해서도 초기화 할수 있다. 즉 다음과 같은 2가지 초기화 방법이 존재한다.

```c
int pthread_cond_signal(pthread_cond_t *cond)
```
: 조건변수 cond에 시그널을 보낸다. 시그널을 보낼경우 cond에서 기다리는(wait) 스레드가 있다면 스레드를 깨우게 된다(봉쇄가 풀림). 만약 조건변수 cond를 기다리는 스레드가 없다면, 아무런 일도 일어나지 않게되며, `여러개의 스레드가 기다리고 있다면 그중 하나의 스레드에게만 전달된다. 이때 어떤 스레드에게 신호가 전달될지는 알수 없다.`

```c
int pthread_cond_broadcast(pthread_cond_t *cond)
```
: 조건변수 cond에서 기다리는(wait) `모든 스레드에게 신호를 보내서, 깨운다`는 점을 제외하고는 pthread_cond_signal과 동일하게 작동한다.

```c
int pthread_cond_wait(pthread_cond_t *cond, pthread_mutex_t *mutex)
```
: 조건변수 cond를 통해서 신호가 전달될때까지 블럭된다. 만약 신호가 전달되지 않는다면 영원히 블럭될수도 있다. pthread_cond_wait는 블럭되기 전에 mutex 잠금을 자동으로 되돌려준다.

```c
int pthread_cond_timedwait(pthread_cond_t *cond, pthread_mutex_t *mutex, const struct timespec *abstime)
```
: 조건변수 cond를 통해서 신호가 전달될때까지 블럭되며 자동으로 mutex을 돌려주는 점에서는 pthread_cond_wait와 동일하다. 그러나 `시간체크가 가능해서 abstime시간동안 신호가 도착하지 않는다면 error 를 발생하면서 리턴한다.` 이때 리턴값은 ETIMEDOUT 이다. errno 가 세팅되는게 아닌, 리턴값으로 에러가 넘어오는것에 주의해야 한다.
또한 pthread_cond_timedwait함수는 다른 signal 에 의해서 interrupted 될수 있으며 이때 EINTR 을 리턴한다. 이 함수를 쓸때는 interrupted 상황에 대한 처리를 해주어야 한다.

```c
int pthread_cond_destroy(pthread_cond_t *cond)
```
: pthread_cond_init를 통해서 생성한 조건변수cond에 대한 자원을 해제한다. destroy 함수를 호출하기 전에 어떤 스레드도 cond에서의 시그널을 기다리지 않는걸 확인해야 한다. 만약 cond 시그널을 기다리는 스레드가 존재한다면 이 함수는 실패하고 EBUSY 를 리턴한다.
