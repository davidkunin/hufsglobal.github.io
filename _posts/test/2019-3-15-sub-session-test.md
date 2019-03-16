---
layout: post
title: 파이썬으로 네이버 뉴스 크롤링 하기.
category: test
---
예비 세션 2 - prod. 승윤


# 네이버 실시간 뉴스 크롤링 

## 1. '웹 크롤링' 이란?

* 웹 사이트들에서 원하는 정보를 추출하는 것으로, '웹 스크래핑(Web Scraping)'이라고도 부른다.
* 라이브러리 : urlopen
* 라이브러리의 의미와 urlopen 라이브러리의 기능 소개 및 설명

## 2. '파싱(Parsing)' 이란?

* 파싱(parsing)은 끌어온 어떠한 문서에서 내가 원하는 데이터를 특정한 패턴이나 순서로 추출하여 가공하는 과정을 의미한다.
* 라이브러리 bs4 BeautifulSoup 
* BeaurifulSoup의 기능 소개 및 설명

## 3. 프로젝트 및 크롤링결과 표출할 기본 앱과 페이지 생성하기.

{% highlight html %}
 python –m venv myvenv
  
 source myvenv/Scripts/activate
  
 pip install django  
  
 django-admin startproject crawlingsession
  
 cd crawlingsession
  
 python manage.py startapp crawlingsessionapp
{% endhighlight %}

* setting.py 파일에 만든 앱 폴더 등록하기
* app폴더 templates 폴더 생성하고 home.html 파일 만들기
* views 파일에 앱 임포트후 home 함수 추가 및 url 설정하기.

## 4. bs4 BeautifulSoup 설치하기.
{% highlight html %}
pip install bs4
{% endhighlight %}

## 5. views 파일에 rullib 라이브러리와 설치한 BeautifulSoup 임포트하기.
{% highlight html %}
import urllib.request
from urllib.request import urlopen
import bs4
{% endhighlight %}

## 6. 네이버 뉴스 페이지 소스 긁어오기.
{% highlight html %}
from django.shortcuts import render

import urllib.request
from urllib.request import urlopen
import bs4

def home(request):
    
    url = "https://news.naver.com/"
    html = urlopen(url)

    
    bsobj = bs4.BeautifulSoup(html.read(), "html.parser")

    return render(request, 'home.html', {'show':bsobj})
{% endhighlight %}

* 코드 설명
* 실시간 뉴스 제목 검색해서 뜨는지 확인해 보기.

## 7. 특정 id 블럭의 정보 뽑아내기.

* 네이버 뉴스 --> 개발자 도구를 통해서 페이지 소스 분석하기 및 설명
* bs4의 find 기능을 활용하여, 뽑아내고자 하는 id 블럭 찾아내기.
* 이번에는 실시간 분야별 메인 뉴스를 뽑아낸다.

{% highlight html %}
bsobj2 = bsobj.find("div", {"id":"main_content"})
{% endhighlight %}

* bsobj2를 넘겨서 출력해보면, 실시간 주요 뉴스의 정보만 뽑아져 나오는 것을 확인 할 수 있음.

## 8. 뉴스 제목만 뽑아내기.

* 뉴스 제목만 뽑아내기 위해, 네이버 뉴스 페이지소스에서 제목이 어떠한 태그 블록에 있는지 찾아보기.
* 네이버 뉴스 제목들이  strong 태그들에 포함 되어있는것 확인하기.
* stong 에 포함되어있는 제목들만 뽑아내기.

{% highlight html %}
 from django.shortcuts import render

import urllib.request
from urllib.request import urlopen
import bs4

def home(request):
    
    url = "https://news.naver.com/"
    html = urlopen(url)
    bsobj = bs4.BeautifulSoup(html.read(), "html.parser")
    bsobj2 = bsobj.find("div", {"id":"main_content"})

    title = bsobj2.findAll("strong")

    return render(request, 'home.html', {'show':title})
{% endhighlight %}

* 하지만, 리스트마다 strong 태그까지 모두 뽑아온 것을 확인 할 수 있다.

## 4. 뽑아낸 제목을 처리하기 쉽도록 strong 태그 없애주기.
* bs4의 text 클래스를 활용.
{% highlight html %}
from django.shortcuts import render

import urllib.request
from urllib.request import urlopen
import bs4

def home(request):
    
    url = "https://news.naver.com/"
    html = urlopen(url)

    
    bsobj = bs4.BeautifulSoup(html.read(), "html.parser")

    bsobj2 = bsobj.find("div", {"id":"main_content"})

    title = bsobj2.findAll("strong")

    title_len = len(lis)
    for i in range(title_len):
        title[i] = title[i].text



    return render(request, 'home.html', {'show':title})
{% endhighlight %}

* 코드 설명하기. (리스트 길이 len, for문 range, title[] 리스트 개념, bs4의 text 기능)
* strong태그가 사라진 것 확인.
* 제목을 보기 쉽게 for문을 써서 다시 출력해 보자. (home.html파일)

{% highlight html %}
{%for title in show%}

기사 제목 : {{title}}<br><br>

{%endfor%} 
{% endhighlight %}

# 추가 내용(wordcount 세션 이후 --> default dictionary 활용하여 뉴스 제목 단어 세어보기).
 ---
 
## 1. default dictionary를 활용하여, 불러온 제목의 단어를 세어보자.

* default dictionary 임포트하기.
{% highlight html %}
from collections import defaultdict
{% endhighlight %}

* default dictionary 기능 설명.

* 리스트를 한개의 문자열로 모두 합치기.
{% highlight html %}
    one_list = ' '.join(title)
{% endhighlight %}

* default dictionary 호출하여 단어 갯수를 담을 사전 초기화하기.
{% highlight html %}
    d=defaultdict()
    d=defaultdict(lambda:0)
{% endhighlight %}

* 단어 쪼개서 갯수 세기.

{% highlight html %}
words = one_list.split(" ")
    
    for i in words:
        d[i] = d[i]+1
{% endhighlight %}

* item 함수로 사전 넘기기. (전체코드)
{% highlight html %}
from django.shortcuts import render

from collections import defaultdict
import urllib.request
from urllib.request import urlopen
import bs4

def home(request):
    
    url = "https://news.naver.com/"
    html = urlopen(url)

    
    bsobj = bs4.BeautifulSoup(html.read(), "html.parser")

    bsobj2 = bsobj.find("div", {"id":"main_content"})

    title = bsobj2.findAll("strong")

    title_len = len(title)
    for i in range(title_len):
        title[i] = title[i].text
    
    one_list = ' '.join(title)

    d=defaultdict()
    d=defaultdict(lambda:0)

    words = one_list.split(" ")
    
    for i in words:
        d[i] = d[i]+1

    return render(request, 'home.html', {'show':title, 'num_c':d.items()})
{% endhighlight %}
 
* 단어 갯수 출력하기.
{% highlight html %}

<h1>실시간 뉴스</h1>
{{show}}

{%for title in show%}

기사 제목 : {{title}}<br><br>

{%endfor%} 

<br>
{% for word, number in num_c %}
    {{word}} : {{number}} 개
<br>
{%endfor%}
{% endhighlight %}
 
# PPT참고

[예비세션2.pdf](https://github.com/seungyuns/newbitonproject/files/2973893/sub_session2.pdf)



