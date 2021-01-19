---
sort: 6
---

# 예외처리

## [ 1 ] 오류와 예외

### 1-1. 오류
#### 1. 구문오류
- 실행 전에 발생하는 오류
#### 2. 예외 또는 런타임 오류
- 실행 중에 발생하는 오류

## [ 2 ] 예외 처리
### 2-1. 기본 예외처리 : 조건문을 사용한 예외처리
- if~else 문을 활용하여 else 문에 예외상황을 처리한다.

### 2-2. try except 구문
#### 1. 문법

```python
try:
    예외 발생 가능한 코드
except:
    예외 발생시 실행될 코드
```

#### 2. 예제

```python
# try except 구문으로 예외 처리
try:
    number_input = int(input('정수 입력>')) # 예외 발생 가능성 있는 구문
    # 출력
    print('원지름:', number_input)
    print('원둘레:', 2 * 3.14 * number_input)
    print('원넓이:', 3.14 * number_input * number_input)
except:
    print('예외가 발생하였습니다.') # 예외 처리
```

#### 3. try except 구문과 pass 키워드의 조합
- 해당코드가 중요하지 않아, 예외가 발생해도 그냥 넘어가게 하려면 except 절에 pass 키워드를 사용한다.
```python
try: 
    예외 발생 가능성이 있는 코드
except:
    pass
```

### 2-3. try except else 구문
#### 1. 문법

```python
try:
    예외가 발생 할 수 있는 코드
except:
    예외 발생 했을때 실행 코드
else:
    예외 발생하지 않았을 때 실행 코드
```

### 2-4. finally 구문

#### 1. 문법

```python
try:
    예외가 발생 할 수 있는 코드
except:
    예외 발생 했을때 실행 코드
else:
    예외 발생하지 않았을 때 실행 코드
finally:
    무조건 실행
```
#### 2. 사용하는 경우
1. try 구문 내부에서 return 처리 했을 경우도, finally 구문은 무조건 실행됨
2. 파일이나 스트림을 열었을 때, finally 에서 무조건 close() 해주어야 함
3. try 구문 내부의 반복문 실행중 break 시에도, finally 구문 무조건 실행

## [ 3 ] 고급 예외 처리
### 3-1. 예외 객체
#### 1. 문법

```python
try:
    예외 발생 가능한 구문
except 예외 종류 as 예외 객체를 활용할 변수 이름:
    예외 발생 시 실행 구문
```

#### 2. Exception
-  'Exception'은, 모든 예외의 부모 클래스
```python
try:
    예외 발생 가능한 구문
except Exception as e :
    예외 처리
```

### 3-2. 예외 구분하기
- 여러가지 예외가 발생할 수 있는 상황에서 예외 구분하여 처리하기

#### 1. 문법
```python
try:
    예외 발생 가능한 구문
except 예외종류 A :
    예외 A 처리
except 예외종류 B :
    예외 B 처리
except 예외종류 C :
    예외 C 처리
```

#### 2. 예제
```python
# 변수 선언
list = [52, 273, 32, 72, 100]
try:
    number = int(input('정수입력>'))
    print("{}번째 요소: {}".format(number, list[number]))
except ValueError as e :
    print('정수를 입력하세요.')
    print('exception:',e)
except IndexError as e :
    print('리스트 인덱스를 벗어났어요!')
    print('exception:',e)
```

### 3-3. 모든 예외 잡기 (Exception 클래스 활용)
#### 1. 예제
```python
# 변수 선언
list = [52, 273, 32, 72, 100]
try:
    number = int(input('정수입력>'))
    print("{}번째 요소: {}".format(number, list[number]))
except ValueError as e :
    print('정수를 입력하세요.')
    print('exception:',e)
except IndexError as e :
    print('리스트 인덱스를 벗어났어요!')
    print('exception:',e)
except Exception as e :
    print('미리 파악하지 못한 예외가 있습니다.")
    print('exception:', e)
```
- 마지막 except 구문에서 Exception 객체를 사용하여 모든 예외를 잡는다.

### 3-4. raise 구문
- 일부러 예외를 발생 시키기 위한 구문
#### 1. 문법
`raise 예외 객체`
