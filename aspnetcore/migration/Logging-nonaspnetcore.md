---
title: Microsoft Extensions. 로깅 2.1에서 2.2 또는 3.0로 마이그레이션
author: pakrym
description: 2.1에서 2.2 또는 3.0로 non-ASP.NET를 사용 하는 Core 응용 프로그램을 마이그레이션하는 방법에 대해 알아봅니다.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
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
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: edb1c6456d0cbbac57d739f61b4c159f146e4f7e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015116"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="d9da7-103">Microsoft Extensions. 로깅 2.1에서 2.2 또는 3.0로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="d9da7-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="d9da7-104">이 문서에서는 `Microsoft.Extensions.Logging` 2.1에서 2.2 또는 3.0로를 사용 하는 Non-ASP.NET Core 응용 프로그램을 마이그레이션하는 일반적인 단계를 간략하게 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9da7-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="d9da7-105">2.1-2.2</span><span class="sxs-lookup"><span data-stu-id="d9da7-105">2.1 to 2.2</span></span>

<span data-ttu-id="d9da7-106">수동으로 `ServiceCollection` 를 만들고 호출 `AddLogging` 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9da7-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="d9da7-107">2.1 예제:</span><span class="sxs-lookup"><span data-stu-id="d9da7-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="d9da7-108">2.2 예제:</span><span class="sxs-lookup"><span data-stu-id="d9da7-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="d9da7-109">2.1 ~ 3.0</span><span class="sxs-lookup"><span data-stu-id="d9da7-109">2.1 to 3.0</span></span>

<span data-ttu-id="d9da7-110">3.0에서는을 사용 `LoggingFactory.Create` 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9da7-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="d9da7-111">2.1 예제:</span><span class="sxs-lookup"><span data-stu-id="d9da7-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="d9da7-112">3.0 예제:</span><span class="sxs-lookup"><span data-stu-id="d9da7-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="d9da7-113">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="d9da7-113">Additional resources</span></span>

* <span data-ttu-id="d9da7-114">[Microsoft. extension. Console NuGet 패키지](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span><span class="sxs-lookup"><span data-stu-id="d9da7-114">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
