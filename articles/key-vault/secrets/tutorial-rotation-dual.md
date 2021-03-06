---
title: 두 개의 자격 증명 세트를 사용하는 리소스에 대한 순환 자습서
description: 이 자습서를 사용하여 두 개의 인증 자격 증명 세트를 사용하는 리소스의 비밀을 자동으로 순환하는 방법을 알아봅니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.openlocfilehash: 72541b8d8f8d8865c680c36f7f84cd91a4ce8ba2
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903329"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-have-two-sets-of-authentication-credentials"></a>두 개의 인증 자격 증명 세트를 사용하는 리소스의 비밀 순환 자동화

Azure 서비스를 인증하는 가장 좋은 방법은 [관리 ID](../general/authentication.md)를 사용하는 것이지만, 이 방법을 사용할 수 없는 시나리오도 있습니다. 이러한 경우 액세스 키 또는 암호가 사용됩니다. 액세스 키와 암호는 자주 순환해야 합니다.

이 자습서에서는 두 개의 인증 자격 증명 세트를 사용하는 데이터베이스 및 서비스의 비밀을 정기적으로 자동 순환하는 방법을 보여줍니다. 특히 이 자습서에서는 Azure Key Vault에 비밀로 저장된 Azure Storage 계정 키를 순환하는 방법을 보여줍니다. Azure Event Grid 알림에 의해 트리거되는 함수를 사용합니다. 

> [!NOTE]
> 스토리지 계정에 대한 위임된 액세스가 가능하도록 공유 액세스 서명 토큰을 제공하면 Storage 계정 키를 Key Vault에서 자동으로 관리할 수 있습니다. 액세스 키가 포함된 스토리지 계정 연결 문자열이 필요한 서비스가 있습니다. 이러한 시나리오의 경우 이 솔루션을 사용하는 것이 좋습니다.

이 자습서에서 설명하는 순환 솔루션은 다음과 같습니다. 

![순환 솔루션을 보여주는 다이어그램](../media/secrets/rotation-dual/rotation-diagram.png)

이 솔루션에서 Azure Key Vault는 스토리지 계정 개별 액세스 키를 후속 버전에서 기본 키와 보조 키 사이에서 교대로 반복되는 동일한 비밀 버전으로 저장합니다. 한 액세스 키가 최신 버전의 비밀에 저장되면 대체 키가 다시 생성되어 Key Vault에 최신 버전의 비밀로 추가됩니다. 이 솔루션은 다시 생성된 최신 키로 새로 고치는 애플리케이션 전체 순환 주기를 제공합니다. 

1. 비밀 만료 30일 전에 Key Vault는 만료 임박 이벤트를 Event Grid에 게시합니다.
1. Event Grid는 이벤트 구독을 확인하고 HTTP POST를 사용하여 이벤트를 구독하는 함수 앱 엔드포인트를 호출합니다.
1. 함수 앱은 (최신 키가 아닌) 대체 키를 식별하고 스토리지 계정을 호출하여 대체 키를 다시 생성합니다.
1. 함수 앱은 다시 생성된 새 키를 Azure Key Vault에 새 버전의 비밀로 추가합니다.

