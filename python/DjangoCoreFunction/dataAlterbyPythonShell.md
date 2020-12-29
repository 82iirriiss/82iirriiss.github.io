---
sort: 2
---

# 장고 파이썬 쉘로 데이터 조작하기

> <font color='red'>간단한 데이터 관리는 Admin 사이트 UI를 활용하지만, 복잡하고 다양한 데이터 처리나 웹 브라우저에 접속 할 필요가 없을 때, 일반적으로 shell 로 데이터를 처리한다.</font>   
> Create   
> Read   
> Update   
> Delete   
> 데이터 조작하기 실습   


## 1. 장고 파이썬 쉘 시작하는 명령어   
- manage.py 를 통해 DJANGO_SETTINGS_MODULE 속성을 이용하여 미리 mysite/settings.py 파일을 임포트 함.

    ```
    ch3>python manage.py shell
    ```

## 2. Create - 데이터 생성/입력
-   save() 명령 실행, 내부적으로 SQL의 INSERT 문장 실행

    ```
    ch3> python manage.py shell
    In [1]: from polls.models import Question, Choice
    In [2]: from django.utils import timezone
    In [3]: q = Question(question_text='What's new?', pub_date=timezone.now())
    In [4]: q.save()
    ```


## 3. Read - 데이터 조회

> 조회 결과는 QuerySet 객체(컬랙션) 로 반환됨   
> QuerySet은 SELECT 문장에 해당하며 QuerySet 객체를 반환하려면, objects 객체를 이용해야 함   
> Where에 해당하는 함수는, filter() 와 exclude() 가 있음   
> 1개의 데이터만 반환하는 것이 확실할 때는, get() 함수를 쓸 수 있음   
> SQL의 OFFSET이나 LIMIT 에 해당하는 기능은 배열 슬라이스를 사용 할 수 있으며, 이때는 리스트를 반환함   

1. 전체 레코드 조회하여 <font color='red'>QuerySet 반환받기</font>
    - 핵심 키워드 : `Question.objects.all()`

    ```
    In [16]: Question.objects.all()
    Out[16]: <QuerySet [<Question: What is your hobby?>, <Question: Who do you like best?>, <Question: Where do you live?>, <Question: What's new?>]>
    ```

2. 조건에 맞는 레코드 검색하기 (Where) : filter(), exclude()
    - objects.all(), filter(), exclude()는 모두 <font color='red'>QuerySet 컬랙션을 반환</font>하므로 `체인`식 호출이 가능

    ```
    In [12]: Question.objects.all().filter(question_text__startswith='What').exclude(pub_date__gte=datetime.now().strftime("%Y-%m-%d")).filter(pub_date__gte=datetime(2005, 1, 30))
    Out[12]: <QuerySet [<Question: What is your hobby?>]>
    ```

3. 한개의 요소만 있는것이 확실한 경우 get() 사용
> 1개의 <font color='red'>객체를 리턴함</font>

    ```
    In [13]: Question.objects.get(pk=1)
    Out[13]: <Question: What is your hobby?>
    ```

4. SQL의 OFFSET 또는 LIMIT 에 해당하는 개수 제한하기
> 배열 슬라이스 문법을 사용함   
> <font color='red'> 리스트를 반환함 </font>

    ```
    In [14]: Question.objects.all()[:2]
    Out[14]: <QuerySet [<Question: What is your hobby?>, <Question: Who do you like best?>]>

    In [15]: Question.objects.all()[1:3]
    Out[15]: <QuerySet [<Question: Who do you like best?>, <Question: Where do you live?>]>
    ```

## 4. Update - 데이터 수정하기

1. 한개의 객체 (row)만 수정하기 : save() 메소드 사용

    ```
    In [23]: q=Question.objects.get(question_text__startswith='What is')

    In [24]: q.question_text = 'What is your favorit thing?'

    In [25]: q.save()

    In [26]: Question.objects.all()
    Out[26]: <QuerySet [<Question: What is your favorit thing?>, <Question: Who do you like best?>, <Question: Where do you live?>, <Question: What's new?>]>
    ```

