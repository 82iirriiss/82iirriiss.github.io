---
sort: 1
---

# 프로젝트 패키지 구성, 네이밍, 설계

> 3티어 방식 : Presentation <-> Business <-> Persistence 로 구성   
> Presentation 계층 : 화면에 보여주는 기술 사용, MVC(Servlet/Controller), JSP    
> Business 계층 : 순수 비지니스 로직, 네이밍은 `xxxService` 클래스와 `xxxServiceImpl` 인터페이스로 구성, 메서드 이름은 주로 고객에 사용하는 용어 사용.   
> Persistence 계층 : 데이터베이스 처리, 개발자가 사용하는 용어 사용. `Mapper` 인터페이스와 `XML` 설정파일로 구성함.   

## 1. Naming Conversion ( 명명규칙 )

1. `xxxController` : Controller 클래스
2. `xxxService, xxxServiceImpl` : 비지니스 영역의 인터페이스는 `xxxService`, 구현은 `xxxServiceImpl` 사용
3. `xxxVO` : 불변성이 강한 데이터베이스의 테이블의 속성을 옮겨놓은 성격이 강함. read-Only의 목적이 강함.
4. `xxxDTO` : 주로 데이터 수집의 용도가 강함. 웹 화면에서 쓰는 속성의 경우.
5. `xxxMapper` : Service 인터페이스와 mybatis xml (sql 관리) 파일을 연결하는 인터페이스 파일.

## 2. 패키지 구성 및 패키지 네이밍

1. `org.example.config` : WebConfig.java, RootConfig.java, ServletConfig.java 등 설정 클래스들이 모인 패키지
2. `org.example.controller` : xxxController.java 파일이 모인 패키지
3. `org.example.service` : xxxService 인터페이스가 모인 패키지
4. `org.example.service.impl` : xxxServiceImpl 구현 클래스가 모인 패키지
5. `org.example.domain` : xxxVO, xxxDTO 파일들이 모인 패키지
6. `org.example.persistence` : MyBatis Mapper 인터페이스 패키지
7. `org.example.exception` : 웹 관련 예외 처리 패키지
8. `org.example.aop` : 스프링 aop 관련 패키지
9. `org.example.security` : 스프링 시큐리티 관련 패키지
10. `org.example.util` : 각종 유틸리티 클래스 관련 패키지

## 3. 설계 과정

1. 고객의 요구사항 인식
- 고객이 게시판에 글을 등록한다.
2. 고객 요구사항 분석 설계 : 어느정도까지 설계가 가능한지 범위를 정해야 함
3. 능숙한 팀원이 많다면 세세한 작업부터, 능숙한 팀원이 많지 않다면 눈에 보이는 결과를 만들어 내는 형태로 진행
4. 요구사항에 따른 화면 설계 
- 어떤 내용들을 입력할 것인가? -> 테이블이나 클래스의 멤버 변수가 추출됨
- 스토리 보드 작성시, Mock-up 툴 이용 : PowerPoint, Balsamiq studio, Pencil Mockup 등 SW 이용
- 화면의 흐름에 따라 url 설계