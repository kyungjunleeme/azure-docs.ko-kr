---
title: 가상 네트워크 규칙 사용-Azure Portal-Azure Database for PostgreSQL-단일 서버
description: Azure Portal를 사용 하 여 단일 서버 Azure Database for PostgreSQL VNet 서비스 끝점 및 규칙 만들기 및 관리
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 377f8eba179253ca6187b10a22970d0eadcda2f6
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489832"
---
# <a name="create-and-manage-vnet-service-endpoints-and-vnet-rules-in-azure-database-for-postgresql---single-server-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure Database for PostgreSQL 단일 서버에서 VNet 서비스 끝점 및 VNet 규칙 만들기 및 관리
VNet(가상 네트워크) 서비스 엔드포인트 및 규칙이 가상 네트워크의 프라이빗 주소 공간을 Azure Database for PostgreSQL 서버로 확장합니다. 제한을 포함하여 Azure Database for PostgreSQL VNet 서비스 엔드포인트에 대한 개요는 [Azure Database for PostgreSQL 서버 VNet 서비스 엔드포인트](concepts-data-access-and-security-vnet.md)를 참조하세요. VNet 서비스 엔드포인트는 Azure Database for PostgreSQL에 대한 지원되는 모든 지역에서 사용할 수 있습니다.

> [!NOTE]
> VNet 서비스 엔드포인트는 범용 및 메모리 최적화 서버에 대해서만 지원됩니다.
> VNet 피어링의 경우 서비스 엔드포인트가 있는 공통 VNet 게이트웨이를 통해 트래픽이 이동하며, 피어로 이동되어야 하는 경우 게이트웨이 VNet의 Azure Virtual Machines가 Azure Database for PostgreSQL 서버에 액세스할 수 있도록 허용하는 ACL/VNet 규칙을 만드세요.


## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>VNet 규칙을 만들고 Azure Portal의 서비스 엔드포인트를 사용하도록 설정

1. PostgreSQL 서버 페이지의 설정 머리글에서 **연결 보안**을 클릭하여 Azure Database for PostgreSQL에 대한 연결 보안 페이지를 엽니다. 

2. Azure 서비스에 대 한 액세스 허용 컨트롤이 **OFF**로 설정 되어 있는지 확인 합니다.

> [!Important]
> 컨트롤을 ON으로 설정 하면 Azure PostgreSQL 데이터베이스 서버는 모든 서브넷의 통신을 수락 합니다. 제어 집합을 ON으로 유지하면 보안 관점에서 과도하게 액세스할 수도 있습니다. Microsoft Azure Virtual Network 서비스 끝점 기능은 Azure Database for PostgreSQL의 가상 네트워크 규칙 기능을 함께 사용 하 여 보안 노출 영역을 줄일 수 있습니다.

3. 다음으로, **+ 기존 가상 네트워크 추가**를 클릭합니다. 기존 VNet이 없는 경우 **+ 새 가상 네트워크 만들기**를 클릭할 수 있습니다. [빠른 시작: Azure Portal을 사용하여 가상 네트워크 만들기](../virtual-network/quick-create-portal.md) 참조

   :::image type="content" source="./media/howto-manage-vnet-using-portal/1-connection-security.png" alt-text="연결 보안 Azure Portal 클릭":::

4. VNet 규칙 이름을 입력하고 구독, 가상 네트워크 및 서브넷 이름을 선택한 다음, **사용**을 클릭합니다. **Microsoft.SQL** 서비스 태그를 사용하여 서브넷에서 VNet 서비스 엔드포인트를 자동으로 사용하도록 설정할 수 있습니다.

   :::image type="content" source="./media/howto-manage-vnet-using-portal/2-configure-vnet.png" alt-text="연결 보안 Azure Portal 클릭":::

    계정에는 가상 네트워크 및 서비스 엔드포인트를 만드는 데 필요한 사용 권한이 있어야 합니다.

    가상 네트워크에 대한 쓰기 액세스 권한이 있는 사용자는 가상 네트워크에서 독립적으로 서비스 엔드포인트를 구성할 수 있습니다.
    
    VNet에 대한 Azure 서비스 리소스를 보호하려면 사용자는 추가되는 서브넷의 "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/"에 대한 사용 권한을 갖고 있어야 합니다. 이 권한은 기본적으로 기본 제공 서비스 관리자 역할에 포함되고 사용자 지정 역할을 만들어서 수정될 수 있습니다.
    
    [기본 제공 역할](../role-based-access-control/built-in-roles.md) 및 [사용자 지정 역할](../role-based-access-control/custom-roles.md)에 특정 권한 할당에 대해 자세히 알아보세요.
    
    VNet 및 Azure 서비스 리소스가 동일한 구독이나 다른 구독에 있을 수 있습니다. VNet 및 Azure 서비스 리소스가 서로 다른 구독에 있는 경우 리소스가 동일한 AD(Active Directory) 테넌트에 있어야 합니다. 두 구독 모두에 **Microsoft .Sql** 리소스 공급자가 등록되어 있는지 확인합니다. 자세한 내용은 [resource-manager-registration][resource-manager-portal]을 참조하세요.

   > [!IMPORTANT]
   > 서비스 엔드포인트를 구성하기 전에 서비스 엔드포인트 및 고려 사항에 대한 이 문서를 읽어보는 것이 매우 좋습니다. **Virtual Network 서비스 엔드포인트:** [Virtual Network 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)는 속성 값에 하나 이상의 정식 Azure 서비스 유형 이름이 포함된 서브넷입니다. VNet 서비스 엔드포인트는 SQL Database라는 Azure 서비스를 나타내는 서비스 형식 이름 **Microsoft.Sql**을 사용합니다. 이 서비스 태그는 Azure SQL Database, Azure Database for PostgreSQL 및 MySQL 서비스에도 적용됩니다. **Microsoft.Sql** 서비스 태그를 VNet 서비스 엔드포인트에 적용하는 경우 Azure SQL Database, Azure Database for PostgreSQL 및 Azure Database for MySQL을 포함하는 모든 Azure Database 서비스에 대한 서비스 엔드포인트 트래픽을 서브넷에서 구성합니다. 
   > 

5. 활성화되고 **확인**을 클릭하면 VNet 서비스 엔드포인트가 VNet 규칙에 따라 사용하도록 설정되는 것을 확인할 수 있습니다.

   :::image type="content" source="./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png" alt-text="연결 보안 Azure Portal 클릭":::

## <a name="next-steps"></a>다음 단계
- 마찬가지로 [Azure CLI를 사용하여 Azure Database for PostgreSQL에 대한 VNET 규칙을 만들고 VNet 서비스 엔드포인트를 사용](howto-manage-vnet-using-cli.md)하도록 스크립팅할 수 있습니다.
- Azure Database for PostgreSQL 서버에 연결 하는 방법에 대 한 도움말은 [Azure Database for PostgreSQL 연결 라이브러리](./concepts-connection-libraries.md) 를 참조 하세요.

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md