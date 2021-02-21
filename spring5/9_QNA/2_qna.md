---
sort: 2
---

# Q&A

## 1. 한글 문제와 UTF-8 처리하기

## 2. 스프링부트 적용하기

## 3. 뒤로가기 문제 해결하기

## 4. MyBatis의 동적 SQL 사용하기

## 5. 오라클 데이터베이스 성능 올리기

## 6. 테스트 하기(Junit)

## 7. Mac OS 에 MySql dmg 설치

1. mySql 다운로드 : <https://dev.mysql.com/downloads/mysql/>
2. *.dmg 파일을 다운로드 받은 후, 해당 파일을 더블클릭하여 설치를 시작한다.
3. 설치 과정은 간단하므로 설치 마법사에 따라서 설치한다.
4. 맥 OS의 '시스템환경설정'에 가보면, mysql 서버 아이콘이 보여진다.
5. mysql 아이콘을 더블 클릭해 보면, 현재 상태를 알 수 있다. 
6. mysql 서버가 실행중인 것을 확인 후 터미널로 이동한다. (DBMS 에서는 자동으로 서버를 스캔하여 접속 가능하다.)
7. 터미널에서 mysql 을 실행한다.
    ```zsh
    $ cd /usr/local/mysql/bin
    $ ./mysql -u root -p
    Enter Password:[비밀번호 입력]
    mysql> 
    ``

8. mysql 경로로 직접 가서 mysql shell 을 실행하지 않고, 'mysql' 명령어만으로 실행하고 싶다면, .zshrc에 alias를 추가한다.
    ```.zshrc
    <!-- 생략 -->
    alias mysql="/usr/local/mysql/bin/mysql"
    <!-- esc + wq: 로 저장 -->
    ```

## 8. domain의 종류인 VO 와 DTO 는 어떤 차이가 있나요?

## 9. myBatis XML 파일에 다중의 파라미터를 전달하려면 어떻게 해야 하나요?
