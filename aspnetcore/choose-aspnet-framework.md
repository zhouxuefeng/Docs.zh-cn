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
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2017
---
# <a name="choose-between-aspnet-and-aspnet-core"></a><span data-ttu-id="82610-104">在 ASP.NET 和 ASP.NET Core 之间进行选择</span><span class="sxs-lookup"><span data-stu-id="82610-104">Choose between ASP.NET and ASP.NET Core</span></span> 

<span data-ttu-id="82610-105">不论在创建怎样的 Web 应用程序，ASP.NET 都可提供解决方案：从面向 Windows Server 的企业 Web 应用程序到面向 Linux 容器的小微服务，以及中间的所有应用程序。</span><span class="sxs-lookup"><span data-stu-id="82610-105">No matter the web application you are creating, ASP.NET has a solution for you: from enterprise web applications targeting Windows Server, to small microservices targeting Linux containers, and everything in between.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="82610-106">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="82610-106">ASP.NET Core</span></span>

<span data-ttu-id="82610-107">ASP.NET Core 是一个跨平台的开源框架，用于在 Windows、macOS 或 Linux 上生成基于云的新式 Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="82610-107">ASP.NET Core is an open-source, cross-platform framework for building modern, cloud-based web applications on Windows, macOS, or Linux.</span></span>

## <a name="aspnet"></a><span data-ttu-id="82610-108">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="82610-108">ASP.NET</span></span>

<span data-ttu-id="82610-109">ASP.NET 是一个成熟的框架，提供在 Windows 上生成基于服务器的企业级 Web 应用程序所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="82610-109">ASP.NET is a mature framework that provides all the services needed to build enterprise-class, server-based web applications on Windows.</span></span>

## <a name="which-one-is-right-for-me"></a><span data-ttu-id="82610-110">哪一个适合我？</span><span class="sxs-lookup"><span data-stu-id="82610-110">Which one is right for me?</span></span>

