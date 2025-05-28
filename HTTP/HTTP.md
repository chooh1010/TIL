## HTTP

### HyperText Transfer Protocol

* HTML, TEXT
* IMAGE, 음성, 영상, 파일
* JSON, XML (API)
* 거의 모든 형태의 데이터 전송 가능
* 서버간에 데이터를 주고 받을 때도 대부분 HTTP 사용

기반 프로토콜

* TCP: HTTP/1.1, HTTP/2
* UDP: HTTP/3

### HTTP 특징

* 클라이언트 서버 구조

* 무상태 프로토콜(스테이스리스), 비연결성

  Stateless

  * 서버가 클라이언트의 상태를 보존X
  * 장점: 서버 확장성 높음(스케일 아웃)
  * 단점: 클라이언트가 추가 데이터 전송

  비연결성

  * 서버 자원을 효율적으로 사용할 수 있음
  * TCP/IP 연결을 새로 맺어야 함
  * 지금은 HTTP 지속 연결(Persistent Connections)로 문제 해결

* HTTP 메시지

* 단순함, 확장 가능

### HTTP 메시지

* start-line = request-line / status-line

  요청 메시지 - GET /search?q=hello&hl=ko HTTP/1.1

  * request-line = method SP request-target SP HTTP-version CRLF

  응답 메시지 - HTTP/1.1 200 OK

  * status-line = HTTP-version SP status-code SP reason-phrase CRLF
  * 이유 문구: 사람이 이해할 수 있는 짧은 상태 코드 설명 글

* ( header-field CRLF ) - Host: www.google.com

  HTTP 헤더

  * HTTP 전송에 필요한 모든 부가정보

  * header-field = field-name ":" OWS field-value OWS(띄어쓰기 허용)
  * field-name은 대소문자 구분 없음

* CRLF

* [ message-body ]

  * 실제 전송할 데이터
  * HTML문서, 이미지, 영상, JSON 등등 byte로 표현할 수 있는 모든 데이터 전송 가능

### HTTP 메서드

#### 리소스와 행위를 분리

가장 중요한 것은 리소스를 식별하는 것

* URI는 리소스만 식별
* 리소스와 해당 리소스를 대상으로 하는 행위를 분리
* 행위는 HTTP 메서드로 구분

#### HTTP 메서드 종류

* GET: 리소스 조회
  * 서버에 전달하고 싶은 데이터는 query를 통해서 전달
* POST: 요청 데이터 처리, 주로 등록에 사용
  * 새 리소스 생성(등록)
  * 요청 데이터 처리
    * 단순히 데이터를 생성하거나, 변경하는 것을 넘어서 프로세스를 처리해야 하는 경우
    * 예) 주문에서 결제완료 -> 배달시작 -> 배달완료 처럼 단순히 값 변경을 넘어 프로세스의 상태가 변경되는 경우
    * POST의 결과로 새로운 리소스가 생성되지 않을 수도 있음
    * 예) POST /orders/{orderId}/start-delivery (컨트롤 URI)
  * 다른 메서드로 처리하기 애매한 경우
    * 예) JSON으로 조회 데이터를 넘겨야 하는데, GET 메서드를 사용하기 어려운 경우
    * 애매하면 POST
* PUT: 리소스를 대체, 해당 리소스가 없으면 생성
  * 클라이언트가 리소스 위치를 알고 URI 지정
* PATCH: 리소스 부분 변경
* DELETE: 리소스 삭제
* HEAD: GET과 동일하지만 메시지 부분을 제외하고, 상태 줄과 헤더만 반환
* OPTIONS: 대상 리소스에 대한 통신 가능 옵션(메서드)을 설명(주로 CORS에서 사용)
* CONNECT: 대상 자원으로 식별되는 서버에 대한 터널을 설정
* TRACE: 대상 리소스에 대한 경로를 따라 메시지 루프백 테스트를 수행

#### HTTP 메서드의 속성

* 안전(Safe Methods)
  * 호출해도 리소스를 변경하지 않는다.
  * GET, HEAD
* 멱등(Idempotent Methods)
  * f(f(x)) = f(x)
  * 한 번 호출하든 두 번 호출하든 100번 호출하든 결과가 똑같다.
  * 자동 복구 메커니즘
  * 외부 요인으로 중간에 리소스가 변경되는 것 까지는 고려하지 않는다.
  * GET, PUT, DELETE
* 캐시가능(Cacheable Methods)
  * 응답 결과 리소스를 캐시해서 사용해도 되는가?
  * GET, HEAD, POST, PATCH 캐시가능
  * 실제로는 GET, HEAD 정도만 캐시로 사용
    * POST, PATCH는 본문 내용까지 캐시 키로 고려해야 하는데, 구현이 쉽지 않음

