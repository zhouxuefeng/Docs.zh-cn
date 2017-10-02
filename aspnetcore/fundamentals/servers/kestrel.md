---
title: "在 ASP.NET Core kestrel web 服务器实现"
author: tdykstra
description: "ASP.NET 核心基于 libuv，引入了 Kestrel，跨平台 web 服务器。"
keywords: "ASP.NET 核心，Kestrel，libuv，url 前缀"
ms.author: tdykstra
manager: wpickett
ms.date: 08/02/2017
ms.topic: article
ms.assetid: 560bd66f-7dd0-4e68-b5fb-f31477e4b785
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/kestrel
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 451c36fc9095b6e076e5287c992b6163205c523b
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2017
---
# <a name="introduction-to-kestrel-web-server-implementation-in-aspnet-core"></a>在 ASP.NET Core Kestrel web 服务器实现简介

通过[Tom Dykstra](https://github.com/tdykstra)， [Chris 跨](https://github.com/Tratcher)，和[Stephen Halter](https://twitter.com/halter73)

Kestrel 是一个跨平台[ASP.NET Core 的 web 服务器](index.md)基于[libuv](https://github.com/libuv/libuv)，跨平台的异步 I/O 库。 Kestrel 是默认情况下，ASP.NET Core 项目模板中包含的 web 服务器。 

Kestrel 支持以下功能：

  * HTTPS
  * 用于启用的不透明升级[Websocket](https://github.com/aspnet/websockets)
  * 为了获得高性能后面 Nginx Unix 套接字 

Kestrel 支持所有平台和.NET 核心支持的版本。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[查看或下载 2.x 的示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/sample2)([如何下载](xref:tutorials/index#how-to-download-a-sample))

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[查看或下载 1.x 的示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/sample1)([如何下载](xref:tutorials/index#how-to-download-a-sample))

---

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>何时使用反向代理使用 Kestrel

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

可以单独使用 Kestrel，也可以将其与反向代理服务器（如 IIS、Nginx 或 Apache）结合使用。 反向代理服务器接收到来自 Internet 的 HTTP 请求，并在进行一些初步处理后将这些请求转发到 Kestrel。

![Kestrel 直接与 Internet 通信，不使用反向代理服务器](kestrel/_static/kestrel-to-internet2.png)

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

如果仅向内部网络公开 Kestrel，可以使用任一配置（无需考虑是否使用反向代理服务器）。

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

如果应用程序仅接受来自内部网络的请求，则可以单独使用 Kestrel。

![Kestrel 直接与你的内部网络进行通信](kestrel/_static/kestrel-to-internal.png)

如果将应用程序公开到 Internet，则必须将 IIS、Nginx 或 Apache 用作反向代理服务器。 反向代理服务器接收到来自 Internet 的 HTTP 请求，并在进行一些初步处理后将这些请求转发到 Kestrel。

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

反向代理是出于安全原因的边缘部署 （从 Internet 的流量到公开） 所必需的。 Kestrel 的 1.x 版本不包含对防御攻击的充分补充。 这包括但不限于适当超时、 大小限制和并发连接限制。

---

你有多个共享相同的 IP 和端口的单个服务器上运行的应用程序时需要反向代理的方案。 不能使用 Kestrel 直接因为 Kestrel 不支持共享相同的 IP 和多个进程之间的端口。 当配置 Kestrel 以在端口上侦听时，它将处理该端口而不考虑主机标头的所有流量。 可以共享端口的反向代理必须转发到 Kestrel 上唯一的 IP 和端口。

即使反向代理服务器不是必需的则使用其中一个可能是出于其他原因的一个不错的选择：

* 这样做可以防止你公开的外围应用。
* 它提供一个可选的额外的配置和安全层。
* 它可能与现有基础结构更好地集成。
* 它简化了负载平衡和 SSL 设置。 仅反向代理服务器需要 SSL 证书，并且该服务器可以与应用程序服务器内部网络上使用普通的 HTTP 通信。

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a>如何在 ASP.NET Core 应用中使用 Kestrel

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)包包含在[Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage)。

默认情况下，ASP.NET Core 项目模板使用 Kestrel。 在*Program.cs*，模板代码调用`CreateDefaultBuilder`，哪些调用[UseKestrel](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions#Microsoft_AspNetCore_Hosting_WebHostBuilderKestrelExtensions_UseKestrel_Microsoft_AspNetCore_Hosting_IWebHostBuilder_)在后台。

[!code-csharp[](kestrel/sample2/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

如果你需要配置 Kestrel 选项，则调用`UseKestrel`中*Program.cs*下面的示例中所示：

[!code-csharp[](kestrel/sample2/Program.cs?name=snippet_DefaultBuilder&highlight=9-16)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

安装[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) NuGet 包。

调用[UseKestrel](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions#Microsoft_AspNetCore_Hosting_WebHostBuilderKestrelExtensions_UseKestrel_Microsoft_AspNetCore_Hosting_IWebHostBuilder_)上的扩展方法`WebHostBuilder`中你`Main`方法，指定任何[Kestrel 选项](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.server.kestrel.kestrelserveroptions)，你将需要在下一部分中所示。

[!code-csharp[](kestrel/sample1/Program.cs?name=snippet_Main&highlight=13-19)]

---

### <a name="kestrel-options"></a>Kestrel 选项

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Kestrel web 服务器的约束配置选项，在面向 Internet 的部署中尤其有用。 下面是一些你可以设置的限制：

- 客户端最大连接数
- 请求正文最大大小
- 请求正文最小数据速率

你设置这些约束和中的其他人`Limits`属性[KestrelServerOptions](https://github.com/aspnet/KestrelHttpServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.Kestrel.Core/KestrelServerOptions.cs)类。 `Limits`属性包含的实例[KestrelServerLimits](https://github.com/aspnet/KestrelHttpServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.Kestrel.Core/KestrelServerLimits.cs)类。 

**最大客户端连接**

打开的并发 TCP 连接的最大数目可以设置整个应用程序替换为以下代码：

[!code-csharp[](kestrel/sample2/Program.cs?name=snippet_Limits&highlight=3-4)]

没有已从 HTTP 或 HTTPS 升级到另一个协议 （例如，在 Websocket 请求） 的连接的单独限制。 升级连接后，它不会根据计数`MaxConcurrentConnections`限制。 

最大连接数为默认情况下的不限 (null)。

**最大请求正文大小**

默认最大请求正文大小为 30,000,000 字节，这是大约 28.6 MB。 

重写中的 ASP.NET 核心 MVC 应用限制的建议的方法是使用[RequestSizeLimit](https://github.com/aspnet/Mvc/blob/rel/2.0.0/src/Microsoft.AspNetCore.Mvc.Core/RequestSizeLimitAttribute.cs)操作方法的特性：

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

下面是一个示例，演示如何配置整个应用程序，每个请求的约束：

[!code-csharp[](kestrel/sample2/Program.cs?name=snippet_Limits&highlight=5)]

您可以重写中中间件的特定请求上的设置：

[!code-csharp[](kestrel/sample2/Startup.cs?name=snippet_Limits&highlight=3-4)]
 
如果你尝试在请求配置限制应用程序已开始读取请求后，将引发异常。 没有`IsReadOnly`属性，告诉你如果`MaxRequestBodySize`属性处于只读状态，这意味着它太迟配置限制。

**最小的请求正文数据速率**

如果数据即将推出的功能在指定的速率，单位为字节/秒，kestrel 检查每隔一秒。 如果速率低于最小值，则会将连接操作已超时。宽限期是时间的 Kestrel 使客户端要增加到最小值; 其发送速率量在该时间段不检查速率。 宽限期有助于避免删除最初发送数据的比率较慢由于 TCP 缓慢启动的连接。

默认最小速度是 240 字节/秒、 5 秒宽限期。

最小速度也适用于响应。 要设置的请求限制和响应上限的代码是除了具有相同`RequestBody`或`Response`中的属性和接口名称。 

下面是一个示例演示如何配置中的最小数据速率*Program.cs*:

[!code-csharp[](kestrel/sample2/Program.cs?name=snippet_Limits&highlight=6-9)]

你可以在中间件中配置每个请求的速率：

[!code-csharp[](kestrel/sample2/Startup.cs?name=snippet_Limits&highlight=5-8)]

有关其他 Kestrel 选项的信息，请参阅以下类：

* [KestrelServerOptions](https://github.com/aspnet/KestrelHttpServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.Kestrel.Core/KestrelServerOptions.cs)
* [KestrelServerLimits](https://github.com/aspnet/KestrelHttpServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.Kestrel.Core/KestrelServerLimits.cs)
* [ListenOptions](https://github.com/aspnet/KestrelHttpServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.Kestrel.Core/ListenOptions.cs)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

有关 Kestrel 选项的信息，请参阅[KestrelServerOptions 类](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.server.kestrel.kestrelserveroptions)。

---

### <a name="endpoint-configuration"></a>终结点配置

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

默认情况下 ASP.NET Core 将绑定到`http://localhost:5000`。 配置 URL 前缀和 Kestrel 通过调用侦听的端口`Listen`或`ListenUnixSocket`方法`KestrelServerOptions`。 (`UseUrls`、`urls`命令行参数，并且还工作 ASPNETCORE_URLS 环境变量但还有一些记下限制[本文稍后的](#useurls-limitations)。)

**将绑定到 TCP 套接字**

`Listen`方法绑定到 TCP 套接字，和选项 lambda 允许你配置的 SSL 证书：

[!code-csharp[](kestrel/sample2/Program.cs?name=snippet_DefaultBuilder&highlight=9-16)]

请注意如何此示例将配置 SSL 特定终结点使用[ListenOptions](https://github.com/aspnet/KestrelHttpServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.Kestrel.Core/ListenOptions.cs)。 可以使用相同的 API 来配置其他 Kestrel 设置针对特定终结点。

[!INCLUDE[How to make an SSL cert](../../includes/make-ssl-cert.md)]

**将绑定到 Unix 套接字**

你可以侦听 Unix 套接字与 Nginx，提高了性能在此示例中所示：

[!code-csharp[](kestrel/sample2/Program.cs?name=snippet_UnixSocket)]

**端口 0**

如果指定端口号 0，Kestrel 动态将绑定到可用端口。 下面的示例演示如何确定 Kestrel 实际绑定到在运行时的端口：

[!code-csharp[](kestrel/sample2/Startup.cs?name=snippet_Configure&highlight=3,13,16-17)]

<a id="useurls-limitations"></a>

**UseUrls 限制**

你可以通过调用配置终结点`UseUrls`方法或使用`urls`命令行自变量或 ASPNETCORE_URLS 环境变量。 这些方法都很有用，如果你想要代码适用于 Kestrel 以外的服务器。 但是，请注意这些限制：

* 不能使用这些方法时使用 SSL。
* 如果你同时使用`Listen`方法和`UseUrls`、`Listen`终结点重写`UseUrls`终结点。

**IIS 的终结点配置**

如果你使用 IIS，IIS 的 URL 绑定重写通过调用设置的任何绑定`Listen`或`UseUrls`。 有关详细信息，请参阅[简介 ASP.NET 核心模块](aspnet-core-module.md)。

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

默认情况下 ASP.NET Core 将绑定到`http://localhost:5000`。 你可以配置 URL 前缀和 Kestrel 使用侦听的端口`UseUrls`扩展方法，`urls`命令行参数或 ASP.NET 核心配置系统。 有关这些方法的详细信息，请参阅[宿主](../../fundamentals/hosting.md)。 有关当你使用 IIS 的反向代理 URL 绑定的工作原理的信息，请参阅[ASP.NET 核心模块](aspnet-core-module.md)。 

---

### <a name="url-prefixes"></a>URL 前缀

如果调用`UseUrls`或使用`urls`命令行自变量或 ASPNETCORE_URLS 环境变量，URL 前缀可以处于任何以下格式。 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

HTTP URL 的前缀有效，则为Kestrel 不支持 SSL，当通过使用配置 URL 绑定`UseUrls`。

* 使用的端口号的 IPv4 地址

  ```
  http://65.55.39.10:80/
  ```

  0.0.0.0 是一种特殊情况，将绑定到所有 IPv4 地址。


* 使用的端口号的 IPv6 地址

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/ 
  ```

  [:] 等同 IPv6 的 IPv4 0.0.0.0。


* 主机名与端口号

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  主机名，*，和 + 不是特殊。 不是可识别的 IP 地址或"localhost"的任何内容将绑定到所有 IPv4 和 IPv6 Ip。 如果你需要将不同的主机名绑定到不同的 ASP.NET Core 应用程序相同的端口上，使用[HTTP.sys](httpsys.md)或如 IIS、 Nginx 或 Apache 的反向代理服务器。

* 使用端口号或环回 IP 与端口号的"Localhost"名称

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  当`localhost`Kestrel 尝试将绑定到 IPv4 和 IPv6 环回接口的指定。 如果请求的端口中使用任一环回接口上的其他服务，则 Kestrel 将无法启动。 如果任一环回接口而不可用任何其他原因 （通常是因为不支持 IPv6 的大多数，） Kestrel 记录一个警告。 

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

* 使用的端口号的 IPv4 地址

  ```
  http://65.55.39.10:80/
  https://65.55.39.10:443/
  ```

  0.0.0.0 是一种特殊情况，将绑定到所有 IPv4 地址。


* 使用的端口号的 IPv6 地址

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/ 
  https://[0:0:0:0:0:ffff:4137:270a]:443/ 
  ```

  [:] 等同 IPv6 的 IPv4 0.0.0.0。


* 主机名与端口号

  ```
  http://contoso.com:80/
  http://*:80/
  https://contoso.com:443/
  https://*:443/
  ```

  主机名， \*，并不特殊 +。 不是可识别的 IP 地址或"localhost"的任何内容将绑定到所有 IPv4 和 IPv6 Ip。 如果你需要将不同的主机名绑定到不同的 ASP.NET Core 应用程序相同的端口上，使用[WebListener](weblistener.md)或如 IIS、 Nginx 或 Apache 的反向代理服务器。

* 使用端口号或环回 IP 与端口号的"Localhost"名称

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  当`localhost`Kestrel 尝试将绑定到 IPv4 和 IPv6 环回接口的指定。 如果请求的端口中使用任一环回接口上的其他服务，则 Kestrel 将无法启动。 如果任一环回接口而不可用任何其他原因 （通常是因为不支持 IPv6 的大多数，） Kestrel 记录一个警告。 

* Unix 套接字

  ```
  http://unix:/run/dan-live.sock  
  ```

**端口 0**

如果指定端口号 0，Kestrel 动态将绑定到可用端口。 绑定到端口 0 允许任何主机名或 IP 除`localhost`名称。

下面的示例演示如何确定 Kestrel 实际绑定到在运行时的端口：

[!code-csharp[](kestrel/sample1/Startup.cs?name=snippet_Configure)]

**SSL 的 URL 前缀**

请务必包括使用的 URL 前缀`https:`如果调用`UseHttps`扩展方法，如下所示。

```csharp
var host = new WebHostBuilder() 
    .UseKestrel(options => 
    { 
        options.UseHttps("testCert.pfx", "testPassword"); 
    }) 
   .UseUrls("http://localhost:5000", "https://localhost:5001") 
   .UseContentRoot(Directory.GetCurrentDirectory()) 
   .UseStartup<Startup>() 
   .Build(); 
```

> [!NOTE]
> 不在相同端口上承载 HTTPS 和 HTTP。

[!INCLUDE[How to make an SSL cert](../../includes/make-ssl-cert.md)]

---
## <a name="next-steps"></a>后续步骤

有关更多信息，请参见以下资源：

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

* [示例应用程序 2.x](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/sample2)
* [Kestrel 源代码](https://github.com/aspnet/KestrelHttpServer)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

* [1.x 的示例应用](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/sample1)
* [Kestrel 源代码](https://github.com/aspnet/KestrelHttpServer)

---
