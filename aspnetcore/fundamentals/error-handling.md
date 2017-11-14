---
title: "ASP.NET Core 中的错误处理"
author: ardalis
description: "了解如何处理 ASP.NET Core 应用程序中的错误。"
keywords: "ASP.NET Core，错误处理，异常处理"
ms.author: tdykstra
manager: wpickett
ms.date: 11/30/2016
ms.topic: article
ms.assetid: 4db51023-c8a6-4119-bbbe-3917e272c260
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/error-handling
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: de2ba0ff9ad17c198c06b510ecfb49f808721bdf
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2017
---
# <a name="introduction-to-error-handling-in-aspnet-core"></a>ASP.NET Core 中的错误处理简介

作者：[Steve Smith](https://ardalis.com/) 和 [Tom Dykstra](https://github.com/tdykstra/)

本文涵盖了一些处理 ASP.NET Core 应用中的常见错误的方法。

[查看或下载的示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/sample)（[如何下载](xref:tutorials/index#how-to-download-a-sample)）

## <a name="the-developer-exception-page"></a>开发者模式异常页

如果需要配置一个可以显示异常详细信息的页面，请先安装`Microsoft.AspNetCore.Diagnostics`NuGet 包并且添加以下代码到 Startup 类（[ Startup 类配置方法](startup.md)）:

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_DevExceptionPage&highlight=7)]

`UseDeveloperExceptionPage`需要放在所有你想要捕获异常的中间件声明之前，如`app.UseMvc`。

>[!WARNING]
> 开发者模式异常页仅在**设置为开发环境时**开启。 否则将会导致在生产中的应用程序运行时泄露详细的异常信息。 了解[环境配置](environments.md)。

若要查看开发者模式异常页，可使用`Development`环境来运行应用程序，并在基础 URL 后添加`?throw=true`。 该页面包括几个选项卡，这些选项卡中包含有异常和请求的信息。 第一个选项卡包括异常堆栈跟踪。 

![堆栈跟踪](error-handling/_static/developer-exception-page.png)

下一个`Query`选项卡将显示查询字符串参数信息。

![查询字符串参数](error-handling/_static/developer-exception-page-query.png)

示例请求没有任何 cookie，但如果有的话，它们将出现在 **Cookies** 选项卡。另外最后一个选项卡显示请求的标头（`Headers`）。

![标头](error-handling/_static/developer-exception-page-headers.png)

## <a name="configuring-a-custom-exception-handling-page"></a>配置自定义异常处理页

配置自定义异常处理页是在运行非`Development`环境时的良好做法。

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_DevExceptionPage&highlight=11)]

在 MVC 应用程序中，不要显式限制提供错误处理页的 HTTP 谓词 Attribute（如`HttpGet`）。 使用显式谓词会阻止某些请求访问方法。

```csharp
[Route("/Error")]
public IActionResult Index()
{
    // Handle error here
}
```

## <a name="configuring-status-code-pages"></a>配置状态代码页

默认情况下，你的应用程序将不提供的 HTTP 状态代码 500 （内部服务器错误） 或 404 （未找到） 等的丰富的状态代码页。 你可以在`Configure`方法中配置`StatusCodePagesMiddleware`来进行添加：

```csharp
app.UseStatusCodePages();
```

默认情况下，此中间件提供简单、 纯文本文件的处理程序常见状态代码 （如 404）：

![404 页](error-handling/_static/default-404-status-code.png)

该中间件支持几种不同的扩展方法。 一种的参数采用 lambda 表达式，另一个操作采用`Content-Type`和一个格式字符串。

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_StatusCodePages)]

```csharp
app.UseStatusCodePages("text/plain", "Status code page, status code: {0}");
```

同时也提供有重定向的扩展方法。 一个将 302 状态代码发送到客户端，并其中一个将原始的状态代码返回到客户端，但还会处理程序执行的重定向 URL。

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

```csharp
app.UseStatusCodePagesWithReExecute("/error/{0}");
```

如果你需要禁用某些请求的状态代码页，可以通过以下代码实现：

```csharp
var statusCodePagesFeature = context.Features.Get<IStatusCodePagesFeature>();
if (statusCodePagesFeature != null)
{
  statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>异常处理代码

在异常处理页中书写代码，可能会导致新的异常，所以在生产环境中，仅包含纯静态内容的异常处理页是一个好主意。

此外，需要注意后当响应已发送标头（`Headers`）时，则不能再更改其响应的状态代码，也不能理发任何异常页或处理程序。 响应必须选择完成响应或取消连接。

## <a name="server-exception-handling"></a>服务器异常处理

除了应用程序中的异常处理逻辑之外，托管应用程序的服务器[服务器](servers/index.md)执行一些异常处理。 如果服务器在发送标头之前捕获异常，服务器将发送具有没有正文的 500 内部服务器错误响应。 如果在服务器已发送标头后捕获异常，服务器会关闭连接。 应用程序无法处理的请求将由服务器进行处理。 发生的任何异常都将由服务器进行处理。 任何配置的自定义错误页面或异常处理中间件或筛选器都不会影响此行为。

## <a name="startup-exception-handling"></a>Startup 异常处理

仅在`Hosting`层可以处理应用程序启动期间发生的异常。 你可以通过配置`captureStartupErrors`和`detailedErrors`键，[在启动期间配置主机的错误响应方式](hosting.md#detailed-errors)。

如果主机地址/端口绑定发生错误后，`Hosting`只捕获启动错误及展示它的错误页。如果因任何原因绑定失败，`Hosting`层记录关键异常，dotnet 进程崩溃 (crash)，并且不显示错误页面。

## <a name="aspnet-mvc-error-handling"></a>ASP.NET MVC 错误处理

[MVC](../mvc/index.md)还拥有一些额外的错误处理特色功能，例如如异常筛选器（Exception Filters）和模型验证。

### <a name="exception-filters"></a>异常筛选器

异常筛选器可以是全局配置，也可以在 MVC 程序中在控制器或 Action 上进行单独声明。 异常筛选器处理 Action 或其它筛选器中产生的未捕获异常。 了解有关中的异常筛选器的详细信息[筛选器](../mvc/controllers/filters.md)。

>[!TIP]
> 异常筛选器非常适用于捕获 MVC 操作内出现的异常，但灵活性不如错误处理中间件。 在使用中间件来应用一般情况，并使用异常筛选器单独处理 MVC 中的异常。

### <a name="handling-model-state-errors"></a>处理模型状态（Model State）错误

[模型验证](../mvc/models/validation.md)在每个控制器 Action 调用之前被触发，它负责检查 Action 方法并提供 `ModelState.IsValid`。

某些应用程序会使用统一的约定来处理模型验证错误，这种情况下使用[筛选器](../mvc/controllers/filters.md)可以更好地实现这种通用性。 你需要带着非法模型的状态来测试你的 Action。 查看[控制器的单元测试逻辑](../mvc/controllers/testing.md)了解详情。



