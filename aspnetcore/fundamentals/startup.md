---
title: "在 ASP.NET Core 中的应用程序启动"
author: ardalis
description: "介绍在 ASP.NET Core Startup 类。"
keywords: "ASP.NET 核心，启动时，配置方法，ConfigureServices 方法"
ms.author: tdykstra
manager: wpickett
ms.date: 02/29/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/startup
ms.openlocfilehash: 94db2ff530b5de7fe357cfb591d09b984cb248f9
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2017
---
# <a name="application-startup-in-aspnet-core"></a>在 ASP.NET Core 中的应用程序启动

通过[Steve Smith](https://ardalis.com/)和[Tom Dykstra](https://github.com/tdykstra/)

`Startup`类配置服务和应用程序的请求管道。 

## <a name="the-startup-class"></a>Startup 类

ASP.NET Core 应用需要`Startup`类，该类名为`Startup`按照约定。 指定的启动类名`Main`程序的[WebHostBuilderExtensions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderextensions) [ `UseStartup<TStartup>` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderextensions#Microsoft_AspNetCore_Hosting_WebHostBuilderExtensions_UseStartup__1_Microsoft_AspNetCore_Hosting_IWebHostBuilder_)方法。 请参阅[宿主](xref:fundamentals/hosting)以详细了解`WebHostBuilder`，运行之前`Startup`。

你可以定义单独`Startup`不同环境中，和相应的将会选择一个在运行时类。 如果指定`startupAssembly`中[WebHost 配置](https://docs.microsoft.com/aspnet/core/fundamentals/hosting?tabs=aspnetcore2x#configuring-a-host)或选项，承载将加载该程序集的启动和搜索`Startup`或`Startup[Environment]`类型。 类中运行该应用程序的当前环境将按优先级排列其名称后缀匹配项，因此，如果*开发*环境，并同时包含`Startup`和`StartupDevelopment`类，`StartupDevelopment`类将为使用。 请参阅[FindStartupType](https://github.com/aspnet/Hosting/blob/rel/1.1.0/src/Microsoft.AspNetCore.Hosting/Internal/StartupLoader.cs)中`StartupLoader`和[使用多个环境](environments.md#startup-conventions)。

或者，你可以定义固定`Startup`将通过调用而不考虑环境中使用的类`UseStartup<TStartup>`。 此为推荐方法。

`Startup`类构造函数可以接受的依赖项，通过提供[依赖关系注入](xref:fundamentals/dependency-injection)。 常见方法是使用`IHostingEnvironment`设置[配置](xref:fundamentals/configuration)源。

`Startup`类必须包含`Configure`方法并且可以根据需要包含`ConfigureServices`方法，这两种应用程序启动时调用。 类还可包含[这些方法的特定于环境的版本](xref:fundamentals/environments#startup-conventions)。 `ConfigureServices`如果存在之前, 调用`Configure`。

了解有关[应用程序启动期间处理的异常](xref:fundamentals/error-handling#startup-exception-handling)。

## <a name="the-configureservices-method"></a>ConfigureServices 方法

[ConfigureServices](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.startupbase#Microsoft_AspNetCore_Hosting_StartupBase_ConfigureServices_Microsoft_Extensions_DependencyInjection_IServiceCollection_)方法是可选的; 但如果使用，则将不调用之前`Configure`web 主机的方法。 Web 主机可以配置某些服务之前``Startup``调用方法 (请参阅[承载](xref:fundamentals/hosting))。 按照约定，[配置选项](xref:fundamentals/configuration)在此方法中设置。

对于需要大量的安装程序的功能有`Add[Service]`上的扩展方法[IServiceCollection](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.dependencyinjection.iservicecollection)。 此示例摘自默认网站模板配置应用程序，以将服务用于实体框架、 标识和 MVC:

[!code-csharp[Main](../common/samples/WebApplication1/Startup.cs?highlight=4,7,11&start=40&end=55)]

将服务添加到服务容器使它们可通过应用程序中[依赖关系注入](xref:fundamentals/dependency-injection)。

## <a name="services-available-in-startup"></a>在启动中可用的服务

ASP.NET 核心依赖关系注入应用程序的启动期间提供服务。 你可以通过作为参数包括适当的接口中请求这些服务你`Startup`类的构造函数或其`Configure`方法。 `ConfigureServices`方法仅采用`IServiceCollection`参数 （但此集合中检索，因此其他参数，则不需要任何已注册的服务可以）。

以下是一些通常由请求的服务`Startup`方法：

* 构造函数中： `IHostingEnvironment`，`ILogger<Startup>`
* 在`ConfigureServices`:`IServiceCollection`
* 在`Configure`: `IApplicationBuilder`， `IHostingEnvironment`，`ILoggerFactory`

通过添加的任何服务``WebHostBuilder````ConfigureServices``方法可请求的``Startup``类构造函数或其``Configure``方法。 使用`WebHostBuilder`若要为任何服务你需要在`Startup`方法。

## <a name="the-configure-method"></a>Configure 方法

`Configure`方法用于指定 ASP.NET 应用程序将如何响应 HTTP 请求。 通过添加配置请求管道[中间件](middleware.md)组件`IApplicationBuilder`提供的依赖关系注入的实例。

在以下示例从默认网站模板中，多个扩展方法用于配置支持的管道[浏览器链接](http://vswebessentials.com/features/browserlink)，错误页、 静态文件、 ASP.NET MVC 和标识。

[!code-csharp[Main](../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

每个`Use`扩展方法将添加[中间件](xref:fundamentals/middleware)请求管道组件。 例如，`UseMvc`扩展方法将添加[路由](routing.md)向请求管道的中间件并配置[MVC](xref:mvc/overview)作为默认处理程序。

有关如何使用`IApplicationBuilder`，请参阅[中间件](xref:fundamentals/middleware)。

等其他服务，`IHostingEnvironment`和`ILoggerFactory`还可以指定在方法签名中，在这种情况下这些服务将[注入](dependency-injection.md)它们是否可用。 

## <a name="additional-resources"></a>其他资源

* [使用多个环境](xref:fundamentals/environments)
* [中间件](xref:fundamentals/middleware)
* [日志记录](xref:fundamentals/logging)
* [配置](xref:fundamentals/configuration)
