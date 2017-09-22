---
title: "在 ASP.NET Core 中承载"
author: guardrex
description: "了解有关 ASP.NET 核心，负责应用程序启动和生存期管理中的 web 主机信息。"
keywords: "ASP.NET 核心 web 主机，IWebHost、 WebHostBuilder、 IHostingEnvironment、 IApplicationLifetime"
ms.author: riande
manager: wpickett
ms.date: 09/21/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/hosting
ms.openlocfilehash: 455b992dc10129278f8e23366aac9d8bcbf5594c
ms.sourcegitcommit: ef9784dd7500f22fb98b3591ebd73d57d4f67544
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/21/2017
---
# <a name="hosting-in-aspnet-core"></a><span data-ttu-id="bd770-104">在 ASP.NET Core 中承载</span><span class="sxs-lookup"><span data-stu-id="bd770-104">Hosting in ASP.NET Core</span></span>

<span data-ttu-id="bd770-105">通过[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bd770-105">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="bd770-106">ASP.NET Core 应用配置和启动*主机*，即负责应用程序启动和生存期管理。</span><span class="sxs-lookup"><span data-stu-id="bd770-106">ASP.NET Core apps configure and launch a *host*, which is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="bd770-107">至少，主机配置服务器和请求处理管道。</span><span class="sxs-lookup"><span data-stu-id="bd770-107">At a minimum, the host configures a server and a request processing pipeline.</span></span>

## <a name="setting-up-a-host"></a><span data-ttu-id="bd770-108">设置主机</span><span class="sxs-lookup"><span data-stu-id="bd770-108">Setting up a host</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="bd770-109">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="bd770-109">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="bd770-110">创建使用的实例的主机[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder)。</span><span class="sxs-lookup"><span data-stu-id="bd770-110">Create a host using an instance of [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder).</span></span> <span data-ttu-id="bd770-111">这通常在您的应用程序的入口点，来执行`Main`方法。</span><span class="sxs-lookup"><span data-stu-id="bd770-111">This is typically performed in your app's entry point, the `Main` method.</span></span> <span data-ttu-id="bd770-112">在项目模板`Main`位于*Program.cs*。</span><span class="sxs-lookup"><span data-stu-id="bd770-112">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="bd770-113">典型*Program.cs*调用[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder)来开始设置主机：</span><span class="sxs-lookup"><span data-stu-id="bd770-113">A typical *Program.cs* calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main)]

<span data-ttu-id="bd770-114">`CreateDefaultBuilder`执行以下任务：</span><span class="sxs-lookup"><span data-stu-id="bd770-114">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="bd770-115">配置[Kestrel](servers/kestrel.md)为 web 服务器。</span><span class="sxs-lookup"><span data-stu-id="bd770-115">Configures [Kestrel](servers/kestrel.md) as the web server.</span></span> <span data-ttu-id="bd770-116">有关 Kestrel 默认选项，请参阅[Kestrel 选项部分中 ASP.NET Core Kestrel web 服务器实现](xref:fundamentals/servers/kestrel#kestrel-options)。</span><span class="sxs-lookup"><span data-stu-id="bd770-116">For the Kestrel default options, see [the Kestrel options section of Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel#kestrel-options).</span></span>
* <span data-ttu-id="bd770-117">将内容的根设置为[Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory)。</span><span class="sxs-lookup"><span data-stu-id="bd770-117">Sets the content root to [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="bd770-118">从加载可选配置：</span><span class="sxs-lookup"><span data-stu-id="bd770-118">Loads optional configuration from:</span></span>
  * <span data-ttu-id="bd770-119">*appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="bd770-119">*appsettings.json*.</span></span>
  * <span data-ttu-id="bd770-120">*appsettings。{环境}.json*。</span><span class="sxs-lookup"><span data-stu-id="bd770-120">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="bd770-121">[用户的机密信息](xref:security/app-secrets)运行的应用`Development`环境。</span><span class="sxs-lookup"><span data-stu-id="bd770-121">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="bd770-122">环境变量。</span><span class="sxs-lookup"><span data-stu-id="bd770-122">Environment variables.</span></span>
  * <span data-ttu-id="bd770-123">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="bd770-123">Command-line arguments.</span></span>
* <span data-ttu-id="bd770-124">配置[日志记录](xref:fundamentals/logging)与控制台和调试输出[日志筛选](xref:fundamentals/logging#log-filtering)日志记录配置部分中指定的规则*appsettings.json*或*appsettings。{环境}.json*文件。</span><span class="sxs-lookup"><span data-stu-id="bd770-124">Configures [logging](xref:fundamentals/logging) for console and debug output with [log filtering](xref:fundamentals/logging#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="bd770-125">当运行 IIS 之后，可让[IIS 集成](xref:publishing/iis)通过配置的基路径和端口服务器应对其侦听时使用[ASP.NET 核心模块](xref:fundamentals/servers/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="bd770-125">When running behind IIS, enables [IIS integration](xref:publishing/iis) by configuring the base path and port the server should listen on when using the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span></span> <span data-ttu-id="bd770-126">该模块将创建 IIS 和 Kestrel 之间的反向代理。</span><span class="sxs-lookup"><span data-stu-id="bd770-126">The module creates a reverse-proxy between IIS and Kestrel.</span></span> <span data-ttu-id="bd770-127">此外可以配置应用到[捕获启动错误](#capture-startup-errors)。</span><span class="sxs-lookup"><span data-stu-id="bd770-127">Also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="bd770-128">有关 IIS 默认选项，请参阅[IIS 选项部分中的主机与 IIS 的 Windows 上的 ASP.NET 核心](xref:publishing/iis#iis-options)。</span><span class="sxs-lookup"><span data-stu-id="bd770-128">For the IIS default options, see [the IIS options section of Host ASP.NET Core on Windows with IIS](xref:publishing/iis#iis-options).</span></span>

<span data-ttu-id="bd770-129">*内容根*确定主机搜索内容的文件，如 MVC 视图文件的位置。</span><span class="sxs-lookup"><span data-stu-id="bd770-129">The *content root* determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="bd770-130">默认内容根是[Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory)。</span><span class="sxs-lookup"><span data-stu-id="bd770-130">The default content root is [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span> <span data-ttu-id="bd770-131">这会导致从根文件夹中启动应用时将 web 项目的根文件夹用作内容的根 (例如，调用[dotnet 运行](/dotnet/core/tools/dotnet-run)项目文件夹中)。</span><span class="sxs-lookup"><span data-stu-id="bd770-131">This results in using the web project's root folder as the content root when the app is started from the root folder (for example, calling [dotnet run](/dotnet/core/tools/dotnet-run) from the project folder).</span></span> <span data-ttu-id="bd770-132">这是默认值中使用[Visual Studio](https://www.visualstudio.com/)和[dotnet 新模板](/dotnet/core/tools/dotnet-new)。</span><span class="sxs-lookup"><span data-stu-id="bd770-132">This is the default used in [Visual Studio](https://www.visualstudio.com/) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="bd770-133">请参阅[ASP.NET 核心中的配置](xref:fundamentals/configuration)有关应用程序配置的详细信息。</span><span class="sxs-lookup"><span data-stu-id="bd770-133">See [Configuration in ASP.NET Core](xref:fundamentals/configuration) for more information on app configuration.</span></span>

> [!NOTE]
> <span data-ttu-id="bd770-134">作为使用静态的替代方法`CreateDefaultBuilder`方法，创建从宿主[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder)是一种受支持的方法与 ASP.NET 核心 2.x。</span><span class="sxs-lookup"><span data-stu-id="bd770-134">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span> <span data-ttu-id="bd770-135">请参阅 ASP.NET Core 1.x 选项卡的详细信息。</span><span class="sxs-lookup"><span data-stu-id="bd770-135">See the ASP.NET Core 1.x tab for more information.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="bd770-136">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="bd770-136">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="bd770-137">创建使用的实例的主机[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder)。</span><span class="sxs-lookup"><span data-stu-id="bd770-137">Create a host using an instance of [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder).</span></span> <span data-ttu-id="bd770-138">这通常在您的应用程序的入口点，来执行`Main`方法。</span><span class="sxs-lookup"><span data-stu-id="bd770-138">This is typically performed in your app's entry point, the `Main` method.</span></span> <span data-ttu-id="bd770-139">在项目模板`Main`位于*Program.cs*。</span><span class="sxs-lookup"><span data-stu-id="bd770-139">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="bd770-140">以下*Program.cs*演示如何使用`WebHostBuilder`来生成主机：</span><span class="sxs-lookup"><span data-stu-id="bd770-140">The following *Program.cs* demonstrates how to use `WebHostBuilder` to build the host:</span></span>

[!code-csharp[Main](../common/samples/WebApplication1/Program.cs)]

<span data-ttu-id="bd770-141">`WebHostBuilder`需要[服务器实现 IServer](servers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="bd770-141">`WebHostBuilder` requires a [server that implements IServer](servers/index.md).</span></span> <span data-ttu-id="bd770-142">内置服务器[Kestrel](servers/kestrel.md)和[HTTP.sys](servers/httpsys.md) (之前的 ASP.NET 核心 2.0 版本中，HTTP.sys 调用[WebListener](xref:fundamentals/servers/weblistener))。</span><span class="sxs-lookup"><span data-stu-id="bd770-142">The built-in servers are [Kestrel](servers/kestrel.md) and [HTTP.sys](servers/httpsys.md) (prior to the release of ASP.NET Core 2.0, HTTP.sys was called [WebListener](xref:fundamentals/servers/weblistener)).</span></span> <span data-ttu-id="bd770-143">在此示例中， [UseKestrel 扩展方法](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1)指定的 Kestrel 服务器。</span><span class="sxs-lookup"><span data-stu-id="bd770-143">In this example, the [UseKestrel extension method](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) specifies the Kestrel server.</span></span>

<span data-ttu-id="bd770-144">*内容根*确定主机搜索内容的文件，如 MVC 视图文件的位置。</span><span class="sxs-lookup"><span data-stu-id="bd770-144">The *content root* determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="bd770-145">默认内容根提供给`UseContentRoot`是[Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory?view=netcore-1.1)。</span><span class="sxs-lookup"><span data-stu-id="bd770-145">The default content root supplied to `UseContentRoot` is [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory?view=netcore-1.1).</span></span> <span data-ttu-id="bd770-146">这会导致从根文件夹中启动应用时将 web 项目的根文件夹用作内容的根 (例如，调用[dotnet 运行](/dotnet/core/tools/dotnet-run)项目文件夹中)。</span><span class="sxs-lookup"><span data-stu-id="bd770-146">This results in using the web project's root folder as the content root when the app is started from the root folder (for example, calling [dotnet run](/dotnet/core/tools/dotnet-run) from the project folder).</span></span> <span data-ttu-id="bd770-147">这是默认值中使用[Visual Studio](https://www.visualstudio.com/)和[dotnet 新模板](/dotnet/core/tools/dotnet-new)。</span><span class="sxs-lookup"><span data-stu-id="bd770-147">This is the default used in [Visual Studio](https://www.visualstudio.com/) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="bd770-148">若要使用 IIS 的反向代理，调用[UseIISIntegration](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions)生成主机的一部分。</span><span class="sxs-lookup"><span data-stu-id="bd770-148">To use IIS as a reverse proxy, call [UseIISIntegration](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions) as part of building the host.</span></span> <span data-ttu-id="bd770-149">`UseIISIntegration`不配置*服务器*、 like [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1)未。</span><span class="sxs-lookup"><span data-stu-id="bd770-149">`UseIISIntegration` doesn't configure a *server*, like [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) does.</span></span> <span data-ttu-id="bd770-150">`UseIISIntegration`配置的基路径和服务器应对其侦听时使用的端口[ASP.NET 核心模块](xref:fundamentals/servers/aspnet-core-module)创建 Kestrel 和 IIS 之间的反向代理。</span><span class="sxs-lookup"><span data-stu-id="bd770-150">`UseIISIntegration` configures the base path and port the server should listen on when using the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) to create a reverse-proxy between Kestrel and IIS.</span></span> <span data-ttu-id="bd770-151">若要使用 ASP.NET Core IIS，则必须同时指定`UseKestrel`和`UseIISIntegration`。</span><span class="sxs-lookup"><span data-stu-id="bd770-151">To use IIS with ASP.NET Core, you must specify both `UseKestrel` and `UseIISIntegration`.</span></span> <span data-ttu-id="bd770-152">`UseIISIntegration`仅当在 IIS 或 IIS Express 后面运行时激活。</span><span class="sxs-lookup"><span data-stu-id="bd770-152">`UseIISIntegration` only activates when running behind IIS or IIS Express.</span></span> <span data-ttu-id="bd770-153">有关详细信息，请参阅[简介 ASP.NET 核心模块](xref:fundamentals/servers/aspnet-core-module)和[ASP.NET 核心模块配置引用](xref:hosting/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="bd770-153">For more information, see [Introduction to ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) and [ASP.NET Core Module configuration reference](xref:hosting/aspnet-core-module).</span></span>

<span data-ttu-id="bd770-154">配置主机 （和 ASP.NET Core 应用） 的最小实现包括指定服务器和应用程序的请求管道的配置项：</span><span class="sxs-lookup"><span data-stu-id="bd770-154">A minimal implementation that configures a host (and an ASP.NET Core app) includes specifying a server and configuration of the app's request pipeline:</span></span>

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

<span data-ttu-id="bd770-155">设置时主机，你可以提供[配置](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure?view=aspnetcore-1.1)和[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices?view=aspnetcore-1.1)方法。</span><span class="sxs-lookup"><span data-stu-id="bd770-155">When setting up a host, you can provide [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure?view=aspnetcore-1.1) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices?view=aspnetcore-1.1) methods.</span></span> <span data-ttu-id="bd770-156">如果指定`Startup`类，它必须定义`Configure`方法。</span><span class="sxs-lookup"><span data-stu-id="bd770-156">If you specify a `Startup` class, it must define a `Configure` method.</span></span> <span data-ttu-id="bd770-157">有关详细信息，请参阅[在 ASP.NET Core 中的应用程序启动](startup.md)。</span><span class="sxs-lookup"><span data-stu-id="bd770-157">For more information, see [Application Startup in ASP.NET Core](startup.md).</span></span> <span data-ttu-id="bd770-158">多次调用`ConfigureServices`将追加到另一个。</span><span class="sxs-lookup"><span data-stu-id="bd770-158">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="bd770-159">多次调用`Configure`或`UseStartup`上`WebHostBuilder`替换以前的设置。</span><span class="sxs-lookup"><span data-stu-id="bd770-159">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="bd770-160">主机配置值</span><span class="sxs-lookup"><span data-stu-id="bd770-160">Host configuration values</span></span>

<span data-ttu-id="bd770-161">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder)提供用于设置大多数可用的配置值的主机，也可直接与设置的方法[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting)和关联的密钥。</span><span class="sxs-lookup"><span data-stu-id="bd770-161">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) provides methods for setting most of the available configuration values for the host, which can also be set directly with [UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="bd770-162">设置一个值，该值时`UseSetting`，值设置为 （用引号引起来） 无论何种类型的字符串。</span><span class="sxs-lookup"><span data-stu-id="bd770-162">When setting a value with `UseSetting`, the value is set as a string (in quotes) regardless of the type.</span></span>

### <a name="capture-startup-errors"></a><span data-ttu-id="bd770-163">捕获启动错误</span><span class="sxs-lookup"><span data-stu-id="bd770-163">Capture Startup Errors</span></span>

<span data-ttu-id="bd770-164">此设置控制启动错误的捕获。</span><span class="sxs-lookup"><span data-stu-id="bd770-164">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="bd770-165">**密钥**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="bd770-165">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="bd770-166">**类型**: *bool* (`true`或`1`)</span><span class="sxs-lookup"><span data-stu-id="bd770-166">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="bd770-167">**默认**： 默认为`false`除非应用程序下运行的 IIS，其中默认值是后面的 Kestrel `true`。</span><span class="sxs-lookup"><span data-stu-id="bd770-167">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="bd770-168">**使用设置**:`CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="bd770-168">**Set using**: `CaptureStartupErrors`</span></span>

<span data-ttu-id="bd770-169">当`false`，退出主机中启动结果时出错。</span><span class="sxs-lookup"><span data-stu-id="bd770-169">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="bd770-170">当`true`，主机在启动期间捕获异常并尝试启动服务器。</span><span class="sxs-lookup"><span data-stu-id="bd770-170">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="bd770-171">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="bd770-171">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="bd770-172">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="bd770-172">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .CaptureStartupErrors(true)
    ...
```

---

### <a name="content-root"></a><span data-ttu-id="bd770-173">内容的根</span><span class="sxs-lookup"><span data-stu-id="bd770-173">Content Root</span></span>

<span data-ttu-id="bd770-174">此设置确定 ASP.NET Core 开始搜索内容的文件，MVC 视图等。</span><span class="sxs-lookup"><span data-stu-id="bd770-174">This setting determines where ASP.NET Core begins searching for content files, such as MVC views.</span></span> 

<span data-ttu-id="bd770-175">**密钥**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="bd770-175">**Key**: contentRoot</span></span>  
<span data-ttu-id="bd770-176">**类型**:*字符串*</span><span class="sxs-lookup"><span data-stu-id="bd770-176">**Type**: *string*</span></span>  
<span data-ttu-id="bd770-177">**默认**： 默认为应用程序集所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="bd770-177">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="bd770-178">**使用设置**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="bd770-178">**Set using**: `UseContentRoot`</span></span>

<span data-ttu-id="bd770-179">内容的根也用作的基路径[Web 根目录设置](#web-root)。</span><span class="sxs-lookup"><span data-stu-id="bd770-179">The content root is also used as the base path for the [Web Root setting](#web-root).</span></span> <span data-ttu-id="bd770-180">如果路径不存在，主机将无法启动。</span><span class="sxs-lookup"><span data-stu-id="bd770-180">If the path doesn't exist, the host fails to start.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="bd770-181">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="bd770-181">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\mywebsite")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="bd770-182">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="bd770-182">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseContentRoot("c:\\mywebsite")
    ...
```

---

### <a name="detailed-errors"></a><span data-ttu-id="bd770-183">详细的错误</span><span class="sxs-lookup"><span data-stu-id="bd770-183">Detailed Errors</span></span>

<span data-ttu-id="bd770-184">确定详细应该捕获错误。</span><span class="sxs-lookup"><span data-stu-id="bd770-184">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="bd770-185">**密钥**： 之后，请</span><span class="sxs-lookup"><span data-stu-id="bd770-185">**Key**: detailedErrors</span></span>  
<span data-ttu-id="bd770-186">**类型**: *bool* (`true`或`1`)</span><span class="sxs-lookup"><span data-stu-id="bd770-186">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="bd770-187">**默认**: false</span><span class="sxs-lookup"><span data-stu-id="bd770-187">**Default**: false</span></span>  
<span data-ttu-id="bd770-188">**使用设置**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="bd770-188">**Set using**: `UseSetting`</span></span>

<span data-ttu-id="bd770-189">启用 (或当<a href="#environment">环境</a>设置为`Development`)，应用程序捕获详细的异常。</span><span class="sxs-lookup"><span data-stu-id="bd770-189">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="bd770-190">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="bd770-190">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="bd770-191">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="bd770-191">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
    ...
```

---

### <a name="environment"></a><span data-ttu-id="bd770-192">环境</span><span class="sxs-lookup"><span data-stu-id="bd770-192">Environment</span></span>

<span data-ttu-id="bd770-193">设置应用程序的环境。</span><span class="sxs-lookup"><span data-stu-id="bd770-193">Sets the app's environment.</span></span>

<span data-ttu-id="bd770-194">**密钥**： 环境</span><span class="sxs-lookup"><span data-stu-id="bd770-194">**Key**: environment</span></span>  
<span data-ttu-id="bd770-195">**类型**:*字符串*</span><span class="sxs-lookup"><span data-stu-id="bd770-195">**Type**: *string*</span></span>  
<span data-ttu-id="bd770-196">**默认**： 生产</span><span class="sxs-lookup"><span data-stu-id="bd770-196">**Default**: Production</span></span>  
<span data-ttu-id="bd770-197">**使用设置**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="bd770-197">**Set using**: `UseEnvironment`</span></span>

<span data-ttu-id="bd770-198">你可以设置*环境*为任何值。</span><span class="sxs-lookup"><span data-stu-id="bd770-198">You can set the *Environment* to any value.</span></span> <span data-ttu-id="bd770-199">框架定义的值包括`Development`， `Staging`，和`Production`。</span><span class="sxs-lookup"><span data-stu-id="bd770-199">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="bd770-200">值都不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="bd770-200">Values aren't case sensitive.</span></span> <span data-ttu-id="bd770-201">默认情况下，*环境*从读取`ASPNETCORE_ENVIRONMENT`环境变量。</span><span class="sxs-lookup"><span data-stu-id="bd770-201">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="bd770-202">使用时[Visual Studio](https://www.visualstudio.com/)，可能会设置环境变量*launchSettings.json*文件。</span><span class="sxs-lookup"><span data-stu-id="bd770-202">When using [Visual Studio](https://www.visualstudio.com/), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="bd770-203">有关详细信息，请参阅[使用多个环境](xref:fundamentals/environments)。</span><span class="sxs-lookup"><span data-stu-id="bd770-203">For more information, see [Working with Multiple Environments](xref:fundamentals/environments).</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="bd770-204">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="bd770-204">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="bd770-205">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="bd770-205">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseEnvironment("Development")
    ...
```

---

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="bd770-206">承载启动程序集</span><span class="sxs-lookup"><span data-stu-id="bd770-206">Hosting Startup Assemblies</span></span>

<span data-ttu-id="bd770-207">设置应用程序的宿主启动程序集。</span><span class="sxs-lookup"><span data-stu-id="bd770-207">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="bd770-208">**密钥**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="bd770-208">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="bd770-209">**类型**:*字符串*</span><span class="sxs-lookup"><span data-stu-id="bd770-209">**Type**: *string*</span></span>  
<span data-ttu-id="bd770-210">**默认**： 空字符串</span><span class="sxs-lookup"><span data-stu-id="bd770-210">**Default**: Empty string</span></span>  
<span data-ttu-id="bd770-211">**使用设置**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="bd770-211">**Set using**: `UseSetting`</span></span>

<span data-ttu-id="bd770-212">托管要在启动时加载的启动程序集的以分号分隔的字符串。</span><span class="sxs-lookup"><span data-stu-id="bd770-212">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="bd770-213">此功能是 ASP.NET 核心 2.0 中的新增功能。</span><span class="sxs-lookup"><span data-stu-id="bd770-213">This feature is new in ASP.NET Core 2.0.</span></span>

<span data-ttu-id="bd770-214">虽然配置值默认为空字符串，宿主启动程序集将始终包括应用程序的程序集。</span><span class="sxs-lookup"><span data-stu-id="bd770-214">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="bd770-215">在提供宿主启动程序集时，它们正在加载应用程序在启动过程中生成其公共服务时添加到应用程序的程序集。</span><span class="sxs-lookup"><span data-stu-id="bd770-215">When you provide hosting startup assemblies, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="bd770-216">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="bd770-216">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="bd770-217">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="bd770-217">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="bd770-218">此功能将不可用在 ASP.NET Core 1.x。</span><span class="sxs-lookup"><span data-stu-id="bd770-218">This feature is unavailable in ASP.NET Core 1.x.</span></span>

---

### <a name="prefer-hosting-urls"></a><span data-ttu-id="bd770-219">选择承载 Url</span><span class="sxs-lookup"><span data-stu-id="bd770-219">Prefer Hosting URLs</span></span>

<span data-ttu-id="bd770-220">指示是否应在使用配置的 Url 上侦听主机`WebHostBuilder`而不使用配置`IServer`实现。</span><span class="sxs-lookup"><span data-stu-id="bd770-220">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="bd770-221">**密钥**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="bd770-221">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="bd770-222">**类型**: *bool* (`true`或`1`)</span><span class="sxs-lookup"><span data-stu-id="bd770-222">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="bd770-223">**默认**: true</span><span class="sxs-lookup"><span data-stu-id="bd770-223">**Default**: true</span></span>  
<span data-ttu-id="bd770-224">**使用设置**:`PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="bd770-224">**Set using**: `PreferHostingUrls`</span></span>

<span data-ttu-id="bd770-225">此功能是 ASP.NET 核心 2.0 中的新增功能。</span><span class="sxs-lookup"><span data-stu-id="bd770-225">This feature is new in ASP.NET Core 2.0.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="bd770-226">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="bd770-226">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="bd770-227">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="bd770-227">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="bd770-228">此功能将不可用在 ASP.NET Core 1.x。</span><span class="sxs-lookup"><span data-stu-id="bd770-228">This feature is unavailable in ASP.NET Core 1.x.</span></span>

---

### <a name="prevent-hosting-startup"></a><span data-ttu-id="bd770-229">阻止托管启动</span><span class="sxs-lookup"><span data-stu-id="bd770-229">Prevent Hosting Startup</span></span>

<span data-ttu-id="bd770-230">可防止托管启动程序集，包括应用程序的程序集的自动加载。</span><span class="sxs-lookup"><span data-stu-id="bd770-230">Prevents the automatic loading of hosting startup assemblies, including the app's assembly.</span></span>

<span data-ttu-id="bd770-231">**密钥**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="bd770-231">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="bd770-232">**类型**: *bool* (`true`或`1`)</span><span class="sxs-lookup"><span data-stu-id="bd770-232">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="bd770-233">**默认**: false</span><span class="sxs-lookup"><span data-stu-id="bd770-233">**Default**: false</span></span>  
<span data-ttu-id="bd770-234">**使用设置**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="bd770-234">**Set using**: `UseSetting`</span></span>

<span data-ttu-id="bd770-235">此功能是 ASP.NET 核心 2.0 中的新增功能。</span><span class="sxs-lookup"><span data-stu-id="bd770-235">This feature is new in ASP.NET Core 2.0.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="bd770-236">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="bd770-236">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="bd770-237">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="bd770-237">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="bd770-238">此功能将不可用在 ASP.NET Core 1.x。</span><span class="sxs-lookup"><span data-stu-id="bd770-238">This feature is unavailable in ASP.NET Core 1.x.</span></span>

---

### <a name="server-urls"></a><span data-ttu-id="bd770-239">服务器 Url</span><span class="sxs-lookup"><span data-stu-id="bd770-239">Server URLs</span></span>

<span data-ttu-id="bd770-240">指示的 IP 地址或主机地址的端口和服务器应侦听的请求的协议。</span><span class="sxs-lookup"><span data-stu-id="bd770-240">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="bd770-241">**密钥**: url</span><span class="sxs-lookup"><span data-stu-id="bd770-241">**Key**: urls</span></span>  
<span data-ttu-id="bd770-242">**类型**:*字符串*</span><span class="sxs-lookup"><span data-stu-id="bd770-242">**Type**: *string*</span></span>  
<span data-ttu-id="bd770-243">**默认**: http://localhost:5000/</span><span class="sxs-lookup"><span data-stu-id="bd770-243">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="bd770-244">**使用设置**:`UseUrls`</span><span class="sxs-lookup"><span data-stu-id="bd770-244">**Set using**: `UseUrls`</span></span>

<span data-ttu-id="bd770-245">设置为以分号分隔 （;） 的 URL 的列表添加到服务器应响应以下前缀。</span><span class="sxs-lookup"><span data-stu-id="bd770-245">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="bd770-246">例如 `http://localhost:123`。</span><span class="sxs-lookup"><span data-stu-id="bd770-246">For example, `http://localhost:123`.</span></span> <span data-ttu-id="bd770-247">使用"\*"以指示服务器应侦听上任何 IP 地址或主机名使用指定的端口和协议的请求 (例如， `http://*:5000`)。</span><span class="sxs-lookup"><span data-stu-id="bd770-247">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="bd770-248">协议 (`http://`或`https://`) 必须包含每个 URL。</span><span class="sxs-lookup"><span data-stu-id="bd770-248">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="bd770-249">支持的格式有所不同服务器。</span><span class="sxs-lookup"><span data-stu-id="bd770-249">Supported formats vary between servers.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="bd770-250">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="bd770-250">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
    ...
```

<span data-ttu-id="bd770-251">Kestrel 具有其自己的终结点配置 API。</span><span class="sxs-lookup"><span data-stu-id="bd770-251">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="bd770-252">有关详细信息，请参阅 [ASP.NET Core 中的 Kestrel Web 服务器实现](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration)。</span><span class="sxs-lookup"><span data-stu-id="bd770-252">For more information, see [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration).</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="bd770-253">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="bd770-253">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
    ...
```

---

### <a name="shutdown-timeout"></a><span data-ttu-id="bd770-254">关闭超时</span><span class="sxs-lookup"><span data-stu-id="bd770-254">Shutdown Timeout</span></span>

<span data-ttu-id="bd770-255">指定等待 web 主机关闭的时间量。</span><span class="sxs-lookup"><span data-stu-id="bd770-255">Specifies the amount of time to wait for the web host to shutdown.</span></span>

<span data-ttu-id="bd770-256">**密钥**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="bd770-256">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="bd770-257">**类型**: *int*</span><span class="sxs-lookup"><span data-stu-id="bd770-257">**Type**: *int*</span></span>  
<span data-ttu-id="bd770-258">**默认**: 5</span><span class="sxs-lookup"><span data-stu-id="bd770-258">**Default**: 5</span></span>  
<span data-ttu-id="bd770-259">**使用设置**:`UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="bd770-259">**Set using**: `UseShutdownTimeout`</span></span>

<span data-ttu-id="bd770-260">虽然密钥接受*int*与`UseSetting`(例如， `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`)，则`UseShutdownTimeout`扩展方法采用`TimeSpan`。</span><span class="sxs-lookup"><span data-stu-id="bd770-260">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the `UseShutdownTimeout` extension method takes a `TimeSpan`.</span></span> <span data-ttu-id="bd770-261">此功能是 ASP.NET 核心 2.0 中的新增功能。</span><span class="sxs-lookup"><span data-stu-id="bd770-261">This feature is new in ASP.NET Core 2.0.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="bd770-262">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="bd770-262">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="bd770-263">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="bd770-263">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="bd770-264">此功能将不可用在 ASP.NET Core 1.x。</span><span class="sxs-lookup"><span data-stu-id="bd770-264">This feature is unavailable in ASP.NET Core 1.x.</span></span>

---

### <a name="startup-assembly"></a><span data-ttu-id="bd770-265">启动程序集</span><span class="sxs-lookup"><span data-stu-id="bd770-265">Startup Assembly</span></span>

<span data-ttu-id="bd770-266">确定要搜索的程序集`Startup`类。</span><span class="sxs-lookup"><span data-stu-id="bd770-266">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="bd770-267">**密钥**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="bd770-267">**Key**: startupAssembly</span></span>  
<span data-ttu-id="bd770-268">**类型**:*字符串*</span><span class="sxs-lookup"><span data-stu-id="bd770-268">**Type**: *string*</span></span>  
<span data-ttu-id="bd770-269">**默认**： 应用程序的程序集</span><span class="sxs-lookup"><span data-stu-id="bd770-269">**Default**: The app's assembly</span></span>  
<span data-ttu-id="bd770-270">**使用设置**:`UseStartup`</span><span class="sxs-lookup"><span data-stu-id="bd770-270">**Set using**: `UseStartup`</span></span>

<span data-ttu-id="bd770-271">您可以按名称引用程序集 (`string`) 或类型 (`TStartup`)。</span><span class="sxs-lookup"><span data-stu-id="bd770-271">You can reference the assembly by name (`string`) or type (`TStartup`).</span></span> <span data-ttu-id="bd770-272">如果选择多个`UseStartup`调用方法，最后一个将优先。</span><span class="sxs-lookup"><span data-stu-id="bd770-272">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="bd770-273">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="bd770-273">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="bd770-274">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="bd770-274">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

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

### <a name="web-root"></a><span data-ttu-id="bd770-275">Web 根目录</span><span class="sxs-lookup"><span data-stu-id="bd770-275">Web Root</span></span>

<span data-ttu-id="bd770-276">设置应用的静态资产的相对路径。</span><span class="sxs-lookup"><span data-stu-id="bd770-276">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="bd770-277">**密钥**: webroot</span><span class="sxs-lookup"><span data-stu-id="bd770-277">**Key**: webroot</span></span>  
<span data-ttu-id="bd770-278">**类型**:*字符串*</span><span class="sxs-lookup"><span data-stu-id="bd770-278">**Type**: *string*</span></span>  
<span data-ttu-id="bd770-279">**默认**： 如果未指定，默认值是"(Content Root)/wwwroot"，如果该路径存在。</span><span class="sxs-lookup"><span data-stu-id="bd770-279">**Default**: If not specified, the default is "(Content Root)/wwwroot", if the path exists.</span></span> <span data-ttu-id="bd770-280">如果路径不存在，则使用无操作文件提供程序。</span><span class="sxs-lookup"><span data-stu-id="bd770-280">If the path doesn't exist, then a no-op file provider is used.</span></span>  
<span data-ttu-id="bd770-281">**使用设置**:`UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="bd770-281">**Set using**: `UseWebRoot`</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="bd770-282">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="bd770-282">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="bd770-283">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="bd770-283">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseWebRoot("public")
    ...
```

---

## <a name="overriding-configuration"></a><span data-ttu-id="bd770-284">重写配置</span><span class="sxs-lookup"><span data-stu-id="bd770-284">Overriding configuration</span></span>

<span data-ttu-id="bd770-285">使用[配置](configuration.md)如何配置主机。</span><span class="sxs-lookup"><span data-stu-id="bd770-285">Use [Configuration](configuration.md) to configure the host.</span></span> <span data-ttu-id="bd770-286">在以下示例中，主机配置 （可选） 中指定*hosting.json*文件。</span><span class="sxs-lookup"><span data-stu-id="bd770-286">In the following example, host configuration is optionally specified in a *hosting.json* file.</span></span> <span data-ttu-id="bd770-287">从加载的任何配置*hosting.json*文件可能会重写通过命令行自变量。</span><span class="sxs-lookup"><span data-stu-id="bd770-287">Any configuration loaded from the *hosting.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="bd770-288">生成的配置 (在`config`) 用于配置与主机`UseConfiguration`。</span><span class="sxs-lookup"><span data-stu-id="bd770-288">The built configuration (in `config`) is used to configure the host with `UseConfiguration`.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="bd770-289">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="bd770-289">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="bd770-290">*hosting.json*:</span><span class="sxs-lookup"><span data-stu-id="bd770-290">*hosting.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

<span data-ttu-id="bd770-291">重写提供的配置`UseUrls`与*hosting.json*配置第一个、 命令行自变量配置第二个：</span><span class="sxs-lookup"><span data-stu-id="bd770-291">Overriding the configuration provided by `UseUrls` with *hosting.json* config first, command-line argument config second:</span></span>

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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="bd770-292">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="bd770-292">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="bd770-293">*hosting.json*:</span><span class="sxs-lookup"><span data-stu-id="bd770-293">*hosting.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

<span data-ttu-id="bd770-294">重写提供的配置`UseUrls`与*hosting.json*配置第一个、 命令行自变量配置第二个：</span><span class="sxs-lookup"><span data-stu-id="bd770-294">Overriding the configuration provided by `UseUrls` with *hosting.json* config first, command-line argument config second:</span></span>

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
> <span data-ttu-id="bd770-295">`UseConfiguration`扩展方法不是当前能够分析返回的一个配置节`GetSection`(例如， `.UseConfiguration(Configuration.GetSection("section"))`。</span><span class="sxs-lookup"><span data-stu-id="bd770-295">The `UseConfiguration` extension method isn't currently capable of parsing a configuration section returned by `GetSection` (for example, `.UseConfiguration(Configuration.GetSection("section"))`.</span></span> <span data-ttu-id="bd770-296">`GetSection`方法筛选到请求的节的配置密钥，但会键上的节名称 (例如， `section:urls`， `section:environment`)。</span><span class="sxs-lookup"><span data-stu-id="bd770-296">The `GetSection` method filters the configuration keys to the section requested but leaves the section name on the keys (for example, `section:urls`, `section:environment`).</span></span> <span data-ttu-id="bd770-297">`UseConfiguration`方法需要要匹配的键`WebHostBuilder`密钥 (例如， `urls`， `environment`)。</span><span class="sxs-lookup"><span data-stu-id="bd770-297">The `UseConfiguration` method expects the keys to match the `WebHostBuilder` keys (for example, `urls`, `environment`).</span></span> <span data-ttu-id="bd770-298">键的节名称存在阻止从配置主机部分的值。</span><span class="sxs-lookup"><span data-stu-id="bd770-298">The presence of the section name on the keys prevents the section's values from configuring the host.</span></span> <span data-ttu-id="bd770-299">将在即将发布的版本中解决此问题。</span><span class="sxs-lookup"><span data-stu-id="bd770-299">This issue will be addressed in an upcoming release.</span></span> <span data-ttu-id="bd770-300">有关详细信息和解决方法，请参阅[将配置节传入到 WebHostBuilder.UseConfiguration 使用完整的密钥](https://github.com/aspnet/Hosting/issues/839)。</span><span class="sxs-lookup"><span data-stu-id="bd770-300">For more information and workarounds, see [Passing configuration section into WebHostBuilder.UseConfiguration uses full keys](https://github.com/aspnet/Hosting/issues/839).</span></span>

<span data-ttu-id="bd770-301">若要指定特定的 URL 上运行的主机，你无法在所需的值在命令提示符下执行时传递`dotnet run`。</span><span class="sxs-lookup"><span data-stu-id="bd770-301">To specify the host run on a particular URL, you could pass in the desired value from a command prompt when executing `dotnet run`.</span></span> <span data-ttu-id="bd770-302">命令行参数重写`urls`值从*hosting.json*文件和服务器侦听端口 8080:</span><span class="sxs-lookup"><span data-stu-id="bd770-302">The command-line argument overrides the `urls` value from the *hosting.json* file, and the server listens on port 8080:</span></span>

```console
dotnet run --urls "http://*:8080"
```

## <a name="ordering-importance"></a><span data-ttu-id="bd770-303">顺序重要性</span><span class="sxs-lookup"><span data-stu-id="bd770-303">Ordering importance</span></span>

<span data-ttu-id="bd770-304">某些`WebHostBuilder`设置第一次读取从环境变量，如果设置。</span><span class="sxs-lookup"><span data-stu-id="bd770-304">Some of the `WebHostBuilder` settings are first read from environment variables, if set.</span></span> <span data-ttu-id="bd770-305">这些环境变量使用格式`ASPNETCORE_{configurationKey}`。</span><span class="sxs-lookup"><span data-stu-id="bd770-305">These environment variables use the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="bd770-306">若要设置该服务器侦听默认情况下的 Url，你可以设置`ASPNETCORE_URLS`。</span><span class="sxs-lookup"><span data-stu-id="bd770-306">To set the URLs that the server listens on by default, you set `ASPNETCORE_URLS`.</span></span>

<span data-ttu-id="bd770-307">你可以通过指定配置重写任何这些环境变量值 (使用`UseConfiguration`) 或通过显式设置的值 (使用`UseSetting`或一个显式的扩展方法中，如`UseUrls`)。</span><span class="sxs-lookup"><span data-stu-id="bd770-307">You can override any of these environment variable values by specifying configuration (using `UseConfiguration`) or by setting the value explicitly (using `UseSetting` or one of the explicit extension methods, such as `UseUrls`).</span></span> <span data-ttu-id="bd770-308">主机使用无论选项上次设置的值。</span><span class="sxs-lookup"><span data-stu-id="bd770-308">The host uses whichever option sets the value last.</span></span> <span data-ttu-id="bd770-309">如果你想要以编程方式设置的默认 URL 为一个值，但允许其与配置中重写，可以使用命令行配置之后设置的 URL。</span><span class="sxs-lookup"><span data-stu-id="bd770-309">If you want to programmatically set the default URL to one value but allow it to be overridden with configuration, you can use command-line configuration after setting the URL.</span></span> <span data-ttu-id="bd770-310">请参阅[重写配置](#overriding-configuration)。</span><span class="sxs-lookup"><span data-stu-id="bd770-310">See [Overriding configuration](#overriding-configuration).</span></span>

## <a name="starting-the-host"></a><span data-ttu-id="bd770-311">正在启动主机</span><span class="sxs-lookup"><span data-stu-id="bd770-311">Starting the host</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="bd770-312">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="bd770-312">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="bd770-313">**运行**</span><span class="sxs-lookup"><span data-stu-id="bd770-313">**Run**</span></span>

<span data-ttu-id="bd770-314">`Run`方法会启动 web 应用，并阻止调用线程，直到该主机是关闭：</span><span class="sxs-lookup"><span data-stu-id="bd770-314">The `Run` method starts the web app and blocks the calling thread until the host is shutdown:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="bd770-315">**Start**</span><span class="sxs-lookup"><span data-stu-id="bd770-315">**Start**</span></span>

<span data-ttu-id="bd770-316">你也可以通过调用非阻止方式在运行主机其`Start`方法：</span><span class="sxs-lookup"><span data-stu-id="bd770-316">You can run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="bd770-317">如果你的 Url 的列表传递`Start`方法，它侦听指定的 Url:</span><span class="sxs-lookup"><span data-stu-id="bd770-317">If you pass a list of URLs to the `Start` method, it listens on the URLs specified:</span></span>

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

<span data-ttu-id="bd770-318">你可以初始化并开始使用的预配置的默认值的新主机`CreateDefaultBuilder`使用静态便捷方法。</span><span class="sxs-lookup"><span data-stu-id="bd770-318">You can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="bd770-319">这些方法启动服务器控制台输出而无需与[WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown)等待中断 （Ctrl-C/SIGINT 或 SIGTERM）：</span><span class="sxs-lookup"><span data-stu-id="bd770-319">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="bd770-320">**开始 （RequestDelegate 应用程序）**</span><span class="sxs-lookup"><span data-stu-id="bd770-320">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="bd770-321">开头`RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="bd770-321">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="bd770-322">在浏览器向发出请求`http://localhost:5000`接收响应"Hello World ！"</span><span class="sxs-lookup"><span data-stu-id="bd770-322">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="bd770-323">`WaitForShutdown`受到阻止，直到颁发中断 （Ctrl-C/SIGINT 或 SIGTERM）。</span><span class="sxs-lookup"><span data-stu-id="bd770-323">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="bd770-324">应用程序并显示`Console.WriteLine`消息并等待 keypress 退出。</span><span class="sxs-lookup"><span data-stu-id="bd770-324">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="bd770-325">**启动 (字符串 url，RequestDelegate 应用)**</span><span class="sxs-lookup"><span data-stu-id="bd770-325">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="bd770-326">使用 URL 启动和`RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="bd770-326">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="bd770-327">生成与相同的结果**开始 （RequestDelegate 应用程序）**，除应用程序响应上`http://localhost:8080`。</span><span class="sxs-lookup"><span data-stu-id="bd770-327">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="bd770-328">**启动 (操作<IRouteBuilder>routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="bd770-328">**Start(Action<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="bd770-329">使用的实例`IRouteBuilder`([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) 用于路由的中间件：</span><span class="sxs-lookup"><span data-stu-id="bd770-329">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

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

<span data-ttu-id="bd770-330">该示例中使用以下浏览器请求：</span><span class="sxs-lookup"><span data-stu-id="bd770-330">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="bd770-331">请求</span><span class="sxs-lookup"><span data-stu-id="bd770-331">Request</span></span>                                    | <span data-ttu-id="bd770-332">响应</span><span class="sxs-lookup"><span data-stu-id="bd770-332">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="bd770-333">Hello，Martin ！</span><span class="sxs-lookup"><span data-stu-id="bd770-333">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="bd770-334">布宜诺斯艾利斯 dias，Catrina ！</span><span class="sxs-lookup"><span data-stu-id="bd770-334">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="bd770-335">引发"ooops ！"的字符串与异常</span><span class="sxs-lookup"><span data-stu-id="bd770-335">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="bd770-336">用字符串引发异常"不过这噢 ！"</span><span class="sxs-lookup"><span data-stu-id="bd770-336">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="bd770-337">Sante，Kevin ！</span><span class="sxs-lookup"><span data-stu-id="bd770-337">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="bd770-338">Hello World!</span><span class="sxs-lookup"><span data-stu-id="bd770-338">Hello World!</span></span>                             |

<span data-ttu-id="bd770-339">`WaitForShutdown`受到阻止，直到颁发中断 （Ctrl-C/SIGINT 或 SIGTERM）。</span><span class="sxs-lookup"><span data-stu-id="bd770-339">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="bd770-340">应用程序并显示`Console.WriteLine`消息并等待 keypress 退出。</span><span class="sxs-lookup"><span data-stu-id="bd770-340">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="bd770-341">**启动 (字符串 url，操作<IRouteBuilder>routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="bd770-341">**Start(string url, Action<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="bd770-342">使用 URL 和的实例`IRouteBuilder`:</span><span class="sxs-lookup"><span data-stu-id="bd770-342">Use a URL and an instance of `IRouteBuilder`:</span></span>

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

<span data-ttu-id="bd770-343">生成与相同的结果**启动 (操作<IRouteBuilder>routeBuilder)**，除应用程序响应在`http://localhost:8080`。</span><span class="sxs-lookup"><span data-stu-id="bd770-343">Produces the same result as **Start(Action<IRouteBuilder> routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="bd770-344">**StartWith (操作<IApplicationBuilder>应用)**</span><span class="sxs-lookup"><span data-stu-id="bd770-344">**StartWith(Action<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="bd770-345">提供一个委托，以配置`IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="bd770-345">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="bd770-346">在浏览器向发出请求`http://localhost:5000`接收响应"Hello World ！"</span><span class="sxs-lookup"><span data-stu-id="bd770-346">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="bd770-347">`WaitForShutdown`受到阻止，直到颁发中断 （Ctrl-C/SIGINT 或 SIGTERM）。</span><span class="sxs-lookup"><span data-stu-id="bd770-347">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="bd770-348">应用程序并显示`Console.WriteLine`消息并等待 keypress 退出。</span><span class="sxs-lookup"><span data-stu-id="bd770-348">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="bd770-349">**StartWith (字符串 url，操作<IApplicationBuilder>应用)**</span><span class="sxs-lookup"><span data-stu-id="bd770-349">**StartWith(string url, Action<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="bd770-350">提供一个 URL，另一个委托，以配置`IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="bd770-350">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="bd770-351">生成与相同的结果**StartWith (操作<IApplicationBuilder>应用)**，除应用程序响应上`http://localhost:8080`。</span><span class="sxs-lookup"><span data-stu-id="bd770-351">Produces the same result as **StartWith(Action<IApplicationBuilder> app)**, except the app responds on `http://localhost:8080`.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="bd770-352">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="bd770-352">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="bd770-353">**运行**</span><span class="sxs-lookup"><span data-stu-id="bd770-353">**Run**</span></span>

<span data-ttu-id="bd770-354">`Run`方法会启动 web 应用，并阻止调用线程，直到该主机是关闭：</span><span class="sxs-lookup"><span data-stu-id="bd770-354">The `Run` method starts the web app and blocks the calling thread until the host is shutdown:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="bd770-355">**Start**</span><span class="sxs-lookup"><span data-stu-id="bd770-355">**Start**</span></span>

<span data-ttu-id="bd770-356">你也可以通过调用非阻止方式在运行主机其`Start`方法：</span><span class="sxs-lookup"><span data-stu-id="bd770-356">You can run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="bd770-357">如果你的 Url 的列表传递`Start`方法，它侦听指定的 Url:</span><span class="sxs-lookup"><span data-stu-id="bd770-357">If you pass a list of URLs to the `Start` method, it listens on the URLs specified:</span></span>


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

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="bd770-358">IHostingEnvironment 接口</span><span class="sxs-lookup"><span data-stu-id="bd770-358">IHostingEnvironment interface</span></span>

<span data-ttu-id="bd770-359">[IHostingEnvironment 接口](/aspnet/core/api/microsoft.aspnetcore.hosting.ihostingenvironment)提供有关应用程序的 web 宿主环境的信息。</span><span class="sxs-lookup"><span data-stu-id="bd770-359">The [IHostingEnvironment interface](/aspnet/core/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="bd770-360">你可以使用[构造函数注入](xref:fundamentals/dependency-injection)获取`IHostingEnvironment`才能使用其属性和扩展方法：</span><span class="sxs-lookup"><span data-stu-id="bd770-360">You can use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="bd770-361">你可以使用[基于约定的方法](xref:fundamentals/environments#startup-conventions)在启动时基于环境配置你的应用程序。</span><span class="sxs-lookup"><span data-stu-id="bd770-361">You can use a [convention-based approach](xref:fundamentals/environments#startup-conventions) to configure your app at startup based on the environment.</span></span> <span data-ttu-id="bd770-362">或者，可以插入`IHostingEnvironment`到`Startup`构造函数用于`ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="bd770-362">Alternatively, you can inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="bd770-363">除了`IsDevelopment`扩展方法，`IHostingEnvironment`提供`IsStaging`， `IsProduction`，和`IsEnvironment(string environmentName)`方法。</span><span class="sxs-lookup"><span data-stu-id="bd770-363">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="bd770-364">请参阅[使用多个环境](xref:fundamentals/environments)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="bd770-364">See [Working with multiple environments](xref:fundamentals/environments) for details.</span></span>

<span data-ttu-id="bd770-365">`IHostingEnvironment`服务还可以被注入直接到`Configure`设置处理管道的方法：</span><span class="sxs-lookup"><span data-stu-id="bd770-365">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up your processing pipeline:</span></span>

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

<span data-ttu-id="bd770-366">可以插入`IHostingEnvironment`到`Invoke`方法时创建自定义[中间件](xref:fundamentals/middleware#writing-middleware):</span><span class="sxs-lookup"><span data-stu-id="bd770-366">You can inject `IHostingEnvironment` into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware#writing-middleware):</span></span>

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

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="bd770-367">IApplicationLifetime 接口</span><span class="sxs-lookup"><span data-stu-id="bd770-367">IApplicationLifetime interface</span></span>

<span data-ttu-id="bd770-368">[IApplicationLifetime 接口](/aspnet/core/api/microsoft.aspnetcore.hosting.iapplicationlifetime)允许你执行后启动和关闭活动。</span><span class="sxs-lookup"><span data-stu-id="bd770-368">The [IApplicationLifetime interface](/aspnet/core/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows you to perform post-startup and shutdown activities.</span></span> <span data-ttu-id="bd770-369">在接口上的三个属性是在可注册的取消标记`Action`方法来定义启动和关闭事件。</span><span class="sxs-lookup"><span data-stu-id="bd770-369">Three properties on the interface are cancellation tokens that you can register with `Action` methods to define startup and shutdown events.</span></span> <span data-ttu-id="bd770-370">此外，还有`StopApplication`方法。</span><span class="sxs-lookup"><span data-stu-id="bd770-370">There's also a `StopApplication` method.</span></span>

| <span data-ttu-id="bd770-371">取消标记</span><span class="sxs-lookup"><span data-stu-id="bd770-371">Cancellation Token</span></span>    | <span data-ttu-id="bd770-372">触发时 &#8230;</span><span class="sxs-lookup"><span data-stu-id="bd770-372">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="bd770-373">主机已完全启动。</span><span class="sxs-lookup"><span data-stu-id="bd770-373">The host has fully started.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="bd770-374">主机正在执行正常关闭。</span><span class="sxs-lookup"><span data-stu-id="bd770-374">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="bd770-375">仍在处理请求。</span><span class="sxs-lookup"><span data-stu-id="bd770-375">Requests may still be processing.</span></span> <span data-ttu-id="bd770-376">关闭受到阻止，直到完成此事件。</span><span class="sxs-lookup"><span data-stu-id="bd770-376">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="bd770-377">主机正在完成正常关闭。</span><span class="sxs-lookup"><span data-stu-id="bd770-377">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="bd770-378">应完全处理所有请求。</span><span class="sxs-lookup"><span data-stu-id="bd770-378">All requests should be completely processed.</span></span> <span data-ttu-id="bd770-379">关闭受到阻止，直到完成此事件。</span><span class="sxs-lookup"><span data-stu-id="bd770-379">Shutdown blocks until this event completes.</span></span> |

| <span data-ttu-id="bd770-380">方法</span><span class="sxs-lookup"><span data-stu-id="bd770-380">Method</span></span>            | <span data-ttu-id="bd770-381">操作</span><span class="sxs-lookup"><span data-stu-id="bd770-381">Action</span></span>                                           |
| ----------------- | ------------------------------------------------ |
| `StopApplication` | <span data-ttu-id="bd770-382">当前应用程序请求终止。</span><span class="sxs-lookup"><span data-stu-id="bd770-382">Requests termination of the current application.</span></span> |

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

## <a name="troubleshooting-systemargumentexception"></a><span data-ttu-id="bd770-383">故障排除 System.ArgumentException</span><span class="sxs-lookup"><span data-stu-id="bd770-383">Troubleshooting System.ArgumentException</span></span>

<span data-ttu-id="bd770-384">**适用于仅 ASP.NET Core 2.0**</span><span class="sxs-lookup"><span data-stu-id="bd770-384">**Applies to ASP.NET Core 2.0 Only**</span></span>

<span data-ttu-id="bd770-385">如果你通过将注入来生成主机`IStartup`直接插入依赖关系注入容器而不是调用`UseStartup`或`Configure`，你可能会遇到以下错误： `Unhandled Exception: System.ArgumentException: A valid non-empty application name must be provided`。</span><span class="sxs-lookup"><span data-stu-id="bd770-385">If you build the host by injecting `IStartup` directly into the dependency injection container rather than calling `UseStartup` or `Configure`, you may encounter the following error: `Unhandled Exception: System.ArgumentException: A valid non-empty application name must be provided`.</span></span>

<span data-ttu-id="bd770-386">这是因为[applicationName(ApplicationKey)](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostdefaults#Microsoft_AspNetCore_Hosting_WebHostDefaults_ApplicationKey) （当前程序集） 需扫描`HostingStartupAttributes`。</span><span class="sxs-lookup"><span data-stu-id="bd770-386">This occurs because the [applicationName(ApplicationKey)](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostdefaults#Microsoft_AspNetCore_Hosting_WebHostDefaults_ApplicationKey) (the current assembly) is required to scan for `HostingStartupAttributes`.</span></span> <span data-ttu-id="bd770-387">如果手动将注入`IStartup`到依赖关系注入容器中，添加对的以下调用你`WebHostBuilder`具有指定的程序集名称：</span><span class="sxs-lookup"><span data-stu-id="bd770-387">If you manually inject `IStartup` into the dependency injection container, add the following call to your `WebHostBuilder` with the assembly name specified:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("applicationName", "<Assembly Name>")
    ...
```

<span data-ttu-id="bd770-388">或者，将添加虚拟`Configure`到你`WebHostBuilder`，哪些集`applicationName`(`ApplicationKey`) 自动：</span><span class="sxs-lookup"><span data-stu-id="bd770-388">Alternatively, add a dummy `Configure` to your `WebHostBuilder`, which sets the `applicationName`(`ApplicationKey`) automatically:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .Configure(_ => { })
    ...
```

<span data-ttu-id="bd770-389">**请注意**： 这是仅需要 ASP.NET 核心 2.0 发行版，只有当你不调用`UseStartup`或`Configure`。</span><span class="sxs-lookup"><span data-stu-id="bd770-389">**NOTE**: This is only required with the ASP.NET Core 2.0 release and only when you don't call `UseStartup` or `Configure`.</span></span>

<span data-ttu-id="bd770-390">有关详细信息，请参阅[公告： Microsoft.Extensions.PlatformAbstractions 已被删除 （注释）](https://github.com/aspnet/Announcements/issues/237#issuecomment-323786938)和[StartupInjection 示例](https://github.com/aspnet/Hosting/blob/8377d226f1e6e1a97dabdb6769a845eeccc829ed/samples/SampleStartups/StartupInjection.cs)。</span><span class="sxs-lookup"><span data-stu-id="bd770-390">For more information, see [Announcements: Microsoft.Extensions.PlatformAbstractions has been removed (comment)](https://github.com/aspnet/Announcements/issues/237#issuecomment-323786938) and the [StartupInjection sample](https://github.com/aspnet/Hosting/blob/8377d226f1e6e1a97dabdb6769a845eeccc829ed/samples/SampleStartups/StartupInjection.cs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bd770-391">其他资源</span><span class="sxs-lookup"><span data-stu-id="bd770-391">Additional resources</span></span>

* [<span data-ttu-id="bd770-392">发布到使用 IIS 的 Windows</span><span class="sxs-lookup"><span data-stu-id="bd770-392">Publish to Windows using IIS</span></span>](../publishing/iis.md)
* [<span data-ttu-id="bd770-393">将发布到 Linux 使用 Nginx</span><span class="sxs-lookup"><span data-stu-id="bd770-393">Publish to Linux using Nginx</span></span>](../publishing/linuxproduction.md)
* [<span data-ttu-id="bd770-394">将发布到使用 Apache 的 Linux</span><span class="sxs-lookup"><span data-stu-id="bd770-394">Publish to Linux using Apache</span></span>](../publishing/apache-proxy.md)
* [<span data-ttu-id="bd770-395">在 Windows 服务中的主机</span><span class="sxs-lookup"><span data-stu-id="bd770-395">Host in a Windows Service</span></span>](xref:hosting/windows-service)
