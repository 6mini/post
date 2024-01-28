---
title: '웹 배포 후 검색 노출(SEO) 세팅 완벽 가이드 (w/ 플라스크(Flask))'
description: "웹 애플리케이션 개발 후 검색 엔진 최적화(SEO)를 통해 온라인 가시성을 높이는 방법을 소개한다. 메타 태그 최적화, Google Search Console 및 Naver Search Advisor 설정, sitemap.xml 생성 및 자동화, 그리고 robots.txt 파일 적용에 대한 실용적인 안내를 제공한다."
date: '2024-01-27'
tags: ['SEO(검색 엔진 최적화)', '메타 태그(Meta Tag)', 'OG(오픈 그래프)', '구글 서치 콘솔(Google Search Console)', '네이버 서치 어드바이저(Naver Search Advisor)', '사이트맵(sitemap.xml)', 'robots.txt', 플라스크(Flask)]
---
# 1. 서론

웹 애플리케이션을 오랜 시간에 걸쳐 성공적으로 개발하고, 수차례 테스트를 진행한 후 배포까지 마치게 된다. 하지만 아무리 잘만든 서비스라도 사람들이 모르면 의미가 없는데, 배포만 한다고 해서 사람들이 알아서 찾아올 턱이 없다. 배포는 끝이 아니라 시작이다. 배포 후 어떻게 하면 우리가 심혈을 기울여 만든 서비스를 효과적으로 알릴 수 있을지에 대한 기본 세팅을 본 아티클을 통해 알아볼 것이다. 웹 서비스 마케팅 방법론 등의 이야기를 하려는 것은 아니고, 적어도 웹 배포를 했다면 '이 정도는 세팅해야 한다'는 내용을 다룰 것이다.

## 1.1 SEO

사람들에게 알릴 쉽고 간단하고 공짜인 방법으로, 'SEO(검색 엔진 최적화, Search Engine Optimization)'가 있다. SEO는 구글이나 네이버 등의 검색 엔진에서 웹사이트나 웹페이지를 더 높은 순위에 노출시키기 위한 방법과 기술을 뜻한다. 목적은 특정 키워드나 문구에 대한 검색 결과에서 웹사이트의 가시성을 향상시켜, 더 많은 방문자와 트래픽을 유치하는 것이다.

![구글에서의 'Yoonmin Lee' 검색 결과](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-deployment-seo-guide-18.png)

위처럼 `Yoonmin Lee`라는 키워드를 검색했을 때, 이 블로그가 전시되는 것 뿐 아니라, 상단에 노출되게 하기 위한 방법을 말한다. 방법론 자체는 키워드 최적화, 콘텐츠 품질, 백링크 구축, 사이트 구조 최적화, 모바일 최적화, 페이지 로딩 속도 개선 등이 존재하지만, 이 아티클에서는 검색했을 때 전시되게끔 초기 설정을 하는 것에 포커스를 맞춰 차례대로 기술할 것이다.

# 2. 메타 태그 최적화

웹사이트의 SEO를 개선하는 중요한 방법은 메타 태그 최적화이다. 메타 태그는 웹페이지의 HTML에 포함되어 검색 엔진 및 다른 웹 서비스에 페이지의 정보를 제공한다. 이 태그들은 페이지의 내용을 요약하고, 검색 결과에서 어떻게 표시될지 결정하는 데 중요한 역할을 한다.

## 2.1 메타 태그의 중요성

메타 태그는 검색 엔진이 웹페이지의 주제와 관련성을 이해하는 데 도움을 준다. 예를 들어, `description` 메타 태그는 검색 결과에서 페이지에 대한 간략한 설명을 제공하고, `keywords` 메타 태그는 페이지와 관련된 키워드를 나열한다. 이러한 정보는 검색 엔진이 페이지의 내용을 분류하고, 관련 검색 쿼리에 대한 결과로 페이지를 표시하는 데 사용된다.

## 2.2 메타 태그


### 2.2.1 기본 메타 태그

웹페이지에 포함시킬 수 있는 몇 가지 기본적인 메타 태그는 다음과 같다:

