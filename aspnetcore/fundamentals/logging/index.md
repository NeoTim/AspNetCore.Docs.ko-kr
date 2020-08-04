---
title: .NET Core 및 ASP.NET Core의 로깅
author: rick-anderson
description: Microsoft.Extensions.Logging NuGet 패키지에서 제공하는 로깅 프레임워크 사용법을 알아보세요.
ms.author: riande
ms.custom: mvc
ms.date: 6/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: a4962c3132c60b68cb2d4b5a97e9b082aba15d15
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2020
ms.locfileid: "87330773"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>.NET Core 및 ASP.NET Core의 로깅

::: moniker range=">= aspnetcore-3.0"

작성자: [Kirk Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

.NET Core는 다양한 기본 제공 및 타사 로깅 공급자에서 작동하는 로깅 API를 지원합니다. 이 문서에서는 기본 제공 공급자를 이용하여 로깅 API를 사용하는 방법을 보여줍니다.

이 문서에 사용된 대부분의 코드 예제는 ASP.NET Core 앱에서 가져온 것입니다. 해당 코드 조각의 로깅 관련 부분은 [제네릭 호스트](xref:fundamentals/host/generic-host)를 사용하는 모든 .NET Core 앱에 적용됩니다. ASP.NET Core 웹앱 템플릿은 제네릭 호스트를 사용합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([다운로드 방법](xref:index#how-to-download-a-sample))

<a name="lp"></a>

## <a name="logging-providers"></a>로깅 공급자

로그를 표시하는 `Console` 공급자를 제외하고 로깅 공급자는 로그를 저장합니다. 예를 들어 Azure Application Insights 공급자는 Azure Application Insights의 로그를 저장합니다. 여러 공급자를 사용하도록 설정할 수 있습니다.

기본 ASP.NET Core 웹앱 템플릿은 다음과 같습니다.

* [제네릭 호스트](xref:fundamentals/host/generic-host)를 사용합니다.
* 다음 로깅 공급자를 추가하는 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>를 호출합니다.
  * [콘솔](#console)
  * [디버그](#debug)
  * [EventSource](#event-source)
  * [EventLog](#welog): Windows에만 해당

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_TemplateCode&highlight=9)]

위의 코드는 ASP.NET Core 웹앱 템플릿을 사용하여 만든 `Program` 클래스를 보여 줍니다. 다음 몇 개 섹션에서는 제네릭 호스트를 사용하는 ASP.NET Core 웹앱 템플릿을 기반으로 하는 샘플을 제공합니다. [비호스트 콘솔 앱](#nhca)은 문서의 뒷부분에 설명되어 있습니다.

`Host.CreateDefaultBuilder`로 추가된 로깅 공급자의 기본 세트를 재정의하려면 `ClearProviders`를 호출하고 필요한 로깅 공급자를 추가합니다. 예를 들어, 다음 코드는

* <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>를 호출하여 작성기에서 <xref:Microsoft.Extensions.Logging.ILoggerProvider> 인스턴스를 모두 제거합니다.
* [Console](#console) 로깅 공급자를 추가합니다.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_AddProvider&highlight=5-6)]

추가 공급자는 다음을 참조하세요.

* [기본 제공 로깅 공급자](#bilp)
* [타사 로깅 공급자](#third-party-logging-providers)

## <a name="create-logs"></a>로그 만들기

로그를 만들려면 [DI(종속성 주입)](xref:fundamentals/dependency-injection)에서 <xref:Microsoft.Extensions.Logging.ILogger%601> 개체를 사용합니다.

다음 예제가 하는 일:

* 형식이 `AboutModel`인 정규화된 이름의 로그 ‘범주’를 사용하는 `ILogger<AboutModel>` 로거를 만듭니다. 로그 범주는 각 로그와 연결된 문자열입니다.
* <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>을 호출하여 `Information` 수준에 로그합니다. 로그 *수준*은 기록된 이벤트의 심각도를 나타냅니다.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=5,14)]

[수준](#log-level) 및 [범주](#log-category)는 이 문서의 뒷부분에 자세히 설명되어 있습니다.

Blazor에 대한 자세한 내용은 이 문서의 [Blazor 및 Blazor WebAssembly에서 로그 만들기](#clib)를 참조하세요.

[Main 및 Startup에서 로그 만들기](#clms)에서는 `Main` 및 `Startup`에서 로그를 만드는 방법을 보여 줍니다.

## <a name="configure-logging"></a>로깅 구성

로깅 구성은 일반적으로 *appsettings*.`{Environment}` *.json* 파일의 `Logging` 섹션에서 제공됩니다. 다음 *appsettings.Development.json* 파일은 ASP.NET Core 웹앱 템플릿으로 생성되었습니다.

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Development.json)]

앞의 JSON에서:

* `"Default"`, `"Microsoft"` 및 `"Microsoft.Hosting.Lifetime"` 범주가 지정되었습니다.
* `"Microsoft"` 범주는 `"Microsoft"`로 시작하는 모든 범주에 적용됩니다. 예를 들어 이 설정은 `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` 범주에 적용됩니다.
* `"Microsoft"` 범주는 로그 수준 `Warning` 이상에 로그됩니다.
* `"Microsoft.Hosting.Lifetime"` 범주는 `"Microsoft"` 범주보다 구체적이므로 `"Microsoft.Hosting.Lifetime"` 범주는 로그 수준 “정보” 이상에 로그됩니다.
* 특정 로그 공급자를 지정하지 않았으므로 `LogLevel`은 [Windows EventLog](#welog)를 제외하고 사용하도록 설정한 모든 로깅 공급자에 적용됩니다.

`Logging` 속성은 <xref:Microsoft.Extensions.Logging.LogLevel> 및 로그 공급자 속성을 포함할 수 있습니다. `LogLevel` 속성은 선택한 범주에 대해 로그할 최소 [수준](#log-level)을 지정합니다. 위의 JSON에서는 `Information` 및 `Warning` 로그 수준을 지정했습니다. `LogLevel` 로그의 심각도를 나타내며 0에서 6 사이의 범위입니다.

`Trace` = 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5 및 `None` = 6

`LogLevel`을 지정하면 지정된 수준 이상의 메시지에 대해 로깅을 사용하도록 설정됩니다. 위의 JSON에서 `Default` 범주는 `Information` 이상에 대해 로그됩니다. 예를 들어 `Information`, `Warning`, `Error` 및 `Critical` 메시지가 로그됩니다. `LogLevel` 지정하지 않으면 로깅은 `Information` 수준으로 기본 설정됩니다. 자세한 내용은 [로그 수준](#llvl)을 참조하세요.

공급자 속성에서 `LogLevel` 속성을 지정할 수 있습니다. 공급자 아래의 `LogLevel`은 해당 공급자에 대해 로그할 수준을 지정하고 공급자 이외의 로그 설정을 재정의합니다. 다음 *appsettings.json* 파일을 고려하세요.

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Prod2.json)]

`Logging.{providername}.LogLevel`의 설정은 `Logging.LogLevel`의 설정을 재정의합니다. 위의 JSON에서는 `Debug` 공급자의 기본 로그 수준이 `Information`로 설정되었습니다.

`Logging:Debug:LogLevel:Default:Information`

위 설정은 `Microsoft.Hosting`을 제외하고 모든 `Logging:Debug:` 범주에 대해 `Information` 로그 수준을 지정합니다. 특정 범주를 나열하면 특정 범주로 기본 범주가 재정의됩니다. 위의 JSON에서 `Logging:Debug:LogLevel` 범주 `"Microsoft.Hosting"` 및 `"Default"`는 `Logging:LogLevel`의 설정을 재정의합니다.

최소 로그 수준은 다음에 대해 지정할 수 있습니다.

* 특정 공급자:  예를 들면 `Logging:EventSource:LogLevel:Default:Information`과 같습니다.
* 특정 범주: 예를 들면 `Logging:LogLevel:Microsoft:Warning`과 같습니다.
* 모든 공급자와 모든 범주: `Logging:LogLevel:Default:Warning`

최소 수준 이하의 모든 로그는 다음과 같이 ***되지 않습니다***.

* 공급자에 전달됩니다.
* 로그되거나 표시됩니다.

모든 로그를 표시하지 않으려면 [LogLevel.None](xref:Microsoft.Extensions.Logging.LogLevel)을 지정합니다. `LogLevel.None`의 값은 `LogLevel.Critical`(5)보다 높은 6입니다.

공급자가 [로그 범위](#logscopes)를 지원하는 경우 `IncludeScopes`는 사용 가능 여부를 나타냅니다. 자세한 내용은 [로그 범위](#logscopes)를 참조하세요.

다음 *appsettings.json* 파일에는 기본적으로 사용하도록 설정되는 모든 공급자가 포함되어 있습니다.

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Production.json)]

앞의 예제에서:

* 범주 및 수준은 제안된 값이 아닙니다. 기본 공급자를 모두 표시하기 위해 제공되는 샘플입니다.
* `Logging.{providername}.LogLevel`의 설정은 `Logging.LogLevel`의 설정을 재정의합니다. 예를 들어 `Debug.LogLevel.Default` 수준은 `LogLevel.Default`수준을 재정의합니다.
* 각 기본 공급자 ‘별칭’을 사용합니다. 각 공급자는 정규화된 형식 이름 대신 구성에서 사용할 수 있는 *별칭*을 정의합니다. 기본 제공 공급자 별칭은 다음과 같습니다.
  * Console
  * Debug
  * EventSource
  * EventLog
  * AzureAppServicesFile
  * AzureAppServicesBlob
  * ApplicationInsights

## <a name="set-log-level-by-command-line-environment-variables-and-other-configuration"></a>명령줄, 환경 변수 및 기타 구성으로 로그 수준 설정

로그 수준은 [구성 공급자](xref:fundamentals/configuration/index) 중 하나로 설정할 수 있습니다. 

[!INCLUDE[](~/includes/environmentVarableColon.md)]

다음 명령은,

* Windows에서 환경 키 `Logging:LogLevel:Microsoft`를 `Information` 값으로 설정합니다.
* ASP.NET Core 웹 애플리케이션 템플릿을 사용하여 만든 앱을 사용할 때 설정을 테스트합니다. `dotnet run` 명령은 `set`을 사용한 후 프로젝트 디렉터리에서 실행해야 합니다.

```cmd
set Logging__LogLevel__Microsoft=Information
dotnet run
```

위의 환경 설정은,

* 설정된 명령 창에서 시작된 프로세스에서만 설정됩니다.
* Visual Studio에서 시작된 브라우저에서는 읽을 수 없습니다.

다음 [setx](/windows-server/administration/windows-commands/setx) 명령은 Windows에서 환경 키 및 값도 설정합니다. `set`와 달리, `setx` 설정은 유지됩니다. `/M` 스위치는 시스템 환경에서 변수를 설정합니다. `/M`을 사용하지 않으면 사용자 환경 변수가 설정됩니다.

```cmd
setx Logging__LogLevel__Microsoft=Information /M
```

[Azure App Service](https://azure.microsoft.com/services/app-service/)의 **설정 > 구성** 페이지에서 **새 애플리케이션 설정**을 선택합니다. Azure App Service 애플리케이션 설정은,

* 미사용 시 암호화되고 암호화된 채널을 통해 전송됩니다.
* 환경 변수로 노출됩니다.

자세한 내용은 [Azure 앱: Azure Portal을 사용하여 앱 구성 재정의](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal) 편을 참조하세요.

환경 변수를 사용하여 ASP.NET Core 구성 값을 설정하는 방법에 대한 자세한 내용은 [환경 변수](xref:fundamentals/configuration/index#environment-variables)를 참조하세요. 명령줄, Azure Key Vault, Azure App Configuration, 기타 파일 형식 등을 포함하여 다른 구성 원본을 사용하는 방법에 대한 자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.

## <a name="how-filtering-rules-are-applied"></a>필터링 규칙 적용 방식

<xref:Microsoft.Extensions.Logging.ILogger%601> 개체를 만들 때 <xref:Microsoft.Extensions.Logging.ILoggerFactory> 개체는 공급자마다 해당 로거에 적용할 단일 규칙을 선택합니다. `ILogger` 인스턴스에서 작성된 모든 메시지는 선택한 규칙에 따라 필터링됩니다. 사용 가능한 규칙 중에서 각 공급자 및 범주 쌍과 가장 관련이 많은 규칙이 선택됩니다.

지정된 범주에 대한 `ILogger`가 생성되면 다음 알고리즘이 각 공급자에 사용됩니다.

* 공급자 또는 공급자의 별칭과 일치하는 모든 규칙을 선택합니다. 일치하는 규칙이 없는 경우 빈 공급자가 있는 모든 규칙을 선택합니다.
* 앞 단계의 결과에서 일치하는 범주 접두사가 가장 긴 규칙을 선택합니다. 일치하는 규칙이 없는 경우 범주를 지정하지 않는 규칙을 모두 선택합니다.
* 여러 규칙을 선택하는 경우 **마지막** 규칙을 사용합니다.
* 규칙을 선택하지 않는 경우 `MinimumLevel`을 사용합니다.

<a name="dnrvs"></a>

## <a name="logging-output-from-dotnet-run-and-visual-studio"></a>dotnet run 및 Visual Studio의 로깅 출력

[기본 로깅 공급자](#lp)를 사용하여 만든 로그는 다음과 같이 표시됩니다.

* Visual Studio
  * 디버그할 때 디버그 출력 창에 표시됨
  * ASP.NET Core 웹 서버 창에 표시됨
* `dotnet run`을 사용하여 앱을 실행할 때 콘솔 창에 표시됨

“Microsoft” 범주로 시작하는 로그는 ASP.NET Core 프레임워크 코드에서 온 것입니다. ASP.NET Core 및 애플리케이션 코드는 동일한 로깅 API와 공급자를 사용합니다.

<a name="lcat"></a>

## <a name="log-category"></a>로그 범주

`ILogger` 개체가 생성되면 ‘범주’가 지정됩니다. 해당 범주는 `ILogger`의 해당 인스턴스에서 만든 각 로그 메시지에 포함됩니다. 범주 문자열은 임의로 지정되지만 규칙은 클래스 이름을 사용하는 것입니다. 예를 들어 컨트롤러에서 이름은 `"TodoApi.Controllers.TodoController"`일 수 있습니다. ASP.NET Core 웹앱은 `ILogger<T>`를 사용하여 `T`의 정규화된 형식 이름을 범주로 사용하는 `ILogger` 인스턴스를 자동으로 가져옵니다.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Privacy.cshtml.cs?name=snippet)]

범주를 명시적으로 지정하려면 `ILoggerFactory.CreateLogger`를 호출합니다.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Contact.cshtml.cs?name=snippet)]

`ILogger<T>`는 `T`의 정규화된 형식 이름으로 `CreateLogger`를 호출하는 것과 동일합니다.

<a name="llvl"></a>

## <a name="log-level"></a>로그 수준

다음 표에서는 <xref:Microsoft.Extensions.Logging.LogLevel> 값, 편리한 `Log{LogLevel}` 확장 메서드 및 추천 사용법을 설명합니다.

| LogLevel | 값 | 메서드 | Description |
| -------- | ----- | ------ | ----------- |
| [추적](xref:Microsoft.Extensions.Logging.LogLevel) | 0 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogTrace%2A> | 가장 자세한 메시지를 포함합니다. 해당 메시지는 중요한 앱 데이터를 포함할 수 있습니다. 해당 메시지는 기본적으로 사용하지 않도록 설정되며 프로덕션에서 사용하도록 설정하면 ***안 됩니다***. |
| [디버그](xref:Microsoft.Extensions.Logging.LogLevel) | 1 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug%2A> | 디버깅 및 개발을 위한 수준입니다. 너무 많으므로 프로덕션에서는 주의해서 사용합니다. |
| [정보](xref:Microsoft.Extensions.Logging.LogLevel) | 2 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> | 앱의 일반적인 흐름을 추적합니다. 장기적인 값이 있을 수 있습니다. |
| [경고](xref:Microsoft.Extensions.Logging.LogLevel) | 3 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> | 비정상적이거나 예기치 않은 이벤트를 위한 수준입니다. 일반적으로 앱의 오류를 발생시키지 않는 오류 또는 조건을 포함합니다. |
| [오류](xref:Microsoft.Extensions.Logging.LogLevel) | 4 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> | 처리할 수 없는 오류 및 예외를 위한 수준입니다. 해당 메시지는 전체 앱 오류가 아닌 현재 작업 또는 요청의 오류를 의미합니다. |
| [심각](xref:Microsoft.Extensions.Logging.LogLevel) | 5 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogCritical%2A> | 즉각적인 대응이 필요한 오류를 위한 수준입니다. 예: 데이터 손실 시나리오, 디스크 공간 부족. |
| [없음](xref:Microsoft.Extensions.Logging.LogLevel) | 6 | | 로깅 범주에서 메시지를 쓰지 않도록 지정합니다. |

위의 표에서는 `LogLevel`이 심각도가 낮은 것에서 높은 것 순으로 표시됩니다.

[Log](xref:Microsoft.Extensions.Logging.LoggerExtensions) 메서드의 첫 번째 매개 변수인 <xref:Microsoft.Extensions.Logging.LogLevel>은 로그의 심각도를 나타냅니다. 대부분의 개발자는 `Log(LogLevel, ...)` 대신 [Log{LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) 확장 메서드를 호출합니다. `Log{LogLevel}` 확장 메서드는 [Log 메서드를 호출하고 LogLevel을 지정](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)합니다. 예를 들어 다음 두 로깅 호출은 기능이 동일하며 같은 로그를 생성합니다.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet0&highlight=6-7)]

`MyLogEvents.TestItem`은 이벤트 ID입니다. `MyLogEvents`는 샘플 앱의 일부이며 [로그 이벤트 ID](#leid) 섹션에 표시됩니다.

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

다음 코드는 `Information` 및 `Warning` 로그를 만듭니다.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

위의 코드에서 첫 번째 `Log{LogLevel}` 매개 변수 `MyLogEvents.GetItem`은 [로그 이벤트 ID](#leid)입니다. 두 번째 매개 변수는 나머지 메서드 매개 변수가 제공하는 인수 값에 대한 자리 표시자를 포함하고 있는 메시지 템플릿입니다. 메서드 매개 변수는 이 문서의 뒷부분에 있는 [메시지 템플릿](#lmt) 섹션에 설명되어 있습니다.

적절한 `Log{LogLevel}` 메서드를 호출하여 특정 스토리지 매체에 기록되는 로그 출력의 양을 제어합니다. 예를 들어:

* 프로덕션:
  * `Trace` 또는 `Information` 수준 로깅은 자세한 로그 메시지를 대량으로 생성합니다. 비용을 관리하고 데이터 스토리지 제한을 초과하지 않으려면 `Trace` 및 `Information` 수준 메시지를 대용량, 저비용 데이터 저장소에 로그합니다. `Trace` 및 `Information`을 특정 범주로 제한하는 것이 좋습니다.
  * `Warning`~`Critical` 수준의 로깅은 적은 로그 메시지를 생성합니다.
    * 비용과 스토리지 제한이 일반적으로 문제가 되지 않습니다.
    * 로그가 적으므로 데이터 저장소를 더 유연하게 선택할 수 있습니다.
* 개발 중:
  * `Warning`로 설정합니다.
  * 문제를 해결할 때는 `Trace` 또는 `Information` 메시지를 추가합니다. 출력을 제한하려면 조사 중인 범주에 대해서만 `Trace` 또는 `Information`을 설정합니다.

ASP.NET Core는 프레임워크 이벤트에 대한 로그를 작성합니다. 예를 들어 다음의 로그 출력을 고려하세요.

* ASP.NET Core 템플릿을 사용하여 만든 Razor Pages 앱
* 로깅을 `Logging:Console:LogLevel:Microsoft:Information`으로 설정
* 개인정보처리방침 페이지로 이동

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/Privacy
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint '/Privacy'
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[3]
      Route matched with {page = "/Privacy"}. Executing page /Privacy
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[101]
      Executing handler method DefaultRP.Pages.PrivacyModel.OnGet - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[102]
      Executed handler method OnGet, returned result .
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[103]
      Executing an implicit handler method - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[104]
      Executed an implicit handler method, returned result Microsoft.AspNetCore.Mvc.RazorPages.PageResult.
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[4]
      Executed page /Privacy in 74.5188ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint '/Privacy'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 149.3023ms 200 text/html; charset=utf-8
```

다음 JSON에서는 `Logging:Console:LogLevel:Microsoft:Information`을 설정합니다.

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<a name="leid"></a>

## <a name="log-event-id"></a>로그 이벤트 ID

각 로그는 *이벤트 ID*를 지정할 수 있습니다. 샘플 앱에서는 `MyLogEvents` 클래스를 사용하여 이벤트 ID를 정의합니다.

<!-- Review: to bad there is no way to use an enum for event ID's -->
[!code-csharp[](index/samples/3.x/TodoApiDTO/Models/MyLogEvents.cs?name=snippet_LoggingEvents)]

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

이벤트 ID는 이벤트 집합을 연결합니다. 예를 들어 페이지에서 항목 목록을 표시하는 것과 관련된 모든 로그는 1001일 수 있습니다.

로깅 공급자는 이벤트 ID를 ID 필드, 로그 메시지에 저장하거나 또는 전혀 저장하지 않을 수도 있습니다. 디버그 공급자는 이벤트 ID를 표시하지 않습니다. 콘솔 공급자는 범주 뒤에 대괄호로 이벤트 ID를 표시합니다.

```console
info: TodoApi.Controllers.TodoItemsController[1002]
      Getting item 1
warn: TodoApi.Controllers.TodoItemsController[4000]
      Get(1) NOT FOUND
```

일부 로깅 공급자는 ID에 대한 필터링을 허용하는 필드에 이벤트 ID를 저장합니다.

<a name="lmt"></a>

## <a name="log-message-template"></a>로그 메시지 템플릿
<!-- Review, Each log API uses a message template. -->
각 로그 API는 메시지 템플릿을 사용합니다. 메시지 템플릿에는 인수가 제공되는 자리 표시자를 포함할 수 있습니다. 번호가 아닌 자리 표시자의 이름을 사용합니다.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

값을 제공하는 데 사용되는 매개 변수를 결정하는 것은 자리 표시자의 이름이 아닌 순서입니다. 다음 코드에서는 매개 변수 이름이 메시지 템플릿의 순서와 일치하지 않습니다.

```csharp
string p1 = "param1";
string p2 = "param2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

앞의 코드는 매개 변수 값을 순서대로 사용하여 로그 메시지를 만듭니다.

```text
Parameter values: param1, param2
```

해당 접근 방식을 통해 로깅 공급자는 [의미 체계 또는 구조적 로깅](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging)를 구현할 수 있습니다. 인수 자체는 서식이 지정된 메시지 템플릿뿐만 아니라 로깅 시스템에 전달됩니다. 따라서 로깅 공급자는 매개 변수 값을 필드로 저장할 수 있습니다. 예를 들어 다음 로거 메서드를 살펴보세요.

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Azure Table Storage에 로그할 경우를 예로 듭니다.

* 각 Azure Table 엔터티에는 `ID` 및 `RequestTime` 속성이 있을 수 있습니다.
* 속성이 있는 테이블은 로그된 데이터의 쿼리를 쉽게 합니다. 예를 들어 문자 메시지의 시간 초과를 구문 분석하지 않고도 특정 `RequestTime` 범위 내에 있는 모든 로그를 쿼리를 통해 찾을 수 있습니다.

## <a name="log-exceptions"></a>예외 로그

로거 메서드에는 예외 매개 변수를 사용하는 오버로드가 있습니다.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Exp)]

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

예외 로깅은 공급자별로 다릅니다.

### <a name="default-log-level"></a>기본 로그 수준

기본 로그 수준을 설정하지 않으면 기본 로그 수준 값은 `Information`입니다.

예를 들어 다음 웹앱을 고려하세요.

* ASP.NET 웹앱 템플릿을 사용하여 만들었습니다.
* *appsettings.json* 및 *appsettings.Development.json*을 삭제하거나 이름을 변경했습니다.

이전 설정을 사용하여 개인정보처리방침이나 홈페이지로 이동하면 범주 이름에 `Microsoft`가 포함된 `Trace`, `Debug`및 `Information` 메시지가 많이 생성됩니다.

다음 코드에서는 구성에 기본 로그 수준이 설정되어 있지 않은 경우 기본 로그 수준을 설정합니다.

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_MinLevel&highlight=10)]

<!-- review required: I say this a couple times -->
일반적으로 로그 수준은 코드에서가 아니라 구성에서 지정해야 합니다.

### <a name="filter-function"></a>필터 함수

필터 함수는 구성 또는 코드를 통해 규칙이 할당되지 않은 모든 공급자와 범주에 대해 호출됩니다.

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterFunction)]

앞의 코드는 범주에 `Controller` 또는 `Microsoft`가 포함되어 있고 로그 수준이 `Information` 이상인 경우 콘솔 로그를 표시합니다.

일반적으로 로그 수준은 코드에서가 아니라 구성에서 지정해야 합니다.

## <a name="aspnet-core-and-ef-core-categories"></a>ASP.NET Core 및 EF Core 범주

다음 표에는 ASP.NET Core 및 Entity Framework Core에서 사용되는 몇 가지 범주와 로그에 관한 참고 사항이 나와 있습니다.

| 범주                            | 참고 |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | 일반 ASP.NET Core 진단. |
| Microsoft.AspNetCore.DataProtection | 고려되고, 발견되고, 사용된 키. |
| Microsoft.AspNetCore.HostFiltering  | 허용되는 호스트. |
| Microsoft.AspNetCore.Hosting        | HTTP 요청을 완료하는 데 걸린 시간과 시작 시간. 로드된 호스팅 시작 어셈블리. |
| Microsoft.AspNetCore.Mvc            | MVC 및 Razor 진단. 모델 바인딩, 필터 실행, 뷰 컴파일 작업 선택. |
| Microsoft.AspNetCore.Routing        | 경로 일치 정보. |
| Microsoft.AspNetCore.Server         | 연결 시작, 중지 및 활성 응답 유지. HTTPS 인증서 정보. |
| Microsoft.AspNetCore.StaticFiles    | 제공된 파일. |
| Microsoft.EntityFrameworkCore       | 일반 Entity Framework Core 진단. 데이터베이스 작업 및 구성, 변경 내용 검색, 마이그레이션. |

콘솔 창에서 더 많은 범주를 보려면 **appsettings.Development.json**을 다음으로 설정합니다.

[!code-json[](index/samples/3.x/MyMain/appsettings.Trace.json)]

<!-- Review: What other providers support scopes? Console is not generally used in staging/production  -->

<a name="logscopes"></a>

## <a name="log-scopes"></a>로그 범위

 *범위*는 논리적 작업 집합을 그룹화할 수 있습니다. 이 그룹화는 집합의 일부로 생성된 각 로그에 동일한 데이터를 연결하는 데 사용될 수 있습니다. 예를 들어 트랜잭션 처리의 일부로 생성되는 모든 로그에는 트랜잭션 ID가 포함될 수 있습니다.

범위:

* <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> 메서드에서 반환하는 <xref:System.IDisposable> 형식입니다.
* 삭제될 때까지 유지됩니다.

범위를 지원하는 공급자는 다음과 같습니다.

* `Console`
* [AzureAppServicesFile 및 AzureAppServicesBlob](xref:Microsoft.Extensions.Logging.AzureAppServices.BatchingLoggerOptions.IncludeScopes)

`using` 블록에 로거 호출을 래핑하여 범위를 사용합니다.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Scopes)]

