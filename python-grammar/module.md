---
sort: 7
---

# 모듈

## 표준 모듈
- 모듈가져오기 : `import 모듈이름`
- 모듈로부터 특정 함수나 변수 가지고 오기 : `from 모듈 이름 import 함수 또는 변수`
- 모듈의 별칭을 만들 때 : `import 모듈이름 as 별칭이름`
- 주의 : 파일을 저장할 때 이미 존재하는 모듈명과 중복되는 이름을 사용하면 안되므로, 모듈은 아래와 같이 사용하는 것이 좋다.
    - `from random import random, randrange, choice`
- 파이썬 라이브러리 문서 : <https://docs.python.org/3/library/index.html>

### [ 1 ] math 모듈
- math 모듈의 함수

|변수 또는 함수 | 설명 |
|---------------|------|
|sin(x) |사인 |
|cos(x) |코사인 |
|tan(x) |탄젠트 |
|log(x[,base]) |로그 |
|ceil(x) |올림 |
|floor(x) |내림 |

- 참고: round() 함수는 반올림 함수로, 정수부분이 홀수일 때 소수점 5면 올리고, 짝수일 때 소수점 5면 내림. 이런 이유로 잘 사용하지 않으니 반올림 할 때 사용하지 말기를!

### [ 2 ] random 모듈
- 참고 : <https://docs.python.org/3/library/random.html#examples-and-recipes>

#### 2-1. random.random() : 0.0<= x < 1.0 사이의 float 리턴
#### 2-2. random.uniform(min, max) : 지정한 범위 사이의 float 리턴
#### 2-3. random.randrange() : 지정한 범위의 int 리턴
- `random.randrange(max)` : 0 ~ max 사이의 int 리턴
- `random.randrange(min, max)` : min ~ max 사이의 int 리턴 

#### 2-4. random.choice(list) : 리스트 내부 요소중에 랜덤하게 선택
#### 2-5. random.shuffle(list) : 리스트 내부 요소를 랜덤하게 섞음
#### 2-6. random.sample(list, k=숫자) : 리스트 요소중 k개를 랜덤하게 뽑음

### [ 3 ] sys 모듈
- 시스템과 관계되는 모듈, 명령 매개변수를 받을 때 많이 사용함
- `import sys`

#### 3-1. sys.argv : 명령 매개변수 리턴
- 만약 명령프롬프트에 `>python module_sys.py 10 20 30` 명령을 실행 했을 때, module_sys.py 파일내의 sys.argv가 실행된다면 ['module_sys.py','10','20','30']을 리턴할 것이다.

#### 3-2. 컴퓨터 환경과 관련된 정보 리턴
- `sys.getwindowsversion()`
- `sys.copyright`
- `sys.version`

#### 3-3. sys.exit() : 프로그램 강제 종료

### [ 4 ] os 모듈
- `import os`

#### 4-1. os.name : 현재 운영체제
#### 4-2. os.getcwd() : 현재 폴더
#### 4-3. os.listdir() : 현재 폴더 내부의 요소
#### 4-4. os.mkdir('폴더이름') : 폴더 생성
#### 4-5. os.rmdir('폴더이름') : 폴더가 비어 있을 때만, 제거가능
#### 4-6. os.rename('원래이름','새이름') : 파일이름 변경
#### 4-7. os.remove('파일이름') 또는 os.unlink('파일이름') : 파일제거
#### 4-8. os.system('dir') : 시스템 명령어 실행

### [ 5 ] datetime 모듈
- `import datetime`

#### 5-1. 현재시간 출력하기 
- `now = datetime.datetime.now()`
- 년 : `now.year`
- 월 : `now.month`
- 일 : `now.day`
- 시 : `now.hour`
- 분 : `now.minute`
- 초 : `now.second`

#### 5-2. 시간 출력 포맷
1. `now.strftime("%Y.%m.%d %H:%M:%S")` : 2021.01.01 01:30:30
2. `"{}년 {}월 {}일 {}시 {}분 {}초".format(now.year, now.month, now.day, now.hour, now.minute, now.second)` : 2021년 1월 1일 1시 30분 30초
3. `now.strftime("%Y{} %m{} %d{} %H{} %M{} %S{}").format(*"년월일시분초")` : 2021년 01월 01일 01시 30분 30초, format의 매개변수 앞에 * 을 붙이면, 요소 하나하나가 매개변수로 지정됨.
    - strftime()은 한국어를 지원하지 않으므로 2, 3번의 방식을 사용

