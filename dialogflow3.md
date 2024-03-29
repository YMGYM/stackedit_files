---
layout: post
title: "dialogflow 남은 기능 알아보기"
categories:
  - 아픈지렁이
tags:
  - Django
  - 챗봇
---

# Dialogflow 남은 기능 알아보기

> 벌써 dialogflow가 질려 가면 안 되는데.. 슬슬 지루해진다.
> 심지어 Update는 생각보다 귀찮은 작업인것을 깨달았고, 안해도 되지 않을까 판단했다..
> 일단 지금은 Delete와 지금까지 써보지 못했던 event와 action을 확인해 볼 계획이다.

## action

`action`이란 intent를 구분해 주는 일종의 변수? 느낌인 듯 하다.
찾아보니 action은 intent보다 서버에 친화적이라고 한다.
따라서 서버에서 intent를 활용해 구현하는 것 보다, action을 활용해 구현하는게 맞을 듯 해서 Delete는 action을 활용해 보도록 하겠다.

사용 예는 다음과 같다.
- parameters 부분에 action칸에 action명을 작성해 준다.
- 코드를 다음과 같이 작성한다.
`views.py`
~~~python
...
elif action == 'order_destroy':
            params = req.get('queryResult').get('parameters')
            return order_destroy(request, params)
...
def order_destroy(request, params):
...

~~~

## Delete

delete 의 경우는 상당히 쉽게 해결했다.
Read와 같이 주문번호를 context로 받고, `delete()`함수를 통해 삭제해주면 그만.

하지만 그 후에 context를 삭제해주는 작업이 필요한 것 같다.
없는 주문번호를 계속 가지고 띄워주면 안 되기 때문에, context의 수명을 0으로 지정하는 방법으로 context를 삭제했다.

`view.py`
~~~python
...
def order_destroy(request, params):
    del_num = params.get('del_num')
    
    item = Delivery.objects.get(pk=del_num)
    item.delete()
    
    response = {'fulfillmentText': '말씀하신 {}번 주문이 삭제되었습니다.'.format(int(del_num)),
                   "outputContexts": [
                {
                  "name": "projects/sweeple-delivery-bot-saxdfa/agent/sessions/ec79f53c-31b2-3a18-998f-32cb63c3a6f2/contexts/order",
                  "lifespanCount": 0,
                  "parameters": {
                    "del_number": item.id
                  }
                }
              ]
        }
    return JsonResponse(response, safe=False)
~~~

## event에 관해서

### event란
event는 메세지 식 입력이 아닌, 다른 방법으로 intent를 연결하는 방법이다.

dialogflow 공식 문서에 적힌 절차는 다음과 같다.

---
1. 최종 사용자가 발화를 입력하거나 말합니다.
2.  Dialogflow가 fulfillment에 구성된  **Intent-1**에 발화를 일치시킵니다.
3.  Dialogflow가 서버에 웹훅 요청을 보냅니다.
4.  서버가 후속 조치 이벤트가 포함된 웹훅 응답으로 응답합니다.
5.  Dialogflow는 사용자에게  **Intent-1**  일치에 대한 응답을 보내는 대신, 이벤트에 구성된  **Intent-2**를 트리거합니다.
6.  Dialogflow는 최종 사용자가  **Intent-2**  일치를 시작한 것처럼 일치를 진행하고,  **Intent-2**  구성에 지정된 대로 필요한 매개변수와 fulfillment를 처리합니다.
---

결국 intent1에 대한 응답 대신 fulfillment를 통해 intent2로 이어진 event 를 트리거하면 intent2에 대한 응답이 전송되는 방식이다.

### event 설정
일단 event를 활용하기 위해 intent를 하나 만들었다.
나는 `order_destroy-no`가 되었을 경우 응답을 설정하기 위해 `destroy_canceled` 라는 event를 만들었다.
그리고 event를 호출하는 Json을 반환하면 된다.

`views.py`
~~~python
...
    elif action == 'sweeple_order_destroy-no':
            return destroy_canceled(request)
...

def destroy_canceled(request):
    response = {
          "followupEventInput": {
            "name": "destroy_canceled",
            "languageCode": "ko"
          }
        }

    
    return JsonResponse(response, safe=False)
~~~

event를 반환하면 fulfillmentText필드는 자동으로 무시되기 때문에 작성하지 않아도 되었다.

이렇게 하면 정상적으로 의도한 intent에 설정된 대답으로 응답한다.



## 마무리
이상으로 dialogflow에 있는 모든 기능들을 다 알아본 것 같다.
마구잡이식으로 공부해서 작성하다보니, 구조도 많이 꼬이고, 모델이나 intent 등 재설계하고 싶은 부분이 많이 있다.
다음에는 최대한 완벽하고 설명하기 쉬운 구조를 향해서 프로젝트를 설정하고, git에 올려 보고 싶다.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEyMjMwODAzMjEsLTIwMDMwMTcyMTQsMj
E0MjE4MTc4LDE5MTA5MDk3MzMsLTczNjM2MDE5MSwxMDQxOTc1
Nzc1LDE1NDE2NTE5ODddfQ==
-->