다음 JSON은 콘솔 공급자에 대해 범위를 사용하도록 설정합니다.

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Scopes.json)]

다음은 콘솔 공급자에 대한 범위를 사용하도록 설정하는 코드입니다.

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_Scopes)]

일반적으로 로깅은 코드에서가 아니라 구성에서 지정해야 합니다.

<a name="bilp"></a>

## <a name="built-in-logging-providers"></a>기본 로깅 공급자

ASP.NET Core에는 다음과 같은 로깅 공급자가 있습니다.

* [콘솔](#console)
* [디버그](#debug)
* [EventSource](#event-source)
* [EventLog](#welog)
* [AzureAppServicesFile 및 AzureAppServicesBlob](#azure-app-service)
* [ApplicationInsights](#azure-application-insights)

ASP.NET Core 모듈을 사용한 `stdout` 및 디버그 로깅에 대한 자세한 내용은 및 <xref:test/troubleshoot-azure-iis> 및 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>을 참조하세요.

### <a name="console"></a>Console

`Console` 공급자는 콘솔에 출력을 로그합니다. 개발 중인 `Console` 로그를 보는 방법에 대한 자세한 내용은 [dotnet run 및 Visual Studio의 로깅 출력](#dnrvs)을 참조하세요.

### <a name="debug"></a>디버그

`Debug` 공급자는 [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) 클래스를 사용하여 로그 출력을 기록합니다. `System.Diagnostics.Debug.WriteLine`을 호출하여 `Debug` 공급자에 기록합니다.

Linux에서 `Debug` 공급자 로그 위치는 배포판마다 다르며 다음 중 하나일 수 있습니다.

* */var/log/message*
* */var/log/syslog*

### <a name="event-source"></a>이벤트 원본

`EventSource` 공급자는 이름이 `Microsoft-Extensions-Logging`인 플랫폼 간 이벤트 원본에 기록합니다. Windows에서 공급자는 [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)를 사용합니다.

#### <a name="dotnet-trace-tooling"></a>dotnet 추적 도구

[dotnet 추적](/dotnet/core/diagnostics/dotnet-trace) 도구는 실행 중인 프로세스의 .NET Core 추적을 수집할 수 있도록 하는 플랫폼 간 CLI 전역 도구입니다. 이 도구는 <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>를 사용하여 <xref:Microsoft.Extensions.Logging.EventSource> 공급자 데이터를 수집합니다.

설치 지침은 [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace)를 참조하세요.

Dotnet 추적 도구를 사용하여 앱에서 추적을 수집합니다.

1. `dotnet run` 명령으로 앱을 실행합니다.
1. .NET Core 앱의 PID(프로세스 식별자)를 확인합니다.
   * Windows에서 다음 접근 방식 중 하나를 사용합니다.
     * 작업 관리자(Ctrl + Alt + Del)
     * [tasklist 명령](/windows-server/administration/windows-commands/tasklist)
     * [Get-Process Powershell 명령](/powershell/module/microsoft.powershell.management/get-process)
   * Linux에서는 [pidof 명령](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html)을 사용합니다.

   앱의 어셈블리와 동일한 이름의 프로세스에 대한 PID를 찾습니다.

1. `dotnet trace` 명령을 실행합니다.

   일반 명령 구문:

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   PowerShell 명령 셸을 사용하는 경우 `--providers` 값을 작은따옴표(`'`)로 묶습니다.

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   Windows가 아닌 플랫폼에서는 출력 추적 파일의 형식을 `speedscope`로 변경하는 `-f speedscope` 옵션을 추가합니다.

   | 키워드 | Description |
   | :-----: | ----------- |
   | 1       | `LoggingEventSource`에 대한 로그 메타 이벤트입니다. `ILogger`)에서 이벤트를 기록하지 않습니다. |
   | 2       | `ILogger.Log()`가 호출될 때 `Message` 이벤트 설정을 켭니다. 서식 지정되지 않은 프로그래밍 방식으로 정보를 제공합니다. |
   | 4       | `ILogger.Log()`가 호출될 때 `FormatMessage` 이벤트 설정을 켭니다. 서식 지정된 문자열 버전의 정보를 제공합니다. |
   | 8       | `ILogger.Log()`가 호출될 때 `MessageJson` 이벤트 설정을 켭니다. 인수의 JSON 표현을 제공합니다. |

   | 이벤트 수준 | 설명     |
   | :---------: | --------------- |
   | 0           | `LogAlways`     |
   | 1           | `Critical`      |
   | 2           | `Error`         |
   | 3           | `Warning`       |
   | 4           | `Informational` |
   | 5           | `Verbose`       |

   `FilterSpecs``{Logger Category}` 및 `{Event Level}`에 대한 항목은 추가 로그 필터링 조건을 나타냅니다. `FilterSpecs` 항목을 세미콜론(`;`)으로 구분합니다.

   Windows 명령 셸을 사용하는 예(`--providers` 값에 작은따옴표를 사용하지 **않음**):

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   이전 명령은 다음과 같이 활성화됩니다.

   * (`2`) 오류에 대해 형식이 지정된 문자열(`4`)을 생성하는 이벤트 원본 로거입니다.
   * `Informational` 로깅 수준에서 `Microsoft.AspNetCore.Hosting` 로깅(`4`).

1. Enter 키 또는 Ctrl + C를 눌러 dotnet 추적 도구를 중지합니다.

   추적은 `dotnet trace` 명령이 실행되는 폴더에 *trace.nettrace* 이름으로 저장됩니다.

1. [Perfview](#perfview)를 사용하여 추적을 엽니다. *trace.nettrace* 파일을 열고 추적 이벤트를 탐색합니다.

앱이 `CreateDefaultBuilder`를 사용하여 호스트를 빌드하지 않는 경우 [이벤트 원본 공급자](#event-source-provider)를 앱의 로깅 구성에 추가합니다.

자세한 내용은 다음을 참조하세요.

* [성능 분석 유틸리티 추적(dotnet 추적)](/dotnet/core/diagnostics/dotnet-trace)(.NET Core 설명서)
* [성능 분석 유틸리티 추적(dotnet 추적)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md)(dotnet/진단 GitHub 리포지토리 설명서)
* [LoggingEventSource 클래스](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource)(.NET API 브라우저)
* <xref:System.Diagnostics.Tracing.EventLevel>
* [LoggingEventSource 참조 원본(3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): 다른 버전의 참조 원본을 가져오려면 분기를 `release/{Version}`으로 변경합니다. 여기서 `{Version}`은 원하는 ASP.NET Core 버전입니다.
* [Perfview](#perfview): 이벤트 원본 추적을 보는 데 유용합니다.

#### <a name="perfview"></a>Perfview

[PerfView 유틸리티](https://github.com/Microsoft/perfview)를 사용하여 로그를 수집하고 볼 수 있습니다. ETW 로그를 보는 다른 도구도 있지만, PerfView는 ASP.NET Core에서 내보내는 ETW 이벤트를 처리하기에 가장 좋은 환경을 제공합니다.

이 공급자가 기록한 이벤트를 수집하도록 PerfView를 구성하려면 **추가 공급자** 목록에 `*Microsoft-Extensions-Logging` 문자열을 추가합니다. 문자열의 시작 부분에 `*`를 누락하지 마세요.

<a name="welog"></a>

### <a name="windows-eventlog"></a>Windows EventLog

`EventLog` 공급자는 로그 출력을 Windows 이벤트 로그에 보냅니다. 다른 공급자와 달리 `EventLog` 공급자는 기본 비공급자 설정을 상속하지 ***않습니다***. `EventLog` 로그 설정을 지정하지 않으면 [LogLevel.Warning으로 기본 설정](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103)됩니다.

<xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> 수준보다 낮은 이벤트를 기록하려면 로그 수준을 명시적으로 설정합니다. 다음 예제에서는 이벤트 로그 기본 로그 수준을 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>으로 설정합니다.

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

[AddEventLog 오버로드](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)는 <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>를 전달할 수 있습니다. `null`이거나 지정하지 않으면 다음 기본 설정이 사용됩니다.

* `LogName`: “Application”
* `SourceName`: “.NET Runtime”
* `MachineName`: 로컬 머신 이름이 사용됩니다.

다음 코드에서는 `SourceName`을 기본값 `".NET Runtime"`에서 `MyLogs`로 변경합니다.

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippetEventLog)]

### <a name="azure-app-service"></a>Azure App Service

[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 공급자 패키지는 Azure App Service 앱의 파일 시스템과 Azure Storage 계정의 [Blob 스토리지](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)에 텍스트 파일을 기록합니다.

이 공급자 패키지는 공유 프레임워크에 포함되어 있지 않습니다. 이 공급자를 사용하려면 프로젝트에 공급자 패키지를 추가합니다.

공급자 설정을 구성하려면 다음 예제와 같이 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> 및 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>를 사용합니다.

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_AzLogOptions)]

Azure App Service에 배포하는 경우 앱은 Azure Portal에서 **App Service** 페이지에 있는 [App Service 로그](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) 섹션의 설정을 사용합니다. 다음 설정이 업데이트되면 앱을 다시 시작하거나 재배포하지 않아도 변경 내용이 즉시 적용됩니다.

* **애플리케이션 로깅(파일 시스템)**
* **애플리케이션 로깅(Blob)**

로그 파일의 기본 위치는 *D:\\home\\LogFiles\\Application* 폴더이며, 기본 파일 이름은 *diagnostics-yyyymmdd.txt*입니다. 기본 파일 크기 제한은 10MB이고, 보존되는 기본 최대 파일 수는 2입니다. 기본 BLOB 이름은 *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*입니다.

이 공급자는 프로젝트가 Azure 환경에서 실행되는 경우에만 로그합니다.

#### <a name="azure-log-streaming"></a>Azure 로그 스트리밍

Azure 로그 스트리밍을 통해 다음에서 로그 활동을 실시간으로 볼 수 있습니다.

* 앱 서버
* 웹 서버
* 실패한 요청 추적

Azure 로그 스트리밍을 구성하려면:

* 앱의 포털 페이지에서 **App Service 로그** 페이지로 이동합니다.
* **애플리케이션 로깅(파일 시스템)** 을 **On**으로 설정합니다.
* 로그 **수준**을 선택합니다. 이 설정은 Azure 로그 스트리밍에만 적용됩니다.

**로그 스트림** 페이지로 이동하여 로그를 봅니다. 로드되는 메시지는 `ILogger` 인터페이스를 사용하여 로그됩니다.

### <a name="azure-application-insights"></a>Azure Application Insights

[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) 공급자 패키지는 [Azure Application Insights](/azure/azure-monitor/app/cloudservices)에 로그를 기록합니다. Application Insights는 웹앱을 모니터링하고 원격 분석 데이터를 쿼리 및 분석하기 위한 도구를 제공하는 서비스입니다. 이 공급자를 사용하는 경우 Application Insights 도구를 사용하여 로그를 쿼리 및 분석할 수 있습니다.

로깅 공급자는 ASP.NET Core에 대한 모든 사용 가능한 원격 분석을 제공하는 패키지인 [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)의 종속성으로 포함됩니다. 이 패키지를 사용하는 경우 공급자 패키지를 설치할 필요가 없습니다.

[Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) 패키지는 ASP.NET Core가 아니라 ASP.NET 4.x용입니다.

자세한 내용은 다음 자료를 참조하세요.

* [Application Insights 개요](/azure/application-insights/app-insights-overview)
* [ASP.NET Core 애플리케이션용 Application Insights](/azure/azure-monitor/app/asp-net-core) - 로깅과 함께 전체 범위 Application Insights 원격 분석을 구현하려면 여기에서 시작합니다.
* [.NET Core ILogger 로그용 ApplicationInsightsLoggerProvider](/azure/azure-monitor/app/ilogger) - 나머지 Application Insights 원격 분석 없이 로깅 공급자를 구현하려면 여기에서 시작합니다.
* [Application Insights 로깅 어댑터](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).
* [Application Insights SDK 설치, 구성 및 초기화](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn 사이트의 대화형 자습서입니다.

## <a name="third-party-logging-providers"></a>타사 로깅 공급자

ASP.NET Core와 호환되는 타사 로깅 프레임워크는 다음과 같습니다.

* [elmah.io](https://elmah.io/)([GitHub 리포지토리](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html)([GitHub 리포지토리](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/)([GitHub 리포지토리](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/)([GitHub 리포지토리](https://github.com/catalingavan/KissLog-net))
* [Log4Net](https://logging.apache.org/log4net/)([GitHub 리포지토리](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/)([GitHub 리포지토리](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/)([GitHub 리포지토리](https://github.com/NLog/NLog.Extensions.Logging))
* [PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/)([GitHub 리포지토리](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))
* [Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/)([GitHub 리포지토리](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging)([Github 리포지토리](https://github.com/googleapis/google-cloud-dotnet))

일부 타사 프레임워크는 [구조적 로깅이라고 하는 의미 체계 로깅](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)을 수행할 수 있습니다.

타사 프레임워크를 사용하는 방법은 다음과 같이 기본 공급자 중 하나를 사용하는 방법과 비슷합니다.

1. 프로젝트에 NuGet 패키지를 추가합니다.
1. 로깅 프레임워크에서 제공하는 `ILoggerFactory` 확장 메서드를 호출합니다.

자세한 내용은 각 공급자의 설명서를 참조하세요. 타사 로깅 공급자는 Microsoft에서 지원되지 않습니다.

<a name="nhca"></a>

## <a name="non-host-console-app"></a>비호스트 콘솔 앱

비 웹 콘솔 앱에서 일반 호스트를 사용하는 방법에 대한 사례는 [백그라운드 작업 샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples)(<xref:fundamentals/host/hosted-services>)의 *Program.cs* 파일을 참조하세요.

제네릭 호스트를 사용하지 않는 앱의 로깅 코드는 [공급자 추가](#add-providers) 및 [로거 생성](#create-logs) 방식에 따라 달라집니다. 

### <a name="logging-providers"></a>로깅 공급자

비 호스트 콘솔 앱에서는 `LoggerFactory`를 만드는 동안 공급자의 `Add{provider name}` 확장 메서드를 호출합니다.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11-12)]

### <a name="create-logs"></a>로그 만들기

로그를 만들려면 <xref:Microsoft.Extensions.Logging.ILogger%601> 개체를 사용합니다. `LoggerFactory`를 사용하여 `ILogger`를 만듭니다.

다음 예제에서는 `LoggingConsoleApp.Program`을 범주로 사용하여 로거를 만듭니다.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=14)]

다음의 ASP.NET Core 예제에서 로거는 `Information`을 수준으로 사용하여 로그를 만드는 데 사용됩니다. 로그 *수준*은 기록된 이벤트의 심각도를 나타냅니다.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=15)]

[수준](#log-level) 및 [범주](#log-category)는 이 문서에 자세히 설명되어 있습니다.

<a name="lhc"></a>

## <a name="log-during-host-construction"></a>호스트 생성 중 로깅

호스트 생성 중 로깅은 직접 지원되지 않습니다. 그러나 별도의 로거를 사용할 수 있습니다. 다음 예에서는 [Serilog](https://serilog.net/) 로거를 사용하여 `CreateHostBuilder`에 로그인합니다. `AddSerilog`에서 `Log.Logger`에 지정된 정적 구성을 사용합니다.

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

<a name="csdi"></a>

## <a name="configure-a-service-that-depends-on-ilogger"></a>ILogger에 종속되는 서비스 구성

이전 버전의 ASP.NET Core에서는 `Startup`에 대한 로거의 생성자 주입이 작동하는데, 이는 웹 호스트에 대한 별도의 DI 컨테이너가 생성되기 때문입니다. 제네릭 호스트에 대해 하나의 컨테이너만 생성되는 이유에 대한 자세한 내용은 [호환성이 손상되는 변경 공지](https://github.com/aspnet/Announcements/issues/353)를 참조하세요.

`ILogger<T>`에 종속되는 서비스를 구성하려면 생성자 주입을 사용하거나 팩터리 메서드를 제공합니다. 팩터리 메서드 접근 방식은 다른 옵션이 없는 경우에만 권장됩니다. 예를 들어 DI에서 제공하는 `ILogger<T>` 인스턴스가 필요한 서비스를 살펴보겠습니다.

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup2.cs?name=snippet_ConfigureServices&highlight=6-10)]

위에 강조 표시된 코드는 DI 컨테이너가 `MyService`의 인스턴스를 처음 생성해야 할 때 실행되는 [Func](/dotnet/api/system.func-2)입니다. 이러한 방식으로 등록된 서비스에 액세스할 수 있습니다.

<a name="clms"></a>

## <a name="create-logs-in-main"></a>Main에서 로그 만들기

다음 코드는 호스트를 빌드한 후 DI에서 `ILogger` 인스턴스를 가져와 `Main`에 로그합니다.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_LogProgram)]

### <a name="create-logs-in-startup"></a>시작 시 로그 만들기

다음 코드는 `Startup.Configure`에 로그를 작성합니다.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Startup.cs?name=snippet_Configure)]

`Startup.ConfigureServices` 메서드에서 DI 컨테이너 설정이 완료되기 전에 로그를 작성하는 작업은 지원되지 않습니다.

* `Startup` 생성자에 대한 로거 주입은 지원되지 않습니다.
* `Startup.ConfigureServices` 메서드 시그니처에 대한 로거 주입은 지원되지 않습니다.

이러한 제한의 이유는 로깅이 DI와 구성에 종속되며, 이는 결국 DI에 종속되기 때문입니다. DI 컨테이너는 `ConfigureServices`가 완료될 때까지 설정되지 않습니다.

`ILogger<T>`에 종속되는 서비스를 구성하는 방법이나 `Startup`로 로거의 생성자 주입이 이전 버전에서 작동한 이유에 대한 자세한 내용은 [ILogger에 종속되는 서비스 구성](#csdi)을 참조하세요.

### <a name="no-asynchronous-logger-methods"></a>비동기 로거 메서드 미지원

로깅은 매우 빨라서 비동기 코드의 성능 비용을 들일 필요가 없습니다. 로깅 데이터 저장소가 느린 경우 직접 작성하지 마세요. 로그 메시지를 처음에 빠른 저장소에 작성한 다음, 나중에 느린 저장소로 이동하는 것이 좋습니다. 예를 들어 SQL Server에 로그하는 경우 `Log` 메서드는 동기식이므로 `Log` 메서드에서 직접 로그하지 마세요. 대신 동기적으로 로그 메시지를 메모리 내 큐에 추가하고 백그라운드 작업자가 큐에서 메시지를 풀하여 SQL Server에 대해 비동기 데이터 푸시 작업을 수행하도록 합니다. 자세한 내용은 [이](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub 이슈를 참조하세요.

<a name="clib"></a>

## <a name="change-log-levels-in-a-running-app"></a>실행 중인 앱에서 로그 수준 변경

로깅 API는 앱이 실행되는 동안 로그 수준을 변경하는 시나리오를 포함하지 않습니다. 그러나 일부 구성 공급자는 구성을 다시 로드할 수 있으며 이는 로깅 구성에 대한 즉각적인 영향을 줍니다. 예를 들어 [파일 구성 공급자](xref:fundamentals/configuration/index#file-configuration-provider)는 기본적으로 로깅 구성을 다시 로드합니다. 앱이 실행되는 동안 코드에서 구성이 변경되면 앱 [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*)를 호출하여 앱의 로깅 구성을 업데이트할 수 있습니다.

## <a name="ilogger-and-iloggerfactory"></a>ILogger 및 ILoggerFactory

<xref:Microsoft.Extensions.Logging.ILogger%601> 및 <xref:Microsoft.Extensions.Logging.ILoggerFactory> 인터페이스와 구현은 .NET Core SDK에 포함되어 있습니다. 또한 다음 NuGet 패키지에서도 사용할 수 있습니다.  

* 인터페이스는 [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)에 있습니다.
* 기본 구현은 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/)에 있습니다.

<!-- review. Why would you want to hard code filtering rules in code? -->
<a name="fric"></a>

## <a name="apply-log-filter-rules-in-code"></a>코드에서 로그 필터 규칙 적용

로그 필터 규칙을 설정하는 기본 방법은 [구성](xref:fundamentals/configuration/index)을 사용하는 것입니다.

다음 예제는 코드에서 필터 규칙을 등록하는 방법을 보여줍니다.

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterInCode)]

`logging.AddFilter("System", LogLevel.Debug)`는 `System`범주 및 로그 수준`Debug`을 지정합니다. 특정 공급자를 구성하지 않았으므로 필터가 모든 공급자에 적용됩니다.

`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)`은 다음을 지정합니다.

* `Debug` 로깅 공급자
* 로그 수준 `Information` 이상
* `"Microsoft"`로 시작하는 모든 범주

## <a name="create-a-custom-logger"></a>사용자 지정 로거 만들기

사용자 지정 로거를 추가하려면 <xref:Microsoft.Extensions.Logging.ILoggerFactory>를 사용하여 <xref:Microsoft.Extensions.Logging.ILoggerProvider>를 추가합니다.

```csharp
public void Configure(
    IApplicationBuilder app,
    IWebHostEnvironment env,
    ILoggerFactory loggerFactory)
{
    loggerFactory.AddProvider(new CustomLoggerProvider(new CustomLoggerConfiguration()));
```

`ILoggerProvider`는 하나 이상의 `ILogger` 인스턴스를 만듭니다. `ILogger` 인스턴스는 프레임워크에서 정보를 로그하는 데 사용됩니다.

### <a name="sample-custom-logger-configuration"></a>샘플 사용자 지정 로거 구성

샘플:

* 이벤트 ID 및 로그 수준으로 로그 콘솔의 색을 설정하는 기본적인 샘플로 설계되었습니다. 로거는 일반적으로 이벤트 ID로 변경되지 않으며 로그 수준에 국한되지 않습니다.
* 다음 구성 유형을 사용하여 로그 수준 및 이벤트 ID에 따라 다른 색의 콘솔 항목을 만듭니다.

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerConfiguration.cs?name=snippet)]

위의 코드는 기본 수준을 `Warning`으로 설정하고 색을 `Yellow`로 설정합니다. `EventId`를 0으로 설정하면 모든 이벤트를 로그합니다.

### <a name="create-the-custom-logger"></a>사용자 지정 로거 만들기

`ILogger` 구현 범주 이름은 일반적으로 로깅 원본입니다. 예를 들어 다음은 로거를 만드는 형식입니다.

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLogger.cs?name=snippet)]

위의 코드는

* 범주 이름별로 로거 인스턴스를 만듭니다.
* `IsEnabled`에서 `logLevel == _config.LogLevel`을 확인하므로 각 `logLevel`에 고유한 로거가 있습니다. 일반적으로 모든 상위 로그 수준에 대해서도 로거를 사용하도록 설정해야 합니다.

```csharp
public bool IsEnabled(LogLevel logLevel)
{
    return logLevel >= _config.LogLevel;
}
```

### <a name="create-the-custom-loggerprovider"></a>사용자 지정 LoggerProvider 만들기

`LoggerProvider`는 로거 인스턴스를 만드는 클래스입니다. 범주별로 로거 인스턴스를 만들 필요가 없을 수도 있지만, NLog 또는 log4net와 같은 일부 로거에 적합합니다. 이렇게 하면 필요한 경우 범주별로 다른 로깅 출력 대상을 선택할 수도 있습니다.

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerProvider.cs?name=snippet)]

