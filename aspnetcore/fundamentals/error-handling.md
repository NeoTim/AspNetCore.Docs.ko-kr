---
title: ASP.NET Core에서 오류 처리
author: rick-anderson
description: ASP.NET Core 앱에서 오류를 처리하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/error-handling
ms.openlocfilehash: 857b4935816d0010fa425ce98f938c87eb35a581
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793458"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="cc68d-103">ASP.NET Core에서 오류 처리</span><span class="sxs-lookup"><span data-stu-id="cc68d-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="cc68d-104">작성자: [Tom Dykstra](https://github.com/tdykstra/) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="cc68d-104">By [Tom Dykstra](https://github.com/tdykstra/) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="cc68d-105">이 항목에서는 ASP.NET Core 웹앱에서 오류를 처리하는 일반적인 접근법을 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="cc68d-106">웹 API는 <xref:web-api/handle-errors>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cc68d-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="cc68d-107">[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)</span><span class="sxs-lookup"><span data-stu-id="cc68d-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="cc68d-108">([다운로드하는 방법](xref:index#how-to-download-a-sample)) 이 항목에는 다양한 시나리오를 사용할 수 있도록 샘플 앱에서 전처리기 지시문(`#if`, `#endif`, `#define`)을 설정하는 방법에 대한 지침이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-108">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="cc68d-109">개발자 예외 페이지</span><span class="sxs-lookup"><span data-stu-id="cc68d-109">Developer Exception Page</span></span>

<span data-ttu-id="cc68d-110">개발자 예외 페이지에는 요청 예외에 대한 자세한 정보가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="cc68d-111">이 페이지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 있는 [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) 패키지를 통해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-111">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="cc68d-112">앱이 개발 [환경](xref:fundamentals/environments)에서 실행 중이면 페이지를 사용하도록 설정하는 코드를 `Startup.Configure` 메서드에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-112">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="cc68d-113">예외를 잡으려는 미들웨어 앞에 <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> 호출을 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-113">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="cc68d-114">**앱이 개발 환경에서 실행 중인 경우에만** 개발자 예외 페이지를 사용하도록 설정하세요.</span><span class="sxs-lookup"><span data-stu-id="cc68d-114">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="cc68d-115">프로덕션 환경에서 앱을 실행할 때 자세한 예외 정보를 공개적으로 공유하기를 원하지는 않을 것입니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-115">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="cc68d-116">환경 구성 방법에 대한 자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cc68d-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="cc68d-117">이 페이지에는 예외 및 요청에 대한 다음 정보가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-117">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="cc68d-118">스택 추적</span><span class="sxs-lookup"><span data-stu-id="cc68d-118">Stack trace</span></span>
* <span data-ttu-id="cc68d-119">쿼리 문자열 매개 변수(있는 경우)</span><span class="sxs-lookup"><span data-stu-id="cc68d-119">Query string parameters (if any)</span></span>
* <span data-ttu-id="cc68d-120">쿠키(있는 경우)</span><span class="sxs-lookup"><span data-stu-id="cc68d-120">Cookies (if any)</span></span>
* <span data-ttu-id="cc68d-121">헤더</span><span class="sxs-lookup"><span data-stu-id="cc68d-121">Headers</span></span>

<span data-ttu-id="cc68d-122">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)에서 개발자 예외 페이지를 확인하려면 `DevEnvironment` 전처리기 지시문을 사용하고 홈페이지에서 **Trigger an exception**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-122">To see the Developer Exception Page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="cc68d-123">예외 처리기 페이지</span><span class="sxs-lookup"><span data-stu-id="cc68d-123">Exception handler page</span></span>

<span data-ttu-id="cc68d-124">프로덕션 환경의 사용자 지정 오류 처리 페이지를 구성하려면 예외 처리 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-124">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="cc68d-125">이 미들웨어는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-125">The middleware:</span></span>

* <span data-ttu-id="cc68d-126">예외를 잡고 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-126">Catches and logs exceptions.</span></span>
* <span data-ttu-id="cc68d-127">표시된 페이지 또는 컨트롤러에 대한 대체 파이프라인에서 요청을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-127">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="cc68d-128">응답이 시작된 경우에는 요청이 다시 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-128">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="cc68d-129">다음 예제에서 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>는 비 개발 환경에서 예외 처리 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="cc68d-130">Razor Pages 앱 템플릿은 *Pages* 폴더에 오류 페이지( *.cshtml*) 및 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 클래스(`ErrorModel`)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="cc68d-131">MVC 앱의 프로젝트 템플릿에는 오류 동작 메서드와 오류 보기가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-131">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="cc68d-132">다음은 동작 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-132">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="cc68d-133">오류 처리기 작업 메서드를 `HttpGet`와 같은 HTTP 메서드 특성을 사용하여 표시하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="cc68d-133">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="cc68d-134">명시적 동사는 일부 요청이 메서드에 도달하지 못하게 방해합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-134">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="cc68d-135">인증되지 않은 사용자가 오류 보기를 수신할 수 있도록 메서드에 대한 익명 액세스를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-135">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="cc68d-136">예외에 액세스</span><span class="sxs-lookup"><span data-stu-id="cc68d-136">Access the exception</span></span>

