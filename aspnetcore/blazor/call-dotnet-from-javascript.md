---
title: ASP.NET Core Blazor의 JavaScript 함수에서 .NET 메서드 호출
author: guardrex
description: Blazor 앱의 JavaScript 함수에서 .NET 메서드를 호출하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2020
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
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: 2a3685dbc654fb8be3420da73e5a62fe729092e6
ms.sourcegitcommit: ad63e131224f3217bbafff4eb0e6fa1b25294460
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89199695"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-no-locblazor"></a>ASP.NET Core Blazor의 JavaScript 함수에서 .NET 메서드 호출

작성자: [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co) 및 [Luke Latham](https://github.com/guardrex)

Blazor 앱은 .NET 메서드에서 JavaScript 함수를 호출하고 JavaScript 함수에서 .NET 메서드를 호출할 수 있습니다. 이러한 시나리오를 *JavaScript 상호 운용성*(*JS interop*)이라고 합니다.

이 문서에서는 JavaScript에서 .NET 메서드를 호출하는 방법을 설명합니다. .NET에서 JavaScript 함수를 호출하는 방법에 대한 내용은 <xref:blazor/call-javascript-from-dotnet>을 참조하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="static-net-method-call"></a>정적 .NET 메서드 호출

JavaScript에서 정적 .NET 메서드를 호출하려면 `DotNet.invokeMethod` 또는 `DotNet.invokeMethodAsync` 함수를 사용합니다. 호출할 정적 메서드의 식별자, 함수를 포함하는 어셈블리의 이름 및 인수를 전달합니다. 비동기 버전은 Blazor Server 시나리오를 지원하는 데 선호됩니다. .NET 메서드는 공용, 정적이며 [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) 특성이 있어야 합니다. 개방형 제네릭 메서드를 호출하는 것은 현재 지원되지 않습니다.

샘플 앱에는 `int` 배열을 반환하기 위한 C# 메서드가 포함되어 있습니다. [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) 특성이 메서드에 적용됩니다.

`Pages/JsInterop.razor`:

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

클라이언트에 제공된 JavaScript는 C# .Net 메서드를 호출합니다.

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

**`Trigger .NET static method ReturnArrayAsync`** 단추를 선택한 경우 브라우저의 웹 개발자 도구에서 콘솔 출력을 검사합니다.

콘솔 출력은 다음과 같습니다.

```console
Array(4) [ 1, 2, 3, 4 ]
```

네 번째 배열 값은 `ReturnArrayAsync`에서 반환된 배열(`data.push(4);`)로 푸시됩니다.

기본적으로 메서드 식별자는 메서드 이름이지만 [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) 특성 생성자를 사용하여 다른 식별자를 지정할 수 있습니다.

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

클라이언트 쪽 JavaScript 파일에서 다음을 실행합니다.

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).

## <a name="instance-method-call"></a>인스턴스 메서드 호출

JavaScript에서 .NET 인스턴스 메서드를 호출할 수도 있습니다. JavaScript에서 .NET 인스턴스 메서드를 호출하려면 다음을 수행합니다.

* JavaScript에 대한 참조로 .NET 인스턴스를 전달합니다.
  * <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>에 대한 정적 호출을 수행합니다.
  * 인스턴스를 <xref:Microsoft.JSInterop.DotNetObjectReference> 인스턴스로 래핑하고 <xref:Microsoft.JSInterop.DotNetObjectReference> 인스턴스에서 <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A>를 호출합니다. <xref:Microsoft.JSInterop.DotNetObjectReference> 개체를 삭제합니다. 이 섹션의 뒷부분을 참조하세요.
* `invokeMethod` 또는 `invokeMethodAsync` 함수를 사용하여 인스턴스에서 .NET 인스턴스 메서드를 호출합니다. JavaScript에서 다른 .NET 메서드를 호출할 때 .NET 인스턴스를 인수로 전달할 수도 있습니다.

> [!NOTE]
> 샘플 앱은 클라이언트 쪽 콘솔에 메시지를 로깅합니다. 샘플 앱에서 보여 주는 다음 예제에서는 브라우저의 개발자 도구에서 브라우저의 콘솔 출력을 검사합니다.

**`Trigger .NET instance method HelloHelper.SayHello`** 단추를 선택하면 `ExampleJsInterop.CallHelloHelperSayHello`를 호출하고 `Blazor` 이름을 메서드에 전달합니다.

`Pages/JsInterop.razor`:

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JSRuntime);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

`CallHelloHelperSayHello`는 `HelloHelper`의 새 인스턴스를 사용하여 JavaScript 함수 `sayHello`를 호출합니다.

`JsInteropClasses/ExampleJsInterop.cs`:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

이 이름은 `HelloHelper.Name` 속성을 설정하는 `HelloHelper`의 생성자에 전달됩니다. JavaScript 함수 `sayHello`가 실행되면 `HelloHelper.SayHello`는 JavaScript 함수를 통해 콘솔에 기록되는 `Hello, {Name}!` 메시지를 반환합니다.

`JsInteropClasses/HelloHelper.cs`:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

브라우저의 웹 개발자 도구에 있는 콘솔 출력:

```console
Hello, Blazor!
```

메모리 누수를 방지하고 <xref:Microsoft.JSInterop.DotNetObjectReference>를 만드는 구성 요소에서 가비지 수집을 허용하려면 다음 방법 중 하나를 채택합니다.

