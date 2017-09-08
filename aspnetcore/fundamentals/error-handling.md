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
ms.openlocfilehash: 5898892c63e978adfabf9939394fef4ea1848d49
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="introduction-to-error-handling-in-aspnet-core"></a><span data-ttu-id="90618-104">ASP.NET 核心中的错误处理简介</span><span class="sxs-lookup"><span data-stu-id="90618-104">Introduction to Error Handling in ASP.NET Core</span></span>

<span data-ttu-id="90618-105">通过[Steve Smith](http://ardalis.com)和[Tom Dykstra](https://github.com/tdykstra/)</span><span class="sxs-lookup"><span data-stu-id="90618-105">By [Steve Smith](http://ardalis.com) and [Tom Dykstra](https://github.com/tdykstra/)</span></span>

<span data-ttu-id="90618-106">本文介绍如何处理 ASP.NET Core 应用中的错误的常见 appoaches。</span><span class="sxs-lookup"><span data-stu-id="90618-106">This article covers common appoaches to handling errors in ASP.NET Core apps.</span></span>

[<span data-ttu-id="90618-107">查看或下载示例代码</span><span class="sxs-lookup"><span data-stu-id="90618-107">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/sample)

## <a name="the-developer-exception-page"></a><span data-ttu-id="90618-108">开发人员异常页</span><span class="sxs-lookup"><span data-stu-id="90618-108">The developer exception page</span></span>

<span data-ttu-id="90618-109">若要配置应用以显示页面，其中显示有关异常的详细的信息，请安装`Microsoft.AspNetCore.Diagnostics`NuGet 包和将行添加到[启动类中配置方法](startup.md):</span><span class="sxs-lookup"><span data-stu-id="90618-109">To configure an app to display a page that shows detailed information about exceptions, install the `Microsoft.AspNetCore.Diagnostics` NuGet package and add a line to the [Configure method in the Startup class](startup.md):</span></span>

<span data-ttu-id="90618-110">[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_DevExceptionPage&highlight=7)]</span><span class="sxs-lookup"><span data-stu-id="90618-110">[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_DevExceptionPage&highlight=7)]</span></span>

<span data-ttu-id="90618-111">Put`UseDeveloperExceptionPage`之前你想要捕获异常，如的任何中间件`app.UseMvc`。</span><span class="sxs-lookup"><span data-stu-id="90618-111">Put `UseDeveloperExceptionPage` before any middleware you want to catch exceptions in, such as `app.UseMvc`.</span></span>

>[!WARNING]
> <span data-ttu-id="90618-112">启用开发人员异常页**仅当在开发环境中运行应用程序**。</span><span class="sxs-lookup"><span data-stu-id="90618-112">Enable the developer exception page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="90618-113">你不想要在生产中的应用程序运行时公开共享详细的异常信息。</span><span class="sxs-lookup"><span data-stu-id="90618-113">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="90618-114">[了解有关配置环境](environments.md)。</span><span class="sxs-lookup"><span data-stu-id="90618-114">[Learn more about configuring environments](environments.md).</span></span>

<span data-ttu-id="90618-115">若要查看开发人员异常页，可使用环境设置为运行示例应用程序`Development`，并添加`?throw=true`到应用程序的基础 URL。</span><span class="sxs-lookup"><span data-stu-id="90618-115">To see the developer exception page, run the sample application with the environment set to `Development`, and add `?throw=true` to the base URL of the app.</span></span> <span data-ttu-id="90618-116">该页面包括有关异常和请求的信息的几个选项卡。</span><span class="sxs-lookup"><span data-stu-id="90618-116">The page includes several tabs with information about the exception and the request.</span></span> <span data-ttu-id="90618-117">第一个选项卡包括堆栈跟踪。</span><span class="sxs-lookup"><span data-stu-id="90618-117">The first tab includes a stack trace.</span></span> 

![堆栈跟踪](error-handling/_static/developer-exception-page.png)

<span data-ttu-id="90618-119">如果有的话下, 一步的选项卡将显示查询字符串参数。</span><span class="sxs-lookup"><span data-stu-id="90618-119">The next tab shows the query string parameters, if any.</span></span>

![查询字符串参数](error-handling/_static/developer-exception-page-query.png)

