---
layout: post
title: "Django Bulk Insert"
comments: true
categories: Django SQL mysql
date: 2018-01-31
---

#### Bulk Insert

- Multi value를 통해 insert 하는 방식

```sql
insert
  into 'TABLE'
values('a0', 'b0', 'c0')
	, ('a1', 'b1', 'c1')
	, ('a2', 'b2', 'c2')
	, .....
```

- 한개의 insert문이 하나의 transaction으로 묶여 실행
- 하나의 row라도 틀린 값 입력 등으로 insert가 실패하면 해당 bulk insert 전부 rollback



**Django bulk create**

- bulk insert를 [bulk insert()](https://docs.djangoproject.com/en/dev/ref/models/querysets/#django.db.models.query.QuerySet.bulk_create) 함수로 queryset 제공

```python
# insert row가 들어갈 list 객체
insert_user_list = []

# value row1 생성
user = User(username='test', password='password')
insert_user_list.append(user)

# value row2 생성
user2 = User(username='test2', password='password)
insert_user_list.append(user2)

# bulk insert 생성
User.objects.bulk_create(insert_user_list)
```
