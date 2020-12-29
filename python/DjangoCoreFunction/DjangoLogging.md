---
sort: 6
---

# 로그 남기기
- 파이썬 로깅 모듈 4가지 
    + 로거
    + 핸들러
    + 필터
    + 포맷터
- <font color='red'>로깅 관련 설정 파일</font>
    + 장고 디폴트 로그 설정 : [장고 설치 홈]/site-package/utils/log.py
    + 사용자 로그 설정 : settings.py의 LOGGING_CONFIG, LOGGING 항목 
- 로깅 주요 컴포넌트 관계

```
{%raw%}
    [Logger]------------>[Handler]<--------------[Fomatter]
             /   |  \
            /    |___\__>[Handler]<--------------[Fomatter]
           /          \
          /            \
   ---------------------------------
   | [Filter]-->[Filter]-->[Filter] |    
   ---------------------------------       
{%endraw%}
```
+ 한개의 Logger에 여러 개의 Handler 지정 가능
+ 필터 체인 가능, 필터는 Logger 나 Handler 양쪽에 적용 가능

## 1. 로거
- 로거 : 로그 메시지를 처리하기 위해 메시지를 담아두는 저장소
- 로거 레벨

    |로그 레벨|정수값|설명|
    |:--------|:-----|:---|
    |NOTSET|0|로그레벨 최하위 수준<br>별도의 로거 또는 핸들러가 없을 때 디폴트 로그 레벨|
    |DEBUG|10|디버그 용도|
    |INFO|20|일반적인 정보|
    |WARNING|30|덜 중요한 문제점 발생시 문제에 대한 정보|
    |ERROR|40|주요 문제점 발생시 문제에 대한 정보|
    |CRITICAL|50|치명적인 문제점 발생시 문제에 대한 정보|

- 로그 레코드 : 로거에 저장되는 메시지, 레코드도 로그레벨을 가짐
- '로그 레코드의 로그 레벨 >= 로거의 로그 레벨' 일 때, 메시지 처리가 진행되며 그 반대는 무시됨

## 2. 핸들러
- 핸들러
    + 메시지에 무슨 작업을 할지 결정하는 엔진
    + 메시지를 화면이나 콘솔, 파일, 네트워크 소켓등 어디에 기록할 것인지 같은 로그 동작을 정의함
    + 핸들러도 로그 레벨을 가지고 있음
    + 로그 레코드의 로그레벨 >= 로그 핸들러의 로그레벨 일 때, 메시지 처리가 진행되며 그 반대는 무시됨
    + 각 핸들러는 서로 다른 로그레벨을 가질 수 있음
        - (예시) : ERROR 또는 CRITICAL 메시지는 표준 출력하는 핸들러, 모든 메시지를 파일에 기록하는 또 다른 핸들러

## 3. 필터
- 로그레코드가 로거에서 핸들러로 넘겨 질때 필터를 이용해 레코드에 추가적인 제어를 할 수 있음
    + 예를 들어,
    + 필터를 추가하여, 특정 소스로 부터 오는 메시지만 핸들러로 넘기거나,
    _ 어떤 조건을 추가하여 조건에 맞으면, ERROR 로그를 WARNNING 로그로 낮추는 등

## 4. 포맷터
- 출력하는 텍스트의 형식

## 5. 로그의 설정

### (1) 별도의 설정을 하지 않았을 때, django의 기본설정을 따름
- [django_home]/site-packages/utils/log.py

