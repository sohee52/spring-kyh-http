# 3. HTTP
## HTTP

- HTTP 의미
    - **HyperText Transfer Protocol**
    - 웹에서 데이터를 주고받기 위한 규칙(프로토콜).
    - 웹에서 클라이언트와 서버가 약속된 규칙대로 데이터를 주고받는 통신 방법
- HTTP 특징
    1. **거의 모든 데이터 전송 가능**
        - 텍스트, HTML 문서, 이미지, 동영상, 음성, 파일, API 데이터(JSON, XML) 등.
        - 심지어 서버끼리도 HTTP를 통해 통신 가능.
    2. **클라이언트-서버 구조**
        - 클라이언트(브라우저, 앱)가 요청(request)을 보내고
        - 서버가 응답(response)을 돌려주는 구조.
    3. **무상태(Stateless)**
        - 요청끼리 서로 기억하지 않음.
        - 예) 로그인 정보를 계속 유지하려면 쿠키/세션 같은 보조 장치 필요.
    4. **비연결성(Connectionless)**
        - 요청/응답이 끝나면 바로 연결을 끊음.
        - 덕분에 서버 자원은 절약되지만, 상태를 유지하려면 추가 기술이 필요.
    5. **단순하고 확장 가능**
        - 메시지 구조가 단순해서 배우기 쉽고,
        - 필요에 따라 새로운 기능(헤더, 메서드 등)을 확장해서 붙일 수 있음.
- HTTP 역사
    - HTTP/0.9 1991년: GET 메서드만 지원, HTTP 헤더X, 웹 페이지의 정적 콘텐츠 제공에만 적합
    - HTTP/1.0 1996년: 메서드, 헤더 추가, 다양한 데이터 전송 가능하지만 요청마다 연결 닫음
    - HTTP/1.1 : 지속 연결 기본, 가상 호스팅(한 서버에서 여러 도메인을 구분 가능), 캐싱·성능 최적화 기능 추가 → 현재까지 가장 널리 쓰이는 버전
    - HTTP/2 2015년: 성능 개선
        - 이진 프로토콜(Binary Protocol): 텍스트 대신 이진 프레임 기반
        - 멀티플렉싱(Multiplexing): 하나의 연결에서 여러 요청/응답 동시 처리
        - 헤더 압축 (HPACK)으로 성능 최적화
        - 서버 푸시(Server Push) 지원
    - HTTP/3 진행중: TCP 대신에 UDP 사용, 성능 개선
        - 연결 수립 지연 최소화 (핸드셰이크 간소화)
        - 멀티플렉싱 성능 향상, HOL Blocking 문제 해결
        - 모바일 환경에서 연결 안정성 높음
- 기반 프로토콜
    - TCP: HTTP/1.1, HTTP/2
    - UDP: HTTP/3
    - 현재 HTTP/1.1 주로 사용
    - HTTP/2, HTTP/3 도 점점 증가

## 클라이언트 서버 구조

- 클라이언트 서버 구조
    - Request Response 구조
    - 클라이언트는 서버에 요청을 보내고, 응답을 대기
    - 서버가 요청에 대한 결과를 만들어서 응답

## Stateful, Stateless

- Stateless 의미, 장단점
    - 서버가 클라이언트의 상태를 보존X
    - 장점: 서버 확장성 높음 (스케일 아웃 - 수평 확장 유리)
    - 단점: 클라이언트가 추가 데이터 전송
- Stateful vs Statelss
    - Stateful: 항상 같은 서버가 유지되어야 한다.
    - Statelss: 아무 서버나 호출해도 된다. → 무한한 서버 증설 가능
- Statelss 실무 한계
    - 모든 것을 무상태로 설계할 수 있는 경우도 있고 없는 경우도 있다.
    - **무상태일 수 있는 경우 예시: 로그인이 필요 없는 단순한 서비스 소개 화면**
    - **상태 유지되어야 하는 경우 예시: 로그인**
    - 일반적으로 **브라우저 쿠키와 서버 세션** 등을 사용해서 상태 유지
    - 상태 유지는 최소한만 사용
    - 최대한 Stateless하게 설계해야 한다.

