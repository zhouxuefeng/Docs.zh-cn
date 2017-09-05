---
title: "在 ASP.NET Core WebListener web 服务器实现"
author: rick-anderson
description: "引入了 WebListener，ASP.NET 核心 Windows 上的 web 服务器。 基于 Http.Sys 内核模式驱动程序，WebListener 是可以用于直接连接到 Internet，不含 IIS 的 Kestrel 的替代方法。"
keywords: "ASP.NET 核心、 WebListener、 HttpListener、 url 前缀，SSL"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: article
ms.assetid: 0a7286e4-6428-424e-b5c4-5c98815cf61c
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/weblistener
ms.openlocfilehash: bcd225875cfe2a544581c331231c704094780ea3
ms.sourcegitcommit: 74e22e08e3b08cb576e5184d16f4af5656c13c0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2017
---
# <a name="weblistener-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="e721a-105">在 ASP.NET Core WebListener web 服务器实现</span><span class="sxs-lookup"><span data-stu-id="e721a-105">WebListener web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="e721a-106">通过[Tom Dykstra](http://github.com/tdykstra)和[Chris 跨](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="e721a-106">By [Tom Dykstra](http://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

> [!NOTE]
> <span data-ttu-id="e721a-107">本主题仅适用于 ASP.NET Core 1.x。</span><span class="sxs-lookup"><span data-stu-id="e721a-107">This topic applies only to ASP.NET Core 1.x.</span></span> <span data-ttu-id="e721a-108">在 ASP.NET 核心 2.0 中，名为 WebListener [HTTP.sys](httpsys.md)。</span><span class="sxs-lookup"><span data-stu-id="e721a-108">In ASP.NET Core 2.0, WebListener is named [HTTP.sys](httpsys.md).</span></span>

<span data-ttu-id="e721a-109">WebListener 是[ASP.NET Core 的 web 服务器](index.md)，仅在 Windows 上运行。</span><span class="sxs-lookup"><span data-stu-id="e721a-109">WebListener is a [web server for ASP.NET Core](index.md) that runs only on Windows.</span></span> <span data-ttu-id="e721a-110">构建的[Http.Sys 内核模式驱动程序](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)。</span><span class="sxs-lookup"><span data-stu-id="e721a-110">It's built on the [Http.Sys kernel mode driver](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span></span> <span data-ttu-id="e721a-111">WebListener 是一种替代方法[Kestrel](kestrel.md) ，可以直接连接到 Internet 使用，而不依赖于为反向代理服务器的 IIS。</span><span class="sxs-lookup"><span data-stu-id="e721a-111">WebListener is an alternative to [Kestrel](kestrel.md) that can be used for direct connection to the Internet without relying on IIS as a reverse proxy server.</span></span> <span data-ttu-id="e721a-112">事实上， **WebListener 不能与使用 IIS 或 IIS Express，因为它与不兼容[ASP.NET 核心模块](aspnet-core-module.md)。**</span><span class="sxs-lookup"><span data-stu-id="e721a-112">In fact, **WebListener can't be used with IIS or IIS Express, as it isn't compatible with the [ASP.NET Core Module](aspnet-core-module.md).**</span></span>

