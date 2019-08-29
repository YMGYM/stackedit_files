# Django Dialogflow Websever

django를 이용해 google dialogflow 의 fulfillment 기능을 이용할 수 있는 샘플 코드입니다. 아래에는 코드에 대한 설명을 적어 두었습니다.


이 자료는 동국대학교 멋쟁이사자처럼 5기 수료생 안민준이 7기 세션을 위해 작성하였습니다.
코드에 대한 자세한 설명 또는 제작 과정은 <a href="https://ymgym.github.io/%EC%95%84%ED%94%88%EC%A7%80%EB%A0%81%EC%9D%B4/2019/08/13/dialogflow(1).html">여기</a> 에서 확인할 수 있습니다.


## 코드 구성

### app 구성

`dialogflow_project` 프로젝트 안에 `crud` 앱을 만들고, `settings.py`에 추가 해 둔 상태입니다.

프로젝트 `urls.py` 에서 `webhook/` 으로 받은 리퀘스트를 `crud` 앱으로 전송합니다. `crud` 에서는 `webhook/` 으로 바로 들어오는 리퀘스트 이외에 어떠한 url도 등록되어 있지 않습니다.

### model 구성

샘플 모델로 주문자의 정보와 내용을 저장하는 `Order` 모델을 작성해 두었습니다. 
주문자의 이름을 작성하는 `name` 열과, 주문 내용(주문한 제품)을 기록하는 `content` 열로 저장되어 있으며, `__str__` 함수를 통해 admin 사이트에서 주문자의 이름으로 나타나도록 설정해 두었습니다.
 `Order` 모델은 어드민 사이트에 register 되어 있으며,   
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE5NTI0MTY0NDNdfQ==
-->