---
title: "ASP.NET Core 中的 Web 服务器实现"
author: tdykstra
description: "介绍适用于 ASP.NET Core 的 Web 服务器 Kestrel 和 WebListener。 提供了如何进行选择以及何时将其与反向代理服务器结合使用的指南。"
keywords: "ASP.NET Core, IServer, Web 服务器, Kestrel, WebListener, 反向代理"
ms.author: tdykstra
manager: wpickett
ms.date: 08/03/2017
ms.topic: article
ms.assetid: dba74f39-58cd-4dee-a061-6d15f7346959
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/index
ms.openlocfilehash: 1e6d0836f0da751fe433273b9a6896fcf259b69d
ms.sourcegitcommit: 74e22e08e3b08cb576e5184d16f4af5656c13c0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2017
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="82cae-105">ASP.NET Core 中的 Web 服务器实现</span><span class="sxs-lookup"><span data-stu-id="82cae-105">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="82cae-106">作者：[Tom Dykstra](http://github.com/tdykstra)、 [Steve Smith](http://ardalis.com)、[Stephen Halter](https://twitter.com/halter73) 和 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="82cae-106">By [Tom Dykstra](http://github.com/tdykstra), [Steve Smith](http://ardalis.com), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="82cae-107">ASP.NET Core 应用程序与进程内 HTTP 服务器实现一起运行。</span><span class="sxs-lookup"><span data-stu-id="82cae-107">An ASP.NET Core application runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="82cae-108">服务器实现侦听 HTTP 请求，并在一系列[请求功能](https://docs.asp.net/en/latest/fundamentals/request-features.html)被撰写到 `HttpContext` 时将这些请求展现到应用程序中。</span><span class="sxs-lookup"><span data-stu-id="82cae-108">The server implementation listens for HTTP requests and surfaces them to the application as sets of [request features](https://docs.asp.net/en/latest/fundamentals/request-features.html) composed into an `HttpContext`.</span></span>

<span data-ttu-id="82cae-109">ASP.NET Core 交付两种服务器实现：</span><span class="sxs-lookup"><span data-stu-id="82cae-109">ASP.NET Core ships two server implementations:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="82cae-110">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="82cae-110">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

* <span data-ttu-id="82cae-111">[Kestrel](kestrel.md) 是跨平台 HTTP 服务器，它基于 [libuv](https://github.com/libuv/libuv)（一个跨平台异步 I/O 库）。</span><span class="sxs-lookup"><span data-stu-id="82cae-111">[Kestrel](kestrel.md) is a cross-platform HTTP server based on [libuv](https://github.com/libuv/libuv), a cross-platform asynchronous I/O library.</span></span>

* <span data-ttu-id="82cae-112">[HTTP.sys](httpsys.md) 是仅适用于 Windows 的 HTTP 服务器，它基于 [Http.Sys 核心驱动程序](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)。</span><span class="sxs-lookup"><span data-stu-id="82cae-112">[HTTP.sys](httpsys.md) is a Windows-only HTTP server based on the [Http.Sys kernel driver](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="82cae-113">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="82cae-113">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

* <span data-ttu-id="82cae-114">[Kestrel](kestrel.md) 是跨平台 HTTP 服务器，它基于 [libuv](https://github.com/libuv/libuv)（一个跨平台异步 I/O 库）。</span><span class="sxs-lookup"><span data-stu-id="82cae-114">[Kestrel](kestrel.md) is a cross-platform HTTP server based on [libuv](https://github.com/libuv/libuv), a cross-platform asynchronous I/O library.</span></span>

* <span data-ttu-id="82cae-115">[WebListener](weblistener.md) 是仅适用于 Windows 的 HTTP 服务器，它基于 [Http.Sys 核心驱动程序](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)。</span><span class="sxs-lookup"><span data-stu-id="82cae-115">[WebListener](weblistener.md) is a Windows-only HTTP server based on the [Http.Sys kernel driver](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span></span>

---

## <a name="kestrel"></a><span data-ttu-id="82cae-116">Kestrel</span><span class="sxs-lookup"><span data-stu-id="82cae-116">Kestrel</span></span>

<span data-ttu-id="82cae-117">Kestrel 是 Web 服务器，它默认包括在 ASP.NET Core 新项目模板中。</span><span class="sxs-lookup"><span data-stu-id="82cae-117">Kestrel is the web server that is included by default in ASP.NET Core new-project templates.</span></span> 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="82cae-118">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="82cae-118">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="82cae-119">可以单独使用 Kestrel，也可以将其与反向代理服务器（如 IIS、Nginx 或 Apache）结合使用。</span><span class="sxs-lookup"><span data-stu-id="82cae-119">You can use Kestrel by itself or with a *reverse proxy server*, such as IIS, Nginx, or Apache.</span></span> <span data-ttu-id="82cae-120">反向代理服务器接收到来自 Internet 的 HTTP 请求，并在进行一些初步处理后将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="82cae-120">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel after some preliminary handling.</span></span>

![Kestrel 直接与 Internet 通信，不使用反向代理服务器](kestrel/_static/kestrel-to-internet2.png)

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="82cae-123">如果仅向内部网络公开 Kestrel，可以使用任一配置（无需考虑是否使用反向代理服务器）。</span><span class="sxs-lookup"><span data-stu-id="82cae-123">Either configuration &mdash; with or without a reverse proxy server &mdash; can also be used if Kestrel is exposed only to an internal network.</span></span>

<span data-ttu-id="82cae-124">有关何时将 Kestrel 与反向代理结合使用的信息，请参阅 [Kestrel 简介](kestrel.md)。</span><span class="sxs-lookup"><span data-stu-id="82cae-124">For information about when to use Kestrel with a reverse proxy, see [Introduction to Kestrel](kestrel.md).</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="82cae-125">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="82cae-125">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="82cae-126">如果应用程序仅接受来自内部网络的请求，则可以单独使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="82cae-126">If your application accepts requests only from an internal network, you can use Kestrel by itself.</span></span>

![Kestrel 直接与你的内部网络进行通信](kestrel/_static/kestrel-to-internal.png)

<span data-ttu-id="82cae-128">如果将应用程序公开到 Internet，则必须将 IIS、Nginx 或 Apache 用作反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="82cae-128">If you expose your application to the Internet, you must use IIS, Nginx, or Apache as a *reverse proxy server*.</span></span> <span data-ttu-id="82cae-129">反向代理服务器接收到来自 Internet 的 HTTP 请求，并在进行一些初步处理后将其转发到 Kestrel，如下图所示。</span><span class="sxs-lookup"><span data-stu-id="82cae-129">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel after some preliminary handling, as shown in the following diagram.</span></span>

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="82cae-131">为边缘部署使用反向代理的最重要的原因（从 Internet 公开到流量）是出于安全考虑。</span><span class="sxs-lookup"><span data-stu-id="82cae-131">The most important reason for using a reverse proxy for edge deployments (exposed to traffic from the Internet) is security.</span></span> <span data-ttu-id="82cae-132">Kestrel 的 1.x 版本不包含对防御攻击的充分补充。</span><span class="sxs-lookup"><span data-stu-id="82cae-132">The 1.x versions of Kestrel don't have a full complement of defenses against attacks.</span></span> <span data-ttu-id="82cae-133">这包括但不限于相应的超时、大小限制和并发连接限制。</span><span class="sxs-lookup"><span data-stu-id="82cae-133">This includes, but isn't limited to, appropriate timeouts, size limits, and concurrent connection limits.</span></span>

<span data-ttu-id="82cae-134">有关何时将 Kestrel 与反向代理结合使用的信息，请参阅 [Kestrel 简介](kestrel.md)。</span><span class="sxs-lookup"><span data-stu-id="82cae-134">For information about when to use Kestrel with a reverse proxy, see [Introduction to Kestrel](kestrel.md).</span></span>

---

<span data-ttu-id="82cae-135">在没有 Kestrel 或[自定义服务器实现](#custom-servers)的情况下，不能使用 IIS、Nginx 或 Apache。</span><span class="sxs-lookup"><span data-stu-id="82cae-135">You can't use IIS, Nginx, or Apache without Kestrel or a [custom server implementation](#custom-servers).</span></span> <span data-ttu-id="82cae-136">ASP.NET Core 设计为在其自己的进程中运行，以实现跨平台统一操作。</span><span class="sxs-lookup"><span data-stu-id="82cae-136">ASP.NET Core was designed to run in its own process so that it can behave consistently across platforms.</span></span> <span data-ttu-id="82cae-137">IIS、 Nginx 和 Apache 规定自己的启动进程和环境；若要直接使用它们，ASP.NET Core 必须满足其各自的需求。</span><span class="sxs-lookup"><span data-stu-id="82cae-137">IIS, Nginx, and Apache dictate their own startup process and environment; to use them directly, ASP.NET Core would have to adapt to the needs of each one.</span></span> <span data-ttu-id="82cae-138">使用 Kestrel 等 Web 服务器实现可提供对启动进程和环境的 ASP.NET Core 控制。</span><span class="sxs-lookup"><span data-stu-id="82cae-138">Using a web server implementation such as Kestrel gives ASP.NET Core control over the startup process and environment.</span></span> <span data-ttu-id="82cae-139">因此，只需将这些 Web 服务器设置为对 Kestrel 发出代理请求，而无需尝试将 ASP.NET Core 应用到 IIS、Nginx 或 Apache。</span><span class="sxs-lookup"><span data-stu-id="82cae-139">So rather than trying to adapt ASP.NET Core to IIS, Nginx, or Apache, you just set up those web servers to proxy requests to Kestrel.</span></span> <span data-ttu-id="82cae-140">无论你部署的位置如何，此安排都可以使 `Program.Main` 和 `Startup` 类在实质上相同。</span><span class="sxs-lookup"><span data-stu-id="82cae-140">This arrangement allows your `Program.Main` and `Startup` classes to be essentially the same no matter where you deploy.</span></span>

### <a name="iis-with-kestrel"></a><span data-ttu-id="82cae-141">IIS 与 Kestrel</span><span class="sxs-lookup"><span data-stu-id="82cae-141">IIS with Kestrel</span></span>

<span data-ttu-id="82cae-142">将 IIS 或 IIS Express 用作 ASP.NET Core 的反向代理时，ASP.NET Core 应用程序在独立于 IIS 工作进程的某个进程中运行。</span><span class="sxs-lookup"><span data-stu-id="82cae-142">When you use IIS or IIS Express as a reverse proxy for ASP.NET Core, the ASP.NET Core application runs in a process separate from the IIS worker process.</span></span> <span data-ttu-id="82cae-143">在 IIS 进程中，运行一个特殊的 IIS 模块，以协调反向代理关系。</span><span class="sxs-lookup"><span data-stu-id="82cae-143">In the IIS process, a special IIS module runs to coordinate the reverse proxy relationship.</span></span>  <span data-ttu-id="82cae-144">这就是 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="82cae-144">This is the *ASP.NET Core Module*.</span></span> <span data-ttu-id="82cae-145">ASP.NET Core 模块的主要功能是启动 ASP.NET Core 应用程序，在其出现故障时重新启动，并向其转发 HTTP 流量。</span><span class="sxs-lookup"><span data-stu-id="82cae-145">The primary functions of the ASP.NET Core Module are to start the ASP.NET Core application, restart it when it crashes, and forward HTTP traffic to it.</span></span> <span data-ttu-id="82cae-146">有关详细信息，请参阅 [ASP.NET Core 模块](aspnet-core-module.md)。</span><span class="sxs-lookup"><span data-stu-id="82cae-146">For more information, see [ASP.NET Core Module](aspnet-core-module.md).</span></span> 

### <a name="nginx-with-kestrel"></a><span data-ttu-id="82cae-147">Nginx 与 Kestrel</span><span class="sxs-lookup"><span data-stu-id="82cae-147">Nginx with Kestrel</span></span>

<span data-ttu-id="82cae-148">有关如何将在 Linux 上使用 Nginx 作为 Kestrel 的反向代理服务器的信息，请参阅[发布到 Linux 生产环境](../../publishing/linuxproduction.md)。</span><span class="sxs-lookup"><span data-stu-id="82cae-148">For information about how to use Nginx on Linux as a reverse proxy server for Kestrel, see [Publish to a Linux Production Environment](../../publishing/linuxproduction.md).</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="82cae-149">Apache 与 Kestrel</span><span class="sxs-lookup"><span data-stu-id="82cae-149">Apache with Kestrel</span></span>

<span data-ttu-id="82cae-150">有关如何将在 Linux 上使用 Apache 作为 Kestrel 的反向代理服务器的信息，请参阅[将 Apache Web 服务器用作反向代理](../../publishing/apache-proxy.md)。</span><span class="sxs-lookup"><span data-stu-id="82cae-150">For information about how to use Apache on Linux as a reverse proxy server for Kestrel, see [Using Apache Web Server as a reverse proxy](../../publishing/apache-proxy.md).</span></span>

## <a name="httpsys"></a><span data-ttu-id="82cae-151">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="82cae-151">HTTP.sys</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="82cae-152">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="82cae-152">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="82cae-153">如果在 Windows 上运行 ASP.NET Core 应用，则 HTTP.sys 是 Kestrel 的替代方法。</span><span class="sxs-lookup"><span data-stu-id="82cae-153">If you run your ASP.NET Core app on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="82cae-154">如果想要在方案中向 Internet 公开应用并且需要使用 Kestrel 并不支持的 HTTP.sys 功能，可以为该方案使用 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="82cae-154">You can use HTTP.sys for scenarios where you expose your app to the Internet and you need HTTP.sys features that Kestrel doesn't support.</span></span> 

![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="82cae-156">对于仅向内部网络公开的应用程序而言，HTTP.sys 同样适用。</span><span class="sxs-lookup"><span data-stu-id="82cae-156">HTTP.sys can also be used for applications that are exposed only to an internal network.</span></span> 

![HTTP.sys 直接与你的内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="82cae-158">对于内部网络方案，通常建议使用 Kestrel 以获得最佳性能；但在某些方案中，你可能只想使用 HTTP.sys 提供的功能。</span><span class="sxs-lookup"><span data-stu-id="82cae-158">For internal network scenarios, Kestrel is generally recommended for best performance; but in some scenarios, you might want to use a feature that only HTTP.sys offers.</span></span> <span data-ttu-id="82cae-159">有关 HTTP.sys 功能的信息，请参阅 [HTTP.sys](httpsys.md)。</span><span class="sxs-lookup"><span data-stu-id="82cae-159">For information about HTTP.sys features, see [HTTP.sys](httpsys.md).</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="82cae-160">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="82cae-160">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="82cae-161">HTTP.sys 在 ASP.NET Core 1.x 中被命名为 WebListener。</span><span class="sxs-lookup"><span data-stu-id="82cae-161">HTTP.sys is named WebListener in ASP.NET Core 1.x.</span></span> <span data-ttu-id="82cae-162">如果在 Windows 上运行 ASP.NET Core 应用，在你想要向 Internet 公开应用但无法使用 IIS 时，可以为方案使用 WebListener 作为替代方法。</span><span class="sxs-lookup"><span data-stu-id="82cae-162">If you run your ASP.NET Core app on Windows, WebListener is an alternative that you can use for scenarios where you want to expose your app to the Internet but you can't use IIS.</span></span>

![Weblistener 直接与 Internet 进行通信](weblistener/_static/weblistener-to-internet.png)

<span data-ttu-id="82cae-164">如果需要使用 Kestrel 并不支持的 WebListener 功能，则对于仅向内部网络公开的应用而言，还可以使用 WebListener 来替代 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="82cae-164">WebListener can also be used in place of Kestrel for applications that are exposed only to an internal network, if you need WebListener features that Kestrel doesn't support.</span></span> 

![Weblistener 直接与你的内部网络进行通信](weblistener/_static/weblistener-to-internal.png)

<span data-ttu-id="82cae-166">对于内部网络方案，通常建议使用 Kestrel 以获得最佳性能，但在某些方案中，你可能只想使用 WebListener 提供的功能。</span><span class="sxs-lookup"><span data-stu-id="82cae-166">For internal network scenarios, Kestrel is generally recommended for best performance, but in some scenarios you might want to use a feature that only WebListener offers.</span></span> <span data-ttu-id="82cae-167">有关 WebListener 功能的信息，请参阅 [WebListener](weblistener.md)。</span><span class="sxs-lookup"><span data-stu-id="82cae-167">For information about WebListener features, see [WebListener](weblistener.md).</span></span>

---

## <a name="notes-about-aspnet-core-server-infrastructure"></a><span data-ttu-id="82cae-168">有关 ASP.NET Core 服务器基础结构的说明</span><span class="sxs-lookup"><span data-stu-id="82cae-168">Notes about ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="82cae-169">`Startup` 类 `Configure`方法中提供的 [`IApplicationBuilder`](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Builder/IApplicationBuilder/index.html#Microsoft.AspNetCore.Builder.IApplicationBuilder.md) 公开了类型 [`IFeatureCollection`](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Http/Features/IFeatureCollection/index.html#Microsoft.AspNetCore.Http.Features.IFeatureCollection.md) 的 `ServerFeatures` 属性。</span><span class="sxs-lookup"><span data-stu-id="82cae-169">The [`IApplicationBuilder`](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Builder/IApplicationBuilder/index.html#Microsoft.AspNetCore.Builder.IApplicationBuilder.md) available in the `Startup` class `Configure` method exposes the `ServerFeatures` property of type [`IFeatureCollection`](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Http/Features/IFeatureCollection/index.html#Microsoft.AspNetCore.Http.Features.IFeatureCollection.md).</span></span> <span data-ttu-id="82cae-170">Kestrel 和 WebListener 仅公开单个功能，即 [`IServerAddressesFeature`](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Hosting/Server/Features/IServerAddressesFeature/index.html#Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature.md)，但是不同的服务器实现可能公开其他功能。</span><span class="sxs-lookup"><span data-stu-id="82cae-170">Kestrel and WebListener both expose only a single feature, [`IServerAddressesFeature`](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Hosting/Server/Features/IServerAddressesFeature/index.html#Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature.md), but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="82cae-171">`IServerAddressesFeature` 可用于查找在运行时服务器实现绑定的的端口类型。</span><span class="sxs-lookup"><span data-stu-id="82cae-171">`IServerAddressesFeature` can be used to find out which port the server implementation has bound to at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="82cae-172">自定义服务器</span><span class="sxs-lookup"><span data-stu-id="82cae-172">Custom servers</span></span>

<span data-ttu-id="82cae-173">如果内置服务器无法满足你的需求，可以创建一个自定义服务器实现。</span><span class="sxs-lookup"><span data-stu-id="82cae-173">If the built-in servers don't meet your needs, you can create a custom server implementation.</span></span> <span data-ttu-id="82cae-174">[.NET 的开放 Web 接口 (OWIN) 指南](../owin.md) 演示了如何编写基于 [Nowin](https://github.com/Bobris/Nowin) 的 [IServer](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.hosting.server.iserver) 实现。</span><span class="sxs-lookup"><span data-stu-id="82cae-174">The [Open Web Interface for .NET (OWIN) guide](../owin.md) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based [IServer](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.hosting.server.iserver) implementation.</span></span> <span data-ttu-id="82cae-175">可以仅执行应用程序所需的功能接口，但至少必须支持 [IHttpRequestFeature](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.http.features.ihttprequestfeature) 和 [IHttpResponseFeature](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.http.features.ihttpresponsefeature)。</span><span class="sxs-lookup"><span data-stu-id="82cae-175">You're free to implement just the feature interfaces your application needs, though at a minimum you must support [IHttpRequestFeature](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.http.features.ihttprequestfeature) and [IHttpResponseFeature](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.http.features.ihttpresponsefeature).</span></span>

## <a name="next-steps"></a><span data-ttu-id="82cae-176">后续步骤</span><span class="sxs-lookup"><span data-stu-id="82cae-176">Next steps</span></span>

<span data-ttu-id="82cae-177">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="82cae-177">For more information, see the following resources:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="82cae-178">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="82cae-178">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

- [<span data-ttu-id="82cae-179">Kestrel</span><span class="sxs-lookup"><span data-stu-id="82cae-179">Kestrel</span></span>](kestrel.md)
- [<span data-ttu-id="82cae-180">Kestrel 与 IIS</span><span class="sxs-lookup"><span data-stu-id="82cae-180">Kestrel with IIS</span></span>](aspnet-core-module.md)
- [<span data-ttu-id="82cae-181">Kestrel 与 Nginx</span><span class="sxs-lookup"><span data-stu-id="82cae-181">Kestrel with Nginx</span></span>](../../publishing/linuxproduction.md)
- [<span data-ttu-id="82cae-182">Kestrel 与 Apache</span><span class="sxs-lookup"><span data-stu-id="82cae-182">Kestrel with Apache</span></span>](../../publishing/apache-proxy.md)
- [<span data-ttu-id="82cae-183">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="82cae-183">HTTP.sys</span></span>](httpsys.md)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="82cae-184">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="82cae-184">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

- [<span data-ttu-id="82cae-185">Kestrel</span><span class="sxs-lookup"><span data-stu-id="82cae-185">Kestrel</span></span>](kestrel.md)
- [<span data-ttu-id="82cae-186">Kestrel 与 IIS</span><span class="sxs-lookup"><span data-stu-id="82cae-186">Kestrel with IIS</span></span>](aspnet-core-module.md)
- [<span data-ttu-id="82cae-187">Kestrel 与 Nginx</span><span class="sxs-lookup"><span data-stu-id="82cae-187">Kestrel with Nginx</span></span>](../../publishing/linuxproduction.md)
- [<span data-ttu-id="82cae-188">Kestrel 与 Apache</span><span class="sxs-lookup"><span data-stu-id="82cae-188">Kestrel with Apache</span></span>](../../publishing/apache-proxy.md)
- [<span data-ttu-id="82cae-189">WebListener</span><span class="sxs-lookup"><span data-stu-id="82cae-189">WebListener</span></span>](weblistener.md)

---
