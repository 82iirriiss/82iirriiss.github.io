---
sort: 4
---

# 반복문

## [ 1 ] 리스트의 반복문
#### 1-1. 리스트
1. [요소, 요소, 요소,...,요소]
2. 각 요소는 여러 자료형으로 구성 할 수 있다.

#### 1-2. 리스트 연산자 : 연결(+), 반복(*), len()
```python
# 리스트
list_a = [1,2,3]
list_b = [4,5,6]
# (+) : 연결
print(list_a + list_b) # [1,2,3,4,5,6]
# (*) : 반복
print(list_a * 3) # [1,2,3,1,2,3,1,2,3]
# len() : 길이구하기
print(len(list_a)) # 3
```

#### 1-3. 리스트에 요소  추가 : append, insert, extend
1. append() : `리스트명.append(요소)`
2. insert() : `리스트명.insert(위치, 요소)`
3. extend() : `리스트명A.extend(리스트명B)`   

```python
list_a = [1,2,3]
# 리스트 요소 추가
list_a.append(4) 
print(list_a) # [1,2,3,4] 
list_a.insert(0, 10)
print(list_a) # [10,1,2,3,4]
list_a.extend([4,5,6]) 
print(list_a) # [10,2,3,4,5,6,7]
```

#### 1-4. 리스트에 요소 제거하기 : del,pop/remove/clear
1. 인덱스로 제거하기 : del, pop
- del 키워드 : `del 리스트명[인텍스]`
- pop 함수 : `리스트명.pop(인덱스)`
2. 값으로 제거하기 : remove
- remove 함수 : `리스트명.remove(값)`
3. 모두 제거하기 : clear
- clear함수 : `리스트명.clear()`

```python
list_a = [0,1,2,3,4,5]

# del 키워드
del list_a[1] 
print(list_a) # [0,2,3,4,5]
# pop(인덱스)
list_a.pop(2)
print(list_a) # [0,2,4,5]
# remove(값) : 일치하는 값이 여러개라도 가장 먼저 찾은 것만 제거 
list_b = [1,2,1,2]
list_b.remove(2)
print(list_b) # [1,1,2]
# clear()
list_b.clear()
print(list_b) # []
```

#### 1-5. 리스트 내부에 있는지 확인 : in/not in 연산자
`값 in 리스트`

```python
list_a = [1,2,3,4,5]
print(6 in list_a) # False
print(6 not in list_a) # True
```

#### 1-6. for 반복문
1. range()를 사용한 반복문
```python
for i in range(5) :
        print(i)
# 실행 결과
1
2
3
4
5
```

2. 리스트와 함께 사용하는 반복문
- 문법   
```python
for 반복자 in 리스트 :
        실행문
```
- 예시
```python
list = [1,2,3,4,5]
# for 반복문
for el in list :
        print(el)
# 실행 결과
1
2
3
4
5
```

## [ 2 ] 딕셔너리의 반복문
#### 2-1. 딕셔너리??
1. 키와 값의 쌍으로 저장되는 형태
2. {} 를 사용하여 선언 (리스트는 []를 사용하는 것과는 다름)

#### 2-2. 딕셔너리 선언하기
1. **키로 문자열을 사용할 때는 꼭 따옴표/홑따옴표를  붙여야 함**
```python
dict = {
    키:값,
    키:값,
    키:값,
    .....
    키:값
}
```

#### 2-3. 딕셔너리 요소 접근 : 딕셔너리[키]
```python
dict_a {
    'A':'에이',
    'B':'비',
    'C':'씨'
}
# 딕셔너리 출력
print(dict_a) # {'A':'에이','B':'삐','C':'씨'}
# 딕셔너리 요소 접근
print(dict_a['A']) # '에이'
```

#### 2-4. 에러
1. NameError : 딕셔너리의 키 이름을 따옴표 없이 선언했을 때, NameError 가 발생
2. KeyError : 존재하지 않는 키에 접근할 때 발생

