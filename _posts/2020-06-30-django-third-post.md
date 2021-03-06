---
title:  "MVC, MVT pattern"
excerpt: "MVC, MVT pattern이란"
categories:
  - blog
tags:
  - [blog, diary, dev, django]
last_modified_at: 2020-06-30 T12:00:00
---
<!-- GitHub Blog 서비스인 github.io 블로그를 시작합니다. -->
{{ page.excerpt }}  

### MVC 패턴
***
Model, View, Controller의 약자

<img src="/assets/images/mvc.png">

#### Model
***
어떤 동작을 수행하는 코드를 말한다.  
사용자에게 어떻게 보일지 신경 쓰지 않아도 되는 모델은 순수 public 함수로만 이루어진다.  
몇몇 함수들은 사용자의 질의(query)에 대해 상태 정보를 제공하고 나머지 함수들은 상태를 수정한다.  

모델은 모델의 상태에 변화가 있을 때 컨트롤러와 뷰에 이를 통보해야한다.   
이와 같은 통보를 통해 뷰는 최신의 결과를 보여줄 수 있고, 컨트롤러는 모델의 변화에 따른 적용 가능한 명령을 추가/제거/수정할 수 있다. 

#### View
***
데이터 및 객체의 입력, 그리고 보여주는 출력을 담당함.  
데이터를 기반으로 사용자들이 볼 수 있는 화면이다. 

뷰는 사용자가 볼 결과물을 생성하기 위해 모델로부터 정보를 읽어온다.  
뷰는 모델이 가지고 있는 정보를 따로 저장해서는 안된다.  
단지 읽어오기만 해야할 뿐! 변경이 일어나면 변경 통지에 대한 처리 방법을 구현해야 한다.

#### Controller
***
데이터와 사용자 인터페이스 요소들을 잇는 다리 역할을 한다.  
즉, 사용자가 데이터를 출력하고 수정하는 것에 대한 '이벤트'들을 처리하는 부분을 뜻한다.

컨트롤러를 사용하여 모델의 상태를 바꾼다.  
이 때 모델의 상태가 바뀌면 등록된 뷰에 자신의 상태가 바뀌었다는 것을 알리고 뷰는 거기에 맞게 사용자에게 모델의 상태를 보여준다.

#### MVC 패턴을 사용하는 이유
***
사용자가 보는 페이지, 데이터 처리 그리고 이 두가지를 중간에서 제어하는 컨트롤러 이 세 가지로 구성되는 하나의 어플리케이션을 만들면 각각 맡은 바에만 집중할 수 있게 된다. 
 
서로 분리되어 각자의 역할에 집중할 수 있게끔 하여 개발을 하고 그렇게 어플리케이션을 만든다면 유지보수성, 확장성, 유연성 등이 증가하고 중복 코딩이라는 문제점 또한 사라진다.

### MVT 패턴
***
장고 프레임워크에서는 View를 Template, Controller는 View라고 표현하며, MVC를 MVT 패턴이라고 한다.  
모델은 데이터 베이스에 저장되는 데이터를 의미하는 것이고, 템플릿은 사용자에게 보여지는 UI부분을, 뷰는 실질적으로 프로그램 로직이 동작하여 데이터를 가져오고 적절하게 처리한 결과를 템플릿에 전달하는 역할을 수행한다.  

<img src="/assets/images/mvt.png">

1. 클라이언트로부터 요청을 받으면 URLconf를 이용하여 URL을 분석한다.  
2. URL 분석 결과를 통해 해당 URL에 대한 처리를 담당할 뷰를 결정한다.  
3. 뷰는 자신의 로직을 실행하면서 만일 데이터 베이스 처리가 필요하면 모델을 통해 처리하고 그 결과를 반환받는다.  
4. 뷰는 자신의 로직 처리가 끝나면 템플릿을 사용하여 클라이언트에 전송할 HTML 파일을 생성한다.  
5. 뷰는 최종 결과로 HTML 파일을 클라이언트에게 보내 응답한다.  

#### Model
***
모델은 사용할 데이터에 대한 정의를 담고있는 클래스다.  
애플리케이션의 제어 흐름 및 처리 로직을 정의한다.

#### View
***
일반적으로 뷰는 웹 요청을 받아서 데이터 베이스 접속 등 해당 어플리케이션의 로직에 맞는 처리를 하고, 그 결과 데이터를 HTML로 변환하기 위하여 템플릿 처리를 한 후에 최종 HTML로 된 응답 데이터를 웹 클라이언트로 반환하는 역할을 한다.

장고에서 뷰는 함수 또는 클래스의 메소드로 작성되며, 웹 요청을 받고 응답을 반환해준다.  
다양한 형태의 응답 데이터를 만들어 내기 위한 로직을 뷰에 작성하는 것인데, 이러한 뷰는 보통 views.py 파일에 작성하지만 원한다면 다른 파일에 작성해도 무방하다.  
다만 파이썬 경로에 있는 파일이어야 장고가 찾을 수 있다.

#### Template
***
장고가 클라이언트에 반환하는 최종 응답은 HTML 텍스트다.  
개발자가 응답에 사용할 *.html 파일을 작성하면, 장고는 이를 해석해서 최종 HTML 텍스트 응답을 생성하고, 이를 클라이언트에게 보내준다. 

템플릿 파일은 *.html 확장자를 가지며, 장고의 템플릿 시스템 문법에 맞게 작성한다.  
유의할 점은 템플릿 파일을 적절한 디렉토리에 위치시켜야 한다는 점이다.  
즉, 장고에서 템플릿 파일을 찾는 방식을 이해하고 있어야하며, 장고는 그에 맞는 위치에 템플릿 파일이 위치해야 템플릿 파일을 찾을 수 있다.

