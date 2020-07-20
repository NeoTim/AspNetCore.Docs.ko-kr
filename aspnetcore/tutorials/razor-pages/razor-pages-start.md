---
title: '자습서: ASP.NET Core에서 Razor Pages 시작'
author: rick-anderson
description: 이 자습서 시리즈는 ASP.NET Core에서 Razor Pages를 사용하는 방법을 보여 줍니다. 모델을 만들고, Razor Pages에 대한 코드를 생성하고, Entity Framework Core 및 SQL Server를 데이터 액세스에 사용하고, 검색 기능을 추가하고, 입력 유효성 검사를 추가하고, 마이그레이션을 사용하여 모델을 업데이트하는 방법을 알아봅니다.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 77b230f14b4eef60d771daf8fe09288a9dd3c69c
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212989"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="09b96-104">자습서: ASP.NET Core에서 Razor Pages 시작</span><span class="sxs-lookup"><span data-stu-id="09b96-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="09b96-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="09b96-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="09b96-106">ASP.NET Core Razor Pages 웹앱을 빌드하는 작업의 기본 사항을 설명하는 자습서 시리즈 중 첫 번째입니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="09b96-107">시리즈가 끝나면 영화의 데이터베이스를 관리하는 앱이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="09b96-108">이 자습서에서는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="09b96-109">Razor Pages 웹앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="09b96-110">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-110">Run the app.</span></span>
> * <span data-ttu-id="09b96-111">프로젝트 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-111">Examine the project files.</span></span>

<span data-ttu-id="09b96-112">이 자습서의 내용을 마치면 이후의 자습서에서 빌드할 Razor Pages 웹앱을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="09b96-114">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="09b96-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="09b96-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="09b96-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="09b96-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="09b96-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="09b96-117">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="09b96-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="09b96-118">Razor Pages 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="09b96-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="09b96-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="09b96-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="09b96-120">Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="09b96-121">새 ASP.NET Core 웹 애플리케이션을 만들고 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="09b96-122">![새 ASP.NET Core 웹 애플리케이션](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="09b96-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="09b96-123">프로젝트 이름을 **RazorPagesMovie**로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="09b96-124">코드를 복사하여 붙여 넣을 때 네임스페이스가 일치하도록 프로젝트 이름을 *RazorPagesMovie*로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="09b96-125">![새 ASP.NET Core 웹 애플리케이션](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="09b96-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="09b96-126">드롭다운에서 **ASP.NET Core 3.1**을 선택하고 **웹 애플리케이션**을 선택한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="09b96-128">다음 시작 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-128">The following starter project is created:</span></span>

  ![솔루션 탐색기](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="09b96-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="09b96-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="09b96-131">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="09b96-132">프로젝트를 포함하는 디렉터리(`cd`)로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="09b96-133">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="09b96-134">`dotnet new` 명령은 *RazorPagesMovie* 폴더에서 새 Razor Pages 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="09b96-135">`code` 명령은 Visual Studio Code의 현재 인스턴스에서 *RazorPagesMovie* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="09b96-136">상태 표시줄의 OmniSharp 불꽃 아이콘이 녹색으로 바뀐 후 **빌드 및 디버그에 필요한 자산이 ‘RazorPagesMovie’에서 누락되었습니다. Add them?** 라는 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="09b96-137">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-137">Select **Yes**.</span></span>

  <span data-ttu-id="09b96-138">*launch.json* 및 *tasks.json* 파일을 포함한 *.vscode* 디렉터리가 프로젝트의 루트 디렉터리에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="09b96-139">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="09b96-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="09b96-140">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-140">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="09b96-142">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **웹 애플리케이션** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="09b96-143">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **웹 애플리케이션** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS 웹앱 템플릿 선택](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="09b96-145">**새 웹 애플리케이션 구성** 대화 상자에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-145">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="09b96-146">**인증**이 **인증 없음**으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-146">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="09b96-147">**대상 프레임워크**를 선택하는 옵션이 제공되는 경우 최신 3.x 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-147">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="09b96-148">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-148">Select **Next**.</span></span>

* <span data-ttu-id="09b96-149">프로젝트 이름을 **RazorPagesMovie**로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-149">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![macOS 프로젝트 이름 지정](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="09b96-151">앱 실행</span><span class="sxs-lookup"><span data-stu-id="09b96-151">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="09b96-152">프로젝트 파일 검토</span><span class="sxs-lookup"><span data-stu-id="09b96-152">Examine the project files</span></span>

<span data-ttu-id="09b96-153">이후 자습서에서 작업할 주요 프로젝트 폴더 및 파일에 대한 개요는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-153">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="09b96-154">페이지 폴더</span><span class="sxs-lookup"><span data-stu-id="09b96-154">Pages folder</span></span>

<span data-ttu-id="09b96-155">Razor Pages 및 지원 파일이 들어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-155">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="09b96-156">각 Razor 페이지는 파일 쌍입니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-156">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="09b96-157">Razor 구문을 사용하는 C# 코드로 HTML 태그를 포함하는 *.cshtml* 파일.</span><span class="sxs-lookup"><span data-stu-id="09b96-157">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="09b96-158">페이지 이벤트를 처리하는 C# 코드가 포함된 *.cshtml.cs* 파일.</span><span class="sxs-lookup"><span data-stu-id="09b96-158">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="09b96-159">지원 파일에는 밑줄로 시작하는 이름이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-159">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="09b96-160">예를 들어 *_Layout.cshtml* 파일은 모든 페이지에 공통되는 UI 요소를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-160">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="09b96-161">이 파일은 페이지 맨 위에 있는 탐색 메뉴를 설정하고 페이지 맨 아래에 저작권 표시를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-161">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="09b96-162">자세한 내용은 <xref:mvc/views/layout>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="09b96-162">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="09b96-163">wwwroot 폴더</span><span class="sxs-lookup"><span data-stu-id="09b96-163">wwwroot folder</span></span>

<span data-ttu-id="09b96-164">HTML 파일, JavaScript 파일 및 CSS 파일과 같은 정적 파일을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-164">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="09b96-165">자세한 내용은 <xref:fundamentals/static-files>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="09b96-165">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="09b96-166">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="09b96-166">appSettings.json</span></span>

<span data-ttu-id="09b96-167">연결 문자열과 같은 구성 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-167">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="09b96-168">자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="09b96-168">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="09b96-169">Program.cs</span><span class="sxs-lookup"><span data-stu-id="09b96-169">Program.cs</span></span>

<span data-ttu-id="09b96-170">프로그램의 진입점을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-170">Contains the entry point for the program.</span></span> <span data-ttu-id="09b96-171">자세한 내용은 <xref:fundamentals/host/generic-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="09b96-171">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="09b96-172">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="09b96-172">Startup.cs</span></span>

<span data-ttu-id="09b96-173">앱 동작을 구성하는 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-173">Contains code that configures app behavior.</span></span> <span data-ttu-id="09b96-174">자세한 내용은 <xref:fundamentals/startup>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="09b96-174">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="09b96-175">다음 단계</span><span class="sxs-lookup"><span data-stu-id="09b96-175">Next steps</span></span>

<span data-ttu-id="09b96-176">시리즈의 다음 자습서로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-176">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="09b96-177">모델 추가</span><span class="sxs-lookup"><span data-stu-id="09b96-177">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="09b96-178">이 시리즈의 첫 번째 자습서입니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-178">This is the first tutorial of a series.</span></span> <span data-ttu-id="09b96-179">[시리즈](xref:tutorials/razor-pages/index)에서는 ASP.NET Core Razor Pages 웹앱을 빌드하는 작업의 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-179">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="09b96-180">시리즈가 끝나면 영화의 데이터베이스를 관리하는 앱이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-180">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="09b96-181">이 자습서에서는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-181">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="09b96-182">Razor Pages 웹앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-182">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="09b96-183">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-183">Run the app.</span></span>
> * <span data-ttu-id="09b96-184">프로젝트 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-184">Examine the project files.</span></span>

<span data-ttu-id="09b96-185">이 자습서의 내용을 마치면 이후의 자습서에서 빌드할 Razor Pages 웹앱을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-185">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="09b96-187">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="09b96-187">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="09b96-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="09b96-188">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="09b96-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="09b96-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="09b96-190">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="09b96-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="09b96-191">Razor Pages 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="09b96-191">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="09b96-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="09b96-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="09b96-193">Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-193">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="09b96-194">새 ASP.NET Core 웹 애플리케이션을 만들고 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-194">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="09b96-196">프로젝트 이름을 **RazorPagesMovie**로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-196">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="09b96-197">코드를 복사하여 붙여 넣을 때 네임스페이스가 일치하도록 프로젝트 이름을 *RazorPagesMovie*로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-197">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/config.png)

* <span data-ttu-id="09b96-199">드롭다운에서 **ASP.NET Core 2.2**를 선택하고 **웹 애플리케이션**을 선택한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-199">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="09b96-201">다음 시작 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-201">The following starter project is created:</span></span>

  ![솔루션 탐색기](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="09b96-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="09b96-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="09b96-204">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-204">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="09b96-205">프로젝트를 포함하는 디렉터리(`cd`)로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-205">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="09b96-206">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-206">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="09b96-207">`dotnet new` 명령은 *RazorPagesMovie* 폴더에서 새 Razor Pages 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-207">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="09b96-208">`code` 명령은 Visual Studio Code의 현재 인스턴스에서 *RazorPagesMovie* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-208">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="09b96-209">상태 표시줄의 OmniSharp 불꽃 아이콘이 녹색으로 바뀐 후 **빌드 및 디버그에 필요한 자산이 ‘RazorPagesMovie’에서 누락되었습니다. Add them?** 라는 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-209">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="09b96-210">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-210">Select **Yes**.</span></span>

  <span data-ttu-id="09b96-211">*launch.json* 및 *tasks.json* 파일을 포함한 *.vscode* 디렉터리가 프로젝트의 루트 디렉터리에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-211">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="09b96-212">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="09b96-212">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="09b96-213">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-213">Select **File** > **New Solution**.</span></span>

![macOS 새 솔루션](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="09b96-215">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **웹 애플리케이션** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-215">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="09b96-216">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **웹 애플리케이션** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-216">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="09b96-217">**새 웹 애플리케이션 구성** 대화 상자에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-217">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="09b96-218">**인증**이 **인증 없음**으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-218">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="09b96-219">**대상 프레임워크**를 선택하는 옵션이 제공되는 경우 최신 2.x 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-219">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="09b96-220">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-220">Select **Next**.</span></span>

* <span data-ttu-id="09b96-221">프로젝트 이름을 **RazorPagesMovie**로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-221">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="09b96-223">앱 실행</span><span class="sxs-lookup"><span data-stu-id="09b96-223">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="09b96-224">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="09b96-224">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="09b96-225">Ctrl+F5를 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-225">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="09b96-226">Visual Studio가 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-226">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="09b96-227">주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-227">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="09b96-228">`localhost`가 로컬 컴퓨터의 표준 호스트 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-228">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="09b96-229">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-229">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="09b96-230">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-230">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="09b96-231">앱의 홈페이지에서 **승인**을 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-231">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="09b96-232">이 앱은 개인 정보를 추적하지 않지만 유럽 연합의 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하기 위해 필요한 경우 프로젝트 템플릿에는 동의 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-232">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="09b96-234">다음 이미지에서는 추적에 동의한 후에 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-234">The following image shows the app after you give consent to tracking:</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="09b96-236">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="09b96-236">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="09b96-237">**Ctrl-F5** 키를 눌러서 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-237">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="09b96-238">Visual Studio Code가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `http://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-238">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="09b96-239">주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-239">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="09b96-240">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-240">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="09b96-241">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-241">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="09b96-242">앱의 홈페이지에서 **승인**을 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-242">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="09b96-243">이 앱은 개인 정보를 추적하지 않지만 유럽 연합의 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하기 위해 필요한 경우 프로젝트 템플릿에는 동의 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-243">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="09b96-245">다음 이미지에서는 추적에 동의한 후에 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-245">The following image shows the app after you give consent to tracking:</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="09b96-247">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="09b96-247">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="09b96-248">디버거 없이 실행하려면 **Cmd-Opt-F5**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-248">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="09b96-249">Visual Studio가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `http://localhost:5001`으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-249">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="09b96-250">앱의 홈페이지에서 **승인**을 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-250">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="09b96-251">이 앱은 개인 정보를 추적하지 않지만 유럽 연합의 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하기 위해 필요한 경우 프로젝트 템플릿에는 동의 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-251">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="09b96-253">다음 이미지에서는 추적에 동의한 후에 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-253">The following image shows the app after you give consent to tracking:</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="09b96-255">프로젝트 파일 검사</span><span class="sxs-lookup"><span data-stu-id="09b96-255">Examine the project files</span></span>

<span data-ttu-id="09b96-256">이후 자습서에서 작업할 주요 프로젝트 폴더 및 파일에 대한 개요는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-256">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="09b96-257">페이지 폴더</span><span class="sxs-lookup"><span data-stu-id="09b96-257">Pages folder</span></span>

<span data-ttu-id="09b96-258">Razor Pages 및 지원 파일이 들어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-258">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="09b96-259">각 Razor 페이지는 파일 쌍입니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-259">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="09b96-260">Razor 구문을 사용하는 C# 코드로 HTML 태그를 포함하는 *.cshtml* 파일.</span><span class="sxs-lookup"><span data-stu-id="09b96-260">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="09b96-261">페이지 이벤트를 처리하는 C# 코드가 포함된 *.cshtml.cs* 파일.</span><span class="sxs-lookup"><span data-stu-id="09b96-261">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="09b96-262">지원 파일에는 밑줄로 시작하는 이름이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-262">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="09b96-263">예를 들어 *_Layout.cshtml* 파일은 모든 페이지에 공통되는 UI 요소를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-263">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="09b96-264">이 파일은 페이지 맨 위에 있는 탐색 메뉴를 설정하고 페이지 맨 아래에 저작권 표시를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-264">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="09b96-265">자세한 내용은 <xref:mvc/views/layout>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="09b96-265">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="09b96-266">wwwroot 폴더</span><span class="sxs-lookup"><span data-stu-id="09b96-266">wwwroot folder</span></span>

<span data-ttu-id="09b96-267">HTML 파일, JavaScript 파일 및 CSS 파일과 같은 정적 파일을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-267">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="09b96-268">자세한 내용은 <xref:fundamentals/static-files>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="09b96-268">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="09b96-269">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="09b96-269">appSettings.json</span></span>

<span data-ttu-id="09b96-270">연결 문자열과 같은 구성 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-270">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="09b96-271">자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="09b96-271">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="09b96-272">Program.cs</span><span class="sxs-lookup"><span data-stu-id="09b96-272">Program.cs</span></span>

<span data-ttu-id="09b96-273">프로그램의 진입점을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-273">Contains the entry point for the program.</span></span> <span data-ttu-id="09b96-274">자세한 내용은 <xref:fundamentals/host/generic-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="09b96-274">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="09b96-275">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="09b96-275">Startup.cs</span></span>

<span data-ttu-id="09b96-276">쿠키에 대한 동의 필요 여부 등 앱 동작을 구성하는 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-276">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="09b96-277">자세한 내용은 <xref:fundamentals/startup>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="09b96-277">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="09b96-278">추가 자료</span><span class="sxs-lookup"><span data-stu-id="09b96-278">Additional resources</span></span>

* [<span data-ttu-id="09b96-279">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="09b96-279">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="09b96-280">다음 단계</span><span class="sxs-lookup"><span data-stu-id="09b96-280">Next steps</span></span>

<span data-ttu-id="09b96-281">시리즈의 다음 자습서로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="09b96-281">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="09b96-282">모델 추가</span><span class="sxs-lookup"><span data-stu-id="09b96-282">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
