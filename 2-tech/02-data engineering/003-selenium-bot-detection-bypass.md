---
title: '셀레니움(Selenium) 웹 크롤링 봇 탐지 우회 방법 4단계: 옵션, 쿠키, VPN, 크롬 디버깅 모드'
description: "셀레니움(Selenium)을 사용하여 웹 크롤링 시 봇 탐지를 우회하는 다양한 전략과 방법을 단계별로 소개한다. 크롬 옵션 조정부터 쿠키 관리, VPN 사용, 크롬 디버깅 모드 활용까지, 단계별로 심화되는 기술을 통해 고도화된 봇 탐지 시스템을 극복하는 방법을 배울 수 있다."
date: '2024-02-15'
tags: [크롤링(Crawling), 스크래핑(Scraping), 봇 탐지 우회, 셀레니움(Selenium), 쿠키(Cookie), "VPN(Virtual Private Network, 가상 사설망)", 크롬 디버깅 모드(Chrome Debugging Mode)]
---
# 0. 서론

나날이 각종 플랫폼에서 봇을 감지하는 기술이 발전하고 있다. 기존에 셀레니움(Selenium)으로 수집하던 방식으로 접근하니 봇 탐지가 고도화되어 더 이상 수집이 불가능한 경우가 발생했다. 하지만 임파서블 이즈 낫띵. 어떤 경우에도 결국 봇 탐지를 우회할 방법이 있다. 단계별로 알아보고 시도해보자.

# 1단계. 각종 크롬 옵션 추가

1단계이다. 먼저 간단한 옵션들만 추가하여 탐지를 피해본다.

## 1.1 Chrome 옵션 설정

```py
from selenium import webdriver
from selenium.webdriver.chrome.options import Options

options = Options()
options.add_argument("disable-blink-features=AutomationControlled")  # 자동화 탐지 방지
options.add_experimental_option("excludeSwitches", ["enable-automation"])  # 자동화 표시 제거
options.add_experimental_option('useAutomationExtension', False)  # 자동화 확장 기능 사용 안 함

driver = webdriver.Chrome(options=options)

```

브라우저의 속성을 변경하여 일반 사용자처럼 보이도록 할 수 있다.

## 1.2 `User-Agent` 변경

```py
options.add_argument("user-agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.150 Safari/537.36")
```

브라우저의 `User-Agent`를 일반 사용자의 것으로 변경하여 탐지를 피할 수 있다.

## 1.3 `WebDriver` 속성 변경

```py
driver.execute_script("Object.defineProperty(navigator, 'webdriver', {get: () => undefined})")
```

셀레니움이 컨트롤하는 크롬 인스턴스의 `navigator.webdriver` 플래그를 변경할 수 있다. 이를 위해 자바스크립트(JavaScript) 코드를 실행해야 한다.

## 1.4 전체 코드

```py
from selenium import webdriver
from selenium.webdriver.chrome.options import Options

options = Options()
options.add_argument("disable-blink-features=AutomationControlled")
options.add_argument("user-agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.150 Safari/537.36")

options.add_experimental_option("excludeSwitches", ["enable-automation"])
options.add_experimental_option('useAutomationExtension', False)

driver = webdriver.Chrome(options=options)

driver.execute_script("Object.defineProperty(navigator, 'webdriver', {get: () => undefined})")
```

이 방법만으로 봇 탐지를 피했다면 축하하지만, 안된다면 다음 단계로 넘어간다.

# 2단계. 쿠키

2단계로 쿠키를 이용해본다. 일반 사용자는 일정 기간 동안 사이트를 재방문할 때 쿠키 정보를 유지한다. 셀레니움 세션을 재사용하거나 쿠키를 저장/로드하는 방식으로 이를 모방해본다.

## 2.1 쿠키 저장

### 2.1.1 셀레니움을 통한 저장

```py
from selenium import webdriver
import json

driver = webdriver.Chrome()

# 웹사이트에 액세스
driver.get("https://shopee.vn/")

# 필요한 작업 수행 (예: 로그인)
# 직접 조작해도 되고 자동화 코드를 짜도 된다.
# ...

input()  # 쿠키에 저장하기 위한 작업이 끝나면 엔터를 누른다.

# 쿠키 저장
cookies = driver.get_cookies()
with open("cookies.json", "w") as file:
    json.dump(cookies, file)

driver.quit()
```

셀레니움을 사용하여 웹사이트에 액세스하고 로그인 또는 필요한 작업을 수행한 후, 쿠키를 저장하는 과정이다.

### 2.1.2 EditThisCookie를 사용하여 쿠키 저장

EditThisCookie는 크롬 확장 프로그램으로, 웹 브라우저의 쿠키를 쉽게 관리할 수 있도록 도와준다. 이 확장 프로그램을 사용하여 쿠키를 저장하는 과정은 다음과 같다.

### 2.1.2.1 EditThisCookie 설치

![EditThisCookie 설치](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/selenium-bot-detection-bypass-1.png)

