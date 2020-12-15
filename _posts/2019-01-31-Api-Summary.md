---
layout: post
title: "API 관련 내용 정리"
image: '/assets/img/'
description: 'API Summary'
tags:
- Infra, API, Cloud
categories:
- Infra, API, Cloud
---

이번에 업무에서 API와 관련된 기술에 대해서 정리하도록 하겠습니다.

> 해당 포스팅은 [그림으로 배우는 클라우드 인프라와 API의 구조](https://book.naver.com/bookdb/book_detail.nhn?bid=12071725) 책의 내용을
정리한 내용입니다.

---

### API란?

#### API (Application Program Interface)

- 정의 : 다른 소프트웨어를 제어하기 위해 미리 `약속된 인터페이스`나 `규약`
- 효과 : 반복되는 코드를 `API`로 이용함으로써 소스 코드의 재사용성을 높여 개발 `생산성`을 올릴 수 있음

![](https://cdn-images-1.medium.com/max/1600/1*fYxRTG8m8lUGdoAqhreXbQ.png)

---

#### Web API

- `HTTP/HTTPS`와 같은 웹 프로토콜을 사용하여 네트워크를 통해 호출하는 API, 웹에서 접근 가능한 특정 `URI`에 `HTTP 요청`을 보내고 이에 대한 응답으로
필요한 정보를 얻어내는 것
- 클라우드 서비스에서 많이 사용

---

### SOA (Service Oriented Architecture)

- 대규모 시스템 구축 시 업무상의 일에 해당하는 부분을 소프트웨어의 서비스로 판단하여 네트워크에 연동하며 시스템을 구축해 가는 방법론
- 폐쇄적인 네트워크 안에서 사내 정보 시스템들을 연계


### 웹 API의 통신 방법 변화

```
SOAP -> REST
```

- SOAP
- REST

---

### SoE (System of Engagement)

- 사내의 데이터를 외부로 제공함으로써 비즈니스 창출을 목표로 함
- 회사들의 API 공개 (Google, Facebook....)

---

### 웹 API의 구성 요소

- 인증 처리 : `액터`를 식별하는 처리 과정
- 제어할 대상 : `리소스`에 해당하며 `URI`로 표현
- 제어 행위
    - `액션`에 해당하며 주로 `HTTP Method`로 표현
    - CRUD (Create, Read, Update, Delete)

---

### URI (Uniform Resource Identifier)

- `API`에서 `리소스를 지정`할 때 사용
- `네트워크 부분 + 경로 부분`

---

### 도메인

- 네트워크상에서 자원의 위치를 표현할 때 사용
- IP 주소와 같이 숫자 형태가 아니라 사람이 식별하기 쉬운 문자 형태
- 오른쪽 -> 왼쪽으로 읽으면 계층 구조가 표현이 됩니다. (Domain Tree)
- 각 도메인마다 명명 규칙이 있습니다.

![](https://cdn-images-1.medium.com/max/2600/1*9cJxMK_qIpdl-wNjUMtBfg.png)

---

### DNS

- 도메인과 IP주소 사이에 변환 기능을 해주는 것
- TCP/IP에서는 통신을 할때 도메인을 IP주소로 변환해서 사용
- 정방향 조회 : 도메인 -> IP 주소 조회
- 역방향 조회 : IP 주소 -> 도메인 조회
- 클라우드 환경에서는 주로 정방향 조회가 일어납니다.

---

### DNS Round Robin

- FQDN과 IP 주소가 1:N인 관계는 대규모 시스템에서 활용
- 대량의 API 요청이 들어오면 하나의 IP주소 만으로 대응이 힘들 수 있음
- 여러개의 FQDN에 대응하는 IP Pool을 만듦
- 요청이 들어올때 IP Pool 에서 IP 주소를 돌려가며 사용
- 클라우드 환경에서는 CDN이나 Load Balancer를 이용

---

### 도메인과 IP 주소의 변환 방법

- Resolver : DNS에서 이름 변환 기능을 제공하는 것
- Stub Resolver : 클라이언트 측의 이름 변환 서비스
- Full Resolver : 서버 측의 이름 변환 서비스
- 캐시 DNS : PC의 네트워크 설정에서 지정된 DNS를 의미하며 로컬 네임 서버라고도 함
- 변환 방법 : 
    1. API 호출 
    2. 클라이언트에서 `Stub Resolver`에게 이름을 변환
    3. 캐시 DNS서버 도메인에 해당하는 IP 주소를 물어봄
    4. 만약 못 찾으면 루트 도메인 -> 최하위 도메인가지 각 DNS에게 물어봄 (= DNS Query)
- 재귀적 질의 (Recursive Query) : 클라이언트에서 DNS 캐시 서버로 질의
- 비재귀적 질의 (Non-recursive Query) : DNS 캐시 서버 자신이 루트 DNS 서버나 다른 DNS 서버로 질의
- DNS 서버의 관리
    - `.kr` :  한국인터넷정보센터가 관리
    - `.com` : 기업을 의미하고 레지스트리에서 관리
- 직접 구매한 도메인을 사용하고 슾아면 도메인의 `CNAME`을 등록

---

### DNS 레코드

DNS 서버에서 IP주소와 도메인을 짝 지은 설정 정보를 DNS 레코드라고 합니다.

| DNS 레코드 | 의미
| :---: | :---
| A | FQDN에 대한 IPv4 정보
| AAAA | FQDN에 대한 IPv6 정보
| CNAME | FQDN 별칭 정보
| PTR | FQDN 역방향 질의 정보
| SOA | DNS 영역에 대한 권한 정보
| NS | DNS 서버 정보
| MX | 이메일 서버 정보
| SPF | 이메일 발신자 자격 증명 정보
| SRV | 프로토콜, 포트 번호 등의 정보
| TXT | 호스트의 부가 정보

---

### URI (Uniform Resource Identifier)

- 웹 API는 리소스 지정시 `URI`를 사용
- URI 종류
    - URL (Uniform Resource Locator)
        - 네트워크상에 있는 리소스의 위치를 알려줄때 사용
    - URN (Uniform Resource Name)
        - 네트워크와 상관없이 리소스의 이름을 BNF (Backus-Naur form)이라 하는 표기법으로 정의
        
![](https://cdn-images-1.medium.com/max/2400/1*Y_v4Vsn248mvzBLt0eX3sA.png)

---

### 엔드 포인트

- 클라우드에 공개된 API를 실행하기 위해 접속하는 연결 접점을 엔드포인트라고 합니다.
- 엔드포인트는 FQDN으로 표현되는데 API의 접점으로 일종의 게이트웨이 역할을 합니다.
- 클라우드 환경에서는 인프라 컴포넌트를 제어하기 위해 엔드포인트에 접속합니다.
- 엔드포인트는 주로 IP주소가 아닌 도메인으로 접속을 합니다.
    - 도메인을 씀으로써 IP주소를 은폐할 수 있습니다.
    - IP주소가 바뀔때 자동으로 바뀐 IP주소로 접속하도록 할 수 있습니다.
    

---

### 리소스 지정 방법
- API를 호출하는 URL에 리소스를 식별할 수 있는 정보
    - `REST API` - 계층화된 경로 정보로 리소스를 식별
        - 리소스의 관계 정보를 URL의 경로에 계층 형태로 표현하는 방법
        - `<endpoint>/.../<service-name>/<object-name>`
    - `Query API` - 쿼리 파라미터로 리소스를 식별
        - 액션에 해당하는 API를 호출할때 옵션을 추가
        - `<endpoint>/.../?Action=???&ID=???`

---

### HTTP

- `Stateless` : 통신할 때 상태를 유지하는 특징
- `Cookie` : HTTP 통신을 하는 클라이언트나 브라우저에 서버가 보낸 정보를 저장하게 하는 기법
- `Cookie Persistence` : 이전에 접속한 적이 있는 웹 서버로 다시 접속할때 쿠키 사용
- `Keep alive` : HTTP의 실제 접속은 TCP에서 이뤄지는데, 보통 요청때마다 매번 접속을 맺고 끊기 때문에 지속적인 통신을 위해서, 명시적으로 TCP 접속을
끊으라고 할때까지 TCP의 접속을 유지하는 기법

---

### HTTP Request

![](https://cdn-images-1.medium.com/max/600/1*ZxgzAbg81dyUPIXAekP15Q.png)

- 요청 행 (Request Line)
    - Method : URI에 대한 제어 행위이자 액션
    - Request URI : 제어할 리소스
        - URI를 FQDN과 경로로 표현하는 방법
        - URI를 절대 경로로 표현하는 방법
    - HTTP Version : HTTP 버전, 주로 HTTP/1.1
- 요청 헤더 (Request Header) : 쿠키나 킵얼라이브와 같은 HTTP 통신과 관련된 제어 정보나 메타 데이터
- 메시지 바디 (Message Body) : 실제로 송수신하려는 데이터 부분

---

### HTTP Response

![](https://cdn-images-1.medium.com/max/1600/1*sC2mJpdGMqtytKuNPjsPew.png)

- 상태 행 (Status Line) : HTTP 요청에 대한 결과가 정상인지, 비정상인지 ex) 200 - 정상, 404 - 찾지못함...
- 응답 헤더 (Response Header) : 웹 서버가 클라이언트에 서버의 정보를 전달
- 메시지 바디 (Message Body) : HTTP 요청에 대한 결과 데이터

---

### HTTP Method

| HTTP 메소드 | CRUD | CRUE 의미 | 의미
| :--- | :--- | :---: | :---
| POST | Create | 등록 | 리소스 생성
| GET | Read | 조회 | 리소스 조회
| PUT | Update | 수정 | 리소스 수정 (= 덮어씀)
| DELETE | Delete | 삭제 | 리소스 삭제
| HEAD | Read | 조회 | HTTP 헤더, 메타 정보 취득
| OPTION | Read | 조회 | 지원 메소드 확인
| PATCH | Update | 조회 | 리소스 일부 수정
| TRACE | - | - | 경로 확인
| CONNECT | - | - | 프록시로 터널링 요구

---

### POST

- POST 메소드는 리소스를 신규 생성할 때 사용합니다.
- 새로운 리소스 생성 = 새로운 URI를 생성
- POST 메소드의 처리가 성공하면 URI에 지정한 리소스 타입 하위의 신규 리소스가 만들어집고 해당하는 `식별 키`도 만들어집니다.

---

### GET

- GET 메소드는 리소스 정보를 조회할 때 사용하는 메소드
- URI에 리소스를 식별하는 키가 포함

---

### PUT

- PUT 메소드는 리소스 정보를 수정할 때 사용
- 내부 동작은 덮어쓰는 것에 가까움

---

### 중요) POST vs PUT

- POST 메소드는 리소스의 키를 지정하지 않아서, URI 수정 대상이 되는 리소스의 식별이 불가
- PUT 메소드는 리소스의 키를 지정할 수 있어서 리소스의 식별이 가능

---

### DELETE

- DELETE 메소드는 리소스를 삭제할 때 사용
- 리소스의 식별 키를 URI에 포함시켜 API로 호출하면 삭제가 됨

---

### HEAD

- 메타 데이터만 조회하고 싶을때 사용

---

### HTTP Header

- HTTP 헤더는 HTTP 통신에 필요한 부가 정보를 전달하기 위해 사용
- HTTP 헤더는 비표준 헤더도 추가 가능한데 이를 확장 헤더라고 함
- 확장 헤더는 `x-`와 같이 접두어를 붙임 (RFC 6648에서 `x-` 명명법은 폐지)

---

### HTTP Header 목록

| 범위 | 필드명 | 의미
| :---: | :--- | :---
| 공통 | Allow | 허용하는 HTTP 메소드
| 공통 | Cache-Control | 캐시 제어
| 공통 | Connection | 접속 제어
| 공통 | Content-Encoding | 바디 부분의 인코딩 방식
| 공통 | Content-Language | 바디 부분의 언어
| 공통 | Content-Length | 바디 부분의 길이 (byte)
| 공통 | Content-Location | 리다이렉트 행선지
| 공통 | Content-MD5 | 바디 부분의 메시지 다이제스트
| 공통 | Content-Range | 바디 부분의 범위
| 공통 | Content-Type | 바디 부분의 MIME 타입
| 공통 | Date | 작성 일시
| 공통 | Expires | 유효기간
| 공통 | Last-Modify | 최종 수정 일시
| 공통 | Pragma | 메시지 디렉티브
| 공통 | Trailer | 메시지 마지막 푸터
| 공통 | Transfer-Encoding | 전송 인코딩 방식
| 공통 | Upgrade | 프로토콜 업그레이드
| 공통 | Via | 프록시 서버 정보
| 공통 | Warning | 에러 통지
| 요청 | Accept | 에러 통지
| 요청 | Accept-Charset | 처리할 수 있는 문자 캐릭터 셋
| 요청 | Accept-Encoding | 처리할 수 있는 인코딩 방식 목록
| 요청 | Accept-Language | 처리할 수 있는 언어
| 요청 | Authorization | 인증 정보
| 요청 | Expect | 기대하는 특정 동작
| 요청 | From | 송식자의 메일 주소
| 요청 | Host | 수신자의 호스트 정보(필수 값)
| 요청 | If-Match | ETag와 일치 여부 확인 조건
| 요청 | If-Modify-Since | 콘텐츠 수정 여부 확인 조건
| 요청 | If-None-Match | ETag와 일치 여부 확인 조건
| 요청 | If-Range | 갱신되지 않은 경우 엔티티 바이트 범위 요청
| 요청 | If-Unmodified-Since | 콘텐츠 수정 여부 확인 조건
| 요청 | Max-Forwards | 최대 홉 수
| 요청 | Proxy-Authorization | 프록시 인증 정보
| 요청 | Range | 엔티티 바이트 범위 요청
| 요청 | TE | 인코딩 우선 순위
| 요청 | User-Agent | 클라이언트 에이전트 정보
| 응답 | Accept-Ranges | 엔티티 바이트 허용 범위
| 응답 | Age | 엔티티 바이트 허용 범위
| 응답 | Etag | 엔티티 바이트 허용 범위
| 응답 | Location | 엔티티 바이트 허용 범위
| 응답 | Proxy-Authenticate | 엔티티 바이트 허용 범위
| 응답 | Retry-After | 엔티티 바이트 허용 범위
| 응답 | Server | 엔티티 바이트 허용 범위
| 응답 | Vary | 엔티티 바이트 허용 범위
| 응답 | WWW-Authenticate | 엔티티 바이트 허용 범위
| 요청 | Cookie | 웹 서버가 보낸 쿠키 값
| 응답 | Set-Cookie| 쿠키 정보 (도메인 및 유효기간)

--- 

### 중요한 헤더

- Host : 호출한 API를 받는 수신 측의 호스트를 의미
- Accept : API 호출 시, 응답으로 받고 싶은 미디어 타입을 정의, 예를 들어 클라이언트가 JSON 형식으로 받고 싶으면 헤더에 JSON을 설정하면 됩니다.
- Last-Modify : 리소스의 최근 갱신 정보
- If-계열 : 조건부 요청으로 활용
- Authorization : 인증에 사용
- Rage : 요청할 바디(엔티티) 데이터의 범위를 지정
- Etag : 엔티티 태그를 줄여 쓴 말로 바디(엔티티)의 데이터 변경 여부를 확인할 수 있는 메타 데이터
- Cache : 캐시를 제어

---

### HTTP 상태 코드

| 상태 코드 | 이름 | 의미
| :---: | :--- | :---
| 200 | OK | 기존에 있는 URI 대한 요청이 성공함 주로 GET이 사용됩 
| 201 | Create | 신규 URI에 댛나 리소스 생성이 성공함 주로 POST가 사용됨 
| 202 | Accepted | 요청은 접수되었으나 리소스 처리는 완료되지 않음 
| 204 | No Contents | 요청은 성공했으나 제공할 콘텐츠가 없음 
| 300 | Multiple Choices | 요청한 URI에 대해서 여러 개의 리소스가 존재함
| 301 | Move Permanently | 요청한 URI가 새 위치로 옮겨갔음, 새 위치는 Location 헤더에 명시됨
| 304 | Not Modified | 요청한 URI의 내용이 변경되지 않음
| 400 | Bad Request | 요청이 정상적이지 않음 API에서 정의되지 않은 요청이 들어옴
| 401 | Unauthorized | 인증 오류
| 403 | Forbidden | 접근 금지, 권한 밖의 접근을 시도함
| 404 | Not Found | 요청한 URI에 해당하는 리소스가 존재하지 않음
| 405 | Method Not Allowed | 메소드 오류, API에서 정의되지 않은 메소드를 호출함
| 406 | Not Acceptable | 처리 불가, Accept 헤더에 명시된 타입과 호환되지 않음
| 408 | Request Timeout | 요청 대기 시간 초과
| 409 | Conflict | 리소스 변경 시 정합성이 맞지 않음
| 429 | Too Many Requests | 요청 횟수 상한 초과
| 500 | Internal Server Error | 서버 내부 오류, 클라우드 측 로직에 문제 발생
| 502 | Bad Gateway | 게이트웨이 오류
| 503 | SErvice Unavailable | 서비스 이용 불가, 과부하가 걸리거나 다운됨
| 504 | Gateway Timeout | 게이트웨이 시간 초과

---

### SOAP (Simple Object Access Protocol)

- 주로 복잡한 비즈니스 로직을 웹 서비스 형태로 제공하는 SOA(Service Oriented Architecture)를 구성할 때 사용하는 기술
- SOAP로 통신하는 시스템은 잘 구조화된 SOAP 메시지를 URI를 통해 주고 받아서 복잡한 제어 처리
- 프로토콜은 주로 HTTP를 사용
- SOAP 메시지는 엔벨로프 (envelope)라는 일종의 봉투와 같은 구조 안에 헤더, 바디가 들어있는데, XML 형태로 구성됨
- 클라우드에서는 SOAP보다는 REST를 사용하는 추세

---

### REST

- URI를 호출할 때 HTTP 메소드를 기반으로 CRUD 조작을 하는 것처럼 비교적 간단한 제어를 할 때 사용
- 클라우드 분야에서는 기본적으로 REST를 사용

---

### XML

- HTTP 요청에 대한 응답 시 데이터를 표현하는 포맷 중 하나
- XML은 마크업 언어로 태그형태로 표현됩니다.
- 일반적으로 `SOAP`을 사용할 때 `XML`로 응답을 합니다.
- HTTP 요청시 `Accept 헤더`에 `application/xml`, `test/plain`과 같은 처리 가능한 미디어 타입으로 요청
- XML은 상세한 태그 정의가 가능하지만, 구문 자체가 길어져 데이터 양이 늘어나는 경향이 있습니다.
- API에 대해서는 클라우드 서비스 측에서 정의한 스키마를 참고
- 현재는 XML의 사용 빈도는 점점 줄어드는 추세
- XML 해석시, `XML Parser`가 필요한데, 일반적으로 `DOM(Document Object Model)`이나 `SAX(Simple API for XML)`를 사용

---

### JSON

- HTTP 요청에 대한 응답 시 데이터를 표현하는 포맷 중 하나
- 자바스크립트를 기반으로 한 데이터 정의 포맷으로 시작
- 객체를 배열 형태로 표현 가능
- 상대적으로 XML보다 가벼움
- JSON 해석시 여러 종류의 라이브러리 지원

---

### cURL

- OS에서 CLI 명령어로 HTTP 요청시 HTTP 프로토콜과 통신할 수 있는 프로그램
- 다음과 같은 형식으로 사용 `curl -X <method> -H <header> -u <user> -cacert <cafile> -d <body> <uri>`

| 옵션 | 설명
| :--- | :---
| `-X` | HTTP 메소드
| `-H` | HTTP 헤더
| `-i` | HTTP 헤더를 출력할 때 지정
| `-u` | 사용자 (인증 시 사용)
| `-cacert` | SSL용 인증서 (HTTPS 통신 시 사용)
| `-d` | 바디

---

### ROA (Resource Oriented Architecture)

- REST API의 사상을 기반으로 리소스 중심적인 API를 사용하는 아키텍처
- REST 기반 서비스를 위한 네 가지 설계 지침
    - 상태를 가지지 않도록 만든다 : 상태를 가지지 않으므로 구현이 쉽고 캐시를 사용할 수 있어 성능이 우수
    - URI는 디렉토리 구조처럼 만든다 : URI의 가독성이 좋고 리소스의 구조를 이해하기 쉽다.
    - HTTP 메소드를 명시적으로 사용한다 : 리소스의 상태 변화를 HTTP 메소드를 활용하여 리소스 중심으로 처리하고 별도의 메소드를 사용해서 행위 중심으로 처리하지 않는다.
    - 응답할 때는 XML이나 JSON을 사용한다 (둘 다 사용해도 무방) : 데이터 표현을 정규화해서 다른 언어나 기술 구조에서도 데이터를 활용할 수 있도록 한다.
- REST API의 특징
    - 비동기 : 먼저 보낸 요청이 뒤에 보낸 요청보다 반드시 먼저 도착한다고 보장할 수 없기 때문에 비동기로 처리해야 함
    - 멱등성 : API를 몇 번을 호출하더라도 리소스에 변경이 발생하지 않는 한 같은 결과가 나와야 함
    - 재시도 : 오류가 발생하더라도 에러 코드를 보고 재시도를 할 수 있음