#### 5-3. 특정시간 이후의 시간 구하기
1. `datetime.timedelta()` : 시간 더하기, 사용하기가 까다로워 잘 사용하지 않음.
```python
now = datetime.datetime.now()
## 특정시간 이후의 시간
after = now + datetime.timedelta(\
    weeks=1,\
    days=1,\
    hours=1,\
    minutes=1,\
    seconds=1)
```

#### 5-4. 특정시간 요소 교체하기
1. `now.replace(year=(now.year + 1)` : 1년 더하기

### [ 6 ] time 모듈
- `import time`
- Unix 타임(1970.01.01.0:0:0 기준으로 계산)을 구할 때, 특정 시간동안 코드 진행을 정지하거나, 실행시간을 측정할 때 사용함
- `time.sleep(5)` : 5초동안 정지 (자주 사용하는 함수이므로, 외워두면 좋다.)

### [ 7 ] urllib 모듈
- url을 다루는 라이브러리

#### 7-1. request
```python
from urllib import request
# urlopen()으로 구글페이지 열기
target = request.urlopen('https://google.com')
output = target.read()
# 출력
print(output)
```

## 외부 모듈
- 파이썬이 기본 제공하는 라이브러리가 아닌, 다른 사람들이 만들어 배포하는 모듈
- 외부 모듈의 예 
    - BeautifulSoup, Flask
    - 웹 개발 : django, flask
    - 기계학습 : scikit-learn, keras, tensorflow
    - 스크레이핑 : requests, Beautiful Soup, scrapy
    - 영상분석 : cv2, pillow
    - 데이터 분석 : pandas, matplotlib

### [ 1 ] 사용법

#### 1. 모듈 설치하기
- 명령 프롬프트 창에서 `pip install 모듈 이름` 으로 설치
- pip 설명 참고 : <https://pip.pypa.io/en/stable/user_quide/#installing-packages>

#### 2. 모듈 찾아보기
1. 책에서 추천받기
2. 파이썬 커뮤니티의 인기 라이브러리 추천받기
3. 구글 검색하기

### [ 2 ] BeautifulSoup 모듈로 날씨 가져오기 예시
- 스크래이핑 관련 라이브러리
- BeautifulSoup 모듈 문서 : <https://www.crummy.com/software/BeautifulSoup/bs4/doc/>
- 기상청 날씨 정보 : <http://www.kma.go.kr/weather/lifenindustry/service_rss.jsp>
```python
# 모듈 읽어들이기
from urllib import request
from bs4 import BeautifulSoup
# urlopen()으로 기상청 날씨 읽기
target = request.urlopen('http://www.kma.go.kr/weather/forecast/mid-term-rss3.jsp?stnId=108')
# BeautifulSoup 사용해 웹 페이지 분석
soup = BeautifulSoup(target, 'html.parser') # 'html.parser'를 매개변수로 넣으면 BeautifulSoup 이라는 특수한 객체를 리턴함
# location 태그 찾기
for location in soup.select('location'):
    # 내부의 city, wf, tmn, tmx 태그 찾아 출력
    print('도시:',location.select_one('cidy').string)
    print('날씨:',location.select_one('wf').string)
    print('최저기온:',location.select_one('tmn').string)
    print('최고기온:',location.select_one('tmx').string)
    print()
```

### [ 3 ] Flask 모듈 사용해 보기
- 웹개발 분야에서, 작은 기능만을 제공하는 웹 개발 프레임워크
- `pip install flask`

#### 3-1. 예제 : flask_basic.py
```python
from flask import Flask
app = Flask(__name__)
@app.route("/")
def hello():
    return "<h1>Hello World!</h1>"
```

#### 3-2. Flask 코드를 실행하는 방법 (특이함)
- 명령 프롬프트에서 아래의 명령어로 실행 (window 환경)
```bash
set FLASK_APP=파일이름.py
flask run
```
    - 'FLASK_APP=..'에서 = 앞뒤로 뛰어쓰기하면 에러남
    - 파일이 위치하는 디렉토리에서 명령을 실행해야 함
    - 리눅스환경에서는 'set' 명령어 대신 'export'로 바꾸어서 실행
- 실행결과
    ```
    > set FLASK_APP=flask_basic.py
    > flask run
        * Serving Flask app 'flask_basic.py'
        * Running on http://127.0.0.1:5000/ (press CTRL+C quit)
    ```

### [ 4 ] 함수 데코레이터
- @로 시작하는 구문
- 함수 데코레이터, 클래스 데코레이터 있음
- 반복적인 내용이 필요할 때, 데코레이터로 만들어 사용하면 편리함

