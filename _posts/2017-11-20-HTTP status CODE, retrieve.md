---
layout: post
date: 2017-11-20
title: "HTTP status CODE, retrieve"
category: Django
comments: true
---

#### HTTP status code

**HTTP 통신시 결과에 따른 응답 상태 코드**

- 200: 성공
- 302:임시 URL로 이동(redirect)
- 404:서버가 요청한 페이지를 찾을 수 없음.(Not found)
- 500:서버 오류 발생(Server Error)



#### code별 응답하기

- 200

  ```python
  from django.http import HttpResponse, JsonResponse
  from django.shortcuts iimport render

  def view1(request):
      return HttpResponse('안녕하세요.')
     
  def view2(request):
      return render(request, 'template.html')

  def view3(request):
      return JsonResponse({'hello':'world'})
  ```

- 302

  ```python
  from django.http import HttpResponseRedirect
  from django.shortcuts import redirect, resolve_url

  def view1(request):
      return HttpResponseRedirect('/blog/')
  	#이동할 url string 지정
      
  def view2(request):
      url = resolve_url('blog:post_list')	# url reverse 적용
      return HttpResponseRedirect(url)
      
  def view3(request):
      return redirect('blog:post_list')
  ```

- 404

  ```python
  from django.http import Http404, HttpResponseNotFound
  from django.shortcuts import get_object_or_404

  def view1(request):
      raise Http303	#Exception class
      
  def view2(request):
      post = get_object_or_404(ModelClass, id=100)
      #해당 id를 가진 ModelClass instance가 없을시 http 404 Exception 발생
      
  def view3(request):
      return HttpResponseNotFound()	#잘 사용하지 않음
  ```

- 500

- 서버에서 요청 처리 중에 예기치 못한 오류(코드오류, 세팅오류)가 발생할 경우

- (Index err, key err, django.db.models.ObjectDoesNotExist...)

  ```python
  from app.models import ModelClass

  def view1(request):
      name = ['Jhon', 'Ann'][100]
      
  def view2(request):
      instance = ModelClass.objects.get(id=100) #없는 id에 접근할 경우 DoesNotExist예외 발생
  ```



#### List화면에서 제목 클릭시 detail 화면으로 이동 구현

```python
#app/urls.py
urlpatterns = [
    url(r'^$', views.study_list),
    url(r'^(?P<id>\d+)/$', views.study_detail),
  #views.py의 study_detail이라는 함수는 id라는 필드값을 인자로 받을 수 있어야 함
]
```

```python
#app/views.py
def study_detail(request, id):
    try:
        study = Study.objects.get(id=id)
    except Study.DoesNotExist:
        raise Http404
        #404로 지정해 주지 않으면 없는 id로 조회시 500에서(코드,서버오류 발생)
        
    return render(request, 'study/study_detail.html', {
        'study': study,
    })
```

```html
<!-- app/templates/studyapp/study_detail.html -->
<body>
    {% raw %}
    <h1>{{ study.title }}</h1>
    {{ study.content|linebreaks }}
  	<!-- 개행문자 적용 시키기 위해 linebreaks 추가 -->
    {% endraw %]
</body>
```

```html
<!-- app/templates/studyapp/study_list.html -->
<!-- a 링크 추가 -->
    {% raw %}
		<a href="/study/{{ study.id }}">
            {{ study.title }}
    </a>
    {% endraw %}
```

```python
#app/views.py
from django.shortcuts import get_object_or_404
def study_detail(request, id):
    #try:
    #    study = Study.objects.get(id=id)
    #except Study.DoesNotExist:
    #    raise Http404
        #404로 지정해 주지 않으면 없는 id로 조회시 500에서(코드,서버오류 발생)
    study = get_object_or_404(Study, id=id)   
    #위에 try~except 문과 같은 동작
    #id가 id인 Study 객체가 있으면 get 없으면 raise 404
    
    return render(request, 'study/study_detail.html', {
        'study': study,
    })
```







