---
layout: post
title:  "Django shell 사용하기"
date: 2017-10-25
categories: Django
comments: true
---

### django shell 사용하기 

> django-extensions 설치

`>pip install django-extensions`

> project>setting.py에 App 추가

`INSTALLED_APPS = [   'django_extensions', ] # _언더바 주의`

> django-shell 실행

`>python manage.py shell_plus 	#terminal 에서 django 프로젝트 객체들 import되어서 실행`

`>python manage.py shell_plus --notebook 	#jupyter notebook 환경에서 django 프로젝트 객체들 import 되어서 실행`

