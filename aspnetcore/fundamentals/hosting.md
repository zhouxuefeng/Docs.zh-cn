---
title: "在 ASP.NET Core 中承载 |Microsoft 文档"
author: ardalis
description: "在 ASP.NET 核心中的 web 主机简介。"
keywords: "ASP.NET 核心，web 主机，IWebHost"
ms.author: riande
manager: wpickett
ms.date: 08/02/2017
ms.topic: article
ms.assetid: 4e45311d-8d56-46e2-b99d-6f65b648a277
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/hosting
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0725f3d2c130068094792e5ba9e17481155e4294
ms.sourcegitcommit: 74e22e08e3b08cb576e5184d16f4af5656c13c0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2017
---
# <a name="introduction-to-hosting-in-aspnet-core"></a>在 ASP.NET Core 中承载的简介

通过[Steve Smith](http://ardalis.com)

若要运行 ASP.NET Core 应用，你需要配置和启动主机使用`WebHostBuilder`。

## <a name="what-is-a-host"></a>主机是什么？

ASP.NET Core 应用需要*主机*在其中执行。 宿主必须实现`IWebHost`接口，公开的功能和服务的集合，该接口和一个`Start`方法。 主机通常使用的实例创建`WebHostBuilder`，这将生成并返回`WebHost`实例。 `WebHost`引用将处理的请求的服务器。 详细了解[服务器](servers/index.md)。

### <a name="what-is-the-difference-between-a-host-and-a-server"></a>主机和服务器之间的区别是什么？

主机负责应用程序启动和生存期管理。 服务器负责接收 HTTP 请求。 主机的责任的一部分包括确保应用程序的服务和服务器都可用并且配置正确。 你可以将正在服务器周围的包装器的主机。 主机配置为使用一个特定的服务器。服务器不知道其主机。

## <a name="setting-up-a-host"></a>设置主机

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET 核心 1.x](#tab/aspnetcore1x)

创建使用的实例的主机`WebHostBuilder`。 这通常是在您的应用程序的入口点： `public static void Main` (项目模板中位于*Program.cs*文件)。 典型*Program.cs*所示下面，演示如何使用`WebHostBuilder`来构建的主机。

[!code-csharp[Main](../common/samples/WebApplication1/Program.cs?highlight=14,15,16,17,18,19,20,21)]

`WebHostBuilder`负责创建主机，将启动应用程序的服务器。 `WebHostBuilder`要求提供实现的服务器`IServer`(`UseKestrel`在上面的代码)。 `UseKestrel`指定的 Kestrel 服务器将由应用程序。

服务器的*内容根*确定为内容文件，如 MVC 视图文件搜索。 默认内容根是从中运行应用程序的文件夹。

> [!NOTE]
> 指定`Directory.GetCurrentDirectory`由于内容根将使用 web 项目的根文件夹作为应用程序的内容根从此文件夹中启动应用时 (例如，调用`dotnet run`web 项目文件夹中)。 这是在 Visual Studio 中使用的默认值和`dotnet new`模板。

若要使用 IIS 的反向代理，调用`UseIISIntegration`生成主机的一部分。 

请注意，`UseIISIntegration`不配置*服务器*、 like`UseKestrel`未。 若要使用 ASP.NET Core IIS，则必须同时指定`UseKestrel`和`UseIISIntegration`。 `UseKestrel`创建 web 服务器和承载应用程序。 `UseIISIntegration`检查 IIS/IISExpress 所使用的环境变量，并配置设置，例如要侦听的端口和要使用的标头。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET 核心 2.x](#tab/aspnetcore2x)

创建使用的实例的主机`WebHostBuilder`。 这通常是在您的应用程序的入口点： `public static void Main` (项目模板中位于*Program.cs*文件)。 典型*Program.cs*、 示调用`CreateDefaultbuilder`来构建的主机：

[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

`CreateDefaultbuilder`创建的实例`WebHostBuilder`生成启动应用程序的服务器的主机。 主机需要[服务器实现 IServer](servers/index.md)。 内置服务器[Kestrel](servers/kestrel.md)和[HTTP.sys](servers/httpsys.md);`CreateDefaultbuilder`默认情况下使用 Kestrel。

`CreateDefaultbuilder`执行将 Kestrel 配置为 web 服务器除了设置任务：

* 将内容的根设置为`Directory.GetCurrentDirectory`。
* 从加载配置：
  * *appsettings.json*
  * *appsettings。\<EnvironmentName >.json*。
  * 用户的机密信息的应用程序在开发环境中运行时
  * 环境变量
  * 提供的命令行参数
* 使用筛选日志记录配置部分中指定的规则来配置控制台和调试输出的日志记录。
* 启用 IIS 集成。
* 在开发环境中运行应用程序时，将添加开发人员异常页。

服务器的*内容根*确定为内容文件，如 MVC 视图文件搜索。 默认内容根是从中运行应用程序的文件夹。

> [!NOTE]
> 指定`Directory.GetCurrentDirectory`由于内容根将使用 web 项目的根文件夹作为应用程序的内容根从此文件夹中启动应用时 (例如，调用`dotnet run`web 项目文件夹中)。 这是在 Visual Studio 中使用的默认值和`dotnet new`模板。

当你使用 IIS 的反向代理时，自动调用 ASP.NET Core`UseIISIntegration`生成主机的一部分。 有关详细信息，请参阅[ASP.NET 核心模块](xref:fundamentals/servers/aspnet-core-module)。

请注意，`UseIISIntegration`不配置*服务器*、 like`UseKestrel`未。 `UseKestrel`创建 web 服务器和承载应用程序。 `UseIISIntegration`检查 IIS/IISExpress 所使用的环境变量，并配置设置，例如要侦听的端口和要使用的标头。

---

只需服务器并配置应用程序的请求管道将包含配置一台主机 （和 ASP.NET Core 应用） 的最小实现：

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .Configure(app =>
    {
        app.Run(async (context) => await context.Response.WriteAsync("Hi!"));
    })
    .Build();

host.Run();
```

> [!NOTE]
> 设置时主机，你可以提供`Configure`和`ConfigureServices`方法，而非或除了指定`Startup`类 (这也必须定义这些方法-请参阅[应用程序启动](startup.md))。 多次调用`ConfigureServices`都将追加到另一个; 调用`Configure`或`UseStartup`将替换以前的设置。

## <a name="configuring-a-host"></a>配置主机

`WebHostBuilder`提供用于设置对于主机，也可通过直接使用设置的大部分的可用的配置值的方法`UseSetting`和关联的密钥。

### <a name="host-configuration-values"></a>主机配置值

**捕获启动错误**`bool`

键： `captureStartupErrors`。 默认为 `false`。 当`false`，退出主机中启动结果时出错。 当`true`，主机将捕获的任何异常`Startup`类，并尝试启动服务器。 它将显示一个错误页面 （泛型类型，或详细，以详细错误设置，下面） 为每个请求。 使用设置`CaptureStartupErrors`方法。

注意： 你的应用程序运行时使用 Kestrel 和 IIS，默认行为是以捕获启动错误。 

```csharp
new WebHostBuilder()
    .CaptureStartupErrors(true)
   ```

**内容根**`string`

键： `contentRoot`。 默认值为 （对于 Kestrel;，为应用程序集所在的文件夹IIS 将使用 web 项目根默认情况下）。 此设置确定其中 ASP.NET Core 将开始搜索内容的文件，MVC 视图等。 此外用作的基路径[Web 根设置](#web-root-setting)。 使用设置`UseContentRoot`方法。 路径必须存在，或者主机将无法启动。

```csharp
new WebHostBuilder()
    .UseContentRoot("c:\\mywebsite")
   ```

**详细错误**`bool`

键： `detailedErrors`。 默认为 `false`。 当`true`（或当环境已设置为"开发"），该应用将显示的启动异常，而不是只是一般性错误页的详细信息。 使用设置`UseSetting`。

```csharp
new WebHostBuilder()
    .UseSetting("detailedErrors", "true")
```

当详细错误设置为`false`和捕获启动 Errors 是`true`，到服务器的每个请求的响应中会显示一个一般性错误页面。

![常规错误页面](hosting/_static/generic-error-page.png)

当详细错误设置为`true`和捕获启动 Errors 是`true`，详细的错误页显示在每个服务器请求的响应。

![详细的错误页](hosting/_static/detailed-error-page.png)

**环境**`string`

键： `environment`。 默认值为"生产"。 可能设置为任何值。 框架定义的值包括"开发"、"过渡"和"生产"。 值不区分大小写。 请参阅[使用多个环境](environments.md)。 使用设置`UseEnvironment`方法。

```csharp
new WebHostBuilder()
    .UseEnvironment("Development")
```

> [!NOTE]
> 默认情况下，环境读取从`ASPNETCORE_ENVIRONMENT`环境变量。 在使用 Visual Studio 时，可能会设置环境变量*launchSettings.json*文件。

<a id="server-urls"></a>

**服务器 Url**`string`

键： `urls`。 设置为分号 （;） 分隔的 URL 的列表添加到服务器应响应以下前缀。 例如 `http://localhost:123`。 可以使用替换域/主机名"\*"以指示服务器应请求任何 IP 地址上侦听或承载使用指定的端口和协议 (例如，`http://*:5000`或`https://*:5001`)。 协议 (`http://`或`https://`) 必须包含每个 URL。 前缀解释配置服务器;支持的格式将有所不同服务器。

```csharp
new WebHostBuilder()
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

在 ASP.NET 核心 2.0 中，Kestrel 具有其自己的终结点配置 API，并且不支持`https://`中`urls`字符串。 有关详细信息，请参阅[简介 Kestrel](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration)。

**程序集启动**`string`

键： `startupAssembly`。 确定要搜索的程序集`Startup`类。 使用设置`UseStartup`方法。 可能会改为引用特定类型使用`WebHostBuilder.UseStartup<StartupType>`。 如果选择多个`UseStartup`调用方法，最后一个将优先。

```csharp
new WebHostBuilder()
    .UseStartup("StartupAssemblyName")
```

<a name=web-root-setting></a>

**Web 根**`string`

键： `webroot`。 如果未指定默认值为`(Content Root Path)\wwwroot`，如果它存在。 如果此路径不存在，则使用无操作文件提供程序。 使用设置`UseWebRoot`。

```csharp
new WebHostBuilder()
    .UseWebRoot("public")
```

### <a name="overriding-configuration"></a>重写配置

使用[配置](configuration.md)设置要由主机使用的配置值。 可能会随后重写这些值。 这使用指定`UseConfiguration`。

```csharp
public static void Main(string[] args)
{
    var config = new ConfigurationBuilder()
        .AddJsonFile("hosting.json", optional: true)
        .AddCommandLine(args)
        .Build();

    var host = new WebHostBuilder()
        .UseConfiguration(config)
        .UseKestrel()
        .Configure(app =>
        {
            app.Run(async (context) => await context.Response.WriteAsync("Hi!"));
        })
        .Build();

    host.Run();
}
```

在上面的示例中，命令行自变量可能会将传入的要配置主机，或配置设置 （可选） 可指定在*hosting.json*文件。 若要指定特定的 URL 上运行的主机，无法在命令提示符下传入所需的值：

```console
dotnet run --urls "http://*:5000"
```

`Run`方法会启动 web 应用，并阻止调用线程，直到该主机是关闭。

```csharp
host.Run();
```

你也可以通过调用非阻止方式在运行主机其`Start`方法：

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

传递到的 Url 的列表`Start`方法和它将侦听指定的 Url:

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

此处是有效的 URL 格式取决于你正在使用的服务器。 有关详细信息，请参阅[服务器 Url](#server-urls)本文前面。

> [!NOTE]
> `UseConfiguration`扩展方法不是当前能够分析返回的一个配置节`GetSection`(例如， `.UseConfiguration(Configuration.GetSection("section"))`。 `GetSection`方法筛选到请求的节的配置密钥，但会键上的节名称 (例如， `section:urls`， `section:environment`)。 `UseConfiguration`方法需要要匹配的键`WebHostBuilder`密钥 (例如， `urls`， `environment`)。 键的节名称存在阻止从配置主机部分的值。 将在即将发布的版本中解决此问题。 有关详细信息和解决方法，请参阅[将配置节传入到 WebHostBuilder.UseConfiguration 使用完整的密钥](https://github.com/aspnet/Hosting/issues/839)。

### <a name="ordering-importance"></a>顺序重要性

`WebHostBuilder`如果，某些环境变量，从第一次读取设置设置。 这些环境变量必须使用格式`ASPNETCORE_{configurationKey}`，因此对于示例设置 Url 服务器将侦听默认情况下，可以将设置`ASPNETCORE_URLS`。

你可以通过指定配置重写任何这些环境变量值 (使用`UseConfiguration`) 或通过显式设置的值 (使用`UseUrls`实例)。 主机将使用无论选项上次设置的值。 为此，`UseIISIntegration`必须显示之后`UseUrls`，因为它将使用一个动态由 IIS 替换 URL。 如果你想要以编程方式设置的默认 URL 为一个值，但允许其与配置中重写，则可按如下所述配置主机：

```csharp
var config = new ConfigurationBuilder()
    .AddCommandLine(args)
    .Build();

var host = new WebHostBuilder()
    .UseUrls("http://*:1000") // default URL
    .UseConfiguration(config) // override from command line
    .UseKestrel()
    .Build();
```

## <a name="additional-resources"></a>其他资源

* [发布到使用 IIS 的 Windows](../publishing/iis.md)
* [将发布到 Linux 使用 Nginx](../publishing/linuxproduction.md)
* [将发布到使用 Apache 的 Linux](../publishing/apache-proxy.md)
* [在 Windows 服务中的主机](xref:hosting/windows-service)

