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
ms.openlocfilehash: f1abb3558546cd907c78b44d9353d9c9f1f5aff1
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="weblistener-web-server-implementation-in-aspnet-core"></a>在 ASP.NET Core WebListener web 服务器实现

通过[Tom Dykstra](https://github.com/tdykstra)和[Chris 跨](https://github.com/Tratcher)

> [!NOTE]
> 本主题仅适用于 ASP.NET Core 1.x。 在 ASP.NET 核心 2.0 中，名为 WebListener [HTTP.sys](httpsys.md)。

WebListener 是[ASP.NET Core 的 web 服务器](index.md)，仅在 Windows 上运行。 构建的[Http.Sys 内核模式驱动程序](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)。 WebListener 是一种替代方法[Kestrel](kestrel.md) ，可以直接连接到 Internet 使用，而不依赖于为反向代理服务器的 IIS。 事实上， **WebListener 不能与使用 IIS 或 IIS Express，因为它与不兼容[ASP.NET 核心模块](aspnet-core-module.md)。**

尽管 ASP.NET Core 开发的目标是 WebListener，它可直接在任何.NET 核心或.NET Framework 应用程序通过[Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) NuGet 包。

WebListener 支持以下功能：

- [Windows 身份验证](xref:security/authentication/windowsauth)
- 端口共享
- 具有 SNI 的 HTTPS
- HTTP/2 tls (Windows 10)
- 直接文件传输
- 响应缓存
- Websocket (Windows 8)

支持的 Windows 版本：

- Windows 7 和 Windows Server 2008 R2 及更高版本

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/weblistener/sample)（[如何下载](xref:tutorials/index#how-to-download-a-sample)）

## <a name="when-to-use-weblistener"></a>何时使用 WebListener

WebListener 可用于部署需要公开直接向 Internet 的服务器，而使用 IIS。

![Weblistener 直接与 Internet 进行通信](weblistener/_static/weblistener-to-internet.png)

由于它在 Http.Sys 上构建，WebListener 不需要反向代理服务器，为了针对攻击提供保护。 Http.Sys 是针对许多类型的攻击提供保护，并提供可靠性、 安全性和功能全面的 web 服务器的可伸缩性的成熟技术。 作为 HTTP 侦听器在 Http.Sys 运行 IIS 本身。 

WebListener 也是一个不错的选择为内部部署时你需要它提供你无法通过使用 Kestrel 获取的功能之一。

![Weblistener 直接与你的内部网络进行通信](weblistener/_static/weblistener-to-internal.png)

## <a name="how-to-use-weblistener"></a>如何使用 WebListener

下面是为主机操作系统和 ASP.NET Core 应用程序的安装程序任务的概述。

### <a name="configure-windows-server"></a>配置 Windows Server

* 安装版本的.NET 应用程序要求，如[.NET 核心](https://download.microsoft.com/download/0/A/3/0A372822-205D-4A86-BFA7-084D2CBE9EDF/DotNetCore.1.0.1-SDK.1.0.0.Preview2-003133-x64.exe)或.NET Framework 4.5.1。

* 预先注册 URL 前缀绑定到 WebListener，以及设置 SSL 证书

   如果你不预先注册 URL 前缀 Windows 中的，你必须使用管理员特权运行你的应用程序。 唯一的例外是如果你将绑定到使用端口号大于 1024; 使用 HTTP (而不是 HTTPS) 的 localhost在这种情况下无需使用管理员特权。

   有关详细信息，请参阅[如何预先注册前缀和配置 SSL](#preregister-url-prefixes-and-configure-ssl)本文后续部分中。

* 打开防火墙端口以允许流量抵达 WebListener。

   你可以使用 netsh.exe 或[PowerShell cmdlet](https://technet.microsoft.com/library/jj554906)。

也有[的 Http.Sys 注册表设置](https://support.microsoft.com/kb/820129)。

### <a name="configure-your-aspnet-core-application"></a>配置 ASP.NET Core 应用程序

* 安装 NuGet 程序包[Microsoft.AspNetCore.Server.WebListener](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.WebListener/)。 这也将安装[Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/)作为依赖项。

* 调用`UseWebListener`上的扩展方法[WebHostBuilder](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilder)中你`Main`方法，指定任何 WebListener[选项](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.AspNetCore.Server.WebListener/WebListenerOptions.cs)和[设置](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.Net.Http.Server/WebListenerSettings.cs)所需如下面的示例中所示：

  [!code-csharp[](weblistener/sample/Program.cs?name=snippet_Main&highlight=13-17)]

* 配置要侦听的 Url 和端口 

  默认情况下 ASP.NET Core 将绑定到`http://localhost:5000`。 若要配置 URL 前缀和端口，可以使用`UseURLs`扩展方法，`urls`命令行自变量或 ASP.NET 核心配置系统。 有关详细信息，请参阅[托管](../../fundamentals/hosting.md)。

  Web 侦听器使用[Http.Sys 前缀字符串格式](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)。 不没有特定于 WebListener 任何前缀字符串格式要求。

  > [!NOTE]
  > 请确保指定相同的前缀字符串中`UseUrls`，在服务器上预先注册。 

* 请确保你的应用程序未配置为运行 IIS 或 IIS Express。

  在 Visual Studio 中，默认启动配置文件适用于 IIS Express。  若要运行该项目作为控制台应用程序必须手动更改所选的配置文件，如下面的屏幕快照中所示。

  ![选择控制台应用程序配置文件](weblistener/_static/vs-choose-profile.png)

## <a name="how-to-use-weblistener-outside-of-aspnet-core"></a>如何使用 ASP.NET Core 之外的 WebListener

* 安装[Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) NuGet 包。

* [预先注册 URL 前缀绑定到 WebListener，以及设置 SSL 证书](#preregister-url-prefixes-and-configure-ssl)在 ASP.NET 核心中使用的一样。

也有[的 Http.Sys 注册表设置](https://support.microsoft.com/kb/820129)。


下面是演示 WebListener 使用 ASP.NET Core 之外的代码示例：

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

## <a name="preregister-url-prefixes-and-configure-ssl"></a>预先注册 URL 前缀和配置 SSL

IIS 和 WebListener 依赖于基础 Http.Sys 内核模式驱动程序以侦听请求，并执行初始处理。 在 IIS 中，管理 UI 也提供相对简单的方式，来配置所有内容。 但是，如果你使用 WebListener 你需要自行配置 Http.Sys。 执行该操作的内置工具是 netsh.exe。 

你需要使用 netsh.exe 的最常见的任务正保留 URL 前缀和指派 SSL 证书。

NetSh.exe 不是使用为新手设计非常方便的工具。 下面的示例演示保留端口 80 和 443 的 URL 前缀所需的最低要求：

```console
netsh http add urlacl url=http://+:80/ user=Users
netsh http add urlacl url=https://+:443/ user=Users
```

下面的示例演示如何将 SSL 证书分配：

```console
netsh http add sslcert ipport=0.0.0.0:443 certhash=MyCertHash_Here appid={00000000-0000-0000-0000-000000000000}".
```

下面是官方的参考文档：

* [Netsh 命令的超文本传输协议 (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)
* [UrlPrefix 字符串](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

以下资源提供了几个方案的详细的说明。 请参阅文章`HttpListener`同样适用于`WebListener`，如二者都基于 Http.Sys。

* [如何：使用 SSL 证书配置端口](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-configure-a-port-with-an-ssl-certificate)
* [HTTPS 通信-HttpListener 基于托管和客户端证书](http://sunshaking.blogspot.com/2012/11/https-communication-httplistener-based.html)这是一个第三方的博客和是相当旧但仍具有有用的信息。
* [如何： 演练使用 HttpListener 或 Http 服务器非托管代码 （c + +） 为 SSL 简单服务器](https://blogs.msdn.microsoft.com/jpsanders/2009/09/29/how-to-walkthrough-using-httplistener-or-http-server-unmanaged-code-c-as-an-ssl-simple-server/)这也是有用的信息与较旧的博客。
* [如何设置 SSL 与.NET 核心 WebListener？](https://blogs.msdn.microsoft.com/timomta/2016/11/04/how-do-i-set-up-a-net-core-weblistener-with-ssl/)

以下是一些可以更方便地使用比 netsh.exe 命令行的第三方工具。 这些不提供的或由 Microsoft 认可。 由于 netsh.exe 本身需要管理员权限时，默认情况下中, 以管理员身份运行工具。

* [http.sys Manager](http://httpsysmanager.codeplex.com/)提供有关列表的 UI 和配置 SSL 证书和选项，前缀保留，和证书信任列表。 
* [HttpConfig](http://www.stevestechspot.com/ABetterHttpcfg.aspx)允许列表或配置 SSL 证书和 URL 前缀。 UI 是更完善比 http.sys 管理器，并公开一些更多配置选项，但其他方面，它提供类似的功能。 它不能创建新的证书信任列表 (CTL)，但可以分配现有的。

对于生成自签名的 SSL 证书，Microsoft 提供了命令行工具： [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968)和 PowerShell cmdlet[的 New-selfsignedcertificate](https://technet.microsoft.com/itpro/powershell/windows/pki/new-selfsignedcertificate)。 此外，还有第三方用户界面工具，可使你更轻松地生成自签名的 SSL 证书：

* [SelfCert](https://www.pluralsight.com/blog/software-development/selfcert-create-a-self-signed-certificate-interactively-gui-or-programmatically-in-net)
* [Makecert UI](http://makecertui.codeplex.com/)

## <a name="next-steps"></a>后续步骤

有关更多信息，请参见以下资源：

* [本文的示例应用](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/weblistener/sample)
* [WebListener 源代码](https://github.com/aspnet/HttpSysServer/)
* [承载](../hosting.md)