<span data-ttu-id="e721a-113">尽管 ASP.NET Core 开发的目标是 WebListener，它可直接在任何.NET 核心或.NET Framework 应用程序通过[Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="e721a-113">Although WebListener was developed for ASP.NET Core, it can be used directly in any .NET Core or .NET Framework application via the [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) NuGet package.</span></span>

<span data-ttu-id="e721a-114">WebListener 支持以下功能：</span><span class="sxs-lookup"><span data-stu-id="e721a-114">WebListener supports the following features:</span></span>

- [<span data-ttu-id="e721a-115">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="e721a-115">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
- <span data-ttu-id="e721a-116">端口共享</span><span class="sxs-lookup"><span data-stu-id="e721a-116">Port sharing</span></span>
- <span data-ttu-id="e721a-117">具有 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="e721a-117">HTTPS with SNI</span></span>
- <span data-ttu-id="e721a-118">HTTP/2 tls (Windows 10)</span><span class="sxs-lookup"><span data-stu-id="e721a-118">HTTP/2 over TLS (Windows 10)</span></span>
- <span data-ttu-id="e721a-119">直接文件传输</span><span class="sxs-lookup"><span data-stu-id="e721a-119">Direct file transmission</span></span>
- <span data-ttu-id="e721a-120">响应缓存</span><span class="sxs-lookup"><span data-stu-id="e721a-120">Response caching</span></span>
- <span data-ttu-id="e721a-121">Websocket (Windows 8)</span><span class="sxs-lookup"><span data-stu-id="e721a-121">WebSockets (Windows 8)</span></span>

<span data-ttu-id="e721a-122">支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="e721a-122">Supported Windows versions:</span></span>

- <span data-ttu-id="e721a-123">Windows 7 和 Windows Server 2008 R2 及更高版本</span><span class="sxs-lookup"><span data-stu-id="e721a-123">Windows 7 and Windows Server 2008 R2 and later</span></span>

[<span data-ttu-id="e721a-124">查看或下载示例代码</span><span class="sxs-lookup"><span data-stu-id="e721a-124">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/weblistener/sample)

## <a name="when-to-use-weblistener"></a><span data-ttu-id="e721a-125">何时使用 WebListener</span><span class="sxs-lookup"><span data-stu-id="e721a-125">When to use WebListener</span></span>

<span data-ttu-id="e721a-126">WebListener 可用于部署需要公开直接向 Internet 的服务器，而使用 IIS。</span><span class="sxs-lookup"><span data-stu-id="e721a-126">WebListener is useful for deployments where you need to expose the server directly to the Internet without using IIS.</span></span>

![Weblistener 直接与 Internet 通信](weblistener/_static/weblistener-to-internet.png)

<span data-ttu-id="e721a-128">由于它在 Http.Sys 上构建，WebListener 不需要反向代理服务器，为了针对攻击提供保护。</span><span class="sxs-lookup"><span data-stu-id="e721a-128">Because it's built on Http.Sys, WebListener doesn't require a reverse proxy server for protection against attacks.</span></span> <span data-ttu-id="e721a-129">Http.Sys 是针对许多类型的攻击提供保护，并提供可靠性、 安全性和功能全面的 web 服务器的可伸缩性的成熟技术。</span><span class="sxs-lookup"><span data-stu-id="e721a-129">Http.Sys is mature technology that protects against many kinds of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="e721a-130">作为 HTTP 侦听器在 Http.Sys 运行 IIS 本身。</span><span class="sxs-lookup"><span data-stu-id="e721a-130">IIS itself runs as an HTTP listener on top of Http.Sys.</span></span> 

<span data-ttu-id="e721a-131">WebListener 也是一个不错的选择为内部部署时你需要它提供你无法通过使用 Kestrel 获取的功能之一。</span><span class="sxs-lookup"><span data-stu-id="e721a-131">WebListener is also a good choice for internal deployments when you need one of the features it offers that you can't get by using Kestrel.</span></span>

![Weblistener 直接与你的内部网络进行通信](weblistener/_static/weblistener-to-internal.png)

## <a name="how-to-use-weblistener"></a><span data-ttu-id="e721a-133">如何使用 WebListener</span><span class="sxs-lookup"><span data-stu-id="e721a-133">How to use WebListener</span></span>

<span data-ttu-id="e721a-134">下面是为主机操作系统和 ASP.NET Core 应用程序的安装程序任务的概述。</span><span class="sxs-lookup"><span data-stu-id="e721a-134">Here's an overview of setup tasks for the host OS and your ASP.NET Core application.</span></span>

### <a name="configure-windows-server"></a><span data-ttu-id="e721a-135">配置 Windows Server</span><span class="sxs-lookup"><span data-stu-id="e721a-135">Configure Windows Server</span></span>

* <span data-ttu-id="e721a-136">安装版本的.NET 应用程序要求，如[.NET 核心](https://go.microsoft.com/fwlink/?LinkID=827524)或.NET Framework 4.5.1。</span><span class="sxs-lookup"><span data-stu-id="e721a-136">Install the version of .NET that your application requires, such as [.NET Core](https://go.microsoft.com/fwlink/?LinkID=827524) or .NET Framework 4.5.1.</span></span>

* <span data-ttu-id="e721a-137">预先注册 URL 前缀绑定到 WebListener，以及设置 SSL 证书</span><span class="sxs-lookup"><span data-stu-id="e721a-137">Preregister URL prefixes to bind to WebListener, and set up SSL certificates</span></span>

   <span data-ttu-id="e721a-138">如果你不预先注册 URL 前缀 Windows 中的，你必须使用管理员特权运行你的应用程序。</span><span class="sxs-lookup"><span data-stu-id="e721a-138">If you don't preregister URL prefixes in Windows, you have to run your application with administrator privileges.</span></span> <span data-ttu-id="e721a-139">唯一的例外是如果你将绑定到使用端口号大于 1024; 使用 HTTP (而不是 HTTPS) 的 localhost在这种情况下无需使用管理员特权。</span><span class="sxs-lookup"><span data-stu-id="e721a-139">The only exception is if you bind to localhost using HTTP (not HTTPS) with a port number greater than 1024; in that case administrator privileges aren't required.</span></span>

   <span data-ttu-id="e721a-140">有关详细信息，请参阅[如何预先注册前缀和配置 SSL](#preregister-url-prefixes-and-configure-ssl)本文后续部分中。</span><span class="sxs-lookup"><span data-stu-id="e721a-140">For details, see [How to preregister prefixes and configure SSL](#preregister-url-prefixes-and-configure-ssl) later in this article.</span></span>

* <span data-ttu-id="e721a-141">打开防火墙端口以允许流量抵达 WebListener。</span><span class="sxs-lookup"><span data-stu-id="e721a-141">Open firewall ports to allow traffic to reach WebListener.</span></span>

   <span data-ttu-id="e721a-142">你可以使用 netsh.exe 或[PowerShell cmdlet](https://technet.microsoft.com/library/jj554906)。</span><span class="sxs-lookup"><span data-stu-id="e721a-142">You can use netsh.exe or [PowerShell cmdlets](https://technet.microsoft.com/library/jj554906).</span></span>

<span data-ttu-id="e721a-143">也有[的 Http.Sys 注册表设置](https://support.microsoft.com/kb/820129)。</span><span class="sxs-lookup"><span data-stu-id="e721a-143">There are also [Http.Sys registry settings](https://support.microsoft.com/kb/820129).</span></span>

### <a name="configure-your-aspnet-core-application"></a><span data-ttu-id="e721a-144">配置 ASP.NET Core 应用程序</span><span class="sxs-lookup"><span data-stu-id="e721a-144">Configure your ASP.NET Core application</span></span>

* <span data-ttu-id="e721a-145">安装 NuGet 程序包[Microsoft.AspNetCore.Server.WebListener](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.WebListener/)。</span><span class="sxs-lookup"><span data-stu-id="e721a-145">Install the NuGet package [Microsoft.AspNetCore.Server.WebListener](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.WebListener/).</span></span> <span data-ttu-id="e721a-146">这也将安装[Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/)作为依赖项。</span><span class="sxs-lookup"><span data-stu-id="e721a-146">This also installs [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) as a dependency.</span></span>

* <span data-ttu-id="e721a-147">调用[ `UseWebListener` ](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Hosting/WebHostBuilderKestrelExtensions/index.html#Microsoft.AspNetCore.Hosting.WebHostBuilderWebListenerExtensions.UseWebListener.md)上的扩展方法[WebHostBuilder](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Hosting/WebHostBuilder/index.html#Microsoft.AspNetCore.Hosting.WebHostBuilder.md)中你`Main`方法，指定任何 WebListener[选项](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.AspNetCore.Server.WebListener/WebListenerOptions.cs)和[设置](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.Net.Http.Server/WebListenerSettings.cs)，你的需要如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="e721a-147">Call the [`UseWebListener`](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Hosting/WebHostBuilderKestrelExtensions/index.html#Microsoft.AspNetCore.Hosting.WebHostBuilderWebListenerExtensions.UseWebListener.md) extension method on [WebHostBuilder](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Hosting/WebHostBuilder/index.html#Microsoft.AspNetCore.Hosting.WebHostBuilder.md) in your `Main` method, specifying any WebListener [options](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.AspNetCore.Server.WebListener/WebListenerOptions.cs) and [settings](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.Net.Http.Server/WebListenerSettings.cs) that you need, as shown in the following example:</span></span>

  [!code-csharp[](weblistener/sample/Program.cs?name=snippet_Main&highlight=13-17)]

* <span data-ttu-id="e721a-148">配置要侦听的 Url 和端口</span><span class="sxs-lookup"><span data-stu-id="e721a-148">Configure URLs and ports to listen on</span></span> 

  <span data-ttu-id="e721a-149">默认情况下 ASP.NET Core 将绑定到`http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="e721a-149">By default ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="e721a-150">若要配置 URL 前缀和端口，可以使用`UseURLs`扩展方法，`urls`命令行自变量或 ASP.NET 核心配置系统。</span><span class="sxs-lookup"><span data-stu-id="e721a-150">To configure URL prefixes and ports, you can use the `UseURLs` extension method, the `urls` command-line argument or the ASP.NET Core configuration system.</span></span> <span data-ttu-id="e721a-151">有关详细信息，请参阅[宿主](../../fundamentals/hosting.md)。</span><span class="sxs-lookup"><span data-stu-id="e721a-151">For more information, see [Hosting](../../fundamentals/hosting.md).</span></span>

  <span data-ttu-id="e721a-152">Web 侦听器使用[Http.Sys 前缀字符串格式](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)。</span><span class="sxs-lookup"><span data-stu-id="e721a-152">Web Listener uses the [Http.Sys prefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span> <span data-ttu-id="e721a-153">不没有特定于 WebListener 任何前缀字符串格式要求。</span><span class="sxs-lookup"><span data-stu-id="e721a-153">There are no prefix string format requirements that are specific to WebListener.</span></span>

  > [!NOTE]
  > <span data-ttu-id="e721a-154">请确保指定相同的前缀字符串中`UseUrls`，在服务器上预先注册。</span><span class="sxs-lookup"><span data-stu-id="e721a-154">Make sure that you specify the same prefix strings in `UseUrls` that you preregister on the server.</span></span> 

* <span data-ttu-id="e721a-155">请确保你的应用程序未配置为运行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="e721a-155">Make sure your application is not configured to run IIS or IIS Express.</span></span>

  <span data-ttu-id="e721a-156">在 Visual Studio 中，默认启动配置文件适用于 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="e721a-156">In Visual Studio, the default launch profile is for IIS Express.</span></span>  <span data-ttu-id="e721a-157">若要运行该项目作为控制台应用程序必须手动更改所选的配置文件，如下面的屏幕快照中所示。</span><span class="sxs-lookup"><span data-stu-id="e721a-157">To run the project as a console application you have to manually change the selected profile, as shown in the following screen shot.</span></span>

  ![选择控制台应用程序配置文件](weblistener/_static/vs-choose-profile.png)

## <a name="how-to-use-weblistener-outside-of-aspnet-core"></a><span data-ttu-id="e721a-159">如何使用 ASP.NET Core 之外的 WebListener</span><span class="sxs-lookup"><span data-stu-id="e721a-159">How to use WebListener outside of ASP.NET Core</span></span>

* <span data-ttu-id="e721a-160">安装[Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="e721a-160">Install the [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) NuGet package.</span></span>

* <span data-ttu-id="e721a-161">[预先注册 URL 前缀绑定到 WebListener，以及设置 SSL 证书](#preregister-url-prefixes-and-configure-ssl)在 ASP.NET 核心中使用的一样。</span><span class="sxs-lookup"><span data-stu-id="e721a-161">[Preregister URL prefixes to bind to WebListener, and set up SSL certificates](#preregister-url-prefixes-and-configure-ssl) as you would for use in ASP.NET Core.</span></span>

<span data-ttu-id="e721a-162">也有[的 Http.Sys 注册表设置](https://support.microsoft.com/kb/820129)。</span><span class="sxs-lookup"><span data-stu-id="e721a-162">There are also [Http.Sys registry settings](https://support.microsoft.com/kb/820129).</span></span>


<span data-ttu-id="e721a-163">下面是演示 WebListener 使用 ASP.NET Core 之外的代码示例：</span><span class="sxs-lookup"><span data-stu-id="e721a-163">Here's a code sample that demonstrates WebListener use outside of ASP.NET Core:</span></span>

```csharp
var settings = new WebListenerSettings();
settings.UrlPrefixes.Add("http://localhost:8080");

using (WebListener listener = new WebListener(settings))
{
    listener.Start();

    while (true)
    {
        var context = await listener.AcceptAsync();
        byte[] bytes = Encoding.ASCII.GetBytes("Hello World: " + DateTime.Now);
        context.Response.ContentLength = bytes.Length;
        context.Response.ContentType = "text/plain";

        await context.Response.Body.WriteAsync(bytes, 0, bytes.Length);
        context.Dispose();
    }
}
```

## <a name="preregister-url-prefixes-and-configure-ssl"></a><span data-ttu-id="e721a-164">预先注册 URL 前缀和配置 SSL</span><span class="sxs-lookup"><span data-stu-id="e721a-164">Preregister URL prefixes and configure SSL</span></span>

<span data-ttu-id="e721a-165">IIS 和 WebListener 依赖于基础 Http.Sys 内核模式驱动程序以侦听请求，并执行初始处理。</span><span class="sxs-lookup"><span data-stu-id="e721a-165">Both IIS and WebListener rely on the underlying Http.Sys kernel mode driver to listen for requests and do initial processing.</span></span> <span data-ttu-id="e721a-166">在 IIS 中，管理 UI 也提供相对简单的方式，来配置所有内容。</span><span class="sxs-lookup"><span data-stu-id="e721a-166">In IIS, the management UI gives you a relatively easy way to configure everything.</span></span> <span data-ttu-id="e721a-167">但是，如果你使用 WebListener 你需要自行配置 Http.Sys。</span><span class="sxs-lookup"><span data-stu-id="e721a-167">However, if you're using WebListener you need to configure Http.Sys yourself.</span></span> <span data-ttu-id="e721a-168">执行该操作的内置工具是 netsh.exe。</span><span class="sxs-lookup"><span data-stu-id="e721a-168">The built-in tool for doing that is netsh.exe.</span></span> 

<span data-ttu-id="e721a-169">你需要使用 netsh.exe 的最常见的任务正保留 URL 前缀和指派 SSL 证书。</span><span class="sxs-lookup"><span data-stu-id="e721a-169">The most common tasks you need to use netsh.exe for are reserving URL prefixes and assigning SSL certificates.</span></span>

<span data-ttu-id="e721a-170">NetSh.exe 不是使用为新手设计非常方便的工具。</span><span class="sxs-lookup"><span data-stu-id="e721a-170">NetSh.exe is not an easy tool to use for beginners.</span></span> <span data-ttu-id="e721a-171">下面的示例演示保留端口 80 和 443 的 URL 前缀所需的最低要求：</span><span class="sxs-lookup"><span data-stu-id="e721a-171">The following example shows the bare minimum needed to reserve URL prefixes for ports 80 and 443:</span></span>

```console
netsh http add urlacl url=http://+:80/ user=Users
netsh http add urlacl url=https://+:443/ user=Users
```

<span data-ttu-id="e721a-172">下面的示例演示如何将 SSL 证书分配：</span><span class="sxs-lookup"><span data-stu-id="e721a-172">The following example shows how to assign an SSL certificate:</span></span>

```console
netsh http add sslcert ipport=0.0.0.0:443 certhash=MyCertHash_Here appid={00000000-0000-0000-0000-000000000000}".
```

<span data-ttu-id="e721a-173">下面是官方的参考文档：</span><span class="sxs-lookup"><span data-stu-id="e721a-173">Here is the official reference documentation:</span></span>

* [<span data-ttu-id="e721a-174">Netsh 命令的超文本传输协议 (HTTP)</span><span class="sxs-lookup"><span data-stu-id="e721a-174">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](http://technet.microsoft.com/library/cc725882.aspx)
* [<span data-ttu-id="e721a-175">UrlPrefix 字符串</span><span class="sxs-lookup"><span data-stu-id="e721a-175">UrlPrefix Strings</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

<span data-ttu-id="e721a-176">以下资源提供了几个方案的详细的说明。</span><span class="sxs-lookup"><span data-stu-id="e721a-176">The following resources provide detailed instructions for several scenarios.</span></span> <span data-ttu-id="e721a-177">请参阅文章`HttpListener`同样适用于`WebListener`，如二者都基于 Http.Sys。</span><span class="sxs-lookup"><span data-stu-id="e721a-177">Articles that refer to `HttpListener` apply equally to `WebListener`, as both are based on Http.Sys.</span></span>

* [<span data-ttu-id="e721a-178">如何： 使用 SSL 证书配置端口</span><span class="sxs-lookup"><span data-stu-id="e721a-178">How to: Configure a Port with an SSL Certificate</span></span>](http://msdn.microsoft.com/library/ms733791.aspx)
* <span data-ttu-id="e721a-179">[HTTPS 通信-HttpListener 基于托管和客户端证书](http://sunshaking.blogspot.com/2012/11/https-communication-httplistener-based.html)这是一个第三方的博客和是相当旧但仍具有有用的信息。</span><span class="sxs-lookup"><span data-stu-id="e721a-179">[HTTPS Communication - HttpListener based Hosting and Client Certification](http://sunshaking.blogspot.com/2012/11/https-communication-httplistener-based.html) This is a third-party blog and is fairly old but still has useful information.</span></span>
* <span data-ttu-id="e721a-180">[如何： 演练使用 HttpListener 或 Http 服务器非托管代码 （c + +） 为 SSL 简单服务器](http://blogs.msdn.com/b/jpsanders/archive/2009/09/29/walkthrough-using-httplistener-as-an-ssl-simple-server.aspx)这也是有用的信息与较旧的博客。</span><span class="sxs-lookup"><span data-stu-id="e721a-180">[How To: Walkthrough Using HttpListener or Http Server unmanaged code (C++) as an SSL Simple Server](http://blogs.msdn.com/b/jpsanders/archive/2009/09/29/walkthrough-using-httplistener-as-an-ssl-simple-server.aspx) This too is an older blog with useful information.</span></span>
* [<span data-ttu-id="e721a-181">如何设置 SSL 与.NET 核心 WebListener？</span><span class="sxs-lookup"><span data-stu-id="e721a-181">How Do I Set Up A .NET Core WebListener With SSL?</span></span>](https://blogs.msdn.microsoft.com/timomta/2016/11/04/how-do-i-set-up-a-net-core-weblistener-with-ssl/)

<span data-ttu-id="e721a-182">以下是一些可以更方便地使用比 netsh.exe 命令行的第三方工具。</span><span class="sxs-lookup"><span data-stu-id="e721a-182">Here are some third-party tools that can be easier to use than the netsh.exe command line.</span></span> <span data-ttu-id="e721a-183">这些不提供的或由 Microsoft 认可。</span><span class="sxs-lookup"><span data-stu-id="e721a-183">These are not provided by or endorsed by Microsoft.</span></span> <span data-ttu-id="e721a-184">由于 netsh.exe 本身需要管理员权限时，默认情况下中, 以管理员身份运行工具。</span><span class="sxs-lookup"><span data-stu-id="e721a-184">The tools run as administrator by default, since netsh.exe itself requires administrator privileges.</span></span>

* <span data-ttu-id="e721a-185">[http.sys Manager](http://httpsysmanager.codeplex.com/)提供有关列表的 UI 和配置 SSL 证书和选项，前缀保留，和证书信任列表。</span><span class="sxs-lookup"><span data-stu-id="e721a-185">[http.sys Manager](http://httpsysmanager.codeplex.com/) provides UI for listing and configuring SSL certificates and options, prefix reservations, and certificate trust lists.</span></span> 
* <span data-ttu-id="e721a-186">[HttpConfig](http://www.stevestechspot.com/ABetterHttpcfg.aspx)允许列表或配置 SSL 证书和 URL 前缀。</span><span class="sxs-lookup"><span data-stu-id="e721a-186">[HttpConfig](http://www.stevestechspot.com/ABetterHttpcfg.aspx) lets you list or configure SSL certificates and URL prefixes.</span></span> <span data-ttu-id="e721a-187">UI 是更完善比 http.sys 管理器，并公开一些更多配置选项，但其他方面，它提供类似的功能。</span><span class="sxs-lookup"><span data-stu-id="e721a-187">The UI is more refined than http.sys Manager and exposes a few more configuration options, but otherwise it provides similar functionality.</span></span> <span data-ttu-id="e721a-188">它不能创建新的证书信任列表 (CTL)，但可以分配现有的。</span><span class="sxs-lookup"><span data-stu-id="e721a-188">It cannot create a new certificate trust list (CTL), but can assign existing ones.</span></span>

<span data-ttu-id="e721a-189">对于生成自签名的 SSL 证书，Microsoft 提供了命令行工具： [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968)和 PowerShell cmdlet[的 New-selfsignedcertificate](https://technet.microsoft.com/library/hh848633)。</span><span class="sxs-lookup"><span data-stu-id="e721a-189">For generating self-signed SSL certificates, Microsoft provides command-line tools: [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968) and the PowerShell cmdlet [New-SelfSignedCertificate](https://technet.microsoft.com/library/hh848633).</span></span> <span data-ttu-id="e721a-190">此外，还有第三方用户界面工具，可使你更轻松地生成自签名的 SSL 证书：</span><span class="sxs-lookup"><span data-stu-id="e721a-190">There are also third-party UI tools that make it easier for you to generate self-signed SSL certificates:</span></span>

* [<span data-ttu-id="e721a-191">SelfCert</span><span class="sxs-lookup"><span data-stu-id="e721a-191">SelfCert</span></span>](https://www.pluralsight.com/blog/software-development/selfcert-create-a-self-signed-certificate-interactively-gui-or-programmatically-in-net)
* [<span data-ttu-id="e721a-192">Makecert UI</span><span class="sxs-lookup"><span data-stu-id="e721a-192">Makecert UI</span></span>](http://makecertui.codeplex.com/)

## <a name="next-steps"></a><span data-ttu-id="e721a-193">后续步骤</span><span class="sxs-lookup"><span data-stu-id="e721a-193">Next steps</span></span>

<span data-ttu-id="e721a-194">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="e721a-194">For more information, see the following resources:</span></span>

* [<span data-ttu-id="e721a-195">本文的示例应用</span><span class="sxs-lookup"><span data-stu-id="e721a-195">Sample app for this article</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/weblistener/sample)
* [<span data-ttu-id="e721a-196">WebListener 源代码</span><span class="sxs-lookup"><span data-stu-id="e721a-196">WebListener source code</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="e721a-197">承载</span><span class="sxs-lookup"><span data-stu-id="e721a-197">Hosting</span></span>](../hosting.md)
