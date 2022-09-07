# 21장 로깅과 사용 추적

## 21.1 로그란 무엇인가?

일반적으로 로깅하는 필드

- HTTP 메서드
- 클라이언트/서버 HTTP 버전
- 리소스 URL
- HTTP 상태 코드
- 요청/응답 메세지 크기 및 본문
- 트랜잭션 일어난 시기
- Referer와 User-Agnet 헤더 값

|필드|설명|
|:-------:|:---------|
|remote host|클라이언트 호스트 명 및 IP 주소|
|username|ident 검색을 수행했다면 인증된 요청자 사용자 이름|
|auth-username|인증을 수행했다면 인증된 요청자 이름|
|timestamp|요청 날짜 및 시간|
|request-line|HTTP 요청 행 그대로 기술|
|response-code|HTTP 상태 코드|
|response-size|응답 엔터티 content length|

예시

```text
209.1.32.44 - - [03/Oct/1999:14:16:00 -0400] "GET / HTTP/1.0" 200 1024
http-guide.com - dg [03/Oct/1999:14:16:32 -0400] "GET / HTTP/1.0" 200 477 
http-guide.com - dg [03/Oct/1999:14:16:32 -0400] "GET /foo HTTP/1.0" 404 0
```

|      필드       |      엔트리1      |        엔트리2         |엔트리2|
|:-------------:|:--------------:|:-------------------:|:--------:|
|  remote host  |  209.1.32.44   |   http-guide.com    |http-guide.com|
|   username    |       -        |          -          |-|
| auth-username |       -        |         dg          |dg|
|   timestamp   |     03/Oct     | 1999 14:16:00 -0400 |03/Oct|1999 14:16:32 -0400|03/Oct|1999 14:16:32 -0400|
|request-line| GET / HTTP/1.0 |   GET / HTTP/1.0    |GET /foo HTTP/1.0|
|response-cde|200|200|404|
|response-size|1024|477|0|

### 21.2.2 혼합 로그 포맷

|필드|설명|
|:-------:|:-------|
|Referer|Referer Http 헤더 값|
|User-Agent|User-Agent Referer Http 헤더 값|

예시 

```text
209.1.32.44 - - [03/Oct/1999:14:16:00 -0400] "GET / HTTP/1.0" 200 1024 "http://www.
joes-hardware.com/" “5.0: Mozilla/4.0 (compatible; MSIE 5.0; Windows 98)"
```

|필드| 값                                                 |
|:------:|:--------------------------------------------------|
|Referer| http://www.joes-hardware.com                      |
|User-Agent| 5.0: Mozila/4.0 [compatible; MSIE 5.0; Windows98] |