- `title`: 페이지의 제목을 지정한다. 이것은 검색 결과와 브라우저 탭에서 표시된다.
    - 웹페이지의 제목은 길어지지 않도록 주의해야 한다. 일반적으로 영문 기준 40자, 한글 기준 20자를 넘지 않는 것이 권장된다. 웹 페이지의 좀 더 상세한 설명은  `description`을 활용하면 된다.
- `description`: 페이지의 간단한 설명을 제공한다. 검색 결과에서 사용자에게 표시된다.
    - 웹페이지의 상세 설명은 일반적으로 영문 160자, 한글 80자 이내로 작성하는 것이 권장된다.
- `keywords`: 페이지 내용과 관련된 키워드 목록을 제공한다.
- `author`: 웹페이지의 저자나 소유자 정보를 제공한다.
- `viewport`: 모바일 장치에 대한 화면 크기와 초기 확대/축소 레벨을 설정한다.

예를 들어, 본 아티클에서 기본 메타 태그를 다음과 같이 작성할 수 있다:

```html
<!-- 기본 메타 태그 -->
<head>
    <title>웹 배포 후 검색 노출(SEO) 세팅 완벽 가이드 (w/ 플라스크(Flask)) | Yoonmin Lee</title>
    <meta name="description" content="웹 애플리케이션 개발 후 검색 엔진 최적화(SEO)를 통해 온라인 가시성을 높이는 방법을 소개한다. 메타 태그 최적화, Google Search Console 및 Naver Search Advisor 설정, sitemap.xml 생성 및 자동화, 그리고 robots.txt 파일 적용에 대한 실용적인 안내를 제공한다.">
    <meta name="keywords" content="SEO(검색 엔진 최적화), 메타 태그(Meta Tag), OG(오픈 그래프), 구글 서치 콘솔(Google Search Console), 네이버 서치 어드바이저(Naver Search Advisor), 사이트맵(sitemap.xml), robots.txt, 플라스크(Flask)">
    <meta name="author" content="Yoonmin Lee">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>
```

### 2.2.2 Open Graph 메타 태그

소셜 미디어에서의 공유를 최적화하기 위해 Open Graph 메타 태그를 사용할 수 있다. 이 태그는 카카오톡, 페이스북, 슬랙 등과 같은 소셜 미디어 플랫폼이 페이지를 어떻게 해석하고 표시할지 결정하는 데 사용된다. 웹페이지가 소셜 미디어에 공유될 때, 제목, 설명, 이미지 등의 정보를 제어한다.

- `og:title`: 공유될 때 표시되는 제목이다.
- `og:description`: 공유될 때 사용되는 간략한 설명이다.
- `og:image`: 공유될 때 표시되는 이미지 URL이다.
- `og:url`: 페이지의 정식 URL이다.
- `og:type`: 페이지의 유형(예: website, article)이다.
- `og:site_name`: 웹사이트의 이름을 정의한다.
- `og:locale`: 페이지의 언어 및 지역 설정을 지정한다.

예를 들어, 본 아티클에서 OG 메타 태그를 다음과 같이 작성할 수 있다:

```html
<meta property="og:title" content="웹 배포 후 검색 노출(SEO) 세팅 완벽 가이드 (w/ 플라스크(Flask)) | Yoonmin Lee">
<meta property="og:description" content="웹 애플리케이션 개발 후 검색 엔진 최적화(SEO)를 통해 온라인 가시성을 높이는 방법을 소개한다. 메타 태그 최적화, Google Search Console 및 Naver Search Advisor 설정, sitemap.xml 생성 및 자동화, 그리고 robots.txt 파일 적용에 대한 실용적인 안내를 제공한다.">
<meta property="og:image" content="https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/thumbnail.png">
<meta property="og:url" content="https://yoonminlee.com/web-deployment-seo-guide">
<meta property="og:type" content="article">
<meta property="og:site_name" content="Yoonmin Lee">
<meta property="og:locale" content="ko_KR">
```

### 2.2.3 트위터 카드 메타 태그

- 트위터 카드 메타 태그는 웹페이지가 트위터에 공유될 때 어떻게 표시될지를 결정한다.

- `twitter:card`: 트위터 카드의 유형을 지정한다. "summary"는 제목, 설명, 썸네일 이미지를 포함한 간단한 카드 형태이다.
- `twitter:title`: 트위터 공유 시 표시될 페이지의 제목이다.
- `twitter:description`: 트위터 공유 시 페이지의 간략한 설명이다.

