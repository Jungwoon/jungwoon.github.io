---
layout: post
title: "가비지 컬렉션(Garbage Collection)"
image: '/assets/img/'
description: '가비지 컬렉션(Garbage Collection)'
tags:
- Java, GC
categories:
- Java, GC
---

이번에는 자바의 `Garbage Collection`에 대해서 알아보도록 하겠습니다. 자바에서는 프로그램 코드에서 메모리를 명시적으로
지정하여 해제하지 않습니다. 이 때 `Garbage Collection`는 더 이상 필요로 하지 않는 (쓰레기)객체를 찾아서 지우는 역할을 합니다. GC는 힙 메모리만 다룹니다.

Garbage 대상이 되는 케이스
- 객체가 `null`인 경우
- 블럭 안에서 생성된 객체는 블럭 종료 후 대상이 됩니다.
- 부모 객체가 `null`이 되면, 참조된 자식 객체들도 대상이 됩니다.


Java에서도 직접 명시적으로 메모리에서 해제할 수 있는데 `null`을 넣어서 해제시키는건 문제가 되지 않지만,
`System.gc()`를 호출하여 해제하는건 시스템에 매우 큰 영향을 끼치므로 `System.gc()`는 절대 사용하면 안됩니다.

> 해당 포스팅은 [Naver D2 - Java Garbage Collection](https://d2.naver.com/helloworld/1329)를 참고했습니다.

---

### Stop-The-World?

GC을 실행하기 위해 JVM이 어플리케이션 실행을 멈추는 것으로, `Stop-The-World`가 발생하면 `GC`를 실행하는
스레드를 제외한 나머지 스레드는 모두 작업을 멈춥니다.

GC 작업을 완료한 이후에야 중단했던 작업이 다시 시작됩니다. 어떤 GC 알고리즘을 사용해도 `Stop-The-World`는 발생하기 때문에
보통 GC 튜닝이란 이 `Stop-The-World` 시간을 줄이는 작업을 말합니다.


---

### GC의 두 가지 전제조건 및 영역

- 대부분의 객체는 금방 접근 불가능한 상태가 됩니다.
- 오래된 객체에서 젊은 객체로의 참조는 아주 적게 발생합니다.

이러한 가설을 `weak generational hypothesis` 라고 하는데 이를 위해 HotSpot VM에는 크게 2개의
물리공간으로 나뉘게 됩니다. 이렇게 둘로 나눈 영역은 `Young 영역`과 `Old 영역`입니다.

![](https://miro.medium.com/max/700/1*u1QbLuXBLz5bHa0nFfgu1w.png)

- `Young 영역` : 새롭게 생선한 객체의 대부분은 여기에 위치합니다. 대부분의 객체가 금방 접근 불가능한 상태가 되기 때문에, 많은 객체가
Young 영역에서 생성되었다가 사라집니다. 이 영역에서 객체가 사라지는걸 `Minor GC`라고 합니다.
- `Old 영역` : Young 영역에서 살아남은 객체는 여기로 복사됩니다. 보통 Young 영역보다 크기가 크기때문에 
GC가 상대적으로 적게 발생합니다. 이 영역에서 객체가 사라지는걸 `Major GC` 또는 `Full GC`라고 합니다.
- `Permanent 영역` : `JVM`에서 사용하는 클래스와 메서드 정보를 가지고 있습니다. 여기서 사라지는것도 `Major GC` 입니다. JDK8부터 `Metaspace`로 대체되었습니다.

 
---

### Young 영역 (Heap)

Young 영역은 아래와 같이 나뉩니다.

![](https://miro.medium.com/max/1000/1*DTdUPS5-OS_qXBiC1zPUpQ.png)

- Eden 영역
- Survivor 영역 (From, To)

1. 새로 생성된 객체의 대부분은 Eden 영역에 위치합니다.
2. Eden 에서 GC가 발생하고 살아남은 객체는 Survivor(From)에 쌓입니다.
3. Survivor(From)이 가득 차면 Survivor(To)로 보내고 Survivor(From)은 비웁니다.
4. 이 과정이 반복되다가 마지막까지 살아남은 객체는 Old 영역으로 갑니다.

---

### Old 영역 (Heap)

Old 영역은 기본적으로 데이터가 가득 차면 GC를 실행합니다. 하지만 GC 방식에 따라서 절차가 달라집니다.

- Serial GC : 적은 메모리와 CPU 코어 개수가 적을 때 적합한 방식
- Parallel GC : Serial GC와 동일한 알고리즘이지만 멀티 스레드로 좀 더 빠릅니다.
- Concurrent Mark & Sweep GC (CMS) : 메모리가 크고 2개 이상의 프로세서를 사용할때 적합합니다.
- G1(Garbage First) GC : 메모리가 크고 멀티 코어 프로세서를를 사용할 때 적합합니다.


#### Serial GC

Young 영역은 앞에 설명한 방식과 동일하게 사용하며, Old 영역의 GC는 Mark-Sweep-Compact 알고리즘을 사용합니다.

1. Mark : 이 알고리즘은 첫 단계에서 Old 영역에 살아 있는 객체를 식별
2. Sweep : 그 다음 Heap의 앞 부분부터 확인하여 살아있는 것만 남깁니다
3. Compact : 마지막에서는 각 객체들이 연속되게 쌓이도록 힙의 가장 앞 부분부터 채워서 객체가 존재하는 부분과 객체가 없는 부분으로 나눕니다

---

#### Parallel GC

Serial GC와 Parallel GC는 기본적으로 동일하지만 Parallel GC는 GC를 처리하는 스레드가 여러개라 좀 더 빠르게 처리할 수 있습니다.

![](https://d2.naver.com/content/images/2015/06/helloworld-1329-4.png)
> 출처 : d2.naver.com

---

### Concurrent Mark Sweep GC (CMS)

![](https://d2.naver.com/content/images/2015/06/helloworld-1329-5.png)
> 출처 : d2.naver.com

1. Initial Mark : 클래스 로더에서 가장 가까운 객체 중 살아 있는 객체만 찾습니다.
2. Concurrent Mark : 방금 살았다고 확인한 객체에서 참조하고 있는 객체를 따라가며 확인합니다.
3. Remark : Concurrent Mark 단계에서 새로 추가되거나 참조가 끊긴 객체를 확인합니다.
4. Concurrent Sweep : 쓰레기를 정리합니다.

- 장점 : Stop-The-World 시간이 매우 짧습니다.
- 단점 : 
    - 더 많은 메모리와 CPU를 사용합니다. 
    - 기본적으로 Compaction 단계를 제공하지 않습니다. 

---

### G1 GC

G1 GC는 메모리가 큰 멀티 코어 머신을 타겟으로 설계되었습니다. JDK7u4 부터 도입이 되어 현재 JDK9에서는 기본 GC로 채택 되었습니다,
Heap 메모리 영역을 작은 단위의 리젼으로 나눠서 관리합니다. 

![](https://plumbr.io/app/uploads/2015/06/g1-011.png)
> 출처 : plumbr

1. Initial Mark (Stop-The-World)
    - Old GC가 필요해지면 Young GC때 함께 시행됩니다.
    - Survivor 영역에서 Old 영역에 있는 객체를 참조하는 Survivor 영역을 표시합니다.
2. Root Region Scanning
    - 어플리케이션 실행 중단없이 첫번재 단계에서 표시한 Survivor 영역을 스캐닝하니다.
3. Concurrent Mark
    - 전체 Heap 영역에서 사용하는 객체를 표시합니다.
    - 이 단계에서 Young GC가 발생하면 멈출 수도 있습니다.
    - 영역별 Live Object 비율을 계산합니다.
4. Remark (Stop-The-World)
    - 빈 영역들은 삭제해서 Free로 만듭니다.
    - 전체 영역의 Live Object 비율을 계산합니다.
5. Copy & Cleanup (Stop-The-World)
    - 가장 빨리 청소가 가능한 Live Object 비율이 낮은 영역들을 선택합니다.
    - Young 영역과 Old 영역이 모두 Clean-up 되고 선택된 영역들은 모두 새로운 영역으로 Compaction 되어 위치하게 됩니다.
6. Full GC
    - Old GC를 통해서만 필요한 Young영역을 확보하지 못하면 Full GC를 실행합니다.

---

### GC 종류

- Minor GC
    - 대상 : Young 영역
    - 시점 : Eden이 full이 되었을때 
- Major GC
    - 대상 : Old 영역
    - Minor GC가 실패했을 때
- Full GC
    - 대상 : 전체 Heap + MetaSpace(Permanent 영역)
    - Minor GC나 Major GC가 실패했을 경우
