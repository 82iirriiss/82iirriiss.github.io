---
sort: 1
---

# 가상환경 사용하기

## 1. 가상환경 구성해 주는 툴
- virtualenv 툴 : python 설치 시, 기본으로 제공되는 가상환경 구성 툴
- venv 모듈 :  python3 설치 시, 제공되는 모듈

## 2. 가상환경 사용하는 이유
- 프로젝트에 따라 별도의 개발 환경을 설치함으로써 다른 파이썬 프로그램에 영향을 주지 않도록 하기 위함
- 파이썬의 기본 라이브러리는 /usr/local/lib/python3.6/ 에 위치함
- 별도로 가상환경에서 추가로 설치하는 외부 패키지는 가상환경의 경로 내에 위치함

## 3. 가상환경 만들기
```bash
# 파이썬 버전 확인
$ python --version
$ python2 --version
$ python3 --version

# venv 모듈 있는지 확인하기 (-m 옵션은, 파이썬 모듈을 직접 실행하는 옵션)
$ python -m venv
$ python2 -m venv
$ python3 -m venv

# 가상환경을 모아둘 디렉터리 VENV 설치하기
$ cd /home/centos/
$ mkdir VENV
$ cd VENV/

# 가상환경 vDjBook 만들기
$ python3 -m venv vDjBook

# 가상환경안으로 진입
$ source /home/centos/VENV/vDjBook/bin/activate
# 가상환경 안에 있다는 것을 프롬프트에 표시
(vDjBook) $
# 가상환경에서 사용하는 파이썬의 실행 위치 확인
(vDjBook) $ which python3

# 가상환경에 설치된 외부 패키지를 또 다른 가상환경에 똑같이 설치하기
(vDjBook) $ pip3 freeze > requirements.txt
(otherVenv) $ pip install -r requirements.txt

# 가상환경에서 빠져나오는 명령어
(vDjBook) $ deactivate

```

## 4. 가상환경에 장고 패키지 설치하기
```bash
# 가상환경 실행
$ source /home/centos/VENV/vDjBook/bin/activate

# 가상환경에서 장고 패키지 설치
(vDjBook) $ pip3 install Django

# 파이썬 외부 패키지에 장고가 설치되었는지 확인
(vDjBook) $ ls -al /home/centos/VENV/vDjBook/lib/python3.6/site-packages/
```

## 5. 가상환경에 그 외의 패키지 설치하기
```bash
# 가상환경 실행
$ source /home/centos/VENV/vDjBook/bin/activate

# 태그 달기 기능에 필요한 패키지
(vDjBook) $ pip3 install django-taggit
(vDjBook) $ pip3 install django-taggit-templatetags2

# 폼 장식하는 패키지
(vDjBook) $ pip3 install django-widget-tweaks

# 이미지 처리에 필요한 패키지
(vDjBook) $ pip3 install Pillow

# 파이썬의 다국어 지원 기능에 따라, 지역에 맞는 로컬시간과 일광절양시간을 적용한 시간 적용하기 위한 패키지
(vDjBook) $ pip3 install pytz
```

## 6. 패키지 설치 툴 업그레이드 하기
```bash
(vDjBook) $ pip3 install -U pip setuptools wheel
```

## 7. InsecurePlatformWarning 해결하기 
```bash
# 패키지 설치중 InsecurePlatformWarning  경고 발생 시, HTTPS 처리하는 openSSL 관련 패키지를 설치 해야 함
(vDjBook) $ pip3 install pyopenssl ndg-httpsclient pyasn1
```

## 8. 가상환경에 설치된 패키지 확인하기
```bash
(vDjBook) $ pip3 list
(vDjBook) $ pip3 freeze
(vDjBook) $ ls -al /home/centos/VENV/vDjBook/lib/python3.6/site-packages/
```