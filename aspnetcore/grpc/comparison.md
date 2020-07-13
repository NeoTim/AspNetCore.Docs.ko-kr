---
title: gRPC 서비스와 HTTP API 비교
author: jamesnk
description: gRPC와 HTTP API를 비교한 방법과 권장 시나리오를 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/comparison
ms.openlocfilehash: caf9e99bcd4a1887fe5d69a3641fabee08573ef1
ms.sourcegitcommit: d1fa3d69dda675d7a52c7100742dfa6297413376
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86093310"
---
# <a name="compare-grpc-services-with-http-apis"></a><span data-ttu-id="9ce5a-103">gRPC 서비스와 HTTP API 비교</span><span class="sxs-lookup"><span data-stu-id="9ce5a-103">Compare gRPC services with HTTP APIs</span></span>

<span data-ttu-id="9ce5a-104">작성자: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="9ce5a-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="9ce5a-105">이 문서에서는 [gRPC 서비스](https://grpc.io/docs/guides/)와 JSON을 사용한 HTTP API(ASP.NET Core [Web API](xref:web-api/index) 포함)를 비교하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-105">This article explains how [gRPC services](https://grpc.io/docs/guides/) compare to HTTP APIs with JSON (including ASP.NET Core [web APIs](xref:web-api/index)).</span></span> <span data-ttu-id="9ce5a-106">앱에 대한 API를 제공하는 데 사용되는 기술은 중요한 선택 이며 gRPC는 HTTP API와 비교해서 고유한 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-106">The technology used to provide an API for your app is an important choice, and gRPC offers unique benefits compared to HTTP APIs.</span></span> <span data-ttu-id="9ce5a-107">이 문서에서는 gRPC의 장점과 단점을 설명하 고 다른 기술에서 gRPC를 사용하는 시나리오를 권장합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-107">This article discusses the strengths and weaknesses of gRPC and recommends scenarios for using gRPC over other technologies.</span></span>

## <a name="high-level-comparison"></a><span data-ttu-id="9ce5a-108">개괄적인 비교</span><span class="sxs-lookup"><span data-stu-id="9ce5a-108">High-level comparison</span></span>

<span data-ttu-id="9ce5a-109">다음 표에서는 gRPC와 JSON을 사용하는 HTTP API 간의 고급 기능 비교를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-109">The following table offers a high-level comparison of features between gRPC and HTTP APIs with JSON.</span></span>

| <span data-ttu-id="9ce5a-110">기능</span><span class="sxs-lookup"><span data-stu-id="9ce5a-110">Feature</span></span>          | <span data-ttu-id="9ce5a-111">gRPC</span><span class="sxs-lookup"><span data-stu-id="9ce5a-111">gRPC</span></span>                                               | <span data-ttu-id="9ce5a-112">JSON을 사용하는 HTTP API</span><span class="sxs-lookup"><span data-stu-id="9ce5a-112">HTTP APIs with JSON</span></span>           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| <span data-ttu-id="9ce5a-113">계약</span><span class="sxs-lookup"><span data-stu-id="9ce5a-113">Contract</span></span>         | <span data-ttu-id="9ce5a-114">필수( *.proto*)</span><span class="sxs-lookup"><span data-stu-id="9ce5a-114">Required (*.proto*)</span></span>                                | <span data-ttu-id="9ce5a-115">선택 사항(OpenAPI)</span><span class="sxs-lookup"><span data-stu-id="9ce5a-115">Optional (OpenAPI)</span></span>            |
| <span data-ttu-id="9ce5a-116">프로토콜</span><span class="sxs-lookup"><span data-stu-id="9ce5a-116">Protocol</span></span>         | <span data-ttu-id="9ce5a-117">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="9ce5a-117">HTTP/2</span></span>                                             | <span data-ttu-id="9ce5a-118">HTTP</span><span class="sxs-lookup"><span data-stu-id="9ce5a-118">HTTP</span></span>                          |
| <span data-ttu-id="9ce5a-119">Payload</span><span class="sxs-lookup"><span data-stu-id="9ce5a-119">Payload</span></span>          | <span data-ttu-id="9ce5a-120">[Protobuf](#performance)(소형, 이진)</span><span class="sxs-lookup"><span data-stu-id="9ce5a-120">[Protobuf (small, binary)](#performance)</span></span>           | <span data-ttu-id="9ce5a-121">JSON(대형, 사람이 읽을 수 있음)</span><span class="sxs-lookup"><span data-stu-id="9ce5a-121">JSON (large, human readable)</span></span>  |
| <span data-ttu-id="9ce5a-122">규범</span><span class="sxs-lookup"><span data-stu-id="9ce5a-122">Prescriptiveness</span></span> | [<span data-ttu-id="9ce5a-123">엄격한 사양</span><span class="sxs-lookup"><span data-stu-id="9ce5a-123">Strict specification</span></span>](#strict-specification)      | <span data-ttu-id="9ce5a-124">느슨함.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-124">Loose.</span></span> <span data-ttu-id="9ce5a-125">모든 HTTP가 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-125">Any HTTP is valid.</span></span>     |
| <span data-ttu-id="9ce5a-126">스트리밍</span><span class="sxs-lookup"><span data-stu-id="9ce5a-126">Streaming</span></span>        | [<span data-ttu-id="9ce5a-127">클라이언트, 서버, 양방향</span><span class="sxs-lookup"><span data-stu-id="9ce5a-127">Client, server, bi-directional</span></span>](#streaming)       | <span data-ttu-id="9ce5a-128">클라이언트, 서버</span><span class="sxs-lookup"><span data-stu-id="9ce5a-128">Client, server</span></span>                |
| <span data-ttu-id="9ce5a-129">브라우저 지원</span><span class="sxs-lookup"><span data-stu-id="9ce5a-129">Browser support</span></span>  | [<span data-ttu-id="9ce5a-130">아니요(gRPC-웹 필요)</span><span class="sxs-lookup"><span data-stu-id="9ce5a-130">No (requires grpc-web)</span></span>](#limited-browser-support) | <span data-ttu-id="9ce5a-131">예</span><span class="sxs-lookup"><span data-stu-id="9ce5a-131">Yes</span></span>                           |
| <span data-ttu-id="9ce5a-132">보안</span><span class="sxs-lookup"><span data-stu-id="9ce5a-132">Security</span></span>         | <span data-ttu-id="9ce5a-133">전송(TLS)</span><span class="sxs-lookup"><span data-stu-id="9ce5a-133">Transport (TLS)</span></span>                                    | <span data-ttu-id="9ce5a-134">전송(TLS)</span><span class="sxs-lookup"><span data-stu-id="9ce5a-134">Transport (TLS)</span></span>               |
| <span data-ttu-id="9ce5a-135">클라이언트 코드 생성</span><span class="sxs-lookup"><span data-stu-id="9ce5a-135">Client code-generation</span></span> | [<span data-ttu-id="9ce5a-136">예</span><span class="sxs-lookup"><span data-stu-id="9ce5a-136">Yes</span></span>](#code-generation)                      | <span data-ttu-id="9ce5a-137">OpenAPI + 타사 도구</span><span class="sxs-lookup"><span data-stu-id="9ce5a-137">OpenAPI + third-party tooling</span></span> |

## <a name="grpc-strengths"></a><span data-ttu-id="9ce5a-138">gRPC 장점</span><span class="sxs-lookup"><span data-stu-id="9ce5a-138">gRPC strengths</span></span>

### <a name="performance"></a><span data-ttu-id="9ce5a-139">성능</span><span class="sxs-lookup"><span data-stu-id="9ce5a-139">Performance</span></span>

<span data-ttu-id="9ce5a-140">gRPC 메시지는 효율적인 이진 메시지 형식인 [Protobuf](https://developers.google.com/protocol-buffers/docs/overview)를 사용하여 직렬화됩니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-140">gRPC messages are serialized using [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), an efficient binary message format.</span></span> <span data-ttu-id="9ce5a-141">Protobuf는 서버와 클라이언트에서 매우 빠르게 직렬화합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-141">Protobuf serializes very quickly on the server and client.</span></span> <span data-ttu-id="9ce5a-142">Protobuf serialization은 작은 메시지 페이로드를 발생시키며 이는 모바일 앱과 같은 제한된 대역폭 시나리오에서 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-142">Protobuf serialization results in small message payloads, important in limited bandwidth scenarios like mobile apps.</span></span>

<span data-ttu-id="9ce5a-143">gRPC는 HTTP 1.x에 비해 상당한 성능 이점을 제공하는, HTTP의 주요 개정판인 HTTP/2용으로 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-143">gRPC is designed for HTTP/2, a major revision of HTTP that provides significant performance benefits over HTTP 1.x:</span></span>

* <span data-ttu-id="9ce5a-144">이진 프레이밍 및 압축.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-144">Binary framing and compression.</span></span> <span data-ttu-id="9ce5a-145">HTTP/2 프로토콜은 간단하며, 보내고 받을 때 모두 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-145">HTTP/2 protocol is compact and efficient both in sending and receiving.</span></span>
* <span data-ttu-id="9ce5a-146">단일 TCP 연결보다 여러 HTTP/2 호출의 멀티플렉싱.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-146">Multiplexing of multiple HTTP/2 calls over a single TCP connection.</span></span> <span data-ttu-id="9ce5a-147">멀티플렉싱은 [HOL(Head of Line) 차단](https://en.wikipedia.org/wiki/Head-of-line_blocking)을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-147">Multiplexing eliminates [head-of-line blocking](https://en.wikipedia.org/wiki/Head-of-line_blocking).</span></span>

<span data-ttu-id="9ce5a-148">HTTP/2는 gRPC에만 국한되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-148">HTTP/2 is not exclusive to gRPC.</span></span> <span data-ttu-id="9ce5a-149">JSON을 사용한 HTTP API를 포함하여 다양한 요청 형식에서 HTTP/2를 사용하고 성능 개선을 활용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-149">Many request types, including HTTP APIs with JSON, can use HTTP/2 and benefit from its performance improvements.</span></span>

### <a name="code-generation"></a><span data-ttu-id="9ce5a-150">코드 생성</span><span class="sxs-lookup"><span data-stu-id="9ce5a-150">Code generation</span></span>

<span data-ttu-id="9ce5a-151">모든 gRPC 프레임워크는 코드 생성에 대한 최고 수준의 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-151">All gRPC frameworks provide first-class support for code generation.</span></span> <span data-ttu-id="9ce5a-152">gRPC 개발에 대한 핵심 파일은 gRPC 서비스 및 메시지의 계약을 정의하는 [.proto file](https://developers.google.com/protocol-buffers/docs/proto3)입니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-152">A core file to gRPC development is the [.proto file](https://developers.google.com/protocol-buffers/docs/proto3), which defines the contract of gRPC services and messages.</span></span> <span data-ttu-id="9ce5a-153">이 파일에서 gRPC 프레임워크는 서비스 기본 클래스, 메시지 및 전체 클라이언트를 코드 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-153">From this file gRPC frameworks will code generate a service base class, messages, and a complete client.</span></span>

<span data-ttu-id="9ce5a-154">서버와 클라이언트 간에 *proto* 파일을 공유하여 메시지와 클라이언트 코드를 종단 간에 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-154">By sharing the *.proto* file between the server and client, messages and client code can be generated from end to end.</span></span> <span data-ttu-id="9ce5a-155">클라이언트의 코드 생성은 클라이언트와 서버에서 메시지의 중복을 제거하고 강력한 형식의 클라이언트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-155">Code generation of the client eliminates duplication of messages on the client and server, and creates a strongly-typed client for you.</span></span> <span data-ttu-id="9ce5a-156">클라이언트를 작성하지 않아도 되므로 많은 서비스를 갖춘 응용 프로그램의 개발 시간이 상당히 절감됩니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-156">Not having to write a client saves significant development time in applications with many services.</span></span>

### <a name="strict-specification"></a><span data-ttu-id="9ce5a-157">엄격한 사양</span><span class="sxs-lookup"><span data-stu-id="9ce5a-157">Strict specification</span></span>

<span data-ttu-id="9ce5a-158">JSON을 사용하는 HTTP API에 대한 공식 사양은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-158">A formal specification for HTTP API with JSON doesn't exist.</span></span> <span data-ttu-id="9ce5a-159">개발자는 URL, HTTP 동사 및 응답 코드의 가장 좋은 형식을 논의합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-159">Developers debate the best format of URLs, HTTP verbs, and response codes.</span></span>

<span data-ttu-id="9ce5a-160">[gRPC 사양](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md)은 gRPC 서비스가 따라야 하는 형식에 대한 지침입니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-160">The [gRPC specification](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) is prescriptive about the format a gRPC service must follow.</span></span> <span data-ttu-id="9ce5a-161">gRPC는 플랫폼 및 구현에 상관없이 일치하므로 논쟁이 불필요하며 개발자 시간을 절약합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-161">gRPC eliminates debate and saves developer time because gRPC is consistent across platforms and implementations.</span></span>

### <a name="streaming"></a><span data-ttu-id="9ce5a-162">스트리밍</span><span class="sxs-lookup"><span data-stu-id="9ce5a-162">Streaming</span></span>

<span data-ttu-id="9ce5a-163">HTTP/2는 수명이 긴 실시간 통신 스트림에 대한 기초를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-163">HTTP/2 provides a foundation for long-lived, real-time communication streams.</span></span> <span data-ttu-id="9ce5a-164">gRPC는 HTTP/2를 통한 스트리밍을 위한 최고 수준의 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-164">gRPC provides first-class support for streaming through HTTP/2.</span></span>

<span data-ttu-id="9ce5a-165">gRPC 서비스는 모든 스트리밍 조합을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-165">A gRPC service supports all streaming combinations:</span></span>

* <span data-ttu-id="9ce5a-166">단항(스트리밍 없음)</span><span class="sxs-lookup"><span data-stu-id="9ce5a-166">Unary (no streaming)</span></span>
* <span data-ttu-id="9ce5a-167">서버-클라이언트 스트리밍</span><span class="sxs-lookup"><span data-stu-id="9ce5a-167">Server to client streaming</span></span>
* <span data-ttu-id="9ce5a-168">클라이언트-서버 스트리밍</span><span class="sxs-lookup"><span data-stu-id="9ce5a-168">Client to server streaming</span></span>
* <span data-ttu-id="9ce5a-169">양방향 스트리밍</span><span class="sxs-lookup"><span data-stu-id="9ce5a-169">Bi-directional streaming</span></span>

### <a name="deadlinetimeouts-and-cancellation"></a><span data-ttu-id="9ce5a-170">최종 기한/시간 초과 및 취소</span><span class="sxs-lookup"><span data-stu-id="9ce5a-170">Deadline/timeouts and cancellation</span></span>

<span data-ttu-id="9ce5a-171">gRPC는 클라이언트가 RPC가 완료될 때까지 대기하는 기간을 지정하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-171">gRPC allows clients to specify how long they are willing to wait for an RPC to complete.</span></span> <span data-ttu-id="9ce5a-172">[최종 기한](https://grpc.io/blog/deadlines)이 서버에 전송되고 서버에서 최종 기한을 초과하는 경우 수행할 작업을 결정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-172">The [deadline](https://grpc.io/blog/deadlines) is sent to the server, and the server can decide what action to take if it exceeds the deadline.</span></span> <span data-ttu-id="9ce5a-173">예를 들어 서버에서 제한 시간에 진행 중인 gRPC/HTTP/데이터베이스 요청을 취소할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-173">For example, the server might cancel in-progress gRPC/HTTP/database requests on timeout.</span></span>

<span data-ttu-id="9ce5a-174">자식 gRPC 호출을 통해 최종 기한 및 취소를 전파하면 리소스 사용 제한을 강제로 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-174">Propagating the deadline and cancellation through child gRPC calls helps enforce resource usage limits.</span></span>

## <a name="grpc-recommended-scenarios"></a><span data-ttu-id="9ce5a-175">gRPC 권장 시나리오</span><span class="sxs-lookup"><span data-stu-id="9ce5a-175">gRPC recommended scenarios</span></span>

<span data-ttu-id="9ce5a-176">gRPC는 다음과 같은 시나리오에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-176">gRPC is well suited to the following scenarios:</span></span>

* <span data-ttu-id="9ce5a-177">**마이크로 서비스**: gRPC는 대기 시간이 짧고 처리량이 높은 통신을 위해 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-177">**Microservices**: gRPC is designed for low latency and high throughput communication.</span></span> <span data-ttu-id="9ce5a-178">gRPC는 효율성이 중요한 경량 마이크로 서비스에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-178">gRPC is great for lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="9ce5a-179">**지점 간 실시간 통신**: 양방향 스트리밍을 위한 뛰어난 지원 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-179">**Point-to-point real-time communication**: gRPC has excellent support for bi-directional streaming.</span></span> <span data-ttu-id="9ce5a-180">gRPC 서비스는 폴링을 사용하지 않고 실시간으로 메시지를 푸시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-180">gRPC services can push messages in real-time without polling.</span></span>
* <span data-ttu-id="9ce5a-181">**Polyglot 환경**: gRPC 도구는 널리 사용되는 모든 개발 언어를 지원하며, 따라서 gRPC는 다중 언어 환경에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-181">**Polyglot environments**: gRPC tooling supports all popular development languages, making gRPC a good choice for multi-language environments.</span></span>
* <span data-ttu-id="9ce5a-182">**네트워크 제한 환경**: gRPC 메시지는 경량 메시지 형식인 Protobuf를 사용하여 직렬화됩니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-182">**Network constrained environments**: gRPC messages are serialized with Protobuf, a lightweight message format.</span></span> <span data-ttu-id="9ce5a-183">gRPC 메시지는 항상 해당하는 JSON 메시지보다 작습니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-183">A gRPC message is always smaller than an equivalent JSON message.</span></span>

## <a name="grpc-weaknesses"></a><span data-ttu-id="9ce5a-184">gRPC 약점</span><span class="sxs-lookup"><span data-stu-id="9ce5a-184">gRPC weaknesses</span></span>

### <a name="limited-browser-support"></a><span data-ttu-id="9ce5a-185">제한된 브라우저 지원</span><span class="sxs-lookup"><span data-stu-id="9ce5a-185">Limited browser support</span></span>

<span data-ttu-id="9ce5a-186">현재 브라우저에서 gRPC 서비스를 직접 호출하는 것은 불가능합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-186">It's impossible to directly call a gRPC service from a browser today.</span></span> <span data-ttu-id="9ce5a-187">gRPC는 HTTP/2 기능을 많이 사용하며, 브라우저에서 gRPC 클라이언트를 지원하기 위해 웹 요청에 필요한 제어 수준을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-187">gRPC heavily uses HTTP/2 features and no browser provides the level of control required over web requests to support a gRPC client.</span></span> <span data-ttu-id="9ce5a-188">예를 들어, 브라우저는 호출자가 HTTP/2를 사용하도록 요구하거나 기본 HTTP/2 프레임에 대한 액세스를 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-188">For example, browsers do not allow a caller to require that HTTP/2 be used, or provide access to underlying HTTP/2 frames.</span></span>

<span data-ttu-id="9ce5a-189">[gRPC-웹](https://grpc.io/docs/tutorials/basic/web.html)은 브라우저에서 제한적 gRPC 지원을 제공하는 gRPC 팀의 추가 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-189">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) is an additional technology from the gRPC team that provides limited gRPC support in the browser.</span></span> <span data-ttu-id="9ce5a-190">gRPC-웹은 모든 최신 브라우저를 지원하는 JavaScript 클라이언트와 서버 상의 gRPC-웹 프록시의 두 부분으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-190">gRPC-Web consists of two parts: a JavaScript client that supports all modern browsers, and a gRPC-Web proxy on the server.</span></span> <span data-ttu-id="9ce5a-191">gRPC-웹 클라이언트는 프록시를 호출하고 프록시는 gRPC 요청을 gRPC 서버에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-191">The gRPC-Web client calls the proxy and the proxy will forward on the gRPC requests to the gRPC server.</span></span>

<span data-ttu-id="9ce5a-192">gRPC-웹에서 모든 gRPC 기능을 지원하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-192">Not all of gRPC's features are supported by gRPC-Web.</span></span> <span data-ttu-id="9ce5a-193">클라이언트 및 양방향 스트리밍이 지원되지 않으며 서버 스트리밍이 제한적으로 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-193">Client and bi-directional streaming isn't supported, and there is limited support for server streaming.</span></span>

> [!TIP]
> <span data-ttu-id="9ce5a-194">.NET Core는 gRPC-Web을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-194">.NET Core has support for gRPC-Web.</span></span> <span data-ttu-id="9ce5a-195">자세한 내용은 <xref:grpc/browser>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-195">Visit <xref:grpc/browser> for more information.</span></span>

### <a name="not-human-readable"></a><span data-ttu-id="9ce5a-196">사람이 읽을 수 없음</span><span class="sxs-lookup"><span data-stu-id="9ce5a-196">Not human readable</span></span>

<span data-ttu-id="9ce5a-197">HTTP API 요청은 텍스트로 전송되며, 사람이 읽고 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-197">HTTP API requests are sent as text and can be read and created by humans.</span></span>

<span data-ttu-id="9ce5a-198">gRPC 메시지는 기본적으로 Protobuf로 인코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-198">gRPC messages are encoded with Protobuf by default.</span></span> <span data-ttu-id="9ce5a-199">Protobuf는 송신 및 수신에 효율적이지만, 이진 형식은 사람이 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-199">While Protobuf is efficient to send and receive, its binary format isn't human readable.</span></span> <span data-ttu-id="9ce5a-200">Protobuf를 사용하려면 올바른 역직렬화를 위해 *.proto* 파일에 지정된 메시지의 인터페이스 설명이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-200">Protobuf requires the message's interface description specified in the *.proto* file to properly deserialize.</span></span> <span data-ttu-id="9ce5a-201">네트워크에서 Protobuf 페이로드를 분석하고 요청을 직접 작성하려면 추가 도구가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-201">Additional tooling is required to analyze Protobuf payloads on the wire and to compose requests by hand.</span></span>

<span data-ttu-id="9ce5a-202">[서버 리플렉션](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) 및 [gRPC 명령줄 도구](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md)와 같은 기능은 이진 Protobuf 메시지를 지원하기 위해 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-202">Features such as [server reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) and the [gRPC command line tool](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) exist to assist with binary Protobuf messages.</span></span> <span data-ttu-id="9ce5a-203">또한 Protobuf 메시지는 [JSON 변환](https://developers.google.com/protocol-buffers/docs/proto3#json)을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-203">Also, Protobuf messages support [conversion to and from JSON](https://developers.google.com/protocol-buffers/docs/proto3#json).</span></span> <span data-ttu-id="9ce5a-204">기본 제공 JSON 변환은 디버그할 때 Protobuf 메시지를 사람이 읽을 수 있는 형식으로 변환하는 효율적인 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-204">The built-in JSON conversion provides an efficient way to convert Protobuf messages to and from human readable form when debugging.</span></span>

## <a name="alternative-framework-scenarios"></a><span data-ttu-id="9ce5a-205">대체 프레임워크 시나리오</span><span class="sxs-lookup"><span data-stu-id="9ce5a-205">Alternative framework scenarios</span></span>

<span data-ttu-id="9ce5a-206">다음과 같은 시나리오에서는 gRPC보다 다른 프레임워크가 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-206">Other frameworks are recommended over gRPC in the following scenarios:</span></span>

* <span data-ttu-id="9ce5a-207">**브라우저에서 액세스 가능한 API**: gRPC는 브라우저에서 완전히 지원되지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-207">**Browser accessible APIs**: gRPC isn't fully supported in the browser.</span></span> <span data-ttu-id="9ce5a-208">gRPC-웹은 브라우저 지원을 제공할 수 있지만 제한 사항이 있으며 서버 프록시를 도입합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-208">gRPC-Web can offer browser support, but it has limitations and introduces a server proxy.</span></span>
* <span data-ttu-id="9ce5a-209">**브로드캐스트 실시간 통신**: gRPC는 스트리밍을 통해 실시간 통신을 지원하지만 등록된 연결에 메시지를 브로드캐스트하는 개념이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-209">**Broadcast real-time communication**: gRPC supports real-time communication via streaming, but the concept of broadcasting a message out to registered connections doesn't exist.</span></span> <span data-ttu-id="9ce5a-210">예를 들어 대화방의 모든 클라이언트에 새 채팅 메시지를 보내야 하는 대화방 시나리오에서 각 gRPC 호출은 클라이언트에 새 채팅 메시지를 개별적으로 스트리밍하는 데 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-210">For example in a chat room scenario where new chat messages should be sent to all clients in the chat room, each gRPC call is required to individually stream new chat messages to the client.</span></span> <span data-ttu-id="9ce5a-211">[SignalR](xref:signalr/introduction)은 이 시나리오에 유용한 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-211">[SignalR](xref:signalr/introduction) is a useful framework for this scenario.</span></span> SignalR<span data-ttu-id="9ce5a-212">에는 영구 연결 개념과 메시지 브로드캐스트에 대한 기본 제공 지원이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-212"> has the concept of persistent connections and built-in support for broadcasting messages.</span></span>
* <span data-ttu-id="9ce5a-213">**프로세스 간 통신**: 들어오는 gRPC 호출을 허용하려면 프로세스에서 HTTP/2 서버를 호스팅해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-213">**Inter-process communication**: A process must host an HTTP/2 server to accept incoming gRPC calls.</span></span> <span data-ttu-id="9ce5a-214">Windows에서 프로세스 간 통신 [파이프](/dotnet/standard/io/pipe-operations)는 빠르고 간단한 통신 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="9ce5a-214">For Windows, inter-process communication [pipes](/dotnet/standard/io/pipe-operations) is a fast, lightweight method of communication.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9ce5a-215">추가 자료</span><span class="sxs-lookup"><span data-stu-id="9ce5a-215">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
