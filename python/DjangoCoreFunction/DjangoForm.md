---
sort: 4
---

# Django 의 폼 처리 기능
> 장고는 FORM 의 데이터를 전송 할 때는, POST 방식만 사용    
> 검색 폼 같은 경우는 GET 방식을 사용하여 북마크 등을 용이하게 함   

## 1. Django 의 폼 기능

1. 폼이란?
- HTML의 <form> 태그
- django의 Form 클래스
- 서버로 제출되는 구조화된 데이터

2. Django의 폼 클래스
- 폼 클래스의 필드는 html의 input 엘리먼트와 매핑됨
- 폼 클래스의 필드 역시 클래스 임
- 데이터가 없는 폼 클래스를 언바운드 폼이라고 함. html에서 보여질 때 비어있거나 디폴트 값으로 채워짐
- 데이터가 있는 폼 클래스를 바운드 폼이라고 함. 데이터 유효성 검사에 사용됨
- is_valid() 함수를 내부적으로 실행 시켜, 유효성 검사를 성공하면 True를 반환하고, 검사에 성공한 값을 cleaned_data 속성에 넣음

3. 폼의 필드 클래스의 기능
- 폼 데이터를 저장하고 있음
- 폼이 제출되면 자신의 유효성을 검사
- 저장하는 데이터의 종류에 따라 자신의 타입을 정함
- 폼 필드는 HTML에서 위젯으로 표현
- 필드 타입마다 디폴트 위젯 클래스를 가지고 있으며, 오버라이딩 될 수 있음

4. 폼을 포함한 템플릿 코드의 렌더링 절차
    1. 렌더링 할 객체를 뷰로 가져오기 (ex. 데이터베이스의 객체)
    2. 그 객체를 템플릿 파일로 넘겨주기
    3. 템플릿 문법을 처리하여 html 언어로 변환하기

## 2. 폼 클래스의 처리
> 폼클래스로 폼 생성 -> 뷰에서 폼클래스 처리 -> 폼 클래스를 템플릿으로 변환     

### (1) 폼클래스로 폼 생성

- polls/forms.py 에 폼클래스 선언
- django.forms.Form 의 자식 클래스로 생성
- 폼 클래스는 모든 필드에 대해 유효성 검사를 실행하는 is_valid() 메소드를 내장하고 있다.

    ```python
    from django import forms

    class NameForm(forms.Form):
        your_name = forms.CharField(label='Your name', max_length=100)
    ```

### (2) 뷰에서 폼클래스 처리

- 뷰에서 폼을 보여주기 위한 get 방식과 폼을 제출하기 위한 post 방식을 구현한다.   

    ```python
    from django.shrtcuts import render
    from django.http import HttpResponseRedirect

    def get_name(request):
        # post 요청 처리
        if request.method == 'POST':
            form = NameForm(request.POST)
            if form.is_valid():
                # 폼 데이터가 유효하면, 데이터를 cleaned_data에 복사하고, '/thanks/'로 리다이렉트 함
                new_name = form.cleaned_data['name']
                return HttpResponseRedirect('/thanks/')
        else:
            form = NameForm()
        # get 방식이거나 폼이 유효하지 않을 때, 'name.html' 템플릿으로 값은 비어 있고 필드만 생성되어 있는 폼 객체를 전달한다.
        return render(request, 'name.html', {'form':form})
    ```


### (3) 폼 클래스를 템플릿으로 변환

- 폼 객체를 템플릿에서 표현하는 방법은 {{ form }} 이외에 3가지가 더 있다.

    1. {% raw %}{{form.as_table}} : form의 각 필드를 <tr> 태그로 감싸서 렌더링, {{ form }} 과 같음{% endraw %}
    2. {% raw %}{{form.as_p}} : form의 각 필드를 <p> 태그로 감싸서 렌더링{% endraw %}
    3. {% raw %}{{form.as_ul}} : form의 각 필드를 <li> 태그로 감싸서 렌더링{% endraw %}

- 폼 객체는 필드만 전달하므로, <form> 요소와 &lt;input type='submit'&gt; 요소, 그외 기타 <table>, <ul> 등은 직접 작성 해야 한다.
 
  - name.html
    ```
    {%raw%}
    <form action='/your_name/' method='post'>
    {% csrf_token %}
    {{ form }}
    </form>
    {%endraw%}
    ```

     ↓ 렌더링
    
    ```
    {%raw%}
    <form action='/your_name/' method='post'>
    <label for="your_name">Your name : </label>
    <input id='your_name' type='text' name='your_name' maxlength='100'>
    <input type='submit' value='Submit'>
    </form>
    {%endraw%}
    ```

