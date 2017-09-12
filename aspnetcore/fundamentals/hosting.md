---
title: "在 ASP.NET Core 中承载"
author: guardrex
description: "了解有关 ASP.NET 核心，负责应用程序启动和生存期管理中的 web 主机信息。"
keywords: "ASP.NET 核心 web 主机，IWebHost、 WebHostBuilder、 IHostingEnvironment、 IApplicationLifetime"
ms.author: riande
manager: wpickett
ms.date: 09/10/2017
ms.topic: article
ms.assetid: 4e45311d-8d56-46e2-b99d-6f65b648a277
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/hosting
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a1a789ff1bc6b3e3af99419e7d74d3fb46bb2345
ms.sourcegitcommit: 368aabde4de3728a8e5a8c016a2ec61f9c0854bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="hosting-in-aspnet-core"></a>在 ASP.NET Core 中承载

通过[Luke Latham](https://github.com/guardrex)

ASP.NET Core 应用配置和启动*主机*，即负责应用程序启动和生存期管理。 至少，主机配置服务器和请求处理管道。

## <a name="setting-up-a-host"></a>设置主机

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

创建使用的实例的主机[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder)。 这通常在您的应用程序的入口点，来执行`Main`方法。 在项目模板`Main`位于*Program.cs*。 典型*Program.cs*调用[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder)来开始设置主机：

[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main)]

`CreateDefaultBuilder`执行以下任务：

* 配置[Kestrel](servers/kestrel.md)为 web 服务器。
* 将内容的根设置为[Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory)。
* 从加载可选配置：
  * *appsettings.json*。
  * *appsettings。{环境}.json*。
  * [用户的机密信息](xref:security/app-secrets)运行的应用`Development`环境。
  * 环境变量。
  * 命令行参数。
