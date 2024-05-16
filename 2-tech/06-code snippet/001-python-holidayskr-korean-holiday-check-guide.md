---
title: '파이썬(Python)으로 한국의 휴일(공휴일)을 확인하는 최신 방법(Feat. holidayskr)'
description: "파이썬(Python)을 사용해 한국의 휴일(공휴일)을 확인하는 방법을 소개한다. holidayskr 라이브러리의 설치부터 특정 날짜나 오늘 날짜가 공휴일인지 확인하는 방법, 그리고 지정한 연도의 모든 공휴일을 조회하는 방법까지 자세히 설명한다. 또한, 이 라이브러리가 기존 문제점을 어떻게 해결했는지에 대해서도 알아보고, 사용자들이 직접 업데이트에 기여할 수 있는 방법을 안내한다. 파이썬으로 한국의 공휴일 정보를 효율적으로 관리하고 싶은 개발자들에게 유용한 리소스이다."
date: '2024-03-12'
tags: [파이썬(Python), 한국 휴일(공휴일), holidayskr, 라이브러리(Library), 패키지(Package), PyPI, 공휴일 API]
---

# 1. 서론

파이썬(Python) 개발을 하다보면 평일과 주말, 휴일(공휴일)을 구분하여 처리해야 할 때가 종종 발생한다. 이때, 파이썬 라이브러리를 사용하여 간단하게 휴일을 확인할 수 있다. 이번 아티클에서는 [holidayskr](https://pypi.org/project/holidayskr/) 라이브러리를 사용하여 한국의 휴일을 확인하는 방법에 대해 알아볼 것이다. 사실 `holidayskr` 라이브러리는 필자가 개발했다. 기존 존재하는 라이브러리의 경우, 선거일이나 대체휴무일을 제대로 반영하지 않았으며, 음력 기반 휴일(설날, 추석, 석가탄신일 등) 계산이 미흡했다. 따라서, 필자는 이러한 문제점을 보완하고자 `holidayskr` 라이브러리를 개발했다.

# 2. `holidayskr` 라이브러리 설치

![holidayskr 패키지(PyPI)](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/python-holidayskr-korean-holiday-check-guide.png)

`holidayskr` 라이브러리는 파이썬의 패키지 관리자인 `pip`를 사용하여 설치할 수 있다. 아래의 명령어를 통해 `holidayskr` 라이브러리를 설치한다:

```sh
$ pip install holidayskr
```

# 3. `holidayskr` 라이브러리 사용법

## 3.1 특정 날짜가 공휴일인지 확인하기

`holidayskr` 라이브러리를 사용하여 특정 날짜가 공휴일인지 확인하는 방법은 다음과 같다:

```py
from holidayskr import is_holiday

# 어린이날 확인하기
if is_holiday('2024-05-05'):
    print("2024년 5월 5일은 공휴일입니다.")
else:
    print("2024년 5월 5일은 공휴일이 아닙니다.")
```

```sh
2024년 5월 5일은 공휴일입니다.
```

## 3.2 오늘 날짜가 공휴일인지 확인하기

`holidayskr` 라이브러리를 사용하여 오늘 날짜가 공휴일인지 확인하는 방법은 다음과 같다:

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

## 3.3 지정한 연도의 모든 공휴일 조회하기

`holidayskr` 라이브러리를 사용하여 지정한 연도의 모든 공휴일을 조회하는 방법은 다음과 같다:

```py
from holidayskr import year_holidays

# 2024년의 모든 공휴일 조회
holidays = year_holidays('2024')
for holiday, holiday_name in holidays:
    print(f"{holiday.strftime('%Y년 %m월 %d일')}은 {holiday_name}입니다.")
```

```sh
2024년 01월 01일은 신정입니다.
2024년 02월 09일은 설날 전날입니다.
2024년 02월 10일은 설날입니다.
2024년 02월 11일은 설날 다음날입니다.
2024년 02월 12일은 대체 공휴일(설날)입니다.
2024년 03월 01일은 3·1절입니다.
2024년 04월 10일은 제22대 국회의원 선거일입니다.
2024년 05월 01일은 근로자의 날입니다.
2024년 05월 05일은 어린이날입니다.
2024년 05월 06일은 대체 공휴일(어린이날)입니다.
2024년 05월 15일은 석가탄신일입니다.
2024년 06월 06일은 현충일입니다.
2024년 08월 15일은 광복절입니다.
2024년 09월 16일은 추석 전날입니다.
2024년 09월 17일은 추석입니다.
2024년 09월 18일은 추석 다음날입니다.
2024년 10월 03일은 개천절입니다.
2024년 10월 09일은 한글날입니다.
2024년 12월 25일은 크리스마스입니다.
```

# 4. 마무리 및 참고

한국의 공휴일은 변동될 수 있으며, 새로운 공휴일이 생길 수도 있다. 웬만한 휴일 정보는 미리 세팅을 해뒀고, 앞으로도 필자가 직접 업데이트를 할 예정이지만, 혹시나 필자가 놓친 휴일의 경우 [holidayskr 레포지토리](https://github.com/6mini/holidayskr)의 `holidayskr.json` 문서를 수정한 후 Pull Request(PR)를 해주시면 반영하도록 할 것이다.

## 4.1 프로젝트 링크

- [holidayskr 깃허브(GitHub) 레포지토리(Repository)](https://github.com/6mini/holidayskr)
- [holidayskr 패키지(PyPI)](https://pypi.org/project/holidayskr/)

## 4.2 관련 아티클

- [파이썬(Python)으로 오늘이나 특정 날짜가 평일(영업일, 근무일)인지 휴일(주말, 공휴일)인지 확인하는 방법(Feat. holidayskr)](/python-check-business-day-holidayskr)
- [파이썬 패키지를 PyPI(Python Package Index)에 배포하는 방법부터 GitHub Actions를 통한 자동화까지](/python-package-deployment-pypi-github-actions)