---
title: "在 ASP.NET Core HTTP.sys web 服务器实现"
author: rick-anderson
description: "引入了 HTTP.sys，ASP.NET 核心 Windows 上的 web 服务器。 基于 Http.Sys 内核模式驱动程序，HTTP.sys 是可以用于直接连接到 Internet，不含 IIS 的 Kestrel 的替代方法。"
keywords: "ASP.NET Core,HttpSys,HTTP.sys,HttpListener,url 前缀 SSL"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: article
ms.assetid: 0a7286e4-6428-424e-b5c4-5c98815cf61c
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 8d46862af44379d8592efdf214a80214dce2d69d
ms.sourcegitcommit: 198fb0488e961048bfa376cf58cb853ef1d1cb91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2017
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a>在 ASP.NET Core HTTP.sys web 服务器实现

通过[Tom Dykstra](https://github.com/tdykstra)和[Chris 跨](https://github.com/Tratcher)

> [!NOTE]
> 本主题仅适用于 ASP.NET 核心 2.0 和更高版本。 在早期版本的 ASP.NET 核心，HTTP.sys 为[WebListener](xref:fundamentals/servers/weblistener)。

HTTP.sys 是[ASP.NET Core 的 web 服务器](index.md)，仅在 Windows 上运行。 构建的[Http.Sys 内核模式驱动程序](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)。 HTTP.sys 是一种替代方法[Kestrel](kestrel.md) ，提供了 Kestel 不一些功能。 **HTTP.sys 不能与使用 IIS 或 IIS Express，因为它是与不兼容[ASP.NET 核心模块](aspnet-core-module.md)。**

HTTP.sys 支持以下功能：

- [Windows 身份验证](xref:security/authentication/windowsauth)
- 端口共享
- 具有 SNI 的 HTTPS
- HTTP/2 tls (Windows 10)
- 直接文件传输
- 响应缓存
- Websocket (Windows 8)

支持的 Windows 版本：

- Windows 7 和 Windows Server 2008 R2 及更高版本

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample)（[如何下载](xref:tutorials/index#how-to-download-a-sample)）

## <a name="when-to-use-httpsys"></a>何时使用 HTTP.sys

HTTP.sys 是用于部署你需要公开直接向 Internet 的服务器，而使用 IIS 的位置。

![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

由于它在 Http.Sys 上构建，HTTP.sys 不需要反向代理服务器，为了针对攻击提供保护。 Http.Sys 是针对许多类型的攻击提供保护，并提供可靠性、 安全性和功能全面的 web 服务器的可伸缩性的成熟技术。 作为 HTTP 侦听器在 Http.Sys 运行 IIS 本身。 

HTTP.sys 时需要的功能中 Kestrel，例如 Windows 身份验证不可用，将为内部部署一个不错的选择。

![HTTP.sys 直接与你的内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

## <a name="how-to-use-httpsys"></a>如何使用 HTTP.sys

下面是为主机操作系统和 ASP.NET Core 应用程序的安装程序任务的概述。

### <a name="configure-windows-server"></a>配置 Windows Server

* 安装版本的.NET 应用程序要求，如[.NET 核心](https://www.microsoft.com/net/download/core)或[.NET Framework](https://www.microsoft.com/net/download/framework)。

* 预先注册 URL 前缀绑定到 HTTP.sys，以及设置 SSL 证书

   如果你不预先注册 URL 前缀 Windows 中的，你必须使用管理员特权运行你的应用程序。 唯一的例外是如果你将绑定到使用端口号大于 1024; 使用 HTTP (而不是 HTTPS) 的 localhost在这种情况下，无需使用管理员特权。

   有关详细信息，请参阅[如何预先注册前缀和配置 SSL](#preregister-url-prefixes-and-configure-ssl)本文后续部分中。

* 打开防火墙端口以允许流量抵达 HTTP.sys。

   你可以使用*netsh.exe*或[PowerShell cmdlet](https://technet.microsoft.com/library/jj554906)。

也有[的 Http.Sys 注册表设置](https://support.microsoft.com/kb/820129)。

### <a name="configure-your-aspnet-core-application-to-use-httpsys"></a>配置 ASP.NET Core 应用程序，以使用 HTTP.sys

* 如果你使用没有包安装需要[Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage。 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)中 metapackage 包含包。

* 调用`UseHttpSys`上的扩展方法`WebHostBuilder`中你`Main`方法，指定任何[HTTP.sys 选项](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs)，你的需要如下面的示例中所示：

  [!code-csharp[](httpsys/sample/Program.cs?name=snippet_Main&highlight=11-19)]

### <a name="configure-httpsys-options"></a>配置 HTTP.sys 选项

以下是一些 HTTP.sys 设置和你可以配置的限制。

**最大客户端连接**

可以为整个应用程序替换为以下代码中设置最大并发打开 TCP 连接数*Program.cs*:

[!code-csharp[](httpsys/sample/Program.cs?name=snippet_Options&highlight=5)]

最大连接数为默认情况下的不限 (null)。

**最大请求正文大小**

默认最大请求正文大小为 30,000,000 字节，这是大约 28.6 MB。

重写中的 ASP.NET 核心 MVC 应用限制的建议的方法是使用[RequestSizeLimit](https://github.com/aspnet/Mvc/blob/rel/2.0.0/src/Microsoft.AspNetCore.Mvc.Core/RequestSizeLimitAttribute.cs)操作方法的特性：

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

下面是一个示例，演示如何配置整个应用程序，每个请求的约束：

[!code-csharp[](httpsys/sample/Program.cs?name=snippet_Options&highlight=6)]

你可以重写中的特定请求上的设置*Startup.cs*:

[!code-csharp[](httpsys/sample/Startup.cs?name=snippet_Configure&highlight=9-10)]
 
如果你尝试在请求配置限制应用程序已开始读取请求后，将引发异常。 没有`IsReadOnly`属性，告诉你如果`MaxRequestBodySize`属性处于只读状态，这意味着它太迟配置限制。

有关其他 HTTP.sys 选项的信息，请参阅[HttpSysOptions](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs)。 

### <a name="configure-urls-and-ports-to-listen-on"></a>配置要侦听的 Url 和端口 

默认情况下 ASP.NET Core 将绑定到`http://localhost:5000`。 若要配置 URL 前缀和端口，可以使用`UseUrls`扩展方法，`urls`命令行参数时，ASPNETCORE_URLS 环境变量，或`UrlPrefixes`属性[HttpSysOptions](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs)。 下面的代码示例使用`UrlPrefixes`。

[!code-csharp[](httpsys/sample/Program.cs?name=snippet_Main&highlight=17)]

一个优点`UrlPrefixes`是如果你尝试添加的格式不正确的前缀立即收到一条错误消息。 一个优点`UseUrls`(与共享`urls`和 ASPNETCORE_URLS) 是您可以更轻松地切换 Kestrel 和 HTTP.sys 之间。

如果你同时使用`UseUrls`(或`urls`或 ASPNETCORE_URLS) 和`UrlPrefixes`中的设置`UrlPrefixes`重写中的`UseUrls`。 有关详细信息，请参阅[托管](xref:fundamentals/hosting)。

HTTP.sys 使用[HTTP 服务器 API UrlPrefix 字符串格式](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)。

> [!NOTE]
> 请确保指定相同的前缀字符串中`UseUrls`或`UrlPrefixes`，在服务器上预先注册。 

### <a name="dont-use-iis"></a>不使用 IIS

请确保你的应用程序未配置为运行 IIS 或 IIS Express。

在 Visual Studio 中，默认启动配置文件适用于 IIS Express。 若要运行项目作为控制台应用程序，手动更改所选的配置文件，如下面的屏幕快照中所示。

![选择控制台应用程序配置文件](httpsys/_static/vs-choose-profile.png)

## <a name="preregister-url-prefixes-and-configure-ssl"></a>预先注册 URL 前缀和配置 SSL

IIS 和 HTTP.sys 依赖于基础 Http.Sys 内核模式驱动程序以侦听请求，并执行初始处理。 在 IIS 中，管理 UI 也提供相对简单的方式，来配置所有内容。 但是，你需要自行配置 Http.Sys。 执行操作，它是内置工具*netsh.exe*。 

与*netsh.exe*可以保留 URL 前缀，还可以将 SSL 证书分配。 该工具需要管理权限。

下面的示例演示保留端口 80 和 443 的 URL 前缀所需的最低要求：

```console
netsh http add urlacl url=http://+:80/ user=Users
netsh http add urlacl url=https://+:443/ user=Users
```

下面的示例演示如何将 SSL 证书分配：

```console
netsh http add sslcert ipport=0.0.0.0:443 certhash=MyCertHash_Here appid={00000000-0000-0000-0000-000000000000}"
```

下面是的参考文档*netsh.exe*:

* [Netsh 命令的超文本传输协议 (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)
* [UrlPrefix 字符串](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

以下资源提供了几个方案的详细的说明。 请参阅 HttpListener 的文章也同样适用于 HTTP.sys，因为二者都基于 Http.Sys。

* [如何：使用 SSL 证书配置端口](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-configure-a-port-with-an-ssl-certificate)
* [HTTPS 通信-HttpListener 基于托管和客户端证书](http://sunshaking.blogspot.com/2012/11/https-communication-httplistener-based.html)这是一个第三方的博客和是相当旧但仍具有有用的信息。
* [如何： 演练使用 HttpListener 或 Http 服务器非托管代码 （c + +） 为 SSL 简单服务器](https://blogs.msdn.microsoft.com/jpsanders/2009/09/29/how-to-walkthrough-using-httplistener-or-http-server-unmanaged-code-c-as-an-ssl-simple-server/)这也是有用的信息与较旧的博客。

以下是一些可以比易于使用的第三方工具*netsh.exe*命令行。 这些不提供的或由 Microsoft 认可。 这些工具以管理员身份运行默认情况下，由于*netsh.exe*本身需要管理员特权。

* [http.sys Manager](http://httpsysmanager.codeplex.com/)提供有关列表的 UI 和配置 SSL 证书和选项，前缀保留，和证书信任列表。 
* [HttpConfig](http://www.stevestechspot.com/ABetterHttpcfg.aspx)允许列表或配置 SSL 证书和 URL 前缀。 UI 是更完善比 http.sys 管理器，并公开一些更多配置选项，但其他方面，它提供类似的功能。 它不能创建新的证书信任列表 (CTL)，但可以分配现有的。

[!INCLUDE[How to make an SSL cert](../../includes/make-ssl-cert.md)]

## <a name="next-steps"></a>后续步骤

有关更多信息，请参见以下资源：

* [本文的示例应用](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample)
* [HTTP.sys 源代码](https://github.com/aspnet/HttpSysServer/)
* [承载](xref:fundamentals/hosting)
