---
title: '자파(Zappa) 튜토리얼(3): 스테이지(Stage) & 로그 레벨(Log level) 관리'
description: "자파(Zappa)와 플라스크(Flask)로 서버리스 애플리케이션을 개발하면서 스테이지 및 로그 레벨 관리의 중요성을 알아본다. 개발, 테스트, 스테이징, 프로덕션 환경을 효율적으로 관리하는 방법과 로그 레벨을 설정하여 비용을 최적화하는 방법에 대한 가이드를 제공한다."
date: '2023-12-04'
tags: [자파(Zappa), AWS, 서버리스(Serverless), 클라우드(Cloud), 웹 개발(Web Development), 스테이지(Stage), 로그 레벨(Log Level)]

---
# 1. 서론

![Flask Zappa 일러트스레이션](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-stage-log-level-management-1.png)

지난 아티클에서 [자파(Zappa)를 활용하여 플라스크(Flask) 기반의 서버리스 애플리케이션을 AWS 람다(Lambda)에 배포](/zappa-flask-serverless-deployment)하는 과정을 살펴보았고, [사용자 정의 도메인 연결](/zappa-custom-domain-route53-gabia)까지 마쳤다. 이번 포스팅에서는 프로젝트를 진행하며 마주칠 수 있는 `dev`, `prod` 등의 다양한 단계, 즉 '스테이지'에 대해 알아볼 예정이다. 필자가 궁금했기에 스터디 후 아카이빙 차원에서 작성하는 아티클이다. 실제 프로덕션 환경에서 스테이지가 어떻게 활용되는지, 현재 블로그 프로젝트에서 이 개념을 어떻게 적용할 수 있을지, 그리고 자파를 이용하여 이를 어떻게 구현할 수 있는지에 대해 다룰 것이다. 더 나아가 로그 레벨을 적절히 설정하여 운영 비용을 효과적으로 제어하는 방법을 살펴볼 것이다. 로그 레벨 관리는 서버리스 아키텍처에서 애플리케이션의 성능 모니터링, 오류 진단, 비용 최적화에 있어 중요한 역할을 한다.

# 2. 스테이지 관리

## 2.1 스테이지 개념

스테이지 관리는 소프트웨어 개발과 배포 과정에서 중요한 역할을 한다. 특히, 서버리스 아키텍처와 AWS 람다(Lambda)와 같은 환경에서는 더욱 그렇다. 스테이지 관리를 효율적으로 진행하면 안정적인 개발, 테스트, 배포가 가능하다. 보통 개발(Development), 테스트(Testing), 스테이징(Staging), 그리고 프로덕션(Production)으로 네 가지 주요 스테이지를 구분하여 사용한다. 

* **개발(Development)**: 이 스테이지는 새로운 기능 개발, 버그 수정, 코드 업데이트 등 초기 개발 활동이 이루어지는 단계이다. 빈번한 변경과 실험이 가능한 환경이며, 실패에 대한 비용이 상대적으로 낮다.
* **테스트(Testing)**: 개발된 기능과 코드가 예상대로 작동하는지 검증하는 단계이다. 여기서는 자동화된 테스트, 단위 테스트, 통합 테스트 등을 수행하여 버그를 찾고 수정한다.
* **스테이징(Staging)**: 실제 프로덕션 환경을 모방한 테스트 환경이다. 스테이징 환경에서는 프로덕션 환경으로 이동하기 전에 애플리케이션을 최종적으로 검증한다. 이 단계는 실제 운영 환경과 가능한 한 유사하게 설정되어야 한다.
* **프로덕션(Production)**: 실제 사용자가 접근하고 사용하는 환경이다. 프로덕션 환경에서의 변경은 매우 신중하게 이루어져야 하며, 최고의 성능, 안정성, 보안을 유지해야 한다.

## 2.2 블로그 프로젝트

필자의 블로그 프로젝트에서는 `dev`와 `prod`라는 두 가지 주요 스테이지만 적용했다. 그 이유인 즉슨, 모든 단계를 거치며 경험해보면 물론 좋겠지만, 모두 적용하기 보다 두 가지 스테이지만 적용함으로써 개발과 운영 사이 균형을 맞추고 간소화하고자 했다. `dev` 개발 스테이지에서 새로운 기능 개발, 디자인 변경 등이 이루어진 후 확인이 완료되면, `prod` 프로덕션 스테이지로 배포하는 방식이다.

## 2.3 Zappa 구현

### 2.3.1 자파 설정

자파는 `zappa_settings.json` 파일을 통해 다양한 환경 설정을 관리하는 것을 이전 포스팅에서 알 수 있었다. 이 파일 내에서 `dev`와 `prod` 스테이지를 구분하여 각각의 설정을 정의할 수 있다. 필자는 아래와 같이 입력하여 스테이지를 구분하여 운영중이다:

```json
{
    "dev": {
        "app_function": "app.app",
        "aws_region": "ap-northeast-2",
        "profile_name": "default",
        "project_name": "yoonminlee",
        "runtime": "python3.9",
        "s3_bucket": "zappa-yoonminlee-dev",
        "certificate_arn": "arn:aws:acm:us-east-1:...",
        "domain": "dev.yoonminlee.com"
    },
    "prod": {
        "app_function": "app.app",
        "aws_region": "ap-northeast-2",
        "profile_name": "default",
        "project_name": "yoonminlee",
        "runtime": "python3.9",
        "s3_bucket": "zappa-yoonminlee-prod",
        "certificate_arn": "arn:aws:acm:us-east-1:...",
        "domain": "yoonminlee.com"
    }
}
```

