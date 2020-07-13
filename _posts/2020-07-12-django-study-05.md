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

### Models.py 설정

```python
from django.db import models

class Board(models.Model):
    title = models.CharField(max_length=128, verbose_name='제목')
    contents = models.TextField(verbose_name='내용') # 길이에 제한이 없음
    writer = models.ForeignKey('user.User', on_delete=models.CASCADE, verbose_name='작성자')

    registered_dttm = models.DateTimeField(auto_now_add=True, verbose_name='등록시간')

    def __str__(self):
        return self.title

    class Meta:
        db_table = 'board'
        verbose_name = '게시글'
        verbose_name_plural = '게시글'
```

```zsh
$ python manage.py makemigrations
```

```zsh
$ python manage.py migrate
```

### Views.py 설정

```python
from django.shortcuts import render, redirect
from django.core.paginator import Paginator
from django.http import Http404
from user.models import User
from .models import Board
from .forms import BoardForm
# Create your views here.
def board_list(request):
    all_boards = Board.objects.all().order_by('-id')

    page = int(request.GET.get('p', 1))
    paginator = Paginator(all_boards, 2)

    boards = paginator.get_page(page)
    return render(request, 'board_list.html', {'boards': boards})

def board_write(request):
  if not request.session.get('user'):
    return redirect('/user/login')

  if request.method == 'POST':
    form = BoardForm(request.POST) # POST일 때 데이터를 넣는다.
    if form.is_valid():
      # 세션에서 유저 아이디를 가져오고
      user_id = request.session.get('user');
      # 유저아이디를 이용해 모델에서 pk가 user_id인 것을 가져옴
      user = User.objects.get(pk=user_id)

      board = Board()
      board.title = form.cleaned_data['title']
      board.contents = form.cleaned_data['contents']
      # 사용자가 로그인했으면 정보가 session에 들어있다.
      board.writer = user # user 를 writer에 추가
      board.save() # save를 통해 데이터베이스에 저장이 된다.

      # redirect 경로
      return redirect('/board/list/')
  else:
    form = BoardForm()
  return render(request, 'board_write.html', {'form': form})

def board_detail(request, pk):
  try:
    board = Board.objects.get(pk=pk)
  except Board.DoesNotExist:
    raise Http404('게시글을 찾을 수 없습니다.')

  return render(request, 'board_detail.html', {'board': board})
```

### Urls.py 설정
```python
from django.urls import path
from . import views

urlpatterns = [
    path('list/', views.board_list, name='board_list'),
    path('detail/<int:pk>/', views.board_detail),
    path('write/', views.board_write),
]
```

### Admin.py 설정

```python
from django.contrib import admin
from .models import Board
# Register your models here.
class BoardAdmin(admin.ModelAdmin):
    list_display = ('title', )

admin.site.register(Board, BoardAdmin)
```

### Forms.py 설정

board/forms.py 생성

```python
from django import forms

class BoardForm(forms.Form):
    title = forms.CharField(
      error_messages={'required': '제목을 입력해주세요.'},
      max_length=128, label="제목"
    )
    contents = forms.CharField(
      error_messages={'required': '내용을 입력해주세요.'},
      widget=forms.Textarea, label="내용"
    )
    tags = forms.CharField(required=False, label="태그")
```
### Templates 생성

board/templates 폴더 생성

### Base.html
{% raw %}
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <title>Base</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
<!--    <link rel="stylesheet" href="/static/bootstrap.min.css" />-->
    <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
    <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>

</head>
<body>

<!--  container  -->
<div class="container">
    {% block contents %}
    {% endblock %}
</div>
<!-- //container -->

</body>
</html>
```
{% endraw %}

### Board_detail.html
{% raw %}
```html
{% extends "base.html" %}

{% block contents %}

<div class="row mt-5">
  <div class="col-12">
    <div class="form-group">
      <label for="title">제목</label>
      <input type="text" class="form-control" id="title" value="{{ board.title }}" readonly />
      <label for="contents">내용</label>
      <textarea class="form-control" readonly>{{ board.contents }}</textarea>
    </div>

    <button class="btn btn-primary" onclick="location.href='/board/list/'">돌아가기</button>
  </div>
