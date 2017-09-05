---
title: "ASP.NET 核心模块"
author: tdykstra
description: "引入了 ASP.NET 核心模块 (ANCM) 使 Kestrel web 服务器可以使用 IIS 或 IIS Express 作为反向代理服务器的 IIS 模块。"
keywords: "ASP.NET 核心、 IIS、 IIS Express，ASP.NET Core 模块 UseIISIntegration"
ms.author: tdykstra
manager: wpickett
ms.date: 08/03/2017
ms.topic: article
ms.assetid: 4661af33-34c5-4d71-93a0-8c7632f43580
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/aspnet-core-module
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c4124f71f30b758d82a6bf641328a8d5abf779f2
ms.sourcegitcommit: 74e22e08e3b08cb576e5184d16f4af5656c13c0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2017
---
# <a name="introduction-to-aspnet-core-module"></a><span data-ttu-id="df721-104">ASP.NET 核心模块简介</span><span class="sxs-lookup"><span data-stu-id="df721-104">Introduction to ASP.NET Core Module</span></span>

<span data-ttu-id="df721-105">通过[Tom Dykstra](http://github.com/tdykstra)， [Rick Strahl](https://github.com/RickStrahl)，和[Chris 跨](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="df721-105">By [Tom Dykstra](http://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), and [Chris Ross](https://github.com/Tratcher)</span></span> 

<span data-ttu-id="df721-106">ASP.NET 核心模块 (ANCM) 允许您在 IIS 中，后面的应用程序中运行 ASP.NET 核心的很好地 （安全、 可管理性，还有很多） 使用 IIS 和使用[Kestrel](kestrel.md)为很好地 （在非常短），并获取从一次这两种技术的优势。</span><span class="sxs-lookup"><span data-stu-id="df721-106">ASP.NET Core Module (ANCM) lets you run ASP.NET Core applications behind IIS, using IIS for what it's good at (security, manageability, and lots more) and using [Kestrel](kestrel.md) for what it's good at (being really fast), and getting the benefits from both technologies at once.</span></span> <span data-ttu-id="df721-107">**ANCM 仅适用于 Kestrel;它与不兼容 WebListener (在 ASP.NET Core 1.x) 或 HTTP.sys （在 2.x)。**</span><span class="sxs-lookup"><span data-stu-id="df721-107">**ANCM works only with Kestrel; it isn't compatible with WebListener (in ASP.NET Core 1.x) or HTTP.sys (in 2.x).**</span></span> 

<span data-ttu-id="df721-108">支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="df721-108">Supported Windows versions:</span></span>

* <span data-ttu-id="df721-109">Windows 7 和 Windows Server 2008 R2 及更高版本</span><span class="sxs-lookup"><span data-stu-id="df721-109">Windows 7 and Windows Server 2008 R2 and later</span></span>

[<span data-ttu-id="df721-110">查看或下载示例代码</span><span class="sxs-lookup"><span data-stu-id="df721-110">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/aspnet-core-module/sample)

## <a name="what-aspnet-core-module-does"></a><span data-ttu-id="df721-111">ASP.NET 核心模块的用途</span><span class="sxs-lookup"><span data-stu-id="df721-111">What ASP.NET Core Module does</span></span>

<span data-ttu-id="df721-112">ANCM 是一个本机的 IIS 模块，挂钩到 IIS 管道和将流量重定向到后端 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="df721-112">ANCM is a native IIS module that hooks into the IIS pipeline and redirects traffic to the backend ASP.NET Core application.</span></span> <span data-ttu-id="df721-113">大多数其他模块，例如 windows 身份验证，仍有机会运行。</span><span class="sxs-lookup"><span data-stu-id="df721-113">Most other modules, such as windows authentication, still get a chance to run.</span></span> <span data-ttu-id="df721-114">ANCM 仅采用控制时为请求中，选择一个处理程序并在应用程序中定义处理程序映射*web.config*文件。</span><span class="sxs-lookup"><span data-stu-id="df721-114">ANCM only takes control when a handler is selected for the request, and handler mapping is defined in the application *web.config* file.</span></span>

