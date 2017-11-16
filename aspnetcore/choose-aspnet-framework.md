---
title: "在 ASP.NET 和 ASP.NET Core 之间进行选择"
author: rick-anderson
description: "了解如何在 ASP.NET 和 ASP.NET Core 之间进行选择。"
keywords: "ASP.NET Core, 基础知识, 概述"
ms.author: riande
manager: wpickett
ms.date: 09/30/2017
ms.topic: article
ms.assetid: f0d17abf-3c69-413e-87fc-30780805e33f
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 875064bd3437acc4e2a53220e19e86431d8c159b
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="choose-between-aspnet-and-aspnet-core"></a>在 ASP.NET 和 ASP.NET Core 之间进行选择 

不论在创建怎样的 Web 应用程序，ASP.NET 都可提供解决方案：从面向 Windows Server 的企业 Web 应用程序到面向 Linux 容器的小微服务，以及中间的所有应用程序。

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core 是一个跨平台的开源框架，用于在 Windows、macOS 或 Linux 上生成基于云的新式 Web 应用程序。

## <a name="aspnet"></a>ASP.NET

ASP.NET 是一个成熟的框架，提供在 Windows 上生成基于服务器的企业级 Web 应用程序所需的所有服务。

## <a name="which-one-is-right-for-me"></a>哪一个适合我？

| ASP.NET Core | ASP.NET |
|---|---|
|针对 Windows、macOS 或 Linux 进行生成|针对 Windows 进行生成|
|[Razor 页面](xref:mvc/razor-pages/index) 是使用 ASP.NET Core 2.0 创建 Web UI 时建议使用的方法。 另请参阅 [MVC](xref:mvc/overview) 和 [Web API](xref:tutorials/first-web-api)|使用 [Web 窗体](https://docs.microsoft.com/aspnet/web-forms)、[SignalR](https://docs.microsoft.com/aspnet/signalr)、[MVC](https://docs.microsoft.com/aspnet/mvc)、[Web API](https://docs.microsoft.com/aspnet/web-api/) 或[网页](https://docs.microsoft.com/aspnet/web-pages)|
|每个计算机多个版本|每个计算机一个版本|
|使用 C# 或 F# 通过 Visual Studio、[Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/) 或 [Visual Studio Code](https://code.visualstudio.com/) 进行开发|使用 C#、VB 或 F# 通过 Visual Studio 进行开发|
|比 ASP.NET 性能更高|良好的性能|
|[选择 .NET Framework 或 .NET Core 运行时](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server)|使用 .NET Framework 运行时|

## <a name="aspnet-core-scenarios"></a>ASP.NET Core 方案

<!-- update link to Razor Pages mvc movie series when done -->
* [Razor 页面](xref:mvc/razor-pages/index) 是使用 ASP.NET Core 2.0 创建 Web UI 时建议使用的方法。
* [网站](xref:tutorials/first-mvc-app/index)
* [API](xref:tutorials/first-web-api)

## <a name="aspnet-scenarios"></a>ASP.NET 方案

* [网站](https://docs.microsoft.com/aspnet/mvc)
* [API](https://docs.microsoft.com/aspnet/web-api)
* [实时](https://docs.microsoft.com/aspnet/signalr)

## <a name="resources"></a>资源

* [ASP.NET 简介](https://docs.microsoft.com/aspnet/overview)
* [ASP.NET Core 简介](xref:index)
