---
sort: 3
---

# 조건문

## [ 1 ] if

#### 1-1. if 문법
```python
if 조건식:
    참 일때, 실행문
```
#### 1-2. 비교연산자
- ==, !=, <, >,<=, >=
-  <font color='blue'>범위구하기</font>: 파이썬은 다음과 같은 연산을 허용함
```python
x = 25
print(10 < x < 30)
# 실행 결과
True
```

#### 1-3. 논리연산자
- not, and, or

## [ 2 ] if~else, elif

#### 2-1. if~else 구문
```python
if 조건문 :
    조건문이 참일 때 실행문
else :
    조건문이 거짓일 때 실행문
```

#### 2-2. elif 구문
```python
if 조건문 A :
    A가 참일 때 실행문
elif 조건문 B :
    B가 참일 때 실행문
elif 조건문 C :
    C가 참일 때 실행문
else :
    모든 조건에 해당하지 않을 때 실행문
```

#### 2-3. False로 변환되는 값들
- None, 숫자 0 과 0.0, 빈 컨테이너 (빈 문자열, 빈 바이트열, 빈 리스트, 빈 튜플, 빈 딕셔너리 등)

#### 2-4. pass 키워드
1. 파이썬은 java 처럼 조건문의 실행문 부분을 '미구현'으로 할 수 없음
2. 조건문의 실행문을 '미구현'상태로 두고 싶을 때 **pass** 키워드 사용

#### 2-5. raise NotImplementedError
- 미구현 상태로 두었을 때, pass 키워드를 사용하지 않고 상태를 인식할 수 있도록 강제로 error를 발생시킬 수 있음
```python
if 조건문 :
    # 미구현 상태
    raise NotImplementedError
```

