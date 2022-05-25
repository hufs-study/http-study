# About Study

[HTTP 완벽 가이드 웹은 어떻게 동작하는가](https://book.naver.com/bookdb/book_detail.naver?bid=8509980)

## 스터디 흐름

```
1. 모든 스터디원은 한 주에 한 챕터씩 읽고 각자 정리해요.
2. 발표자 외 인원은 질문 및 중요하다고 생각 되는 내용을 준비해요.
3. 주마다 발표자를 정해 정리한 내용을 바탕으로 자유롭게 발표를 진행해요.
4. 발표가 모두 끝나면 해당 내용을 함께 리뷰해요.
5. 리뷰가 모두 끝나면 간단한 텍스트로 회고를 남기고 마무리해요.
```

## Git 사용법

### 폴더 구조
```
.
ᄂ 1장 HTTP 개관
    ᄂ {스터디원 이니셜}
        ᄂ README.md
    ᄂ {스터디원 이니셜}
        ᄂ README.md
    ᄂ {스터디원 이니셜}
        ᄂ README.md
    ᄂ {스터디원 이니셜}
        ᄂ README.md
ᄂ 2장 URL과 리소스
    ᄂ {스터디원 이니셜}
        ᄂ README.md
    ᄂ {스터디원 이니셜}
        ᄂ README.md
    ᄂ {스터디원 이니셜}
        ᄂ README.md
    ᄂ {스터디원 이니셜}
        ᄂ README.md
...
```

### Git Branch 및 Merge

#### Branch & Commit Message Naming

```
주차별 branch : 1st-week, 2nd-week, ...
스터디원 각각의 branch : 스터디원 이니셜 
Commit Message : docs: {해당 챕터 제목} ~~~
Pull Request : {해당 주차} 주차 ~~~
```

#### Git Flow 

```
[스터디 진행 전]
1. 스터디원 각각의 branch 생성
2. 정리 내용을 README.md에 담아 스터디원 각각의 branch에 push
3. main branch에서 주차별 branch 생성
4. 스터디원 각각의 branch -> 주차별 branch PR
5. PR에 질문 or 중요하다고 생각하는 내용 comment

[스터디 진행 후]
6. 스터디원 모두 PR을 함께 리뷰하며 comment 작성
7. 스터디를 마무리하며 각자 PR에 스터디 회고 comment 작성
8. PR squash merge & branch 삭제
9. 주차별 branch -> main branch PR 및 normal merge
10. 주차별 branch 삭제
```
