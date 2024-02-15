---
title: '웹 크롤링 IP 차단 우회 방법론: AWS EC2 재부팅을 통한 IP 변경 및 연속 수집 자동화 전략'
description: "Amazon Web Services(AWS)의 Elastic Compute Cloud(EC2)를 이용하여 웹 크롤링 시 발생하는 IP 차단 문제를 우회하고, 크롤링 작업을 자동화하는 방법을 단계별로 설명한다. EC2 인스턴스 재부팅을 통한 IP 변경, AWS S3와 람다(Lambda) 함수를 사용한 자동 재시작 및 종료 제어 방법을 포함한다."
date: '2024-02-15'
tags: [크롤링(Crawling), 스크래핑(Scraping), IP 차단 우회, 자동화(Automation), AWS, 람다(Lambda), EC2(Elastic Compute Cloud), S3(Simple Storage Service)]
---
# 1. 서론

웹 크롤링을 진행해본 사람이라면 인지하겠지만, 조금이라도 고도화된 웹 페이지를 크롤링할 때 IP 차단에 대한 문제를 마주치는 건 불가피하다. 이는 웹 페이지의 서버가 과도한 요청으로 인해 자원을 과부하시키는 것을 방지하기 위한 조치로, 크롤링을 통해 데이터를 수집하려는 사용자의 IP를 차단하는 것이다. 그래서 이를 우회하기 위한 전략이 필요한데, 조금만 검색해보면 다음과 같은 방법들을 찾을 수 있다:

1. **`User-Agent` 설정**: 웹 브라우저의 `User-Agent`를 설정하여 서버에 요청을 보내면, 서버는 사용자의 브라우저로 인식하게 되어 IP 차단을 우회할 수 있다. 하지만 이 방법은 완전하지 않으며, 한계가 있다.
2. **IP 프록시 서버 혹은 Tor 등을 이용한 IP 변경**: IP 프록시 서버를 이용하여 IP를 변경하거나, Tor와 같은 익명화 네트워크를 이용하여 차단을 우회할 수 있다. 하지만 이 방법은 무료로 사용할 수 있는 서비스가 제한적이며, 유료로 사용할 경우 비용 문제가 발생한다. 속도의 이슈 또한 피해갈 수 없다.
3. **크롤링 속도를 불규칙하게 조절**: 서버에 요청을 보낼 때마다 요청 간격을 랜덤하게 설정하여, 서버에서 과부하로 인식하지 않도록 할 수 있다. 하지만 이 방법은 크롤링 속도를 느리게 만들어야 하므로, 시간이 돈인 우리에게는 비효율적이다.

필자는 위 방법들을 모두 사용해보았지만, 시간 혹은 비용면에서 효율적이지 못하거나 효과적으로 IP 차단을 우회하기 어려웠다. 하지만 Amazon Web Services(AWS)의 Elastic Compute Cloud(EC2)의 특성 이용하여 IP 차단을 우회하는 방법을 사용하면서, 안정적으로 크롤링을 수행할 수 있었다. 간단히 소개하자면, EC2의 재부팅을 통해 할당받는 IP가 변경되는 특성을 사용하여 차단 시 인스턴스 중지 후 시작하는 방법을 다룰텐데, 프로그램과 인스턴스가 종료되는 만큼, 작업의 연속성 유지 방법에 대해서도 다룰 것이다. AWS 람다(Lambda)와 S3 이벤트 트리거를 활용하여 인스턴스의 자동 재시작 및 종료를 구현하는 방법까지 소개하여, 이 방법을 통한 웹 크롤링 작업을 완전 자동화하는 방법에 대해 설명하고자 한다.

# 2. EC2 인스턴스와 IP 우회

## 2.1 EC2 인스턴스의 특성을 활용한 IP 우회 방법

EC2는 사용자에게 가상 서버를 제공하는 AWS 서비스이다. 각 EC2 인스턴스는 고유한 공용 IP 주소를 할당받으며, 이 주소는 인스턴스를 재부팅할 때마다 변경된다. 크롤링 작업에 있어, 이 특성을 활용하면 웹사이트로부터 IP 차단을 효과적으로 우회할 수 있다.

![인스턴스를 재부팅하는 것이 아닌 중지 후 시작을 하는 경우에만 IP가 변경](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-crawling-ip-bypass-with-ec2-1.png)

