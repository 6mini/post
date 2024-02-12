---
title: '리눅스(Linux)나 유닉스(Unix)에서 터미널 세션이 종료되도 프로그램을 계속 실행하는 방법: nohup 튜토리얼'
description: "리눅스(Linux)와 유닉스(Unix) 시스템에서 nohup 명령어를 사용하여 터미널 세션이 종료된 후에도 파이썬 스크립트 및 기타 프로그램을 지속적으로 실행하는 방법을 소개한다. 이 아티클에서는 nohup 사용법, 실시간 모니터링, 프로세스 관리까지 자세한 튜토리얼을 제공한다."
date: '2023-12-13'
tags: [리눅스(Linux), 유닉스(Unix), 파이썬(Python), nohup, 백그라운드, 터미널 명령어]
---
# 1. 서론

![리눅스(Linux) nohup 일러스트레이션](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/linux-unix-nohup-continuous-program-execution-1.webp)


리눅스(Linux)나 유닉스(Unix) 시스템을 이용하여 프로그래밍을 하는 개발자라면, 장시간 실행해야 하는 스크립트나 프로그램을 실행한 후 터미널 세션을 유지하지 않아도 지속적으로 구동시켜야하는 경우가 생긴다. 이를테면, 오랜 수집 시간이 소요되는 1회성 데이터 수집 프로그램이나, 기존 장시간 돌아가는 프로그램의 트러블 슈팅 후 수동으로 돌리는 경우 등이 있다. 이 때 세션을 종료해도 작업이 계속 되게 할 수 있는 `nohup`이라는 명령어가 존재한다. 자세히 알아보자.

# 2. 문제 상황

만약, 파이썬(Python) 프로그램 예제가 있다면:

```py
import time
from datetime import datetime

for _ in range(10):
    now = datetime.now()
    print(f"Current time: {now}")
    time.sleep(5)
```

만약 위와 같은 파이썬 프로그램 예제가 존재하고, 이 프로그램을 실행 중 종료하면 바로 프로그램의 실행이 끝난다.

```sh
$ python3 example.py
Current time: 2024-02-08 04:18:57.410940
Current time: 2024-02-08 04:19:02.416118
# 구동 중 Command + C 혹은 터미널 종료
```

필자는 `nohup`을 모르던 아가(물론 지금도 아가지만) 시절 백그라운드에서 돌리기 위해, 크론탭(Crontab)을 이용하여 1, 2분 뒤로 스케쥴링하고, 프로그램이 실행되고나면 크론을 제거하는 바보같은 행동을 했었다.

# 3. `nohup`이란?
`nohup`은 "No Hangup"의 줄임말로, 리눅스(Linux)나 유닉스(Unix) 계열 시스템에서 사용되는 명령어이다. 이 명령어는 터미널이 종료되거나 사용자가 로그아웃한 후에도 프로세스가 계속 실행되도록 한다. 기본적으로 터미널 세션이 종료되면, 그 세션을 통해 시작된 모든 백그라운드 프로세스와 작업들도 함께 종료된다. `nohup`을 사용하면 이러한 기본 동작을 변경하여, 터미널 세션이 종료되어도 특정 명령어나 프로그램이 계속 실행되도록 할 수 있다.

# 4. `nohup` 명령어 사용법

가장 간단한 형태로, `nohup` 명령어는 다음과 같이 사용된다:

```sh
$ nohup [명령어] &
```

여기서 `[명령어]`는 백그라운드에서 실행하고자 하는 명령어이다. `&`는 해당 명령어를 백그라운드에서 실행하라는 리눅스 쉘의 지시어이다.

## 4.1 예제

위 파이썬 예제 코드를 실행하면 아래와 같다:

```sh
$ nohup python3 example.py &
[1] 2851
nohup: ignoring input and appending output to 'nohup.out'
```

위와 같이 출력된다면, 성공적으로 실행됐다.

# 5. 출력

기본적으로, nohup으로 실행된 프로세스의 출력은 `nohup.out` 파일에 저장된다:

```sh
$ cat nohup.out
Current time: 2024-02-08 04:28:49.749706
Current time: 2024-02-08 04:28:54.754765
Current time: 2024-02-08 04:28:59.759895
Current time: 2024-02-08 04:29:04.764951
Current time: 2024-02-08 04:29:09.770131
Current time: 2024-02-08 04:29:14.775176
Current time: 2024-02-08 04:29:19.780285
Current time: 2024-02-08 04:29:24.785306
Current time: 2024-02-08 04:29:29.790443
Current time: 2024-02-08 04:29:34.795487
```