동일한 인증서로 인증 받은 도메인과 하위 도메인을 각 스테이지에 부여했다. 

### 2.3.2 배포 및 관리

`zappa deploy dev` 명령어는 `dev` 환경을 배포하고, `zappa update prod`는 `prod` 환경을 업데이트하는 방식으로 각 환경에 맞는 명령은 따로 이루어진다.

# 3. 로그 레벨 관리

서버리스 아키텍처에서 로깅은 애플리케이션의 상태를 모니터링하고, 문제를 진단하며, 보안 사고를 추적하는 데 필수적인 요소이다. 로그 레벨을 적절히 설정하는 것은 운영 비용을 절감하고, 로그의 가독성을 향상시키며, 중요한 정보만을 수집하는 데 도움을 준다. 현재 로그 레벨을 따로 설정하지 않아, 디버깅 모드의 로그가 다 전시되었기에 생각보다 AWS CloudWatch의 요금이 많이 나왔다. 요금 절감을 위해 로그 레벨을 더욱 높은 단계로 설정하는 방법을 안내할 것이다. 

## 3.1 로그 레벨 개념

로그 레벨은 애플리케이션에서 기록되는 로그의 상세도를 결정한다. 다음은 일반적인 로그 레벨들이다:

- **DEBUG**: 개발 중에 가장 상세한 정보를 제공한다. 배포 환경에서는 일반적으로 사용하지 않는다.
- **INFO**: 일반적인 운영 중에 유용한 정보를 기록한다.
- **WARNING**: 잠재적 문제를 알릴 때 사용한다.
- **ERROR**: 심각한 문제가 발생했을 때 사용한다.
- **CRITICAL**: 시스템에 심각한 손상을 줄 수 있는 문제가 발생했을 때 사용한다.

## 3.2 Zappa 설정에서 로그 레벨 설정

Zappa를 사용하여 로그 레벨을 설정하는 방법은 매우 간단하다. `zappa_settings.json` 파일 내에 `log_level` 키를 추가하여 원하는 로그 레벨을 지정할 수 있다. 예를 들어, 다음과 같이 설정할 수 있다:

```json
{
    "dev": {
        // 다른 설정들...
        "log_level": "WARNING"
    },
    "prod": {
        // 다른 설정들...
        "log_level": "ERROR"
    }
}
```

이렇게 설정함으로써, 개발 환경에서는 `WARNING` 이상의 로그를, 프로덕션 환경에서는 `ERROR` 이상의 로그만을 기록하게 된다. 이를 통해 불필요한 정보의 로깅을 방지하고, 로그를 통해 발생할 수 있는 비용을 절감할 수 있다.

## 3.3 로그 비용 관리

로그 레벨을 조정함으로써 로그 데이터의 양을 관리할 수 있으며, 이는 AWS CloudWatch와 같은 로그 관리 서비스에 저장되는 로그 양에 직접적으로 영향을 미친다. AWS에서 로그 데이터에 대해 청구하는 비용은 저장된 로그의 양과 로그 데이터의 보존 기간에 따라 달라진다. 따라서 로그 레벨을 적절히 설정하여 비용을 절약하는 것이 중요하다.



# 4. 마무리 

스테이지 관리와 로그 레벨 설정은 자파를 사용하여 플라스크 기반 서버리스 애플리케이션을 효과적으로 운영하는 데 있어 중요한 측면이다. 스테이지를 통해 개발, 테스트, 스테이징, 프로덕션 환경을 체계적으로 관리할 수 있으며, 로그 레벨 설정을 통해 필요한 정보만을 기록함으로써 비용을 최적화할 수 있다. 이런 관리 방법은 안정적인 서버리스 애플리케이션 운영을 위해 필수적이며, 개발자가 애플리케이션의 품질을 향상시키고 비용을 효율적으로 관리할 수 있도록 돕는다.

이로써 어느정도 혼자 운영할 수 있는 환경은 만들었지만, 욕심에 의해 추가적으로 자동화 배포 프로세스를 구축해보고 싶다. CI/CD(지속적 통합/지속적 배포) 파이프라인을 구축하여 배포 프로세스를 자동화해보고 싶다. 실수를 줄이고, 개발 생산성을 향상 시킬 수 있을 것 같아서 추후 적용하게 된다면 아티클로 다룰 것이다.

## 4.1 관련 아티클

- **자파(Zappa) 튜토리얼**
    1. [플라스크(Flask) 서버리스(Serverless) 배포](/zappa-flask-serverless-deployment)
    2. [사용자 정의 도메인(Custom Domain) 연결(Feat. AWS Route53 & 가비아(Gabia))](/zappa-custom-domain-route53-gabia)
    3. [스테이지(Stage) & 로그 레벨(Log level) 관리](/zappa-stage-log-level-management)
    4. [배포 해제 및 사용자 정의 도메인(Custom Domain) 제거](/zappa-undeploy-custom-domain-removal)
- [파비콘 만들기 및 적용 완벽 튜토리얼: Adobe Color, favicon.io, RealFaviconGenerator 사이트 활용](/favicon-creation-tutorial)
- [웹 배포 후 검색 노출(SEO) 세팅 완벽 가이드 (w/ 플라스크(Flask))](/web-deployment-seo-guide)
- [웹 배포 후 트래픽 분석 및 수익화를 위한 구글 애널리틱스(Google Analytics, GA4)와 애드센스(AdSense) 초기 세팅 및 연동 완벽 가이드](/google-analytics-adsense-setup-guide)
- [구글 서치 콘솔(Google Search Console)과 구글 애널리틱스(Google Analytics, GA4) 연결](/connect-google-search-console-analytics)