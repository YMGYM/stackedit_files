---
layout: post
title: "dialogflow 기초"
categories:
  - 아픈지렁이
tags:
  - Django
  - 챗봇
---

# Dialogflow와 Django 연결하기

> Dialogflow는 구글에서 제공하고 있는 자연어 기반 챗봇 서비스이다.
> 구글 어시스턴트와 페이스북 메신저 등 다양한 연동 기능을 제공하고 있다.
> Django를 통해 기본 서버를 구축하고, fulfillment 기능을 통해 서버와 연동하는 방법을 진행하려 한다.

> 서비스를 제작할 때 다음 게시글을 많이 참고했다.
> https://www.pragnakalp.com/dialogflow-tutorial-create-fulfillment-webhook-using-python-django/


## Django App 만들기

시작은 언제나 하던 대로, `startapp`을 이용해 앱을 만들어 준 뒤에, `setting.py`에 추가해주자.
나는 연세우유의 제품 중 하나인 '스위플' 을 소재로 잡았다.

`urls` 에는 `action/` 이란 주소로 왔을때 app으로 넘겨주도록 설정했다.

`[프로젝트명]/urls.py`
~~~python
from django.contrib import admin
from django.urls import path, include
from sweeple import urls as sweeple_url

urlpatterns = [
    path('admin/', admin.site.urls),
    path('action/', include(sweeple_url)),
]
~~~

`sweeple/urls.py`
~~~python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.action, name="acton"),
]
~~~


## `views.py` 꾸미기

`view`의 경우는 이야기가 조금 복잡해졌다.

1. 우선 dialogflow 서버에서 POST방식으로 리퀘스트를 보냈을 때,  csrf 에러를 일으키지 않아야 하고,
2. JSON 형태의 데이터를 반환해 주어야 한다.


<!--stackedit_data:
eyJoaXN0b3J5IjpbMzg4ODEyMTg5LC0xOTM0Njg3MDA3XX0=
-->