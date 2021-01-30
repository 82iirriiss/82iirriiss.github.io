---
sort: 3
---

# 모델 개발

<br>

## 1. bookmark/models.py 에 테이블 정의

```python 
from django.db import models

class Bookmark(models.Model):
    title = models.CharField('TITLE', max_length=100, blank=True)
    url = models.URLField('URL', unique=True)

    def __str__(self):
        return self.title
```

<br>

## 2. Admin 사이트에 테이블 반영

- models.py 에서 정의한 테이블을 bookmark/admin.py 파일에 등록
- admin.site.register(Bookmark, BookmarkAdmin) 함수 호출대신 데코레이션 `@admin.register(Bookmark)` 사용하면 편리함

```python
from django.contrib import admin
from bookmark.models import Bookmark

@admin.register(Bookmark)
class BookmarkAdmin(admin.ModelAdmin):
    list_display = ('id', 'title', 'url')
```

<br>

## 3. 데이터베이스 변경 사항 반영

- **새로 생성한 bookmark 에 대한 DB 변경 사항을 추출하여, 반영하는 작업**

```bash
(venv) $ python3 manage.py makemigrations bookmark #bookmark 생략 가능
(venv) $ python3 manage.py migrate
```

- **테이블 모습 확인하기**

```bash
(venv)$ python manage.py runserver
```
- http://localhost/admin 에서 bookmark 테이블 확인