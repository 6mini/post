---
title: '자파(Zappa) 튜토리얼(2): 사용자 정의 도메인(Custom Domain) 연결(Feat. AWS Route53 & 가비아(Gabia))'
description: '자파(Zappa)를 사용하여 배포된 서버리스 애플리케이션에 AWS의 Route 53 또는 가비아(Gabia)를 통해 사용자 정의 도메인(Custom Domain)을 설정하는 과정을 안내한다.'
date: '2023-11-24'
tags: [자파(Zappa), AWS, 서버리스(Serverless), 클라우드(Cloud), 웹 개발(Web Development), 사용자 정의 도메인(Custom Domain), Route53, 가비아(Gabia)]
---
# 1. 서론

[자파(Zappa)를 이용한 플라스크(Flask) 서버리스 애플리케이션의 AWS 람다(Lambda) 배포를 진행했던 지난 포스팅](/zappa-flask-serverless-deployment)에 이어 이번 포스팅에서는 사용자 정의 도메인 연결 과정을 다룰 것이다.


# 2. 자파(Zappa)와 사용자 정의 도메인(Custom Domain)

현재 자파 배포로 자동 생성된 API 게이트웨이(Gateway) 엔드포인트를 통해 애플리케이션에 접속할 수 있다. 그러나 자파가 제공하는 URL은 보기 흉하다. 이를테면:

```txt
https://q1w2e3r4t5y6.execute-api.ap-northeast-2.amazonaws.com/dev/
        ^^^^^^^^^^^^^^^^^^^^^^^^                              ^^^
          자동 생성된 API Gateway                             Zappa 환경
```

이상적인 대부분의 URL은 다음과 같다:

```txt
https://yoonminlee.com/
```

이처럼 도메인을 아름답게 만드는 과정은 자파를 통해 쉽게 진행할 수 있다. 실제 서비스를 운영하기 위해서는 사용자가 기억하기 쉬운 도메인이 필수적이다. AWS에서 제공하는 기본 URL보다는 브랜드와 연관된 도메인이 좋을 것 같다.

이 포스팅에서는 AWS Route 53 뿐 아니라 한국의 대표적 도메인 및 호스팅 서비스 제공업체인 가비아(Gabia)를 통해 사용자 정의 도메인을 설정하는 방법을 모두 다룰 것이다.

## 2.1. HTTPS

HTTPS는 웹사이트의 보안과 신뢰성을 높이는 중요한 요소이다. 자파는 HTTPS를 기본적으로 지원하며, 사용자 정의 도메인 설정 과정에서 자동으로 HTTPS를 적용한다. ACM(AWS Certificate Manager)를 통해 무료로 SSL/TLS 인증서를 발급받을 수 있으며, 이를 통해 안전한 연결을 보장할 수 있다.

## 2.2 필요한 서비스

자파에 사용자 정의 도메인을 설정하기 위해 필요한 서비스는 다음과 같다:

- **도메인 등록 서비스**: AWS Route 53 또는 가비아 등을 통해 도메인을 구매하고 등록한다.
- **DNS 서비스**: Route 53 또는 가비아의 DNS 설정을 통해 도메인을 관리한다.
- **인증 서비스**: ACM(AWS Certificate Manager)을 사용하여 사이트 트래픽을 암호화한다.

위 서비스는 모두 자파와 결합하여 사용한다. 많은 곳에서 위 서비스를 하지만 아마존과 같은 회사는 세 가지 서비스를 모두 제공하기 때문에 이용 시 편리하다.

궁극적으로 AWS API 게이트웨이는 디지털 인증서를 활용하여 HTTPS를 제공할 뿐만 아니라 자파 환경 경로를 숨기는 CloudFront 배포와 연결된다. 마지막으로 DNS 레코드는 이 CloudFront Distro를 가리키며 완성된다.

> **가비아를 다루는 이유**: `.day`와 같은 도메인을 구매하고 싶었으나, AWS에서는 `.day` 도메인을 지원하지 않는다. 따라서 가비아에서 도메인을 구매했고, 다른 사용자들도 가비아를 사용할 일이 많을 것으로 생각하여 가비아를 다루는 방법도 포함했다.

# 3. 사용자 정의 도메인 등록