* 配置[日志记录](xref:fundamentals/logging)与控制台和调试输出[日志筛选](xref:fundamentals/logging#log-filtering)日志记录配置部分中指定的规则*appsettings.json*或*appsettings。{环境}.json*文件。
* 当运行 IIS 之后，通过配置的基路径和服务器应对其侦听时使用的端口启用 IIS 集成[ASP.NET 核心模块](xref:fundamentals/servers/aspnet-core-module)。 该模块创建 Kestrel 和 IIS 之间的反向代理。 此外可以配置应用到[捕获启动错误](#capture-startup-errors)。

*内容根*确定主机搜索内容的文件，如 MVC 视图文件的位置。 默认内容根是[Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory)。 这会导致从根文件夹中启动应用时将 web 项目的根文件夹用作内容的根 (例如，调用[dotnet 运行](/dotnet/core/tools/dotnet-run)项目文件夹中)。 这是默认值中使用[Visual Studio](https://www.visualstudio.com/)和[dotnet 新模板](/dotnet/core/tools/dotnet-new)。

请参阅[ASP.NET 核心中的配置](xref:fundamentals/configuration)有关应用程序配置的详细信息。

> [!NOTE]
> 作为使用静态的替代方法`CreateDefaultBuilder`方法，创建从宿主[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder)是一种受支持的方法与 ASP.NET 核心 2.x。 请参阅 ASP.NET Core 1.x 选项卡的详细信息。

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

创建使用的实例的主机[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder)。 这通常在您的应用程序的入口点，来执行`Main`方法。 在项目模板`Main`位于*Program.cs*。 以下*Program.cs*演示如何使用`WebHostBuilder`来生成主机：

[!code-csharp[Main](../common/samples/WebApplication1/Program.cs)]

`WebHostBuilder`需要[服务器实现 IServer](servers/index.md)。 内置服务器[Kestrel](servers/kestrel.md)和[HTTP.sys](servers/httpsys.md) (之前的 ASP.NET 核心 2.0 版本中，HTTP.sys 调用[WebListener](xref:fundamentals/servers/weblistener))。 在此示例中， [UseKestrel 扩展方法](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1)指定的 Kestrel 服务器。

*内容根*确定主机搜索内容的文件，如 MVC 视图文件的位置。 默认内容根提供给`UseContentRoot`是[Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory?view=netcore-1.1)。 这会导致从根文件夹中启动应用时将 web 项目的根文件夹用作内容的根 (例如，调用[dotnet 运行](/dotnet/core/tools/dotnet-run)项目文件夹中)。 这是默认值中使用[Visual Studio](https://www.visualstudio.com/)和[dotnet 新模板](/dotnet/core/tools/dotnet-new)。

若要使用 IIS 的反向代理，调用[UseIISIntegration](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions)生成主机的一部分。 `UseIISIntegration`不配置*服务器*、 like [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1)未。 `UseIISIntegration`配置的基路径和服务器应对其侦听时使用的端口[ASP.NET 核心模块](xref:fundamentals/servers/aspnet-core-module)创建 Kestrel 和 IIS 之间的反向代理。 若要使用 ASP.NET Core IIS，则必须同时指定`UseKestrel`和`UseIISIntegration`。 `UseIISIntegration`仅当在 IIS 或 IIS Express 后面运行时激活。 有关详细信息，请参阅[简介 ASP.NET 核心模块](xref:fundamentals/servers/aspnet-core-module)和[ASP.NET 核心模块配置引用](xref:hosting/aspnet-core-module)。

配置主机 （和 ASP.NET Core 应用） 的最小实现包括指定服务器和应用程序的请求管道的配置项：

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .Configure(app =>
    {
        app.Run(context => context.Response.WriteAsync("Hello World!"));
    })
    .Build();

host.Run();
```

---

设置时主机，你可以提供[配置](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure?view=aspnetcore-1.1)和[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices?view=aspnetcore-1.1)方法。 如果指定`Startup`类，它必须定义`Configure`方法。 有关详细信息，请参阅[在 ASP.NET Core 中的应用程序启动](startup.md)。 多次调用`ConfigureServices`将追加到另一个。 多次调用`Configure`或`UseStartup`上`WebHostBuilder`替换以前的设置。

## <a name="host-configuration-values"></a>主机配置值

[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder)提供用于设置大多数可用的配置值的主机，也可直接与设置的方法[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting)和关联的密钥。 设置一个值，该值时`UseSetting`，值设置为 （用引号引起来） 无论何种类型的字符串。

### <a name="capture-startup-errors"></a>捕获启动错误

此设置控制启动错误的捕获。

**密钥**: captureStartupErrors  
**类型**: *bool* (`true`或`1`)  
**默认**： 默认为`false`除非应用程序下运行的 IIS，其中默认值是后面的 Kestrel `true`。  
**使用设置**:`CaptureStartupErrors`

当`false`，退出主机中启动结果时出错。 当`true`，主机在启动期间捕获异常并尝试启动服务器。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .CaptureStartupErrors(true)
    ...
```

---

### <a name="content-root"></a>内容的根

此设置确定 ASP.NET Core 开始搜索内容的文件，MVC 视图等。 

**密钥**: contentRoot  
**类型**:*字符串*  
**默认**： 默认为应用程序集所在的文件夹。  
**使用设置**:`UseContentRoot`

内容的根也用作的基路径[Web 根目录设置](#web-root)。 如果路径不存在，主机将无法启动。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\mywebsite")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseContentRoot("c:\\mywebsite")
    ...
```

---

### <a name="detailed-errors"></a>详细的错误

确定详细应该捕获错误。

**密钥**： 之后，请  
**类型**: *bool* (`true`或`1`)  
**默认**: false  
**使用设置**:`UseSetting`

启用 (或当<a href="#environment">环境</a>设置为`Development`)，应用程序捕获详细的异常。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
    ...
```

---

### <a name="environment"></a>环境

设置应用程序的环境。

**密钥**： 环境  
**类型**:*字符串*  
**默认**： 生产  
**使用设置**:`UseEnvironment`

你可以设置*环境*为任何值。 框架定义的值包括`Development`， `Staging`，和`Production`。 值都不区分大小写。 默认情况下，*环境*从读取`ASPNETCORE_ENVIRONMENT`环境变量。 使用时[Visual Studio](https://www.visualstudio.com/)，可能会设置环境变量*launchSettings.json*文件。 有关详细信息，请参阅[使用多个环境](xref:fundamentals/environments)。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseEnvironment("Development")
    ...
```

---

### <a name="hosting-startup-assemblies"></a>承载启动程序集

设置应用程序的宿主启动程序集。

**密钥**: hostingStartupAssemblies  
**类型**:*字符串*  
**默认**： 空字符串  
**使用设置**:`UseSetting`

托管要在启动时加载的启动程序集的以分号分隔的字符串。 此功能是 ASP.NET 核心 2.0 中的新增功能。

虽然配置值默认为空字符串，宿主启动程序集将始终包括应用程序的程序集。 在提供宿主启动程序集时，它们正在加载应用程序在启动过程中生成其公共服务时添加到应用程序的程序集。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

此功能将不可用在 ASP.NET Core 1.x。

---

### <a name="prefer-hosting-urls"></a>选择承载 Url

指示是否应在使用配置的 Url 上侦听主机`WebHostBuilder`而不使用配置`IServer`实现。

**密钥**: preferHostingUrls  
**类型**: *bool* (`true`或`1`)  
**默认**: true  
**使用设置**:`PreferHostingUrls`

此功能是 ASP.NET 核心 2.0 中的新增功能。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

此功能将不可用在 ASP.NET Core 1.x。

---

### <a name="prevent-hosting-startup"></a>阻止托管启动

可防止托管启动程序集，包括应用程序的程序集的自动加载。

**密钥**: preventHostingStartup  
**类型**: *bool* (`true`或`1`)  
**默认**: false  
**使用设置**:`UseSetting`

此功能是 ASP.NET 核心 2.0 中的新增功能。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

此功能将不可用在 ASP.NET Core 1.x。

---

### <a name="server-urls"></a>服务器 Url

指示的 IP 地址或主机地址的端口和服务器应侦听的请求的协议。

**密钥**: url  
**类型**:*字符串*  
**默认**: http://localhost:5000/  
**使用设置**:`UseUrls`

设置为以分号分隔 （;） 的 URL 的列表添加到服务器应响应以下前缀。 例如 `http://localhost:123`。 使用"\*"以指示服务器应侦听上任何 IP 地址或主机名使用指定的端口和协议的请求 (例如， `http://*:5000`)。 协议 (`http://`或`https://`) 必须包含每个 URL。 支持的格式有所不同服务器。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
    ...
```

Kestrel 具有其自己的终结点配置 API。 有关详细信息，请参阅 [ASP.NET Core 中的 Kestrel Web 服务器实现](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration)。

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
    ...
```

---

### <a name="shutdown-timeout"></a>关闭超时

指定等待 web 主机关闭的时间量。

**密钥**: shutdownTimeoutSeconds  
**类型**: *int*  
**默认**: 5  
**使用设置**:`UseShutdownTimeout`

虽然密钥接受*int*与`UseSetting`(例如， `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`)，则`UseShutdownTimeout`扩展方法采用`TimeSpan`。 此功能是 ASP.NET 核心 2.0 中的新增功能。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

此功能将不可用在 ASP.NET Core 1.x。

---

### <a name="startup-assembly"></a>启动程序集

确定要搜索的程序集`Startup`类。

**密钥**: startupAssembly  
**类型**:*字符串*  
**默认**： 应用程序的程序集  
**使用设置**:`UseStartup`

您可以按名称引用程序集 (`string`) 或类型 (`TStartup`)。 如果选择多个`UseStartup`调用方法，最后一个将优先。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
    ...
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseStartup("StartupAssemblyName")
    ...
```

```csharp
var host = new WebHostBuilder()
    .UseStartup<TStartup>()
    ...
```

---

### <a name="web-root"></a>Web 根目录

设置应用的静态资产的相对路径。

**密钥**: webroot  
**类型**:*字符串*  
**默认**： 如果未指定，默认值是"(Content Root)/wwwroot"，如果该路径存在。 如果路径不存在，则使用无操作文件提供程序。  
**使用设置**:`UseWebRoot`

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseWebRoot("public")
    ...
```

---

## <a name="overriding-configuration"></a>重写配置

使用[配置](configuration.md)如何配置主机。 在以下示例中，主机配置 （可选） 中指定*hosting.json*文件。 从加载的任何配置*hosting.json*文件可能会重写通过命令行自变量。 生成的配置 (在`config`) 用于配置与主机`UseConfiguration`。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

*hosting.json*:

```json
{
    urls: "http://*:5005"
}
```

重写提供的配置`UseUrls`与*hosting.json*配置第一个、 命令行自变量配置第二个：

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        BuildWebHost(args).Run();
    }

    public static IWebHost BuildWebHost(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hosting.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            })
            .Build();
    }
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

*hosting.json*:

```json
{
    urls: "http://*:5005"
}
```

重写提供的配置`UseUrls`与*hosting.json*配置第一个、 命令行自变量配置第二个：

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hosting.json", optional: true)
            .AddCommandLine(args)
            .Build();

        var host = new WebHostBuilder()
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .UseKestrel()
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            })
            .Build();

        host.Run();
    }
}
```

---

> [!NOTE]
> `UseConfiguration`扩展方法不是当前能够分析返回的一个配置节`GetSection`(例如， `.UseConfiguration(Configuration.GetSection("section"))`。 `GetSection`方法筛选到请求的节的配置密钥，但会键上的节名称 (例如， `section:urls`， `section:environment`)。 `UseConfiguration`方法需要要匹配的键`WebHostBuilder`密钥 (例如， `urls`， `environment`)。 键的节名称存在阻止从配置主机部分的值。 将在即将发布的版本中解决此问题。 有关详细信息和解决方法，请参阅[将配置节传入到 WebHostBuilder.UseConfiguration 使用完整的密钥](https://github.com/aspnet/Hosting/issues/839)。

若要指定特定的 URL 上运行的主机，你无法在所需的值在命令提示符下执行时传递`dotnet run`。 命令行参数重写`urls`值从*hosting.json*文件和服务器侦听端口 8080:

```console
dotnet run --urls "http://*:8080"
```

## <a name="ordering-importance"></a>顺序重要性

某些`WebHostBuilder`设置第一次读取从环境变量，如果设置。 这些环境变量使用格式`ASPNETCORE_{configurationKey}`。 若要设置该服务器侦听默认情况下的 Url，你可以设置`ASPNETCORE_URLS`。

你可以通过指定配置重写任何这些环境变量值 (使用`UseConfiguration`) 或通过显式设置的值 (使用`UseSetting`或一个显式的扩展方法中，如`UseUrls`)。 主机使用无论选项上次设置的值。 如果你想要以编程方式设置的默认 URL 为一个值，但允许其与配置中重写，可以使用命令行配置之后设置的 URL。 请参阅[重写配置](#overriding-configuration)。

## <a name="starting-the-host"></a>正在启动主机

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

**运行**

`Run`方法会启动 web 应用，并阻止调用线程，直到该主机是关闭：

```csharp
host.Run();
```

**Start**

你也可以通过调用非阻止方式在运行主机其`Start`方法：

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

如果你的 Url 的列表传递`Start`方法，它侦听指定的 Url:

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

你可以初始化并开始使用的预配置的默认值的新主机`CreateDefaultBuilder`使用静态便捷方法。 这些方法启动服务器控制台输出而无需与[WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown)等待中断 （Ctrl-C/SIGINT 或 SIGTERM）：

**开始 （RequestDelegate 应用程序）**

开头`RequestDelegate`:

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

在浏览器向发出请求`http://localhost:5000`接收响应"Hello World ！" `WaitForShutdown`受到阻止，直到颁发中断 （Ctrl-C/SIGINT 或 SIGTERM）。 应用程序并显示`Console.WriteLine`消息并等待 keypress 退出。

