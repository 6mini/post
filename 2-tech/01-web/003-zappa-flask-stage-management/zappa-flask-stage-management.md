---
title: 'Zappa를 통한 Flask 서버리스 배포(3): 스테이지 관리'
description: 'Zappa와 Flask를 사용한 서버리스 애플리케이션 개발의 중요한 부분인 스테이지 관리에 대해 알아본다. 개발, 테스트, 스테이징, 프로덕션 환경을 효율적으로 관리하는 방법과 자파를 이용한 구현 과정에 대해 자세히 안내한다.'
date: '2023-12-02'
tags: [Zappa, Flask, AWS, Lambda, Serverless, 클라우드 컴퓨팅, 웹 개발, 스테이지]
---
# 1. 서론

![Flask Zappa 일러트스레이션](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-flask-stage-management-1.png)

지난 아티클에서 [자파(Zappa)를 활용하여 플라스크(Flask) 기반의 서버리스 애플리케이션을 AWS 람다(Lambda)에 배포](https://yoonminlee.com/zappa-flask-serverless-lambda)하는 과정을 살펴보았고, [사용자 정의 도메인 연결](https://yoonminlee.com/zappa-flask-custom-domain)까지 마쳤다. 이번 포스팅에서는 프로젝트를 진행하며 마주칠 수 있는 `dev`, `prod` 등의 스테이지에 대해 알아볼 예정이다. 필자가 궁금했기에 스터디 후 아카이빙 차원에서 작성하는 아티클이다. 실제 프로덕션 환경에서 스테이지가 어떻게 활용되는지, 현재 블로그 프로젝트에서 이 개념을 어떻게 적용할 수 있을지, 그리고 자파를 이용하여 이를 어떻게 구현할 수 있는지에 대해 다룰 것이다.

# 2. 스테이지 개념

스테이지 관리는 소프트웨어 개발과 배포 과정에서 중요한 역할을 한다. 특히, 서버리스 아키텍처와 AWS 람다(Lambda)와 같은 환경에서는 더욱 그렇다. 스테이지 관리를 효율적으로 진행하면 안정적인 개발, 테스트, 배포가 가능하다. 보통 개발(Development), 테스트(Testing), 스테이징(Staging), 그리고 프로덕션(Production)으로 네 가지 주요 스테이지를 구분하여 사용한다. 

* **개발(Development)**: 이 스테이지는 새로운 기능 개발, 버그 수정, 코드 업데이트 등 초기 개발 활동이 이루어지는 단계이다. 빈번한 변경과 실험이 가능한 환경이며, 실패에 대한 비용이 상대적으로 낮다.
* **테스트(Testing)**: 개발된 기능과 코드가 예상대로 작동하는지 검증하는 단계이다. 여기서는 자동화된 테스트, 단위 테스트, 통합 테스트 등을 수행하여 버그를 찾고 수정한다.
* **스테이징(Staging)**: 실제 프로덕션 환경을 모방한 테스트 환경이다. 스테이징 환경에서는 프로덕션 환경으로 이동하기 전에 애플리케이션을 최종적으로 검증한다. 이 단계는 실제 운영 환경과 가능한 한 유사하게 설정되어야 한다.
* **프로덕션(Production)**: 실제 사용자가 접근하고 사용하는 환경이다. 프로덕션 환경에서의 변경은 매우 신중하게 이루어져야 하며, 최고의 성능, 안정성, 보안을 유지해야 한다.

# 3. 블로그 프로젝트

필자의 블로그 프로젝트에서는 `dev`와 `prod`라는 두 가지 주요 스테이지만 적용했다. 그 이유인 즉슨, 모든 단계를 거치며 경험해보면 물론 좋겠지만, 모두 적용하기 보다 두 가지 스테이지만 적용함으로써 개발과 운영 사이 균형을 맞추고 간소화하고자 했다. `dev` 개발 스테이지에서 새로운 기능 개발, 디자인 변경 등이 이루어진 후 확인이 완료되면, `prod` 프로덕션 스테이지로 배포하는 방식이다.

# 4. Zappa 구현

## 4.1 자파 설정

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

## 4.2 배포 및 관리

`zappa deploy dev` 명령어는 `dev` 환경을 배포하고, `zappa update prod`는 `prod` 환경을 업데이트하는 방식으로 각 환경에 맞는 명령은 따로 이루어진다.

# 5. 마무리 

자파를 활용한 Flask 기반 서버리스 애플리케이션의 스테이지 관리에 대해 간단하게나마 이해할 수 있었다. 스테이지 관리는 안정적인 개발, 테스트, 그리고 배포 프로세스를 위해 필수적이다. 특히, 서버리스 아키텍처에서 이러한 관리는 애플리케이션의 안정성과 확장성을 결정하는 중요한 요소라는 생각이 든다. 스테이지 관리는 개발의 각 단계를 명확하게 구분하고, 각 단계에 맞는 전략을 적용함으로써 전체적인 애플리케이션의 품질을 높일 수 있다. Zappa와 같은 도구를 사용함으로써 서버리스 아키텍처의 이점을 최대한 활용할 수 있으며, 이를 통해 더욱 빠르고, 안정적인 애플리케이션 개발과 운영이 가능해진다.

이로써 어느정도 혼자 운영할 수 있는 환경은 만들었지만, 욕심에 의해 추가적으로 자동화 배포 프로세스를 구축해보고 싶다. CI/CD(지속적 통합/지속적 배포) 파이프라인을 구축하여 배포 프로세스를 자동화해보고 싶다. 실수를 줄이고, 개발 생산성을 향상 시킬 수 있을 것 같아서 추후 적용하게 된다면 아티클로 다뤄보도록 할 것이다.

## 5.1 관련 아티클

- Zappa를 통한 Flask 서버리스 배포
    1. [Zappa를 통한 Flask 서버리스 배포(1): AWS Lambda에 배포](/zappa-flask-serverless-lambda)
    2. [Zappa를 통한 Flask 서버리스 배포(2): 사용자 정의 도메인 연결](/zappa-flask-custom-domain)
    3. [Zappa를 통한 Flask 서버리스 배포(3): 스테이지 관리](/zappa-flask-stage-management)