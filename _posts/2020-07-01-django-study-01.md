---
title:  "Polls App"
excerpt: "django 로 Polls app 만들기"
categories:
  - blog
tags:
  - [blog, django, dev, django]
last_modified_at: 2020-07-02 T17:00:00
---
## Polls App 만들기

Django 를 이용해 Polls app 을 만들어 본다.

### Project 생성
***
```zsh
mkdir polls_app
```
```zsh
django-admin startproject config .
```
```
python manage.py migrate
```

### App 생성
***
```zsh
python manage.py startapp polls
```

### Settings.py 수정  
***
* config / settings.py

```python
INSTALLED_APPS = [
  'django.contrib.admin',
  'django.contrib.auth',
  'django.contrib.contenttypes',
  'django.contrib.sessions',
  'django.contrib.messages',
  'django.contrib.staticfiles',
  'polls', # 추가
]
```

### Model
***
* polls / models.py  

```python
from django.db import models

# 질문 모델 생성
class Question(models.Model):
    question_text = models.CharField(max_length=200) # 질문 글자수 최대 200자 설정
    pub_date = models.DateTimeField('date published') # 시간대별 정렬을 위해 설정
    # 객체를 문자열로 표현할 때 사용하는 함수
    def __str__(self):
        return self.question_text
# 선택지 모델 생성
class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE) # 질문을 받아온다
    choice_text = models.CharField(max_length=200) # 선택지 글자수 최대 200자 설정
    votes = models.IntegerField(default=0) # 투표 결과값 기본 0으로 설정
    def __str__(self):
        return self.choice_text
```

### Database 생성
***
```zsh
python manage.py makemigrations polls
```
```zsh
python manage.py migrate
```

### Admin
***
```zsh
python manage.py createsuperuser
ID 입력
e-mail 입력
password 입력
```

* polls / admin.py

```python
from django.contrib import admin
from .models import Question, Choice

# admin 사이트에서 선택지 설정
class ChoiceInline(admin.TabularInline):
    model = Choice
    extra = 3
class QuestionAdmin(admin.ModelAdmin):
    inlines = [ChoiceInline]
admin.site.register(Question, QuestionAdmin)
```

### 관리자 페이지에서 Question 생성
***
http://127.0.0.1:8000/admin/ 접속 후 생성한 ID로 로그인

생성된 Quesiton 확인

<img src="/assets/images/django/polls_admin01.png">

Question 접속 / Add Question 클릭

Question 내용 작성 후 SAVE

<img src="/assets/images/django/polls_admin02.png">

#### View
***
* polls / views.py 수정

#### Index View
***
```python
from .models import Question

# 기본 화면
def index(request):
    latest_question_list = Question.objects.order_by("-pub_date") # 시간 순서대로 질문 목록 출력
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
```
#### Detail View
***
```python
from django.shortcuts import render, get_object_or_404

# 상세 화면
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id) # object를 가져오거나 없을 시 404 화면 출력
    return render(request, 'polls/detail.html', {'question': question})
```

#### Results View
***
```python
# 결과 화면
def results(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/results.html', {'question': question})
```

#### Vote view
***
```python
from django.http import HttpResponseRedirect
# 투표 화면
def vote(request, question_id):
    question = Question.objects.get(pk=question_id)
    selected_choice = question.choice_set.get(pk=request.POST['choice'])
    selected_choice.votes += 1 # 투표 시 결과에 + 1
    selected_choice.save() # 결과값 저장
    return HttpResponseRedirect('results') # 결과 화면으로 돌아간다
```

### Url
***
* polls / urls.py 생성

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'), # 기본 접속 시 url
    path('<int:question_id>/', views.detail, name="detail"), # 상세 화면 url
    path('<int:question_id>/results', views.results, name='results'), # 결과 화면 url
    path('<int:question_id>/vote', views.vote, name='vote'), # 투표 화면 url
]
```

* config / urls.py 수정

```python
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('polls/', include('polls.urls')) # polls app 에서 설정한 url 가져오기
]
```

### Templates
***
* polls / templates / polls 폴더 생성

#### Index.html
***
* polls / templates / polls / index.html 생성

{% raw %}
```html
# 기본 화면 템플릿
<body>

{% if latest_question_list %}
    <ul>
        {% for question in latest_question_list %}
            <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
        {% endfor %}
    </ul>
{% else %}
    <p>No polls are available</p>
{% endif %}

</body>
```
{% endraw %}

#### Detail.html
***
* polls / templates / polls / detail.html 생성

{% raw %}
```html
# 상세 화면 템플릿
<body>
    <h1>{{ question.question_text }}</h1>
      <form action="/polls/{{ question.id }}/vote" method="post">
        {% for choice in question.choice_set.all %}
          <input type="radio" name="choice" value="{{ choice.id }}">{{ choice.choice_text }}</input><br>
        {% endfor %}
        <input type="submit" value="vote">
    </form>
</body>
```
{% endraw %}

#### Results.html
***
* polls / templates / polls / results.html 생성

{% raw %}
```html
# 결과 화면 템플릿
<body>
    <h1>{{ question.question_text }}</h1>
    <ul>
        {% for choice in question.choice_set.all %}
            <li>{{ choice.choice_text }}:{{ choice.votes }}</li>
        {% endfor %}
    </ul>
</body>
```
{% endraw %}

### CSRF 공격 방어 기능 삭제
***
* config / settings.py

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    # 'django.middleware.csrf.CsrfViewMiddleware', # 주석 처리
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

### App 실행
***
```zsh
python manage.py runserver
```

http://127.0.0.1:8000/polls/ 접속

### Polls 실행 화면
#### 투표 기본 화면
***
<img src="/assets/images/django/polls_result01.png">

#### 투표 상세 화면
***
<img src="/assets/images/django/polls_result02.png">

#### 투표 결과 화면
***
<img src="/assets/images/django/polls_result03.png">

