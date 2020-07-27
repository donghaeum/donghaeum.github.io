---
title:  "Board App - User"
excerpt: "django 로 게시판 만들기"
categories:
  - blog
tags:
  - [blog, diary, dev]
last_modified_at: 2020-07-06 T14:00:00
---
### 게시판 만들기

django 로 게시판 만들기

### Project 생성

```zsh
$ mkdir board_app
```

```zsh
$ cd board_app
```

```zsh
$ django-admin startproject config .
```

```zsh
$ python manage.py migrate
```

```zsh
$ python manage.py startapp user
```
### Settings.py 설정
```python
INSTALLED_APPS = [
  ...
  'user',
]

...

STATIC_URL = '/static/'

STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static')
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

```zsh
$ python manage.py makemigrations
```

```zsh
$ python manage.py migrate
```

### Views.py 설정
```python
from django.http import HttpResponse
from django.shortcuts import render, redirect
from django.contrib.auth.hashers import make_password, check_password
from .models import User
from .forms import LoginForm

def register(request):  
  if request.method == 'GET':
    # 경로는 템플릿 폴더를 바라보므로 경로를 따로 표현할 필요는 없다
    return render(request, 'register.html')
  elif request.method == 'POST':    
    username = request.POST.get('username', None)
    password = request.POST.get('password', None)
    re_password = request.POST.get('re-password', None)
    useremail = request.POST.get('useremail', None)
    # form 을 통해 넘어오는 input 속성의 name 값을 키로 사용해서 값을 얻는다.

    res_data = {}
    # 먼저 모든 값들이 들어와 있는지 확인을 하고 들어오지 않았을 때 아래 메시지 출력
    if not (username and password and re_password and useremail):
        res_data['info_error'] = '회원정보가 올바르게 입력되지 않았습니다.'
    elif password != re_password:
        res_data['password_error'] = '비밀번호가 다릅니다.'
    else:
      user = User(
        username=username,
        useremail=useremail,
        # 장고에서는 암호화해서 저장하는 make_password 메서드를 지원해줌
        # password=password가 아니라 아래처럼 더 개선된 방법으로 작성할 수 있다.
        password=make_password(password) # 비밀번호를 함수 안에 전달
      )
    # 이렇게 하고서 저장을 하면 끝이남
    user.save() # 클래스 변수 객체를 하나 생성하고 저장하면 된다..!

    return render(request, 'register.html', res_data)
```

### Urls.py 설정
```python
from django.urls import path
from . import views

urlpatterns = [
    path('register/', views.register, name='register'),
    path('login/', views.login, name='login'),
    path('logout/', views.logout, name='logout'),
]
```

### Admin.py 설정
```python
from django.contrib import admin
from .models import User
# Register your models here.
class UserAdmin(admin.ModelAdmin):
    list_display = ('username', 'password')
admin.site.register(User, UserAdmin)
```

### Forms.py 생성

user/forms.py 생성

```python
from django import forms
from .models import User
from django.contrib.auth.hashers import check_password

class LoginForm(forms.Form):
    username = forms.CharField(
        error_messages={
            'required': '아이디를 입력해주세요'
        },
        max_length=32, label="사용자 이름")
    password = forms.CharField(
        error_messages={
            'required': '비밀번호를 입력해주세요'
        },
        widget=forms.PasswordInput, label="비밀번호")
    # 비밀번호를 패스워드 타입으로 만들기 위해선 위젯을 직접 지정해야 한다.

    # 비밀번호 일치여부 확인하는 코드
    def clean(self):
        cleaned_data = super().clean() # 값이 들어있지 않으면 여기서 실패처리해서 나간다.
        # 값이 들어왔다는 검증이 끝나면 값을 가지고 온 후에
        username = cleaned_data.get('username')
        password = cleaned_data.get('password')

        if username and password:
            try:
                user = User.objects.get(username=username)
            except User.DoesNotExist:
                self.add_error('username', '아이디가 없습니다.')
                return # 예외 이후 코드의 진행을 return을 통해 막음

            if not check_password(password, user.password):
                self.add_error('password', '비밀번호가 틀렸습니다.') # 특정 필드에 에러를 넣는 함수
            else:
                self.user_id = user.id # 이렇게 하면 self를 통해서 클래스 변수로 들어가고 밖에서도 접근할 수 있게 된다.
