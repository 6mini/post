---
title: 'AWS 람다(Lambda) 튜토리얼(2): 환경 변수 설정, IAM 역할에 권한 추가, 타임아웃 및 메모리 설정'
description: 'AWS Lambda를 사용하여 데이터를 크롤링하고 AWS S3에 데이터를 저장하는 예제를 토대로, 환경 변수 설정, IAM 역할에 권한 추가, 타임아웃 및 메모리 설정을 통해 Lambda 함수의 성능과 보안을 최적화하는 방법을 상세히 다룬다.'
date: '2024-01-17'
tags: [AWS, 람다(Lambda), 서버리스(Serverless), 클라우드(Cloud), 튜토리얼(Tutorial), IAM]
---
# 1. 서론

저번 아티클에서는 AWS Lambda 함수를 생성하고, 간단한 "Hello from Lambda" 응답을 통해 기능을 테스트하는 방법을 배웠다. 또한, 실행 로그와 모니터링을 통해 Lambda 함수의 성능과 상태를 평가하는 방법도 살펴보았다.

이 아티클에서는 AWS Lambda를 사용하여 데이터 크롤링 프로그램을 구현하고, 그 결과를 AWS S3에 저장하는 과정을 예제로 사용하며, 환경 변수 설정과 Lambda에 연결된 IAM 역할에 권한을 추가하는 방법에 대해서 자세히 다룬다. 그리고 중요한 실행 설정인 타임아웃 및 메모리 설정에 대해 자세히 살펴본다.

# 2. 크롤링 및 데이터 적재 예제 코드

다음은 Lambda에서 실행할 수 있는 간단한 Python 크롤링 및 데이터 저장 예제 코드이다. 이 코드는 웹페이지의 데이터를 크롤링하여 AWS S3 버킷에 저장하는 기능을 수행한다:

```py
import boto3
import requests
from bs4 import BeautifulSoup
import pandas as pd

s3 = boto3.client("s3")

def crawl_data(url):
    res = requests.get(url)
    soup = BeautifulSoup(res.content, "html.parser")
    # 여기에 크롤링 로직을 구현
    df = pd.DataFrame(...) # 크롤링 결과를 데이터프레임으로 변환
    return df

def save_to_s3(df, bucket, key):
    csv_buffer = df.to_csv(index=False).encode()
    s3.put_object(Body=csv_buffer, Bucket=bucket, Key=key)

def lambda_handler(event, context):
    url = "여기에 크롤링할 URL을 입력"
    df = crawl_data(url)
    bucket = "여기에 S3 버킷 이름을 입력"
    key = "여기에 S3 객체 키를 입력"
    save_to_s3(df, bucket, key)

    return {
        'statusCode': 200,
        'body': json.dumps('Data successfully scraped and saved to S3')
    }
```

이 코드는 크롤링할 URL에서 데이터를 가져와 DataFrame으로 변환한 후, 지정된 S3 버킷에 CSV 파일 형태로 저장한다. 이 예제를 통해 Lambda에서 Python을 사용한 크롤링과 S3와의 데이터 연동을 실습할 수 있다. 이제 이 코드를 기반으로 Lambda에서의 각종 설정에 대해 알아볼 것이다.

# 3. 환경 변수 설정

환경 변수는 주로 중요한 정보나 변경 가능성이 있는 값을 저장하는 데 사용된다. 일반적으로 API 키, 데이터베이스 연결 문자열, 비밀 토큰 등과 같이 보안적으로 민감한 데이터를 저장하지만, 이 코드의 경우 민감한 데이터가 존재하지 않으므로 S3 버킷 이름과 같은 상대적으로 위험이 적은 정보를 저장하며 실습을 진행한다.

## 3.1 환경 변수 추가

![Lambda 함수 "환경 변수(Environment variables)" 섹션의 "편집(Edit)" 버튼 클릭](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/aws-lambda-environment-variables-iam-timeout-memory-1.png)

Lambda 함수의 "구성(Configuration)" 탭에서 "환경 변수(Environment variables)" 섹션으로 이동한 후, "편집(Edit)" 버튼을 클릭한다.

![환경 변수 편집 절차](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/aws-lambda-environment-variables-iam-timeout-memory-2.png)

키(Key)" 필드에 `BUCKET_NAME`을 입력하고, "값(Value)" 필드에 S3 버킷 이름을 입력한다. 예를 들어, `my-lambda-bucket`을 입력할 수 있다.

