---
layout: post
title: "ExoPlayer 정리"
image: '/assets/img/'
description: 'ExoPlayer Summary'
tags:
- Android
- ExoPlayer
- Library
categories:
- Android
- Library
---

# ExoPlayer 정리

최근 개인적으로 이직을 하고 급하게 프로젝트를 진행하느라 장시간 포스팅이 소홀해졌는데, 프로젝트도 막바지이고 프로젝트를 진행하면서 
알게된 삽질(?)을 공유하고자 포스팅을 다시 시작하려고 합니다.

이번 프로젝트에서 TicTok과 같이 스와이프로 동영상을 넘기는 프로젝트를 진행해야 했는데 그때 ExoPlayer를 사용하게 되면서
알게된 여러 삽질에 대해서 정리를 해보고자 합니다.

---

## ExoPlayer란?

[ExoPlayer](https://exoplayer.dev) 구글에서 만든 미디어 재생 라이브러리로, 다양한 종류의 미디어 파일을
쉽게 재생할 수 있도록 도와줍니다. 심지어 별 다른 설정 없이도 네트워크로부터 미디어를 스트리밍 형태로 불러와 재생할 수도 있고
다양한 포맷들을 지원하먀 커스터마이징도 지원합니다.

ExoPlayer를 사용한 대표적인 서비스에 `Youtube`가 있습니다.

---

## ExoPlayer에서 지원하는 포맷들

ExoPlayer에서는 다양한 포맷들을 지원하고 있으며, 크게 아래의 포맷들을 지원합니다.

### Adaptive Stremaing

Adaptive Streaming은 적응형 스트리밍으로 사용자의 네트워크 상태에 적응해서 스트리밍을 해주는
기술입니다. 동영상 콘텐츠를 다양한 해상도로 인코딩해서 저장하고 이를 잘게 잘게쪼개어 저장을 해놨다가
사용자의 네트워크 상황에 따라서 최적의 동영상 콘텐츠 조각을 가져와 스트리밍 해주는 방식입니다.

대표적으로 Youtube가 이러한 방식입니다. 통신 상황에 따라서 자동으로 해상도가 좋아졌다 나빠졌다하는걸  
경험해보셨을 것입니다. 이를 이용하면 서비스를 제공하는 서버의 트래픽을 관리할 수 있을 뿐 아니라 사용자는  
끊기지 않고 동영상 시청이 가능하게 됩니다.

1. DASH (MPEG-DASH) : 국제 표준화된 최초의 적응 비트레이트 HTTP 기반 스트리밍 솔루션
2. HLS (Apple-HLS) : 애플에서 만든 스트리밍 솔루션
3. SmoothStreaming (Microsoft-SmoothStreaming) : MS에서 만든 스트리밍 솔루션

