# 2. URI - Web Browser
## URI

- URI (Uniform Resource Identifier)
    - **정의**: 인터넷 상의 자원을 식별하는 **통일된 방식**.
    - **구성 요소 의미**
        - **Uniform**: 일관된 형식으로 표현
        - **Resource**: 웹 페이지, 이미지, 파일 등 식별할 수 있는 모든 대상
        - **Identifier**: 다른 것과 구별할 수 있는 정보
    - **하위 개념**
        - **URL**: 위치(주소)로 자원을 지정
        - **URN**: 이름으로 자원을 지정 (위치는 변해도 이름은 불변)
- URL (Uniform Resource Locator)
    - **Locator** = "위치 지정자"
    - 자원의 **위치**를 기반으로 식별

      → `https://www.google.com/search?q=hello`

- URN (Uniform Resource Name)
    - **Name** = "이름 지정자"
    - 자원의 **이름**으로만 식별 (위치는 바뀌어도 동일하게 인식 가능)
    - 예: `urn:isbn:978-3-16-148410-0`
    - 하지만 **URN만으로 실제 자원 접근**은 아직 보편화되지 않음
- URL 전체 문법
    - scheme://[userinfo@]host[:port][/path][?query][#fragment]
    - https://www.google.com:443/search?q=hello&hl=ko
    - scheme
        - 주로 프로토콜 사용
        - 프로토콜 = 어떤 방식으로 자원에 접근할 것인가 하는 약속 규칙
        - ex. http, https, ftp 등
        - http는 80 포트, https는 443 포트를 주로 사용, 포트는 생략 가능
        - https는 http에 보안 추가 (HTTP Secure)
    - userinfo
        - URL에 사용자 정보를 포함해서 인증할 때 사용
        - 거의 사용하지 않는다.
    - host
        - = 호스트명
        - 도메인명 또는 IP 주소를 직접 사용 가능
    - port
        - 접속 포트
        - 일반적으로 생략
        - 생략 시 http는 80, https는 443
    - path
        - = 리소스 경로
        - 계층적 구조로 되어 있다.
        - ex. /home/file1.jpg
        - ex. /members
        - ex. /members/100
        - ex. /items/iphone12
    - query
        - key = value 형태
        - ?로 시작, &로 추가 가능 ex. ?keyA=valueA&keyB=valueB
        - query parameter, query string 등으로 불림
        - 웹서버에 제공하는 파라미터, 문자 형태
    - fragment
        - html 내부 북마크 등에 사용
        - 서버에 전송하는 정보 아님