| <span data-ttu-id="82610-111">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="82610-111">ASP.NET Core</span></span> | <span data-ttu-id="82610-112">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="82610-112">ASP.NET</span></span> |
|---|---|
|<span data-ttu-id="82610-113">针对 Windows、macOS 或 Linux 进行生成</span><span class="sxs-lookup"><span data-stu-id="82610-113">Build for Windows, macOS, or Linux</span></span>|<span data-ttu-id="82610-114">针对 Windows 进行生成</span><span class="sxs-lookup"><span data-stu-id="82610-114">Build for Windows</span></span>|
|<span data-ttu-id="82610-115">[Razor 页面](xref:mvc/razor-pages/index) 是使用 ASP.NET Core 2.0 创建 Web UI 时建议使用的方法。</span><span class="sxs-lookup"><span data-stu-id="82610-115">[Razor Pages](xref:mvc/razor-pages/index) is the recommended approach to create a Web UI with ASP.NET Core 2.0.</span></span> <span data-ttu-id="82610-116">另请参阅 [MVC](xref:mvc/overview) 和 [Web API](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="82610-116">See also [MVC](xref:mvc/overview) and [Web API](xref:tutorials/first-web-api)</span></span>|<span data-ttu-id="82610-117">使用 [Web 窗体](https://docs.microsoft.com/aspnet/web-forms)、[SignalR](https://docs.microsoft.com/aspnet/signalr)、[MVC](https://docs.microsoft.com/aspnet/mvc)、[Web API](https://docs.microsoft.com/aspnet/web-api/) 或[网页](https://docs.microsoft.com/aspnet/web-pages)</span><span class="sxs-lookup"><span data-stu-id="82610-117">Use [Web Forms](https://docs.microsoft.com/aspnet/web-forms), [SignalR](https://docs.microsoft.com/aspnet/signalr), [MVC](https://docs.microsoft.com/aspnet/mvc), [Web API](https://docs.microsoft.com/aspnet/web-api/), or [Web Pages](https://docs.microsoft.com/aspnet/web-pages)</span></span>|
|<span data-ttu-id="82610-118">每个计算机多个版本</span><span class="sxs-lookup"><span data-stu-id="82610-118">Multiple versions per machine</span></span>|<span data-ttu-id="82610-119">每个计算机一个版本</span><span class="sxs-lookup"><span data-stu-id="82610-119">One version per machine</span></span>|
|<span data-ttu-id="82610-120">使用 C# 或 F# 通过 Visual Studio、[Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/) 或 [Visual Studio Code](https://code.visualstudio.com/) 进行开发</span><span class="sxs-lookup"><span data-stu-id="82610-120">Develop with Visual Studio, [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/), or [Visual Studio Code](https://code.visualstudio.com/) using C# or F#</span></span>|<span data-ttu-id="82610-121">使用 C#、VB 或 F# 通过 Visual Studio 进行开发</span><span class="sxs-lookup"><span data-stu-id="82610-121">Develop with Visual Studio using C#, VB, or F#</span></span>|
|<span data-ttu-id="82610-122">比 ASP.NET 性能更高</span><span class="sxs-lookup"><span data-stu-id="82610-122">Higher performance than ASP.NET</span></span>|<span data-ttu-id="82610-123">良好的性能</span><span class="sxs-lookup"><span data-stu-id="82610-123">Good performance</span></span>|
|[<span data-ttu-id="82610-124">选择 .NET Framework 或 .NET Core 运行时</span><span class="sxs-lookup"><span data-stu-id="82610-124">Choose .NET Framework or .NET Core runtime</span></span>](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server)|<span data-ttu-id="82610-125">使用 .NET Framework 运行时</span><span class="sxs-lookup"><span data-stu-id="82610-125">Use .NET Framework runtime</span></span>|

## <a name="aspnet-core-scenarios"></a><span data-ttu-id="82610-126">ASP.NET Core 方案</span><span class="sxs-lookup"><span data-stu-id="82610-126">ASP.NET Core scenarios</span></span>

<!-- update link to Razor Pages mvc movie series when done -->
* <span data-ttu-id="82610-127">[Razor 页面](xref:mvc/razor-pages/index) 是使用 ASP.NET Core 2.0 创建 Web UI 时建议使用的方法。</span><span class="sxs-lookup"><span data-stu-id="82610-127">[Razor Pages](xref:mvc/razor-pages/index) is the recommended approach to create a Web UI with ASP.NET Core 2.0.</span></span>
* [<span data-ttu-id="82610-128">网站</span><span class="sxs-lookup"><span data-stu-id="82610-128">Websites</span></span>](xref:tutorials/first-mvc-app/index)
* [<span data-ttu-id="82610-129">API</span><span class="sxs-lookup"><span data-stu-id="82610-129">APIs</span></span>](xref:tutorials/first-web-api)

## <a name="aspnet-scenarios"></a><span data-ttu-id="82610-130">ASP.NET 方案</span><span class="sxs-lookup"><span data-stu-id="82610-130">ASP.NET scenarios</span></span>

* [<span data-ttu-id="82610-131">网站</span><span class="sxs-lookup"><span data-stu-id="82610-131">Websites</span></span>](https://docs.microsoft.com/aspnet/mvc)
* [<span data-ttu-id="82610-132">API</span><span class="sxs-lookup"><span data-stu-id="82610-132">APIs</span></span>](https://docs.microsoft.com/aspnet/web-api)
* [<span data-ttu-id="82610-133">实时</span><span class="sxs-lookup"><span data-stu-id="82610-133">Real-time</span></span>](https://docs.microsoft.com/aspnet/signalr)

## <a name="resources"></a><span data-ttu-id="82610-134">资源</span><span class="sxs-lookup"><span data-stu-id="82610-134">Resources</span></span>

* [<span data-ttu-id="82610-135">ASP.NET 简介</span><span class="sxs-lookup"><span data-stu-id="82610-135">Introduction to ASP.NET</span></span>](https://docs.microsoft.com/aspnet/overview)
* [<span data-ttu-id="82610-136">ASP.NET Core 简介</span><span class="sxs-lookup"><span data-stu-id="82610-136">Introduction to ASP.NET Core</span></span>](xref:index)
