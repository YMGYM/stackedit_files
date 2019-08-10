
---
layout: post
title: "장고 CRUD"
categories:
  - 장고공부
tags:
  - Django
---



# 장고 CRUD
> 
> 공백기(?) 동안 멋쟁이사자처럼 장고 인강을 들으며 CRUD 공부를 했다.
> 역시 장고 사이트의 설명보단, 멋사 인강이 훨씬 나은것 같다..
> 스스로 공부를 해 낼 수 있는 힘을 길러야겠다고 생각하긴하는데..
>  
> 이 포스팅에서는 멋사 인강을 들으면서 배운 내용을 복습하는 차원에서  CRUD를 만들어 보면서, 


## 프로젝트 생성

장고 프로젝트를 생성한다.
사용하고 있는 컴퓨터 특성상 IDE를 사용할 수 없으므로, 'goorm.io'의 '구름 IDE' 를 이어서 사용하려고 한다.
구름 IDE에서는 프로젝트 생성까지 편하게 할 수 있다.

## 앱 생성

원하는 디렉토리로 가서 앱을 생성한다. ``python manage.py startapp [appname]``

`setting.py`의 INSTALLEDE_APPS 에 생성한 앱 이름을 추가한다.


## URL 설정
프로젝트 이름과 똑같은 폴더에 `urls.py`에 url을 설정한다.

~~~ python
from django.contrib import admin
from django.urls import path, include
# include 추가. 내가 생성한 앱의 urls.py로 request를 전송하는 듯 하다.
from crud import urls as crud_urls
# crud 앱에서 urls 추가

urlpatterns = [
    path('admin/', admin.site.urls),
    path('crud/', include(crud_urls)),
]
~~~
crud 앱에 urls 가 없기 떄문에 `urls.py`생성 후 다음 입력
~~~python
from django.urls import path
from . import views

appname="crud"
urlpatterns =[
    path('', views.list, name = "list"),
]
~~~

같은 앱에서 views를 불러와 연결시킬 수 있다.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEzNDcyMDQxOTQsOTA0NjIwOTg4XX0=
-->