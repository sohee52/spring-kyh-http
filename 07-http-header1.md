# 7. HTTP - header1
## HTTP 개요

- HTTP 표준: RFC7230~7235
    - 메시지 본문(message body)을 통해 표현 데이터 전달
    - 메시지 본문 = 페이로드(payload)
    - 표현 = 요청이나 응답에서 전달할 실제 데이터
    - 표현 헤더는 표현 데이터를 해석할 수 있는 정보 제공
        - 데이터 유형(html, json), 데이터 길이, 압축 정보 등등
    - 참고: 표현 헤더는 표현 메타데이터와, 페이로드 메시지를 구분해야 하지만, 여기서는 생략

## 표현

- 표현 헤더
    - = 메시지 바디의 데이터 타입, 압축 방식, 언어, 길이 등 데이터를 해석하는 데 필요한 정보를 담는 헤더들
    - 표현 헤더는 전송, 응답 둘다 사용
- 표현 헤더 종류
    - Content-Type
        - 표현 데이터의 형식 설명
        - 미디어 타입, 문자 인코딩
        - 예)
            - text/html; charset=utf-8
            - application/json
            - image/png
    - Content-Encoding
        - 표현 데이터의 압축 방식
        - 데이터를 전달하는 곳에서 압축 후 인코딩 헤더 추가
        - 데이터를 읽는 쪽에서 인코딩 헤더의 정보로 압축 해제
        - 예)
            - gzip : 압축되었음을 나타냄
            - deflate : 압축되었음을 나타냄
            - identity : 압축 안 되었음을 나타냄
    - Content-Language
        - 표현 데이터의 자연 언어
        - 예)
            - ko
            - en
            - en-US
    - Content-Length
        - 표현 데이터의 길이
        - 바이트 단위
        - Transfer-Encoding(전송 코딩)을 사용하면 Content-Length를 사용하면 안됨
            - Content-Length 헤더는 메시지 본문의 정확한 바이트 길이를 알려줍니다. Transfer-Encoding: chunked 와 같은 방식은 본문을 조각(chunk) 단위로 전송합니다. Content-Length와 Transfer-Encoding은 HTTP 메시지 본문 길이를 해석하는 방식이 다르기 때문에 동시에 쓰면 충돌한다.

## 콘텐츠 협상

- 협상 (Content Negotiation)
    - 클라이언트가 선호하는 표현 요청
    - Accept
        - 클라이언트가 선호하는 미디어 타입 전달
    - Accept-Charset
        - 클라이언트가 선호하는 문자 인코딩
    - Accept-Encoding
        - 클라이언트가 선호하는 압축 인코딩
    - Accept-Language
        - 클라이언트가 선호하는 자연 언어
    - 협상 헤더는 요청시에만 사용
- 협상 우선순위: Quality Values (q)
    1. 0~1, 클수록 높은 우선순위, 생략하면 1
        - 예시

            ```
            GET /event
            Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7
            ```

            - ko-KR;q=1 (q생략)
            - ko;q=0.9
            - en-US;q=0.8
            - en;q=0.7
    2. 구체적인 것이 우선한다.
        - 예시

            ```
            GET /event
            Accept: text/*, text/plain, text/plain;format=flowed, */*
            ```

            - 1순위: `text/plain;format=flowed`
            - 2순위: `text/plain`
            - 3순위: `text/*`
            - 4순위: `*/*`
    3. 구체적인 것을 기준으로 미디어 타입을 맞춘다.
        - 예시


            ```
            Accept: text/*;q=0.3, text/html;q=0.7, text/html;level=1,
            text/html;level=2;q=0.4, */*;q=0.5
            ```
            
            ![스크린샷 2025-08-01 161008.png](attachment:0685929d-a860-466e-8899-1eb20326a609:스크린샷_2025-08-01_161008.png)


## 전송 방식

