# 5장 웹 서버

## 5.1 다채로운 웹 서버

‘웹 서버'는 웹 서버 소프트웨어와 웹 페이지 제공에 특화된 장비 모두를 가리킨다.

리소스에 대한 HTTP 요청을 받아서 클라이언트에게 돌려준다.

<br>

### 5.1.1 웹 서버 구현

웹 서버는 HTTP 및 그와 관련된 TCP 처리를 구현한 것이다.

- HTTP 프로토콜을 구현
- 웹 리소스를 관리
- 웹 서버 관리 기능을 제공
- TCP 커넥션 관리에 대한 책임을 운영체제와 나눠 갖는다
  - 운영체제는 컴퓨터 시스템의 하드웨어를 관리
  - TCP/IP 네트워크 지원
  - 웹 리소스 유지 위한 파일 시스템
  - 현재 연산 활동을 제어하기 위한 프로세스 관리

웹 서버는 여러가지 형태가 가능하다

<br>

### 5.1.2 다목적 소프트웨어 웹 서버

네트워크에 연결된 표준 컴퓨터 시스템에서 동작한다.

<br>

### 5.1.3 임베디드 웹 서버

일반 소비자용 제품에 내장될 목적으로 만들어진 작은 웹 서버 (프린터, 가전제품) ⇒ 웹 브라우저 인터페이스 관리 콘솔을 제공할 수도 있다.

<br>

## 5.2 간단한 펄 웹 서버

최소한의 기능을 포함한 HTTP 서버는 간단하 Perl 코드로 생성이 가능하다.

- type-o-serve 최소기능 프로그램 예시
  클라이언트-프락시 간의 상호작용 테스트 프로그램 : HTTP 요청 메시지를 정확하게 기록하고, 어떤 HTTP 응답 메시지라도 돌려줄 수 있게 설계

<br>


## 5.3 진짜 웹 서버가 하는 일

공통적으로 웹 서버가 수행하는 일

1. 커넥션 맺기 (클라이언트를 구분하여 커넥션 유지)
2. 요청 받기 (HTTP 요청 메시지 읽기)
3. 요청 처리 (요청 메시지 해석)
4. 리소스 접근 (메시지에서 지정한 리소스에 접근)
5. 응답 만들기 (올바른 헤더 포함 HTTP 응답 메시지 생성)
6. 응답 보내기 (클라이언트에 응답 돌려주기)
7. 트랜잭션 로그 남기기 (로그파일에 트랜잭션 완료 기록 저장)

<br>

## 5.4 단계 1 : 클라이언트 커넥션 수락

클라이언트가 이미 서버에 열려 있는 지속적 커넥션을 갖고 있다면, 요청을 위해 계속해서 그 커넥션을 사용할 수 있다. 그렇지 않을 경우 새 커넥션을 연다.

<br>

### 5.4.1 새 커넥션 다루기

- 클라이언트 → 웹 서버 TCP 커넥션 요청에서, 웹 서버는 커넥션을 맺고 TCP 커넥션에서 IP 주소를 추출해 어떤 클라이언트의 요청인지 확인한다. (유닉스 환경에서는 getpeername을 호출해 소켓으로부터 추출한다.)
- 허용되면 새 커넥션을 목록에 추가하고 데이터 수신을 위한 준비를 한다.
- 웹 서버는 클라이언트의 IP 주소나 호스트 명이 인가되지 않았을 경우 커넥션을 닫을 수 있다.

<br>

### 5.4.2 클라이언트 호스트 명 식별

- ‘역방향 DNS’를 이용해 클라이언트 IP 주소를 호스트 명으로 변환한다.
- 호스트 명을 구체적인 접근 제어와 로깅을 위해 사용할 수 있다.
- ‘Hostname lookup’은 웹 트랜잭션을 느리게 할 수 있어 대용량 웹 서버에서는 Hostname resolution을 특정 콘텐츠에서만 켜둔다.

<br>

### 5.4.3 Ident를 통해 클라이언트 사용자 알아내기

IETF ident 프로토콜은 서버에게 어떤 사용자 이름이 HTTP 커넥션을 초기화했는지 찾아낼 수 있게 해준다.

- 웹 서버 로깅에서 유용한 정보로, Common Log Format의 두 번째 필드는 각 HTTP 요청의 ident 사용자 이름을 담고 있다.
  - 새 커넥션을 맺고 난 뒤, 서버는 자신의 커넥션을 클라이언트 identd 서버 포트를 향해 열고(113번 포트를 사용한다.) 새 커넥션에 대응하는 사용자 이름을 요청한다.
  - ident 프로토콜의 불안정성, HTTP 트랜잭션 지연 등의 이유로 공공 인터넷에서는 잘 동작하지 않는다.

