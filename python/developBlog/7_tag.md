---
sort: 7
---

# Blog앱에 태그 달기

1. 각 포스트마다 태그 달기
2. 태그에 해당하는 포스트를 리스트로 보여주기
3. 태그들만 모아서 각 태그에 링크 달기
4. 사용하는 패키지 : django-taggit, django-taggit-templatetags2

* \[참고\] django-taggit 문서 : <https://django-taggit.readthedocs.io/en/latest/index.html>
* \[참고\] django-taggit-templatetags2 문서 : <https://github.com/fizista/django-taggit-templatetags2>

## 1. 설계

#### [ 1 ] 테이블 설계
- Post 테이블에 'tags' 필드 추가
- 'tags' 필드의 타입은, django-taggit 에서 미리 설정해 둔 'TaggableManager' 를 사용하여야 함

|필드명 |타입 |제약조건 |설명 |
|-------|-----|---------|-----|
|id |Integer|PK, Audo Increment |기본키 |
|title |CharField(50) | |포스트 제목 |
|slug |SlugField(50) |Unique |포스트 제목 별칭 |
|description|CharField(100) |Blank |포스트 내용 한 줄 설명|
|content|TextField| |포스트 내용 기록 |
|create_dt|DateTimeField |auto_now_add|생성날짜|
|modify_dt|DateTimeField |auto_now|수정날짜| 
|**tags**|**TaggableManager** |**Blank, Null** |**포스트에 등록한 태그**|

#### [ 2 ] URL 설계
- 태그 클라우드 (태그들의 모음) URL
- 특정 태그가 달려 있는 포스트들의 리스트 URL

