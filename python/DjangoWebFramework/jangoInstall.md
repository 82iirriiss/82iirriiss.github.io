---
sort: 1
---

# 장고 프로그램 설치

> <font color='red'>python, pip 설치되어 있어야 함 (python 설치 시, pip 자동 설치)</font>   

## 1. 윈도우/리눅스/macOs 공통 설치
```
>pip install Django
```

- 리눅스의 경우, sudo pip install Django (sudo를 이용하여 관리자 권한 득)

## 2. Django 제거

```
>cd (생략, python 설치된 폴더)/site-packages/
>rm -rf django
>rm -rf Django*
```

- 장고가 설치된 디렉토리가 알고 싶다면??

```
>python -c 'import django; print(django.__path__)'
```

## 3. Django 버전확인
```
> python -m django --version
```
