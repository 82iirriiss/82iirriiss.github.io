---
sort : 1
---

# 아파치 웹 서버와 연동
> 아파치 웹서버 프로그램 : httpd   
> 아파치 웹서버와 장고 연동 프로그램 : mod_wsgi

## 1. 리눅스 환경 설정
. CentOs 7~
. Python3.6 (Centos 7~8이 Python3.6까지 지원하고 있음)   

### (1) 리눅스 기본 설정
1. 리눅스에서 배포할 사용자로 로그인 ( 나의 경우, centos 사용자로 로그인)
2. dnf (centos 패키지 관리프로그램) 업데이트 : `sudo dnf update`
3. python 설치
`$ sudo dnf install python3.*`
4. http, http-devel 설치 (작성 당시 2.4)
`$ sudo dnf install http http-devel`
5. git 설치
`$ sudo dnf install git`

### (2) python 가상환경 설정 및 장고 설치
1. 파이썬 가상환경을 만들 디렉토리 생성
`/home/centos> mkdir VENV`
2. 가상환경 디렉토리로 이동 후, 가상환경 설치 
```console 
/home/centos> cd VENV
/home/contos> virtualenv --python python3.6 v3PyBook 
```
- 'python3.6'버전을 설치하며, 설치할 장소는 'v3PyBook' 으로 지정 

3. 가상환경 실행 
```
/home/centos> source VENV/v3PyBook/bin/activate
(v3PyBook)/home/centos>
```

4. 가상환경에서 Django 설치 (관리자 권한에서 설치)
```
(v3PyBook)/home/centos> su
password: [root 사용자 비밀번호 입력]
(v3PyBook)/home/centos> pip install Django==2.0
```

## 2. 프로젝트 설정 변경

### (1) 프로젝트 다운로드
1. 프로젝트를 다운받을 디렉토리를 생성 후, 해당 디렉토리로 이동
```
/home/centos> mkdir pyBook
/home/centos> cd pyBook
```
2. **github**에서 프로젝트를 다운로드 받는다. (교재의 소스를 다운받았다.)
`/home/centos/pyBook>git clone 'https://github.com/millni/Django-hanbit.git'`
3. 프로젝트의 이름을 'ch8(임의의 이름)'으로 변경한다.
`/home/centos/pyBook>mv python-project ch8` 

### (2) 프로젝트 settings.py 파일 변경
. ch8/mysite/settings.py 파일

1. DEBUG 모드 변경 : 운영모드로 전환
```python
# DEBUG = True
DEBUG = False
```

2. ALLOWED_HOST 변경 : 아파치 서버의 ip (현재는 아파치와 프로젝트가 같은 PC 이므로 꼭 변경이 필요하지 않음)
```python
# ALLOWED_HOST = ['localhost','127.0.0.1']
ALLOWED_HOST = ['192.168.111.100','localhost','127.0.0.1']
```

3. STATIC_ROOT 경로 추가
```python
STATIC_ROOT = os.path.join(BASE_DIR, 'www_dir', 'static')
```

### (3) STATIC 파일 모으기
. (가상환경에서 실행)
```
(v3PyBook)/home/centos/pyBook/ch8> python manage.py collectstatic 
```

### (4) SECRET_KEY 변경 : 하드코딩 -> 파일

1.  settings.py 변경
```python
# SECRET_KEY = 어쩌구저쩌구~~
with open(os.path.join(BASE_DIR, 'www_dir', 'secret_key.txt')) as f:
    SECRET_KEY = f.read().strip()
```

2. secret_key.txt 파일 생성 후 키 입력
```
/home/centos/pyBook/ch8/www_dir> vim secret_key.txt
어쩌구저쩌구~~~
```

### (5) db.sqlite3 파일 위치 변경 및 migrate
1. settings.py 파일에서 DB 파일의 위치를 수정한다
```python
DATABASES = {
    . . .
    'NAME': os.path.join(BASE_DIR, 'db', 'db.sqlite3'),
}
```

