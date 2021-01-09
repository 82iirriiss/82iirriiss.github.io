---
sort : 1
---

# NGINX 웹 서버와 연동
- 2004년 러시아에서 만든 무료 오픈소스 웹서버
- 아파치보다 동시처리 능력을 높이고 메모리를 적게 사용
- NGINX와 장고 사이에서 WAS 역할을 하는 uWSGI 모듈과 gunicorn이 있으나, 대부분 uWSGI를 사용

## 1. 장고 설정 변경하기

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

## 3. NGINX 설치
> https://docs.nginx.com 사이트 참조. NGINX Plus > Admin Guide > Installing NGINX and NGINX Plus 로 이동하여 상세 정보 확인   

. NGINX 설치 시 선택 항목

|선택항목 | 선택 | 가능한 다른 선택들 |
|---------|------|--------------------|
|솔루션명 |NGINX Open Source |NGINX Plus(유료) |
|버전   |Stable |Mainline(시험용 기능 포함) |
|컴파일여부 |Prebuilt |Compiling from Source |
|서버 OS | CentOs or RHEL |Debian, Ubuntu, SUSE |
|저장소 |NGINX 저장소 신규 생성 |OS 제공 기본 저장소 사용 |

1. 저장소 생성
    ```
    $ sudo vi /etc/yum.repos.d/nginx.repo

    [nginx]
    name=nginx repo
    baseurl=http://nginx.org/packages/centos/8/$basearch/
    gpgcheck=0
    enabled=1
    ```

2. 저장소 업데이트
    ```
    $ sudo yum update
    ```

3. 설치완료 후 nginx 가동
    ```
    $ sudo nginx
    ```

4. nginx 정상 가동 확인
    ```
    $ curl -I 127.0.0.1

    HTTP/1.1 200 OK
    Server: nginx/1.14.0 
    ```
    - 200 OK가 나오면, 정상가동됨

### <font color='red'> ✤ nginx 명령 </font>
. nginx 가동 : `$ sudo nginx`   
. nginx 정지 : `$ sudo nginx -s stop`   
. nginx 재가동 : `$ sudo nginx -s reload`   
. nginx 설정 파일 테스트 : `$ sudo nginx -t`   
. nginx 도움말 : `$ sudo nginx -h`    

## 4. NGINX 설정
> 설정파일 위치 : /etc/nginx/conf.d/   

```conf
$ sudo vim /etc/nginx/conf.d/ch9_nginx.conf

server {
    listen 8000;
    server_name 127.0.0.1;

    # access_log /var/log/nginx/codejob.co.kr_access.log;   // 이미 /etc/nginx/nginx.conf에 지정되어 있음.
    # error_log /var/log/nginx/codejob.co.kr_error.log;
    
    location = /favicon.ico {access_log off; log_not_found off; }  
    # /favicon.ico 으로 요청이 들어오면, access_log 기록하지 않고, /favicon.ico 을 찾지 못해도 error_log 에 기록하지 않음

    location /static/ {
        root /home/centos/pyBook/ch9/www_dir; // 정적파일의 루트 디렉토리 지정
        # alias /home/centos/pyBook/ch9/www_dir/static/; // alias 디렉티브를 사용 할 수도 있음. path 끝에 '/'로 끝나야 함
    }

    location / {
        include /home/centos/pyBook/ch9/www_dir/uwsgi_params; // 위의 URL 외의 URL 처리, uwsgi 에 넘겨줄 파라미터 정의
        uwsgi_pass 127.0.0.1:8001; //nginx 에서 uwsgi 프로그램으로 처리르 위임. 웹서버와 wsgi 가 통신하기 위한 ip:port 설정
        # uwsgi_pass unix:///home/centos/pyBook/ch9/www_dir/ch9.sock; // 위의 작업을 UDS(unix domain socket) 방식을 이용해도 됨. 웹서버와 WAS 가 같은 H/W에 존재 할 때는 UDS 방법의 성능이 훨씬 우세함 
    }
}

$ cp /etc/uwsgi/nginx/uwsgi_params /home/centos/pyBook/ch9/www_dir/  //uwsgi_params 는 uwsgi 를 설치 시 생성될 것임. 이 파일을 장고 프로젝트의 적절한 위치로 복사
```

## 5. uWSGI 설치
> uWSGI 공식문서 : <http://uwsgi-docs.readthedocs.io/en/latest/tutorials/Django_and_nginx.html>   
> 운영환경을 만들 때는 전역 설치를 하는 것이 일반적이므로, root 또는 sudo 권한으로 설치  