<span data-ttu-id="cc68d-137"><xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>를 사용하여 오류 처리기 컨트롤러 또는 페이지에서 예외 및 원래 요청 경로에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-137">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="cc68d-138">클라이언트에 중요한 오류 정보를 **제공하지 마세요**.</span><span class="sxs-lookup"><span data-stu-id="cc68d-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="cc68d-139">오류 제공은 보안 위험입니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="cc68d-140">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)에서 예외 처리 페이지를 확인하려면 `ProdEnvironment` 및 `ErrorHandlerPage` 전처리기 지시문을 사용하고 홈페이지에서 **Trigger an exception**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-140">To see the exception handling page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="cc68d-141">예외 처리기 람다</span><span class="sxs-lookup"><span data-stu-id="cc68d-141">Exception handler lambda</span></span>

<span data-ttu-id="cc68d-142">[사용자 지정 예외 처리기 페이지](#exception-handler-page)의 대안은 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>에 람다를 제공하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-142">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span></span> <span data-ttu-id="cc68d-143">람다를 사용하면 응답을 반환하기 전에 오류에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-143">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="cc68d-144">다음은 예외 처리를 위해 람다를 사용하는 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-144">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="cc68d-145">위의 코드에서 Internet Explorer 브라우저에 IE 오류 메시지가 아닌 오류 메시지가 표시되도록 `await context.Response.WriteAsync(new string(' ', 512));`가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-145">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="cc68d-146">자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/AspNetCore.Docs/issues/16144)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cc68d-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="cc68d-147"><xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> 또는 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>에서 클라이언트에 중요한 오류 정보를 **제공하지 마세요**.</span><span class="sxs-lookup"><span data-stu-id="cc68d-147">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="cc68d-148">오류 제공은 보안 위험입니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-148">Serving errors is a security risk.</span></span>

<span data-ttu-id="cc68d-149">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)에서 예외 처리 람다의 결과를 확인하려면 `ProdEnvironment` 및 `ErrorHandlerLambda` 전처리기 지시문을 사용하고 홈페이지에서 **Trigger an exception**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-149">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="cc68d-150">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="cc68d-150">UseStatusCodePages</span></span>

<span data-ttu-id="cc68d-151">기본적으로 ASP.NET Core 앱은 ‘404 - 찾을 수 없음’과 같은 HTTP 상태 코드에 대한 상태 코드 페이지를 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-151">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="cc68d-152">앱은 상태 코드와 빈 응답 본문을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-152">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="cc68d-153">상태 코드 페이지를 제공하려면 상태 코드 페이지 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-153">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="cc68d-154">이 미들웨어는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함된 [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) 패키지를 통해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-154">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="cc68d-155">일반적인 오류 상태 코드에 대해 기본 텍스트 전용 처리기를 사용하려면 `Startup.Configure` 메서드에서 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-155">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="cc68d-156">요청 처리 미들웨어(예: 정적 파일 미들웨어 및 MVC 미들웨어) 이전에 `UseStatusCodePages`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-156">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="cc68d-157">다음은 기본 처리기를 통해 표시되는 텍스트의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-157">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="cc68d-158">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)에서 다양한 상태 코드 페이지 형식 중 하나를 확인하려면 `StatusCodePages`로 시작하는 전처리기 지시문 중 하나를 사용하고 홈페이지에서 **Trigger a 404**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-158">To see one of the various status code page formats in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="cc68d-159">형식 문자열을 사용하는 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="cc68d-159">UseStatusCodePages with format string</span></span>

<span data-ttu-id="cc68d-160">응답 콘텐츠 형식 및 텍스트를 사용자 지정하려면 콘텐츠 형식 및 형식 문자열을 사용하는 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*>의 오버로드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-160">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="cc68d-161">람다를 사용하는 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="cc68d-161">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="cc68d-162">사용자 지정 오류 처리 및 응답 쓰기 코드를 지정하려면 람다 식을 사용하는 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*>의 오버로드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-162">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="cc68d-163">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="cc68d-163">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="cc68d-164"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> 확장 메서드는:</span><span class="sxs-lookup"><span data-stu-id="cc68d-164">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> extension method:</span></span>

