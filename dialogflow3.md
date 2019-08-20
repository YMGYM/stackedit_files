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
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTM4NDQ2MjU1MCwtNzM2MzYwMTkxLDEwND
E5NzU3NzUsMTU0MTY1MTk4N119
-->