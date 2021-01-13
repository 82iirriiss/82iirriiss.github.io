---
sort : 6
---

# 템플릿 개발 시 알아두기

## 1. 날짜관련 필드의 포맷(ex. modify_dt, create_dt )
1. 참고: <https://docs.djangoproject.com/en/2.2/ref/templates/builtins/#date>
2. \{\{ post.modify_dt\|date:"N d, Y" \}\} : July 05, 2019 형식으로 표현됨
3. \{\{ object.modify_dt\|date:"j F Y" \}\} : 12 July 2015 형식
4. {% raw %}{% now "N d, Y" %}{% endraw %} : 오늘 날짜의 July 18, 2015 형식
5. {% raw %}{{ post.modify_dt|date:'Y-m-d' }}{% endraw %} : 날짜를 2019-06-12 형식으로 표현
6. {% raw %}{{ post.modify_dt|date:'F' }}{% endraw %} : 월을 july 형식으로 표현
7. {% raw %}{{ post.modify_dt|date:'N, Y' }}{% endraw %} : 날짜를 May, 2015 형식으로 표현

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
