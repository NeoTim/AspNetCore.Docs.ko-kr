---
title: ASP.NET Core 호스트 및 배포 Blazor Server
author: guardrex
description: ASP.NET Core를 사용하여 Blazor Server 앱을 호스트 및 배포하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/server
ms.openlocfilehash: e59579046ecbfdbb4cca79bfb0e39d299e26913c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402600"
---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="57640-103">Blazor Server 호스트 및 배포</span><span class="sxs-lookup"><span data-stu-id="57640-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="57640-104">작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="57640-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="57640-105">호스트 구성 값</span><span class="sxs-lookup"><span data-stu-id="57640-105">Host configuration values</span></span>

<span data-ttu-id="57640-106">[Blazor Server 앱](xref:blazor/hosting-models#blazor-server)은 [일반 호스트 구성 값](xref:fundamentals/host/generic-host#host-configuration)을 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57640-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="57640-107">배포</span><span class="sxs-lookup"><span data-stu-id="57640-107">Deployment</span></span>

<span data-ttu-id="57640-108">[Blazor Server 호스팅 모델](xref:blazor/hosting-models#blazor-server)을 사용하면 Blazor가 서버의 ASP.NET Core 앱 내에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="57640-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="57640-109">UI 업데이트, 이벤트 처리 및 JavaScript 호출은 [SignalR](xref:signalr/introduction) 연결을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="57640-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="57640-110">ASP.NET Core 앱을 호스팅할 수 있는 웹 서버가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="57640-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="57640-111">Visual Studio는 **Blazor Server 앱** 프로젝트 템플릿([`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 사용하는 경우 `blazorserverside` 템플릿)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="57640-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="57640-112">확장성</span><span class="sxs-lookup"><span data-stu-id="57640-112">Scalability</span></span>

<span data-ttu-id="57640-113">Blazor Server 앱에 사용 가능한 인프라를 최대한 활용하도록 배포를 계획합니다.</span><span class="sxs-lookup"><span data-stu-id="57640-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="57640-114">Blazor Server 앱 확장성에 대해서는 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57640-114">See the following resources to address Blazor Server app scalability:</span></span>

* <span data-ttu-id="57640-115">[Blazor Server 앱의 기본 사항](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="57640-115">[Fundamentals of Blazor Server apps](xref:blazor/hosting-models#blazor-server)</span></span>
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="57640-116">배포 서버</span><span class="sxs-lookup"><span data-stu-id="57640-116">Deployment server</span></span>

<span data-ttu-id="57640-117">단일 서버의 확장성(규모 확대)을 고려할 때 앱에 사용할 수 있는 메모리는 사용자 요구가 늘어남에 따라 앱이 소진하는 첫 번째 리소스일 가능성이 높습니다.</span><span class="sxs-lookup"><span data-stu-id="57640-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="57640-118">서버에서 사용 가능한 메모리는 다음 사항에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57640-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="57640-119">서버가 지원할 수 있는 활성 회로 수</span><span class="sxs-lookup"><span data-stu-id="57640-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="57640-120">클라이언트의 UI 대기 시간</span><span class="sxs-lookup"><span data-stu-id="57640-120">UI latency on the client.</span></span>

<span data-ttu-id="57640-121">안전하고 확장 가능한 Blazor 서버 앱을 빌드하는 방법에 대한 지침은 <xref:blazor/security/server/threat-mitigation>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57640-121">For guidance on building secure and scalable Blazor server apps, see <xref:blazor/security/server/threat-mitigation>.</span></span>

<span data-ttu-id="57640-122">각 회로는 최소 *Hello World*와 같은 앱에 약 250KB의 메모리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57640-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="57640-123">회로의 크기는 앱의 코드 및 각 구성 요소와 연결된 상태 유지 관리 요구 사항에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="57640-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="57640-124">애플리케이션 및 인프라를 개발하는 도중에 리소스 요구를 측정하는 것이 좋지만, 다음 기준은 배포 대상을 계획할 때 출발점으로 삼을 수 있습니다. 앱에서 5,000명의 동시 사용자를 지원할 것으로 예상되는 경우 앱에 최소 1.3GB(또는 사용자당 273KB)의 서버 메모리를 예산하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="57640-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a>SignalR<span data-ttu-id="57640-125"> 구성</span><span class="sxs-lookup"><span data-stu-id="57640-125"> configuration</span></span>

Blazor Server<span data-ttu-id="57640-126"> 앱은 ASP.NET Core SignalR을 사용하여 브라우저와 통신합니다.</span><span class="sxs-lookup"><span data-stu-id="57640-126"> apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="57640-127">[SignalR의 호스팅 및 크기 조정 조건](xref:signalr/publish-to-azure-web-app)이 Blazor Server 앱에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57640-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

Blazor<span data-ttu-id="57640-128">는 짧은 대기 시간, 안정성 및 [보안](xref:signalr/security) 덕분에 WebSocket을 SignalR 전송으로 사용하는 경우에 가장 효과적입니다.</span><span class="sxs-lookup"><span data-stu-id="57640-128"> works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="57640-129">WebSocket을 사용할 수 없거나 앱이 긴 폴링을 사용하도록 명시적으로 구성된 경우 SignalR에서 긴 폴링을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57640-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="57640-130">Azure App Service에 배포하는 경우 서비스에 대한 Azure Portal 설정에서 WebSocket을 사용하도록 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="57640-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="57640-131">Azure App Service에 대해 앱을 구성하는 방법에 대한 자세한 내용은 [SignalR 게시 지침](xref:signalr/publish-to-azure-web-app)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57640-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-signalr-service"></a><span data-ttu-id="57640-132">Azure SignalR 서비스</span><span class="sxs-lookup"><span data-stu-id="57640-132">Azure SignalR Service</span></span>

<span data-ttu-id="57640-133">Blazor Server 앱에 [Azure SignalR Service](/azure/azure-signalr)를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="57640-133">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="57640-134">이 서비스를 사용하면 Blazor Server 앱을 다수의 동시 SignalR 연결로 스케일 업할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57640-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="57640-135">또한 SignalR 서비스의 글로벌 및 고성능 데이터 센터는 지리적 위치로 인한 대기 시간을 줄이는 데 큰 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="57640-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="57640-136">앱을 구성하고 원하는 경우 Azure SignalR Service를 프로비전하려면 다음 단계를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="57640-136">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="57640-137">[사전 렌더링하는 경우 클라이언트를 동일한 서버로 다시 리디렉션하는](xref:blazor/hosting-models#connection-to-the-server)*고정 세션*을 지원하려면 해당 서비스를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="57640-137">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="57640-138">`ServerStickyMode` 옵션 또는 구성 값을 `Required`(으)로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="57640-138">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="57640-139">일반적으로 앱은 다음 방법 중 **하나**를 사용하여 구성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="57640-139">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="57640-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="57640-140">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="57640-141">구성(다음 방법 중 **하나**를 사용):</span><span class="sxs-lookup"><span data-stu-id="57640-141">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="57640-142">`appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="57640-142">`appsettings.json`:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="57640-143">Azure Portal에서 앱 서비스의 **구성** > **애플리케이션 설정**(**이름**: `Azure:SignalR:ServerStickyMode`, **값**: `Required`).</span><span class="sxs-lookup"><span data-stu-id="57640-143">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="57640-144">Visual Studio에 Blazor Server 앱을 위한 Azure 앱 게시 프로필을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="57640-144">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="57640-145">**Azure SignalR Service** 종속성을 프로필에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="57640-145">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="57640-146">앱에 할당할 기존 Azure SignalR Service 인스턴스가 Azure 구독에 없는 경우 **새 Azure SignalR Service 인스턴스 만들기**를 선택하여 새 서비스 인스턴스를 프로비전합니다.</span><span class="sxs-lookup"><span data-stu-id="57640-146">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="57640-147">Azure에 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="57640-147">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="57640-148">IIS</span><span class="sxs-lookup"><span data-stu-id="57640-148">IIS</span></span>

<span data-ttu-id="57640-149">IIS를 사용하는 경우 다음을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="57640-149">When using IIS, enable:</span></span>

* <span data-ttu-id="57640-150">[IIS WebSocket](xref:fundamentals/websockets#enabling-websockets-on-iis)</span><span class="sxs-lookup"><span data-stu-id="57640-150">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="57640-151">[애플리케이션 요청 라우팅에 대한 고정 세션](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)</span><span class="sxs-lookup"><span data-stu-id="57640-151">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="57640-152">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="57640-152">Kubernetes</span></span>

<span data-ttu-id="57640-153">[Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)(고정 세션용 Kubernetes 주석)을 사용하여 다음과 같은 수신 정의를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="57640-153">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a><span data-ttu-id="57640-154">Nginx를 사용하는 Linux</span><span class="sxs-lookup"><span data-stu-id="57640-154">Linux with Nginx</span></span>

<span data-ttu-id="57640-155">SignalR WebSocket이 제대로 작동하려면 프록시의 `Upgrade` 및 `Connection` 헤더가 다음 값으로 설정되고 다음 중 하나에 `$connection_upgrade`가 매핑되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="57640-155">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="57640-156">기본적으로 업그레이드 헤더 값입니다.</span><span class="sxs-lookup"><span data-stu-id="57640-156">The Upgrade header value by default.</span></span>
* <span data-ttu-id="57640-157">`close` 업그레이드 헤더가 없거나 비어 있는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="57640-157">`close` when the Upgrade header is missing or empty.</span></span>

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

<span data-ttu-id="57640-158">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57640-158">For more information, see the following articles:</span></span>

* [<span data-ttu-id="57640-159">WebSocket Proxy로 활용하는 NGINX</span><span class="sxs-lookup"><span data-stu-id="57640-159">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="57640-160">WebSocket 프록시</span><span class="sxs-lookup"><span data-stu-id="57640-160">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a><span data-ttu-id="57640-161">Apache를 사용하는 Linux</span><span class="sxs-lookup"><span data-stu-id="57640-161">Linux with Apache</span></span>

<span data-ttu-id="57640-162">Linux에서 Apache 뒤에 Blazor 앱을 호스트하려면 HTTP 및 WebSockets 트래픽에 대해 `ProxyPass`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="57640-162">To host a Blazor app behind Apache on Linux, configure `ProxyPass` for HTTP and WebSockets traffic.</span></span>

<span data-ttu-id="57640-163">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="57640-163">In the following example:</span></span>

* <span data-ttu-id="57640-164">Kestrel 서버가 호스트 컴퓨터에서 실행되고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57640-164">Kestrel server is running on the host machine.</span></span>
* <span data-ttu-id="57640-165">앱은 포트 5000에서 트래픽을 수신 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="57640-165">The app listens for traffic on port 5000.</span></span>

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

<span data-ttu-id="57640-166">다음 모듈을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="57640-166">Enable the following modules:</span></span>

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

<span data-ttu-id="57640-167">브라우저 콘솔에서 WebSockets 오류를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="57640-167">Check the browser console for WebSockets errors.</span></span> <span data-ttu-id="57640-168">오류의 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="57640-168">Example errors:</span></span>

* <span data-ttu-id="57640-169">Firefox가 ws://the-domain-name.tld/_blazor?id=XXX에서 서버에 대한 연결을 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="57640-169">Firefox can't establish a connection to the server at ws://the-domain-name.tld/_blazor?id=XXX.</span></span>
* <span data-ttu-id="57640-170">오류: ‘WebSockets’ 전송을 시작하지 못했습니다. 오류: 전송에 오류가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57640-170">Error: Failed to start the transport 'WebSockets': Error: There was an error with the transport.</span></span>
* <span data-ttu-id="57640-171">오류: ‘LongPolling’ 전송을 시작하지 못했습니다. TypeError: this.transport가 정의되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="57640-171">Error: Failed to start the transport 'LongPolling': TypeError: this.transport is undefined</span></span>
* <span data-ttu-id="57640-172">오류: 사용 가능한 전송 중 하나를 사용하여 서버에 연결할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="57640-172">Error: Unable to connect to the server with any of the available transports.</span></span> <span data-ttu-id="57640-173">WebSockets 실패</span><span class="sxs-lookup"><span data-stu-id="57640-173">WebSockets failed</span></span>
* <span data-ttu-id="57640-174">오류: 연결 상태가 ‘연결됨’이 아닌 경우 데이터를 보낼 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="57640-174">Error: Cannot send data if the connection is not in the 'Connected' State.</span></span>

<span data-ttu-id="57640-175">자세한 내용은 [Apache 설명서](https://httpd.apache.org/docs/current/mod/mod_proxy.html)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57640-175">For more information, see the [Apache documentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="57640-176">네트워크 대기 시간 측정</span><span class="sxs-lookup"><span data-stu-id="57640-176">Measure network latency</span></span>

<span data-ttu-id="57640-177">[JS interop](xref:blazor/call-javascript-from-dotnet)은 다음 예제와 같이 네트워크 대기 시간을 측정하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57640-177">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

<span data-ttu-id="57640-178">적절한 UI 환경을 위해 UI 대기 시간을 250ms 이하로 유지하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="57640-178">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