<span data-ttu-id="df721-115">因为 ASP.NET Core 应用程序在进程中运行的 IIS 工作进程分开，ANCM 还未处理管理。</span><span class="sxs-lookup"><span data-stu-id="df721-115">Because ASP.NET Core applications run in a process separate from the IIS worker process, ANCM also does process management.</span></span> <span data-ttu-id="df721-116">当第一个请求传入并且重新启动该崩溃时，ANCM 启动 ASP.NET Core 应用程序的过程。</span><span class="sxs-lookup"><span data-stu-id="df721-116">ANCM starts the process for the ASP.NET Core application when the first request comes in and restarts it when it crashes.</span></span> <span data-ttu-id="df721-117">这是实质上是相同经典 ASP.NET 应用程序的行为运行中进程在 IIS 中，并且由管理 WAS （Windows 激活服务）。</span><span class="sxs-lookup"><span data-stu-id="df721-117">This is essentially the same behavior as classic ASP.NET applications that run in-process in IIS and are managed by WAS (Windows Activation Service).</span></span>

<span data-ttu-id="df721-118">下面是阐释了 IIS、 ANCM 和 ASP.NET Core 应用程序之间的关系的关系图。</span><span class="sxs-lookup"><span data-stu-id="df721-118">Here's a diagram that illustrates the relationship between IIS, ANCM, and ASP.NET Core applications.</span></span>

![ASP.NET 核心模块](aspnet-core-module/_static/ancm.png)

<span data-ttu-id="df721-120">请求来自 Web 和命中的内核模式 Http.Sys 驱动程序，将其路由到主端口 (80) 或 SSL 端口 (443) 上的 IIS。</span><span class="sxs-lookup"><span data-stu-id="df721-120">Requests come in from the Web and hit the kernel mode Http.Sys driver which routes them into IIS on the primary port (80) or SSL port (443).</span></span> <span data-ttu-id="df721-121">ANCM 将请求转发到 ASP.NET 核心应用程序上不是端口 80/443 对应用程序配置的 HTTP 端口。</span><span class="sxs-lookup"><span data-stu-id="df721-121">ANCM forwards the requests to the ASP.NET Core application on the HTTP port configured for the application, which is not port 80/443.</span></span>