2. ch8 아래 db 디렉토리를 생성 후 db.sqlite3을 옮긴다
```
/home/centos/pyBook/ch8> mkdir db
/home/centos/pyBook/ch8> mv db.sqlite3 db/db.sqlite3
/hoem/centos/pyBook/ch8> python manage.py migrate
```

### (6) db 폴더와 log 폴더 권한 변경
```
/home/centos/pyBook/ch8> sudo chmod 777 db
/home/centos/pyBook/ch8> sudo chmod 666 db/db.sqlite3

/home/centos/pyBook/ch8> sudo chmod 777 log
/home/centos/pyBook/ch8> sudo chmod 666 log/mysite.log
```

## mod_wsgi 확장모듈
> 내장모드 : 파이썬에 mod_wsgi 모듈을 내장하여 WSGI 프로그램을 실행하는 방식. 소스 반영 시, 해당  아파칭에 배치중인 모든 어플리케이션 재가동 해야 함   
> 데몬모드 : WSGI 전용 프로세스에서 WSGI 어플리케이션을 관리함. 상대적으로 적은 메모리 차지하고 각 어플리케이션 간 영향을 최소화 함. 데몬모드를 권장함   

### (1) mod_wsgi 설치
. mod_wsgi 프로그램 컴파일 파이썬 버전과 장고 어플리케이션 실행하는 파이썬 버젼이 같아야 함
1. 가상환경 내에서 mod_wsgi 설치
    
    ```
    /home/centos/VENV/v3PyBook> source bin/activate
    (v3PyBook)/home/centos/VENV/v3PyBook>sudo pip install mod_wsgi

    //만약 sudo 가 안 먹히면, su 를 입력하여 관리자 모드에서 pip 명령어 실행
    ```

2. 설치 확인

    .  mod_wsgi-express 명령으로 아파치 기본페이지 동작 확인
        
    ```
    (v3PyBook) $  mod_wsgi-express start-server
    ```
    - 웹브라우저에서 'http://localhost:8000' 입력 시, 화면에서  아파치 메인화면이 보여짐

    . mod_wsgi-express 로 내 프로젝트 동작 확인 (꼭 ch8에서 확인할 것)
        
    ```
    (v3pyBook)/home/centos/pyBook/ch8> mod_wsgi-express start-server mysite/wsgi.py
    ```
    - 웹브라우저에서 'http://localhost:8000' 입력 시, 화면에서 프로젝트 메인화면이 보여짐

    . python 테스트 서버로 프로젝트 동작 확인
    
    ```
    (v3pyBook)/home/centos/pyBook/ch8> python manage.py runserver
    ```
    - 웹브라우저에서 'http://localhost:8000' 입력 시, 화면에서 프로젝트 메인화면이 보여짐

### (2) mod_wsgi 아파치 모듈로 등록하기
> mod_wsgi를 아파치의 확장모듈로 설치 하기   
> 아파치 설정 수정하기   

1. mod_wsgi를 아파치 확장모듈로 설치
    ```
    (v3PyBook)/home/centos/VENV/v3PyBook > sudo /home/centos/VENV/v3PyBook/bin/mod_wsgi-express install-module

    LoadModule wsgi_module "/usr/lib64/httpd/modules/mod_wsgi-py36.cpython-36m-x86_64-linux-gnu.so"
    WSGIPythonHome "/home/centos/VENV/v3PyBook"
    ```

2. mod_wsgi 를 아파치 확장모듈로 등록하기
    ```
    (v3PyBook) $ cd /etc/httpd/conf.modules.d/
    (v3PyBook) $ sudo vi 10-wsgi.conf

    LoadModule wsgi_module "/usr/lib64/httpd/modules/mod_wsgi-py36.cpython-36m-x86_64-linux-gnu.so"
    ```
    - 1의 설치 결과로 나온 LoadModule wsgi ...(생략)...linux-gnu.so" 를 *.conf 파일에 작성하면 된다.
    - 이름은 임의로 해도 되지만, 확장자는 꼭 *.conf 로 지정한다.