위의 코드에서 <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*>는 범주 이름별로 `ColorConsoleLogger`의 단일 인스턴스를 만들어 [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2)에 저장합니다.

### <a name="usage-and-registration-of-the-custom-logger"></a>사용자 지정 로거의 사용 및 등록

`Startup.Configure`에 로거를 등록합니다.

[!code-csharp[](index/samples/3.x/CustomLogger/Startup.cs?name=snippet)]

앞의 코드에서는 `ILoggerFactory`를 위한 확장 메서드를 하나 이상 제공합니다.

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerExtensions.cs?name=snippet)]

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/logging/loggermessage>
* 로깅 버그는 [github.com/dotnet/runtime/](https://github.com/dotnet/runtime/issues) 리포지토리에 만들어집니다.
* <xref:blazor/fundamentals/logging>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Steve Smith](https://ardalis.com/)

.NET Core는 다양한 기본 제공 및 타사 로깅 공급자에서 작동하는 로깅 API를 지원합니다. 이 문서에서는 기본 제공 공급자를 이용하여 로깅 API를 사용하는 방법을 보여줍니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>공급자 추가

로깅 공급자는 로그를 표시하거나 저장합니다. 예를 들어 콘솔 공급자는 콘솔에 로그를 표시하고 Azure Application Insights 공급자는 로그를 Azure Application Insights에 저장합니다. 여러 공급자를 추가하여 로그를 여러 대상으로 보낼 수 있습니다.

공급자를 추가하려면 *Program.cs*에서 공급자의 `Add{provider name}` 확장 메서드를 호출합니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

앞의 코드는 `Microsoft.Extensions.Logging` 및 `Microsoft.Extensions.Configuration`에 대한 참조를 요구합니다.

기본 프로젝트 템플릿은 다음과 같은 로깅 공급자를 추가하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>를 호출합니다.

* Console
* 디버그
* EventSource(ASP.NET Core 2.2부터 시작)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

`CreateDefaultBuilder`를 사용하는 경우 기본 제공자를 사용자가 선택한 대로 바꿀 수 있습니다. <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>를 호출하고 원하는 공급자를 추가합니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

문서의 뒷부분에 나오는 [기본 제공 로깅 공급자](#built-in-logging-providers) 및 [타사 로깅 공급자](#third-party-logging-providers)에서 더 자세히 알아봅니다.

## <a name="create-logs"></a>로그 만들기

로그를 만들려면 <xref:Microsoft.Extensions.Logging.ILogger%601> 개체를 사용합니다. 웹앱 또는 호스트되는 서비스에서는 DI(종속성 주입)로부터 `ILogger`를 가져옵니다. 비 호스트 콘솔 앱에서는 `LoggerFactory`를 사용하여 `ILogger`를 만듭니다.

다음 ASP.NET Core 예제는 `TodoApiSample.Pages.AboutModel`을 범주로 사용하여 로거를 만듭니다. 로그 *범주*는 각 로그와 연결된 문자열입니다. DI에서 제공한 `ILogger<T>` 인스턴스는 `T` 형식의 정규화된 이름을 범주로 가진 로그를 만듭니다. 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

다음의 ASP.NET Core 및 콘솔 앱 예제에서 로거는 `Information`을 수준으로 사용하여 로그를 만드는 데 사용됩니다. 로그 *수준*은 기록된 이벤트의 심각도를 나타냅니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[수준](#log-level) 및 [범주](#log-category)는 이 문서의 뒷부분에 자세히 설명되어 있습니다.

### <a name="create-logs-in-startup"></a>시작 시 로그 만들기

`Startup` 클래스에서 로그를 작성하려면 생성자 시그니처에 `ILogger` 매개 변수를 포함합니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a>Program 클래스에서 로그 만들기

`Program` 클래스에 로그를 작성하려면 DI에서 `ILogger` 인스턴스를 가져옵니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

호스트 생성 중 로깅은 직접 지원되지 않습니다. 그러나 별도의 로거를 사용할 수 있습니다. 다음 예에서는 [Serilog](https://serilog.net/) 로거를 사용하여 `CreateWebHostBuilder`에 로그인합니다. `AddSerilog`에서 `Log.Logger`에 지정된 정적 구성을 사용합니다.

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="no-asynchronous-logger-methods"></a>비동기 로거 메서드 미지원

로깅은 매우 빨라서 비동기 코드의 성능 비용을 들일 필요가 없습니다. 로깅 데이터 저장소가 느린 경우 직접 작성하지 마세요. 로그 메시지를 처음에 빠른 저장소에 작성한 다음, 나중에 느린 저장소로 이동하는 것이 좋습니다. 예를 들어 SQL Server에 로그하는 경우 `Log` 메서드는 동기식이므로 `Log` 메서드에서 직접 로그하지는 않습니다. 대신 동기적으로 로그 메시지를 메모리 내 큐에 추가하고 백그라운드 작업자가 큐에서 메시지를 풀하여 SQL Server에 대해 비동기 데이터 푸시 작업을 수행하도록 합니다. 자세한 내용은 [이](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub 이슈를 참조하세요.

## <a name="configuration"></a>Configuration

로깅 공급자 구성은 하나 이상의 구성 공급자에서 제공합니다.

* 파일 형식(INI, JSON 및 XML).
* 명령줄 인수.
* 환경 변수.
* 메모리 내 .NET 개체.
* 암호화되지 않은 [암호 관리자](xref:security/app-secrets) 저장소.
* 암호화된 사용자 저장소(예:[Azure Key Vault](xref:security/key-vault-configuration)).
* 사용자 지정 공급자(설치 또는 생성된).

예를 들어 로깅 구성은 일반적으로 앱 설정 파일의 `Logging` 섹션에서 제공됩니다. 다음 예제에서는 일반적인 *appsettings.Development.json* 파일의 콘텐츠를 보여줍니다.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

`Logging` 속성은 `LogLevel` 및 로그 공급자 속성을 포함할 수 있습니다(Console이 나타나 있습니다).

`Logging` 아래의 `LogLevel` 속성은 선택한 범주에 대해 로그할 최소 [수준](#log-level)을 지정합니다. 이 예제에서는 `System` 및 `Microsoft` 범주는 `Information` 수준으로 로그되고 다른 모든 범주는 `Debug` 수준으로 로그됩니다.

`Logging` 아래의 다른 속성은 로깅 공급자를 지정합니다. 이 예제는 콘솔 공급자에 대한 것입니다. 공급자가 [로그 범위](#log-scopes)를 지원하는 경우 `IncludeScopes`는 사용 가능 여부를 나타냅니다. 공급자 속성(예: 예제에서 `Console`)은 `LogLevel` 속성을 지정할 수도 있습니다. 공급자 아래의 `LogLevel`은 해당 공급자에 대한 로그 수준을 지정합니다.

수준이 `Logging.{providername}.LogLevel`에 지정된 경우 `Logging.LogLevel`에 설정된 모든 수준을 재정의합니다. 예를 들어, 다음과 같은 JSON을 가정해 봅시다.

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

위의 JSON에서 `Console` 공급자 설정은 이전(기본) 로그 수준을 재정의합니다.

로깅 API는 앱이 실행되는 동안 로그 수준을 변경하는 시나리오를 포함하지 않습니다. 그러나 일부 구성 공급자는 구성을 다시 로드할 수 있으며 이는 로깅 구성에 대한 즉각적인 영향을 줍니다. 예를 들어 [파일 구성 공급자](xref:fundamentals/configuration/index#file-configuration-provider)는 설정 파일을 읽기 위해 `CreateDefaultBuilder`에 의해 추가되며 기본적으로 로깅 구성을 다시 로드합니다. 앱이 실행되는 동안 코드에서 구성이 변경되면 앱 [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*)를 호출하여 앱의 로깅 구성을 업데이트할 수 있습니다.

구성 공급자 구현에 대한 자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.

## <a name="sample-logging-output"></a>샘플 로깅 출력

이전 섹션에 나와 있는 샘플 코드를 사용하면 명령줄에서 앱을 실행할 때 콘솔에 로그가 표시됩니다. 다음은 콘솔 출력의 예입니다.

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

위의 로그는 샘플 앱의 `http://localhost:5000/api/todo/0`에 HTTP Get 요청을 하여 생성되었습니다.

다음은 Visual Studio에서 샘플 앱을 실행할 때 디버그 창에 나타나는 것과 동일한 로그의 예입니다.

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

이전 섹션에 표시된 `ILogger` 호출을 통해 생성된 로그는 "TodoApi"로 시작합니다. "Microsoft" 범주로 시작하는 로그는 ASP.NET Core 프레임워크 코드에서 온 것입니다. ASP.NET Core 및 애플리케이션 코드는 동일한 로깅 API와 공급자를 사용합니다.

이 문서의 나머지 부분에서는 로깅에 대한 일부 세부 정보 및 옵션을 설명합니다.

## <a name="nuget-packages"></a>NuGet 패키지

`ILogger` 및 `ILoggerFactory` 인터페이스는 [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)에 있으며, 두 인터페이스의 기본 구현은 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/)에 있습니다.

## <a name="log-category"></a>로그 범주

`ILogger` 개체가 생성되면 개체에 대한 *범주*가 지정됩니다. 해당 범주는 `ILogger`의 해당 인스턴스에서 만든 각 로그 메시지에 포함됩니다. 범주는 임의의 문자열일 수 있지만 "TodoApi.Controllers.TodoController"와 같은 클래스 이름을 사용하는 것이 규칙입니다.

`ILogger<T>`를 사용하여 `T`의 정규화된 형식 이름을 범주로 사용하는 `ILogger` 인스턴스를 가져옵니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

범주를 명시적으로 지정하려면 `ILoggerFactory.CreateLogger`를 호출합니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

`ILogger<T>`는 `T`의 정규화된 형식 이름으로 `CreateLogger`를 호출하는 것과 동일합니다.

## <a name="log-level"></a>로그 수준

모든 로그는 <xref:Microsoft.Extensions.Logging.LogLevel> 값을 지정합니다. 로그 수준은 심각도 또는 중요도를 나타냅니다. 예를 들어 메서드가 정상적으로 종료되면 `Information` 로그를 작성하고 메서드가 *404 찾을 수 없음* 상태 코드를 반환하면 `Warning` 로그를 작성할 수 있습니다.

다음 코드는 `Information` 및 `Warning` 로그를 만듭니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

위의 코드에서 `MyLogEvents.GetItem` 및 `MyLogEvents.GetItemNotFound` 매개 변수는 [로그 이벤트 ID](#log-event-id)입니다. 두 번째 매개 변수는 나머지 메서드 매개 변수가 제공하는 인수 값에 대한 자리 표시자를 포함하고 있는 메시지 템플릿입니다. 메서드 매개 변수는 이 문서의 [로그 메시지 템플릿 섹션](#lmt)에 설명되어 있습니다.

메서드 이름에 수준을 포함하는 로그 메서드(예: `LogInformation` 및 `LogWarning`)는 [ILogger에 대한 확장 메서드](xref:Microsoft.Extensions.Logging.LoggerExtensions)입니다. 이 메서드들은 `LogLevel` 매개 변수를 사용하는 `Log` 메서드를 호출합니다. 이러한 확장 메서드 중 하나를 호출하는 대신 `Log` 메서드를 직접 호출할 수도 있지만, 구문이 비교적 복잡합니다. 자세한 내용은 <xref:Microsoft.Extensions.Logging.ILogger> 및 [로거 확장 소스 코드](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)를 참조하세요.

ASP.NET Core는 다음과 같은 로그 수준을 정의하며, 여기에는 가장 낮은 심각도에서 가장 높은 심각도 순으로 정렬되어 있습니다.

* Trace = 0

  일반적으로 디버깅에만 유용한 정보입니다. 이러한 메시지는 중요한 애플리케이션 데이터를 포함할 수 있으므로 프로덕션 환경에서 사용하면 안 됩니다. *기본적으로 사용하지 않도록 설정됩니다.*

* Debug = 1

  개발 및 디버깅에 유용할 수 있는 정보입니다. 예: `Entering method Configure with flag set to true.` 로그 볼륨이 크기 때문에 문제 해결 시에만 `Debug` 수준 로그를 프로덕션 환경에서 사용합니다.

* Information = 2

  앱의 일반적인 흐름을 추적합니다. 이러한 로그는 일반적으로 장기적인 가치가 있습니다. 예: `Request received for path /api/todo`

* Warning = 3

  앱의 흐름에서 비정상적이거나 예기치 않은 이벤트를 위한 수준입니다. 앱이 중지되지는 않지만 조사해야 하는 오류 또는 기타 조건이 여기에 포함될 수 있습니다. 처리된 예외는 `Warning` 로그 수준을 사용하는 일반적인 장소입니다. 예: `FileNotFoundException for file quotes.txt.`

* Error = 4

  처리할 수 없는 오류 및 예외를 위한 수준입니다. 이러한 메시지는 전체 앱 오류가 아닌 현재 동작 또는 작업(예: 현재 HTTP 요청)의 오류를 의미합니다. 예제 로그 메시지: `Cannot insert record due to duplicate key violation.`

* Critical = 5

  즉각적인 대응이 필요한 오류를 위한 수준입니다. 예: 데이터 손실 시나리오, 디스크 공간 부족.

로그 수준을 사용하여 특정 스토리지 매체 또는 디스플레이 창에 기록되는 로그 출력의 양을 제어합니다. 예를 들어:

* 프로덕션:
  * `Trace`~`Information` 수준 로깅은 자세한 로그 메시지를 대량으로 생성합니다. 비용을 관리하고 데이터 저장소 제한을 초과하지 않으려면 `Trace`~`Information` 수준 메시지를 대용량, 저비용 데이터 저장소에 기록합니다.
  * `Warning`~`Critical` 수준 로깅에서 생성되는 로그 메시지는 일반적으로 수량 및 용량이 더 적습니다. 따라서 비용 및 스토리지 제한은 대개의 경우 문제가 되지 않으므로 데이터 스토리지를 유연하게 선택할 수 있습니다.
* 개발 중:
  * `Warning`~`Critical` 메시지를 콘솔에 기록합니다.
  * 문제 해결 시 `Trace`~`Information` 메시지를 추가합니다.

이 문서의 뒷부분에 나오는 [로그 필터링](#log-filtering) 섹션에서는 공급자가 처리하는 로그 수준을 제어하는 방법을 설명합니다.

ASP.NET Core는 프레임워크 이벤트에 대한 로그를 작성합니다. 이 문서 앞부분에 나온 로그 예제는 `Information` 수준 미만 로그를 제외했기 때문에 `Debug` 또는 `Trace` 수준 로그가 생성되지 않습니다. 다음은 `Debug` 로그를 표시하도록 구성된 샘플 앱을 실행하여 생성된 콘솔 로그의 예입니다.

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a>로그 이벤트 ID

각 로그는 *이벤트 ID*를 지정할 수 있습니다. 샘플 앱은 로컬에 정의된 `LoggingEvents` 클래스를 사용하여 이 작업을 수행합니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

이벤트 ID는 이벤트 집합을 연결합니다. 예를 들어 페이지에서 항목 목록을 표시하는 것과 관련된 모든 로그는 1001일 수 있습니다.

로깅 공급자는 이벤트 ID를 ID 필드, 로그 메시지에 저장하거나 또는 전혀 저장하지 않을 수도 있습니다. 디버그 공급자는 이벤트 ID를 표시하지 않습니다. 콘솔 공급자는 범주 뒤에 대괄호로 이벤트 ID를 표시합니다.

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>로그 메시지 템플릿

각 로그는 메시지 템플릿을 지정합니다. 메시지 템플릿에는 인수가 제공되는 자리 표시자를 포함할 수 있습니다. 번호가 아닌 자리 표시자의 이름을 사용합니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

값을 제공하는 데 사용되는 매개 변수를 결정하는 것은 자리 표시자의 이름이 아닌 순서입니다. 다음 코드에서는 매개 변수 이름이 메시지 템플릿 순서와 일치하지 않습니다.

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

이 코드는 매개 변수 값을 순서대로 사용하여 로그 메시지를 만듭니다.

```text
Parameter values: parm1, parm2
```

로깅 프레임워크는 로깅 공급자가 [구조화된 로깅이라고도 하는 의미 체계 로깅](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)을 구현할 수 있도록 이 방식으로 작동합니다. 인수 자체는 서식이 지정된 메시지 템플릿뿐만 아니라 로깅 시스템에 전달됩니다. 이 정보를 통해 로깅 공급자는 매개 변수 값을 필드로 저장할 수 있습니다. 예를 들어 로거 메서드 호출이 다음과 같다고 가정합니다.

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Azure Table Storage에 로그를 보내는 경우 각 Azure Table 엔터티는 로그 데이터에 대한 쿼리를 간소화하는 `ID` 및 `RequestTime` 속성을 가질 수 있습니다. 쿼리는 문자 메시지의 시간 초과를 구문 분석하지 않고 특정 `RequestTime` 범위 내의 모든 로그를 찾을 수 있습니다.

## <a name="logging-exceptions"></a>예외 로깅

로거 메서드는 다음 예제와 같이 예외를 전달할 수 있는 오버로드를 갖고 있습니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

공급자마다 다양한 방식으로 예외 정보를 처리합니다. 다음은 위에서 살펴본 코드의 디버그 공급자 출력의 예제입니다.

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>로그 필터링

특정 공급자 및 범주 또는 모든 공급자나 모든 범주의 최소 로그 수준을 지정할 수 있습니다. 최소 수준 미만인 로그는 해당 공급자에게 전달되지 않으므로 표시되거나 저장되지 않습니다.

모든 로그를 표시하지 않으려면 `LogLevel.None`을 최소 로그 수준으로 지정합니다. `LogLevel.None`의 정수 값은 `LogLevel.Critical`(5)보다 높은 6입니다.

### <a name="create-filter-rules-in-configuration"></a>구성에서 필터 규칙 만들기

프로젝트 템플릿 코드는 `CreateDefaultBuilder`를 호출하여 콘솔, 디버그 및 EventSource(ASP.NET Core 2.2 이상) 공급자에 대한 로깅을 설정합니다. [이 문서의 앞](#configuration)에서 설명한 것처럼 `CreateDefaultBuilder` 메서드는 `Logging` 섹션에서 구성을 조회하는 로깅을 설정합니다.

구성 데이터는 다음 예제와 같이 공급자 및 범주별로 최소 로그 수준을 지정합니다.

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

이 JSON은 6개의 필터 규칙을 만드는데, 하나는 디버그 공급자용이고, 4개는 콘솔 공급자용이고, 나머지 하나는 모든 공급자용입니다. `ILogger` 개체가 생성될 때 각 공급자에 대해 단일 규칙이 선택됩니다.

### <a name="filter-rules-in-code"></a>코드의 필터 규칙

다음 예제는 코드에서 필터 규칙을 등록하는 방법을 보여줍니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

두 번째 `AddFilter`는 형식 이름을 사용하여 디버그 공급자를 지정합니다. 첫 번째 `AddFilter`는 공급자 형식을 지정하지 않으며, 따라서 모든 공급자에 적용됩니다.

### <a name="how-filtering-rules-are-applied"></a>필터링 규칙 적용 방식

이전 예제의 구성 데이터 및 `AddFilter` 코드는 다음 표에 표시된 규칙을 만듭니다. 처음 6개는 구성 예제로부터 비롯된 것이고 마지막 2개는 코드 예제로부터 비롯된 것입니다.

| 번호 | 공급자      | 다음으로 시작하는 범주...          | 최소 로그 수준 |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | 디버그         | 모든 범주                          | 정보       |
| 2      | Console       | Microsoft.AspNetCore.Mvc.Razor.Internal | Warning           |
| 3      | Console       | Microsoft.AspNetCore.Mvc.Razor.Razor    | 디버그             |
| 4      | Console       | Microsoft.AspNetCore.Mvc.Razor          | Error             |
| 5      | Console       | 모든 범주                          | 정보       |
| 6      | 모든 공급자 | 모든 범주                          | 디버그             |
| 7      | 모든 공급자 | 시스템                                  | 디버그             |
| 8      | 디버그         | Microsoft                               | Trace             |

`ILogger` 개체를 만들 때 `ILoggerFactory` 개체는 공급자마다 해당 로거에 적용할 단일 규칙을 선택합니다. `ILogger` 인스턴스에서 작성된 모든 메시지는 선택한 규칙에 따라 필터링됩니다. 사용 가능한 규칙 중에서 각 공급자 및 범주 쌍에 적용 가능한 가장 구체적인 규칙이 선택됩니다.

지정된 범주에 대한 `ILogger`가 생성되면 다음 알고리즘이 각 공급자에 사용됩니다.

* 공급자 또는 공급자의 별칭과 일치하는 모든 규칙을 선택합니다. 일치하는 규칙이 없는 경우 빈 공급자가 있는 모든 규칙을 선택합니다.
* 앞 단계의 결과에서 일치하는 범주 접두사가 가장 긴 규칙을 선택합니다. 일치하는 규칙이 없는 경우 범주를 지정하지 않는 규칙을 모두 선택합니다.
* 여러 규칙을 선택하는 경우 **마지막** 규칙을 사용합니다.
* 규칙을 선택하지 않는 경우 `MinimumLevel`을 사용합니다.

앞의 규칙 목록을 사용하여 “Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine” 범주에 대한 `ILogger` 개체를 만든다고 가정합니다.

* 디버그 공급자에게는 규칙 1, 6, 8이 적용됩니다. 규칙 8이 가장 구체적이므로 선택됩니다.
* 콘솔 공급자에게는 규칙 3, 4, 5, 6이 적용됩니다. 규칙 3이 가장 구체적입니다.

결과 `ILogger` 인스턴스는 `Trace` 수준 이상의 로그를 디버그 공급자에게 보냅니다. `Debug` 수준 이상의 로그는 콘솔 공급자에게 전송됩니다.

### <a name="provider-aliases"></a>공급자 별칭

각 공급자는 정규화된 형식 이름 대신 구성에서 사용할 수 있는 *별칭*을 정의합니다.  기본 공급자의 경우 다음 별칭을 사용합니다.

* Console
* Debug
* EventSource
* EventLog
* TraceSource
* AzureAppServicesFile
* AzureAppServicesBlob
* ApplicationInsights

### <a name="default-minimum-level"></a>기본 최소 수준

특정 공급자 및 범주에 대한 구성 또는 코드의 규칙이 적용되지 않는 경우에만 적용되는 최소 수준 설정이 있습니다. 다음 예제는 최소 수준을 설정하는 방법을 보여 줍니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

최소 수준을 명시적으로 설정하지 않으면 `Trace` 및 `Debug` 로그를 무시하는 `Information`이 기본값으로 설정됩니다.

### <a name="filter-functions"></a>필터 함수

필터 함수는 구성 또는 코드를 통해 규칙이 할당되지 않은 모든 공급자와 범주에 대해 호출됩니다. 함수의 코드는 공급자 형식, 범주 및 로그 수준에 액세스할 수 있습니다. 예를 들어:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a>시스템 범주 및 수준

다음은 ASP.NET Core 및 Entity Framework Core에서 사용되는 몇 가지 범주로, 예상되는 로그에 대한 참고 사항입니다.

| 범주                            | 참고 |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | 일반 ASP.NET Core 진단. |
| Microsoft.AspNetCore.DataProtection | 고려되고, 발견되고, 사용된 키. |
| Microsoft.AspNetCore.HostFiltering  | 허용되는 호스트. |
| Microsoft.AspNetCore.Hosting        | HTTP 요청을 완료하는 데 걸린 시간과 시작 시간. 로드된 호스팅 시작 어셈블리. |
| Microsoft.AspNetCore.Mvc            | MVC 및 Razor 진단. 모델 바인딩, 필터 실행, 뷰 컴파일 작업 선택. |
| Microsoft.AspNetCore.Routing        | 경로 일치 정보. |
| Microsoft.AspNetCore.Server         | 연결 시작, 중지 및 활성 응답 유지. HTTPS 인증서 정보. |
| Microsoft.AspNetCore.StaticFiles    | 제공된 파일. |
| Microsoft.EntityFrameworkCore       | 일반 Entity Framework Core 진단. 데이터베이스 작업 및 구성, 변경 내용 검색, 마이그레이션. |

## <a name="log-scopes"></a>로그 범위

 *범위*는 논리적 작업 집합을 그룹화할 수 있습니다. 이 그룹화는 집합의 일부로 생성된 각 로그에 동일한 데이터를 연결하는 데 사용될 수 있습니다. 예를 들어 트랜잭션 처리의 일부로 생성되는 모든 로그에는 트랜잭션 ID가 포함될 수 있습니다.

범위는 <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> 메서드에서 반환하는 `IDisposable` 형식이며 삭제될 때까지 유지됩니다. `using` 블록에 로거 호출을 래핑하여 범위를 사용합니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

다음은 콘솔 공급자에 대한 범위를 사용하도록 설정하는 코드입니다.

*Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> 범위 기반 로깅을 사용하려면 `IncludeScopes` 콘솔 로거 옵션을 구성해야 합니다.
>
> 구성에 대한 자세한 내용은 [구성](#configuration) 섹션을 참조하세요.

각 로그 메시지는 범위 정보를 포함합니다.

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>기본 로깅 공급자

ASP.NET Core는 다음 공급자를 제공합니다.

* [콘솔](#console-provider)
* [디버그](#debug-provider)
* [EventSource](#event-source-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

ASP.NET Core 모듈을 사용한 표준 출력(stdout) 및 디버그 로깅에 대한 자세한 내용은 <xref:test/troubleshoot-azure-iis> 및 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>을 참조하세요.

### <a name="console-provider"></a>콘솔 공급자

[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 공급자 패키지는 콘솔에 로그 출력을 보냅니다. 

```csharp
logging.AddConsole();
```

콘솔 로깅 출력을 보려면 프로젝트 폴더에서 명령 프롬프트를 열고 다음 명령을 실행합니다.

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a>디버그 공급자

[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 공급자 패키지는 [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) 클래스(`Debug.WriteLine` 메서드 호출)를 사용하여 로그 출력을 씁니다.

Linux에서 이 공급자는 */var/log/message*에 로그를 씁니다.

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>이벤트 원본 공급자

[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) 공급자 패키지는 `Microsoft-Extensions-Logging` 이름을 사용하여 플랫폼 간 이벤트 원본에 씁니다. Windows에서 공급자는 [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)를 사용합니다.

```csharp
logging.AddEventSourceLogger();
```

호스트를 빌드하기 위해 `CreateDefaultBuilder`가 호출되면 이벤트 원본 공급자가 자동으로 추가됩니다.

[PerfView 유틸리티](https://github.com/Microsoft/perfview)를 사용하여 로그를 수집하고 볼 수 있습니다. ETW 로그를 보는 다른 도구도 있지만, PerfView는 ASP.NET Core에서 내보내는 ETW 이벤트를 처리하기에 가장 좋은 환경을 제공합니다.

이 공급자가 기록한 이벤트를 수집하도록 PerfView를 구성하려면 **추가 공급자** 목록에 `*Microsoft-Extensions-Logging` 문자열을 추가합니다. (문자열의 시작 부분에 별표를 누락하지 마세요.)

![Perfview 추가 공급자](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Windows EventLog 공급자

[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 공급자 패키지는 Windows 이벤트 로그에 로그 출력을 보냅니다.

```csharp
logging.AddEventLog();
```

[AddEventLog 오버로드](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)를 사용하여 <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>를 전달할 수 있습니다. `null`이거나 지정하지 않으면 다음 기본 설정이 사용됩니다.

* `LogName`: “Application”
* `SourceName`: “.NET Runtime”
* `MachineName`: 로컬 머신 이름이 사용됩니다.

[경고 수준 이상](#log-level)의 이벤트가 기록됩니다. 다음 예제에서는 이벤트 로그 기본 로그 수준을 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>으로 설정합니다.

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

### <a name="tracesource-provider"></a>TraceSource 공급자

[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) 공급자 패키지는 <xref:System.Diagnostics.TraceSource> 라이브러리 및 공급자를 사용합니다.

```csharp
logging.AddTraceSource(sourceSwitchName);
```

[AddTraceSource 오버로드](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions)를 사용하여 원본 스위치 및 추적 수신기를 전달할 수 있습니다.

이 공급자를 사용하려면 앱이 .NET Framework(.NET Core가 아닌)에서 실행되어야 합니다. 공급자는 샘플 앱에 사용된 <xref:System.Diagnostics.TextWriterTraceListener>와 같은 다양한 [수신기](/dotnet/framework/debug-trace-profile/trace-listeners)로 메시지를 라우팅할 수 있습니다.

### <a name="azure-app-service-provider"></a>Azure App Service 공급자

[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 공급자 패키지는 Azure App Service 앱의 파일 시스템과 Azure Storage 계정의 [Blob 스토리지](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)에 텍스트 파일을 기록합니다.

```csharp
logging.AddAzureWebAppDiagnostics();
```

이 공급자 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있지 않습니다. .NET Framework를 대상으로 지정하거나 `Microsoft.AspNetCore.App` 메타패키지를 참조하는 경우 공급자 패키지를 프로젝트에 추가합니다. 

<xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> 오버로드로 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>를 전달할 수 있습니다. 설정 개체는 로깅 출력 템플릿, BLOB 이름, 파일 크기 제한과 같은 기본 설정을 재정의할 수 있습니다. (*출력 템플릿*은 `ILogger` 메서드를 호출과 함께 제공되는 것 이외에 모든 로그에 적용되는 메시지 템플릿입니다.)

App Service 앱에 배포할 때 응용 프로그램은 Azure 포털 **App Service** 페이지의 [App Service 로그](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) 섹션에 있는 설정을 따릅니다. 다음 설정이 업데이트되면 앱을 다시 시작하거나 재배포하지 않아도 변경 내용이 즉시 적용됩니다.

* **애플리케이션 로깅(파일 시스템)**
* **애플리케이션 로깅(Blob)**

로그 파일의 기본 위치는 *D:\\home\\LogFiles\\Application* 폴더이며, 기본 파일 이름은 *diagnostics-yyyymmdd.txt*입니다. 기본 파일 크기 제한은 10MB이고, 보존되는 기본 최대 파일 수는 2입니다. 기본 BLOB 이름은 *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*입니다.

공급자는 프로젝트가 Azure 환경에서 실행되는 경우에만 작동합니다. 프로젝트를 로컬로 실행하는 경우에는 아무 영향도 없습니다&mdash;로컬 파일 또는 로컬 개발 스토리지에 Blob을 기록하지 않습니다.

#### <a name="azure-log-streaming"></a>Azure 로그 스트리밍

Azure 로그 스트리밍을 통해 로그 작업을 실시간으로 볼 수 있습니다.

* 앱 서버
* 웹 서버
* 실패한 요청 추적

Azure 로그 스트리밍을 구성하려면:

* 앱의 포털 페이지에서 **App Service 로그** 페이지로 이동합니다.
* **애플리케이션 로깅(파일 시스템)** 을 **On**으로 설정합니다.
* 로그 **수준**을 선택합니다. 이 설정은 앱의 다른 로깅 공급자가 아닌 Azure 로그 스트리밍에만 적용됩니다.

**로그 스트림** 페이지로 이동하여 앱 메시지를 봅니다. 앱이 `ILogger` 인터페이스를 통해 기록한 것입니다.

### <a name="azure-application-insights-trace-logging"></a>Azure Application Insights 추적 로깅

[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) 공급자 패키지는 Azure Application Insights에 로그를 기록합니다. Application Insights는 웹앱을 모니터링하고 원격 분석 데이터를 쿼리 및 분석하기 위한 도구를 제공하는 서비스입니다. 이 공급자를 사용하는 경우 Application Insights 도구를 사용하여 로그를 쿼리 및 분석할 수 있습니다.

로깅 공급자는 ASP.NET Core에 대한 모든 사용 가능한 원격 분석을 제공하는 패키지인 [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)의 종속성으로 포함됩니다. 이 패키지를 사용하는 경우 공급자 패키지를 설치할 필요가 없습니다.

ASP.NET 4.x용으로 제공되는 [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) 패키지&mdash;를 사용하지 마세요.

자세한 내용은 다음 자료를 참조하세요.

* [Application Insights 개요](/azure/application-insights/app-insights-overview)
* [ASP.NET Core 애플리케이션용 Application Insights](/azure/azure-monitor/app/asp-net-core) - 로깅과 함께 전체 범위 Application Insights 원격 분석을 구현하려면 여기에서 시작합니다.
* [.NET Core ILogger 로그용 ApplicationInsightsLoggerProvider](/azure/azure-monitor/app/ilogger) - 나머지 Application Insights 원격 분석 없이 로깅 공급자를 구현하려면 여기에서 시작합니다.
* [Application Insights 로깅 어댑터](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).
* [Application Insights SDK 설치, 구성 및 초기화](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn 사이트의 대화형 자습서입니다.

## <a name="third-party-logging-providers"></a>타사 로깅 공급자

ASP.NET Core와 호환되는 타사 로깅 프레임워크는 다음과 같습니다.

* [elmah.io](https://elmah.io/)([GitHub 리포지토리](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html)([GitHub 리포지토리](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/)([GitHub 리포지토리](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/)([GitHub 리포지토리](https://github.com/catalingavan/KissLog-net))
* [Log4Net](https://logging.apache.org/log4net/)([GitHub 리포지토리](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/)([GitHub 리포지토리](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/)([GitHub 리포지토리](https://github.com/NLog/NLog.Extensions.Logging))
* [Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/)([GitHub 리포지토리](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging)([Github 리포지토리](https://github.com/googleapis/google-cloud-dotnet))

일부 타사 프레임워크는 [구조적 로깅이라고 하는 의미 체계 로깅](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)을 수행할 수 있습니다.

타사 프레임워크를 사용하는 방법은 다음과 같이 기본 공급자 중 하나를 사용하는 방법과 비슷합니다.

1. 프로젝트에 NuGet 패키지를 추가합니다.
1. 로깅 프레임워크에서 제공하는 `ILoggerFactory` 확장 메서드를 호출합니다.

자세한 내용은 각 공급자의 설명서를 참조하세요. 타사 로깅 공급자는 Microsoft에서 지원되지 않습니다.

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
