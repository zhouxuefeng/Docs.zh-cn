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
# <a name="introduction-to-aspnet-core-module"></a>ASP.NET 核心模块简介

通过[Tom Dykstra](http://github.com/tdykstra)， [Rick Strahl](https://github.com/RickStrahl)，和[Chris 跨](https://github.com/Tratcher) 

ASP.NET 核心模块 (ANCM) 允许您在 IIS 中，后面的应用程序中运行 ASP.NET 核心的很好地 （安全、 可管理性，还有很多） 使用 IIS 和使用[Kestrel](kestrel.md)为很好地 （在非常短），并获取从一次这两种技术的优势。 **ANCM 仅适用于 Kestrel;它与不兼容 WebListener (在 ASP.NET Core 1.x) 或 HTTP.sys （在 2.x)。** 

支持的 Windows 版本：

* Windows 7 和 Windows Server 2008 R2 及更高版本

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/aspnet-core-module/sample)

## <a name="what-aspnet-core-module-does"></a>ASP.NET 核心模块的用途

ANCM 是一个本机的 IIS 模块，挂钩到 IIS 管道和将流量重定向到后端 ASP.NET Core 应用程序。 大多数其他模块，例如 windows 身份验证，仍有机会运行。 ANCM 仅采用控制时为请求中，选择一个处理程序并在应用程序中定义处理程序映射*web.config*文件。

因为 ASP.NET Core 应用程序在进程中运行的 IIS 工作进程分开，ANCM 还未处理管理。 当第一个请求传入并且重新启动该崩溃时，ANCM 启动 ASP.NET Core 应用程序的过程。 这是实质上是相同经典 ASP.NET 应用程序的行为运行中进程在 IIS 中，并且由管理 WAS （Windows 激活服务）。

下面是阐释了 IIS、 ANCM 和 ASP.NET Core 应用程序之间的关系的关系图。

![ASP.NET 核心模块](aspnet-core-module/_static/ancm.png)

请求来自 Web 和命中的内核模式 Http.Sys 驱动程序，将其路由到主端口 (80) 或 SSL 端口 (443) 上的 IIS。 ANCM 将请求转发到 ASP.NET 核心应用程序上不是端口 80/443 对应用程序配置的 HTTP 端口。

