---
title: ASP.NET Core SignalR 지원 플랫폼
author: bradygaster
description: ASP.NET Core SignalR 지원 플랫폼에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- ASP.NET Core Identity
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
ms.openlocfilehash: a342dd787eceadd22ac26b57a3615a6b0b21f461
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754504"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a>ASP.NET Core SignalR 지원 플랫폼

## <a name="server-system-requirements"></a>서버 시스템 요구 사항

SignalR ASP.NET Core은 ASP.NET Core에서 지 원하는 모든 서버 플랫폼을 지원 합니다.

## <a name="javascript-client"></a>JavaScript 클라이언트

[JavaScript 클라이언트](xref:signalr/javascript-client) 는 nodejs 8 이상 버전과 다음 브라우저에서 실행 됩니다.

| 브라우저                          | 버전         |
| -------------------------------- | --------------- |
| IOS를 비롯 한 Apple Safari      | 현재&dagger; |
| Google Chrome(Android 포함) | 현재&dagger; |
| Microsoft Edge                   | 현재&dagger; |
| Mozilla Firefox                  | 현재&dagger; |

&dagger;*Current* 는 최신 버전의 브라우저를 나타냅니다.

## <a name="net-client"></a>.NET 클라이언트

[.Net 클라이언트](xref:signalr/dotnet-client) 는 ASP.NET Core에서 지 원하는 모든 플랫폼에서 실행 됩니다. 예를 들어 xamarin 개발자는 xamarin.ios 11.14.0.4 이상을 사용 하 여 8.4.0.1 이상 및 iOS 앱을 사용 하 여 Android 앱을 빌드하는 [데 사용할 SignalR 수 있습니다](https://github.com/aspnet/Announcements/issues/305) .

서버에서 IIS를 실행 하는 경우 Websocket 전송에는 Windows Server 2012 이상에서 IIS 8.0 이상이 필요 합니다. 다른 전송은 모든 플랫폼에서 지원 됩니다.

## <a name="java-client"></a>Java 클라이언트

[Java 클라이언트](xref:signalr/java-client) 는 java 8 이상 버전을 지원 합니다.

## <a name="unsupported-clients"></a>지원 되지 않는 클라이언트

다음 클라이언트는 사용할 수 있지만 실험적 이거나 비공식적입니다. 현재 지원 되지 않으며 그렇지 않을 수도 있습니다.

* [C + + 클라이언트](https://github.com/aspnet/SignalR-Client-Cpp)

* [Swift 클라이언트](https://github.com/moozzyk/SignalR-Client-Swift)
