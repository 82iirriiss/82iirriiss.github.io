---
sort: 1
---

# 설계시 알아두기

## 1. 설계 순서
1. 화면 UI 설계 : 화면 캡쳐본이나 디자인 본
2. 테이블 설계 양식  

    |필드명|타입|제약조건|설명|
    |---|---|---|---|
    |id|integer|PK, Auto Increment|기본키|

3. 로직 설계 양식   

    | URL | View(=controller) | Templates |
    |-----|-------------------|-----------|
    |/bookmark/|BookmarkLV.as_view()|boomark_list.html|

4. URL 설계 양식   

    | URL 패턴 | 뷰 이름 | 템플릿 파일 이름 |
    |----------|---------|------------------|
    |/bookmark/99/ |BookmarkDV(DetailView)|bookmark_detail.html|


## 2. 작업/코딩 순서

| 작업순서 | 관련명령/파일 | 필요한 작업 내용 |
|----------|---------------|------------------|
|뼈대만들기 | startproject<br>settings.py<br>migrate<br>createsuperuser<br>startapp<br>settings.py| mysite 프로젝트 생성<br>프로젝트 설정 항목 변경<br>User/Group 테이블 생성<br>프로젝트 관리자인 슈퍼유저 생성<br>북마크 앱 생성<br>북마크 앱 등록 |
|모델코딩 | models.py<br>admin.py<br>makemigrations<br>migrate|모델(테이블)정의<br>Admin 사이트에 모델 등록<br>모델의 변경사항 추출<br>변경사항을 데이터베이스에 반영|
|URLconf코딩 |urls.py |URL 정의 |
|뷰 코딩 | views.py | 뷰 로직 구성 |
|템플릿 코딩하기 | templates 디렉토리 |템플릿 파일 작성|
