---
sort: 5
---

# 뷰 개발

- bookmark/views.py

```python
from django.views.generic import ListView, DetailView
from bookmark.models import Bookmark


# Create your views here.
from mysite.views import OwnerMixin


class BookmarkLV(ListView):
    model = Bookmark


class BookmarkDV(DetailView):
    model = Bookmark
```

- ListView의 컨텍스트 변수는 기본적으로 `object_list` 를 사용
- ListView의 템플릿 파일 이름은 기본적으로 `모델명_list.html` 을 사용

<br>

- DetailView의 컨텍스트 변수는 기본적으로 `object` 를 사용
- DetailView의 템플릿 파일 이름은 기본적으로 `모델명_detail.html` 를 사용