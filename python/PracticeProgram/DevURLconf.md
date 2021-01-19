---
sort : 4
---

# URLconf 개발 시 알아두기

## PathParameter

### 1. Slug

1. 예시
```python
re_path(r'^post/(?P<slug>[-\w]+)/$', views.PostDV.as_view(), name='post_detail')
```
- slug의 pathparameter 지정을 <slug:slug> 로 할 경우, 한글을 인식하지 못함.
- <slug> 컨버터는 '[-a-zA-Z0-9_]+'만 허용하기 때문.
-  그러므로, 정규 표현식을 사용하여 한글도 입력 할 수 있도록 해 주어야 함

