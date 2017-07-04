---
layout: post
title: "Activity Lifecycle"
image: '/assets/img/'
description: '안드로이드 액티비티 생명주기'
tags:
- Android
categories:
- Android
---

### Activity Lifecycle

이번에 포스팅 할 내용은 안드로이드 액티비티의 생명주기에 관해서 포스팅하려고 합니다.

![img](https://cdn-images-1.medium.com/max/1600/1*WORms3m3HA475pEcXoXFsQ.png)

#### 실행(Active, Running)
: 사용자가 직접 사용하는 상태로, 스택의 제일 위의 있으며 화면상에서도 역시 제일 위에 있습니다. 사용자의 입력을 직접 처리합니다.

#### 일시정지(Pause)
: 포커스를 잃었지만, 사용자에게 보이는 상태입니다. 위쪽에 다른 액티비티가 있지만 화면 전체를 다 가리지 않았거나 반투명한 상태가 이 경우에 해당합니다. 살아 있는 상태와 같지만 시스템에 의해 강제 종료될 수도 있습니다.

#### 정지(Stopped)
: 다른 액티비티에 의해 완전히 가려진 상태이며, 사용자 눈에 보이지 않습니다. 그러나 모든 정보를 다 유지하고 잇으므로 언제든 다시 활성화될 수 있습니다. 시스템은 메모리가 부족하면 정지 상태의 액티비티를 언제든지 강제 종료할 수 있습니다.