* <span data-ttu-id="cc68d-165">‘302 - 찾음’ 상태 코드를 클라이언트에 보냅니다. </span><span class="sxs-lookup"><span data-stu-id="cc68d-165">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="cc68d-166">클라이언트를 URL 템플릿에 제공된 위치로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-166">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="cc68d-167">예제에서 볼 수 있는 것과 같이 URL 템플릿에는 상태 코드에 대한 `{0}` 자리 표시자가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-167">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="cc68d-168">URL 템플릿이 물결표(~)로 시작하는 경우 물결표는 앱의 `PathBase`로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-168">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="cc68d-169">앱 내의 엔드포인트를 가리키는 경우 해당 엔드포인트에 대한 MVC 뷰 또는 Razor 페이지를 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-169">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="cc68d-170">Razor Pages 예제를 보려면 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)의 *Pages/StatusCode.cshtml*을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cc68d-170">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="cc68d-171">이 메서드는 일반적으로 앱이 다음과 같은 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-171">This method is commonly used when the app:</span></span>

* <span data-ttu-id="cc68d-172">일반적으로 다른 앱이 오류를 처리하는 상황에서 앱이 클라이언트를 다른 엔드포인트로 리디렉션해야 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="cc68d-172">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="cc68d-173">웹앱의 경우 클라이언트의 브라우저 주소 표시줄에 리디렉션된 엔드포인트가 반영됩니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-173">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="cc68d-174">초기 리디렉션 응답과 함께 원래 상태 코드를 유지하고 반환하면 안 되는 경우.</span><span class="sxs-lookup"><span data-stu-id="cc68d-174">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="cc68d-175">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="cc68d-175">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="cc68d-176"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> 확장 메서드는:</span><span class="sxs-lookup"><span data-stu-id="cc68d-176">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> extension method:</span></span>

* <span data-ttu-id="cc68d-177">원래 상태 코드를 클라이언트에 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-177">Returns the original status code to the client.</span></span>
* <span data-ttu-id="cc68d-178">대체 경로에서 요청 파이프라인을 다시 실행하여 응답 본문을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-178">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="cc68d-179">앱 내의 엔드포인트를 가리키는 경우 해당 엔드포인트에 대한 MVC 뷰 또는 Razor 페이지를 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-179">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="cc68d-180">Razor Pages 예제를 보려면 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)의 *Pages/StatusCode.cshtml*을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cc68d-180">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="cc68d-181">이 메서드는 일반적으로 앱이 다음과 같은 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-181">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="cc68d-182">다른 엔드포인트로 리디렉션하지 않고 요청을 처리해야 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="cc68d-182">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="cc68d-183">웹앱의 경우 클라이언트의 브라우저 주소 표시줄에 원래 요청된 엔드포인트가 반영됩니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-183">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="cc68d-184">원래 상태 코드를 유지하고 응답과 함께 반환해야 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="cc68d-184">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="cc68d-185">URL 및 쿼리 문자열 템플릿에는 상태 코드에 대한 자리 표시자(`{0}`)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-185">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="cc68d-186">URL 템플릿은 슬래시(`/`)로 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-186">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="cc68d-187">경로에서 자리 표시자를 사용하는 경우, 엔드포인트(페이지 또는 컨트롤러)가 경로 세그먼트를 처리할 수 있는지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="cc68d-187">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="cc68d-188">예를 들어 오류를 위한 Razor 페이지는 `@page` 지시문을 사용하여 선택적 경로 세그먼트 값을 수락해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-188">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="cc68d-189">오류를 처리하는 엔드포인트는 다음 예제와 같이 오류를 생성한 원래 URL을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-189">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="cc68d-190">상태 코드 페이지 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="cc68d-190">Disable status code pages</span></span>

<span data-ttu-id="cc68d-191">MVC 컨트롤러 또는 작업 메서드에 대한 상태 코드 페이지를 비활성화하려면 [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) 특성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-191">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="cc68d-192">Razor Pages 처리기 메서드 또는 MVC 컨트롤러에서 특정 요청에 대한 상태 코드 페이지를 사용하지 않으려면 <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-192">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="cc68d-193">예외 처리 코드</span><span class="sxs-lookup"><span data-stu-id="cc68d-193">Exception-handling code</span></span>

<span data-ttu-id="cc68d-194">예외 처리 페이지의 코드도 예외를 던질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-194">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="cc68d-195">프로덕션 오류 페이지를 완전한 정적 콘텐츠로 구성하는 것은 종종 좋은 생각입니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-195">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="cc68d-196">응답 헤더</span><span class="sxs-lookup"><span data-stu-id="cc68d-196">Response headers</span></span>

<span data-ttu-id="cc68d-197">또한 응답의 헤더가 전송되고 나면 다음 제한이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-197">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="cc68d-198">앱에서 응답의 상태 코드를 변경할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-198">The app can't change the response's status code.</span></span>
* <span data-ttu-id="cc68d-199">예외 페이지 또는 처리기를 실행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-199">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="cc68d-200">응답을 완료하거나 연결이 중단되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-200">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="cc68d-201">서버 예외 처리</span><span class="sxs-lookup"><span data-stu-id="cc68d-201">Server exception handling</span></span>

