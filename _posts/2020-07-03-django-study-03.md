---
title:  "Project File"
excerpt: "Django project 기본 파일 설명"
categories:
  - blog
tags:
  - [blog, diary, dev]
last_modified_at: 2020-07-03T11:00:00
---

### Settings.py

settings.py 는 프로젝트 설정 파일입니다.  
처음 프로젝트를 생성하면 django 가 기본 사항들을 자동으로 등록해주므로, 추가적으로 필요한 사항은 개발을 진행하면서 개발자가 원하는 내용을 등록하면 됩니다.

* 데이터베이스 설정 : default 로 sqllite3 를 지정
* 애플리케이션 등록 : 프로젝트에 포함되는 애플리케이션들은 모두 설정 파일에 등록 - settings.py / INSTALLED_APPS
* 템플릿 항목 설정 : TEMPLATES 항목으로 지정
* 정적 파일 항목 설정 : STATIC_URL 등 관련 항목 지정
* 타임존 지정 : 세계표준시(UTC) 로 설정되어 있는데 한국 시간으로 변경해야한다.

### Models.py

models.py 는 테이블을 정의하는 파일입니다.  
데이터 베이스 처리는 ORM(Object Relational Mapping) 기법을 사용합니다.  
테이블을 class로 mapping 해서 테이블에 대한 CRUD(Create, Read, Update, Delete) 기능을 class 객체에 대해 수행하면, django 가 내부적으로 SQL 처리를 하여 데이터베이스에 반영해주는 방식입니다.

테이블의 신규 생성, 테이블의 정의 변경 등 models.py 파일에서 데이터베이스 변경 사항이 발생하면 이를 데이터베이스에 실제로 반영해주는 작업을 해야합니다.  
변경 사항 반영을 위해 `makemigrations` 와 `migrate` 명령어를 사용합니다.

### Urls.py

프로젝트 전체 URL 을 정의하는 프로젝트 URL 과 앱마다 정의하는 앱 URL, 2계층으로 나눠서 코딩을 합니다.

### Views.py

views.py 는 view 로직을 코딩하는 가장 중요한 파일입니다. 간단한 로직이면 몇 줄만 코딩하면 되지만 프로젝트 개발 범위가 커짐에 따라 로직도 점점 복잡해지고 views.py 의 코딩량도 많아집니다.   

**가독성과 유지보수 편리성, 재활용 등을 고려해야한다.**

### Templates

웹 화면 별로 template 파일(.html) 이 하나씩 필요하므로 웹 프로그램 개발 시 여러 개의 template 파일을 작성하게 되고 이러한 파일들을 한 곳에 모아두기 위해 디렉터리가 필요합니다.

template 디렉터리는 project template 디렉터리와 앱 template 디렉터리로 구분해서 사용합니다.  
project template 디렉터리에는 base.html 등 전체 프로젝트의 룩앤필(Look and Feel)에 관련된 파일들을 모아두고 각 앱에서 사용하는 template 파일들은 앱 template 디렉터리에 위치시킵니다.