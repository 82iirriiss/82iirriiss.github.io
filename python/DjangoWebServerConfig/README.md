---
sort: 6
---

# Django 웹서버 연동원리

> 참고 : <https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/>   
> 장고의 wsgi.py 파일   
> 장고의 WSGI 인터페이스   
> 운영서버 적용 전 장고의 설정 변경 사항   

1. 운영서버로 배포 시, 운영환경의 웹서버가 우리가 만든 웹 애플리케이션을 인식 할 수 있도록 몇가지 설정사항 변경이 필요하다.
2. 운영서버는 거의 리눅스를 사용하므로, centOs 기준으로 설명한다.
3. 개발 PC에서 운영 PC로 소스를 복사하기 위해 FTP 파일이 필요한데, Xftp, WinSCP, FileZilla 등을 사용 할 수 있다.

source: `{{ page.path }}`
