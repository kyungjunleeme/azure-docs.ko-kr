---
title: Azure IoT 솔루션 사이트 - Azure | Microsoft Docs
description: AzureIoTSolutions.com 웹 사이트를 사용하여 솔루션 가속기를 배포하는 방법에 대해 설명합니다.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: b05ed6e1239721bcf3c1cf33d3ee63a992fd9843
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853233"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>azureiotsolutions.com 사이트를 사용하여 솔루션 가속기 배포

[AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators)에서 Azure IoT 솔루션 가속기를 Azure 구독에 배포할 수 있습니다. AzureIoTSolutions.com에서 Microsoft 오픈 소스 및 파트너 솔루션 가속기를 모두 호스트합니다. 이 솔루션 가속기는 [Azure IoT 참조 아키텍처](/azure/architecture/reference-architectures/iot)와 맞습니다. 사이트를 사용하여 솔루션 가속기를 데모 또는 프로덕션 환경으로 빠르게 배포할 수 있습니다.

![AzureIoTSolutions.com](media/iot-accelerators-permissions/iotsolutionscom.png)

> [!TIP]
> 배포 프로세스에 더 많은 제어가 필요한 경우 CLI를 사용하여 솔루션 가속기를 배포할 수 있습니다.

다음 구성으로 솔루션 가속기를 배포할 수 있습니다.

* **표준**: 프로덕션 환경을 개발 하기 위한 확장 된 인프라 배포입니다. Azure Container Service는 마이크로 서비스를 여러 개의 Azure 가상 머신에 배포합니다. Kubernetes는 개별 마이크로 서비스를 호스팅하는 Docker 컨테이너를 오케스트레이션합니다.
* **기본**: 데모를 위한 저렴 한 비용 버전 또는 배포를 테스트 하는 데 사용할 수 있습니다. 모든 마이크로 서비스가 단일 Azure 가상 머신에 배포됩니다.
* **Local**: 테스트 및 개발을 위한 로컬 컴퓨터 배포입니다. 이 방법은 마이크로 서비스를 로컬 Docker 컨테이너에 배포하고, 클라우드의 IoT Hub, Azure Cosmos DB 및 Azure Storage 서비스에 연결합니다.

각 솔루션 가속기는 IoT Hub, Azure Stream Analytics, Cosmos DB와 같은 다양한 Azure 서비스 조합을 사용합니다. 자세한 내용을 보려면 [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators)에서 솔루션 가속기를 선택하세요.

## <a name="sign-in-at-azureiotsolutionscom"></a>azureiotsolutions.com에서 로그인

솔루션 가속기를 배포하려면 먼저 Azure 구독과 연결된 자격 증명을 사용하여 AzureIoTSolutions.com에 로그인해야 합니다. 계정이 두 개 이상의 Microsoft Azure AD(Active Directory) 테넌트와 연결된 경우 **계정 선택** 드롭다운을 사용하여 사용할 디렉터리를 선택할 수 있습니다.

솔루션 가속기를 배포하고 사용자를 관리하며 Azure 서비스를 관리하는 권한은 선택한 디렉터리 내의 역할에 따라 다릅니다. 솔루션 가속기와 연관된 일반적인 Azure AD 역할은 다음과 같습니다.

* **전역 관리자**: Azure AD 테 넌 트 당 많은 [전역 관리자가](../active-directory/roles/permissions-reference.md) 있을 수 있습니다.

  * Azure AD 테넌트를 만들 때, 만드는 사람은 기본적으로 해당 테넌트의 전역 관리자입니다.
  * 전역 관리자는 기본 및 표준 솔루션 가속기를 배포할 수 있습니다.

* **도메인 사용자**: Azure AD 테 넌 트 당 많은 도메인 사용자가 있을 수 있습니다. 도메인 사용자는 기본 솔루션 가속기를 배포할 수 있습니다.

* **게스트 사용자**: Azure AD 테 넌 트 당 많은 게스트 사용자가 있을 수 있습니다. 게스트 사용자는 Azure AD 테넌트에서 솔루션 가속기를 배포할 수 없습니다.

Azure AD의 사용자 및 역할에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [Azure Active Directory에서 사용자 만들기](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [앱에 사용자 할당](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>디바이스 선택

AzureIoTSolutions.com 사이트는 [IoT용 Azure Certified 디바이스 카탈로그](https://catalog.azureiotsolutions.com/)로 연결됩니다.

이 카탈로그는 사용자의 IoT 솔루션 빌드를 시작하기 위해 솔루션 가속기에 연결할 수 있는 수백 개의 인증된 IoT 하드웨어 디바이스를 보여 줍니다.

![디바이스 카탈로그](media/iot-accelerators-permissions/devicecatalog.png)

하드웨어 제조업체인 경우 **파트너 되기** 를 클릭하여 IoT용 Certified 프로그램을 통해 Microsoft와 협력하는 방법을 알아보세요.

## <a name="next-steps"></a>다음 단계

IoT 솔루션 가속기 중 하나를 사용해 보려면 빠른 시작: [연결 된 팩터리 솔루션 사용해 보기](quickstart-connected-factory-deploy.md)를 확인 하세요.
