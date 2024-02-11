---
title: '한글 URL이 포함된 사이트맵(sitemap.xml) 작성 가이드'
description: "한글 URL을 포함하는 사이트맵(sitemap.xml) 작성 방법에 대한 가이드이다. 퍼센트 인코딩의 중요성, 표준 준수 방법, 검색 엔진 최적화(SEO) 팁을 포함하여, 웹 개발자와 웹사이트 소유주가 글로벌 및 로컬 검색 엔진에서의 가시성을 높일 수 있도록 도와준다."
date: '2024-02-11'
tags: ['한글 URL', 사이트맵(sitemap.xml), 퍼센트 인코딩(Percent Encoding), SEO(검색 엔진 최적화), 플라스크(Flask)]
---
# 1. 서론

![블로그의 태그별 아티클 페이지](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/guide-creating-sitemap-korean-urls-1.png)

현재 필자의 블로그에 태그를 통해 아티클을 모아 볼 수 있도록 태그별 아티클 페이지를 구현했다. 새로운 페이지를 추가한 만큼 `sitemap.xml`에 반영하는 작업을 진행해야 한다. 필자는 `yoonminlee.com/{태그명}` 형태로 라우팅하기로 결정했는데, 보통의 경우는 영어 slug를 통해 라우팅했지만, 이 경우 URL에 한글이 포함되는 것이 불가피하다. 그러다 문득 걱정이 됐다. 한글로 사이트맵을 작성해도 상관 없을까? 인코딩이 필요할까? 해답을 찾아가는 과정을 작성해본다.

# 한글이 포함된 URL을 sitemap.xml에 표시하는 최적의 방법

만약 `웹 개발(Web Development)`이라는 태그가 존재하고, 이 태그를 보여주는 URL이 `yoonminlee.com/웹-개발(Web-Development)` 이라면, 아래 둘 중 어떻게 사이트맵을 작성해야하는지 궁금할 것이다:

```xml
<url>
    <loc>https://yoonminlee.com/웹-개발(Web-Development)</loc>
    <lastmod>2023-11-23</lastmod>
    <priority>0.6</priority>
</url>

or

<url>
    <loc>https://yoonminlee.com/%EC%9B%B9-%EA%B0%9C%EB%B0%9C(Web-Development)</loc>
    <lastmod>2023-11-23</lastmod>
    <priority>0.6</priority>
</url>
```

# 권장하는 방법

결론부터 이야기하자면, 사이트맵 파일에서 URL을 지정할 때는 퍼센트 인코딩된 형태를 사용하는 것이 권장된다. 이는 XML 문서에서 유효한 URL을 보장하고, 다양한 검색 엔진과의 호환성을 유지하기 위한 것이다. URL에 한글 또는 다른 비ASCII 문자가 포함되어 있는 경우, 퍼센트 인코딩은 이러한 문자들이 XML 문서 내에서 올바르게 해석되고 처리될 수 있도록 한다.

# 왜 퍼센트 인코딩된 URL을 권장하는가?

1. **표준 준수**: URI(Uniform Resource Identifiers) 표준(RFC 3986)에 따르면, URI에는 ASCII 문자 집합 외의 문자를 포함시킬 수 없다. 따라서 한글과 같은 비ASCII 문자는 퍼센트 인코딩을 통해 ASCII 문자로 변환되어야 한다.
2. **검색 엔진 최적화**: 검색 엔진 크롤러는 퍼센트 인코딩된 URL을 올바르게 처리할 수 있도록 설계되어 있다. 퍼센트 인코딩을 사용함으로써 모든 검색 엔진이 사이트맵 내의 URL을 정확히 이해하고 색인화할 수 있다.
3. **범용성과 호환성**: 퍼센트 인코딩된 URL은 다양한 시스템과 소프트웨어에서 널리 지원되며, 글로벌 환경에서의 호환성을 보장한다.

## 예시

- **비인코딩 URL**: `http://example.com/한글`
- **퍼센트 인코딩 URL**: `http://example.com/%ED%95%9C%EA%B8%80`

# 퍼센트 인코딩 방법

대부분의 프로그래밍 언어나 웹 프레임워크는 URL을 퍼센트 인코딩하는 함수나 메서드를 제공하므로, 사이트맵을 생성할 때 이러한 기능을 활용할 수 있다. 필자의 경우 아래 코드와 같이, 플라스크(Flask) 웹 프레임워크에서 태그 혹은 포스팅 DB에 접근하여 사이트맵 생성을 자동화해놓았기 때문에 자동적으로 퍼센트 인코딩 된 값으로 생성됐다:

