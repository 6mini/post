---
title: '웹 배포 후 트래픽 분석 및 수익화를 위한 구글 애널리틱스(Google Analytics, GA4)와 애드센스(AdSense) 초기 세팅 및 연동 완벽 가이드'
description: "웹사이트 개발 후 트래픽 분석 및 수익화를 위한 구글 애널리틱스(GA4)와 구글 애드센스 설정 및 연동 방법을 소개한다. 웹사이트의 트래픽 추적, 사용자 행동 분석, 수익화 전략에 대한 실용적인 가이드를 제공한다."
date: '2024-01-30'
tags: ['구글 애널리틱스(Google Analytics, GA4)', 구글 애드센스(Google AdSense), 웹 개발(Web Development)]
---
# 1. 서론
 
웹 애플리케이션을 성공적으로 배포하고 SEO 설정까지 완료했다면, 이제 웹사이트의 트래픽을 분석하고 수익을 창출하는 단계로 넘어갈 시간이다. 웹을 구축했다면, 내가 만든 웹사이트에 몇 명이 어디에 얼마나 머물고 갔는지 분명 궁금할 것이다. 하지만 트래픽을 알아보기 위해 직접 세팅하는 것은 분명 어려운 일이다. 하지만 우리에겐 GA4라고 불리우는 무료 서비스인 구글 애널리틱스가 있고, 태그 하나만 간단하게 설정해주면 구글이 알아서 트래픽 분석을 도와준다. 실제로 각 GA4의 각 기능이나 분석법 등은 매우 다양하기 때문에 잘 다루려면 더욱 심도있는 컨텐츠로의 학습이 필요하겠지만, 여기서는 초기 세팅을 어떻게 하는지 알아볼 것이다. 또한 으레 사이드 프로젝트를 웹을 구축하는 사람이라면 누구나 꿈꾸는 수익화를 위한 구글 애드센스(Google AdSense)를 세팅하는 방법에 대해 알아볼 것이다. 애드센스 또한 깊이있게 광고를 설정하는 방법을 이 아티클에서는 다루지 않고, 초기 세팅과 광고 게재 검토 요청까지의 과정만을 다룰 것이다. 더 나아가 트래픽에 의한 수익을 함께보기 측정하기 위해 두 가지를 연동하는 방법까지 알아볼 것이다.

![사이드 프로젝트로 개발한 "텍스트 이모지 생성기" 웹 애플리케이션 메인 화면](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-19.png)

본 아티클에서는 최근 필요에 의해 필자가 개발한 "[텍스트 이모지 생성기](https://text-to-emoji.com)"에 적용하는 것을 예제로 진행할 것이다.


# 2. 구글 애널리틱스(Google Analytics, GA4)

## 2.1 구글 애널리틱스란?

구글 애널리틱스(Google Analytics, GA4)는 웹사이트나 앱의 트래픽 및 사용자 행동을 분석하기 위한 서비스다. 다양한 데이터와 통계를 제공하여 웹사이트 소유자나 마케터가 사이트나 앱이 어떻게 사용되고 있는지 파악할 수 있도록 도와준다. 구체적으로 GA4가 제공하는 주요 기능과 그 사용 이유는 다음과 같다:

1. **트래픽 분석**: 웹사이트에 방문하는 사용자 수, 사용자들이 어디에서 왔는지(예: 검색 엔진, 소셜 미디어, 직접 방문 등), 사이트에 얼마나 오래 머무르는지 등의 데이터를 제공한다.
2. **행동 추적**: 사용자가 사이트에서 어떤 행동을 하는지를 분석한다. 예를 들어, 어떤 페이지를 가장 많이 보는지, 어떤 경로로 사이트를 탐색하는지 등의 정보를 얻을 수 있다.
3. **전환율 측정**: 목표 설정 기능을 통해 사이트 방문자가 특정 행동(예: 상품 구매, 뉴스레터 구독 등)을 할 때 이를 추적할 수 있다. 이를 통해 마케팅 캠페인이나 사이트 변경 사항이 전환율에 어떤 영향을 미치는지 분석할 수 있다.
4. **고객 통찰력**: 사용자의 연령, 성별, 관심사, 지리적 위치 등 다양한 인구 통계학적 정보를 제공한다. 이를 통해 타깃 고객을 더 잘 이해하고 맞춤형 콘텐츠나 광고를 제공할 수 있다.
5. **모바일 앱 분석**: 모바일 앱 사용에 대한 데이터도 제공한다. 이를 통해 앱의 성능을 모니터링하고 사용자 참여를 높일 수 있는 방법을 찾을 수 있다.

GA를 사용하는 주된 이유는 이러한 데이터를 통해 사용자 경험을 개선하고, 마케팅 전략을 보다 효과적으로 수립하며, 사이트나 앱의 성능을 최적화할 수 있기 때문이다.

## 2.2 구글 애널리틱스 설정

