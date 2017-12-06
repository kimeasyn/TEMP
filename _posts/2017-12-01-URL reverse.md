---
layout: post
title: "URL reverse"
comments: true
categories: Django
date: 2017-12-01
---

#### URL reverse

- 기존 : urls.py에 URL -> view함수를 매칭

- urls.py에 등록한 url이 바뀌면 다른 파일에서 지정한 url들을 다 바꿔줘야함..(누락 가능성이 많음)

- url 주소는 urls.py에서 한번 지정한 뒤 템플릿 등에서 사용할때는 url reverse를 이용해 사용

- url reverse : url이 변경되더라도 URL Reverse가 변경된 URL을 지정한 name으로 추적하여 URL을 일일히 계산하지 않아도 됨

  ```python
  # app/urls.py
  urlpatterns = [
      url(r'^$', views.list_view, name='list_view'),
      # 기존의 url 패턴에 name을 지정해준뒤 사용
  ]
  ```

  ```html
  <!-- URL reverse의 형식 -->
  <!-- app/templates/app/list.html -->
  <!-- 기존 -->
  <a href="/app/list_view/{% raw %}{{ study.id }}">
  	{% study.title %}{% endraw %}
  </a>
  <!-- url reverse -->
  {% raw %}
  <a href="{% url "list" study.id %}">
    {% study.title %}
  </a>
  {% endraw %}  
  ```

  ​

#### URL Reverse를 수행하는 4가지 함수

- reverse: 매칭되는 url이 없으면 NoReverseMatch 예외 발생

  - return str

    ```python
    reverse('app:model_detail', args=[5])
    # position argument 사용
    reverse('app:model_detail', kwargs={'id':5})
    # keword argument 사용
    ```

- resolve_url : 매칭되는 url이 없으면 **인자문자열** 그대로 리턴

  - 내부적으로 reverse 함수 사용

  - return str

    ```python
    resolve_url('app:model_detail', 5)
    # argument 형식 지정없이 사용
    resolve_url('app:model_detail', id=10)
    ```

- redirect: 매칭 url이 없으면 **인자문자열**을 url로 판단

  - 내부적으로 resolve_url 함수 사용

  - return HttpResponse

    ```python
    redirect('app:model_detail', 5)
    # argument 형식 지정없이 사용
    redirect('app:model_detail', id=10)
    ```

- url template tag: 내부적으로 reverse 함수 사용

  - return str



#### URL namespace

- url도 템플릿과 마찬가지로 중복을 허용하지만 우선순위에 따라 적용하기 때문에 원치않는 url을 로드해올 수도있음

- 위와 같은 현상을 방지하기 위해 템플릿에서 appname/templates/ 와 같은 디렉토리 구조를 가졌던 것과 유사하게 urlpatterns에서 namespace를 지정한다.

  ```python
  # project/urls.py
  urlpatterns = [
      url(r'^appname/', include('appname.urls', namespace='appname')),
      # include 함수에 namespace 인자 추가
      # 보통 중복을 방지하기 위해 app이름과 같은 namespace명 사용
  ]
  ```

  ```html
  <!-- app/model_list.html -->
  {% raw %}
  <a href="{% url "appname:model_detail" appname.id %}"></a>
  {% endraw %}
  <!-- url 사용시 namespace를 지정해서 지정해주어야함 -->
  ```



#### root(/) 주소 redirect 페이지 지정하기

```python
# project/urls.py
from django.shortcuts import redirect

def root_redirect(request):
    return redirect('app:model_list')

urlpatterns = [
    url(r'^$', root_redirect, name='root'),
]
```

```python
# project/urls.py
# lambda 함수 사용
from django.shortcuts import redirect

urlpatterns = [
    url(r'^$', lambda r: redirect('appname:model_list'), name='root'),
]
```

```python
# proejct/urls.py
# django cbv(RedirectView) 사용
from django.views.generic import RedirectView

urlpatterns = [
    url(r'^$', RedirectView.as_view(pattern_name='appname:model_list')),
]
```



#### Model 클래스 안에 get_absolute_url 함수 정의하기

- reverse url을 사용하면서 url을 사용하기 간편해졌지만 url을 사용하는 횟수가 많아지면 일일히 namespace:url name및 argument를 지정해줘야 하는 것이 ~~이것마저~~ 귀찮고 소모적이다

- model내에 get_absolute_url 함수를 작성하여 간편하게 사용하자

- model클래스의 detail view 를 만들게 되면 함께 만들어서 편하게 바로 사용할수 있도록 한다

- Create/Update CBV 사용시 success_url을 지정해 주지 않으면 자동으로 get_absolute_url 함수를 사용하여 이동한다

  ```python
  #app/models.py
  from django.core.urlresolvers import reverse

  class Study(models.model):
      
      def get_absolute_url(self):
          # 함수를 통해 reverse url정보를 얻어온다
          # 이름이 틀리면 안된다
          return reverse('study:study_list', args=[self.id])
  ```

   ```python
  model = Model.objects.get(id=10)
  # get_absolute_url 함수를 작성하면 아래 4개의 결과값이 똑같다
  print(reverse('app:model_list', args=[model.id]))
  print(resolve_url('app:model_list', model.id))
  print(model.get_absolute_url())
  print(resolve_url(model))
  # resolve_url에 model instance를 넘길 때 해당 model class 내에 get_absolute_url 함수가 정의되어 있으면 해당 모델의 get_absolute_url 함수를 실행시킨다. 
   ```

  ​
