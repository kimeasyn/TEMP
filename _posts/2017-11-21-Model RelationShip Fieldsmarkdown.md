---
layout: post
title: "Model RelationShip Fields"
date: 2017-11-21
category: Django
comments: true
---

#### Model Relationsheip Fields

- 1:N 관계

```python
#app/models.py
class Sub_Model(models.Model):
	main_model = models.ForeignKey(Main_Model) #Main_model에 대해서 1:N의 관계를 가지는 field
```

```
>python manage.py makemigrations study
>python manage.py migrate study
```

> model 변경 후 db에 적용

```python
#app/admin.py
#admin.py에 생성한 db model 추가
from .models import Comment
class CommentAdmin(models.ModelAdmin):
	pass
```

```html
<!-- study_detail.html
	 디테일 화면에 comment 추가-->
{% raw %}
{{ study.comment_set.all }}
{% endraw %}
```

```
> comment = Comment.objects.first()
> comment.study
  = Study.objects.get(id=comment.study_id)
```



#### ForeignKey.on_delete 옵션

- 1:N 관계에서 1에 해당하는 row가 삭제될 시 N의 처리에 대한 동작 설정
- CASCADE: 연결된 ROW 일괄 삭제(default)
- PROTECT: ProtectedError 발생시키며 삭제하지 않음
- SET_NULL: null = True 설정이 되어있을 때 삭제되면 해당 필드들을 null 설정
- SET_DEFAULT: 필드에 지정된 디폴트 값으로 변경
- SET: 값이나 함수의 호출 결과값으로  지정



#### ForeignKey필드의 Related name

- 1:N 관계에서 1에서 N측의 필드로 접근하고자 할때는 필드명_set 으로 접근

```python
study = Study.objects.first()
Comment.objects.filter(study=study)	#방법1
study.commnet_set.all()				#방법2 related_name 사용
```

- 여러개의 앱에서 같은 이름의 model, field 사용시 related_name 중복에러가 발생할 수 있으므로 ForeignKey 설정시 related_name option 지정

```python
#app/models.py
class Study(models.Model):
    user = models.ForeignKey(settings.AUTH_USER_MODEL, related_name='study_user_set')
    ...
    user = models.ForeignKey(settings.AUTH_USER_MODEL, related_name='+')
    # related_name 포기
    # user.study_set.all() 사용 x
    # study.objects.filter(user=user)은 사용가능
```



- N:N 관계

```python
#글 - Tag관계(N:N)
class study(models.Model):
	tag_set = models.ManyToManyField('Tag', black=True)
    #지정할 models 클래스는 문자열로 지정이 가능
    #ManyToManyField는 거의 필수 해제(blank=true)지정

class Tag(models.Model):
	name = models.CharField(max_length=20)

#ManyToManyField는 양쪽 어느 곳에 두어도 상관이 없음
#그러나 다른 model에서도 사용 가능하게끔 relation을 묶어 두는것이 더 효율적
# ex) Tag model을 Study에서도, 새로운 모델 Article에서도 사용 가능하도록 study에 ManyToMany 정의
```

```python
#app/admin.py
@admin.register(Tag)
class TagAdmin(admin.ModelAdmin):
    list_display = ['name']
```

- ManyToManyField 생성시 n:n 관계 표현을 위한 중간 테이블(model1_model2 _set) 자동 생성



- 1:1 관계
- Django에서는 기본으로 django.contrib.auth.models.User 모델 제공
- django.contrib.auth.models.User 모델은 수정 불가 하므로 따로 Profile이라는 model을 만들어 부가정보 저장
- OneToOneField 사용

```tcl
workspace/study> python manage.py startapp accounts
#profile model을 위한 accounts app 생성
#app 생성후 settings.py - installedapp 에 추가 및 url.py 설정	
```

```python
#accounts/models.py
from django.db import models
from django.contrib.auth.models import User


class Profile(models.Model):
    user = models.OneToOneField(User)		#bad case
    address = models.CharField(max_length=100)
    phone_number = models.CharField(max_length=30)
```

> python mange.py migrate 시 Mysql Stirct Mode is not set for database connection 'default' 에러 발생시

```python
#project/settings.py

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'OPTIONS':{
            'init_command': "SET sql_mode='STRICT_TRANS_TABLES'",
             #구문 추가
        }
    }
}
```

```python
#accounts/admin.py
@admin.register(Profile)
class ProfileAdmin(admin.ModelAdmin):
    pass
```

- /admin 페이지에서 profile 객체 추가시 등록된 User 객체 선택가능
- 동일한 User 객체에 대해 여러개의 profile 객체 등록 불가능(OneToOneFiled로 지정되어 있기 때문)
- Foreign key와 비교하면 생설되는 필드명은 같으나 유일성의 차이(OneToOne 의 경우 Unique옵션이 붙음)

```
> profile.user_Id
 =profile.user
# foreign key, onetoon field 등 relation을 맺고있는 필드에 접근시 본 모델 instance를 획득할떄 해당 관계를 맺고 있는 instance를 획득해 오는 것이 아니고 관계를 맺고 있는 객체 접근시 instance 획득(최대한 늦게)
```



#### writer(models.CharField()) 대신 settings.AUTH_USER_MODEL 사용

```python
from django.conf import settings
class Study(models.Model):
    # writer = models.CharField(max_length=100, verbose_name='글쓴이')
    user = models.ForeignKey(settings.AUTH_USER_MODEL)
    ...
    # db migrate시 default 값 지정해줘야 함(user_id)
```