#### HTTP 메서드 활용

클라이언트에서 서버로 데이터 전송

* 쿼리 파라미터를 통한 데이터 전송
  * GET
  * 주로 정렬 필터(검색어)
* 메시지 바디를 통한 데이터 전송
  * POST, PUT, PATCH
  * 회원 가입, 상품 주문, 리소스 등록, 리소스 변경

#### HTML Form 데이터 전송

* Content-Type: application/x-www-form-urlencoded 사용
  * 전송 데이터를 url encoding 처리
* Content-Type: multipart/form-data
  * 파일 업로드 같은 바이너리 데이터 전송시 사용
  * 다른 종류의 여러 파일과 폼의 내용 함께 전송 가능
* GET, POST만 지원

#### HTTP API 데이터 전송

* POST, PUT, PATCH: 메시지 바디를 통해 데이터 전송
* GET: 조회, 쿼리 파라미터로 데이터 전달
* Content-Type: application/json을 주로 사용
  * TEXT, XML, JSON 등등

### HTTP API 설계

POST 기반 등록

* 서버가 리소스 URI 결정
* 컬렉션(Collection)
  * 서버가 관리하는 리소스 디렉토리

PUT 기반 등록

* 클라이언트가 리소스의 URI를 결정.
* 스토어(Store)
  * 클라이언트가 관리하는 리소스 저장소

#### HTML FORM 사용

* GET, POST만 지원
* 컨트롤 URI
  * GET, POST만 지원하므로 제약이 있기 때문에 동사로 된 리소스 경로 사용
  * POST의 /new, /edit, /delete가 컨트롤 URI
  * HTTP 메서드로 해결하기 애매한 경우 사용(HTTP API 포함)

#### 참고하면 좋은 URI 설계 개념

https://restfulapi.net/resource-naming

### HTTP 상태코드

#### 2xx (Successful)

클라이언트의 요청을 성공적으로 처리

* 200 OK
* 201 Created 
  * 요청 성공해서 새로운 리소스가 생성됨
* 202 Accepted
  * 요청이 접수되었으나 처리가 완료되지 않았음
  * 배치 처리 같은 곳에서 사용
* 204 No Content
  * 서버가 요청을 성공적으로 수행했지만, 응답 페이로드 본문에 보낼 데이터가 없음
  * 웹 문서 편집기에서 save 버튼

#### 3xx (Redirection)

요청을 완료하기 위해 유저 에이전트의 추가 조치 필요

* 영구 리다이렉션 (301, 308)

  * 리소스의 URI가 영구적으로 이동
  * 검색 엔진 등에서도 변경 인지
  * 301 Moved Permanently
    * 리다이렉트시 요청 메서드가 GET으로 변하고, 본문이 제거될 수 있음(MAY)
  * 308 Permanent Redirect
    * 리다이렉트시 요청 메서드와 본문 유지(처음 POST를 보내면 리다이렉트도 POST)

* 일시적인 리다이렉션 (302, 307, 303)

  * 리소스의 URI가 일시적으로 변경

  * 검색 엔진 등에서 URL을 변경하면 안됨

  * 302 Found

    * 리다이렉트시 요청 메서드가 GET으로 변하고, 본문이 제거될 수 있음(MAY)

  * 307 Temporary Redirect

    * 302와 기능은 많음
    * 리다이렉트시 요청 메서드와 본문 유지(요청 메서드를 변경하면 안된다. MUST NOT)

  * 303 See Other

    * 302와 기능은 같음
    * 리다이렉트 요청 메서드가 GET으로 변경

  * PRG: Post/Redirect/Get

    * POST로 주문후에 새로 고침으로 인한 중복 주문 방지
    * POST로 주문후에 주문 결과 화면을 GET 메서드로 리다이렉트

    * 새로 고침 해도 GET으로 결과 화면만 조회

* 기타 리다이렉션 (300, 304)

  * 300 Multiple Choices: 안씀
  * 304 Not Modified
    * 캐시를 목적으로 사용
    * 클라이언트에게 리소스가 수정되지 않았음을 알려준다. 따라서 클라이언트는 로컬 PC에 저장된 캐시를 재사용한다. (캐시로 리다이렉트 한다.)
    * 304응답은 응답에 메시지 바디를 포함하면 안됨.

#### 4xx (Client Error)

클라이언트 오류