**启动 (字符串 url，RequestDelegate 应用)**

使用 URL 启动和`RequestDelegate`:

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

生成与相同的结果**开始 （RequestDelegate 应用程序）**，除应用程序响应上`http://localhost:8080`。

**启动 (操作<IRouteBuilder>routeBuilder)**

使用的实例`IRouteBuilder`([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) 用于路由的中间件：

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

该示例中使用以下浏览器请求：

| 请求                                    | 响应                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | Hello，Martin ！                           |
| `http://localhost:5000/buenosdias/Catrina` | 布宜诺斯艾利斯 dias，Catrina ！                    |
| `http://localhost:5000/throw/ooops!`       | 引发"ooops ！"的字符串与异常 |
| `http://localhost:5000/throw`              | 用字符串引发异常"不过这噢 ！" |
| `http://localhost:5000/Sante/Kevin`        | Sante，Kevin ！                            |
| `http://localhost:5000`                    | Hello World!                             |

`WaitForShutdown`受到阻止，直到颁发中断 （Ctrl-C/SIGINT 或 SIGTERM）。 应用程序并显示`Console.WriteLine`消息并等待 keypress 退出。

**启动 (字符串 url，操作<IRouteBuilder>routeBuilder)**

使用 URL 和的实例`IRouteBuilder`:

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

生成与相同的结果**启动 (操作<IRouteBuilder>routeBuilder)**，除应用程序响应在`http://localhost:8080`。

**StartWith (操作<IApplicationBuilder>应用)**

提供一个委托，以配置`IApplicationBuilder`:

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

在浏览器向发出请求`http://localhost:5000`接收响应"Hello World ！" `WaitForShutdown`受到阻止，直到颁发中断 （Ctrl-C/SIGINT 或 SIGTERM）。 应用程序并显示`Console.WriteLine`消息并等待 keypress 退出。

**StartWith (字符串 url，操作<IApplicationBuilder>应用)**

提供一个 URL，另一个委托，以配置`IApplicationBuilder`:

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

生成与相同的结果**StartWith (操作<IApplicationBuilder>应用)**，除应用程序响应上`http://localhost:8080`。

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

**运行**

`Run`方法会启动 web 应用，并阻止调用线程，直到该主机是关闭：

```csharp
host.Run();
```

**Start**

你也可以通过调用非阻止方式在运行主机其`Start`方法：

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

如果你的 Url 的列表传递`Start`方法，它侦听指定的 Url:


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

---

## <a name="ihostingenvironment-interface"></a>IHostingEnvironment 接口

[IHostingEnvironment 接口](/aspnet/core/api/microsoft.aspnetcore.hosting.ihostingenvironment)提供有关应用程序的 web 宿主环境的信息。 你可以使用[构造函数注入](xref:fundamentals/dependency-injection)获取`IHostingEnvironment`才能使用其属性和扩展方法：

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

你可以使用[基于约定的方法](xref:fundamentals/environments#startup-conventions)在启动时基于环境配置你的应用程序。 或者，可以插入`IHostingEnvironment`到`Startup`构造函数用于`ConfigureServices`:

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> 除了`IsDevelopment`扩展方法，`IHostingEnvironment`提供`IsStaging`， `IsProduction`，和`IsEnvironment(string environmentName)`方法。 请参阅[使用多个环境](xref:fundamentals/environments)有关详细信息。

`IHostingEnvironment`服务还可以被注入直接到`Configure`设置处理管道的方法：

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the developer exception page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

可以插入`IHostingEnvironment`到`Invoke`方法时创建自定义[中间件](xref:fundamentals/middleware#writing-middleware):

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

## <a name="iapplicationlifetime-interface"></a>IApplicationLifetime 接口

[IApplicationLifetime 接口](/aspnet/core/api/microsoft.aspnetcore.hosting.iapplicationlifetime)允许你执行后启动和关闭活动。 在接口上的三个属性是在可注册的取消标记`Action`方法来定义启动和关闭事件。 此外，还有`StopApplication`方法。

| 取消标记    | 触发时 &#8230; |
| --------------------- | --------------------- |
| `ApplicationStarted`  | 主机已完全启动。 |
| `ApplicationStopping` | 主机正在执行正常关闭。 仍在处理请求。 关闭受到阻止，直到完成此事件。 |
| `ApplicationStopped`  | 主机正在完成正常关闭。 应完全处理所有请求。 关闭受到阻止，直到完成此事件。 |

| 方法            | 操作                                           |
| ----------------- | ------------------------------------------------ |
| `StopApplication` | 当前应用程序请求终止。 |

```csharp
public class Startup 
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime) 
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

## <a name="troubleshooting-systemargumentexception"></a>故障排除 System.ArgumentException

**适用于仅 ASP.NET Core 2.0**

如果你通过将注入来生成主机`IStartup`直接插入依赖关系注入容器而不是调用`UseStartup`或`Configure`，你可能会遇到以下错误： `Unhandled Exception: System.ArgumentException: A valid non-empty application name must be provided`。

这是因为[applicationName(ApplicationKey)](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostdefaults#Microsoft_AspNetCore_Hosting_WebHostDefaults_ApplicationKey) （当前程序集） 需扫描`HostingStartupAttributes`。 如果手动将注入`IStartup`到依赖关系注入容器中，添加对的以下调用你`WebHostBuilder`具有指定的程序集名称：

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("applicationName", "<Assembly Name>")
    ...
```

或者，将添加虚拟`Configure`到你`WebHostBuilder`，哪些集`applicationName`(`ApplicationKey`) 自动：

```csharp
WebHost.CreateDefaultBuilder(args)
    .Configure(_ => { })
    ...
```

**请注意**： 这是仅需要 ASP.NET 核心 2.0 发行版，只有当你不调用`UseStartup`或`Configure`。

有关详细信息，请参阅[公告： Microsoft.Extensions.PlatformAbstractions 已被删除 （注释）](https://github.com/aspnet/Announcements/issues/237#issuecomment-323786938)和[StartupInjection 示例](https://github.com/aspnet/Hosting/blob/8377d226f1e6e1a97dabdb6769a845eeccc829ed/samples/SampleStartups/StartupInjection.cs)。

## <a name="additional-resources"></a>其他资源

* [发布到使用 IIS 的 Windows](../publishing/iis.md)
* [将发布到 Linux 使用 Nginx](../publishing/linuxproduction.md)
* [将发布到使用 Apache 的 Linux](../publishing/apache-proxy.md)
* [在 Windows 服务中的主机](xref:hosting/windows-service)