참고로, 인스턴스를 "재부팅"하는 것이 아닌, "중지" 후 "시작"을 하는 경우에만 IP가 변경되니 참고하면 좋다.

# 3. 우회 전략

전략 소개에 앞서 예제 상황을 가정한다:

1. 크롤링 상황: 무수히 많은 특정 상품들의 정보를 월에 한 번 크롤링한다.
2. 크롤링 대상: `f'https://example.com/{product_id}'`
3. 크롤링 속도: EC2 인스턴스의 요금을 최소화하기 위해 ASAP


## 3.1 기존 크롤러 코드

기존 아래와 같은 예제 코드를 통해 크롤링을 수행한다고 가정한다:

```py
import requests
from bs4 import BeautifulSoup
from concurrent.futures import ThreadPoolExecutor, as_completed
import pandas as pd

# 제품 정보를 수집하는 함수
def fetch_product_info(product_id):
    url = f"https://example.com/{product_id}"
    
    headers = {'User-Agent': 'Mozilla/5.0'}
    response = requests.get(url, headers=headers)
    
    soup = BeautifulSoup(response.text, 'html.parser')
    
    # 제품 이름
    product_name = soup.select_one('h1.product-name').text
    
    # 필요한 정보 추출
    data = {
        'product_id' : product_id,
        'product_name' : product_name,
        ...
        # 기타 정보
    }

    return data


# 데이터베이스에 저장하는 함수
def save_to_db(df):
    ...

if __name__ == "__main__":
    # 크롤링 대상 상품 ID
    product_ids = [1001, 1002, 1003, 1004, 1005, ...]

    # 제품 정보 수집
    products_info = []
    # 멀티 프로세싱을 통한 크롤링
    with ThreadPoolExecutor(max_workers=10) as executor:
        futures = {executor.submit(fetch_product_info, product_id): product_id for product_id in product_ids}
        
        for future in as_completed(futures):
            data = future.result()
            products_info.append(data)


    # 수집된 정보를 데이터프레임으로 변환
    df = pd.DataFrame(products_info)

    # 데이터베이스에 저장
    save_to_db(df)
```

웹 크롤링을 위한 기본적인 구조로, 정의된 여러 제품의 ID(`product_ids`)에 대한 정보를 멀티 프로세싱을 통해 수집하는 절차를 보여준다.

## 3.2 IP 차단 감지 및 대응 전략

위 코드를 실행하여 빠른 속도로 수집을 진행하다보면, 어느 순간부터 서버로부터 응답을 받지 못하게 되는데, 이는 IP 차단으로 인한 것이다. 이를 확인하기 위해, 크롤링을 수행하는 도중에 서버로부터 응답을 받지 못하는 경우, 제품 정보를 수집하는 함수(`fetch_product_info`)에 다음과 같은 코드를 추가하여 IP 차단 여부를 확인하여 작업을 중단시킬 수 있다:

```py
# 제품 정보를 수집하는 함수
def fetch_product_info(product_id, stop_event):
    # 작업 중단 여부를 전달하기 위한 이벤트
    # 이벤트가 끝난 경우 그냥 다 패스
    if stop_event.is_set():
        return None

    url = f"https://example.com/{product_id}"
    
    headers = {'User-Agent': 'Mozilla/5.0'}
    response = requests.get(url, headers=headers)

    # IP 차단 여부 확인
    if response.status_code != 200:
        print(f"IP 차단 감지: {response.status_code}")
        return None

    try:
        soup = BeautifulSoup(response.text, 'html.parser')
        
        product_name = soup.select_one('h1.product-name').text
        
        data = {
            'product_id' : product_id,
            'product_name' : product_name,
            ...
        }

        return data
    
    # 기타 예외 처리
    except Exception as e:
        print(f"크롤링 오류: {e}")
        return None
```

부모로부터 종료 이벤트를 받아 작업을 끝내는 코드와, IP 차단 여부 확인 코드 및 기타 크롤링 오류를 처리하는 코드를 추가하여, 오류가 발생한 경우에는 `None`을 반환하도록 한다. 다음 아래와 같은 `process_id_list`라는 함수를 새롭게 생성하여, 연속되는 에러를 감지하고 크롤링을 수행하는 함수를 종료시킨다:

