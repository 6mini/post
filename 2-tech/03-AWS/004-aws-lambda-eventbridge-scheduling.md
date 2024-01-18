---
title: 'AWS Lambda(Lambda) 튜토리얼(4): 이벤트 브릿지(EventBridge)를 통한 스케쥴링'
description: ''
date: '2024-01-12'
tags: []
---
# 서론

저번 아티클에서는 AWS Lambda의 계층(Layer) 기능을 활용하여 판다스(Pandas)를 비롯한 다양한 라이브러리를 Lambda 함수에 통합하는 방법에 대해 알아보았다. 이번 아티클에서는 Lambda 함수를 스케줄링하는 방법을 소개할 것이다. 스케줄링은 주기적으로 Lambda 함수를 자동으로 실행하도록 설정하는 것을 말한다. 이를 위해 비교적 간단한 AWS의 이벤트 브릿지(EventBridge)를 사용할 것이다.

# AWS 이벤트 브릿지(EventBridge)란?

AWS 이벤트 브릿지(EventBridge)는 다양한 AWS 서비스 간 이벤트를 중개하는 서비스로, 특정 이벤트에 반응하여 AWS Lambda와 같은 타겟을 자동으로 실행할 수 있게 해준다.

# EventBridge를 사용한 스케줄링 설정

![Alt text](aws-lambda-eventbridge-scheduling.md-1.png)

이전 튜토리얼에서 다룬 방법을 통해 생성된 함수의 개요에서 "트리거 추가(Add trigger)" 버튼을 클릭한다.

![Alt text](aws-lambda-eventbridge-scheduling.md-2.png)

"EventBridge(CloudWatch Events)"를 선택한다.

![Alt text](aws-lambda-eventbridge-scheduling.md-3.png)

"규칙 이름(Rule name)"을 입력하고, "예약 표현식(Schedule expression)"에 스케줄 표현식을 입력한다. 예를 들어, `cron(0 12 * * ? *)`은 매일 정오에 실행하는 표현식이다. "추가(Create)" 버튼을 클릭하여 트리거를 Lambda 함수에 연결한다.

# 테스트 및 모니터링

![Alt text](aws-lambda-eventbridge-scheduling.md-4.png)

트리거를 추가한 후, 실제로 스케줄링이 제대로 작동하는지 확인하는 것이 중요하다. AWS Lambda 콘솔에서 함수의 "모니터링" 탭을 통해 실행 기록과 로그를 확인할 수 있다.

# 마무리

AWS Lambda와 EventBridge를 사용하여 간단하게 함수를 스케줄링하는 방법을 마지막으로 AWS Lambda 튜토리얼을 마치게 되었다. 이 튜토리얼 시리즈를 통해, AWS Lambda의 기본적인 함수 생성부터 로그 관리, 환경 변수 설정, IAM 역할과 권한 설정, 메모리와 타임아웃 최적화, 계층 추가 및 판다스 라이브러리의 활용, 그리고 이벤트 브릿지를 통한 스케줄링에 이르기까지 다양한 주제들을 알아보았다.

이제 기본적으로 AWS Lambda를 사용함과 동시에, 서버리스 아키텍처의 장점을 최대한 활용할 준비가 되었다. AWS Lambda는 끊임없이 발전하고 있으며, 새로운 기능과 업데이트가 지속적으로 제공된다. 지금까지 배운 내용을 기반으로 계속해서 새로운 지식을 탐구하고 AWS Lambda를 통해 무한한 가능성을 탐험하길 바란다.

## 관련 아티클

- **AWS 람다(Lambda) 튜토리얼**
    1. [함수 생성 및 테스트, 로그 및 모니터링](/aws-lambda-function-creation-testing-log-monitoring)
    2. [환경 변수 설정, IAM 역할에 권한 추가, 타임아웃 및 메모리 설정](/aws-lambda-environment-variables-iam-timeout-memory)
    3. [계층(Layer) 추가 및 판다스(Pandas) 사용 방법](/aws-lambda-layer-pandas)
    4. [이벤트 브릿지(EventBridge)를 통한 스케쥴링](/aws-lambda-eventbridge-scheduling)