---
title:  "Start django"
excerpt: "django 로 hello world 출력"
categories:
  - blog
tags:
  - [blog, django, dev]
last_modified_at: 2020-06-18T01:22:00
---
<!-- GitHub Blog 서비스인 github.io 블로그를 시작합니다. -->
## django 로 서버 실행하기
django 로 서버 실행

### 프로젝트 폴더 생성  
***
```bash
$ mkdir "folder name"
```

### 프로젝트 폴더로 이동
***
```bash
$ cd "folder name"
```

### django 설치 
***  
```bash
$ pip install django
```  

### 프로젝트 생성  
***
```bash
$ django-admin startproject config .
```

### migrate  
***
```bash
$ python manage.py migrate
```

### 서버 실행  
***
```bash
$ python manage.py runserver  
```
http://127.0.0.1:8000/ 접속  

### 관리자 계정 생성  
***
```bash
$ python manage.py createsuperuser  
ID 입력
e-mail 입력
password 입력
``` 
http://127.0.0.1:8000/admin 접속  

## django 로 hello world! 출력하기
django 로 hello world 출력

### app 생성  
***
```bash
$ python manage.py startapp "app name"  
```

### page 생성  
#### "app name" / view.py  
***
```python
from django.shortcuts import render  
from django.http import HttpResponse  
# Create your views here.  
def hello_world(request):  
    return HttpResponse('Hello World!')
```

### urls.py 생성  
#### "app name" / urls.py  
***
```python
from django.urls import path  
from . import views  
urlpatterns = [  
path('', views.hello_world, name='hello_world')  
]  
```
### urls.py 수정  
#### config / urls.py 
***
```python 
from django.contrib import admin  
from django.urls import path, include  
urlpatterns = [  
path('admin/', admin.site.urls),  
path('hello_world/', include('hello.urls'))  
]  
```  
### 서버 실행  
***
```bash
$ python manage.py runserver
```

{{ page.excerpt }}  
최종 수정 시간: {{ page.last_modified_at }}