이 방법으로 환경 변수를 설정함으로써, 코드 내에서 하드코딩된 값을 제거하고 보다 안전하게 정보를 관리할 수 있다. 또한, 이를 통해 개발, 테스트 및 프로덕션 환경 간 설정값을 쉽게 전환할 수 있다.

## 3.2 환경 변수 사용 예

```py
import os
import boto3

bucket = os.environ['BUCKET_NAME']

def lambda_handler(event, context):
    s3 = boto3.client('s3')
    # S3 버킷과 상호작용하는 코드

```

이 코드는 `BUCKET_NAME`이라는 환경 변수를 사용하여 S3 버킷의 이름을 가져온다.

## 3.3 예제 코드에서 활용

아래는 수정된 Lambda 함수 코드 예시이다. 이 코드는 `BUCKET_NAME` 환경 변수를 사용하여 S3 버킷 이름을 동적으로 가져온다:

```py
import boto3
import os
import requests
from bs4 import BeautifulSoup
import pandas as pd

def crawl_data(url):
    res = requests.get(url)
    soup = BeautifulSoup(res.content, "html.parser")
    # 여기에 크롤링 로직을 구현합니다.
    df = pd.DataFrame(...) # 크롤링 결과를 데이터프레임으로 변환
    return df

def save_to_s3(df, bucket, key):
    csv_buffer = df.to_csv(index=False).encode()
    s3 = boto3.client("s3")
    s3.put_object(Body=csv_buffer, Bucket=bucket, Key=key)

def lambda_handler(event, context):
    bucket = os.environ['BUCKET_NAME']
    key = "여기에 S3 객체 키를 입력"
    url = "여기에 크롤링할 URL을 입력"

    df = crawl_data(url)
    save_to_s3(df, bucket, key)

    return {
        'statusCode': 200,
        'body': json.dumps('Data successfully scraped and saved to S3')
    }
```

이 코드는 환경 변수 `BUCKET_NAME`에서 S3 버킷 이름을 가져와 `save_to_s3` 함수에 전달한다. 이렇게 함으로써, 함수 코드를 수정하지 않고도 AWS Management Console에서 S3 버킷을 변경할 수 있다.

# 4. Lambda와 연결된 IAM 역할에 권한 추가

IAM(Identity and Access Management) 역할은 Lambda 함수가 AWS의 다른 서비스와 상호작용할 때 필요한 권한을 정의한다. 적절한 IAM 역할과 정책 설정은 함수의 보안을 강화하고, 필요한 서비스에만 접근을 제한한다. Lambda 함수가 AWS 서비스, 예를 들어 S3에 접근하기 위해서는 적절한 권한이 필요하다. 이를 위해 IAM 역할에 필요한 권한을 추가한다. 참고로 Lambda 함수 생성 시, 연결된 IAM 역할이 자동으로 추가되어있기 때문에 권한을 추가하는 방법만 설명한다.

## 4.1 S3 접근 권한 추가

![Lambda 함수 역할 이름 클릭](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/aws-lambda-environment-variables-iam-timeout-memory-3.png)

Lambda 함수의 "구성(Configuration)" 탭에서 "권한(Permissions)" 섹션으로 이동한 후, "역할 이름(`myTestFunction-role-qwert1y`)"을 클릭하여 해당 역할로 이동한다.

![인라인 정책 생성](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/aws-lambda-environment-variables-iam-timeout-memory-4.png)

IAM 역할의 "권한(Permissions)" 탭에서 "권한 추가(Add permissions)" 버튼 클릭 후, "인라인 정책 생성(Add inline policy)"을 클릭한다.

![JSON 형태 정책 작성 및 다음](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/aws-lambda-environment-variables-iam-timeout-memory-5.png)

