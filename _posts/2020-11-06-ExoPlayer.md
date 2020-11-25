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

[ExoPlayer](https://exoplayer.dev)는 구글에서 만든 미디어 재생 라이브러리로, 다양한 종류의 미디어 파일을
쉽게 재생할 수 있도록 도와줍니다. 심지어 별 다른 설정 없이도 네트워크로부터 미디어를 스트리밍 형태로 불러와 재생할 수도 있고
다양한 포맷들을 지원하먀 커스터마이징도 지원합니다.

ExoPlayer를 사용한 대표적인 서비스에는 `Youtube`가 있습니다.

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

---

### Progressive container formats

1. MP4
2. M4A
3. FMP4
4. WebM
5. Matroska
6. MP3 - 오직 고정 비트 레이트 검색을 사용해서만 Seeking 가능
7. Ogg - Vorbis, Opus, FLAC 포함
8. WAV
9. MPEG-TS
10. MPEG-PS
11. FLV - Seeking 안됨
12. ADTS(AAC) - 오직 고정 비트 레이트 검색을 사용해서만 Seeking 가능)
13. FLAC - FLAC extension을 사용해야 함
14. AMR - 오직 고정 비트 레이트 검색을 사용해서만 Seeking 가능

---

### Sample formats

기본적으로 ExoPlayer는 Android의 Decoder를 사용하기 때문에 Android에서 지원하는  
포맷들을 기본적으로 제공하고 이 외에도 Extension을 통해서 확장할 수도 있습니다.

---

#### 안드로이드 지원 포맷 (Audio)

1. AAC LC - `.3gp`, `.mp4`, `.m4a`, `.aac`, `.ts`
2. HE-AACv1(AAC+) - `.3gp`, `.mp4`, `.m4a`, `.aac`, `.ts`
3. AAC ELD - `.3gp`, `.mp4`, `.m4a`, `.aac`, `.ts`
4. AMR-NB - `.3gp`
5. AMR-WB - `.3gp`
6. FLAC - `.flac`
7. GSM - `.gsm`
8. MIDI - `.mid`, `.xmf`, `.mxmf`, `.rttl`, `rtx`, `.ota`, `.imy`
9. MP3 - `.mp3`
10. Opus - `.mkv`
11. PCM/WAVE - `.wav`
12. Vorbis - `.ogg`, `.mkv`

---

#### 안드로이드 지원 포맷 (Video)

1. H.263 - `.3gp`, `.mp4`
2. H.264 (BP) - `.3gp`, `.mp4`, `.ts`
3. H.264 (MP)
4. H.265 (HEVC) - `.mp4`
5. MPEG-4 SP - `.3gp`
6. VP8 - `.webm`
7. VP9 - `.webm`

---

#### FFmpeg extension

FFmpeg extension을 통하여 아래의 더 다양한 포맷도 사용할 수 있습니다.

1. Vorbis
2. Opus
3. FLAC
4. ALAC
5. PCM μ-law
6. PCM A-law
7. MP1, MP2, MP3
8. AMR-NB
9. AMR-WB
10. AAC
11. AC-3
12. E-AC-3
13. DTS, DTS-HD
14. TrueHD

---

### Standalon subtitle formats

1. WebVTT
2. TTML / SMPTE-TT
3. SubRip
4. SubStationAlpha (SSA/ASS)

---

## ExoPlayer 주요 컴포넌트

ExoPlayer에는 다양한 종류의 컴포넌트들이 있는데 각 컴퓨넌트들이 어떤 역할을 하는지 알아보도록 하겠습니다.

### PlayerView

비디오를 불러와서 실제 UI에 부려줄때 사용되는 UI 요소로 xml에 선언을 해놓고 SimpleExoPlayer와 바인딩하여 사용합니다.

### ExoPlayer

비디오를 화면에 뿌려주는 가장 중요한 역할을 하는 컴포넌트입니다.

### DataSourceFactory

`MediaSource`를 생성할때 `DataSourceFactory`를 넣어줘야 하는데, `DataSource`는 `URI 리소스로부터 데이터를 읽는데 사용`합니다. 여러 종류의 `DataSource`가
있는데 결국은 모두 `DataSource`를 상속받아 만들어져있습니다.

