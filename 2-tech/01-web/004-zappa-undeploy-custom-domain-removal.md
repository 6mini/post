---
title: '자파(Zappa) 튜토리얼(4): 배포 해제 및 사용자 정의 도메인(Custom Domain) 제거'
description: '자파(Zappa)를 사용하여 배포된 애플리케이션의 사용자 지정 도메인을 AWS에서 안전하게 제거하는 방법을 단계별로 안내한다. 배포 해제(Undeploy) 후에도 남아있는 도메인을 제거하여 깔끔하게 프로젝트 종료 및 리전 변경 시 재배포 전의 클린업 방법을 배운다.'
date: '2023-12-05'
tags: [자파(Zappa), AWS, 서버리스(Serverless), 클라우드(Cloud), 웹 개발(Web Development), 사용자 정의 도메인(Custom Domain), 클라우드프론트(Cloudfront), ACM]
---
# 1. 서론

[자파(Zappa)를 통해 플라스크(Flask) 애플리케이션을 훌륭하게 배포](/zappa-flask-serverless-deployment)했고, [사용자 정의 도메인(Custom Doamin)을 연결함](/zappa-custom-domain-route53-gabia)으로써 그럴싸한 서비스를 만들었지만, 앞으로 서비스를 운용하다 보면 가끔 배포를 해제해야 할 일이 발생할 수 있다. 이를 테면 필자의 경우:

1. 서비스를 더 이상 운용하지 않아 종료하는 경우
2. 해외 리전으로 배포했다가 국내 리전으로 변경하기 위해 해당 리전에서 도메인을 제거한 후 국내 서버로 재배포 및 도메인 재연결하는 경우

위 두 가지 경우를 겪은 경험이 있다. 단순하게 `zappa undeploy {환경명}`을 진행하면 자동으로 API 게이트웨이(Gateway)는 제거가 되지만, 도메인 연결은 해제되지 않는다. 실제로 도메인의 인증서를 제거하려 했더니 연결된 리소스가 클라우드프론트(Cloudfront)에 존재해서 삭제하지 못해 당황했던 경험이 있다.

![자파(Zappa)를 배포 해제했음에도 남아 있는 AWS Certificate Manager의 연결된 리소스](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-undeploy-custom-domain-removal-1.png)

또한 사용자 정의 도메인을 올바르게 해제하지 않은 상태에서 다른 환경에 도메인을 연결하려하니 `BadRequestException: An error occurred (BadRequestException) when calling the CreateDomainName operation: One or more of the CNAMEs you provided are already associated with a different resource.` 에러를 마주친 경험이 있다. 그렇다면 이제부터 올바르게 사용자 정의 도메인을 제거하고 배포 해제하는 방법을 알아보자.


# 2. API 게이트웨이(Gateway)에서 사용자 지정 도메인 제거

## 2.1 앱 재배포 및 재인증 