## mod_wsgi를 내장모드로 웹서버 실행
### (1) 아파치 설정
```
$ cd /etc/httpd/conf.d/
$ sudo vim django.conf

WSGIScriptAlias /   /home/centos/pyBook/ch8/mysite/wsgi.py //1
WSGIPythonHome /home/centos/VENV/v3PyBook //2
WSGIPythonPath /home/centos/pyBook/ch8 //3

<Directory /home/centos/pyBook/ch8/mysite> // 프로젝트 디렉토리
<Files wsgi.py>
Require all granted // 접근허가
</Files>
</Directory>

Alias /static/ /home/centos/pyBook/ch8/www_dir/static/
<Directory /home/centos/pyBook/ch8/www_dir/static> // 정적파일 디렉토리
Require all granted  //접근 허가
</Directory>
```
- 1. /etc/httpd/conf/httpd.conf 에 직접 추가 해도 되고, /ect/httpd/conf.d/ 폴더 하위에 *.conf 로 추가해도 된다
    (httpd.conf 에서 conf.d 디렉토리 하위의 *.conf를 모두 불러들이기 때문)
- 2. WSGIPythonHome 으로 파이썬 가상공간의 홈 경롤르 잡아준다 (잘못 쓸 경우, 'client denied ...' 에러나 Permission 에러가 발생함 )
- 3. WSGIPythonPath 로 프로젝트 base 디렉토리를 잡아준다.

### (2) 작업 확인하기 (아파치 웹서버로 프로젝트 실행)
- 만약, SELinux 가 적용되어 있을 시, 작동이 안할 수도 있으므로 SELinux 설정 변경
```
$ sudo getenforce
Enforcing
$ sudo setenforce permissive
$ sudo getenforce
Permissive
```
- 로그인 중에만 일시 적용되며, 완전히 변경하고 싶으면 /etc/selinux/config 파일에서 변경 해야 함

- 아파치를 가동한다
`$ sudo apachectl start`

- 웹 브라우저에서 http://localhost 를 실행 시, 내 프로젝트 메인화면이 실행된다.

## mod_wsgi를 데몬모드로 웹서버 실행

### (1) 아파치 설정
```
$ cd /etc/httpd/conf.d/
$ sudo vim django.conf

WSGIScriptAlias /   /home/centos/pyBook/ch8/mysite/wsgi.py //1
WSGIDaemonProcess mygroup python-home=/home/centos/VENV/v3PyBook python-path=/home/centos/VENV/pyBook/ch8 //2
WSGIProcessGroup mygroup //3

<Directory /home/centos/pyBook/ch8/mysite> // 프로젝트 디렉토리
<Files wsgi.py>
Require all granted // 접근허가
</Files>
</Directory>

Alias /static/ /home/centos/pyBook/ch8/www_dir/static/
<Directory /home/centos/pyBook/ch8/www_dir/static> // 정적파일 디렉토리
Require all granted  //접근 허가
</Directory>
```
- 1. 아파치 웹서비스의 루트(/)와 wsgi.py 파일의 위치를 맵핑. / 로 시작하는 모든 요청은 wsgi.py 파일의 WSGI application에서 처리한다는 의미
- 2. 장고를 실행하기 위한 데몬 프로세스 정의
- 3. 장고 프로세스에서 실행되는 프로세스 그룹 지정 같은 프로세스 그룹에 할당된 애플리케이션은 같은 데몬 프로세스에서 실행됨

### (2) 작업 확인하기 (아파치 웹서버로 프로젝트 실행)
- 아파치를 가동한다   
`$ sudo apachectl start`

- 웹 브라우저에서 http://localhost 를 실행 시, 내 프로젝트 메인화면이 실행된다.
