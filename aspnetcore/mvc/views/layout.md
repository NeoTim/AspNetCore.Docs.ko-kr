---
title: ASP.NET Core의 레이아웃
author: ardalis
description: ASP.NET Core 앱에서 뷰를 렌더링하기 전에 일반적인 레이아웃을 사용하고, 지시문을 공유하고, 공용 코드를 실행하는 방법을 알아봅니다.
ms.author: riande
ms.date: 07/30/2019
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
uid: mvc/views/layout
ms.openlocfilehash: 308e567e0480f83972ab7a55c7b957af83a164fd
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630694"
---
# <a name="layout-in-aspnet-core"></a>ASP.NET Core의 레이아웃

작성자: [Steve Smith](https://ardalis.com/) 및 [Dave Brock](https://twitter.com/daveabrock)

페이지 및 보기는 시각적 개체 및 프로그래밍 요소를 자주 공유합니다. 이 문서에서는 다음을 수행하는 방법을 보여줍니다.

* 일반적인 레이아웃 사용.
* 지시문 공유.
* 페이지 또는 보기를 렌더링하기 전에 일반적인 코드 실행.

이 문서에서는 MVC: 페이지와 뷰를 사용 하 ASP.NET Core 하는 두 가지 방법에 대 한 레이아웃을 설명 Razor 합니다. 이 항목에서는 차이점이 최소화되어 있습니다.

* Razor 페이지는 *pages* 폴더에 있습니다.
* 보기를 사용하는 컨트롤러는 *Views* 폴더의 보기를 사용합니다.

## <a name="what-is-a-layout"></a>레이아웃이란

대부분의 웹앱에는 사용자가 페이지를 탐색하는 동안 일관된 환경을 제공하는 일반적인 레이아웃이 있습니다. 일반적으로 레이아웃에는 앱 헤더, 탐색 또는 메뉴 요소, 바닥글과 같은 공통 사용자 인터페이스 요소가 포함됩니다.

![페이지 레이아웃 예제](layout/_static/page-layout.png)

스크립트 및 스타일시트와 같은 일반적인 HTML 구조도 앱 내의 많은 페이지에서 자주 사용됩니다. 이러한 공유 요소는 모두 응용 프로그램 내에서 사용 되는 보기에서 참조할 수 있는 *레이아웃* 파일에 정의 될 수 있습니다. 레이아웃은 뷰의 중복 코드를 줄입니다.

규칙에 따라, ASP.NET Core 앱의 기본 레이아웃 이름을 *_Layout.cshtml*로 지정합니다. 템플릿을 사용하여 생성된 새로운 ASP.NET Core 프로젝트의 레이아웃 파일:

* Razor Pages: *pages/Shared/_Layout cshtml*

  ![솔루션 탐색기의 Pages 폴더](layout/_static/rp-web-project-views.png)

* 보기를 사용하는 컨트롤러: *Views/Shared/_Layout.cshtml*

  ![솔루션 탐색기의 Views 폴더](layout/_static/mvc-web-project-views.png)

이 레이아웃은 앱의 뷰에 대한 최상위 수준 템플릿을 정의합니다. 앱에는 레이아웃이 필요하지 않습니다. 앱은 각 뷰에서 다른 레이아웃을 지정하여 두 개 이상의 레이아웃을 정의할 수 있습니다.

다음 코드는 컨트롤러 및 보기를 사용하여 만든 템플릿 프로젝트의 레이아웃 파일을 보여줍니다.

[!code-cshtml[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a>레이아웃 지정

Razor 뷰에는 `Layout` 속성이 있습니다. 이 속성을 설정하여 레이아웃을 지정하는 개별 뷰:

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

지정된 레이아웃은 전체 경로(예: */Pages/Shared/_Layout.cshtml* 또는 */Views/Shared/_Layout.cshtml*) 또는 부분적인 이름(예: `_Layout`)을 사용할 수 있습니다. 부분 이름이 제공 되 면 Razor 뷰 엔진은 표준 검색 프로세스를 사용 하 여 레이아웃 파일을 검색 합니다. 처리기 메서드(또는 컨트롤러)가 있는 폴더가 먼저 검색된 후 *공유* 폴더가 검색됩니다. 이 검색 프로세스는 [부분 뷰](xref:mvc/views/partial#partial-view-discovery)를 검색하는 데 사용된 프로세스와 동일합니다.

기본적으로 모든 레이아웃에서 `RenderBody`를 호출해야 합니다. `RenderBody` 호출이 배치될 때마다 뷰의 내용이 렌더링됩니다.

<a name="layout-sections-label"></a>
<!-- https://stackoverflow.com/questions/23327578 -->
### <a name="sections"></a>섹션

레이아웃은 `RenderSection`을 호출하여 필요에 따라 하나 이상의 *섹션*을 참조합니다. 섹션에서는 특정 페이지 요소를 배치할 위치를 구성하는 방법을 제공합니다. `RenderSection` 호출 때마다 섹션이 필수 또는 옵션인지 여부를 지정할 수 있습니다.

```html
<script type="text/javascript" src="~/scripts/global.js"></script>

@RenderSection("Scripts", required: false)
```

필수 섹션이 없는 경우 예외가 throw됩니다. 개별 뷰는 구문을 사용 하 여 섹션 내에서 렌더링할 콘텐츠를 지정 합니다 `@section` Razor . 페이지 또는 보기에서 섹션을 정의하는 경우 렌더링되어야 합니다(그렇지 않은 경우 오류 발생).

`@section`페이지 보기의 예제 정의는 Razor 다음과 같습니다.

```html
@section Scripts {
     <script type="text/javascript" src="~/scripts/main.js"></script>
}
```

이전 코드에서는 *scripts/main.js*가 페이지 또는 보기의 `scripts` 섹션에 추가됩니다. 동일한 앱의 다른 페이지 또는 보기는 이 스크립트가 필요하지 않을 수 있으며, 스크립트 섹션을 정의하지 않습니다.

다음 태그는 [부분 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)를 사용하여 *_ValidationScriptsPartial.cshtml*을 렌더링합니다.

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

이전 태그가 [스 캐 폴딩 Identity ](xref:security/authentication/scaffold-identity)에 의해 생성 되었습니다.

페이지 또는 보기에 정의된 섹션은 즉시 레이아웃 페이지에서만 사용할 수 있습니다. 이들은 부분 뷰, 뷰 구성 요소 또는 뷰 시스템의 다른 부분에서 참조할 수 없습니다.

### <a name="ignoring-sections"></a>섹션 무시

기본적으로 콘텐츠 페이지 본문 및 모든 섹션은 레이아웃 페이지에서 모두 렌더링되어야 합니다. Razor뷰 엔진은 본문과 각 섹션이 렌더링 되었는지 여부를 추적 하 여이를 적용 합니다.

뷰 엔진이 본문 또는 섹션을 무시하도록 지시하려면 `IgnoreBody` 및 `IgnoreSection` 메서드를 호출합니다.

페이지의 본문 및 모든 섹션은 Razor 렌더링 되거나 무시 되어야 합니다.

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a>공유 지시문 가져오기

뷰 및 페이지는 Razor 지시문을 사용 하 여 네임 스페이스를 가져오고 [종속성 주입](dependency-injection.md)을 사용할 수 있습니다. 여러 보기에서 공유하는 지시문을 공용 *_ViewImports.cshtml* 파일에 지정할 수 있습니다. `_ViewImports` 파일은 다음 지시문을 지원합니다.

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

이 파일은 Razor 함수 및 섹션 정의와 같은 다른 기능을 지원 하지 않습니다.

샘플 `_ViewImports.cshtml` 파일:

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

ASP.NET Core MVC 앱에 대한 *_ViewImports.cshtml* 파일은 일반적으로 *Pages*(또는 *Views*) 폴더에 배치됩니다. *_ViewImports.cshtml* 파일은 모든 폴더 내에 배치할 수 있으며, 이 경우 해당 폴더 및 해당 하위 폴더 내의 페이지 또는 보기에만 적용됩니다. `_ViewImports` 파일은 루트 수준부터 처리된 후 각 폴더의 페이지 또는 보기 자체의 위치까지 처리됩니다. 루트 수준에 지정된 `_ViewImports` 설정은 폴더 수준에서 재정의될 수 있습니다.

예를 들어 다음을 가정해 보세요.

* 루트 수준 *_ViewImports.cshtml* 파일에 `@model MyModel1` 및 `@addTagHelper *, MyTagHelper1`이 포함되어 있습니다.
* 하위 폴더 *_ViewImports.cshtml* 파일에 `@model MyModel2` 및 `@addTagHelper *, MyTagHelper2`이 포함되어 있습니다.

하위 폴더의 페이지 및 보기는 태그 도우미와 `MyModel2` 모델에 액세스할 수 있습니다.

파일 계층 구조에 *_ViewImports.cshtml* 파일이 여러 개 있을 경우 지시문의 결합된 동작은 다음과 같습니다.

* `@addTagHelper`, `@removeTagHelper`: 순서대로 모두 실행
* `@tagHelperPrefix`: 뷰에 가장 가까운 것이 다른 것보다 우선함
* `@model`: 뷰에 가장 가까운 것이 다른 것보다 우선함
* `@inherits`: 뷰에 가장 가까운 것이 다른 것보다 우선함
* `@using`: 모두 포함됨. 중복 항목은 무시됨
* `@inject`: 각 속성에 대해 뷰에 가장 가까운 것이 같은 속성 이름의 다른 것보다 우선함

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a>각 뷰 이전에 코드 실행

*_ViewStart.cshtml* 파일에 각 보기 또는 페이지를 배치하기 전에 실행되어야 하는 코드. 규칙에 따라, *_ViewStart.cshtml* 파일은 *Pages*(또는 *Views*) 폴더에 있습니다. *_ViewStart.cshtml*에 나열된 문은 모든 전체 뷰(레이아웃 및 부분 뷰가 아님) 이전에 실행됩니다. [ViewImports.cshtml](xref:mvc/views/layout#viewimports)처럼 *_ViewStart.cshtml*은 계층적입니다. *_ViewStart.cshtml* 파일이 보기 또는 페이지 폴더에 정의된 경우 *Pages*(또는 *Views*) 폴더의 루트에 정의된 항목 뒤에 실행됩니다(있는 경우).

샘플 *_ViewStart.cshtml* 파일:

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

위의 파일은 모든 뷰가 *_Layout.cshtml* 레이아웃을 사용하도록 지정합니다.

*_ViewStart.cshtml* 및 *_ViewImports.cshtml*은 일반적으로 */Pages/Shared*(또는 */Views/Shared*) 폴더에 배치되지 **않습니다**. 이러한 파일의 앱 수준 버전은 */Pages*(또는 */Views*) 폴더에 직접 배치해야 합니다.