```py
dynamodb = boto3.resource('dynamodb')
post_table = dynamodb.Table('Posts')

def get_post_data_and_tag_first_appearance():
    response = post_table.scan()
    post_data = [{'id': item['post_id'], 'date': item['date']} for item in response['Items']]
    
    # 태그와 그 최초 등장 날짜를 추적하는 딕셔너리
    tag_first_appearance = {}
    for item in response['Items']:
        if 'tags' in item and item['tags']:
            for tag in item['tags']:
                # 태그가 이전에 등장했는지 확인하고, 처음 등장하는 경우 혹은 더 이른 날짜가 있는 경우 업데이트
                if tag not in tag_first_appearance or item['date'] < tag_first_appearance[tag]:
                    tag_first_appearance[tag] = item['date']

    return post_data, tag_first_appearance

@app.route('/sitemap.xml', methods=['GET'])
def sitemap():
    post_data, tag_first_appearance = get_post_data_and_tag_first_appearance()
    
    # 모든 페이지 정보를 포함할 리스트 초기화
    all_pages = []

    # 메인 페이지 정보 추가
    for main_page in main_page_list:
        if main_page == 'home':
            main_page_url = url_for('path', path='', _external=True)
            all_pages.append({'loc': main_page_url, 'lastmod': '2023-11-22', 'priority': '1.0'})
        else:
            main_page_url = url_for('path', path=main_page, _external=True)
            all_pages.append({'loc': main_page_url, 'lastmod': '2024-02-06', 'priority': '0.8'})

    # 태그 페이지 정보 추가
    for tag, first_date in tag_first_appearance.items():
        tag_url = url_for('path', path=tag.replace(' ', '-'), _external=True)
        all_pages.append({'loc': tag_url, 'lastmod': first_date, 'priority': '0.6'})

    # 포스트 페이지 정보 추가
    for data in post_data:
        post_url = url_for('path', path=data['id'], _external=True)
        all_pages.append({'loc': post_url, 'lastmod': data['date'], 'priority': '0.5'})

    # 모든 페이지를 lastmod에 따라 오름차순으로 정렬, lastmod가 같으면 loc로 오름차순 정렬
    sorted_pages = sorted(all_pages, key=lambda x: (x['lastmod'], x['loc']))

    # XML 출력 생성
    xml_output = ['<?xml version="1.0" encoding="UTF-8"?>']
    xml_output.append('<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">')

    for page in sorted_pages:
        xml_output.append('<url>')
        xml_output.append(f'<loc>{page["loc"]}</loc>')
        xml_output.append(f'<lastmod>{page["lastmod"]}</lastmod>')
        xml_output.append(f'<priority>{page["priority"]}</priority>')
        xml_output.append('</url>')

    xml_output.append('</urlset>')
    return Response('\n'.join(xml_output), mimetype='application/xml')
```

생성된 XML 파일은 아래와 같다.

```xml
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
<url>
    <loc>https://yoonminlee.com/%EC%BD%94%EB%93%9C%EC%8A%A4%ED%85%8C%EC%9D%B4%EC%B8%A0(Codestates)-AI-%EB%B6%80%ED%8A%B8%EC%BA%A0%ED%94%84(Bootcamp)</loc>
    <lastmod>2021-12-22</lastmod>
    <priority>0.6</priority>
</url>
<url>
    <loc>https://yoonminlee.com/%ED%9A%8C%EA%B3%A0(Retrospect)</loc>
    <lastmod>2021-12-22</lastmod>
    <priority>0.6</priority>
</url>
<url>
    <loc>https://yoonminlee.com/codestates-ai-bootcamp-all-retrospect</loc>
    <lastmod>2021-12-22</lastmod>
    <priority>0.5</priority>
</url>
<url>
    <loc>https://yoonminlee.com/2021-retrospect</loc>
    <lastmod>2022-01-06</lastmod>
    <priority>0.5</priority>
</url>
<url>
    <loc>https://yoonminlee.com/codestates-ai-bootcamp-review</loc>
    <lastmod>2022-01-28</lastmod>
    <priority>0.5</priority>
</url>
<url>
    <loc>https://yoonminlee.com/2022-retrospect</loc>
    <lastmod>2023-01-01</lastmod>
    <priority>0.5</priority>
</url>
<url>
    <loc>https://yoonminlee.com/</loc>
    <lastmod>2023-11-22</lastmod>
    <priority>1.0</priority>
</url>
```


혹여 굳이 여러 사이트가 존재하지 않아서 하드 코딩으로 사이트맵을 작성하는 경우, [한글 URL을 인코딩 및 디코딩 해주는 사이트](http://www.hipenpal.com/tool/url-encode-and-decode-in-korean.php)를 이용하면 편하게 변환할 수 있기 때문에 추천한다.

![URL 인코딩/디코딩 사이트](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/guide-creating-sitemap-korean-urls-2.png)

# 마무리

## 관련 아티클

- [웹 배포 후 검색 노출(SEO) 세팅 완벽 가이드 (w/ 플라스크(Flask))](https://yoonminlee.com/web-deployment-seo-guide#4-sitemapxml)