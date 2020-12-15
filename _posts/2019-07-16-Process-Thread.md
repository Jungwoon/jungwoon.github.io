---
layout: post
title: "안드로이드 프로세스 및 스레드 (Android Process and Thread)"
image: '/assets/img/'
description: '안드로이드 프로세스 및 스레드 (Android Process and Thread)'
tags:
- Android
categories:
- Android
---

이번에 안드로이드 프로세스 및 스레드 개념에 대해서 다시 공부할 일이 있었는데 해당 내용을 포스팅해놓으면 좋을거 같아서 포스팅하겠습니다.
해당 내용은 안드로이드 공식 홈페이지의 [프로세스 및 스레드](https://developer.android.com/guide/components/processes-and-threads)를 
참고하여 작성하였습니다.


기본적으로 안드로이드는 하나의 프로세스와 하나의 스레드로 어플리케이션을 실행합니다. 이때 스레드를 `메인 스레드(=UI 스레드)`라고 합니다. 
이를 변경하기 위해서는 매니페스트의 `android:process` 특성을 통해서 변경할 수 있습니다. 

안드로이드는 메모리가 부족하거나 사용자에게 더욱 즉각적인 서비스를 제공하기 위해서 프로세스의 종료를 결정할 수 있습니다. 이때 여러 우선순위를 통해서 결정되는데 
대표적으로 현재 앱이 포그라운드에 있는지 백그라운드에 있는지에 따라 달라질 수 있습니다.

리눅스의 프로세스와 스레드와 거의 동일한 개념을 가지기 때문에 그 부분을 통해서 다뤄보도록 하겠습니다. 

---

### OOM(Out Of Memory) vs LMK (Low Memory Killer)

- `OOM(Out Of Memory)` : 프로그램이나 운영체제에서 사용할 메모리를 할당하지 못하는 컴퓨터 동작 상태입니다. 리눅스는 `MMU(Memory Management Unit)`이란 
하드웨어를 이용하여 가상 메모리를 물리 메모리로 변환하여 사용하는데, 가상 메모리로 사용하기 때문에 물리 메모리보다 더 큰 메모리를 사용할 때 `OOM`이 발생합니다.
- `LMK(Low Memory Killer)` : `Linux Kernel`의 `OOM Killer`가 안드로이드의 주요 서비스나 어플리케이션을 죽일 수 있는 문제를 보완하기 위해, 
가용 메모리가 설정된 6개의 임계치(`minifree`) 이하로 떨어지면 해당 그룹 별로 프로세스를 죽이도록 설정한 기능으로 OOM 전에 여유 메모리를 확보하기 위한 안드로이드에서
추가된 모듈입니다.

---

### 프로세스(Process) vs 스레드(Thread)

여기서는 프로세스와 스레드의 차이를 한번 알아보도록 하겠습니다.

> 해당 내용은 [Heee's Development Blog](https://gmlwjd9405.github.io/2018/09/14/process-vs-thread.html)를 참고헸습니다.

---

####  프로세스(Process)

![](https://miro.medium.com/max/1000/1*UhN3WWDfMTXkV0l4IXbAtg.png)

- 의미
    - 메모리에 올라와 실행되고 있는 프로그램 인스턴스
    - 운영체제로부터 시스템 자원을 할당받는 작업의 단위
    - 실행된 프로그램
- 시스템으로 부터 할당받는 자원
    - CPU 시간
    - 주소 공간
    - `Stack`, `Heap`, `Data`, `Text`의 구조로 되어 있는 독립된 메모리 영역
- 특징
    - 프로세스는 `각각 독립된 메모리 영역(Stack, Heap, Data, Text)`을 할당 받습니다.
    - 프로세스당 최소 `하나 이상의 스레드(=메인 스레드)`를 갖습니다.
    - 각각의 프로세스는 별도의 주소 공간에서 실행되며, 한 프로세스는 다른 프로세스의 변수나 자료구조에 접근할 수 없습니다.
    - 독립된 공간이기 때문에 한 프로세스가 다른 프로세스의 자원에 접근하려면 프로세스간 통신(IPC)를 사용해야 합니다. 

위에는 간단히 프로세스의 개념을 보는 내용이었고 아래는 좀 더 자세한 구조에 대해서 알아보도록 하겠습니다.

---

#### 프로세스 구조

![](https://miro.medium.com/max/700/1*7x2gHvoS4uvRxtaoweLi0A.png)

- `Stack` : 임시 데이터가 저장되는 곳
- `Heap` : 동적으로 할당되는 데이터가 저장되는 곳으로 컴파일 타임에 크기 결정 
- `Data` : 변수가 할당되는 곳
    - `BSS` : 초기화되지 않은 변수가 저장되는 곳 
    - `Data` : 초기화된 변수가 저장되는 곳
- `Text` : 실제 실행되는 기계어 및 어셈블리 코드가 쌓이는 곳

---

#### 프로세스의 상태

![](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter3/3_02_ProcessState.jpg)
> 출처 : https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/3_Processes.html

1. new : 프로세스 생성 중
2. ready : CPU에 할당되기는 대기하는 중
3. running : CPU에서 명령이 실행 되는 중
4. waiting : 프로세스에서 어떤 사건이 발생하기를 기다리는 중
5. terminated : 프로세스가 종료하는 중

---

### 프로세스 상태 전이

1. Dispatch (ready -> running) : 여러 프로세들 중 한 프로세스를 선정하여 CPU에 할당
2. Time Run Out (running -> ready) : 할당된 시간이 지나면 Timeout Interrupt
3. Event Wait (running -> waiting) : I/O 입출력 발생 (CPU 사용 중 I/O 행위가 필요하여 대기 상태로 이동)
4. Wake-Up (waiting -> ready) : I/O 요청이 완료되면 다시 ready 상태로 전이

---

#### 프로세스 제어 블록 (Process Control Block, PCB) 

![](https://i.imgur.com/CuS0wlk.jpg)
> 출처 : https://www.ques10.com/p/24811/short-note-on-process-control-block/

- 프로세스의 실행을 제어하는 데 사용하기 위한 자료구조
- 운영체제의 메모리 공간에 저장

---

#### 컨텍스트 스위칭 (Context Switching)

스케줄러가 `CPU`를 프로세스에게 할당하려고 할 때 현재 프로세스의 context(프로세스 제어를 위한 다양한 정보)를 
`PCB`에 저장하고 새로운 프로세스의 `PCB`로 교체하는 것


Context Switching 과정
1. 프로세스의 대부분 정보는 Register에 저장되고 PCB(Process Control Block)로 관리되고 있습니다.
2. 프로세스의 상태가 바뀔때 현재 실행하고 있는 프로세스의 PCB 정보를 저장하게 됩니다.
3. 다음 실행할 프로세스의 PCB 정보를 읽어 실행을 합니다.
4. 다시 `2.`의 프로세스가 실행될때 이전에 저장한 PCB 정보를 읽어서 다시 실행하게 됩니다.

Context Switching Cost
- 캐시 초기화
- Memory Mapping 초기화
- 메모리 접근을 위해 커널은 항상 실행되어야 합니다.

---

### 프로세스 스케줄링 (Process Scheduling)

![](https://miro.medium.com/max/700/1*8lLFF_wsBIpcXDk92CsKNQ.png)

- 여러개의 프로세스 중 `어떤 프로세스를 CPU에 할당할지를 정하는 것`
- 많은 수의 프로세스가 활성화 되어 CPU 이용률을 높임
- 한 순간에 하나의 프로세스만 CPU를 사용
- 다중 프로그래밍 및 시분할 기능을 실현하기 위한 핵심 수단
- 프로세스 상태를 ready -> running 으로 바꾸는 과정 (`Dispatch`)
- 스케쥴링 큐에서 상태를 전이할 프로세스를 선택하는 것
- 스케줄링 정책 혹은 스케줄링 알고리즘이 중요

---

#### 스케줄링 알고리즘 (Scheduling Algorithm)

1. `FCFS` (First-Come, First-Served)
    - `먼저 들어온 순서대로 처리`
    - 비선점 스케줄링
    - 평규 대기 시간이 길어질 수 있음
        - 프로세스의 도착 순서에 따라 다른 결과
    - CPU와 I/O 자원의 이용률이 낮아질 수 있음
        - Convoy 효과 : 여러 프로세스가 CPU를 얻기 위해 하나의 긴 프로세스를 기다리는 상황
2. `SJF` (Shortest-Job-First)
    - `가장 짧은 CPU 타임을 가진 프로세스를 실행`
    - 평균 대기 시간 기준으로 최적의 스케줄링 알고리즘
    - 프로세스의 다음 CPU 타임을 정확히 알 수 없기 때문에 어려움
3. `Priority`
    - 우선순위에 따라 프로세스의 `우선순위가 높은것부터 CPU에 할당`
    - 우선순위가 동일하다면 `FCFS 스케줄링` 적용
    - 문제점 : 새로 도착하는 프로세스들의 우선순위가 계속 높으면 낮은 우선순위의 프로세스가 무한히 대기하는 경우가 발생 (starvation)
        - 해결 방법 : 대기 시간이 길어질때마다 우선순위를 높여줌 (aging)
4. `RR` (Round-Robin)
    - `하나의 CPU에서 여러 프로세스가 실행되는거처럼 보이게 하는 것`
    - Ready Queue는 Circular FIFO Queue로 구현
    - 매 Time Quantum 마다 스케줄링 수행
    - Time Quantum에 따라 알고리즘 성능이 작아짐
5. `Multilevel Queue`
    - Ready Queue에 있는 것들을 각각의 Queue에 넣어서 `목적에 따라 서로 다른 스케줄링 알고리즘을 사용`
    - 특성이 상이한 프로세스 그룹에 따라 별도의 ready queue를 구현
        - Foreground 프로세스 : `RR`
        - Background 프로세스 : `FCFS`

---

#### 스레드(Thread)

![](https://miro.medium.com/max/1000/1*EMXGwiKPr8VZcz8AosvOPQ.png)
 
- 의미
    - 프로세스의 특정한 수행 경로
    - 프로세스가 할당받은 자원을 이용한 실행 단위
- 특징
    - 스레드는 프로세스 내에서 각각 Stack만 할당받고 Code, Data, Heap은 프로세스내의 다른 스레드들과 공유합니다.
    - 스레드는 한 프로세스 내에서 동작되는 실행의 흐름으로 프로세스 내의 주소 공간이나 자원들을 같은 프로세스 내의 스레드들과 공유합니다.
    - 같은 프로세스 내의 스레드들은 힙 공간을 공유하지만 프로세스는 다른 프로세스의 메모리에 직접 접근할 수 없습니다.
    - 각각의 스레드는 별도의 레지스터와 스텍을 갖지만, 힙 메모리는 서로 읽고 쓸 수 있습니다.
    - 한 스레드가 공유된 프로세스 자원을 변경하면 다른 스레드들도 변경 결과를 즉시 볼 수 있습니다.

---

### 멀티 프로세스(Multi Process) vs 멀티 스레드(Multi Thread)

이번에는 멀티 프로세스와 멀티 스레드 차이를 살펴보도록 하겠습니다.

---

#### 멀티 프로세스(Multi Process)

하나의 응용 프로그램을 여러 개의 프로세스로 구성하여 각각의 프로세스가 작업을 처리하도록 하는 방식입니다.

- 장점 : 여러 프로세스 중 하나가 죽어도 다른 프로세스에는 영향이 없습니다. (서로 독립된 공간이기 때문에)
- 단점
    - `Context Switching Overhead` : Context Switching 과정에서 캐시 메모리 초기화 등 무거운 작업이 진행되고 많은 시간이 소보되는 등의 오버헤드가 발생합니다.
    - 프로세스는 각각의 독립된 메모리 영역을 할당받았기 때문에 프로세스 사이에서 공유하는 메모리가 없어 매번 캐시에 있는 모든 데이터를 리셋하고 다시 캐시 정보를 불러야 합니다.
    - 서로 별도의 공간을 가지기 때문에 프로세스간 통신이 어렵습니다. 통신을 위해서 IPC 같은 복잡한 기법을 사용해야합니다.
    
---

#### 멀티 스레드(Multi Thread)

하나의 응용 프로그램을 여러 개의 스레드로 구성하여 처리하는 각각의 스레드가 작업을 처리하도록 하는 방식입니다.

- 장점
    - 응답성 : 다른 스레드의 실행 시간이 길거나 입출력을 위해 블록되더라도 계속 실행을 허용
    - 자원 공유 : 프로세스(코드 및 데이터 등)의 자원을 자동적으로 공유
    - 경제성 : 프로세스의 생성과 `Context Switching`보다 빠르고 자원 사용이 적다.
    - 확장성 : 멀티 프로세서 시스템에서 병렬성 증가
- 단점
    - 주의 깊게 설계해야 합니다.
    - 디버깅이 까다롭습니다.
    - 다른 프로세스에서 스레드를 제어할 수 없습니다. (프로세스간 서로 다른 공간이기 때문에)
    - 멀티 스레드의 경우 자원 공유의 문제가 발생할 수 있습니다. (동기화 문제)
    - 하나의 스레드에 문제가 생기면 전체 프로세스가 영향을 받습니다.


---

### 안드로이드의 프로세스 수명 주기

안드로이드 시스템은 최대한 오래 어플리케이션 프로세스를 유지하려고 하지만 우선순위를 통해서 더 중요한 프로세스에서 사용할 메모리를 확보해야 합니다.
이때 유지할 프로세스와 종료할 프로세스를 결정하기 위해 각각의 구성 요소의 상태에 기초하여 프로세스에 `중요 계층`을 부여하고 중요도가 낮은 프로세스부터 제거됩니다.

중요 게층에는 다섯 가지 단계가 있는데 다음 목록은 중요도에 따른 프로세스 유형을 나타냅니다. (1번이 가장 중요하고 5번이 가장 중요도가 낮습니다.)


1. 포그라운드 프로세스 (Foreground Process)
2. 가시적 프로세스 (Visible Process)
3. 서비스 프로세스 (Service Process)
4. 백그라운드 프로세스 (Background Process)
5. 빈 프로세스 (Empty Process)

![](https://miro.medium.com/max/1400/1*3r0nqWQAM-KQ5WukCg6AwQ.png)

---

### 스레드

어플리케이션이 시작되면 시스템은 어플리케이션에 대한 메인 스레드를 생성합니다. 시스템은 구성 요소의 각 인스턴스별로 별도의 스레드를 실행하지 않고 메인 스레드에서
처리를 합니다. 대표적으로 UI를 처리하는 모든 작업은 메인 스레드에서 동작해야 합니다.

안드로이드에서는 데이터베이스 쿼리나 네트워크 액세스 등의 긴 작업을 메인 스레드에서 동작하지 않습니다. 이를 위해 별도의 스레드 처리를 해야합니다.

먼저 가장 간단히 사용할 수 있는건 Runnable 객체를 만들어서 새로운 스레드에서 작업을 돌리는 방법이 있습니다. 

```java
Thread(Runnable { 
    // do something
}).start()
```

만약 메인 스레드와 상호 작용을 해야 한다면 `AsyncTask`를 이용해 작업을 할 수도 있습니다.
하지만 AsyncTask 작업중 Activity 예기치 못하게 죽는다면 `Memory Leak`이 발생할 수도 있습니다.

```java

// AsyncTask<자료형1, 자료형2, 자료형3>()
// 자료형1 : doInBackground의 매개변수 자료형
// 자료형2 : onProgressUpdate의 매개변수 자료형
// 자료형3 : onPostExecute의 매개변수 자료형
// 여기서는 전부 Void로 주었습니다.
class Progress : AsyncTask<Void, Void, Void>() {

    override fun onPreExecute() {
        super.onPreExecute()

        // 실행 전 준비 해야하는 작업
    }

    override fun doInBackground(vararg params: Void?): Void {

        // 백그라운드에서 동작해야할 작업 ex) 데이터베이스 쿼리, 네트워크 작업...
    }

    override fun onProgressUpdate(vararg values: Void?) {
        super.onProgressUpdate(*values)

        // 중간 중간 UI 스레드에서 처리해야 하는 부분 (ex - UI 업데이트)
    }

    override fun onPostExecute(result: Void?) {
        super.onPostExecute(result)

        // 작업이 다 끝나고 실행해야 하는 작업

    }
}
```

아니면 RxJava를 사용하여 별도의 스케쥴러에서 작업을 시켜도 됩니다.

```java
val home = "/Users/jungwoon"
val files = File(home).listFiles()

val source = Observable.fromArray(*files!!)
    .filter { !it.isDirectory }
    .map { it.absolutePath }
    .subscribeOn(Schedulers.io())

source.subscribe(System.out::println)
sleep(500)
```