```
$ sudo yum install python3-devel
$ sudo pip3 install uwsgi
```
- 주의 : python3-devel (파이썬 라이브러리) 먼저 설치 후 uwsgi 설치

### <font color='red'> ✤ uwsgi 명령 </font>
- 기동 : `$ uwsgi`   
- 정지 : `$ uwsgi --stop pid-file`
- 재기동: `$ uwsgi --reload pid-file`   
- 도움말: `$ uwsgi --help`   

## 6. uWSGI 설정
> 장고 프로젝트별 하나의 설정파일 필요함   
> vassals 는, 자식프로세스를 뜻하는 uWSGI 용어   

```ini
$ sudo mkdir -p /etc/uwsgi/vassals
$ sudo vi /etc/uwsgi/vassals/ch9_uwsgi.ini

[uwsgi]
chdir = /home/centos/pyBook/ch9 # 장고의 베이스 디렉토리
home = /home/centos/VENV/v3PyBook # 가상환경 사용 시, 가상환경의 루트 디렉토리 (가상환경 사용하지 않으면 생략)
module = mysite.wsgi:application # wsgi.py 파일의 모듈 경로 및 application 변수
socket = :8001 # 웹서버와 통신할 소켓의 포트 번호
# socket = /home/centos/pyBook/ch9/www_dir/ch9.sock # 웹서버가 UDS 사용 시 소켓 파일의 경로. nginx 설정 파일의 소켓파일과 경로가 같음.
# chmod-socket = 666
master = True # 별도의 마스터 프로세스가 가동되도록 지정
processes = 5 # uwsgi 가동 시 자식 프로세스를 5개 생성
pidfile = /tmp/ch9-master.pid # 마스터 프로세스 ID를 저장할 파일
vacuum = True # 프로세스 종료 시 소켓파일을 포함하여 환경변수 클리어
max-requests = 5000 # 현 프로세스에서 처리할 최대 요청 개수 지정
daemonize = /var/log/uwsgi/ch9.log # 백그라운드에서 프로세스가 실행되도록 데몬화하고 데몬에서 사용할 로그 파일을 지정

$ sudo touch /var/log/uwsgi/ch9.log # 설정파일에서 지정한 로그파일 생성
$ sudo chmod 666 /var/log/uwsgi/ch9.log # uwsgi 프로세스가 쓸 수 있도록 권한을 변경
```

## 7. 작업 확인하기
> nginx는 루트권한으로 실행 해야 함   
> uwsgi는 루트계정 및 일반사용자 계정으로도 실행 할 수 있음   

### (1) uWSGI Emperor 모드
- 시스템 전역으로 uwsgi 프로그램을 설치 하였으므로, 루트 권한으로 실행   
- 루트 권한으로 실행시 모동 Emperor 모드로 실행함   
- <font color='red' > Emperor 모드 : 설정파일 변경시 자동으로 프로세스 재가동 </font>   

1. nginx와 uwsgi 프로그램을 실행한다.
```bash
$ sudo nginx
#  이미 실행 중이라면, 아래의 명령 실행
$ sudo nginx -s reload
# uwsgi 서버 가동, uid,gid는 uwsgi 프로세스의 주인이 될 사용자와 그룹 아이디를 지정
# uwsgi 명령에 옵션을 붙여 실행 할 수도 있지만, 
# *.ini 파일에 명령을 기록 후 *.ini 파일을 실행 시킬 수도 있음
$ sudo uwsgi --emperor /etc/uwsgi/vassals --uid centos --gid centos
```

2. 웹 브라우저에서 http://localhost:8000/ 을 요청하면, 프로젝트 메인화면이 실행된다. 

### (2) uWSGI 일반모드
> 간단하게 사용하거나 소규모 프로젝트라면, Emperor 모드가 아닌 일반모드로 실행 할 수 있음   
> 이 경우, 일반 사용자계정으로 설정파일 생성 후, 실행 해도 됨, 그래서 설정파일과 로그 파일을 장고 프로젝트 하위로 변경   

