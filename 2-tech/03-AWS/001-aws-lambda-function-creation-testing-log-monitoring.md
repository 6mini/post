---
title: 'AWS 람다(Lambda) 튜토리얼(1): 함수 생성 및 테스트, 로그 및 모니터링'
description: 'AWS Lambda를 이용한 서버리스 컴퓨팅 기초, Lambda 함수 생성 및 테스트, 로그 및 모니터링 방법을 다룬다. 데이터 스크래핑을 위한 Lambda 활용법과 EC2 대신 Lambda를 선택하는 이유, 그리고 Lambda의 일반적인 사용 사례까지 탐구한다. 서버리스 아키텍처와 클라우드 컴퓨팅의 효율성을 극대화하는 방법을 배운다.'
date: '2024-01-15'
tags: [AWS, 람다(Lambda), 서버리스(Serverless), 클라우드(Cloud), 튜토리얼(Tutorial)]
---
# 1. 서론

AWS 람다(Lambda)는 서버리스 컴퓨팅 서비스로, 서버 관리 없이 코드를 실행할 수 있게 해준다. Lambda 함수를 생성하여 데이터를 스크래핑하고 결과를 AWS S3에 저장하는 실습 예제를 통해, AWS Lambda를 구축하고 세팅하는 방법을 아티클별로 다룰룰 것이다.

# 2. Lambda 서비스 소개

Lambda는 코드를 실행하는데 필요한 서버 관리를 AWS가 대신 해주는 서비스이며, 사용자는 코드와 이벤트 트리거만 관리하면 된다. Lambda는 사용량 기반 과금 모델을 사용하므로, 실행 시간에 따라 비용이 발생한다. 하지만, 월 1,000,000회의 프리티어를 제공하기에, 잘 활용하면 비용 효율적인 서버리스 아키텍처를 구성할 수 있다.

## 2.1 데이터 스크래핑에서 EC2 대신 AWS Lambda를 사용하는 이유

나는 기존 데이터 스크래핑 프로그램을 EC2 인스턴스를 통해 스케줄링했다. 여러 이유들 때문에 EC2 대신 AWS Lambda를 사용하기로 결정했고, 이 시리즈는 이 작업을 진행하면서 정리한 절차이다. 그럼 어떤 이유에서 EC2 대신 Lambda를 사용하기로 했을까?

AWS Lambda를 사용하는 주된 이유 중 하나는 관리의 용이성과 비용 효율성이다. EC2 인스턴스를 사용할 때는 서버의 운영 상태를 지속적으로 관리하고 모니터링해야 하며, 서버가 실행되는 동안 지속적으로 비용이 발생한다. 반면, Lambda는 서버리스 아키텍처를 제공하여 서버 관리에 대한 부담을 줄여주며, 실제로 코드가 실행되는 시간에만 비용을 지불하게 된다.

특히, 데이터 스크래핑과 같은 작업에서 Lambda를 사용하면 다음과 같은 이점이 있다:

1. **자동 스케일링**: Lambda는 요청 수에 따라 자동으로 스케일링되므로, 트래픽이 많은 시간에도 안정적으로 데이터를 수집할 수 있다.
2. **이벤트 기반 실행**: Lambda는 특정 이벤트(예: S3 버킷에 파일 업로드, DynamoDB 테이블 업데이트)에 의해 자동으로 트리거 될 수 있어, 실시간 데이터 처리에 적합하다.
3. **유연한 리소스 할당**: 데이터 스크래핑 작업의 복잡성과 필요한 리소스에 따라 메모리와 실행 시간을 조절할 수 있다.

## 2.2 AWS Lambda의 일반적인 활용 사례

AWS Lambda는 그 유연성과 확장성 덕분에 다양한 활용 사례에 적합하다. 실제로 나의 경우에도 각종 웹 애플리케이션, 슬랙봇의 이벤트 기반 작업 처리 및 상호 작용, URL 단축 및 리다이렉션 등에 활발하게 사용하고 있다. 몇 가지 일반적인 사용 사례는 다음과 같다:

1. **웹 어플리케이션 및 API 백엔드**: Lambda를 사용하여 서버리스 백엔드를 구축하고 API Gateway와 함께 웹 어플리케이션 및 RESTful API를 구현할 수 있다.
2. **데이터 처리 및 변환**: Lambda는 S3 버킷에 업로드된 파일을 처리하거나 DynamoDB와 같은 데이터베이스에서 데이터 변환 작업을 수행하는 데 사용될 수 있다.
3. **실시간 파일 처리**: 이미지, 비디오, 로그 파일 등을 실시간으로 처리하고 변환하는 데 사용된다.
4. **IoT 백엔드**: Lambda는 IoT 디바이스에서 발생하는 이벤트에 반응하여 데이터를 처리하고 응답하는 데 사용될 수 있다.
5. **기계 학습 모델 배포**: 학습된 기계 학습 모델을 Lambda에 배포하여 실시간 예측을 제공할 수 있다.

<!--ad-->

# 3. Lambda 함수 생성

