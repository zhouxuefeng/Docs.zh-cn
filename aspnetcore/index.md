---
title: "ASP.NET Core 简介"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 08/03/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: index
ms.openlocfilehash: 10831719630bc638ce2f7424f53548518868d433
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="introduction-to-aspnet-core"></a>ASP.NET Core 简介

作者：[Daniel Roth](https://github.com/danroth27)[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Shaun Luttin](https://twitter.com/dicshaunary)

ASP.NET Core 是一个跨平台的高性能[开源](https://github.com/aspnet/home)框架，用于生成基于云且连接 Internet 的新式应用程序。 使用 ASP.NET Core，可以：

* 生成 Web 应用和服务、IoT 应用和移动后端。
* 在 Windows、macOS 和 Linux 上使用喜爱的开发工具。
* 部署到云或本地
* 在 [.NET Core 或 .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server) 上运行。

## <a name="why-use-aspnet-core"></a>为何使用 ASP.NET Core？

数百万开发人员在使用（并继续使用）ASP.NET 来创建 Web 应用。 ASP.NET Core 是重新设计的 ASP.NET，对体系结构进行了更改，提供更精简的模块化框架。

ASP.NET Core 具有如下优点：

* 生成 Web UI 和 Web API 的统一场景。
* [新式客户端框架](xref:client-side/index)与开发工作流的集成。
* 基于环境的云就绪[配置系统](xref:fundamentals/configuration)。
* 内置[依赖项注入](xref:fundamentals/dependency-injection)。
* 轻型高性能模块化 HTTP 请求管道。
* 能够在 IIS 上进行托管或在自己的进程中进行自托管。
* 可以在 [.NET Core](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server) 上运行，支持真正的并行应用版本控制。
* 简化新式 Web 开发的工具。
* 能够在 Windows、macOS 和 Linux 进行生成和运行。
* 开源和关注社区。

ASP.NET Core 完全作为 [NuGet](https://nuget.org) 包的一部分提供。 这可优化应用，使其只包含需要的 NuGet 包。 较小的应用图面区域的优势包括：提升安全性、减少维护和提高性能。

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>使用 ASP.NET Core MVC 生成 Web API 和 Web UI

ASP.NET Core MVC 提供帮助生成 [Web API](xref:tutorials/index#building-web-apis) 和 [Web 应用](xref:tutorials/index#building-web-applications)的功能：

* [Model-View-Controller (MVC) 模式](xref:mvc/overview) 使 Web API 和 Web 应用[可测试](testing/index.md)。
* [Razor 页面](xref:mvc/razor-pages/index)（2.0 中的新增功能）是基于页面的编程模式，它使 Web UI 的生成更加简单高效。
* [Razor 语法](xref:mvc/views/razor)为 [Razor 页面](xref:mvc/razor-pages/index)和 [MVC 视图](xref:mvc/views/overview)提供高效的语言。
* [标记帮助程序](xref:mvc/views/tag-helpers/intro)使服务器端代码可以在 Razor 文件中参与创建和呈现 HTML 元素。
* 内置的[多数据格式和内容协商](mvc/models/formatting.md)支持使 Web API 可访问多种客户端，包括浏览器和移动设备。
* [模型绑定](xref:mvc/models/model-binding)自动将数据从 HTTP 请求映射到操作方法参数。
* [模型验证](xref:mvc/models/validation)自动执行客户端和服务器端验证。

## <a name="client-side-development"></a>客户端开发

ASP.NET Core 经过精心设计，可与多种客户端框架无缝集成，包括 [AngularJS](xref:client-side/angular)、[KnockoutJS](xref:client-side/knockout) 和 [Bootstrap](xref:client-side/bootstrap)。 有关详细信息，请参阅[客户端开发](client-side/index.md)。

## <a name="next-steps"></a>后续步骤

有关更多信息，请参见以下资源：

* [ASP.NET Core 教程](xref:tutorials/index)
* [ASP.NET Core 基础知识](xref:fundamentals/index)
* [每周的 ASP.NET Community Standup](https://live.asp.net/) 介绍团队的进度和计划，包括新的博客和第三方软件。