2. 여러개의 객체 한번에 수정하기 : update() 메소드 사용

    ```
    In [28]: Question.objects.filter(question_text__startswith='What').update(question_text='Everything is the same')
    Out[28]: 2
    ```

## 5. Delete - 데이터 삭제
> delete() 메소드 사용   
> Fk 로 연결된 Choice 데이터도 연쇄 삭제됨   

1. 조건에 해당하는 데이터 삭제하기

    ```
    In [32]: Question.objects.filter(question_text__startswith='Everything').delete()
    Out[32]: (5, {'polls.Choice': 3, 'polls.Question': 2})

    In [33]: Question.objects.all()
    Out[33]: <QuerySet [<Question: Who do you like best?>, <Question: Where do you live?>]>

    In [34]: Choice.objects.all()
    Out[34]: <QuerySet [<Choice: Reading>, <Choice: Soccer>, <Choice: Climbing>, <Choice: seoul, Korea>, <Choice: jeju, Korea>, <Choice: Asan, Korea>]>
    ```

2. 모든 데이터 삭제하기 : all()메소드를 사용해야만 모두 삭제 할 수 있음. 삭제의 위험성을 낮추기 위한 조치

    - (O) `Question.objects.all().delete()`
    - (x) `Question.objects.delete()`

## 6. FK 로 연결된 Choice 모델 데이터 조작하기
> choice_set 속성을 이용한다.   

### (1) Question 테이블의 레코드 하나를 조회한다

    >>> q = Question.objects.get(pk=2)

### (2) 조회된 Question 객체 q에 연결된 Choice 레코드를 모두 조회한다

    In [37]: q.choice_set.all()
    Out[37]: <QuerySet [<Choice: Reading>, <Choice: Soccer>, <Choice: Climbing>]>

### (3) question 객체에 답변항목인 choice 객체 3개를 생성해 보자

    In [36]: q = Question.objects.get(pk=2)

    In [37]: q.choice_set.all()
    Out[37]: <QuerySet [<Choice: Reading>, <Choice: Soccer>, <Choice: Climbing>]>

    In [38]: q.choice_set.create(choice_text='Sleeping', votes=0)
    Out[38]: <Choice: Sleeping>

    In [39]: q.choice_set.create(choice_text='Eating', votes=0)
    Out[39]: <Choice: Eating>

    In [40]: c = q.choice_set.create(choice_text='Palying', votes=0)

### (4) choice 객체에 연결되어 있는 Question 객체를 조회해 보자

    In [40]: c = q.choice_set.create(choice_text='Palying', votes=0)

    In [41]: c.question
    Out[41]: <Question: Who do you like best?>

### (5) 반대로, question 객체에 연결된 choice 객체를 조회해 보자

    In [42]: q.choice_set.all()
    Out[42]: <QuerySet [<Choice: Reading>, <Choice: Soccer>, <Choice: Climbing>, <Choice: Sleeping>, <Choice: Eating>, <Choice: Palying>]>

    In [43]: q.choice_set.count()
    Out[43]: 6

### (6) __ 를 이용하여 객체간의 관계를 표현해 보자

(ex) pub_date 속성이 올해인 Question 객체에 연결된 Choice 객체를 모두 조회하는 명령

    In [44]: current_year = timezone.now().year

    In [45]: Choice.objects.filter(question__pub_date__year=current_year)
    Out[45]: <QuerySet [<Choice: Reading>, <Choice: Soccer>, <Choice: Climbing>, <Choice: seoul, Korea>, <Choice: jeju, Korea>, <Choice: Asan, Korea>, <Choice: Sleeping>, <Choice: Eating>, <Choice: Palying>]>

### (7) choice_set 중에 한개의 객체를 삭제해 보자

    In [46]: c = q.choice_set.filter(choice_text__startswith='Sleeping')

    In [47]: c.delete()
    Out[47]: (1, {'polls.Choice': 1})

    In [48]: