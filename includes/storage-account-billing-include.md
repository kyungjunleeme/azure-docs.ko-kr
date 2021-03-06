---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/19/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: fcfe05db6a9be1049ca5da06985f31135ac79f3b
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95564265"
---
스토리지 계정 사용량에 따라 Azure Storage 요금이 청구됩니다. 스토리지 계정의 모든 개체는 그룹으로 합산 청구됩니다. 

스토리지 비용은 다음 요인에 따라 계산됩니다. 

* **지역** 이란 계정이 기초하는 지리적 위치를 의미합니다.
* **계정 유형** 은 사용 중인 스토리지 계정 유형을 나타냅니다. 
* **액세스 계층** 은 범용 v2 또는 Blob 스토리지 계정에 지정한 데이터 사용 패턴을 나타냅니다.
* 스토리지 **용량** 은 데이터를 저장하는 데 사용하는 스토리지 계정 할당량이 어느 정도인지를 나타냅니다.
* **복제** 에 따라 한 번에 유지 관리되는 데이터의 복사본 수 및 위치가 결정됩니다.
* **트랜잭션** 은 Azure Storage에 대한 모든 읽기 및 쓰기 작업을 나타냅니다.
* **데이터 송신** 은 Azure 지역 외부에서 전송되는 데이터를 나타냅니다. 스토리지 계정의 데이터에 동일한 지역에서 실행되지 않는 애플리케이션이 액세스하는 경우 데이터 송신 요금이 부과됩니다. 리소스 그룹을 기반으로 동일한 지역의 데이터 및 서비스를 그룹화하여 송신 요금을 제한하는 방법에 대한 자세한 내용은 [Azure 리소스 그룹이란?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group)을 참조하세요. 

[Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/) 페이지는 계정 유형, 스토리지 용량, 복제 및 트랜잭션에 대한 자세한 가격 정보를 제공합니다. [데이터 전송 가격 정보](https://azure.microsoft.com/pricing/details/data-transfers/) 에서는 데이터 송신에 대한 자세한 가격 정보를 제공합니다. [Azure Storage 가격 계산기](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) 를 사용하여 비용을 예측할 수 있습니다.