- 단순 전송 (Simple Transmission)
    - 파일 · 데이터를 “있는 그대로” 한 번에 내려-보내는 가장 기본적인 방식
    - Content-Length: 3423

    ```html
    HTTP/1.1 200 OK
    Content-Type: text/html;charset=UTF-8
    Content-Length: 3423
    <html>
     <body>...</body>
    </html>
    ```

- 압축 전송 (Compressed Transmission)
    - 서버가 응답을 `gzip`, `br`(Brotli) 등으로 압축해 보내고, 클라이언트가 해제합니다. 네트워크 대역폭을 줄여 속도를 높일 수 있습니다.
    - Content-Encoding: gzip

    ```html
    HTTP/1.1 200 OK
    Content-Type: text/html;charset=UTF-8
    Content-Encoding: gzip
    Content-Length: 521
    lkj123kljoiasudlkjaweioluywlnfdo912u34ljko98udjkl
    ```

- 분할 전송 (Chunked Transfer Encoding)
    - 큰 파일을 여러 “조각”으로 나누어 업·다운로드하거나, 응답 본문을 **실시간으로** 조각-조각(청크) 보내는 방식입니다.
    - Transfer-Encoding: chunked

    ```html
    HTTP/1.1 200 OK
    Content-Type: text/plain
    Transfer-Encoding: chunked
    5
    Hello
    5
    World
    0
    \r\n
    ```

- 범위 전송 (Range Requests)
    - 클라이언트가 파일의 **일부 바이트 범위**만 요청합니다. 중단된 다운로드 재개(resume), 동영상 seek에 필수입니다
    - Content-Range: bytes 1001-2000 / 2000

    ```html
    HTTP/1.1 200 OK
    Content-Type: text/plain
    Content-Range: bytes 1001-2000 / 2000
    qweqwe1l2iu3019u2oehj1987askjh3q98y
    ```


## 일반 정보

- From
    - 유저 에이전트의 이메일 정보
    - 일반적으로 잘 사용되지 않음
    - 검색 엔진 같은 곳에서, 주로 사용
    - 요청에서 사용
- Referer
    - 이전 웹 페이지 주소
    - 현재 요청된 페이지의 이전 웹 페이지 주소
    - A -> B로 이동하는 경우 B를 요청할 때 Referer: A 를 포함해서 요청
    - Referer를 사용해서 유입 경로 분석 가능
    - 요청에서 사용
    - 참고: referer는 단어 referrer의 오타
- User-Agent
    - 유저 에이전트 애플리케이션 정보
    - `user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36`
    - 클라이언트의 애플리케이션 정보(웹 브라우저 정보, 등등)
    - 통계 정보
    - 어떤 종류의 브라우저에서 장애가 발생하는지 파악 가능
    - 요청에서 사용
- Server
    - 요청을 처리하는 ORIGIN 서버의 소프트웨어 정보
    - Server: Apache/2.2.22 (Debian)
    - server: nginx
    - 응답에서 사용
- Date
    - 메시지가 발생한 날짜와 시간
    - Date: Tue, 15 Nov 1994 08:12:31 GMT
    - 응답에서 사용

## 특별한 정보

- Host
    - 요청한 호스트 정보(도메인)
    - 필수
    - 하나의 서버가 여러 도메인을 처리해야 할 때
    - 하나의 IP 주소에 여러 도메인이 적용되어 있을 때
- Location
    - 페이지 리다이렉션
    - 웹 브라우저는 3xx 응답의 결과에 Location 헤더가 있으면, Location 위치로 자동 이동(리다이렉트)
    - 응답코드 3xx에서 설명
    - 201 (Created): Location 값은 요청에 의해 생성된 리소스 URI
    - 3xx (Redirection): Location 값은 요청을 자동으로 리디렉션하기 위한 대상 리소스를 가리킴
- Allow
    - 허용 가능한 HTTP 메서드
    - 405 (Method Not Allowed) 에서 응답에 포함해야함
    - Allow: GET, HEAD, PUT
