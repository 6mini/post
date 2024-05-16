---
title: '파이썬 패키지를 PyPI(Python Package Index)에 배포하는 방법부터 GitHub Actions를 통한 자동화까지'
description: "파이썬(Python) 패키지를 PyPI(Python Package Index)에 개발하고 배포하는 전체 과정을 설명한다. PyPI의 기능과 중요성, 패키지 개발 및 배포 절차, GitHub Actions를 활용한 배포 자동화 방법까지 포함된다. 필자가 개발한 holidayskr라는 예시 패키지를 통해 실질적인 배포 과정을 단계별로 설명하여, 개발자가 자신의 파이썬 프로젝트를 PyPI에 쉽게 배포할 수 있도록 돕는다. 또한, 프로젝트 관리, 협업, 버전 관리, 문서화 등 개발 스킬을 연습하는 데 유용한 팁까지 제공한다."
date: '2024-03-06'
tags: [파이썬(Python), PyPI(Python Package Index), 패키지 배포(Package Distribution), 깃허브(Github), 깃허브 액션(GitHub Actions), 자동화(Automation)]
---
# 1. 서론

이번에 처음으로 PyPI(Python Package Index)에 필자가 개발한 패키지를 배포했다. 배포 배경부터 배포 절차, GitHub Actions를 통한 자동화까지 배포 과정을 튜토리얼 형식으로 정리하여 제공하고자 한다.

## 1.1 PyPI(Python Package Index)란?

먼저 PyPI에 대해 알아보자. PyPI(Python Package Index)는 파이썬(Python) 프로그래밍 언어를 위한 소프트웨어 저장소이다. PyPI를 사용하면 파이썬 커뮤니티가 개발한 수천 개의 소프트웨어 패키지를 찾아보고, 다운로드하며, 설치할 수 있다. 이는 파이썬 패키지 관리자인 `pip`와 직접 연동되어, 사용자가 필요한 패키지를 쉽게 설치하고 관리할 수 있게 해준다.

개발자는 누구나 자신이 개발한 파이썬 패키지를 PyPI에 업로드하여 공유할 수 있고, 다른 개발자가 재사용할 수 있다. 구글링을 조금만 해보면 원하는 기능을 수행하는 패키지를 쉽게 찾을 수 있고, 찾은 패키지는 `pip` 명령어를 통해 손쉽게 설치할 수 있다. 같은 패키지의 다양한 버전을 관리할 수 있고, 필요에 따라 특정 버전을 설치할 수 있다. 많은 파이썬 패키지들은 다른 패키지에 의존하는데, PyPI와 `pip`은 패키지를 설치할 때 이러한 의존성을 알아서 해결해 준다.

패키지 개발자는 패키지를 준비하고, `setup.py` 파일을 작성한 뒤, `twine`과 같은 도구를 사용하여 패키지를 PyPI에 업로드하는 간단한 절차만으로 배포가 가능하다. 본 아티클을 통해 PyPI에 패키지를 배포하는 방법을 알아볼 것이다. 예제로 필자가 개발한 `holidayskr` 패키지를 PyPI에 배포하는 과정을 설명한다.

## 1.2 배포 배경

한국에는 다양한 공휴일이 존재한다. 양력으로 고정된 공휴일 뿐 아니라, 설날 혹은 추석과 같은 음력 공휴일, 그리고 매년 다르게 설정되는 대체 공휴일이나 선거일 등의 공휴일이 있다. 사내에서 근무일에만 동작해야하는 파이썬(Python) 스크립트를 작성했는데, 기존에 존재하는 한국 공휴일 라이브러리는 대체 공휴일을 반영하지 않았다. 또한 음력 변환을 중국 기반으로 진행해 한국에서 정확도가 떨어지는 문제가 있었다. 이를 해결하기 위해 공공 데이터 API를 사용하기도 했지만, 안정적이지 못한 증상이 있었다. 어느 정도 수동으로 기입하는 방식으로 셋업이 불가피하다고 느꼈고, 이를 라이브러리로 배포해서 필자 뿐 아니라 모든 개발자가 사용할 수 있도록 하면 어떨까 생각했다.

아래와 같이 간단하지만 정확한 기능을 제공할 수 있도록 파이썬 스크립트를 작성했다:

