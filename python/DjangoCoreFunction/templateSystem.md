---
sort: 3
---

# 템플릿 시스템
> 템플릿 코드 : 렌더링 하기 전의 템플릿 파일   
> 템플릿 파일 : 렌더링을 마친 후 html 등의 문서파일로 해석된 파일   

## 1. 템플릿 변수

1. 변수의 표현식

```
{% raw %}
{{variable}}
{{variable.prop}}
{% endraw %}
```

2. 정의되어 있지 않은 변수일 경우, '' 빈 문자열로 채워줌. 이 값을 변경하려면 settings.py 파일의 다음과 같은 속성을 지정해 주면 됨

    `TEMPLATE_STRING_IF_INVALID`

## 2. 템플릿 필터 
> 필터 참고하기 : <https://docs.djangoproject.com/en/2.1/ref/templates/builtins>

1. 파이프(\|) 문자 사용
{%raw%}
    `{{ name:lower}}` : name 변수값을 소문자로 변경해 주는 필터
{%endraw%}
2. 필터를 체인으로 연결 할 수 있음
{%raw%}
    `{{text|escape|linebreaks}}` : text 문자열의 특수문자를 escape 해주고, 그 결과를 <p> 태그로 감싸줌
{%endraw%}
3. 몇가지 필터는 인자를 가진 경우도 있음
    - bio 변수값 중 앞에 30개의 단어만 보여주고, 줄바꿈 문자는 모두 없애는 필터
    {%raw%}
        `{{bio|truncatewords:30}}`
    {%endraw%}
    - 인자에 빈칸이 있는 경우 따옴표로 감싸줌
    {%raw%}
        `{{list|join:" // "}}` : list 요소를 ' // '로 연결해 줌. list가 ['a','b','c'] 라면 'a // b // c' 가 됨
    {%endraw%}
    - value 값이 False 이거나 없는 경우, "nothing"으로 보여줌
    {%raw%}
        `{{value|default:"nothing"}}`
    {%endraw%}
    - value 변수값의 길이를 반환
    {%raw%}
        `{{value|length}}` : value 변수는 문자열이나 리스트도 가능함
    {%endraw%}
    - value 변수값에서 HTML 태그를 모두 없애 줌
    {%raw%}
        `{{value|striptags}}`
    {%endraw%}
    - value 값이 복수이면 복수 접미사를 붙여줌
    {%raw%}
        `{{value|pluralize}}` : value가 복수이면 's'를 붙여줌   
        `{{value|pluralize:'es'}}`: value가 복수이면 'es'를 붙여줌    
        `{{value|pluralize:'ies'}}` : value가 복수이면 'ies'를 붙여줌   
    {%endraw%}
    - value에 2를 더하기를 해줌
    {%raw%}
        `{{value|add:'2'}}`
    {%endraw%}
        - 예시
        {%raw%}
            `{{'python'|add:'django'}}` => 'pythondjango'   
            `{{[1,2,3]|add:[4,5,6]}}` => [1,2,3,4,5,6]   
            `{{'5'|add:'10'}}` => 15   
        {%endraw%}

## 3. 템플릿 태그

### (1) {%raw%}{% for %} 태그{%endraw%}
- 예시

```html
{%raw%}
{% for athlete in athlete_list %}
    <li>{{ athlete.name }}</li>
{% endfor %}
{%endraw%}
```

- loop 시 사용할 수 있는 여러가지 변수

|변수명 |설명   |
|-------|-------|
|forloop.counter    |1부터 카운트   |
|forloop.counter0  |0부터 카운트   |
|forloop.revcounter |역순으로 1부터 카운트  |
|forloop.revcounter0    |역순으로 0부터 카운트  |
|forloop.first  |루프 첫번째 실행이면 True   |
|forloop.last   |루프 마지막 실행이면 True  |
|forloop.parentloop |중첩 루프에서 바로 상위의 루프를 의미함    |

### (2) {%raw%}{% if %} 태그{%endraw%}
- 예시

```html
{%raw%}
{% if athlete_list %}
    Number of athletes: {{ athlete_list|length }}
{% elif athlete_in_locker_room_list %}
    Athletes should be out of the locker room soon!
{% else %}
    No athletes.
{% endif %}
{%endraw%}
```

- {%raw%}`{{if athlete_list|length > 1 }}` : {% if %} 태그에 필터와 연산자를 사용 할 수 있다.{%endraw%}
- {%raw%}and, or, not, and not, ==, !=, <, >, <=, >=, in, not in : {% if %} 태그에 불린 연산자를 사용 할 수 있다.{%endraw%}

### (3) {%raw%}{% csrf_token %} 태그{%endraw%}

- POST 방식의 폼을 사용하는 템플릿 코드에서 csrf_token을 사용 해야 함
- 위치는, <form> 엘리먼트 첫줄에 넣어줌   
- CSRF 토큰값 검증 실행하면,403 에러를 보여줌
- 외부 URL로는 보내지 말것
- 예시

```
{%raw%}
 <form action='.' method='post'>{% csrf_token %}
{%endraw%}
```

### (4) {%raw%}{% url %} 태그{%endraw%}
> 소스에 url 하드코딩을 방지하기 위해 사용