예를 들어, 본 아티클에서 트위터 카드 메타 태그를 다음과 같이 작성할 수 있다:

```html
<meta name="twitter:card" content="summary">
<meta name="twitter:title" content="웹 배포 후 검색 노출(SEO) 세팅 완벽 가이드 (w/ 플라스크(Flask)) | Yoonmin Lee">
<meta name="twitter:description" content="웹 애플리케이션 개발 후 검색 엔진 최적화(SEO)를 통해 온라인 가시성을 높이는 방법을 소개한다. 메타 태그 최적화, Google Search Console 및 Naver Search Advisor 설정, sitemap.xml 생성 및 자동화, 그리고 robots.txt 파일 적용에 대한 실용적인 안내를 제공한다.">
```

## 2.3 동적 메타 태그 설정 및 적용 예

내 블로그와 같이 많은 페이지가 생겨나는 웹 애플리케이션의 경우, 일일이 메타 태그를 설정하는 것은 여간 번거로운 일이 아니다. 아래는 이 블로그에 적용한 Flask 웹 애플리케이션에 메타 태그를 동적으로 추가하는 방법이다.

### 2.3.1 HTML 템플릿 설정

`base.html` 파일(또는 웹사이트의 기본 레이아웃 템플릿)에 다음과 같이 메타 태그를 추가한다:

```html
<!DOCTYPE html>
<html lang="ko">
    <head>
        <!-- SEO Meta Tags -->
        <title>{% block base_head_title %}{% endblock %}Yoonmin Lee</title>
        <meta name="description" content="{% block base_head_description %}{% endblock %}">
        <meta name="keywords" content="이윤민, 블로그, Yoonmin Lee, blog{% block base_head_keywords %}{% endblock %}">
        <meta name="author" content="Yoonmin Lee">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        
        <!-- OG Meta Tags -->
        <meta property="og:title" content="{% block base_og_title %}{% endblock %}Yoonmin Lee">
        <meta property="og:description" content="{% block base_og_description %}{% endblock %}">
        <meta property="og:image" content="https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/thumbnail.png">
        <meta property="og:url" content="{% block base_og_url %}{% endblock %}">
        <meta property="og:type" content="{% block base_og_type %}{% endblock %}">
        <meta property="og:site_name" content="Yoonmin Lee">
        <meta property="og:locale" content="ko_KR">
        
        <!-- Twitter Meta Tags -->
        <meta name="twitter:card" content="summary">
        <meta name="twitter:title" content="{% block base_twitter_title %}{% endblock %}Yoonmin Lee">
        <meta name="twitter:description" content="{% block base_twitter_description %}{% endblock %}">
        
        <!-- 기타 필요한 태그 -->
    </head>
    <body>
        <!-- 페이지 내용 -->
    </body>
</html>
```

이렇게 설정하면 각 페이지에서 필요한 메타 정보를 제공할 수 있는 '블록'을 정의한다. 각 페이지에서 이 블록을 재정의하여 페이지별로 다른 메타 정보를 설정할 수 있다.

### 2.3.2 포스팅 HTML 적용 예

이후 만약 각 포스팅마다 적용하고 싶으면, 아래와 같은 `post.html`을 구성하여 포스팅마다 동적인 메타 태그 값을 부여할 수 있다.

```html
{% extends "base.html" %}

{% block base_head_title %}{{ post.title }} | {% endblock %}
{% block base_og_title %}{{ post.title }} | {% endblock %}
{% block base_twitter_title %}{{ post.title }} | {% endblock %}

{% block base_head_description %}{{ post.description }}{% endblock %}
{% block base_og_description %}{{ post.description }}{% endblock %}
{% block base_twitter_description %}{{ post.description }}{% endblock %}

{% block base_og_type %}article{% endblock %}
{% block base_og_url %}https://yoonminlee.com/{{ post.post_id }}{% endblock %}

{% block base_head_keywords %}{% for tag in post.tags %}, {{ tag }}{% endfor %}{% endblock %}
```

