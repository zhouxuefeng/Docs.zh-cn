---
title: "ASP.NET Core 基础知识"
author: rick-anderson
description: "本文提供了有关构建 ASP.NET Core 应用程序时需要了解的基础概念的高级概述。"
keywords: "ASP.NET Core, 基础知识, 概述"
ms.author: riande
manager: wpickett
ms.date: 08/18/2017
ms.topic: get-started-article
ms.assetid: a19b7836-63e4-44e8-8250-50d426dd1070
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/index
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5d8ca35b0e2e4b6e9b1ec745a3a7cf7c3983c461
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="aspnet-core-fundamentals-overview"></a>ASP.NET Core 基础知识概述

ASP.NET Core 应用程序是在其 `Main` 方法中创建 Web 服务器的控制台应用：

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program2x.cs)]

`Main` 方法调用 `WebHost.CreateDefaultBuilder`，后者按照生成器模式来创建 Web 应用程序主机。 生成器提供定义 Web 服务器（例如，`UseKestrel`）和启动类 (`UseStartup`) 的方法。 在前面的例子中，自动分配了 [Kestrel](xref:fundamentals/servers/kestrel) Web 服务器。 ASP.NET Core 的 Web 主机将尝试在 IIS 上运行（如果可用）。 对于其他 Web 服务器（如 [HTTP.sys](xref:fundamentals/servers/httpsys)），可通过调用相应的扩展方法来使用。 在下一节对 `UseStartup` 进行了更深入的介绍。

`IWebHostBuilder` 是 `WebHost.CreateDefaultBuilder` 调用的返回类型，它提供了许多可选方法。 其中的一些方法包括用于在 HTTP.sys 中托管应用程序的 `UseHttpSys`，以及用于指定根内容目录的 `UseContentRoot`。 `Build` 和 `Run` 方法生成 `IWebHost` 对象，它将托管应用程序并开始侦听 HTTP 请求。

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program.cs)]

`Main` 方法使用 `WebHostBuilder`，后者按照生成器模式来创建 Web 应用程序主机。 生成器提供定义 Web 服务器（例如，`UseKestrel`）和启动类 (`UseStartup`) 的方法。 在前面的示例中，使用了 [Kestrel](xref:fundamentals/servers/kestrel) Web 服务器。 对于其他 Web 服务器（如 [WebListener](xref:fundamentals/servers/weblistener)），可通过调用相应的扩展方法来使用。 在下一节对 `UseStartup` 进行了更深入的介绍。

`WebHostBuilder` 提供了许多可选方法，其中包括用于在 IIS 和 IIS Express 中进行托管的 `UseIISIntegration`，以及用于指定根内容目录的 `UseContentRoot`。 `Build` 和 `Run` 方法生成 `IWebHost` 对象，它将托管应用程序并开始侦听 HTTP 请求。

---

## <a name="startup"></a>启动

`WebHostBuilder` 上的 `UseStartup` 方法为你的应用指定 `Startup` 类：

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program2x.cs?highlight=10&range=6-17)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program.cs?highlight=7&range=6-17)]

---

`Startup` 类用于定义请求处理管道和配置应用程序所需的任何服务。 `Startup` 必须是公共类，并包含以下方法：

```csharp
public class Startup
{
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app)
    {
    }
}
```

