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

### csrf 오류 방지하기

ROR에서는 csrf 관련 체크 명령을 삭제하는 방법으로 오류를 '회피' 할 수 있었다.
이왕이면 이런 방법이 아니라 token을 발급하는 방법으로 가고 싶었지만,

dialogflow에서 request를 보낼 때 `"responseId"`라는 이름으로 임의의 보안 값을 발생시키는 것으로 보인다.
따라서 django 에서도 비슷한 방법을 사용해 도 될 것 같다.

csrf 체크를 회피하는 방법은 다음과 같았다.

`views.py`
~~~python
...
from django.views.decorators.csrf import csrf_exempt
...

@csrf_exempt
def action(request):
...
~~~

### JSON 처리하기

레일즈와는 달리, 장고는 JSON을 바로 처리할 수 없다. 관련 모듈을 `import`해줘야 한다.
방법은 매우 쉽고 간단했다.
`views.py`
~~~python
...
from django.http import JsonResponse
import json

...
 return JsonResponse([JSON_name], safe=False)
~~~
`safe=False` 속성은, `TypeError`를 방지하기 위해 쓰인다.
JSON 형태를 딕셔너리 형태로 오해해 오류를 뱉어내는걸 방지하기 위해 쓰인다고 한다.


## fulfillment 설정

### intent 만들기

우선, 스위플의 재고를 물어보는 `intent`를 만들고, `enable webhook call for this intent`를 활성화 시켰다.(맨 아래)

### fulfillment 설정

fulfullment 탭에 들어가, `webhook` 을 켜고, 사이트 주소를 입력한다.

#### 이부부분에서 한참 안됐는데..
사이트 주소는 `[내 url]/action/` 식으로 uri를 꼭 적어 주어야 한다.
카카오톡 api 는 `/keyboard`를 알아서 붙여서 request를 했었었는데..


## `views.py`설정

마지막으로, request를 했을 때, 응답을 보내 보자.

`views.py`를 다음과 같이 수정하면 된다
~~~python
...
def action(request):
    # if request.method == 'post':
        fulfillmentText = {'fulfillmentText': '테스트'}
        return JsonResponse(fulfillmentText, safe=False)

~~~



자세한 사항
[https://developers.google.com/actions/build/json/dialogflow-webhook-json]
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTA5MDU2NzMzMCwtODk5MDAyMTQ3LC0yNz
U2NDM1NzQsLTE5MzQ2ODcwMDddfQ==
-->