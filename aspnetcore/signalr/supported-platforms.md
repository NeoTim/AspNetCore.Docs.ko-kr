---
title: ASP.NET Core SignalR 지원 플랫폼
author: bradygaster
description: ASP.NET Core SignalR 지원 플랫폼에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: e6989f431d0d72241a6e1b94759585c02ef7398f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021551"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="78430-103">ASP.NET Core SignalR 지원 플랫폼</span><span class="sxs-lookup"><span data-stu-id="78430-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="78430-104">서버 시스템 요구 사항</span><span class="sxs-lookup"><span data-stu-id="78430-104">Server system requirements</span></span>

<span data-ttu-id="78430-105">SignalRASP.NET Core은 ASP.NET Core에서 지 원하는 모든 서버 플랫폼을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="78430-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="78430-106">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="78430-106">JavaScript client</span></span>

<span data-ttu-id="78430-107">[JavaScript 클라이언트](xref:signalr/javascript-client) 는 nodejs 8 이상 버전과 다음 브라우저에서 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="78430-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="78430-108">브라우저</span><span class="sxs-lookup"><span data-stu-id="78430-108">Browser</span></span>                         | <span data-ttu-id="78430-109">버전</span><span class="sxs-lookup"><span data-stu-id="78430-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="78430-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="78430-110">Microsoft Edge</span></span>                  | <span data-ttu-id="78430-111">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="78430-111">Current&dagger;</span></span> |
| <span data-ttu-id="78430-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="78430-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="78430-113">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="78430-113">Current&dagger;</span></span> |
| <span data-ttu-id="78430-114">Google Chrome; Android 포함</span><span class="sxs-lookup"><span data-stu-id="78430-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="78430-115">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="78430-115">Current&dagger;</span></span> |
| <span data-ttu-id="78430-116">Safari iOS 포함</span><span class="sxs-lookup"><span data-stu-id="78430-116">Safari; includes iOS</span></span>            | <span data-ttu-id="78430-117">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="78430-117">Current&dagger;</span></span> |
| <span data-ttu-id="78430-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="78430-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="78430-119">11</span><span class="sxs-lookup"><span data-stu-id="78430-119">11</span></span>              |

<span data-ttu-id="78430-120">&dagger;*Current* 는 최신 버전의 브라우저를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="78430-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="78430-121">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="78430-121">.NET client</span></span>

<span data-ttu-id="78430-122">[.Net 클라이언트](xref:signalr/dotnet-client) 는 ASP.NET Core에서 지 원하는 모든 플랫폼에서 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="78430-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="78430-123">예를 들어 xamarin 개발자는 xamarin.ios 11.14.0.4 이상을 사용 하 여 8.4.0.1 이상 및 iOS 앱을 사용 하 여 Android 앱을 빌드하는 [데 사용할 SignalR 수 있습니다](https://github.com/aspnet/Announcements/issues/305) .</span><span class="sxs-lookup"><span data-stu-id="78430-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="78430-124">서버에서 IIS를 실행 하는 경우 Websocket 전송에는 Windows Server 2012 이상에서 IIS 8.0 이상이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="78430-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="78430-125">다른 전송은 모든 플랫폼에서 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="78430-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="78430-126">Java 클라이언트</span><span class="sxs-lookup"><span data-stu-id="78430-126">Java client</span></span>

<span data-ttu-id="78430-127">[Java 클라이언트](xref:signalr/java-client) 는 java 8 이상 버전을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="78430-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="78430-128">지원 되지 않는 클라이언트</span><span class="sxs-lookup"><span data-stu-id="78430-128">Unsupported clients</span></span>

<span data-ttu-id="78430-129">다음 클라이언트는 사용할 수 있지만 실험적 이거나 비공식적입니다.</span><span class="sxs-lookup"><span data-stu-id="78430-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="78430-130">현재 지원 되지 않으며 그렇지 않을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78430-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="78430-131">[C + + 클라이언트](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="78430-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="78430-132">[Swift 클라이언트](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="78430-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
