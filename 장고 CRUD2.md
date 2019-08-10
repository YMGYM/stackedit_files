
---
layout: post
title: "장고 CRUD(2)"
categories:
  - 장고공부
tags:
  - Django
---



# 장고 CRUD-2
> 
> 레일즈의 scaffold 형태로 제작을 해 보는게 프로젝트의 목표지만, 제대로 하고 있는지 모르겠다.
> 레일즈는 rails way가 있지만 장고는 django way가 있을 수도 있는 것 아닐까..?
> 그래도 일단 아는 것 부터 차곡차곡 완성하는게 맞을 것 같으니까.


## admin 생성하기

장고는 내부에 admin 툴을 기본 탑재하고 있었다. ` /admin/`으로 쉽게 접속할 수 있다는 것이 좋은듯.

어드민 계정을 생성하기 위해 터미널에 다음을 입력한다.

~~~
$ python manage.py createsuperuser
Username (leave blank to use 'root'): rootuser
Email address: aa@aa.com
Password:
Password (again):
Superuser created successfully.

~~~
admin 사이트에 접속 할 경우 내가 생성한 모델은 나오지 않기 때문에 모델을 register해 주어야 한다.
이는 `admin.py`에서 진행한다.

~~~ python
from django.contrib import admin
from .models import Post

admin.site.register(Post)
~~~

### admin에서 특정 행 이름 보기
사이트에서 확인해 본 결과 [모델명 object(n)]의 형태로 표시가 되는데, 이를 내가 원하는 형태로 바꾸고 싶은 경우, 모델에 다음과 같이 추가하면 된다
].
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjE1ODcyMzExLDczMDk5ODExNl19
-->