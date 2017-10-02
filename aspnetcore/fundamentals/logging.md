---
title: "在 ASP.NET 核心中的日志记录"
author: ardalis
description: "了解有关 ASP.NET 核心中的日志记录框架。 发现的内置日志记录提供程序，并了解有关常用的第三方提供程序的详细信息。"
keywords: "ASP.NET 核心，日志记录，日志记录 providers,Microsoft.Extensions.Logging,ILogger,ILoggerFactory,LogLevel,WithFilter,TraceSource,EventLog,EventSource,scopes"
ms.author: tdykstra
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: ac27ac68-d76a-4f8e-b8ab-ea045803e5f2
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/logging
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9557e9f6915507450de3ffe500582839a28c3f0c
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2017
---
# <a name="introduction-to-logging-in-aspnet-core"></a><span data-ttu-id="9b696-105">在 ASP.NET 核心中日志记录的简介</span><span class="sxs-lookup"><span data-stu-id="9b696-105">Introduction to Logging in ASP.NET Core</span></span>

<span data-ttu-id="9b696-106">通过[Steve Smith](https://ardalis.com/)和[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="9b696-106">By [Steve Smith](https://ardalis.com/) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="9b696-107">ASP.NET 核心支持适用于各种日志记录提供程序的日志记录 API。</span><span class="sxs-lookup"><span data-stu-id="9b696-107">ASP.NET Core supports a logging API that works with a variety of logging providers.</span></span> <span data-ttu-id="9b696-108">内置提供程序允许你将日志发送到一个或多个目标，而第三方日志记录框架中，您可以插入。</span><span class="sxs-lookup"><span data-stu-id="9b696-108">Built-in providers let you send logs to one or more destinations, and you can plug in a third-party logging framework.</span></span> <span data-ttu-id="9b696-109">这篇文章演示如何在代码中使用的内置日志记录 API 和提供程序。</span><span class="sxs-lookup"><span data-stu-id="9b696-109">This article shows how to use the built-in logging API and providers in your code.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="9b696-110">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="9b696-110">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="9b696-111">[查看或下载的示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/sample2)([如何下载](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9b696-111">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/sample2) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="9b696-112">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="9b696-112">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="9b696-113">[查看或下载的示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/sample)([如何下载](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9b696-113">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

---

## <a name="how-to-create-logs"></a><span data-ttu-id="9b696-114">如何创建日志</span><span class="sxs-lookup"><span data-stu-id="9b696-114">How to create logs</span></span>

<span data-ttu-id="9b696-115">若要创建日志，获取`ILogger`对象[依赖关系注入](dependency-injection.md)容器：</span><span class="sxs-lookup"><span data-stu-id="9b696-115">To create logs, get an `ILogger` object from the [dependency injection](dependency-injection.md) container:</span></span>

[!code-csharp[](logging/sample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="9b696-116">然后该记录器对象调用日志记录方法：</span><span class="sxs-lookup"><span data-stu-id="9b696-116">Then call logging methods on that logger object:</span></span>

[!code-csharp[](logging/sample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="9b696-117">此示例将创建日志与`TodoController`类作为*类别*。</span><span class="sxs-lookup"><span data-stu-id="9b696-117">This example creates logs with the `TodoController` class as the *category*.</span></span>  <span data-ttu-id="9b696-118">目录将介绍[本文稍后的](#log-category)。</span><span class="sxs-lookup"><span data-stu-id="9b696-118">Categories are explained [later in this article](#log-category).</span></span>

<span data-ttu-id="9b696-119">ASP.NET 核心不提供异步记录器方法因为日志记录应该速度较快，不值得使用异步的成本。</span><span class="sxs-lookup"><span data-stu-id="9b696-119">ASP.NET Core does not provide async logger methods because logging should be so fast that it isn't worth the cost of using async.</span></span> <span data-ttu-id="9b696-120">如果你在其中，不为 true 的情况下，请考虑更改你记录的方式。</span><span class="sxs-lookup"><span data-stu-id="9b696-120">If you're in a situation where that's not true, consider changing the way you log.</span></span>  <span data-ttu-id="9b696-121">如果数据存储区较慢，首先，将日志消息写入快速存储，则稍后将它们移至慢速存储。</span><span class="sxs-lookup"><span data-stu-id="9b696-121">If your data store is slow, write the log messages to a fast store first, then move them to a slow store later.</span></span> <span data-ttu-id="9b696-122">例如，登录到的消息队列中读取并保存到慢速存储由另一个进程。</span><span class="sxs-lookup"><span data-stu-id="9b696-122">For example, log to a message queue that is read and persisted to slow storage by another process.</span></span>

## <a name="how-to-add-providers"></a><span data-ttu-id="9b696-123">如何添加提供程序</span><span class="sxs-lookup"><span data-stu-id="9b696-123">How to add providers</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="9b696-124">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="9b696-124">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="9b696-125">日志记录提供程序采用与创建的消息`ILogger`对象和显示或将其存储。</span><span class="sxs-lookup"><span data-stu-id="9b696-125">A logging provider takes the messages that you create with an `ILogger` object and displays or stores them.</span></span> <span data-ttu-id="9b696-126">例如，控制台提供程序显示在控制台中，消息和 Azure 应用程序服务提供商可以将其存储在 Azure blob 存储。</span><span class="sxs-lookup"><span data-stu-id="9b696-126">For example, the Console provider displays messages on the console, and the Azure App Service provider can store them in Azure blob storage.</span></span>

<span data-ttu-id="9b696-127">若要使用提供程序，调用提供程序的`Add<ProviderName>`中的扩展方法*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9b696-127">To use a provider, call the provider's `Add<ProviderName>` extension method in *Program.cs*:</span></span>

[!code-csharp[](logging/sample2/Program.cs?name=snippet_ExpandDefault&highlight=16,17)]

<span data-ttu-id="9b696-128">默认项目模板将设置日志记录的方式，你看到它在上面的代码，但`ConfigureLogging`调用可通过`CreateDefaultBuilder`方法。</span><span class="sxs-lookup"><span data-stu-id="9b696-128">The default project template sets up logging the way you see it in the preceding code, but the `ConfigureLogging` call is done by the `CreateDefaultBuilder` method.</span></span> <span data-ttu-id="9b696-129">以下是的代码*Program.cs* ，它由项目模板创建：</span><span class="sxs-lookup"><span data-stu-id="9b696-129">Here's the code in *Program.cs* that is created by project templates:</span></span>

[!code-csharp[](logging/sample2/Program.cs?name=snippet_TemplateCode&highlight=7)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="9b696-130">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="9b696-130">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="9b696-131">日志记录提供程序采用与创建的消息`ILogger`对象和显示或将其存储。</span><span class="sxs-lookup"><span data-stu-id="9b696-131">A logging provider takes the messages that you create with an `ILogger` object and displays or stores them.</span></span> <span data-ttu-id="9b696-132">例如，控制台提供程序显示在控制台中，消息和 Azure 应用程序服务提供商可以将其存储在 Azure blob 存储。</span><span class="sxs-lookup"><span data-stu-id="9b696-132">For example, the Console provider displays messages on the console, and the Azure App Service provider can store them in Azure blob storage.</span></span>

<span data-ttu-id="9b696-133">若要使用的提供程序，安装其 NuGet 包和实例上调用提供程序的扩展方法`ILoggerFactory`，下面的示例中所示。</span><span class="sxs-lookup"><span data-stu-id="9b696-133">To use a provider, install its NuGet package and call the provider's extension method on an instance of `ILoggerFactory`, as shown in the following example.</span></span>

[!code-csharp[](logging/sample//Startup.cs?name=snippet_AddConsoleAndDebug&highlight=3,5-7)]

<span data-ttu-id="9b696-134">ASP.NET 核心[依赖关系注入](dependency-injection.md)(DI) 提供`ILoggerFactory`实例。</span><span class="sxs-lookup"><span data-stu-id="9b696-134">ASP.NET Core [dependency injection](dependency-injection.md) (DI) provides the `ILoggerFactory` instance.</span></span> <span data-ttu-id="9b696-135">`AddConsole`和`AddDebug`中定义的扩展方法[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)和[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/)包。</span><span class="sxs-lookup"><span data-stu-id="9b696-135">The `AddConsole` and `AddDebug` extension methods are defined in the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) and [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) packages.</span></span> <span data-ttu-id="9b696-136">每个扩展方法调用`ILoggerFactory.AddProvider`方法，并传递提供程序实例中。</span><span class="sxs-lookup"><span data-stu-id="9b696-136">Each extension method calls the `ILoggerFactory.AddProvider` method, passing in an instance of the provider.</span></span> 

> [!NOTE]
> <span data-ttu-id="9b696-137">本文的示例应用程序添加日志记录中的提供商`Configure`方法`Startup`类。</span><span class="sxs-lookup"><span data-stu-id="9b696-137">The sample application for this article adds logging providers in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="9b696-138">如果你想要从更早版本执行的代码获取日志输出，添加中的日志记录提供程序`Startup`改为类构造函数。</span><span class="sxs-lookup"><span data-stu-id="9b696-138">If you want to get log output from code that executes earlier, add logging providers in the `Startup` class constructor instead.</span></span> 

---

<span data-ttu-id="9b696-139">你将找到有关每个信息[内置日志记录提供程序](#built-in-logging-providers)并链接到[第三方日志记录提供程序](#third-party-logging-providers)文章中更高版本。</span><span class="sxs-lookup"><span data-stu-id="9b696-139">You'll find information about each [built-in logging provider](#built-in-logging-providers) and links to [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="9b696-140">日志记录输出示例</span><span class="sxs-lookup"><span data-stu-id="9b696-140">Sample logging output</span></span>

<span data-ttu-id="9b696-141">与在前面部分所示的示例代码，你将看到在控制台中的日志，从命令行运行时。</span><span class="sxs-lookup"><span data-stu-id="9b696-141">With the sample code shown in the preceding section, you'll see logs in the console when you run from the command line.</span></span> <span data-ttu-id="9b696-142">下面是控制台输出示例：</span><span class="sxs-lookup"><span data-stu-id="9b696-142">Here's an example of console output:</span></span>

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
 
<span data-ttu-id="9b696-143">这些日志创建通过转到`http://localhost:5000/api/todo/0`，随即将会触发这两者的执行`ILogger`前面部分中显示的调用。</span><span class="sxs-lookup"><span data-stu-id="9b696-143">These logs were created by going to `http://localhost:5000/api/todo/0`, which triggers execution of both `ILogger` calls shown in the preceding section.</span></span>

<span data-ttu-id="9b696-144">下面是相同的日志的示例，当在 Visual Studio 中运行示例应用程序在调试窗口中显示：</span><span class="sxs-lookup"><span data-stu-id="9b696-144">Here's an example of the same logs as they appear in the Debug window when you run the sample application in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404 
```

<span data-ttu-id="9b696-145">创建的日志`ILogger`前面部分中显示的调用以"TodoApi.Controllers.TodoController"开头。</span><span class="sxs-lookup"><span data-stu-id="9b696-145">The logs that were created by the `ILogger` calls shown in the preceding section begin with "TodoApi.Controllers.TodoController".</span></span> <span data-ttu-id="9b696-146">开始使用"Microsoft"类别的日志都是从 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="9b696-146">The logs that begin with "Microsoft" categories are from ASP.NET Core.</span></span> <span data-ttu-id="9b696-147">ASP.NET 核心本身和应用程序代码正在使用相同的日志记录 API 和相同的日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="9b696-147">ASP.NET Core itself and your application code are using the same logging API and the same logging providers.</span></span>

<span data-ttu-id="9b696-148">本文的其余部分介绍一些详细信息和日志记录选项。</span><span class="sxs-lookup"><span data-stu-id="9b696-148">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="9b696-149">NuGet 包</span><span class="sxs-lookup"><span data-stu-id="9b696-149">NuGet packages</span></span>

<span data-ttu-id="9b696-150">`ILogger`和`ILoggerFactory`接口位于[Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)，并为它们的默认实现处于[Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/)。</span><span class="sxs-lookup"><span data-stu-id="9b696-150">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="9b696-151">日志类别</span><span class="sxs-lookup"><span data-stu-id="9b696-151">Log category</span></span>

<span data-ttu-id="9b696-152">A*类别*附带你创建的每个日志。</span><span class="sxs-lookup"><span data-stu-id="9b696-152">A *category* is included with each log that you create.</span></span>  <span data-ttu-id="9b696-153">在创建时指定类别`ILogger`对象。</span><span class="sxs-lookup"><span data-stu-id="9b696-153">You specify the category when you create an `ILogger` object.</span></span> <span data-ttu-id="9b696-154">类别可以是任意字符串，但一个约定是使用日志将写入从其类的完全限定的名称。</span><span class="sxs-lookup"><span data-stu-id="9b696-154">The category may be any string, but a convention is to use the fully qualified name of the class from which the logs are written.</span></span>  <span data-ttu-id="9b696-155">例如:"TodoApi.Controllers.TodoController"。</span><span class="sxs-lookup"><span data-stu-id="9b696-155">For example: "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="9b696-156">你可以指定为字符串的类别或使用派生自类型类别的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="9b696-156">You can specify the category as a string or use an extension method that derives the category from the type.</span></span> <span data-ttu-id="9b696-157">若要以字符串形式指定的类别，调用`CreateLogger`上`ILoggerFactory`实例，如下所示。</span><span class="sxs-lookup"><span data-stu-id="9b696-157">To specify the category as a string, call `CreateLogger` on an `ILoggerFactory` instance, as shown below.</span></span>

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="9b696-158">大多数情况下，它将是管理更轻松地使用`ILogger<T>`，如下面的示例。</span><span class="sxs-lookup"><span data-stu-id="9b696-158">Most of the time, it will be easier to use  `ILogger<T>`, as in the following example.</span></span>

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="9b696-159">这是等效于调用`CreateLogger`的完全限定的类型名称与`T`。</span><span class="sxs-lookup"><span data-stu-id="9b696-159">This is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="9b696-160">日志级别</span><span class="sxs-lookup"><span data-stu-id="9b696-160">Log level</span></span>

<span data-ttu-id="9b696-161">每次写入日志时，你指定其[LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel)。</span><span class="sxs-lookup"><span data-stu-id="9b696-161">Each time you write a log, you specify its [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="9b696-162">日志级别指示严重性或重要性的程度。</span><span class="sxs-lookup"><span data-stu-id="9b696-162">The log level indicates the degree of severity or importance.</span></span>  <span data-ttu-id="9b696-163">例如，你可以编写`Information`日志方法通常情况下，结束时`Warning`日志方法将返回 404 返回代码，当和`Error`时捕获了意外的异常记录。</span><span class="sxs-lookup"><span data-stu-id="9b696-163">For example, you might write an `Information` log when a method ends normally, a `Warning` log when a method returns a 404 return code, and an `Error` log when you catch an unexpected exception.</span></span>

<span data-ttu-id="9b696-164">在下面的代码示例，方法的名称 (例如， `LogWarning`) 指定的日志级别。</span><span class="sxs-lookup"><span data-stu-id="9b696-164">In the following code example, the names of the methods (for example, `LogWarning`) specify the log level.</span></span>  <span data-ttu-id="9b696-165">第一个参数是[记录事件 ID](#log-event-id) （本文后面所述）。</span><span class="sxs-lookup"><span data-stu-id="9b696-165">The first parameter is the [Log event ID](#log-event-id) (explained later in this article).</span></span>  <span data-ttu-id="9b696-166">剩余的参数构造的日志消息字符串。</span><span class="sxs-lookup"><span data-stu-id="9b696-166">The remaining parameters construct a log message string.</span></span>

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="9b696-167">方法名称中包括的级别的日志方法[ILogger 的扩展方法](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions)。</span><span class="sxs-lookup"><span data-stu-id="9b696-167">Log methods that include the level in the method name are [extension methods for ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions).</span></span> <span data-ttu-id="9b696-168">在后台，这些方法调用`Log`采用的方法`LogLevel`参数。</span><span class="sxs-lookup"><span data-stu-id="9b696-168">Behind the scenes, these methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="9b696-169">你可以调用`Log`直接方法，而不是这些扩展方法，但语法之一是相对复杂。</span><span class="sxs-lookup"><span data-stu-id="9b696-169">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="9b696-170">有关详细信息，请参阅[ILogger 接口](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger)和[记录器扩展源代码](https://github.com/aspnet/Logging/blob/master/src/Microsoft.Extensions.Logging.Abstractions/LoggerExtensions.cs)。</span><span class="sxs-lookup"><span data-stu-id="9b696-170">For more information, see the [ILogger interface](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) and the [logger extensions source code](https://github.com/aspnet/Logging/blob/master/src/Microsoft.Extensions.Logging.Abstractions/LoggerExtensions.cs).</span></span>

<span data-ttu-id="9b696-171">ASP.NET 核心定义了以下[日志级别](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel)，从最低到最高严重性此处排序。</span><span class="sxs-lookup"><span data-stu-id="9b696-171">ASP.NET Core defines the following [log levels](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel), ordered here from least to highest severity.</span></span>

* <span data-ttu-id="9b696-172">跟踪 = 0</span><span class="sxs-lookup"><span data-stu-id="9b696-172">Trace = 0</span></span>

  <span data-ttu-id="9b696-173">仅对于开发人员调试问题有价值的信息。</span><span class="sxs-lookup"><span data-stu-id="9b696-173">For information that is valuable only to a developer debugging an issue.</span></span> <span data-ttu-id="9b696-174">这些消息可能包含敏感应用程序数据，并因此不应启用在生产环境中。</span><span class="sxs-lookup"><span data-stu-id="9b696-174">These messages may contain sensitive application data and so should not be enabled in a production environment.</span></span> <span data-ttu-id="9b696-175">*默认情况下已禁用。*</span><span class="sxs-lookup"><span data-stu-id="9b696-175">*Disabled by default.*</span></span> <span data-ttu-id="9b696-176">示例：`Credentials: {"User":"someuser", "Password":"P@ssword"}`</span><span class="sxs-lookup"><span data-stu-id="9b696-176">Example: `Credentials: {"User":"someuser", "Password":"P@ssword"}`</span></span>

* <span data-ttu-id="9b696-177">调试 = 1</span><span class="sxs-lookup"><span data-stu-id="9b696-177">Debug = 1</span></span>

  <span data-ttu-id="9b696-178">有关在开发和调试过程中具有短期用途的信息。</span><span class="sxs-lookup"><span data-stu-id="9b696-178">For information that has short-term usefulness during development and debugging.</span></span> <span data-ttu-id="9b696-179">示例：`Entering method Configure with flag set to true.`你通常不会使`Debug`级别记录在生产环境中，除非你正在排查的因为数量过多的日志。</span><span class="sxs-lookup"><span data-stu-id="9b696-179">Example: `Entering method Configure with flag set to true.` You typically would not enable `Debug` level logs in production unless you are troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="9b696-180">信息 = 2</span><span class="sxs-lookup"><span data-stu-id="9b696-180">Information = 2</span></span>

  <span data-ttu-id="9b696-181">用于跟踪应用程序的常规流。</span><span class="sxs-lookup"><span data-stu-id="9b696-181">For tracking the general flow of the application.</span></span> <span data-ttu-id="9b696-182">这些日志通常包含一些长期的值。</span><span class="sxs-lookup"><span data-stu-id="9b696-182">These logs typically have some long-term value.</span></span> <span data-ttu-id="9b696-183">示例：`Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="9b696-183">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="9b696-184">警告 = 3</span><span class="sxs-lookup"><span data-stu-id="9b696-184">Warning = 3</span></span>

  <span data-ttu-id="9b696-185">在应用程序流中的异常或意外事件。</span><span class="sxs-lookup"><span data-stu-id="9b696-185">For abnormal or unexpected events in the application flow.</span></span> <span data-ttu-id="9b696-186">其中可能包括错误或其他条件，不会导致应用程序停止，但这可能需要进行调查。</span><span class="sxs-lookup"><span data-stu-id="9b696-186">These may include errors or other conditions that do not cause the application to stop, but which may need to be investigated.</span></span> <span data-ttu-id="9b696-187">处理的异常是要使用的一个常见位置`Warning`日志级别。</span><span class="sxs-lookup"><span data-stu-id="9b696-187">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="9b696-188">示例：`FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="9b696-188">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="9b696-189">错误 = 4</span><span class="sxs-lookup"><span data-stu-id="9b696-189">Error = 4</span></span>

  <span data-ttu-id="9b696-190">错误和不能处理的异常。</span><span class="sxs-lookup"><span data-stu-id="9b696-190">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="9b696-191">这些邮件表示当前的活动或操作 （如当前 HTTP 请求） 中失败，则不应用程序范围失败。</span><span class="sxs-lookup"><span data-stu-id="9b696-191">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an application-wide failure.</span></span> <span data-ttu-id="9b696-192">示例日志消息：`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="9b696-192">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="9b696-193">关键 = 5</span><span class="sxs-lookup"><span data-stu-id="9b696-193">Critical = 5</span></span>

  <span data-ttu-id="9b696-194">需要立即关注的故障。</span><span class="sxs-lookup"><span data-stu-id="9b696-194">For failures that require immediate attention.</span></span> <span data-ttu-id="9b696-195">示例： 数据丢失的情况，磁盘空间不足。</span><span class="sxs-lookup"><span data-stu-id="9b696-195">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="9b696-196">日志级别可用于控制多少日志输出写入到特定的存储介质或显示窗口。</span><span class="sxs-lookup"><span data-stu-id="9b696-196">You can use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="9b696-197">例如，在生产环境中可能需要的所有日志`Information`级别并降低转到卷数据存储区和的所有日志`Warning`级别和更高版本以转到值数据存储区。</span><span class="sxs-lookup"><span data-stu-id="9b696-197">For example, in production you might want all logs of `Information` level and lower to go to a volume data store, and all logs of `Warning` level and higher to go to a value data store.</span></span> <span data-ttu-id="9b696-198">在开发期间，你可能通常发送的日志`Warning`或更高严重性到控制台。</span><span class="sxs-lookup"><span data-stu-id="9b696-198">During development, you might normally send logs of `Warning` or higher severity to the console.</span></span> <span data-ttu-id="9b696-199">然后，当你需要进行故障排除，可以将添加`Debug`级别。</span><span class="sxs-lookup"><span data-stu-id="9b696-199">Then when you need to troubleshoot, you can add `Debug` level.</span></span> <span data-ttu-id="9b696-200">[日志筛选](#log-filtering)本文后面的部分介绍如何控制提供程序处理的日志级别。</span><span class="sxs-lookup"><span data-stu-id="9b696-200">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="9b696-201">ASP.NET 核心框架写入`Debug`级别 framework 事件日志。</span><span class="sxs-lookup"><span data-stu-id="9b696-201">The ASP.NET Core framework writes `Debug` level logs for framework events.</span></span> <span data-ttu-id="9b696-202">日志示例前面在本文中排除以下日志`Information`级别，因此没有`Debug`级别的日志中所示。</span><span class="sxs-lookup"><span data-stu-id="9b696-202">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` level logs were shown.</span></span> <span data-ttu-id="9b696-203">以下是控制台日志的示例，如果运行示例应用程序配置为显示`Debug`和更高版本日志中的控制台提供程序。</span><span class="sxs-lookup"><span data-stu-id="9b696-203">Here's an example of console logs if you run the sample application configured to show `Debug` and higher logs for the console provider.</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="9b696-204">日志事件 ID</span><span class="sxs-lookup"><span data-stu-id="9b696-204">Log event ID</span></span>

<span data-ttu-id="9b696-205">每次写入日志时，你可以指定*事件 ID*。</span><span class="sxs-lookup"><span data-stu-id="9b696-205">Each time you write a log, you can specify an *event ID*.</span></span> <span data-ttu-id="9b696-206">通过使用本地定义的示例应用程序执行上述`LoggingEvents`类：</span><span class="sxs-lookup"><span data-stu-id="9b696-206">The sample app does this by using a locally-defined `LoggingEvents` class:</span></span>

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](logging/sample//Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="9b696-207">事件 ID 是一个整数值，可用于将一组记录的事件与另一个相关联。</span><span class="sxs-lookup"><span data-stu-id="9b696-207">An event ID is an integer value that you can use to associate a set of logged events with one another.</span></span> <span data-ttu-id="9b696-208">例如的日志，以将项添加到购物车可能是事件 ID 1000，并且可能事件 ID 为 1001年的日志，以完成购买。</span><span class="sxs-lookup"><span data-stu-id="9b696-208">For instance, a log for adding an item to a shopping cart could be event ID 1000 and a log for completing a purchase could be event ID 1001.</span></span>

<span data-ttu-id="9b696-209">在日志记录输出中，可能存储在一个字段或文本消息，具体取决于提供程序中包含的事件 ID。</span><span class="sxs-lookup"><span data-stu-id="9b696-209">In logging output, the event ID may be stored in a field or included in the text message, depending on the provider.</span></span>  <span data-ttu-id="9b696-210">调试提供程序不会显示事件 Id，但控制台提供程序在显示这些方括号分类之后：</span><span class="sxs-lookup"><span data-stu-id="9b696-210">The Debug provider doesn't show event IDs, but the console provider shows them in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-format-string"></a><span data-ttu-id="9b696-211">日志消息格式字符串</span><span class="sxs-lookup"><span data-stu-id="9b696-211">Log message format string</span></span>

<span data-ttu-id="9b696-212">写入日志中，每次你提供文本消息。</span><span class="sxs-lookup"><span data-stu-id="9b696-212">Each time you write a log, you provide a text message.</span></span> <span data-ttu-id="9b696-213">消息字符串可以包含命名的占位符的参数值放在如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="9b696-213">The message string can contain named placeholders into which argument values are placed, as in the following example:</span></span>

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="9b696-214">占位符，其名称，顺序确定哪些参数用于它们。</span><span class="sxs-lookup"><span data-stu-id="9b696-214">The order of placeholders, not their names, determines which parameters are used for them.</span></span> <span data-ttu-id="9b696-215">例如，如果你有以下代码：</span><span class="sxs-lookup"><span data-stu-id="9b696-215">For example, if you have the following code:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="9b696-216">生成的日志消息将如下所示：</span><span class="sxs-lookup"><span data-stu-id="9b696-216">The resulting log message would look like this:</span></span>

```
Parameter values: parm1, parm2
```

<span data-ttu-id="9b696-217">日志记录框架进行消息中以使日志记录提供程序用来实现这种方式的格式设置[语义的日志记录，也称为结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="9b696-217">The logging framework does message formatting in this way to make it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="9b696-218">因为参数本身传递到日志记录系统，而不仅仅是格式化的消息字符串，日志记录提供程序可以将参数值存储为除了消息字符串的字段。</span><span class="sxs-lookup"><span data-stu-id="9b696-218">Because the arguments themselves are passed to the logging system, not just the formatted message string, logging providers can store the parameter values as fields in addition to the message string.</span></span> <span data-ttu-id="9b696-219">例如，如果你将定向到 Azure 表存储，你的日志输出和记录器方法调用如下所示：</span><span class="sxs-lookup"><span data-stu-id="9b696-219">For example, if you are directing your log output to Azure Table Storage, and your logger method call looks like this:</span></span>

```csharp
_logger.LogInformation("Getting item {ID} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="9b696-220">每个 Azure 表实体可能具有`ID`和`RequestTime`属性，可以简化对日志数据的查询。</span><span class="sxs-lookup"><span data-stu-id="9b696-220">Each Azure Table entity could have `ID` and `RequestTime` properties, which would simplify queries on log data.</span></span> <span data-ttu-id="9b696-221">无法查找在特定的所有日志`RequestTime`范围，而无需分析的短信超时时间。</span><span class="sxs-lookup"><span data-stu-id="9b696-221">You could find all logs within a particular `RequestTime` range, without having to parse the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="9b696-222">日志记录异常</span><span class="sxs-lookup"><span data-stu-id="9b696-222">Logging exceptions</span></span>

<span data-ttu-id="9b696-223">记录器方法具有重载，以让你通过引发异常，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="9b696-223">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="9b696-224">不同的提供程序以不同方式处理的异常信息。</span><span class="sxs-lookup"><span data-stu-id="9b696-224">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="9b696-225">下面是从上面显示的代码的调试提供程序输出示例。</span><span class="sxs-lookup"><span data-stu-id="9b696-225">Here's an example of Debug provider output from the code shown above.</span></span>

```
TodoApi.Controllers.TodoController:Warning: GetById(036dd898-fb01-47e8-9a65-f92eb73cf924) NOT FOUND

System.Exception: Item not found exception.
 at TodoApi.Controllers.TodoController.GetById(String id) in C:\logging\sample\src\TodoApi\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="9b696-226">日志筛选</span><span class="sxs-lookup"><span data-stu-id="9b696-226">Log filtering</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="9b696-227">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="9b696-227">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="9b696-228">你可以指定特定的提供程序和类别或对所有提供程序或所有类别的最小日志级别。</span><span class="sxs-lookup"><span data-stu-id="9b696-228">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span>  <span data-ttu-id="9b696-229">因此不要获取显示或存储，与该提供程序，不传递的最低级别上深化任何日志。</span><span class="sxs-lookup"><span data-stu-id="9b696-229">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span> 

<span data-ttu-id="9b696-230">如果你想要禁止显示所有日志，你可以指定`LogLevel.None`作为最小日志级别。</span><span class="sxs-lookup"><span data-stu-id="9b696-230">If you want to suppress all logs, you can specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="9b696-231">整数值`LogLevel.None`为 6，即高于`LogLevel.Critical`(5)。</span><span class="sxs-lookup"><span data-stu-id="9b696-231">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="9b696-232">**在配置中创建筛选器规则**</span><span class="sxs-lookup"><span data-stu-id="9b696-232">**Create filter rules in configuration**</span></span>

<span data-ttu-id="9b696-233">项目模板创建调用的代码`CreateDefaultBuilder`设置在控制台和调试提供程序的日志记录。</span><span class="sxs-lookup"><span data-stu-id="9b696-233">The project templates create code that calls `CreateDefaultBuilder` to set up logging for the Console and Debug providers.</span></span> <span data-ttu-id="9b696-234">`CreateDefaultBuilder`方法还设置日志记录以寻找中配置`Logging`部分，使用类似于下面的代码：</span><span class="sxs-lookup"><span data-stu-id="9b696-234">The `CreateDefaultBuilder` method also sets up logging to look for configuration in a `Logging` section, using code like the following:</span></span>

[!code-csharp[](logging/sample2/Program.cs?name=snippet_ExpandDefault&highlight=15)]

<span data-ttu-id="9b696-235">配置数据指定最小的日志级别由提供商和类别，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="9b696-235">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](logging/sample2/appsettings.json)]

<span data-ttu-id="9b696-236">此 JSON 创建六个筛选规则，一个用于调试提供程序、 控制台的提供程序的四个，并且适用于所有提供程序的一个。</span><span class="sxs-lookup"><span data-stu-id="9b696-236">This JSON creates six filter rules, one for the Debug provider, four for the Console provider, and one that applies to all providers.</span></span> <span data-ttu-id="9b696-237">你将看到为每个提供程序选择这些规则的更高版本如何只需之一时`ILogger`创建对象。</span><span class="sxs-lookup"><span data-stu-id="9b696-237">You'll see later how just one of these rules is chosen for each provider when an `ILogger` object is created.</span></span>

<span data-ttu-id="9b696-238">**在代码中的筛选规则**</span><span class="sxs-lookup"><span data-stu-id="9b696-238">**Filter rules in code**</span></span>

<span data-ttu-id="9b696-239">你可以在代码中，注册的筛选规则，如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="9b696-239">You can register filter rules in code, as shown in the following example:</span></span>

[!code-csharp[](logging/sample2/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="9b696-240">第二个`AddFilter`指定调试提供程序通过使用其类型名称。</span><span class="sxs-lookup"><span data-stu-id="9b696-240">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="9b696-241">第一个`AddFilter`适用于所有提供程序，因为它未指定提供程序类型。</span><span class="sxs-lookup"><span data-stu-id="9b696-241">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

<span data-ttu-id="9b696-242">**如何筛选规则将应用**</span><span class="sxs-lookup"><span data-stu-id="9b696-242">**How filtering rules are applied**</span></span>

<span data-ttu-id="9b696-243">配置数据和`AddFilter`在上面的示例所示的代码创建下表中所示的规则。</span><span class="sxs-lookup"><span data-stu-id="9b696-243">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="9b696-244">前六个来自配置示例，并且最后两个来自此代码示例。</span><span class="sxs-lookup"><span data-stu-id="9b696-244">The first six come from the configuration example and the last two come from the code example.</span></span>

<span data-ttu-id="9b696-245">数字</span><span class="sxs-lookup"><span data-stu-id="9b696-245">Number</span></span>|<span data-ttu-id="9b696-246">提供程序</span><span class="sxs-lookup"><span data-stu-id="9b696-246">Provider</span></span>|<span data-ttu-id="9b696-247">开头的类别</span><span class="sxs-lookup"><span data-stu-id="9b696-247">Categories that begin with</span></span>|<span data-ttu-id="9b696-248">最小日志级别</span><span class="sxs-lookup"><span data-stu-id="9b696-248">Minimum log level</span></span>|
------|--------|--------------------------|-----------------|
<span data-ttu-id="9b696-249">1</span><span class="sxs-lookup"><span data-stu-id="9b696-249">1</span></span>|<span data-ttu-id="9b696-250">调试</span><span class="sxs-lookup"><span data-stu-id="9b696-250">Debug</span></span>|<span data-ttu-id="9b696-251">全部类别</span><span class="sxs-lookup"><span data-stu-id="9b696-251">All categories</span></span>|<span data-ttu-id="9b696-252">信息</span><span class="sxs-lookup"><span data-stu-id="9b696-252">Information</span></span>|
<span data-ttu-id="9b696-253">2</span><span class="sxs-lookup"><span data-stu-id="9b696-253">2</span></span>|<span data-ttu-id="9b696-254">控制台</span><span class="sxs-lookup"><span data-stu-id="9b696-254">Console</span></span>|<span data-ttu-id="9b696-255">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="9b696-255">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span>|<span data-ttu-id="9b696-256">警告</span><span class="sxs-lookup"><span data-stu-id="9b696-256">Warning</span></span>|
<span data-ttu-id="9b696-257">3</span><span class="sxs-lookup"><span data-stu-id="9b696-257">3</span></span>|<span data-ttu-id="9b696-258">控制台</span><span class="sxs-lookup"><span data-stu-id="9b696-258">Console</span></span>|<span data-ttu-id="9b696-259">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="9b696-259">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>|<span data-ttu-id="9b696-260">调试</span><span class="sxs-lookup"><span data-stu-id="9b696-260">Debug</span></span>|
<span data-ttu-id="9b696-261">4</span><span class="sxs-lookup"><span data-stu-id="9b696-261">4</span></span>|<span data-ttu-id="9b696-262">控制台</span><span class="sxs-lookup"><span data-stu-id="9b696-262">Console</span></span>|<span data-ttu-id="9b696-263">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="9b696-263">Microsoft.AspNetCore.Mvc.Razor</span></span>|<span data-ttu-id="9b696-264">错误</span><span class="sxs-lookup"><span data-stu-id="9b696-264">Error</span></span>|
<span data-ttu-id="9b696-265">5</span><span class="sxs-lookup"><span data-stu-id="9b696-265">5</span></span>|<span data-ttu-id="9b696-266">控制台</span><span class="sxs-lookup"><span data-stu-id="9b696-266">Console</span></span>|<span data-ttu-id="9b696-267">全部类别</span><span class="sxs-lookup"><span data-stu-id="9b696-267">All categories</span></span>|<span data-ttu-id="9b696-268">信息</span><span class="sxs-lookup"><span data-stu-id="9b696-268">Information</span></span>|
<span data-ttu-id="9b696-269">6</span><span class="sxs-lookup"><span data-stu-id="9b696-269">6</span></span>|<span data-ttu-id="9b696-270">所有提供程序</span><span class="sxs-lookup"><span data-stu-id="9b696-270">All providers</span></span>|<span data-ttu-id="9b696-271">全部类别</span><span class="sxs-lookup"><span data-stu-id="9b696-271">All categories</span></span>|<span data-ttu-id="9b696-272">调试</span><span class="sxs-lookup"><span data-stu-id="9b696-272">Debug</span></span>
<span data-ttu-id="9b696-273">7</span><span class="sxs-lookup"><span data-stu-id="9b696-273">7</span></span>|<span data-ttu-id="9b696-274">所有提供程序</span><span class="sxs-lookup"><span data-stu-id="9b696-274">All providers</span></span>|<span data-ttu-id="9b696-275">系统</span><span class="sxs-lookup"><span data-stu-id="9b696-275">System</span></span>|<span data-ttu-id="9b696-276">调试</span><span class="sxs-lookup"><span data-stu-id="9b696-276">Debug</span></span>
<span data-ttu-id="9b696-277">8</span><span class="sxs-lookup"><span data-stu-id="9b696-277">8</span></span>|<span data-ttu-id="9b696-278">调试</span><span class="sxs-lookup"><span data-stu-id="9b696-278">Debug</span></span>|<span data-ttu-id="9b696-279">Microsoft</span><span class="sxs-lookup"><span data-stu-id="9b696-279">Microsoft</span></span>|<span data-ttu-id="9b696-280">跟踪</span><span class="sxs-lookup"><span data-stu-id="9b696-280">Trace</span></span>

<span data-ttu-id="9b696-281">当你创建`ILogger`要写入日志，对象`ILoggerFactory`对象选择每个提供程序将应用于该记录器一条规则。</span><span class="sxs-lookup"><span data-stu-id="9b696-281">When you create an `ILogger` object to write logs with, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="9b696-282">由该编写的所有消息`ILogger`根据选定的规则筛选对象。</span><span class="sxs-lookup"><span data-stu-id="9b696-282">All messages written by that `ILogger` object are filtered based on the selected rules.</span></span> <span data-ttu-id="9b696-283">从可用的规则选择类别对每个提供程序以及可能最特定的规则。</span><span class="sxs-lookup"><span data-stu-id="9b696-283">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="9b696-284">为每个提供程序，使用以下算法时`ILogger`创建给定类别：</span><span class="sxs-lookup"><span data-stu-id="9b696-284">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="9b696-285">选择与提供程序或其别名匹配的所有规则。</span><span class="sxs-lookup"><span data-stu-id="9b696-285">Select all rules that match the provider or its alias.</span></span>  <span data-ttu-id="9b696-286">如果找不到之后，请使用空提供程序选择的所有规则。</span><span class="sxs-lookup"><span data-stu-id="9b696-286">If none are found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="9b696-287">从前面的步骤的结果，选择规则最长匹配类别前缀。</span><span class="sxs-lookup"><span data-stu-id="9b696-287">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="9b696-288">如果找不到之后，选择不指定类别的所有规则。</span><span class="sxs-lookup"><span data-stu-id="9b696-288">If none are found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="9b696-289">如果选择多个规则需要**最后一个**一个。</span><span class="sxs-lookup"><span data-stu-id="9b696-289">If multiple rules are selected take the **last** one.</span></span>
* <span data-ttu-id="9b696-290">如果选择没有规则时，使用`MinimumLevel`。</span><span class="sxs-lookup"><span data-stu-id="9b696-290">If no rules are selected, use `MinimumLevel`.</span></span>
 
<span data-ttu-id="9b696-291">例如，假设你没有前面列出的规则，并且你创建`ILogger`类别"Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine"的对象：</span><span class="sxs-lookup"><span data-stu-id="9b696-291">For example, suppose you have the preceding list of rules and you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="9b696-292">调试提供程序，将应用规则 1、 6 和 8。</span><span class="sxs-lookup"><span data-stu-id="9b696-292">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="9b696-293">规则 8 是最具体的这就是选择。</span><span class="sxs-lookup"><span data-stu-id="9b696-293">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="9b696-294">控制台提供程序，将应用规则 3、 4、 5 和 6。</span><span class="sxs-lookup"><span data-stu-id="9b696-294">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="9b696-295">规则 3 是最具体。</span><span class="sxs-lookup"><span data-stu-id="9b696-295">Rule 3 is most specific.</span></span>

<span data-ttu-id="9b696-296">当你创建的日志与`ILogger`类别"Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine"，日志的`Trace`级别和更高版本将转到的调试提供程序和日志`Debug`级别和更高版本将转到控制台提供程序。</span><span class="sxs-lookup"><span data-stu-id="9b696-296">When you create logs with an `ILogger` for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine", logs of `Trace` level and above will go to the Debug provider, and logs of `Debug` level and above will go to the Console provider.</span></span>

<span data-ttu-id="9b696-297">**提供程序别名**</span><span class="sxs-lookup"><span data-stu-id="9b696-297">**Provider aliases**</span></span>

<span data-ttu-id="9b696-298">可以使用的类型名称来指定提供程序在配置中，但每个提供程序定义短*别名*即使用起来更为简便。</span><span class="sxs-lookup"><span data-stu-id="9b696-298">You can use the type name to specify a provider in configuration, but each provider defines a shorter *alias* that is easier to use.</span></span> <span data-ttu-id="9b696-299">对于内置提供程序，使用以下别名：</span><span class="sxs-lookup"><span data-stu-id="9b696-299">For the built-in providers, use the following aliases:</span></span>

- <span data-ttu-id="9b696-300">控制台</span><span class="sxs-lookup"><span data-stu-id="9b696-300">Console</span></span>
- <span data-ttu-id="9b696-301">调试</span><span class="sxs-lookup"><span data-stu-id="9b696-301">Debug</span></span>
- <span data-ttu-id="9b696-302">事件日志</span><span class="sxs-lookup"><span data-stu-id="9b696-302">EventLog</span></span>
- <span data-ttu-id="9b696-303">AzureAppServices</span><span class="sxs-lookup"><span data-stu-id="9b696-303">AzureAppServices</span></span>
- <span data-ttu-id="9b696-304">TraceSource</span><span class="sxs-lookup"><span data-stu-id="9b696-304">TraceSource</span></span>
- <span data-ttu-id="9b696-305">EventSource</span><span class="sxs-lookup"><span data-stu-id="9b696-305">EventSource</span></span>

<span data-ttu-id="9b696-306">**默认最小级别**</span><span class="sxs-lookup"><span data-stu-id="9b696-306">**Default minimum level**</span></span>

<span data-ttu-id="9b696-307">没有从配置或代码的任何规则不适用于给定的提供程序和类别的情况下，才会生效的最低级别设置。</span><span class="sxs-lookup"><span data-stu-id="9b696-307">There is a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="9b696-308">下面的示例演示如何设置的最小级别：</span><span class="sxs-lookup"><span data-stu-id="9b696-308">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](logging/sample2/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="9b696-309">如果未显式设置的最低级别，则默认值是`Information`，这意味着，`Trace`和`Debug`日志将被忽略。</span><span class="sxs-lookup"><span data-stu-id="9b696-309">IF you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

<span data-ttu-id="9b696-310">**筛选器函数**</span><span class="sxs-lookup"><span data-stu-id="9b696-310">**Filter functions**</span></span>

<span data-ttu-id="9b696-311">你可以在应用筛选规则的筛选器函数中编写代码。</span><span class="sxs-lookup"><span data-stu-id="9b696-311">You can write code in a filter function to apply filtering rules.</span></span> <span data-ttu-id="9b696-312">筛选器函数调用的所有提供程序和没有分配给他们通过配置或代码的规则的类别。</span><span class="sxs-lookup"><span data-stu-id="9b696-312">A filter function is invoked for all providers and categories that do not have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="9b696-313">函数中的代码有权访问提供程序类型、 类别和日志级别，以决定应记录一条消息。</span><span class="sxs-lookup"><span data-stu-id="9b696-313">Code in the function has access to the provider type, category, and log level to decide whether or not a message should be logged.</span></span> <span data-ttu-id="9b696-314">例如: </span><span class="sxs-lookup"><span data-stu-id="9b696-314">For example:</span></span>

[!code-csharp[](logging/sample2/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="9b696-315">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="9b696-315">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="9b696-316">某些日志记录提供程序，你可以指定当写入到存储媒体或忽略日志基于日志级别和类别。</span><span class="sxs-lookup"><span data-stu-id="9b696-316">Some logging providers let you specify when logs should be written to a storage medium or ignored based on log level and category.</span></span>

<span data-ttu-id="9b696-317">`AddConsole`和`AddDebug`扩展方法提供重载，可以在筛选条件将传递。</span><span class="sxs-lookup"><span data-stu-id="9b696-317">The `AddConsole` and `AddDebug` extension methods provide overloads that let you pass in filtering criteria.</span></span> <span data-ttu-id="9b696-318">下面的示例代码会导致控制台提供程序，若要忽略日志下面`Warning`级别，而调试提供程序将忽略框架创建的日志。</span><span class="sxs-lookup"><span data-stu-id="9b696-318">The following sample code causes the console provider to ignore logs below `Warning` level, while the Debug provider ignores logs that the framework creates.</span></span>

[!code-csharp[](logging/sample/Startup.cs?name=snippet_AddConsoleAndDebugWithFilter&highlight=6-7)]

<span data-ttu-id="9b696-319">`AddEventLog`方法具有一个重载采用`EventLogSettings`实例，其中可能包含中的筛选函数其`Filter`属性。</span><span class="sxs-lookup"><span data-stu-id="9b696-319">The `AddEventLog` method has an overload that takes an `EventLogSettings` instance, which may contain a filtering function in its `Filter` property.</span></span> <span data-ttu-id="9b696-320">TraceSource 提供程序不提供任何这些重载，因为其日志记录级别和其他参数基于`SourceSwitch`和`TraceListener`它使用。</span><span class="sxs-lookup"><span data-stu-id="9b696-320">The TraceSource provider does not provide any of those overloads, since its logging level and other parameters are based on the  `SourceSwitch` and `TraceListener` it uses.</span></span>

<span data-ttu-id="9b696-321">你可以设置对所有提供程序与注册的筛选规则`ILoggerFactory`实例使用`WithFilter`扩展方法。</span><span class="sxs-lookup"><span data-stu-id="9b696-321">You can set filtering rules for all providers that are registered with an `ILoggerFactory` instance by using the `WithFilter` extension method.</span></span> <span data-ttu-id="9b696-322">下面的示例限制同时允许在调试级别的应用程序日志的警告的 framework 日志 （类别以"Microsoft"或"System"）。</span><span class="sxs-lookup"><span data-stu-id="9b696-322">The example below limits framework logs (category begins with "Microsoft" or "System") to warnings while letting the app log at debug level.</span></span>

[!code-csharp[](logging/sample/Startup.cs?name=snippet_FactoryFilter&highlight=6-11)]

<span data-ttu-id="9b696-323">如果你想要使用筛选来阻止从正在写入的特定类别的所有日志，你可以指定`LogLevel.None`作为该类别的最小日志级别。</span><span class="sxs-lookup"><span data-stu-id="9b696-323">If you want to use filtering to prevent all logs from being written for a particular category, you can specify `LogLevel.None` as the minimum log level for that category.</span></span> <span data-ttu-id="9b696-324">整数值`LogLevel.None`为 6，即高于`LogLevel.Critical`(5)。</span><span class="sxs-lookup"><span data-stu-id="9b696-324">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="9b696-325">`WithFilter`提供扩展方法是通过[Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="9b696-325">The `WithFilter` extension method is provided by the [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) NuGet package.</span></span> <span data-ttu-id="9b696-326">该方法返回一个新`ILoggerFactory`将筛选日志消息传递到所有记录器提供程序的实例注册到它。</span><span class="sxs-lookup"><span data-stu-id="9b696-326">The method returns a new `ILoggerFactory` instance that will filter the log messages passed to all logger providers registered with it.</span></span> <span data-ttu-id="9b696-327">它不影响任何其他`ILoggerFactory`实例，包括原始`ILoggerFactory`实例。</span><span class="sxs-lookup"><span data-stu-id="9b696-327">It does not affect any other `ILoggerFactory` instances, including the original `ILoggerFactory` instance.</span></span>

---

## <a name="log-scopes"></a><span data-ttu-id="9b696-328">日志作用域</span><span class="sxs-lookup"><span data-stu-id="9b696-328">Log scopes</span></span>

<span data-ttu-id="9b696-329">你可以将一组内的逻辑操作的*作用域*以便将相同的数据附加到该集的一部分创建的每个日志。</span><span class="sxs-lookup"><span data-stu-id="9b696-329">You can group a set of logical operations within a *scope* in order to attach the same data to each log that is created as part of that set.</span></span>  <span data-ttu-id="9b696-330">例如，你可能想处理包含事务 id。 要使事务的一部分创建的每个日志</span><span class="sxs-lookup"><span data-stu-id="9b696-330">For example, you might want every log created as part of processing a transaction to include the transaction ID.</span></span>

<span data-ttu-id="9b696-331">作用域是`IDisposable`返回的类型的`ILogger.BeginScope<TState>`方法且持续，直至释放它为止。</span><span class="sxs-lookup"><span data-stu-id="9b696-331">A scope is an `IDisposable` type that is returned by the `ILogger.BeginScope<TState>` method and lasts until it is disposed.</span></span> <span data-ttu-id="9b696-332">由包装你记录器调用中使用的作用域`using`块，如下所示：</span><span class="sxs-lookup"><span data-stu-id="9b696-332">You use a scope by wrapping your logger calls in a `using` block, as shown here:</span></span>

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="9b696-333">下面的代码使控制台提供程序的作用域：</span><span class="sxs-lookup"><span data-stu-id="9b696-333">The following code enables scopes for the console provider:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="9b696-334">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="9b696-334">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="9b696-335">在*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9b696-335">In *Program.cs*:</span></span>

[!code-csharp[](logging/sample2/Program.cs?name=snippet_Scopes&highlight=4)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="9b696-336">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="9b696-336">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="9b696-337">在*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9b696-337">In *Startup.cs*:</span></span>

[!code-csharp[](logging/sample/Startup.cs?name=snippet_Scopes&highlight=6)]

---

<span data-ttu-id="9b696-338">每条日志消息包括指定了作用域的信息：</span><span class="sxs-lookup"><span data-stu-id="9b696-338">Each log message includes the scoped information:</span></span>

```
info: TodoApi.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="9b696-339">内置日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="9b696-339">Built-in logging providers</span></span>

<span data-ttu-id="9b696-340">ASP.NET 核心附带以下提供程序：</span><span class="sxs-lookup"><span data-stu-id="9b696-340">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="9b696-341">控制台</span><span class="sxs-lookup"><span data-stu-id="9b696-341">Console</span></span>](#console)
* [<span data-ttu-id="9b696-342">调试</span><span class="sxs-lookup"><span data-stu-id="9b696-342">Debug</span></span>](#debug)
* [<span data-ttu-id="9b696-343">EventSource</span><span class="sxs-lookup"><span data-stu-id="9b696-343">EventSource</span></span>](#eventsource)
* [<span data-ttu-id="9b696-344">EventLog</span><span class="sxs-lookup"><span data-stu-id="9b696-344">EventLog</span></span>](#eventlog)
* [<span data-ttu-id="9b696-345">TraceSource</span><span class="sxs-lookup"><span data-stu-id="9b696-345">TraceSource</span></span>](#tracesource)
* [<span data-ttu-id="9b696-346">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="9b696-346">Azure App Service</span></span>](#appservice)

<a id="console"></a>
### <a name="the-console-provider"></a><span data-ttu-id="9b696-347">控制台提供程序</span><span class="sxs-lookup"><span data-stu-id="9b696-347">The console provider</span></span>

<span data-ttu-id="9b696-348">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console)提供程序包将日志输出发送到控制台。</span><span class="sxs-lookup"><span data-stu-id="9b696-348">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="9b696-349">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="9b696-349">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
logging.AddConsole()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="9b696-350">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="9b696-350">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
loggerFactory.AddConsole()
```

<span data-ttu-id="9b696-351">[AddConsole 重载](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.consoleloggerextensions)让中传递的最小日志级别、 筛选器函数和一个布尔值，该值指示是否支持作用域。</span><span class="sxs-lookup"><span data-stu-id="9b696-351">[AddConsole overloads](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.consoleloggerextensions) let you pass in an a minimum log level, a filter function, and a boolean that indicates whether scopes are supported.</span></span>  <span data-ttu-id="9b696-352">另一个选项是传入`IConfiguration`对象，可以指定作用域支持和日志记录级别。</span><span class="sxs-lookup"><span data-stu-id="9b696-352">Another option is to pass in an `IConfiguration` object, which can specify scopes support and logging levels.</span></span> 

<span data-ttu-id="9b696-353">如果你正在考虑在生产中使用控制台提供程序，请注意它对性能产生重大影响。</span><span class="sxs-lookup"><span data-stu-id="9b696-353">If you are considering the console provider for use in production, be aware that it has a significant impact on performance.</span></span>

<span data-ttu-id="9b696-354">在 Visual Studio 中，创建新项目时`AddConsole`方法如下所示：</span><span class="sxs-lookup"><span data-stu-id="9b696-354">When you create a new project in Visual Studio, the `AddConsole` method looks like this:</span></span>

```csharp
loggerFactory.AddConsole(Configuration.GetSection("Logging"));
```

<span data-ttu-id="9b696-355">此代码是指`Logging`部分*appSettings.json*文件：</span><span class="sxs-lookup"><span data-stu-id="9b696-355">This code refers to the `Logging` section of the *appSettings.json* file:</span></span>

[!code-json[](logging/sample//appsettings.json)]

<span data-ttu-id="9b696-356">允许应用程序时显示警告的限制 framework 日志的设置，以中所述，在调试级别记录[日志筛选](#log-filtering)部分。</span><span class="sxs-lookup"><span data-stu-id="9b696-356">The settings shown limit framework logs to warnings while allowing the app to log at debug level, as explained in the [Log filtering](#log-filtering) section.</span></span> <span data-ttu-id="9b696-357">有关详细信息，请参阅[配置](configuration.md)。</span><span class="sxs-lookup"><span data-stu-id="9b696-357">For more information, see [Configuration](configuration.md).</span></span>

---

<a id="debug"></a>
### <a name="the-debug-provider"></a><span data-ttu-id="9b696-358">调试提供程序</span><span class="sxs-lookup"><span data-stu-id="9b696-358">The Debug provider</span></span>

<span data-ttu-id="9b696-359">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug)提供程序包通过使用写入日志输出[System.Diagnostics.Debug](https://docs.microsoft.com/dotnet/core/api/system.diagnostics.debug#System_Diagnostics_Debug)类 (`Debug.WriteLine`方法调用)。</span><span class="sxs-lookup"><span data-stu-id="9b696-359">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](https://docs.microsoft.com/dotnet/core/api/system.diagnostics.debug#System_Diagnostics_Debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="9b696-360">在 Linux 上，此提供程序写入的日志传输到*/var/log/message*。</span><span class="sxs-lookup"><span data-stu-id="9b696-360">On Linux, this provider writes logs to */var/log/message*.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="9b696-361">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="9b696-361">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
logging.AddDebug()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="9b696-362">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="9b696-362">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
loggerFactory.AddDebug()
```

<span data-ttu-id="9b696-363">[AddDebug 重载](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.debugloggerfactoryextensions)让你传入的最小日志级别或筛选器函数。</span><span class="sxs-lookup"><span data-stu-id="9b696-363">[AddDebug overloads](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.debugloggerfactoryextensions) let you pass in a minimum log level or a filter function.</span></span>

---

<a id="eventsource"></a>
### <a name="the-eventsource-provider"></a><span data-ttu-id="9b696-364">EventSource 提供程序</span><span class="sxs-lookup"><span data-stu-id="9b696-364">The EventSource provider</span></span>

<span data-ttu-id="9b696-365">对于面向 ASP.NET Core 1.1.0 应用，或更高版本， [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource)提供程序包可以实现事件跟踪。</span><span class="sxs-lookup"><span data-stu-id="9b696-365">For apps that target ASP.NET Core 1.1.0 or higher, the [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package can implement event tracing.</span></span> <span data-ttu-id="9b696-366">在 Windows 上，它使用[ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)。</span><span class="sxs-lookup"><span data-stu-id="9b696-366">On Windows, it uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span> <span data-ttu-id="9b696-367">提供程序是跨平台的但不有任何事件收集和显示尚未为 Linux 或 macOS 的工具。</span><span class="sxs-lookup"><span data-stu-id="9b696-367">The provider is cross-platform, but there are no event collection and display tools yet for Linux or macOS.</span></span> 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="9b696-368">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="9b696-368">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
logging.AddEventSourceLogger()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="9b696-369">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="9b696-369">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
loggerFactory.AddEventSourceLogger()
```

---

<span data-ttu-id="9b696-370">收集并查看日志一种好方法是使用[PerfView 实用工具](https://www.microsoft.com/download/details.aspx?id=28567)。</span><span class="sxs-lookup"><span data-stu-id="9b696-370">A good way to collect and view logs is to use the [PerfView utility](https://www.microsoft.com/download/details.aspx?id=28567).</span></span> <span data-ttu-id="9b696-371">有其他工具来查看 ETW 日志，但在 PerfView 提供用于处理由 ASP.NET 发出的 ETW 事件的最佳体验。</span><span class="sxs-lookup"><span data-stu-id="9b696-371">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET.</span></span> 

<span data-ttu-id="9b696-372">若要配置 PerfView 收集事件所记录的此提供程序，请将字符串添加`*Microsoft-Extensions-Logging`到**其他提供程序**列表。</span><span class="sxs-lookup"><span data-stu-id="9b696-372">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="9b696-373">（未遗漏星号字符串的开头。）</span><span class="sxs-lookup"><span data-stu-id="9b696-373">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview 其他提供程序](logging/_static/perfview-additional-providers.png)

<span data-ttu-id="9b696-375">捕获 Nano 服务器上的事件需要进行一些其他设置：</span><span class="sxs-lookup"><span data-stu-id="9b696-375">Capturing events on Nano Server requires some additional setup:</span></span>

* <span data-ttu-id="9b696-376">将 PowerShell 远程处理连接到 Nano Server:</span><span class="sxs-lookup"><span data-stu-id="9b696-376">Connect PowerShell remoting to the Nano Server:</span></span>

  ```powershell
  Enter-PSSession [name]
  ```

* <span data-ttu-id="9b696-377">创建的 ETW 会话：</span><span class="sxs-lookup"><span data-stu-id="9b696-377">Create an ETW session:</span></span>

  ```powershell
  New-EtwTraceSession -Name "MyAppTrace" -LocalFilePath C:\trace.etl
  ```

* <span data-ttu-id="9b696-378">添加 ETW 提供程序[CLR](https://docs.microsoft.com/dotnet/framework/performance/clr-etw-providers)，ASP.NET 核心和其他根据需要。</span><span class="sxs-lookup"><span data-stu-id="9b696-378">Add ETW providers for [CLR](https://docs.microsoft.com/dotnet/framework/performance/clr-etw-providers), ASP.NET Core, and others as needed.</span></span> <span data-ttu-id="9b696-379">ASP.NET 核心提供程序 GUID 是`3ac73b97-af73-50e9-0822-5da4367920d0`。</span><span class="sxs-lookup"><span data-stu-id="9b696-379">The ASP.NET Core provider GUID is `3ac73b97-af73-50e9-0822-5da4367920d0`.</span></span> 

  ```powershell
  Add-EtwTraceProvider -Guid "{e13c0d23-ccbc-4e12-931b-d9cc2eee27e4}" -SessionName MyAppTrace
  Add-EtwTraceProvider -Guid "{3ac73b97-af73-50e9-0822-5da4367920d0}" -SessionName MyAppTrace
  ```

* <span data-ttu-id="9b696-380">运行该站点，并执行任何操作所需跟踪信息。</span><span class="sxs-lookup"><span data-stu-id="9b696-380">Run the site and do whatever actions you want tracing information for.</span></span>

* <span data-ttu-id="9b696-381">停止跟踪会话完成后：</span><span class="sxs-lookup"><span data-stu-id="9b696-381">Stop the tracing session when you're finished:</span></span>

  ```powershell
  Stop-EtwTraceSession -Name "MyAppTrace"
  ```

<span data-ttu-id="9b696-382">生成*C:\trace.etl*上其他版本的 Windows 可以使用 PerfView 分析文件。</span><span class="sxs-lookup"><span data-stu-id="9b696-382">The resulting *C:\trace.etl* file can be analyzed with PerfView as on other editions of Windows.</span></span>

<a id="eventlog"></a>
### <a name="the-windows-eventlog-provider"></a><span data-ttu-id="9b696-383">Windows 事件日志提供程序</span><span class="sxs-lookup"><span data-stu-id="9b696-383">The Windows EventLog provider</span></span>

<span data-ttu-id="9b696-384">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog)提供程序包将日志输出发送到 Windows 事件日志中。</span><span class="sxs-lookup"><span data-stu-id="9b696-384">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="9b696-385">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="9b696-385">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
logging.AddEventLog()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="9b696-386">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="9b696-386">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
loggerFactory.AddEventLog()
```

<span data-ttu-id="9b696-387">[AddEventLog 重载](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.eventloggerfactoryextensions)让你传入`EventLogSettings`或最小日志级别。</span><span class="sxs-lookup"><span data-stu-id="9b696-387">[AddEventLog overloads](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.eventloggerfactoryextensions) let you pass in `EventLogSettings` or a minimum log level.</span></span>

---

<a id="tracesource"></a>
### <a name="the-tracesource-provider"></a><span data-ttu-id="9b696-388">TraceSource 提供程序</span><span class="sxs-lookup"><span data-stu-id="9b696-388">The TraceSource provider</span></span>

<span data-ttu-id="9b696-389">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource)提供程序包使用[System.Diagnostics.TraceSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracesource)库和提供程序。</span><span class="sxs-lookup"><span data-stu-id="9b696-389">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the [System.Diagnostics.TraceSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracesource) libraries and providers.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="9b696-390">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="9b696-390">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
logging.AddTraceSource(sourceSwitchName);
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="9b696-391">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="9b696-391">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
loggerFactory.AddTraceSource(sourceSwitchName);
```

---

<span data-ttu-id="9b696-392">[AddTraceSource 重载](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.tracesourcefactoryextensions)使传入一个源开关和跟踪侦听器。</span><span class="sxs-lookup"><span data-stu-id="9b696-392">[AddTraceSource overloads](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.tracesourcefactoryextensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="9b696-393">若要使用此提供程序，应用程序必须在.NET Framework （而不是.NET Core） 上运行。</span><span class="sxs-lookup"><span data-stu-id="9b696-393">To use this provider, an application has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="9b696-394">使用提供程序，可以将消息路由到各种[侦听器](https://docs.microsoft.com/dotnet/framework/debug-trace-profile/trace-listeners)，如[TextWriterTraceListener](https://docs.microsoft.com/dotnet/api/system.diagnostics.textwritertracelistenerr)示例应用程序中使用。</span><span class="sxs-lookup"><span data-stu-id="9b696-394">The provider lets you route messages to a variety of [listeners](https://docs.microsoft.com/dotnet/framework/debug-trace-profile/trace-listeners), such as the [TextWriterTraceListener](https://docs.microsoft.com/dotnet/api/system.diagnostics.textwritertracelistenerr) used in the sample application.</span></span>

<span data-ttu-id="9b696-395">下面的示例将配置`TraceSource`日志的提供程序`Warning`和更高版本到控制台窗口的消息。</span><span class="sxs-lookup"><span data-stu-id="9b696-395">The following example configures a `TraceSource` provider that logs `Warning` and higher messages to the console window.</span></span>

[!code-csharp[](logging/sample/Startup.cs?name=snippet_TraceSource&highlight=9-12)]

<a id="appservice"></a>
### <a name="the-azure-app-service-provider"></a><span data-ttu-id="9b696-396">Azure App Service 提供程序</span><span class="sxs-lookup"><span data-stu-id="9b696-396">The Azure App Service provider</span></span>

<span data-ttu-id="9b696-397">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices)提供程序包将日志写入文本文件 Azure App Service 应用程序的文件系统中和为[blob 存储](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)在 Azure 存储帐户。</span><span class="sxs-lookup"><span data-stu-id="9b696-397">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span> <span data-ttu-id="9b696-398">提供程序是仅适用于应用程序的目标 ASP.NET 核心 1.1.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="9b696-398">The provider is available only for apps that target ASP.NET Core 1.1.0 or higher.</span></span> 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="9b696-399">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="9b696-399">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

> [!NOTE]
> <span data-ttu-id="9b696-400">ASP.NET 核心 2.0 处于预览状态。</span><span class="sxs-lookup"><span data-stu-id="9b696-400">ASP.NET Core 2.0 is in preview.</span></span>  <span data-ttu-id="9b696-401">部署到 Azure App Service 时，可能无法运行的最新预览版本创建的应用。</span><span class="sxs-lookup"><span data-stu-id="9b696-401">Apps created with the latest preview release may not run when deployed to Azure App Service.</span></span> <span data-ttu-id="9b696-402">当发布 ASP.NET 核心 2.0 后时，在 Azure App Service 上运行 2.0 应用程序和 Azure 应用程序服务提供程序将按此处所指示方式工作。</span><span class="sxs-lookup"><span data-stu-id="9b696-402">When ASP.NET Core 2.0 is released, Azure App Service will run 2.0 apps, and the Azure App Service provider will work as indicated here.</span></span>

<span data-ttu-id="9b696-403">无需安装的提供程序包或调用`AddAzureWebAppDiagnostics`扩展方法。</span><span class="sxs-lookup"><span data-stu-id="9b696-403">You don't have to install the provider package or call the `AddAzureWebAppDiagnostics` extension method.</span></span>  <span data-ttu-id="9b696-404">将应用部署到 Azure App Service，该提供程序时自动提供给你的应用程序。</span><span class="sxs-lookup"><span data-stu-id="9b696-404">The provider is automatically available to your app when you deploy the app to Azure App Service.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="9b696-405">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="9b696-405">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
loggerFactory.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="9b696-406">`AddAzureWebAppDiagnostics`重载的允许你传入[AzureAppServicesDiagnosticsSettings](https://github.com/aspnet/Logging/blob/c7d0b1b88668ff4ef8a86ea7d2ebb5ca7f88d3e0/src/Microsoft.Extensions.Logging.AzureAppServices/AzureAppServicesDiagnosticsSettings.cs)，与其重写默认设置，如日志记录输出模板、 blob 名称和文件大小限制。</span><span class="sxs-lookup"><span data-stu-id="9b696-406">An `AddAzureWebAppDiagnostics` overload lets you pass in [AzureAppServicesDiagnosticsSettings](https://github.com/aspnet/Logging/blob/c7d0b1b88668ff4ef8a86ea7d2ebb5ca7f88d3e0/src/Microsoft.Extensions.Logging.AzureAppServices/AzureAppServicesDiagnosticsSettings.cs), with which you can override default settings such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="9b696-407">(*输出模板*是消息格式字符串应用于所有日志，在你提供在调用时`ILogger`方法。)</span><span class="sxs-lookup"><span data-stu-id="9b696-407">(*Output template* is a message format string that is applied to all logs, on top of the one that you provide when you call an `ILogger` method.)</span></span>  

---

<span data-ttu-id="9b696-408">当你将部署到 App Service 应用时，你的应用程序遵循中的设置[诊断日志](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/#enablediag)部分**App Service** Azure 门户页。</span><span class="sxs-lookup"><span data-stu-id="9b696-408">When you deploy to an App Service app, your application honors the settings in the [Diagnostic Logs](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="9b696-409">更改这些设置时，所做的更改会立即生效且无需重新启动应用，或重新部署代码到它。</span><span class="sxs-lookup"><span data-stu-id="9b696-409">When you change those settings, the changes take effect immediately without requiring that you restart the app or redeploy code to it.</span></span> 

![Azure 日志记录设置](logging/_static/azure-logging-settings.png)

<span data-ttu-id="9b696-411">日志文件的默认位置处于*d:\\主\\LogFiles\\应用程序*文件夹，以及默认文件名称是*诊断 yyyymmdd.txt*。</span><span class="sxs-lookup"><span data-stu-id="9b696-411">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="9b696-412">默认文件大小限制为 10 MB，而保留的文件的默认最大数量为 2。</span><span class="sxs-lookup"><span data-stu-id="9b696-412">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="9b696-413">默认 blob 名称是*{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*。</span><span class="sxs-lookup"><span data-stu-id="9b696-413">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span> <span data-ttu-id="9b696-414">有关默认行为的详细信息，请参阅[AzureAppServicesDiagnosticsSettings](https://github.com/aspnet/Logging/blob/c7d0b1b88668ff4ef8a86ea7d2ebb5ca7f88d3e0/src/Microsoft.Extensions.Logging.AzureAppServices/AzureAppServicesDiagnosticsSettings.cs)。</span><span class="sxs-lookup"><span data-stu-id="9b696-414">For more information about default behavior, see [AzureAppServicesDiagnosticsSettings](https://github.com/aspnet/Logging/blob/c7d0b1b88668ff4ef8a86ea7d2ebb5ca7f88d3e0/src/Microsoft.Extensions.Logging.AzureAppServices/AzureAppServicesDiagnosticsSettings.cs).</span></span>

<span data-ttu-id="9b696-415">当在 Azure 环境中运行你的项目时，该提供程序才有效。</span><span class="sxs-lookup"><span data-stu-id="9b696-415">The provider only works when your project runs in the Azure environment.</span></span>  <span data-ttu-id="9b696-416">本地运行时无任何效果&mdash;它不会写入本地文件或 blob 的本地开发存储。</span><span class="sxs-lookup"><span data-stu-id="9b696-416">It has no effect when you run locally &mdash; it does not write to local files or local development storage for blobs.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="9b696-417">第三方日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="9b696-417">Third-party logging providers</span></span>

<span data-ttu-id="9b696-418">以下是使用 ASP.NET Core 某些第三方日志记录框架：</span><span class="sxs-lookup"><span data-stu-id="9b696-418">Here are some third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="9b696-419">[elmah.io](https://github.com/elmahio/Elmah.Io.Extensions.Logging) -Elmah.Io 服务提供程序</span><span class="sxs-lookup"><span data-stu-id="9b696-419">[elmah.io](https://github.com/elmahio/Elmah.Io.Extensions.Logging) - provider for the Elmah.Io service</span></span>

* <span data-ttu-id="9b696-420">[JSNLog](http://jsnlog.com) -JavaScript 异常和其他客户端事件记录在服务器端日志中。</span><span class="sxs-lookup"><span data-stu-id="9b696-420">[JSNLog](http://jsnlog.com) - logs JavaScript exceptions and other client-side events in your server-side log.</span></span>

* <span data-ttu-id="9b696-421">[Loggr](https://github.com/imobile3/Loggr.Extensions.Logging) -Loggr 服务提供程序</span><span class="sxs-lookup"><span data-stu-id="9b696-421">[Loggr](https://github.com/imobile3/Loggr.Extensions.Logging) - provider for the Loggr service</span></span>

* <span data-ttu-id="9b696-422">[NLog](https://github.com/NLog/NLog.Extensions.Logging) -NLog 库的提供程序</span><span class="sxs-lookup"><span data-stu-id="9b696-422">[NLog](https://github.com/NLog/NLog.Extensions.Logging) - provider for the NLog library</span></span>

* <span data-ttu-id="9b696-423">[Serilog](https://github.com/serilog/serilog-extensions-logging) -Serilog 库的提供程序</span><span class="sxs-lookup"><span data-stu-id="9b696-423">[Serilog](https://github.com/serilog/serilog-extensions-logging) - provider for the Serilog library</span></span>

<span data-ttu-id="9b696-424">某些第三方框架可以执行[语义的日志记录，也称为结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="9b696-424">Some third-party frameworks can do [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="9b696-425">使用第三方框架是类似于使用内置提供程序之一： 将 NuGet 包添加到你的项目，对调用扩展方法`ILoggerFactory`。</span><span class="sxs-lookup"><span data-stu-id="9b696-425">Using a third-party framework is similar to using one of the built-in providers: add a NuGet package to your project and call an extension method on `ILoggerFactory`.</span></span> <span data-ttu-id="9b696-426">有关详细信息，请参阅每个框架文档。</span><span class="sxs-lookup"><span data-stu-id="9b696-426">For more information, see each framework's documentation.</span></span>

<span data-ttu-id="9b696-427">你可以创建你自己的自定义提供程序，以支持其他日志记录框架或您自己的日志记录要求。</span><span class="sxs-lookup"><span data-stu-id="9b696-427">You can create your own custom providers as well, to support other logging frameworks or your own logging requirements.</span></span>