```py
import requests
from datetime import datetime, timedelta
from korean_lunar_calendar import KoreanLunarCalendar


def download_holiday_data(url, retries=50):
    """GitHub에서 공휴일 데이터를 다운로드합니다. 실패 시 재시도."""
    for attempt in range(retries):
        try:
            response = requests.get(url)
            response.raise_for_status()
            return response.json()  # 성공 시 JSON 데이터 반환
        except Exception as e:
            print(f"Request error occurred: {e}, retrying {attempt + 1}/{retries}")
    raise Exception("Reached maximum retry attempts. Data download failed.")


# GitHub에서 공휴일 데이터를 다운로드
URL = "https://raw.githubusercontent.com/6mini/holidayskr/main/holidayskr.json"
HOLIDAY_DATA = download_holiday_data(URL)


def convert_lunar_to_solar(year, month, day, adjust=0):
    """음력 날짜를 양력 날짜로 변환합니다."""
    calendar = KoreanLunarCalendar()
    calendar.setLunarDate(year, int(month), int(day), False)
    solar_date = datetime.strptime(calendar.SolarIsoFormat(), '%Y-%m-%d').date()
    return solar_date + timedelta(days=adjust)


def get_holidays(year):
    """해당 연도의 모든 공휴일을 가져옵니다 (양력 고정, 음력 고정, 연도별 특정)."""
    # 양력 고정 공휴일
    fixed_holidays = [
        (datetime.strptime(f"{year}-{holiday['date']}", '%Y-%m-%d').date(), holiday['name'])
        for holiday in HOLIDAY_DATA['solar_holidays']
    ]
    
    # 음력 고정 공휴일을 양력으로 변환
    lunar_holidays = []
    for holiday in HOLIDAY_DATA['lunar_holidays']:
        month, day = holiday['date'].split('-')
        solar_date = convert_lunar_to_solar(year, month, day)
        lunar_holidays.append((solar_date, holiday['name']))
        if month in ['01', '08']:  # 설날과 추석은 전날, 다음날도 공휴일 처리
            lunar_holidays.append((solar_date - timedelta(days=1), holiday['name'] + " 전날"))
            lunar_holidays.append((solar_date + timedelta(days=1), holiday['name'] + " 다음날"))
    
    # 연도별 특정 공휴일
    specific_holidays = [
        (datetime.strptime(f"{year}-{holiday['date']}", '%Y-%m-%d').date(), holiday['name'])
        for holiday in HOLIDAY_DATA['year_specific_holidays'].get(str(year), [])
    ]
    
    # 모든 공휴일을 날짜 기준으로 정렬
    all_holidays = sorted(fixed_holidays + lunar_holidays + specific_holidays, key=lambda x: x[0])
    
    return all_holidays


def is_holiday(date_str):
    """지정된 날짜가 공휴일인지 확인합니다."""
    try:
        date = datetime.strptime(date_str, '%Y-%m-%d').date()
    except ValueError:
        raise ValueError("Invalid date format. Use 'YYYY-MM-DD'.")

    year = date.year
    all_holidays = get_holidays(year)
    
    return any(holiday[0] == date for holiday in all_holidays)


def today_is_holiday():
    """현재 날짜가 공휴일인지 확인합니다."""
    kst_now = datetime.utcnow() + timedelta(hours=9)
    date_str = kst_now.strftime('%Y-%m-%d')
    return is_holiday(date_str)


def year_holidays(year_str):
    """지정된 연도의 모든 공휴일을 반환합니다."""
    try:
        year = int(year_str)
    except ValueError:
        raise ValueError("Invalid year format. Use 'YYYY'.")

    return get_holidays(year)
```

깃허브에서 다운받을 공휴일 JSON 데이터는 다음과 같다:

```json
{
    "solar_holidays": [
        {"date": "01-01", "name": "신정"},
        {"date": "03-01", "name": "3·1절"},
        {"date": "05-01", "name": "근로자의 날"},
        {"date": "05-05", "name": "어린이날"},
        {"date": "06-06", "name": "현충일"},
        {"date": "08-15", "name": "광복절"},
        {"date": "10-03", "name": "개천절"},
        {"date": "10-09", "name": "한글날"},
        {"date": "12-25", "name": "크리스마스"}
    ],
    "lunar_holidays": [
        {"date": "01-01", "name": "설날"},
        {"date": "04-08", "name": "석가탄신일"},
        {"date": "08-15", "name": "추석"}
    ],
    "year_specific_holidays": {
        "2024": [
            {"date": "02-12", "name": "대체 공휴일(설날)"},
            {"date": "04-10", "name": "제22대 국회의원 선거일"},
            {"date": "05-06", "name": "대체 공휴일(어린이날)"}
        ],
        "2025": [
            {"date": "03-03", "name": "대체 공휴일(3·1절)"},
            {"date": "05-06", "name": "대체 공휴일(어린이날, 석가탄신일 중복 공휴일)"}
        ],
        "2026": [
            {"date": "03-02", "name": "대체 공휴일(3·1절)"},
            {"date": "05-25", "name": "대체 공휴일(석가탄신일)"},
            {"date": "06-08", "name": "대체 공휴일(현충일)"},
            {"date": "08-17", "name": "대체 공휴일(광복절)"},
            {"date": "10-05", "name": "대체 공휴일(한글날)"}
        ]
    }
}
```

