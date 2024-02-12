---
title: '구글 서치 콘솔(Google Search Console)과 구글 애널리틱스(Google Analytics, GA4) 연결'
description: "구글 서치 콘솔(Google Search Console)과 구글 애널리틱스(Google Analytics, GA4)를 연결하여 웹사이트의 성능을 향상시키는 방법을 소개한다. 통합된 데이터 보기, 검색 성능과 사용자 행동의 상관관계 분석, 전환율 최적화, 보다 정확한 타겟팅, 문제 해결 및 보고서 개선 등의 혜택을 제공한다."
date: '2024-02-11'
tags: ['구글 애널리틱스(Google Analytics, GA4)', 구글 서치 콘솔(Google Search Console), 웹 개발(Web Development)]
---
# 1. 서론
 
웹사이트 관리와 최적화는 다양한 도구와 분석 기능을 활용하여 이루어진다. 특히, 구글 서치 콘솔(Google Search Console)과 구글 애널리틱스(Google Analytics, GA4)는 웹사이트의 성능을 모니터링하고 사용자 경험을 분석하는 데 있어 필수 도구이다.

# 2. 연동의 이유

서치 콘솔과 애널리틱스를 연동하는 이유는 다음과 같은 몇 가지 주요 혜택 때문이다:

## 2.1 통합된 데이터 보기

구글 서치 콘솔은 웹사이트의 검색 트래픽과 성능을 모니터링하는 데 중점을 둔 반면, 구글 애널리틱스는 웹사이트 방문자의 행동과 상호작용을 분석하는 데 초점을 맞춘다. 이 두 도구를 연동하면 웹사이트의 트래픽 소스, 사용자 행동, 전환율 등에 대한 보다 포괄적인 이해를 얻을 수 있다.

## 2.2 검색 성능과 사용자 행동의 상관관계 분석

연동을 통해 어떤 검색 쿼리가 웹사이트로 유입되는 트래픽을 생성하는지, 그리고 이 트래픽이 웹사이트에서 어떻게 행동하는지를 파악할 수 있다. 이 정보는 콘텐츠 전략을 최적화하고 사용자 경험을 개선하는 데 유용하다.

## 2.3 전환율 최적화

특정 검색 쿼리나 페이지가 전환에 기여하는 정도를 이해함으로써, 마케팅과 SEO(Search Engine Optimization) 전략을 더 효과적으로 조정할 수 있다. 이는 ROI(투자 대비 수익)를 극대화하는 데 도움이 된다.

## 2.4 보다 정확한 타겟팅

검색 쿼리 데이터와 사용자 행동 분석을 결합함으로써, 타겟 오디언스의 구체적인 관심사와 필요를 더 잘 이해하고 이에 맞는 콘텐츠와 마케팅 메시지를 개발할 수 있다.

## 2.5 문제 해결

서치 콘솔은 웹사이트의 기술적 문제(예: 크롤링 에러, 모바일 사용성 문제 등)를 식별하는 데 도움을 준다. 이 데이터를 애널리틱스와 연동하면 문제가 사용자 경험에 미치는 영향을 더 잘 이해하고 우선 순위를 정하여 해결할 수 있다.

## 2.6 보고서와 인사이트의 개선

두 도구를 연동하면, 사용자 맞춤 보고서를 생성하거나 고급 분석 기능을 사용하여 데이터를 더 깊이 있게 분석할 수 있다. 이를 통해 데이터 기반 의사 결정을 더 잘 지원할 수 있다.

# 3. 연동 방법

