---
title: '자파(Zappa) 튜토리얼(1): 플라스크(Flask) 서버리스(Serverless) 배포'
description: '자파(Zappa)를 사용하여 AWS 람다(Lambda)에 서버리스(Serverless)로 플라스크(Flask) 애플리케이션을 배포하는 방법에 대해 안내한다.'
date: '2023-11-23'
tags: [자파(Zappa), 플라스크(Flask), AWS, 람다(Lambda), 서버리스(Serverless), 클라우드(Cloud), 웹 개발(Web Development)]
---
# 1. 서론

웹 애플리케이션을 배포하려는 방법은 어떤 것들이 있을까? 전통적으로 서버를 직접 관리하거나 클라우드 서비스를 이용하여 배포하는 방식 등이 존재한다. 하지만 필자와 같이 개인용 블로그 등을 배포하고자 하는 경우 서버를 계속 띄워놓는 비용이나 인프라를 관리하는 일 자체가 큰 리소스 낭비로 느껴진다. 이 경우처럼 상시의 큰 트래픽이 있는 서비스를 하는 것이 아닌, 단순히 개인용 웹 애플리케이션을 배포하고 싶을 때 서버리스(Serverless) 컴퓨팅을 사용하면 효율적으로 웹 애플리케이션을 배포할 수 있다.

## 1.1 서버리스(Serverless)

서버리스 컴퓨팅(Serverless Computing)은 요즘 웹 개발의 핵심 트렌드 중 하나이다. AWS 람다(Lambda)와 같은 서버리스 플랫폼을 사용하면 인프라 관리에 드는 시간과 비용을 줄이면서도 효율적으로 애플리케이션을 배포하고 운영할 수 있다. 필자도 혼자 만드는 여러 웹 애플리케이션을 서버리스로 배포하여 인프라 관리 인풋을 굉장히 많이 아꼈다. 이 포스팅을 통해 필자가 애용하는 파이썬(Python) 기반의 웹 프레임워크인 플라스크(Flask) 애플리케이션을 AWS 람다(Lambda)에 자파(Zappa)를 통해 배포하는 과정을 소개한다.

## 1.2 자파(Zappa)란?

![자파(Zappa) 공식 이미지(출처: Zappa 깃허브)](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-flask-serverless-deployment-1.png)

