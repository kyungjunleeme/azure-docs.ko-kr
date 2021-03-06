---
title: 방화벽 규칙 관리-Azure CLI-Azure Database for PostgreSQL 유연한 서버
description: Azure CLI 명령줄을 사용 하 여 Azure Database for PostgreSQL 유연한 서버에 대 한 방화벽 규칙을 만들고 관리 합니다.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 36249694c5a4de8a738853892f827c6d9e1e4aff
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489475"
---
# <a name="create-and-manage-azure-database-for-postgresql---flexible-server-firewall-rules-using-the-azure-cli"></a>Azure CLI를 사용 하 여 Azure Database for PostgreSQL 유연한 서버 방화벽 규칙 만들기 및 관리

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

Azure Database for PostgreSQL 유연한 서버는 유연한 서버에 연결 하는 두 가지 유형의 상호 배타적인 네트워크 연결 방법을 지원 합니다. 다음은 두 가지 옵션입니다.

* 퍼블릭 액세스(허용된 IP 주소)
* 프라이빗 액세스(VNet 통합)

이 문서에서는 Azure CLI를 사용 하 여 **공용 액세스 (허용 된 IP 주소)** 로 PostgreSQL 서버를 만드는 방법에 중점을 두고 서버를 만든 후에 방화벽 규칙을 만들고, 업데이트 하 고, 삭제 하 고, 나열 하 고, 표시 하는 데 사용할 수 있는 Azure CLI 명령에 대 한 개요를 제공 합니다. *공용 액세스 (허용 되는 ip 주소)* 를 사용 하는 경우 PostgreSQL 서버에 대 한 연결은 허용 된 ip 주소만으로 제한 됩니다. 클라이언트 IP 주소는 방화벽 규칙에서 허용 되어야 합니다. 자세히 알아보려면 [공용 액세스 (허용 된 IP 주소)](./concepts-networking.md#public-access-allowed-ip-addresses)를 참조 하세요. 방화벽 규칙은 서버를 만들 때 정의할 수 있습니다 (권장). 하지만 나중에 추가할 수도 있습니다.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

[Azure Cloud Shell](../../cloud-shell/overview.md)은 이 문서의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다.

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/bash](https://shell.azure.com/bash)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 열 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, **Enter**를 선택하여 실행합니다.

CLI를 로컬로 설치하고 사용하려면 이 빠른 시작에서 Azure CLI 버전 2.0 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

[az login](/cli/azure/reference-index#az-login) 명령을 사용하여 계정에 로그인해야 합니다. Azure 계정의 **구독 id** 를 참조 하는 **id** 속성을 기록해 둡니다.

```azurecli-interactive
az login
```

[az account set](/cli/azure/account#az-account-set) 명령을 사용하여 계정에 속한 특정 구독을 선택합니다. 명령에서 **subscription** 인수 값으로 사용 하려면 **az Login** output에서 **ID** 값을 적어 둡니다. 구독이 여러 개인 경우 리소스가 과금되어야 할 적절한 구독을 선택합니다. 모든 구독을 가져오려면 [az account list](/cli/azure/account#az-account-list)를 사용합니다.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-firewall-rule-during-flexible-server-create-using-azure-cli"></a>Azure CLI를 사용 하 여 유연한 서버를 만드는 동안 방화벽 규칙 만들기

명령을 사용 하 여 `az postgres flexible-server --public access` *공용 액세스 (허용 된 IP 주소)* 로 유연한 서버를 만들고 유연한 서버를 만들 때 방화벽 규칙을 구성할 수 있습니다. **--Public 액세스** 스위치를 사용 하 여 서버에 연결할 수 있는 허용 된 IP 주소를 제공할 수 있습니다. 허용 되는 IP 주소 목록에 포함할 단일 또는 IP 주소 범위를 제공할 수 있습니다. IP 주소 범위는 대시로 구분 해야 하며 공백을 포함 하지 않습니다. 아래 예제와 같이 CLI를 사용 하 여 유연한 서버를 만드는 다양 한 옵션이 있습니다.

Azure CLI 참조 설명서를 참조 하세요. <!--FIXME --> 구성 가능한 CLI 매개 변수의 전체 목록을 참조하세요. 예를 들어 아래 명령에서 필요에 따라 리소스 그룹을 지정할 수 있습니다.

- 공용 액세스를 사용 하 여 유연한 서버를 만들고 클라이언트 IP 주소를 추가 하 여 서버에 액세스할 수 있도록 합니다.
    ```azurecli-interactive
    az postgres flexible-server create --public-access <my_client_ip>
    ```
- 공용 액세스를 사용 하 여 유연한 서버를 만들고이 서버에 액세스할 수 있는 IP 주소 범위를 추가 합니다.

    ```azurecli-interactive
    az postgres flexible-server create --public-access <start_ip_address-end_ip_address>
    ```
- 공용 액세스를 사용 하 여 유연한 서버를 만들고 Azure IP 주소의 응용 프로그램에서 유연한 서버에 연결할 수 있도록 허용
    ```azurecli-interactive
    az postgres flexible-server create --public-access 0.0.0.0
    ```
    > [!IMPORTANT]
    > 이 옵션은 다른 고객의 구독에서 연결을 포함 하 여 Azure 내의 azure 서비스 및 리소스에서이 서버로의 공용 액세스를 허용 하도록 방화벽을 구성 합니다. 이 옵션을 선택할 때 로그인 및 사용자 권한이 부여된 사용자만으로 액세스를 제한하는지 확인합니다.
    >
- - 공용 액세스를 사용 하 여 유연한 서버 만들기 및 모든 IP 주소 허용
    ```azurecli-interactive
    az postgres flexible-server create --public-access all
    ```
    >[!Note]
    > 위의 명령은 시작 IP 주소 = 0.0.0.0, 끝 IP 주소 = 255.255.255.255를 사용 하는 방화벽 규칙을 만들고 IP 주소는 차단 되지 않습니다. 인터넷의 모든 호스트에서이 서버에 액세스할 수 있습니다. 이 규칙은 임시로만 사용 하 고 중요 한 데이터가 포함 되지 않은 테스트 서버 에서만 사용 하는 것이 좋습니다.
- IP 주소가 없는 공용 액세스를 사용 하 여 유연한 서버 만들기
    ```azurecli-interactive
    az postgres flexible-server create --public-access none
    ```
    >[!Note]
    > 방화벽 규칙 없이는 서버를 만들지 않는 것이 좋습니다. 방화벽 규칙을 추가 하지 않으면 클라이언트는 서버에 연결할 수 없습니다.
## <a name="create-and-manage-firewall-rule-after-server-create"></a>서버를 만든 후 방화벽 규칙 만들기 및 관리
**Az postgres 신축 서버 방화벽** 규칙을 사용 하 여 Azure CLI에서 방화벽 규칙을 만들고, 삭제, 나열, 표시 및 업데이트할 수 있습니다.

명령:
- **만들기**: 유연한 서버 방화벽 규칙을 만듭니다.
- **목록**: 유연한 서버 방화벽 규칙을 나열 합니다.
- **업데이트**: 유연한 서버 방화벽 규칙을 업데이트 합니다.
- **표시**: 유연한 서버 방화벽 규칙의 세부 정보를 표시 합니다.
- **삭제**: 유연한 서버 방화벽 규칙을 삭제 합니다.

Azure CLI 참조 설명서를 참조 하세요. <!--FIXME --> 구성 가능한 CLI 매개 변수의 전체 목록을 참조하세요. 예를 들어 아래 명령에서 필요에 따라 리소스 그룹을 지정할 수 있습니다.

### <a name="create-a-firewall-rule"></a>방화벽 규칙을 만들기
명령을 사용 `az postgres flexible-server firewall-rule create` 하 여 서버에서 새 방화벽 규칙을 만듭니다.
IP 주소 범위에 대 한 액세스를 허용 하려면 다음 예제와 같이 IP 주소를 시작 IP 주소와 끝 IP 주소로 제공 합니다.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

단일 IP 주소에 대 한 액세스를 허용 하려면이 예제와 같이 단일 IP 주소만 제공 하면 됩니다.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 1.1.1.1
```

Azure IP 주소의 응용 프로그램이 유연한 서버에 연결할 수 있도록 하려면이 예제와 같이 IP 주소 0.0.0.0을 시작 IP로 제공 합니다.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 0.0.0.0
```

> [!IMPORTANT]
> 이 옵션은 다른 고객의 구독에서 연결을 포함 하 여 Azure 내의 azure 서비스 및 리소스에서이 서버로의 공용 액세스를 허용 하도록 방화벽을 구성 합니다. 이 옵션을 선택할 때 로그인 및 사용자 권한이 부여된 사용자만으로 액세스를 제한하는지 확인합니다.
> 
성공하면 각각의 create 명령 출력은 만든 방화벽 규칙의 세부 정보를 JSON 형식(기본값)으로 나열합니다. 오류가 있는 경우 출력은 오류 메시지 텍스트를 대신 표시합니다.

### <a name="list-firewall-rules"></a>방화벽 규칙 나열 
명령을 사용 `az postgres flexible-server firewall-rule list` 하 여 서버에 있는 기존 서버 방화벽 규칙을 나열 합니다. 서버 이름 특성은 **--name** 스위치에 지정 되어 있습니다. 
```azurecli-interactive
az postgres flexible-server firewall-rule list --name mydemoserver
```
출력에 기본적으로 JSON 형식으로 규칙(있는 경우)이 나열됩니다. --Output table * * 스위치를 사용 하 여 결과를 더 읽기 쉬운 테이블 형식으로 출력할 수 있습니다.
```azurecli-interactive
az postgres flexible-server firewall-rule list --name mydemoserver --output table
```

### <a name="update-a-firewall-rule"></a>방화벽 규칙 업데이트
명령을 사용 `az postgres flexible-server firewall-rule update` 하 여 서버에서 기존 방화벽 규칙을 업데이트 합니다. 기존 방화벽 규칙의 이름과 업데이트할 시작 IP 주소 및 끝 IP 주소 특성을 입력으로 제공 합니다.
```azurecli-interactive
az postgres flexible-server firewall-rule update --name mydemoserver --rule-name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
성공하면 명령 출력은 업데이트한 방화벽 규칙의 세부 정보를 기본적으로 JSON 형식으로 나열합니다. 오류가 있는 경우 출력은 오류 메시지 텍스트를 대신 표시합니다.

> [!NOTE]
> 방화벽 규칙이 존재하지 않는 경우 업데이트 명령에 의해 생성됩니다.
### <a name="show-firewall-rule-details"></a>방화벽 규칙 세부 정보 표시
명령을 사용 `az postgres flexible-server firewall-rule show` 하 여 서버에서 기존 방화벽 규칙 세부 정보를 표시 합니다. 기존 방화벽 규칙의 이름을 입력합니다.
```azurecli-interactive
az postgres flexible-server firewall-rule show --name mydemoserver --rule-name FirewallRule1
```
성공하면 명령 출력은 지정한 방화벽 규칙의 세부 정보를 기본적으로 JSON 형식으로 나열합니다. 오류가 있는 경우 출력은 오류 메시지 텍스트를 대신 표시합니다.

### <a name="delete-a-firewall-rule"></a>방화벽 규칙 삭제
명령을 사용 `az postgres flexible-server firewall-rule delete` 하 여 서버에서 기존 방화벽 규칙을 삭제 합니다. 기존 방화벽 규칙의 이름을 제공합니다.
```azurecli-interactive
az postgres flexible-server firewall-rule delete --name mydemoserver --rule-name FirewallRule1
```
성공하면 출력은 없습니다. 실패하면 오류 메시지 텍스트가 표시됩니다.

## <a name="next-steps"></a>다음 단계
- [Azure Database for PostgreSQL 유연한 서버에서 네트워킹](./concepts-networking.md) 에 대 한 자세한 정보
- [Azure Database for PostgreSQL 유연한 서버 방화벽 규칙](./concepts-networking.md#public-access-allowed-ip-addresses) 에 대해 자세히 알아보기
- [Azure Portal를 사용 하 여 Azure Database for PostgreSQL 유연한 서버 방화벽 규칙을 만들고 관리](./how-to-manage-firewall-portal.md)합니다.
