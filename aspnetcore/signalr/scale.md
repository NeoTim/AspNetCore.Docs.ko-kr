---
title: ASP.NET Core SignalR 프로덕션 호스팅 및 크기 조정
author: bradygaster
description: ASP.NET Core SignalR를 사용 하는 앱에서 성능 및 크기 조정 문제를 방지 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
no-loc:
- SignalR
uid: signalr/scale
ms.openlocfilehash: bb32bb8617f8a3e4170eeb7e38696ee2bbcafe03
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172548"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a>ASP.NET Core SignalR 호스팅 및 크기 조정

[Andrew Stanton-간호사](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster)및 [Tom Dykstra](https://github.com/tdykstra)

이 문서에서는 ASP.NET Core SignalR를 사용 하는 트래픽이 많은 앱에 대 한 호스팅 및 확장 고려 사항을 설명 합니다.

## <a name="sticky-sessions"></a>고정 세션

SignalR에서는 특정 연결에 대 한 모든 HTTP 요청을 동일한 서버 프로세스에서 처리 해야 합니다. 서버 팜 (여러 서버)에서 SignalR를 실행 하는 경우 "고정 세션"을 사용 해야 합니다. "고정 세션"은 일부 부하 분산 장치에서 세션 선호도 라고도 합니다. Azure App Service는 ARR ( [응용 프로그램 요청 라우팅](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) )을 사용 하 여 요청을 라우팅합니다. Azure App Service에서 "ARR 선호도" 설정을 사용 하도록 설정 하면 "고정 세션"을 사용할 수 있습니다. 고정 세션이 필요 하지 않은 유일한 경우는 다음과 같습니다.

1. 단일 서버에서 호스팅할 때 단일 프로세스에서.
1. Azure SignalR 서비스를 사용 하는 경우.
1. 모든 클라이언트가 Websocket **만** 사용 하도록 구성 되 **고** [skipnegotiation 설정은](xref:signalr/configuration#configure-additional-options) 클라이언트 구성에서 사용 하도록 설정 됩니다.

다른 모든 상황 (Redis 후면판을 사용 하는 경우 포함)에서는 고정 세션에 대해 서버 환경을 구성 해야 합니다.

SignalR에 대 한 Azure App Service를 구성 하는 방법에 대 한 지침은 <xref:signalr/publish-to-azure-web-app>를 참조 하세요.

## <a name="tcp-connection-resources"></a>TCP 연결 리소스

웹 서버에서 지원할 수 있는 동시 TCP 연결 수가 제한 됩니다. 표준 HTTP 클라이언트는 *임시* 연결을 사용 합니다. 클라이언트를 유휴 상태로 전환 하 고 나중에 다시 열 때 이러한 연결을 닫을 수 있습니다. 반면에 SignalR 연결은 *영구적*입니다. SignalR 연결은 클라이언트가 유휴 상태가 되는 경우에도 계속 열려 있습니다. 많은 클라이언트에 서비스를 제공 하는 트래픽이 많은 앱에서 이러한 영구 연결을 사용 하면 서버가 최대 연결 수에 도달할 수 있습니다.

또한 영구 연결에서는 추가 메모리를 사용 하 여 각 연결을 추적 합니다.

SignalR에서 연결 관련 리소스를 많이 사용 하는 것은 동일한 서버에서 호스트 되는 다른 웹 앱에 영향을 줄 수 있습니다. SignalR가 열려 있고 마지막으로 사용 가능한 TCP 연결을 보유 하 고 있는 경우 같은 서버의 다른 웹 앱에도 더 이상 사용할 수 있는 연결이 없습니다.

서버에 연결 되지 않은 경우 임의의 소켓 오류 및 연결 다시 설정 오류가 표시 됩니다. 예들 들어 다음과 같습니다.

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

SignalR 리소스 사용이 다른 웹 앱에서 오류를 발생 시 키 지 않도록 하려면 다른 웹 앱과 다른 서버에서 SignalR를 실행 합니다.

SignalR 리소스를 사용 하 여 SignalR 앱에서 오류를 발생 시 키 지 않도록 확장 하 여 서버가 처리 해야 하는 연결 수를 제한 합니다.

## <a name="scale-out"></a>확장

SignalR를 사용 하는 앱은 모든 연결을 추적 하 여 서버 팜에 대 한 문제를 생성 해야 합니다. 서버를 추가 하 고 다른 서버에서 알지 못하는 새 연결을 가져옵니다. 예를 들어 다음 다이어그램의 각 서버에 있는 SignalR는 다른 서버에서의 연결을 인식 하지 못합니다. 서버 중 하나의 SignalR가 모든 클라이언트에 메시지를 보내려고 할 때 메시지는 해당 서버에 연결 된 클라이언트로만 이동 합니다.

![후면판 없이 SignalR 크기 조정](scale/_static/scale-no-backplane.png)

이 문제를 해결 하기 위한 옵션은 [Azure SignalR 서비스](#azure-signalr-service) 및 [Redis 백플레인](#redis-backplane)입니다.

## <a name="azure-signalr-service"></a>Azure SignalR Service

Azure SignalR Service는 후면판이 아닌 프록시입니다. 클라이언트는 서버에 대 한 연결을 시작할 때마다 서비스에 연결 하기 위해 리디렉션됩니다. 이 프로세스는 다음 다이어그램에 설명 되어 있습니다.

![Azure SignalR Service에 대 한 연결 설정](scale/_static/azure-signalr-service-one-connection.png)

그 결과 서비스는 모든 클라이언트 연결을 관리 하는 반면, 각 서버는 다음 다이어그램에 표시 된 것과 같이 서비스에 대 한 연결 수가 작은 경우에만 필요 합니다.

![서비스에 연결 된 클라이언트, 서비스에 연결 된 서버](scale/_static/azure-signalr-service-multiple-connections.png)

규모 확장에 대 한이 방법은 Redis 후면판 대안에 비해 몇 가지 이점이 있습니다.

* [클라이언트 선호도](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)라고도 하는 고정 세션은 클라이언트에서 연결할 때 Azure SignalR 서비스로 즉시 리디렉션되도록 요구 되지 않습니다.
* SignalR 앱은 전송 된 메시지 수를 기준으로 규모를 확장할 수 있으며, Azure SignalR 서비스는 연결 수를 자동으로 조정 하는 동안 자동으로 크기를 조정 합니다. 예를 들어 수천 개의 클라이언트가 있을 수 있지만, 초당 몇 개의 메시지만 보내는 경우 SignalR 앱은 연결 자체를 처리 하기 위해 여러 서버로 확장할 필요가 없습니다.
* SignalR 앱은 SignalR 없이 웹 앱 보다 훨씬 더 많은 연결 리소스를 사용 하지 않습니다.

이러한 이유로 App Service, Vm 및 컨테이너를 포함 하 여 Azure에서 호스트 되는 모든 ASP.NET Core SignalR 앱에 대해 Azure SignalR 서비스를 권장 합니다.

자세한 내용은 [Azure SignalR Service 설명서](/azure/azure-signalr/signalr-overview)를 참조 하세요.

## <a name="redis-backplane"></a>Redis 백플레인

[Redis](https://redis.io/) 는 게시/구독 모델을 사용 하 여 메시징 시스템을 지 원하는 메모리 내 키-값 저장소입니다. SignalR Redis 후면판은 pub/sub 기능을 사용 하 여 메시지를 다른 서버로 전달 합니다. 클라이언트에서 연결 하면 연결 정보가 후면판에 전달 됩니다. 서버는 모든 클라이언트에 메시지를 보내려고 할 때 후면판으로 보냅니다. 후면판은 연결 된 모든 클라이언트와 해당 클라이언트가 있는 서버를 인식 합니다. 각 서버를 통해 모든 클라이언트에 메시지를 보냅니다. 이 프로세스는 다음 다이어그램에 설명 되어 있습니다.

![Redis 백플레인, 한 서버에서 모든 클라이언트로 전송 되는 메시지](scale/_static/redis-backplane.png)

Redis 후면판은 사용자의 인프라에서 호스트 되는 앱에 대해 권장 되는 스케일 아웃 방법입니다. 데이터 센터와 Azure 데이터 센터 간에 상당한 연결 대기 시간이 있는 경우 Azure SignalR Service는 대기 시간이 짧고 처리량이 많은 요구 사항이 있는 온-프레미스 앱에 대 한 실용적인 옵션이 아닐 수 있습니다.

앞에서 설명한 Azure SignalR 서비스 이점은 Redis 후면판의 단점입니다.

* 다음 두 가지 조건에 **모두** 해당 하는 경우를 제외 하 고, [클라이언트 선호도](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)라고도 하는 고정 세션이 필요 합니다.
  * 모든 클라이언트는 Websocket **만** 사용 하도록 구성 됩니다.
  * [Skipnegotiation 설정은](xref:signalr/configuration#configure-additional-options) 클라이언트 구성에서 사용 하도록 설정 됩니다. 
   서버에서 연결을 시작한 후에는 해당 서버에서 연결을 유지 해야 합니다.
* SignalR 앱은 전송 되는 메시지가 적은 경우에도 클라이언트 수에 따라 규모를 확장 해야 합니다.
* SignalR 앱은 SignalR없이 웹 앱 보다 훨씬 더 많은 연결 리소스를 사용 합니다.

## <a name="iis-limitations-on-windows-client-os"></a>Windows 클라이언트 OS에 대 한 IIS 제한 사항

Windows 10 및 Windows 8.x은 클라이언트 운영 체제입니다. 클라이언트 운영 체제의 IIS는 동시 연결 수가 10 개로 제한 됩니다. SignalR의 연결은 다음과 같습니다.

* 일시적 이며 자주 다시 설정 됩니다.
* 더 이상 사용 되지 않는 경우 즉시 삭제 **되지 않습니다** .

위의 조건은 클라이언트 OS에서 10 개의 연결 제한에 도달할 가능성이 높습니다. 개발에 클라이언트 OS를 사용 하는 경우 다음을 수행 하는 것이 좋습니다.

* IIS를 사용 하지 않습니다.
* Kestrel 또는 IIS Express을 배포 대상으로 사용 합니다.

## <a name="linux-with-nginx"></a>Nginx를 사용하는 Linux

SignalR Websocket에 대해 프록시의 `Connection` 및 `Upgrade` 헤더를 다음으로 설정 합니다.

```nginx
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

자세한 내용은 [NGINX as a WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx/)(NGINX를 WebSocket 프록시로 사용)를 참조하세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하십시오.

* [Azure SignalR 서비스 설명서](/azure/azure-signalr/signalr-overview)
* [Redis 후면판 설정](xref:signalr/redis-backplane)