먼저 [구글 애널리틱스](https://analytics.google.com/)에 접속 후 로그인을 진행한다. 만약 계정이 없다면 [계정을 생성](https://support.google.com/analytics/answer/1009694?hl=ko)한다. 계정 생성은 쉬우므로 본 아티클에선 넘어간다.

![구글 서치 콘솔 링크 연결](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/connect-google-search-console-analytics-1.png)

[구글 애널리틱스](https://analytics.google.com/)의 톱니바퀴 모양인 "관리" 탭에서 "제품 링크" 탭의 "Search Console 링크" 탭에 접속한 후, "연결" 버튼을 통해 연동을 진행한다.

![구글 서치 콘솔 계정 선택](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/connect-google-search-console-analytics-2.png)

"계정 선택" 링크를 통해 본인이 관리하는 서치 콘솔 속성에 대한 연결을 진행한다.

![속성 연결](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/connect-google-search-console-analytics-3.png)

연결하려는 속성을 체크한 후 "확인" 버튼을 클릭한다.

![속성 성택 완료](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/connect-google-search-console-analytics-4.png)

"다음" 버튼을 클릭한다.

![웹 스트림 선택](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/connect-google-search-console-analytics-5.png)

웹 스트림을 선택한다.

![데이터 스트림 선택](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/connect-google-search-console-analytics-6.png)

데이터 스트림을 선택한다.

![웹 스트림 선택 완료](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/connect-google-search-console-analytics-7.png)

선택이 완료되었다면, "다음" 버튼을 클릭한다.

![검토 후 제출](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/connect-google-search-console-analytics-8.png)

검토 후 "보내기" 버튼을 통해 제출한다.

![연결 생성](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/connect-google-search-console-analytics-9.png)

성공적으로 연결이 생성된다.

# 3. 구글 서치 콘솔 라이브러리 게시

구글 애널리틱스 보고서에서 서치 콘솔 데이터를 확인하기 위해 라이브러리를 추가한다.

![구글 애널리틱스의 서치 콘솔 라이브러리 게시](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/connect-google-search-console-analytics-10.png)

[구글 애널리틱스](https://analytics.google.com/)의 "보고서" 탭의 "라이브러리" 탭에 접속하면 서치 콘솔 컬렉션이 전시된다. 만약 없다면 하루 뒤 확인해본다. 서치 콘솔의 "케밥 메뉴"(세로로 점 3개가 나열된 아이콘)을 눌러 "게시"를 클릭한다.

![보고서 메뉴의 서치 콘솔 메뉴](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/connect-google-search-console-analytics-11.png)

이제 보고서 메뉴에 서치 콘솔 메뉴가 생긴 것을 확인할 수 있다. 여기서 구글 자연 검색의 검색어와 유입 페이지 등을 확인할 수 있다.

# 4. 서치 콘솔 보고서 확인

서치 콘솔 보고서는 구글 애널리틱스와 구글 서치 콘솔이 통합된 기능을 제공한다. 이 보고서를 사용하면 웹사이트의 검색 트래픽, 성능, 사용자의 검색 쿼리 등을 분석할 수 있다.

![서치 콘솔 보고서의 쿼리 메뉴](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/connect-google-search-console-analytics-12.png)

구체적으로 다음과 같은 정보를 포함한다:

1. **쿼리**: 사용자가 구글 검색에서 웹사이트를 찾기 위해 입력한 검색어이다. 이 데이터를 통해 가장 인기 있는 검색 쿼리를 파악하고, 특정 키워드에 대한 웹사이트의 성능을 분석할 수 있다.
2. **페이지**: 사용자가 검색 결과에서 클릭하여 방문한 웹사이트의 페이지이다. 가장 많이 방문한 페이지를 알 수 있으며, 이를 통해 콘텐츠 전략을 조정할 수 있다.
3. **국가**: 방문자의 위치 정보이다. 다양한 지역에서의 웹사이트 성능을 분석할 수 있다.
4. **기기**: 방문자가 사용한 기기(모바일, 태블릿, 데스크탑) 정보이다. 기기별 성능을 분석하여 최적화 전략을 세울 수 있다.


## 4.1 Google 자연 검색 트래픽 메뉴

Google 자연 검색 트래픽은 구글 애널리틱스에서 웹사이트로 유입되는 트래픽 중에서 구글 검색을 통해 자연스럽게 발생하는 방문자를 분석하는 부분이다.

![서치 콘솔 보고서의 Google 자연 검색 트래픽 메뉴](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/connect-google-search-console-analytics-13.png)

이 메뉴를 통해 다음과 같은 분석을 할 수 있다:

1. **방문자 수**: 구글 자연 검색을 통해 웹사이트를 방문한 사용자 수이다.
2. **세션 수**: 검색을 통해 생성된 총 세션 수이다.
3. **반송률**: 단일 페이지 방문 후 사이트를 떠난 방문자의 비율이다. 낮은 반송률은 콘텐츠가 관련성이 높고 사용자의 관심을 끌었다는 신호가 될 수 있다.
4. **페이지/세션**: 세션당 평균 페이지 조회 수이다. 이 지표는 사용자의 사이트 내 활동 정도를 나타낸다.
5. **평균 세션 지속 시간**: 사용자가 사이트에 머문 평균 시간이다.

이 두 기능을 통해 웹사이트의 검색 엔진 최적화(SEO) 상태를 평가하고, 사용자의 검색 패턴과 선호도에 맞춰 콘텐츠와 마케팅 전략을 조정할 수 있다.

# 5. 마무리

## 5.1 관련 아티클

- **자파(Zappa) 튜토리얼**
    1. [플라스크(Flask) 서버리스(Serverless) 배포](/zappa-flask-serverless-deployment)
    2. [사용자 정의 도메인(Custom Domain) 연결(Feat. AWS Route53 & 가비아(Gabia))](/zappa-custom-domain-route53-gabia)
    3. [스테이지(Stage) & 로그 레벨(Log level) 관리](/zappa-stage-log-level-management)
    4. [배포 해제 및 사용자 정의 도메인(Custom Domain) 제거](/zappa-undeploy-custom-domain-removal)
- [파비콘 만들기 및 적용 완벽 튜토리얼: Adobe Color, favicon.io, RealFaviconGenerator 사이트 활용](/favicon-creation-tutorial)
- [웹 배포 후 검색 노출(SEO) 세팅 완벽 가이드 (w/ 플라스크(Flask))](/web-deployment-seo-guide)
- [웹 배포 후 트래픽 분석 및 수익화를 위한 구글 애널리틱스(Google Analytics, GA4)와 애드센스(AdSense) 초기 세팅 및 연동 완벽 가이드](/google-analytics-adsense-setup-guide)
- [구글 서치 콘솔(Google Search Console)과 구글 애널리틱스(Google Analytics, GA4) 연결](/connect-google-search-console-analytics)