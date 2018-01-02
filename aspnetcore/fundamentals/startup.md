---
title: "在 ASP.NET Core 中的应用程序启动"
author: ardalis
description: "发现服务和应用程序的请求管道 Startup 类在 ASP.NET 核心中的配置的方式。"
ms.author: tdykstra
manager: wpickett
ms.custom: mvc
ms.date: 12/08/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/startup
ms.openlocfilehash: dd2eb3d3996bc0bf277c8d5e772c8568ef9f147e
ms.sourcegitcommit: f5a7f0198628f0d152257d90dba6c3a0747a355a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2017
---
# <a name="application-startup-in-aspnet-core"></a>在 ASP.NET Core 中的应用程序启动

通过[Steve Smith](https://ardalis.com)， [Tom Dykstra](https://github.com/tdykstra)，和[Luke Latham](https://github.com/guardrex)

`Startup`类配置服务和应用程序的请求管道。

## <a name="the-startup-class"></a>Startup 类

ASP.NET Core 应用使用`Startup`类，该类名为`Startup`按照约定。 `Startup`类：

* 可以选择性地包含[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices)方法来配置应用程序的服务。
* 必须包括[配置](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure)方法来创建应用程序的请求处理管道。

`ConfigureServices`和`Configure`时启动该应用程序运行时调用：

[!code-csharp[Main](startup/snapshot_sample/Startup1.cs)]

指定`Startup`类，该类具有[WebHostBuilderExtensions](/dotnet/api/Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions) [UseStartup&lt;TStartup&gt; ](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup#Microsoft_AspNetCore_Hosting_WebHostBuilderExtensions_UseStartup__1_Microsoft_AspNetCore_Hosting_IWebHostBuilder_)方法：

[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=10)]

`Startup`类构造函数接受由主机定义的依赖项。 一个常见用途[依赖关系注入](xref:fundamentals/dependency-injection)到`Startup`类是将注入[IHostingEnvironment](/dotnet/api/Microsoft.AspNetCore.Hosting.IHostingEnvironment)由环境中配置服务：

[!code-csharp[Main](startup/snapshot_sample/Startup2.cs)]

将注入的替代方法`IHostingStartup`是使用基于约定的方法。 应用程序可以定义单独`Startup`用于不同的环境的类 (例如， `StartupDevelopment`)，并且在运行时选择了适当的 startup 类。 其名称后缀与当前的环境匹配的类进行优先级排序。 如果应用程序在开发环境中运行，并且同时包含`Startup`类和一个`StartupDevelopment`类，`StartupDevelopment`使用类。 有关详细信息请参阅[使用多个环境](xref:fundamentals/environments#startup-conventions)。

若要详细了解`WebHostBuilder`，请参阅[宿主](xref:fundamentals/hosting)主题。 有关在启动过程中处理错误的信息，请参阅[启动异常处理](xref:fundamentals/error-handling#startup-exception-handling)。

## <a name="the-configureservices-method"></a>ConfigureServices 方法

[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices)方法是：

* 可选。
* Web 宿主之前调用`Configure`方法来配置应用程序的服务。
* 其中[配置选项](xref:fundamentals/configuration/index)设置的约定。

将服务添加到服务容器使它们可在应用内和在`Configure`方法。 服务是通过解析[依赖关系注入](xref:fundamentals/dependency-injection)或从[IApplicationBuilder.ApplicationServices](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.applicationservices)。

Web 主机可以配置某些服务之前`Startup`调用方法。 中提供了详细信息[宿主](xref:fundamentals/hosting)主题。 

对于需要大量的安装程序的功能，有`Add[Service]`上的扩展方法[IServiceCollection](/dotnet/api/Microsoft.Extensions.DependencyInjection.IServiceCollection)。 典型 web 应用将注册到服务的实体框架、 标识和 MVC:

[!code-csharp[Main](../common/samples/WebApplication1/Startup.cs?highlight=4,7,11&start=40&end=55)]

## <a name="services-available-in-startup"></a>在启动中可用的服务

Web 主机提供某些服务可供`Startup`类构造函数。 应用程序添加其他服务通过`ConfigureServices`。 在主机和应用程序服务都然后可以在`Configure`和整个应用程序。

## <a name="the-configure-method"></a>Configure 方法

[配置](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure)方法用于指定应用程序如何响应的 HTTP 请求。 通过添加配置请求管道[中间件](xref:fundamentals/middleware)组件[IApplicationBuilder](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder)实例。 `IApplicationBuilder`可供`Configure`方法，但它未注册到服务容器中。 承载创建`IApplicationBuilder`并直接将其传递`Configure`([引用源](https://github.com/aspnet/Hosting/blob/release/2.0.0/src/Microsoft.AspNetCore.Hosting/Internal/WebHost.cs#L179-L192))。

[ASP.NET Core 模板](/dotnet/core/tools/dotnet-new)支持开发人员异常页上，配置管道[浏览器链接](http://vswebessentials.com/features/browserlink)，错误页、 静态文件和 ASP.NET MVC:

[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Startup.cs?range=28-48&highlight=5,6,10,13,15)]

每个`Use`扩展方法将中间件组件添加到请求管道。 例如，`UseMvc`扩展方法将添加[路由的中间件](xref:fundamentals/routing)向请求管道并配置[MVC](xref:mvc/overview)作为默认处理程序。

其他服务，如`IHostingEnvironment`和`ILoggerFactory`，还可以将方法签名中指定。 如果指定，如果它们被注入其他服务。

有关详细信息如何使用`IApplicationBuilder`，请参阅[中间件](xref:fundamentals/middleware)。

## <a name="convenience-methods"></a>便利方法

[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder.configureservices)和[配置](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure)便捷的方法，可以使用而不是指定`Startup`类。 多次调用`ConfigureServices`将追加到另一个。 多次调用`Configure`使用最后一次的方法调用。

[!code-csharp[Main](startup/snapshot_sample/Program.cs?highlight=16,20)]

## <a name="startup-filters"></a>启动筛选器

使用[IStartupFilter](/dotnet/api/microsoft.aspnetcore.hosting.istartupfilter)的开头或末尾应用的配置中间件[配置](#the-configure-method)中间件管道。 `IStartupFilter`可用于确保中间件运行之前或之后添加通过库进行的开头或末尾应用程序的请求处理管道的中间件。

`IStartupFilter`实现单个方法[配置](/dotnet/api/microsoft.aspnetcore.hosting.istartupfilter.configure)，其接收并返回`Action<IApplicationBuilder>`。 [IApplicationBuilder](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder)定义用于配置应用的请求管道的类。 有关详细信息，请参阅[使用 IApplicationBuilder 创建中间件管道](xref:fundamentals/middleware#creating-a-middleware-pipeline-with-iapplicationbuilder)。

每个`IStartupFilter`请求管道中实现一个或多个中间件。 筛选器添加到服务容器的顺序调用。 筛选可能会增加中间件之前或之后将控件传递给下一个筛选器，因此它们将追加到的开头或末尾应用管道。

[示例应用程序](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/startup/sample/)([如何下载](xref:tutorials/index#how-to-download-a-sample)) 演示如何注册与中间件`IStartupFilter`。 示例应用程序包含中间件的查询字符串参数设置选项值：

[!code-csharp[Main](startup/sample/RequestSetOptionsMiddleware.cs?name=snippet1)]

`RequestSetOptionsMiddleware`中配置`RequestSetOptionsStartupFilter`类：

[!code-csharp[Main](startup/sample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

`IStartupFilter`注册中的服务容器中`ConfigureServices`:

[!code-csharp[Main](startup/sample/Startup.cs?name=snippet1&highlight=3)]

时的查询字符串参数`option`提供该中间件处理的值分配之前 MVC 中间件呈现响应：

![显示呈现的索引页的浏览器窗口。 如从中间件基于请求的查询字符串参数和值的选项设置为从中间件的页呈现选项的值。](startup/_static/index.png)

中间件执行顺序设置的顺序由`IStartupFilter`注册：

* 多个`IStartupFilter`实现可与相同的对象进行交互。 如果排序很重要，排序其`IStartupFilter`服务注册其中间件应运行的顺序相匹配。
* 库可以添加与一个或多个中间件`IStartupFilter`运行之前或之后向其他应用程序中间件注册的实现`IStartupFilter`。 若要调用`IStartupFilter`之前添加的库的中间件的中间件`IStartupFilter`，库添加到服务容器之前定位服务注册。 若要此后调用它，请添加库后位置服务注册。

## <a name="additional-resources"></a>其他资源

* [承载](xref:fundamentals/hosting)
* [使用多个环境](xref:fundamentals/environments)
* [中间件](xref:fundamentals/middleware)
* [日志记录](xref:fundamentals/logging/index)
* [配置](xref:fundamentals/configuration/index)
* [StartupLoader 类： FindStartupType 方法 （引用源）](https://github.com/aspnet/Hosting/blob/rel/2.0.0/src/Microsoft.AspNetCore.Hosting/Internal/StartupLoader.cs#L66-L116)