매년 추가되는 공휴일의 경우 JSON 파일만 수정해주면 된다. 간단하게 주요 기능을 설명하자면 다음과 같다:

1. **GitHub에서 공휴일 데이터 다운로드**: `download_holiday_data` 함수는 레포지토리 URL에서 공휴일 데이터를 JSON 형식으로 다운로드한다. 네트워크 요청 실패 시 재시도하는 로직을 포함시켰다.
2. **음력 날짜를 양력으로 변환**: `convert_lunar_to_solar` 함수는 음력 날짜를 양력 날짜로 변환한다. 이 변환은 `KoreanLunarCalendar`라는 한국식 음력 변환기 라이브러리를 사용하여 수행한다.
3. **해당 연도의 모든 공휴일 조회**: `get_holidays` 함수는 양력 고정 공휴일, 음력 고정 공휴일을 양력으로 변환한 날짜, 그리고 연도별 특정 공휴일을 포함하여 해당 연도의 모든 공휴일을 조회한다.
4. **특정 날짜가 공휴일인지 확인**: `is_holiday` 함수는 주어진 날짜가 공휴일인지를 확인한다. 해당 연도의 모든 공휴일 정보를 조회한 후 주어진 날짜와 비교하는 방식이다.
5. **현재 날짜가 공휴일인지 확인**: `today_is_holiday` 함수는 현재 날짜를 기준으로 공휴일 여부를 확인한다. 이 함수는 UTC 시간에 9시간(한국 표준시)을 더해 현재 한국 날짜를 계산하고, 이를 `is_holiday` 함수에 전달하여 확인한다.
6. **지정된 연도의 모든 공휴일 반환**: `year_holidays` 함수는 입력받은 연도에 해당하는 모든 공휴일을 반환한다. `get_holidays` 함수를 사용하여 해당 연도의 공휴일 정보를 조회하고 반환한다.

## 1.3 배포 목적

제일 먼저, 여러 프로젝트에서 사용될 코드이기 때문에 코드 스니펫을 복붙하기가 귀찮았다. 또 열심히 만든 코드인데 필자만 사용하기 아까웠고, 이 프로그램은 많은 파이썬 개발자들이 요긴히 사용할 수 있을거라 생각했기 때문에 PyPI에 배포하고자 했다. 오픈 소스 커뮤니티에 기여함으로써 다른 개발자들이 이 것을 기반으로 새로운 프로젝트를 개발하거나 기존 프로젝트를 개선할 수 있도록 하고 싶은 마음도 있고, 다른 개발자로부터의 피드백을 바탕으로 패키지의 기능을 지속적으로 개선하고, 발견된 버그를 수정하여 사용자 경험을 향상시키고 싶었다. 마지막으로, 매년 변동되는 공휴일의 경우 JSON 파일에 추가해줘야하기 때문에, 필자 말고도 여러명이 사용한다면, 더욱 높은 정확성을 기대할 수 있을 것이라 생각했다.

# 2. 배포 절차

이제 실질적으로 위에서 만든 함수 코드를 배포해보자.

## 2.1 PyPI 계정 생성