"JSON" 탭을 선택하고, 정책을 작성한다. 예를 들어, 특정 S3 버킷에 대한 읽기 권한을 부여하는 정책은 다음과 같다:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject"
      ],
      "Resource": "arn:aws:s3:::example-bucket/*"
    }
  ]
}
```

![정책 검토 및 생성](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/aws-lambda-environment-variables-iam-timeout-memory-6.png)

정책에 이름을 지정하고 정책을 검토한 후, "정책 생성(Create policy)"을 클릭하여 새 정책을 생성한다. 이렇게 하여 Lambda 함수에 필요한 권한을 추가하고, 보안을 강화할 수 있다.

# 5. 타임아웃 및 메모리 설정

Lambda 함수의 효율적인 실행을 위해서는 타임아웃과 메모리 설정이 중요하다. 특히 데이터 크롤링과 같이 시간이 다소 걸릴 수 있는 작업을 수행할 때, 이 설정들은 함수의 성능에 큰 영향을 미친다.

## 5.1 타임아웃의 중요성

타임아웃 설정은 Lambda 함수가 얼마나 오랫동안 실행될 수 있는지 결정한다. 데이터 크롤링 작업은 네트워크 지연, 대량의 데이터 처리 등으로 인해 예상보다 오래 걸릴 수 있다. 타임아웃이 너무 짧으면 함수가 작업을 완료하기 전에 중단될 수 있다.

## 5.2 메모리의 중요성

메모리 설정은 Lambda 함수가 사용할 수 있는 최대 메모리 양을 결정한다. 데이터 크롤링과 같이 메모리 집약적인 작업을 수행할 때 적절한 메모리 할당은 성능을 크게 향상시킬 수 있다.

## 5.3 타임아웃 및 메모리 설정

![Lambda 함수 일반 구성 편집](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/aws-lambda-environment-variables-iam-timeout-memory-7.png)

Lambda 함수의 "구성(Configuration)" 탭에서 "일반 구성(General configuration)" 섹션을 찾아 "편집(Edit)" 버튼을 클릭한다.

![메모리 및 타임아웃 설정 편집](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/aws-lambda-environment-variables-iam-timeout-memory-8.png)

"메모리(Memory)" 설정에서 필요한 메모리 양을 설정한다. 데이터 크롤링의 경우, 작업의 복잡성에 따라 128MB부터 시작하여 필요에 따라 증가시킬 수 있다. "타임아웃(Timeout)" 설정에서 실행 시간을 조정한다. 예를 들어, 크롤링 작업에 3분이 필요하다고 예상된다면, 타임아웃을 3분 또는 그보다 약간 더 길게 설정할 수 있다. 참고로 타임아웃은 15분을 넘길 수 없다. 

## 5.4 타임아웃 및 메모리 설정의 최적화

### 5.4.1 실험을 통한 최적화

함수의 타임아웃과 메모리 설정은 "하나의 정답"이 존재하지 않는다. 따라서, 다양한 설정을 실험해보고 함수의 성능을 모니터링하는 것이 중요하다. 예를 들어, 메모리를 증가시키면 함수가 더 빠르게 실행될 수 있지만, 비용도 증가할 수 있다.

### 5.4.2 모니터링을 통한 조정

AWS Lambda는 실행 로그와 모니터링 도구를 제공한다. 이를 통해 함수의 실행 시간, 메모리 사용량, 에러 발생 여부 등을 확인할 수 있다. 이 데이터를 기반으로 타임아웃과 메모리 설정을 조정할 수 있다.

이러한 단계별 설정과 최적화 과정을 통해 Lambda 함수의 성능을 크롤링 작업에 적합하게 조정할 수 있다. 또한, 이 설정들은 비용 효율성과 성능 사이의 균형을 맞추는 데도 도움을 준다.

# 6. 마무리

AWS Lambda를 사용하여 데이터 크롤링 프로그램을 구현하는 데 필요한 중요한 설정들, 즉 환경 변수 설정, IAM 역할에 권한 추가, 그리고 타임아웃 및 메모리 설정에 대해 자세히 살펴보았다. 이러한 설정들은 Lambda 함수의 안정성과 효율성을 높이는 데 중요한 역할을 한다.

다음 아티클에서는 AWS Lambda의 또 다른 중요한 기능인 '계층(Layer)'에 대해 자세히 다룰 예정이다. 계층을 활용하면 코드 재사용성을 높이고, 라이브러리 관리를 더욱 용이하게 할 수 있다. 특히, 데이터 분석에 자주 사용되는 판다스(Pandas) 라이브러리를 Lambda 함수에 통합하는 방법에 대해서도 살펴볼 것이다.

## 6.1 관련 아티클

- **AWS 람다(Lambda) 튜토리얼**
    1. [함수 생성 및 테스트, 로그 및 모니터링](/aws-lambda-function-creation-testing-log-monitoring)
    2. [환경 변수 설정, IAM 역할에 권한 추가, 타임아웃 및 메모리 설정](/aws-lambda-environment-variables-iam-timeout-memory)
    3. [계층(Layer) 추가 및 판다스(Pandas) 사용 방법](/aws-lambda-layer-pandas)