<br>

## 5.5 단계 2 : 요청 메시지 수신

커넥션에 데이터가 도착하면 웹 서버는 커넥션에서 데이터를 읽어 들이고 파싱하여 요청 메시지를 구상한다.

[파싱 과정]

- 요청줄에서 요청 메서드, URI, 버전 번호를 찾는다.
- 메시지 헤더들을 읽는다.
- 요청 본문을 읽는다.
- 각 단계는 CRLF 문자열로 구분된다.

파싱할 때, 입력 데이터를 네트워크로부터 불규칙적으로 받기 때문에 이해 가능한 수준의 분량을 확보할 때까지 메시지의 일부분을 메모리에 임시로 저장해두어야한다.

<br>

### 5.5.1 메시지의 내부 표현

요청 메시지를 쉽게 다룰 수 있도록 메시지의 조각에 대한 포인터와 길이를 담는 내부 자료 구조에 저장한다.

헤더는 속도가 빠른 룩업 테이블에 저장되어 각 필드에 신속하게 접근할 수 있다.

<br>

### 5.5.2 커넥션 입력/출력 처리 아키텍쳐

수 많은 커넥션들의 통신 속 서로 다른 요청 속도에 대비하는 웹 서버는 아키텍쳐의 차이에 따라 새로운 요청을 처리하는 방식이 달라진다.

- 단일 스레드 웹 서버
  - 한 번에 하나씩 요청을 처리 = 트랜잭션이 완료되면, 다음 커넥션이 처리
  - 구현하기 간단하지만 처리 도중 다른커넥션이 무시되어 성능 문제를 발생
- 멀티프로세스와 멀티스레드 웹 서버
  - 여러 요청 동시 처리 위해서 필요할 때마다 스레드/프로세스를 준비할 수 있다.
  - 동시 커넥션으로 인해 너무 많은 메모리나 시스템 리소스를 차지할 수 있기 때문에 최대 개수를 제한한다.
- 다중 I/O 서버
  - 모든 커넥션은 동시에 활동을 감시당한다.
  - 커넥션의 상태가 바뀌면 작업을 수행하여 유휴 상태의 컨넥션을 기다리는 리소스 낭비를 하지 않는다.
- 다중 멀티스레드 웹 서버
  - 멀티스레딩과 multiplexing을 결합하여 여러 개의 스레드는 각각 열려있는 커넥션을 감시하고 조금씩 작업을 수행한다.

<br>

## 5.6 단계 3 : 요청 처리

웹 서버가 요청으로부터 메서드, 리소스, 헤더, 본문을 얻어내어 처리한다.

<br>

## 5.7 단계 4 : 리소스의 매핑과 접근

웹 서버는 리소스 서버이다. (HTML 페이지, JPEG 이미지 컨텐츠 제공, 동적 콘텐츠도 제공 한다.)

콘텐츠를 클라이언트에게 전달하기 전, 요청 메시지의 URI에 대응하는 알맞은 콘텐츠나 콘텐츠 생성기를 웹 서버에서 찾아서 리소스를 식별해야한다.

<br>

### 5.7.1 Docroot

요청 URI를 웹 서버의 파일 시스템 안에 있는 파일 이름으로 사용하는 단순한 방법으로 리소스를 매핑한다.

웹 서버 파일 시스템의 특별한 폴더를 웹 콘텐츠를 위해 예약하는데 이것이 docroot이다.

ex) 요청 URI : ‘/specials/saw-blade.gif’ ⇒ ‘/usr/local/httpd/files/specials/saw-blade.gif’ 파일을 반환

- 가상 호스팅된 docrrot

가상 호스팅 웹 서버는 URI나 HOST 헤더에서 얻은 IP 주소나 호스트 명을 이용해 올바른 문서 루트를 식별한다.

⇒ 하나의 웹 서버 위에서 두 개의 사이트가 완전히 분리된 콘텐츠를 갖고 호스팅 되도록 할 수 있다.

ex) A ⇒ /docs/joe/index.html , B ⇒ /docs/mary/index.html

- 사용자 홈 디렉터리 docroots

사용자들이 한 대의 웹 서버에서 각자 개인 웹 사이트를 만들 수 있도록 하는 것.

ex) 요청 URI에서 /~bob/index.html ⇒ /home/bob/public_html

<br>

### 5.7.2 디렉터리 목록

