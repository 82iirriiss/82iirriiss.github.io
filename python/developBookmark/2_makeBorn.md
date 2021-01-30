---
sort: 2
---

# 개발 뼈대 세우기

<br>

## 1. 프로젝트 생성

```bash
$ source /home/centos/VENV/venv/bin/activate
(venv) $ cd /home/centos/projectDir/
(venv) $ django-admin startproject mysite
# 베이스디렉토리명 변경
(venv) $ mv mysite myPrj 
```
- 베이스디렉토리(루트디렉토리) mysite와 하위의 프로젝트 관리용 디렉토리 mysite 2개가 생성됨
- 베이스디렉토리명은 변경해도 아무 영향이 없으므로, 프로젝트명으로 변경

## 2. 설정파일 settings.py  파일 변경 (mysite/settings.py)

#### (1) **ALLOWED_HOSTS** 

- 서버의 IP나 도메인 등록
- `ALLOWED_HOSTS = ['192.168.111.100','localhost','127.0.0.1']`
#### (2) **INSTALLED_APPS**

- startapp 명령으로 신규 앱을 생성 후, INSTALLED_APPS 에 등록
```bash
(venv) $ python3 manage.py startapp bookmark
(venv) $ cat /bookmark/apps.py
(생략)
class BookmarkConfig(AdminConfig)
(생략)
(venv) $ cd ..
(venv) $ vim mysite/settings.py
```

```python
# 앱  추가
INSTALLED_APPS = [..(생략),
bookmark.apps.BookmarkConfig,
]
```

#### (3) **TEMPLATES의 'DIRS' 항목**

```python
TEMPLATES = [
    ...
    'DIRS'=[os.path.join(BASE_DIR, 'templates')],
    ...
]
```

#### (4) **데이트베이스 엔진 (기본은 sqlite3)**

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3' # 이부분 내가 사용코저 하는 DB 엔진으로 변경. 오라클, mysql 등..
    }
}
```

#### (5) **TIME_ZONE**

- 한국시간으로 변경
- `TIME_ZONE = 'Asia/Seoul'`

#### (6) **STATIC_URL, STATICFILES_DIRS**

- 정적파일들을 모아둘 경로를 지정
```python
STATIC_URL = '/static/'
STATICFILES_DIRS = [os.path.join(BASE_DIR, 'static')]   #추가
```

#### (7) **MEDIA_URL, MEDIA_ROOT**

- 파일 업로드 기능을 개발할 때 필요한 설정
```python
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

#### (8) **USE_TZ**

- 한국 시간대만 사용하는 프로젝트의 경우, 아래와 같이 설정하면 DB에 저장되는 시간도 한국시간으로 저장되어 편리함
- `USE_TZ = False`

#### (9) **LANGUAGE_CODE**

- Admin 사이트 화면의 메뉴 및 설명이나, 날짜/시간에 대한 표현이 달라지므로 주의
- 아래와 같이 설정하면 한국어로 표현됨
- `LANGUAGE_CODE = 'ko-kr'`

<br> 

## 3. 기본테이블 생성 (User, Group)

#### (1) **장고가 기본적으로 제공하는 User, Group 테이블을 생성하는 과정**

```bash
(venv)$ python3 manage.py migrate
```
<br>

## 4. 슈퍼유저 생성

#### (1) **장고가 제공하는 Admin 사이트에 접속할 관리자(슈퍼유저) 생성 과정**
#### (2) Username, Email, Password, Password(again) 을 입력 해야 함

```bash
(venv) $ python3 manage.py createsuperuser
```
<br>

## 5. 애플리케이션 생성

```bash
(venv) $ python3 manage.py startapp bookmark
```

<br>

## 6. 애플리케이션 등록

#### (1) **bookmark 앱 생성시 자동으로 생성된 apps.py에 정의된 BookmarkConfig를 settings.py에 등록해야 함**

```python
INSTALLED_APPS = [
    ....
    'bookmark.apps.BookmarkConfig', # 추가
]
```
