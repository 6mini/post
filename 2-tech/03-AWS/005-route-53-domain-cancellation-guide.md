---
title: 'AWS Route 53 도메인 해지 가이드: 환불 절차 포함'
description: "AWS Route 53에서 구매한 도메인을 해지하고 환불받는 방법에 대해 자세하게 설명한다. AWS Support를 통한 환불 요청 절차, 호스팅 영역 삭제 방법 및 도메인 해지 과정을 단계별로 안내하여, 사용자가 도메인 관리에 있어서 신중한 결정을 내릴 수 있도록 돕는다."
date: '2024-01-30'
tags: [AWS, Route 53, AWS Support, 도메인]
---

# 1. 서론

Amazon Web Services(AWS)의 Route 53은 도메인 등록 및 관리 서비스이다. 하지만 때로는 도메인을 해지하고 환불을 받아야 할 상황이 발생할 수 있다. 이 포스팅에서는 Route 53 도메인을 해지하고 환불을 받는 전체 절차를 상세히 안내할 것이다.

![Route 53의 등록된 도메인 목록](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/route-53-domain-cancellation-guide-1.png)

블로그용 도메인을 생성할 당시 AWS Route 53을 통해 `leeyoonmin.com`라는 도메인을 구매했다. 도메인을 구매한 시기는 블로그 제작을 진행하기 전이었는데, 블로그를 배포하려고 보니 도메인이 `yoonminlee.com`으로 설정하는 것이 더욱 적절할 것이란 판단이 들었다. 그래서 일단 `yoonminlee.com` 도메인을 구매하여 연결하였고, 처치 곤란인 `leeyoonmin.com` 도메인을 해지하고 환불을 진행하고자 했다. 이 과정을 진행한 절차를 정리해본다.

![Route 53 호스팅 영역의 월 요금 내역](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/route-53-domain-cancellation-guide-2.png)

기본적으로 도메인을 구매하면 호스팅 영역이 자동으로 생성되는데, 이 것을 유지하는 것에도 월 0.5USD가 부과된다. 이를 유지하는 월 고정 요금이 아깝기도 하고, 딱히 `leeyoonmin.com` 도메인을 활용하지 않을 것 같아 한 푼이라도 아껴보고자 하는 마음으로 절차를 진행했다.

# 2. 호스팅 영역 삭제

