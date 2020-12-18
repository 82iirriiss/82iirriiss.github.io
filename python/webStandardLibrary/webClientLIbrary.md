---
sort: 1
---

# 웹 클라이언트 라이브러리
> __KEY WORD__   
> `#urllib.parse  #urlparse()  #urllib.request  #urlopen()  #POST요청  #Request()  #build_open()  #HTTPBasicAuthHandler  #HTTPCookieProcessor
>  #install_open()  #ProxyHandler  #ProxyBasicAuthHandler  #HTMLParser  #feed()`

## 1. urllib.parse 모듈
> URL 분해, 조립, 변경, URL 문자 인코딩, 디코딩 등을 처리.   
> urlparse(), urljoin(), parse_qs(), quote(), encode() 함수등이 있음.   
> <https://docs.python.org/3/library/urllib.parse.html#module-urllib.parse>      

### urlparse()
- 예제
```
>>> from urllib.parse import urlparse
>>> result = urlparse("http://www.python.org:80/guido/python.html;philosophy?overall=3#n10")
>> result
ParseResult(scheme='http', netloc='www.python.org:80', path='/guido/python.html', params='philosophy', query='overall=3', fragment='n10')
```

## 2. urllib.request 모듈
> 주어진 URL에서 데이터를 가져오는 기능

### urlopen()
- 문법
```
urlopen(url, data=None, [timeout])
```
  + url 인자 : 문자나 Request 클래스
  + url에 file 스킴 지정 시, 파일을 열 수 있음
  + data 인자가 None 이거나 없으면 GET 요청, data 인자로 문자열이 있으면 POST 요청   

### urlopen()으로 get 방식 요청하기
```
>>> from urllib.request import urlopen
>>> f = urlopen('http://www.example.com')
>>> print(f.read(500).decode('utf-8'))
```

### urlopen()으로 post 방식 요청하기
  + data 인자는 `바이트 스트링` 이어야 함
- 예제
``` 
>>> from urllib.request import urlopen
>>> data = "language=python&framework=django"
>>> f = urlopen("http://127.0.0.1:8000", bytes(data, encoding='utf-8'))
>> print(f.read(500).decode('utf-8'))
```

### urlopen()과 Request 클래스 이용하여 요청 헤드 지정하기
```
>>> from urllib.request import urlopen, Request
>>> from urllib.parse import urlencode
>>>
>>> url = 'http://127.0.0.1:8000'
>>> data = {
  'name':'김윤미',
  'email':'kimyn@naver.com',
  'url':'http://www.naver.com'
}
>>> encDate = urlencode(data)
>>> postData = bytes(encData, encoding='utf-8')
>>> req = Request(url, data=postData)
>>> req.add_header('Content-Type', 'application/x-www-form-rulencoded')
>>> f = req.urlopen(req)
>>> print(f.info())
>>> print(f.read(500).decode('utf-8'))
```

### build_opener()와 HTTPBasicAuthHandler 클래스를 이용하여 인증요청 보내기
```
>>> from urllib.request import HTTPBasicAuthHandler, build_opener
>>> auth_handler = HTTPBasicAuthHandler()
>>> auth_handler.add_password(realm='kym', user='ymkim', passwd='ymkimadmin', uri='http:// 127.0.0.1:8000/auth')
>>> opener = build_opener(auth_handler)
>>> resp = opener.open('http://127.0.0.1:8000/ahtu/')
>>> print(resp.read().decode('utf-8'))
```
  + 핸들러 생성 => 생성한 핸들러를 오프너에 전달하여 오프너 생성 => 오프너로 요청

### build_open()와 HTTPCookieProcessor 클래스로 쿠키데이터 포함한 요청 하기
```
from urllib.request import Request, HTTPCookieProcessor, build_opener
url = 'http://127.0.0.1:8000/cookie'
# get 방식으로 쿠키핸들러 요청하기
# 쿠키 핸들러 생성, 쿠키 데이터 저장
cookie_handler = HTTPCookieProcessor()
opener = build_open(cookie_handler)

req = Request(url)
res = opener.open(req)
print(res.info())
print(res.read().decode('utf-8'))

# 이전의 요청에서 받은 쿠키를 헤더에 담아서 Post 요청
data = 'language=python&framework=django'
encData = bytes(data, encoding='utf-8')
req = Request(url, encData)
res = opener.open(req)
print(res.info())
print(res.read().decode('utf-8'))
```

