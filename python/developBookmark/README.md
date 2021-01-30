---
sort: 11
---

# bookmark 앱 개발
> 자세한 소스는 깃헙을 확인바람    

1. 설계 : 화면UI 설계 -> 테이블 설계 -> 로직 설계 -> URL 설계 -> 작업/코딩
2. 장고 뼈대 개발
    - 프로젝트 생성 : `(venv)$ django-admin startproject mysite`
    - 프로젝트 설정 파일 변경 : mysite/settings.py
    - 기본 테이블 생성 : `(venv)$ python3 manage.py migrate`
    - 슈퍼유저 생성 : `(venv) $ python3 manage.py createsuperuser`
    - 애플리케이션 생성 : `(venv) $ python3 manage.py startapp myapp`
    - 애플리케이션 등록 : seggings.py 의 INSTALLED_APPS 리스트에 'myapp.apps.MyappConfig'(예시) 추가
3. 모델 개발
    - 테이블 정의 : myapp/models.py 
    - admin 사이트에 테이블 반영 : myapp/admin.py
    - 데이터베이스 변경사항 반영  
        ```
        (venv)$ python3 manage.py makemigrations myapp
        (venv)$ python3 manage.py migrate
        ```
    - 테이블 모습 확인 
        - `(venv)$ python3 manage.py runserver # 백그라운드 실행 시 끝에 &를 붙인다.`
        - 웹브라우저 : http://localhost:8000/admin/
4. URLconf 개발
5. 뷰 개발
6. 템플릿 개발

source: `{{ page.path }}`
