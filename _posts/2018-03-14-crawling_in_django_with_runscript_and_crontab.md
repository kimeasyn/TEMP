---
layout: post
title:  "Django 프로젝트에서 크롤링 스크립트를 django-extensions & crontab 으로 실행하기"
date: 2018-03-14
categories: Django, crawling, crontab, django-extensions, python
comments: true
---

#### Django 프로젝트에서 크롤링 스크립트를 django-extensions & crontab으로 실행하기
   > 장고 프로젝트에서 주기적으로 크롤링 데이터를 활용하면서 좀더 편리하게 작업하기 위한 과정을 설명해놓은 포스트입니다. django, BeautifulSoup, django-extensions runscript, crontab 등 각 키워드들에 대한 설명은 간략하게 넘어가겠습니다.


1. **모델 생성하기** 

   - `django-admin startproject myproj`로 프로젝트 생성, `python manage.py startapp myapp`으로 앱을 생성한 뒤 크롤링 한 데이터를 관리할 모델을 정의한다

     ```Python
     # myapp/models.py
     class News(models.Model):
         title = models.CharField(max_length=200)
     ```

   - model 정의후 migrate

     ```
     > python manage.py makemigrations myapp
     > python manage.py migrate myapp
     ```

2. **크롤링 스크립트 작성하기**

   - 크롤링을 위한 BeautifulSoup 라이브러리 설치

     ```
     > pip install bs4
     ```


   - 간단하게 네이버에서 뉴스 목록중 제목 5개만 추려오는 크롤링 스크립트를 작성한다

     ```python
     # myproject/get_news.py
     from bs4 import BeautifulSoup
     import urllib.request as rq

     # 네이버 뉴스 - e스포츠란의 메인페이지의 html 요소들을 가져온다
     url = "http://sports.news.naver.com/esports/index.nhn"
     res = rq.urlopen(url)
     soup = BeautifulSoup(res, "html.parser")

     # 네이버 뉴스 - e스포츠란의 메인페이지의 뉴스목록 html 요소들을 가져온다
     news_title_list = soup.select("#content > div > div.home_grid > div.content > div.home_article > div.home_news > ul > li > a")
     max_news_count = 5
     # 뉴스 목록중 5개만 크롤링
     news_count = 0

     for title in news_title_list:
         if news_count >= max_news_count:
             break
         else:
             # 뉴스 제목의 a 태그중 title 요소를 출력
             news_title = title.attrs['title']
             print("[%s]" % news_title)
             news_count += 1
     ```

   - 크롤링한 뉴스 제목을 처음에 정의한 모델 인스턴스를 활용해 데이터베이스에 insert 한다.

     ```python
     # myproject/get_news.py
     # django proeject - app 에 소속되지 않은 파이썬 스크립트에서 장고 환경을 사용하기 위한 설정
     import os
     os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'config.settings')
     import django
     django.setup()

     from bs4 import BeautifulSoup
     import urllib.request as rq
     # django model import
     from myapp.models import News

     url = "http://sports.news.naver.com/esports/index.nhn"
     res = rq.urlopen(url)
     soup = BeautifulSoup(res, "html.parser")

     news_title_list = soup.select("#content > div > div.home_grid > div.content > div.home_news > div.home_news > ul > li > a")
     max_news_count = 5
     news_count = 0

     for title in news_title_list:
         if news_count >= max_news_count:
             break
         else:
             news_title = title.attrs['title']
             # News 모델에 크롤링한 데이터를 이용하여 데이터 삽입
             News.objects.create(title=news_title)

             news_count += 1
     ```

3. **django-extensions runscript를 활용하여 스크립트 실행**

   - Django-extensions 가 지원하는 runscript를 사용하면 파이썬 스크립트를 `python manage.py shell` 을 사용한것 처럼 작성할 수가 있다.

     ```python
     # myapp/get_news.py

     # import os
     # os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'config.settings')
     # import django
     # django.setup()
     from bs4 import BeautifulSoup
     import urllib.request as rq
     # django model import
     from myapp.models import News

     def run(args=5):
         url = "http://sports.news.naver.com/esports/index.nhn"
         res = rq.urlopen(url)
         soup = BeautifulSoup(res, "html.parser")

         news_title_list = soup.select("#content > div > div.home_grid > div.content > div.home_news > div.home_news > ul > li > a")
         # 가져올 뉴스 타이틀의 갯수를 runscript 실행시 매개변수로 지정할 수 있다.
         max_news_count = args
         news_count = 0

         for title in news_title_list:
             if news_count >= max_news_count:
                 break
             else:
                 news_title = title.attrs['title']
                 News.objects.create(title=news_title)

                 news_count += 1
     ```

   - Django-extensions runscript는 마치 java의 `public static void main(){}` 처럼 `run` 함수 안에 있는 내용을 실행 시킨다. `run`함수는 `run(args)`처럼 매개변수를 받을 수도 있고 매개변수 없이 선언 및 실행도 가능하다.

   - runscript를 사용하기 위해선 당연히 django-extensions를 설치하고 장고 프로젝트의 `INSTALLED_APP`에 추가해줘야 한다.

     ```
     > pip install django-extensions
     ```

     ```Python
     # setting.py
     INSTALLED_APP = [
       ...,
       'django_extensions' # '-'가 아닌 '_' 이다 주의!
     ]
     ```

   - Django-extensions runscript는 `manage.py`가 있는 상위 폴더에서 실행이 가능하다

     ```
     # 매개변수가 없는 경우
     > python manage.py runscript get_news
     # 매개변수가 있는 경우
     > python manage.py runscript get_news --script-args 10
     ```

   - 위와 같이 실행할 경우 버젼이 맞지않다는 메세지가 뜰 수도 있는데 이럴때는 `python manage.py runscript -v2 get_news`와 같은 식으로 실행시켜 주면된다

   - `runscript get_news.py`라던지 `runscript /myapp/scripts/get_news`와 같이 실행시키면 **안된다** 실행시켜보면 알겠지만 runscript 자체가 해당 프로젝트의 폴더들을 순회하면서 `get_news`와 같은 python파일을 찾아서 실행시키기 때문에 자칫하면 원하지 않는 스크립트가 실행될 수도 있다. 

4. **runscript를 crontab으로 주기적으로 실행하기**

   - 특정 데이터를 특정 주기마다 가지고 오고 싶을때 `cron`만한게 없다.

   - Mac 기준 기본으로 `crontab` 이 설치되어있다.

   - `crontab -e` 명령어를 쳐서 새로운 작업을 추가해준다

     ```
     >crontab -e

     * 10 * * * /path/to/python /Users/path/to/manage.py runscript -v2 get_news 10
     # 매일 10시에 해당 명령어를 실행시킨다.
     ```

   - 주기적으로 실행할 `python manage.py runscript ~~` 명령어를 입력해 주었는데 주의할 점은 `crontab`에서 파일이나 폴더를 지정할 때는  **절대경로**로 지정해야 한다는 것이다. 터미널처럼 `python manage.py`를 입력하면 제대로 실행되지 않는다..

   - 만약 venv를 사용할 경우 `/venv/bin/python`처럼 사용하는 가상환경의 `bin`폴더에 있는 python 파일을 지정해주어야 한다.

     `manage.py `도 마찬가지로 절대경로로 지정해주어야 한다.