사용자 정의 도메인을 설정하기 위해선 먼저 도메인을 등록해야 한다. [Route 53](https://us-east-1.console.aws.amazon.com/route53/domains/home?region=ap-northeast-2#/)이나 [가비아](https://domain.gabia.com/regist/regist_domain)를 통해 원하는 도메인을 찾고, 구입 및 등록 과정을 진행한다. 

## 3.1 도메인 등록 서비스

### 3.1.1 Route 53

![AWS Route 53 도메인 등록](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-1.png)

Route 53은 AWS에서 제공하는 클라우드 기반 DNS 서비스이다. 도메인 등록부터 호스팅 영역 설정까지 AWS 플랫폼 내에서 일괄 관리할 수 있다. 자세한 등록 과정은 쉬우므로 생략한다.

### 3.1.2 가비아

![가비아 도메인 등록](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-2.png)

가비아는 한국의 주요 도메인 및 호스팅 서비스 제공업체이다. 사용자 친화적인 인터페이스와 국내 사용자에게 익숙한 서비스를 제공한다. 자세한 등록 과정은 쉬우므로 생략한다.

## 3.2 호스팅 영역 및 DNS 설정

등록한 도메인을 관리하기 위해서는 호스팅 영역과 DNS 설정이 필요하다.

![AWS Route 53 호스팅 영역](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-3.png)

호스팅 영역(Hosting Zone)은 DNS 서버에서 도메인 이름에 대한 DNS 레코드를 관리하는 데 사용되며, DNS 레코드(DNS Record)는 도메인 이름 시스템(Domain Name System, DNS)에서 사용되는 규칙 또는 지침의 한 형태이다. 간단히 말해, DNS 레코드는 인터넷에서 특정 도메인 이름(예: www.example.com)이 어떻게 처리되어야 하는지에 대한 정보를 담고 있다.

### 3.2.1 Route 53

AWS Route 53에서 도메인을 등록했다면 축하한다. 가격도 비교적 저렴할 뿐 아니라 자동으로 호스팅 영역을 생성해 준다. 확인해 보면 이미 생성되어 있으므로 [다음 과정](#4-acm)으로 넘어가면 된다. 물론 네임 서버(Name Server, NS) 레코드도 자동으로 생성되어 있을 것이다.

> 만약 호스팅 영역을 삭제했다면 다시 생성해야 하는데, 그 경우 네임 서버 레코드를 얼라인 시켜줘야한다. 이 과정은 추후 포스팅에서 다룰 것이다.

### 3.2.2 가비아

#### 3.2.2.1 호스팅 영역 생성

가비아에서 구매했다면 몇 가지 복잡한 절차가 필요하다. 먼저 호스팅 영역을 생성해야 한다.

![AWS Route 53 호스팅 영역 생성](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-4.png)

Route53의 `호스팅 영역`에서 `호스팅 영역 생성` 버튼을 클릭한다.

![AWS Route 53 호스팅 영역 구성](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-5.png)

`도메인 이름`을 입력하고 `호스팅 영역을 생성`한다.

> 참고로 가비아 튜토리얼에서는 `6ixtest.com`을 사용하고, Route 53 튜토리얼에서는 `yoonminlee.com`을 사용한다. 헷갈리지 말길 바란다.

#### 3.2.2.2 DNS 설정

##### 3.2.2.2.1 NS 설정

호스팅 영역을 생성하면 아마존 네임 서버(NS)를 확인할 수 있다. 이를 가비아에 등록하는 절차가 필요하다.

![AWS Route 53 호스팅 영역 NS 값](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-6.png)

해당 호스팅 영역의 네임 서버 레코드의 값 4가지를 복사한다.

![가비아의 My가비아](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-7.png)

가비아 로그인 후 `My 가비아`에서 `도메인`을 클릭한다.

![가비아 도메인 관리](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-8.png)

해당 도메인의 `관리` 버튼을 클릭한다.

![가비아 네임 서버 설정](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-9.png)

가비아의 도메인 관리 화면에서 네임 서버 1~4차 네임서버를 설정해 줄 것이다. `설정` 버튼을 클릭한다.

![가비아 네임 서버 적용](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-10.png)

아까 복사한 호스팅 영역의 네임 서버 4가지의 값을 1~4차에 걸쳐서 입력한다. (이때 제일 마지막의 `.`은 제거하여서 입력하면 된다) `적용` 버튼을 누른다.

##### 3.2.2.2.2 A 레코드 설정

배포한 자파의 API 게이트웨이 엔드포인트를 가리키는 레코드의 생성이 필요하다.

![AWS Route 53 호스팅 영역 레코드 생성](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-11.png)

생성된 호스팅 영역에서 `레코드 생성` 버튼을 누른다.

![AWS Route 53 호스팅 영역 레코드 생성 단순 라우팅](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-12.png)

`단순 라우팅`을 선택한 후 다음 단계로 넘어간다.

![AWS Route 53 호스팅 영역 레코드 생성 단순 레코드 정의(1)](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-13.png)

`단순 레코드 정의` 버튼을 클릭한다.

![AWS Route 53 호스팅 영역 레코드 생성 단순 레코드 정의(2)](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-14.png)

원하는 서브 도메인을 입력(루트 도메인에 대한 레코드를 생성하려면 비워 둔다)하고 레코드 유형으로 `A`를 선택한다. 값/트래픽 라우팅 대상에서는 `API Gateway API`에 대한 별칭, 리전은 `ap-northeast-2`를 선택한다. 배포한 자파에 대한 엔드 포인트를 선택(필자는 이미 등록했기에 보이지 않는 상태)한 후 `단순 레코드 정의` 버튼을 누른다. 그 후 레코드 생성 버튼을 누르면 된다.

# 4. ACM 인증서 요청 및 설정

AWS Certificate Manager(ACM)를 사용하여 SSL/TLS 인증서를 요청하고, 이를 도메인에 적용한다. ACM을 사용하면 인증서의 관리, 배포, 갱신이 용이하며, HTTPS로의 전환을 간소화할 수 있다.

## 4.1 인증서 요청

![ACM 인증서 요청](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-15.png)

먼저 [ACM 콘솔](https://us-east-1.console.aws.amazon.com/acm/home?region=us-east-1#/certificates/request)에서 인증서를 요청한다. 이때 주의할 점은 리전을 `us-east-1`로 설정해야 한다. 이유는 자파가 `us-east-1` 리전의 인증서만 지원하기 때문이다. 배포 위치는 어디든 상관없다.

![ACM 퍼블릭 인증서 요청](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-16.png)

퍼블릭 인증서를 요청한다.

![ACM 인증서 도메인 이름 설정](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-17.png)

도메인 이름을 입력한다. 필자의 경우, 블로그를 지속해서 업그레이드할 계획이기 때문에 개발용 `dev` 스테이지를 위한 하위 도메인까지 함께 신청했다. `*.domain.com`과 같은 형태로 요청하면, 하위 도메인까지 모두 인증서를 발급받을 수 있다.

> 검증이 끝나고 나면 도메인 목록을 수정할 수 없기 때문에 신중하게 입력해야 한다. 변경 사항이 있으면 새 인증서를 발급받아야 한다.

![ACM 인증서 검증 방법 및 키 알고리즘](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-18.png)

검증 방법으로 `DNS 검증`을 선택하고, `키 알고리즘`도 기본인 `RSA 2048`을 선택한다.

> AWS에서는 다음과 같은 이유로 DNS 검증 방식을 권장하고 있다: Route 53으로 퍼블릭 DNS 레코드를 관리하는 경우, ACM이 직접 레코드를 업데이트할 수 있다.ACM은 인증서가 사용 중이고, DNS 레코드가 Route53 같은 도메인 데이터베이스에 잘 유지되어 있으면, 인증서를 자동으로 갱신해 준다. 이메일 검증 인증서를 갱신하려면, 도메인 소유자의 작업이 필요하다.

## 4.2 인증서 DNS 검증

### 4.2.1 Route 53

ACM에서 제공하는 `Route 53에서 레코드 생성` 버튼을 클릭하면, ACM이 자동으로 필요한 DNS 레코드를 Route 53 호스팅 영역에 추가한다.

![ACM 인증서 Route 53에서 레코드 생성](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-19.png)

Route 53에 레코드가 추가되면, ACM은 자동으로 해당 레코드를 확인하여 도메인 소유권을 검증한다. 이 과정은 몇 분에서 몇 시간이 걸릴 수 있으며, 성공적으로 완료되면 인증서 상태가 `발급됨(Issued)`으로 변경된다.

> 필자는 여기서 검증이 지속해서 검토 대기 중에 머문 상태가 두 번 있었다. 한 번은 Route 53에서 도메인을 구매한 후 소유권 확인을 진행하지 않아서였고, 또 다른 한 번은 호스팅 영역을 삭제한 후 다시 생성했기에 네임서버가 도메인과 얼라인 되지 않은 문제였다. 이 경우 또한 추후 포스팅에서 심도있게 다룰 것이다.

### 4.2.2. 가비아

가비아에서 DNS를 인증하는 방법이다.

![ACM 인증서 CNAME 이름 및 값 복사](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-20.png)

인증서 요청 후에 보이는 `CNAME 이름`과 `CNAME 값`을 복사한다.

![가비아의 My가비아](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-7.png)

가비아 로그인 후 `My 가비아`에서 `도메인`을 클릭한다.

![가비아 도메인 관리](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-8.png)

해당 도메인의 `관리` 버튼을 클릭한다.

![가비아 DNS 정보 및 관리](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-21.png)

`DNS 정보` 메뉴에서 `DNS 관리` 버튼을 클릭한다.

![가비아 DNS 설정](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-22.png)

해당 도메인을 선택하여 `DNS 설정` 버튼을 클릭한다.

![가비아 DNS 레코드 추가](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-23.png)

`레코드 추가` 버튼을 눌러 `CNAME` 타입의 `호스트`와 `값/위치`를 넣는다. 이때, `호스트`에 `CNAME 이름`을 넣는데, `domain.com.`을 제외하고 입력한다. `값/위치`에 `CNAME 값`을 입력한다. 이어서 `확인`과 `저장` 버튼을 누르면 얼마 후 인증서 상태가 `발급됨(Issued)`으로 변경된다.

성공적으로 인증서가 발급되었으면, 배포한 자파와 도메인을 연결해 보자.

# 5. Zappa 설정 및 도메인 연결

## 5.1 Zappa 설정 파일 변경
발급된 인증서를 자파 설정 파일에 추가하고, 자파를 통해 도메인을 AWS 람다와 연결한다. 

![ACM 인증서 ARM 복사](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-24.png)

먼저 발급된 인증서의 ARN을 복사한다.

앱에서 생성된 `zappa_settings.json` 설정 파일에서 배포한 스테이지에 아래의 내용을 추가한다.

```json
{
    "dev": {
        ...
        "certificate_arn": "arn:aws:acm:us-east-1:arn",
        "domain": "dev.yoonminlee.com",
        ...
    }
}
```

## 5.2 Zappa 도메인 인증

터미널에서 아래의 명령어를 실행하여 자파를 사용하여 도메인을 인증한다.

```sh
(zappa_venv) zappa-tutorial$ zappa certify dev
Calling certify for environment dev..
Are you sure you want to certify? [y/n] y
Certifying domain dav.yoonmin.lee..
Created a new domain name with supplied certificate. Please note that it can take up to 40 minutes for this domain to be created and propagated through AWS, but it requires no further work on your part.
Certificate updated!
```

# 6. 마무리 및 참고

이제 사용자 정의 도메인으로 접속하여 정상적으로 동작하는지 확인한다.

![Zappa 사용자 정의 도메인 적용 완료](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-custom-domain-route53-gabia-25.png)

성공적으로 도메인이 연결된 것을 확인할 수 있다.

다음 포스팅에서는 자파의 스테이징을 통해 개발, 테스트, 운영 환경을 구분하는 방법을 다룰 것이다. 배포 서버를 구축하며 궁금했던 부분이 많았기에, 필자는 이를 어떤 방식으로 활용하고 있는지 다룰 예정이다.

## 6.1 관련 아티클

- **자파(Zappa) 튜토리얼**
    1. [플라스크(Flask) 서버리스(Serverless) 배포](/zappa-flask-serverless-deployment)
    2. [사용자 정의 도메인(Custom Domain) 연결(Feat. AWS Route53 & 가비아(Gabia))](/zappa-custom-domain-route53-gabia)
    3. [스테이지(Stage) & 로그 레벨(Log level) 관리](/zappa-stage-log-level-management)
    4. [배포 해제 및 사용자 정의 도메인(Custom Domain) 제거](/zappa-undeploy-custom-domain-removal)
- [파비콘 만들기 및 적용 완벽 튜토리얼: Adobe Color, favicon.io, RealFaviconGenerator 사이트 활용](/favicon-creation-tutorial)
- [웹 배포 후 검색 노출(SEO) 세팅 완벽 가이드 (w/ 플라스크(Flask))](/web-deployment-seo-guide)
- [웹 배포 후 트래픽 분석 및 수익화를 위한 구글 애널리틱스(Google Analytics, GA4)와 애드센스(AdSense) 초기 세팅 및 연동 완벽 가이드](/google-analytics-adsense-setup-guide)

## 6.2 참고

- [Using a Custom Domain](https://romandc.com/zappa-django-guide/walk_domain/)
- [[AWS] EC2와 도메인 연결하기 (feat. 가비아)](https://developer-ping9.tistory.com/320)