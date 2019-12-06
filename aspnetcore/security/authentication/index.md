---
title: ASP.NET Core 인증 개요
author: mjrousos
description: ASP.NET Core의 인증에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 11/22/2019
uid: security/authentication/index
ms.openlocfilehash: 5e6c875188831c468bc6ca52ce71c5961b43573c
ms.sourcegitcommit: 0dd224b2b7efca1fda0041b5c3f45080327033f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74681364"
---
# <a name="overview-of-aspnet-core-authentication"></a>ASP.NET Core 인증 개요

[Mike Rousos](https://github.com/mjrousos) 작성

인증은 사용자 ID를 확인하는 프로세스입니다. [권한 부여](xref:security/authorization/introduction)는 사용자에게 리소스에 대한 액세스 권한이 있는지를 확인하는 프로세스입니다. ASP.NET Core에서는 인증이 인증 [미들웨어](xref:fundamentals/middleware/index)에서 사용되는 `IAuthenticationService`을(를) 사용하여 처리됩니다. 인증 서비스는 등록된 인증 처리기를 사용하여 인증 관련 작업을 완료합니다. 인증 관련 작업의 예는 다음과 같습니다.

* 사용자 인증.
* 인증되지 않은 사용자가 제한된 리소스에 액세스하려고 할 때 응답합니다.

등록된 인증 처리기와 해당 구성 옵션을 "체계"라고 합니다.

인증 체계는 `Startup.ConfigureServices`에 인증 서비스를 등록하여 지정됩니다.

* `services.AddAuthentication`(예: `AddJwtBearer` 또는 `AddCookie`)를 호출한 후에 스키마별 확장 메서드를 호출합니다. 이 확장 메서드는 [AuthenticationBuilder. AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*)을 사용하여 적절한 설정으로 체계를 등록합니다.
* 드물게 [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*)을 직접 호출합니다.

예를 들어, 다음 코드는 쿠키 및 JWT 전달자 인증 체계의 인증 서비스와 처리기를 등록합니다.

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

`AddAuthentication` 매개 변수 `JwtBearerDefaults.AuthenticationScheme`는 특정 체계가 요청되지 않은 경우 기본적으로 사용할 체계의 이름입니다.

여러 스키마를 사용하는 경우, 권한 부여 정책(또는 권한 부여 특성)은 사용자를 인증하기 위해 사용해야 하는 [인증 체계(또는 체계)를 지정](xref:security/authorization/limitingidentitybyscheme)할 수 있습니다. 위의 예제에서, 쿠키 인증 체계는 이름을 지정하여 사용할 수 있습니다(`AddCookie`를 호출할 때 다른 이름을 제공할 수 있지만 기본적으로 `CookieAuthenticationDefaults.AuthenticationScheme`).

경우에 따라, `AddAuthentication`에 대한 호출은 다른 확장 메서드에서 자동으로 수행됩니다. 예를 들어, [ASP.NET Core Identity](xref:security/authentication/identity)를 사용하는 경우 `AddAuthentication`이(가) 내부적으로 호출됩니다.

인증 미들웨어는 앱의 `IApplicationBuilder`에서 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 확장 메서드를 호출하여 `Startup.Configure`에 추가됩니다. `UseAuthentication`을(를) 호출하면 이전에 등록된 인증 체계를 사용하는 미들웨어가 등록됩니다. 인증되는 사용자에 따라 달라지는 미들웨어 이전에 `UseAuthentication`을(를) 호출합니다. 엔드포인트 라우팅을 사용하는 경우 `UseAuthentication`에 대한 호출이 다음과 같이 이동해야 합니다.

* `UseRouting` 후에 이동하여 인증 결정에 경로 정보를 사용할 수 있습니다.
* `UseEndpoints` 전에 이동하여 사용자가 엔드포인트에 액세스하기 전에 인증됩니다.

## <a name="authentication-concepts"></a>인증 개념

### <a name="authentication-scheme"></a>인증 체계

인증 체계는 다음에 해당하는 이름입니다.

* 인증 처리기.
* 처리기의 특정 인스턴스를 구성하기 위한 옵션입니다.

체계는 연결된 처리기의 인증, 챌린지 및 금지 동작을 참조하기 위한 메커니즘으로 유용합니다. 예를 들어, 권한 부여 정책은 사용자를 인증하는 데 사용해야 하는 권한 부여 체계(또는 체계)를 이름으로 지정할 수 있습니다. 인증을 구성할 때 기본 인증 체계를 지정하는 것이 일반적입니다. 리소스에서 특정 체계를 요청하지 않는 한, 기본 체계가 사용됩니다. 또한 다음과 같은 작업을 수행할 수 있습니다.

* 인증, 챌린지 및 금지 작업에 사용할 다른 기본 체계를 지정합니다.
* [정책 체계](xref:security/authentication/policyschemes)를 사용하여 여러 스키마를 하나로 결합합니다.

### <a name="authentication-handler"></a>인증 처리기

인증 처리기:

* 체계의 동작을 구현하는 형식입니다.
* <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> 또는 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>에서 파생됩니다.
* 사용자를 인증해야 하는 기본 책임이 있습니다.

인증 체계의 구성 및 들어오는 요청 컨텍스트를 기반으로 하는 인증 처리기:

* 인증에 성공하면 사용자 ID를 나타내는 <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> 개체를 생성합니다.
* 인증에 실패하면 '결과 없음' 또는 '실패'를 반환합니다.
* 사용자가 리소스에 액세스하려는 경우에 챌린지 및 금지 작업에 대한 메서드를 사용합니다.
  * 액세스 권한이 없습니다(금지).
  * 인증되지 않았습니다(챌린지).

### <a name="authenticate"></a>인증

인증 체계의 인증 작업은 요청 컨텍스트를 기반으로 사용자의 id를 구성합니다. 인증에 성공했는지와 그런 경우 인증 티켓의 사용자 ID를 나타내는 <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult>을(를) 반환합니다. HttpContext.AuthenticateAsync를 참조하세요. 인증 예는 다음과 같습니다.

* 쿠키에서 사용자 ID를 생성하는 쿠키 인증 체계입니다.
* JWT 전달자 체계는 JWT 전달자 토큰을 역직렬화하고 유효성을 검사하여 사용자 ID를 생성합니다.

### <a name="challenge"></a>과제

인증 챌린지는 인증되지 않은 사용자가 인증을 요구하는 엔드포인트를 요청하는 경우 권한 부여를 수행하여 호출됩니다. 인증 챌린지를 발행합니다(예를 들어, 익명 사용자가 제한된 리소스를 요청하거나 로그인 링크를 클릭하는 경우). 권한 부여는 지정된 인증 체계를 사용하여 챌린지를 호출하거나, 기본값(지정되지 않은 경우)을 호출합니다. HttpContext.ChallengeAsync를 참조하세요. 인증 챌린지 예는 다음과 같습니다.

* 사용자를 로그인 페이지로 리디렉션하는 쿠키 인증 체계입니다.
* `www-authenticate: bearer` 헤더를 사용하여 401 결과를 반환하는 JWT 전달자 체계입니다.

챌린지 작업을 통해 요청된 리소스에 액세스하는 데 사용할 인증 메커니즘을 확인할 수 있습니다.

### <a name="forbid"></a>금지

인증된 사용자가 액세스를 허용하지 않는 리소스에 액세스를 시도할 때 인증 체계의 금지 작업이 권한 부여에 의해 호출됩니다. HttpContext.ForbidAsync를 참조하세요. 인증 금지 예는 다음과 같습니다.
* 액세스를 사용할 수 없음을 나타내는 페이지로 리디렉션하는 쿠키 인증 체계입니다.
* 403 결과를 반환하는 JWT 전달자 체계입니다.
* 사용자가 리소스에 대한 액세스 권한을 요청할 수 있는 페이지로 리디렉션하는 사용자 지정 인증 체계입니다.

금지 작업을 통해 다음을 확인할 수 있습니다.

* 인증되었습니다.
* 요청된 리소스에 액세스할 수 없습니다.

챌린지와 금지 간의 차이점에 대해서는 다음 링크를 참조하세요.

* [작업 리소스 처리기를 사용하는 챌린지 및 금지](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler)
* [챌린지와 금지 간의 차이](xref:security/authorization/secure-data#challenge).

## <a name="additional-resources"></a>추가 자료

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>