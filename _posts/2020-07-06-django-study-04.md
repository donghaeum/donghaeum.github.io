---
title:  "Board App - User"
excerpt: "django 로 게시판 만들기"
categories:
  - blog
tags:
  - [blog, diary, dev]
last_modified_at: 2020-07-06T14:00:00
---
### 게시판 만들기

django 로 게시판 만들기

### Project 생성

```zsh
mkdir board_app
```

```zsh
cd board_app
```

```zsh
django-admin startproject config .
```

```zsh
python manage.py migrate
```

```zsh
python manage.py startapp user
```
### Settings.py 설정
```python
INSTALLED_APPS = [
  ...
  'user',
]
```

### Models.py 설정
```python
from django.db import models
class User(models.Model):
    username = models.CharField(max_length=32, verbose_name='사용자명')
    password = models.CharField(max_length=64, verbose_name='비밀번호')
    useremail = models.EmailField(max_length=128, verbose_name='이메일')
    registered_dttm = models.DateTimeField(auto_now_add=True, verbose_name='등록일')
    def __str__(self):
        return self.username
    class Meta:
        db_table = 'board_user'
        verbose_name = '사용자'
        verbose_name_plural = '사용자'
```
### 
