---
title: Azure Active Directory B2C를 사용하여 ASP.NET Core Blazor WebAssembly 호스트된 앱 보호
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 123b664b87eb41a8f07344608713d9aed7a0aa37
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402249"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 ASP.NET Core Blazor WebAssembly 호스트된 앱 보호

작성자: [Javier Calvarro Nelson](https://github.com/javiercn) 및 [Luke Latham](https://github.com/guardrex)

이 문서에서는 인증을 위해 [AAD(Azure Active Directory) B2C](/azure/active-directory-b2c/overview)를 사용하는 Blazor WebAssembly 독립 실행형 앱을 만드는 방법을 설명합니다.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>AAD B2C에 앱을 등록하고 솔루션 만들기

### <a name="create-a-tenant"></a>테넌트 만들기

[자습서: Azure Active Directory B2C 테넌트 만들기](/azure/active-directory-b2c/tutorial-create-tenant)의 지침에 따라 AAD B2C 테넌트를 만듭니다.

다음과 같은 정보를 기록해 둡니다.

* AAD B2C 인스턴스(예: 후행 슬래시를 포함하는 `https://contoso.b2clogin.com/`)
* AAD B2C 테넌트 도메인(예: `contoso.onmicrosoft.com`)

### <a name="register-a-server-api-app"></a>서버 API 앱 등록

[자습서: Azure Active Directory B2C에서 애플리케이션 등록](/azure/active-directory-b2c/tutorial-register-applications)의 지침에 따라 ‘서버 API 앱’에 대해 AAD 앱을 등록하고 다음을 수행합니다.

1. **Azure Active Directory** > **앱 등록**에서 **새 등록**을 선택합니다.
1. 앱의 **이름**을 지정합니다(예: **Blazor Server AAD B2C**).
1. **지원되는 계정 유형**으로 다중 테넌트 옵션 **조직 디렉터리 또는 ID 공급자의 계정. Azure AD B2C를 사용하여 사용자 인증**을 선택합니다.
1. 이 시나리오에서는 ‘서버 API 앱’에 **리디렉션 URI**가 필요하지 않으므로 드롭다운이 **웹**으로 설정된 상태로 두고 리디렉션 URI를 입력하지 않습니다.
1. **사용 권한** > **openid 및 offline_access 권한에 대한 관리자 동의 허용**이 사용하도록 설정되었는지 확인합니다.
1. **등록**을 선택합니다.

다음과 같은 정보를 기록해 둡니다.

* ‘서버 API 앱’ 애플리케이션 ID(클라이언트 ID)(예: `11111111-1111-1111-1111-111111111111`)
* 디렉터리 ID(테넌트 ID)(예: `222222222-2222-2222-2222-222222222222`)
* AAD 테넌트 도메인(예: `contoso.onmicrosoft.com`) 도메인은 Azure Portal에서 등록된 앱의 **브랜딩** 블레이드에 **게시자 도메인**으로 표시되어 있습니다.

**API 표시**에서:

1. **범위 추가**를 선택합니다.
1. **저장 및 계속**을 선택합니다.
1. **범위 이름**을 지정합니다(예: `API.Access`).
1. **관리자 동의 표시 이름**을 지정합니다(예: `Access API`).
1. **관리자 동의 설명**을 지정합니다(예: `Allows the app to access server app API endpoints.`).
1. **상태**가 **사용**으로 설정되었는지 확인합니다.
1. **범위 추가**를 선택합니다.

다음과 같은 정보를 기록해 둡니다.

* 앱 ID URI(예: `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111` 또는 사용자가 지정한 사용자 지정 값)
* 기본 범위(예: `API.Access`)

### <a name="register-a-client-app"></a>클라이언트 앱 등록

다시 한번 [자습서: Azure Active Directory B2C에서 애플리케이션 등록](/azure/active-directory-b2c/tutorial-register-applications)의 지침에 따라 ‘클라이언트 앱’에 대해 AAD 앱을 등록하고 다음을 수행합니다.

1. **Azure Active Directory** > **앱 등록**에서 **새 등록**을 선택합니다.
1. 앱의 **이름**을 지정합니다(예: **Blazor 클라이언트 AAD B2C**).
1. **지원되는 계정 유형**으로 다중 테넌트 옵션 **조직 디렉터리 또는 ID 공급자의 계정. Azure AD B2C를 사용하여 사용자 인증**을 선택합니다.
1. **리디렉션 URI** 드롭다운은 **웹**으로 설정된 상태로 두고, 리디렉션 URI를 `https://localhost:{PORT}/authentication/login-callback`으로 지정합니다. Kestrel에서 실행되는 앱의 기본 포트는 5001입니다. 앱이 다른 Kestrel 포트에서 실행되는 경우 해당 앱의 포트를 사용합니다. IIS Express의 경우, 앱에 대해 임의로 생성되는 포트를 **디버그** 패널의 서버 앱 속성에서 확인할 수 있습니다. 이 시점에는 앱이 존재하지 않고 IIS Express 포트가 알려지지 않았으므로 앱이 만들어진 후에 이 단계로 돌아와서 리디렉션 URI를 업데이트하세요. [앱 만들기](#create-the-app) 섹션에서 IIS Express 사용자에게 리디렉션 URI를 업데이트하라고 알려 주는 설명이 표시됩니다.
1. **사용 권한** > **openid 및 offline_access 권한에 대한 관리자 동의 허용**이 사용하도록 설정되었는지 확인합니다.
1. **등록**을 선택합니다.

애플리케이션 ID(클라이언트 ID)를 기록해 둡니다(예: `11111111-1111-1111-1111-111111111111`).

**인증** > **플랫폼 구성** > **웹**에서:

1. `https://localhost:{PORT}/authentication/login-callback`의 **리디렉션 URI**가 있는지 확인합니다.
1. **암시적 허용**에서는 **액세스 토큰** 및 **ID 토큰**의 확인란을 선택합니다.
1. 이 환경에서는 앱의 나머지 기본값을 그대로 사용해도 좋습니다.
1. **저장** 단추를 선택합니다.

**API 사용 권한**에서:

1. **사용 권한 추가**를 선택하고 **내 API**를 선택합니다.
1. **이름** 열에서 ‘서버 API 앱’을 선택합니다(예: **Blazor Server AAD B2C**).
1. **API** 목록을 엽니다.
1. API에 대한 액세스를 사용하도록 설정합니다(예: `API.Access`).
1. **권한 추가**를 선택합니다.
1. **{테넌트 이름}에 대한 관리자 동의 허용** 단추를 선택합니다. **예**를 선택하여 확인합니다.

**홈** > **Azure AD B2C** > **사용자 흐름**에서:

[가입 및 로그인 사용자 흐름 만들기](/azure/active-directory-b2c/tutorial-create-user-flows)

최소한 **애플리케이션 클레임** > **표시 이름** 사용자 특성을 선택하여 `LoginDisplay` 구성 요소(`Shared/LoginDisplay.razor`)의 `context.User.Identity.Name`을 채웁니다.

앱에 대해 만들어진 가입 및 로그인 사용자 흐름 이름을 기록해 둡니다(예: `B2C_1_signupsignin`).

### <a name="create-the-app"></a>앱 만들기

다음 명령에서 자리 표시자를 앞에서 기록해 둔 정보로 바꾸고 명령 셸에서 명령을 실행합니다.

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

출력 위치를 지정하려면 명령에 경로와 함께 출력 옵션을 포함합니다(예: `-o BlazorSample`). 출력 위치는 프로젝트 폴더가 존재하지 않는 경우 프로젝트 폴더를 만듭니다. 폴더 이름도 프로젝트 이름의 일부가 됩니다.

> [!NOTE]
> 앱 ID URI를 `app-id-uri` 옵션으로 전달합니다. 단, 클라이언트 앱에서 구성 변경이 필요할 수 있습니다. 이에 대해서는 [액세스 토큰 범위](#access-token-scopes) 섹션에서 설명합니다.
>
> 호스팅형 Blazor 템플릿에 의해 설정된 범위에서는 앱 ID URI 호스트가 반복되어 있을 수 있습니다. ‘클라이언트 앱’의 `Program.Main`(`Program.cs`)에서 `DefaultAccessTokenScopes` 컬렉션에 대해 구성된 범위가 올바른지 확인합니다.

> [!NOTE]
> Azure Portal에서 ‘클라이언트 앱’의 **인증** > **플랫폼 구성** > **웹** > **리디렉션 URI**는 Kestrel 서버에서 기본 설정으로 실행되는 앱의 경우 포트 5001로 구성됩니다.
>
> ‘클라이언트 앱’이 임의의 IIS Express 포트에서 실행되는 경우 앱의 포트는 **디버그** 패널의 ‘서버 앱’ 속성에서 확인할 수 있습니다. 
>
> 앞에서 ‘클라이언트 앱’의 알려진 포트로 포트가 구성되지 않았다면 Azure Portal에서 ‘클라이언트 앱’의 등록으로 돌아가서 리디렉션 URI를 올바른 포트로 업데이트합니다. 

## <a name="server-app-configuration"></a>서버 앱 구성

‘이 섹션은 솔루션의 **`Server`** 앱에 적용됩니다.’

### <a name="authentication-package"></a>인증 패키지

ASP.NET Core Web API에 대한 호출의 인증 및 권한 부여 지원은 [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) 패키지에서 제공합니다.

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a>인증 서비스 지원

`AddAuthentication` 메서드는 앱 내에서 인증 서비스를 설정하고 JWT 전달자 처리기를 기본 인증 메서드로 구성합니다. <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> 메서드는 JWT 전달자 처리기에서 Azure Active Directory B2C에서 내보낸 토큰의 유효성을 검사하는 데 필요한 특정 매개 변수를 설정합니다.

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> 및 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>은 다음을 수행합니다.

* 앱이 수신 요청에 대해 토큰의 구문 분석 및 유효성 검사를 시도하도록 합니다.
* 올바른 자격 증명 없이 보호된 리소스에 액세스하려는 요청은 모두 실패하도록 합니다.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>User.Identity.Name

기본적으로 `User.Identity.Name`은 채워지지 않습니다.

앱이 `name` 클레임 유형으로부터 값을 받도록 구성하려면 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions>의 <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType>을 구성합니다.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>앱 설정

`appsettings.json` 파일은 액세스 토큰의 유효성을 검사하는 데 사용되는 JWT 전달자 처리기를 구성하기 위한 옵션을 포함합니다.

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

예:

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a>WeatherForecast 컨트롤러

WeatherForecast 컨트롤러(*Controllers/WeatherForecastController.cs*)는 컨트롤러에 적용된 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성을 사용하여, 보호된 API를 노출합니다. 다음과 같은 사항을 이해하는 것이 **중요합니다**.

* 이 API 컨트롤러의 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성은 무단 액세스로부터 이 API 컨트롤러를 보호하는 유일한 항목입니다.
* Blazor WebAssembly 앱에서 사용되는 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성은 앱이 올바르게 작동하려면 사용자에게 권한이 부여되어야 한다는 힌트만 앱에 전달합니다.

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>클라이언트 앱 구성

‘이 섹션은 솔루션의 **`Client`** 앱에 적용됩니다.’

### <a name="authentication-package"></a>인증 패키지

앱이 개별 B2C 계정을 사용하도록 만들어진 경우(`IndividualB2C`) 해당 앱은 자동으로 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview)([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/))에 대한 패키지 참조를 받습니다. 패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 데 사용할 토큰을 가져올 수 있도록 지원하는 기본 형식 세트를 제공합니다.

앱에 인증을 추가하는 경우에는 패키지를 앱의 프로젝트 파일에 수동으로 추가합니다.

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) 패키지는 타동적으로 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 패키지를 앱에 추가합니다.

### <a name="authentication-service-support"></a>인증 서비스 지원

서버 프로젝트로 요청을 전송할 때 액세스 토큰을 포함하는 <xref:System.Net.Http.HttpClient> 인스턴스에 대한 지원이 추가됩니다.

`Program.cs`:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

자리 표시자 `{APP ASSEMBLY}`는 앱의 어셈블리 이름입니다(예: `BlazorSample.ServerAPI`).

사용자 인증에 대한 지원은 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) 패키지에서 제공하는 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 확장 메서드를 통해 서비스 컨테이너에 등록됩니다. 이 메서드는 앱이 IP(Identity 공급자)와 상호 작용하는 데 필요한 서비스를 설정합니다.

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 메서드는 콜백을 받아서 앱을 인증하는 데 필요한 매개 변수를 구성합니다. 앱을 구성하는 데 필요한 값은 앱을 등록할 때 Azure Portal AAD 구성에서 얻을 수 있습니다.