#### 2-5. 딕셔너리에 값 추가하기/삭제하기
1. 추가하기 문법
```
딕셔너리[새로운 키] = 새로운 값
```
2. 제거하기 문법 : del 키워드 사용
```python
del dict_a['키']
```
3. 딕셔너리 내부에 키가 있는지 확인
- in 키워드
```python
key = 'A'
if key in dict_a:
    실행문
```
- get() 함수 : 키가 없을 지라도 error 발생하지 않고 None 을 출력함
```python
value = dict_a.get("존재하지 않는 키")
print(value) # None
if value == None :
    print("잘못된 키에 접근했습니다.")
```
4. 딕셔너리의 for 반복문
- 문법
```python
for Key in dictionary :
    실행문
```

## [ 3 ] 범위 자료형 : range()
1. 매개변수로 숫자 한개
`range(n)` : 0 ~ n-1까지
2. 매개변수로 숫자 두개
`range(n,m)` : n ~ m-1 까지
3. 매개변수가 숫자 3개
`range(n, m, k)` :n ~ m-1 의 범위, 앞뒤 정수가 k 만큼 차이를 가짐
4. range()를 list 로 변환
```python
print(list(range(10)))
# 실행 결과
[0,1,2,3,4,5,6,7,8,9]
```

#### 3-1. for 문과 range() 함께 사용하기
```python
for 숫자변수 in range(n,m) :
    #실행문 : n ~ m-1 번 반복
```

#### 3-2. for 문과 list, range() 를 조합하여 사용하기
```python
list = [0,1,2,3,4,5]
for i in range(len(list))
    print("{}번째 반복: 값은 {}".format(i, list[i]))
```

#### 3-3. 반대로 반복하기 : 역반복문
1. range()의 매개변수 3개 이용하기
```python
for i in range(4,0-1,-1) :
    print("현재 반복 변수: {}".format(i))
# 실행 결과
현재 반복 변수 : 4
현재 반복 변수 : 3
현재 반복 변수 : 2
현재 반복 변수 : 1
현재 반복 변수 : 0
```
2. reversed() 함수 이용하기
```python
for i in reversed(range(5)) :
    print("현재반복 변수: {}".format(i))
# 실행 결과
현재 반복 변수 : 4
현재 반복 변수 : 3
현재 반복 변수 : 2
현재 반복 변수 : 1
현재 반복 변수 : 0
```

## [ 4 ] while 반복문
#### 4-1. 상태 기반 while 반복문
```python
list_a = [1,2,1,2]
value = 2
# 리스트에  value가 있다면 반복
while value in list_a :
    list_a.remove(value)
```
#### 4-2. 시간 기반 while 반복문
```python
import time
number = 0
# 5초 동안 반복  
target_tick = time.time() + 5
while time.time() < target_tick :
    number += 1
# 출력
print("5초동안 {}번 반복했습니다.".format(number))
# 실행결과
5초 동안 142830405번 반복 했습니다.
```

## [ 5 ] 문자열, 리스트, 딕셔너리 관련 기본 함수
#### 5-1. 리스트 관련 함수
1. min(), max(), sum()
```python
list = [102,  52, 273, 32, 77]
print(min(list)) # 32
print(max(list)) # 273
print(sum(list)) # 537
# 꼭 리스트 객체를 쓰지 않고, 숫자를 나열해서 최솟값,최댓값을 구할 수 있다.
print(min(1,2,3)) # 1
print(max(1,2,3)) # 3
```
2. reversed()
- 기본 사용법
```python
list_a = [1,2,3,4,5]
list_reversed = reversed(list_a)
print(list(list_reversed))
# 실행 결과
[5,4,3,2,1]
```
- <font color='red'>reversed()를 이용하여 for 문을 쓸 때는, for문 안에서 reversed()를 써야 함</font>
```python
list_a = [1,2,3,4,5]
for i in reversed(list_a) :
    # 실행문
    print("-", i)
# 실행 결과
-5
-4
-3
-2
-1
```
3. <font color='red'>확장슬라이싱 [::-1]</font>
- reversed()와 같은 기능으로, 리스트를 뒤짚는 방법
```python
list_a = [1,2,3,4,5]
print(list_a[::-1])
# 실행 결과
5
4
3
2
1
```
4. enumerate() 함수와 반복문
- enumerate() : 리스트의 요소를 순회할 때, 인덱스를 확인할 수 있도록 도와주는 함수
```python
list_a = ['A','B','C']
# enumerate() 함수 적용하여 출력
print(list(enumerate(list_a)))
# 실행 결과
[(0, 'A'), (1, 'B'), (2, 'C')]
# 반복문과 조합하기
for i, value in enumerate(list_a) :
    print("{}번째 요소: {}".format(i, value))
# 실행 결과
0번째 요소 : A
1번째 요소 : B
2번째 요소 : C
```

