---
sort: 1
---

# 파이썬 코딩 시, 알아야 할 꿀팁!

## 날짜 입력하기

    - datetime.datetime.now() 보다는, timezone.now() 사용을 추천함
    - current_year = timezone.now().year

## __str__() 메소드

    - 객체를 string 으로 표현해 주기 위해 모든 클래스에 정의 할 수 있다.
    - Question 객체를 알아보기 쉬운 스트링으로 표현하기 위해 self.question_text를 __str__() 함수에서 리턴한다.

## 장고 설치 위치 확인하기

```console
> python -c 'import django; print(django.__path__)'
```

## models 의 필드 알아보기
- 도큐먼트 참조 : <https://docs.djangoproject.com/en/2.1/ref/models/fields/>

## 테이블 간의 관계 (3가지)
- ForeignKey
- ManyToManyField
- OneToOneField

## 장고 파이썬 쉘 (jango python shell) 시작 명령어
- python manage.py shell

## 장고에서 지원하는 model의 필드
- 참고 : <https://docs.djangoproject.com/en/3.1/ref/models/fields/>

## CentOs 에서 Python의 특정버전을 범용적으로 사용하는 방법
`sudo alternatives --set python /usr/bin/python3` 

## 운영서버 이전하기 전 설정 체크 명령어
`$ python manage.py check --deploy`

