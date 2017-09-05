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
# <a name="introduction-to-hosting-in-aspnet-core"></a><span data-ttu-id="d7d03-104">在 ASP.NET Core 中承载的简介</span><span class="sxs-lookup"><span data-stu-id="d7d03-104">Introduction to hosting in ASP.NET Core</span></span>

<span data-ttu-id="d7d03-105">通过[Steve Smith](http://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="d7d03-105">By [Steve Smith](http://ardalis.com)</span></span>

<span data-ttu-id="d7d03-106">若要运行 ASP.NET Core 应用，你需要配置和启动主机使用`WebHostBuilder`。</span><span class="sxs-lookup"><span data-stu-id="d7d03-106">To run an ASP.NET Core app, you need to configure and launch a host using `WebHostBuilder`.</span></span>

## <a name="what-is-a-host"></a><span data-ttu-id="d7d03-107">主机是什么？</span><span class="sxs-lookup"><span data-stu-id="d7d03-107">What is a Host?</span></span>

<span data-ttu-id="d7d03-108">ASP.NET Core 应用需要*主机*在其中执行。</span><span class="sxs-lookup"><span data-stu-id="d7d03-108">ASP.NET Core apps require a *host* in which to execute.</span></span> <span data-ttu-id="d7d03-109">宿主必须实现`IWebHost`接口，公开的功能和服务的集合，该接口和一个`Start`方法。</span><span class="sxs-lookup"><span data-stu-id="d7d03-109">A host must implement the `IWebHost` interface, which exposes collections of features and services, and a `Start` method.</span></span> <span data-ttu-id="d7d03-110">主机通常使用的实例创建`WebHostBuilder`，这将生成并返回`WebHost`实例。</span><span class="sxs-lookup"><span data-stu-id="d7d03-110">The host is typically created using an instance of a `WebHostBuilder`, which builds and returns a  `WebHost` instance.</span></span> <span data-ttu-id="d7d03-111">`WebHost`引用将处理的请求的服务器。</span><span class="sxs-lookup"><span data-stu-id="d7d03-111">The `WebHost` references the server that will handle requests.</span></span> <span data-ttu-id="d7d03-112">详细了解[服务器](servers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="d7d03-112">Learn more about [servers](servers/index.md).</span></span>

### <a name="what-is-the-difference-between-a-host-and-a-server"></a><span data-ttu-id="d7d03-113">主机和服务器之间的区别是什么？</span><span class="sxs-lookup"><span data-stu-id="d7d03-113">What is the difference between a host and a server?</span></span>

<span data-ttu-id="d7d03-114">主机负责应用程序启动和生存期管理。</span><span class="sxs-lookup"><span data-stu-id="d7d03-114">The host is responsible for application startup and lifetime management.</span></span> <span data-ttu-id="d7d03-115">服务器负责接收 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="d7d03-115">The server is responsible for accepting HTTP requests.</span></span> <span data-ttu-id="d7d03-116">主机的责任的一部分包括确保应用程序的服务和服务器都可用并且配置正确。</span><span class="sxs-lookup"><span data-stu-id="d7d03-116">Part of the host's responsibility includes ensuring the application's services and the server are available and properly configured.</span></span> <span data-ttu-id="d7d03-117">你可以将正在服务器周围的包装器的主机。</span><span class="sxs-lookup"><span data-stu-id="d7d03-117">You can think of the host as being a wrapper around the server.</span></span> <span data-ttu-id="d7d03-118">主机配置为使用一个特定的服务器。服务器不知道其主机。</span><span class="sxs-lookup"><span data-stu-id="d7d03-118">The host is configured to use a particular server; the server is unaware of its host.</span></span>

## <a name="setting-up-a-host"></a><span data-ttu-id="d7d03-119">设置主机</span><span class="sxs-lookup"><span data-stu-id="d7d03-119">Setting up a Host</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="d7d03-120">ASP.NET 核心 1.x</span><span class="sxs-lookup"><span data-stu-id="d7d03-120">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="d7d03-121">创建使用的实例的主机`WebHostBuilder`。</span><span class="sxs-lookup"><span data-stu-id="d7d03-121">You create a host using an instance of `WebHostBuilder`.</span></span> <span data-ttu-id="d7d03-122">这通常是在您的应用程序的入口点： `public static void Main` (项目模板中位于*Program.cs*文件)。</span><span class="sxs-lookup"><span data-stu-id="d7d03-122">This is typically done in your app's entry point: `public static void Main` (which in the project templates is located in a *Program.cs* file).</span></span> <span data-ttu-id="d7d03-123">典型*Program.cs*所示下面，演示如何使用`WebHostBuilder`来构建的主机。</span><span class="sxs-lookup"><span data-stu-id="d7d03-123">A typical *Program.cs*, shown below, demonstrates how to use a `WebHostBuilder` to build a host.</span></span>

<span data-ttu-id="d7d03-124">[!code-csharp[Main](../common/samples/WebApplication1/Program.cs?highlight=14,15,16,17,18,19,20,21)]</span><span class="sxs-lookup"><span data-stu-id="d7d03-124">[!code-csharp[Main](../common/samples/WebApplication1/Program.cs?highlight=14,15,16,17,18,19,20,21)]</span></span>

<span data-ttu-id="d7d03-125">`WebHostBuilder`负责创建主机，将启动应用程序的服务器。</span><span class="sxs-lookup"><span data-stu-id="d7d03-125">The `WebHostBuilder` is responsible for creating the host that will bootstrap the server for the app.</span></span> <span data-ttu-id="d7d03-126">`WebHostBuilder`要求提供实现的服务器`IServer`(`UseKestrel`在上面的代码)。</span><span class="sxs-lookup"><span data-stu-id="d7d03-126">`WebHostBuilder` requires you provide a server that implements `IServer` (`UseKestrel` in the code above).</span></span> <span data-ttu-id="d7d03-127">`UseKestrel`指定的 Kestrel 服务器将由应用程序。</span><span class="sxs-lookup"><span data-stu-id="d7d03-127">`UseKestrel` specifies the Kestrel server will be used by the app.</span></span>

<span data-ttu-id="d7d03-128">服务器的*内容根*确定为内容文件，如 MVC 视图文件搜索。</span><span class="sxs-lookup"><span data-stu-id="d7d03-128">The server's *content root* determines where it searches for content files, like MVC View files.</span></span> <span data-ttu-id="d7d03-129">默认内容根是从中运行应用程序的文件夹。</span><span class="sxs-lookup"><span data-stu-id="d7d03-129">The default content root is the folder from which the application is run.</span></span>

> [!NOTE]
> <span data-ttu-id="d7d03-130">指定`Directory.GetCurrentDirectory`由于内容根将使用 web 项目的根文件夹作为应用程序的内容根从此文件夹中启动应用时 (例如，调用`dotnet run`web 项目文件夹中)。</span><span class="sxs-lookup"><span data-stu-id="d7d03-130">Specifying `Directory.GetCurrentDirectory` as the content root will use the web project's root folder as the app's content root when the app is started from this folder (for example, calling `dotnet run` from the web project folder).</span></span> <span data-ttu-id="d7d03-131">这是在 Visual Studio 中使用的默认值和`dotnet new`模板。</span><span class="sxs-lookup"><span data-stu-id="d7d03-131">This is the default used in Visual Studio and `dotnet new` templates.</span></span>

<span data-ttu-id="d7d03-132">若要使用 IIS 的反向代理，调用`UseIISIntegration`生成主机的一部分。</span><span class="sxs-lookup"><span data-stu-id="d7d03-132">To use IIS as a reverse proxy, call `UseIISIntegration` as part of building the host.</span></span> 

<span data-ttu-id="d7d03-133">请注意，`UseIISIntegration`不配置*服务器*、 like`UseKestrel`未。</span><span class="sxs-lookup"><span data-stu-id="d7d03-133">Note that `UseIISIntegration` doesn't configure a *server*, like `UseKestrel` does.</span></span> <span data-ttu-id="d7d03-134">若要使用 ASP.NET Core IIS，则必须同时指定`UseKestrel`和`UseIISIntegration`。</span><span class="sxs-lookup"><span data-stu-id="d7d03-134">To use IIS with ASP.NET Core, you must specify both `UseKestrel` and `UseIISIntegration`.</span></span> <span data-ttu-id="d7d03-135">`UseKestrel`创建 web 服务器和承载应用程序。</span><span class="sxs-lookup"><span data-stu-id="d7d03-135">`UseKestrel` creates the web server and hosts the app.</span></span> <span data-ttu-id="d7d03-136">`UseIISIntegration`检查 IIS/IISExpress 所使用的环境变量，并配置设置，例如要侦听的端口和要使用的标头。</span><span class="sxs-lookup"><span data-stu-id="d7d03-136">`UseIISIntegration` examines environment variables used by IIS/IISExpress and configures settings such as the port to listen on and the headers to use.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="d7d03-137">ASP.NET 核心 2.x</span><span class="sxs-lookup"><span data-stu-id="d7d03-137">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="d7d03-138">创建使用的实例的主机`WebHostBuilder`。</span><span class="sxs-lookup"><span data-stu-id="d7d03-138">You create a host using an instance of `WebHostBuilder`.</span></span> <span data-ttu-id="d7d03-139">这通常是在您的应用程序的入口点： `public static void Main` (项目模板中位于*Program.cs*文件)。</span><span class="sxs-lookup"><span data-stu-id="d7d03-139">This is typically done in your app's entry point: `public static void Main` (which in the project templates is located in a *Program.cs* file).</span></span> <span data-ttu-id="d7d03-140">典型*Program.cs*、 示调用`CreateDefaultbuilder`来构建的主机：</span><span class="sxs-lookup"><span data-stu-id="d7d03-140">A typical *Program.cs*, shown below, calls `CreateDefaultbuilder` to build a host:</span></span>

<span data-ttu-id="d7d03-141">[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]</span><span class="sxs-lookup"><span data-stu-id="d7d03-141">[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]</span></span>

<span data-ttu-id="d7d03-142">`CreateDefaultbuilder`创建的实例`WebHostBuilder`生成启动应用程序的服务器的主机。</span><span class="sxs-lookup"><span data-stu-id="d7d03-142">`CreateDefaultbuilder` creates an instance of `WebHostBuilder` to build the host that bootstraps the server for the app.</span></span> <span data-ttu-id="d7d03-143">主机需要[服务器实现 IServer](servers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="d7d03-143">The host requires a [server that implements IServer](servers/index.md).</span></span> <span data-ttu-id="d7d03-144">内置服务器[Kestrel](servers/kestrel.md)和[HTTP.sys](servers/httpsys.md);`CreateDefaultbuilder`默认情况下使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="d7d03-144">The built-in servers are [Kestrel](servers/kestrel.md) and [HTTP.sys](servers/httpsys.md); `CreateDefaultbuilder` use Kestrel by default.</span></span>

<span data-ttu-id="d7d03-145">`CreateDefaultbuilder`执行将 Kestrel 配置为 web 服务器除了设置任务：</span><span class="sxs-lookup"><span data-stu-id="d7d03-145">`CreateDefaultbuilder` performs set-up tasks in addition to configuring Kestrel as the web server:</span></span>

* <span data-ttu-id="d7d03-146">将内容的根设置为`Directory.GetCurrentDirectory`。</span><span class="sxs-lookup"><span data-stu-id="d7d03-146">Sets the content root to `Directory.GetCurrentDirectory`.</span></span>
* <span data-ttu-id="d7d03-147">从加载配置：</span><span class="sxs-lookup"><span data-stu-id="d7d03-147">Loads configuration from:</span></span>
  * <span data-ttu-id="d7d03-148">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="d7d03-148">*appsettings.json*</span></span>
  * <span data-ttu-id="d7d03-149">*appsettings。\<EnvironmentName >.json*。</span><span class="sxs-lookup"><span data-stu-id="d7d03-149">*appsettings.\<EnvironmentName>.json*.</span></span>
  * <span data-ttu-id="d7d03-150">用户的机密信息的应用程序在开发环境中运行时</span><span class="sxs-lookup"><span data-stu-id="d7d03-150">user secrets when the app runs in the Development environment</span></span>
  * <span data-ttu-id="d7d03-151">环境变量</span><span class="sxs-lookup"><span data-stu-id="d7d03-151">environment variables</span></span>
  * <span data-ttu-id="d7d03-152">提供的命令行参数</span><span class="sxs-lookup"><span data-stu-id="d7d03-152">supplied command line args</span></span>
* <span data-ttu-id="d7d03-153">使用筛选日志记录配置部分中指定的规则来配置控制台和调试输出的日志记录。</span><span class="sxs-lookup"><span data-stu-id="d7d03-153">Configures logging for console and debug output, with filtering rules specified in a Logging configuration section.</span></span>
* <span data-ttu-id="d7d03-154">启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="d7d03-154">Enables IIS integration.</span></span>
* <span data-ttu-id="d7d03-155">在开发环境中运行应用程序时，将添加开发人员异常页。</span><span class="sxs-lookup"><span data-stu-id="d7d03-155">Adds the developer exception page when the app runs in the Development environment.</span></span>

<span data-ttu-id="d7d03-156">服务器的*内容根*确定为内容文件，如 MVC 视图文件搜索。</span><span class="sxs-lookup"><span data-stu-id="d7d03-156">The server's *content root* determines where it searches for content files, like MVC View files.</span></span> <span data-ttu-id="d7d03-157">默认内容根是从中运行应用程序的文件夹。</span><span class="sxs-lookup"><span data-stu-id="d7d03-157">The default content root is the folder from which the application is run.</span></span>

> [!NOTE]
> <span data-ttu-id="d7d03-158">指定`Directory.GetCurrentDirectory`由于内容根将使用 web 项目的根文件夹作为应用程序的内容根从此文件夹中启动应用时 (例如，调用`dotnet run`web 项目文件夹中)。</span><span class="sxs-lookup"><span data-stu-id="d7d03-158">Specifying `Directory.GetCurrentDirectory` as the content root will use the web project's root folder as the app's content root when the app is started from this folder (for example, calling `dotnet run` from the web project folder).</span></span> <span data-ttu-id="d7d03-159">这是在 Visual Studio 中使用的默认值和`dotnet new`模板。</span><span class="sxs-lookup"><span data-stu-id="d7d03-159">This is the default used in Visual Studio and `dotnet new` templates.</span></span>

<span data-ttu-id="d7d03-160">当你使用 IIS 的反向代理时，自动调用 ASP.NET Core`UseIISIntegration`生成主机的一部分。</span><span class="sxs-lookup"><span data-stu-id="d7d03-160">When you use IIS as a reverse proxy, ASP.NET Core automatically calls `UseIISIntegration` as part of building the host.</span></span> <span data-ttu-id="d7d03-161">有关详细信息，请参阅[ASP.NET 核心模块](xref:fundamentals/servers/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="d7d03-161">For more information, see [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span></span>

<span data-ttu-id="d7d03-162">请注意，`UseIISIntegration`不配置*服务器*、 like`UseKestrel`未。</span><span class="sxs-lookup"><span data-stu-id="d7d03-162">Note that `UseIISIntegration` doesn't configure a *server*, like `UseKestrel` does.</span></span> <span data-ttu-id="d7d03-163">`UseKestrel`创建 web 服务器和承载应用程序。</span><span class="sxs-lookup"><span data-stu-id="d7d03-163">`UseKestrel` creates the web server and hosts the app.</span></span> <span data-ttu-id="d7d03-164">`UseIISIntegration`检查 IIS/IISExpress 所使用的环境变量，并配置设置，例如要侦听的端口和要使用的标头。</span><span class="sxs-lookup"><span data-stu-id="d7d03-164">`UseIISIntegration` examines environment variables used by IIS/IISExpress and configures settings such as the port to listen on and the headers to use.</span></span>

---

<span data-ttu-id="d7d03-165">只需服务器并配置应用程序的请求管道将包含配置一台主机 （和 ASP.NET Core 应用） 的最小实现：</span><span class="sxs-lookup"><span data-stu-id="d7d03-165">A minimal implementation of configuring a host (and an ASP.NET Core app) would include just a server and configuration of the app's request pipeline:</span></span>

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
> <span data-ttu-id="d7d03-166">设置时主机，你可以提供`Configure`和`ConfigureServices`方法，而非或除了指定`Startup`类 (这也必须定义这些方法-请参阅[应用程序启动](startup.md))。</span><span class="sxs-lookup"><span data-stu-id="d7d03-166">When setting up a host, you can provide `Configure` and `ConfigureServices` methods, instead of or in addition to specifying a `Startup` class (which must also define these methods - see [Application Startup](startup.md)).</span></span> <span data-ttu-id="d7d03-167">多次调用`ConfigureServices`都将追加到另一个; 调用`Configure`或`UseStartup`将替换以前的设置。</span><span class="sxs-lookup"><span data-stu-id="d7d03-167">Multiple calls to `ConfigureServices` will append to one another; calls to `Configure` or `UseStartup` will replace previous settings.</span></span>

## <a name="configuring-a-host"></a><span data-ttu-id="d7d03-168">配置主机</span><span class="sxs-lookup"><span data-stu-id="d7d03-168">Configuring a Host</span></span>

<span data-ttu-id="d7d03-169">`WebHostBuilder`提供用于设置对于主机，也可通过直接使用设置的大部分的可用的配置值的方法`UseSetting`和关联的密钥。</span><span class="sxs-lookup"><span data-stu-id="d7d03-169">The `WebHostBuilder` provides methods for setting most of the available configuration values for the host, which can also be set directly using `UseSetting` and associated key.</span></span>

### <a name="host-configuration-values"></a><span data-ttu-id="d7d03-170">主机配置值</span><span class="sxs-lookup"><span data-stu-id="d7d03-170">Host Configuration Values</span></span>

<span data-ttu-id="d7d03-171">**捕获启动错误**`bool`</span><span class="sxs-lookup"><span data-stu-id="d7d03-171">**Capture Startup Errors** `bool`</span></span>

<span data-ttu-id="d7d03-172">键： `captureStartupErrors`。</span><span class="sxs-lookup"><span data-stu-id="d7d03-172">Key: `captureStartupErrors`.</span></span> <span data-ttu-id="d7d03-173">默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="d7d03-173">Defaults to `false`.</span></span> <span data-ttu-id="d7d03-174">当`false`，退出主机中启动结果时出错。</span><span class="sxs-lookup"><span data-stu-id="d7d03-174">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="d7d03-175">当`true`，主机将捕获的任何异常`Startup`类，并尝试启动服务器。</span><span class="sxs-lookup"><span data-stu-id="d7d03-175">When `true`, the host will capture any exceptions from the `Startup` class and attempt to start the server.</span></span> <span data-ttu-id="d7d03-176">它将显示一个错误页面 （泛型类型，或详细，以详细错误设置，下面） 为每个请求。</span><span class="sxs-lookup"><span data-stu-id="d7d03-176">It will display an error page (generic, or detailed, based on the Detailed Errors setting, below) for every request.</span></span> <span data-ttu-id="d7d03-177">使用设置`CaptureStartupErrors`方法。</span><span class="sxs-lookup"><span data-stu-id="d7d03-177">Set using the `CaptureStartupErrors` method.</span></span>

<span data-ttu-id="d7d03-178">注意： 你的应用程序运行时使用 Kestrel 和 IIS，默认行为是以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="d7d03-178">Note: When your app runs with Kestrel and IIS, the default behavior is to capture startup errors.</span></span> 

```csharp
new WebHostBuilder()
    .CaptureStartupErrors(true)
   ```

<span data-ttu-id="d7d03-179">**内容根**`string`</span><span class="sxs-lookup"><span data-stu-id="d7d03-179">**Content Root** `string`</span></span>

<span data-ttu-id="d7d03-180">键： `contentRoot`。</span><span class="sxs-lookup"><span data-stu-id="d7d03-180">Key: `contentRoot`.</span></span> <span data-ttu-id="d7d03-181">默认值为 （对于 Kestrel;，为应用程序集所在的文件夹IIS 将使用 web 项目根默认情况下）。</span><span class="sxs-lookup"><span data-stu-id="d7d03-181">Defaults to the folder where the application assembly resides (for Kestrel; IIS will use the web project root by default).</span></span> <span data-ttu-id="d7d03-182">此设置确定其中 ASP.NET Core 将开始搜索内容的文件，MVC 视图等。</span><span class="sxs-lookup"><span data-stu-id="d7d03-182">This setting determines where ASP.NET Core will begin searching for content files, such as MVC Views.</span></span> <span data-ttu-id="d7d03-183">此外用作的基路径[Web 根设置](#web-root-setting)。</span><span class="sxs-lookup"><span data-stu-id="d7d03-183">Also used as the base path for the [Web Root Setting](#web-root-setting).</span></span> <span data-ttu-id="d7d03-184">使用设置`UseContentRoot`方法。</span><span class="sxs-lookup"><span data-stu-id="d7d03-184">Set using the `UseContentRoot` method.</span></span> <span data-ttu-id="d7d03-185">路径必须存在，或者主机将无法启动。</span><span class="sxs-lookup"><span data-stu-id="d7d03-185">Path must exist, or host will fail to start.</span></span>

```csharp
new WebHostBuilder()
    .UseContentRoot("c:\\mywebsite")
   ```

<span data-ttu-id="d7d03-186">**详细错误**`bool`</span><span class="sxs-lookup"><span data-stu-id="d7d03-186">**Detailed Errors** `bool`</span></span>

<span data-ttu-id="d7d03-187">键： `detailedErrors`。</span><span class="sxs-lookup"><span data-stu-id="d7d03-187">Key: `detailedErrors`.</span></span> <span data-ttu-id="d7d03-188">默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="d7d03-188">Defaults to `false`.</span></span> <span data-ttu-id="d7d03-189">当`true`（或当环境已设置为"开发"），该应用将显示的启动异常，而不是只是一般性错误页的详细信息。</span><span class="sxs-lookup"><span data-stu-id="d7d03-189">When `true` (or when Environment is set to "Development"), the app will display details of startup exceptions, instead of just a generic error page.</span></span> <span data-ttu-id="d7d03-190">使用设置`UseSetting`。</span><span class="sxs-lookup"><span data-stu-id="d7d03-190">Set using `UseSetting`.</span></span>

```csharp
new WebHostBuilder()
    .UseSetting("detailedErrors", "true")
```

<span data-ttu-id="d7d03-191">当详细错误设置为`false`和捕获启动 Errors 是`true`，到服务器的每个请求的响应中会显示一个一般性错误页面。</span><span class="sxs-lookup"><span data-stu-id="d7d03-191">When Detailed Errors is set to `false` and Capture Startup Errors is `true`, a generic error page is displayed in response to every request to the server.</span></span>

![常规错误页面](hosting/_static/generic-error-page.png)

<span data-ttu-id="d7d03-193">当详细错误设置为`true`和捕获启动 Errors 是`true`，详细的错误页显示在每个服务器请求的响应。</span><span class="sxs-lookup"><span data-stu-id="d7d03-193">When Detailed Errors is set to `true` and Capture Startup Errors is `true`, a detailed error page is displayed in response to every request to the server.</span></span>

![详细的错误页](hosting/_static/detailed-error-page.png)

<span data-ttu-id="d7d03-195">**环境**`string`</span><span class="sxs-lookup"><span data-stu-id="d7d03-195">**Environment** `string`</span></span>

<span data-ttu-id="d7d03-196">键： `environment`。</span><span class="sxs-lookup"><span data-stu-id="d7d03-196">Key: `environment`.</span></span> <span data-ttu-id="d7d03-197">默认值为"生产"。</span><span class="sxs-lookup"><span data-stu-id="d7d03-197">Defaults to "Production".</span></span> <span data-ttu-id="d7d03-198">可能设置为任何值。</span><span class="sxs-lookup"><span data-stu-id="d7d03-198">May be set to any value.</span></span> <span data-ttu-id="d7d03-199">框架定义的值包括"开发"、"过渡"和"生产"。</span><span class="sxs-lookup"><span data-stu-id="d7d03-199">Framework-defined values include "Development", "Staging", and "Production".</span></span> <span data-ttu-id="d7d03-200">值不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="d7d03-200">Values are not case sensitive.</span></span> <span data-ttu-id="d7d03-201">请参阅[使用多个环境](environments.md)。</span><span class="sxs-lookup"><span data-stu-id="d7d03-201">See [Working with Multiple Environments](environments.md).</span></span> <span data-ttu-id="d7d03-202">使用设置`UseEnvironment`方法。</span><span class="sxs-lookup"><span data-stu-id="d7d03-202">Set using the `UseEnvironment` method.</span></span>

```csharp
new WebHostBuilder()
    .UseEnvironment("Development")
```

> [!NOTE]
> <span data-ttu-id="d7d03-203">默认情况下，环境读取从`ASPNETCORE_ENVIRONMENT`环境变量。</span><span class="sxs-lookup"><span data-stu-id="d7d03-203">By default, the environment is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="d7d03-204">在使用 Visual Studio 时，可能会设置环境变量*launchSettings.json*文件。</span><span class="sxs-lookup"><span data-stu-id="d7d03-204">When using Visual Studio, environment variables may be set in the *launchSettings.json* file.</span></span>

<a id="server-urls"></a>

<span data-ttu-id="d7d03-205">**服务器 Url**`string`</span><span class="sxs-lookup"><span data-stu-id="d7d03-205">**Server URLs** `string`</span></span>

<span data-ttu-id="d7d03-206">键： `urls`。</span><span class="sxs-lookup"><span data-stu-id="d7d03-206">Key: `urls`.</span></span> <span data-ttu-id="d7d03-207">设置为分号 （;） 分隔的 URL 的列表添加到服务器应响应以下前缀。</span><span class="sxs-lookup"><span data-stu-id="d7d03-207">Set to a semicolon (;) separated list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="d7d03-208">例如 `http://localhost:123`。</span><span class="sxs-lookup"><span data-stu-id="d7d03-208">For example, `http://localhost:123`.</span></span> <span data-ttu-id="d7d03-209">可以使用替换域/主机名"\*"以指示服务器应请求任何 IP 地址上侦听或承载使用指定的端口和协议 (例如，`http://*:5000`或`https://*:5001`)。</span><span class="sxs-lookup"><span data-stu-id="d7d03-209">The domain/host name can be replaced with "\*" to indicate the server should listen to requests on any IP address or host using the specified port and protocol (for example, `http://*:5000` or `https://*:5001`).</span></span> <span data-ttu-id="d7d03-210">协议 (`http://`或`https://`) 必须包含每个 URL。</span><span class="sxs-lookup"><span data-stu-id="d7d03-210">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="d7d03-211">前缀解释配置服务器;支持的格式将有所不同服务器。</span><span class="sxs-lookup"><span data-stu-id="d7d03-211">The prefixes are interpreted by the configured server; supported formats will vary between servers.</span></span>

```csharp
new WebHostBuilder()
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="d7d03-212">在 ASP.NET 核心 2.0 中，Kestrel 具有其自己的终结点配置 API，并且不支持`https://`中`urls`字符串。</span><span class="sxs-lookup"><span data-stu-id="d7d03-212">In ASP.NET Core 2.0, Kestrel has its own endpoint configuration API and does not support `https://` in the `urls` string.</span></span> <span data-ttu-id="d7d03-213">有关详细信息，请参阅[简介 Kestrel](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration)。</span><span class="sxs-lookup"><span data-stu-id="d7d03-213">For more information, see [Introduction to Kestrel](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration).</span></span>

<span data-ttu-id="d7d03-214">**程序集启动**`string`</span><span class="sxs-lookup"><span data-stu-id="d7d03-214">**Startup Assembly** `string`</span></span>

<span data-ttu-id="d7d03-215">键： `startupAssembly`。</span><span class="sxs-lookup"><span data-stu-id="d7d03-215">Key: `startupAssembly`.</span></span> <span data-ttu-id="d7d03-216">确定要搜索的程序集`Startup`类。</span><span class="sxs-lookup"><span data-stu-id="d7d03-216">Determines the assembly to search for the `Startup` class.</span></span> <span data-ttu-id="d7d03-217">使用设置`UseStartup`方法。</span><span class="sxs-lookup"><span data-stu-id="d7d03-217">Set using the `UseStartup` method.</span></span> <span data-ttu-id="d7d03-218">可能会改为引用特定类型使用`WebHostBuilder.UseStartup<StartupType>`。</span><span class="sxs-lookup"><span data-stu-id="d7d03-218">May instead reference specific type using `WebHostBuilder.UseStartup<StartupType>`.</span></span> <span data-ttu-id="d7d03-219">如果选择多个`UseStartup`调用方法，最后一个将优先。</span><span class="sxs-lookup"><span data-stu-id="d7d03-219">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
new WebHostBuilder()
    .UseStartup("StartupAssemblyName")
```

<a name=web-root-setting></a>

<span data-ttu-id="d7d03-220">**Web 根**`string`</span><span class="sxs-lookup"><span data-stu-id="d7d03-220">**Web Root** `string`</span></span>

<span data-ttu-id="d7d03-221">键： `webroot`。</span><span class="sxs-lookup"><span data-stu-id="d7d03-221">Key: `webroot`.</span></span> <span data-ttu-id="d7d03-222">如果未指定默认值为`(Content Root Path)\wwwroot`，如果它存在。</span><span class="sxs-lookup"><span data-stu-id="d7d03-222">If not specified the default is `(Content Root Path)\wwwroot`, if it exists.</span></span> <span data-ttu-id="d7d03-223">如果此路径不存在，则使用无操作文件提供程序。</span><span class="sxs-lookup"><span data-stu-id="d7d03-223">If this path doesn't exist, then a no-op file provider is used.</span></span> <span data-ttu-id="d7d03-224">使用设置`UseWebRoot`。</span><span class="sxs-lookup"><span data-stu-id="d7d03-224">Set using `UseWebRoot`.</span></span>

```csharp
new WebHostBuilder()
    .UseWebRoot("public")
```

### <a name="overriding-configuration"></a><span data-ttu-id="d7d03-225">重写配置</span><span class="sxs-lookup"><span data-stu-id="d7d03-225">Overriding Configuration</span></span>

<span data-ttu-id="d7d03-226">使用[配置](configuration.md)设置要由主机使用的配置值。</span><span class="sxs-lookup"><span data-stu-id="d7d03-226">Use [Configuration](configuration.md) to set configuration values to be used by the host.</span></span> <span data-ttu-id="d7d03-227">可能会随后重写这些值。</span><span class="sxs-lookup"><span data-stu-id="d7d03-227">These values may be subsequently overridden.</span></span> <span data-ttu-id="d7d03-228">这使用指定`UseConfiguration`。</span><span class="sxs-lookup"><span data-stu-id="d7d03-228">This is specified using `UseConfiguration`.</span></span>

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

<span data-ttu-id="d7d03-229">在上面的示例中，命令行自变量可能会将传入的要配置主机，或配置设置 （可选） 可指定在*hosting.json*文件。</span><span class="sxs-lookup"><span data-stu-id="d7d03-229">In the example above, command-line arguments may be passed in to configure the host, or configuration settings may optionally be specified in a *hosting.json* file.</span></span> <span data-ttu-id="d7d03-230">若要指定特定的 URL 上运行的主机，无法在命令提示符下传入所需的值：</span><span class="sxs-lookup"><span data-stu-id="d7d03-230">To specify the host run on a particular URL, you could pass in the desired value from a command prompt:</span></span>

```console
dotnet run --urls "http://*:5000"
```

<span data-ttu-id="d7d03-231">`Run`方法会启动 web 应用，并阻止调用线程，直到该主机是关闭。</span><span class="sxs-lookup"><span data-stu-id="d7d03-231">The `Run` method starts the web app and blocks the calling thread until the host is shutdown.</span></span>

```csharp
host.Run();
```

<span data-ttu-id="d7d03-232">你也可以通过调用非阻止方式在运行主机其`Start`方法：</span><span class="sxs-lookup"><span data-stu-id="d7d03-232">You can run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="d7d03-233">传递到的 Url 的列表`Start`方法和它将侦听指定的 Url:</span><span class="sxs-lookup"><span data-stu-id="d7d03-233">Pass a list of URLs to the `Start` method and it will listen on the URLs specified:</span></span>

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

<span data-ttu-id="d7d03-234">此处是有效的 URL 格式取决于你正在使用的服务器。</span><span class="sxs-lookup"><span data-stu-id="d7d03-234">The URL formats that are valid here depend on the server you're using.</span></span> <span data-ttu-id="d7d03-235">有关详细信息，请参阅[服务器 Url](#server-urls)本文前面。</span><span class="sxs-lookup"><span data-stu-id="d7d03-235">For more information, see [Server URLs](#server-urls) earlier in this article.</span></span>

> [!NOTE]
> <span data-ttu-id="d7d03-236">`UseConfiguration`扩展方法不是当前能够分析返回的一个配置节`GetSection`(例如， `.UseConfiguration(Configuration.GetSection("section"))`。</span><span class="sxs-lookup"><span data-stu-id="d7d03-236">The `UseConfiguration` extension method isn't currently capable of parsing a configuration section returned by `GetSection` (for example, `.UseConfiguration(Configuration.GetSection("section"))`.</span></span> <span data-ttu-id="d7d03-237">`GetSection`方法筛选到请求的节的配置密钥，但会键上的节名称 (例如， `section:urls`， `section:environment`)。</span><span class="sxs-lookup"><span data-stu-id="d7d03-237">The `GetSection` method filters the configuration keys to the section requested but leaves the section name on the keys (for example, `section:urls`, `section:environment`).</span></span> <span data-ttu-id="d7d03-238">`UseConfiguration`方法需要要匹配的键`WebHostBuilder`密钥 (例如， `urls`， `environment`)。</span><span class="sxs-lookup"><span data-stu-id="d7d03-238">The `UseConfiguration` method expects the keys to match the `WebHostBuilder` keys (for example, `urls`, `environment`).</span></span> <span data-ttu-id="d7d03-239">键的节名称存在阻止从配置主机部分的值。</span><span class="sxs-lookup"><span data-stu-id="d7d03-239">The presence of the section name on the keys prevents the section's values from configuring the host.</span></span> <span data-ttu-id="d7d03-240">将在即将发布的版本中解决此问题。</span><span class="sxs-lookup"><span data-stu-id="d7d03-240">This issue will be addressed in an upcoming release.</span></span> <span data-ttu-id="d7d03-241">有关详细信息和解决方法，请参阅[将配置节传入到 WebHostBuilder.UseConfiguration 使用完整的密钥](https://github.com/aspnet/Hosting/issues/839)。</span><span class="sxs-lookup"><span data-stu-id="d7d03-241">For more information and workarounds, see [Passing configuration section into WebHostBuilder.UseConfiguration uses full keys](https://github.com/aspnet/Hosting/issues/839).</span></span>

### <a name="ordering-importance"></a><span data-ttu-id="d7d03-242">顺序重要性</span><span class="sxs-lookup"><span data-stu-id="d7d03-242">Ordering Importance</span></span>

<span data-ttu-id="d7d03-243">`WebHostBuilder`如果，某些环境变量，从第一次读取设置设置。</span><span class="sxs-lookup"><span data-stu-id="d7d03-243">`WebHostBuilder` settings are first read from certain environment variables, if set.</span></span> <span data-ttu-id="d7d03-244">这些环境变量必须使用格式`ASPNETCORE_{configurationKey}`，因此对于示例设置 Url 服务器将侦听默认情况下，可以将设置`ASPNETCORE_URLS`。</span><span class="sxs-lookup"><span data-stu-id="d7d03-244">These environment variables must use the format `ASPNETCORE_{configurationKey}`, so for example to set the URLs the server will listen on by default, you would set `ASPNETCORE_URLS`.</span></span>

<span data-ttu-id="d7d03-245">你可以通过指定配置重写任何这些环境变量值 (使用`UseConfiguration`) 或通过显式设置的值 (使用`UseUrls`实例)。</span><span class="sxs-lookup"><span data-stu-id="d7d03-245">You can override any of these environment variable values by specifying configuration (using `UseConfiguration`) or by setting the value explicitly (using `UseUrls` for instance).</span></span> <span data-ttu-id="d7d03-246">主机将使用无论选项上次设置的值。</span><span class="sxs-lookup"><span data-stu-id="d7d03-246">The host will use whichever option sets the value last.</span></span> <span data-ttu-id="d7d03-247">为此，`UseIISIntegration`必须显示之后`UseUrls`，因为它将使用一个动态由 IIS 替换 URL。</span><span class="sxs-lookup"><span data-stu-id="d7d03-247">For this reason, `UseIISIntegration` must appear after `UseUrls`, because it replaces the URL with one dynamically provided by IIS.</span></span> <span data-ttu-id="d7d03-248">如果你想要以编程方式设置的默认 URL 为一个值，但允许其与配置中重写，则可按如下所述配置主机：</span><span class="sxs-lookup"><span data-stu-id="d7d03-248">If you want to programmatically set the default URL to one value, but allow it to be overridden with configuration, you could configure the host as follows:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="d7d03-249">其他资源</span><span class="sxs-lookup"><span data-stu-id="d7d03-249">Additional resources</span></span>

* [<span data-ttu-id="d7d03-250">发布到使用 IIS 的 Windows</span><span class="sxs-lookup"><span data-stu-id="d7d03-250">Publish to Windows using IIS</span></span>](../publishing/iis.md)
* [<span data-ttu-id="d7d03-251">将发布到 Linux 使用 Nginx</span><span class="sxs-lookup"><span data-stu-id="d7d03-251">Publish to Linux using Nginx</span></span>](../publishing/linuxproduction.md)
* [<span data-ttu-id="d7d03-252">将发布到使用 Apache 的 Linux</span><span class="sxs-lookup"><span data-stu-id="d7d03-252">Publish to Linux using Apache</span></span>](../publishing/apache-proxy.md)
* [<span data-ttu-id="d7d03-253">在 Windows 服务中的主机</span><span class="sxs-lookup"><span data-stu-id="d7d03-253">Host in a Windows Service</span></span>](xref:hosting/windows-service)

