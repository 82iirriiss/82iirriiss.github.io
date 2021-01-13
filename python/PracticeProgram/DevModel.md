---
sort: 3
---

# 모델 개발 시 알아두기

## 1. bookmark/models.py 에 테이블 정의
1. **테이블 - 클래스, 컬럼 - 클래스 변수**
2. **테이블 클래스는 django.db.models.Model 클래스를 상속**

## 2. Admin 사이트에 테이블 반영
1. models.py 에서 정의한 테이블을 bookmark/admin.py 파일에 등록
2. admin.site.register(Bookmark, BookmarkAdmin) 함수 호출대신 데코레이션 <font color='red'>@admin.register(Bookmark)</font> 사용하면 편리함

```python
from django.contrib import admin
from bookmark.models import Bookmark

@admin.register(Bookmark)
class BookmarkAdmin(admin.ModelAdmin):
    list_display = ('id', 'title', 'url')
```

## 3. 데이터베이스 변경 사항 반영

1. **새로 생성한 bookmark 에 대한 DB 변경 사항을 추출하여, 반영하는 작업**

```bash
(venv) $ python3 manage.py makemigrations bookmark #bookmark 생략 가능
(venv) $ python3 manage.py migrate
```

### <font color="blue">⌘ 마이그레이션 명령 </font>
<div style='background-color: #D9EEF1;border-radius: 5px;padding: 10px;'>
1. (venv) $ python3 manage.py showmigrations : 모든 마이그레이션 보여주고, 각 마이그레이션 별 적용여부 확인<br>
2. (venv) $ python3 manage.py sqlmigrate bookmark 0001 : bookmark 앱의 0001번 마이그레이션을 적용 할 때 사용될 sql 문 보여줌
</div>

## 4. 컬럼의 데이터필드 타입 

- from django.db import models

### (1) models.SlugField(옵션,)

1. 슬러그(Slug)란?
- 페이지나 포스트를 설명하는 핵심단어의 집합
- 웹 개발시에는 특정 콘텐츠의 고유주소로도 사용됨
- 보통, 페이지나 포스트의 제목에서 조사, 전치사, 쉼표, 마침표등을 빼고 띄어쓰기는 하이픈(-)으로 바꾸어 만듦
- URL에서 Slug를 고유주소로 사용함으로써 검색 엔진에서 빨리 페이지를 찾아주고 정확도를 높일 수 있음
2. SlugField 필드타입
- URL 에서 pk 대신 사용됨. pk (=id) 를 사용하는 것 보다 슬러그를 사용하면 이해하기 쉬움
- Slug 필드의 기본 길이는 50
- 해당 Slug 필드에는 기본적으로 **인텍스**가 생성됨

### (2) models.CharField()
### (3) models.TextField()
### (4) models.DateTimeField()
## 5. 데이터 필드의 옵션들

1. blank=True : 빈칸 허용 여부
2. auto_now_add=True : models.DateTimeField 의 옵션으로, 생성될 때 시간을 자동 기록
3. auto_now=True : models.DateTimeField 의 옵션으로, 객체가 저장될 때 (= 객체가 변경될 때) 시간을 자동으로 기록
4. verbose_name='TITLE' : 폼 화면에서 레이블로 사용되는 문구로, 해당 컬럼의 별칭
5. max_length=50 : 최대 길이
6. allow_unicode=True : 한글입력을 허용함
7. help_text='도움말' : Admin 사이트에서 입력필드를 설명하는 문구

## 6. Model 클래스의 Meta 내부 클래스

1. 필드 속성외에 필요한 모델의 파라미터 정의
2. 예제

```python
class Post(models.Model):
    ...(생략)...
    class Meta:
        verbose_name = 'post' # 테이블의 단수 별칭
        verbose_name_plural = 'posts' # 테이블의 복수 별칭
        db_table = 'blog_posts' # 데이터베이스에 저장되는 테이블의 이름 (이렇게 지정하지 않으면, 앱명_모델클래스명 이 기본이므로 blog_post 였을 것임.)
        ordering = ('-modify_dt',) # 내림차순 정렬
```
- verbose_name : 테이블의 단수 별칭
- verbose_name_plural : 테이블의 복수 별칭
- db_table : DB에 저장될 테이블의 이름
- ordering : 테이블 조회시 기본 정렬

## 7. Model 클래스의 함수 정의

1. 예제
```python
class Post(models.Model) :
    ...(생략)...

    def __str__(self):
        return self.title
    
    def get_absolute_url(self):
        return reverse('blog:post_detail', args=(self.slug,))
    
    def get_previous(self):
        return self.get_previous_by_modify_dt()

    def get_next(self):
        return self.get_next_by_modify_dt()
```

-  \_\_str\_\_ 함수 : 모델을 출력 할 때, \_\_str\_\_() 함수를 이용함
-  get_absolute_url() 함수 : 'blog:post_detail'의 URL 을 리턴하고, 인자로 slug 값을 리턴합, templates 에서 모델객체.get_absolute_url 로 호출
-  get_previous() 함수 : 장고의 내장함수 <font color='red'>get_previous_by_modify_dt()</font>를 리턴하여, modify_dt로 정렬 하였을 때 이전 모델객체(row)를 반환, templates 에서 모델객체.get_previous 로 호출
-  get_next() 함수 : 장고의 내장함수 <font color='red'>get_next_by_modify_dt()</font>를 리턴하여, modify_dt로 정렬 하였을 때 다음 모델 객체(row)를 반환, templates 에서 모델명.get_next 로 호출

## 8. Model 클래스를 admin.py에 등록

### (1) 등록 속성

1. 예제
```python
@admin.register(Post)
class PostAdmin(admin.admin.ModelAdmin):
    list_display = ('id','title','modify_dt')
    list_fiter = ('modify_dt')
    search_fields = ('title', 'content')
    prepopulated_fields = {'slug':('title',)}
```

2. list_display :  Admin 사이트에 보여지는 컬럼의 순서
3. list_filter : 특정 필터링 결과만 보여짐
4. search_fields : 지정된 컬럼으로 검색할 수 있는 검색필드 보여짐
5. prepopulated_fields : 자동 입력으로, 위의 예제에서는 slug를 title을 사용해 미리 채워지도록 함
