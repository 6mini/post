---
title: '파비콘 만들기 및 적용 완벽 튜토리얼: Adobe Color, favicon.io, RealFaviconGenerator 사이트 활용'
description: "웹 개발자를 위한 간단하고 전문적인 파비콘 제작 및 적용 방법을 다양한 사이트와 함께 알아본다. Adobe Color로 컬러 추출부터, favicon.io를 이용한 파비콘 제작, RealFaviconGenerator로 다양한 브라우저와 플랫폼에 적용하는 방법까지 상세히 설명한다. 더 나아가 Flask에 적용하는 과정까지 소개한다."
date: '2024-01-28'
tags: [파비콘(favicon), Adobe Color, favicon.io, RealFaviconGenerator, 플라스크(Flask), 웹 개발(Web Development)]
---
# 1. 서론

매번 웹 애플리케이션 배포 프로젝트 진행하며 파비콘을 간단하게 만들 때, 필자가 유용하게 사용하고 있기 때문에 아카이브 겸 포스팅한다. 찾을 때마다 특정 키워드로 검색해도 제대로 안나오는 경우가 많기 때문이다. 이 방법은 굉장히 간단한 방법이면서도 전문적으로 세팅할 수 있기 때문에 많은 분들에게 유용하리라 생각이든다. 기본적으로 예쁜 파비콘 디자인을 위한 컬러 추출 사이트와 파비콘 생성 사이트, 모든 브라우저에 적용되도록 도와주는 사이트를 소개한다. 모두 웹 기반에서 동작되기 때문에 간단하게 사용할 수 있다.

일단 사용 예제는 현재 포스팅이 전시되고 있는 블로그의 파비콘을 만들 때 상황을 기반으로 플로우를 설명할 것이다.

# 2. 컬러 추출

파비콘의 컬러를 설정할 때 어느정도 웹 사이트와의 조화가 중요하다고 생각하기 때문에 웹사이트에서 사용된 포인트 컬러를 추출하여 파비콘에 적용하고자 한다.

![블로그 메인 화면](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/favicon-creation-tutorial-1.png)

블로그의 포인트 컬러는 이미지에서 보이는 검색창 배경에서 쓰인 색상이다. 개인적으로 다크모드 구현이 우선 순위였기 때문에, 좋아하는 색인 파란 계열을 다크모드와 라이트모드 둘 다 어우러지는 색으로 골랐다. 사실 개발을 하면서부터 색상 코드를 사용하여 등록했지만, 색상 코드를 모른다는 가정하에 플로우를 진행하겠다.

색상을 추출할 사이트는 바로 "[Adobe Color](https://color.adobe.com/ko/create/image)"이다. 사이트에 접속한다.

![Adobe Color 색상 추출 절차](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/favicon-creation-tutorial-2.png)

"색상" 탭에서 "이미지"를 선택하고, 파일을 드래그앤드롭하면 쉽게 색상을 추출할 수 있다.

![색상 코드 확인](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/favicon-creation-tutorial-3.png)

실제 색상이 있는 영역을 선택하여 컬러 코드를 따온다. "#5981D4"라는 색을 파비콘의 컬러로 사용할 것이다.

# 3. 파비콘 생성

추출한 색상을 토대로 간단하게 파비콘을 생성해보자. 파비콘 생성은 "[favicon.io](https://favicon.io/)"에서 진행한다.

![favicon.io 메인 화면](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/favicon-creation-tutorial-4.png)

이 사이트에서는 PNG 파일이나 텍스트, 이모지 등을 파비콘의 확장자로 변경할 수 있는데, 본 예제에서는 간단하게 텍스트로 진행할 것이다. 실제로 필자도 본 블로그의 파비콘을 텍스트로 만들었다.

![Generate From Text 화면](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/favicon-creation-tutorial-5.png)

개인적으로는 여기서 만드는 파비콘이 가장 단순하면서도 깔끔하고 있어보이게 만들어지는 것 같았다. 원하는 텍스트와 원하는 컬러 조합을 선택하면 끝이다. 추가적으로 마음에드는 폰트를 하나하나 눌러보면서 확인해보고 사이즈를 맞춰주면 된다.

![브라우저 탭 부분 확인](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/favicon-creation-tutorial-7.png)

