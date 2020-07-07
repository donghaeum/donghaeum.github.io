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


