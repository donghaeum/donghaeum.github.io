---
title:  "Board App - Board"
excerpt: "django 로 게시판 만들기"
categories:
  - blog
tags:
  - [blog, diary, dev]
last_modified_at: 2020-07-06T14:00:00
---
### 게시판 만들기

django 로 게시판 만들기


### App 생성

```zsh
$ python manage.py startapp board
```

### Settings.py 설정
```python
INSTALLED_APPS = [
  ...
  'board',
  'user',
]
```