---
sort: 2
---

# 자료형

#### 자료형 확인 : type() 함수   

```python
>>> print(type("안녕하세요?"))
<class 'str'>
```

## [ 1 ] 문자열
#### 1. 큰 따옴표, 작은 따옴표 모두 사용 가능   
#### 2. 이스케이프 문자 사용하여 특수문자 표현 가능
#### 3. 여러줄 문자열
- '\n' 을 사용
- """~""", '''~'''
```python
>>>print("""줄바꿈이 있는
문자열
출력""")
줄바꿈이 있는
문자열
출력
```

#### 4. 줄바꿈 없이 문자열 만들기

```python
>>> print("""
줄바꿈이 있는
문자열
출력
""") # 시작과 끝의 줄바꿈이 발생됨
(줄바꿈 발생)
줄바꿈이 있는
문자열
출력
(줄바꿈 발생)

>>>print("""\
줄바꿈이 있는 
문자열
출력\
""") # 줄바꿈 없이 바로 출력 되어짐
줄바꿈이 있는
문자열
출력
```

#### 5. 문자열의 연산자
1.  \+    
- 문자열의 연결
- 문자열은 무조건 문자열끼리 \+로 연결
```python
>>>print("안녕" + "하세요")
안녕하세요
```
2. \*
- 문자열 * 숫자
- 문자열을 숫자만큼 반복
```python
>>>print('안녕!'*3)
안녕!안녕!안녕!
```
3. [숫자]
- 인덱싱
- 문자선택 연산자   
```python
>>>print("안녕하세요"[0])
안
>>>print("안녕하세요"[-1])
요
```
4. [숫자:숫자]
- 슬라이싱
- 문자열 범위 선택 연산자
- 마지막 숫자의 문자는 포함하지 않음   
```python
>>>print('안녕하세요'[1:3])
녕하
```
5. IndexError 예외 
- 리스트/문자열의 수를 벗어나는 요소/글자를 선택할 때 발생

#### 6. 문자열 길이 구하기 : len("문자열")   
```python
>>>print(len("안녕하세요"))
5
```

## [ 2 ] 숫자

#### 1. 숫자의 종류

1. 정수, 실수   

```python
>>>print(type(52))
<class 'int'>
>>>print(type(52.344))
<class 'float'>
```

#### 2. 숫자 연산자

1. 사칙연산 : +, -, *, /
2. **정수나누기 연산자 : //**
3. 나머지 연산자 : %
4. 제곱 연산자 : **   

```python
>>>print(3/2)
1.5
>>>print(3//2)
1
>>>print(3**3)
27
```

#### 3. TypeError 예외
1. 서로 다른 자료를 연산하면 TypeError 예외 발생

## [ 3 ] 변수와 입력
- KEY: `#변수선언 #변수할당 #변수참조 #input() #int() #float() #str()`
- 파이썬은 변수타입을 쓰지않음
```python
a = '문자열'
```

#### 3-1. 복합 대입 연산자
1. +=, -=, *=, /=, %=, **=
2. 문자열도 사용 가능

#### 3-2. input()

1. 사용자 입력 받는 함수
2. 프롬프트 문자열
3. input() 은 무조건 문자열 타입을 반환   

```python
>>>string = input("문자를 입력하세요:")
문자를 입력하세요:문자
>>>print(type(string))
< class 'str' >
>>>number = input("숫자를 입력하세요 :")
숫자를 입력하세요 : 1234
>>>print(type(number))
< class 'str' >
# 불값도 마찬가지로 문자열을 반환
```

#### 3-3. 문자열을 숫자로 바꾸기

1. int()
2. float()
3. ValueError 예외 : 숫자가 아닌 것을 숫자로 변환 하려고 할 때 발생하는 에러, 실수를 int 형으로 변환하려고 할 때

#### 3-4. 숫자를 문자열로 바꾸기

1. str()

## [ 4 ] 숫자와 문자열의 다양한 함수 및 기능
- KEY: `#format() upper() lower() strip() find() in split()`

#### 4-1. 문자열의 format()

```python
"{}".format(10)
"{}과 {}".format(10, 20)
"{0},{1},{3}".format(101,202,303)
```