<span data-ttu-id="df721-122">Kestrel 侦听来自 ANCM 的流量。</span><span class="sxs-lookup"><span data-stu-id="df721-122">Kestrel listens for traffic coming from ANCM.</span></span>  <span data-ttu-id="df721-123">ANCM 指定通过在启动时，环境变量的端口和[UseIISIntegration](#call-useiisintegration)方法将服务器配置为侦听`http://localhost:{port}`。</span><span class="sxs-lookup"><span data-stu-id="df721-123">ANCM specifies the port via environment variable at startup, and the [UseIISIntegration](#call-useiisintegration) method configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="df721-124">有一些其他检查，以拒绝不是从 ANCM 的请求。</span><span class="sxs-lookup"><span data-stu-id="df721-124">There are additional checks to reject requests not from ANCM.</span></span> <span data-ttu-id="df721-125">（ANCM 不支持 HTTPS 转发，因此即使 IIS 通过 HTTPS 接收到请求通过 HTTP 转发。）</span><span class="sxs-lookup"><span data-stu-id="df721-125">(ANCM does not support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.)</span></span>

<span data-ttu-id="df721-126">Kestrel ANCM 从那里获取请求，并将它们推送到 ASP.NET 核心中间件管道，其处理它们，然后将它们作为传递`HttpContext`到应用程序逻辑的实例。</span><span class="sxs-lookup"><span data-stu-id="df721-126">Kestrel picks up requests from ANCM and pushes them into the ASP.NET Core middleware pipeline, which then handles them and passes them on as `HttpContext` instances to application logic.</span></span> <span data-ttu-id="df721-127">然后，应用程序的响应会传递回 IIS，它们回退到 HTTP 客户端启动请求的推送。</span><span class="sxs-lookup"><span data-stu-id="df721-127">The application's responses are then passed back to IIS, which pushes them back out to the HTTP client that initiated the requests.</span></span>

<span data-ttu-id="df721-128">ANCM 具有少数几个其他函数：</span><span class="sxs-lookup"><span data-stu-id="df721-128">ANCM has a few other functions as well:</span></span>

* <span data-ttu-id="df721-129">设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="df721-129">Sets environment variables.</span></span>
* <span data-ttu-id="df721-130">日志`stdout`输出到文件存储。</span><span class="sxs-lookup"><span data-stu-id="df721-130">Logs `stdout` output to file storage.</span></span>
* <span data-ttu-id="df721-131">将转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="df721-131">Forwards Windows authentication tokens.</span></span>

## <a name="how-to-use-ancm-in-aspnet-core-apps"></a><span data-ttu-id="df721-132">如何在 ASP.NET Core 应用中使用 ANCM</span><span class="sxs-lookup"><span data-stu-id="df721-132">How to use ANCM in ASP.NET Core apps</span></span>

<span data-ttu-id="df721-133">本部分概述了设置的 IIS 服务器和 ASP.NET Core 应用程序的过程。</span><span class="sxs-lookup"><span data-stu-id="df721-133">This section provides an overview of the process for setting up an IIS server and ASP.NET Core application.</span></span> <span data-ttu-id="df721-134">有关详细说明，请参阅[发布到 IIS](../../publishing/iis.md)。</span><span class="sxs-lookup"><span data-stu-id="df721-134">For detailed instructions, see [Publishing to IIS](../../publishing/iis.md).</span></span>

### <a name="install-ancm"></a><span data-ttu-id="df721-135">安装 ANCM</span><span class="sxs-lookup"><span data-stu-id="df721-135">Install ANCM</span></span>

<span data-ttu-id="df721-136">ASP.NET 核心模块必须安装在 IIS 服务器上和在 IIS Express 在开发计算机上。</span><span class="sxs-lookup"><span data-stu-id="df721-136">The ASP.NET Core Module has to be installed in IIS on your servers and in IIS Express on your development machines.</span></span> <span data-ttu-id="df721-137">对于服务器，纳入 ANCM [.NET 核心 Windows 服务器承载捆绑](https://aka.ms/dotnetcore.2.0.0-windowshosting)。</span><span class="sxs-lookup"><span data-stu-id="df721-137">For servers, ANCM is included in the [.NET Core Windows Server Hosting bundle](https://aka.ms/dotnetcore.2.0.0-windowshosting).</span></span> <span data-ttu-id="df721-138">对于开发计算机，Visual Studio 会自动安装 ANCM 在 IIS Express 中，并在 IIS 中如果计算机上已安装。</span><span class="sxs-lookup"><span data-stu-id="df721-138">For development machines, Visual Studio automatically installs ANCM in IIS Express, and in IIS if it is already installed on the machine.</span></span>

### <a name="install-the-iisintegration-nuget-package"></a><span data-ttu-id="df721-139">安装 IISIntegration NuGet 包</span><span class="sxs-lookup"><span data-stu-id="df721-139">Install the IISIntegration NuGet package</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="df721-140">ASP.NET 核心 2.x</span><span class="sxs-lookup"><span data-stu-id="df721-140">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="df721-141">[Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)包包含在 ASP.NET Core metapackages ([Microsoft.AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore/)和[Microsoft.AspNetCore.All](xref:fundamentals/metapackage)).</span><span class="sxs-lookup"><span data-stu-id="df721-141">The [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/) package is included in the ASP.NET Core metapackages ([Microsoft.AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore/) and [Microsoft.AspNetCore.All](xref:fundamentals/metapackage)).</span></span> <span data-ttu-id="df721-142">如果不使用 metapackages 之一时，安装`Microsoft.AspNetCore.Server.IISIntegration`单独。</span><span class="sxs-lookup"><span data-stu-id="df721-142">If you don't use one of the metapackages, install `Microsoft.AspNetCore.Server.IISIntegration` separately.</span></span> <span data-ttu-id="df721-143">`IISIntegration`包是读取广播 ANCM 设置你的应用程序通过环境变量的互操作性包。</span><span class="sxs-lookup"><span data-stu-id="df721-143">The `IISIntegration` package is an interoperability pack that reads environment variables broadcast by ANCM to set up your app.</span></span> <span data-ttu-id="df721-144">环境变量提供配置信息，例如要侦听的端口。</span><span class="sxs-lookup"><span data-stu-id="df721-144">The environment variables provide configuration information, such as the port to listen on.</span></span> 

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="df721-145">ASP.NET 核心 1.x</span><span class="sxs-lookup"><span data-stu-id="df721-145">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="df721-146">在你的应用程序，安装[Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)。</span><span class="sxs-lookup"><span data-stu-id="df721-146">In your application, install [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/).</span></span> <span data-ttu-id="df721-147">`IISIntegration`包是读取广播 ANCM 设置你的应用程序通过环境变量的互操作性包。</span><span class="sxs-lookup"><span data-stu-id="df721-147">The `IISIntegration` package  is an interoperability pack that reads environment variables broadcast by ANCM to set up your app.</span></span> <span data-ttu-id="df721-148">环境变量提供配置信息，例如要侦听的端口。</span><span class="sxs-lookup"><span data-stu-id="df721-148">The environment variables provide configuration information, such as the port to listen on.</span></span> 

---

### <a name="call-useiisintegration"></a><span data-ttu-id="df721-149">调用 UseIISIntegration</span><span class="sxs-lookup"><span data-stu-id="df721-149">Call UseIISIntegration</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="df721-150">ASP.NET 核心 2.x</span><span class="sxs-lookup"><span data-stu-id="df721-150">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="df721-151">`UseIISIntegration`上的扩展方法[ `WebHostBuilder` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilder)使用 IIS 运行时自动调用。</span><span class="sxs-lookup"><span data-stu-id="df721-151">The `UseIISIntegration` extension method on [`WebHostBuilder`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilder) is called automatically when you run with IIS.</span></span>

<span data-ttu-id="df721-152">如果你不使用 ASP.NET Core metapackages 之一，但尚未安装`Microsoft.AspNetCore.Server.IISIntegration`包，则会收到运行时错误。</span><span class="sxs-lookup"><span data-stu-id="df721-152">If you aren't using one of the ASP.NET Core metapackages and haven't installed the  `Microsoft.AspNetCore.Server.IISIntegration` package, you get a runtime error.</span></span> <span data-ttu-id="df721-153">如果调用`UseIISIntegration`显式，获取一个编译时错误，如果未安装此包。</span><span class="sxs-lookup"><span data-stu-id="df721-153">If you call `UseIISIntegration` explicitly, you get a compile time error if the package isn't installed.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="df721-154">ASP.NET 核心 1.x</span><span class="sxs-lookup"><span data-stu-id="df721-154">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="df721-155">应用程序中`Main`方法中，调用`UseIISIntegration`上的扩展方法[ `WebHostBuilder` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilder)。</span><span class="sxs-lookup"><span data-stu-id="df721-155">In your application's `Main` method, call the `UseIISIntegration` extension method on [`WebHostBuilder`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilder).</span></span> 

[!code-csharp[](aspnet-core-module/sample/Program.cs?name=snippet_Main&highlight=12)]

---

<span data-ttu-id="df721-156">`UseIISIntegration`方法会查找环境变量 ANCM 设置，和它否 ops 如果未找到它们。</span><span class="sxs-lookup"><span data-stu-id="df721-156">The `UseIISIntegration` method looks for environment variables that ANCM sets, and it no-ops if they aren't found.</span></span> <span data-ttu-id="df721-157">此行为帮助实现各种方案，例如开发和测试在 macOS 或 Linux 上和部署到运行 IIS 的服务器。</span><span class="sxs-lookup"><span data-stu-id="df721-157">This behavior facilitates scenarios like developing and testing on macOS or Linux and deploying to a server that runs IIS.</span></span> <span data-ttu-id="df721-158">运行时在 macOS 或 Linux 上，Kestrel 充当 web 服务器;但是，当应用程序部署到 IIS 环境中，它会自动使用 ANCM 和 IIS。</span><span class="sxs-lookup"><span data-stu-id="df721-158">While running on macOS or Linux, Kestrel acts as the web server; but when the app is deployed to the IIS environment, it automatically uses ANCM and IIS.</span></span>

### <a name="ancm-port-binding-overrides-other-port-bindings"></a><span data-ttu-id="df721-159">ANCM 端口绑定将替代其他端口绑定</span><span class="sxs-lookup"><span data-stu-id="df721-159">ANCM port binding overrides other port bindings</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="df721-160">ASP.NET 核心 2.x</span><span class="sxs-lookup"><span data-stu-id="df721-160">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="df721-161">ANCM 生成要分配给后端进程的动态端口。</span><span class="sxs-lookup"><span data-stu-id="df721-161">ANCM generates a dynamic port to assign to the back-end process.</span></span> <span data-ttu-id="df721-162">`UseIISIntegration`方法拾取此动态端口，并配置要侦听的 Kestrel `http://locahost:{dynamicPort}/`。</span><span class="sxs-lookup"><span data-stu-id="df721-162">The `UseIISIntegration` method picks up this dynamic port and configures Kestrel to listen on `http://locahost:{dynamicPort}/`.</span></span> <span data-ttu-id="df721-163">此设置将替代其他 URL 配置，如调用到`UseUrls`或[Kestrel 的侦听 API](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration)。</span><span class="sxs-lookup"><span data-stu-id="df721-163">This overrides other URL configurations, such as calls to `UseUrls` or [Kestrel's Listen API](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration).</span></span> <span data-ttu-id="df721-164">因此，你不必调用`UseUrls`或 Kestrel 的`Listen`API 使用 ANCM 时。</span><span class="sxs-lookup"><span data-stu-id="df721-164">Therefore, you don't need to call `UseUrls` or Kestrel's `Listen` API when you use ANCM.</span></span> <span data-ttu-id="df721-165">如果调用`UseUrls`或`Listen`，Kestrel 在运行不含 IIS 应用程序时指定的端口上侦听。</span><span class="sxs-lookup"><span data-stu-id="df721-165">If you do call `UseUrls` or `Listen`, Kestrel listens on the port you specify when you run the app without IIS.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="df721-166">ASP.NET 核心 1.x</span><span class="sxs-lookup"><span data-stu-id="df721-166">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="df721-167">ANCM 生成要分配给后端进程的动态端口。</span><span class="sxs-lookup"><span data-stu-id="df721-167">ANCM generates a dynamic port to assign to the back-end process.</span></span> <span data-ttu-id="df721-168">`UseIISIntegration`方法拾取此动态端口，并配置要侦听的 Kestrel `http://locahost:{dynamicPort}/`。</span><span class="sxs-lookup"><span data-stu-id="df721-168">The `UseIISIntegration` method picks up this dynamic port and configures Kestrel to listen on `http://locahost:{dynamicPort}/`.</span></span> <span data-ttu-id="df721-169">此设置将替代其他 URL 配置，如调用到`UseUrls`。</span><span class="sxs-lookup"><span data-stu-id="df721-169">This overrides other URL configurations, such as calls to `UseUrls`.</span></span> <span data-ttu-id="df721-170">因此，你不必调用`UseUrls`当你使用 ANCM。</span><span class="sxs-lookup"><span data-stu-id="df721-170">Therefore, you don't need to call `UseUrls` when you use ANCM.</span></span> <span data-ttu-id="df721-171">如果调用`UseUrls`，Kestrel 在运行不含 IIS 应用程序时指定的端口上侦听。</span><span class="sxs-lookup"><span data-stu-id="df721-171">If you do call `UseUrls`, Kestrel listens on the port you specify when you run the app without IIS.</span></span>

<span data-ttu-id="df721-172">在 ASP.NET 核心 1.0 中，如果调用`UseUrls`，称之为**之前**调用`UseIISIntegration`以便不会被覆盖，ANCM 配置端口。</span><span class="sxs-lookup"><span data-stu-id="df721-172">In ASP.NET Core 1.0, if you call `UseUrls`, call it **before** you call `UseIISIntegration` so that the ANCM-configured port doesn't get overwritten.</span></span> <span data-ttu-id="df721-173">此调用顺序不需要 ASP.NET 核心 1.1 中，因为 ANCM 设置将重写`UseUrls`。</span><span class="sxs-lookup"><span data-stu-id="df721-173">This calling order isn't required in ASP.NET Core 1.1, because the ANCM setting overrides `UseUrls`.</span></span>

---

### <a name="configure-ancm-options-in-webconfig"></a><span data-ttu-id="df721-174">在 Web.config 中配置 ANCM 选项</span><span class="sxs-lookup"><span data-stu-id="df721-174">Configure ANCM options in Web.config</span></span>

<span data-ttu-id="df721-175">ASP.NET 核心模块的配置存储在*Web.config*位于应用程序的根文件夹中的文件。</span><span class="sxs-lookup"><span data-stu-id="df721-175">Configuration for the ASP.NET Core Module is stored in the *Web.config* file that is located in the application's root folder.</span></span> <span data-ttu-id="df721-176">此文件中的设置指向启动命令并启动 ASP.NET Core 应用的自变量。</span><span class="sxs-lookup"><span data-stu-id="df721-176">Settings in this file point to the startup command and arguments that start your ASP.NET Core app.</span></span> <span data-ttu-id="df721-177">有关 Web.config 的示例代码和配置选项的指南，请参阅[ASP.NET 核心模块配置参考](../../hosting/aspnet-core-module.md)。</span><span class="sxs-lookup"><span data-stu-id="df721-177">For sample Web.config code and guidance on configuration options, see [ASP.NET Core Module Configuration Reference](../../hosting/aspnet-core-module.md).</span></span>

### <a name="run-with-iis-express-in-development"></a><span data-ttu-id="df721-178">在开发过程中使用 IIS Express 运行</span><span class="sxs-lookup"><span data-stu-id="df721-178">Run with IIS Express in development</span></span>

<span data-ttu-id="df721-179">可以通过使用 ASP.NET Core 模板定义的默认配置文件的 Visual Studio 启动 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="df721-179">IIS Express can be launched by Visual Studio using the default profile defined by the ASP.NET Core templates.</span></span>

## <a name="next-steps"></a><span data-ttu-id="df721-180">后续步骤</span><span class="sxs-lookup"><span data-stu-id="df721-180">Next steps</span></span>

<span data-ttu-id="df721-181">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="df721-181">For more information, see the following resources:</span></span>

* [<span data-ttu-id="df721-182">本文的示例应用</span><span class="sxs-lookup"><span data-stu-id="df721-182">Sample app for this article</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/aspnet-core-module/sample)
* [<span data-ttu-id="df721-183">ASP.NET 核心模块的源代码</span><span class="sxs-lookup"><span data-stu-id="df721-183">ASP.NET Core Module source code</span></span>](https://github.com/aspnet/AspNetCoreModule)
* [<span data-ttu-id="df721-184">ASP.NET 核心模块配置参考</span><span class="sxs-lookup"><span data-stu-id="df721-184">ASP.NET Core Module Configuration Reference</span></span>](../../hosting/aspnet-core-module.md)
* [<span data-ttu-id="df721-185">发布到 IIS</span><span class="sxs-lookup"><span data-stu-id="df721-185">Publishing to IIS</span></span>](../../publishing/iis.md)