먼저 [Route 53의 `호스팅 영역`](https://us-east-1.console.aws.amazon.com/route53/v2/hostedzones)에서 삭제할 도메인의 호스팅 영역을 선택한 뒤 삭제를 진행한다.

![Route 53 호스팅 영역 삭제 방법](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/route-53-domain-cancellation-guide-3.png)

아주 손쉽게 삭제가 완료된다.

# 3. 도메인 환불 절차

## 3.1 AWS Support 센터 사례 전송

도메인 환불 절차는 [AWS Support 센터](https://support.console.aws.amazon.com/support/home)를 통해 진행한다.

![AWS Support 센터 사례 생성](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/route-53-domain-cancellation-guide-4.png)

`사례 생성` 버튼을 클릭한다.

![사례의 서비스와 범주 입력](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/route-53-domain-cancellation-guide-5.png)

`계정 및 결제` 문제에서 `Domains` 서비스, `Restoration` 범주로 선택하고 다음 단계로 간다.

GPT를 통해 간단한 환불 요청문을 작성했다. 양식은 아래와 같다.

> 제목: 도메인 구매 취소 및 환불 요청
> 
> 안녕하세요,
> 
> [귀하의 이름]입니다. 최근 귀사를 통해 도메인 [잘못 구매한 도메인 이름]를 등록했습니다. 그러나 도메인 구매 후 실수로 잘못된 이름을 선택한 것을 발견했습니다. 이에 따라, 해당 도메인의 구매 취소와 환불을 요청드리고자 합니다.
> 
> 도메인 구매 일자: [구매 날짜]
> 결제 방법: [결제 방법]
> 주문 번호: [주문 번호 또는 참조 번호]
> 
> 제가 원하는 도메인은 [원래 구매하려고 했던 도메인 이름]입니다. 실수로 잘못된 도메인을 구매한 것은 전적으로 제 책임이지만, 귀사의 이해와 협조를 부탁드립니다.
> 
> 환불 절차에 대한 지침을 제공해 주시면 감사하겠습니다. 필요한 추가 정보나 문서가 있으시다면 알려주시기 바랍니다.
> 
> 귀사의 신속한 대응에 미리 감사드리며, 이 문제를 원만히 해결할 수 있기를 바랍니다.
> 
> 감사합니다.
> 
> [귀하의 전체 이름]
> [연락처 정보 - 이메일 주소, 전화번호 등]

## 3.2 도메인 주문 번호 확인

주문 번호는 [Route 53의 `등록된 도메인`](https://us-east-1.console.aws.amazon.com/route53/v2/domains)에서 확인할 수 있었다.

![Route 53의 등록된 도메인에서 결제 보고서 다운로드](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/route-53-domain-cancellation-guide-6.png)

`결제 보고서 다운로드` 버튼을 클릭한다.

![결제 해당 월의 CSV 보고서 다운로드](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/route-53-domain-cancellation-guide-7.png)

도메인을 구매한 월의 결제 보고서 CSV를 다운로드한다.

![인보이스 확인](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/route-53-domain-cancellation-guide-8.png)

다운로드한 CSV 파일을 열어보면, `InvoiceId`를 확인할 수 있다. 이 값을 넣어준다.

## 3.3 사례 제출

![사례 입력 및 제출](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/route-53-domain-cancellation-guide-9.png)

작성이 잘 되었다면, `AWS에 문의` 버튼을 클릭하여 사례를 제출한다.

## 3.4 사례 답변

하루하고 5시간만에 답변이 왔다.

![사례 답변](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/route-53-domain-cancellation-guide-10.png)

기본적으로 도메인은 환불이 불가능하지만, 이번 한 번에 한하여 도메인 비용을 환불해준다고 한다. 답변 받은 절차에 따라 도메인 해지를 진행하고 다시 답신을 제출해보자.

# 4. 도메인 해지

## 4.1 도메인 삭제

![Route 53 도메인 삭제](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/route-53-domain-cancellation-guide-11.png)

환불받고자하는 도메인으로 이동한 뒤, `도메인 삭제` 버튼을 클릭한다. 손쉽게 삭제가 진행되며, 삭제가 완료된다면 아래와 같은 메일이 온다.

![삭제 완료 메일 확인](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/route-53-domain-cancellation-guide-12.png)

## 4.2 사례 답변

> 안녕하세요 Glarindha J.님,
>
> Amazon Web Services 팀의 도움에 감사드립니다.
> 
> 'leeyoonmin.com' 도메인 등록 취소에 대한 안내를 주셔서 감사합니다. AWS 도메인 이름 등록 계약의 6.3 및 9.1.3 조항에 따라 도메인 이름 등록이나 갱신에 대한 환불이 불가능하다는 점을 이해했습니다. 하지만 이번 한 번에 한하여 도메인 비용을 환불해주신다니 정말 감사드립니다.
> 
> 제공해주신 문서에 따라 도메인을 삭제했습니다. 도메인 삭제 후 확인 이메일도 받았습니다.
> 또한, 미래에 예상치 못한 요금이 발생하지 않도록 호스팅 영역도 삭제했습니다. 이에 대한 지침도 감사합니다.
> 
> 이에 따라 도메인 요금 조정을 부탁드리겠습니다.
> 
> 감사합니다.
> 
> 이윤민
> real6mini@gmail.com

삭제 후 위와 같이 답변을 제출했다.

# 5. 결과

![환불 처리 답변 수신](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/route-53-domain-cancellation-guide-13.png)

성공적으로 환불 처리 답변을 받게되었다.

![환불 메일 확인](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/route-53-domain-cancellation-guide-14.png)

곧이어 환불을 확인할 수 있었다.

# 6. 마무리

AWS Route 53에서 도메인을 해지하고 환불받는 과정을 살펴보았다. 생각보다 수월했으며, AWS 지원팀에 굉장히 감사한 마음이 든다. 여담이지만, 이후 또 사용하지 않을 도메인을 구매하게 되어 염치불구하고 환불 신청을 했지만, 당연히 알려준 것과 같이 환불이 이루어지지 않았다. 나도 그렇지만, 도메인을 구매할 때 신중을 가하여 구매하길 바란다.