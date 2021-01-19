---
sort: 5
---

# 함수

## [ 1 ] 가변 매개변수
### 1-1. 문법   
```python
def 함수이름(매개변수, 매개변수, *가변매개변수) :
        # 실행문
```
1. 가변 매개변수뒤에 일반 매개변수가 올 수 없다.
2. 가변 매개변수는 하나만 사용 가능하다.

### 1-2. 예제
```python
def print_n_times(n, *values):
    # n번 반복
    for i in range(n):
        # 가변매개변수는 리스트처럼 활용
        for value in values:
            print(value)
        # 줄바꿈
        print()
# 함수 호출
print_n_times(3, '안녕하세요','즐거운','파이썬')
# 실행 결과
안녕하세요
즐거운
파이썬
# 줄바꿈    
안녕하세요
즐거운
파이썬
# 줄바꿈
안녕하세요
즐거운
파이썬
# 줄바꿈
```

## [ 2 ] 기본 매개변수
- 매개변수를 입력하지 않았을 때 기본적으로 들어가는 기본값

### 2-1. 문법
```python
def 함수 이름(매개변수, 기본매개변수이름1=값1, 기본매개변수2=값2):
    # 실행문
```
1. 기본 매개변수 뒤에는 일반 매개변수가 올 수 없다.

### 2-2. 예제
```python
def print_n_times(value, n = 2) :
    # n번 반복
    for i in range(n):
        print(value)
# 함수 호출
print_n_times('안녕하세요.')
# 실행 결과
안녕하세요.
안녕하세요.
```

## [ 3 ] 키워드 매개변수
- 가변 매개변수와 기본 매개변수를 함께 사용 할 수 있도록 만든 기능
- 함수를 호출할 때, 기본 매개변수는 이름과 값을 함께 지정하는 방법

### 3-1. 예제
```python
def print_n_times (*values, n=2):
    for i in range(n):
        for value in values :
            print(value)
        print()
# 함수 호출 : 기본함수 n에 값을 지정하여 호출
print_n_times('안녕하세요','즐거운','파이썬', n = 3)
# 실행 결과
안녕하세요
즐거운
파이썬
# 줄바꿈
안녕하세요
즐거운
파이썬
# 줄바꿈
안녕하세요
즐거운
파이썬
# 줄바꿈
```

## [ 4 ] 튜플
- 리스트와 유사하지만, 요소를 변경할 때 차이가 있다.

### 4-1. 튜플의 선언
- `(데이터, 데이터, 데이터,...)`
- 요소를 하나만 가지는 튜플은? `(데이터, )`

### 4-2. 튜플의 출력
- `튜플이름[인덱스]`

### 4-3. 튜플의 사용
#### 1. 리스트와 튜플의 특이한 사용
```python
# 리스트와 튜플의 특이한 사용
[a, b] = [10, 20]
(c, d) = (10, 20)
print("a:",a) # a:10
print("b:",b) # b:20
print("c:",c) # c:10
print("d:",d) # d:20
```

#### 2. 괄호가 없는 튜플
- 튜플은 괄호를 생략해도 되는 상황이면, 괄호를 생략해도 된다.

```python
# 괄호가 없는 튜플
tuple = 10, 20, 30, 40
print(tuple)
# 실행 결과
(10, 20, 30, 40)
print(type(tuple))
# 실행 결과
< class tuple >
####################
# 괄호가 없는 튜플의 활용
a, b, c = 10, 20, 30
print(a)
print(b)
print(c)
# 실행 결과
10
20
30
```

#### 3. 변수의 값을 교환하는 튜플
- temp 를 이용하지 않고, 한번에 교환할 수 있다.
```python
a, b = 10, 20
# 값을 교환
a, b = b, a
print('a:',a)
print('b:',b)
# 실행 결과
a: 20
b: 10
```

### 4-4. 튜플과 함수
- <font color='red'>튜플은 함수의 리턴에 많이 사용, 여러개의 값을 리턴.</font>
- 튜플을 사용하는 내장함수의 예 : enumerate(), items(), divmod()

#### 1. 예제
```python
def test():
    return (10, 20)
a, b = test()
print(a)
print(b)
# 실행 결과
10
20
```

## [ 5 ] 람다
 - 람다는, 함수를 매개로 전달하는 코드를 더 효율적으로 작성 할 수 있다.

### 5-1. 함수의 매개변수로 함수 전달하기
```python
# 매개변수로 받은 함수를 5번 호출하는 함수
def call_5_times(func):
    for i in range(5):
    func()
# 매개로 전달할 함수
def print_hello():
    print('Hello!')
# 조합하여 호출하기
call_5_times(print_hello)
# 실행 결과
Hello!
Hello!
Hello!
Hello!
Hello!
```

### 5-2. filter() 와 map() 함수
- 함수를 매개변수로 전달하는 대표함수

#### 1. map()
- 문법 : `map(함수, 리스트)`
- 리스트의 요소를 함수에 넣고 리턴된 값으로 새로운 리스트를 구성해 주는 함수

#### 2. filter()
- 문법 : `filter(함수, 리스트)`
- 리스트 요소를 함수에 넣고 리턴된 값이 True인 요소로만 새로운 리스트를 구성해 주는 함수

#### 3. map(), filter()의 예제
```python
# 함수 선언
def power(item) : 
    return item * item
def under_3(item) :
    return item < 3
# 변수 선언
list_a = [1,2,3,4,5]
#################################
# map() 예제
output_a = map(power, list_a)
print(list(output_a))
# 실행결과
[1,4,9,16,25]
##################################
# filter() 예제
output_b = filter(under_3, list_a)
print(list(output_b))
# 실행결과
[1,2]
```

