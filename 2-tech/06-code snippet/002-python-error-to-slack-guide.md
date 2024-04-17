---
title: '파이썬(Python) 프로그램 에러를 슬랙(Slack) 메시지로 전송하는 최적의 방법'
description: "파이썬(Python) 프로그램의 예외와 에러를 실시간으로 슬랙(Slack)으로 전송하는 방법을 설명한다. 슬랙 API를 활용하여 에러 알림 시스템을 구축하는 과정, 필요한 사전 준비 사항, 그리고 실제 코드 구현 방법을 포함한 가이드를 제공한다. 또한, 스택 트레이스를 포함한 예외 메시지를 슬랙 스레드로 분할 전송하는 방법을 통해, 깔끔하고 효율적인 알림 시스템을 만드는 방법을 배울 수 있다. 이 아티클은 실시간 오류 모니터링과 빠른 대응이 필요한 개발자와 시스템 관리자에게 유용하다."
date: '2024-04-17'
tags: [파이썬(Python), 슬랙(Slack), API, 예외 처리(Exception Handling), 에러 알림(Error Notification), 슬랙 봇(Slack Bot)]
---
# 1. 서론

파이썬(Python) 프로그래밍 과정에서 예외 처리는 어플리케이션의 안정성을 높이고, 오류를 신속하게 발견하며 해결하는 데 필수다. 특히, 실시간으로 오류를 모니터링하고 알림을 받을 수 있다면, 시스템 운영에 있어 큰 이점을 제공한다. 이번 아티클에서는 파이썬 프로그램에서 발생한 예외를 통해 슬랙(Slack)으로 알림을 보내는 방법에 대해 설명할 것이다. 특히, 에러의 모든 스택 트레이스를 포함하여 스레드(Thread) 댓글로 전송하기 때문에, 깔끔하고 효율적인 알림 시스템을 구축할 수 있다.

# 2. 사전 준비 사항

- 슬랙 API 사용을 위한 슬랙 앱(Slack App) 생성 및 봇 유저 액세스 토큰(Bot User OAuth Access Token) 획득
- 알림을 받을 슬랙 채널 ID 확인
- 파이썬 환경에 `requests` 모듈 설치 필요(`pip install requests`)

# 3. 코드

## 3.1 모듈 임포트

```python
import json
import requests
import traceback
import time
```

## 3.2 함수 정의

```py
def send_slack_notification(blocks, thread_ts=None):
    """
    Slack 채널에 메시지를 전송하는 함수이다.
    Args:
    blocks (list): Slack 메시지 블록의 리스트이다.
    thread_ts (str, optional): 스레드의 타임스탬프이다. 지정된 경우 메시지를 해당 스레드에 전송한다.
    Returns:
    dict: Slack API 응답이다.
    """
    SLACK_BOT_TOKEN = "your-slack-bot-token"
    
    url = "https://slack.com/api/chat.postMessage"
    headers = {
        "Authorization": "Bearer " + SLACK_BOT_TOKEN,
        "Content-Type": "application/json"
    }
    data = {
        "channel": "your-slack-channel-id",
        "blocks": json.dumps(blocks)
    }
    if thread_ts:
        data["thread_ts"] = thread_ts
    
    response = requests.post(url, headers=headers, data=json.dumps(data))
    if not response.ok:
        print(f"메시지 전송 오류: {response.text}")
    else:
        return response.json()

def handle_error(e, program_name):
    """
    발생한 예외를 처리하고 에러 메시지 및 스택 트레이스를 Slack으로 전송하는 함수이다.
    Args:
    e (Exception): 발생한 예외 객체이다.
    program_name (str): 에러가 발생한 프로그램의 이름이다.
    """
    
    exc_traceback = traceback.format_exc()
    error_title = f"⚠️ ['{program_name}' 오류 발생] ⚠️"
    error_msg = str(e)
    
    # 에러 제목과 메시지를 포함하는 메인 메시지 블록 생성
    blocks = [
        {
            "type": "header",
            "text": {
                "type": "plain_text",
                "text": error_title
            }
        },
        {
            "type": "section",
            "text": {
                "type": "mrkdwn",
                "text": "에러 내용: " + error_msg
            }
        }
    ]

    # 메인 에러 메시지를 Slack에 전송
    main_message_response = send_slack_notification(blocks)
    thread_ts = main_message_response.get('ts')
    
    # 에러 스택 트레이스를 Slack 스레드로 전송하며, 내용이 3000자를 초과하면 분할하여 전송
    max_chars = 3000
    if len(exc_traceback) > max_chars:
        chunks = [exc_traceback[i:i+max_chars] for i in range(0, len(exc_traceback), max_chars)]
        for chunk in chunks:
            send_slack_notification([{"type": "section", "text": {"type": "mrkdwn", "text": chunk}}], thread_ts=thread_ts)
    else:
        send_slack_notification([{"type": "section", "text": {"type": "mrkdwn", "text": exc_traceback}}], thread_ts=thread_ts)
```