### install_opener() 와 ProxyHandler 및 ProxyBasicAuthHandler 클래스로 프록시 처리
```python
import urllib.request

url = 'http://www.example.com'
proxyServer = 'http://www.proxy.com:3128'

# proxy 서버를 설정함
proxy_handler = urllib.request.ProxyHandler({'http':proxyServer})
# 프록시 서버 설정 무시하고 웹서버로 요청 시,
# proxy_handler = urllib.request.ProxyHandler({})

# 프록시 서버에 대한 인증 처리
proxy_auth_handler = urllib.request.ProxyBasicAuthHandler()
proxy_auth_handler.add_password('realm', 'host', 'username', 'password')

# 2개의 핸들러를 오프너에 등록
opener = urllib.request.build_opener(proxy_handler, proxy_auth_handler)
# 디폴트 오프너로 등록
urllib.request.install_opener(opener)

# opener.open() 대신 urlopen()을 사용
f = urllib.request.urlopen(url)
print("geturl():", f.geturl())
print(f.read(300).decode('utf-8'))
```

### urllib.request와 HTMLParser를 이용해서 이미지만 검색하여 리스트를 보여주기
```python
from html.parser import HTMLParser
from urllib.request import urlopen

# HTMLParser 를 사용할 때는, 이렇게 HTMLParser를 상속받고, 필요한 메소드를 overrwide 합니다.
class ImageParser(HTMLParser):
    # 'img' 태그를 찾기 위해서 handler_starttag 를 오버라이드 합니다.
    def handle_starttag(self, tag, attrs):
        if tag != 'img':
            return
        if not hasattr(self, 'result'):
            self.result = []

        for name, value in attrs:
            if name == 'src':
                # img src 태그를 찾으면 self.result 목록에 추가합니다.
                self.result.append(value)


def parse_image(data):
    parser = ImageParser()
    # HTML문장을 feed() 함수에 넣어주면, 바로 파싱하고 그 결과를 parser.result에 추가
    parser.feed(data)
    # set 에 parser.result를 넣어주어 중복된 값을 삭제합니다.
    dataSet = set( x for x in parser.result)
    return dataSet

def main():
    url = "http://www.google.co.kr"
    # urlopen 함수를 이용하여 구글의 첫페이지를 가져옵니다.
    with urlopen(url) as f:
        chatset = f.info().get_param('charset')
        data = f.read().decode(chatset)

    dataSet = parse_image(data)
    print('\n>>>> Fetch Images from', url)
    print('\n'.join(sorted(dataSet)))

```

## 3. http.client 모듈
> GET, POST 이외의 방식으로 요청하는등 저수준의 더 세밀한 기능이 필요할 때 사용   
> putheader(), endheaders(), send() 등의 함수가 있다.   
> 참조: <https://docs.python.org/3/library/http.client.html>   

### http.client 모듈의 코딩 순서

|순번 |코딩순서 |코딩예시|
|-----|---------|--------|
|1    |연결 객체 생성 | conn = http.client.HTTPConnection('www.naver.com') |
|2    |요청 보냄|conn.request('GET','/index.html') |
|3    |응답 객체 생성|res = conn.getresponse() |
|4    |응답 데이터 읽음 |data = res.read() |
|5    |연결 닫음 |conn.close() |

### GET 방식 요청하기