이제 웹사이트에 어떻게 적용할 수 있을지 알아볼 것이다. 먼저 [구글 애널리틱스](https://analytics.google.com/)에 접속 후 로그인을 진행한다. 만약 계정이 없다면 [계정을 생성](https://support.google.com/analytics/answer/1009694?hl=ko)한다. 계정 생성은 쉬우므로 본 아티클에선 넘어간다.

![구글 애널리틱스 속성 만들기](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-1.png)

[구글 애널리틱스](https://analytics.google.com/)의 톱니바퀴 모양인 "관리" 탭에서 "만들기" 버튼을 클릭한 후, "속성" 버튼을 통해 속성 생성을 진행한다.

![시설 세부정보 입력](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-2.png)

"속성 이름"과 "보고 시간대", "통화"를 설정한 후 "다음" 버튼을 클릭한다. 필자는 본 튜토리얼에서 "Text to Emoji"로 지정했고, 당당한 대한민국 국민이기에 보고 시간대와 통화를 대한민국으로 지정했다.

![비즈니스 세부정보 입력](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-3.png)

"업종 카테고리"와 "비즈니스 규모"를 적절히 선택한 후 "다음" 버튼을 클릭한다.

![비즈니스 목표 선택](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-4.png)

원하는 "비즈니스 목표"를 선택한 후, "만들기" 버튼을 클릭한다.

![데이터 수집 플랫폼으로 웹 선택](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-5.png)

웹 애플리케이션에 대한 분석을 진행하기 때문에 "웹" 버튼을 클릭한다.

![웹 스트림 설정](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-6.png)

그 후 전시되는 "데이터 스트림 설정"에서 "웹사이트 URL"과 "스트림 이름"을 설정한 후 "스트림 만들기"를 통해 스트림을 생성한다.

![직접 설치 및 코드 스니펫 복사](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-7.png)

이제 이 스트림을 설치해야하며, 우리는 직접 만든 웹 애플리케이션을 등록할 것이기 때문에, "직접 설치"를 통해 진행한다. 전시되는 구글 태그를 헤드(`<head>`)에 붙여넣은 후 검증을 진행해야한다. 먼저 구글 태그를 복사한다.

웹 앱의 HTML에서 헤드(`<head>`) 요소 바로 다음에 붙여 넣는다:

```html
<!DOCTYPE html>
<html lang="ko">
    <head>
        ...
        <!-- Google tag (gtag.js) -->
        <script async src="https://www.googletagmanager.com/gtag/js?id=G-QW1ER2TYUI"></script>
        <script>
        window.dataLayer = window.dataLayer || [];
        function gtag(){dataLayer.push(arguments);}
        gtag('js', new Date());

        gtag('config', 'G-QW1ER2TYUI');
        </script>
        ...
    </head>
```

![웹사이트 테스트](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-8.png)

"테스트" 버튼을 통해 태그 적용을 테스트한다.

![테스트 확인](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-13.png)

초록 체크 표시가 전시되면, 올바르게 적용된 것이다.

![데이터 수집 시작](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-14.png)

웹사이트의 추가를 확인한 후, "다음" 버튼을 클릭한다.

![데이터 수집 대기 중](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-9.png)

세팅은 쉽게 끝났다. 이제 데이터 수집을 기다리면 된다.

# 3. 구글 애드센스(Google AdSense)

## 3.1 구글 애드센스란?

구글 애드센스(Google AdSense)는 웹사이트, 블로그 등의 플랫폼에서 광고를 게재하여 수익을 창출할 수 있도록 하는 서비스이다. 디지털 광고를 통한 수익 창출에 중요한 역할을 하며, 이 서비스의 주요 특징과 사용 이유는 다음과 같습니다:

1. **광고 게재**: 웹사이트나 블로그에 광고를 게재할 수 있다. 광고는 텍스트, 이미지, 비디오 또는 인터랙티브 미디어 광고일 수 있으며, 구글 알고리즘에 의해 자동으로 타겟팅되고 최적화된다.
2. **콘텐츠 및 관심 기반 타겟팅**: 웹사이트의 콘텐츠와 방문자의 관심사를 분석하여 관련성 높은 광고를 게재한다. 이는 광고의 클릭률을 높이고 사용자 경험을 개선하는 데 도움이 된다.
3. **수익 창출**: 웹사이트 방문자가 광고를 클릭하거나 볼 때마다 수익이 발생한다. 이를 통해 콘텐츠 제작자나 웹사이트 운영자는 자신의 플랫폼을 통해 지속적인 수익을 얻을 수 있다.
4. **사용 편의성**: 애드센스는 사용하기 쉽도록 설계되었습니다. 간단한 등록 절차와 몇 가지 설정을 통해 빠르게 광고를 시작할 수 있다.
5. **유연한 광고 관리**: 웹사이트에 어떤 유형의 광고를 게재할지, 광고의 위치는 어디로 할지 등을 설정할 수 있다. 또한, 수익과 성능에 대한 상세한 보고서를 제공하여 광고 전략을 조정하는 데 도움을 준다.

## 3.2 구글 에드센스 설정

![구글 애드센스에서 새 사이트 생성](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-10.png)

[구글 애드센스](https://www.google.com/adsense)의 "사이트" 탭에서 "새 사이트" 버튼을 클릭한다.

![웹 사이트 도메인 입력](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-11.png)

"웹 사이트"에 도메인을 입력한다.


![애드센스 코드 스니펫으로 사이트 소유권 확인](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-12.png)

구글 애널리틱스와 유사하게 "애드센스 코드 스니펫"으로 소유권 확인을 진행한다. 애드센스 코드를 복사한 후 헤드(`<head>`)에 붙여넣는다:

```html
<!DOCTYPE html>
<html lang="ko">
    <head>
        ...
        <!-- Google Adsense -->
        <script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-1234567890123456"
            crossorigin="anonymous"></script>
        ...
    </head>
```

![코드 삽입 체크 후 확인 버튼 클릭](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-15.png)

코드 삽입이 완료되었다면, "코드를 삽입했습니다." 체크 박스를 체크한 후 "확인" 버튼을 클릭한다.

![사이트 확인 모달창 전시](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-16.png)

잘 세팅했다면, "사이트가 확인되었습니다"라는 모달창이 전시된다.

![광고 게재 검토 요청](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-17.png)

"검토 요청" 버튼을 통해 검토를 요청한다.

![검토 요청 절차 완료](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-18.png)

성공적으로 검토가 요청되며, 검토가 끝나면 광고 게재 가능 여부를 메일로 알려준다.


# 4. 구글 애널리틱스(GA4)와 애드센스 연결

## 4.1 연동의 이점

구글 애널리틱스(Google Analytics, GA4)와 구글 애드센스(Google AdSense)를 연동하면 웹사이트의 트래픽 데이터와 수익 데이터를 함께 분석할 수 있다. 광고 수익 최적화, 사용자 행동 이해, 효과적인 콘텐츠 전략 수립 등에 필수이다. 연동의 이점은 다음과 같다:

1. **통합 데이터 분석**: 애널리틱스와 애드센스 데이터를 함께 확인함으로써, 광고 수익과 사용자 행동의 상관관계를 보다 명확히 파악할 수 있다.
2. **사용자 세분화**: 애널리틱스에서 제공하는 상세한 사용자 데이터를 활용하여 애드센스 광고의 타겟팅을 더욱 정밀하게 조정할 수 있다.
3. **성능 최적화**: 어떤 콘텐츠가 가장 많은 수익을 창출하는지, 어떤 광고가 사용자 참여도를 높이는지 등을 분석하여 전략을 조정할 수 있다.

## 4.2 연동 절차

![구글 애널리틱스에서 Google 애드센스 링크 연결](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-20.png)

[구글 애널리틱스](https://analytics.google.com/)의 톱니바퀴 모양인 "관리" 탭에서 "제품 링크"아래 "Google 애드센스 링크"를 클릭한 후, "연결" 버튼을 클릭한다.

![구글 애드센스 게시자 선택](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-21.png)

"Google 애드센스 게시자 선택" 링크를 클릭한다.

![게시자 ID 선택](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-22.png)

자동으로 전시되는 게시자 ID를 선택한 후, "확인" 버튼을 클릭한다.

![게시자 선택 확인](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-23.png)

게시자 선택이 완료되었다면 "다음" 버튼을 클릭한다.

![수익 데이터 보고 사용 설정](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-24.png)

"켜기"의 활성화를 확인한 후, "다음" 버튼을 클릭한다.

![게시자 및 설정 검토 후 제출](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/google-analytics-adsense-setup-guide-25.png)

최종적으로 "보내기" 버튼을 통해 제출한다. 이렇게 연동도 끝이 났다.

# 5. 마무리

웹사이트의 트래픽 분석과 수익화를 위한 구글 애널리틱스와 구글 애드센스의 초기 세팅 및 연동 과정에 대해 알아보았다. 이 과정을 통해 웹사이트의 성능을 모니터링하고, 사용자 행동을 이해하며, 광고 수익을 최적화하는 데 필요한 통찰력을 얻을 수 있을 것이다.

## 5.1 관련 아티클

- **자파(Zappa) 튜토리얼**
    1. [플라스크(Flask) 서버리스(Serverless) 배포](/zappa-flask-serverless-deployment)
    2. [사용자 정의 도메인(Custom Domain) 연결(Feat. AWS Route53 & 가비아(Gabia))](/zappa-custom-domain-route53-gabia)
    3. [스테이지(Stage) & 로그 레벨(Log level) 관리](/zappa-stage-log-level-management)
    4. [배포 해제 및 사용자 정의 도메인(Custom Domain) 제거](/zappa-undeploy-custom-domain-removal)
- [파비콘 만들기 및 적용 완벽 튜토리얼: Adobe Color, favicon.io, RealFaviconGenerator 사이트 활용](/favicon-creation-tutorial)
- [웹 배포 후 검색 노출(SEO) 세팅 완벽 가이드 (w/ 플라스크(Flask))](/web-deployment-seo-guide)
- [웹 배포 후 트래픽 분석 및 수익화를 위한 구글 애널리틱스(Google Analytics, GA4)와 애드센스(AdSense) 초기 세팅 및 연동 완벽 가이드](/google-analytics-adsense-setup-guide)