- 형식   
    ```
    {%raw%}
    {% url 'namespace:view-name' arg1 arg2 %}
    {%endraw%}
    ```

    + namespace : urls.py 파일의 include()함수 또는 app_name 변수에 정의한 이름공간
    + view-nme : urls.py 파일에서 정의한 URL 패턴이름
    + argN : 뷰 함수에서 사용하는 인자로, PathParameter. 빈칸으로 구분함

- 예시   
    ```
    {%raw%}
    <form action="{% url 'polls:vote' question.id  %}" method="post">
    {%endraw%}
    ```

### (5) {%raw%}{% with %} 태그{%endraw%}
> 특정 값을 변수에 저장해 두는 기능     

- 예시
    ```
    {%raw%}
    {% with total=business.employees.count %}
        {{ total }} people works at business
    {% endwith %}

    // 또는

    {% with business.employees.count as total %}
        {{ total }} people works at business
    {% endwith %}
    {%endraw%}
    ```


### (6) {%raw%}{% load %} 태그{%endraw%}
> 사용자 정의 태그 및 필터를 로딩       
> 참고 : <https://docs.djangoproject.com/en/2.1/ref/templates/builtins/>

- 예시 : somelibrary.py 와 package.otherlibrary.py 에 있는 사용자 정의 태그 및 필터를 로딩 해 줌
    ```
    {%raw%}{% load somelibrary package.otherlibrary %}{%endraw%}
    ```

## 4. 템플릿 주석

1. 한줄 주석 : {# #}
    ```
    {%raw%}{# greeting #}hello 
    {# {% if foo %}bar{% else %} #}{%endraw%}
    ```
2. 여러줄 주석 :{%raw%} {% comment %} ~ {% endcomment %} {%endraw%}

- {%raw%}{% comment %} 태그는 중첩해서 사용할 수 없다.{%endraw%}

    ```
    {%raw%}
    {%comment  "Optional note" %}
    <p>Commented out text here</p>
    {% endcomment %}
    {%endraw%}
    ```

## 5. HTML 이스케이프
> django 는 탬플릿으로 변수를 넘겨 줄 때, HTML 태그에 사용되는 몇몇 문자를 자동으로 이스케이프 한다.    

1. 자동 이스케이프 하는 문자들
    - &lt; : &amp;lt; 으로 변경
    - &gt; : &amp;gt; 으로 변경
    - ' : &amp;39; 으로 변경
    - " : &amp;quot;으로 변경
    - &amp; : &amp;amp; 으로 변경

2. 자동 이스케이프를 방지하는 방법 ( HTML 태그를 입력한 그대로 태그로 이용하고 싶을 때)

    - safe 필터를 사용하는 방법
        ```
        {% raw %}
        # data = "<b>data" 라고 가정할 때,
        This will not be escaped: {% data:safe %}
        {% endraw %}
        ```

    - {%raw%} **{% autoescape %}**  태그를 사용하는 방법 {%endraw%}, <font color='red'>autoescape 태그를 사용하는 것을 권장</font>
        ```
        {%raw%}
        {% autoescape off %}
        Hello {{ name }}
        {% endautoescape %}
        {%endraw%}
        ```

## 6. 템플릿 상속
> block 태그 이용

### 예제 : 부모 템플릿 base.html

```
{%raw%}
<!DOCTYPE html>
<html lang="en">
<head>
    <link rel="stylesheet" href="style.css"/>
    <title>{% block title %}My amazing site{% endblock %}</title>
</head>

<body>
    <div id="sidebar">
        {% block sidebar %}
        <ul>
            <li><a href="/">HOME</li>
            <li><a href="/blog/">Blog</a></li>
        </ul>
        {% endblock %}
    </div>
    <div id="content">
        {% block content %}{% endblock %}
    </div>
</body>
</html>
{%endraw%}
```

### 예제 : 자식 템플릿

```
{%raw%}
{% extends "base.html" %} 

{% block title %}My amazing blog{% endblock %}
{% block content %}
{% for entry in blog_entries %}
    <h2>{{ entry.title }}</h2>
    <p>{{ entry.body }}</p>
{% endfor %}
{% endblock %}
{%endraw%}
```

+ 부모 템플릿을 extends 키워드로 상속함
+ 부모 템플릿 block 중, title 과 content 만 다시 작성하였으므로, sidebar 는 부모의 코드를 그대로 재사용함


### 템플릿 상속은 3단계를 권장

+ 1 단계 : 사이트 전체의 룩앤필을 담고 있는 base.html
+ 2 단계 : base.html 을 상속받는 하위 섹션별 템플릿, base_news.html, base_sports.html 등..
+ 3 단계 : 적절한 2단계 템플릿을 상속받는 개별 페이지

### 템플릿 상속을 정의할 때 유의사항

+ extends 태그는 가장 먼저 나와야 함
+ 1 단계 부모템플릿에 공통된 부분을 많이 코딩하는게 좋음
+ 부모 템플릿의 bock을 그대로 사용하고 싶다면, 자식 템플릿에서 {{block.super}} 를 사용. 부모 템플릿을 사용하면서 내용을 추가 할 때 사용
+ endblock content 처럼 endblock 뒤에 block 이름을 명시 해 주어도 됨