그럼 본론으로 들어가, Lambda 함수를 생성하는 방법부터 천천히 알아볼 것이다. 먼저 [AWS Management Console](https://ap-northeast-2.console.aws.amazon.com/console/home?region=ap-northeast-2#)에 접속하고, AWS 계정에 로그인한다.

![AWS 서비스 목록에서 Lambda 서비스 이동](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/aws-lambda-function-creation-testing-log-monitoring-1.png)

AWS 서비스 목록에서 "[Lambda](https://ap-northeast-2.console.aws.amazon.com/lambda/home?region=ap-northeast-2#/functions)"를 찾아 이동한다.

![함수 생성](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/aws-lambda-function-creation-testing-log-monitoring-2.png)

"함수 생성(Create Function)" 버튼을 클릭한다.

![함수 생성 절차](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/aws-lambda-function-creation-testing-log-monitoring-3.png)

"새로 작성(Author from scratch)"을 선택한다. "함수 이름(Function name)"에 예를 들어 'myTestFunction'과 같은 이름을 입력한다. "런타임(Runtime)"에서 사용할 Python 버전을 선택한다. 예를 들어 "Python 3.12"을 선택할 수 있다. 모든 설정을 마친 후 "함수 생성(Create function)" 버튼을 클릭한다.

# 4. Hello from Lambda!

Lambda 함수 생성 후에는 기본적으로 작성된 코드가 있다. 이 코드는 간단한 "Hello from Lambda" 메시지를 반환하는 예제이다. 생성된 Lambda 함수의 콘솔에서 기본적으로 작성된 코드를 확인한다. 기본 코드는 일반적으로 다음과 같다:

```python
import json

def lambda_handler(event, context):
    # TODO implement
    return {
        'statusCode': 200,
        'body': json.dumps('Hello from Lambda!')
    }
```

![Lambda 콘솔의 테스트](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/aws-lambda-function-creation-testing-log-monitoring-4.png)

Lambda 콘솔에서 "테스트(Test)" 버튼을 클릭한다.

![테스트 이벤트 구성](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/aws-lambda-function-creation-testing-log-monitoring-5.png)

새 테스트 이벤트를 생성하며, 이벤트 이름을 입력한다(예: "TestEvent"). 테스트 이벤트에 대한 입력값은 "hello-world" 템플릿으로 설정하면 된다. "저장" 버튼을 클릭한다.

![테스트 실행](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/aws-lambda-function-creation-testing-log-monitoring-4.png)

"테스트" 버튼을 클릭하여 함수를 실행한다.

![실행 결과 확인](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/aws-lambda-function-creation-testing-log-monitoring-6.png)

실행 결과를 확인한다. 성공적으로 실행되었다면, 'Hello from Lambda!' 메시지가 반환된다.

<!--ad-->

# 5. 로그 및 모니터링

Lambda 함수 실행 후, 로그 및 모니터링을 통해 함수의 실행 상태와 성능을 확인할 수 있다. AWS Lambda는 Amazon CloudWatch와 통합되어 있어, 로그 데이터와 메트릭스를 쉽게 확인할 수 있다.

![Lambda 콘솔에서 CloudWatch Logs 보기](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/aws-lambda-function-creation-testing-log-monitoring-7.png)

Lambda 콘솔에서 "모니터링" 탭의 "CloudWatch Logs 보기" 버튼을 클릭한다.

![CloudWatch 로그 페이지](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/aws-lambda-function-creation-testing-log-monitoring-8.png)

CloudWatch 로그 페이지에서는 각 실행에 대한 로그 스트림을 볼 수 있다.

![로그 스트림 클릭](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/aws-lambda-function-creation-testing-log-monitoring-9.png)

로그 스트림을 클릭하여 각 실행에 대한 세부 로그를 확인한다.

![로그 이벤트 확인](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/aws-lambda-function-creation-testing-log-monitoring-10.png)

여기에는 실행 시간, 메모리 사용량, 반환 값 및 함수 실행 중 발생한 모든 출력이나 오류 메시지가 포함된다.

# 6. 마무리

Lambda 함수를 생성하고, 간단한 "Hello from Lambda" 응답을 통해 기능을 테스트하는 방법에 대해 알아보았다. 또한, 실행 로그와 모니터링을 통해 Lambda 함수의 성능과 상태를 평가하는 방법도 살펴보았다.

다음 튜토리얼에서는 AWS Lambda 함수의 확장된 설정에 초점을 맞출 것이다. 실질적으로 간단한 크롤링 코드를 적용하고 Lambda 함수에 필요한 추가적인 설정을 살펴볼 것이다:

- **IAM 역할 설정**: Lambda 함수가 AWS S3와 같은 다른 AWS 서비스에 접근할 수 있도록 IAM 역할과 권한을 설정하는 방법을 알아본다.
- **환경 변수 설정**: Lambda 함수에서 사용할 환경 변수를 설정하는 방법과 그 중요성에 대해 알아본다.
- **타임아웃 및 메모리 설정**: Lambda 함수의 실행 시간과 메모리 할당량을 조절하여 최적화하는 방법을 알아본다.

## 6.1 관련 아티클

- **AWS 람다(Lambda) 튜토리얼**
    1. [함수 생성 및 테스트, 로그 및 모니터링](/aws-lambda-function-creation-testing-log-monitoring)
    2. [환경 변수 설정, IAM 역할에 권한 추가, 타임아웃 및 메모리 설정](/aws-lambda-environment-variables-iam-timeout-memory)
    3. [계층(Layer) 추가 및 판다스(Pandas) 사용 방법](/aws-lambda-layer-pandas)
    4. [이벤트 브릿지(EventBridge)를 통한 스케줄링](/aws-lambda-eventbridge-scheduling)