* 클라이언트의 요청에 잘못된 문법등으로 서버가 요청을 수행할 수 없음
* 오류의 원인이 클라이언트에 있음
* 재시도가 실패함
* 400 Bad Request
  * 클라이언트가 잘못된 요청을 해서 서버가 요청을 처리할 수 없음
  * 요청 파라미터가 잘못되거나, API 스펙이 맞지 않을 때
* 401 Unauthorized
  * 클라이언트가 해당 리소스에 대한 인증이 필요함
* 403 Forbidden
  * 서버가 요청을 이해했지만 승인을 거부함
  * 주로 인증 자격 증명은 있지만, 접근 권한이 불충분한 경우
* 404 Not Found
  * 요청 리소스를 찾을 수 없음
  * 또는 클라이언트가 권한이 부족한 리소스에 접근할 때 해당 리소스를 숨기고 싶을 때

#### 5xx (Server Error)

서버 오류

* 서버 문제로 오류 발생
* 서버에 문제가 있기 때문에 재시도하면 성공할 수도 있음
* 500 Internal Server Error
  * 서버 내부 문제로 오류 발생
* 503 Service Unavailable
  * 서비스 이용 불가
  * 서버가 일시적인 과부하 또는 예정된 작업으로 잠시 요청을 처리할 수 없음
  * Retry-After 헤더 필드로 얼마뒤에 복구되는지 보낼 수도 있음

### HTTP 헤더

헤더 분류

* General 헤더: 메시지 전체에 적용되는 정보, 예) Connection: close
* Request 헤더: 요청 정보, 예) User-Agent: Mozilla/5.0
* Response 헤더: 응답 정보, 예) Server: Apache
* Entity 헤더: 엔티티 바디 정보, 예) Content-Type: text/html, Content-Length: 3423

#### HTTP BODY

* 메시지 본문(message body)을 통해 표현 데이터 전달
* 메시지 본문 = payload
* 표현은 요청이나 응답에서 전달할 실제 데이터
* 표현 헤더는 표현 데이터를 해석할 수 있는 정보 제공
  * 데이터 유형(html, json), 데이터 길이, 압축 정보 등등

#### 표현

* Content-Type: 표현 데이터 형식
  * text/html; charset=utf-8
  * application/json
* Content-Encoding: 표현 데이터의 압축 방식
  * gzip
* Content-Language: 표현 데이터의 자연 언어
  * ko
  * en
* Content-Length: 표현 데이터의 길이
  * 바이트 단위
  * Transfer-Encoding을 사용하면 Content-Length를 사용하면 안됨
* 표현 헤더는 전송, 응답 둘다 사용

### 협상(Content Negotiation)

클라이언트가 선호하는 표현 요청

* Accept: 클라이언트가 선호하는 미디어 타입 전달
* Accept-Charset: 클라이언트가 선호하는 문자 인코딩
* Accept-Encoding: 클라이언트가 선호하는 압축 인코딩
* Accept-Language: 클라이언트가 선호하는 자연 언어
* 협상 헤더는 요청시에만 사용
* Accept-Language: ko-KR, ko;q=0.9,en-US;q=0.8,en;q=0.7
* 구체적인 것이 우선한다.

#### 전송 방식

* 단순 전송 (Content-Length)
* 압축 전송 (Content-Encoding)
* 분할 전송 (Transfer-Encoding)
* 범위 전송 (Range, Content-Range)

#### 일반 정보

* From: 유저 에이전트의 이메일 정보
  * 요청에서 사용
* Referer: 이전 웹 페이지 주소
  * 현재 요청된 페이지의 이전 웹 페이지의 주소
  * Referer를 사용해서 유입 경로 분석 가능
  * 요청에서 사용
* User-Agent: 유저 에이전트 애플리케이션 정보
  * 클라이언트의 애플리케이션 정보(웹 브라우저 정보, 등등)
  * 요청에서 사용
* Server: 요청을 처리하는 오리진 서버의 소프트웨어 정보
  * Server: Apache/2.2.22 (Debian)
  * server: nginx
  * 응답에서 사용
* Date: 메시지가 생성된 날짜
  * 응답에서 사용

#### 특별한 정보

* Host: 요청한 호스트 정보(도메인)
  * 요청에서 사용
  * 필수
  * 하나의 서버가 여러 도메인을 처리해야 할 때
  * 하나의 IP 주소에 여러 도메인이 적용되어 있을 때
* Location: 페이지 리다이렉션
  * 웹 브라우저는 3xx 응답의 결과에 Location 헤더가 있으면, Location 위치로 자동 이동
* Allow: 허용 가능한 HTTP 메서드
  * 405 (Method Not Allowed) 에서 응답에 포함해야함
  * Allow: GET, HEAD, PUT