[자파(Zappa)](https://github.com/zappa/Zappa)는 서버리스 파이썬 웹 애플리케이션을 AWS 람다와 API 게이트웨이(Gateway)에 쉽게 배포할 수 있도록 해주는 툴이다. 자파를 사용하면 기존 플라스크 애플리케이션을 손쉽게 AWS 람다 환경으로 이전할 수 있다.

# 2. 플라스크(Flask) 애플리케이션 준비

## 2.1 가상환경 설치

자파는 파이썬 가상환경(Virtual Environment)를 만들어 그 가상환경을 통째로 람다에 배포하기 때문에 가상환경 구성이 무엇보다 중요하다. 필자는 익숙하고 가벼운 `virtualenv`로 진행한다.

```sh
$ mkdir zappa-tutorial                  # zappa-tutorial이라는 이름의 프로젝트 폴더 생성
$ cd zappa-tutorial                     # zappa-tutorial 폴더로 이동
zappa-tutorial$ pip install virtualenv  # virtualenv 설치
zappa-tutorial$ virtualenv zappa_venv   # zappa_venv라는 이름의 가상환경 생성
zappa-tutorial$ source zappa-tutorial/zappa_venv/bin/activate # 가상환경 실행
(zappa_venv) zappa-tutorial$            # 가상환경이 실행되었음을 확인 
```

가상환경 위에서 설치되는 모든 `pip` 라이브러리는 원래의 `pip` 라이브러리와 독립하여 실행된다. 따라서 플라스크와 자파가 이미 설치되어 있더라도 가상환경 내에 다시 설치한다.

```sh
(zappa_venv) zappa-tutorial$ pip3 install Flask # 플라스크 설치
(zappa_venv) zappa-tutorial$ pip3 install zappa # 자파 설치
```

## 2.2 간단한 웹 앱 제작

굉장히 간단한 웹 앱을 만드는 예제를 소개한다. 필자는 위에서 `zappa-tutorial`이라는 프로젝트 폴더를 생성했고, `app.py`라는 파일을 만들어서 아래와 같이 작성했다.

```py
from flask import Flask

app = Flask(__name__)      # 파일 이름과 같은 Flask 앱 객체 생성

@app.route("/")            # "/" 경로로 접속하는 경우
def home():
    return "Hello, World!" # "Hello, World!"를 출력

if __name__ == '__main__':
    app.run(debug=True)
```

이제 플라스크 웹 애플리케이션을 실행한다. `FLASK_APP` 환경 변수를 설정하고, `flask run` 명령어를 통해 실행해도 되고, 단순히 `python app.py` 명령어를 통해 실행해도 된다.

```sh
(zappa_venv) zappa-tutorial$ export FLASK_APP=app.py # Flask 객체가 선언된 파일명을 앱으로 지정
(zappa_venv) zappa-tutorial$ flask run               # Flask 앱 실행

or

(zappa_venv) zappa-tutorial$ python app.py           # Flask 앱 실행
```

`127.0.0.1:5000`으로 접속하면 `Hello, World!`가 출력될 것이다.

![`127.0.0.1:5000` 접속 후 `Hello, World!` 출력 화면](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-flask-serverless-deployment-2.png)

해당 세팅을 기반으로 배포하고 싶은 애플리케이션을 만들면 된다.

# 3. 자파(Zappa) 배포

이제부터 만든 플라스크 애플리케이션을 자파를 통해 AWS 람다에 배포한다.

## 3.1 AWS IAM 설정

먼저 자파가 우리의 AWS 계정을 조작할 수 있도록 권한을 주어야 한다. AWS에는 [AWS IAM(Identity and Access Management)](https://us-east-1.console.aws.amazon.com/iam)이라는 AWS 권한 관리 서비스가 있다.

![AWS IAM을 통해 액세스 키를 만드는 방법](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-flask-serverless-deployment-3.png)

AWS에 회원가입 및 로그인 후, 화면 우측 상단의 아이디를 클릭하면 나타나는 `보안 자격 증명`에 들어가, 하단 `액세스 키 만들기`에서 루트 액세스 키(root access key)를 만든다. 이때 `Access Key ID`와 `Secret Access Key`를 발급받아 비밀 공간에 메모한다.

자파는 AWS CLI(Command Line Interface)를 통해 AWS 계정을 조작하기 때문에, AWS CLI를 설치한 후 CLI에서 아래와 같이 AWS 계정 정보를 입력한다.

```sh
(zappa_venv) zappa-tutorial$ aws configure
AWS Access Key ID [None]: QWER********
AWS Secret Access Key [None]: asdf********
Default region name [None]: ap-northeast-2  # 서울 리전
Default output format [None]: json
```

이제 자파는 AWS CLI를 이용해 우리의 AWS 계정을 조작할 수 있다.

## 3.2 `zappa-settings.json` 파일 생성

배포를 위해 `zappa-settings.json` 파일을 생성해야 한다. 자파에서는 `zappa init`이라는 명령어를 통해 쉽고 빠르게 `zappa-settings.json` 파일을 생성할 수 있다.

```sh
(zappa_venv) zappa-tutorial$ zappa init
# 1. 환경 이름 설정: 보통 개발 단계에서 dev 스테이지로 설정
What do you want to call this environment (default 'dev'): dev

# 2. 람다 함수 코드가 저장될 S3 버킷 생성
Your Zappa deployments will need to be uploaded to a private S3 bucket.
If you donʼt have a bucket yet, weʼll create one for you too.
What do you want to call your bucket? (default 'zappa-qwerty1234'): zappa-tutorial-dev

# 3. 플라스크 애플리케이션 인식
It looks like this is a Flask application.
Whatʼs the modular path to your appʼs function?
This will likely be something like 'app.app'.
We discovered: app.app
Where is your appʼs function? (default 'app.app'): app.app

# 4. ap-northeast-2 외 다른 나라에서 글로벌하게 서비스할지 선택
Would you like to deploy this application globally? (default 'n') [y/n/(p)rimary]: y

# 5. 설정을 만들어 저장
Okay, hereʼs your zappa_settings.json:
{
    "dev": {
        "app_function": "app.app",
        "aws_region": "ap-northeast-2",
        "profile_name": "default",
        "project_name": "zappa-tutorial",
        "runtime": "python3.9",
        "s3_bucket": "zappa-tutorial-dev"
    }
    ...
}
Does this look okay? (default 'y') [y/n]: y
```

람다 함수의 이름은 `{프로젝트명}-{환경명}`이 된다. 프로젝트명은 프로젝트 폴더 이름, 여기서는 `zappa-tutorial`이고, 환경명은 지정해 준 값, 여기서는 `dev`이므로, 람다 함수 이름은 `zappa-tutorial-dev`가 된다. 람다 함수 코드는 AWS S3 버킷에 저장된다.

## 3.3 배포

이제 아래와 같이 배포한다.

```sh
(zappa_venv) zappa-tutorial$ zappa deploy dev # 설정했던 환경의 이름
Downloading and Installing dependencies..
...
Uploading zappa-tutorial-dev.zip (*.*MiB)..   # 전체 가상환경이 zip으로 압축
...
Deploying API Gateway..                       # 람다 함수를 AWS API Gateway에 연결해 URL을 자동으로 생성
...
Deployment complete! https://q1w2e3r4t5y6.execute-api.ap-northeast-2.amazonaws.com/dev
```

이제 `https://q1w2e3r4t5y6.execute-api.ap-northeast-2.amazonaws.com/dev`로 접속하면, `Hello, World!`가 출력되는 걸 확인할 수 있다. 웹 애플리케이션이 자파를 통해 AWS S3에 업로드된 뒤 AWS 람다를 통해 서비스되기 때문에, 이를 AWS API Gateway로 접근할 수 있는 것이다.

![배포 후 자동 생성 된 API 게이트웨이 도메인 접속 후 'Hello, World!' 출력 화면](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/zappa-flask-serverless-deployment-4.png)

# 4. 마무리 및 참고

아주 간단하고 빠르게 웹 애플리케이션을 배포했다. 그것도 클라우드에 서버리스로 말이다. 비용 효율적이며 인프라 관리 걱정 없이 개발에 집중할 수 있게 되었다. 근데 아무래도 URL을 보면 굉장히 못생겼다. 이대로 배포했다간 사용자들이 접속하기가 굉장히 불편할 것이다. 다음 포스팅에서는 이를 개선하기 위해 자파를 통해 배포한 웹 애플리케이션에 사용자 정의 도메인을 연결하는 방법을 소개한다.

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

## 4.2 참고

- [Zappa 공식 문서](https://github.com/zappa/Zappa)
- [Flask Microservice 구축 - Zappa로 AWS Lambda에 Flask 띄우기](https://panda5176.tistory.com/39)