## 3.3 코드 설명

### 3.3.1 `send_slack_notification` 함수

이 함수는 슬랙 API를 사용하여 지정된 슬랙 채널에 메시지를 전송한다. `blocks` 매개변수를 통해 메시지의 형식을 정의하고, `thread_ts` 매개변수가 있을 경우 특정 스레드에 메시지를 전송한다.

### 3.3.2 `handle_error` 함수

이 함수는 `Exception` 객체와 프로그램 이름을 매개변수로 받으며, 예외가 발생했을 때, 에러의 제목과 내용을 포함하는 메인 메시지 블록을 생성하고, `send_slack_notification` 함수를 호출하여 메시지를 전송한다. 이후, 스택 트레이스를 스레드로 전송하며, 3000자를 초과하는 경우 분할하여 전송한다.

# 4. 사용 예제

```py
def example_function():
    # 예를 들어, 0으로 나누기를 시도하여 에러를 발생시키는 코드
    result = 1 / 0
    return result


if __name__ == "__main__":
    # 사용 예제 1
    try:
        # 예제 함수 실행
        example_function()
    except Exception as e:
        # 에러가 발생하면 handle_error 함수를 호출하여 Slack으로 알림 전송
        handle_error(e, 'example_function')

    # 사용 예제 2
    message = 'example_message'
    send_slack_notification([{"type": "section", "text": {"type": "mrkdwn", "text": message}}])
```

예제 코드는 실제로 `division by zero` 에러를 발생시키고, `handle_error` 함수를 호출하여 슬랙으로 알림을 전송하는 과정을 보여준다. 또한, 간단한 메시지를 슬랙으로 직접 전송하는 추가 예제도 포함시켰다.

## 4.1 실행 결과

### 4.1.1 사용 예제 1번

![사용 예제 1번 결과: 프로그램 이름 및 에러 알림](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/python-error-to-slack-guide-1.png)

![사용 예제 1번 결과: 스레드의 댓글로 스택 트레이스 전송](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/python-error-to-slack-guide-2.png)

정상적으로 슬랙 채널에 프로그램 이름과 애러 내용, 스택 트레이스가 전송되었음을 확인할 수 있다.

### 4.1.2 사용 예제 2번

![사용 예제 2번 결과: 간단한 메시지 전송](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/python-error-to-slack-guide-3.png)

정상적으로 슬랙 채널에 메시지가 전송되었음을 확인할 수 있다.

# 5. 마무리 및 참고

이러한 방식으로 슬랙 알림 기능을 활용하면, 실시간으로 시스템의 상태를 모니터링하고 빠르게 대응할 수 있는 환경을 구축할 수 있다. 이 포스팅을 통해 소개된 코드는 베이스라인으로 사용될 수 있으며, 필요에 따라 커스터마이징하여 활용할 수 있다.

## 5.1 추가 팁

- [슬랙 메시지 포맷팅을 사용](https://app.slack.com/block-kit-builder/)하여 메시지의 가독성을 높인다.
- 다양한 슬랙 API 기능을 탐색하고, 알림 시스템의 기능을 확장한다.
- 예외가 발생하지 않는 경우에도 정기적인 상태 보고 메시지를 전송하여 시스템의 안정성을 확인한다.

## 5.2 전체 코드 확인

- [깃허브(Github) slack_error_notification.py Gist](https://gist.github.com/6mini/89819ae9482d8fd6d74024839706aa2d)