* Retry-After: 유저 에이전트가 다음 요청을 하기까지 기다려야 하는 시간
  * 503 (Service Unavailable): 서비스가 언제까지 불능인지 알려줄 수 있음

#### 인증

* Authorization: 클라이언트 인증 정보를 서버에 전달
* WWW-Authenticate: 리소스 접근시 필요한 인증 방법 정의
  * 401 Unauthorized 응답과 함께 사용

#### 쿠키

* Set-Cookie: 서버에서 클라이언트로 쿠키 전달(응답)
* Cookie: 클라이언트가 서버에서 받은 쿠키를 저장하고, HTTP 요청시 서버로 전달
* 사용처
  * 사용자 로그인 세션 관리
  * 광고 정보 트래킹
* 쿠키 정보는 항상 서버에 전송됨
  * 네트워크 트래픽 추가 유발
  * 최소한의 정보만 사용
  * 서버에 전송하지 않고 웹 브라우저 내부에 데이터를 저장하고 싶으면 웹 스토리지 참고
* 생명 주기 (Expires, max-age)
  * 세션 쿠키: 만료 날짜를 생략하면 브라우저 종료시까지만 유지
  * 영속 쿠키: 만료 날짜를 입력하면 해당 날짜까지 유지
* 도메인
  * 명시: 명시한 문서 기준 도메인 + 서브 도메인 포함
  * 생략: 현재 문서 기준 도메인만 적용
* 경로
  * 이 경로를 포함한 하위 경로 페이지만 쿠키 접근
  * 일반적으로 path=/ 루트로 지정
* 보안
  * Secure
    * https인 경우에만 전송
  * HttpOnly
    * XSS 공격 방지
    * 자바스크립트에서 접근 불가(document.cookie)
    * HTTP 전송에만 사용
  * SameSite
    * XSRF 공격 방지
    * 요청 도메인과 쿠키에 설정된 도메인이 같은 경우만 쿠키 전송

#### 검증 헤더와 조건부 요청

* 캐시 유효 시간이 초과해도, 서버의 데이터가 갱신되지 않으면
* 304 Not Modified + 헤더 메타 정보만 응답
* 클라이언트는 캐시에 저장되어 있는 데이터 재활용
* 검증 헤더
  * 캐시 데이터와 서버 데이터가 같은지 검증하는 데이터
  * Last-Modified, ETag
* 조건부 요청 헤더
  * 검증 헤더로 조건에 따른 분기
  * If-Modified-Since: Last-Modified 사용
  * If-None-Match: ETag 사용
  * 조건이 만족하면 200 OK
  * 조건이 만족하지 않으면 304 Not Modified

Last-Modified, If-Modified-Since 단점

* 날짜 기반의 로직 사용
* 데이터를 수정해서 날짜가 다르지만, 같은 데이터를 수정해서 데이터 결과가 똑같은 경우
* 서버에서 별도의 캐시 로직을 관리하고 싶은 경우
  * ex) 스페이스나 주석처럼 크게 영향이 없는 변경에서 캐시를 유지하고 싶은 경우

ETag, If-None-Match

* ETag(Entity Tag)
* 캐시용 데이터에 임의의 고유한 버전 이름을 담아둠(Ex. Etag: "v1.0")
* 데이터가 변경되면 이 이름을 바꿔서 변경함(Hash를 다시 생성)
* ETag만 보내서 같으면 유지, 다르면 다시 받기
* 캐시 제어 로직을 서버에서 완전히 관리

#### 캐시와 조건부 요청 헤더

캐시 제어 헤더

* Cache-Control: 캐시 제어
  * max-age: 캐시 유효 시간, 초 단위
  * no-cache: 데이터는 캐시해도 되지만, 항상 origin 서버에 검증하고 사용
  * must-revalidate
    * 캐시 만료후 최초 조회시 origin 서버에 검증해야함
    * origin 서버 접근 실패시 반드시 오류 발생 - 504(Gateway Timeout)
  * no-store: 데이터에 민감한 정보가 있으므로 저장하면 안됨
  * public: 응답이 public 캐시에 저장되어도 됨
  * private: 응답이 해당 사용자만을 위한 것임, private 캐시에 저장해야 함
  * s-maxage: 프록시 캐시에만 적용되는 max-age
* Pragma: 캐시 제어(하위 호한)
* Expires: 캐시 유효 기간(하위 호환)
  * 캐시 만료일 지정
* Age: 60 (HTTP 헤더)
  * 오리진 서버에서 응답 후 프록시 캐시 내에 머문 시간(초)

#### 캐시 무효화

* Cache-Control: no-cache, no-store, must-revalidate
* Pragma: no-cache