## 비연결성(Connectionless)

- 비연결성 특징, 장점
    - HTTP는 기본적으로 연결을 유지하지 않는 모델
    - 일반적으로 초 단위 이하의 빠른 속도로 응답
    - 1시간 동안 수천명이 서비스를 사용해도 실제 서버에서 동시에 처리하는 요청은 수십개 이하로 매우 작음 ex. 웹 브라우저에서 계속 연속해서 검색 버튼을 누르지 않는다.
    - 서버 자원을 매우 효율적으로 사용할 수 있다.
- 비연결성 한계와 극복
    - 한계
        - TCP/IP 연결을 새로 맺어야 한다. - 3 way handshake 시간 추가
        - 웹 브라우저로 사이트를 요청하면 HTML 뿐만 아니라 자바스크립트, css, 추가 이미지 등 수 많은 자원이 함께 다운로드 → 낭비
    - 극복
        - 지금은 HTTP 지속 연결(Persistent Connections)로 문제 해결
        - HTTP/2, HTTP/3에서 더 많은 최적화

## HTTP 메시지

- HTTP 메시지 구조 (요청, 응답 둘다)
    - start-line 시작 라인 - request-line / status-line
    - header 헤더
    - empty line 공백 라인 (CRLF = \r\n)
    - message body 메시지 바디
- 시작 라인
    - request-line / status-line
    - request-line (요청 메시지)
        - method SP(공백) request-target SP HTTP-version CRLF(엔터)
        - ex. GET /search?q=hello&hl=ko HTTP/1.1
        - HTTP 메서드 종류와 의미
            - 종류
                - GET, POST, PUT, DELETE …
            - 의미
                - 서버가 수행해야 할 동작 지정
                - GET: 서버에서 데이터를 조회할 때 사용
                - POST: 서버에 새로운 리소스를 생성하거나, 데이터를 처리 요청할 때 사용
                - PUT: 서버의 기존 리소스를 전체 수정(덮어쓰기)할 때 사용
                - DELETE: 서버의 리소스를 삭제할 때 사용
        - 요청 대상
            - absolute-path[?query]
            - 절대 경로 = “/”로 시작하는 경로
            - ex. /search?q=hello&hl=ko
        - HTTP 버전
    - status-line (응답 메시지)
        - HTTP-version SP status-code SP reason-phrase CRLF
        - ex.  HTTP/1.1 200 OK
        - HTTP 버전
        - HTTP 상태 코드
            - 요청 성공, 실패를 나타냄
            - 200 : 성공
            - 400 : 클라이언트 내부 오류
            - 500 : 서버 내부 오류
        - 이유 문구
            - 사람이 이해할 수 있는 짧은 상태 코드 설명 글 ex. OK
- HTTP 헤더
    - **field-name :** OWS **field-value** OWS (OWS:띄어쓰기 허용)
    - ex. Host: www.google.com
    - ex. Content-Type: text/html;charset=UTF-8
    - field-name의 특징
        - 대소문자 구분 없음
    - 용도
        - HTTP 전송에 필요한 모든 부가 정보
        - ex. 메시지 바디의 내용, 메시지 바디의 크기, 압축, 인증, 요청 클라이언트(브라우저) 정보, 서버 애플리케이션 정보, 캐시 관리 정보...
        - 표준 헤더가 너무 많음
        - https://en.wikipedia.org/wiki/List_of_HTTP_header_fields
        - 필요시 임의의 헤더 추가 가능 ex. helloworld: hihi
    - 공백 라인
- HTTP message body

    ```html
    <html>
    	<body> ... </body>
    </html>
    ```

    - 용도
        - 실제 전송할 데이터
        - HTML 문서, 이미지, 영상, JSON 등등 byte로 표현할 수 있는 모든 데이터 전송 가능