---
sort: 8
---

# 클래스

## 클래스 선언
```python
class 클래스이름:
    클래스 내용
```

## 클래스 객체(인스턴스) 만들기
```python
인스턴스 변수이름 = 클래스이름()
```

## 생성자
- 클래스 내부에 \_\_init\_\_함수 만들어 처리

```python
class 클래스이름:
    def __init__(self, 추가 매개변수):
        pass
```

## 메소드
```python
class 클래스이름:
    def 메소드 이름(self, 추가 매개변수):
        pass
```

## isinstance(인스턴스, 클래스)
- 어떤 클래스의 인스턴스인지 확인

```python
class Student:
    def __init__(self):
        pass
student = Student()
# 인스턴스 확인하기
print(isinstance(student, Student))
# 출력 결과
True
```

## 특수한 이름의 메소드
- 클래스를 사용할 때 제공해 주는 보조기능

### [ 1 ] \_\_str\_\_() 함수
- str() 함수를 호출 할 때, \_\_str\_\_() 함수가 실행됨
- 객체를 대표하는 문자열을 리턴함

### [ 2 ] 크기비교함수
- \_\_eq\_\_(), \_\_ne\_\_(), \_\_gt\_\_(), \_\_ge\_\_(), \_\_lt\_\_(), \_\_le\_\_()
- 클래스 구문 내에서 오버라이드 하여 사용할 수 있음

## 클래스 변수와 메소드

### [ 1 ] 클래스 변수 

#### 1-1. 만들기
- 선언

```python
class 클래스이름:
    변수이름 = 값
```

- 호출

```python
클래스이름.변수이름
```

#### 1-2. 예제
```python
# 클래스 선언
class Student:
    # 클래스 변수 선언
    count = 0
    # 생성자
    def __init__(self, name, korean, math, english, science):
        # 인스턴스 변수 초기화
        self.name = name
        self.korean = korean
        self.math = math
        self.english = english
        self.science = science
        # 클래스 변수 설정
        Student.count += 1
        print('{}번째 학생이 생성되었습니다.'.format(Student.count))
# 생략        
```

### [ 2 ] 클래스 함수

#### 2-1. 클래스 함수 만들기
- 선언 : 클래스 함수의 첫번째 매개변수로 특이하게 클래스 자체가 들어오며, 임의의 별칭으로 cls를 사용하였음

```python
class 클래스 이름:
    @classmethod
    def 클래스 함수(cls, 매개변수):
        pass
```

- 호출

```python
클래스 이름.함수 이름(매개변수)
```

### [ 3 ] 프라이빗 변수
- 클래스 내에서만 사용하려고 선언하는 변수로, `\_\_변수이름` 형태로 선언함.
- 외부에서 프라이빗 변수에 접근하려고 하면 AttributeError 발생함.

```python
import math

class Circle:
    def __init__(self, radius):
        self.__radius = radius
    def get_circumference(self):
        pass
    def get_area(self):
        pass
```

### [ 4 ] 게터/세터

#### 4-1. 기본 게터/세터
- 프라이빗 변수에 접근하기 위해 간접적으로 접근할 수 있도록 해주는 함수

```python
import math

class Circle:
    def __init__(self, radius):
        self.__radius = radius
    def get_circumference(self):
        pass
    def get_area(self):
        pass
    # 게터
    def get_radius(self):
        return self.__radius
    # 세터
    def set_radius(self, value):
        self.__radius = value
```

#### 4-2. 데코레이터를 사용한 게터와 세터

```python
import math

class Circle:
    def __init__(self, radius):
        self.__radius = radius
    def get_circumference(self):
        pass
    def get_area(self):
        pass
    # 게터
    @property
    def radius(self):
        return self.__radius
    # 세터
    @radius.setter
    def radius(self, value):
        self.__radius = value
```

## [ 5 ] 상속

### 5-1. 상속
- 다른 누군가 만들어 놓은 기본형태에 내가 원하는 것만 교체하는 것이 '상속'.

#### 1. 상속의 기본형태

```python
# 부모
class Parent:
    def __init__(self):
        self.value = '테스트'
        print('parent의 init()이 호출됨')
    def test(self):
        print('parent의 test()가 호출됨')
# 자식
class Child(Parent):
    def __init__(self):
        Parent.__init__(self)
        print('child 클래스의 init()이 호출됨')
# 자식 클래스 인스턴스 생성하고 부모의 메소드 호출
child = Child()
child.test()
print(child.value)
# 실행 결과
parent의 init()이 호출됨
child의 init()이 호출됨
parent의 test()가 호출됨
테스트
```

#### 2. 상속을 활용하여 예외 클래스 생성하기
- Exception 클래스를 상속하여 CustomException 클래스를 생성해 보자

```python
class CustomException(Exception):
    def __init__(self):
        Exception.__init__(self)
# 생성한 CustomException을 강제 발생 시켜보자
raise CustomException
# 실행 결과
Traceback...(생략)
__Main__.CustomException
```

#### 3. 자식 클래스에서 부모의 함수를 오버라이드(재정의) 해보기

```python
class CustomException(Exception):
    def __init__(self):
        Exception.__init__(self)
        print('######## 내가 만든 오류가 생성됨 ########')
    def __str__(self):
        return '오류가 발생했어요'
raise CustomException
# 실행 결과
######## 내가 만든 오류가 생성됨 ########
Traceback....
(생략)
__Main__.CustomException: 오류가 발생했어요
```

#### 4. 부모 클래스 확장하기 ( 자식클래스에서 부모에 없는 함수를 정의하기 )

```python
class CustomException(Exception):
    def __init__(self, message, value):
        Exception.__init__(self)
        self.message = message
        self.value = value
    def __str__(self):
        return self.message
    def print(self):
        print('####오류 발견####')
        print('메시지:',self.message)
        print('값:', self.value)
# 예외 발생
try:
    raise CustomException('테스트', 273)
except CustomException as e:
    e.print()
```

### 5-2. 다중상속
- 다른 누군가가 만들어 놓은 형태들을 조립해서 내가 원하는 것을 만드는 것이 '다중상속'