예제 코드에서 지정한 출력문이 정상적으로 전시된 걸 확인할 수 있다.

하지만 우리는 하나의 파일만 구동하는 것이 아닐 것이기 때문에 다른 출력 파일을 지정할 일이 생기고, 원한다면 출력을 리디렉션할 수 있다:

```sh
$ nohup [명령어] > output.txt 2>&1 &
```

여기서 `> output.txt`는 표준 출력을 `output.txt`로 리디렉션하고, `2>&1`은 표준 에러도 같은 파일로 리디렉션한다.

## 5.1 예제

예제 프로그램을 통해 명령어를 적용하여 실행한다:

```sh
$ nohup python3 example.py > output.txt 2>&1 &
```

출력을 확인한다:

```sh
$ cat output.txt
nohup: ignoring input
Current time: 2024-02-08 04:36:30.245988
Current time: 2024-02-08 04:36:35.250945
Current time: 2024-02-08 04:36:40.256084
Current time: 2024-02-08 04:36:45.261181
Current time: 2024-02-08 04:36:50.265461
Current time: 2024-02-08 04:36:55.270408
Current time: 2024-02-08 04:37:00.275561
Current time: 2024-02-08 04:37:05.280502
Current time: 2024-02-08 04:37:10.285645
Current time: 2024-02-08 04:37:15.290614
[1]+  Done      nohup python3 example.py > output.txt 2>&1
```

# 6. 실시간 모니터링

실시간으로 출력문을 확인하기 위해 `cat` 명령어를 계속 입력할 수는 없는 노릇이다. 분명 실시간으로 출력문을 봐야할 일이 생길 것이고, 이 때 `tail -f` 명령어를 사용하면 된다.

## 6.1 `tail`이란?

`tail` 명령어는 리눅스나 유닉스 기반 시스템에서 파일의 내용을 끝부터 출력하는 데 사용된다. 일반적으로 로그 파일이나 다른 지속적으로 업데이트되는 파일의 최신 내용을 확인할 때 유용하다. `tail` 명령어의 `-f` 옵션은 파일의 "follow" 모드를 활성화하여, 파일에 새로운 내용이 추가될 때마다 실시간으로 출력을 갱신한다.

## 6.2 `tail -f` 사용법

기본 형식은 다음과 같다:

```sh
tail -f [파일명]
```

이 명령어는 지정된 파일의 마지막 10줄을 출력하고, 파일이 업데이트될 때마다 새로운 내용을 계속해서 출력한다. 이는 로그 파일을 모니터링할 때 특히 유용하다.

### 6.2.1 예제

예제 프로그램을 모니터링하고 싶다면 다음 명령어를 사용할 수 있다:

```sh
$ tail -f output.txt
Current time: 2024-02-08 04:43:39.878872
Current time: 2024-02-08 04:43:44.884098
Current time: 2024-02-08 04:43:49.889194
Current time: 2024-02-08 04:43:54.894306
Current time: 2024-02-08 04:43:59.899387
```
이 명령어는 `output.txt` 파일의 마지막 내용을 출력하고, 새로운 로그 메시지가 파일에 추가될 때마다 즉시 그 내용을 터미널에 출력한다.

# 7. 실행 중인 프로세스 확인

`nohup`으로 실행한 프로세스가 정상적으로 작동하는지 확인하기 위해 `ps` 명령어를 사용할 수 있다:

```sh
$ ps ax | grep example.py
    2946 pts/1    S      0:00 python3 example.py
```

이 명령은 `example.py`가 포함된 모든 프로세스를 나열한다.

# 8. 프로세스 종료

백그라운드에서 실행 중인 프로세스를 종료하려면, 먼저 프로세스 ID(PID)를 찾아야 합니다. 그런 다음 kill 명령어를 사용하여 PID를 종료한다:

```sh
$ kill [PID]
```

## 8.1 예제

```sh
$ ps ax | grep example.py
   2960 pts/1    S      0:00 python3 example.py
$ kill 2960
$ ps ax | grep example.py

```

실행한 프로그램에 대한 PID는 `2960`이고 해당 PID를 통해 종료한다.

# 9. 주의사항

`nohup`을 사용할 때, 시스템이 재부팅되면 해당 프로세스도 종료된다. 시스템 재부팅 후에도 프로세스가 계속 실행되길 원한다면, cron의 @reboot 기능을 사용하거나 시스템의 시작 스크립트에 추가해야 한다. `nohup.out` 파일이 클 경우, 디스크 공간을 소모할 수 있으니 주기적으로 확인하고 관리하는 것이 좋다.