만약 사용자 정의 도메인(Custom Domain)을 제거하지 않고 `undeploy`를 통해 배포 해제를 진행한 경우, 기존 환경에 재배포하고 재인증하는 과정이 필요하다. 아직 배포를 해제하지 않은 경우엔 [다음 단계](#22)를 진행하면 된다.

CLI를 통해 아래의 명령어를 입력하여 앱을 재배포하고 재인증한다:

```sh
zappa deploy {환경명}
zappa certify {환경명}
```

## 2.2 사용자 지정 도메인 삭제

![AWS API 게이트웨이(Gateway)의 사용자 지정 도메인 이름 삭제 방법](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-undeploy-custom-domain-removal-2.png)

AWS 콘솔의 [API 게이트웨이(Gateway)](https://ap-northeast-2.console.aws.amazon.com/apigateway)에 접속한다. `사용자 지정 도메인 이름` 메뉴에서 제거하고자 하는 도메인을 선택한 후 `삭제` 버튼을 통해 삭제한다.

# 3. 자파(Zappa) 배포 해제

CLI를 통해 자파 배포 해제 명령을 실행하여 람다(Lambda) 함수와 관련 리소스를 제거한다:

```sh
zappa undeploy {환경명}
```

이 명령은 람다와 자파 설정에 의해 생성된 기타 AWS 리소스를 제거한다.

# 4. 도메인 관련 리소스 제거

필요한 경우 자파(Zappa)와 도메인을 연결하면서 설정된 리소스들을 삭제한다. Route 53의 DNS 레코드나 호스팅 영역, 인증서 등을 제거하는 절차를 안내한다. 호스팅 영역의 경우 유지하게 될 경우 월 고정 요금이 과금되니 꼭 제거해 주는 게 좋다.

## 4.1 Route 53

### 4.1.1 DNS 레코드 삭제

![AWS Route 53 DNS 레코드 삭제 방법](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-undeploy-custom-domain-removal-3.png)

AWS 콘솔에서 [Route 53](https://us-east-1.console.aws.amazon.com/route53)의 호스팅 영역에서 해당 도메인을 접속한다. `NS`와 `SOA` 유형의 레코드를 제외한 레코드를 선택하여 `레코드 삭제` 버튼을 통해 삭제를 진행한다.

### 4.1.2 호스팅 영역 삭제

![AWS Route 53 호스팅 영역 삭제 방법](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-undeploy-custom-domain-removal-4.png)

AWS 콘솔에서 [Route 53](https://us-east-1.console.aws.amazon.com/route53)의 호스팅 영역에서 제거하고자 하는 이름을 선택 후 `삭제` 버튼을 통해 삭제를 진행한다.

## 4.2 ACM 인증서 제거

![AWS Certificate Manager에서 인증서 삭제 방법](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-undeploy-custom-domain-removal-5.png)

AWS 콘솔에서 [AWS Certificate Manager](https://us-east-1.console.aws.amazon.com/acm)의 `인증서 나열`에 접속한다. 혹시나 아무 인증서도 나열되지 않는다면 리전을 확인한다. 자파(Zappa)는 버지니아 북부의 리전만 허용하기 때문에 `us-east-1`로 접속하면 보일 것이다. 만약 본 아티클의 과정을 잘 따라왔다면 제거하고자 하는 도메인의 인증서 `사용 중`의 상태가 `아니오`로 표시되어 있을 것이다. 아직 `예`라면 AWS에서 전파가 완료될 때까지 조금만 더 기다려보자. `사용 중`의 상태가 `아니오`라면 이제 인증서까지 완벽하게 삭제할 수 있다.

# 5. 마무리 및 참고

자파(Zappa)를 활용한 서버리스 배포는 편의성과 빠른 배포 속도로 매력적인 옵션이지만, 프로젝트의 종료 또는 다른 환경으로 이전 시, 사용자 지정 도메인과 연결된 리소스를 깔끔하게 제거하는 것이 중요하다.

배포 해제(undeploy) 과정에서 종종 간과되기 쉬운 API 게이트웨이(Gateway)의 사용자 지정 도메인(Custom Domain) 제거, Route 53의 DNS 레코드 및 호스팅 영역 관리, 그리고 ACM 인증서의 제거는 AWS에서 프로젝트를 완전히 정리하는 데 필수적이다. 불필요한 비용 발생을 방지하고, 자원을 효율적으로 관리하는 데 도움이 된다.

## 5.1 관련 아티클

- **자파(Zappa) 튜토리얼**
    1. [플라스크(Flask) 서버리스(Serverless) 배포](/zappa-flask-serverless-deployment)
    2. [사용자 정의 도메인(Custom Domain) 연결(Feat. AWS Route53 & 가비아(Gabia))](/zappa-custom-domain-route53-gabia)
    3. [스테이지(Stage) & 로그 레벨(Log level) 관리](/zappa-stage-log-level-management)
    4. [배포 해제 및 사용자 정의 도메인(Custom Domain) 제거](/zappa-undeploy-custom-domain-removal)

## 5.2 참고

- [Zappa Undeploy Leaves behind a Custom Domain Name](https://github.com/Miserlou/Zappa/issues/1276)