#### 5-2. 딕셔너리의 items() 함수와 반복문
```python
dict = {'A': '값A',
        'B' : '값B',
        'C' : '값C'
    }
# items() 사용하여 딕셔너리 출력하기
print(dict.items())
# 실행 결과
dict_items([('A', '값A'), ('B', '값B'), ('C', '값C')])
# for 반복문과 items() 함수 조합
for key, value in dict :
    print("키:{}, 값:{}".format(key, value))
# 실행 결과
키:A,값:값A
키:B,값:값B
키:C,값:값C
```
#### 5-3. 리스트 내포
- 반복할 수 있는 data를 이용하여 새로운 리스트를 생성하는 방법
- 문법 : `리스트 이름 = [표현식 for 반복자 in 반복할 수 있는것]`
- 기본 리스트 내포 사용법

```python
# 리스트 내포를 이용해 짝수의 제곱을 구하는 방법
list_a = [i*i for i in range(0,20,2)]
print(list_a) # 0, 4, 16, 36, 64 ...,324
```
- 조건을 활용한 리스트 내포 사용법 : `리스트 이름 = [표현식 for 반복자 in 반복할 수 있는 것 if 조건문]`

```python
list_a = ['사과', '자두', '초콜릿', '바나나', '체리']
list_b = [ fruit for fruit in list_a if fruit != '초콜릿' ]
print(list_b)
# 실행 결과
['사과','자두','바나나','체리']
```

#### 5-4. 기타 (1)
1. if 조건문에서 여러줄 사용하기 : () 안에 문자열을 입력하는 방법   
```python
num = int(input('숫자를 입력하세요.'))
if num % 2 == 0 :
    # 아래와 같이 ()안에 여러줄의 문자열을 입력하면, 기본적으로는 한줄로 표현됨. 
    # 아래와 같이 여러줄로 표현하고 실제 출력 시, 줄바꿈하고 싶으면 \n 을 사용함.
    print((
            "입력한 문자열은 {}입니다."
            "{}는 짝수 입니다."
        ).format(num, num))
# 실행 결과
입력한 문자열은 12입니다.12는 짝수 입니다.
```
2. 문자열의 join() 함수
- 문법 : `문자열.join(문자열 리스트)`
- 예제
```python
print("::".join(['1','2','3']))
# 실행 결과
1::2::3
```
3. 여러줄 문자열과 join()을 조합하여 줄바꿈을 표현   
```python
print(('\n'.join([
        "안녕하세요.",
        "저는",
        "누구누구 입니다."
    ])
))
# 실행 결과
안녕하세요.
저는 
누구누구 입니다.
```

#### 5-5 기타 (2)
1. 이터레이터
- 반복할 수 있는 것 : 이터러블 ( 리스트, 딕셔너리, 문자열 튜플 )
- 이터러블중에서 next() 로 하나씩 꺼낼수 있는 요소를 **이터레이터**라고 함 
- reversed(list)의 반환물처럼 출력 시, 주소가 출력되는 이터러블
- enumerate(list)의 반환물
- 이터레이터 : 반복문의 매개변수로 전달 할 수 있으며, next() 함수로 요소를 꺼낼 수 있어야 함