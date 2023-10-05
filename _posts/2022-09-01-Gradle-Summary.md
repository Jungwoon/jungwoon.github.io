---
layout: post
title: "그레이들(Gradle) 내용 정리"
image: '/assets/img/'
description: 'Gradle Summary'
tags:
- Gradle
- Build
categories:
- Build
---

안드로이드 프로젝트를 만들때 자동으로 생성이 되고 필요한 부분만 수정하면서 쓰다 보니 자세히 알지 못했었는데, 좀 자세히 알아두면 좋겠다 해서
[그레이들(Gradle)](https://search.shopping.naver.com/book/catalog/32442250829) 를 책을 읽고 공부하면서 알게 된 부분에 대해서 정리해 보고자 합니다.

---

## 그레이들(Gradle)

그레이들은 빌드 도구 중 하나로 빌드를 할 때 다양하게 도와주는 역할을 합니다. 기존의 `Maven`이나 `Ant`와 같은 서비스들도 빌드 도구들 입니다.

> 빌드 vs 컴파일
>
> 컴파일 : 작성한 소스코드를 컴퓨터가 이해할 수 있는 기계어로 변환하는 작업
>
> 빌드 : 실행가능한 소프트웨어 결과물을 만드는 일련의 작업 (컴파일, 테스트, 배포, 문서화 등)

### 특징
- 스크립트 기반 언어
- 그루비 기반이기 때문에 문법 구조가 자바와 비슷 (현재는 코틀린 kts도 지원)
- 기존의 빌드 도구들인 앤트나 메이븐 등에 대하여 이식하여 사용할 수 있음
- DSL 문법 제공

---

### 빌드 도구의 작업 수행 과정

![](https://miro.medium.com/max/1400/1*qtitLh8idmmjFBGpeKjJ7g.png)

---

### 그레이들의 스크립트

- 초기화 스크립트(init script)
  - 내용 : 사용자 정보, 실행 환경, 초기 선언 값 설정 등 초기 설정 정보를 기술하고 정의
  - 기본 객체 : Gradle 객체
  - 파일명 : init.gradle
- 설정 스크립트(setting script)
  - 내용 : 해당 파일로 싱글 프로젝트인지, 멀티 프로젝트인지 판단
  - 기본 객체 : Settings 객체
  - 파일명 : settings.gradle
- 빌드 스크립트(build script)
  - 내용 : 빌드 수행과 관련된 의존 관계 정의, 태스크 정의 등의 내용이 기술, `settings.gradle`이나 `init.gradle`파일을 참조하거나 상호작용
  - 기본 객체 : Project 객체
  - 파일명 : build.gradle

---

### buildSrc

빌드 스크립트들이 공통으로 참조하게 되는 내용을 모아두는 디렉토리, 안드로이드 프로젝트에서는 여러 모듈에서 공통으로 사용할 버전 정보를 모아놓는 용도로 사용

---

### 그레이들의 빌드 수행

1. 명령어 해석/수행
   1. 전달받은 명령어를 해석하고 수행
2. 그레이들 실행
   1. 실행 옵션
      1. CLI (default)
      2. 데몬 (--daemon)
      3. GUI (--gui)
   2. 실행 옵션 우선순위 : 내장 옵션 > GUI > 데몬
3. 스크립트 초기화
   1. 스크립트 파일 확인 및 읽기
   2. 멀티 or 싱글 프로젝트 판단
   3. Project 객체 생성
   4. 명령어 옵션 및 인수 설정
4. 프로젝트 설정
   1. 참조 중인 라이브러리 확인
   2. 태스크 객체 및 태스크 그래프 생성 (** 태스크 그래프 : 태스크들 사이의 의존 관계를 표현한 그래프)
5. 태스크 실행
   1. 태스크 추출
   2. 태스크 실행
6. 결과 출력

---

### 그레이들의 스크립트 파일 구조

그레이들의 스크립트는 그루비 스크립트로 크게 두 가지 구성 요소로 되어 있는데 처리문 영역과 스크립트 블록 영역으로 나뉩니다.

- 처리문 : 일반적인 프로그래밍에서의 지역 변수, 속성 정의 및 설정, 메서드 등
- 스크립트 블록 : 특성한 그레이들의 프로젝트를 빌드하기 위한 부분

```java
// 처리문
def id = 'gradle'

// 스크립트 블록
repositories {
    mavenCentral()
}
```
---

### 그레이들의 주요 스크립트 블록

|    스크립트 블록     | 주요 내용                 |
|:--------------:|:----------------------|
|  repositories  | 저장소 설정                |
|  dependencies  | 의존 관계 설정              |
|  buildscript   | 빌드 스크립트 클래스 패스 설정     |
|   initscript   | 초기화 스크립트 설정           |
| configurations | 환경 구성 설정              |
|  allprojects   | 서브 프로젝트 포함 전체 프로젝트 설정 |
|  subprojects   | 서브 프로젝트 설정            |
|   altifacts    | 빌드 결과에 대한 설정          |

---

### 그레이들의 변수

|   변수    | 설명                    | 사용 범위            |
|:-------:|:----------------------|:-----------------|
|  지역 변수  | 선언된 부분에서 영향력 있는 변수    | 그레이들의 모든 스크립트 파일 |
| 시스템 속성  | 시스템 관련 정보를 저장하는 변수    | 그레이들의 모든 스크립트 파일 |
|  확장 속성  | 도메인 객체 확장 용도로 사용하는 변수 | 그레이들의 모든 스크립트 파일 |
| 프로젝트 속성 | 프로젝트에서 사용하는 변수        | 빌드 스크립트          |

** 확장 속성

도메인 객체의 속성을 추가할 때 사용하며 ExtraPropertiesExtension객체와 관련이 있습니다. ext 키워드와 함께 사용할 수 있습니다.

```java
// 확장 속성 지정 방법
ext {
    extpro1 = 'pro1'
    extpro2 = 'pro2
}

// 사용방법
println '속성값 1 : ' + ext.extpro1
println '속성값 2 : ' + ext.extpro2
```

---

### 그레이들의 도메인 객체

#### 1. Project 객체
build.gradle과 일대일 관계로 프로젝트의 모든 정보를 참조해야 하기 때문에 다른 도메인 객체보다 다양한 속성과 메서드 등을 제공

1. 빌드를 수행하기 위한 Settings 객체 생성
2. settings.gradle 스크립트 파일이 있을 경우 Settings 객체와 비교
3. 구성된 Settings 객체를 이용하여 Project 객체의 계층 구조 생성
4. 프로젝트가 멀티 프로젝트일 경우, 부모 프로젝트로부터 Project 객체를 생성하고 다음으로 자식 프로젝트의 Project 객체를 생성

Project 객체의 속성

| 속성            | 설명                                                                     |
|:--------------|:-----------------------------------------------------------------------|
| version       | 프로젝트나 결과물의 버전 (설정 없을 경우 specified)                                     |
| description   | 프로젝트 설명                                                                |
| name          | 프로젝트의 이름                                                               |
| state         | 프로젝트 빌드 상태 (`NOT EXECUTED`, `EXECUTING`, `EXECUTED`, `FAILED`)         |
| status        | 프로젝트 결과물의 상태 (`NOT EXECUTED`, `EXECUTING`, `EXECUTED`, `FAILED`)       |
| path          | 프로젝트 경로 (구분자 ':')                                                      |
| projectDir    | 프로젝트 기준 디렉토리                                                           |
| group         | 프로젝트가 속한 그룹 (특별한 경우만 지정, 루트 프로젝트는 공백문자, 하위 프로제긑는 루트 프로젝트나 부모 프로젝트로 지정) |
| buildDir      | 프로젝트 밀드 디렉토리 (모든 결과물이 생성되는 디렉토리, 기본값: projectDir/build)                |
| plugins       | Project 객체에 적용된 플러그인의 컨테이너                                             |
| project       | 기준 프로젝트 참조                                                             |
| rootProject   | 루트 프로젝트 참조                                                             |
| prarent       | 기준 프로젝트의 상위 프로젝트 참조                                                    |
| childProjects | 기준 프로젝트의 하위 프로젝트 참조 (Map 형식)                                           |
| allprojects   | 기준 프로젝트에 포함된 모든 프로젝트 참조 (Set 형식)                                       |
| subprojects   | 기준 프로젝트 이하의 모든 프로젝트 참조 (Set 형식)                                        |


Project 객체의 기타 속성

| 속성           | 설명                                                               |
|:-------------|:-----------------------------------------------------------------|
| defaultTasks | 프로젝트 기본 수행 태스크 설정                                                |
| repositories | 의존 관계를 확인하고 프로젝트에서 생성 또는 필요한 라이브러리 등을 업로드하기 위해 사용되는 저장소를 생성 및 설정 |
| tasks        | 프로젝트의 태스크                                                        |
| ant          | AntBuilder를 위해 사용되는 속성 (빌드 파일에서 Ant를 실행 가능)                      |

Project 객체의 API

| API                                 | 설명                                             |
|:------------------------------------|:-----------------------------------------------|
| project(path)                       | 지정된 경로의 프로젝트에 대하여 설정 (상대 경로 가능)                |
| project(path, configureClosure)     | 지정된 경로의 프로젝트에 대하여 클로저를 사용하여 프로젝트 구성 (상대 경로 가능) |
| absoluteProjectPath(path)           | 절대 경로를 변환하여 프로젝트 확인                            |
| apply(closure)                      | 플러그인이나 스크립트를 적용                                |
| configure(object, configureClosure) | 클로저를 통하여 설정된 상태를 이용하여 객체를 구성                   |
| subprojects(action)                 | 해당 프로젝트의 하위 프로젝트 설정                            |
| task(name)                          | 주어진 이름으로 태스크를 생성하고 프로젝트에 추가                    |
| afterEvaluate(action)               | 프로젝트가 평가된 직후 추가                                |
| beforeEvaluate(action)              | 프로젝트가 평가되기 바로 직전 추가                            |



#### 2. Task 객체

Task는 컴파일하거나 Javadoc을 생성하는 것처럼 빌드를 수행하기 위한 하나의 작업 단위를 나타냅니다.

Task 객체는 실행을 위해 execute(Task)가 내부적으로 호출되어 지정된 태스크 순서대로 실행됩니다.

Task 객체의 속성

| 속성             | 설명                                          |
|:---------------|:--------------------------------------------|
| name           | 태스크 이름 (프로젝트 내에서 태스크를 고유하게 식별)              |
| description    | 태스크 설명                                      |
| group          | 태스크가 속한 그룹                                  |
| path           | 태스크 경로 (구분자 : ':')                          |
| action         | 태스크에 의해 실행되는 순서 지정                          |
| dependOn       | 태스크의 의존 관계 지정 및 표시                          |
| enabled        | 태스크의 실행 여부 설정                               |
| finalzedBy     | 해당 태스크 실행 후 최종 수행할 태스크 지정                   |
| inputs         | 태스크 입력 정보                                   |
| mustRunAfter   | 태스크 실행 순서 제어 (강제적)                          |
| shouldRunAfter | 태스크 실행 순서 제어 (비강제적)                         |
| state          | 태스크의 실행 상태 (실행 여부, 실패 여부 등 태스크 실행 관련 정보 제공) |


Task 객체의 API

| API                       | 설명                                                          |
|:--------------------------|:------------------------------------------------------------|
| doFirst(action)           | 해당 태스크를 실행하기 위한 Action 객체 리스트의 처음 부분에 위치하여 태스크가 실행될 때 먼저 처리 |
| doLast(action)            | 해당 태스크를 실행하기 위한 Action 객체 리스트의 맨 마지막 부분에 위치하여 실행            |
| deleteAllAction(action)   | 해당 태스크의 Action 객체 리스트 모두를 제거                                |
| leftShift(action)         | << 연산자를 사용하여 leftShift() 호출, Action 객체 리스트의 마지막 부분에 추가시킴    |
| property(propertyName)    | 태스크의 지정된 속성의 값을 출력                                          |
| setProperty(name, value)  | 태스크의 속성 설정                                                  |
| hasProperty(propertyName) | 태스크의 속성 설정지정된 속성을 가졌는지를 확인                                  |
| dependsOn(paths)          | 태스크 간의 의존 관계                                                |
| onlyIf(onlyIfSpec)        | 지정된 조건을 만족할 경우 태스크 실행                                       |


#### 3. Gradle 객체

그레이들 자체의 실행 환경을 나타내는 객체로 초기화 스크립트와 연관이 있으며 초기화 스크립트로부터 위임되어집니다.

Gradle 객체의 속성

| 속성                | 설명                                                      |
|:------------------|:--------------------------------------------------------|
| gradle            | Gradle 객체 반환, 초기화 스크립트에서 Gradle 속성과 메서드에 명시적으로 접근할 때 사용 |
| gradleHomeDir     | Gradle의 홈 디렉토리 경로                                       |
| gradleUserHomeDir | 사용자의 홈 디렉토리 경로                                          |
| gradleVersion     | 현재 사용중인 Gradle 버전                                       |
| includeBuilds     | 포함된 빌드 관련 정보                                            |
| plugins           | Gradle 객체에 적용된 플럭읜 컨테이너                                 |
| rootProject       | 루트 프로젝트 위치                                              |
| startParameter    | 빌드 수행 관련 파라미터 정보                                        |
| taskGraph         | 태스크 그래프 정보 표시                                           |


Gradle 객체의 API

| API                                      | 설명                                    |
|:-----------------------------------------|:--------------------------------------|
| addBuildListener(buildListener)          | BuildListener 추가, 빌드 실행 중 발생되는 이벤트 전달 |
| addListener(listener)                    | 지정된 리스너 추가, 지정된 인터페이스 구현 가능           |
| removeListener(listener)                 | 빌드로부터 지정된 리스너 제거                      |
| addProjectEvaluationListener(listener)   | 프로젝트 평가 리스너                           |
| afterProject()                           | 프로젝트 평가한 후 바로 호출할 작업 추가               |
| apply()                                  | 플러그인 또는 스크립트 적용                       |
| beforeProject()                          | 프로젝트가 평가되기 바로 전에 호출할 작업 추가            |
| buildFinished()                          | 빌드가 완료될 때 호출할 작업 추가                   |
| projectsEvaluated()                      | 모든 프로젝트를 평가한 후 호출할 작업 추가              |
| projectsLoaded()                         | 프로젝트가 빌드를 위해 로드된 후 호출할 작업 추가          |
| settingsEvaluated()                      | 프로젝트 관련 빌드 설정이 로드 및 평가될 때 호출할 작업 추가   |
| useLogger()                              | Logger를 사용할 수 있도록 제공                  |