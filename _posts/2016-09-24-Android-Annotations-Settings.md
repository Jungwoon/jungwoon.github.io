---
layout: post
title: "Android Annotations 설정 방법"
image: '/assets/img/'
description: '코드를 반으로 줄여주는 Android Annotations 설정법'
tags:
- Android
- Android Annotations
categories:
- Android
---

내가 사용하고 있는 프로젝트에 안드로이드 어노테이션 적용을 하려고 보니 초기 설정 해줘야 하는 부분들이 있어서 이 부분 공유 드립니다.

### Setting 설정

- 우선 Annotations 설정을 허용해줘야 합니다.
- File -> Other Settings -> Default Settings... 로 이동합니다.

![img](https://cdn-images-1.medium.com/max/2000/1*nolZaC500rFyWw7DDqS2Fw.png)


- Build, Execution, Deployment -> Compiler -> Annotation Processors 로 이동합니다.
- 그 다음 Enable annotation processing 부분을 체크 합니다.

![img](https://cdn-images-1.medium.com/max/1600/1*LnmqZhsdT1brVeX5OuXoPQ.png)

### Gradle 설정
- build.gradle(Project)
- 아래 코드를 dependencies에 추가합니다.

```
classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
```

![img](https://cdn-images-1.medium.com/max/2000/1*Xs-Nd7c8gpDma-yW0dnzBQ.png)

- build.gradle(app)
- 아래 코드를 맨 윗쪽에 추가

```
apply plugin: 'com.neenbedankt.android-apt'
def AAVersion = '4.1.0'
```

- dependencies 를 만들어 아래 코드 추가

```
dependencies {
  apt "org.androidannotations:androidannotations:$AAVersion"
  compile "org.androidannotations:androidannotations-api:$AAVersion"
}
```

- android -> dependencies에 아래 코드 추가

```
compile 'org.androidannotations:androidannotations:4.1.0'
```

![img](https://cdn-images-1.medium.com/max/2000/1*bVW0foXiv0NmNP1mMouIlA.png)

- 기본 생성되는 MainActivity를 다음과 같이 수정합니다.

```java
@EAActivity(R.layout.activity_main) // 기본 레이아웃
public class MainActivity extends AppCompatActivity {
  // 내용  
}
```
![img](https://cdn-images-1.medium.com/max/2000/1*sBFbr7j2bZRCwxmqgEh_YA.png)


- AndroidManifest.xml 에서 activity 이름을 MainActivity -> MainActivity_ 로 바꿔서 Rebuild를 해줍니다.

![img](https://cdn-images-1.medium.com/max/2000/1*aVZS7mbFlPv63ExbkTzH0w.png)

### 마무리
이렇게 하면 Android Annotations 초기 설정이 끝납니다. 버전 부분은 달라질 수 있습니다. AndroidAnnotations는 기본적으로 클래스명_ 과 같이 형태를 가지고 있습니다. 이후 생성되는 class는 자동으로 _ 가 붙어서 생성이 됩니다.