- Retry-After
    - 유저 에이전트가 다음 요청을 하기까지 기다려야 하는 시간
    - 503 (Service Unavailable): 서비스가 언제까지 불능인지 알려줄 수 있음
    - Retry-After: Fri, 31 Dec 1999 23:59:59 GMT (날짜 표기)
    - Retry-After: 120 (초단위 표기)

## 인증

- Authorization (요청 헤더)
    - 클라이언트 → 서버 방향
    - 클라이언트가 서버에 인증 정보를 제출할 때 사용
    - 예: `Authorization: Basic dXNlcjpwYXNz`
- WWW-Authenticate (응답 헤더)
    - 서버 → 클라이언트 방향.
    - 서버가 클라이언트에게 “이 리소스에 접근하려면 특정 인증 방식으로 인증해야 한다”라는 요구를 전달.
    - 401 Unauthorized 응답과 함께 사용
    - 예시: `WWW-Authenticate: Newauth realm="apps", type=1, title="Login to \"apps\"", Basic realm="simple"`

## 쿠키

- Set-Cookie
    - 서버에서 클라이언트로 쿠키 전달(응답)
- Cookie
    - 클라이언트가 서버에서 받은 쿠키를 저장하고, HTTP 요청시 서버로 전달
    - 예) set-cookie: sessionId=abcde1234; expires=Sat, 26-Dec-2020 00:00:00 GMT; path=/; [domain=.google.com](http://domain=.google.com/); Secure
- 사용처
    - 사용자 로그인 세션 관리
    - 광고 정보 트래킹
- 쿠키 정보는 항상 서버에 전송됨
    - 네트워크 트래픽 추가 유발
    - 최소한의 정보만 사용(세션 id, 인증 토큰)
    - 서버에 전송하지 않고, 웹 브라우저 내부에 데이터를 저장하고 싶으면 웹 스토리지 (localStorage, sessionStorage) 참고
- 주의!
    - 보안에 민감한 데이터는 저장하면 안됨(주민번호, 신용카드 번호 등등)
- 생명주기
    - Set-Cookie: **expires**=Sat, 26-Dec-2020 04:39:21 GMT
        - 만료일이 되면 쿠키 삭제
    - Set-Cookie: **max-age**=3600 (3600초)
        - 0이나 음수를 지정하면 쿠키 삭제
    - 세션 쿠키
        - 만료 날짜를 생략하면 브라우저 종료시 까지만 유지
    - 영속 쿠키
        - 만료 날짜를 입력하면 해당 날짜까지 유지
- 도메인
    - 예) [domain=example.org](http://domain=example.org/)
    - 명시
        - 명시한 문서 기준 도메인 + 서브 도메인 포함
        - 예) domain=example.org를 지정해서 쿠키 생성
            - example.org는 물론이고 dev.example.org도 쿠키 접근
    - 생략
        - 현재 문서 기준 도메인만 적용
        - 예: [example.org](http://example.org/) 에서 쿠키를 생성하고 domain 지정을 생략
            - [example.org](http://example.org/) 에서만 쿠키 접근, dev.example.org는 쿠키 미접근
- 경로
    - 이 경로를 포함한 하위 경로 페이지만 쿠키 접근
    - 일반적으로 path=/ 루트로 지정
    - 예) path=/home 지정
        - /home -> 가능
        - /home/level1 -> 가능
        - /home/level1/level2 -> 가능
        - /hello -> 불가능
- 보안
    - Secure
        - 쿠키는 http, https를 구분하지 않고 전송
        - **Secure를 적용하면 https인 경우에만 전송**
    - HttpOnly
        - XSS 공격 방지
        - 자바스크립트에서 접근 불가(document.cookie)
        - HTTP 전송에만 사용
    - SameSite
        - XSRF 공격 방지
        - 요청 도메인과 쿠키에 설정된 도메인이 같은 경우만 쿠키 전송