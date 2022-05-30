# 4장 커넥션 관리

## 4.1 TCP 커넥션

- 웹 브라우저 &rarr; TCP Connection을 통한 웹 서버 요청

```http
http://www.joes-hardware.com:80/power-tools.html
```

```text
1. 브라우저 &rarr; www.joes-hardware.com 이라는 호스트 명 추출
2. 브라우저 &rarr; 이 호스트 명에 대한 IP 주소 검색
3. 브라우저 &rarr; 포트 번호(= 80) 검색
4. 브라우저 &rarr; 202.43.78.3 IP의 포트 번호 80으로 TCP Connection 생성
5. 브라우저 &rarr; 서버로 HTTP GET 요청 메시지 전송
6. 브라우저 &rarr; 서버에서 온 HTTP 응답 메시지 수신
7. 브라우저 Connection 해제
```

### 4.1.2 TCP 스트림은 세그먼트로 나뉘어 IP 패킷을 통해 전송된다

> HTTPS = HTTP + 보안 기능(= TLS, SSL &rarr; HTTP와 TCP 사이에 있는 암호화 계층)

<div align="center">
    <img src="./img/1.PNG" alt="" />
</div>

`HTTP`가 메시지를 전송할 때 `TCP 연결`을 통해 메시지 내용을 순서대로 전송  

아래 과정은 개발자에게 노출 X

```text
1. TCP -> 세그먼트 단위로 데이터 스트림 분할
2. 각 TCP 세그먼트는 IP 패킷에 담아 전달
```

<div align="center">
    <img src="./img/2.PNG" alt="" />
</div>

### 4.1.3 TCP 커넥션 유지하기

> 발신지 IP 주소, 발신지 포트, 수신지 IP 주소, 수신지 포트 &rarr; TCP Connection 식별

| 커넥션 |발신지 IP 주소|발신지 포트|목적지 IP 주소|목적지 포트|
|:---:|:---------:|:---------:|:---------:|:---------:|
|  A  |209.1.32.34|2034|204.62.128.58|4133|
|  B  |209.1.32.35|3227|204.62.128.58|4140|
|  C  |209.1.32.34|3105|207.25.71.25|80|
|  D  |209.1.32.89|5100|207.25.71.25|80|

### 4.1.4 TCP 소켓 프로그래밍

<div align="center">
    <img src="./img/3.PNG" alt="" style="width:600px;"/>
</div>

<br>

## 4.2 TCP 성능에 대한 고려

> HTTP 트랜잭션 성능 &larr; TCP 성능 영향 // 계층이 바로 위 아래 존재하기 때문

### 4.2.1 HTTP 트랜잭션 지연

> 트랜잭션 처리 시간 < TCP Connection 설정, 요청 전송, 응답 메시지 전송 시간  
> 대부분 HTTP 지연 &rarr; TCP 네트워크 통신에서 발생

- HTTP 트랜잭션 처리 과정

<div align="center">
    <img src="./img/4.PNG" alt="" />
</div>

- HTTP 트랜잭션 지연 원인
  - #### 1. DNS &rarr; 최근 호스트 접속 기록이 없으면 `DNS`를 통해 URI에 있는 호스트 명을 IP 주소로 변환하는 데 큰 시간 소비
  - #### 2. 비효율적인 TCP Connection 설정 시간 &rarr; 새로운 TCP Connection에서 항상 설정 시간이 발생
  - #### 3. 요청 메시지가 인터넷을 통해 전달되고 서버에 의해 처리되는 시간 발생
  - #### 4. 웹 서버 &rarr; HTTP 응답 전송 시간 발생

### 4.2.3 TCP Connection Hand Shake 지연

> 새로운 TCP Connection을 열 때 Connection을 위한 조건을 맞추기 위해 연속으로 IP 패킷 교환  
> 작은 크기의 데이터 전송에 Connection 사용되면 매우 비효율적

> 50% 이상의 지연 차지  
> 클라이언트 &harr; 서버 Connection 위해 `SYN`, `ACK` hand shake 하는데 이 때 지연 발생 

- TCP Connection Hand Shake 순서

<div align="center">
  <img src="./img/5.PNG" alt="" />
</div>

```text
1. 클라이언트 &rarr; 새로운 TCP Connection 위해 작은 TCP 패킷(= SYN, Connection 생성 요청 Flag) 서버 전송
2. 서버 &rarr; Connection 요청이 받아들여졌음을 의미하는 SYN, ACK Flag를 TCP 패킷에 담아 클라이언트 전송
3. 클라이언트 &rarr; Connection 완료를 알리기 위해 서버에 다시 확인 응답 신호 전송
```

### 4.2.4 확인 응답 지연

1. 성공적인 데이터 전송을 보장하기 위해 TCP &rarr; 확인 체계 운영
2. 세그먼트 수신 성공 &rarr; 확인 응답 패킷 전송
3. 확인 응답 패킷 크기 &darr; &darr; &darr; &rarr; 효율 위해 같은 방향으로 송출 되는 데이터 편승
4. **같은 방향 송출 데이터 패킷 찾지 못하면 지연 발생**

### 4.2.5 TCP 느린 시작(slow start)

1. TCP Connection 첫 생성 시에 속도 제한 &rarr; 전송 성공 시에 속도 제한 &uarr; &uarr; &uarr;  
2. `지속 Connection` : 이미 존재하는 Connection **재사용**

### 4.2.6 네이글(Nagle) 알고리즘과 TCP_NODELAY

> Nagle 알고리즘이란, 세그먼트가 최대 크기가 되기 전까지 전송 지연
 
- **목표 패킷까지 채우지 못하면** 지연 심해짐
- `확인 응답 지연`과 함께 쓰이면 매우 비효율적
- `TCP_NODELAY` &rarr; `Nagle 알고리즘` 비활성화

### 4.2.7 TIME_WAIT의 누적과 포트 고갈

- TCP Connection의 종단에서 TCP를 끊으면 종단에서는 Connection의 IP 주소와 포트 번호를 메모리의 작은 제어영역에 기록 (일정 시간동안 TCP Connection 생성 방지 목적)
- 이전 Connection의 패킷이 그 해당 Connection과 같은 연결 값으로 생성되면 패킷이 중복되며 TCP 데이터 충돌
- 포트 고갈 문제를 겪지 않더라도 많은 Connection 연결이나 많은 대기 상태의 제어 블록이 있는 상황은 주의
- 현대의 빠른 라우터들 적용 이후 Connection 닫힌 후 패킷이 중복되는 경우 거의 사라짐

<br>

## 4.3 HTTP 커넥션 관리

### 4.3.1 흔히 잘못 이해하는 Connection 헤더

> HTTP 헤더 필드명 : 해당 Connection에만 해당되는 헤더 나열  
> 임시 토큰 : Connection에 대한 비표준 옵션  
> Close : Connection 작업 완료되면 종료되어야함 의미

- Connection 헤더 + 메세지 전달 받으면 요청에 기술된 모든 옵션 적용
- Connection 모든 헤더 삭제

### 4.3.2 순차적인 트랜잭션 처리에 의한 지연

> 각 트랜잭션 마다 새로운 Connection &rarr; `각 Connection 맺는데 발생하는 지연` + `slow start 지연`

<div align="center">
  <img src="./img/6.PNG" alt="" />
</div>

<br>

## 4.4 병렬 커넥션


