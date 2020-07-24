---
title:  "Selenium 사용하여 Crawling 하기"
excerpt: "selenium"
categories:
  - blog
tags:
  - [blog, diary, dev, python]
last_modified_at: 2020-07-17 T20:00:00
---

## Selenium 사용하기


### Selenium 설치

```zsh
$ pip install selenium
```

### Chrome WebDriver 설치하기

https://chromedriver.chromium.org/downloads 접속 후 Chrome 버전에 맞게 설치

### Selenium 사용하여 네이버 로그인하기

#### Naver 접속하기
```python
from selenium import webdriver
import time

driver = webdriver.Chrome('chrome driver 설치된 경로')

driver.get("https://www.naver.com") # naver 주소로 접속한다.

driver.maximize_window() # 창을 최대화한다.

time.sleep(5) # 5초간 멈춤 상태

driver.close() # 브라우저를 종료한다.
```

#### Naver 로그인창 들어가기

##### 주소를 이용해 접속
```python
from selenium import webdriver
import time

driver = webdriver.Chrome('chrome driver 설치된 경로')

driver.get("https://www.naver.com")

driver.maximize_window()

time.sleep(5)

driver.get('https://nid.naver.com/nidlogin.login?mode=form&url=https%3A%2F%2Fwww.naver.com')
```
