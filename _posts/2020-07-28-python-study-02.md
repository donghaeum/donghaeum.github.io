---
title:  "Selenium 사용하여 Crawling 하기 2"
excerpt: "selenium"
categories:
  - blog
tags:
  - [blog, diary, dev, python]
last_modified_at: 2020-07-28 T20:00:00
---

### Selenium 사용하여 Instagram 해시태그 크롤링 하기

```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from bs4 import BeautifulSoup
import time
from tqdm import tqdm
import pandas as pd
from urllib.request import urlopen, Request

# selenium의 webdriver로 크롬 브라우저를 실행한다
driver = webdriver.Chrome("chrome driver 설치된 경로")

# "Instagram"에 접속한다
driver.get("https://www.instagram.com/")

# 창을 최대화 한다
driver.maximize_window()

# 3초간 sleep
time.sleep(3)

# 인스타그램에 로그인하기
login_elem = driver.find_element_by_name("username") # 로그인 화면에서 아이디 입력창의 태그를 찾는다
login_elem.send_keys("instagram id", Keys.TAB, "instagram pwd") # 아이디 입력 후 tab키 사용 후 비밀번호 입력
login_elem.submit() # 로그인하기

time.sleep(5)

# '음식물 오염 제거' 결과물들의 해시태그를 크롤링해온다
driver.get("https://www.instagram.com/explore/tags/음식물오염제거/")

time.sleep(5)

SCROLL_PAUSE_TIME = 1.0
reallink = []

while True:
    pageString = driver.page_source
    bsObj = BeautifulSoup(pageString, 'lxml')

    for link1 in bsObj.find_all(name='div', attrs={"class":"Nnq7C weEfm"}):
        for i in range(3):
            title = link1.select('a')[i]
            real = title.attrs['href']
            reallink.append(real)
    last_height = driver.execute_script('return document.body.scrollHeight')
    driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
    time.sleep(SCROLL_PAUSE_TIME)
    new_height = driver.execute_script("return document.body.scrollHeight")

    if new_height == last_height:
        driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
        time.sleep(SCROLL_PAUSE_TIME)
        new_height = driver.execute_script("return document.body.scrollHeight")

        if new_height == last_height:
            break
        else:
            last_height = new_height
            continue

num_of_data = len(reallink)

print('총 {0}개의 데이터를 수집합니다.'.format(num_of_data))
csvtext = []

for i in tqdm(range(num_of_data)):

    csvtext.append([])
    req = Request("https://www.instagram.com/p"+reallink[i], headers={'User-Agent': 'Mozila/5.0'})

    webpage = urlopen(req).read()
    soup = BeautifulSoup(webpage, 'lxml', from_encoding='utf-8')
    soup1 = soup.find('meta', attrs={'property':"og:description"})

    reallink1 = soup1['content']
    reallink1 = reallink1[reallink1.find("@") + 1:reallink1.find(")")]
    reallink1 = reallink1[:20]

    if reallink1 == '':
        reallink1 = "Null"
    csvtext[i].append(reallink1)

    for reallink2 in soup.find_all('meta', attrs={'property':"instapp:hashtags"}):
        hashtags = reallink2['content'].rstrip(',')
        csvtext[i].append(hashtags)

    # csv로 저장

    data = pd.DataFrame(csvtext)
    data.to_csv('insta.txt', encoding='utf-8')

driver.close()
```