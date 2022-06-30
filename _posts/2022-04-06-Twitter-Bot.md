---
title: Twitter Project - Github Actions를 이용해 학식 자동 트윗 봇
author: NadudAn
date:   2022-04-06 15:00:00 +0900
categories: [Portfolio, Projects, Twitter]
tag: [Portfolio, Projects, Twitter, Api, TwitterDeveloper]
---

### excerpt

tweeting University Cafeteria's Lunch Menus in twitter for myself. And learning how to crawl the data with python, and how to use a api.


# Info

<table> 
  <tr>
    <td>프로젝트명</td>
    <td>공주대(천안) 학식봇</td>
    <td>제작기간</td>
    <td>2022.04.03 ~ 2022.04.06 (4일)</td>
  </tr>
  <tr>
    <td>참여인원</td>
    <td>1명</td>
    <td>개발환경</td>
    <td>Python, Twitter API, Tweepy, Selenium, Github Action</td>
  </tr>
  <tr rowspan = 2>
    <td>목적</td>
    <td colspan = 3>
      <ol>
        <li>API 및 크롤링 익히기</li>
        <li>자동봇 만들어보기</li>
      </ol>
    </td>
  </tr>
  <tr rowspan = 6>
    <td>구현내용</td>
    <td colspan = 3>
      <ol>
        <li>파이썬을 이용한 크롤링으로 학교 홈페이지 내 데이터 가져오기</li>
        <li>트위터 API와 Tweepy를 사용해 트윗</li>
        <li>깃허브 액션을 이용해 매일 UTC 기준 01:00(한국시간 오전 10시)마다 학식 메뉴 트윗</li>
      </ol>
    </td>
  </tr>
  <tr>
    <td>기타</td>
    <td>개인 프로젝트</td>
    <td>결과물</td>
    <td><a href='https://twitter.com/KNU_Lunch_Menu'>@KNU_Lunch_Menu</a></td>
  </tr>
</table>

<hr>

## [Twitter Project] 공주대 학식 자동트윗봇

먼저 이 프로젝트를 처음으로 트위터 api를 사용했다. 꽤 복잡하고 어려웠지만 하다보니 할만했던 것 같다. 밤새 코딩하면서 재미를 느낄 수 있었다.

### Twitter Developer

![화면](https://user-images.githubusercontent.com/84761609/168482702-14d92f29-ca96-457a-bb7b-4023352aa565.png)

트위터 봇을 만드려면 트위터 디벨로퍼의 승인을 받은 트위터 계정이 필요하다. 승인 받는 방법은 구글링을 통해 따로 알아보길 바란다.
[트위터 디벨로퍼 승인받은 당시의 소감](https://blog.naver.com/dsd932/222690657076)

### Twitter api & Tweepy
봇을 만들기 위해선 4개의 키가 필요하다.

1. Api Key
2. Api Key Secret
3. Access Token
4. Access Token Secret

나는 이번 프로젝트에서 Tweepy, Selenium, Chrome Driver를 사용하였다. 
그런데, 자동봇을 만들기 위해 파이썬으로 코딩하여 트윗을 작성하면 알겠지만 개발자 계정에 트윗이 된다.
매번 봇을 만들 때마다 승인을 받는건 여간 귀찮은 일이 아니다. 따라서 트위터 디벨로퍼에서 발급받은
저 4개의 키를 이용해 자동봇 계정의 Access Token과 Access Token Secret을 만들 것이다.
해당 내용에 대해선 [블로그에 자세히 적어놓았다.](https://nadudan.github.io/TwitterApi-AccessToken-Error/)

### Github Environment

내 깃허브 코드를 보면 알겠지만 Api key나 Access token이 드러나있지 않고 Environment를 이용하여 환경에서 변수를 받아왔다.
해당 4개의 키를 그대로 드러내기엔 계정의 중요한 정보라 거부감이 든다.

![KakaoTalk_20220516_012834789](https://user-images.githubusercontent.com/84761609/168483328-d776383c-05af-4d2e-80a1-60db872f345b.png)

따라서 나는 Secret에 인증키들을 등록하는 방법을 사용하였다.

### workflow

나는 workflow를 윈도우 버전으로 작성하였다. 매일 UTC 00:00 기준으로 트윗이 올라가게끔 작성하였으며 트윗할 때마다 크롬 드라이버를 다운받아 unzip한 뒤 사용하도록 만들었다.
프로젝트의 더 자세한 코드는 https://github.com/NadudAn/KNU_Cafeteria_Menu에서 볼 수 있다.

```workflow
# This workflow will install Python dependencies, run tests and lint with a variety of Python versions
# For more information see: https://help.github.com/actions/language-and-framework-guides/using-python-with-github-actions

name: Python package

on:
  schedule: 
    - cron: '0 0 * * *'
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:

    runs-on: windows-latest
    #strategy:
      #fail-fast: false
      #matrix:
        #python-version: ["3.8", "3.9", "3.10"]

    steps:
    - uses: actions/checkout@v3
    - name: Set up Python 3.10
      uses: actions/setup-python@v3
      with:
        python-version: 3.10
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
        #if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
    - name: Download chromedriver
      run: |
        (New-Object System.Net.WebClient).DownloadFile('https://chromedriver.storage.googleapis.com/100.0.4896.60/chromedriver_win32.zip', 'chromedriver.zip')
        Expand-Archive .\chromedriver.zip .
        #tar -zxvf [chromedriver.zip] -C [chromedriver]
    - name: Tweets
      run: |
        python3 "main.py"
      env:
        TWITTER_API_KEY: ${{ secrets.TWITTER_API_KEY }}
        TWITTER_API_SECRET: ${{ secrets.TWITTER_API_SECRET }}
        TWITTER_ACCESS_TOKEN: ${{ secrets.TWITTER_ACCESS_TOKEN }}
        TWITTER_ACCESS_TOKEN_SECRET: ${{ secrets.TWITTER_ACCESS_TOKEN_SECRET }}
```

## 동작 화면

![Desktop View](https://user-images.githubusercontent.com/84761609/168483987-c0f0af12-ef92-4ae1-806e-0fdedd2bd78e.jpg){: style="max-width: 200px" .left}
![Desktop View](https://user-images.githubusercontent.com/84761609/168483989-9f98ecbb-21c4-4162-8671-525d17d639b6.jpg){: style="max-width: 200px" .left}

