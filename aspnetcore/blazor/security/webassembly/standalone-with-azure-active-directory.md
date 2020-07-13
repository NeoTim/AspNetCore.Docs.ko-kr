---
title: Azure Active Directory를 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱 보호
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 9cd6097dfaa31a1329d3ea8ca6293b33e3bdb3c3
ms.sourcegitcommit: f7873c02c1505c99106cbc708f37e18fc0a496d1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147720"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="7d057-102">Azure Active Directory를 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱 보호</span><span class="sxs-lookup"><span data-stu-id="7d057-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="7d057-103">작성자: [Javier Calvarro Nelson](https://github.com/javiercn) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7d057-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7d057-104">인증을 위해 [AAD(Azure Active Directory)](https://azure.microsoft.com/services/active-directory/)를 사용하는 Blazor WebAssembly 독립 실행형 앱을 만들려면:</span><span class="sxs-lookup"><span data-stu-id="7d057-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="7d057-105">[AAD 테넌트 및 웹 애플리케이션 만들기](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="7d057-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="7d057-106">Azure Portal의 **Azure Active Directory** > **앱 등록** 영역에서 AAD 앱을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="7d057-107">앱의 **이름**을 지정합니다(예: **Blazor 독립 실행형 AAD**).</span><span class="sxs-lookup"><span data-stu-id="7d057-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="7d057-108">**지원되는 계정 유형**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="7d057-109">이 환경에서는 **이 조직 디렉터리의 계정만**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="7d057-110">**리디렉션 URI** 드롭다운은 **웹**으로 설정된 상태로 두고, 리디렉션 URI를 `https://localhost:{PORT}/authentication/login-callback`으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-110">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="7d057-111">Kestrel에서 실행되는 앱의 기본 포트는 5001입니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-111">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="7d057-112">앱이 다른 Kestrel 포트에서 실행되는 경우 해당 앱의 포트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-112">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="7d057-113">IIS Express의 경우, 앱에 대해 임의로 생성되는 포트를 **디버그** 패널의 앱 속성에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-113">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="7d057-114">이 시점에는 앱이 존재하지 않고 IIS Express 포트가 알려지지 않았으므로 앱이 만들어진 후에 이 단계로 돌아와서 리디렉션 URI를 업데이트하세요.</span><span class="sxs-lookup"><span data-stu-id="7d057-114">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="7d057-115">이 항목의 뒷부분에서 IIS Express 사용자에게 리디렉션 URI를 업데이트하라고 알려 주는 설명이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-115">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="7d057-116">**사용 권한** > **openid 및 offline_access 권한에 대한 관리자 동의 허용** 확인란을 선택 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-116">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="7d057-117">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-117">Select **Register**.</span></span>

<span data-ttu-id="7d057-118">다음과 같은 정보를 기록해 둡니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-118">Record the following information:</span></span>

* <span data-ttu-id="7d057-119">애플리케이션(클라이언트) ID(예: `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span><span class="sxs-lookup"><span data-stu-id="7d057-119">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="7d057-120">디렉터리(테넌트) ID(예: `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span><span class="sxs-lookup"><span data-stu-id="7d057-120">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="7d057-121">**인증** > **플랫폼 구성** > **웹**에서:</span><span class="sxs-lookup"><span data-stu-id="7d057-121">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="7d057-122">`https://localhost:{PORT}/authentication/login-callback`의 **리디렉션 URI**가 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-122">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="7d057-123">**암시적 허용**에서는 **액세스 토큰** 및 **ID 토큰**의 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-123">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="7d057-124">이 환경에서는 앱의 나머지 기본값을 그대로 사용해도 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-124">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="7d057-125">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-125">Select the **Save** button.</span></span>

<span data-ttu-id="7d057-126">빈 폴더에 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-126">Create the app in an empty folder.</span></span> <span data-ttu-id="7d057-127">다음 명령에서 자리 표시자를 앞에서 기록해 둔 정보로 바꾸고 명령 셸에서 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="7d057-128">자리표시자</span><span class="sxs-lookup"><span data-stu-id="7d057-128">Placeholder</span></span>   | <span data-ttu-id="7d057-129">Azure Portal 이름</span><span class="sxs-lookup"><span data-stu-id="7d057-129">Azure portal name</span></span>       | <span data-ttu-id="7d057-130">예제</span><span class="sxs-lookup"><span data-stu-id="7d057-130">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="7d057-131">애플리케이션(클라이언트) ID</span><span class="sxs-lookup"><span data-stu-id="7d057-131">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | <span data-ttu-id="7d057-132">디렉터리(테넌트) ID</span><span class="sxs-lookup"><span data-stu-id="7d057-132">Directory (tenant) ID</span></span>   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="7d057-133">`-o|--output` 옵션으로 지정된 출력 위치는 프로젝트 폴더가 없는 경우 폴더를 하나 만들고 앱 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-133">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="7d057-134">Azure Portal에서 앱의 **인증** > **플랫폼 구성** > **웹** > **리디렉션 URI**는 Kestrel 서버에서 기본 설정으로 실행되는 앱의 경우 포트 5001로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-134">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="7d057-135">앱이 임의의 IIS Express 포트에서 실행되는 경우 앱의 포트는 **디버그** 패널의 앱 속성에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-135">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="7d057-136">앞에서 앱의 알려진 포트로 포트가 구성되지 않았다면 Azure Portal에서 앱의 등록으로 돌아가서 리디렉션 URI를 올바른 포트로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-136">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="7d057-137">앱을 만든 후에는 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-137">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="7d057-138">AAD 사용자 계정을 사용하여 앱에 로그인하기.</span><span class="sxs-lookup"><span data-stu-id="7d057-138">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="7d057-139">Microsoft API에 대한 액세스 토큰 요청하기.</span><span class="sxs-lookup"><span data-stu-id="7d057-139">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="7d057-140">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7d057-140">For more information, see:</span></span>
  * [<span data-ttu-id="7d057-141">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="7d057-141">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="7d057-142">[빠른 시작: 웹 API를 공개하는 애플리케이션 구성](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7d057-142">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="7d057-143">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="7d057-143">Authentication package</span></span>

<span data-ttu-id="7d057-144">앱이 회사 또는 학교 계정을 사용하도록 만들어진 경우(`SingleOrg`) 해당 앱은 자동으로 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview)([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/))에 대한 패키지 참조를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-144">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="7d057-145">패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 데 사용할 토큰을 가져올 수 있도록 지원하는 기본 형식 세트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-145">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="7d057-146">앱에 인증을 추가하는 경우에는 패키지를 앱의 프로젝트 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-146">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="7d057-147">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) 패키지는 타동적으로 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 패키지를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-147">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="7d057-148">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="7d057-148">Authentication service support</span></span>

<span data-ttu-id="7d057-149">사용자 인증에 대한 지원은 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) 패키지에서 제공하는 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 확장 메서드를 통해 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-149">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="7d057-150">이 메서드는 앱이 IP(Identity 공급자)와 상호 작용하는 데 필요한 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-150">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="7d057-151">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="7d057-151">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="7d057-152"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 메서드는 콜백을 받아서 앱을 인증하는 데 필요한 매개 변수를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-152">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="7d057-153">앱을 구성하는 데 필요한 값은 앱을 등록할 때 AAD 구성에서 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-153">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="7d057-154">구성은 `wwwroot/appsettings.json` 파일에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-154">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="7d057-155">예:</span><span class="sxs-lookup"><span data-stu-id="7d057-155">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="7d057-156">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="7d057-156">Access token scopes</span></span>

<span data-ttu-id="7d057-157">Blazor WebAssembly 템플릿은 앱이 보안 API에 대한 액세스 토큰을 요청하도록 자동으로 구성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-157">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="7d057-158">로그인 흐름의 일부로 액세스 토큰을 프로비저닝하려면 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>의 기본 액세스 토큰 범위에 해당 범위를 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7d057-158">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="7d057-159">자세한 내용은 ‘추가 시나리오’ 문서의 다음 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7d057-159">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="7d057-160">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="7d057-160">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="7d057-161">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="7d057-161">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="7d057-162">Imports 파일</span><span class="sxs-lookup"><span data-stu-id="7d057-162">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="7d057-163">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="7d057-163">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="7d057-164">App 구성 요소</span><span class="sxs-lookup"><span data-stu-id="7d057-164">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="7d057-165">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="7d057-165">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="7d057-166">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="7d057-166">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="7d057-167">Authentication 구성 요소</span><span class="sxs-lookup"><span data-stu-id="7d057-167">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="7d057-168">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7d057-168">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="7d057-169">보안 기본 클라이언트가 있는 앱의 인증되지 않거나 권한이 부여되지 않은 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="7d057-169">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="7d057-170">Microsoft ID 플랫폼 설명서</span><span class="sxs-lookup"><span data-stu-id="7d057-170">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
