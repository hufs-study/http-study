
<br>
<br>
<br>


### 16.1 국제적인 콘텐츠를 다루기 위해 필요한 HTTP 지원  
`서버`는 HTTP `Content-Type charset 매개변수`와 `Content-Language` 헤더를 이용  
`클라이언트`는 차셋 인코딩 알고리즘들과 언어들 중 어떤 것을 이해할 수 있고 선호하는지 말해주기 위해 `Accept-Charset`과 `Accept-Language` 이용  

<br>
<br>

### 16.2.1 차셋(Charset)은 글자를 비트로 변환하는 인코딩이다.
`HTTP 차셋 값은 어떻게 엔티티 콘텐츠의 bit들을 특정 문자 체계의 글자들로 바꾸는지 말해준다.`  

<br>

* `Server side`
<div align="center">
    <img src="./img/1.png" alt="" style="width: 400px;" />
</div>
위의 Content-Type 헤더는 수신자(클라이언트)에게 콘텐츠가 HTML 파일이며, 콘텐츠를 디코딩하기 위해 iso-8859-6 (아랍 문자집한 디코딩 기법)을 이용하라고 말해준다.  

<br>

> 다만, HTTP는 전송에만 관심을 갖는다. 전송된 콘텐츠를 어떻게 표현할 것인가는 사용자의 그래픽 디스플레이 소프트웨어(브라우저, 운영체제, 글꼴)가 결정한다.

> 만약 클라이언트가 문자 인코딩을 추측하지 못했다면, iso-8859-1인 것으로 가정한다. 

<br>
<br>

* `Client side`
<div align="center">
    <img src="./img/2.png" alt="" style="width: 400px;" />
</div>
위의 Accept-Charset 헤더는 서버에게 클라이언트가 어떤 문자 체계를 지원하는지 알려준다. (지원하는 문자 인코딩의 목록)  

> 한편, 이중 어떤 인코딩으로 콘텐츠를 반환할지는 서버의 자유이다.  

<br>
<br>

### 16.4.1 Content-Language 헤더  
Content-Language 엔티티 헤더 필드는 엔티티가 어떤 언어 사용자를 대상으로 하고 있는지를 서술  

<br>

### 16.4.2 Accept-Language 헤더  
Accept-Language 헤더는 클라이언트가 선호하는 언어를 알려준다.  
