---
title: Azure AD에서 엔터프라이즈 앱에 대 한 사용자 로그인 사용 안 함
description: Azure Active Directory에서 사용자가 로그인하지 않도록 엔터프라이즈 애플리케이션을 비활성화하는 방법
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/12/2019
ms.author: kenwith
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0671d3dec963c0b475133881b00224cfe11e8370
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861647"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory에서 엔터프라이즈 앱에 대한 사용자 로그인 비활성화

엔터프라이즈 응용 프로그램을 사용 하지 않도록 설정 하 여 사용자가 Azure Active Directory (Azure AD)에서 로그인 할 수 없도록 하는 것이 쉽습니다. 엔터프라이즈 앱을 관리 하려면 적절 한 권한이 필요 합니다. 그리고 디렉터리에 대 한 전역 관리자 여야 합니다.

## <a name="how-do-i-disable-user-sign-ins"></a>사용자 로그인을 비활성화하려면 어떻게 합니까?

1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **모든 서비스** 를 선택하고 텍스트 상자에 **Azure Active Directory** 를 입력한 다음, **입력** 을 선택합니다.
1. **Azure Active Directory**  -   **_directoryname_*_ 창 (즉, 관리 중인 디렉터리에 대 한 Azure AD 창)에서 _* 엔터프라이즈 응용 프로그램을 선택** 합니다.
1. **엔터프라이즈 응용 프로그램-모든 응용 프로그램** 창에서 관리할 수 있는 앱 목록이 표시 됩니다. 앱을 선택합니다.
1. **_Appname_*_ 창 (즉, 제목에서 선택한 앱의 이름이 있는 창)에서 _ 속성을 선택*** 합니다.
1. **_Appname_*_-_* 속성** 창에서 **사용자가 로그인 할 수 있습니까?** 에서 **아니요** 를 선택 합니다.
1. **저장** 명령을 선택합니다.

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Azure AD PowerShell을 사용 하 여 목록에 없는 앱을 사용 하지 않도록 설정

엔터프라이즈 앱 목록에 표시 되지 않는 앱의 AppId를 알고 있는 경우 (예: 앱을 삭제 했거나 Microsoft에서 사전 인증 하는 앱으로 인해 서비스 주체를 아직 만들지 않은 경우) 앱에 대 한 서비스 주체를 수동으로 만든 다음 [AzureAD PowerShell cmdlet](/powershell/module/azuread/New-AzureADServicePrincipal)을 사용 하 여 사용 하지 않도록 설정할 수 있습니다.

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>다음 단계

* [내 그룹 모두 보기](../fundamentals/active-directory-groups-view-azure-portal.md)
* [엔터프라이즈 앱에 사용자 또는 그룹 할당](assign-user-or-group-access-portal.md)
* [엔터프라이즈 앱에서 사용자 또는 그룹 할당 제거](./assign-user-or-group-access-portal.md)
* [엔터프라이즈 앱의 이름 또는 로고 변경](./add-application-portal-configure.md)
