---
sort : 6
---

# 템플릿 개발

- 북마크 목록 : bookmark/templates/bookmark/bookmark_list.html

    ```python
    {% raw %}
    {% extends 'base.html' %}
    {% block title%}bookmark_list.html{% endblock %}

    {% block content %}
            <h1>Bookmark List</h1>
            <ul>
                {% for bookmark in object_list %}
                    <li><a href="{% url 'bookmark:detail' bookmark.id %}">{{bookmark}}</a></li>
                {% endfor %}
            </ul>
    {% endblock content%}
    {% endraw %}
    ```
    - views.py 에서 전해준 object_list 컨텍스트 변수를 사용하고 있다.
    - `{% raw %}{% url %}{% endraw %}`를 통해 bookmark/<bookmark.id>(자세히 보기) url 링크를 걸어주었다.
    - `{% raw %}{{bookmark}}{% endraw %}` 는, models.py 의 Bookmark 클래스를 정의 할 때 작성했던 함수 `__str__()` 의 반환으로 bookmark.title 을 반환한다.

- 북마크 상세보기 : bookmark/templates/bookmark/bookmark_detail.html

    ```python
    {% raw %}
    {% extends 'base.html' %}

    {% block title %}bookmark_detail.html{% endblock title %}

    {% block content %}
                <h1>{{object.title}}</h1>
                <ul>
                    <li>URL : <a href="{{object.url}}">{{object.url}}</a></li>
                </ul>
    {% endblock content %}
    {% endraw %}
    ```
    - base.html은 추후에 만들 네이게이션바 이다.
    - views.py에서 전달해준  object 컨덱스트 변수를 사용하고 있다.
