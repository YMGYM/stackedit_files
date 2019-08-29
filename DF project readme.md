# Django Dialogflow Websever

django를 이용해 google dialogflow 의 fulfillment 기능을 이용할 수 있는 샘플 코드입니다. dialogflow 챗봇을 활용해 django 모델 CRUD (create, read, update, delete)를 할 수 있게 코드를 작성해 두었습니다.


이 자료는 동국대학교 멋쟁이사자처럼 5기 수료생 안민준이 7기 세션을 위해 작성하였습니다.
코드에 대한 자세한 설명 또는 제작 과정은 <a href="https://ymgym.github.io/%EC%95%84%ED%94%88%EC%A7%80%EB%A0%81%EC%9D%B4/2019/08/13/dialogflow(1).html">여기</a> 에서 확인할 수 있습니다.


## 코드 구성

### app 구성

`dialogflow_project` 프로젝트 안에 `crud` 앱을 만들고, `settings.py`에 추가 해 둔 상태입니다.

프로젝트 `urls.py` 에서 `webhook/` 으로 받은 리퀘스트를 `crud` 앱으로 전송합니다. `crud` 에서는 `webhook/` 으로 바로 들어오는 리퀘스트 이외에 어떠한 url도 등록되어 있지 않습니다.

### model 구성

샘플 모델로 주문자의 정보와 내용을 저장하는 `Order` 모델을 작성해 두었습니다. 

`Order` 모델에는 주문자의 이름을 작성하는 `name` 열과, 주문 내용(주문한 제품)을 기록하는 `content` 열로 저장되어 있으며, `__str__` 함수를 통해 admin 페이지에서 주문자의 이름으로 나타나도록 설정해 두었습니다.

`Order` 모델은 admin 페이지에 register 되어 있습니다.

### View 구성

`views.py` 에는 JSON타입 반환과 CSRF 토큰 오류를 방지하기 위해 다음과 같은 코드가 들어 있습니다.

~~~python
...
# JSON 타입 반환을 위해 import
from django.http import JsonResponse
import json

#csrf 예외를 위해 import
from django.views.decorators.csrf import csrf_exempt

@csrf_exempt
...
~~~
## CRUD

CRUD 각 기능 별로 사용되는 dialogflow의 요소들은 다음과 같으며, 각 요소는 최초로 다뤄진 부분에서 간략하게 설명합니다.

Create
> fulfillment 연결, parameters, action, context

Read
> context

Update
> context

Delete
> follow-up context, context 삭제, event,


## Code 설명

CRUD 각 요소별로 나눠서 하나하나 설명하도록 하겠습니다.

### webhook


<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEyODcwNzMzODIsNjkxMjk1NjYyLC0xMD
U1NDA2NjE2XX0=
-->