
---
layout: post
title: "장고 CRUD"
categories:
  - 장고공부
tags:
  - Django
---



# 장고 CRUD-1
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

app_name="crud"
urlpatterns =[
    path('', views.list, name = "list"),
]
~~~

같은 앱에서 views를 불러와 연결시킬 수 있다.
app_name을 사용해서 url의 이름을 지정했다. rails의 컨트롤러 기능과 유사한 것 같다.
)
`views.py`에 list액션이 없기 때문에 생성한다

~~~python
def list(request):
    return render(request, 'list.html')
~~~
렌더링할 html파일을 만들기 위해 templates라는 폴더를 만들고 list.html을 생성했다.

서버를 돌려서 `/crud/`에 접속해 보면 `list.html`이 렌더링된다.


## 모델 설정
모델을 만들어 보자. 모델의 기본적인 개념은 rails 의 그것과 많이 비슷한듯 하다.

모델에 관련된 설정은 `models.py`가 해준다.
~~~python
from django.db import models

# Create your models here.

class Post(models.Model):
    # Model을 상속받아 Posts클래스 생성
    # 단수 모델을 만드는 것이 차이점?
    
    # --- 모델에 들어갈 column -----
    title = models.CharField(max_length=100)
    content = models.TextField()
    
    # 내용에 관계 없이 필수로 들어가야하는 column
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
~~~
다음과 같이 입력 후에 터미널에 입력

~~~
python manage.py makemigrations

python manage.py migrate
~~~

마이그레이션을 완료한다.

## Create

입력하기 위해 새로운 입력 페이지를 제작.
`new.html` 을 사용하겠다.

url작업은 위와 같다.

`new.html`은 입력을 위핸 form 을 작성했다.
~~~html
<h2>
    새로 만들기
</h2>

<form action="[% url 'crud:create' %]" method="POST">
{% csrf_token %}
    <label>제목</label><br>
    <input type="text" name="title"><br>
    <label>글 내용</label><br>
    <textarea name="content"></textarea><br>
    <input type="submit" value="제출">
</form>
~~~


create url을 위한 작업이다.
urls.py
~~~python
from django.shortcuts import render, redirect
# redirect 을 사용하기 위해 넣어 줌


def list(request):
    return render(request, 'list.html')


def new(request):
    return render(request, 'new.html')

def create(request):
    if request.method == 'POST':
        title = request.POST.get(title)
        content = request.POST.get(content)
        
        return redirect('crud:list', {'title': title, 'content': content})
    
    # request로부터 데이터 받아서 딕셔너리로 전달
~~~



<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE0NDIxNDI0ODYsMjY4OTI3NDAsLTE4MT
Y3Njg3NTgsLTE2NDkyOTY3MDcsMjE2OTQyMjA2LDkwNDYyMDk4
OF19
-->