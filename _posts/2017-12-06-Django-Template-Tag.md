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
  {% extends "test.html" %}
  {% for u in users %}
  {% endfor %}			<!-- 구문의 끝 표기 해주어야 함-->
  {% endraw %}
  ```

- 파이썬 로직 사용 불가 템플릿 렌더링 전에 필요한 값을 인자로 전달받음( View -> Template )