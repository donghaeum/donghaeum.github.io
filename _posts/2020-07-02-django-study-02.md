---
title:  "Bookmark App"
excerpt: "django 로 Bookmark app 만들기"
categories:
  - blog
tags:
  - [blog, django, dev]
last_modified_at: 2020-06-18T01:22:00
---
## Bookmark App 만들기

### Project 생성
```zsh
mkdir bookmark_app
```
```zsh
django-admin startproject config .
```
```zsh
python manage.py migrate
```
```zsh
python manage.py startapp bookmark
```

### Settings.py 수정

* config / settings.py

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'bookmark', # 추가
]
```
```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')], # 추가
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

### Model
* bookmark / models.py 수정

```python
from django.db import models

# Create your models here.
class Bookmark(models.Model):
    site_name = models.CharField(max_length=130)
    url = models.URLField('Site URL')

    def __str__(self):
        return self.site_name + ':' + self.url
```
```zsh
python manage.py makemigrations
```
```zsh
python manage.py migrate
```

### Admin 생성
```zsh
python manage.py createsuperuser
ID 입력
e-mail 입력
password 입력
```

* bookmark / admin.py 수정

```python
from django.contrib import admin
from .models import Bookmark

admin.site.register(Bookmark)
```

### View

* bookmark / views.py 수정

```python
from django.shortcuts import render
from django.views.generic import ListView, CreateView, DetailView, UpdateView, DeleteView
from django.urls import reverse_lazy
from .models import Bookmark

class BookmarkList(ListView):
    model = Bookmark
    paginate_by = 5
class BookmarkCreateView(CreateView):
    model = Bookmark
    fields = ['site_name', 'url']
    success_url = reverse_lazy('list')
class BookmarkDetailView(DeleteView):
    model = Bookmark
class BookmarkUpdateView(UpdateView):
    model = Bookmark
    fields = ['site_name', 'url']
    success_url = reverse_lazy('list')
    template_name_suffix = '_update'
class BookmarkDeleteView(DeleteView):
    model = Bookmark
    fields = ['site_name', 'url']
    success_url = reverse_lazy('list')
```

### Url

* bookmark / urls.py 생성

```python
from django.urls import path
from .views import *
urlpatterns = [
    path('', BookmarkList.as_view(), name='list'),
    path('add/', BookmarkCreateView.as_view(), name='add'),
    path('detail/<int:pk>/', BookmarkDetailView.as_view(), name='detail'),
    path('_update/<int:pk>/', BookmarkUpdateView.as_view(), name='update'),
    path('delete/<int:pk>/', BookmarkDeleteView.as_view(), name='delete'),
]
```

* config / urls.py 수정

```python
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('bookmark/', include('bookmark.urls')), # 추가
]
```
### Templates 생성

* bookmark_app / templates 폴더 생성

#### Base.html

* bookmark_app / templates / Base.html 생성

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{% block title %} {% endblock %} </title>

    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
    <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
    <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>


</head>
<body>
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <a class="navbar-brand" href="#">Bookmark</a>
  <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>
  <div class="collapse navbar-collapse" id="navbarSupportedContent">
    <ul class="navbar-nav mr-auto">
      <li class="nav-item active">
        <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
      </li>
    </ul>
  </div>
</nav>

<div class="container">
  {% block content %}

    {% endblock %}

</div>

</body>
</html>
```

* bookmark / templates / bookmark 폴더 생성

#### bookmark_confirm_delete.html

* bookmark / templates / bookmark / bookmark_confirm_delete.html 생성

```html
{% extends 'Base.html' %}

{% block title %}Delete{% endblock %}


{% block content %}
<form action="" method="post">
    {% csrf_token %}
    <div class="alert alert-danger">
        삭제할까요? {{ object }}
    </div>
    <input type="submit" value="삭제" class="btn btn-danger">
</form>

{% endblock %}
```