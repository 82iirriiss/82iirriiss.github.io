---
sort : 4
---

# URLconf 개발

- mysite/urls.py

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('bookmark/', include('bookmark.urls'))
]

```

- bookmark/urls.py

```python
from django.urls import path
from bookmark import views as v

app_name = 'bookmark'
urlpatterns = [
        path('', v.BookmarkLV.as_view(), name='index'),
        path('<int:pk>/', v.BookmarkDV.as_view(), name='detail')
        ]
```



