---
title: ASP.NET Core Web API에서 응답 데이터 서식 지정
author: ardalis
description: ASP.NET Core Web API에서 응답 데이터의 서식을 지정하는 방법을 알아봅니다.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
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
uid: web-api/advanced/formatting
ms.openlocfilehash: 8aa94bd1f33d1dd8ce8e7f50468ed60b4ccb2515
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019939"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="05e85-103">ASP.NET Core Web API에서 응답 데이터 서식 지정</span><span class="sxs-lookup"><span data-stu-id="05e85-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="05e85-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="05e85-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="05e85-105">ASP.NET Core MVC는 응답 데이터 서식 지정을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="05e85-106">응답 데이터는 특정 형식 또는 클라이언트 요청 형식에 대한 응답으로 형식을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="05e85-107">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="05e85-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="05e85-108">형식별 작업 결과</span><span class="sxs-lookup"><span data-stu-id="05e85-108">Format-specific Action Results</span></span>

<span data-ttu-id="05e85-109">일부 작업 결과 형식은 <xref:Microsoft.AspNetCore.Mvc.JsonResult> 및 <xref:Microsoft.AspNetCore.Mvc.ContentResult>와 같이 특정 형식과 관련됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="05e85-110">작업은 클라이언트 기본 설정에 관계 없이 특정 형식으로 서식이 지정된 결과를 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="05e85-111">예를 들어 `JsonResult`를 반환하면 JSON 형식의 데이터가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="05e85-112">`ContentResult` 또는 문자열 하나를 반환하면 일반 텍스트 형식의 문자열 데이터가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="05e85-113">특정 형식을 반환하는 작업은 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="05e85-114">ASP.NET Core는 모든 개체 반환 값을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="05e85-115"><xref:Microsoft.AspNetCore.Mvc.IActionResult> 형식이 아닌 개체를 반환하는 작업의 결과는 적절한 <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> 구현을 사용하여 직렬화됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="05e85-116">자세한 내용은 <xref:web-api/action-return-types>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="05e85-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="05e85-117">기본 제공 도우미 메서드 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*>는 다음 JSON 형식 데이터를 반환합니다. [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="05e85-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="05e85-118">샘플 다운로드는 작성자 목록을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="05e85-119">F12 브라우저 개발자 도구 또는 이전 코드의 [Postman](https://www.getpostman.com/tools) 사용:</span><span class="sxs-lookup"><span data-stu-id="05e85-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="05e85-120">\*\*content-type:이 포함된 응답 헤더 \*\* `application/json; charset=utf-8`이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="05e85-121">요청 헤더가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-121">The request headers are displayed.</span></span> <span data-ttu-id="05e85-122">예를 들어 `Accept` 헤더가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-122">For example, the `Accept` header.</span></span> <span data-ttu-id="05e85-123">`Accept` 헤더는 앞의 코드에서 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="05e85-124">서식 있는 일반 텍스트 데이터를 반환하려면 <xref:Microsoft.AspNetCore.Mvc.ContentResult> 및 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A> 도우미를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult> and the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="05e85-125">위의 코드에서 반환된 `Content-Type`은 `text/plain`입니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="05e85-126">문자열을 반환하면 `text/plain`의 `Content-Type`이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="05e85-127">반환 형식이 여러 개인 작업의 경우 `IActionResult`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="05e85-128">예를 들어 수행된 작업의 결과에 따라 서로 다른 HTTP 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="05e85-129">콘텐츠 협상</span><span class="sxs-lookup"><span data-stu-id="05e85-129">Content negotiation</span></span>

<span data-ttu-id="05e85-130">콘텐츠 협상은 클라이언트가 [헤더 수락](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)을 지정하는 경우 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="05e85-131">ASP.NET Core MVC에서 사용하는 기본 형식은 [JSON](https://json.org/)입니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="05e85-132">콘텐츠 협상은 다음과 같은 특징을 가집니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-132">Content negotiation is:</span></span>

* <span data-ttu-id="05e85-133"><xref:Microsoft.AspNetCore.Mvc.ObjectResult>에 의해 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="05e85-134">도우미 메서드에서 반환된 상태 코드별 작업 결과 상태 코드에 기본적으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="05e85-135">작업 결과 도우미 메서드는 `ObjectResult`를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="05e85-136">모델 형식이 반환되는 경우 반환 형식은 `ObjectResult`입니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="05e85-137">다음 작업 메서드는 `Ok` 및 `NotFound` 도우미 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="05e85-138">기본적으로 ASP.NET Core는 `application/json`, `text/json`, `text/plain` 미디어 형식을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-138">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="05e85-139">[Fiddler](https://www.telerik.com/fiddler) 또는 [Postman](https://www.getpostman.com/tools)과 같은 도구는 반환 형식을 지정하도록 `Accept` 요청 헤더를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="05e85-140">`Accept` 헤더에 서버가 지원하는 형식이 포함되어 있으면 해당 형식이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-140">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="05e85-141">다음 섹션에서는 추가적인 포맷터를 추가하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-141">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="05e85-142">컨트롤러 작업은 POCO(Plain Old CLR Objects)를 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-142">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="05e85-143">POCO가 반환되면 런타임에서는 개체를 래핑하는`ObjectResult`를 자동으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-143">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="05e85-144">클라이언트는 형식이 지정된 직렬화된 개체를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-144">The client gets the formatted serialized object.</span></span> <span data-ttu-id="05e85-145">반환되는 개체가 `null`이면 `204 No Content` 응답이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-145">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="05e85-146">개체 형식 반환하기:</span><span class="sxs-lookup"><span data-stu-id="05e85-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="05e85-147">위 코드에서는 유효한 작성자 별칭에 대한 요청이 작성자의 데이터와 함께 `200 OK` 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-147">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="05e85-148">잘못된 별칭에 대한 요청은 `204 No Content` 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-148">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="05e85-149">Accept 헤더</span><span class="sxs-lookup"><span data-stu-id="05e85-149">The Accept header</span></span>

<span data-ttu-id="05e85-150">콘텐츠 *협상*은 `Accept` 헤더가 요청에 나타나는 경우에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-150">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="05e85-151">요청에 Accept 헤더가 포함되어 있으면 ASP.NET Core는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-151">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="05e85-152">기본 설정 순서에 따라 Accept 헤더의 미디어 형식을 열거합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-152">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="05e85-153">지정된 형식 중 하나로 응답을 생성할 수 있는 포맷터를 찾으려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-153">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="05e85-154">클라이언트의 요청을 충족할 수 있는 포맷터가 없는 경우 ASP.NET Core는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-154">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="05e85-155"><xref:Microsoft.AspNetCore.Mvc.MvcOptions>이 설정된 경우 `406 Not Acceptable`을 반환하거나</span><span class="sxs-lookup"><span data-stu-id="05e85-155">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="05e85-156">응답을 생성할 수 있는 첫 번째 포맷터를 찾으려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-156">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="05e85-157">요청된 형식에 대해 포맷터가 구성되지 않은 경우에는 개체의 서식을 지정할 수 있는 첫 번째 포맷터가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-157">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="05e85-158">요청에 `Accept` 헤더가 표시되지 않을 경우 다음이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-158">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="05e85-159">개체를 처리할 수 있는 첫 번째 포맷터는 응답을 직렬화하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-159">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="05e85-160">발생하는 협상이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-160">There isn't any negotiation taking place.</span></span> <span data-ttu-id="05e85-161">서버에서 반환할 형식을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-161">The server is determining what format to return.</span></span>

<span data-ttu-id="05e85-162">Accept 헤더에 `*/*`가 포함되는 경우, `RespectBrowserAcceptHeader`가 <xref:Microsoft.AspNetCore.Mvc.MvcOptions>에서 true로 설정되지 않으면 헤더는 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-162">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="05e85-163">브라우저 및 콘텐츠 협상</span><span class="sxs-lookup"><span data-stu-id="05e85-163">Browsers and content negotiation</span></span>

<span data-ttu-id="05e85-164">일반적인 API 클라이언트와 달리 웹 브라우저는 `Accept` 헤더를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-164">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="05e85-165">웹 브라우저는 와일드카드를 비롯한 다양한 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-165">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="05e85-166">기본적으로 프레임워크가 브라우저에서 요청이 수신되는 것을 감지할 경우 다음이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-166">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="05e85-167">`Accept` 헤더는 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-167">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="05e85-168">달리 구성되지 않은 경우 콘텐츠가 JSON에 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-168">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="05e85-169">이것은 API를 소비할 때 브라우저에서 보다 일관된 환경을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-169">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="05e85-170">브라우저 Accept 헤더를 적용하도록 앱을 구성하려면 <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader>를 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-170">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="05e85-171">포맷터 구성</span><span class="sxs-lookup"><span data-stu-id="05e85-171">Configure formatters</span></span>

<span data-ttu-id="05e85-172">추가 형식을 지원해야 하는 앱은 적절한 NuGet 패키지를 추가하고 지원을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-172">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="05e85-173">입력 및 출력에 대한 개별 포맷터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-173">There are separate formatters for input and output.</span></span> <span data-ttu-id="05e85-174">입력 포맷터는 [모델 바인딩](xref:mvc/models/model-binding)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-174">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="05e85-175">출력 포맷터는 응답의 형식을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-175">Output formatters are used to format responses.</span></span> <span data-ttu-id="05e85-176">사용자 지정 포맷터 생성에 대한 정보는 [사용자 지정 포맷터](xref:web-api/advanced/custom-formatters)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="05e85-176">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="05e85-177">XML 형식 지원 추가</span><span class="sxs-lookup"><span data-stu-id="05e85-177">Add XML format support</span></span>

<span data-ttu-id="05e85-178"><xref:System.Xml.Serialization.XmlSerializer>를 사용하여 구현된 XML 포맷터는 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>를 호출하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-178">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="05e85-179">위의 코드는 `XmlSerializer`를 사용하여 결과를 직렬화합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-179">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="05e85-180">위의 코드를 사용할 때 컨트롤러 메서드는 요청의 `Accept` 헤더에 따라 적절한 형식을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-180">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="05e85-181">System.Text.Json 기반 포맷터 구성</span><span class="sxs-lookup"><span data-stu-id="05e85-181">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="05e85-182">`System.Text.Json` 기반 포맷터의 기능은 `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`를 사용하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-182">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="05e85-183">`JsonResult`를 사용하여 동작 단위로 출력 serialization 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-183">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="05e85-184">예:</span><span class="sxs-lookup"><span data-stu-id="05e85-184">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="05e85-185">Newtonsoft.Json 기반 JSON 형식 지원 추가</span><span class="sxs-lookup"><span data-stu-id="05e85-185">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="05e85-186">ASP.NET Core 3.0 이전에는 `Newtonsoft.Json` 패키지를 사용하여 구현된 JSON 포맷터를 기본적으로 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-186">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="05e85-187">ASP.NET Core 3.0 이후의 기본 JSON 포맷터는 `System.Text.Json`을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-187">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="05e85-188">`Newtonsoft.Json`기반 포맷터 및 기능에 대 한 지원은 [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet 패키지를 설치 하 고에서 구성 하 여 사용할 수 있습니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="05e85-188">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="05e85-189">일부 기능은 `System.Text.Json` 기반 포맷터와 잘 호환되지 않고 `Newtonsoft.Json` 기반 포맷터에 대한 참조를 필요로 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-189">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="05e85-190">앱이 다음과 같은 경우 `Newtonsoft.Json` 기반 포맷터를 계속해서 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-190">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="05e85-191">`Newtonsoft.Json` 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-191">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="05e85-192">예를 들어 `[JsonProperty]` 또는 `[JsonIgnore]`입니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-192">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="05e85-193">직렬화 설정을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-193">Customizes the serialization settings.</span></span>
* <span data-ttu-id="05e85-194">`Newtonsoft.Json`은 제공하는 기능에 의존합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-194">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="05e85-195">`Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`를 구성하는 경우.</span><span class="sxs-lookup"><span data-stu-id="05e85-195">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="05e85-196">ASP.NET Core 3.0 이전의 `JsonResult.SerializerSettings`는 `Newtonsoft.Json` 고유의 `JsonSerializerSettings`의 인스턴스를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-196">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="05e85-197">[OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) 문서를 생성하는 경우.</span><span class="sxs-lookup"><span data-stu-id="05e85-197">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="05e85-198">`Newtonsoft.Json` 기반 포맷터의 기능은 `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`를 사용하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-198">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="05e85-199">`JsonResult`를 사용하여 동작 단위로 출력 serialization 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-199">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="05e85-200">예:</span><span class="sxs-lookup"><span data-stu-id="05e85-200">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="05e85-201">XML 형식 지원 추가</span><span class="sxs-lookup"><span data-stu-id="05e85-201">Add XML format support</span></span>

<span data-ttu-id="05e85-202">XML 형식 지정은 [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet 패키지를 필요로 합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-202">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="05e85-203"><xref:System.Xml.Serialization.XmlSerializer>를 사용하여 구현된 XML 포맷터는 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>를 호출하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-203">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="05e85-204">위의 코드는 `XmlSerializer`를 사용하여 결과를 직렬화합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-204">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="05e85-205">위의 코드를 사용할 때 컨트롤러 메서드는 요청의 `Accept` 헤더에 따라 적절한 형식을 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-205">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="05e85-206">형식 지정</span><span class="sxs-lookup"><span data-stu-id="05e85-206">Specify a format</span></span>

<span data-ttu-id="05e85-207">응답 형식을 제한 하려면 필터를 적용 [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) 합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-207">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="05e85-208">대부분의 [필터](xref:mvc/controllers/filters)와 마찬가지로, `[Produces]` 작업, 컨트롤러 또는 전역 범위에서 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-208">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="05e85-209">이전 [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) 필터:</span><span class="sxs-lookup"><span data-stu-id="05e85-209">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="05e85-210">컨트롤러 내의 모든 작업이 JSON 형식 응답을 반환하도록 강제합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-210">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="05e85-211">다른 포맷터가 구성되고 클라이언트에서 다른 형식을 지정하는 경우 JSON이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-211">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="05e85-212">자세한 내용은 [필터](xref:mvc/controllers/filters)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="05e85-212">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="05e85-213">특수한 사례 포맷터</span><span class="sxs-lookup"><span data-stu-id="05e85-213">Special case formatters</span></span>

<span data-ttu-id="05e85-214">일부 특수한 경우 기본 제공 포맷터를 사용하여 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-214">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="05e85-215">기본적으로 `string` 반환 형식은 *text/plain*(`Accept` 헤더를 통해 요청된 경우 *text/html*)으로 형식이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-215">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="05e85-216">이 동작은 <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>를 제거하여 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-216">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="05e85-217">포맷터들은 `ConfigureServices` 방법에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-217">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="05e85-218">모델 개체 반환 형식이 있는 작업은 `null`을 반환하는 경우 `204 No Content`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-218">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="05e85-219">이 동작은 <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>를 제거하여 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-219">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="05e85-220">다음 코드를 `StringOutputFormatter` 및 `HttpNoContentOutputFormatter`를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-220">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="05e85-221">`StringOutputFormatter`가 없으면 기본 제공 JSON 포맷터가 `string` 반환 형식의 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-221">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="05e85-222">기본 제공 JSON 포맷터가 제거되고 XML 포맷터를 사용할 수 있는 경우, XML 포맷터가 `string` 반환 형식의 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-222">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="05e85-223">그렇지 않으면 `string` 반환 형식이 `406 Not Acceptable`을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-223">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="05e85-224">`HttpNoContentOutputFormatter`가 없으면 null 개체는 구성된 포맷터를 사용하여 서식이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-224">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="05e85-225">예:</span><span class="sxs-lookup"><span data-stu-id="05e85-225">For example:</span></span>

* <span data-ttu-id="05e85-226">JSON 포맷터는 `null`의 본문이 포함된 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-226">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="05e85-227">XML 포맷터는 `xsi:nil="true"`로 설정된 특성을 사용하여 빈 XML 요소를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-227">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="05e85-228">응답 형식 URL 매핑</span><span class="sxs-lookup"><span data-stu-id="05e85-228">Response format URL mappings</span></span>

<span data-ttu-id="05e85-229">클라이언트는 URL의 일부로 특정 형식을 요청할 수 있습니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-229">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="05e85-230">쿼리 문자열 또는 경로의 부분에서.</span><span class="sxs-lookup"><span data-stu-id="05e85-230">In the query string or part of the path.</span></span>
* <span data-ttu-id="05e85-231">.Xml 또는 .json과 같은 서식 지정 파일 확장명을 사용하여.</span><span class="sxs-lookup"><span data-stu-id="05e85-231">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="05e85-232">요청 경로의 매핑은 API가 사용하는 경로에 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-232">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="05e85-233">예:</span><span class="sxs-lookup"><span data-stu-id="05e85-233">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="05e85-234">위 경로를 사용하면 요청된 형식을 선택적 파일 확장명으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-234">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="05e85-235">특성은에 [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) 형식 값이 있는지 확인 `RouteData` 하 고 응답이 생성 될 때 응답 형식을 적절 한 포맷터에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="05e85-235">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="05e85-236">라우팅</span><span class="sxs-lookup"><span data-stu-id="05e85-236">Route</span></span>        |             <span data-ttu-id="05e85-237">포맷터</span><span class="sxs-lookup"><span data-stu-id="05e85-237">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="05e85-238">기본 출력 포맷터</span><span class="sxs-lookup"><span data-stu-id="05e85-238">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="05e85-239">JSON 포맷터(구성된 경우)</span><span class="sxs-lookup"><span data-stu-id="05e85-239">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="05e85-240">XML 포맷터(구성된 경우)</span><span class="sxs-lookup"><span data-stu-id="05e85-240">The XML formatter (if configured)</span></span>  |
