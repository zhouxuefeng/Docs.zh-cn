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
# <a name="introduction-to-logging-in-aspnet-core"></a>在 ASP.NET 核心中日志记录的简介

通过[Steve Smith](https://ardalis.com/)和[Tom Dykstra](https://github.com/tdykstra)

ASP.NET 核心支持适用于各种日志记录提供程序的日志记录 API。 内置提供程序允许你将日志发送到一个或多个目标，而第三方日志记录框架中，您可以插入。 这篇文章演示如何在代码中使用的内置日志记录 API 和提供程序。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[查看或下载的示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/sample2)([如何下载](xref:tutorials/index#how-to-download-a-sample))

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[查看或下载的示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/sample)([如何下载](xref:tutorials/index#how-to-download-a-sample))

---

## <a name="how-to-create-logs"></a>如何创建日志

若要创建日志，获取`ILogger`对象[依赖关系注入](dependency-injection.md)容器：

[!code-csharp[](logging/sample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

然后该记录器对象调用日志记录方法：

[!code-csharp[](logging/sample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

此示例将创建日志与`TodoController`类作为*类别*。  目录将介绍[本文稍后的](#log-category)。

ASP.NET 核心不提供异步记录器方法因为日志记录应该速度较快，不值得使用异步的成本。 如果你在其中，不为 true 的情况下，请考虑更改你记录的方式。  如果数据存储区较慢，首先，将日志消息写入快速存储，则稍后将它们移至慢速存储。 例如，登录到的消息队列中读取并保存到慢速存储由另一个进程。

## <a name="how-to-add-providers"></a>如何添加提供程序

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

日志记录提供程序采用与创建的消息`ILogger`对象和显示或将其存储。 例如，控制台提供程序显示在控制台中，消息和 Azure 应用程序服务提供商可以将其存储在 Azure blob 存储。

若要使用提供程序，调用提供程序的`Add<ProviderName>`中的扩展方法*Program.cs*:

[!code-csharp[](logging/sample2/Program.cs?name=snippet_ExpandDefault&highlight=16,17)]

默认项目模板将设置日志记录的方式，你看到它在上面的代码，但`ConfigureLogging`调用可通过`CreateDefaultBuilder`方法。 以下是的代码*Program.cs* ，它由项目模板创建：

[!code-csharp[](logging/sample2/Program.cs?name=snippet_TemplateCode&highlight=7)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

日志记录提供程序采用与创建的消息`ILogger`对象和显示或将其存储。 例如，控制台提供程序显示在控制台中，消息和 Azure 应用程序服务提供商可以将其存储在 Azure blob 存储。

若要使用的提供程序，安装其 NuGet 包和实例上调用提供程序的扩展方法`ILoggerFactory`，下面的示例中所示。

[!code-csharp[](logging/sample//Startup.cs?name=snippet_AddConsoleAndDebug&highlight=3,5-7)]

ASP.NET 核心[依赖关系注入](dependency-injection.md)(DI) 提供`ILoggerFactory`实例。 `AddConsole`和`AddDebug`中定义的扩展方法[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)和[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/)包。 每个扩展方法调用`ILoggerFactory.AddProvider`方法，并传递提供程序实例中。 

> [!NOTE]
> 本文的示例应用程序添加日志记录中的提供商`Configure`方法`Startup`类。 如果你想要从更早版本执行的代码获取日志输出，添加中的日志记录提供程序`Startup`改为类构造函数。 

---

你将找到有关每个信息[内置日志记录提供程序](#built-in-logging-providers)并链接到[第三方日志记录提供程序](#third-party-logging-providers)文章中更高版本。

## <a name="sample-logging-output"></a>日志记录输出示例

与在前面部分所示的示例代码，你将看到在控制台中的日志，从命令行运行时。 下面是控制台输出示例：

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
 
这些日志创建通过转到`http://localhost:5000/api/todo/0`，随即将会触发这两者的执行`ILogger`前面部分中显示的调用。

下面是相同的日志的示例，当在 Visual Studio 中运行示例应用程序在调试窗口中显示：

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404 
```

创建的日志`ILogger`前面部分中显示的调用以"TodoApi.Controllers.TodoController"开头。 开始使用"Microsoft"类别的日志都是从 ASP.NET Core。 ASP.NET 核心本身和应用程序代码正在使用相同的日志记录 API 和相同的日志记录提供程序。

本文的其余部分介绍一些详细信息和日志记录选项。

## <a name="nuget-packages"></a>NuGet 包

`ILogger`和`ILoggerFactory`接口位于[Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)，并为它们的默认实现处于[Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/)。

## <a name="log-category"></a>日志类别

A*类别*附带你创建的每个日志。  在创建时指定类别`ILogger`对象。 类别可以是任意字符串，但一个约定是使用日志将写入从其类的完全限定的名称。  例如:"TodoApi.Controllers.TodoController"。

你可以指定为字符串的类别或使用派生自类型类别的扩展方法。 若要以字符串形式指定的类别，调用`CreateLogger`上`ILoggerFactory`实例，如下所示。

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

大多数情况下，它将是管理更轻松地使用`ILogger<T>`，如下面的示例。

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

这是等效于调用`CreateLogger`的完全限定的类型名称与`T`。

## <a name="log-level"></a>日志级别

每次写入日志时，你指定其[LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel)。 日志级别指示严重性或重要性的程度。  例如，你可以编写`Information`日志方法通常情况下，结束时`Warning`日志方法将返回 404 返回代码，当和`Error`时捕获了意外的异常记录。

在下面的代码示例，方法的名称 (例如， `LogWarning`) 指定的日志级别。  第一个参数是[记录事件 ID](#log-event-id) （本文后面所述）。  剩余的参数构造的日志消息字符串。

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

方法名称中包括的级别的日志方法[ILogger 的扩展方法](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions)。 在后台，这些方法调用`Log`采用的方法`LogLevel`参数。 你可以调用`Log`直接方法，而不是这些扩展方法，但语法之一是相对复杂。 有关详细信息，请参阅[ILogger 接口](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger)和[记录器扩展源代码](https://github.com/aspnet/Logging/blob/master/src/Microsoft.Extensions.Logging.Abstractions/LoggerExtensions.cs)。

ASP.NET 核心定义了以下[日志级别](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel)，从最低到最高严重性此处排序。

* 跟踪 = 0

  仅对于开发人员调试问题有价值的信息。 这些消息可能包含敏感应用程序数据，并因此不应启用在生产环境中。 *默认情况下已禁用。* 示例：`Credentials: {"User":"someuser", "Password":"P@ssword"}`

* 调试 = 1

  有关在开发和调试过程中具有短期用途的信息。 示例：`Entering method Configure with flag set to true.`你通常不会使`Debug`级别记录在生产环境中，除非你正在排查的因为数量过多的日志。

* 信息 = 2

  用于跟踪应用程序的常规流。 这些日志通常包含一些长期的值。 示例：`Request received for path /api/todo`

* 警告 = 3

  在应用程序流中的异常或意外事件。 其中可能包括错误或其他条件，不会导致应用程序停止，但这可能需要进行调查。 处理的异常是要使用的一个常见位置`Warning`日志级别。 示例：`FileNotFoundException for file quotes.txt.`

* 错误 = 4

  错误和不能处理的异常。 这些邮件表示当前的活动或操作 （如当前 HTTP 请求） 中失败，则不应用程序范围失败。 示例日志消息：`Cannot insert record due to duplicate key violation.`

* 关键 = 5

  需要立即关注的故障。 示例： 数据丢失的情况，磁盘空间不足。

日志级别可用于控制多少日志输出写入到特定的存储介质或显示窗口。 例如，在生产环境中可能需要的所有日志`Information`级别并降低转到卷数据存储区和的所有日志`Warning`级别和更高版本以转到值数据存储区。 在开发期间，你可能通常发送的日志`Warning`或更高严重性到控制台。 然后，当你需要进行故障排除，可以将添加`Debug`级别。 [日志筛选](#log-filtering)本文后面的部分介绍如何控制提供程序处理的日志级别。

ASP.NET 核心框架写入`Debug`级别 framework 事件日志。 日志示例前面在本文中排除以下日志`Information`级别，因此没有`Debug`级别的日志中所示。 以下是控制台日志的示例，如果运行示例应用程序配置为显示`Debug`和更高版本日志中的控制台提供程序。

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

## <a name="log-event-id"></a>日志事件 ID

每次写入日志时，你可以指定*事件 ID*。 通过使用本地定义的示例应用程序执行上述`LoggingEvents`类：

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](logging/sample//Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

事件 ID 是一个整数值，可用于将一组记录的事件与另一个相关联。 例如的日志，以将项添加到购物车可能是事件 ID 1000，并且可能事件 ID 为 1001年的日志，以完成购买。

在日志记录输出中，可能存储在一个字段或文本消息，具体取决于提供程序中包含的事件 ID。  调试提供程序不会显示事件 Id，但控制台提供程序在显示这些方括号分类之后：

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-format-string"></a>日志消息格式字符串

写入日志中，每次你提供文本消息。 消息字符串可以包含命名的占位符的参数值放在如以下示例所示：

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

占位符，其名称，顺序确定哪些参数用于它们。 例如，如果你有以下代码：

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

生成的日志消息将如下所示：

```
Parameter values: parm1, parm2
```

日志记录框架进行消息中以使日志记录提供程序用来实现这种方式的格式设置[语义的日志记录，也称为结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。 因为参数本身传递到日志记录系统，而不仅仅是格式化的消息字符串，日志记录提供程序可以将参数值存储为除了消息字符串的字段。 例如，如果你将定向到 Azure 表存储，你的日志输出和记录器方法调用如下所示：

```csharp
_logger.LogInformation("Getting item {ID} at {RequestTime}", id, DateTime.Now);
```

每个 Azure 表实体可能具有`ID`和`RequestTime`属性，可以简化对日志数据的查询。 无法查找在特定的所有日志`RequestTime`范围，而无需分析的短信超时时间。

## <a name="logging-exceptions"></a>日志记录异常

记录器方法具有重载，以让你通过引发异常，如以下示例所示：

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

不同的提供程序以不同方式处理的异常信息。 下面是从上面显示的代码的调试提供程序输出示例。

```
TodoApi.Controllers.TodoController:Warning: GetById(036dd898-fb01-47e8-9a65-f92eb73cf924) NOT FOUND

System.Exception: Item not found exception.
 at TodoApi.Controllers.TodoController.GetById(String id) in C:\logging\sample\src\TodoApi\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>日志筛选

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

你可以指定特定的提供程序和类别或对所有提供程序或所有类别的最小日志级别。  因此不要获取显示或存储，与该提供程序，不传递的最低级别上深化任何日志。 

如果你想要禁止显示所有日志，你可以指定`LogLevel.None`作为最小日志级别。 整数值`LogLevel.None`为 6，即高于`LogLevel.Critical`(5)。

**在配置中创建筛选器规则**

项目模板创建调用的代码`CreateDefaultBuilder`设置在控制台和调试提供程序的日志记录。 `CreateDefaultBuilder`方法还设置日志记录以寻找中配置`Logging`部分，使用类似于下面的代码：

[!code-csharp[](logging/sample2/Program.cs?name=snippet_ExpandDefault&highlight=15)]

配置数据指定最小的日志级别由提供商和类别，如以下示例所示：

[!code-json[](logging/sample2/appsettings.json)]

此 JSON 创建六个筛选规则，一个用于调试提供程序、 控制台的提供程序的四个，并且适用于所有提供程序的一个。 你将看到为每个提供程序选择这些规则的更高版本如何只需之一时`ILogger`创建对象。

**在代码中的筛选规则**

你可以在代码中，注册的筛选规则，如下面的示例中所示：

[!code-csharp[](logging/sample2/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

第二个`AddFilter`指定调试提供程序通过使用其类型名称。 第一个`AddFilter`适用于所有提供程序，因为它未指定提供程序类型。

**如何筛选规则将应用**

配置数据和`AddFilter`在上面的示例所示的代码创建下表中所示的规则。 前六个来自配置示例，并且最后两个来自此代码示例。

数字|提供程序|开头的类别|最小日志级别|
------|--------|--------------------------|-----------------|
1|调试|全部类别|信息|
2|控制台|Microsoft.AspNetCore.Mvc.Razor.Internal|警告|
3|控制台|Microsoft.AspNetCore.Mvc.Razor.Razor|调试|
4|控制台|Microsoft.AspNetCore.Mvc.Razor|错误|
5|控制台|全部类别|信息|
6|所有提供程序|全部类别|调试
7|所有提供程序|系统|调试
8|调试|Microsoft|跟踪

当你创建`ILogger`要写入日志，对象`ILoggerFactory`对象选择每个提供程序将应用于该记录器一条规则。 由该编写的所有消息`ILogger`根据选定的规则筛选对象。 从可用的规则选择类别对每个提供程序以及可能最特定的规则。

为每个提供程序，使用以下算法时`ILogger`创建给定类别：

* 选择与提供程序或其别名匹配的所有规则。  如果找不到之后，请使用空提供程序选择的所有规则。
* 从前面的步骤的结果，选择规则最长匹配类别前缀。 如果找不到之后，选择不指定类别的所有规则。
* 如果选择多个规则需要**最后一个**一个。
* 如果选择没有规则时，使用`MinimumLevel`。
 
例如，假设你没有前面列出的规则，并且你创建`ILogger`类别"Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine"的对象：

* 调试提供程序，将应用规则 1、 6 和 8。 规则 8 是最具体的这就是选择。
* 控制台提供程序，将应用规则 3、 4、 5 和 6。 规则 3 是最具体。

当你创建的日志与`ILogger`类别"Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine"，日志的`Trace`级别和更高版本将转到的调试提供程序和日志`Debug`级别和更高版本将转到控制台提供程序。

**提供程序别名**

可以使用的类型名称来指定提供程序在配置中，但每个提供程序定义短*别名*即使用起来更为简便。 对于内置提供程序，使用以下别名：

- 控制台
- 调试
- 事件日志
- AzureAppServices
- TraceSource
- EventSource

**默认最小级别**

没有从配置或代码的任何规则不适用于给定的提供程序和类别的情况下，才会生效的最低级别设置。 下面的示例演示如何设置的最小级别：

[!code-csharp[](logging/sample2/Program.cs?name=snippet_MinLevel&highlight=3)]

如果未显式设置的最低级别，则默认值是`Information`，这意味着，`Trace`和`Debug`日志将被忽略。

**筛选器函数**

你可以在应用筛选规则的筛选器函数中编写代码。 筛选器函数调用的所有提供程序和没有分配给他们通过配置或代码的规则的类别。 函数中的代码有权访问提供程序类型、 类别和日志级别，以决定应记录一条消息。 例如: 

[!code-csharp[](logging/sample2/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

某些日志记录提供程序，你可以指定当写入到存储媒体或忽略日志基于日志级别和类别。

`AddConsole`和`AddDebug`扩展方法提供重载，可以在筛选条件将传递。 下面的示例代码会导致控制台提供程序，若要忽略日志下面`Warning`级别，而调试提供程序将忽略框架创建的日志。

[!code-csharp[](logging/sample/Startup.cs?name=snippet_AddConsoleAndDebugWithFilter&highlight=6-7)]

`AddEventLog`方法具有一个重载采用`EventLogSettings`实例，其中可能包含中的筛选函数其`Filter`属性。 TraceSource 提供程序不提供任何这些重载，因为其日志记录级别和其他参数基于`SourceSwitch`和`TraceListener`它使用。

你可以设置对所有提供程序与注册的筛选规则`ILoggerFactory`实例使用`WithFilter`扩展方法。 下面的示例限制同时允许在调试级别的应用程序日志的警告的 framework 日志 （类别以"Microsoft"或"System"）。

[!code-csharp[](logging/sample/Startup.cs?name=snippet_FactoryFilter&highlight=6-11)]

如果你想要使用筛选来阻止从正在写入的特定类别的所有日志，你可以指定`LogLevel.None`作为该类别的最小日志级别。 整数值`LogLevel.None`为 6，即高于`LogLevel.Critical`(5)。

`WithFilter`提供扩展方法是通过[Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) NuGet 包。 该方法返回一个新`ILoggerFactory`将筛选日志消息传递到所有记录器提供程序的实例注册到它。 它不影响任何其他`ILoggerFactory`实例，包括原始`ILoggerFactory`实例。

---

## <a name="log-scopes"></a>日志作用域

你可以将一组内的逻辑操作的*作用域*以便将相同的数据附加到该集的一部分创建的每个日志。  例如，你可能想处理包含事务 id。 要使事务的一部分创建的每个日志

作用域是`IDisposable`返回的类型的`ILogger.BeginScope<TState>`方法且持续，直至释放它为止。 由包装你记录器调用中使用的作用域`using`块，如下所示：

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

下面的代码使控制台提供程序的作用域：

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

在*Program.cs*:

[!code-csharp[](logging/sample2/Program.cs?name=snippet_Scopes&highlight=4)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

在*Startup.cs*:

[!code-csharp[](logging/sample/Startup.cs?name=snippet_Scopes&highlight=6)]

---

每条日志消息包括指定了作用域的信息：

```
info: TodoApi.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>内置日志记录提供程序

ASP.NET 核心附带以下提供程序：

* [控制台](#console)
* [调试](#debug)
* [EventSource](#eventsource)
* [EventLog](#eventlog)
* [TraceSource](#tracesource)
* [Azure 应用服务](#appservice)

<a id="console"></a>
### <a name="the-console-provider"></a>控制台提供程序

[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console)提供程序包将日志输出发送到控制台。 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
logging.AddConsole()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddConsole()
```

[AddConsole 重载](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.consoleloggerextensions)让中传递的最小日志级别、 筛选器函数和一个布尔值，该值指示是否支持作用域。  另一个选项是传入`IConfiguration`对象，可以指定作用域支持和日志记录级别。 

如果你正在考虑在生产中使用控制台提供程序，请注意它对性能产生重大影响。

在 Visual Studio 中，创建新项目时`AddConsole`方法如下所示：

```csharp
loggerFactory.AddConsole(Configuration.GetSection("Logging"));
```

此代码是指`Logging`部分*appSettings.json*文件：

[!code-json[](logging/sample//appsettings.json)]

允许应用程序时显示警告的限制 framework 日志的设置，以中所述，在调试级别记录[日志筛选](#log-filtering)部分。 有关详细信息，请参阅[配置](configuration.md)。

---

<a id="debug"></a>
### <a name="the-debug-provider"></a>调试提供程序

[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug)提供程序包通过使用写入日志输出[System.Diagnostics.Debug](https://docs.microsoft.com/dotnet/core/api/system.diagnostics.debug#System_Diagnostics_Debug)类 (`Debug.WriteLine`方法调用)。

在 Linux 上，此提供程序写入的日志传输到*/var/log/message*。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
logging.AddDebug()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddDebug()
```

[AddDebug 重载](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.debugloggerfactoryextensions)让你传入的最小日志级别或筛选器函数。

---

<a id="eventsource"></a>
### <a name="the-eventsource-provider"></a>EventSource 提供程序

对于面向 ASP.NET Core 1.1.0 应用，或更高版本， [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource)提供程序包可以实现事件跟踪。 在 Windows 上，它使用[ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)。 提供程序是跨平台的但不有任何事件收集和显示尚未为 Linux 或 macOS 的工具。 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
logging.AddEventSourceLogger()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddEventSourceLogger()
```

---

收集并查看日志一种好方法是使用[PerfView 实用工具](https://www.microsoft.com/download/details.aspx?id=28567)。 有其他工具来查看 ETW 日志，但在 PerfView 提供用于处理由 ASP.NET 发出的 ETW 事件的最佳体验。 

若要配置 PerfView 收集事件所记录的此提供程序，请将字符串添加`*Microsoft-Extensions-Logging`到**其他提供程序**列表。 （未遗漏星号字符串的开头。）

![Perfview 其他提供程序](logging/_static/perfview-additional-providers.png)

捕获 Nano 服务器上的事件需要进行一些其他设置：

* 将 PowerShell 远程处理连接到 Nano Server:

  ```powershell
  Enter-PSSession [name]
  ```

* 创建的 ETW 会话：

  ```powershell
  New-EtwTraceSession -Name "MyAppTrace" -LocalFilePath C:\trace.etl
  ```

* 添加 ETW 提供程序[CLR](https://docs.microsoft.com/dotnet/framework/performance/clr-etw-providers)，ASP.NET 核心和其他根据需要。 ASP.NET 核心提供程序 GUID 是`3ac73b97-af73-50e9-0822-5da4367920d0`。 

  ```powershell
  Add-EtwTraceProvider -Guid "{e13c0d23-ccbc-4e12-931b-d9cc2eee27e4}" -SessionName MyAppTrace
  Add-EtwTraceProvider -Guid "{3ac73b97-af73-50e9-0822-5da4367920d0}" -SessionName MyAppTrace
  ```

* 运行该站点，并执行任何操作所需跟踪信息。

* 停止跟踪会话完成后：

  ```powershell
  Stop-EtwTraceSession -Name "MyAppTrace"
  ```

生成*C:\trace.etl*上其他版本的 Windows 可以使用 PerfView 分析文件。

<a id="eventlog"></a>
### <a name="the-windows-eventlog-provider"></a>Windows 事件日志提供程序

[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog)提供程序包将日志输出发送到 Windows 事件日志中。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
logging.AddEventLog()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddEventLog()
```

[AddEventLog 重载](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.eventloggerfactoryextensions)让你传入`EventLogSettings`或最小日志级别。

---

<a id="tracesource"></a>
### <a name="the-tracesource-provider"></a>TraceSource 提供程序

[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource)提供程序包使用[System.Diagnostics.TraceSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracesource)库和提供程序。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
logging.AddTraceSource(sourceSwitchName);
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddTraceSource(sourceSwitchName);
```

---

[AddTraceSource 重载](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.tracesourcefactoryextensions)使传入一个源开关和跟踪侦听器。

若要使用此提供程序，应用程序必须在.NET Framework （而不是.NET Core） 上运行。 使用提供程序，可以将消息路由到各种[侦听器](https://docs.microsoft.com/dotnet/framework/debug-trace-profile/trace-listeners)，如[TextWriterTraceListener](https://docs.microsoft.com/dotnet/api/system.diagnostics.textwritertracelistenerr)示例应用程序中使用。

下面的示例将配置`TraceSource`日志的提供程序`Warning`和更高版本到控制台窗口的消息。

[!code-csharp[](logging/sample/Startup.cs?name=snippet_TraceSource&highlight=9-12)]

<a id="appservice"></a>
### <a name="the-azure-app-service-provider"></a>Azure App Service 提供程序

[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices)提供程序包将日志写入文本文件 Azure App Service 应用程序的文件系统中和为[blob 存储](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)在 Azure 存储帐户。 提供程序是仅适用于应用程序的目标 ASP.NET 核心 1.1.0 或更高版本。 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

> [!NOTE]
> ASP.NET 核心 2.0 处于预览状态。  部署到 Azure App Service 时，可能无法运行的最新预览版本创建的应用。 当发布 ASP.NET 核心 2.0 后时，在 Azure App Service 上运行 2.0 应用程序和 Azure 应用程序服务提供程序将按此处所指示方式工作。

无需安装的提供程序包或调用`AddAzureWebAppDiagnostics`扩展方法。  将应用部署到 Azure App Service，该提供程序时自动提供给你的应用程序。

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddAzureWebAppDiagnostics();
```

`AddAzureWebAppDiagnostics`重载的允许你传入[AzureAppServicesDiagnosticsSettings](https://github.com/aspnet/Logging/blob/c7d0b1b88668ff4ef8a86ea7d2ebb5ca7f88d3e0/src/Microsoft.Extensions.Logging.AzureAppServices/AzureAppServicesDiagnosticsSettings.cs)，与其重写默认设置，如日志记录输出模板、 blob 名称和文件大小限制。 (*输出模板*是消息格式字符串应用于所有日志，在你提供在调用时`ILogger`方法。)  

---

当你将部署到 App Service 应用时，你的应用程序遵循中的设置[诊断日志](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/#enablediag)部分**App Service** Azure 门户页。 更改这些设置时，所做的更改会立即生效且无需重新启动应用，或重新部署代码到它。 

![Azure 日志记录设置](logging/_static/azure-logging-settings.png)

日志文件的默认位置处于*d:\\主\\LogFiles\\应用程序*文件夹，以及默认文件名称是*诊断 yyyymmdd.txt*。 默认文件大小限制为 10 MB，而保留的文件的默认最大数量为 2。 默认 blob 名称是*{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*。 有关默认行为的详细信息，请参阅[AzureAppServicesDiagnosticsSettings](https://github.com/aspnet/Logging/blob/c7d0b1b88668ff4ef8a86ea7d2ebb5ca7f88d3e0/src/Microsoft.Extensions.Logging.AzureAppServices/AzureAppServicesDiagnosticsSettings.cs)。

当在 Azure 环境中运行你的项目时，该提供程序才有效。  本地运行时无任何效果&mdash;它不会写入本地文件或 blob 的本地开发存储。

## <a name="third-party-logging-providers"></a>第三方日志记录提供程序

以下是使用 ASP.NET Core 某些第三方日志记录框架：

* [elmah.io](https://github.com/elmahio/Elmah.Io.Extensions.Logging) -Elmah.Io 服务提供程序

* [JSNLog](http://jsnlog.com) -JavaScript 异常和其他客户端事件记录在服务器端日志中。

* [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging) -Loggr 服务提供程序

* [NLog](https://github.com/NLog/NLog.Extensions.Logging) -NLog 库的提供程序

* [Serilog](https://github.com/serilog/serilog-extensions-logging) -Serilog 库的提供程序

某些第三方框架可以执行[语义的日志记录，也称为结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。

使用第三方框架是类似于使用内置提供程序之一： 将 NuGet 包添加到你的项目，对调用扩展方法`ILoggerFactory`。 有关详细信息，请参阅每个框架文档。

你可以创建你自己的自定义提供程序，以支持其他日志记录框架或您自己的日志记录要求。