- `DefaultDataSourceFactory(context, userAgent)` : 가장 기본적인 `DataSourceFactory`
- `DefaultHttpDataSourceFactory(userAgent)` : `Android`의 `HttpURLConnection`을 이용


### MediaItem

MediaItem은 Media을 재생하기 가장 작은 항목으로 Uri를 기반으로 MediaItem을 생성할 수 있습니다. 또한 미디어에 다양한 MetaData를 세팅할 수 있습니다.

재생목록은 MediaItem을 기반으로 생성이 됩니다. 실제 재생을 위해서는 MediaItem을 이용하여 MediaSource를 만들어야 합니다.

아래 예제는 간단히 `MediaItem`을 만드는 샘플입니다.

```java
val mediaItem = MediaItem.fromUri(Uri.parse(videoUrl))
```

아례 예제는 `MediaItem`에 여러 `MetaData`를 `추가`하여 생성하는 단계 입니다. 아래에는 `MediaId`와 `TAG`만 달았는데 그 이외에도 `DRM`이나 `광고`등 다양한
설정들을 세팅할 수 있습니다. 자세한 내용은 [여기](https://exoplayer.dev/media-items.html) 를 통해 좀 더 자세히 알아볼 수 있습니다.

```java
val extendMediaItem = MediaItem.Builder().apply { 
    setUri(Uri.parse(url))
    setMediaId("video-01")
    setTag("TAG_01")
}.build()
```


### MediaSource

`ExoPlayer`에서 재생을 하기 위해서는 `MediaSource`가 필요하는데 `MediaSource`는 `MediaItem`을 이용하여 생성한 뒤에 `ExoPlayer`와 연결하여 사용합니다. 

`MediaSource`의 종류와 만드는 방법은 아래와 같습니다.

- ProgressiveMediaSource : 일반 미디어 파일 형식 재생
```java
// 비디오 URL
val url = "https://www.learningcontainer.com/wp-content/uploads/2020/05/sample-mp4-file.mp4"

// MediaItem을 만들고
val mediaItem = MediaItem.fromUri(Uri.parse(url))

// MediaSource를 만들고
val userAgent = Util.getUserAgent(this, this.applicationInfo.name)
val factory = DefaultDataSourceFactory(this, userAgent)
val progressiveMediaSource = ProgressiveMediaSource.Factory(factory).createMediaSource(mediaItem)
```

- DashMediaSource : DASH 형식 재생
```java
// 비디오 URL
val url = "https://www.learningcontainer.com/wp-content/uploads/2020/05/sample-mp4-file.mp4"

// MediaItem을 만들고
val mediaItem = MediaItem.fromUri(Uri.parse(url))

// MediaSource를 만들고
val userAgent = Util.getUserAgent(this, this.applicationInfo.name)
val factory = DefaultHttpDataSourceFactory(userAgent)
val dashMediaSource = DashMediaSource.Factory(factory).createMediaSource(mediaItem)
```

- SsMediaSource : SmoothStreaming 형식 재생
```java
// 비디오 URL
val url = "https://www.learningcontainer.com/wp-content/uploads/2020/05/sample-mp4-file.mp4"

// MediaItem을 만들고
val mediaItem = MediaItem.fromUri(Uri.parse(url))

// MediaSource를 만들고
val userAgent = Util.getUserAgent(this, this.applicationInfo.name)
val factory = DefaultHttpDataSourceFactory(userAgent)
val ssMediaSource = SsMediaSource.Factory(factory).createMediaSource(mediaItem)
```

- HlsMediaSource : HLS 형식 재생
```java
// 비디오 URL
val url = "https://www.learningcontainer.com/wp-content/uploads/2020/05/sample-mp4-file.mp4"

// MediaItem을 만들고
val mediaItem = MediaItem.fromUri(Uri.parse(url))

// MediaSource를 만들고
val userAgent = Util.getUserAgent(this, this.applicationInfo.name)
val factory = DefaultHttpDataSourceFactory(userAgent)
val hlsMediaSource = HlsMediaSource.Factory(factory).createMediaSource(mediaItem)
```


> ExoPlayer 버전 `2.12` 이전에는 `DefaultMediaSourceFactory`를 사용했지만 `2.12` 이후에는 Deprecated되어 `ProgressiveMediaSource`를 이용해야합니다.

### Track Selector

TrackSelector는 영상의 Track정보를 세팅하는 역할들 하는 컴포넌트로 비디오의 비트레이트, 비디오 사이즈, 대역폭등 다양한 설정을 도와줍니다.
아래 예제 이외에도 다양한 옵션들이 있기에 [여기](https://exoplayer.dev/track-selection.html) 를 확인하면 더 자세히 알 수 있습니다.

```java
val trackSelector = DefaultTrackSelector(this)
trackSelector.setParameters(
trackSelector.buildUponParameters()
    .setMaxVideoSizeSd() // 비디오 트랙 선택을 SD로 제한
    .setMaxAudioBitrate(1000) // 최대 오디오 bitrate를 1000으로 설정
)

val player = SimpleExoPlayer.Builder(this).apply {
setTrackSelector(trackSelector)
}.build()
```


### PlayerControlView

PlayerControlView는 아래와 같이 재생을 도와주는 UI 컴포넌트입니다. 기본적으로 별다른 설정을 하지 않아도 아래와 같은 ControlView는 제공이 됩니다.

![](https://miro.medium.com/max/700/1*S4a4wZ_In5IJsxWpWUzFYQ.png)

만약 해당 ControlView에 대해서 커스터마이징을 하고 싶다면 아래와 같이 커스터마이징도 가능합니다.

![](https://exoplayer.dev/images/overriding-layoutfiles.png)

새로운 레이아웃을 만든 다음에 PlayerView의 `controller_layout_id` 부분에 연결을 해주면 커스텀된 PlayerControlView를 사용할 수 있습니다.

```xml
<com.google.android.exoplayer2.ui.PlayerView
    android:id="@+id/player_view"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:controller_layout_id="@layout/custom_controls"/>
```

---

##  playbackState 별로 리스너 설정하기

플레이어의 상태별로 특정 동작을 하도록 리스너를 추가할 수도 있습니다.

```java
SimpleExoPlayer.Builder(context).build().apply {
    this.addListener(object : Player.EventListener {
        override fun onPlayerStateChanged(playWhenReady: Boolean, playbackState: Int) {
            super.onPlayerStateChanged(playWhenReady, playbackState)
            
            when (playbackState) {
                Player.STATE_IDLE -> {
                    // 초기 상태, 플레이어가 중지 된 상태, 재생이 실패한 상태입니다.
                }
                Player.STATE_BUFFERING -> {
                    // 플레이어는 현재 위치에서 즉시 플레이 할 수 없습니다. 이것은 더 많은 데이터를로드해야하기 때문에 주로 발생합니다.
                }
                Player.STATE_READY -> {
                    // 플레이어는 현재 위치에서 즉시 플레이 할 수 있습니다.
                }
                Player.STATE_ENDED -> {
                   // 플레이어가 모든 미디어 재생을 마쳤습니다.
                }
            }
        }
    })
}
```


---

## 실습

이번에는 ExoPlayer를 이용하여 동영상을 재생하는 프로젝트를 간단히 만들어보도록 하겠습니다. 아래 이미지가 실제 프로젝트 실행화면입니다.

![](https://miro.medium.com/max/700/1*AwJNXBNHyTvWzryOwwpTnA.png)


### 라이브러리 세팅하기

아래는 `build.gradle(project)`의 아래를 추가해야합니다.

```
repositories {
    google()
    jcenter()
}
```


아래는 `build.gradle(app)`에 넣어야 하며 각각의 라이브러리들은 아래와 같습니다.

1. exoplayer-core (필수) - 필수적인 라이브러리로 반드시 있어야 합니다.
2. exoplayer-dash (선택) - MPEG `DASH` 지원
3. exoplayer-hls (선택) - APPLE `HLS` 지원
4. exoplayer-smoothstreaming (선택) -  - MicroSoft `SmoothStreaming` 지원
5. exoplayer-ui (선택) - UI 관련 다양한 기능들 지원

```
dependency {
    implementation 'com.google.android.exoplayer:exoplayer-core:2.11.8'
    implementation 'com.google.android.exoplayer:exoplayer-dash:2.11.5'
    implementation 'com.google.android.exoplayer:exoplayer-hls:2.11.5'
    implementation 'com.google.android.exoplayer:exoplayer-smoothstreaming:2.11.5'
    implementation 'com.google.android.exoplayer:exoplayer-ui:2.11.8'
}
```

우선 레이아웃부터 잡도록 하겠습니다.
]

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <com.google.android.exoplayer2.ui.PlayerView
        android:id="@+id/player_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="@color/black"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```
[activity_main.xml]


통신을 해야하기 때문에 `AndroidManifest.xml`에 아래 권한을 추가합니다. 

```
<uses-permission android:name="android.permission.INTERNET"/>
```

전체 소스는 아래와 같습니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.global.exoplayer">

    <uses-permission android:name="android.permission.INTERNET"/>

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.ExoPlayer">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```
[AndroidManifest.xml]

실제 돌아가는 소스는 다음과 같고 간단히 소스를 설명하자면 다음 Step으로 이루어 집니다.

1. `PlayerView`를 xml에 정의
2. `PlayerView`에 `SimpleExoPlayer 인스턴스` `바인딩`
3. 비디오 URL을 이용하여 `MediaItem` 생성
4. `MediaItem`을 이용하여 `MediaSource` 생성
5. `MediaSource`를 `SimpleExoPlayer에` 연결
6. `SimpleExoPlayer` 준비 및 재생


여기서 중요한 점은 `MediaItem`을 통해서 `MediaSource`를 만들어야지 `재생 가능한 객체가 생성` 됩니다.

![](https://miro.medium.com/max/700/1*mpgLUceGZWqI2B2xPYD_vA.png)


```java
package com.global.exoplayer

import android.net.Uri
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import com.google.android.exoplayer2.MediaItem
import com.google.android.exoplayer2.SimpleExoPlayer
import com.google.android.exoplayer2.source.ProgressiveMediaSource
import com.google.android.exoplayer2.ui.PlayerView
import com.google.android.exoplayer2.upstream.DefaultDataSourceFactory
import com.google.android.exoplayer2.util.Util


class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val playerView: PlayerView = findViewById(R.id.player_view)

        // SimpleExoPlayer 인스턴스
        val simpleExoPlayer = SimpleExoPlayer.Builder(this).build()
        playerView.player = simpleExoPlayer // PlayerView에 만들어진 SimpleExoPlayer 인스턴스 바인딩

        // 비디오 URL
        val url = "https://www.learningcontainer.com/wp-content/uploads/2020/05/sample-mp4-file.mp4"

        // MediaItem을 만들고
        val mediaItem = MediaItem.fromUri(Uri.parse(url))

        // MediaSource를 만들고
        val userAgent = Util.getUserAgent(this, this.applicationInfo.name)
        val factory = DefaultDataSourceFactory(this, userAgent)
        val progressiveMediaSource = ProgressiveMediaSource.Factory(factory).createMediaSource(mediaItem)

        // 만들어진 MediaSource를 연결
        simpleExoPlayer.setMediaSource(progressiveMediaSource)

        // Player 준비
        simpleExoPlayer.prepare()

        // Player 재생
        simpleExoPlayer.play()
    }
}
```
[MainActivity.kt]

---

## 삽질노트

저는 ExoPlayer를 이용하여 틱톡가 같이 스와이프로 계속 동영상 시청을 할 수 있는 프로젝트를 진행했는데, 잘 나오던 동영상이 어느 시점만 넘어가면
화면이 까맣게 나오면서 재생이 되지 않는 문제가 발생하였습니다. 알아본본 결과최대 SimpleExoPlayer 인스턴스를 만들어 재생할 수 있는 최대 개수가 저의 경우엔
15개가 한계였습니다.(만들 수 있는 decor 개수)

만약 사용하려면 매번 만들어진 인스턴스를 release()를 통해 풀어주고 사용해야 합니다. 아니면 PlayerView.switchTargetView() 메소드를 통해서 만들어놓은 인스턴스를 재 활용해야합니다.

```java
PlayerView.switchTargetView(player 인스턴스, 바꾸기 전 PlayerView, 바꿀 PlayerView)
or
simpleExoPlayer.release()
```