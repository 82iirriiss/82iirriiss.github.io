---
sort: 5
---

# 클래스 형 뷰
> 이전까지는 뷰를 함수로 작성하였으나, 클래스형 뷰를 사용하면 장점이 많음       

## 1. 클래스형 뷰의 첫단계 - URLconf 설정
- urls.py
- URLconf에 함수가 아닌 클래스 뷰를 사용 할 것이라는 표시를 해 주어야 함
- MyView 라는 클래스 뷰를 사용한다 가정하면,
    ```python
    from django.urls import path
    from polls.views import MyView

    urlpatterns = [
        path('about/', MyView.as_view())
    ] 
    ```
    + **as_view()** : URL해석기는 기본적으로 데이터를 함수로 전달하므로, 클래스로 진입하기 위한 as_view() 내장 메소드가 필요. '진입 메소드' 라고 함
    + **dispatch()** :  내장 메소드. as_view()에서 인스턴스를 생성하고, 인스턴스 내에서 GET/POST 메소드 요청을 중계하는 역할. 메소드가 없을 땐, **HttpResponseNotAllowed** 익센션 발생시킴

## 2. 클래스형 뷰 작성 : MyView 클래스
- views.py

    ```python
    from django.http import HttpResponse
    from django.views.generic import View

    class MyView(View):
        def get(self, request):
            # 뷰 로직 작성
            return HttpResponse('result')
    ```

## 3. 클래스형 뷰의 메소드 구분
- GET, POST 등 HTTP 메소드에 따른 처리 기능이 지정된 메소드명으로 구분 할 수 있음
    + def get():
    + def post():
    + def head():
- 클래스형 뷰로 HEAD 메소드 코딩 예제
    ```python
    # views.py
    from django.http import HttpResponse
    from django.views.generic import ListView
    from books.models import Book

    class BookListView(ListView):
        model = Book

        def head(self, *args, **kwargs):
            last_book = self.get_queryset().latest('publication_date')
            response = HttpResponse('')
            response['Last-Modified'] = last_book.publication_date.strftime('%a, %d %b %Y %H:%M:%S GMT')
            return response
    ```

## 4. 클래스형 뷰의 상속기능
- 먼저 **제네릭뷰**에 대해 알아야 함. 클래스 뷰의 대부분이 제네릭 뷰를 상속받고 있음
- **제네릭뷰** : 모델, 뷰, 템플릿 개발과정의 공통된 단순반복 작업들을 추상화하고, 장고에서 미리 만들어 제공해주는 클래스형 뷰를 의미

- 제네릭뷰의 예제
    + some_app/urls.py
        ```python
        from django.urls import path
        from some_app.views import AboutView

        urlpatterns = [
            path('about/', AboutView.as_view())
        ]
        ```
    + some_app/views.py
        ```python
        from django.views.generic import TemplateView

        class AboutView(TemplateView):
            template_name = "about.html"
        ```    

- 또는,
    + 간단히 호출만 하는 뷰라면, urls.py에서 한번에 처리가 가능
        ```python
        from django.urls import path
        from django.views.generic import TemplateView

        urlpatterns = [
            path('about/', TemplateView.as_view(template_name='about.html'))
        ]
        ```
        - template_name 은 TemplateView 클래스에 정의된 클래스 속성인데, **오버라이드** 하고 있다.

## 5. 클래스형 제네릭 뷰의 종류
> 참고 : <https://docs.djangoproject.com/en/2.1/ref/class-based-views>
- Base View : 뷰 클래스 생성하고 다른 제네릭 뷰의 부모 클래스를 제공하는 기본 클래스 뷰
- Generic Display View : 객체의 리스트를 보여주고, 특정 개체의 상세정보를 보여줌
- Generic Edit View : 폼을 통해 객체를 생성, 수정, 삭제 하는 기능 제공
- Generic Date View : 날짜 기반 객체를 연/월/일 페이지로 구분하여 보여줌

|제네릭 뷰 분류 |제네릭 뷰 이름 |뷰의 기능 또는 역할    |
|---------------|---------------|-----------------------|
|Base View      |View<br>TemplateView<br>RedirectView       |최상위 뷰<br>주어진 템플릿을 렌더링<br>주어진 URL을 리다이렉트 시킴              |
|Generic Display View   |ListView<br>DetailView     |조건에 맞는 여러개의 객체를 보여줌<br>객체 하나에 대한 상세한 정보 제공        |
|Generic Edit View      |FormView<br>CreateView<br>UpdateView<br>DeleteView     |주어진 폼을 보여줌<br>객체를 생성하는 폼을 보여줌<br>기존객체를 수정하는 폼을 보여줌<br>기존객체를 삭제하는 폼을 보여줌        |
|Generic Date View  |ArchiveIndexView<br>YearArchiveView<br>MonthArchiveView<br>WeekArchiveView<br>DayArchiveView<br>TodayArchiveView<br>DateDetailView |조건에 맞는 여러개의 객체 및  그 객체들에 대한 날짜정보를 보여줌<br>연도가 주어지면 그 연도에 해당하는 객체들을 보여줌<br>연,월이 주어지면 그에 해당하는 객체들이 보여짐<br>연도와 주차가 주어지면 그에 해당하는 객체들을 보여줌<br>연,월,일이 주어지면 그 날짜에 해당하는 객체들을 보여줌<br>오늘 날짜에 해당하는 객체들을 보여줌<br>연,월,일,기본키가 주어지면 그에 해당하는 특정 객체 하나에 대한 상세한 정보를 보여줌  |

## 6. 클래스형 뷰에서 폼 처리
- Get 처리, 유효한 Post 처리, 유효하지 않은 Post 처리

### (1) 클래스형 뷰로 폼을 처리하는 예시 - View 
    
```python
from django.http import HttpResponseRedirect
from django.shortcuts import render
from django.views.generic import View

from .forms import MyForm

class MyFormView(View):
    form_class = MyForm
    initial = {'key':'value'}
    template_name = 'form_template.html'

    def get(self, request, *args, **kwargs):
        
        form = self.form_class(initial=self.initial)
        return render(request, self.template_name, {'form':form}) # get 방식 요청은, 비어있는 form 제공

    def post(self, request, *args, **kwargs):
        form = self.form_class(request.POST)

        if form.is_valid():
            # cleaned_data로 관련로직 처리
            return HttpResponseRedirect('/success/') # 유효한 form 처리, 성공적으로 리다이렉트

        return render(request, self.template_name, {'form':form}) # 유효하지 않은 form 처리, 이전에 입력했던 값들을 그대로 template 에 보여줌

```

### (2) FormView 제네릭뷰로 폼을 처리하는 예시 - FormView
   
```python
from .forms import MyForm
from django.views.generic.edit import FormView

# FromView에 이미 정의되어 있으므로, get(), post()가 불필요함
class MyFormView(FormView):
    form_class = MyForm
    template_name = 'form_template.html'
    success_url = '/thanks/'

    def form_valid(self, form):
        #cleaned_data로 관련 로직 처리
        return super(MyFormView, self).form_valid(form)
```

- FromView에 이미 정의되어 있으므로, get(), post()가 불필요함
- form_class : 사용자에게 보여줄 form, forms.py에 정의한 폼 클래스
- template_name : 렌더링할 템플릿 파일 이름
- success_url : MyFormView 가 정상적으로 처리되었을 때 리다이렉트 할 주소
- form_valid() : 유효한 폼 데이터로 처리할 로직. super()를 사용하면, success_url로 리다이렉트까지 처리됨
