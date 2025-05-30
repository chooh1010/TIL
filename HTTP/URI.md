### URI(Uniform Resource Identifier)

* Uniform: 리소스 식별하는 통일된 방식
* Resource: 자원, URI로 식별할 수 있는 모든 것(제한 없음)
* Identifier: 다른 항목과 구분하는데 필요한 정보

#### URL, URN

* URL(Uniform Resource Locator): 리소스가 있는 위치를 지정
* URN(Uniform Resource Name): 리소스에 이름을 부여
* 위치는 변할 수 있지만, 이름은 변하지 않는다.
* urn:isbn:8960777331 (어떤 책의 isbn URN)
* URN 이름만으로 실제 리소스를 찾을 수 있는 방법이 보편화 되지 않음

### URL

전체 문법

scheme://[userinfo@]host[:port]/path[?query]#fragment

https://www.google.com:443/search?q=hello&hl=ko

* scheme - 프로토콜(https)
  * 주로 프로토콜 사용
  * 프로토콜: 어떤 방식으로 자원에 접근할 것인가 하는 약속 규칙 (ex. http, https, ftp...) 
  * 포트는 생략 가능
* userinfo
  * URL에 사용자정보를 포함해서 인증
  * 거의 사용하지 않음
* 호스트명(www.google.com) 
  * 도메인명 또는 IP 주소를 직접 사용가능
* 포트 번호(443)
  * 접속 포트
  * 일반적으로 생략, 생략시 http는 80, https는 443 
* path(/search)
  * 리소스 경로, 계층적 구조
* 쿼리 파라미터(q=hello&hl=ko)
  * key=value 형태
  * ?로 시작, &로 추가 가능 ?keyA=valueA&keyB=valueB
  * query parameter, query string 등으로 불림, 웹서버에 제공하는 파라미터, 문자 형태
* fragment
  * https://docs.spring.io/spring-boot/docs/current/reference/html/getting-started.html#getting-started-introducing-spring-boot
  * html 내부 북마크 등에 사용
  * 서버에 전송하는 정보 아님



### HTTP 메시지 전송

1. 웹 브라우저가 HTTP 메시지 생성
2. SOCKET 라이브러리를 통해 전달
   - A: TCP/IP 연결(IP, PORT)
   - B: 데이터 전달
3. TCP/IP 패킷 생성, HTTP 메시지 포함