<span data-ttu-id="90618-121">此请求没有任何 cookie，但如果它这样做，它们将出现在**Cookie**选项卡。</span><span class="sxs-lookup"><span data-stu-id="90618-121">This request didn't have any cookies, but if it did, they would appear on the **Cookies** tab.</span></span> <span data-ttu-id="90618-122">你可以看到在最后一个选项卡中传递的标头。</span><span class="sxs-lookup"><span data-stu-id="90618-122">You can see the headers that were passed in the last tab.</span></span>

![标头](error-handling/_static/developer-exception-page-headers.png)

## <a name="configuring-a-custom-exception-handling-page"></a><span data-ttu-id="90618-124">配置自定义异常处理页</span><span class="sxs-lookup"><span data-stu-id="90618-124">Configuring a custom exception handling page</span></span>

<span data-ttu-id="90618-125">它是配置异常处理程序页后，可以使用应用程序未在运行时一个好办法`Development`环境。</span><span class="sxs-lookup"><span data-stu-id="90618-125">It's a good idea to configure an exception handler page to use when the app is not running in the `Development` environment.</span></span>

<span data-ttu-id="90618-126">[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_DevExceptionPage&highlight=11)]</span><span class="sxs-lookup"><span data-stu-id="90618-126">[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_DevExceptionPage&highlight=11)]</span></span>

<span data-ttu-id="90618-127">在 MVC 应用程序，不显式修饰的错误处理程序操作方法使用 HTTP 方法属性，如`HttpGet`。</span><span class="sxs-lookup"><span data-stu-id="90618-127">In an MVC app, don't explicitly decorate the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="90618-128">使用显式谓词无法阻止一些请求访问方法。</span><span class="sxs-lookup"><span data-stu-id="90618-128">Using explicit verbs could prevent some requests from reaching the method.</span></span>

```csharp
[Route("/Error")]
public IActionResult Index()
{
    // Handle error here
}
```

## <a name="configuring-status-code-pages"></a><span data-ttu-id="90618-129">配置状态代码页</span><span class="sxs-lookup"><span data-stu-id="90618-129">Configuring status code pages</span></span>

<span data-ttu-id="90618-130">默认情况下，你的应用程序将不提供的 HTTP 状态代码 500 （内部服务器错误） 或 404 （未找到） 等的丰富的状态代码页。</span><span class="sxs-lookup"><span data-stu-id="90618-130">By default, your app will not provide a rich status code page for HTTP status codes such as 500 (Internal Server Error) or 404 (Not Found).</span></span> <span data-ttu-id="90618-131">你可以配置`StatusCodePagesMiddleware`通过将行添加到`Configure`方法：</span><span class="sxs-lookup"><span data-stu-id="90618-131">You can configure the `StatusCodePagesMiddleware` by adding a line to the `Configure` method:</span></span>

```csharp
app.UseStatusCodePages();
```

<span data-ttu-id="90618-132">默认情况下，此中间件添加简单、 纯文本文件的处理程序常见状态代码 （如 404）：</span><span class="sxs-lookup"><span data-stu-id="90618-132">By default, this middleware adds simple, text-only handlers for common status codes, such as 404:</span></span>

![404 页](error-handling/_static/default-404-status-code.png)

<span data-ttu-id="90618-134">该中间件支持几种不同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="90618-134">The middleware supports several different extension methods.</span></span> <span data-ttu-id="90618-135">一种采用 lambda 表达式，另一个操作采用内容类型和格式字符串。</span><span class="sxs-lookup"><span data-stu-id="90618-135">One takes a lambda expression, another takes a content type and format string.</span></span>

<span data-ttu-id="90618-136">[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_StatusCodePages)]</span><span class="sxs-lookup"><span data-stu-id="90618-136">[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_StatusCodePages)]</span></span>

```csharp
app.UseStatusCodePages("text/plain", "Status code page, status code: {0}");
```

<span data-ttu-id="90618-137">也有重定向扩展方法。</span><span class="sxs-lookup"><span data-stu-id="90618-137">There are also redirect extension methods.</span></span> <span data-ttu-id="90618-138">一个将 302 状态代码发送到客户端，并其中一个将原始的状态代码返回到客户端，但还会处理程序执行的重定向 URL。</span><span class="sxs-lookup"><span data-stu-id="90618-138">One sends a 302 status code to the client, and one returns the original status code to the client but also executes the handler for the redirect URL.</span></span>

<span data-ttu-id="90618-139">[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]</span><span class="sxs-lookup"><span data-stu-id="90618-139">[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]</span></span>

