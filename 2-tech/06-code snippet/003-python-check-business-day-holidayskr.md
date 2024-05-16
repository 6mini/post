---
title: '파이썬(Python)으로 오늘이나 특정 날짜가 평일(영업일, 근무일)인지 휴일(주말, 공휴일)인지 확인하는 방법(Feat. holidayskr)'
description: "파이썬의 datetime 모듈과 holidayskr 라이브러리를 활용하여 날짜가 평일인지, 주말이나 공휴일인지를 자동으로 판별하는 방법을 소개한다. 일정 관리 시스템과 업무 자동화 스크립트 개발에 필수적인 이 기능은 특히 공휴일이 연도에 따라 변하는 대한민국에서 유용하게 사용될 수 있다. 대체 공휴일까지 고려한 자동 계산 방법을 배우고, 프로그램 실행을 조건적으로 제어하는 예제 코드도 제공한다."
date: '2024-05-16'
tags: [파이썬(Python), 한국 휴일(공휴일), holidayskr, 라이브러리(Library), 패키지(Package), PyPI, 공휴일 API]
---
# 1. 서론

파이썬(Python)을 활용하여 날짜가 평일인지, 주말 혹은 공휴일인지를 판별하는 것은 일정 관리 시스템 개발, 업무 자동화 스크립트 작성 등 다양한 프로젝트에서 필수적인 기능이다. 특히, 대한민국의 경우 공휴일이 정해져 있고, 이 공휴일은 연도에 따라 변할 수 있으며, 대체 공휴일이라는 것이 존재하기 때문에, 이를 자동으로 확인할 수 있는 방법을 알아두는 것이 좋다. 이번 아티클에서는 파이썬의 `datetime` 모듈과 `holidayskr` 라이브러리를 사용하여 이러한 기능을 구현하는 방법에 대해 알아볼 것이다.

# 2. 기본 개념 이해

## 2.1 `datetime` 모듈

파이썬에서 날짜와 시간을 다루기 위해서는 `datetime` 모듈을 사용한다. 이 모듈을 통해 현재 날짜와 시간을 얻거나, 특정 날짜를 지정하여 그 날의 요일 등을 알 수 있다. 예를 들어, `datetime.datetime.now()` 함수는 현재 날짜와 시간을 반환한다. 또한, `datetime` 객체의 `weekday()` 메소드를 사용하면 주어진 날짜의 요일을 숫자로 반환한다(월요일은 0, 일요일은 6).

## 2.2 `holidayskr` 라이브러리

대한민국의 공휴일을 확인하기 위해서는 `holidayskr` 라이브러리를 사용할 수 있다. 이 라이브러리는 대한민국의 공휴일을 자동으로 계산해주며, 특정 연도의 모든 공휴일을 얻거나, 특정 날짜나 오늘이 공휴일인지 여부를 확인할 수 있다.

# 3. 설치 방법

먼저, 필요한 라이브러리를 설치한다. `datetime` 모듈은 파이썬에 기본적으로 포함되어 있으므로 별도로 설치할 필요가 없지만, `holidayskr` 라이브러리는 설치해야 한다.

```sh
$ pip install holidayskr
```

# 4. 예제 코드

## 4.1 현재 날짜

### 4.1.1 평일 여부 확인

먼저, `datetime` 모듈로 현재 날짜가 평일인지 확인한다.

```python
from datetime import datetime, timedelta

# 현재 UTC 시간
now_utc = datetime.utcnow()
# 한국 시간
kst_offset = timedelta(hours=9)
now_kst = now_utc + kst_offset

if now_kst.weekday() < 5:
    print("오늘은 평일입니다.")
else:
    print("오늘은 주말입니다.")
```

```sh
오늘은 평일입니다.
```

만약 `weekday()`가 5보다 작으면(월요일부터 금요일까지는 0부터 4까지의 값을 가지므로), 오늘은 평일이라고 판단한다.

### 4.1.2 공휴일 여부 확인

다음으로, `holidayskr` 모듈을 통해 현재 날짜가 공휴일인지 확인한다.

```py
from holidayskr import today_is_holiday

# 오늘 날짜 공휴일 확인
if today_is_holiday():
    print("오늘은 공휴일입니다.")
else:
    print("오늘은 공휴일이 아닙니다.")
```

```sh
오늘은 공휴일이 아닙니다.
```

### 4.1.3 영업일 여부 통합 확인

위 파이썬 코드를 참고하여, 현재 날짜가 평일(영업일, 근무일)인지 휴일(주말, 공휴일)인지 확인하는 함수를 작성한다.

