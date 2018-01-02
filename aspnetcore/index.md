---
title: "ASP.NET Core 简介"
author: rick-anderson
description: "提供 ASP.NET Core 简介。"
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 12/12/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: index
ms.openlocfilehash: 3a18ed30819a3d395e9bfb5dba0547667a4425e8
ms.sourcegitcommit: 198fb0488e961048bfa376cf58cb853ef1d1cb91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2017
---
# <a name="introduction-to-aspnet-core"></a>ASP.NET Core 简介

作者：[Daniel Roth](https://github.com/danroth27)[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Shaun Luttin](https://twitter.com/dicshaunary)

ASP.NET Core 是一个跨平台的高性能[开源](https://github.com/aspnet/home)框架，用于生成基于云且连接 Internet 的新式应用程序。 使用 ASP.NET Core，可以：

* 生成 Web 应用和服务、[IoT](https://www.microsoft.com/internet-of-things/) 应用和移动后端。
* 在 Windows、macOS 和 Linux 上使用喜爱的开发工具。
* 部署到云或本地。
* 在 [.NET Core 或 .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server) 上运行。

## <a name="why-use-aspnet-core"></a>为何使用 ASP.NET Core？

数百万开发人员使用过（并将继续使用）[ASP.NET 4.x](https://docs.microsoft.com/en-us/aspnet/overview) 创建 Web 应用。 ASP.NET Core 是重新设计的 ASP.NET 4.x，更改了体系结构，形成了更精简的模块化框架。

ASP.NET Core 具有如下优点：

* 生成 Web UI 和 Web API 的统一场景。
* 集成[新式客户端框架](xref:client-side/index)和开发工作流。
* 基于环境的云就绪[配置系统](xref:fundamentals/configuration/index)。
* 内置[依赖项注入](xref:fundamentals/dependency-injection)。
* 轻型的[高性能](https://github.com/aspnet/benchmarks)模块化 HTTP 请求管道。
* 能够在 [IIS](xref:publishing/iis)、[Nginx](xref:publishing/linuxproduction)、[Apache](xref:publishing/apache-proxy)、[Docker](xref:publishing/docker) 上进行托管或在自己的进程中进行自托管。
* 定目标到 [.NET Core](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server) 时，可以使用并行应用版本控制。
* 简化新式 Web 开发的工具。
* 能够在 Windows、macOS 和 Linux 进行生成和运行。
* 开放源代码和[以社区为中心](https://live.asp.net/)。

ASP.NET Core 完全作为 [NuGet](https://www.nuget.org/) 包的一部分提供。 这样一来，可以将应用优化为只包含必需 NuGet 包。 实际上，定目标到 .NET Core 的 ASP.NET Core 2.x 应用只需要使用[一个 NuGet 包](xref:fundamentals/metapackage)。 较小的应用图面区域的优势包括：提升安全性、减少维护和提高性能。

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>使用 ASP.NET Core MVC 生成 Web API 和 Web UI

ASP.NET Core MVC 提供生成 [Web API](xref:tutorials/index#building-web-apis) 和 [Web 应用](xref:tutorials/index#building-web-applications)所需的功能：

* [Model-View-Controller (MVC) 模式](xref:mvc/overview) 使 Web API 和 Web 应用[可测试](testing/index.md)。
* ASP.NET Core 2.0 中新增的 [Razor 页面](xref:mvc/razor-pages/index)是基于页面的编程模型，可简化 Web UI 生成并提高工作效率。
* [Razor 标记](xref:mvc/views/razor)提供了适用于 [Razor 页面](xref:mvc/razor-pages/index)和 [MVC 视图](xref:mvc/views/overview)的高效语法。
* [标记帮助程序](xref:mvc/views/tag-helpers/intro)使服务器端代码可以在 Razor 文件中参与创建和呈现 HTML 元素。
* 内置的[多数据格式和内容协商](mvc/models/formatting.md)支持使 Web API 可访问多种客户端，包括浏览器和移动设备。
* [模型绑定](xref:mvc/models/model-binding)自动将 HTTP 请求中的数据映射到操作方法参数。
* [模型验证](xref:mvc/models/validation)自动执行客户端和服务器端验证。

## <a name="client-side-development"></a>客户端开发

ASP.NET Core 与常用客户端框架和库（包括 [Angular](xref:spa/angular)、[React](xref:spa/react) 和 [Bootstrap](xref:client-side/bootstrap)）无缝集成。 有关详细信息，请参阅[客户端开发](xref:client-side/index)。

## <a name="next-steps"></a>后续步骤

有关更多信息，请参见以下资源：

* [ASP.NET Core 教程](xref:tutorials/index)
* [ASP.NET Core 基础知识](xref:fundamentals/index)
* [每周 ASP.NET Community Standup](https://live.asp.net/) 介绍了团队的工作进度和计划。 它以新博客和第三方软件为重点。
