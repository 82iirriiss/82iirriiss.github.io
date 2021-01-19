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

## 외부 모듈

## 모듈 만들기
