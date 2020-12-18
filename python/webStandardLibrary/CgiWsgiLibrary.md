---
sort: 3
---

# CGI/WSGI 라이브러리

> CGI/WSGI는 웹서버로 들어오는 요청을 웹어플리케이션으로 처리를 위임하여 결과를 반환받을 때 웹서버와 웹 어플리케이션 사이의 규약을 준수한 라이브러리   
> CGI의 단점을 업그레이드 시킨것이 WSGI 임   
> Django 는 WSGI를 사용함   

## CGI 관련 모듈 (현재에 와서 사용빈도 낮음)
- cgi 모듈 : FieldStorage 클래스 정의 ( post 요청으로 들어온 파라미터를 처리하기 위한 클래스 )
- cgitb 모듈 : 에러 발생 시, 에러에 대한 상세 정보를 제공하기 위한 모듈

## WSGI 
- WSGI 서버 (mod_wsgi, uWSGI, Gunicorn) : 범용 웹서버인 apache나 NginX는 wsgi 처리 기능이 없으므로, 그러한 웹서버와의 통신 규격을 처리해 주는 파이썬 모듈
- Django도 wsgi 규격을 처리해 줌으로 WSGI 서버라 할 수 있다

## WSGI의 애플리케이션 처리 과정
1. 웹서버가 요청을 받음 : url 분석 / 필요시 WSGI에 처리 위임
2. WSGI 서버가 파라미터 전달받음 : wsgi.py 실행 / application(environ, start_response) 함수 호출 / console 출력
    - environ : 프레임워크에 정의되어 있음, HTTP_HOST, HTTP_USER_AGENT, SERVER_PROTOCOL 등의 HTTP 환경변수
    - start_response(status, headers) : 반드시 호출해야 하며, 인자가 이미 정해져 있음. 그냥 사용하면 됨
3. application 이 실행됨 : environ 환경변수 처리 / 뷰 처리 / HTTPRequest 객체 생성 / start_response() 함수 호출 / HTTPResponse 리턴
    - application 함수의 리턴값은 iterable 타입이어야 함

## wsgiref.simple_server 모듈
> 웹 프레임워크가 제공하는 wsgi 서버  
> WSGIServer 클래스, WSGIRequestHandler 클래스 제공  
> 장고의 runserver 도 wsgiref.simple_server 로 만듦  

```python
from wsgiref.simple_server import make_server

def application(environ, start_response):
    status = '200 OK'
    headers = [('Content-Type','text/plain')]
    start_response(status, headers)
    response = [b'This is a simple WSGI Application']

    return response

if __name__ == '__main__':
    print('Started WSGI Server on port 8888')
    # wsgi API 규격 : make_server(), serve_forever()
    server = make_server('',8888,application)
    server.serve_forever()
```
- 웹브라우저에 'http://localhost:8888' 실행하여 확인