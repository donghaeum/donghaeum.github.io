---
title:  "Polls App"
excerpt: "django 로 Polls app 만들기"
categories:
  - blog
tags:
  - [blog, django, dev]
last_modified_at: 2020-06-18T01:22:00
---
### Polls

Project 생성
```zsh
django-admin startproject config .
```
```
python manage.py migrate
```

App 생성
```zsh
python manage.py startapp polls
```

Setting.py 수정
config/setting.py
```python
INSTALLED_APPS = [
  'django.contrib.admin',
  'django.contrib.auth',
  'django.contrib.contenttypes',
  'django.contrib.sessions',
  'django.contrib.messages',
  'django.contrib.staticfiles',
  'polls',
]
```