Kestrel 侦听来自 ANCM 的流量。  ANCM 指定通过在启动时，环境变量的端口和[UseIISIntegration](#call-useiisintegration)方法将服务器配置为侦听`http://localhost:{port}`。 有一些其他检查，以拒绝不是从 ANCM 的请求。 （ANCM 不支持 HTTPS 转发，因此即使 IIS 通过 HTTPS 接收到请求通过 HTTP 转发。）

Kestrel ANCM 从那里获取请求，并将它们推送到 ASP.NET 核心中间件管道，其处理它们，然后将它们作为传递`HttpContext`到应用程序逻辑的实例。 然后，应用程序的响应会传递回 IIS，它们回退到 HTTP 客户端启动请求的推送。

ANCM 具有少数几个其他函数：

* 设置环境变量。
* 日志`stdout`输出到文件存储。
* 将转发 Windows 身份验证令牌。

## <a name="how-to-use-ancm-in-aspnet-core-apps"></a>如何在 ASP.NET Core 应用中使用 ANCM

本部分概述了设置的 IIS 服务器和 ASP.NET Core 应用程序的过程。 有关详细说明，请参阅[发布到 IIS](../../publishing/iis.md)。

### <a name="install-ancm"></a>安装 ANCM

ASP.NET 核心模块必须安装在 IIS 服务器上和在 IIS Express 在开发计算机上。 对于服务器，纳入 ANCM [.NET 核心 Windows 服务器承载捆绑](https://aka.ms/dotnetcore.2.0.0-windowshosting)。 对于开发计算机，Visual Studio 会自动安装 ANCM 在 IIS Express 中，并在 IIS 中如果计算机上已安装。

### <a name="install-the-iisintegration-nuget-package"></a>安装 IISIntegration NuGet 包

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET 核心 2.x](#tab/aspnetcore2x)

[Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)包包含在 ASP.NET Core metapackages ([Microsoft.AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore/)和[Microsoft.AspNetCore.All](xref:fundamentals/metapackage)). 如果不使用 metapackages 之一时，安装`Microsoft.AspNetCore.Server.IISIntegration`单独。 `IISIntegration`包是读取广播 ANCM 设置你的应用程序通过环境变量的互操作性包。 环境变量提供配置信息，例如要侦听的端口。 

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET 核心 1.x](#tab/aspnetcore1x)

在你的应用程序，安装[Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)。 `IISIntegration`包是读取广播 ANCM 设置你的应用程序通过环境变量的互操作性包。 环境变量提供配置信息，例如要侦听的端口。 

---

### <a name="call-useiisintegration"></a>调用 UseIISIntegration

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET 核心 2.x](#tab/aspnetcore2x)

`UseIISIntegration`上的扩展方法[ `WebHostBuilder` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilder)使用 IIS 运行时自动调用。

如果你不使用 ASP.NET Core metapackages 之一，但尚未安装`Microsoft.AspNetCore.Server.IISIntegration`包，则会收到运行时错误。 如果调用`UseIISIntegration`显式，获取一个编译时错误，如果未安装此包。

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET 核心 1.x](#tab/aspnetcore1x)

应用程序中`Main`方法中，调用`UseIISIntegration`上的扩展方法[ `WebHostBuilder` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilder)。 

[!code-csharp[](aspnet-core-module/sample/Program.cs?name=snippet_Main&highlight=12)]

---

`UseIISIntegration`方法会查找环境变量 ANCM 设置，和它否 ops 如果未找到它们。 此行为帮助实现各种方案，例如开发和测试在 macOS 或 Linux 上和部署到运行 IIS 的服务器。 运行时在 macOS 或 Linux 上，Kestrel 充当 web 服务器;但是，当应用程序部署到 IIS 环境中，它会自动使用 ANCM 和 IIS。

### <a name="ancm-port-binding-overrides-other-port-bindings"></a>ANCM 端口绑定将替代其他端口绑定

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET 核心 2.x](#tab/aspnetcore2x)

ANCM 生成要分配给后端进程的动态端口。 `UseIISIntegration`方法拾取此动态端口，并配置要侦听的 Kestrel `http://locahost:{dynamicPort}/`。 此设置将替代其他 URL 配置，如调用到`UseUrls`或[Kestrel 的侦听 API](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration)。 因此，你不必调用`UseUrls`或 Kestrel 的`Listen`API 使用 ANCM 时。 如果调用`UseUrls`或`Listen`，Kestrel 在运行不含 IIS 应用程序时指定的端口上侦听。

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET 核心 1.x](#tab/aspnetcore1x)

ANCM 生成要分配给后端进程的动态端口。 `UseIISIntegration`方法拾取此动态端口，并配置要侦听的 Kestrel `http://locahost:{dynamicPort}/`。 此设置将替代其他 URL 配置，如调用到`UseUrls`。 因此，你不必调用`UseUrls`当你使用 ANCM。 如果调用`UseUrls`，Kestrel 在运行不含 IIS 应用程序时指定的端口上侦听。

在 ASP.NET 核心 1.0 中，如果调用`UseUrls`，称之为**之前**调用`UseIISIntegration`以便不会被覆盖，ANCM 配置端口。 此调用顺序不需要 ASP.NET 核心 1.1 中，因为 ANCM 设置将重写`UseUrls`。

---

### <a name="configure-ancm-options-in-webconfig"></a>在 Web.config 中配置 ANCM 选项

ASP.NET 核心模块的配置存储在*Web.config*位于应用程序的根文件夹中的文件。 此文件中的设置指向启动命令并启动 ASP.NET Core 应用的自变量。 有关 Web.config 的示例代码和配置选项的指南，请参阅[ASP.NET 核心模块配置参考](../../hosting/aspnet-core-module.md)。

### <a name="run-with-iis-express-in-development"></a>在开发过程中使用 IIS Express 运行

可以通过使用 ASP.NET Core 模板定义的默认配置文件的 Visual Studio 启动 IIS Express。

## <a name="next-steps"></a>后续步骤

有关更多信息，请参见以下资源：

* [本文的示例应用](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/aspnet-core-module/sample)
* [ASP.NET 核心模块的源代码](https://github.com/aspnet/AspNetCoreModule)
* [ASP.NET 核心模块配置参考](../../hosting/aspnet-core-module.md)
* [发布到 IIS](../../publishing/iis.md)