```py
import threading

def process_id_list(product_ids, error_ids, final_error_ids):
    # 연속 에러 체크 카운터
    consecutive_error_counter = 0
    
    # 결과 저장 리스트
    results = []
    
    # 중단 여부 확인을 위한 이벤트
    stop_event = threading.Event()
    stop_event.clear()

    # 남은 아이템 수 출력
    len_product_ids = len(product_ids)
    print("남은 아이템 수:", len_product_ids)

    with ThreadPoolExecutor(max_workers=10) as executor:
        futures = {executor.submit(fetch_product_info, product_id, stop_event): product_id for product_id in product_ids}
        for future in as_completed(futures):
            product_id = futures[future]
            result = future.result()
            
            # 이벤트가 끝난 경우 그냥 다 패스해주기
            if stop_event.is_set():
                pass
            
            # 5회 이상 에러 발생 시    
            elif consecutive_error_counter >= 5:
                print("5회 연속 에러로 인한 인스턴스 재구동")
                # 종료 이벤트 설정
                stop_event.set()
            
            # 정상 동작 시
            elif result is not None:
                # 결과 저장
                results.append(result)
                # 연속 에러 카운터 초기화
                consecutive_error_counter = 0
                # 처리 ID 제거: 재실행할 때 다시 돌아가면 안됨
                product_ids.remove(product_id)
            
            # 에러 발생
            else:
                print(f"{product_id}에러 발생")
                # 만약 한번 에러난 이력이 있으면
                if product_id in error_ids:
                    # 최종 에러 리스트에 추가
                    final_error_ids.append(product_id)
                    # 작업 목록에서 제거
                    product_ids.remove(product_id)
                else:
                    # 에러 리스트에 추가
                    error_ids.append(product_id)
                    # 연속 에러 카운터 증가
                    consecutive_error_counter += 1
                        
    return results



if __name__ == "__main__":
    # 크롤링 대상 상품 ID
    product_ids = [1001, 1002, 1003, 1004, 1005, ...]

    # 에러 발생 상품 ID
    error_ids = []
    
    # 최종 에러 상품 ID
    final_error_ids = []
    
    # 상품 정보 수집
    products_info = process_id_list(product_ids, error_ids, final_error_ids)
    
    # 수집된 정보를 데이터프레임으로 변환
    df = pd.DataFrame(products_info)

    # 데이터베이스에 저장
    save_to_db(df)
```

제품 정보 수집(`fetch_product_info`) 함수에서 각 제품 ID에 대해 웹 페이지에 접속하여 제품 정보를 크롤링한다. `stop_event` 인자를 통해 외부에서 크롤링 작업을 중단할 수 있는 기능을 추가했다. IP가 차단되었거나 기타 예외가 발생하는 경우, 적절한 핸들링을 통해 에러 메시지를 출력하고 None을 반환한다. 멀티스레딩을 통한 병렬 크롤링(`process_id_list`) 함수에서 `stop_event`를 활용하여 연속된 에러 발생 또는 특정 조건 충족 시 크롤링을 중단한다. 처음 발생한 에러는 재시도 목록(`error_ids`)에 추가되며, 같은 제품에 대해 연속적으로 에러가 발생하면 최종 에러 목록(`final_error_ids`)에 추가된다. IP 차단으로 인한 5회 이상의 연속된 에러 발생 시 `stop_event`를 설정하여 모든 크롤링 작업을 중단한다.

# 4. 작업 연속성 유지

## 4.1 재부팅 혹은 종료를 위한 트리거 추가 및 변수 저장

이제 위 프로그램이 실행되고, 연속 에러 발생 시 인스턴스를 재부팅 시켜줄건데, 인스턴스가 재부팅된 후에도 작업하던 내용에 대한 연속성을 유지하기 위해 아래와 같이 각종 변수를 저장하고 읽어오는 코드를 추가한다:

```py
import datetime
import boto3
from pytz import timezone
import pickle

# 트리거용 S3 버킷에 파일을 업로드하는 함수
def s3_trigger(s3_prefix):
    now = datetime.datetime.now(timezone('Asia/Seoul'))
    date_str = now.strftime('%Y-%m-%d_%H:%M')
    
    s3_bucket = 'my-bucket-name'
    
    s3 = boto3.client('s3')
    
    s3_key = s3_prefix + date_str + '.txt'
    s3.put_object(Body='', Bucket=s3_bucket, Key=s3_key)


if __name__ == "__main__":
    # 변수 관리용 연월 설정
    now = datetime.datetime.now(timezone('Asia/Seoul'))
    year_month = now.strftime('%Y-%m')    
    
    
    try:
        # 이어서 진행할 경우 파일에서 읽어오기
        with open(f"state_{year_month}.pkl", 'rb') as f:
            product_ids, error_ids, final_error_ids = pickle.load(f)        
            
    except FileNotFoundError:
        # 파일이 없는 경우 새로운 월로 간주
        product_ids = [1001, 1002, 1003, 1004, 1005, ...]
        error_ids = []
        final_error_ids = []

    # 작업 수행
    results = process_id_list(product_ids, error_ids, final_error_ids)

    # 결과 저장
    if results:
        save_to_db(results)

    # 작업 완료 확인
    if not product_ids:
        # 변수 저장            
        with open(f"state_{year_month}.pkl", 'wb') as f:
            pickle.dump((product_ids, error_ids, final_error_ids), f)
        
        # 인스턴스 종료 위한 작업 완료 트리거
        s3_prefix = 'complete/'
        s3_trigger(s3_prefix)
        
    # 작업 미완료 시
    else:
        # 변수 저장            
        with open(f"state_{year_month}.pkl", 'wb') as f:
            pickle.dump((id_list, error_ids, twice_error_ids, start_time, total_len_id_list, count, complete_count), f)

        # 인스턴스 재부팅 위한 트리거
        s3_prefix = 'reboot/'
        s3_trigger(s3_prefix)
```

AWS S3 버킷을 사용하여 크롤링 작업의 상태를 관리하고, 작업의 시작, 진행, 완료 상태를 트리거하는 로직을 구현했다. `s3_trigger` 함수는 특정 S3 버킷에 빈 파일을 업로드함으로써 AWS 람다(Lambda)에서 이를 감지하고 작업을 수행하도록 유도한다. 메인 로직에서는 현재 날짜를 기준으로 상태를 관리하는 파일을 생성 또는 읽어오고, 크롤링 작업의 결과에 따라 데이터베이스에 저장하거나 상태 파일을 업데이트한다. 작업이 완료되면 `complete/` 프리픽스를 사용하여 S3에 트리거 파일을 업로드하고, 작업이 아직 미완료 상태일 경우 `reboot/` 프리픽스로 트리거 파일을 업로드하여 인스턴스의 재부팅 또는 다음 작업 단계를 알린다. 이를 통해 월 단위의 크롤링 작업을 연속성 있게 자동화할 수 있다.

## 4.2 재부팅 시 자동 실행을 위한 크론탭(CronTab) 설정

이제 크롤링 작업을 완전히 자동화하기 위해 EC2 인스턴스가 재부팅될 때마다 자동으로 크롤링 스크립트가 실행되도록 설정한다. 이를 위해 리눅스(Linux) 시스템의 크론탭(CronTab)을 사용할 수 있다. 크론탭은 시간 기반의 작업 스케줄러로, 정해진 시간에 스크립트나 명령어를 자동으로 실행할 수 있게 해준다. EC2 인스턴스의 경우, 인스턴스가 시작될 때 특정 스크립트를 실행하도록 크론탭을 설정함으로써, 크롤링 작업의 자동화를 구현할 수 있다.

### 4.2.1 크론탭 설정 방법

1. **크론탭 열기**: 터미널에서 `crontab -e` 명령어를 입력하여 크론탭 설정 파일을 연다. 처음 사용하는 경우, 편집기를 선택하라는 메시지가 나타날 수 있다.
2. **재부팅 시 스크립트 실행 설정**: 크론탭 파일에 다음과 같은 내용을 추가한다. 이는 인스턴스가 재부팅될 때마다 지정된 스크립트(예: your_script.py)를 60초 후에 실행하도록 설정한다. 이때, 스크립트 실행 결과를 로그 파일(예: log_file.log)에 저장하도록 설정할 수 있다. 여기서 `-u` 옵션은 파이썬 인터프리터가 버퍼링 없이 직접 출력하도록 한다. 이는 실시간 로깅에 유용할 수 있습니다.
```sh
@reboot sleep 60 && /usr/bin/python3 -u /path/to/your_script.py >> /path/to/log_file.log 2>&1
```
3. **크론탭 저장 및 종료**: 설정을 완료한 후 파일을 저장하고 크론탭 편집기를 종료한다. 이제 EC2 인스턴스가 재부팅될 때마다 설정한 크롤링 스크립트가 자동으로 실행된다.