브라우저 탭 부분에서의 파비콘도 함께 변경되니 이 부분을 함께 봐가면서 결정하는 것도 추천한다. 개인적으로 디테일이 마음에 드는 부분이다.

![블로그에 사용할 파비콘 제작 후 다운로드](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/favicon-creation-tutorial-6.png)

필자의 경우 이전 추출한 색상 코드 배경에 "Yoonmin Lee"의 첫 글자인 "Y"를 넣어서 만들었다. 개인적으로 "Y"라는 알파벳에 적절한 폰트를 찾는게 어려웠지만, "Archivo Black"을 적용하였다. 프리뷰를 확인하고 마음에 든다면 다운로드를 진행하면 된다. 파비콘 마련도 끝났다.

# 4. 모든 브라우저 환경으로의 파비콘 변환

이제 파비콘을 만들었으니, 모든 브라우저에서 적용되도록 변환해주는 사이트를 소개한다. "[RealFaviconGenerator](https://realfavicongenerator.net/)"에서 진행한다.

![RealFaviconGenerator의 "Select your Favicon image" 버튼 클릭](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/favicon-creation-tutorial-8.png)

하나의 파비콘 파일로 모든 브라우저 뿐 아니라, 모든 플랫폼에 직접 만든 파비콘이 아름답게 적용되도록 만들어주는 사이트이다. "Select your Favicon image" 버튼을 통해 아까 다운로드한 파비콘을 선택한다.

![512px 사이즈의 파비콘 선택](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/favicon-creation-tutorial-9.png)

가장 큰 512px 사이즈의 파비콘을 선택해준다.

![자동 변환 확인](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/favicon-creation-tutorial-10.png)

자동적으로 여러가지 브라우저 및 플랫폼에서 어떻게 보여지는지 확인할 수 있다. 크게 세부적으로 설정할 부분은 없는데, 신경써주면 좋을 부분만 명시할 것이다.

!["Windows Metro" 설정](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/favicon-creation-tutorial-11.png)

먼저 윈도우 메트로(Windows Metro). 아이콘 뒤 배경색을 설정해준다. 멋있게 "Y"만 아이콘으로 두고 배경색을 파란색으로 지정하고 싶었지만, 귀찮으니까 넘어간다. 블로그가 더 유명해지면 설정하는 것으로 하자.

!["macOS Safari" 설정](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/favicon-creation-tutorial-12.png)

다음은 맥OS 사파리(Safari). 자동으로 모노크롬으로 설정해준다. "threshold"를 적절히 설정하여 만들어준다.

!["App name" 설정](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/favicon-creation-tutorial-13.png)

최종적으로 옵션이긴한데, 앱 이름을 설정해주면 모든 설정은 끝난다. "Generate your Favicons and HTML code" 버튼을 눌러준다.

!["Favicon package" 클릭하여 패키지 다운로드 및 코드 복사](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/favicon-creation-tutorial-14.png)

성공적으로 생성되었다. "Favicon package"를 클릭하여 패키지를 다운받는다. 그 후 헤드(`<head>`)에 들어갈 코드를 복사해둔다.

# 5. 플라스크(Flask)에 적용

잘 만들어진 파비콘을 블로그에 적용해본다. 본 예제에서는 파이썬 웹 프레임워크인 플라스크(Flask)를 사용한다. 다른 웹 프레임워크를 사용한다면, 적절히 변경하여 적용하면 된다.

![파비콘 파일 추가](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/favicon-creation-tutorial-15.png)

먼저 `static` 폴더에 `icons`폴더를 만들어 이전에 받은 패키지의 모든 파일을 무지성으로 넣어준다. 그 후 `templates` 폴더의 `base.html`(본인의 html 문서)을 열어 헤드 부분에 이전에 복사한 코드를 넣어준다. 다만 필자와 같이 플라스크를 사용하는 경우 어느정도 코드 변환이 필요하다. 필자는 아래와 같이 코드를 추가했다.

