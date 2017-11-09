---
layout: post
title: "실시간 데이터 처리 개념"
image: '/assets/img/'
description: '실시간 데이터 처리를 위한 개념'
tags:
- Realtime Data
- Streaming
- Bigdata
categories:
- Bigdata
---


# 스트리밍 데이터 처리 개념
다음 내용들은 조대협님의 블로그의 내용들을 공부하면서 정리한 내용입니다.

> [조대협님의 블로그](http://bcho.tistory.com)

### Bounded data vs Unbounded data
- Bounded data : 데이터가 저장되고 더 이상 증가나 변경이 없이 유지되는 데이터  
ex) 1월 정산 데이터
- Unbounded data : 데이터의 수가 정해져있지 않고 계속해서 추가되는 데이터  
ex) SNS 타임피드, 증권 거래, 로그...

### Event time vs Processing time
- Event time : 이벤트가 발생할 시간
- Processing time : 이벤트가 서버로 전달되고 로직을 수행하여 저장된 시간
- Skew : 이상적으로는 같아야 하지만 중간에 Latency가 발생을 하여 지연이 발생하는데 이를 Skew라고 한다.

![img](https://cdn-images-1.medium.com/max/600/1*2ozJ8YtOOKRjxX3Ej9DArQ.png)

### Bounded data의 처리
이미 저장되어 있는 데이터를 처리하기 때문에 별다른 처리 패턴이 필요없이 데이터를 읽어서 한번에 처리해서 저장하면 된다.

![img](https://cdn-images-1.medium.com/max/600/1*d39Z_-tVkVioiNe0Zn_SFg.png)

### Unbounded data의 처리
크게 두가지 처리 방식이 있다.

- Fixed Windows  
스트리밍으로 들어오는 데이터를 일정 시간 단위로 모은 후, 배치로 처리하는 방식으로 구현이 간단하다는 장점이 있지만, 데이터가 수집 된 후 처리를 시작하기 때문에, 실시간성이 떨어진다.  
ex) 10-11시 데이터 수집, 11시-12시 데이터 수집

![img](https://cdn-images-1.medium.com/max/1200/1*7xyafF15E5AWSbg2Nerb8g.png)

- Streaming 처리  
Unbounded 데이터를 제대로 처리하려면 스트리밍 처리를 하는 것이 좋은데, 스트리밍 처리 방법에는 크게 Time agnostic, Filtering, Inner Join, Windowing 방식등이 있다.  

기본적으로 스트리밍 처리에는 Skew가 환경에 다라 변화가 심하여 데이터가 도착하는 시간이 일정하지 않다.


### Streaming 처리 방법
- Time agnostic  
시간 속성을 가지지 않은 데이터로 들어오는대로 처리

- Filtering  
특정 데이터만 필터링 하여 저장하는 구조


- Inner joins  
독립적인 Unbounded data를 비교 매칭하여 값을 구하는 방식, 양쪽 스트림에서 데이터 항상 같은 시간에 도착하는 것이 아니기 때문에, 다음과 같은 매커니즘이 필요

```
A 도착 -> B 도착할때까지 버퍼에 저장 -> B 도착 -> 조인하여 결과 저장 -> 버퍼에서 삭제
```

![img](https://cdn-images-1.medium.com/max/600/1*-jjGTYkw5WAW2F6Q8wM4UQ.png)


- Approximation algorithms  
시급한 분석이 필요한 경우, 전체 데이터를 분석하지 않고 일부만 분석하거나, 대략적인 데이터의 근사값만을 구하는 방법으로 대표적으로 K-means나 Approximate Top-N등이 있다.  
ex) VOD에서 최근 10분간 인기있는 비디오 목록, 12시간 동안 가장 많이 팔린 제품  


- Windowing : 일정 시간 간격으로 처리하는 방법
	- Fixed Window : 정확하기 일정 시간 단위로 시간 윈도우를 쪼개는 개념
	- Sliding Window : 현재시간으로부터 +- N 시간의 데이터를 특정 시간마다 추출하는 방식
	- Session Window : 사용자가 일정 기간동안 데이터를 보내고 일정시간 동안 데이터 없는 동안을 윈도우로 묶는 개념


### 시간대별 Window 처리 방식
- Processing time based windowing : 도착한 순서대로 처리해서 저장
- Event time based windowing : Event time을 기준으로 데이터를 처리하는 개념으로 Skew가 발생하는 경우 저장했다가 처리해줘야 하기 때문에 Buffering과 Completeness란 개념이 필요

![img](https://cdn-images-1.medium.com/max/400/1*RgjuniWDpJN8KQ29avCRiw.png)
