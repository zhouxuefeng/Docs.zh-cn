---
title: "在 ASP.NET Core HTTP.sys web 服务器实现"
author: rick-anderson
description: "引入了 HTTP.sys，ASP.NET 核心 Windows 上的 web 服务器。 基于 Http.Sys 内核模式驱动程序，HTTP.sys 是可以用于直接连接到 Internet，不含 IIS 的 Kestrel 的替代方法。"
keywords: "ASP.NET 核心、 httpsys 将其保留、 HTTP.sys、 HttpListener、 url 前缀，SSL"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: article
ms.assetid: 0a7286e4-6428-424e-b5c4-5c98815cf61c
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 4173a2ef539859031674fb613b25031e3b343c67
ms.sourcegitcommit: 74e22e08e3b08cb576e5184d16f4af5656c13c0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2017
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="53668-105">在 ASP.NET Core HTTP.sys web 服务器实现</span><span class="sxs-lookup"><span data-stu-id="53668-105">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="53668-106">通过[Tom Dykstra](http://github.com/tdykstra)和[Chris 跨](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="53668-106">By [Tom Dykstra](http://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

> [!NOTE]
> <span data-ttu-id="53668-107">本主题仅适用于 ASP.NET 核心 2.0 和更高版本。</span><span class="sxs-lookup"><span data-stu-id="53668-107">This topic applies only to ASP.NET Core 2.0 and later.</span></span> <span data-ttu-id="53668-108">在早期版本的 ASP.NET 核心，HTTP.sys 为[WebListener](WebListener.md)。</span><span class="sxs-lookup"><span data-stu-id="53668-108">In earlier versions of ASP.NET Core, HTTP.sys is named [WebListener](WebListener.md).</span></span>

<span data-ttu-id="53668-109">HTTP.sys 是[ASP.NET Core 的 web 服务器](index.md)，仅在 Windows 上运行。</span><span class="sxs-lookup"><span data-stu-id="53668-109">HTTP.sys is a [web server for ASP.NET Core](index.md) that runs only on Windows.</span></span> <span data-ttu-id="53668-110">构建的[Http.Sys 内核模式驱动程序](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)。</span><span class="sxs-lookup"><span data-stu-id="53668-110">It's built on the [Http.Sys kernel mode driver](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span></span> <span data-ttu-id="53668-111">HTTP.sys 是一种替代方法[Kestrel](kestrel.md) ，提供了 Kestel 不一些功能。</span><span class="sxs-lookup"><span data-stu-id="53668-111">HTTP.sys is an alternative to [Kestrel](kestrel.md) that offers some features that Kestel doesn't.</span></span> <span data-ttu-id="53668-112">**HTTP.sys 不能与使用 IIS 或 IIS Express，因为它与不兼容[ASP.NET 核心模块](aspnet-core-module.md)。**</span><span class="sxs-lookup"><span data-stu-id="53668-112">**HTTP.sys can't be used with IIS or IIS Express, as it isn't compatible with the [ASP.NET Core Module](aspnet-core-module.md).**</span></span>

<span data-ttu-id="53668-113">HTTP.sys 支持以下功能：</span><span class="sxs-lookup"><span data-stu-id="53668-113">HTTP.sys supports the following features:</span></span>

- [<span data-ttu-id="53668-114">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="53668-114">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
- <span data-ttu-id="53668-115">端口共享</span><span class="sxs-lookup"><span data-stu-id="53668-115">Port sharing</span></span>
- <span data-ttu-id="53668-116">具有 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="53668-116">HTTPS with SNI</span></span>
- <span data-ttu-id="53668-117">HTTP/2 tls (Windows 10)</span><span class="sxs-lookup"><span data-stu-id="53668-117">HTTP/2 over TLS (Windows 10)</span></span>
- <span data-ttu-id="53668-118">直接文件传输</span><span class="sxs-lookup"><span data-stu-id="53668-118">Direct file transmission</span></span>
- <span data-ttu-id="53668-119">响应缓存</span><span class="sxs-lookup"><span data-stu-id="53668-119">Response caching</span></span>
- <span data-ttu-id="53668-120">Websocket (Windows 8)</span><span class="sxs-lookup"><span data-stu-id="53668-120">WebSockets (Windows 8)</span></span>

<span data-ttu-id="53668-121">支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="53668-121">Supported Windows versions:</span></span>

- <span data-ttu-id="53668-122">Windows 7 和 Windows Server 2008 R2 及更高版本</span><span class="sxs-lookup"><span data-stu-id="53668-122">Windows 7 and Windows Server 2008 R2 and later</span></span>

[<span data-ttu-id="53668-123">查看或下载示例代码</span><span class="sxs-lookup"><span data-stu-id="53668-123">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample)

## <a name="when-to-use-httpsys"></a><span data-ttu-id="53668-124">何时使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="53668-124">When to use HTTP.sys</span></span>

<span data-ttu-id="53668-125">HTTP.sys 是用于部署你需要公开直接向 Internet 的服务器，而使用 IIS 的位置。</span><span class="sxs-lookup"><span data-stu-id="53668-125">HTTP.sys is useful for deployments where you need to expose the server directly to the Internet without using IIS.</span></span>

![HTTP.sys 直接与 Internet 通信](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="53668-127">由于它在 Http.Sys 上构建，HTTP.sys 不需要反向代理服务器，为了针对攻击提供保护。</span><span class="sxs-lookup"><span data-stu-id="53668-127">Because it's built on Http.Sys, HTTP.sys doesn't require a reverse proxy server for protection against attacks.</span></span> <span data-ttu-id="53668-128">Http.Sys 是针对许多类型的攻击提供保护，并提供可靠性、 安全性和功能全面的 web 服务器的可伸缩性的成熟技术。</span><span class="sxs-lookup"><span data-stu-id="53668-128">Http.Sys is mature technology that protects against many kinds of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="53668-129">作为 HTTP 侦听器在 Http.Sys 运行 IIS 本身。</span><span class="sxs-lookup"><span data-stu-id="53668-129">IIS itself runs as an HTTP listener on top of Http.Sys.</span></span> 

<span data-ttu-id="53668-130">HTTP.sys 时需要的功能中 Kestrel，例如 Windows 身份验证不可用，将为内部部署一个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="53668-130">HTTP.sys is a good choice for internal deployments when you need a feature not available in Kestrel, such as Windows authentication.</span></span>

![HTTP.sys 直接与你的内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

## <a name="how-to-use-httpsys"></a><span data-ttu-id="53668-132">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="53668-132">How to use HTTP.sys</span></span>

<span data-ttu-id="53668-133">下面是为主机操作系统和 ASP.NET Core 应用程序的安装程序任务的概述。</span><span class="sxs-lookup"><span data-stu-id="53668-133">Here's an overview of setup tasks for the host OS and your ASP.NET Core application.</span></span>

### <a name="configure-windows-server"></a><span data-ttu-id="53668-134">配置 Windows Server</span><span class="sxs-lookup"><span data-stu-id="53668-134">Configure Windows Server</span></span>

* <span data-ttu-id="53668-135">安装版本的.NET 应用程序要求，如[.NET 核心](https://www.microsoft.com/net/download/core)或[.NET Framework](https://www.microsoft.com/net/download/framework)。</span><span class="sxs-lookup"><span data-stu-id="53668-135">Install the version of .NET that your application requires, such as [.NET Core](https://www.microsoft.com/net/download/core) or [.NET Framework](https://www.microsoft.com/net/download/framework).</span></span>

* <span data-ttu-id="53668-136">预先注册 URL 前缀绑定到 HTTP.sys，以及设置 SSL 证书</span><span class="sxs-lookup"><span data-stu-id="53668-136">Preregister URL prefixes to bind to HTTP.sys, and set up SSL certificates</span></span>

   <span data-ttu-id="53668-137">如果你不预先注册 URL 前缀 Windows 中的，你必须使用管理员特权运行你的应用程序。</span><span class="sxs-lookup"><span data-stu-id="53668-137">If you don't preregister URL prefixes in Windows, you have to run your application with administrator privileges.</span></span> <span data-ttu-id="53668-138">唯一的例外是如果你将绑定到使用端口号大于 1024; 使用 HTTP (而不是 HTTPS) 的 localhost在这种情况下，无需使用管理员特权。</span><span class="sxs-lookup"><span data-stu-id="53668-138">The only exception is if you bind to localhost using HTTP (not HTTPS) with a port number greater than 1024; in that case, administrator privileges aren't required.</span></span>

   <span data-ttu-id="53668-139">有关详细信息，请参阅[如何预先注册前缀和配置 SSL](#preregister-url-prefixes-and-configure-ssl)本文后续部分中。</span><span class="sxs-lookup"><span data-stu-id="53668-139">For details, see [How to preregister prefixes and configure SSL](#preregister-url-prefixes-and-configure-ssl) later in this article.</span></span>

* <span data-ttu-id="53668-140">打开防火墙端口以允许流量抵达 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="53668-140">Open firewall ports to allow traffic to reach HTTP.sys.</span></span>

   <span data-ttu-id="53668-141">你可以使用*netsh.exe*或[PowerShell cmdlet](https://technet.microsoft.com/library/jj554906)。</span><span class="sxs-lookup"><span data-stu-id="53668-141">You can use *netsh.exe* or [PowerShell cmdlets](https://technet.microsoft.com/library/jj554906).</span></span>

<span data-ttu-id="53668-142">也有[的 Http.Sys 注册表设置](https://support.microsoft.com/kb/820129)。</span><span class="sxs-lookup"><span data-stu-id="53668-142">There are also [Http.Sys registry settings](https://support.microsoft.com/kb/820129).</span></span>

### <a name="configure-your-aspnet-core-application-to-use-httpsys"></a><span data-ttu-id="53668-143">配置 ASP.NET Core 应用程序，以使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="53668-143">Configure your ASP.NET Core application to use HTTP.sys</span></span>

* <span data-ttu-id="53668-144">如果你使用没有包安装需要[Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage。</span><span class="sxs-lookup"><span data-stu-id="53668-144">No package install is needed if you use the [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage.</span></span> <span data-ttu-id="53668-145">[Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)中 metapackage 包含包。</span><span class="sxs-lookup"><span data-stu-id="53668-145">The [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) package is included in the metapackage.</span></span>

* <span data-ttu-id="53668-146">调用`UseHttpSys`上的扩展方法`WebHostBuilder`中你`Main`方法，指定任何[HTTP.sys 选项](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs)，你的需要如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="53668-146">Call the `UseHttpSys` extension method on `WebHostBuilder` in your `Main` method, specifying any [HTTP.sys options](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs) that you need, as shown in the following example:</span></span>

  [!code-csharp[](HttpSys/sample/Program.cs?name=snippet_Main&highlight=11-19)]

### <a name="configure-httpsys-options"></a><span data-ttu-id="53668-147">配置 HTTP.sys 选项</span><span class="sxs-lookup"><span data-stu-id="53668-147">Configure HTTP.sys options</span></span>

<span data-ttu-id="53668-148">以下是一些 HTTP.sys 设置和你可以配置的限制。</span><span class="sxs-lookup"><span data-stu-id="53668-148">Here are some of the HTTP.sys settings and limits that you can configure.</span></span>

<span data-ttu-id="53668-149">**最大客户端连接**</span><span class="sxs-lookup"><span data-stu-id="53668-149">**Maximum client connections**</span></span>

<span data-ttu-id="53668-150">可以为整个应用程序替换为以下代码中设置最大并发打开 TCP 连接数*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="53668-150">The maximum number of concurrent open TCP connections can be set for the entire application with the following code in *Program.cs*:</span></span>

[!code-csharp[](HttpSys/sample/Program.cs?name=snippet_Options&highlight=5)]

<span data-ttu-id="53668-151">最大连接数为默认情况下的不限 (null)。</span><span class="sxs-lookup"><span data-stu-id="53668-151">The maximum number of connections is unlimited (null) by default.</span></span>

<span data-ttu-id="53668-152">**最大请求正文大小**</span><span class="sxs-lookup"><span data-stu-id="53668-152">**Maximum request body size**</span></span>

<span data-ttu-id="53668-153">默认最大请求正文大小为 30,000,000 字节，这是大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="53668-153">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6MB.</span></span>

<span data-ttu-id="53668-154">重写中的 ASP.NET 核心 MVC 应用限制的建议的方法是使用[RequestSizeLimit](https://github.com/aspnet/Mvc/blob/rel/2.0.0/src/Microsoft.AspNetCore.Mvc.Core/RequestSizeLimitAttribute.cs)操作方法的特性：</span><span class="sxs-lookup"><span data-stu-id="53668-154">The recommended way to override the limit in an ASP.NET Core MVC app is to use the [RequestSizeLimit](https://github.com/aspnet/Mvc/blob/rel/2.0.0/src/Microsoft.AspNetCore.Mvc.Core/RequestSizeLimitAttribute.cs) attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="53668-155">下面是一个示例，演示如何配置整个应用程序，每个请求的约束：</span><span class="sxs-lookup"><span data-stu-id="53668-155">Here's an example that shows how to configure the constraint for the entire application, every request:</span></span>

[!code-csharp[](HttpSys/sample/Program.cs?name=snippet_Options&highlight=6)]

<span data-ttu-id="53668-156">你可以重写中的特定请求上的设置*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="53668-156">You can override the setting on a specific request in *Startup.cs*:</span></span>

[!code-csharp[](httpsys/sample/Startup.cs?name=snippet_Configure&highlight=9-10)]
 
<span data-ttu-id="53668-157">如果你尝试在请求配置限制应用程序已开始读取请求后，将引发异常。</span><span class="sxs-lookup"><span data-stu-id="53668-157">An exception is thrown if you try to configure the limit on a request after the application has started reading the request.</span></span> <span data-ttu-id="53668-158">没有`IsReadOnly`属性，告诉你如果`MaxRequestBodySize`属性处于只读状态，这意味着它太迟配置限制。</span><span class="sxs-lookup"><span data-stu-id="53668-158">There's an `IsReadOnly` property that tells you if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="53668-159">有关其他 HTTP.sys 选项的信息，请参阅[HttpSysOptions](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs)。</span><span class="sxs-lookup"><span data-stu-id="53668-159">For information about other HTTP.sys options, see [HttpSysOptions](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs).</span></span> 

### <a name="configure-urls-and-ports-to-listen-on"></a><span data-ttu-id="53668-160">配置要侦听的 Url 和端口</span><span class="sxs-lookup"><span data-stu-id="53668-160">Configure URLs and ports to listen on</span></span> 

<span data-ttu-id="53668-161">默认情况下 ASP.NET Core 将绑定到`http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="53668-161">By default ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="53668-162">若要配置 URL 前缀和端口，可以使用`UseUrls`扩展方法，`urls`命令行参数时，ASPNETCORE_URLS 环境变量，或`UrlPrefixes`属性[HttpSysOptions](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs)。</span><span class="sxs-lookup"><span data-stu-id="53668-162">To configure URL prefixes and ports, you can use the `UseUrls` extension method, the `urls` command-line argument, the ASPNETCORE_URLS environment variable, or the `UrlPrefixes` property on [HttpSysOptions](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs).</span></span> <span data-ttu-id="53668-163">下面的代码示例使用`UrlPrefixes`。</span><span class="sxs-lookup"><span data-stu-id="53668-163">The following code example uses `UrlPrefixes`.</span></span>

[!code-csharp[](HttpSys/sample/Program.cs?name=snippet_Main&highlight=17)]

<span data-ttu-id="53668-164">一个优点`UrlPrefixes`是如果你尝试添加的格式不正确的前缀立即收到一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="53668-164">An advantage of `UrlPrefixes` is that you get an error message immediately if you try to add a prefix that is formatted wrong.</span></span> <span data-ttu-id="53668-165">一个优点`UseUrls`(与共享`urls`和 ASPNETCORE_URLS) 是您可以更轻松地切换 Kestrel 和 HTTP.sys 之间。</span><span class="sxs-lookup"><span data-stu-id="53668-165">An advantage of `UseUrls` (shared with `urls` and ASPNETCORE_URLS) is that you can more easily switch between Kestrel and HTTP.sys.</span></span>

<span data-ttu-id="53668-166">如果你同时使用`UseUrls`(或`urls`或 ASPNETCORE_URLS) 和`UrlPrefixes`中的设置`UrlPrefixes`重写中的`UseUrls`。</span><span class="sxs-lookup"><span data-stu-id="53668-166">If you use both `UseUrls` (or `urls` or ASPNETCORE_URLS) and `UrlPrefixes`, the settings in `UrlPrefixes` override the ones in `UseUrls`.</span></span> <span data-ttu-id="53668-167">有关详细信息，请参阅[宿主](xref:fundamentals/hosting)。</span><span class="sxs-lookup"><span data-stu-id="53668-167">For more information, see [Hosting](xref:fundamentals/hosting).</span></span>

<span data-ttu-id="53668-168">HTTP.sys 使用[HTTP 服务器 API UrlPrefix 字符串格式](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)。</span><span class="sxs-lookup"><span data-stu-id="53668-168">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

> [!NOTE]
> <span data-ttu-id="53668-169">请确保指定相同的前缀字符串中`UseUrls`或`UrlPrefixes`，在服务器上预先注册。</span><span class="sxs-lookup"><span data-stu-id="53668-169">Make sure that you specify the same prefix strings in `UseUrls` or `UrlPrefixes` that you preregister on the server.</span></span> 

### <a name="dont-use-iis"></a><span data-ttu-id="53668-170">不使用 IIS</span><span class="sxs-lookup"><span data-stu-id="53668-170">Don't use IIS</span></span>

<span data-ttu-id="53668-171">请确保你的应用程序未配置为运行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="53668-171">Make sure your application isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="53668-172">在 Visual Studio 中，默认启动配置文件适用于 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="53668-172">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="53668-173">若要运行项目作为控制台应用程序，手动更改所选的配置文件，如下面的屏幕快照中所示。</span><span class="sxs-lookup"><span data-stu-id="53668-173">To run the project as a console application, manually change the selected profile, as shown in the following screen shot.</span></span>

![选择控制台应用程序配置文件](httpsys/_static/vs-choose-profile.png)

## <a name="preregister-url-prefixes-and-configure-ssl"></a><span data-ttu-id="53668-175">预先注册 URL 前缀和配置 SSL</span><span class="sxs-lookup"><span data-stu-id="53668-175">Preregister URL prefixes and configure SSL</span></span>

<span data-ttu-id="53668-176">IIS 和 HTTP.sys 依赖于基础 Http.Sys 内核模式驱动程序以侦听请求，并执行初始处理。</span><span class="sxs-lookup"><span data-stu-id="53668-176">Both IIS and HTTP.sys rely on the underlying Http.Sys kernel mode driver to listen for requests and do initial processing.</span></span> <span data-ttu-id="53668-177">在 IIS 中，管理 UI 也提供相对简单的方式，来配置所有内容。</span><span class="sxs-lookup"><span data-stu-id="53668-177">In IIS, the management UI gives you a relatively easy way to configure everything.</span></span> <span data-ttu-id="53668-178">但是，你需要自行配置 Http.Sys。</span><span class="sxs-lookup"><span data-stu-id="53668-178">However, you need to configure Http.Sys yourself.</span></span> <span data-ttu-id="53668-179">执行操作，它是内置工具*netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="53668-179">The built-in tool for doing that is *netsh.exe*.</span></span> 

<span data-ttu-id="53668-180">与*netsh.exe*可以保留 URL 前缀，还可以将 SSL 证书分配。</span><span class="sxs-lookup"><span data-stu-id="53668-180">With *netsh.exe* you can reserve URL prefixes and assign SSL certificates.</span></span> <span data-ttu-id="53668-181">该工具需要管理权限。</span><span class="sxs-lookup"><span data-stu-id="53668-181">The tool requires administrative privileges.</span></span>

<span data-ttu-id="53668-182">下面的示例演示保留端口 80 和 443 的 URL 前缀所需的最低要求：</span><span class="sxs-lookup"><span data-stu-id="53668-182">The following example shows the minimum needed to reserve URL prefixes for ports 80 and 443:</span></span>

```console
netsh http add urlacl url=http://+:80/ user=Users
netsh http add urlacl url=https://+:443/ user=Users
```

<span data-ttu-id="53668-183">下面的示例演示如何将 SSL 证书分配：</span><span class="sxs-lookup"><span data-stu-id="53668-183">The following example shows how to assign an SSL certificate:</span></span>

```console
netsh http add sslcert ipport=0.0.0.0:443 certhash=MyCertHash_Here appid={00000000-0000-0000-0000-000000000000}"
```

<span data-ttu-id="53668-184">下面是的参考文档*netsh.exe*:</span><span class="sxs-lookup"><span data-stu-id="53668-184">Here is the reference documentation for *netsh.exe*:</span></span>

* [<span data-ttu-id="53668-185">Netsh 命令的超文本传输协议 (HTTP)</span><span class="sxs-lookup"><span data-stu-id="53668-185">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](http://technet.microsoft.com/library/cc725882.aspx)
* [<span data-ttu-id="53668-186">UrlPrefix 字符串</span><span class="sxs-lookup"><span data-stu-id="53668-186">UrlPrefix Strings</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

<span data-ttu-id="53668-187">以下资源提供了几个方案的详细的说明。</span><span class="sxs-lookup"><span data-stu-id="53668-187">The following resources provide detailed instructions for several scenarios.</span></span> <span data-ttu-id="53668-188">请参阅 HttpListener 的文章也同样适用于 HTTP.sys，因为二者都基于 Http.Sys。</span><span class="sxs-lookup"><span data-stu-id="53668-188">Articles that refer to HttpListener apply equally to HTTP.sys, as both are based on Http.Sys.</span></span>

* [<span data-ttu-id="53668-189">如何： 使用 SSL 证书配置端口</span><span class="sxs-lookup"><span data-stu-id="53668-189">How to: Configure a Port with an SSL Certificate</span></span>](http://msdn.microsoft.com/library/ms733791.aspx)
* <span data-ttu-id="53668-190">[HTTPS 通信-HttpListener 基于托管和客户端证书](http://sunshaking.blogspot.com/2012/11/https-communication-httplistener-based.html)这是一个第三方的博客和是相当旧但仍具有有用的信息。</span><span class="sxs-lookup"><span data-stu-id="53668-190">[HTTPS Communication - HttpListener based Hosting and Client Certification](http://sunshaking.blogspot.com/2012/11/https-communication-httplistener-based.html) This is a third-party blog and is fairly old but still has useful information.</span></span>
* <span data-ttu-id="53668-191">[如何： 演练使用 HttpListener 或 Http 服务器非托管代码 （c + +） 为 SSL 简单服务器](http://blogs.msdn.com/b/jpsanders/archive/2009/09/29/walkthrough-using-httplistener-as-an-ssl-simple-server.aspx)这也是有用的信息与较旧的博客。</span><span class="sxs-lookup"><span data-stu-id="53668-191">[How To: Walkthrough Using HttpListener or Http Server unmanaged code (C++) as an SSL Simple Server](http://blogs.msdn.com/b/jpsanders/archive/2009/09/29/walkthrough-using-httplistener-as-an-ssl-simple-server.aspx) This too is an older blog with useful information.</span></span>

<span data-ttu-id="53668-192">以下是一些可以比易于使用的第三方工具*netsh.exe*命令行。</span><span class="sxs-lookup"><span data-stu-id="53668-192">Here are some third-party tools that can be easier to use than the *netsh.exe* command line.</span></span> <span data-ttu-id="53668-193">这些不提供的或由 Microsoft 认可。</span><span class="sxs-lookup"><span data-stu-id="53668-193">These are not provided by or endorsed by Microsoft.</span></span> <span data-ttu-id="53668-194">这些工具以管理员身份运行默认情况下，由于*netsh.exe*本身需要管理员特权。</span><span class="sxs-lookup"><span data-stu-id="53668-194">The tools run as administrator by default, since *netsh.exe* itself requires administrator privileges.</span></span>

* <span data-ttu-id="53668-195">[http.sys Manager](http://httpsysmanager.codeplex.com/)提供有关列表的 UI 和配置 SSL 证书和选项，前缀保留，和证书信任列表。</span><span class="sxs-lookup"><span data-stu-id="53668-195">[http.sys Manager](http://httpsysmanager.codeplex.com/) provides UI for listing and configuring SSL certificates and options, prefix reservations, and certificate trust lists.</span></span> 
* <span data-ttu-id="53668-196">[HttpConfig](http://www.stevestechspot.com/ABetterHttpcfg.aspx)允许列表或配置 SSL 证书和 URL 前缀。</span><span class="sxs-lookup"><span data-stu-id="53668-196">[HttpConfig](http://www.stevestechspot.com/ABetterHttpcfg.aspx) lets you list or configure SSL certificates and URL prefixes.</span></span> <span data-ttu-id="53668-197">UI 是更完善比 http.sys 管理器，并公开一些更多配置选项，但其他方面，它提供类似的功能。</span><span class="sxs-lookup"><span data-stu-id="53668-197">The UI is more refined than http.sys Manager and exposes a few more configuration options, but otherwise it provides similar functionality.</span></span> <span data-ttu-id="53668-198">它不能创建新的证书信任列表 (CTL)，但可以分配现有的。</span><span class="sxs-lookup"><span data-stu-id="53668-198">It cannot create a new certificate trust list (CTL), but can assign existing ones.</span></span>

[!INCLUDE[How to make an SSL cert](../../includes/make-ssl-cert.md)]

## <a name="next-steps"></a><span data-ttu-id="53668-199">后续步骤</span><span class="sxs-lookup"><span data-stu-id="53668-199">Next steps</span></span>

<span data-ttu-id="53668-200">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="53668-200">For more information, see the following resources:</span></span>

* [<span data-ttu-id="53668-201">本文的示例应用</span><span class="sxs-lookup"><span data-stu-id="53668-201">Sample app for this article</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/HttpSys/sample)
* [<span data-ttu-id="53668-202">HTTP.sys 源代码</span><span class="sxs-lookup"><span data-stu-id="53668-202">HTTP.sys source code</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="53668-203">承载</span><span class="sxs-lookup"><span data-stu-id="53668-203">Hosting</span></span>](xref:fundamentals/hosting)