#### 4. 람다 : 간단하게 함수를 선언하는 방법
- `lambda 매개변수:리턴값`

#### 5. 람다의 예제
1. 람다
```python
# 함수 선언
power = lambda x : x * x
under_3 = lambda x : x < 3
# 변수 선언
list_a = [1,2,3,4,5]
##################################
# map() 사용
output_a = map(power, list_a)
print(list(output_a))
# 실행결과
[1,4,9,16,25]
##################################
# filter() 사용
output_b = filter(under_3, list_a)
print(list(output_b))
# 실행결과
[1,2]
```
2. <font color='red'>인라인 람다 사용법 : 람다는 함수의 매개변수에 곧바로 넣을 수 있다.</font>
```python
# 변수
list_a = [1,2,3,4,5]
# map() 사용
output_a = map(lambda x: x * x, list_a)
# filter() 사용
output_b = filter(lambda x: x < 3, list_a)
```

#### 6. 매개변수가 여러개인 람다
-`lambda x, y: x * y`

## [ 6 ] 파일처리
- 파일 : 텍스트 파일, 바이너리 파일.
- 파일 열기, 파일 읽기, 파일 쓰기

### 6-1. 파일 열기/닫기
- 열기 : `파일객체 = open(문자열: 파일경로, 문자열: 읽기 모드)`
- 닫기 : `파일객체.close`
- 읽기 모드 

|모드 |설명 |
|-----|-----|
|w  |write(새로 쓰기)|
|a  |append(이어 쓰기)|
|r  |read(읽기)|

- 예제
```python
# 파일열기
file = open('basic.txt','w')
# 파일쓰기
file.write("Hello Python Programming...!")
# 파일닫기
file.close()
```

### 6-2. with 키워드
- 파일을 열고, 닫지않는 실수를 방지하기 위해 구문이 종료될 때 파일도 함께 닫힌다.
- 문법
```python
with open( 파일경로, 모드) as 파일 객체 :
    실행문
```
- 예제
```python
with open('basic.txt', 'w') as file : 
    file.write('Hello Python programming..!')
```

### 6-3. 텍스트 읽기
- write(), read()
- 텍스트 쓰기 : `파일객체.write()`
- 텍스트 읽기 : `파일객체.read()`
- 예제
```python
with open('basic.txt', 'r') as f :
    content = f.read()
print(content)
```

### 6-4. 텍스트 한 줄씩 읽기
- 텍스트파일 : CSV(Comma Separated Values), XML, JSON 등
- 한 줄씩 읽기 문법
```python
for  한줄을 나타내는 문자열 in 파일객체 :
    처리
```
- 한줄씩 읽기 예제
```python
with open('basic.txt','r') as f:
    for line in f :
        # line으로 처리할 내용을 처리함
```

## [ 7 ] 제너레이터
- 이터레이터를 직접 만들때 사용
- 함수는 <font color='red'>yield 키워드</font>를 사용하면 제너레이터 함수가 됨
- 제너레이터 함수를 호출해도 함수 내부 코드는 실행되지 않으나, <font color='red'>next()</font> 함수를 사용해 함수를 실행 할 수 있음

### 7-1. 제너레이터 함수

#### 1. 예제
```python
# 함수 선언
def test():
    print('함수 호출됨')
    yield 'test' # next()로 제너레이터 실행 시 'test'가 리턴됨
# 함수 호출
print('A 통과')
test()
print('B 통과')
test()
print(test())
# 실행 결과
A 통과
B 통과
<generator object test at 주소>
```
- 제너레이터는 '제너레이터'를 리턴함.
- 제너레이터는 호출한다고 실행되지 않음.
- 제너레이터는 next() 함수를 사용해 함수를 실행함.
- next() 로 함수 실행 시, yield 키워드 부분까지만 실행되고 yield 뒤에 뒤따라 입력한 값이 리턴됨

#### 2. 제너레이터 함수와 next() 함수
```python
# 함수 선언
def test(): # 내부에 yield 함수가 있으므로, 제너레이터 함수
    print('A 통과')
    yield 1
    print('B 통과')
    yield 2
    print('C 통과')
# 함수 호출
output = test() # 제너레이터 함수는 호출만으로 실행되지 않음
# next() 호출
print('D 통과')
a = next(output) # next() 함수 실행 했으므로, test()함수의 첫번째 yield 까지 실행됨
print(a) # test()가 첫번째 yield에 뒤 따라오는 값을 리턴하여, 1을 리턴
print('E 통과')
b = next(output) # next() 함수 실행 했으므로, test()함수의 두번째 yield 까지 실행됨
print(b) # 두번째 yield 뒤의 값을 리턴하여, 2를 리턴함
print('F 통과')
c = next(output) # next() 함수 실행 했으므로, test()함수의 끝까지 실행됨
print(c) #  Error!! 마지막은 yield가 없어 리턴할 값이 없음
# 한번더 실행
next(output) # 함수가 끝나면, StopIteration 예외가 발생함
################################
# 실행 결과
D 통과
A 통과
1
E 통과
B 통과
2
F 통과
C 통과
# 이후 에러 발생 
# error1 : print(c)에서 리턴받은 값이 없으므로 에러 발생
# error2 : 마지막의 next(output)은 함수가 끝났으므로, 'StopIteration' 예외 발생
```