</div>

{% endblock %}
```
{% endraw %}

### Board_list.html
{% raw %}
```html
{% extends "base.html" %}

{% block contents %}

<div class="row mt-5">
  <div class="col-12">
    <table class="table table-light">
      <thead class="thead-light">
        <tr>
          <th>#</th>
          <th>제목</th>
          <th>아이디</th>
          <th>일시</th>
        </tr>
      </thead>
      <tbody class="text-dark">
        <!--  for 문을 통해 요소를 순회  -->
          {% for board in boards %}
            <tr onclick="location.href='/board/detail/{{ board.id }}'">
              <td>{{ board.id }}</td>
              <td>{{ board.title }}</td>
              <td>{{ board.writer }}</td>
              <td>{{ board.registered_dttm }}</td>
            </tr>
          {% endfor %}
        </tbody>
    </table>
  </div>
</div>

<div class="row">
  <div class="col-12">
    <nav>
      <ul class="pagination justify-content-center">
          {% if boards.has_previous %}
          <li class="page-item">
              <a class="page-link" href="?p={{ boards.previous_page_number }}">prev</a>
          <li>
          {% else %}
          <li class="page-item disabled">
              <a class="page-link" href="#">prev</a>
          <li>
          {% endif %}

          <li class="page-item active">
              <a class="page-link" href="#">{{ boards.number }} / {{ boards.paginator.num_pages }}</a>
          </li>

          {% if boards.has_next %}
          <li class="page-item">
              <a class="page-link" href="?p={{ boards.next_page_number }}">next</a>
          <li>
          {% else %}
          <li class="page-item disabled">
              <a class="page-link" href="#">next</a>
          </li>
          {% endif %}
      </ul>
    </nav>
  </div>
</div>

<div class="row">
  <div class="col-12">
    <button class="btn btn-primary" onclick="location.href='/board/write/'">글쓰기</button>
    <button class="btn btn-primary" onclick="location.href='/'">Home</button>

  </div>
</div>

{% endblock %}
```
{% endraw %}

### Board_write.html
{% raw %}
```html
{% extends "base.html" %}

{% block contents %}

<div class="row mt-5">
  <div class="col-12">
    <form method="post" action=".">
      {% csrf_token %}
      {% for field in form %}
        <div class="form-group">
          <label for="{{ field.id_for_label }}">{{ field.label }}</label>
          {{ field.field.widget.name }}
          <!--  새로운 태그를 만들어야 한다.-->
          {% ifequal field.name 'contents' %}
              <textarea class="form-control" name="{{ field.name }}" placeholder="{{ field.label }}"></textarea>
          {% else %}
          <input type="{{ field.field.widget.input_type }}" class="form-control" id="{{ field.id_for_label }}"
                 placeholder="{{ field.label }}" name="{{ field.name }}" />
          {% endifequal %}
        </div>

        {% if field.errors %}
            <span style="color: red">{{ field.errors }}</span>
        {% endif %}
      {% endfor %}
      <button type="submit" class="btn btn-primary">글쓰기</button>
    </form>
      <form method="post" action=".">
          <button type="button" class="btn btn-primary" onclick="location.href='/board/list/'">돌아가기</button>
      </form>

  </div>
</div>

{% endblock %}
```
{% endraw %}

### App 실행

```zsh
$ python manage.py runserver
```

http://127.0.0.1:8000/ 접속

#### 게시판 기본 화면
***
<img src="/assets/images/board_01.png">

#### 게시판 회원가입 화면
***
<img src="/assets/images/board_02.png">

#### 게시판 로그인 화면
***
<img src="/assets/images/board_03.png">

#### 게시판 로그인 시 기본 화면
***
<img src="/assets/images/board_06.png">

#### 게시판 글 목록 화면
***
<img src="/assets/images/board_04.png">

#### 게시판 글 쓰기 화면
***
<img src="/assets/images/board_05.png">