```csharp
app.UseStatusCodePagesWithReExecute("/error/{0}");
```

<span data-ttu-id="90618-140">如果你需要禁用某些请求的状态代码页，你可以执行操作：</span><span class="sxs-lookup"><span data-stu-id="90618-140">If you need to disable status code pages for certain requests, you can do so:</span></span>

```csharp
var statusCodePagesFeature = context.Features.Get<IStatusCodePagesFeature>();
if (statusCodePagesFeature != null)
{
  statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="90618-141">异常处理代码</span><span class="sxs-lookup"><span data-stu-id="90618-141">Exception-handling code</span></span>

<span data-ttu-id="90618-142">异常处理页的代码可能会引发异常。</span><span class="sxs-lookup"><span data-stu-id="90618-142">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="90618-143">它通常是生产错误页，以包含纯静态内容的一个好主意。</span><span class="sxs-lookup"><span data-stu-id="90618-143">It's often a good idea for production error pages to consist of purely static content.</span></span>

<span data-ttu-id="90618-144">此外，注意后已发送响应的标头，则不能更改响应的状态代码，也可以任何异常页或处理程序运行。</span><span class="sxs-lookup"><span data-stu-id="90618-144">Also, be aware that once the headers for a response have been sent, you can't change the response's status code, nor can any exception pages or handlers run.</span></span> <span data-ttu-id="90618-145">响应必须完成或放弃连接。</span><span class="sxs-lookup"><span data-stu-id="90618-145">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="90618-146">服务器异常处理</span><span class="sxs-lookup"><span data-stu-id="90618-146">Server exception handling</span></span>

<span data-ttu-id="90618-147">除了异常处理应用程序中的逻辑[服务器](servers/index.md)承载您的应用程序将执行一些异常处理。</span><span class="sxs-lookup"><span data-stu-id="90618-147">In addition to the exception handling logic in your app, the [server](servers/index.md) hosting your app will perform some exception handling.</span></span> <span data-ttu-id="90618-148">如果之前已发送标头，服务器会捕捉异常则将发送具有没有正文的 500 内部服务器错误响应。</span><span class="sxs-lookup"><span data-stu-id="90618-148">If the server catches an exception before the headers have been sent it sends a 500 Internal Server Error response with no body.</span></span> <span data-ttu-id="90618-149">如果已发送标头后，它会捕获异常，则关闭连接。</span><span class="sxs-lookup"><span data-stu-id="90618-149">If it catches an exception after the headers have been sent, it closes the connection.</span></span> <span data-ttu-id="90618-150">将服务器处理未由你的应用程序的请求的处理和所发生的任何异常将由服务器的异常处理。</span><span class="sxs-lookup"><span data-stu-id="90618-150">Requests that are not handled by your app will be handled by the server, and any exception that occurs will be handled by the server's exception handling.</span></span> <span data-ttu-id="90618-151">任何自定义错误页或异常处理中间件或已为你的应用程序配置的筛选器将不会影响此行为。</span><span class="sxs-lookup"><span data-stu-id="90618-151">Any custom error pages or exception handling middleware or filters you have configured for your app will not affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="90618-152">启动异常处理</span><span class="sxs-lookup"><span data-stu-id="90618-152">Startup exception handling</span></span>

<span data-ttu-id="90618-153">仅在承载层可以处理应用程序启动期间发生的异常。</span><span class="sxs-lookup"><span data-stu-id="90618-153">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="90618-154">应用程序启动过程中发生的异常可能会影响服务器行为。</span><span class="sxs-lookup"><span data-stu-id="90618-154">Exceptions that occur during app startup can impact server behavior.</span></span> <span data-ttu-id="90618-155">例如，如果发生异常，然后才能调用`KestrelServerOptions.UseHttps`，承载层捕获异常，启动服务器，并在非 SSL 端口上显示一个错误页面。</span><span class="sxs-lookup"><span data-stu-id="90618-155">For example, if an exception happens before you call `KestrelServerOptions.UseHttps`, the hosting layer catches the exception, starts the server, and displays an error page on the non-SSL port.</span></span> <span data-ttu-id="90618-156">如果该行执行后，将发生异常，错误提供的页面是通过 HTTPS 相反。</span><span class="sxs-lookup"><span data-stu-id="90618-156">If an exception happens after that line executes, the error page is served over HTTPS instead.</span></span>

<span data-ttu-id="90618-157">你可以[配置主机的行为方式中的错误响应在启动期间](hosting.md#configuring-a-host)使用`CaptureStartupErrors`和`detailedErrors`密钥。</span><span class="sxs-lookup"><span data-stu-id="90618-157">You can [configure how the host will behave in response to errors during startup](hosting.md#configuring-a-host) using `CaptureStartupErrors` and the `detailedErrors` key.</span></span>

## <a name="aspnet-mvc-error-handling"></a><span data-ttu-id="90618-158">ASP.NET MVC 错误处理</span><span class="sxs-lookup"><span data-stu-id="90618-158">ASP.NET MVC error handling</span></span>

<span data-ttu-id="90618-159">[MVC](../mvc/index.md)应用具有一些用于处理错误，如配置异常筛选器和执行模型验证其他选项。</span><span class="sxs-lookup"><span data-stu-id="90618-159">[MVC](../mvc/index.md) apps have some additional options for handling errors, such as configuring exception filters and performing model validation.</span></span>

### <a name="exception-filters"></a><span data-ttu-id="90618-160">异常筛选器</span><span class="sxs-lookup"><span data-stu-id="90618-160">Exception Filters</span></span>

<span data-ttu-id="90618-161">全局范围内或在 MVC 应用程序按每个控制器或每个操作，可以配置异常筛选器。</span><span class="sxs-lookup"><span data-stu-id="90618-161">Exception filters can be configured globally or on a per-controller or per-action basis in an MVC app.</span></span> <span data-ttu-id="90618-162">这些筛选器处理的控制器操作或另一个筛选器，执行过程中出现任何未处理的异常，否则不调用。</span><span class="sxs-lookup"><span data-stu-id="90618-162">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter, and are not called otherwise.</span></span> <span data-ttu-id="90618-163">了解有关中的异常筛选器的详细信息[筛选器](../mvc/controllers/filters.md)。</span><span class="sxs-lookup"><span data-stu-id="90618-163">Learn more about exception filters in [Filters](../mvc/controllers/filters.md).</span></span>

>[!TIP]
> <span data-ttu-id="90618-164">异常筛选器非常适用于捕获 MVC 操作内出现的异常，但它们不是灵活性不如错误处理中间件。</span><span class="sxs-lookup"><span data-stu-id="90618-164">Exception filters are good for trapping exceptions that occur within MVC actions, but they're not as flexible as error handling middleware.</span></span> <span data-ttu-id="90618-165">首选中间件一般情况下，并使用筛选器仅需要在其中执行错误处理*以不同方式*基于的 MVC 操作已被选。</span><span class="sxs-lookup"><span data-stu-id="90618-165">Prefer middleware for the general case, and use filters only where you need to do error handling *differently* based on which MVC action was chosen.</span></span>

### <a name="handling-model-state-errors"></a><span data-ttu-id="90618-166">处理模型状态错误</span><span class="sxs-lookup"><span data-stu-id="90618-166">Handling Model State Errors</span></span>

<span data-ttu-id="90618-167">[模型验证](../mvc/models/validation.md)发生之前被调用，每个控制器操作和操作方法负责检查`ModelState.IsValid`并相应地作出反应。</span><span class="sxs-lookup"><span data-stu-id="90618-167">[Model validation](../mvc/models/validation.md) occurs prior to each controller action being invoked, and it is the action method’s responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span>

<span data-ttu-id="90618-168">某些应用程序会选择在这种情况下遵守标准的约定来处理模型验证错误，[筛选器](../mvc/controllers/filters.md)可能是适当的位置，若要实现此类策略。</span><span class="sxs-lookup"><span data-stu-id="90618-168">Some apps will choose to follow a standard convention for dealing with model validation errors, in which case a [filter](../mvc/controllers/filters.md) may be an appropriate place to implement such a policy.</span></span> <span data-ttu-id="90618-169">你应测试你的操作无效的模型状态的行为方式。</span><span class="sxs-lookup"><span data-stu-id="90618-169">You should test how your actions behave with invalid model states.</span></span> <span data-ttu-id="90618-170">了解详细信息中[测试控制器逻辑](../mvc/controllers/testing.md)。</span><span class="sxs-lookup"><span data-stu-id="90618-170">Learn more in [Testing controller logic](../mvc/controllers/testing.md).</span></span>



