---
sort: 5
---

# 뷰 개발 시 알아두기

## 제네릭 뷰 : django.views.generic

### 1. ListView
1. 예제
```python
class BookmarkListView(ListView):
    model = Bookmark
```

2. 명시적으로 지정하지 않아도 템플릿으로 전달하는 컨텍스트 변수와 템플릿 이름을 알아서 지정함
- 개발자가 원하면 지정할 수도 있음
- 컨텍스트 변수 : <font color='red'>object_list</font>
- 템플릿 파일명 : 모델명(소문자)_list.html (ex: bookmark_list.html)

### 2. DetailView
1. 예제
```python
class BookmarkDetailView(DetailView):
    model = Bookmark
```

2. 명시적으로 지정하지 않아도 컨텍스트 변수와 템플릿 파일명을 알아서 지정함
- 개발자가 원하면 지정할 수도 있음
- 컨텍스트 변수 : object
- 템플릿 파일명 : 모델명(소문자)_detail.html


### 3. 날짜 제네릭 뷰 ( django.views.generic.dates )
#### [1] ArchiveIndexView

1. 예시
```python
class PostAV(ArchiveIndexView):
model = Post
date_field = 'modify_dt'
```

2. 모델(=테이블)로 부터 객체 리스트를 가져와 날짜필드로 정렬하여 출력함
3. date_field='modify_dt' : 정렬 시 기준이 되는 컬럼을 'modify_dt' 로 정함
4. 따로 지정하지 않는다면, 템플릿 파일명은 '모델클래스_archive.html' 이다. ( post_archive.html )

#### [2] YearArchiveView

1. 예시
```python
class PostYAV(YearArchiveView):
model = Post
date_field = 'modify_dt'
make_object_list = True
```

2. 날짜 필드의 연도를 기준으로 객체 리스트를 가져와 그 객체들이 속한 **월을 리스트**로 출력
3. make_object_list = True : 연도에 해당하는 객체의 리스트를 만들어 템플릿에 넘겨줌. 컨텍스트 변수로 object_list 를 사용함   
4. 따로 지정하지 않으면, 템플릿 파일명은 '모델명_archive_year.html' 이다. ( post_archive_year.html )

#### [3] MonthArchiveView
1. 예시
```python
class PostMAV(MonthArchiveView):
model = Post
date_field = 'modify_dt'
```

2. 날짜 필드의 연도/월을 기준으로 객체 리스트를 가져와 해당 연도/월에 속한 객체의 리스트를 출력
3. 따로 지정하지 않으면, 템플릿 파일명은 '모델명_archive_month.html' 이다. ( post_archive_month.html )

#### [4] DayArchiveView
1. 예시
```python
class PostDAV(DayArchiveView):
model = Post
date_field = 'modify_dt'
```

2. 날짜 필드의 연/월/일을 기준으로 객체 리스트를 가져와 출력
3. 따로 지정하지 않으면, 템플릿 파일명은 '모델명_archive_day.html' 이다. ( post_archive_day.html )

#### [5] TodayArchiveView
1. 예시
```python
class PostTAV(TodayArchiveView):
model = Post
date_field = 'modify_dt'
```

2. 날짜 필드가 오늘인 객체 리스트를 가져와 그 리스트를 출력
3. 따로 지정하지 않으면, 템플릿 파일명은 '모델명_archive_day.html' 이다.   
( post_archive_day.html, DayArchiveView를 상속한 PostDAV 뷰클래스와 같은 템플릿을 사용한다)