구성은 `wwwroot/appsettings.json` 파일에서 제공합니다.

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

예:

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a>액세스 토큰 범위

기본 액세스 토큰 범위는 다음과 같은 액세스 토큰 범위 목록을 나타냅니다.

* 기본적으로 로그인 요청에 포함되어 있음.
* 인증 직후에 액세스 토큰을 프로비저닝하는 데 사용됨.

모든 범위는 Azure Active Directory 규칙에 따라 동일한 앱에 속해 있어야 합니다. 필요에 따라 추가 API 앱에 대한 추가 범위가 추가될 수 있습니다.

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

자세한 내용은 ‘추가 시나리오’ 문서의 다음 섹션을 참조하세요.

* [추가 액세스 토큰 요청](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [나가는 요청에 토큰 연결](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a>Imports 파일

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>인덱스 페이지

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>App 구성 요소

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin 구성 요소

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay 구성 요소

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Authentication 구성 요소

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>FetchData 구성 요소

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>앱 실행

서버 프로젝트에서 앱을 실행합니다. Visual Studio를 사용하는 경우 다음 중 하나를 수행합니다.

* 도구 모음의 **시작 프로젝트** 드롭다운 목록을 ‘서버 API 앱’으로 설정하고 **실행** 단추를 선택합니다.
* **솔루션 탐색기**에서 서버 프로젝트를 선택하고 도구 모음에서 **실행** 단추를 선택하거나 **디버그** 메뉴에서 앱을 시작합니다.

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/security/webassembly/additional-scenarios>
* [보안 기본 클라이언트가 있는 앱의 인증되지 않거나 권한이 부여되지 않은 웹 API 요청](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [자습서: Azure Active Directory B2C 테넌트 만들기](/azure/active-directory-b2c/tutorial-create-tenant)
* [Microsoft ID 플랫폼 설명서](/azure/active-directory/develop/)