```python
from datetime import datetime, timedelta
from holidayskr import today_is_holiday

def check_if_business_day():
    # 현재 UTC 시간
    now_utc = datetime.utcnow()
    # 한국 시간
    kst_offset = timedelta(hours=9)
    now_kst = now_utc + kst_offset

    # 평일 확인 (월요일=0, 일요일=6)
    is_weekday = now_kst.weekday() < 5
    # 공휴일 확인
    is_holiday = today_is_holiday()

    if is_weekday and not is_holiday:
        return "오늘은 영업일입니다."
    else:
        return "오늘은 휴일입니다."

# 함수 실행 및 결과 출력
result = check_if_business_day()
print(result)
```

## 4.2 특정 날짜 영업일 여부 확인

2024년 5월 5일 어린이날을 통해 특정 날짜의 영업일 여부 확인도 구현해본다.

```py
from datetime import datetime, timedelta
from holidayskr import is_holiday

def check_if_business_day(date_input):
    # 입력된 날짜 파싱
    date_obj = datetime.strptime(date_input, "%Y-%m-%d")
    
    # 한국 시간 조정 (필요한 경우에만 사용)
    kst_offset = timedelta(hours=9)
    date_kst = date_obj + kst_offset

    # 평일 확인 (월요일=0, 일요일=6)
    is_weekday = date_kst.weekday() < 5
    # 공휴일 확인
    is_holiday_status = is_holiday(date_input)

    if is_weekday and not is_holiday_status:
        return "해당 날짜는 영업일입니다."
    else:
        return "해당 날짜는 휴일입니다."

# 함수 실행 및 결과 출력
specific_date = "2024-05-05"  # 'YYYY-MM-DD' 형식으로 날짜를 지정
result = check_if_business_day(specific_date)
print(result)
```

```sh
해당 날짜는 휴일입니다.
```

## 4.3 파이썬 프로그램을 영업일에만 실행하기

파이썬 프로그램이 현재 날짜가 영업일인 경우에만 구동되도록 하는 예제 코드를 작성한다. 기존에 제공된 `check_if_business_day` 함수를 사용하여 현재 날짜가 영업일인지 확인하고, 영업일일 때만 특정 기능이 실행되도록 설정한다.

```py
from datetime import datetime, timedelta
from holidayskr import today_is_holiday

def check_if_business_day():
    # 현재 UTC 시간
    now_utc = datetime.utcnow()
    # 한국 시간
    kst_offset = timedelta(hours=9)
    now_kst = now_utc + kst_offset

    # 평일 확인 (월요일=0, 일요일=6)
    is_weekday = now_kst.weekday() < 5
    # 공휴일 확인
    is_holiday = today_is_holiday()

    return is_weekday and not is_holiday

def main_program_function():
    # 여기에 프로그램의 주요 기능을 구현
    print("프로그램을 실행합니다.")

if check_if_business_day():
    main_program_function()
else:
    print("오늘은 휴일이므로 프로그램이 실행되지 않습니다.")
```

# 5. 마무리 및 참고

파이썬의 `datetime` 모듈과 `holidayskr` 라이브러리를 사용하여 날짜가 평일인지, 주말이나 공휴일인지 판별하는 방법을 소개했다. 이러한 기능은 일정 관리 시스템, 업무 자동화 스크립트, 그리고 다양한 소프트웨어 개발 프로젝트에서 매우 유용하게 활용될 수 있다. 이를 통해, 사용자는 현재 날짜 또는 특정 날짜가 영업일인지 여부를 자동으로 판단할 수 있으며, 이 정보를 바탕으로 프로그램의 실행 여부를 조건적으로 결정할 수 있다. 이는 특히 업무 스케줄링과 자동화에서 중요한 역할을 한다. 또한, `holidayskr` 라이브러리의 활용은 대한민국의 공휴일을 자동으로 계산해주므로, 연휴나 대체 공휴일 같은 변수도 쉽게 관리할 수 있게 해준다. 이는 휴일이 자주 변경될 수 있는 환경에서 특히 유용하다.

## 5.1 프로젝트 링크

- [깃허브(Github) check_business_day_kr.py Gist](https://gist.github.com/6mini/a348c74431babc69e9cef6115bfb7b00)
- [holidayskr 깃허브(GitHub) 레포지토리(Repository)](https://github.com/6mini/holidayskr)
- [holidayskr 패키지(PyPI)](https://pypi.org/project/holidayskr/)

## 5.2 관련 아티클

- [파이썬(Python)으로 한국의 휴일(공휴일)을 확인하는 최신 방법(Feat. holidayskr)](/python-holidayskr-korean-holiday-check-guide)
- [파이썬 패키지를 PyPI(Python Package Index)에 배포하는 방법부터 GitHub Actions를 통한 자동화까지](/python-package-deployment-pypi-github-actions)