메타 태그는 웹사이트의 SEO 전략에서 중요한 부분을 차지한다. 올바르게 설정하면 검색 엔진 결과의 가시성을 높이고, 소셜 미디어에서의 공유를 개선하여 트래픽과 참여도를 증가시킬 수 있다.

# 3. Search Console

이제 서치 콘솔(Search Console)에 웹사이트를 등록하는 작업을 진행한다. 서치 콘솔은 우리의 사이트가 각 검색 엔진 플랫폼의 검색 결과에서 어떻게 나타나는지를 모니터링하고 최적화할 수 있게 해주는 서비스이다. 구글은 [Google Search Console](https://search.google.com/search-console?resource_id=https%3A%2F%2F6ixtest.com%2F), 네이버는 [Naver Search Advisor](https://searchadvisor.naver.com/console/board)를 통해 제공하고 있다. 쉽게 말해, 각 검색 엔진 플랫폼의 우리 사이트의 존재를 알려주는 작업이다. 그럼 구글과 네이버에 어떻게 우리 사이트를 알려줄 수 있을지 알아보자.

## 3.1 Google Search Console

### 3.1.1 속성 추가

[Google Search Console](https://search.google.com/search-console?resource_id=https%3A%2F%2F6ixtest.com%2F)에 접속 후 먼저 소유권 확인을 진행해야한다.

![Google Search Console 내 속성 추가](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-deployment-seo-guide-1.png)

`속성 추가` 버튼을 눌러 속성을 추가한다.

![URL 접두어 입력](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-deployment-seo-guide-2.png)

필자는 이 블로그에서 `yoonminlee.com`이라는 단일 URL로만 구성하고, 하위 페이지는 모두 라우트를 통해 구현할 예정이기 때문에 `URL 접두어`로 진행했다. 만약 `m.domain.com`이나 `www.domain.com`처럼 하위 도메인을 사용한다면 DNS 인증을 진행하면 된다. URL 입력후 `계속` 버튼을 누른다.

![소유권 확인 절차: HTML 파일 다운로드](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-deployment-seo-guide-3.png)

`googleq1w2e3r4.html` 형태의 버튼을 클릭하여 파일을 다운로드한다. `https://domain.com/googleq1w2e3r4.html`이라는 URL로 접속 시 해당 파일이 전시되게끔 세팅해야한다.

### 3.1.2. Flask 적용

![다운로드한 파일을 templates 폴더에 추가](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-deployment-seo-guide-4.png)

다운로드한 HTML 파일을 `templates` 폴더에 위치 시킨 후, 플라스크 앱에 아래 코드를 추가한다:

```py
from flask import Flask, render_template

app = Flask(__name__)

@app.route('/googleq1w2e3r4.html')
def google_site_verification():
    return render_template('googleq1w2e3r4.html') 
```

코드 추가 후 앱 업데이트를 진행한다.

![HTML 파일 전시 확인](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-deployment-seo-guide-10.png)

이제 `https://domain.com/googleq1w2e3r4.html`에 접속해보면 성공적으로 HTML 파일이 전시된다.

### 3.1.3 소유권 확인

![소유권 확인 절차: 확인 버튼 클릭](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-deployment-seo-guide-12.png)

소유권 확인의 `확인` 버튼을 누르면 성공적으로 소유권이 확인된다.

![소유권 확인](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-deployment-seo-guide-13.png)

## 3.2 Naver Search Advisor

### 3.2.1 사이트 등록

![Naver Search Advisor 사이트 등록](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-deployment-seo-guide-6.png)

[Naver Search Advisor](https://searchadvisor.naver.com/console/board) 웹 마스터 도구의 `사이트 관리`에서 사이트를 등록한다.

![사이트 소유확인 절차: HTML 확인 파일 다운로드](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-deployment-seo-guide-7.png)

네이버도 구글과 유사하게 `HTML 파일 업로드 방식`으로 진행한다. `HTML 확인 파일` 링크를 클릭하여 파일을 다운로드한다.

### 3.2.2 Flask 적용

![다운로드한 파일을 templates 폴더에 추가](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-deployment-seo-guide-8.png)

다운로드한 HTML 확인 파일을 `templates` 폴더에 위치 시킨 후, 플라스크 앱에 아래 코드를 추가한다. 위에서 작업한 구글 인증 코드도 함께 명시했으니, 헷갈리지 말길 바란다.

```py
from flask import Flask, render_template

app = Flask(__name__)

@app.route('/googleq1w2e3r4.html')
def google_site_verification():
    return render_template('googleq1w2e3r4.html')

@app.route('/naverq1w2e3r4t5y6.html')
def naver_site_verification():
    return render_template('naverq1w2e3r4t5y6.html')
```

코드 추가 후 앱 업데이트를 진행한다.

![HTML 파일 전시 확인](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-deployment-seo-guide-11.png)

이제 `https://domain.com/naverq1w2e3r4t5y6.html`에 접속해보면 성공적으로 HTML 파일이 전시된다.

### 3.2.3 소유 확인

![사이트 소유확인 절차: 소유확인 버튼 클릭](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-deployment-seo-guide-15.png)

다시 `사이트 소유확인`에서 `소유확인` 버튼을 클릭하면 성공적으로 사이트 소유가 확인된다.

![사이트 소유 확인](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-deployment-seo-guide-14.png)


# 4. sitemap.xml

사이트맵(`sitemap.xml`)은 웹사이트의 모든 주요 페이지들을 목록화한 파일이다. 구글과 네이버 같은 검색 엔진이 웹사이트의 내용과 구조를 더 잘 이해하고, 효율적으로 웹사이트 내 페이지를 크롤링하도록 도와준다. 앞서 언급한 SEO에도 도움을 크게 주는 파일이라 꼭 작성 후 각 서치 콘솔에 등록해주는게 좋다.

보통 [XML-sitemap](https://www.xml-sitemaps.com/) 등의 서비스를 통해 수동으로 사이트맵을 생성하여 앱에 라우팅 시킨 후 제출한다. 하지만 난 블로그를 배포했기 때문에 수동으로 진행한다면 포스팅이 생길 때 마다 제출해야할 것이다. 그래서 이 포스팅에서는 사이트맵 라우팅 자동화 절차를 플라스크를 통해 진행하려한다.

## 4.1 Flask에서 `sitemap.xml` 생성 자동화

코드 자체는 아래와 같이 짰다. 코드 플로우는 `/sitemap.xml`으로 라우트가 되면, 현재 웹사이트에 정의되어 존재하는 메인 페이지를 `xml` 리스트에 추가한다. 그 후 블로그 포스트들의 ID와 날짜가 적재된 `Posts` 데이터베이스에 접근(여기서는 `DynamoDB`)하여 각 `post_id`와 `date`를 활용하여 전체 `xml`을 생성한다.

```py
from flask import Flask, url_for, Response
import boto3

app = Flask(__name__)

dynamodb = boto3.resource('dynamodb')
post_table = dynamodb.Table('Posts')

# DynamoDB에서 포스트의 ID와 날짜 정보를 가져오는 함수
def get_post_data():
    response = post_table.scan()
    post_data = [{'id': item['post_id'], 'date': item['date']} for item in response['Items']]
    return post_data

# 현재 정의된 메인 페이지 리스트
main_page_list = [
    'home', 'tag', 'about'
]

# 사이트맵 라우트
@app.route('/sitemap.xml', methods=['GET'])
def sitemap():
    post_data = get_post_data()
    post_data = sorted(post_data, key=lambda x: x['date']) # 날짜로 오름차순 정렬

    xml_output = ['<?xml version="1.0" encoding="UTF-8"?>']
    xml_output.append('<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">')

    # 메인 URL 추가
    for main_page in main_page_list:
        if main_page == 'home': # home일 경우 최상위로 표시
            main_page = ''      # 최상위로 경로로 표시
            priority = '1.0'    # 우선 순위 1.0 할당
        else:
            priority = '0.8'    # 나머지 메인 페이지 우선 순위 1.0 할당
            
        main_page_url = url_for('path', path=main_page, _external=True)
        xml_output.append('<url>')
        xml_output.append(f'<loc>{main_page_url}</loc>')
        xml_output.append(f'<lastmod>2023-12-01</lastmod>')
        xml_output.append(f'<priority>{priority}</priority>')
        xml_output.append('</url>')

    # 포스트 URL 추가
    for data in post_data:
        post_url = url_for('path', path=data['id'], _external=True)
        xml_output.append('<url>')
        xml_output.append(f'<loc>{post_url}</loc>')
        xml_output.append(f'<lastmod>{data["date"]}</lastmod>')
        xml_output.append('<priority>0.5</priority>') # 우선 순위 0.5 할당
        xml_output.append('</url>')

    xml_output.append('</urlset>')
    return Response('\n'.join(xml_output), mimetype='application/xml') # xml 출력
```

위와 같이 서버에서 즉각적으로 각종 URL을 불러오게 세팅을 한다면, 앞으로는 건들일 필요 없이 사이트맵을 자동화하여 관리할 수 있다.

![사이트맵 생성 확인](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-deployment-seo-guide-19.png)

이제 `/sitemap.xml`로 라우트하면 성공적으로 사이트맵이 생성된 모습을 확인할 수 있다. 참고로 `lastmod`는 페이지 생성일, `priority`는 페이지 중요도를 나타낸다.

## 4.2 각 서치 콘솔에 등록

### 4.2.1 Google Search Console

![Google Search Console 사이트맵 제출](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-deployment-seo-guide-20.png)

[Google Search Console](https://search.google.com/search-console?resource_id=https%3A%2F%2F6ixtest.com%2F) 메뉴의 `Sitemaps`로 접속하여 이전에 설정했던 `sitemap.xml`을 입력 후 제출한다.

![사이트맵 제출 확인](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-deployment-seo-guide-21.png)

아주 손쉽게 사이트맵의 제출을 확인할 수 있다.

### 4.2.2 Naver Search Advisor

![Naver Search Advisor 사이트맵 제출](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-deployment-seo-guide-22.png)

[Naver Search Advisor](https://searchadvisor.naver.com/console/board) `요청` 메뉴의 `사이트맵 제출`로 접속하여 사이트맵의 Full URL을 입력한 뒤 제출하면 네이버 또한 손쉽게 사이트맵 제출이 끝난다.



# 5. robot.txt

`robots.txt` 파일은 웹사이트의 루트 디렉토리에 위치하는 텍스트 파일로, 검색 엔진의 웹 크롤러(봇)에게 웹사이트의 특정 부분을 크롤링하지 말라고 지시하는 역할을 한다. 이 파일 또한 웹사이트의 SEO에 중요한 역할을 하고, 개인 정보 보호에 중요한 역할을 한다.

## 5.1 `robots.txt` 파일의 생성 및 구조

### 5.1.1 생성

블로그 루트 디렉토리에 `robots.txt` 파일을 만든다. 이 파일에는 검색 엔진 크롤러가 접근을 제한해야 하는 URL 경로에 대한 지시사항이 포함된다.

### 5.1.2 기본 구조

`robots.txt` 파일의 기본 구조는 다음과 같다:

```txt
User-agent: [크롤러 이름]
Disallow: [제한할 URL 경로]

Sitemap: [사이트맵 URL]
```

- `User-agent`: 지시사항이 적용될 특정 크롤러 또는 모든 크롤러(*)를 지정한다.
- `Disallow`: 크롤러가 접근을 금지해야 하는 URL 경로를 지정한다.
- `Sitemap`: 사이트맵의 위치를 제공하여 크롤러가 웹사이트의 구조를 더 잘 이해할 수 있도록 한다.

## 5.2 설정 예시

블로그에서 일반적으로 크롤링이 허용되는 경로와 제외되어야 할 경로를 정의한다. 예를 들어, 전체 사이트의 크롤링을 허용하면서 특정 경로(예: 404 오류 페이지)를 제외하는 설정은 다음과 같다:

```txt
User-agent: *
Disallow: /404/

Sitemap: https://domain.com/sitemap.xml
```

이 설정은 모든 크롤러가 `/404/` 경로를 제외한 사이트의 모든 부분을 크롤링할 수 있도록 허용한다. 또한, `Sitemap` 지시어를 통해 사이트맵의 위치를 명시하여 검색 엔진이 웹사이트의 전체 구조를 쉽게 이해하고 효율적으로 색인화할 수 있도록 한다.

## 5.3 Flask에서 `robots.txt` 적용

Flask 웹 애플리케이션에서 `robots.txt` 파일을 반환하는 라우트를 설정한다. `robots.txt` 파일을 애플리케이션의 `static` 폴더에 저장한 후, 다음과 같이 라우트를 설정한다:

```py
@app.route('/robots.txt')
def robots_txt():
    return app.send_static_file('robots.txt')
```

코드 추가 후 앱 업데이트를 진행한다.

![robots.txt 확인](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-deployment-seo-guide-23.png)

이제 `https://domain.com/robots.txt`에 접속해보면 성공적으로 `robots.txt` 파일이 전시딘다. 이로써 웹 크롤러가 `robots.txt` 파일에 명시된 지침에 따라 특정 페이지를 크롤링하지 않도록 할 수 있으며, 웹사이트의 SEO 전략을 보다 효과적으로 관리할 수 있다.

## 5.4 서치 콘솔 등록

[Google Search Console](https://search.google.com/search-console?resource_id=https%3A%2F%2F6ixtest.com%2F)의 경우 `robots.txt`를 따로 등록할 필요가 없다. 이에 따라 네이버만 진행한다.

### 5.4.1 Naver Search Advisor

![Naver Search Advisor robot.txt 수집요청](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-deployment-seo-guide-24.png)

[Naver Search Advisor](https://searchadvisor.naver.com/console/board) `검증` 메뉴의 `robots.txt`를 접속한 후 `수집요청` 버튼을 클릭한다.

![robot.txt 전시 확인](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-deployment-seo-guide-25.png)

수집된다면 상단의 창에 전시됨을 확인할 수 있다.

# 6. 마무리 및 참고

웹 애플리케이션 배포 후 필수적인 SEO 설정과 관련된 여러 단계들을 살펴보았다. 기본적인 메타 태그 설정을 통한 최적화부터, [Google Search Console](https://search.google.com/search-console?resource_id=https%3A%2F%2F6ixtest.com%2F)과 [Naver Search Advisor](https://searchadvisor.naver.com/console/board) 설정, `sitemap.xml`의 생성과 자동화, 그리고 `robots.txt` 파일의 적용 및 설정에 대해 다뤘다. 이 과정은 웹사이트가 검색 엔진에 잘 노출되도록 돕고, 결과적으로 더 많은 트래픽과 가시성을 확보하는 데 중요한 역할을 할 것이다.

다음 아티클에서는 웹사이트의 트래픽 분석과 수익 창출을 위해 중요한 두 가지 도구, [Google AdSense](https://www.google.com/adsense)와 [Google Analytics](https://analytics.google.com/)의 등록 및 연결 과정을 다룰 예정이다. 이 두 도구는 웹사이트의 성과를 분석하고, 이를 기반으로 수익을 창출하는 데 필수적인 역할을 한다. 본 아티클에서 다루려 했으나, SEO를 위한 설정과는 성격이 다르기 때문에 다음 아티클에서 다룰 것이다.

## 6.1 관련 아티클

- **자파(Zappa) 튜토리얼**
    1. [플라스크(Flask) 서버리스(Serverless) 배포](/zappa-flask-serverless-deployment)
    2. [사용자 정의 도메인(Custom Domain) 연결(Feat. AWS Route53 & 가비아(Gabia))](/zappa-custom-domain-route53-gabia)
    3. [스테이지(Stage) & 로그 레벨(Log level) 관리](/zappa-stage-log-level-management)
    4. [배포 해제 및 사용자 정의 도메인(Custom Domain) 제거](/zappa-undeploy-custom-domain-removal)
- [파비콘 만들기 및 적용 완벽 튜토리얼: Adobe Color, favicon.io, RealFaviconGenerator 사이트 활용](/favicon-creation-tutorial)

## 6.2 참고

- [스타트업을 위한 검색 노출(SEO) 가이드](https://yozm.wishket.com/magazine/detail/2256/)
- [Python에서 Xml 사이트맵 페이지 만들기](https://webisfree.com/2020-08-17/python%EC%97%90%EC%84%9C-xml-%EC%82%AC%EC%9D%B4%ED%8A%B8%EB%A7%B5-%ED%8E%98%EC%9D%B4%EC%A7%80-%EB%A7%8C%EB%93%A4%EA%B8%B0)
- [메타 태그를 통한 검색엔진 최적화 (SEO)](https://www.daleseo.com/html-meta-tags-for-seo/)