```

### Templates 생성

user/templates 폴더 생성

#### Base.html

user/templates/base.html
{% raw %}
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <title>Base</title>
    <!-- head 태그내에 Bootstrap 태그가 오면 된다. -->
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

#### Home.html 
{% raw %}
```html
{% extends 'base.html' %}

{% block contents %}
<!--  Home  -->
<div class="row mt-5">
  <div class="col-12 text-center">
    <h1>Home Page</h1>
  </div>
</div>

<div class="row mt-5">
  {% if request.session.user %}
    <div class="col-12">
      <button class="btn btn-primary btn-block" onclick="location.href='/user/logout/'">Logout</button>
    </div>
  {% else %}
    <div class="col-6">
      <button class="btn btn-primary btn-block" onclick="location.href='/user/login/'">Login</button>
    </div>
    <div class="col-6">
      <button class="btn btn-primary btn-block" onclick="location.href='/user/register/'">Sign in</button>
    </div>
  {% endif %}
</div>

<div class="row mt-1">
  <div class="col-12">
    <button class="btn btn-primary btn-block" onclick="location.href='/board/list/'">View Post</button>
  </div>
</div>
<!--  //Home  -->
{% endblock %}
```
{% endraw %}
#### Login.html
{% raw %}
```html
{% extends 'base.html' %}

{% block contents %}
<!--  Login  -->
<div class="row mt-5">
    <div class="col-12 text-center">
        <h1>로그인</h1>
    </div>
</div>

<div class="row mt-5">
    <div class="col-12">
        {{ info_error }}
        {{ password_error }}
        {{ password_valid }}
    </div>
</div>

<div class="row mt-5">
    <div class="col-12">
        <form method="post" action=".">
            {% csrf_token %}
            {% for field in form %}
                <div class="form-group">
                    <label for="{{ field.id_for_label }}">{{ field.label }}</label>
                    <input type="{{ field.field.widget.input_type }}" class="form-control" id="{{ field.id_for_label }}"
                           placeholder="{{ field.label }}" name="{{ field.name }}" />
                </div>
                {% if field.errors %}
                    <span style="color: red">{{ field.errors }}</span>
                {% endif %}
            {% endfor %}
          <button type="submit" class="btn btn-primary">로그인</button>
        </form>
    </div>
</div>
<!--  //Login  -->
{% endblock %}
```
{% endraw %}
#### Register.html
{% raw %}
```html
{% extends 'base.html' %}

{% block contents %}
<div class="row mt-5">
    <div class="col-12 text-center">
        <h1>회원가입</h1>
    </div>
</div>

<div class="row mt-5">
    <div class="col-12">
        {{ info_error }}
    </div>
</div>

<div class="row mt-5">
    <div class="col-12">
        <form method="post" action=".">
          {% csrf_token %}
          <div class="form-group">
            <label for="username">사용자 이름</label>
            <input type="text" class="form-control" id="username" name="username" placeholder="사용자 이름">
          </div>
          <div class="form-group">
            <label for="password">비밀번호</label>
            <input type="password" class="form-control" placeholder="비밀번호" id="password" name="password">
                {{ password_error }}
          </div>
          <div class="form-group">
            <label for="re-password">비밀번호 확인</label>
            <input type="password" class="form-control" placeholder="비밀번호 확인" id="re-password" name="re-password">
                {{ password_error }}
          </div>
          <div class="form-group">
            <label for="useremail">이메일 주소</label>
            <input type="email" class="form-control" placeholder="이메일 주소" id="useremail" name="useremail">
          </div>

          <button type="submit" class="btn btn-primary">등록</button>
        </form>
    </div>
</div>

{% endblock %}
```
{% endraw %}