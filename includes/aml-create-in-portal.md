---
title: 포함 파일
description: 포함 파일
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 11/04/2019
ms.openlocfilehash: f5f132d257e30cd8f4fa1153087bf0df2f0f5b2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91841855"
---
1. Azure 구독에 대한 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Azure Portal의 왼쪽 위 모서리에서 **+ 리소스 만들기**를 선택합니다.

    ![리소스 만들기 옵션을 보여주는 스크린샷.](media/aml-create-in-portal/create-workspace.gif)

1. 검색 창을 사용하여 **Machine Learning**을 찾습니다.

1. **Machine Learning**을 선택합니다.

1. **Machine Learning** 창에서 **만들기**를 선택하여 시작합니다.

1. 새 작업 영역을 구성하려면 다음 정보를 제공하세요.

   필드|Description
   ---|---
   작업 영역 이름 |작업 영역을 식별하는 고유한 이름을 입력합니다. 이 예제에서는 **docs-ws**를 사용합니다. 이름은 리소스 그룹 전체에서 고유해야 합니다. 다른 사용자가 만든 작업 영역과 구별되고 기억하기 쉬운 이름을 사용하세요.
   Subscription |사용할 Azure 구독을 선택합니다.
   Resource group | 구독에서 기존 리소스 그룹을 사용하거나 이름을 입력하여 새 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유합니다. 이 예에서는 **docs-aml**을 사용합니다. 
   위치 | 사용자 및 데이터 리소스와 가장 가까운 위치를 선택하여 작업 영역을 만듭니다.
   Workspace Edition | 이 자습서의 작업 영역 유형으로 **기본**을 선택합니다. 작업 영역 유형에 따라 액세스할 수 있는 기능과 가격이 결정됩니다. 이 자습서의 모든 것은 기본 또는 엔터프라이즈 작업 영역으로 수행할 수 있습니다.

1. 작업 영역 구성을 마쳤으면 **검토 + 만들기**를 선택합니다.

   > [!Warning]
   > 클라우드에서 작업 영역을 만드는 데 몇 분 정도 걸릴 수 있습니다.

   프로세스가 완료되면 배포 성공 메시지가 표시됩니다.
 
 1. 새 작업 영역을 보려면 **리소스로 이동**을 선택합니다.

