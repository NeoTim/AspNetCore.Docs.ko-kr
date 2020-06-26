---
title: ASP.NET Core Blazor 종속성 주입
author: guardrex
description: Blazor 앱이 구성 요소에 서비스를 주입할 수 있는 방법을 알아봅니다.
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
uid: blazor/fundamentals/dependency-injection
ms.openlocfilehash: 24cd5ae837eeb4c89a15bab2948dde2eface0c0d
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242799"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="2b492-103">ASP.NET Core Blazor 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="2b492-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="2b492-104">작성자: [Rainer Stropek](https://www.timecockpit.com) 및 [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="2b492-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

Blazor<span data-ttu-id="2b492-105">는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-105"> supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2b492-106">앱은 기본 제공 서비스를 구성 요소에 삽입하여 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="2b492-107">앱은 사용자 지정 서비스를 정의 및 등록하고 DI를 통해 앱 전체에서 사용하도록 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="2b492-108">DI는 중앙 위치에 구성된 서비스에 액세스하기 위한 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="2b492-109">이는 Blazor 앱에서 다음과 같은 경우에 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="2b492-110">여러 구성 요소에서 *singleton* 서비스라고 하는 단일 서비스 클래스 인스턴스를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="2b492-111">참조 추상화를 사용하여 구체적인 서비스 클래스의 구성 요소를 분리합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="2b492-112">예를 들어, 앱의 데이터에 액세스하기 위한 `IDataAccess` 인터페이스를 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="2b492-113">이 인터페이스는 구체적 `DataAccess` 클래스에 의해 구현되고 앱의 서비스 컨테이너에 서비스로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="2b492-114">구성 요소는 DI를 사용하여 `IDataAccess` 구현을 수신하는 경우 구체적인 형식에 결합되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="2b492-115">단위 테스트의 모의 구현을 위해서는 이 구현을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="2b492-116">기본 서비스</span><span class="sxs-lookup"><span data-stu-id="2b492-116">Default services</span></span>

<span data-ttu-id="2b492-117">기본 서비스는 앱의 서비스 컬렉션에 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="2b492-118">서비스</span><span class="sxs-lookup"><span data-stu-id="2b492-118">Service</span></span> | <span data-ttu-id="2b492-119">수명</span><span class="sxs-lookup"><span data-stu-id="2b492-119">Lifetime</span></span> | <span data-ttu-id="2b492-120">설명</span><span class="sxs-lookup"><span data-stu-id="2b492-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="2b492-121">Transient</span><span class="sxs-lookup"><span data-stu-id="2b492-121">Transient</span></span> | <span data-ttu-id="2b492-122">URI로 식별되는 리소스에서 HTTP 요청을 보내고 HTTP 응답을 받기 위한 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="2b492-123">Blazor WebAssembly 앱의 <xref:System.Net.Http.HttpClient> 인스턴스는 브라우저를 사용하여 백그라운드에서 HTTP 트래픽을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-123">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br>Blazor<span data-ttu-id="2b492-124"> 서버 앱에는 기본적으로 서비스로 구성된 <xref:System.Net.Http.HttpClient>는 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-124"> Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="2b492-125">Blazor 서버 앱에 <xref:System.Net.Http.HttpClient>를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-125">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span><br><br><span data-ttu-id="2b492-126">자세한 내용은 <xref:blazor/call-web-api>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2b492-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="2b492-127">Singleton(Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="2b492-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="2b492-128">Scoped(Blazor 서버)</span><span class="sxs-lookup"><span data-stu-id="2b492-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="2b492-129">JavaScript 호출이 디스패치되는 JavaScript 런타임의 인스턴스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="2b492-130">자세한 내용은 <xref:blazor/call-javascript-from-dotnet>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2b492-130">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="2b492-131">Singleton(Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="2b492-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="2b492-132">Scoped(Blazor 서버)</span><span class="sxs-lookup"><span data-stu-id="2b492-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="2b492-133">URI 및 탐색 상태를 사용하기 위한 도우미를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="2b492-134">자세한 내용은 [URI 및 탐색 상태 도우미](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2b492-134">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="2b492-135">사용자 지정 서비스 공급자는 테이블에 나열된 기본 서비스를 자동으로 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="2b492-136">사용자 지정 서비스 공급자를 사용하고 표에 표시된 서비스가 필요한 경우 새 서비스 공급자에 필요한 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="2b492-137">앱에 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="2b492-137">Add services to an app</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="2b492-138"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="2b492-138"> WebAssembly</span></span>

<span data-ttu-id="2b492-139">`Program.cs`의 `Main` 메서드에서 앱의 서비스 컬렉션용 서비스를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-139">Configure services for the app's service collection in the `Main` method of `Program.cs`.</span></span> <span data-ttu-id="2b492-140">다음 예제에서는 `MyDependency` 구현을 `IMyDependency`에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-140">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="2b492-141">일단 호스트를 빌드하면 구성 요소를 렌더링하기 전에 루트 DI 범위에서 서비스에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-141">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="2b492-142">이러한 기능은 콘텐츠를 렌더링하기 전에 초기화 논리를 실행하는 데 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-142">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="2b492-143">또한 호스트는 앱에 대한 중앙 구성 인스턴스도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-143">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="2b492-144">이전 예제를 기준으로 작성한 날씨 서비스 URL은 기본 구성 원본(예: `appsettings.json`)에서 `InitializeWeatherAsync`로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-144">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### <a name="blazor-server"></a>Blazor<span data-ttu-id="2b492-145"> 서버</span><span class="sxs-lookup"><span data-stu-id="2b492-145"> Server</span></span>

<span data-ttu-id="2b492-146">새 앱을 만든 후 `Startup.ConfigureServices` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="2b492-147"><xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> 메서드는 서비스 설명자 개체(<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>) 목록에 해당하는 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-147">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="2b492-148">서비스 설명자를 서비스 컬렉션에 제공하여 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-148">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="2b492-149">다음 예제에서는 `IDataAccess` 인터페이스와 해당 구체적 구현 `DataAccess`를 통해 이러한 개념을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="2b492-150">서비스 수명</span><span class="sxs-lookup"><span data-stu-id="2b492-150">Service lifetime</span></span>

<span data-ttu-id="2b492-151">다음 표에 표시된 수명으로 서비스를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="2b492-152">수명</span><span class="sxs-lookup"><span data-stu-id="2b492-152">Lifetime</span></span> | <span data-ttu-id="2b492-153">설명</span><span class="sxs-lookup"><span data-stu-id="2b492-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor<span data-ttu-id="2b492-154"> WebAssembly 앱에는 현재, DI 범위에 대한 개념이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-154"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="2b492-155">`Scoped` 등록 서비스는 `Singleton` 서비스처럼 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-155">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="2b492-156">그러나 Blazor 서버 호스팅 모델은 `Scoped` 수명을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-156">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="2b492-157">Blazor 서버 앱에서 범위가 지정된 서비스 등록은 *연결*로 범위가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-157">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="2b492-158">따라서 현재, 브라우저에서 클라이언트 쪽을 실행하려는 의도가 있더라도 현재 사용자로 범위를 지정해야 하는 서비스에 대해서는 범위 지정 서비스를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-158">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="2b492-159">DI는 서비스의 *단일 인스턴스*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-159">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="2b492-160">`Singleton` 서비스가 필요한 모든 구성 요소는 동일한 서비스의 인스턴스를 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-160">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="2b492-161">구성 요소는 서비스 컨테이너에서 `Transient` 서비스의 인스턴스를 가져올 때마다 서비스의 *새 인스턴스*을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-161">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="2b492-162">DI 시스템은 ASP.NET Core에서 DI 시스템을 기준으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-162">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="2b492-163">자세한 내용은 <xref:fundamentals/dependency-injection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2b492-163">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="2b492-164">구성 요소에서 서비스 요청</span><span class="sxs-lookup"><span data-stu-id="2b492-164">Request a service in a component</span></span>

<span data-ttu-id="2b492-165">서비스 컬렉션에 서비스를 추가한 후에는 [\@inject](xref:mvc/views/razor#inject) Razor 지시문을 사용하여 서비스를 구성 요소에 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-165">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="2b492-166">[`@inject`](xref:mvc/views/razor#inject)에는 다음 두 개의 매개 변수가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-166">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="2b492-167">유형: 주입할 서비스의 유형입니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-167">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="2b492-168">속성: 주입된 앱 서비스를 받는 속성의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-168">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="2b492-169">이 속성은 수동으로 만들 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-169">The property doesn't require manual creation.</span></span> <span data-ttu-id="2b492-170">컴파일러에서 속성을 만들기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-170">The compiler creates the property.</span></span>

<span data-ttu-id="2b492-171">자세한 내용은 <xref:mvc/views/dependency-injection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2b492-171">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="2b492-172">여러 [`@inject`](xref:mvc/views/razor#inject) 문을 사용하여 여러 서비스를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-172">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="2b492-173">다음 예제에서는 [`@inject`](xref:mvc/views/razor#inject)를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-173">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="2b492-174">`Services.IDataAccess`를 구현하는 서비스는 구성 요소의 속성 `DataRepository`에 주입됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-174">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="2b492-175">코드가 `IDataAccess` 추상화만 사용하는 방식에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="2b492-175">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="2b492-176">내부적으로 생성된 속성(`DataRepository`)은 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 특성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-176">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="2b492-177">일반적으로 이 특성은 직접 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-177">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="2b492-178">구성 요소에 기본 클래스가 필요하고 주입된 속성이 기본 클래스에도 필요하면 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 특성을 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-178">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="2b492-179">기본 클래스에서 파생된 구성 요소에서는 [`@inject`](xref:mvc/views/razor#inject) 지시문이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-179">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="2b492-180">기본 클래스의 <xref:Microsoft.AspNetCore.Components.InjectAttribute>만 있으면 충분합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-180">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="2b492-181">서비스에서 DI 사용</span><span class="sxs-lookup"><span data-stu-id="2b492-181">Use DI in services</span></span>

<span data-ttu-id="2b492-182">복잡한 서비스에는 추가 서비스가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-182">Complex services might require additional services.</span></span> <span data-ttu-id="2b492-183">이전 예제에서는 `DataAccess`에 <xref:System.Net.Http.HttpClient> 기본 서비스가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-183">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="2b492-184">서비스에서는 [`@inject`](xref:mvc/views/razor#inject)(또는 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 특성)를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-184">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="2b492-185">대신 *생성자 주입*을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-185">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="2b492-186">서비스의 생성자에 매개 변수를 추가하여 필요한 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-186">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="2b492-187">DI는 서비스를 만들 때 생성자에 필요한 서비스를 인식하고 적절히 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-187">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

<span data-ttu-id="2b492-188">생성자 주입의 필수 조건:</span><span class="sxs-lookup"><span data-stu-id="2b492-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="2b492-189">모든 인수를 DI에서 처리할 수 있는 생성자가 하나 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="2b492-190">DI에서 다루지 않는 추가 매개 변수는 기본값이 지정되면 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="2b492-191">적용 가능한 생성자는 `public`이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-191">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="2b492-192">적용 가능한 생성자가 하나 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-192">One applicable constructor must exist.</span></span> <span data-ttu-id="2b492-193">모호한 경우 시 DI는 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="2b492-194">DI 범위를 관리 하는 유틸리티 기본 구성 요소 클래스</span><span class="sxs-lookup"><span data-stu-id="2b492-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="2b492-195">ASP.NET Core 앱에서 범위가 지정된 서비스는 일반적으로 현재 요청으로 범위가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="2b492-196">요청이 완료된 후에는 모든 범위 지정 또는 임시 서비스가 DI 시스템에서 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="2b492-197">Blazor 서버 앱에서 요청 범위는 클라이언트 연결 기간 동안 지속되므로 임시 및 범위가 지정된 서비스가 예상보다 훨씬 오래 지속될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="2b492-198">Blazor WebAssembly 앱에서, 범위가 지정된 수명으로 등록된 서비스는 싱글톤으로 처리되므로 일반적인 ASP.NET Core 앱의 범위가 지정된 서비스보다 오래 지속됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="2b492-199">Blazor 앱에서 서비스 수명을 제한하는 방법은 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 형식을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-199">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="2b492-200"><xref:Microsoft.AspNetCore.Components.OwningComponentBase>는 구성 요소의 수명에 해당하는 DI 범위를 만드는 <xref:Microsoft.AspNetCore.Components.ComponentBase>에서 파생된 추상 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-200"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="2b492-201">이 범위를 사용하는 경우 수명 범위를 지정한 DI 서비스를 사용하고 해당 구성 요소만큼 지속되도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-201">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="2b492-202">구성 요소가 제거되면 구성 요소 범위 지정 서비스 공급자의 서비스도 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-202">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="2b492-203">이 기능은 다음과 같은 서비스에 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-203">This can be useful for services that:</span></span>

* <span data-ttu-id="2b492-204">일시적 수명은 부적절하므로 구성 요소 내에서 다시 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-204">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="2b492-205">싱글톤 수명은 부적절하므로 구성 요소에서 공유할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-205">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="2b492-206">두 가지 버전의 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 형식을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-206">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="2b492-207"><xref:Microsoft.AspNetCore.Components.OwningComponentBase>는 <xref:System.IServiceProvider> 형식의 보호된 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 속성을 사용하여 <xref:Microsoft.AspNetCore.Components.ComponentBase> 형식의 삭제 가능한 추상 자식입니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-207"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="2b492-208">이 공급자는 구성 요소의 수명으로 범위가 지정된 서비스를 확인하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-208">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="2b492-209">[`@inject`](xref:mvc/views/razor#inject) 또는 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 특성을 사용하여 구성 요소에 주입된 DI 서비스는 구성 요소의 범위에서 만들어지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-209">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="2b492-210">구성 요소의 범위를 사용하려면 <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> 또는 <xref:System.IServiceProvider.GetService%2A>를 사용하여 서비스를 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-210">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="2b492-211"><xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 공급자를 사용하여 확인된 모든 서비스에는 동일한 범위에서 종속성이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-211">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <span data-ttu-id="2b492-212"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601>는 <xref:Microsoft.AspNetCore.Components.OwningComponentBase>에서 파생되고 범위가 지정된 DI 공급자에서 `T`의 인스턴스를 반환하는 <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-212"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="2b492-213">이 형식은 구성 요소의 범위를 사용하여 DI 컨테이너에서 앱에 필요한 기본 서비스가 하나 있는 경우 <xref:System.IServiceProvider> 인스턴스를 사용하지 않고 범위 지정 서비스에 액세스할 수 있는 편리한 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-213">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="2b492-214"><xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 속성을 사용할 수 있으므로 필요한 경우 앱에서 다른 형식의 서비스를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-214">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="2b492-215">DI의 Entity Framework DbContext 사용</span><span class="sxs-lookup"><span data-stu-id="2b492-215">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="2b492-216">웹앱의 DI에서 검색할 하나의 공통 서비스 형식은 EF(Entity Framework)<xref:Microsoft.EntityFrameworkCore.DbContext> 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-216">One common service type to retrieve from DI in web apps is Entity Framework (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> objects.</span></span> <span data-ttu-id="2b492-217"><xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>를 사용하여 EF 서비스를 등록하면 기본적으로 <xref:Microsoft.EntityFrameworkCore.DbContext>를 범위가 지정된 서비스로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-217">Registering EF services using <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> adds the <xref:Microsoft.EntityFrameworkCore.DbContext> as a scoped service by default.</span></span> <span data-ttu-id="2b492-218">범위가 지정된 서비스로 등록하면 <xref:Microsoft.EntityFrameworkCore.DbContext> 인스턴스가 수명이 길며 앱 전체에서 공유되기 때문에 Blazor 앱에서 문제가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-218">Registering as a scoped service can lead to problems in Blazor apps because it causes <xref:Microsoft.EntityFrameworkCore.DbContext> instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="2b492-219"><xref:Microsoft.EntityFrameworkCore.DbContext>는 스레드로부터 안전하지 않으며 동시에 사용하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-219"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="2b492-220">앱에 따라 <xref:Microsoft.AspNetCore.Components.OwningComponentBase>를 사용하여 <xref:Microsoft.EntityFrameworkCore.DbContext>의 범위를 단일 구성 요소로 제한하면 이 문제를 해결*할 수 있습니다*.</span><span class="sxs-lookup"><span data-stu-id="2b492-220">Depending on the app, using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> to limit the scope of a <xref:Microsoft.EntityFrameworkCore.DbContext> to a single component *may* solve the issue.</span></span> <span data-ttu-id="2b492-221">구성 요소가 동시에 <xref:Microsoft.EntityFrameworkCore.DbContext>를 사용하지 않는 경우 <xref:Microsoft.AspNetCore.Components.OwningComponentBase>에서 구성 요소를 파생시키고 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>에서 <xref:Microsoft.EntityFrameworkCore.DbContext>를 검색하는 것는 것만으로 다음이 보장되므로 충분합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-221">If a component doesn't use a <xref:Microsoft.EntityFrameworkCore.DbContext> in parallel, deriving the component from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and retrieving the <xref:Microsoft.EntityFrameworkCore.DbContext> from <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> is sufficient because it ensures that:</span></span>

* <span data-ttu-id="2b492-222">개별 구성 요소는 <xref:Microsoft.EntityFrameworkCore.DbContext>를 공유하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-222">Separate components don't share a <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span>
* <span data-ttu-id="2b492-223"><xref:Microsoft.EntityFrameworkCore.DbContext>는 종속되는 구성 요소가 있는 경우에만 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-223">The <xref:Microsoft.EntityFrameworkCore.DbContext> lives only as long as the component depending on it.</span></span>

<span data-ttu-id="2b492-224">단일 구성 요소에서 <xref:Microsoft.EntityFrameworkCore.DbContext>를 동시에 사용할 수 있는 경우(예: 사용자가 단추를 선택할 때마다) <xref:Microsoft.AspNetCore.Components.OwningComponentBase>를 사용하더라도 동시 EF 작업과 관련된 문제를 피할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-224">If a single component might use a <xref:Microsoft.EntityFrameworkCore.DbContext> concurrently (for example, every time a user selects a button), even using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="2b492-225">이 경우 각 논리적 EF 작업에 대해 다른 <xref:Microsoft.EntityFrameworkCore.DbContext>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-225">In that case, use a different <xref:Microsoft.EntityFrameworkCore.DbContext> for each logical EF operation.</span></span> <span data-ttu-id="2b492-226">다음 방식 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-226">Use either of the following approaches:</span></span>

* <span data-ttu-id="2b492-227"><xref:Microsoft.EntityFrameworkCore.DbContextOptions%601>를 인수로 사용하여 <xref:Microsoft.EntityFrameworkCore.DbContext>를 직접 만듭니다. 이 항목은 DI에서 검색할 수 있으며 스레드로부터 안전합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-227">Create the <xref:Microsoft.EntityFrameworkCore.DbContext> directly using <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> as an argument, which can be retrieved from DI and is thread safe.</span></span>

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = new AppDbContext(DbContextOptions))
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

* <span data-ttu-id="2b492-228">임시 수명을 사용하여 서비스 컨테이너의 <xref:Microsoft.EntityFrameworkCore.DbContext>를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-228">Register the <xref:Microsoft.EntityFrameworkCore.DbContext> in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="2b492-229">컨텍스트를 등록할 때 <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-229">When registering the context, use <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="2b492-230"><xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 확장 메서드는 <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime> 형식의 두 가지 선택적 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-230">The <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method takes two optional parameters of type <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime>.</span></span> <span data-ttu-id="2b492-231">이 방법을 사용하려면 `contextLifetime` 매개 변수만 <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>이면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-231">To use this approach, only the `contextLifetime` parameter needs to be <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="2b492-232">`optionsLifetime`은 기본값 <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType>를 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-232">`optionsLifetime` can keep its default value of <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType>.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="2b492-233">임시 <xref:Microsoft.EntityFrameworkCore.DbContext>는 여러 EF 작업을 동시에 실행하지 않는 구성 요소에 일반([`@inject`](xref:mvc/views/razor#inject) 사용)으로 주입될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-233">The transient <xref:Microsoft.EntityFrameworkCore.DbContext> can be injected as normal (using [`@inject`](xref:mvc/views/razor#inject)) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="2b492-234">여러 EF 작업을 동시에 수행할 수 있는 항목은 <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A>를 사용하여 각 병렬 작업에 대해 별도의 <xref:Microsoft.EntityFrameworkCore.DbContext> 개체를 요청할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b492-234">Those that may perform multiple EF operations simultaneously can request separate <xref:Microsoft.EntityFrameworkCore.DbContext> objects for each parallel operation using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A>.</span></span>

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = ServiceProvider.GetRequiredService<AppDbContext>())
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="2b492-235">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2b492-235">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="2b492-236">임시 및 공유 인스턴스에 대한 `IDisposable` 지침</span><span class="sxs-lookup"><span data-stu-id="2b492-236">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>