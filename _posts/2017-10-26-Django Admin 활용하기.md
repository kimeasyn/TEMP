---
layout: post
title:  "Django Admin 활용하기"
date: 2017-10-26
categories: Django
---

### Django Admin 활용하기

> /admin 페이지에서 모델 객체 이용하기

```python
#App/admin.py
from .models import 모델객체

admin.site.register(모델객체)
```



> /admin 페이지 커스터 마이징

```python
#App/admin.py
class 모델Admin(admin.ModelAdmin):
	list_display = ['컬럼1', '컬럼2', '컬럼3',]

admin.site.register(모델객체, 모델Admin)
```

하면 admin 페이지에서 객체 형식이 아닌 list_display에서 지정한 컬럼들이 표시되는것을 확인할 수 있다.



> 장식자(decorator) 방식

```python
@admin.register(모델객체)
class 모델Admin(admin.ModelAdmin):
	list_display = ['컬럼1', '컬럼2', '컬럼3',]

#admin.site.register(모델객체, 모델Admin)	생략
```



> 함수 활용하기

```python
class 모델Admin(admin.ModelAdmin):
	list_display = ['컬럼1', '컬럼2', '컬럼3', 'get_col_size']
	
	def get_col_size(self, 모델객체):
		return '사이즈는 {}'.format(len(모델객체.컬럼))
	get_col_size.short_description = '사이즈' 		#admin 페이지에서 보여줄 필드명 수정
```

ManyToManyField 는 미지원 하니 함수를 활용하는것을 권장



> Admin Actions

- admin 페이지에서 체크박스 등으로 선택한 인스턴스들에 대해 삭제 이외에 Action 함수를 통해 특수한 기능을 수행 가능하도록 하는 기능
- ModelAdmin 클래스 내 멤버함수로 action 함수 구현
- 멤버action함수.short_description 변수로 action 설명 추가
- ModelAdmin actions 내에 등록

```python
#예제
#app/models.py
class 모델객체(models.Model):
	STATUS_CHOICE = (
        ('h', 'Hide'),
        ('o', 'Open'),	
	)
	status = models.CharField(max_length=1, choices=STATUS_CHOICE)
```

> python3 manage.py makemigrations --STATUS_CHOICE에 있는 값중 하나 기본값으로 지정
>
> python3 manage.py migrate

```python
@admin.register(Study)
class StudyAdmin(admin.ModelAdmin):
    #해당 모델 객체에 action 함수 지정
    actions = ['make_open',]

    #action 함수 생성
    def make_open(self, request, queryset):
        updated_count = queryset.update(status='o')
        self.message_user(request, '{}건의 글을 open 하였습니다.'.format(updated_count))
```

