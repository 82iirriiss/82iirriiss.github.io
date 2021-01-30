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


### <font color="blue">*참고* 마이그레이션 명령 </font>
<div style='background-color: #D9EEF1;border-radius: 5px;padding: 10px;'>
1. (venv) $ python3 manage.py showmigrations : 모든 마이그레이션 보여주고, 각 마이그레이션 별 적용여부 확인<br>
2. (venv) $ python3 manage.py sqlmigrate bookmark 0001 : bookmark 앱의 0001번 마이그레이션을 적용 할 때 사용될 sql 문 보여줌
</div>


## PathParameter

### 1. Slug

1. 예시
```python
re_path(r'^post/(?P<slug>[-\w]+)/$', views.PostDV.as_view(), name='post_detail')
```
- slug의 pathparameter 지정을 <slug:slug> 로 할 경우, 한글을 인식하지 못함.
- <slug> 컨버터는 '[-a-zA-Z0-9_]+'만 허용하기 때문.
-  그러므로, 정규 표현식을 사용하여 한글도 입력 할 수 있도록 해 주어야 함


# 1. 날짜관련 필드의 포맷(ex. modify_dt, create_dt )
1. 참고: <https://docs.djangoproject.com/en/2.2/ref/templates/builtins/#date>
2. \{\{ post.modify_dt\|date:"N d, Y" \}\} : July 05, 2019 형식으로 표현됨
3. \{\{ object.modify_dt\|date:"j F Y" \}\} : 12 July 2015 형식
4. {% raw %}{% now "N d, Y" %}{% endraw %} : 오늘 날짜의 July 18, 2015 형식
5. {% raw %}{{ post.modify_dt\|date:'Y-m-d' }}{% endraw %} : 날짜를 2019-06-12 형식으로 표현
6. {% raw %}{{ post.modify_dt\|date:'F' }}{% endraw %} : 월을 july 형식으로 표현
7. {% raw %}{{ post.modify_dt\|date:'N, Y' }}{% endraw %} : 날짜를 May, 2015 형식으로 표현

## 2. 컨텍스트 오브젝트
1. **<font color="blue">object_list</font>**  : ListView등의 클래스뷰에서 전달하는 객체 리스트
2. **<font color="blue">object</font>** : DetailView 등의 클래스뷰에서 전달하는 객체 리스트
3. **<font color="blue">page_obj</font>** : ListView등의 클래스뷰에서 전달하는 객체 리스트. Page 객체가 들어 있는 컨텍스트 변수.
- **<font color="blue">page_obj.has_previous</font>** : 현재 페이지 기준으로 이전 페이지가 있는지 여부
- **<font color="blue">page_obj.has_next</font>** : 현재 페이지 기준으로 다음 페이지가 있는지 여부
- **<font color="blue">page_obj.previous_page_number</font>** : 이전 페이지의 번호
- **<font color="blue">page_obj.next_page_number</font>** : 다음 페이지의 번호
- **<font color="blue">page_obj.number</font>** : 현재 페이지의 번호
- **<font color="blue">page_obj.paginator.num_pages</font>** : 총 페이지 갯수
4. 예시

```
{% raw %}
<h1>Blog List</h1>
<br>

{% for post in object_list %}
    <h3><a href="{{ post.get_absolute_url }}">{{ post.title }}</a></h3>
    {{ post.modify_dt|date:"N d, Y"}}
    <p>{{ post.description }}</p>
{% endfor %}

<br>

<div>
    <span>
    {% if page_obj.has_previous %}
        <a href="?page={{ page_obj.previous_page_number }}">PreviousPage</a>
    {% endif %}

    Page {{ page_obj.number }} of {{ page_obj.paginator.num_pages }}

    {% if page_obj.has_next %}
        <a href="?page={{ page_obj.next_page_number }}">NextPage</a>
    {% endif %}
    </span>
</div>
{% endraw %}
```
5. **<font color="blue">date_list</font>** : 날짜관련 클래스뷰 ( XXXarchiveView 또는 ArchiveIndexView ) 에서 넘겨주는 컨텍스트 변수, DateQuerySet 객체 리스트를 담고 있음. 날짜정보만 추출한 리스트. 객체는 datetime.date 타입의 객체.
6. **<font color="blue">latest</font>** : ArchiveIndexView에서만 정의된 변수

## 3. 템플릿 필터
1. \{\{ object.content\|<font color="blue">linebreaks</font> \}\} : \\n(newline)을 인식하게 함 
2. \{\{ date\|<font color="blue">date:'Y'</font> \}\} : date객체로부터 YYYY 형식의 연도만 추출함, 2020 형식

## 4. Tag, 템플릿문법 등
1. 검색엔진에 노출돼야 하는 페이지라면 &lt;title&gt; 태그에 의미있는 문구를 넣을 것