```html
<head>
    ...
    <!-- Favicon -->
    <link rel="apple-touch-icon" sizes="180x180" href="{{ url_for('static', filename='icons/apple-touch-icon.png') }}">
    <link rel="icon" type="image/png" sizes="32x32" href="{{ url_for('static', filename='icons/favicon-32x32.png') }}">
    <link rel="icon" type="image/png" sizes="16x16" href="{{ url_for('static', filename='icons/favicon-16x16.png') }}">
    <link rel="manifest" href="{{ url_for('static', filename='icons/site.webmanifest') }}">
    <link rel="mask-icon" href="{{ url_for('static', filename='icons/safari-pinned-tab.svg') }}" color="#5bbad5">
    <meta name="apple-mobile-web-app-title" content="Yoonmin Lee">
    <meta name="application-name" content="Yoonmin Lee">
    <meta name="msapplication-TileColor" content="#5981d4">
    <meta name="theme-color" content="#ffffff">
    ...
</head>
```

!["/favicon.ico" 라우팅 설정](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/favicon-creation-tutorial-16.png)

최종적으로 `/favicon.ico`로 라우팅 시 파비콘을 확인할 수 있어야 한다. 플라스크의 경우 `app.py`에 아래와 같이 코드를 추가해주면 된다.

```py
@app.route('/favicon.ico')
def favicon():
    return app.send_static_file('icons/favicon.ico')

@app.route('/site.webmanifest')
def webmanifest():
    return app.send_static_file('icons/site.webmanifest')

@app.route('/safari-pinned-tab.svg')
def safari_pinned_tab():
    return app.send_static_file('icons/safari-pinned-tab.svg')

@app.route('/mstile-150x150.png')
def mstile_150x150():
    return app.send_static_file('icons/mstile-150x150.png')

@app.route('/favicon-32x32.png')
def favicon_32x32():
    return app.send_static_file('icons/favicon-32x32.png')

@app.route('/favicon-16x16.png')
def favicon_16x16():
    return app.send_static_file('icons/favicon-16x16.png')

@app.route('/browserconfig.xml')
def browserconfig_xml():
    return app.send_static_file('icons/browserconfig.xml')

@app.route('/apple-touch-icon.png')
def apple_touch_icon_png():
    return app.send_static_file('icons/apple-touch-icon.png')

@app.route('/android-chrome-512x512.png')
def android_chrome_512x512_png():
    return app.send_static_file('icons/android-chrome-512x512.png') 

@app.route('/android-chrome-192x192.png') 
def android_chrome_192x192_png(): 
     return app.send_static_file('icons/android-chrome-192x192.png') 
```

# 6. 결과

![파비콘 설정 확인](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/favicon-creation-tutorial-17.png)

예쁘게 잘 설정되었다. 끝.

# 7. 마무리 및 참고

이번 아티클을 통해 Adobe Color, favicon.io, RealFaviconGenerator를 활용하여 웹사이트에 적합한 파비콘을 만들고 적용하는 방법을 자세히 소개했다. 파비콘은 웹사이트의 첫인상을 좌우하는 중요한 요소이니만큼, 귀찮더라도 디테일한 설정을 통해 전문적인 웹 애플리케이션을 구축하길 바란다.

## 7.1 관련 아티클

- **자파(Zappa) 튜토리얼**
    1. [플라스크(Flask) 서버리스(Serverless) 배포](/zappa-flask-serverless-deployment)
    2. [사용자 정의 도메인(Custom Domain) 연결(Feat. AWS Route53 & 가비아(Gabia))](/zappa-custom-domain-route53-gabia)
    3. [스테이지(Stage) & 로그 레벨(Log level) 관리](/zappa-stage-log-level-management)
    4. [배포 해제 및 사용자 정의 도메인(Custom Domain) 제거](/zappa-undeploy-custom-domain-removal)
- [파비콘 만들기 및 적용 완벽 튜토리얼: Adobe Color, favicon.io, RealFaviconGenerator 사이트 활용](/favicon-creation-tutorial)
- [웹 배포 후 검색 노출(SEO) 세팅 완벽 가이드 (w/ 플라스크(Flask))](/web-deployment-seo-guide)
- [웹 배포 후 트래픽 분석 및 수익화를 위한 구글 애널리틱스(Google Analytics, GA4)와 애드센스(AdSense) 초기 세팅 및 연동 완벽 가이드](/google-analytics-adsense-setup-guide)

## 7.2 참고

- [Adobe Color](https://color.adobe.com/ko/create/image)
- [favicon.io](https://favicon.io/)
- [RealFaviconGenerator](https://realfavicongenerator.net/)