먼저 PyPI에 패키지를 배포하기 위해서는 계정이 필요하다. PyPI 계정을 생성하기 위해 [PyPI 웹사이트](https://PyPI.org/)에 접속한다.

![PyPI 회원가입](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/python-package-deployment-pypi-github-actions-1.png)

"Register" 링크를 클릭한 뒤, 비교적 간단한 값의 입력만으로 계정을 생성할 수 있다. 이 후 이메일 인증과 MFA 인증 절차를 거쳐야하는데 쉬우므로 여기선 넘어가도록 한다.

## 2.2 PyPI에 등록하고자 하는 패키지명 검색

PyPI에 등록하고자 하는 패키지명이 이미 존재하는지 확인해야 한다. 이를 위해 PyPI 웹사이트에서 사용하고 싶은 패키지명을 검색해본다.

![PyPI에 등록하고자 하는 패키지명 검색](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/python-package-deployment-pypi-github-actions-3.png)

필자의 경우 원래 원하는 네이밍이 있지만, 이미 존재하는 패키지명이어서 `holidayskr`이라는 패키지명을 선택했다.

## 2.3 깃허브 레포지토리(GitHub Repository) 생성

다음으로 배포할 패키지의 소스 코드를 저장할 깃허브 레포지토리를 생성한다. 필자는 네이밍 깔맞춤을 굉장히 좋아하므로 `holidayskr`이라는 이름으로 레포지토리를 생성했다.

![깃허브 레포지토리(GitHub Repository) 생성 절차](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/python-package-deployment-pypi-github-actions-2.png)

깃허브의 "+" 버튼을 클릭한 후, "New Repository" 메뉴를 선택한다. 레포지토리 이름과 설명을 적절히 입력한다. "Public"으로 설정해야하며, 편하게 "Initialize this repository with:" 탭에서 "Add a README file" 옵션을 선택한다. 여기서 꿀팁은 "Add .gitignore" 템플릿으로 "Python"을 설정해주고, "Choose a license" 옵션에 "MIT License"를 선택해주면 나중에 추가 설정이 필요없다.

🤷🏻‍♂️: 왜 "Add .gitignore" 템플릿으로 "Python"을 선택했나요?

> 파이썬 프로젝트에서 자주 발생하는 불필요한 파일(예: `__pycache__`, `.pyc` 파일 등)과 민감한 설정(예: `.env`)을 자동으로 무시하기 위함이다. 프로젝트를 깨끗하게 유지하고, 실수로 중요한 정보를 공개 레포지토리에 업로드하는 것을 방지한다. 가상 환경 폴더와 개발 도구 설정 파일도 포함되어, 자신의 환경에 맞게 작업할 수 있도록 한다.

🤷🏻‍♂️: 왜 "MIT License"를 선택했나요?

> MIT License는 사용, 수정, 배포의 자유를 제공하는 간결하고 유연한 오픈 소스 라이센스이다. 사용자에게 쉽게 이해되는 조건 하에 소프트웨어를 자유롭게 활용할 수 있도록 하며, 상업적 사용도 명시적으로 허용한다. 이로 인해 소프트웨어의 광범위한 채택과 개발자 간의 협업이 촉진되며, 다른 라이선스와의 호환성도 뛰어나 오픈 소스 커뮤니티에서의 재사용성을 높인다.

## 2.4 패키지 구조 설정

깃허브 레포지토리를 생성했다면, 해당 레포지토리를 클론하여 로컬 환경에 가져온다. 이후, 패키지를 배포하기 전, 적절한 디렉토리 구조를 설정해야 한다. 예를 들어 `holidayskr` 패키지의 경우 다음과 같은 구조가 될 것이다:

```markdown
holidayskr/
├── holidayskr/
│   ├── __init__.py
│   └── core.py
├── tests/
│   ├── __init__.py
│   └── test_core.py
├── setup.py
├── README.md
├── requirements.txt
└── LICENSE
```

위와 같은 구조를 설정한 후, 이제 천천히 각 파일에 들어갈 기본적인 스크립트를 작성해보자.

### 2.4.1 `holidayskr/core.py` 작성

이 파일에는 위에서 작성한 휴일 계산 로직이 그대로 들어간다. 패키지의 실질적인 기능이 들어가는 파일이다. 개발한 코드를 그대로 복붙한다.

### 2.4.2 `holidayskr/__init__.py` 작성

이 파일은 패키지를 초기화하고, 필요한 경우 패키지 사용자가 직접 접근할 함수나 클래스를 불러오는 역할을 한다. `core.py`에서 정의할 주요 함수(위에서 작성한 코드의 주요 함수)를 불러온다:

```py
from .core import is_holiday, today_is_holiday, year_holidays
```

필자의 경우 라이브러리에서 사용할 `is_holiday`, `today_is_holiday`, `year_holidays` 함수를 불러왔다.

### 2.4.3 `tests/test_core.py` 작성

`test_core.py` 파일에는 `core.py`의 함수들을 테스트하기 위한 코드가 들어간다. `pytest`를 사용할 것이며 테스트 코드 예시는 다음과 같다:

```py
import pytest
from datetime import datetime
from holidayskr.core import download_holiday_data, convert_lunar_to_solar, get_holidays, is_holiday, today_is_holiday, year_holidays


# 1. 공휴일 데이터 다운로드 테스트
def test_download_holiday_data():
    url = "https://raw.githubusercontent.com/6mini/holidayskr/main/holidayskr.json"
    data = download_holiday_data(url)
    assert data is not None, "다운로드 받은 데이터가 없습니다."
    assert 'solar_holidays' in data, "'solar_holidays' 키가 데이터에 없습니다."
    assert 'lunar_holidays' in data, "'lunar_holidays' 키가 데이터에 없습니다."
    assert 'year_specific_holidays' in data, "'year_specific_holidays' 키가 데이터에 없습니다."


# 2. 음력에서 양력으로의 변환 테스트
@pytest.mark.parametrize("year, month, day, expected", [
    (2024, 1, 1, "2024-02-10"),
    (2025, 1, 1, "2025-01-29"),
    (2026, 1, 1, "2026-02-17"),
])
def test_convert_lunar_to_solar(year, month, day, expected):
    result = convert_lunar_to_solar(year, month, day)
    assert result.strftime('%Y-%m-%d') == expected, f"예상 결과와 다릅니다: {expected}, 받은 결과: {result}"


# 3. 특정 날짜가 공휴일인지 확인하는 테스트
@pytest.mark.parametrize("date_str, expected", [
    ("2024-01-01", True),  # 신정
    ("2024-02-10", True),  # 설날
    ("2024-05-01", True),  # 근로자의 날
    ("2024-12-25", True),  # 크리스마스
    ("2024-06-06", True),  # 현충일
    ("2024-04-10", True),  # 22대 국회의원선거
    ("2024-04-22", False)  # 공휴일이 아닌 날
])
def test_is_holiday(date_str, expected):
    assert is_holiday(date_str) == expected, f"{date_str}의 공휴일 여부가 예상과 다릅니다."


# 4. 연도별 공휴일 리스트 테스트
def test_year_holidays():
    year = "2024"
    holidays = year_holidays(year)
    assert holidays is not None, "반환된 공휴일 리스트가 없습니다."
    assert len(holidays) > 0, "공휴일 리스트가 비어있습니다."


# 5. 현재 날짜가 공휴일인지 확인하는 테스트
def test_today_is_holiday():
    # 이 테스트는 현재 날짜에 따라 결과가 달라질 수 있으므로, 실제 사용 시에는 적절히 수정이 필요합니다.
    # 예를 들어, 실제 공휴일 날짜를 설정하여 테스트하거나, 테스트 환경에서 날짜를 조작하는 방법을 고려할 수 있습니다.
    is_holiday = today_is_holiday()
    assert isinstance(is_holiday, bool), "반환된 값이 bool 타입이 아닙니다."


# 6. 연도별 특정 공휴일 테스트
@pytest.mark.parametrize("year, date, expected_name", [
    (2024, "2024-02-12", "대체 공휴일(설날)"),
    (2024, "2024-04-10", "제22대 국회의원 선거일"),
    (2025, "2025-03-03", "대체 공휴일(3·1절)"),
    (2026, "2026-05-25", "대체 공휴일(석가탄신일)"),
])
def test_year_specific_holidays(year, date, expected_name):
    holidays = year_holidays(str(year))
    assert any(holiday for holiday in holidays if holiday[0].strftime('%Y-%m-%d') == date and holiday[1] == expected_name), \
        f"{year}년에 {date}({expected_name})가 공휴일 리스트에 없습니다."


# 7. 음력 공휴일의 양력 변환과 연속된 날짜 테스트
@pytest.mark.parametrize("year, lunar_date, expected_dates", [
    (2024, "01-01", ["2024-02-09", "2024-02-10", "2024-02-11"]),  # 설날과 전날, 다음날
    (2024, "08-15", ["2024-09-16", "2024-09-17", "2024-09-18"]),  # 추석과 전날, 다음날
])
def test_lunar_holidays_with_surrounding_days(year, lunar_date, expected_dates):
    holidays = year_holidays(str(year))
    for expected_date in expected_dates:
        assert any(holiday for holiday in holidays if holiday[0].strftime('%Y-%m-%d') == expected_date), \
            f"{year}년 {lunar_date}의 변환된 날짜 {expected_date}가 공휴일 리스트에 없습니다."


# 8. 잘못된 입력에 대한 예외 처리 테스트
def test_invalid_date_format():
    with pytest.raises(ValueError):
        is_holiday("2024-02-30")  # 잘못된 날짜 형식

def test_invalid_year_format():
    with pytest.raises(ValueError):
        year_holidays("20XX")  # 잘못된 연도 형식


# 9. 비공휴일 확인 테스트
@pytest.mark.parametrize("date_str", [
    "2024-01-02",  # 신정 다음 날
    "2024-07-01",  # 중간의 평일
])
def test_not_a_holiday(date_str):
    assert not is_holiday(date_str), f"{date_str}는 공휴일이 아닙니다."
```

위 코드는 `holiydayskr`에서 사용될 함수가 정확한 결과를 반환하는지 확인하기 위한 테스트 코드이다. 참고로 `tests/__init__.py` 파일은 비어있어도 된다.

#### 2.4.3.1 테스트 방법

그럼 테스트를 한 번 진행해보자.

##### 2.4.3.1.1 테스트 환경 설정

테스트를 실행하기 전에 `pytest`가 설치되어 있어야 한다. 아직 설치되지 않았다면, 다음 명령어로 설치한다:

```sh
$ pip install pytest
```

##### 2.4.3.1.2 테스트 실행

터미널을 열고 `tests` 디렉토리가 위치한 상위 디렉토리로 이동한다. 다음 명령어를 실행하여 `pytest`를 통해 모든 테스트를 실행한다:

```sh
$ pytest
```

```markdown
=============================================== test session starts ================================================
platform darwin -- Python 3.10.13, pytest-8.0.1, pluggy-1.4.0
rootdir: /Users/6mini/holidayskr
collected 23 items                                                                                                 

tests/test_core.py .......................                                                                   [100%]

================================================ 23 passed in 1.26s ================================================
```

`pytest`는 `tests` 디렉토리 내의 모든 파일을 자동으로 찾아, `test_`로 시작하는 함수를 테스트 케이스로 실행한다. 테스트가 성공하면, 각 테스트 케이스별로 성공 여부를 보여준다. 실패한 테스트가 있다면, 실패한 원인과 함께 상세 정보를 출력한다.

### 2.4.4 `requirements.txt` 작성

`requirements.txt` 파일은 프로젝트가 의존하는 외부 파이썬 패키지들을 명시하는 파일이다. 이 파일에 명시된 패키지들은 프로젝트 실행 혹은 개발을 위해 필수적으로 설치되어야 하는 패키지들이다. `holidayskr` 패키지의 경우, `requests`, `korean_lunar_calendar` 등의 패키지가 필요하므로 다음과 같이 작성한다:

```txt
korean_lunar_calendar>=0.2.1
requests>=2.0.0
```

`pytest`는 개발 의존성으로 간주될 수 있기 때문에, 일반적으로 `requirements.txt`에 포함시키지 않고, 대신 `dev-requirements.txt` 파일을 따로 만들어 관리할 수 있다. 이런 경우, `dev-requirements.txt`에 다음과 같이 작성할 수 있다:

```txt
pytest>=6.0.0
```

이렇게 분리하면, 개발 환경과 프로덕션 환경의 의존성을 명확히 구분할 수 있어 관리가 용이해진다. 

### 2.4.5 `setup.py` 작성

`setup.py` 파일은 패키지의 메타데이터와 의존성 정보를 담고 있다. 예를 들어:

```py
from setuptools import setup, find_packages

setup(
    name='your_package_name',
    version='0.0.0',
    description='Your package description',
    long_description=open('README.md').read(),
    long_description_content_type='text/markdown',
    author='Your Name',
    author_email='your.email@example.com',
    url='https://github.com/yourusername/yourpackage',
    packages=find_packages(),
    install_requires=[
        'package1>=0.0.0',
    ],
    classifiers=[
        'Programming Language :: Python :: 3',
        'License :: OSI Approved :: MIT License',
        'Operating System :: OS Independent',
    ],
    python_requires='>=3.6',
)
```

위와 같은 양식으로 작성하면 되고, 필자의 경우 `holidaykr` 패키지에 다음과 같이 작성했다:

```py
from setuptools import setup, find_packages

setup(
    name='holidayskr',
    version='0.1.0',
    author='Yoonmin Lee',
    author_email='real6mini@gmail.com',
    packages=find_packages(),
    description='대한민국의 공휴일을 계산하는 Python 패키지입니다. 양음력 고휴일 뿐 아니라, 매년 변동되는 공휴일(대체 공휴일, 선거일 등)까지 포함하여 정확한 공휴일 정보를 제공합니다. 금일 혹은 특정 날짜가 공휴일인지 확인하거나, 주어진 연도의 모든 공휴일을 조회할 수 있습니다.',
    long_description=open('README.md').read(),
    long_description_content_type='text/markdown',
    url='https://github.com/6mini/holidayskr',
    license='MIT',
    install_requires=[
        'korean_lunar_calendar>=0.2.1',
        'requests>=2.0.0',
    ],
    extras_require={
        'dev': [
            'pytest>=6.0.0',
            'check-manifest',
            'twine',
        ],
    },
    classifiers=[
        "Programming Language :: Python :: 3",
        "Programming Language :: Python :: 3.6",
        "Programming Language :: Python :: 3.7",
        "Programming Language :: Python :: 3.8",
        "Programming Language :: Python :: 3.9",
        "Programming Language :: Python :: 3.10",
        "License :: OSI Approved :: MIT License",
        'Operating System :: OS Independent',
        "Development Status :: 4 - Beta",
        "Intended Audience :: Developers",
        "Topic :: Software Development :: Libraries :: Python Modules",
        "Topic :: Office/Business :: Scheduling",
    ],
    keywords='Korea holidays lunar-calendar public-holidays',
    python_requires='>=3.6',
)
```

### 2.4.6 `README.md` 작성

`README.md` 파일은 패키지를 이해하는 데 도움이 될 문서이다. 필자의 경우 프로젝트에 대한 간단한 설명과 사용방법, 기여 방법, 라이선스, 연락처 등을 포함하여 작성했다. 여기서 작성하는 리드미 문서는 깃허브 뿐 아니라 PyPI의 "Project description"에도 표시된다. 궁금하다면 가서 보자: https://pypi.org/project/holidayskr/

### 2.4.7 `LICENSE` 작성

`LICENSE` 파일은 패키지의 라이선스 정보를 담고 있다. 필자의 경우 MIT 라이선스를 사용하려 했고, 깃허브 레포지토리를 생성할 때 선택했으므로 자동으로 [MIT 라이선스 텍스트](https://opensource.org/licenses/MIT)가 작성되어 있다.

## 2.5 배포

이제 준비는 끝났다. 배포해보자.

### 2.5.1 준비 작업

패키지를 빌드하기 위해 필요한 `setuptools`와 `wheel` 패키지를 설치한다:

```sh
$ pip install setuptools wheel
```

패키지를 PyPI에 업로드하기 위해 필요한 `twine` 패키지를 설치한다:

```sh
$ pip install twine
```

### 2.5.2 패키지 빌드

프로젝트 루트 디렉토리에서 다음 명령을 실행하여 소스 배포와 `wheel` 배포를 생성한다.

```sh
$ python setup.py sdist bdist_wheel
```

이 명령은 `dist/` 디렉토리에 패키지 파일을 생성한다.

### 2.5.3 PyPI에 배포

생성된 패키지를 PyPI에 업로드한다.

```sh
$ twine upload dist/*
```

첫 업로드 시 PyPI 사용자 이름과 비밀번호를 입력하라는 메시지가 표시된다.(현재는 API 토큰을 사용하는 것을 권장하고 있다. API 토큰을 사용하는 방법은 쉬우므로 생략한다.)

![배포가 완료된 holidayskr 패키지](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/python-package-deployment-pypi-github-actions-4.png)

업로드가 성공하면, 패키지가 PyPI에 등록되고, 이제 `pip install` 명령으로 패키지를 설치할 수 있다. 이제 모든 파이썬 개발자들이 내가 배포한 패키지를 사용할 수 있게 되었다.

# 3. GitHub Actions를 통한 배포 자동화

배포는 끝났지만, 최고의 사용성을 위해선 꾸준히 패키지를 유지보수해줘야 한다. 이를 위해선 새로운 기능을 추가하거나 버그를 수정할 때마다 패키지를 업데이트하고, PyPI에 재배포해야 한다. 이러한 작업을 자동화하기 위해 GitHub Actions를 사용할 수 있다.

이 자동화 구현은 [시멘틱 버저닝(Semantic Versioning)](https://semver.org/)(1.0.0, 1.1.0 등…)을 통한 태그 번호 기반 자동 버전 관리 릴리스에 중점을 둘 것이다. 깃허브에 푸시(push)한 태그 번호에 따라 PyPI에 릴리즈를 만들 것이다.

## 3.1 Workflows File 작성

먼저 새로운 파일을 작성해야한다:

```markdown
holidayskr/
├── .github
│   └── workflows
│       └── publish.yml
├── holidayskr/
│   ├── __init__.py
│   └── core.py
├── tests/
│   ├── __init__.py
│   └── test_core.py
├── setup.py
├── README.md
├── requirements.txt
└── LICENSE
```

위와 같은 구조와 같이 추가로 `.github/workflows` 경로에 `publish.yml` 파일을 생성하여 작성한다:

```yml
name: Publish Python distributions to PyPI
 
on:
  push:
    tags:
      - '*'

jobs:
  pypi-publish:
    name: upload release to PyPI
    runs-on: ubuntu-latest
    environment: release
    permissions:
      id-token: write
    steps:
      - uses: actions/checkout@v3
      - name: Set up Python
        uses: actions/setup-python@v3
        with:
          python-version: '3.x'

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          python -m pip install setuptools wheel twine

      - name: Extract tag name
        id: tag
        run: echo ::set-output name=TAG_NAME::${GITHUB_REF#refs/tags/}

      - name: Update version in setup.py
        run: |
          VERSION=${{ steps.tag.outputs.TAG_NAME }}
          VERSION=${VERSION#v}
          sed -i "s/{{VERSION_PLACEHOLDER}}/$VERSION/g" setup.py

      - name: Build and publish
        run: |
          python setup.py sdist bdist_wheel

      - name: Publish package distributions to PyPI
        uses: pypa/gh-action-pypi-publish@release/v1
```

위 코드는 태그가 `v1.0.0`과 같은 형태로 푸시되면 `1.0.0`을 추출하여 `setup.py` 파일의 버전을 업데이트하고, 패키지를 빌드한 뒤 PyPI에 업로드한다.

## 3.2 `setup.py` 파일 수정

```py
from setuptools import setup, find_packages

setup(
    name='holidayskr',
    version='{{VERSION_PLACEHOLDER}}',
    author='Yoonmin Lee',
    author_email='real6mini@gmail.com',
    packages=find_packages(),
    ...
```

`tags`에서 추출한 버전을 패키지를 배포할 때 넣어줘야 하기 때문에 `version`을 위와 같은 스크립트로 수정한다.

## 3.3 PyPI 패키지에 퍼블리셔(Publisher) 추가

이제 퍼블리셔(Publisher)를 패키지에 추가만 하면 된다. ["Your account"의 "Publishing"](https://pypi.org/manage/account/publishing/) 탭에서 "Add a new pending publisher" 항목을 찾는다.

![PyPI 패키지에 퍼블리셔(Publisher) 추가](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/python-package-deployment-pypi-github-actions-5.png)

위 이미지와 같이 각 항목을 적절한 값으로 채워넣고 "Add" 버튼을 통해 추가한다.

![PyPI 패키지에 퍼블리셔(Publisher) 확인](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/python-package-deployment-pypi-github-actions-6.png)

패키지에 등록된 퍼블리셔를 확인할 수 있다.

## 3.4 배포

이제 새로운 태그를 푸시하면, GitHub Actions가 자동으로 배포를 진행할 것이다.

```py
$ git add .
$ git commit -m "버전 1.0.1 배포"
$ git tag v1.0.0
$ git push origin --tags
```

이후 깃허브 작업이 실행되는 것을 볼 수 있으며, 정상일 경우 패키지가 PyPI에 배포된다.

![GitHub Actions 작업 실행 내역](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/python-package-deployment-pypi-github-actions-7.png)

레포지토리(Repository)의 액션(Actions) 탭에서 작업의 실행을 볼 수 있다.

# 4. 마무리 및 참고

파이썬 패키지를 개발하고 PyPI에 배포하는 전체 과정을 알아보았다. 또, GitHub Actions를 활용한 배포 자동화 방법까지 알아보았다. 이제 전 세계 파이썬 개발자들과 공유할 수 있게 되었다. 패키지 배포는 단순히 코드 공유를 넘어, 개발자로서의 가시성을 높이고, 기술적 역량을 증명하는 하나의 방법이 될 수 있다고 생각한다. 또, 프로젝트 관리 및 협업, 버전 관리, 문서화 등의 중요한 개발 스킬을 연습할 수 있다고 생각한다.

## 4.1 프로젝트 바로가기

- [holidayskr Repository](https://github.com/6mini/holidayskr)
- [holidayskr PyPI](https://pypi.org/project/holidayskr/)

## 4.2 참고

- [Python Packaging User Guide](https://packaging.python.org/)
- [Semantic Versioning 2.0.0](https://semver.org/)
- [Automate PyPi releases with Github Actions](https://medium.com/@VersuS_/automate-pypi-releases-with-github-actions-4c5a9cfe947d)

## 4.3 관련 아티클

- [파이썬(Python)으로 오늘이나 특정 날짜가 평일(영업일, 근무일)인지 휴일(주말, 공휴일)인지 확인하는 방법(Feat. holidayskr)](/python-check-business-day-holidayskr)
- [파이썬(Python)으로 한국의 휴일(공휴일)을 확인하는 최신 방법(Feat. holidayskr)](/python-holidayskr-korean-holiday-check-guide)