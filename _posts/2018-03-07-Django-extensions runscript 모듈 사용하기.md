---
layout: post
title: "Django-extensions runscript로 스크립트 파일 실행"
comments: true
categories: Django
date: 2018-03-07
---

#### Django-extensions runscript 모듈 사용하기

Django 프로젝트에서 beautiful soup 크롤링 등 파이썬 스크립트를 앱을 생성하지 않고 포함하고 있어야 할 경우 

기존에는 

```python
# run: python test.py
import os
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'config.settings')
import django
django.setup()

from app.models import Model1, Model2, Model3
```

와 같은 django 세팅을 지정해주는 스크립트가 포함되어야 했다



이렇게 파일을 구성할 경우 로직과 상관없는 코드가 반복 포함되고

경우에 따라 `setdefault`가 까다로워서 어떻게 바꿀수없나 고민하던 도중 [django-extensions runscript](http://django-extensions.readthedocs.io/en/latest/runscript.html#debugging) 모듈을 찾았다.

```
> pip3 install django-extensions
```

```Python
# settings.py
INSTALLED_APPS = (
    ...
    'django_extensions',
)
```

위와 같이 해당 django 프로젝트에 django-extensions를 설치한 뒤 파일을 구동시킬 경우 main으로 작동할 run() 함수를 작성한다

```Python
#scripts/sciprt.py
def run():
    url = "kimeasyn.github.io"
    .....
```

django 프로젝트의 manage.py 가 있는 폴더에서 `python manage.py runscript script` 명령어로 실행시켜 주면된다(.py 확장자명은 생략)

환경에 따라 `python manage.py runscript -v2 script  `v2` 혹은 v3 옵션을 붙여줘야 하는 경우도 있다.



run함수에 인자값을 전달할 수도 있는데

```python
def run(arg):
    print(arg)
```

`python manage.py runscript script --script-args test` 뒤에 `--script-args ` 명령어를 추가함으로써 run함수에 매개변수를 전달할 수도 있다.



runscript는 꽤 까다로운데 해당 python 파일에 자잘한 문제가 있을경우 제대로 실행되지 않는다.

그리고 명령어를 실행시킨 폴더에서 해당 스크립트를 찾아서 실행시키는 것이 아닌, 

```
> python manage.py runscript script				# script.py 실행
Check for django.contrib.admin.scripts.script
Check for django.contrib.auth.scripts.script
Check for django.contrib.contenttypes.scripts.script
Check for django.contrib.sessions.scripts.script
Check for django.contrib.messages.scripts.script
Check for django.contrib.staticfiles.scripts.script
Check for django_extensions.scripts.script
Check for django.contrib.humanize.scripts.script
Check for livereload.scripts.script
Check for main.scripts.script
```

프로젝트 내의 모든 폴더를 돌면서 해당 스크립트를 찾아 실행시킨다.

여러 폴더에 스크립트 파일을 분산시켜놓을 경우 네이밍 중복이 일어나지 않도록 신경도 써야겠다.