#### 4-1. 예제
- hello() 함수를 실행하면, 실행 전에 '인사 시작', 실행 후에 '인사 끝' 을 출력하는 데코레이터 만들어 보기
- func_deco.py
```python
# 데코레이터 생성
def test(function):
    def wrapper():
        print('인사시작')
        function()
        print('인사 끝')
    return wrapper
# 데코레이터 붙인 함수 생성
@test
def hello():
    print('hello')
# 함수 호출
hello()
# 실행 결과
인사시작
hello
인사 끝
```

## 모듈 만들기
1. 일반적인 *.py 파일을 만든 후, 그안에 변수와 함수등을 만든다.
2. 다른 파일에서 (1)에서 만든 파일을 import 하여 사용한다.

- 모듈 : test_module.py

```python
PI = 3.14
def number_input():
    output = ....(생략)
    return float(output)
def get_circumference(radius):
    ...(생략)..
    return 2 * PI * radus
def get_circle_area(radius):
    return PI * radius * radius
```

- import 하여 사용 : main.py

```python
import test_module as test
radius = test.number_input()
.....import 한 test_module 내부의 함수를 사용
```

## \_\_name\_\_=="\__main\_\_"

### [ 1 ] \_\_name\_\_
- 프로그램 진입점, **엔트리 포인트** 또는 **메인**
- 엔트리 포인트 내부의 __name__은 __main__이다.

### [ 2 ] 모듈의 \_\_name\_\_
- 모듈 내부에서의 __name__을 출력하면, 모듈의 이름이 나옴
- main.py

```python
import test_module
print('메인의 __name__ 출력')
print(__name__)
print()
```
- test_module.py

```python
print('모듈의 __name__ 출력')
print(__name__)
```

- main.py 파일을 실행 시, 결과
    ```python
    모듈의 __name__출력
    test_module
    메인의 __name__출력
    main
    ```
    - main.py에서 import 한 파일의 실행코드 먼저 실행하여 모듈의 __name__이 먼저 출력됨

### [ 3 ] __name__의 활용
- 현재 파일이 모듈로 실행되었는지, 엔트리포인트로 실행되었는지 확인 할 때 사용
- 엔트리포인트에서만 모듈이 실행되도록, 모듈 파일에 아래의 추가구문을 삽입하여 실행문을 작성함

- test_module.py

```python
# 여러가지 모듈의 함수 및 변수
if __name__=='__main__' :
    엔트리포인트에서 실행될 코드
```

### [ 4 ] 패키지
- 모듈이 모두 모여 구조를 이루면 패키지가 됨.
- 관련 모듈을 특정 디렉토리 아래에 모아 놓음

### [ 5 ] \_\_init\_\_.py
- 패키지 내의 모듈을 한꺼번에 가져오고 싶을 때 패키지 폴더 내부에 \_\_init\_\_.py 파일을 만들어 사용
- 패키지를 읽어 들일 때, \_\_init\_\_.py 파일을 가장 먼저 실행됨 -> 패키지 관련 초기화 처리에 적합
- \_\_init\_\_.py 파일에 <font color='red'>__all__</font>이라는 이름의 **리스트**를 만들어, 이 <font color='red'>리스트에 지정한 모듈들이 from 패키지이름 import * 할 때 전부 읽혀들여짐</font>

## 패키지 만들어 보기
- module_package/test_package/module_a.py

```python
variable_a = 'a 모듈의 변수'
```

- module_package/test_package/module_b.py

```python
variable_b = 'b 모듈의 변수'
```

- module_package/test_package/\_\_init\_\_.py

```python
__all__=['module_a','module_b'] # * 사용시 읽어들일 모듈 목록
# 패키지 읽어들일 때 처리를 작성 할 수 있음
print('test_package를 읽어들입니다.')
```

- module_package/test_package/main.py

```python
from test_package import *
print(module_a.variable_a)
print(module_b.variable_b)
```

- 실행 결과

```
test_package를 읽어 들였습니다.
a 모듈의 변수
b 모듈의 변수
```

## 바이너리 데이터 다루기
- 이미지나 동영상
- 파이썬은 바이너리를 무조건 ASCII 코드표로 인코딩해서 출력
- 인터넷의 이미지 저장하기

```python
from urllib import request
# 이미지 읽어들이기
target = request.urlopen("http://www.hanbit.co.kr/images/common/log_hanbit.png")
output = target.read()
print(output)
# 출력결과 : 바이너리 파일은 문단의 가장앞에 b 가 붙음
b` ....(생략)
# 이미지(바이너리 파일)를 쓰기
file = open('output.png', 'wb') # 읽기모드에 꼭 b 를 붙여야 함
file.write(output)
file.close()
```

