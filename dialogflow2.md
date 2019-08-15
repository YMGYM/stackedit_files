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
        if intent == "스위플광고":
            return add(request)


def add(request):
    sweeples = Sweeple.objects.all()
    item = random.choice(sweeples)
    fulfillmentText = {'fulfillmentText': item.taste + ' 스위플은 어떠신가요? ' + item.description}
    return JsonResponse(fulfillmentText, safe=False)
~~~

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTkzNzI4NzIzOSwtMTkyMjE5OTEyNiwtOT
AwNzE3NTIwXX0=
-->