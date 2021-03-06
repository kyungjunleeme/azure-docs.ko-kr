---
title: 사용자 지정 정책을 사용 하 여 세션 동작 구성-Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C에서 사용자 지정 정책을 사용 하 여 세션 동작을 구성 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 31257d795dbd06da65e3d07e18a16d9bdf7e782a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961105"
---
# <a name="configure-session-behavior-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용 하 여 세션 동작 구성

Azure Active Directory B2C (Azure AD B2C)의 [sso (Single sign-on) 세션](session-overview.md) 관리를 통해 관리자는 사용자가 이미 인증 된 후 사용자와의 상호 작용을 제어할 수 있습니다. 예를 들어 관리자는 id 공급자의 선택 여부 또는 계정 세부 정보를 다시 입력 해야 하는지 여부를 제어할 수 있습니다. 이 문서에서는 Azure AD B2C에 대한 SSO 설정을 구성하는 방법을 설명합니다.

## <a name="session-behavior-properties"></a>세션 동작 속성

다음 속성을 사용하여 웹 애플리케이션 세션을 관리할 수 있습니다.

- **웹앱 세션 수명(분)** - 인증 성공 시 사용자의 브라우저에 저장된 Azure AD B2C 세션 쿠키의 수명입니다.
  - 기본값은 86400 초 (1440 분)입니다.
  - 최소 (포함) = 900 초 (15 분)
  - 최대 (포함) = 86400 초 (1440 분)
- **웹 앱 세션 제한 시간** - [세션 만료 유형](session-overview.md#session-expiry-type), *롤링*또는 *절대*입니다. 
- **Single sign-on 구성** -Azure AD B2C 테 넌 트의 여러 앱 및 사용자 흐름에 걸친 SSO (Single Sign-On) 동작의 [세션 범위](session-overview.md#session-scope) 입니다. 

## <a name="configure-the-properties"></a>속성 구성

세션 동작 및 SSO 구성을 변경하려면 [RelyingParty](relyingparty.md) 요소 내에 **UserJourneyBehaviors** 요소를 추가합니다.  **UserJourneyBehaviors** 요소는 **DefaultUserJourney** 바로 뒤에 있어야 합니다. **UserJourneyBehavors** 요소는 다음 예제와 같이 표시 됩니다.

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

## <a name="configure-sign-out-behavior"></a>로그 아웃 동작 구성

### <a name="secure-your-logout-redirect"></a>로그 아웃 리디렉션 보안

로그 아웃 한 후에는 `post_logout_redirect_uri` 응용 프로그램에 대해 지정 된 회신 url에 관계 없이 사용자가 매개 변수에 지정 된 URI로 리디렉션됩니다. 그러나 유효한를 `id_token_hint` 전달 하 고 **로그 아웃 요청에 ID 토큰 필요** 를 설정 하는 경우 Azure AD B2C는 `post_logout_redirect_uri` 리디렉션을 수행 하기 전에의 값이 응용 프로그램의 구성 된 리디렉션 uri 중 하 나와 일치 하는지 확인 합니다. 응용 프로그램에 대해 일치 하는 회신 URL이 구성 되지 않은 경우 오류 메시지가 표시 되 고 사용자가 리디렉션되지 않습니다. 

로그 아웃 요청에 ID 토큰을 요구 하려면 [RelyingParty](relyingparty.md) 요소 내에 **UserJourneyBehaviors** 요소를 추가 합니다. 그런 다음 **SingleSignOn** 요소의 **EnforceIdTokenHintOnLogout** 를로 설정 `true` 합니다. **UserJourneyBehaviors** 요소는 다음 예제와 같이 표시 됩니다.

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Tenant" EnforceIdTokenHintOnLogout="true"/>
</UserJourneyBehaviors>
```

응용 프로그램 로그 아웃 URL을 구성 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 테 넌 트가 포함 된 디렉터리를 선택 하 여 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **앱 등록**를 선택 하 고 응용 프로그램을 선택 합니다.
1. **인증**을 선택합니다.
1. **로그 아웃 URL** 텍스트 상자에 사후 로그 아웃 리디렉션 URI를 입력 하 고 **저장**을 선택 합니다.

### <a name="single-sign-out"></a>Single Sign-Out

#### <a name="configure-the-applications"></a>응용 프로그램 구성

사용자를 Azure AD B2C 로그 아웃 끝점으로 리디렉션하는 경우 (OAuth2 및 SAML 프로토콜 모두) 브라우저에서 사용자의 세션을 지웁니다 Azure AD B2C.  [Single sign-on](session-overview.md#single-sign-out)을 허용 하려면 `LogoutUrl` Azure Portal에서 응용 프로그램의를 설정 합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. 페이지의 오른쪽 위 모서리에서 계정을 클릭 하 여 Azure AD B2C 디렉터리를 선택 합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택 하 고 **앱 등록**을 선택한 다음 응용 프로그램을 선택 합니다.
1. **인증**을 선택합니다.
1. **로그 아웃 URL** 텍스트 상자에 사후 로그 아웃 리디렉션 URI를 입력 하 고 **저장**을 선택 합니다.

#### <a name="configure-the-token-issuer"></a>토큰 발급자 구성 

단일 로그 아웃을 지원 하려면 JWT 및 SAML 토큰 발급자 기술 프로필은 다음을 지정 해야 합니다.

- 프로토콜 이름 (예:) `<Protocol Name="OpenIdConnect" />`
- 세션 기술 프로필에 대 한 참조입니다 (예:) `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />` .

다음 예제에서는 단일 로그 아웃으로 JWT 및 SAML 토큰 발급자를 보여 줍니다.

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C 세션](session-overview.md)에 대해 자세히 알아보세요.