* `ConfigureServices` 定义应用程序所使用的[服务](#services)（如 ASP.NET Core MVC、Entity Framework Core、标识等）。

* `Configure` 定义请求管道中的[中间件](xref:fundamentals/middleware)。

有关详细信息，请参阅[应用程序启动](xref:fundamentals/startup)。

## <a name="services"></a>服务

服务是应用程序中常用的组件。 可以通过[依存关系注入](xref:fundamentals/dependency-injection) (DI) 来获取服务。 ASP.NET Core 包括默认支持[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)的本机控制反转 (IoC) 容器。 可将本机容器替换成你所选择的容器。 DI 除了具备松散耦合优势以外，还可以使你在应用程序中使用服务。 例如，可以在应用程序中使用[日志记录](xref:fundamentals/logging)。

有关详细信息，请参阅[依存关系注入](xref:fundamentals/dependency-injection)。

## <a name="middleware"></a>中间件

在 ASP.NET Core 中，使用[中间件](xref:fundamentals/middleware)来撰写请求管道。 ASP.NET Core 中间件在 `HttpContext` 上执行异步逻辑，然后调用序列中的下一个中间件或直接终止请求。 通过在 `Configure` 方法中调用 `UseXYZ` 扩展方法来添加名为“XYZ”的中间件组件。

ASP.NET Core 包含一组丰富的内置中间件：

* [静态文件](xref:fundamentals/static-files)

* [路由](xref:fundamentals/routing)

* [身份验证](xref:security/authentication/index)

可以将任何基于 [OWIN](http://owin.org) 的中间件与 ASP.NET Core 结合使用，也可以编写自己的自定义中间件。

有关详细信息，请参阅[中间件](xref:fundamentals/middleware)和 [.NET 的开放 Web 接口 (OWIN)](xref:fundamentals/owin)。

## <a name="servers"></a>服务器

ASP.NET Core 托管模型并不直接侦听请求，而是依赖于 HTTP 服务器实现来将请求转发到应用程序。 转发的请求被打包为一组可通过接口进行访问的功能对象。 应用程序将其撰写到 `HttpContext` 中。 ASP.NET Core 包含托管的跨平台 Web 服务器，名为 [Kestrel](xref:fundamentals/servers/kestrel)。 Kestrel 通常在生产 Web 服务器（如 [IIS](https://www.iis.net/) 或 [nginx](http://nginx.org)）后台运行。

有关详细信息，请参阅[服务器](xref:fundamentals/servers/index)和[托管](xref:fundamentals/hosting)。

## <a name="content-root"></a>内容根

内容根是应用所使用的任何内容的基路径，如视图、[Razor 页面](xref:mvc/razor-pages/index) 和静态资产。 默认情况下，内容根与用于托管应用程序的可执行文件的应用程序基路径相同。 内容根的其他位置由 `WebHostBuilder` 指定。

## <a name="web-root"></a>Web 根

应用程序的 Web 根是项目中的目录，其中包含公共资源、CSS 等静态资源、JavaScript 和图形文件。 默认情况下，静态文件中间件仅提供 Web 根目录及其子目录中的文件。 有关详细信息，请参阅[使用静态文件](xref:fundamentals/static-files)。 Web 根路径默认为 /wwwroot，但可以使用 `WebHostBuilder` 来指定其他位置。

## <a name="configuration"></a>配置

ASP.NET Core 使用新的配置模型来处理简单的名称/值对。 新的配置模型不基于 `System.Configuration` 或 web.config，相反，它是从一组有序的配置提供程序中拉取的。 内置配置提供程序支持各种文件格式（XML、 JSON、INI）和环境变量，从而实现基于环境的配置。 也可以编写你自己的自定义配置提供程序。

有关详细信息，请参阅[配置](xref:fundamentals/configuration)。

## <a name="environments"></a>环境

环境（如“开发”环境和“生产”环境）是 ASP.NET Core 的高级概念，可使用环境变量进行设置。

有关详细信息，请参阅[使用多个环境](xref:fundamentals/environments)。

## <a name="net-core-vs-net-framework-runtime"></a>.NET Core 与 .NET Framework 运行时

ASP.NET Core 应用程序可以面向 .NET Core 或 .NET Framework 运行时。 有关详细信息，请参阅[在 .NET Core 和 .NET Framework 之间进行选择](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server)。

## <a name="additional-information"></a>其他信息

另请参阅以下主题：

- [错误处理](xref:fundamentals/error-handling)
- [文件提供程序](xref:fundamentals/file-providers)
- [全球化和本地化](xref:fundamentals/localization)
- [日志记录](xref:fundamentals/logging)
- [管理应用程序状态](xref:fundamentals/app-state)