# 5. AWS 람다를 이용한 인스턴스 제어

AWS 람다(Lambda)는 서버리스 컴퓨팅 서비스로, 서버를 직접 관리하지 않고도 코드를 실행할 수 있게 해준다. 이를 활용해 EC2 인스턴스의 자동 재부팅 및 종료 제어 로직을 구현할 수 있다. AWS S3 이벤트 트리거와 함께 사용하여, 특정 S3 버킷에 파일이 업로드 될 때마다 람다 함수를 실행시키는 방식으로 크롤링 작업의 자동화 흐름을 완성할 수 있다.

## 5.1  재부팅 람다 함수 생성

먼저 `EC2RebootFunction`라는 함수를 생성한다. 람다 함수 생성 방법은 본 아티클에서 다루지 않는다. 궁금하다면, 다음 아티클을 참고한다: [AWS 람다(Lambda) 튜토리얼(1): 함수 생성 및 테스트, 로그 및 모니터링](/aws-lambda-function-creation-testing-log-monitoring)

함수 코드 영역에 다음과 같은 파이썬 코드를 작성하여 EC2 인스턴스를 제어한다. 이 코드는 EC2 인스턴스의 상태를 확인하고, 인스턴스를 재부팅하는 기능을 한다:

```py
import boto3

# EC2 클라이언트 생성
ec2 = boto3.client('ec2')

# 재부팅할 인스턴스 ID
instance_id = 'i-01a23456b7cd8910e'

def lambda_handler(event, context):
    # 인스턴스 중지
    ec2.stop_instances(InstanceIds=[instance_id])
    # 인스턴스 중지 대기
    waiter = ec2.get_waiter('instance_stopped')
    waiter.wait(InstanceIds=[instance_id])

    # 인스턴스 시작
    ec2.start_instances(InstanceIds=[instance_id])

    print(f'Instance {instance_id} restarted successfully')
```

코드 작성이 완료되면, 함수를 배포한다. 이제 이 람다 함수는 S3 이벤트나 다른 AWS 서비스에서 직접 호출될 수 있다.


## 5.2 S3 이벤트 트리거 설정

위 람다 함수가 S3 버킷의 `reboot/` 경로에 파일이 업로드될 때마다 실행되도록 트리거를 추가한다.

![람다(Lambda) 함수 트리거 추가](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-crawling-ip-bypass-with-ec2-3.png)

람다 함수 "구성" 탭의 "트리거" 탭에서 "트리거 추가" 버튼을 클릭한다.

![람다(Lambda) 함수 트리거 구성](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/web-crawling-ip-bypass-with-ec2-2.png)

트리거는 "S3", 버킷은 "my-bucket-name"으로 설정한다. 이벤트 타입은 "All Object Created events"로 설정하고, 프리픽스(Prefix)와 서픽스(Suffix)는 "reboot/" 경로, ".txt"로 설정한다. 이렇게 설정 하면, `my-bucket-name/reboot/` 경로에 `txt` 파일이 업로드될 때마다 람다 함수가 실행된다.

## 5.3 종료 람다 함수 생성 및 트리거 설정

위와 비슷한 방법으로 인스턴스의 종료를 위한 `EC2StopFunction`과 같은 람다 함수를 생성하고 트리거를 추가한다:

```py
import boto3

# EC2 클라이언트 생성
ec2 = boto3.client('ec2')

# 종료할 인스턴스 ID
instance_id = 'i-01a23456b7cd8910e'

def lambda_handler(event, context):
    # 인스턴스 중지
    ec2.stop_instances(InstanceIds=[instance_id])
    # 인스턴스 중지 대기
    waiter = ec2.get_waiter('instance_stopped')
    waiter.wait(InstanceIds=[instance_id])

    print(f'Instance {instance_id} stopped successfully')
```