```python
from http.client import HTTPConnection

host = 'www.naver.com'
# HTTPConnection()의 인수로 url('http://www....')이 아닌 host('www....')를 이용한다.
conn = HTTPConnection(host)
# conn.request(메소드, url, [body], [header])
conn.request('GET','/')
res1 = conn.getresponse()
print(res1.status, res1.reason)
# 데이터를 모두 읽어들여야 다음 요청이 가능하다. 그렇지 않으면 error가 발생한다.
data1 = res1.read()

# 두번째 요청
conn.request('GET','/')
res2 = conn.getresponse()
print(r2.status, r2.reason)
data2 = res2.read()
print(data2.decode())
conn.close()
```

### HEAD 메소드로 요청하기

```python
from http.client import HTTPConnection
conn = HTTPConnection('www.naver.com')
conn.request('HEAD','/')
resp = conn.getresponse()
data = resp.read()
# head를 요청하였으므로, data( =body) 에는 아무것도 없어서 
# 0 이 출력될 것이다.
print(len(data))
```

### POST 방식으로 요청하기
```python
from http.client import HTTPConnection
from urllib.parse import urlencode

host = '127.0.0.1:8000'
# 인코딩 해야 함
params = urlencode({
  'language':'python',
  'name':'김윤미',
  'email':'kym@naver.com'
})
headers = {
  'Content-Type':'application/x-www-form-urlencoded',
  'Accept':'text/plain'
}
conn = HTTPConnection(host)
conn.request('POST','', params, headers)
resp = conn.getresponse()
print(resp.status, resp.reason)

data = resp.read()
print(data.encode('utf-8'))

conn.close()
```

### PUT 메소드로 요청하기

```python
from http.client import HTTPConnection
from urllib.parse import urlencode

host = '127.0.0.1:8000'
# 인코딩 해야 함
params = urlencode({
  'language':'python',
  'name':'김윤미',
  'email':'kym@naver.com'
})
headers = {
  'Content-Type':'application/x-www-form-urlencoded',
  'Accept':'text/plain'
}
conn = HTTPConnection(host)
conn.request('PUT','', params, headers)
resp = conn.getresponse()
print(resp.status, resp.reason)

data = resp.read(300)
print(data.encode('utf-8'))

conn.close()
```

### 웹사이트에서 이미지만을 검색하여 그 이미지들을 다운로드 하는 방법
```python
import os
from http.client import HTTPConnection
from urllib.parse import urljoin, urlunparse
from urllib.request import urlretrieve
from html.parser import HTMLParser

# HTMLParser를 사용할 때는, HTMLParser를 상속하고, 필요한 메소드를 오버라이드하여 사용함.
class ImageParser(HTMLParser):
    # 이미지 태그를 찾기 위해 handle_starttag를 오버라이드 함
    def handle_starttag(self, tag, attrs):
        if tag != 'img':
            return
        if not hasattr(self, 'result'):
            self.result = []
        for name, value in attrs:
            if name == 'src':
                self.result.append(value)

# HTML 문장을 feed()에 주면, 바로 파싱하고 parser.result 리스트에 추가함.
def download_image(url, data):
    if not os.path.exists('DOWNLOAD'):
        os.makedirs('DOWNLOAD')

    parser = ImageParser()
    parser.feed(data)
    dataSet = set(x for x in parser.result)

    for x in sorted(dataSet):
        # 다운로드 하기위해 baseUrl과 파일명을 합쳐서 완전한 다운로드 주소를 만듦
        imageUrl = urljoin(url, x)
        basename = os.path.basename(imageUrl)
        print('basename:',basename)
        targetFile = os.path.join('DOWNLOAD', basename)

        print('Downloading...', imageUrl)
        # src로부터 파일을 가져와서 targetFile로 생성해 줌.
        urlretrieve(imageUrl, targetFile)

def main():
    host = 'www.google.co.kr'

    conn = HTTPConnection(host)
    conn.request('GET','')
    resp = conn.getresponse()
    # 인코딩 방식을 알아내는 방법
    charset = resp.msg.get_param('charset')
    data = resp.read().decode(charset)
    conn.close()

    print('\n>>>>>> Download Images from ', host)
    url = urlunparse(('http',host,'','','',''))
    download_image(url,data)

if __name__ == '__main__':
    main()
```