## <a name="prerequisites"></a>사전 요구 사항
* Azure 구독 [체험 계정을 만듭니다.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure Key Vault.
* Azure 스토리지 계정 2개

기존 키 자격 증명 모음 및 기존 스토리지 계정이 없는 경우 이 배포 링크를 사용하면 됩니다.

[![[Azure에 배포]라는 레이블이 지정된 링크](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. **리소스 그룹** 에서 **새로 만들기** 를 선택합니다. 그룹 이름을 **akvrotation** 으로 지정한 다음, **확인** 을 선택합니다.
1. **검토 + 만들기** 를 선택합니다.
1. **만들기** 를 선택합니다.

    ![리소스 그룹을 만드는 방법을 보여주는 스크린샷](../media/secrets/rotation-dual/dual-rotation-1.png)

이제 키 자격 증명 모음과 스토리지 계정 2개가 있습니다. Azure CLI에서 다음 명령을 실행하여 이 설정을 확인할 수 있습니다.

```azurecli
az resource list -o table -g akvrotation
```

다음 출력과 비슷한 결과가 표시됩니다.

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv         akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotationstorage     akvrotation      eastus      Microsoft.Storage/storageAccounts
akvrotationstorage2    akvrotation      eastus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-the-key-rotation-function"></a>키 순환 함수 만들기 및 배포

다음으로, 시스템 관리 ID와 기타 필수 구성 요소를 사용하여 함수 앱을 만듭니다. 그리고 스토리지 계정 키의 순환 함수를 배포합니다.

함수 앱 순환 함수에는 다음과 같은 구성 요소 및 구성이 필요합니다.
- Azure App Service 계획
- 함수 앱 트리거를 관리하는 스토리지 계정
- Key Vault의 비밀에 액세스하는 액세스 정책
- 스토리지 계정 액세스 키에 액세스할 수 있도록 함수 앱에 할당된 스토리지 계정 키 운영자 서비스 역할
- 이벤트 트리거와 HTTP 트리거를 사용하는 키 순환 함수(주문형 순환)
- **SecretNearExpiry** 이벤트에 대한 Event Grid 이벤트 구독

1. Azure 템플릿 배포 링크를 선택합니다. 

   [![Azure 템플릿 배포 링크](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FFunction%2Fazuredeploy.json)

1. **리소스 그룹** 목록에서 **akvrotation** 을 선택합니다.
1. **스토리지 계정 RG** 상자에 스토리지 계정이 있는 리소스 그룹의 이름을 입력합니다. 키 순환 함수를 배포할 리소스 그룹과 동일한 리소스 그룹에 스토리지 계정이 이미 있는 경우 기본값 **[resourceGroup().name]** 을 유지합니다.
1. **스토리지 계정 이름** 상자에 순환할 액세스 키가 포함된 스토리지 계정의 이름을 입력합니다.
1. **Key Vault RG** 상자에 키 자격 증명 모음이 있는 리소스 그룹의 이름을 입력합니다. 키 순환 함수를 배포할 리소스 그룹과 동일한 리소스 그룹에 키 자격 증명 모음이 이미 있는 경우 기본값 **[resourceGroup().name]** 을 유지합니다.
1. **Key Vault 이름** 상자에 키 자격 증명 모음의 이름을 입력합니다.
1. **함수 앱 이름** 상자에 함수 앱의 이름을 입력합니다.
1. **비밀 이름** 상자에 액세스 키를 저장할 비밀의 이름을 입력합니다.
1. **리포지토리 URL** 상자에 함수 코드의 GitHub 위치( **https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell.git** )를 입력합니다.
1. **검토 + 만들기** 를 선택합니다.
1. **만들기** 를 선택합니다.

   ![첫 번째 스토리지 계정을 만드는 방법을 보여주는 스크린샷](../media/secrets/rotation-dual/dual-rotation-2.png)

위의 단계를 마치면 스토리지 계정, 서버 팜, 함수 앱 및 Application Insights가 생깁니다. 배포가 완료되면 다음 페이지가 표시됩니다. ![배포 완료 페이지를 보여주는 스크린샷](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> 오류가 발생하는 경우 **재배포** 를 선택하여 구성 요소 배포를 완료할 수 있습니다.


순환 함수의 배포 템플릿과 코드는 [GitHub](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)에서 찾을 수 있습니다.

## <a name="add-the-storage-account-access-keys-to-key-vault"></a>Key Vault에 스토리지 계정 액세스 키 추가

먼저 사용자에게 **비밀 관리** 권한을 부여하도록 액세스 정책을 설정합니다.

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

이제 스토리지 계정 액세스 키를 값으로 사용하여 새 비밀을 만들 수 있습니다. 또한 순환 함수가 스토리지 계정에서 키를 다시 생성하려면 스토리지 계정 리소스 ID, 비밀 유효 기간, 비밀에 추가할 키 ID가 필요합니다.

스토리지 계정 리소스 ID를 확인합니다. 이 값은 `id` 속성에서 찾을 수 있습니다.
```azurecli
az storage account show -n akvrotationstorage
```

key 값을 가져올 수 있도록 다음과 같이 스토리지 계정 액세스 키를 나열합니다.

```azurecli
az storage account keys list -n akvrotationstorage 
```

`key1Value` 및 `storageAccountResourceId`에 대해 검색된 값을 사용하여 다음 명령을 실행합니다.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey --vault-name akvrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

만료 날짜가 짧은 비밀을 만들면 `SecretNearExpiry` 이벤트가 몇 분 내에 게시됩니다. 이 이벤트는 비밀을 순환하는 함수를 트리거합니다.

스토리지 계정 키와 Key Vault 비밀을 검색하고 비교하여 액세스 키가 다시 생성되었는지 확인할 수 있습니다.

다음 명령을 사용하여 비밀 정보를 가져옵니다.
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey
```
`keyName`을 대체하도록 `CredentialId`가 업데이트되고 `value`가 다시 생성됩니다. ![첫 번째 스토리지 계정에 대한 a z keyvault secret show 명령의 출력을 보여주는 스크린샷](../media/secrets/rotation-dual/dual-rotation-4.png)

다음과 같이 액세스 키를 검색하여 값을 비교합니다.
```azurecli
az storage account keys list -n akvrotationstorage 
```
![첫 번째 스토리지 계정에 대한 a z storage account keys list 명령의 출력을 보여주는 스크린샷](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-storage-accounts-for-rotation"></a>순환할 스토리지 계정 추가

동일한 함수 앱을 다시 사용하여 여러 스토리지 계정의 키를 순환할 수 있습니다. 

순환하려는 기존 함수에 스토리지 계정 키를 추가하려면 다음이 필요합니다.
- 스토리지 계정 액세스 키에 액세스할 수 있도록 함수 앱에 할당된 스토리지 계정 키 운영자 서비스 역할
- **SecretNearExpiry** 이벤트에 대한 Event Grid 이벤트 구독

1. Azure 템플릿 배포 링크를 선택합니다. 

   [![Azure 템플릿 배포 링크](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FAdd-Event-Subscriptions%2Fazuredeploy.json)

1. **리소스 그룹** 목록에서 **akvrotation** 을 선택합니다.
1. **스토리지 계정 이름** 상자에 순환할 액세스 키가 포함된 스토리지 계정의 이름을 입력합니다.
1. **Key Vault 이름** 상자에 키 자격 증명 모음의 이름을 입력합니다.
1. **함수 앱 이름** 상자에 함수 앱의 이름을 입력합니다.
1. **비밀 이름** 상자에 액세스 키를 저장할 비밀의 이름을 입력합니다.
1. **검토 + 만들기** 를 선택합니다.
1. **만들기** 를 선택합니다.

   ![추가 스토리지 계정을 만드는 방법을 보여주는 스크린샷](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Key Vault에 또 다른 스토리지 계정 액세스 키 추가

스토리지 계정 리소스 ID를 확인합니다. 이 값은 `id` 속성에서 찾을 수 있습니다.
```azurecli
az storage account show -n akvrotationstorage2
```

key2 값을 가져올 수 있도록 다음과 같이 스토리지 계정 액세스 키를 나열합니다.

```azurecli
az storage account keys list -n akvrotationstorage2 
```

`key2Value` 및 `storageAccountResourceId`에 대해 검색된 값을 사용하여 다음 명령을 실행합니다.

```azurecli
tomorrowDate=`date -d tomorrow -Iseconds -u | awk -F'+' '{print $1"Z"}'`
az keyvault secret set --name storageKey2 --vault-name akvrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

다음 명령을 사용하여 비밀 정보를 가져옵니다.
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey2
```
`keyName`을 대체하도록 `CredentialId`가 업데이트되고 `value`가 다시 생성됩니다. ![두 번째 스토리지 계정에 대한 a z keyvault secret show 명령의 출력을 보여주는 스크린샷](../media/secrets/rotation-dual/dual-rotation-8.png)

다음과 같이 액세스 키를 검색하여 값을 비교합니다.
```azurecli
az storage account keys list -n akvrotationstorage 
```
![두 번째 스토리지 계정에 대한 a z storage account keys list 명령의 출력을 보여주는 스크린샷](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="key-vault-dual-credential-rotation-functions"></a>Key Vault 이중 자격 증명 순환 함수

- [스토리지 계정](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Redis cache](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="next-steps"></a>다음 단계
- 개요: [Azure Event Grid를 사용하여 Key Vault 모니터링](../general/event-grid-overview.md)
- 방법: [Azure Portal에서 첫 번째 함수 만들기](../../azure-functions/functions-create-first-azure-function.md)
- 방법: [Key Vault 비밀 변경 시 이메일 받기](../general/event-grid-logicapps.md)
- 참조: [Azure Key Vault에 대한 Azure Event Grid 이벤트 스키마](../../event-grid/event-schema-key-vault.md)
