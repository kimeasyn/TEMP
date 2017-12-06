---
layout: post
title: "Django Template Loader"
comments: true
categories: Django
date: 2017-11-30
---

#### Django Template Loader - django의 템플릿 파일 관리



**Django Template Loader**

- 프로젝트 내 다수 디렉토리 중에서 지정 상대경로를 갖는 템플릿을 찾음
- app_directories.Loader와 filesystem.Loader
- django 서버가 시작할 때 위 Loader를 통해 템플릿 디렉토리가 후보 디렉토리 리스트를 작성



**Template 파일을 활용하는 함수**

- render: HttpResponse 객체를 리턴
- render_to_string: 렌더링한 문자열을 리턴



**app_directories.Loader**

- settings.INSTALLED_APPS에 설정된 APP 디렉토리 내에 Template 경로에서 template 파일을 찾음
- app 디렉토리 별로 각각 template 파일을 위치시키는 것이 관리성이 좋음

```python
# project/settings.py
TEMPLATES = [
  {
    ....
    'APP_DIRS': True,	#app_directories.Loader 사용 지정
    ....
  }
]
```



**filesystem.Loader**

- 프로젝트 전반적으로 쓸 템플릿은 특정 앱 내부 경로가 아닌 별도의 경로에 저장이 필요

```python
# project/settings.py
TEMPLATES = [
  {
    ....
      'DIRS': [
          os.path.join(BASE_DIR, 'project name', 'templates'),
      ]
    ....
  }
]
```



**Django가 template을 찾는 원리**

```python
# project/settings.py
TEMPLATES = [
  {
    ....
      'DIRS': [
          os.path.join(BASE_DIR, 'project name', 'templates'),
      ],
      'APP_DIRS': True,
    ....
  }
]
```

project 내에 app1, app2의 앱이 존재하고 위와 같이 templates 세팅이 되어있을 경우 django가 인식하는 template 폴더는 아래와 같다

> 1. app1/templates	
> 2. app2/templates
> 3. project/templates

`render(request, 'test.html')`과 같은 코드가 있을 때 django는 위 폴더마다 'test.html'이라는 이름을 가진 템플릿 파일을 찾는다.

1번 폴더에 없을 경우 2번 폴더로, 2번 폴더에도 없으면 마지막 3번 폴더로, 모든 템플릿 폴더에도 없을 경우 템플릿을 찾을수 없다는 에러를 발생시킨다.

그런데 만약 test.html 파일이 app1/templates 에도 있고 app2/templates 에도 있다면 경우에 따라 app2의 test.html을 불러오고 싶은데 app1의 test.html파일을 불러온다던가 하는 경우가 생겨버린다.

`app1_test.html` `app2_test.html` 과 같이 파일명으로 구분해줘도 되지만 파일명이 너무 길어져 관리 측면에서 좋지 않다.

따라서 templates 폴더 및에 하위 폴더로 app 이름의 폴더를 또 하나 만들어서 관리해 주는것이 용이하다.

`app1/templates/app1/test.html` `app2/template/app2/test.html`

app1의 test.html 사용시: `render(request, 'app1/test.html')`

이런식으로 디렉토리를 만들어서 관리하게 되면 test.html은 앞에 `app/ `경로가 추가되어 사용하기 때문에 중복되어 로딩할 수 없다.

>django는 한 프로젝트 내에 중복된 app이름을 가질 수 없기 때문에 위와 같은 경로로 작성하면 중복된 템플릿 파일을 가질 수 없다.



**render_to_string 사용**

- template 파일은 주로 html로 사용하지만 css, js, txt등 문자열로 된 파일이라면 무엇이든 가능하다

  ```html
  <!-- accounts/templates/accounts/welcome.txt-->
  {% raw %}
  {{ user }}님 지금은 {{ time }} 입니다.
  {% endraw %}
  ```

  ```python
  # django shell notebook 실행
  # (python manage.py shell_plus --notebook)
  from django.template.loader import render_to_string
  render_to_string('accounts/welcome.txt', {'user': '철수', 'time': '2017-11-30 19:00'})
  ```

  `철수님 지금은 2017-11-30 19:00 입니다.`