1. 정수 출력의 다양한 쓰임
- 정수 :  `output_a = '{:d}'.format(52)`
- 특정 칸에 출력 : `output_b="{:5d}".format(53)` 
- 빈칸을 0으로 채우기 : `output_b="{:05d}".format(52)`
- 양수/음수 부호와 함께 출력 : `output_c="{:+d}".format(-52)` 또는 `output_c="{:+d}".format(52)`
- 부호부분 빈칸으로 비우고 출력 : `output_d="{: d}".format(-52)` 또는 `output_d="{: d}".format(52)`
- 부호와 빈칸을 조합 할 때 = 기호를 앞에 붙일 수 있음
- 다양한 형태로 조합해 보기   

    ```python
    # 조합하기
    output_h = "{:+5d}".format(52) # 기호를 뒤로 밀기 : 양수
    output_i = "{:+5d}".format(-52) # 기호를 뒤로 밀기 : 음수
    output_j = "{:=+5d}".format(52) # 기호를 앞으로 밀기 : 양수
    output_k = "{:=+5d}".format(-52) # 기호를 앞으로 밀기 : 음수
    output_l = "{:+05d}".format(52) # 기호를 붙이고 0으로 채우기 : 양수
    output_m = "{:+05d}".format(-52) # 기호를 붙이고 0으로 채우기 : 음수
    print(output_h)
    print(output_i)
    print(output_j)
    print(output_k)
    print(output_l)
    print(output_m)
    # 출력
    +52
    -52
    +  52
    -  52
    +0052
    -0052
    ```

2. 부동 소수점 출력의 다양한 쓰임   
- float의 기본형
```python
output_a = "{:f}".format(52.273) 
output_b = "{:15f}".format(52.273) # 15칸 만들기
output_c = "{:+15f}".format(52.273) # 15칸에 부호 추가
output_d = "{:+015f}".format(52.273) # 15칸에 부호 추가하고 0으로 채우기
print(output_a)
print(output_b)
print(output_c)
print(output_d)
# 실행결과
52.273000
    52.273000
    +52.273000
+0000052.273000      
```
- 소수점 아래 지정하기 
```python
output_a = "{:15.3f}".format(52.273) # 총 15칸을 차지하고, 소수점 아래 3자리
output_b = "{:15.2f}".format(52.273)
output_c = "{:15.1f}".format(52.273)
print(output_a)
print(output_b)
print(output_c)
# 실행결과
         52.273
          52.27
           52.3
```
- 의미없는 소수점 제거하기 : 소수점 아래 0 제거
```python
output_a = 52.0
output_b = "{:g}".format(output_a)
print(output_a)
print(output_b)
# 실행결과
52.0
52
```

#### 4-2. 대소문자 바꾸기 
- **upper()**
- **lower()**
```python
a = 'Hello'
print(a.upper())
print(a.lower())
# 실행결과
HELLO
hello
```
#### 4-3. 문자열 공백 제거
1. 양옆 공백 제거 : **strip()**
2. 왼쪽 공백 제거 : **lstrip()**
3. 오른쪽공백제거 : **rstrip()**

#### 4-4. 문자열 구성 파악 : isXX()
1. **isalnum()** : 문자열이 숫자와 알파벳으로만 구성되었는지
2. **isalpha()**
3. **isdentifier()** : 문자열이 식별자로 사용 할 수 있는지
4. **isdecimal()**
5. **isdigit()**
6. **isspace()**
7. **islower()**
8. **isupper()**

#### 4-5. 문자열 찾기 : find(), rfind() ( 0부터 시작)
1. **find()** : 왼쪽부터 찾아서 처음 등작하는 위치
2. **rfind()** : 오른쪽부터 찾아서 처음 등장하는 위치  

#### 4-6. 문자열과 in 연산자
1. 문자열 내부에 어떤 문자열이 있는지 확인
2. 결과는 **불**로 반환
```python
print("안녕" in "안녕하세요?")
# 실행 결과
True
```

#### 4-7. 문자열 자르기 : split()
- 결과는 **리스트**로 반환
```python
a = "10 20 30 40 50".split(" ")
print(a)
# 실행 결과
['10', '20', '30', '40', '50']
```