웹 서버는 경로가 파일이 아닌 디렉터리를 가리키는 URL에 대한 요청을 받을 수 있다.

요청 시

- 에러를 반환
- 디렉터리 대신 특별한 ‘Index파일’을 반환한다.
- 디렉터리를 탐색해서 내용을 담은 HTML 페이지를 반환한다.

⇒ 대부분의 웹 서버는 요청한 URL에 대응되는 디렉터리 안에서 index.html을 요청한다.

⇒ 디렉터리 색인 기능도 있다.

<br>

### 5.7.3 동적 콘텐츠 리소스 매핑

웹 서버는 URL를 동적 리소스를 다루는 어플리케이션 서버(백엔드)와 연결하여 실행 방식을 알려줄 수 있다.

<br>

### 5.7.4 서버사이드 인클루드 (SSI)

서버가 리소스의 콘텐츠를 클라이언트에게 보내기 전에 처리하는 것.

콘텐츠에 변수 이름이나 내장도니 스크립트가 될 수 있는 특별한 패턴이 있는지 검사를 받는다. ⇒ 동적 콘텐츠 생성의 쉬운 방법

<br>

### 5.7.5 접근 제어

각각의 리소스에 클라이언트의 IP 주소에 근거하여 접근을 제어하고 비밀번호를 물어볼 수도 있다.

<br>

## 5.8 단계 5 : 응답 만들기

서버가 리소스 식별 후, 요청 메서드로 서술되는 동작을 수행한 뒤 응답 메시지를 반환한다.

<br>

### 5.8.1 응답 엔터티

트랜잭션이 응답 본문을 생성하면, 그 내용을 응답 메시지와 함께 돌려보낸다.

- MIME 타입 서술된 Content-Type 헤더
- Content-Length 헤더
- 실제 응답 본문 내용

<br>

### 5.8.2 MIME 타입 결정하기

- mime.types

파일 이름의 확장자를 사용할 수 있기에 MIME 타입이 담겨 있는 파일을 탐색하여 각 리소스의 MIME 타입을 계산한다.

- 매직 타이핑

파일 내용을 검사해서 알려진 패턴에 대한 테이블에 해당하는 패턴이 있는지 찾아볼 수 있다. 느리지만 표준 확장자 없이 이름 지어진 경우 편리하다.

- 유형 명시

특정 파일이나 디렉터리 안의 파일들이 확장자나 내용에 상관없이 특정 MIME 타입을 갖도록 설정

- 유형 협상

한 리소스가 어떤 종류의 문서 형식에 속하도록 설정. (사용자와의 협상)

<br>

### 5.8.3 리다이렉션

3XX 상태 코드로 지칭되며, Location 응답헤더는 다른 위치의 URI를 포함한다.

리다이렉트가 유효한 경우

- 영구히 리소스가 옮겨진 경우

새 URL이 부여되어 새로운 위치로 옮겨졌거나 이름이 바뀐 경우. 클라이언트는 북마크를 갱신하거나 할 수 있다고 말해줄 수 있다.

⇒ 301 Moved Permanently

- 임시로 리소스가 옮겨진 경우

클라이언트가 나중에 원래 URL로 찾아오고 북마크도 갱신하지 않아야 한다.

⇒ 303 See Other, 307 Temporary Redirect

- URL 증강
  문맥 정보를 포함시키기 위해 재작성된 URL로 리다이렉트 시킨다.
  - 서버는 상태 정보를 내포한 새 URL 생성하고 사용자를 이 새 URL로 리다이렉트 한다. (뚱뚱한 URL).
  - 클라이언트는 리다이렉트를 따라가서, 상태정보가 추가된 완전한 URL을 포함한 요청을 다시 보낸다.
  ⇒ 트랜잭션 간 상태를 유지하는 방법 : 303, 307
- 부하 균형
  과부하를 피해서 부하가 덜한 곳으로 리다이렉트 시킬 수 있다.
  ⇒ 303, 307
- 친밀한 다른 서버가 있을 때
  사용자에 대한 정보를 가진 다른 서버로 리다이렉트 할 수 있다.
  ⇒ 303, 307
- 디렉터리 이름 정규화
  / 추가한 URI로 리다이렉트

<br>

## 5.9 단계 6 : 응답 보내기

커넥션 상태를 추적해야 하며, 커넥션을 닫는 과정을 주의해야한다.

<br>

## 5.10 단계 7 : 로깅

트랜잭션이 완료되었을 때, 웹 서버는 트랜잭션이 어떻게 수행되었는지 로그파일에 로그를 기록한다.
