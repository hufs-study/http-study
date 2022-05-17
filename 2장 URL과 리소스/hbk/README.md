# 2장 URL과 리소스

## 2절 URL 문법

<div align="center">
    <img src="./img/url.png" alt="">
</div>

|컴포넌트|설명|기본값|
|:----:|:-----|:----:|
|스킴|리소스를 가져오기위해 **서버에 접근할 수 있도록 하는 프로토콜** 종류 명시|-|
|사용자 이름|몇몇 스킴은 **리소스에 접근 하기 위해 사용자 이름** 요구|anonymous|
|비밀번호|`ftp://{사용자 이름}:{비밀번호}@{호스트}/{리소스 위치}`|<이메일 주소>|
|호스트|리소스를 호스팅하는 서버의 호스트명, IP 주소|-|
|포트|리소스를 호스팅하는 서버가 열어놓은 포트 번호, HTTP 기본 포트 = 80|스킴에 따라 다름|
|경로|이전 컴포넌트와 /으로 구분되어 서버 내 리소스가 위치한 경로|-|
|파라미터|특정 스킴에서의 입력 파라미터|-|
|질의|스킴에서 애플리케이션에 파라미터를 전달 URL의 끝에 물음표로 구분|-|
|프래그먼트|리소스의 일부를 가리키는 이름, **클라이언트에서만 사용**하고 URL 끝에서 #으로 구분|-|

### Scheme

- 사용할 프로토콜 명시
- 리소스에 요청/접근 하는 방법 명시

|scheme|설명|형식|예시|
|:------:|:--------------|:------|:------|
|http|**사용자 이름 & 비밀번호 없이** 일반 URL 형식을 지키는 하이퍼텍스트 전송 프로토콜|http：//〈호스트〉:〈포트〉/〈경로〉?〈질의〉#〈프래그먼트〉|http://www.naver.com|
|https|`http`와 거의 유사하나 `http` 연결 양 끝단에서 암호화를 위해 SSL 사용|https：//〈호스트〉:〈포트〉/〈경로〉?〈질의〉#〈프래그먼트〉|https://www.naver.com|
|mailto|이메일 주소를 가리킴|mailto： < RFC-822-addr-spec >|mailto:white_gyu@naver.com|
|ftp|**파일 전송 프로토콜**|ftp：//〈사용자 이름〉:〈비밀번호〉®〈호스트〉:〈포트〉/〈경로〉;〈파라미터〉|ftp://anonymous:my_passwd(@ftp.prep.ai.mit.edu/pub/gnu|
|rtsp, rtspu|**실시간 스트리밍 프로토콜**, `rtspu`&rarr; `UDP 프로토콜` 사용|rtsp：//〈사용자 이름>:〈비밀번호〉©〈호스트〉:〈포트〉/〈경로)|rtsp://www.naver.com:554/interview|
|file|주어진 호스트 기기에서 **바로 접근할 수 있는 파일**들을 나타냄|file：//〈호스트〉/〈경로〉|file://desktop/my/photo/hello.png|
|news|특정 문서나 뉴스 그룹에 접근하는데 사용|news：(newsgroup), news：(news-article-id)|news:rec.arts.startrek|
|telnet|대화형 서비스 접근, 열려 있는 포트 확인|telnet：//〈사용자 이름〉:〈비밀번호〉®〈호스트〉:〈포트〉/|telnet：//slurp：webhound@joes-hardware.com：23/|

### 사용자 이름 & 비밀번호

```url
ftp://ftp.prep.ai.mit.edu/pub/gnu
ftp://anonymous@ftp.prep.ai.mit.edu/pub/gnu
ftp://anonymous:my_passwd(@ftp.prep.ai.mit.edu/pub/gnu
http://joe:joespasswd*www.joes-hardware.com/sales_info.txt
```

### 호스트 & 포트

- 하나의 호스트에는 **여러 개의 프로세스가 소켓**을 사용하여 통신을 하고 있어 **각 소켓을 구분**할 필요 존재
- 소켓 구분 역할 &rarr; `포트`

### fragment

- HTML에는 각 요소에 `id` 속성을 부여 가능
- URL에 `프래그먼트`를 전달하면 페이지가 해당 `id`가 있는 곳으로 스크롤 이동


## 3절 단축 URL

> `URL` = `상대 URL` + `절대 URL`  
> `상대 URL` = base를 기반으로 한 상대적인 경로
> `절대 URL` = 리소스에 접근하는데 필요한 모든 정보

```HTML
<!-- http://www.joes-hardware.com/tools.html 리소스 -->

<HTML>
<HEAD><TITLE>Joe' s Tools</TITLE></HEAD>
<BODY>
    <H1> Tools Page </Hl>
    <H2> Hammers <H2>
    <P> Joe's Hardware Online has the largest selection of <A href="./hammers.html">hammers</A> on earth.
</B0DY>
</HTML>
```

<div align="center">
    <img src="./img/comparative-url.png" alt="">
</div>

### URL 확장

- 호스트 명 확장 : `naver` 입력 &rarr; 브라우저 `www`+ `naver` + `.com` = `www.naver.com`

- 히스토리 확장 : 방문했던 URL 기록 저장 &rarr; **URL 입력 자동완성** 기능

## 4절 안전하지 않은 문자

### 인코딩 체계

> URL 텍스트를 똑바로 전송하기 위해 아스키 코드를 통해 인코딩

<div align="center">
    <img src="./img/encoding-decoding.png" alt="">
</div>


- URL 인코딩/디코딩이 필요한 이유
  - 인터넷을 통해 전송할 수 있는 문자 = **only** `ASCII`
  - `ASCII` 문자라 하더라도 **예약된 의미를 갖고 있는 문자**의 경우, 그 문자 자체의 의미를 전달하고 싶은 경우에는 **이스케이프 처리** 필요

### 문자 제한

|문자|선점 및 제한|
|:------:|:----------|
|%|인코딩 된 문자에 사용할 이스케이프 토큰으로 선점|
|/|경로 컴포넌트에 있는 경로 세그먼트를 나누는 목적|
|.|경로 컴포넌트에서 선점|
|..|경로 컴포넌트에서 선점|
|#|프래그먼트 구획 문자로 선점|
|?|질의 문자열의 구획 문자로 선점|
|;|파리미터의 구획 문자로 선점|
|:|스킴, 사용자 이름/비밀번호, 호스트/포트 구획 문자로 선점|
|$, +|선점|
|@, &, =|특정 스킴에서 특별한 의미가 있기 때문에 선점|
|{, }, `|`, \ , - [, ]|게이트웨이와 같은 여러 전송 에이전트에서 불안전하게 다루기 때문에 제한|
|(, )|`httpL//www.naver.com)`, URL 범위 밖에서 역할이 있는 문자이기 때문에 반드시 인코딩|