1. uWSGI 설정파일 장고프로젝트 하위에 작성        
    ```bash   
    $ cd /home/centos/pyBook/ch9/www_dir
    $ vim ch9_uwsgi.ini

    # 하위는 *.ini 파일로, 내용은 daemonize 설정(로그 설정)을 빼고, 루트모드에서 실행 시와 같다.
    [uwsgi]
    chdir = /home/centos/pyBook/ch9 # 장고의 베이스 디렉토리
    home = /home/centos/VENV/v3PyBook # 가상환경 사용 시, 가상환경의 루트 디렉토리 (가상환경 사용하지 않으면 생략)
    module = mysite.wsgi:application # wsgi.py 파일의 모듈 경로 및 application 변수
    socket = :8001 # 웹서버와 통신할 소켓의 포트 번호
    # socket = /home/centos/pyBook/ch9/www_dir/ch9.sock # 웹서버가 UDS 사용 시 소켓 파일의 경로. nginx 설정 파일의 소켓파일과 경로가 같음.
    # chmod-socket = 666
    master = True # 별도의 마스터 프로세스가 가동되도록 지정
    processes = 5 # uwsgi 가동 시 자식 프로세스를 5개 생성
    pidfile = /tmp/ch9-master.pid # 마스터 프로세스 ID를 저장할 파일
    vacuum = True # 프로세스 종료 시 소켓파일을 포함하여 환경변수 클리어
    max-requests = 5000 # 현 프로세스에서 처리할 최대 요청 개수 지정
    daemonize = /home/centos/pyBook/ch9/www_dir/ch9.log # 백그라운드에서 프로세스가 실행되도록 데몬화하고 데몬에서 사용할 로그 파일을 지정

    # 로그파일을 생성하고 권한을 변경
    $ touch /home/centos/pyBook/ch9/www_dir/ch9.log
    $ chmod 666 /home/centos/pyBook/ch9/www_dir/ch9.log
    ```

2. nginx 와 uWSGI 프로그램 가동   
. 일반모드이므로, uwsgi도 일반 사용자로 실행

    ```bash
    $ cd /home/centos/pyBook/ch9/
    $ uwsgi --ini www_dir/ch9_uwsgi.ini

    # uwsgi 중지 명령
    $ uwsgi --stop /tmp/ch9-master.pid
    ```
3. 동작 확인
- 웹브라우저로 http://localhost:8000/을 호출하여, 프로젝트의 메인화면이 실행되는지 확인

### (3) 유닉스 도메인 소켓 사용
> 웹서버와 WAS가 동일한 H/W 에서 실행될 때는, 네트워크 모듈을 통한 소켓통신이 아니라, 파일 시스템을 사용하여 소켓 통신을 하는 것이 더 성능이 좋다.   
> 파일 시스템을 사용한 소켓 통신중 하나가 **유닉스 도메인 소켓(UDS)** 방식이다.   
> uWSGI 도 UDS 방식을 지원한다.   

1. NGINX 설정 파일 수정
    ```conf
    $ sudo vim /etc/nginx/conf.d/ch9_nginx.conf

    server {
        (생략)
        location / {
            include /home/centos/pyBook/ch9/www_dir/uwsgi_params;
            # uwsgi_pass 127.0.0.1:8001
            uwsgi_pass unix:///home/centos/pyBook/ch9/www_dir/ch9.sock;
        }
    }
    ```
    - uwsgi_pass 를 ip:포트 방식이 아닌 소켓의 경로를 지정하는 것이 핵심

2. uWSGI 설정 파일 수정 (Emperor 모드 예제)
    ```ini
    $ cd /etc/uwsgi/vassals
    $ sudo vim ch9_uwsgi.ini
    
    [uwsgi]
    socket = /home/centos/pyBook/ch9/www_dir/ch9.sock
    chmod-socket = 666
    (아래내용 동일)
    ```

3. uWSGI 프로그램을 실행 시키기 위한 *.ini 파일 생성 
> `$ sudo uwsgi --emperor /etc/uwsgi/vassals --uid centos --gid centos` 로 *.ini 파일없이 실행 가능함   

    ```ini
    $ cd /etc/uwsgi
    $ sudo vim uwsgi_emperor.ini

    [uwsgi]
    emperor = /etc/uwsgi/vassals
    uid = centos
    gid = centos
    master = True
    pidfile = /tmp/emperor.pid
    vacuum = True
    daemonize = /var/log/uwsgi/emperor.log
    ```

4. ninx와 uwsgi 프로그램 실행   
    ```bash
    $ sudo nginx
    $ sudo uwsgi --ini /etc/uwsgi/uwsgi_emperor.ini

    # 중지명령
    $ sudo nginx -s stop
    $ sudo uwsgi --stop /tmp/emperor.pid
    ```

5. 동작 확인   
. 웹브라우저에서 'http://localhost:8000' 을 호출하면 프로젝트 메인화면이 실행됨
