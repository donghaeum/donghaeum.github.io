---
title:  "django 시작"
excerpt: "django 로 hello world 출력"
categories:
  - blog
tags:
  - [blog, django, dev]
last_modified_at: 2020-06-18T01:22:00
---
<!-- GitHub Blog 서비스인 github.io 블로그를 시작합니다. -->
## django 로 hello world 출력하기

프로젝트 폴더 생성  

```python
mkdir "folder name"
```

프로젝트 폴더로 이동
```python
cd "folder name"
```


django 설치   
```python
pip install django
```  

프로젝트 생성  
```python
django-admin startproject config .
```

migrate  
```python
python manage.py migrate
```

서버 실행  
```python
python manage.py runserver  
```
http://127.0.0.1:8000/ 접속  

관리자 계정 생성  
```python
python manage.py createsuperuser  
ID  
e-mail  
password 
``` 
http://127.0.0.1:8000/admin 접속  

app 생성  
```python
python manage.py startapp "app name"  
```

page 생성  
"app name"/view.py  
```python
from django.shortcuts import render  
from django.http import HttpResponse  
# Create your views here.  
def hello_world(request):  
    return HttpResponse('Hello World!')
```

urls.py 생성  
"app name"/urls.py  
```python
from django.urls import path  
from . import views  
urlpatterns = [  
path('', views.hello_world, name='hello_world')  
]  
```
config/urls.py 
```python 
from django.contrib import admin  
from django.urls import path, include  
urlpatterns = [  
path('admin/', admin.site.urls),  
path('hello_world/', include('hello.urls'))  
]  
```  
서버 실행  
```python
python manage.py runserver
```

{{ page.excerpt }}
최종 수정 시간: {{ page.last_modified_at }}