```python
DEFAULT_LOGGING = {
    'version': 1, # dicConfig 버전1 형식
    'disable_existing_loggers': False, # 기존의 로거들을 비활성화 할것인가?, jango는 False를 권장
    'filters': {
        'require_debug_false': { # 디버그가 False인 경우만 핸들러를 동작함
            '()': 'django.utils.log.RequireDebugFalse', # ()는, 필터 객체 생성 클래스로 장고에서 생성한 클래스
        },
        'require_debug_true': { # 디버그가 True인 경우만 핸들러를 동작함
            '()': 'django.utils.log.RequireDebugTrue', # ()는 필터 객체 생성 클래스
        },
    },
    'formatters': {
        'django.server': { # 로그 생성시간과 메시지만을 출력
            '()': 'django.utils.log.ServerFormatter',
            'format': '[{server_time}] {message}',
            'style': '{',
        }
    },
    'handlers': {
        'console': { # INFO 이상의 메시지를 표준에러로 출력해주는 StreamHandler 클래스를 사용함.
            'level': 'INFO',
            'filters': ['require_debug_true'],
            'class': 'logging.StreamHandler',
        },
        'django.server': { 
            'level': 'INFO',
            'class': 'logging.StreamHandler',
            'formatter': 'django.server',
        },
        'mail_admins': {# ERROR 그 이상의 메시지를 관리자 메일로 보내주는 AdminEmailHandler 클래스를 생성
            'level': 'ERROR',
            'filters': ['require_debug_false'],
            'class': 'django.utils.log.AdminEmailHandler'
        }
    },
    'loggers': {
        'django': { # django.* 계층, 즉  최상위 로거
            'handlers': ['console', 'mail_admins'],
            'level': 'INFO',
        },
        'django.server': { # 상위로거로 메시지를 전파하지 않음. 개발용 웹서버인 runserver에서 사용하는 로거. 5XX는 Error로, 4XX는 WARNNING 으로 메시지 출력
            'handlers': ['django.server'],
            'level': 'INFO',
            'propagate': False,
        },
    }
}
```

### (2) 사용자 정의 로거
- mysite/settings.py
- LOGGING 항목에 새로운 로거를 추가하거나, django 디폴트 로거들을 오버라이딩하는 것도 가능

```python
LOGGING_CONFIG = 'logging.config.dictConfig'    # 설정하지 않아도 디폴트가 dictConfig이므로 생략 가능, 로깅 설정에 사용하는 함수를 지정
LOGGING = { # 개발자가 로깅을 설정할때 LOGGING 항목을 사용
    'version':1,
    'disable_existing_loggers':False,
    'formatters':{
        'verbose':{ # verbose 포맷터 정의
            'format':"[%(asctime)s] %(levelname)s %[%(name)s:%(lineno)s] %(message)s", # [로그메시지 기록시간], 로그레벨 이름, [로거이름:라인번호],로그메시지 순으로 출력 , 
            'datefmt':"%d/%b/%Y %H:%M:%S" # 시간의 포맷은 날짜/월축약형/연도 시(24시기준)/분/초 형식으로 출력
        },
    },
    'handlers':{
        'file': {
            'level':'DEBUG',
            'class':'logging.FileHandler',
            'filename':os.path.join(BASE_DIR,'logs','mysite.log'), # root디렉토리/logs/mysite.log 파일로 출력
            'formatter':'verbose'
        },
        'loggers':{
            'django':{ # 디폴트로 설정되어 있는 로거인데, 오버라이딩 하고 있음
                'handlers':['file'],
                'level': 'DEBUG',
            },
            'mysite':{ 
                'handlers':['file'],
                'level':'DEBUG',
            }
        }
    }
}
```

### 3. django 디폴트 로깅설정 무시하는 설정

```python
# settings.py

LOGGING_CONFIG = None
LOGGING = {
    # 내가 원하는 로깅 컴포넌트를 설정함
}

import logging.config
logging.config.dictConfig(LOGGING)
```

### 4. 설정한 로거를 소스에서 사용하는 방법

```python
# some_app/views.py
import logging

logger = logging.getLogger('mylogger')

def my_view(request, arg1, arg):
    # 로직
    if bad_mojo:
        # ERROR 레벨의 로그 레코드 생성함
        logger.error('Something went wrong!')
```
- logging.getLogger() :  관행적으로 **\_\_name\_\_** (해당 파일 이름) 구문을 사용, '' 로 설정하면 최상위 루트 로거가 됨
    + 만일 ch3/polls/views 파일이라면, **\_\_name\_\_**은 polls.views 가 됨
    + 로깅 호출은, 부모에게서 전파되므로 최상위 루트 로거에서 핸들러 하나만 만들어도 하위 로거의 모든 로깅 호출함
    + 예시 : project 이름공간에 정의된 로그 핸들러는 project.interesting 로거 및 project.interesting.stuff 로거가 보내주는 모든 로그 메시지를 잡음
        ```python
        # 로거 이름으로 계층화
        logger = logging.getLogger('project.interesting.stuff')
        ```

- 로깅 메소드 : 로거 객체는 로그 레벨별로 로그 호출 메소드를 갖고 있음
    + logger.debug()
    + logger.info()
    + logger.warning()
    + logger.error()
    + logger.critical()

    + logger.log()
    + logger.exception() : 익셉션 스택 트레이스 정보를 포함하는 ERROR 레벨의 로그 메시지 생성