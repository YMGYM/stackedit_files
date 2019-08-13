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
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTkwMTQ0MTUzNSwtMTkzNDY4NzAwN119
-->