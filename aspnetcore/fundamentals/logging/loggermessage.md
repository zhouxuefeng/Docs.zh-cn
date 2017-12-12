---
title: "通过在 ASP.NET Core LoggerMessage 的高性能日志记录"
author: guardrex
description: "了解如何使用 LoggerMessage 功能来创建可缓存于高性能日志记录方案需要较少的对象分配比记录器扩展方法的委托。"
ms.author: riande
manager: wpickett
ms.date: 11/03/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/logging/loggermessage
ms.openlocfilehash: defba75c6c9ea13d24af4cd8515d82d9e7cf9853
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a>通过在 ASP.NET Core LoggerMessage 的高性能日志记录

作者：[Luke Latham](https://github.com/guardrex)

[LoggerMessage](/dotnet/api/microsoft.extensions.logging.loggermessage)功能创建一个可缓存委托需要较少的对象分配，并减少计算开销比[记录器扩展方法](/dotnet/api/Microsoft.Extensions.Logging.LoggerExtensions)，如`LogInformation`， `LogDebug`，和`LogError`. 对于高性能日志记录方案，使用`LoggerMessage`模式。

`LoggerMessage`提供了相对记录器扩展方法的以下性能优势：

* 记录器扩展方法要求使用"装箱"（转换） 的值类型，如`int`，到`object`。 `LoggerMessage`模式通过使用静态来避免装箱`Action`字段和具有强类型参数的扩展方法。
* 记录器扩展方法必须分析每次写入日志消息的消息模板 （命名的格式字符串）。 `LoggerMessage`只需一次分析模板时定义该消息。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/sample/)（[如何下载](xref:tutorials/index#how-to-download-a-sample)）

示例应用演示`LoggerMessage`功能跟踪系统的基本引号。 应用程序添加和删除使用内存中数据库的引号。 这些操作出现时，使用生成日志消息`LoggerMessage`模式。

## <a name="loggermessagedefine"></a>LoggerMessage.Define

[定义 （LogLevel，EventId，字符串）](/dotnet/api/microsoft.extensions.logging.loggermessage.define)创建`Action`日志记录消息的委托。 `Define`重载允许将最多六个类型参数传递给命名的格式字符串 （模板）。

## <a name="loggermessagedefinescope"></a>LoggerMessage.DefineScope

[DefineScope(String)](/dotnet/api/microsoft.extensions.logging.loggermessage.definescope)创建`Func`定义的委托[日志范围](xref:fundamentals/logging/index#log-scopes)。 `DefineScope`重载允许将最多三个类型参数传递给命名的格式字符串 （模板）。

## <a name="message-template-named-format-string"></a>消息模板 （名为格式字符串）

到提供的字符串`Define`和`DefineScope`方法是一个模板，而不相比内, 插的字符串。 占位符将填充的类型指定的顺序。 在模板中的占位符名称应为在模板具说明性且更一致。 用作结构化的日志数据中的属性名称。 我们建议[Pascal 大小写](/dotnet/standard/design-guidelines/capitalization-conventions)的占位符名称。 例如， `{Count}`， `{FirstName}`。

## <a name="implementing-loggermessagedefine"></a>实现 LoggerMessage.Define

每条日志消息是`Action`中创建的某个静态字段保存`LoggerMessage.Define`。 例如，示例应用程序创建一个字段，以描述索引页 GET 请求的日志消息 (*Internal/LoggerExtensions.cs*):

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet1)]

有关`Action`，指定：

* 日志级别。
* 唯一的事件标识符 ([EventId](/dotnet/api/microsoft.extensions.logging.eventid)) 替换为静态的扩展方法的名称。
* 消息模板 （名为格式字符串）。 

示例应用程序集的索引页的请求:

* 日志级别设置为`Information`。
* 事件 id`1`同名的`IndexPageRequested`方法。
* 消息模板 （名为格式字符串） 为字符串。

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet5)]

提供使用事件 id 来丰富日志记录时，结构化日志记录存储区可能使用的事件名称。 例如， [Serilog](https://github.com/serilog/serilog-extensions-logging)使用事件名称。

`Action`通过强类型扩展方法调用。 `IndexPageRequested`方法的示例应用中记录为索引页 GET 请求消息：

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet9)]

`IndexPageRequested`在中记录器上调用`OnGetAsync`中的方法*Pages/Index.cshtml.cs*:

[!code-csharp[Main](loggermessage/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

检查应用程序的控制台输出：

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

若要将参数传递给一条日志消息，请创建静态字段时定义最多六个类型。 当通过定义添加引号示例应用程序日志，string`string`键入`Action`字段：

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet2)]