<span data-ttu-id="cc68d-202">앱의 예외 처리 논리 외에도 [HTTP 서버 구현](xref:fundamentals/servers/index)에서 몇 가지 예외를 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-202">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="cc68d-203">응답 헤더가 전송되기 전에 예외를 catch한 서버는 응답 본문 없이 ‘500 - 내부 서버 오류’ 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-203">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="cc68d-204">응답 헤더가 전송된 후에 예외를 catch한 서버는 연결을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-204">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="cc68d-205">앱으로 처리되지 않는 요청은 서버에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-205">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="cc68d-206">서버에서 요청을 처리할 때 발생하는 모든 예외는 서버의 예외 처리에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-206">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="cc68d-207">앱의 사용자 지정 오류 페이지, 예외 처리 미들웨어 및 필터는 이 동작에 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-207">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="cc68d-208">시작 예외 처리</span><span class="sxs-lookup"><span data-stu-id="cc68d-208">Startup exception handling</span></span>

<span data-ttu-id="cc68d-209">호스팅 계층만 앱 시작 시 발생하는 예외를 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-209">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="cc68d-210">[시작 오류를 캡처](xref:fundamentals/host/web-host#capture-startup-errors)하고 [자세한 오류를 캡처](xref:fundamentals/host/web-host#detailed-errors)하도록 호스트를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-210">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="cc68d-211">호스팅 계층은 호스트 주소/포트 바인딩 후에 오류가 발생하는 경우에만 캡처된 시작 오류에 대한 오류 페이지만 표시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-211">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="cc68d-212">바인딩이 실패하면 결과는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-212">If binding fails:</span></span>

* <span data-ttu-id="cc68d-213">호스팅 계층에서 심각한 예외를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-213">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="cc68d-214">dotnet 프로세스의 작동이 중단됩니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-214">The dotnet process crashes.</span></span>
* <span data-ttu-id="cc68d-215">HTTP 서버가 [Kestrel](xref:fundamentals/servers/kestrel)인 경우 오류 페이지가 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-215">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="cc68d-216">[IIS](/iis)(또는 Azure App Service) 또는 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)에서 실행 중일 때, 프로세스를 시작할 수 없는 경우 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 ‘502.5 - 프로세스 실패’를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-216">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="cc68d-217">자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cc68d-217">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="cc68d-218">데이터베이스 오류 페이지</span><span class="sxs-lookup"><span data-stu-id="cc68d-218">Database error page</span></span>

<span data-ttu-id="cc68d-219">데이터베이스 오류 페이지 미들웨어는 Entity Framework 마이그레이션을 사용하여 해결할 수 있는 데이터베이스 관련 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-219">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="cc68d-220">이 예외가 발생하면 문제 해결을 위한 가능한 작업의 세부 정보가 포함된 HTML 응답이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-220">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="cc68d-221">이 페이지는 개발 환경에서만 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-221">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="cc68d-222">`Startup.Configure`에 코드를 추가하여 페이지를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-222">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="cc68d-223"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A>에는 [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-223"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="cc68d-224">예외 필터</span><span class="sxs-lookup"><span data-stu-id="cc68d-224">Exception filters</span></span>

<span data-ttu-id="cc68d-225">MVC 앱에서는 예외 필터를 전역으로 구성하거나 컨트롤러별 또는 작업별로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-225">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="cc68d-226">Razor Pages 앱에서는 전역으로 구성하거나 페이지 모델별로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-226">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="cc68d-227">이러한 필터는 컨트롤러 작업 또는 다른 필터를 실행하는 동안 발생하는 처리되지 않은 예외를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-227">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="cc68d-228">자세한 내용은 <xref:mvc/controllers/filters#exception-filters>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cc68d-228">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="cc68d-229">예외 필터는 MVC 작업 내에서 발생하는 예외를 트래핑하는 데 유용하지만 예외 처리 미들웨어만큼 유연하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-229">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="cc68d-230">미들웨어를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="cc68d-230">We recommend using the middleware.</span></span> <span data-ttu-id="cc68d-231">선택한 MVC 작업에 따라 오류 처리를 다르게 수행해야 하는 경우에만 필터를 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="cc68d-231">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="cc68d-232">모델 상태 오류</span><span class="sxs-lookup"><span data-stu-id="cc68d-232">Model state errors</span></span>

<span data-ttu-id="cc68d-233">모델 상태 오류를 처리하는 방법에 대한 자세한 내용은 [모델 바인딩](xref:mvc/models/model-binding) 및 [모델 유효성 검사](xref:mvc/models/validation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cc68d-233">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cc68d-234">추가 자료</span><span class="sxs-lookup"><span data-stu-id="cc68d-234">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
