---
sort : 1
---

# 장고의 wsgi.py 파일

1. 웹서버와 애플리케이션을 연결하는데 필요한 파일
2. wsgi.py 에는 웹서버가 애플리케이션을 호출하는데 사용하는 application 객체가 정의되어 있는데, 이름이 반드시 application 이어야 함
    - wsgi.py : `application = get_wsgi_application()`
3. application 객체의 위치를 지정해 주어야 한다.
    - 웹서버 (Apache, NginX등) : httpd.conf나 uwsgi.ini 설정 파일등에 위치를 명시
    - 장고 개발서버 (runserver) : settings.py 파일에 WSGI_APPLICATION 변수로 지정
        - settings.py : `WSGI_APPLICATION = 'mysite.wsgi.application'`
4. 웹서버가 application 객체를 호출 하기 위해, 애플리케이션에 대한 설정 정보 로딩. 설정 정보를 wsgi.py 파일에 지정
    - wsgi.py : `os.environ.setdefault("DJANGO_SETTINGS_MODULE", "mysite.settings"`

# 장고의 WSGI 인터페이스

1. mysite/wsgi.py : `application=get_wsgi_application()` 의 get_wsgi_application()은 Django site-packages/core/wsgi.py 에 선언된 함수로, WSGI 규격을 작성해 둔 WSGIHandler() 를 반환한다.
2. 웹서버에서 장고 애플리케이션을 실행하기 위해서는, application 이 정의된 wsgi.py의 위치를 알아야 하므로, 웹서버/WAS서버 설정파일에는 wsgi.py 파일의 경로가 정의되어야 한다.
