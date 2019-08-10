
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
사이트에서 확인해 본 결과 [모델명 object(n)]의 형태로 표시가 되는데, 이를 내가 원하는 형태로 바꾸고 싶은 경우, 모델에 다음과 같이 추가하면 된다.

~~~python
    def __str__(self):
        return self.title
~~~

모델을 수정했지만, 따로 마이그레이션을 다시 할 필요는 없이 바로 적용되는 듯하다. 
아마 column을 수정한 것은 아니라서 그런 것 같다. 데이터베이스를 직접 수정한 것은 아니라서..


##  List
지금까지 작성된 데이터들을 list페이지에 보여주자.
 
 우선 view에서 데이터들을 넘겨 주어야 한다.
 ~~~ python
def list(request):
    posts = Post.objects.all()
    return render(request, 'list.html', {'posts':posts})
~~~

Post 모델의 모든 데이터들을 posts에 담고, 딕셔너리를 통해 넘겨주었다.

list.html은 다음과 같이 꾸며 주었다.
~~~html
<h2>
    메인페이지입니다.
</h2>

{% for item in posts %}

<h5>
    <a href="#"> 제목 : {{ item.title }} </a>
</h5>

{% endfor %}
~~~

##  Read
이제 제목을 클릭하면, 그 데이터만 따로 보여주는 Read 부분을 해 보자.

우선 id를 넘겨받았을 때 모델을 사용해 그 오브젝트를 찾을 수 있어야 하므로 view에서 다음과 같이 작성한다.

~~~python
def read(request, id):
    post = Post.objects.get(pk=id)
    return render(request, 'read.html', {'post':post})
~~~
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTc5ODc5ODQ4NSwxMDM2MDYyMTE5LDczMD
k5ODExNl19
-->