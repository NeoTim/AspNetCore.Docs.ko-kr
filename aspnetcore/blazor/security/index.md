---
title: ASP.NET Core Blazor 인증 및 권한 부여
author: guardrex
description: Blazor 인증 및 권한 부여 시나리오에 대해 알아봅니다.
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
uid: blazor/security/index
ms.openlocfilehash: 14cf614bf5d4f2ad6a34c49cb08277a2deae8d00
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242952"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="eeea5-103">ASP.NET Core Blazor 인증 및 권한 부여</span><span class="sxs-lookup"><span data-stu-id="eeea5-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="eeea5-104">작성자: [Steve Sanderson](https://github.com/SteveSandersonMS) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="eeea5-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="eeea5-105">ASP.NET Core는 Blazor 앱의 보안 구성 및 관리를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-105">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="eeea5-106">Blazor Server 및 Blazor WebAssembly 앱 간의 보안 시나리오는 서로 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-106">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="eeea5-107">Blazor 서버 앱은 서버에서 실행되기 때문에 권한 부여 확인을 통해 다음을 결정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-107">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="eeea5-108">사용자에게 표시되는 UI 옵션(예: 사용자가 사용할 수 있는 메뉴 항목)</span><span class="sxs-lookup"><span data-stu-id="eeea5-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="eeea5-109">앱 영역과 구성 요소의 액세스 규칙</span><span class="sxs-lookup"><span data-stu-id="eeea5-109">Access rules for areas of the app and components.</span></span>

Blazor<span data-ttu-id="eeea5-110"> WebAssembly 앱은 클라이언트에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-110"> WebAssembly apps run on the client.</span></span> <span data-ttu-id="eeea5-111">권한 부여는 표시할 UI 옵션을 결정하는 ‘용도로만’ 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="eeea5-112">사용자가 클라이언트 쪽 확인을 수정하거나 무시할 수 있기 때문에 Blazor WebAssembly 앱은 권한 부여 액세스 규칙을 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-112">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="eeea5-113">[Razor Pages 권한 부여 규칙](xref:security/authorization/razor-pages-authorization)은 라우팅 가능한 Razor 구성 요소에 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-113">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="eeea5-114">라우팅할 수 없는 Razor 구성 요소가 [페이지에 포함](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#render-components-from-a-page-or-view)된 경우 페이지의 권한 부여 규칙은 페이지 콘텐츠의 나머지 부분과 함께 Razor 구성 요소에 간접적으로 영향을 미칩니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-114">If a non-routable Razor component is [embedded in a page](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="eeea5-115"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> 및 <xref:Microsoft.AspNetCore.Identity.UserManager%601>는 Razor 구성 요소에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-115"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="eeea5-116">인증</span><span class="sxs-lookup"><span data-stu-id="eeea5-116">Authentication</span></span>

Blazor<span data-ttu-id="eeea5-117">는 기존 ASP.NET Core 인증 메커니즘을 사용하여 사용자 ID를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-117"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="eeea5-118">정확한 메커니즘은 Blazor 앱이 호스트된 방식이 Blazor WebAssembly와 Blazor 서버 중 무엇인지에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-118">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="blazor-webassembly-authentication"></a>Blazor<span data-ttu-id="eeea5-119"> WebAssembly 인증</span><span class="sxs-lookup"><span data-stu-id="eeea5-119"> WebAssembly authentication</span></span>

<span data-ttu-id="eeea5-120">Blazor WebAssembly 앱에서는 사용자가 클라이언트 쪽 코드를 모두 수정할 수 있기 때문에 인증 확인을 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-120">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="eeea5-121">JavaScript SPA 프레임워크 또는 모든 운영 체제의 네이티브 앱을 포함하여 모든 클라이언트 쪽 앱 기술에는 동일하게 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-121">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="eeea5-122">다음을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-122">Add the following:</span></span>

* <span data-ttu-id="eeea5-123">앱의 프로젝트 파일에 대한 [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) 패키지 참조</span><span class="sxs-lookup"><span data-stu-id="eeea5-123">A package reference for [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>
* <span data-ttu-id="eeea5-124">앱의 `_Imports.razor` 파일에 대한 `Microsoft.AspNetCore.Components.Authorization` 네임스페이스</span><span class="sxs-lookup"><span data-stu-id="eeea5-124">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's `_Imports.razor` file.</span></span>

<span data-ttu-id="eeea5-125">인증을 처리하려면 이어지는 섹션에서 설명하는 기본 제공 또는 사용자 지정 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 서비스의 구현 방법을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="eeea5-125">To handle authentication, implementation of a built-in or custom <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service is covered in the following sections.</span></span>

<span data-ttu-id="eeea5-126">앱 및 구성을 만드는 방법에 대한 자세한 내용은 <xref:blazor/security/webassembly/index> 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="eeea5-126">For more information on creating apps and configuration, see <xref:blazor/security/webassembly/index>.</span></span>

### <a name="blazor-server-authentication"></a>Blazor<span data-ttu-id="eeea5-127"> 서버 인증</span><span class="sxs-lookup"><span data-stu-id="eeea5-127"> Server authentication</span></span>

Blazor<span data-ttu-id="eeea5-128"> 서버 앱은 SignalR를 사용하여 생성된 실시간 연결을 통해 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-128"> Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="eeea5-129">[SignalR 기반 앱](xref:signalr/authn-and-authz)의 인증은 연결 시 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-129">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="eeea5-130">인증은 쿠키 또는 다른 전달자 토큰을 기반으로 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-130">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="eeea5-131">앱 및 구성을 만드는 방법에 대한 자세한 내용은 <xref:blazor/security/server/index> 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="eeea5-131">For more information on creating apps and configuration, see <xref:blazor/security/server/index>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="eeea5-132">AuthenticationStateProvider 서비스</span><span class="sxs-lookup"><span data-stu-id="eeea5-132">AuthenticationStateProvider service</span></span>

<span data-ttu-id="eeea5-133">기본 제공 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 서비스는 ASP.NET Core's `HttpContext.User`에서 인증 상태 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-133">The built-in <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="eeea5-134">이것이 바로 인증 상태가 기존의 ASP.NET Core 인증 메커니즘과 통합되는 방식입니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-134">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="eeea5-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>는 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 구성 요소와 <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> 구성 요소가 인증 상태를 가져오는 데 사용하는 기본 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> is the underlying service used by the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component to get the authentication state.</span></span>

<span data-ttu-id="eeea5-136">일반적으로 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>를 직접 사용하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-136">You don't typically use <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly.</span></span> <span data-ttu-id="eeea5-137">이 문서의 뒷부분에서 설명하는 [`AuthorizeView` 구성 요소](#authorizeview-component) 또는 [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) 접근 방식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-137">Use the [`AuthorizeView` component](#authorizeview-component) or [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="eeea5-138"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>를 직접 사용하는 경우의 주요 단점은 기본 인증 상태 데이터가 변경될 때 구성 요소가 자동으로 알림을 받지 못하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-138">The main drawback to using <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="eeea5-139"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 서비스는 다음 예제와 같이 현재 사용자의 <xref:System.Security.Claims.ClaimsPrincipal> 데이터를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-139">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type: @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="eeea5-140">`user.Identity.IsAuthenticated`가 `true`이면, 사용자가 <xref:System.Security.Claims.ClaimsPrincipal>이므로 클레임을 열거하고 역할의 멤버 자격을 평가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-140">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="eeea5-141">DI(종속성 주입) 및 서비스에 대한 자세한 내용은 <xref:blazor/fundamentals/dependency-injection> 및 <xref:fundamentals/dependency-injection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="eeea5-141">For more information on dependency injection (DI) and services, see <xref:blazor/fundamentals/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="eeea5-142">사용자 지정 AuthenticationStateProvider 구현</span><span class="sxs-lookup"><span data-stu-id="eeea5-142">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="eeea5-143">앱에 사용자 지정 공급자가 필요한 경우 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>를 구현하고 `GetAuthenticationStateAsync`를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-143">If the app requires a custom provider, implement <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> and override `GetAuthenticationStateAsync`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

<span data-ttu-id="eeea5-144">Blazor WebAssembly 앱에서 `CustomAuthStateProvider` 서비스는 `Program.cs`의 `Main`에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-144">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of `Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="eeea5-145">Blazor 서버 앱에서 `CustomAuthStateProvider` 서비스는 `Startup.ConfigureServices`에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-145">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="eeea5-146">모든 사용자는 앞에 나온 예제의 `CustomAuthStateProvider`를 사용하여 사용자 이름 `mrfibuli`로 인증되었습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-146">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="eeea5-147">인증 상태를 연계 매개 변수로 공개</span><span class="sxs-lookup"><span data-stu-id="eeea5-147">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="eeea5-148">사용자가 실행한 작업을 수행하는 경우와 같이 절차적 논리에 인증 상태 데이터가 필요한 경우 `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` 형식의 연계 매개 변수를 정의하여 인증 상태 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-148">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`:</span></span>

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="eeea5-149">`user.Identity.IsAuthenticated`가 `true`이면, 클레임을 열거하고 역할의 멤버 자격을 평가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-149">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="eeea5-150">`App` 구성 요소(`App.razor`)에서 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> 및 <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> 구성 요소를 사용하여 `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` 연계 매개 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-150">Set up the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter using the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> components in the `App` component (`App.razor`):</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)" />
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="eeea5-151">Blazor WebAssembly 앱에서 `Program.Main`에 옵션 및 인증 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-151">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="eeea5-152">Blazor 서버 앱에는 옵션 및 인증 서비스가 이미 있으므로 추가 작업이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-152">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="eeea5-153">권한 부여</span><span class="sxs-lookup"><span data-stu-id="eeea5-153">Authorization</span></span>

<span data-ttu-id="eeea5-154">사용자가 인증되면, 사용자가 수행할 수 있는 작업을 제어하기 위해 ‘권한 부여’ 규칙이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-154">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="eeea5-155">일반적으로 다음 여부에 따라 액세스가 허용 또는 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-155">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="eeea5-156">사용자가 인증(로그인)되었는지 여부</span><span class="sxs-lookup"><span data-stu-id="eeea5-156">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="eeea5-157">사용자가 ‘역할’에 속하는지 여부</span><span class="sxs-lookup"><span data-stu-id="eeea5-157">A user is in a *role*.</span></span>
* <span data-ttu-id="eeea5-158">사용자에게 ‘클레임’이 있는지 여부</span><span class="sxs-lookup"><span data-stu-id="eeea5-158">A user has a *claim*.</span></span>
* <span data-ttu-id="eeea5-159">‘정책’이 충족되었는지 여부</span><span class="sxs-lookup"><span data-stu-id="eeea5-159">A *policy* is satisfied.</span></span>

<span data-ttu-id="eeea5-160">이러한 각 개념은 ASP.NET Core MVC 또는 Razor Pages 앱에서와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-160">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="eeea5-161">ASP.NET Core 보안에 대한 자세한 내용은 [ASP.NET Core 보안 및 Identity](xref:security/index)의 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="eeea5-161">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="eeea5-162">AuthorizeView 구성 요소</span><span class="sxs-lookup"><span data-stu-id="eeea5-162">AuthorizeView component</span></span>

<span data-ttu-id="eeea5-163"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 구성 요소는 사용자에게 볼 수 있는 권한이 있는지 여부에 따라 선택적으로 UI를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-163">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="eeea5-164">이 접근 방식은 사용자에게 데이터를 ‘표시’하기만 하면 되고 절차적 논리에 사용자 ID를 사용할 필요가 없는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-164">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="eeea5-165">이 구성 요소는 로그인한 사용자 정보에 액세스하는 데 사용할 수 있는 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> 형식의 `context` 변수를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-165">The component exposes a `context` variable of type <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="eeea5-166">사용자가 인증되지 않은 경우에 표시할 다른 콘텐츠를 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-166">You can also supply different content for display if the user isn't authenticated:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="eeea5-167"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 구성 요소는 `NavMenu` 구성 요소(`Shared/NavMenu.razor`)에서 [`NavLink` 구성 요소](xref:blazor/fundamentals/routing#navlink-component)(<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)에 대한 목록 항목(`<li>...</li>`)을 표시하는 데 사용할 수 있지만, 이 방법은 렌더링된 출력에서 목록 항목을 제거할 뿐입니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-167">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component can be used in the `NavMenu` component (`Shared/NavMenu.razor`) to display a list item (`<li>...</li>`) for a [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="eeea5-168">사용자가 해당 구성 요소로 이동하는 것을 방지하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-168">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="eeea5-169">`<Authorized>` 및 `<NotAuthorized>` 태그의 콘텐츠에는 다른 대화형 구성 요소와 같은 임의 항목이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-169">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="eeea5-170">UI 옵션이나 액세스를 제어하는 역할 또는 정책과 같은 권한 부여 조건은 [권한 부여](#authorization) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-170">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="eeea5-171">권한 부여 조건을 지정하지 않으면 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>는 기본 정책을 사용하고 다음과 같이 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-171">If authorization conditions aren't specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses a default policy and treats:</span></span>

* <span data-ttu-id="eeea5-172">인증(로그인)된 사용자를 권한 있는 사용자로 처리</span><span class="sxs-lookup"><span data-stu-id="eeea5-172">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="eeea5-173">인증되지 않은(로그아웃된) 사용자를 권한 없는 사용자로 처리</span><span class="sxs-lookup"><span data-stu-id="eeea5-173">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="eeea5-174">역할 기반 및 정책 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="eeea5-174">Role-based and policy-based authorization</span></span>

<span data-ttu-id="eeea5-175"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 구성 요소는 ‘역할 기반’ 또는 ‘정책 기반’ 권한 부여를 지원합니다. </span><span class="sxs-lookup"><span data-stu-id="eeea5-175">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="eeea5-176">역할 기반 권한 부여의 경우 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-176">For role-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="eeea5-177">자세한 내용은 <xref:security/authorization/roles>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="eeea5-177">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="eeea5-178">정책 기반 권한 부여의 경우 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-178">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="eeea5-179">클레임 기반 권한 부여는 정책 기반 권한 부여의 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-179">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="eeea5-180">예를 들어 사용자에게 특정 클레임이 있어야 하는 정책을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-180">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="eeea5-181">자세한 내용은 <xref:security/authorization/policies>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="eeea5-181">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="eeea5-182">이러한 API는 Blazor 서버나 Blazor WebAssembly 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-182">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="eeea5-183"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> 또는 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy>를 지정하지 않으면 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>는 기본 정책을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-183">If neither <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> nor <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> is specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="eeea5-184">비동기 인증 중에 표시되는 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="eeea5-184">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="eeea5-185">에서는 인증 상태를 *비동기적으로* 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-185"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="eeea5-186">이 접근 방식의 주요 시나리오는 Blazor WebAssembly 앱이 외부 엔드포인트에 인증 요청을 하는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-186">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="eeea5-187">인증이 진행되는 동안 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>는 기본적으로 아무 콘텐츠도 표시하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-187">While authentication is in progress, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> displays no content by default.</span></span> <span data-ttu-id="eeea5-188">인증 중에 콘텐츠를 표시하려면 `<Authorizing>` 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-188">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

<span data-ttu-id="eeea5-189">이 접근 방식은 일반적으로 Blazor 서버 앱에 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-189">This approach isn't normally applicable to Blazor Server apps.</span></span> Blazor<span data-ttu-id="eeea5-190"> 서버 앱은 상태가 설정되는 즉시 인증 상태를 알 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-190"> Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="eeea5-191">Blazor 서버 앱의 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 구성 요소에 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> 콘텐츠를 제공할 수는 있지만, 이 콘텐츠는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-191"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> content can be provided in a Blazor Server app's <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="eeea5-192">[Authorize] 특성</span><span class="sxs-lookup"><span data-stu-id="eeea5-192">[Authorize] attribute</span></span>

<span data-ttu-id="eeea5-193">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성은 Razor 구성 요소에서 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-193">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="eeea5-194">Blazor 라우터를 통해 연결된 `@page` 구성 요소에서만 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-194">Only use [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="eeea5-195">권한 부여는 라우팅의 일부로만 수행되고, 페이지에 렌더링된 자식 구성 요소에 대해서는 수행되지 ‘않습니다’.</span><span class="sxs-lookup"><span data-stu-id="eeea5-195">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="eeea5-196">페이지 내의 특정 파트 표시 권한을 부여하려면 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>를 대신 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-196">To authorize the display of specific parts within a page, use <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> instead.</span></span>

<span data-ttu-id="eeea5-197">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성은 역할 기반 또는 정책 기반 권한 부여도 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-197">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="eeea5-198">역할 기반 권한 부여의 경우 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-198">For role-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="eeea5-199">정책 기반 권한 부여의 경우 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-199">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="eeea5-200"><xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> 또는 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy>를 지정하지 않으면 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)는 기본적으로 다음과 같이 처리하는 기본 정책을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-200">If neither <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> nor <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> is specified, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="eeea5-201">인증(로그인)된 사용자를 권한 있는 사용자로 처리</span><span class="sxs-lookup"><span data-stu-id="eeea5-201">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="eeea5-202">인증되지 않은(로그아웃된) 사용자를 권한 없는 사용자로 처리</span><span class="sxs-lookup"><span data-stu-id="eeea5-202">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="eeea5-203">Router 구성 요소를 사용하여 권한 없는 콘텐츠 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="eeea5-203">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="eeea5-204"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> 구성 요소와 함께 <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소를 사용하면 다음과 같은 경우 앱이 사용자 지정 콘텐츠를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-204">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component, in conjunction with the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="eeea5-205">콘텐츠를 찾을 수 없는 경우</span><span class="sxs-lookup"><span data-stu-id="eeea5-205">Content isn't found.</span></span>
* <span data-ttu-id="eeea5-206">사용자가 구성 요소에 적용된 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 조건을 충족하지 못하는 경우</span><span class="sxs-lookup"><span data-stu-id="eeea5-206">The user fails an [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) condition applied to the component.</span></span> <span data-ttu-id="eeea5-207">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성은 [`[Authorize]` 속성](#authorize-attribute) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-207">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="eeea5-208">비동기 인증이 진행 중인 경우</span><span class="sxs-lookup"><span data-stu-id="eeea5-208">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="eeea5-209">기본 Blazor 서버 프로젝트 템플릿에서 `App` 구성 요소(`App.razor`)는 사용자 지정 콘텐츠를 설정하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-209">In the default Blazor Server project template, the `App` component (`App.razor`) demonstrates how to set custom content:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    <h1>Sorry</h1>
                    <p>You're not authorized to reach this page.</p>
                    <p>You may need to log in as a different user.</p>
                </NotAuthorized>
                <Authorizing>
                    <h1>Authentication in progress</h1>
                    <p>Only visible while authentication is in progress.</p>
                </Authorizing>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="eeea5-210">`<NotFound>`, `<NotAuthorized>` 및 `<Authorizing>` 태그의 콘텐츠에는 다른 대화형 구성 요소와 같은 임의 항목이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-210">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="eeea5-211">`<NotAuthorized>` 요소를 지정하지 않으면 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView>는 다음 대체 메시지를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-211">If the `<NotAuthorized>` element isn't specified, the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="eeea5-212">인증 상태 변경 알림</span><span class="sxs-lookup"><span data-stu-id="eeea5-212">Notification about authentication state changes</span></span>

<span data-ttu-id="eeea5-213">예를 들어 사용자가 로그아웃했거나 다른 사용자가 해당 역할을 변경하여 기본 인증 상태 데이터가 변경되었음을 앱이 확인하면, [사용자 지정 `AuthenticationStateProvider`](#implement-a-custom-authenticationstateprovider)는 필요에 따라 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 기본 클래스에서 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-213">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom `AuthenticationStateProvider`](#implement-a-custom-authenticationstateprovider) can optionally invoke the method <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> on the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> base class.</span></span> <span data-ttu-id="eeea5-214">그러면 인증 상태 데이터의 소비자(예: <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>)가 새 데이터를 사용하여 다시 렌더링하라는 알림을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-214">This notifies consumers of the authentication state data (for example, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="eeea5-215">절차적 논리</span><span class="sxs-lookup"><span data-stu-id="eeea5-215">Procedural logic</span></span>

<span data-ttu-id="eeea5-216">앱이 절차적 논리의 일부로 권한 부여 규칙을 확인해야 하는 경우, `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` 형식의 연계 매개 변수를 사용하여 사용자의 <xref:System.Security.Claims.ClaimsPrincipal>을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-216">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="eeea5-217">`IAuthorizationService` 등의 다른 서비스와 `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`를 결합하여 정책을 평가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-217">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```razor
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="eeea5-218">Blazor WebAssembly 앱 구성 요소에서 <xref:Microsoft.AspNetCore.Authorization> 및 <xref:Microsoft.AspNetCore.Components.Authorization> 네임스페이스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-218">In a Blazor WebAssembly app component, add the <xref:Microsoft.AspNetCore.Authorization> and <xref:Microsoft.AspNetCore.Components.Authorization> namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="eeea5-219">이러한 네임스페이스는 앱의 `_Imports.razor` 파일에 추가하여 전역적으로 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-219">These namespaces can be provided globally by adding them to the app's `_Imports.razor` file.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="eeea5-220">오류 문제 해결</span><span class="sxs-lookup"><span data-stu-id="eeea5-220">Troubleshoot errors</span></span>

<span data-ttu-id="eeea5-221">일반적인 오류:</span><span class="sxs-lookup"><span data-stu-id="eeea5-221">Common errors:</span></span>

* <span data-ttu-id="eeea5-222">**권한을 부여하려면 `Task\<AuthenticationState>` 형식의 연계 매개 변수가 필요합니다. `CascadingAuthenticationState`를 사용하여 이 변수를 제공하는 방법을 고려하세요.**</span><span class="sxs-lookup"><span data-stu-id="eeea5-222">**Authorization requires a cascading parameter of type `Task\<AuthenticationState>`. Consider using `CascadingAuthenticationState` to supply this.**</span></span>

* <span data-ttu-id="eeea5-223">**`authenticationStateTask`에 대해 `null` 값을 받았습니다.**</span><span class="sxs-lookup"><span data-stu-id="eeea5-223">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="eeea5-224">인증을 사용할 수 있는 Blazor 서버 템플릿으로 프로젝트를 만들지 않았을 가능성이 큽니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-224">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="eeea5-225">UI 트리의 일부를 `<CascadingAuthenticationState>`로 래핑합니다. 다음은 `App` 구성 요소(`App.razor`)를 래핑한 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-225">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (`App.razor`) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="eeea5-226"><xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState>는 기본 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> DI 서비스로부터 받은 `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` 연계 매개 변수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="eeea5-226">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> supplies the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter, which in turn it receives from the underlying <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eeea5-227">추가 자료</span><span class="sxs-lookup"><span data-stu-id="eeea5-227">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="eeea5-228">[Awesome Blazor: 인증](https://github.com/AdrienTorris/awesome-blazor#authentication) 커뮤니티 샘플 링크</span><span class="sxs-lookup"><span data-stu-id="eeea5-228">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>