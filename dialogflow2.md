---
layout: post
title: "dialogflow 기초"
categories:
  - 아픈지렁이
tags:
  - Django
  - 챗봇
---

# Dialogflow에서의 CRUD

> 멋쟁이사자처럼 강의를 따라가서 그런건지, 나는 CRUD를 매우 중요하게 여기는 편이다.
> CRUD만 있으면 웬만한 플랫폼을 다 만들 수 있을 것이라고 생각할 정도다.
> 따라서 webhook 을 통해서 Dialogflow(DF)에서도 CRUD를 구현해 보고자 한다.

## 다른 view로 넘기기?

생각을 해 봤는데, 모든 intent를 `action()`에서 처리하게 되면 코드가 매우 복잡해질 듯 해서, view를 여러 개 만들어서 작업하고 싶었다.
그런데, 그렇게 하면 모든 뷰에 `csrf_except`를 걸어줘야 하는데 `action/`은 그렇다 쳐도, 다른 뷰로 넘어가는 순간 csrf 위험에 노출되는 문제가 생긴다.

이를 해결하기 위해 우선 임시방편으로 다음과 같은 형태를 취하고자 한다.

- url을 만들지 않아, 주소창에 직접 입력하는 것으로는 들어갈 수 없게 한다.
- url을 `action/`하나만 만들어서, `action()`만을 통하게 한다.


### intent 파악후, 넘기기
DF 로부터의 request는 JSON 형태로 이루어진다.
샘플 요청을 보내 보면 request 내용은 다음과 같다
~~~json
{
  "responseId": "[이 값은 매일 달라집니다.]",
  "queryResult": {
    "queryText": "[사용자가 입력한 값]",
    "parameters": {
      ...
    },
    "allRequiredParamsPresent": true,
    "intent": {
      "name": "[인텐트의 ID값인듯]",
      "displayName": "스위플광고"
    },
    "intentDetectionConfidence": 0.7146579,
    "languageCode": "ko"
  },
  "originalDetectIntentRequest": {
    "payload": {}
  },
  "session": "[보안값?]"
}
~~~

다른 인자는 차차 살펴보도록 하고, 내가 확인해야할 정보는 'queryResult' 안의 'intent' 안의 'displayName'이다.
따라서 이것은 다음과 같은 코드를 통해 가져올 수 있다.
`views.py`
~~~python
...

def action(request):
    if request.method == 'POST':
        req = json.loads(request.body)
        # 인텐트를 파악합니다
        intent = req.get('queryResult').get('intent').get('displayName')
~~~


#### 여담 'post' 와 'POST'

한참 작업하고 있는데, 갑자기 오류를 뱉길래 원인을 한참 찾았더니 POST가 소문자로 적혀있었다... django는 HTTP 메소드의 대소문자를 구분하는것 같다.

이제 이 인텐트를 if문을 통해 구분해서, 다음 함수로 넘기기만 하면 된다.

`views.py`
~~~ python
...
from .models import Sweeple
import random
...

        if intent == "스위플광고":
            return ad(request)


def ad(request):
    sweeples = Sweeple.objects.all()
    item = random.choice(sweeples)
    fulfillmentText = {'fulfillmentText': item.taste + ' 스위플은 어떠신가요? ' + item.description}
    return JsonResponse(fulfillmentText, safe=False)
~~~

스위플의 맛이 어떤 것이 있냐는 intent가 오면, `ad()`함수로 넘겨 준다.
`ad()`에서는 스위플 모델중 아무 값이나 하나 선택해서 제목과 설명을 `fulfillmentText`로 날려 준다.


## Create

챗봇을 할 때 create를 할 때 가장 들기 쉬운 예제가 제품 주문인 듯하다..
많은 사람들이 제품 주문으로 예를 들고 있다.
따라서 나도 스위플 주문하는 것으로 만들어 보겠다.

우선 model을 하나 더 만들어서 migration 했다.

`models.py`
~~~python
class Delivery(models.Model):
    taste = models.CharField(max_length=20)
    number = models.IntegerField()
    name = models.CharField(max_length=20)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    def __str__(self):
        return self.name
~~~


### intent  만들기
`스위플주문`이란 이름으로 인텐트를 새로 만들었다.
주문을 받기 위해서는 파라미터 값으로, 어떤 제품을 주문 받을지, 몇 개나 필요한지, 그리고 적어도 주문자의 이름 정도는 알아야 한다.
이를 해결하기 위해 `parameter`를 이용한다.

DF의 파라미터는 다음과 같은 속성이 있다.

- required : 이 파라미터값이 꼭 필요한지 나타낸다.
- parameter name : 파라미터의 이름이다. @ 뒤에 나타낸다.
- entity : 파라미터 값이 어떤 `entity`를 가지는지 나타낸다.
- value :  파라미터 값을 변수처럼 사용하게 한다 . $ 뒤에 나타낸다.
- prompts : required 파라미터가 충족되지 않았을 때 그것을 묻는 질문이다.

이것들을 이용해서, model의 `taste` `name` `number`을 채울 수 있도록 하고, 그걸 장고 서버로 전송했다.

### view 꾸미기

우선 `action()`에서 받은 파라미터값을 다음 함수로 넘겨 주어야 하므로
다음과 같이 작성한다.

`views.py`
~~~python
...

        elif intent == "스위플주문":
            params = req.get('queryResult').get('parameters')
            return create_delivery(request, params)

...

def create_delivery(request, params):
~~~
함수의 인자를 통해 딕셔너리를 바로 전달 할 수 있었다. 해보니까 되네..

그리고 이제 view에서 배웠던 Create와 같게 만들면 된다.

`views.py`
~~~python
...

def create_delivery(request, params):
    
    taste = params.get('taste')
    name = params.get('name')
    number = params.get('number')
    item = Delivery(taste = taste, name=name, number=number)
    item.save()
...
~~~

마지막으로 주문을 나중에 쉽게 확인할 수 있도록, 주문번호를 사용해야 하는데, 일단은 pk값을 주문번호로 주도록 하겠다.
item이라는 변수를 사용하고 있어서 `item.id`를 사용할 수 있을까? 했더니 진짜 사용할 수 있더라.. 대단하다.

`views.py`
~~~python
...

fulfillmentText = {'fulfillmentText' : '감사합니다. 주문번호는 {} 입니다.'.format(item.id)}
    return JsonResponse(fulfillmentText, safe=False)
~~~
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTgwODQ4MDc4OCwtMTM4NDc2NDczNSwxMD
g0MDczNjMsLTE5ODU1MzczNDQsLTE5MjIxOTkxMjYsLTkwMDcx
NzUyMF19
-->