크롬 웹 스토어에서 [EditThisCookie](https://chromewebstore.google.com/detail/fngmhnnpilhplaeedifhccceomclgfbg?hl=ko) 확장 프로그램을 설치한다.

### 2.1.2.2 쿠키 내보내기

![EditThisCookie 쿠키 내보내기](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/selenium-bot-detection-bypass-2.png)

쿠키를 저장하고자 하는 웹사이트에 접속한다. 저장되었으면 하는 테스크(예: 로그안)를 진행 후 EditThisCookie 확장 프로그램을 클릭한다. 내보내기 버튼을 클릭한 후 `cookies.json` 파일을 만들어 붙여넣는다.

## 2.2 쿠키 로드

어떠한 방법으로든 쿠키를 저장하였다면, 이제 쿠키를 로드하여 적용한다.

```py
# 쿠키 로드
cookies_file_path = "cookies.json"
with open(cookies_file_path, 'r') as cookies_file:
    cookies = json.load(cookies_file)

driver.get("https://shopee.vn/")

# 쿠키를 하나씩 추가하기 전에 'sameSite' 속성 확인 및 수정
for cookie in cookies:
    if 'sameSite' in cookie and cookie['sameSite'] not in ['Strict', 'Lax', 'None']:
        cookie['sameSite'] = 'None'
    driver.add_cookie(cookie)

# 페이지를 다시 로드하여 쿠키 적용
driver.refresh()
```

위 코드와 같이 쿠키를 적용하고자 하는 웹사이트에 접속한 후, 쿠키를 하나씩 추가한다. 이때 `sameSite` 속성이 `Strict` 또는 `Lax`인 경우 `None`으로 변경해야 한다. 이후 페이지를 다시 로드하여 쿠키를 적용한다. 이 방법은 봇 탐지 뿐 아니라 로그인이 잦은 경우에서의 크롤링에서도 유용하다.

## 2.3 전체 코드

1, 2단계를 합친 전체 코드를 공유한다.

```py
from selenium import webdriver
from selenium.webdriver.chrome.options import Options

options = Options()
options.add_argument("disable-blink-features=AutomationControlled")
options.add_argument("user-agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.150 Safari/537.36")

options.add_experimental_option("excludeSwitches", ["enable-automation"])
options.add_experimental_option('useAutomationExtension', False)

driver = webdriver.Chrome(options=options)

driver.execute_script("Object.defineProperty(navigator, 'webdriver', {get: () => undefined})")

# 쿠키 로드
cookies_file_path = "cookies.json"
with open(cookies_file_path, 'r') as cookies_file:
    cookies = json.load(cookies_file)

driver.get("https://shopee.vn/")

# 쿠키를 하나씩 추가하기 전에 'sameSite' 속성 확인 및 수정
for cookie in cookies:
    if 'sameSite' in cookie and cookie['sameSite'] not in ['Strict', 'Lax', 'None']:
        cookie['sameSite'] = 'None'
    driver.add_cookie(cookie)

# 페이지를 다시 로드하여 쿠키 적용
driver.refresh()
```

# 3단계. VPN

3단계로 [VPN](/vpn)을 이용해본다. 해외에서 접속한 경우를 막을 가능성을 위해 VPN을 사용한다. 필자는 [NordVPN](https://nordvpn.com/ko/offer-site/)라는 유료 서비스를 사용하였지만, 이 마저도 성공할 수 없었다. 무료로 사용할 수 있는 VPN 서비스도 있으니 찾아보고 적용해보길 바란다. 최종 단계로 넘어가보자.

# 4단계. 크롬 디버깅 모드

마지막 최종 단계이다. 쉽게 설명하자면, 일반 크롬과 똑같은 설정으로 셀레니움을 접속시키는 절차라고 생각하면 된다. 크롬을 디버깅 모드로 열어서 `debugging port`를 열고, 해당 포트에 접속해 크롬을 제어할 수 있다. 실제 일반 크롬 브라우저가 열린 것이기 때문에 서버 측에서 봇으로 감지하기가 어렵게 된다.

## 4.1 크롬 디버깅 모드 실행

터미널에서 아래 명령어를 입력하여 크롬 디버깅 모드를 실행한다.

```sh
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --remote-debugging-port=9222 --user-data-dir="~/ChromeProfile"
```

![크롬 디버깅 모드 실행](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/selenium-bot-detection-bypass-3.png)

## 4.2 셀레니움과 디버깅 모드 연결

디버깅 모드를 실행한 후, 셀레니움을 이용하여 디버깅 모드에 연결한다.

```py
from selenium import webdriver
from selenium.webdriver.chrome.options import Options

# Chrome 디버그 옵션 설정
options = Options()
options.add_experimental_option("debuggerAddress", "localhost:9222")

# 기존 Chrome 인스턴스에 연결
driver = webdriver.Chrome(options=options)

# 이제 driver 객체를 사용하여 브라우저를 제어
# 예: 현재 열린 탭의 URL 가져오기
print(driver.current_url)
```

이 방법으로 끝내 봇 탐지 회피를 성공할 수 있었다. 하지만, 쇼피에서는 연결 후 셀레니움을 통해 다른 페이지로 이동하는 순간 또 봇으로 인식하는 바람에, 애초에 수집할 페이지로 이동한 후 디버깅 모드를 연결하여 자동화 작업을 실시했다.

# 5. 마무리 및 참고

웹 크롤링 시 봇 탐지를 우회하는 다양한 단계와 방법을 소개했다. 첫 번째 단계에서 셀레니움(Selenium)을 사용하여 크롬 옵션을 조정하고, `User-Agent`를 변경하여 간단한 봇 탐지 메커니즘을 우회하는 방법을 다뤘고, 두 번째 단계에서는 쿠키를 저장하고 로드하는 방법을 통해 사용자 세션을 유지하며 봇 탐지를 우회하는 전략을 소개했다. 세 번째 단계에서는 VPN을 사용하여 지리적 위치를 변경함으로써 탐지를 피하는 방법을 설명했고, 마지막으로, 크롬 디버깅 모드를 활용하여 실제 사용자의 브라우저와 동일한 환경을 구성함으로써 가장 고도화된 봇 탐지 시스템을 우회하는 방법을 제공했다.

## 5.1 관련 아티클

- [웹 크롤링 IP 차단 우회 방법론: AWS EC2 재부팅을 통한 IP 변경 및 연속 수집 자동화 전략](/web-crawling-ip-bypass-with-ec2)