* <xref:Microsoft.JSInterop.DotNetObjectReference> 인스턴스를 만든 클래스에서 개체를 삭제합니다.

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime jsRuntime;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime jsRuntime)
      {
          this.jsRuntime = jsRuntime;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return jsRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  `ExampleJsInterop` 클래스에 표시된 이전 패턴은 구성 요소에서 구현될 수도 있습니다.

  ```razor
  @page "/JSInteropComponent"
  @using {APP ASSEMBLY}.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JSRuntime

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JSRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```
  
  자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).

* 구성 요소나 클래스가 <xref:Microsoft.JSInterop.DotNetObjectReference>를 삭제하지 않는 경우 `.dispose()`를 호출하여 클라이언트에서 개체를 삭제합니다.

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a>구성 요소 인스턴스 메서드 호출

구성 요소의 .NET 메서드를 호출하려면 다음을 수행합니다.

* `invokeMethod` 또는 `invokeMethodAsync` 함수를 사용하여 구성 요소에 대한 정적 메서드 호출을 수행합니다.
* 구성 요소의 정적 메서드는 인스턴스 메서드 호출을 호출되는 <xref:System.Action>으로 래핑합니다.

> [!NOTE]
> 여러 사용자가 동일한 구성 요소를 동시에 사용할 수 있는 Blazor Server 앱의 경우 도우미 클래스를 사용하여 인스턴스 메서드를 호출합니다.
>
> 자세한 내용은 [구성 요소 인스턴스 메서드 도우미 클래스](#component-instance-method-helper-class) 섹션을 참조하세요.

클라이언트 쪽 JavaScript에서 다음을 실행합니다.

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).

`Pages/JSInteropComponent.razor`:

```razor
@page "/JSInteropComponent"

<p>
    Message: @message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action action;
    private string message = "Select the button.";

    protected override void OnInitialized()
    {
        action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

인스턴스 메서드에 인수를 전달하려면 다음을 수행합니다.

* JS 메서드 호출에 매개 변수를 추가합니다. 다음 예제에서는 이름이 메서드에 전달됩니다. 필요에 따라 목록에 매개 변수를 추가할 수 있습니다.

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).

* 매개 변수에 대한 <xref:System.Action>에 올바른 형식을 제공합니다. C# 메서드에 매개 변수 목록을 제공합니다. 매개 변수(`action.Invoke(name)`)를 사용하여 <xref:System.Action>(`UpdateMessage`)을 호출합니다.

  `Pages/JSInteropComponent.razor`:

  ```razor
  @page "/JSInteropComponent"

  <p>
      Message: @message
  </p>

  <p>
      <button onclick="updateMessageCallerJS('Sarah Jane')">
          Call JS Method
      </button>
  </p>

  @code {
      private static Action<string> action;
      private string message = "Select the button.";

      protected override void OnInitialized()
      {
          action = UpdateMessage;
      }

      private void UpdateMessage(string name)
      {
          message = $"{name}, UpdateMessage Called!";
          StateHasChanged();
      }

      [JSInvokable]
      public static void UpdateMessageCaller(string name)
      {
          action.Invoke(name);
      }
  }
  ```

  **JS 메서드 호출** 단추가 선택된 경우 `message`를 출력합니다.

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a>구성 요소 인스턴스 메서드 도우미 클래스

도우미 클래스는 인스턴스 메서드를 <xref:System.Action>으로 호출하는 데 사용됩니다. 도우미 클래스는 다음과 같은 경우에 유용합니다.

* 동일한 형식의 여러 구성 요소가 동일한 페이지에 렌더링되는 경우
* 여러 사용자가 구성 요소를 동시에 사용할 수 있는 Blazor Server 앱을 사용하는 경우

다음 예제에서는

* `JSInteropExample` 구성 요소에는 몇 가지 `ListItem` 구성 요소가 포함되어 있습니다.
* 각 `ListItem` 구성 요소는 메시지와 단추로 구성됩니다.
* `ListItem` 구성 요소 단추를 선택하면 해당 `ListItem`의 `UpdateMessage` 메서드가 목록 항목 텍스트를 변경하고 단추를 숨깁니다.

`MessageUpdateInvokeHelper.cs`:

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        this.action = action;
    }

    [JSInvokable("{APP ASSEMBLY}")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).

클라이언트 쪽 JavaScript에서 다음을 실행합니다.

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).

`Shared/ListItem.razor`:

```razor
@inject IJSRuntime JsRuntime

<li>
    @message
    <button @onclick="InteropCall" style="display:@display">InteropCall</button>
</li>

@code {
    private string message = "Select one of these list item buttons.";
    private string display = "inline-block";
    private MessageUpdateInvokeHelper messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JsRuntime.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        display = "none";
        StateHasChanged();
    }
}
```

`Pages/JSInteropExample.razor`:

```razor
@page "/JSInteropExample"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>순환 개체 참조 방지

순환 참조를 포함하는 개체는 다음 중 하나에 대해 클라이언트에서 직렬화될 수 없습니다.

* .NET 메서드 호출
* 반환 형식에 순환 참조가 있는 경우 C#에서 JavaScript 메서드 호출

자세한 내용은 다음 문제를 참조하세요.

* [순환 참조가 지원되지 않음, 두 가지 사용(dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [제안: 직렬화할 때 순환 참조를 처리하는 메커니즘 추가(dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/call-javascript-from-dotnet>
* [`InteropComponent.razor` 예제(dotnet/AspNetCore GitHub 리포지토리, 3.1 릴리스 분기)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Blazor Server 앱에서 대량 데이터 전송 수행](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