이렇게 자동으로 재부팅을 통해 IP를 바꿔가며 크롤링을 수행할 수 있는 작업은 끝났다.

## 5.4 시작 람다 함수 생성 및 트리거 설정

이제 매월 인스턴스만 켜주면 성공적으로 모든 자동화를 이뤄낼 수 있다. 아래 코드의 `EC2StartFunction`과 같은 람다 함수를 생성하고, 이벤트 브릿지 등을 통해 매월 1일에 실행되도록 설정한다:

```py
import boto3

# EC2 클라이언트 생성
ec2 = boto3.client('ec2')

# 시작할 인스턴스 ID
instance_id = 'i-01a23456b7cd8910e'

def lambda_handler(event, context):
    # 인스턴스 시작
    ec2.start_instances(InstanceIds=[instance_id])
```

# 6. 마무리 및 참고

이 아티클을 통해, Amazon Web Services(AWS)의 Elastic Compute Cloud(EC2)를 활용하여 웹 크롤링 시 IP 차단을 우회하는 방법과 크롤링 작업을 자동화하는 전체적인 프로세스를 소개했다. 이 방법은 IP 차단을 효과적으로 우회하고, 크롤링 작업의 연속성을 유지할 수 있게 해주며, AWS의 다양한 서비스를 사용하여 크롤링 작업을 완전 자동화하는 방법을 설명했다. 이 과정에서 EC2 인스턴스의 재부팅을 통한 IP 주소 변경, 멀티 스레딩을 이용한 효율적인 데이터 수집, AWS S3와 람다(Lambda) 함수를 사용한 작업의 자동 재시작 및 종료 제어 등을 다루었다. 또한, 재부팅 시 자동 실행을 위한 크론탭(CronTab) 설정과 시작, 재부팅, 종료를 위한 람다 함수의 구현 방법을 살펴보았다.

## 6.1 전체 코드

