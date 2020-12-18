---
sort: 2
---

# 2. 웹 서버 라이브러리
> 웹서버는 프레임워크를 사용해서 개발하는 경우가 대부분이지만, 웹 서버 라이브러리의 동작원리를 익히는 것이 고급 개발자로 가는 길이다.   

## http.server의 주요 클래스

|클래스 명  |주요기능   |
|:---        |:---        |
|HTTPServer |. 웹서버를 만들기 위한 클래스, ip와 port를 바인딩 함 <br>. HTTPServer객체 생성 시, 핸들러가 반드시 필요함   |
|BaseHTTPRequestHandler |. 핸들러를 만들기 위한 기반 클래스. HTTP 처리 로직이 들어 있음<br>. 이 클래스를 상속 받아, 자신의 로직 처리를 담당하는 핸들러를 만들 수 있음 |
|SimpleHTTPRequestHandler|. BaseHTTPRequestHandler를 상속받아 만든 클래스<br>. GET과 HEAD 메소드 처리가 가능한 핸들러|
|CGIHTTPRequestHAndler|. SimpleHTTPRequestHandler클래스를 상속받아 만든 클래스<br>. 추가적으로 POST와 CGI 처리가 가능한 핸들러 클래스|

## 가장 간단한 웹서버 만들기 샘플
> HTTPServer와 BaseHTTPRequestHandler 를 이용

```python
from http.server import HTTPServer, BaseHTTPRequestHandler

class MyHandler(BaseHTTPRequestHandler):
    def do_GET(self):
        self.send_response_only(200, 'OK')
        self.send_header('Content-Type', 'text/plain')
        self.end_headers()
        self.wfile.write(b"Hello World")

if __name__ == '__main__':
    # 서버의 ip, port, 핸들러를 인자로 하여, HttpServer객체를 생성한다.
    server = HTTPServer(('',8888), MyHandler)
    print("Started WebServer on port 8888......")
    print("Press ^C to quit WebServer")
    # 요청을 처리함
    server.serve_forever()
```
- 웹서버를 만드는 방법은 일정한 룰에 의해 작성됨

## SimpleHTTPRequestHandler 클래스
> 별도의 코딩이 필요 없음  
> do_GET(), do_HEAD() 메소드가 정의되어 있음 

```
$ python -m http.server 8888
```
- 웹브라우저에 http://localhost:8888 을 요청하면, 내 PC의 디렉터리 목록이 보여진다.

## CGIHTTPRequestHandler 클래스
> 미리 구현되어 있어서, 즉시 웹서버 실행이 가능함  
> do_POST()메소드가 정의되어 있고 CGI 웹서버 실행만 가능함    
> CGI 웹서버 실행시 --cgi 옵션을 이용  
> 디폴트 포트번호는 8000   
> http.server --cgi 실행위치가 웹서버의 루트 디렉토리가 됨

```
$python -m http.server 8888 --cgi
```

### CGIHTTPRequestHandler 클래스를 이용하여 Post 요청 해보기
- 서버 스크립트
    + 반드시 cgi-bin 폴더안에 파일을 생성해야 함
    + 파일에 실행권한을 주어야 함  
    `$ chmod 755 ./cgi-bin/cgi_server.py`  
    + 새로운 터미널을 열고 cgi-bin 폴더의 상위폴더에서 http.server --cgi 를 실행하면, 자동으로 cgi-bin 폴더안의 cgi-server.py가 실행됨  
    `$ python -m http.server 8888 --cgi`

```python
#!/usr/bin/env python

import cgi

form = cgi.FieldStorage()
name = form.getvalue('name')
email = form.getvalue('email')
url = form.getvalue('url')

print('Content-Type: text/plain')
print()

print('Welcome... CGI Script')
print('name is ', name)
print('email is ', email)
print('url is ', url)
```

- 클라이언트 스크립트
    + http.server --cgi 명령을 실행한 위치가 루트가 되므로, url이 .../cgi-bin/cgi_server.py 가 되는 것임   

```python
from urllib.request import urlopen
from urllib.parse import urlencode

url = 'http://127.0.0.1:8888/cgi-bin/cgi_server.py'
data = urlencode({
    'name':'김윤미',
    'email':'kym@naver.com',
    'url':'http://www.naver.com'
})
postData = data.encode()

f = urlopen(url, postData)
print(f.read().decode('utf-8'))

```