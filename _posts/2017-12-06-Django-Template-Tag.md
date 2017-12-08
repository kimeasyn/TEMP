---
layout: post
date: 2017-12-06
title: "Django Template Tag"
comments: true
categories: Django
---

> 본 포스트는 AskDjango의  [장고 기본편 ](https://nomade.kr/vod/django)을 보고 정리한 내용입니다.

#### Django의 철학

- 다른 프레임워크의 MVC와 유사한 **MTV** 사용
- Fat **M**odel: 비즈니스 로직을 최대한 model에서 구현하자
- Stupid **T**emplate:  비즈니스 로직을 최대한 지양하자
- Thin **V**iew: 해당 요청을 처리할때 필요한 인자처리 및 model인스턴스 획득 등 정도의 기능만 하도록 지향



#### Template Engines 몇가지

- Django Template Engine: Django 기본 지원 템플릿 엔진
- Jinja2: 써드파티 엔진이었으나 최근에 최소한의 지원이 내장됨
  - Django Template Engine과 문법이 유사
  - Django-jinja 추천
- Mako, Hamlpy



#### Django Tmeplate 문법

- `{% raw %}{% %}{% endraw %}` 사용

  ```html
  {% raw %}
  {% extends "test.html" %}	<!-- Django Template Tage // 필요한 인자 -->
  	{% for u in users %}
  		...
  	{% endfor %}			<!-- 일부 문법의 경우 구문의 끝 표기 해주어야 함-->
  {% endraw %}
  ```

- 파이썬 로직 사용 불가 템플릿 렌더링 전에 필요한 값을 인자로 전달받음( View -> Template )

- 인자를 넘길때는 `()과 쉼표,`를 사용하지 않을뿐 파이썬의 안자 넘기는 형식과 유사하다(positional argument, keyword arguments)



#### 변수 사용

```html
{% raw %}
{{ val_name }}			<!-- 변수 이름으로 사용 -->
{{ dic.key }}			<!-- = dic['key'}-->
{{ object.attr }}
{{ object.function}}	<!-- 인자없는 함수 사용 가능(callable) -->
{{ list.0 }}			<!-- list의 index로 사용 가능 = list(0)-->
{% endraw %}
```



#### Django Template Tag

- Dajgno Template용 Tag

- `{% raw %} {% %} {% endraw %}` 하나만 쓰기도 하고 `{% raw %}{% for i in range(10) %}{% endif %}{% endraw %}`처럼 2개 이상으로 쓰이기도 한다

- [내장 Tag](https://docs.djangoproject.com/ko/1.11/ref/templates/builtins/)가 존재하며 별로 커스텀 Tag 추가 가능

- comment tage및 `#` 을 이용해 주석 처리 가능

  ```html
  {% raw %}
  {# 주석1 #}
  {% comment "주석에 대한 설명(달지 않아도 됨)" %}
  	주석2
  	주석3
  	주석4
  {% endcomment %}
  {% endraw %}
  ```

  - Server단의 주석임 Client단에서의 javascript의 주석`/**/`이나 html 주석`<!-- -->`과 다름