- [GitHub Gist: 6mini/web_crawling_ip_bypass_with_ec2.py](https://gist.github.com/6mini/44ff7413f1b88bdb377e8f2ca731d5f5)

```py
import requests
from bs4 import BeautifulSoup
from concurrent.futures import ThreadPoolExecutor, as_completed
import pandas as pd
import threading
import datetime
import boto3
from pytz import timezone
import pickle


# 제품 정보를 수집하는 함수
def fetch_product_info(product_id, stop_event):
    # 작업 중단 여부를 전달하기 위한 이벤트
    # 이벤트가 끝난 경우 그냥 다 패스
    if stop_event.is_set():
        return None

    url = f"https://example.com/{product_id}"
    
    headers = {'User-Agent': 'Mozilla/5.0'}
    response = requests.get(url, headers=headers)

    # IP 차단 여부 확인
    if response.status_code != 200:
        print(f"IP 차단 감지: {response.status_code}")
        return None

    try:
        soup = BeautifulSoup(response.text, 'html.parser')
        
        product_name = soup.select_one('h1.product-name').text
        
        data = {
            'product_id' : product_id,
            'product_name' : product_name,
            ...
        }

        return data
    
    # 기타 예외 처리
    except Exception as e:
        print(f"크롤링 오류: {e}")
        return None


# 데이터베이스에 저장하는 함수
def save_to_db(df):
    ...



def process_id_list(product_ids, error_ids, final_error_ids):
    # 연속 에러 체크 카운터
    consecutive_error_counter = 0
    
    # 결과 저장 리스트
    results = []
    
    # 중단 여부 확인을 위한 이벤트
    stop_event = threading.Event()
    stop_event.clear()

    # 남은 아이템 수 출력
    len_product_ids = len(product_ids)
    print("남은 아이템 수:", len_product_ids)

    with ThreadPoolExecutor(max_workers=10) as executor:
        futures = {executor.submit(fetch_product_info, product_id, stop_event): product_id for product_id in product_ids}
        for future in as_completed(futures):
            product_id = futures[future]
            result = future.result()
            
            # 이벤트가 끝난 경우 그냥 다 패스해주기
            if stop_event.is_set():
                pass
            
            # 5회 이상 에러 발생 시    
            elif consecutive_error_counter >= 5:
                print("5회 연속 에러로 인한 인스턴스 재구동")
                # 종료 이벤트 설정
                stop_event.set()
            
            # 정상 동작 시
            elif result is not None:
                # 결과 저장
                results.append(result)
                # 연속 에러 카운터 초기화
                consecutive_error_counter = 0
                # 처리 ID 제거: 재실행할 때 다시 돌아가면 안됨
                product_ids.remove(product_id)
            
            # 에러 발생
            else:
                print(f"{product_id}에러 발생")
                # 만약 한번 에러난 이력이 있으면
                if product_id in error_ids:
                    # 최종 에러 리스트에 추가
                    final_error_ids.append(product_id)
                    # 작업 목록에서 제거
                    product_ids.remove(product_id)
                else:
                    # 에러 리스트에 추가
                    error_ids.append(product_id)
                    # 연속 에러 카운터 증가
                    consecutive_error_counter += 1
                        
    return results


# 트리거용 S3 버킷에 파일을 업로드하는 함수
def s3_trigger(s3_prefix):
    now = datetime.datetime.now(timezone('Asia/Seoul'))
    date_str = now.strftime('%Y-%m-%d_%H:%M')
    
    s3_bucket = 'my-bucket-name'
    
    s3 = boto3.client('s3')
    
    s3_key = s3_prefix + date_str + '.txt'
    s3.put_object(Body='', Bucket=s3_bucket, Key=s3_key)


if __name__ == "__main__":
    # 변수 관리용 연월 설정
    now = datetime.datetime.now(timezone('Asia/Seoul'))
    year_month = now.strftime('%Y-%m')    
    
    
    try:
        # 이어서 진행할 경우 파일에서 읽어오기
        with open(f"state_{year_month}.pkl", 'rb') as f:
            product_ids, error_ids, final_error_ids = pickle.load(f)        
            
    except FileNotFoundError:
        # 파일이 없는 경우 새로운 월로 간주
        product_ids = [1001, 1002, 1003, 1004, 1005, ...]
        error_ids = []
        final_error_ids = []

    # 작업 수행
    results = process_id_list(product_ids, error_ids, final_error_ids)

    # 결과 저장
    if results:
        save_to_db(results)

    # 작업 완료 확인
    if not product_ids:
        # 변수 저장            
        with open(f"state_{year_month}.pkl", 'wb') as f:
            pickle.dump((product_ids, error_ids, final_error_ids), f)
        
        # 인스턴스 종료 위한 작업 완료 트리거
        s3_prefix = 'complete/'
        s3_trigger(s3_prefix)
        
    # 작업 미완료 시
    else:
        # 변수 저장            
        with open(f"state_{year_month}.pkl", 'wb') as f:
            pickle.dump((product_ids, error_ids, final_error_ids), f)

        # 인스턴스 재부팅 위한 트리거
        s3_prefix = 'reboot/'
        s3_trigger(s3_prefix)
        
        
        
# EC2RebootFunction
import boto3

# EC2 클라이언트 생성
ec2 = boto3.client('ec2')

# 재부팅할 인스턴스 ID
instance_id = 'i-01a23456b7cd8910e'

def lambda_handler(event, context):
    # 인스턴스 중지
    ec2.stop_instances(InstanceIds=[instance_id])
    # 인스턴스 중지 대기
    waiter = ec2.get_waiter('instance_stopped')
    waiter.wait(InstanceIds=[instance_id])

    # 인스턴스 시작
    ec2.start_instances(InstanceIds=[instance_id])

    print(f'Instance {instance_id} restarted successfully')
    
    
    
# EC2StopFunction
import boto3

# EC2 클라이언트 생성
ec2 = boto3.client('ec2')

# 종료할 인스턴스 ID
instance_id = 'i-01a23456b7cd8910e'

def lambda_handler(event, context):
    # 인스턴스 중지
    ec2.stop_instances(InstanceIds=[instance_id])
    # 인스턴스 중지 대기
    waiter = ec2.get_waiter('instance_stopped')
    waiter.wait(InstanceIds=[instance_id])

    print(f'Instance {instance_id} stopped successfully')
    
    
    
# EC2StartFunction
import boto3

# EC2 클라이언트 생성
ec2 = boto3.client('ec2')

# 시작할 인스턴스 ID
instance_id = 'i-01a23456b7cd8910e'

def lambda_handler(event, context):
    # 인스턴스 시작
    ec2.start_instances(InstanceIds=[instance_id])
```