该委托的日志消息模板提供的类型从接收其占位符值。 示例应用程序定义委托的添加引号参数所在的引号`string`:

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet6)]

用于添加引号、 静态的扩展方法`QuoteAdded`、 接收报价自变量值并将其传递给`Action`委托：

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet10)]

索引页的代码隐藏文件中 (*Pages/Index.cshtml.cs*)，`QuoteAdded`调用来记录消息：

[!code-csharp[Main](loggermessage/sample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

检查应用程序的控制台输出：

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the consequences of avoiding reality. - Ayn Rand')
```

本示例应用程序实现`try` &ndash; `catch`引号删除模式。 一条信息性消息只记录成功的删除操作。 引发异常时，将删除操作的记录一条错误消息。 日志消息的不成功，则删除操作包括异常堆栈跟踪 (*Internal/LoggerExtensions.cs*):

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet7)]

请注意如何将异常传递给该委托`QuoteDeleteFailed`:

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet11)]

索引页代码隐藏文件中，成功引号删除调用`QuoteDeleted`记录器方法。 当为删除，找不到有引号`ArgumentNullException`引发。 通过捕获异常`try` &ndash; `catch`语句，并通过调用记录`QuoteDeleteFailed`方法中记录器`catch`块 (*Pages/Index.cshtml.cs*):

[!code-csharp[Main](loggermessage/sample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

在成功删除引号，检查应用程序的控制台输出：

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the consequences of avoiding reality. - Ayn Rand' Id = 1)
```

当引号删除失败时，检查应用程序的控制台输出。 请注意，日志消息中包含的异常：

```console
fail: LoggerMessageSample.Pages.IndexModel[5]
      => RequestId:0HL90M6E7PHK5:00000010 RequestPath:/ => /Index
      Quote delete failed (Id = 999)
System.ArgumentNullException: Value cannot be null.
Parameter name: entity
   at Microsoft.EntityFrameworkCore.Utilities.Check.NotNull[T](T value, String parameterName)
   at Microsoft.EntityFrameworkCore.DbContext.Remove[TEntity](TEntity entity)
   at Microsoft.EntityFrameworkCore.Internal.InternalDbSet`1.Remove(TEntity entity)
   at LoggerMessageSample.Pages.IndexModel.<OnPostDeleteQuoteAsync>d__14.MoveNext() in 
      <PATH>\sample\Pages\Index.cshtml.cs:line 87
```

## <a name="implementing-loggermessagedefinescope"></a>实现 LoggerMessage.DefineScope

定义[日志范围](xref:fundamentals/logging/index#log-scopes)要应用于使用的日志消息的一系列[DefineScope(String)](/dotnet/api/microsoft.extensions.logging.loggermessage.definescope)方法。

示例应用程序具有**全部清除**删除所有数据库中引号的按钮。 删除其中一个会删除引号一次。 删除引号，每次`QuoteDeleted`对记录器调用方法。 日志范围添加到这些日志消息。

启用`IncludeScopes`控制台记录器选项中：

[!code-csharp[Main](loggermessage/sample/Program.cs?name=snippet1&highlight=22)]

设置`IncludeScopes`ASP.NET 核心 2.0 应用程序中需要来启用日志作用域。 设置`IncludeScopes`通过*appsettings*配置文件是已计划 ASP.NET 核心 2.1 版本的功能。

示例应用程序清除其他提供程序，并添加筛选器以减少日志记录输出。 这使得更轻松地查看演示的示例的日志消息`LoggerMessage`功能。

若要创建的日志作用域，添加一个字段以保存`Func`委托的范围。 示例应用程序创建一个名为字段`_allQuotesDeletedScope`(*Internal/LoggerExtensions.cs*):

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet4)]

使用`DefineScope`来创建委托。 最多三种类型可以被指定用于作为模板自变量时调用委托。 示例应用使用一个包含已删除引号数的消息模板 (`int`类型):

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet8)]

提供日志消息的静态扩展方法。 消息模板中包括任何类型参数对于显示的命名属性。 示例应用程序采用`count`的引号，若要删除并返回`_allQuotesDeletedScope`:

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet12)]

调用中的日志记录扩展作用域包装`using`块：

[!code-csharp[Main](loggermessage/sample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

检查应用程序的控制台输出中的日志消息。 下面的结果显示删除包含的日志范围消息使用的三个引号：

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

## <a name="see-also"></a>请参阅

* [日志记录](xref:fundamentals/logging/index)
