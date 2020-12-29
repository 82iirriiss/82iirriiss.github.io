---
sort: 2
---

# 운영 서버 적용 전 장고의 설정 변경 사항

## 1. 운영서버에 필요한 항목이 잘 설정되었는지 체크하는 명령어
`$python manage.py check --deploy`

## 2. settings.py의 SECRET_KEY

- 운영모드에서는 settings.py 파일에 하드코딩된 SECRET_KEY 를 **환경변수**에 저장하거나 **파일에 저장**하여 그 경로를 settings.py 파일에 적어준다.
```python
import os
SECRET_KEY = os.environ['SECRET_KEY']
# 또는,
with open (os.path.join(BASE_DIR, 'www_dir', 'secret_key.txt')) as f:
    SECRET_KEY = f.read().strip()
```

## 3. settigns.py의 DEBUG 모드

- 운영모드에서는 settings.py 파일의 DEBUG 모드를 **False**로 변경한다.

## 4. settigns.py의 ALLOWED_HOSTS 항목

- 운영모드에서는 장고가 실행되는 서버의 IP 주소나 도메인을 등록한다.

## 5. settings.py 파일의 STATIC_ROOT 항목과 collectstatic 명령

- 운영모드에서는 웹서버에게 정적파일의 위치를 알려주어야 한다.
- settings.py 파일의 'STATIC_ROOT' 항목은 `collectstatic` 명령 실행 시, 정적파일을 한곳에 모아주는 디렉토리 임
```python
STATIC_ROOT = os.path.join(BASE_DIR, 'www_dir', 'static')
```
- 이 디렉토리는 웹 서버의 설정 파일에도 등록해야 함
    + Apache의 경우, httpd.conf의 Alias/static/ 항목에 등록

- collectstatic 명령 실행
    + <font color='red'>주의!</font>
        - settings.py의 STATICFILES_DIRS 항목에 STATIC_ROOT 항목에 정의된 디렉토리는 없어야 함
        - collectstatic 명령 시, STATICFILES_DIRS 항목에 정의된 디렉토리의 정적파일을 모아서, STATIC_ROOT에 복사해 주기 때문

```console
$ python manage.py collectstatic
```

## 6. settings.py의 DATABASES NAME 항목과 접근권한

1. settings.py 파일의 DATABASES의 NAME 속성값의 경로를 **db/db.sqlist3**으로 변경
```python
DATABASES = {
    'NAME' : os.path.join(BASE_DIR, 'db', 'db.sqlite3')
}
```

2. DATABASE 디렉토리 및 파일의 접근권한 설정 ( 웹서버가 rw 할 수 있도록 )
```console
ch3 > mkdir db
ch3 > mv db.sqlite3 db/
ch3 > chmod 777 db/
ch3 > chmod 666 db/db.sqlite3 
```

## 7. settings.py의 LOGGING 항목과 로그파일 위치 접근권한
- settings.py 파일의 LOGGING 항목에 로그파일 위치가 정의되어 있음
- 웹 서버가 로그파일에 접근 할 수 있도록 접근권한 설정
```console
ch3 >chmod 777 logs/
ch3 >chmod 666 logs/mysite.log
```
## 8. 캐시서버와 데이터베이스 서버의 접속 PASSWORD 다른곳에 저장
- SECRET_KEY 처럼, settings.py 파일에 하드코딩된 비밀번호를 다른 곳에 저장

## 9. 메일발송 기능이 있을 경우, SERVER_EMAIL 및 DEFAULT_FROM_EMAIL 항목 설정
- 발신자 주소를 root@localhost 및 webmaster@localhost 로 지정
 