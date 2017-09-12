---
title: "ASP.NET 核心中的错误处理"
author: ardalis
description: "说明如何进行处理 ASP.NET Core 应用程序中的错误"
keywords: "ASP.NET 核心，错误处理，异常处理，"
ms.author: tdykstra
manager: wpickett
ms.date: 11/30/2016
ms.topic: article
ms.assetid: 4db51023-c8a6-4119-bbbe-3917e272c260
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/error-handling
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 96a4fed19887a7a9eba08ec70296147f22e41569
ms.sourcegitcommit: 368aabde4de3728a8e5a8c016a2ec61f9c0854bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="introduction-to-error-handling-in-aspnet-core"></a>ASP.NET 核心中的错误处理简介

通过[Steve Smith](https://ardalis.com/)和[Tom Dykstra](https://github.com/tdykstra/)

本文介绍如何处理 ASP.NET Core 应用中的错误的常见 appoaches。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/sample)

## <a name="the-developer-exception-page"></a>开发人员异常页

若要配置应用以显示页面，其中显示有关异常的详细的信息，请安装`Microsoft.AspNetCore.Diagnostics`NuGet 包和将行添加到[启动类中配置方法](startup.md):

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_DevExceptionPage&highlight=7)]

Put`UseDeveloperExceptionPage`之前你想要捕获异常，如的任何中间件`app.UseMvc`。

>[!WARNING]
> 启用开发人员异常页**仅当在开发环境中运行应用程序**。 你不想要在生产中的应用程序运行时公开共享详细的异常信息。 [了解有关配置环境](environments.md)。

若要查看开发人员异常页，可使用环境设置为运行示例应用程序`Development`，并添加`?throw=true`到应用程序的基础 URL。 该页面包括有关异常和请求的信息的几个选项卡。 第一个选项卡包括堆栈跟踪。 

![堆栈跟踪](error-handling/_static/developer-exception-page.png)

如果有的话下, 一步的选项卡将显示查询字符串参数。

![查询字符串参数](error-handling/_static/developer-exception-page-query.png)

此请求没有任何 cookie，但如果它这样做，它们将出现在**Cookie**选项卡。你可以看到在最后一个选项卡中传递的标头。

![标头](error-handling/_static/developer-exception-page-headers.png)

## <a name="configuring-a-custom-exception-handling-page"></a>配置自定义异常处理页

它是配置异常处理程序页后，可以使用应用程序未在运行时一个好办法`Development`环境。

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_DevExceptionPage&highlight=11)]

在 MVC 应用程序，不显式修饰的错误处理程序操作方法使用 HTTP 方法属性，如`HttpGet`。 使用显式谓词无法阻止一些请求访问方法。

```csharp
[Route("/Error")]
public IActionResult Index()
{
    // Handle error here
}
```

## <a name="configuring-status-code-pages"></a>配置状态代码页

默认情况下，你的应用程序将不提供的 HTTP 状态代码 500 （内部服务器错误） 或 404 （未找到） 等的丰富的状态代码页。 你可以配置`StatusCodePagesMiddleware`通过将行添加到`Configure`方法：

```csharp
app.UseStatusCodePages();
```

默认情况下，此中间件添加简单、 纯文本文件的处理程序常见状态代码 （如 404）：

![404 页](error-handling/_static/default-404-status-code.png)

该中间件支持几种不同的扩展方法。 一种采用 lambda 表达式，另一个操作采用内容类型和格式字符串。

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_StatusCodePages)]

```csharp
app.UseStatusCodePages("text/plain", "Status code page, status code: {0}");
```

也有重定向扩展方法。 一个将 302 状态代码发送到客户端，并其中一个将原始的状态代码返回到客户端，但还会处理程序执行的重定向 URL。

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

```csharp
app.UseStatusCodePagesWithReExecute("/error/{0}");
```

如果你需要禁用某些请求的状态代码页，你可以执行操作：

```csharp
var statusCodePagesFeature = context.Features.Get<IStatusCodePagesFeature>();
if (statusCodePagesFeature != null)
{
  statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>异常处理代码

异常处理页的代码可能会引发异常。 它通常是生产错误页，以包含纯静态内容的一个好主意。

此外，注意后已发送响应的标头，则不能更改响应的状态代码，也可以任何异常页或处理程序运行。 响应必须完成或放弃连接。

## <a name="server-exception-handling"></a>服务器异常处理

除了异常处理应用程序中的逻辑[服务器](servers/index.md)承载您的应用程序执行某些异常处理。 如果服务器捕捉异常发送标头之前，服务器将发送具有没有正文的 500 内部服务器错误响应。 如果服务器捕获异常，已发送标头后，服务器会关闭连接。 不处理你的应用程序的请求是由服务器处理的。 出现的任何异常由服务器的异常处理。 任何配置自定义错误页或异常处理中间件或筛选器不会影响此行为。

## <a name="startup-exception-handling"></a>启动异常处理

仅在承载层可以处理应用程序启动期间发生的异常。 你可以[配置主机的行为方式中的错误响应在启动期间](hosting.md#detailed-errors)使用`captureStartupErrors`和`detailedErrors`密钥。

如果此错误发生后主机地址/端口绑定，承载可以只显示捕获的启动错误的错误页。 如果出于任何原因失败任何绑定，在承载层记录关键异常，dotnet 进程崩溃 (crash)，并不显示任何错误页。

## <a name="aspnet-mvc-error-handling"></a>ASP.NET MVC 错误处理

[MVC](../mvc/index.md)应用具有一些用于处理错误，如配置异常筛选器和执行模型验证其他选项。

### <a name="exception-filters"></a>异常筛选器

全局范围内或在 MVC 应用程序按每个控制器或每个操作，可以配置异常筛选器。 这些筛选器处理的控制器操作或另一个筛选器，执行过程中出现任何未处理的异常，否则不调用。 了解有关中的异常筛选器的详细信息[筛选器](../mvc/controllers/filters.md)。

>[!TIP]
> 异常筛选器非常适用于捕获 MVC 操作内出现的异常，但它们不是灵活性不如错误处理中间件。 首选中间件一般情况下，并使用筛选器仅需要在其中执行错误处理*以不同方式*基于的 MVC 操作已被选。

### <a name="handling-model-state-errors"></a>处理模型状态错误

[模型验证](../mvc/models/validation.md)发生之前被调用，每个控制器操作和操作方法负责检查`ModelState.IsValid`并相应地作出反应。

某些应用程序会选择在这种情况下遵守标准的约定来处理模型验证错误，[筛选器](../mvc/controllers/filters.md)可能是适当的位置，若要实现此类策略。 你应测试你的操作无效的模型状态的行为方式。 了解详细信息中[测试控制器逻辑](../mvc/controllers/testing.md)。



