---
title: 선불 예약 된 용량-Azure Database for MySQL
description: 예약 된 용량의 Azure Database for MySQL 계산 리소스에 대 한 선불
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 33c2617a22945ecb11586bc282e7f9f61b6e49ca
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008551"
---
# <a name="prepay-for-azure-database-for-mysql-compute-resources-with-reserved-capacity"></a>예약 된 용량의 Azure Database for MySQL 계산 리소스에 대 한 선불

이제 Azure Database for MySQL를 사용 하 여 종 량 제 가격과 비교 하 여 계산 리소스에 대 한 총액 비용을 절감할 수 있습니다. Azure Database for MySQL 예약 된 용량을 사용 하 여 1 년 또는 3 년 동안 MySQL server에 대 한 사전 약정을 수행 하 여 계산 비용에 대 한 상당한 할인을 받으세요. Azure Database for MySQL reserved capacity를 구매 하려면 Azure 지역, 배포 유형, 성능 계층 및 용어를 지정 해야 합니다. </br>

특정 Azure Database for MySQL 서버에 예약을 할당할 필요가 없습니다. 이미 실행 중인 Azure Database for MySQL 또는 새로 배포 된는 예약 된 가격 책정의 혜택을 자동으로 받게 됩니다. 예약을 구입하면 1년 또는 3년 동안의 컴퓨팅 비용을 선결제하게 됩니다. 예약을 구매 하는 즉시, 예약 특성과 일치 하는 Azure database for MySQL 계산 요금은 더 이상 종 량 제 요금으로 청구 되지 않습니다. 예약은 MySQL 데이터베이스 서버와 관련 된 소프트웨어, 네트워킹 또는 저장소 요금을 포함 하지 않습니다. 예약 기간이 끝나면 청구 혜택이 만료 되 고 Azure Database for MySQL는 종 량 제 가격으로 청구 됩니다. 예약은 자동 갱신되지 않습니다. 가격 책정 정보는 [Azure Database for MySQL 예약 된 용량 제공](https://azure.microsoft.com/pricing/details/mysql/)을 참조 하세요. </br>

[Azure Portal](https://portal.azure.com/)에서 Azure Database for MySQL 예약 된 용량을 구매할 수 있습니다. 예약 요금은 [사전 결제 또는 월별 결제](../cost-management-billing/reservations/prepare-buy-reservation.md)로 처리할 수 있습니다. 예약 된 용량을 구입 하려면:

* 종 량 제 요금은 하나 이상의 Enterprise 또는 개별 구독에 대 한 소유자 역할에 속해야 합니다.
* Enterprise 구독의 경우 [EA 포털](https://ea.azure.com/)에서 **예약 인스턴스 추가** 를 활성화해야 합니다. 이 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.
* CSP (클라우드 솔루션 공급자) 프로그램의 경우 관리 에이전트 또는 판매 에이전트만 Azure Database for MySQL 예약 된 용량을 구매할 수 있습니다. </br>

엔터프라이즈 고객과 종 량 제 고객의 예약 구매 요금에 대 한 자세한 내용은 [기업 등록에 대 한 azure 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 및 [종 량 제 구독에 대 한 azure 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage.md)를 참조 하세요.


## <a name="determine-the-right-database-size-before-purchase"></a>구매 하기 전에 올바른 데이터베이스 크기 확인

예약 크기는 특정 지역 내에서 기존 또는 곧 배포 되는 서버에서 사용 하는 총 계산 용량을 기반으로 하며 동일한 성능 계층 및 하드웨어 생성을 사용 해야 합니다.</br>

예를 들어 Gen5 – 32 vCore MySQL 데이터베이스와 두 개의 메모리 최적화 Gen5 – 16 vCore MySQL 데이터베이스를 실행 하는 경우를 가정해 보겠습니다. 또한 다음 달에 추가 범용, Gen5 – 32 vCore 데이터베이스 서버 및 메모리 최적화 Gen5 – 16 vCore 데이터베이스 서버 하나를 배포 하려는 경우를 가정해 보겠습니다. 최소 1 년 동안 이러한 리소스가 필요 하다는 것을 알고 있다고 가정해 보겠습니다. 이 경우 64 (2x32) Vcores를 구매 하 고 단일 데이터베이스 일반 용도의 경우 1 년 예약, 단일 데이터베이스 메모리 최적화에 대 한 48 (2x16 + 16) Vcores 1 년 예약을 구매 해야 합니다. Gen5


## <a name="buy-azure-database-for-mysql-reserved-capacity"></a>Azure Database for MySQL 예약 된 용량 구입

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **모든 서비스** > **예약** 을 선택합니다.
3. **추가** 를 선택한 다음 구매 예약 창에서 **Azure Database for MySQL** 을 선택 하 여 MySQL 데이터베이스용 새 예약을 구매 합니다.
4. Fill-필수 필드입니다. 선택한 특성과 일치 하는 기존 또는 새 데이터베이스는 예약 된 용량 할인을 얻기 위해 한정 됩니다. 할인을 받는 Azure Database for MySQL 서버의 실제 수는 선택한 범위와 수량에 따라 달라 집니다.


:::image type="content" source="media/concepts-reserved-pricing/mysql-reserved-price.png" alt-text="예약 된 가격 책정 개요":::


다음 표에서는 필수 필드에 대해 설명 합니다.

| 필드 | Description |
| :------------ | :------- |
| 구독   | 예약 된 Azure Database for MySQL 용량 예약에 대 한 비용을 지불 하는 데 사용 되는 구독입니다. 구독에 대 한 결제 방법은 Azure Database for MySQL 예약 된 용량 예약에 대 한 선행 비용으로 청구 됩니다. 구독 유형은 기업 계약 (제품 번호: MS-AZR-0017P-0017P 또는 MS-AZR-0017P-Ms-azr-0148p) 이거나 종 량 제 가격을 포함 하는 개별 계약 (제품 번호: MS-MS-AZR-0017P-0003P 또는-0017P) 이어야 합니다. Enterprise 구독에 대한 요금은 등록의 금액 약정 잔액에서 차감되거나 초과 비용으로 청구됩니다. 종 량 제 가격의 개별 구독에 대해 요금 청구는 구독에 대 한 신용 카드 또는 청구서 지불 방법으로 청구 됩니다.
| Scope | VCore 예약의 범위는 하나의 구독 또는 여러 구독 (공유 범위)을 포함할 수 있습니다. 다음을 선택하는 경우: </br></br> **공유**, vcore 예약 할인은 청구 컨텍스트 내의 모든 구독에서 실행 중인 Azure Database for MySQL 서버에 적용 됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종량제 고객의 공유 범위는 계정 관리자가 만든 모든 종량제 구독입니다.</br></br> **단일 구독** 에서 vcore 예약 할인은이 구독의 Azure Database for MySQL 서버에 적용 됩니다. </br></br> **단일 리소스 그룹**-예약 할인이 선택한 구독의 Azure Database for MySQL 서버와 해당 구독 내에서 선택한 리소스 그룹에 적용 됩니다.
| 지역 | Azure Database for MySQL 예약 된 용량 예약이 적용 되는 Azure 지역입니다.
| 배포 유형 | 예약을 구입할 Azure Database for MySQL 리소스 형식입니다.
| 성능 계층 | Azure Database for MySQL 서버에 대 한 서비스 계층입니다.
| 용어 | 1년
| 수량 | Azure Database for MySQL 예약 된 용량 예약 내에서 구매한 계산 리소스의 양입니다. 수량은 선택한 Azure 지역 및 성능 계층의 많은 vCores로, 예약 되 고 청구 할인이 적용 됩니다. 예를 들어 미국 동부 지역에서 Gen5 16 vCores의 총 계산 용량을 사용 하 여 Azure Database for MySQL 서버를 실행 하는 경우 모든 서버에 대 한 혜택을 최대화 하려면 수량을 16으로 지정 합니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)을 참조하세요.

## <a name="vcore-size-flexibility"></a>vCore 크기 유연성

vCore 크기 유연성을 통해 예약된 용량 이점을 잃지 않고 성능 계층 및 지역 내에서 크기를 확장 또는 축소할 수 있습니다. 

## <a name="need-help--contact-us"></a>도움이 필요 하세요? 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

VCore 예약 할인은 Azure Database for MySQL 예약 된 용량 예약 범위 및 특성과 일치 하는 Azure Database for MySQL 서버 수에 자동으로 적용 됩니다. Azure Portal, PowerShell, CLI 또는 API를 통해 Azure database for MySQL 예약 용량 예약의 범위를 업데이트할 수 있습니다. </br></br>
Azure Database for MySQL 예약 된 용량을 관리 하는 방법에 대 한 자세한 내용은 Azure Database for MySQL 예약 된 용량 관리를 참조 하세요.

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Reservations 이란](../cost-management-billing/reservations/save-compute-costs-reservations.md)?
* [Azure Reservations 관리](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Azure 예약 할인 이해](../cost-management-billing/reservations/understand-reservation-charges.md)
* [종량제 구독의 예약 사용량 이해](../cost-management-billing/reservations/understand-reservation-charges-mysql.md)
* [엔터프라이즈 등록에서 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](/partner-center/azure-reservations)