|URL패턴 |뷰 이름 |템플릿 파일명 |
|--------|--------|--------------|
|/blog/|PostLV(ListView)|post_all.html|
|/blog/post/|PostLV(ListView)|post_all.html|
|/blog/post/django-example/|PostDV(DetailView)|post_detail.html|
|/blog/archive/|PostAV(ArchiveIndexView)|post_archive.html|
|/blog/archive/2012/|PostYAV(YearArchiveView)|post_archive_year.html|
|/blog/archive/2012/nov/|PostMAV(MonthArchiveView)|post_archive_month.html|
|/blog/archive/2012/nov/10/|PostDAV(DayArchiveView)|post_archive_day.html|
|/blog/archive/today/|PostTAV(TodayArchiveView)|post_archive_day.html|
|**/blog/tag/**|**TagCloudTV(TemplateView)**|**taggit_cloud.html**|
|**/blog/tag/tagname/**|**TaggedObjectLV(ListView)**|**taggit_post_list.html**|

## 2. 작업 순서
1. 뼈대만들기 : settings.py에 INSTALLED_APPS 항목에 taggit, taggit_templatetags2 추가, tag 관련 옵션 추가
2. 모델링 : Post 모델에 tags 컬럼추가 및 메소드 오버라이딩 후 migrate 하기
3. URLconf 코딩 : tag 관련 페이지의 url 추가하기
4. View 개발 : tag 관련 클래스 뷰 추가
5. templates 개발 : post_detail.html, taggit_post_list.html, taggit_cloud.html 추가 개발

## 3. 뼈대 만들기
#### [ 1 ] mysite/settings.py  수정하기

```python
INSTALLED_APPS =[
    # 생략
    'taggit.apps.TaggitAppConfig', # 참고 사이트에 따르면, 'taggit' 으로 등록해도 됨. 지금은 django-taggit 패키지의 apps.py 를 참고하여 기입함
    'taggit-templatetags2',
]
# 생략

# 옵션 추가
TAGGIT_CASE_INSENSITIVE = True # 대소문자 구분하지 않음
TAGGIT_LIMIT = 50  # 태그 클라우드에 나타나는 태그의 최대 갯 수
```

## 4. 모델 코딩하기
#### [ 1 ] Post 모델 객체에 'tags' 필드 추가

```python
# blog/models.py
# 상단 생략
from taggit.managers import TaggableManager 

# 중간 생략, modify_dt 속성 다음에 'tags' 추가
tags = TaggableManager(blank=True)

# 이하 생략
```
- TaggableManager : taggit 패키지에 정의되어 있으며, ManyToManyField와 models.Manager 의 역할을 동시에 함. Tags 라는 별칭과 null=True 옵션이 디폴트 설정됨

#### [ 2 ] 태그이름이 어드민 사이트에 나타나도록 admin.py 변경

```python
# blog/admin.py

class PostAdmin(admin.ModelAdmin):
list_display = ('id', 'title', 'modify_dt', 'tag_list') # 장고의 models 에서 TaggableManager 타입을 지원하지 않으므로, taggit에서는 태그이름이 화면에 나올 수 있도록 'tag_list' 를 제공함
# 생략

# 두 메소드 추가
    
    # Post 테이블과 taggit 에서 제공하는 Tag 테이블이 ManyToMany 관계이므로, Tag 테이블 관련 레코드를 한번의 쿼리로 가져오기 위한 메소드. 
    # 쿼리 횟수를 줄여 성능을 높이고저 prefetch_related() 메소드 사용함 
    def get_queryset(self, request):
        return super().get_queryset(request).prefetch_related('tags')

    # tag_list로 보여줄 내용을 정의
    def tag_list(self, obj):
        return ', '.join(o.name for o in obj.tags.all())
```

#### [ 3 ] 변경사항 데이터베이스에 반영

```bash
(venv) $ python3 manage.py makemigrations
(venv) $ python3 manage.py migrate
```

#### [ 4 ] Admin 사이트 확인 
- (venv) $ python3 manage.py runserver
- http://localhost:8000/admin
- Posts 테이블에 'tags' 항목이 추가됨 (tags는 콤마로 구분된 태그이름을 나열하여 등록함)
- TAGGIT 앱에 Tag와 TaggedItem 2개의 테이블이 추가됨

## 5. URLconf 코딩
```python
# blog/urls.py

# 상단 생략

# Example : /blog/tag/
    path('tag/', views.TagCloudTV.as_view(), name='tag_cloud') # TamplateView 를 사용. taggit-templatetag2에서 처리를 template 에서 하도록 설계되었음
# Example : /blog/tag/tagname/
    path('tag/<str:tag>/', views.TaggedObjectLV.as_view(), name = 'tagged_object_list')
```

## 6. 뷰 코딩
```python
# blog/views.py

# 상위 내용 생략

# 코드가 간단한 이유는, 클라우드 처리기능이 템플릿 파일에 있기 때문. 
# {% raw %}템플릿에서 {% get_tagcloud %}  템플릿 태그가 처리를 함.{% endraw %}
class TagCloudTV(TemplateView):
    template_name = 'taggit/taggit_cloud.html'

class TaggedObjectLV(ListView):
    template_name = 'taggit/taggit_post_list.html'
    model = Post

    # 모델에서 반환한 Post & Tag 조인 테이블에서 tags의 name이 PATHParameter로 넘어온 tag 와 같은 목록만 반환
    def get_queryset(self):
        return Post.objects.filter(tags__name=self.kwargs.get('tag')) 

    # 기본적으로 ListView 에서는 object_list 변수를 넘겨주지만, 여기에 'tagname'을 추가하기 위해 메소드를 오버라이딩 함
    def get_context_data(self, **kwargs): 
        context = super().get_context_data(**kwargs) 
        context['tagname'] = self.kwargs['tag']
        return context
```

## 7. 탬플릿 코딩
- 템플릿 파일의 위치는 <font color='red'>root/blog/templates/taggit/*.html</font> 로 하였음
-  root/templates/taggit/*.html 로 위치를 지정하는 것도 좋은 방법임

#### [ 1 ] tags 가 보이도록 post_detail.html 수정
```
{% raw %}
{% extends "base.html" %}

{% block title %}post_detail.html{% endblock %}

{% block content %}
    <h2>{{ object.title }}</h2>

    <p>
        {% if object.get_next %}
        <a href="{{ object.get_next.get_absolute_url }}" title="View previous post">
            <i class="fas fa-arrow-circle-left"></i> {{ object.get_next }}
        </a>
        {% endif %}

        {% if object.get_previous %}
        | <a href="{{ object.get_previous.get_absolute_url }}" title="View next post">
        {{ object.get_previous }} <i class="fas fa-arrow-circle-right"></i>
        </a>
        {% endif %}
    </p>

    <div>{{ object.modify_dt|date:"j F Y" }}</div>
    <br>

    <div>
        {{ object.content|linebreaks }}
    </div>

    <br>
    <div>
        <b>TAGS</b> <i class="fas fa-tag"></i>
        {% load taggit_templatetags2_tags %}
        {% get_tags_for_object object as "tags" %}
        {% for tag in tags %}
        <a href="{% url 'blog:tagged_object_list' tag.name %}">{{tag.name}}</a>
        {% endfor %}
	&emsp;
        <a href="{% url 'blog:tag_cloud' %}"> <span class="btn btn-info btn-sm">TagCloud</span> </a>
    </div>
{% endblock %}
{% endraw %}
```
- <font color='red'>{% raw %}{% load taggit_templatetags2_tags %}{% endraw %} : taggit 패키지에 정의된 템플릿 태그를 사용하기 위하여 load 함</font>
- <font color='red'>{% raw %}{% get_tags_for_object %}{% endraw %} : object 객체에 달려 있는 태그들의 리스트를 추출하는 커스텀 태그, 추출한 태그 리스트는 tags 변수에 할당</font>

#### [ 2 ] taggit_cloud.html
```
{% raw %}
{% extends "base.html" %}

{% block title %}taggit_cloud.html{% endblock %}

{% block extra-style %}
<style type="text/css">
.tag-cloud {
    width: 40%;
    margin-left: 30px;
    text-align: center;
    padding: 5px;
    border: 1px solid orange;
    background-color: #ffc;
}
.tag-1 {font-size: 12px;}
.tag-2 {font-size: 14px;}
.tag-3 {font-size: 16px;}
.tag-4 {font-size: 18px;}
.tag-5 {font-size: 20px;}
.tag-6 {font-size: 24px;}
</style>
{% endblock %}

{% block content %}
    <h1>Blog Tag Cloud</h1>
    <br>

    <div class="tag-cloud">
        {% load taggit_templatetags2_tags %}
        {% get_tagcloud as tags %}
        {% for tag in tags %}
        <span class="tag-{{tag.weight|floatformat:0}}">
            <a href="{% url 'blog:tagged_object_list' tag.name %}"> {{tag.name}}({{tag.num_times}})</a>
        </span>
        {% endfor %}
    </div>
{% endblock %}


{% endraw %}
```
- <font color='red'>{% raw %}{% get_tagcloud as tags %}{% endraw %} : 모든 태그를 추출하는 커스텀 태그. 태그 리스트를 tags 변수에 할당</font>
- <font color='red'>**태그 객체와 관련된 속성들**</font>
    + <font color='red'>{% raw %}{{tag.name}}: 태그의 이름{% endraw %}</font>
    + <font color='red'>{% raw %}{{tag.num_times}}: 태그가 몇번 사용되었는지{% endraw %}</font>
    + <font color='red'>{% raw %}{{tag.weight}}: 태그의 중요도, {{tag.num_times}}에 의해 변환된 수치 {% endraw %}</font>
    + <font color='red'>{% raw %}{{tag.weight|floatformat:0}}: tag.weight 를 반올림하여 정수로 표현하는 필터 {% endraw %}</font>

#### [ 3 ] taggit_post_list.html
```
{% raw %}
{% extends "base.html" %}

{% block title %}taggit_post_list.html{% endblock %} 

{% block content %}

    <h1>Posts for tag - {{ tagname }}</h1> 
    <br>

    {% for post in object_list %}
        <h2><a href='{{ post.get_absolute_url }}'>{{ post.title }}</a></h2> 
        {{ post.modify_dt|date:"N d, Y" }} 
        <p>{{ post.description }}</p> 
    {% endfor %}

{% endblock %}


{% endraw %}
```
- <font color='red'>{% raw %}{{tagname}}: view 에서 정의한 컨텍스트 변수에 추가한 속성{% endraw %}</font>