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
> 따라서 webhook 을 통해서 DF에서도 CRUD를 구현해 보고자 한다.

## 다른 view로 넘기기?

생각을 해 봤는데, 모든 intent를 `action()`에서 처리하게 되면 코드가 매우 복잡해질 듯 해서, view를 여러 개 만들어서 작업하고 싶었다.
그런데, 그렇게 하면 모든 뷰에 
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTM3NTA4MDAxMiwtMTkyMjE5OTEyNiwtOT
AwNzE3NTIwXX0=
-->