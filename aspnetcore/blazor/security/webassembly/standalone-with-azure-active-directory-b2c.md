---
title: Azure Active Directory B2C를 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱 보호
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 579e1774929219c9dc90752253c5a1ea7000cf82
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243449"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="951ed-102">Azure Active Directory B2C를 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱 보호</span><span class="sxs-lookup"><span data-stu-id="951ed-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="951ed-103">작성자: [Javier Calvarro Nelson](https://github.com/javiercn) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="951ed-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="951ed-104">인증을 위해 [AAD(Azure Active Directory) B2C](/azure/active-directory-b2c/overview)를 사용하는 Blazor WebAssembly 독립 실행형 앱을 만들려면:</span><span class="sxs-lookup"><span data-stu-id="951ed-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

<span data-ttu-id="951ed-105">다음 항목의 지침에 따라 테넌트를 만들고 Azure Portal에 웹앱을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

[<span data-ttu-id="951ed-106">AAD B2C 테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="951ed-106">Create an AAD B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)

<span data-ttu-id="951ed-107">다음과 같은 정보를 기록해 둡니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-107">Record the following information:</span></span>

* <span data-ttu-id="951ed-108">AAD B2C 인스턴스(예: 후행 슬래시를 포함하는 `https://contoso.b2clogin.com/`).</span><span class="sxs-lookup"><span data-stu-id="951ed-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span>
* <span data-ttu-id="951ed-109">AAD B2C 테넌트 도메인(예: `contoso.onmicrosoft.com`).</span><span class="sxs-lookup"><span data-stu-id="951ed-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="951ed-110">다시 한번 [자습서: Azure Active Directory B2C에서 애플리케이션 등록](/azure/active-directory-b2c/tutorial-register-applications)의 지침에 따라 ‘클라이언트 앱’에 대해 AAD 앱을 등록하고 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-110">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="951ed-111">**Azure Active Directory** > **앱 등록**에서 **새 등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-111">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="951ed-112">앱의 **이름**을 지정합니다(예: **Blazor 독립 실행형 AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="951ed-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="951ed-113">**지원되는 계정 유형**으로 다중 테넌트 옵션 **조직 디렉터리 또는 ID 공급자의 계정. Azure AD B2C를 사용하여 사용자 인증**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-113">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="951ed-114">**리디렉션 URI** 드롭다운은 **웹**으로 설정된 상태로 두고, 리디렉션 URI를 `https://localhost:{PORT}/authentication/login-callback`으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-114">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="951ed-115">Kestrel에서 실행되는 앱의 기본 포트는 5001입니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="951ed-116">앱이 다른 Kestrel 포트에서 실행되는 경우 해당 앱의 포트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-116">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="951ed-117">IIS Express의 경우, 앱에 대해 임의로 생성되는 포트를 **디버그** 패널의 앱 속성에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-117">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="951ed-118">이 시점에는 앱이 존재하지 않고 IIS Express 포트가 알려지지 않았으므로 앱이 만들어진 후에 이 단계로 돌아와서 리디렉션 URI를 업데이트하세요.</span><span class="sxs-lookup"><span data-stu-id="951ed-118">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="951ed-119">이 항목의 뒷부분에서 IIS Express 사용자에게 리디렉션 URI를 업데이트하라고 알려 주는 설명이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-119">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="951ed-120">**사용 권한** > **openid 및 offline_access 권한에 대한 관리자 동의 허용**이 사용하도록 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-120">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="951ed-121">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-121">Select **Register**.</span></span>

<span data-ttu-id="951ed-122">애플리케이션 ID(클라이언트 ID)를 기록해 둡니다(예: `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="951ed-122">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="951ed-123">**인증** > **플랫폼 구성** > **웹**에서:</span><span class="sxs-lookup"><span data-stu-id="951ed-123">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="951ed-124">`https://localhost:{PORT}/authentication/login-callback`의 **리디렉션 URI**가 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-124">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="951ed-125">**암시적 허용**에서는 **액세스 토큰** 및 **ID 토큰**의 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-125">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="951ed-126">이 환경에서는 앱의 나머지 기본값을 그대로 사용해도 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-126">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="951ed-127">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-127">Select the **Save** button.</span></span>

<span data-ttu-id="951ed-128">**홈** > **Azure AD B2C** > **사용자 흐름**에서:</span><span class="sxs-lookup"><span data-stu-id="951ed-128">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="951ed-129">가입 및 로그인 사용자 흐름 만들기</span><span class="sxs-lookup"><span data-stu-id="951ed-129">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="951ed-130">최소한 **애플리케이션 클레임** > **표시 이름** 사용자 특성을 선택하여 `LoginDisplay` 구성 요소(`Shared/LoginDisplay.razor`)의 `context.User.Identity.Name`을 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-130">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="951ed-131">앱에 대해 만들어진 가입 및 로그인 사용자 흐름 이름을 기록해 둡니다(예: `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="951ed-131">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="951ed-132">다음 명령에서 자리 표시자를 앞에서 기록해 둔 정보로 바꾸고 명령 셸에서 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-132">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
```

<span data-ttu-id="951ed-133">출력 위치를 지정하려면 명령에 경로와 함께 출력 옵션을 포함합니다(예: `-o BlazorSample`). 출력 위치는 프로젝트 폴더가 존재하지 않는 경우 프로젝트 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-133">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="951ed-134">폴더 이름도 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-134">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="951ed-135">Azure Portal에서 앱의 **인증** > **플랫폼 구성** > **웹** > **리디렉션 URI**는 Kestrel 서버에서 기본 설정으로 실행되는 앱의 경우 포트 5001로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-135">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="951ed-136">앱이 임의의 IIS Express 포트에서 실행되는 경우 앱의 포트는 **디버그** 패널의 앱 속성에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-136">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="951ed-137">앞에서 앱의 알려진 포트로 포트가 구성되지 않았다면 Azure Portal에서 앱의 등록으로 돌아가서 리디렉션 URI를 올바른 포트로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-137">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="951ed-138">앱을 만든 후에는 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-138">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="951ed-139">AAD 사용자 계정을 사용하여 앱에 로그인하기.</span><span class="sxs-lookup"><span data-stu-id="951ed-139">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="951ed-140">Microsoft API에 대한 액세스 토큰 요청하기.</span><span class="sxs-lookup"><span data-stu-id="951ed-140">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="951ed-141">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="951ed-141">For more information, see:</span></span>
  * [<span data-ttu-id="951ed-142">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="951ed-142">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="951ed-143">[빠른 시작: 웹 API를 공개하는 애플리케이션 구성](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="951ed-143">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="951ed-144">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="951ed-144">Authentication package</span></span>

<span data-ttu-id="951ed-145">앱이 개별 B2C 계정을 사용하도록 만들어진 경우(`IndividualB2C`) 해당 앱은 자동으로 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview)([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/))에 대한 패키지 참조를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-145">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="951ed-146">패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 데 사용할 토큰을 가져올 수 있도록 지원하는 기본 형식 세트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-146">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="951ed-147">앱에 인증을 추가하는 경우에는 패키지를 앱의 프로젝트 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-147">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="951ed-148">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) 패키지는 타동적으로 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 패키지를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-148">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="951ed-149">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="951ed-149">Authentication service support</span></span>

<span data-ttu-id="951ed-150">사용자 인증에 대한 지원은 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) 패키지에서 제공하는 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 확장 메서드를 통해 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-150">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="951ed-151">이 메서드는 앱이 IP(Identity 공급자)와 상호 작용하는 데 필요한 모든 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-151">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="951ed-152">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="951ed-152">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="951ed-153"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 메서드는 콜백을 받아서 앱을 인증하는 데 필요한 매개 변수를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-153">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="951ed-154">앱을 구성하는 데 필요한 값은 앱을 등록할 때 AAD 구성에서 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-154">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="951ed-155">구성은 `wwwroot/appsettings.json` 파일에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-155">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="951ed-156">예:</span><span class="sxs-lookup"><span data-stu-id="951ed-156">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="951ed-157">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="951ed-157">Access token scopes</span></span>

<span data-ttu-id="951ed-158">Blazor WebAssembly 템플릿은 앱이 보안 API에 대한 액세스 토큰을 요청하도록 자동으로 구성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-158">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="951ed-159">로그인 흐름의 일부로 액세스 토큰을 프로비저닝하려면 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>의 기본 액세스 토큰 범위에 해당 범위를 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="951ed-159">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="951ed-160">자세한 내용은 ‘추가 시나리오’ 문서의 다음 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="951ed-160">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="951ed-161">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="951ed-161">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="951ed-162">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="951ed-162">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="951ed-163">Imports 파일</span><span class="sxs-lookup"><span data-stu-id="951ed-163">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="951ed-164">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="951ed-164">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="951ed-165">App 구성 요소</span><span class="sxs-lookup"><span data-stu-id="951ed-165">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="951ed-166">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="951ed-166">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="951ed-167">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="951ed-167">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="951ed-168">Authentication 구성 요소</span><span class="sxs-lookup"><span data-stu-id="951ed-168">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="951ed-169">추가 자료</span><span class="sxs-lookup"><span data-stu-id="951ed-169">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="951ed-170">보안 기본 클라이언트가 있는 앱의 인증되지 않거나 권한이 부여되지 않은 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="951ed-170">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="951ed-171">자습서: Azure Active Directory B2C 테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="951ed-171">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="951ed-172">Microsoft ID 플랫폼 설명서</span><span class="sxs-lookup"><span data-stu-id="951ed-172">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)