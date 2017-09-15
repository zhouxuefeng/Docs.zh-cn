---
title: "在 ASP.NET 和 ASP.NET Core 之间进行选择"
author: rick-anderson
description: "如何在 ASP.NET 和 ASP.NET Core 之间进行选择。"
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/21/2017
ms.topic: article
ms.assetid: f0d17abf-3c69-413e-87fc-30780805e33f
ms.technology: aspnet
ms.prod: asp.net-core
msc.legacyurl: /learn
msc.type: content
ms.openlocfilehash: b9c8a54ceea3ff1c1c9bcff692c884761b17fd73
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="choose-between-aspnet-and-aspnet-core"></a><span data-ttu-id="bbda5-103">在 ASP.NET 和 ASP.NET Core 之间进行选择</span><span class="sxs-lookup"><span data-stu-id="bbda5-103">Choose between ASP.NET and ASP.NET Core</span></span> 

<span data-ttu-id="bbda5-104">不论在创建怎样的 Web 应用程序，ASP.NET 都可提供解决方案：从面向 Windows Server 的企业 Web 应用程序到面向 Linux 容器的小微服务，以及中间的所有应用程序。</span><span class="sxs-lookup"><span data-stu-id="bbda5-104">No matter the web application you are creating, ASP.NET has a solution for you: from enterprise web applications targeting Windows Server, to small microservices targeting Linux containers, and everything in between.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="bbda5-105">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bbda5-105">ASP.NET Core</span></span>

<span data-ttu-id="bbda5-106">ASP.NET Core 是一个跨平台的开源框架，用于在 Windows、macOS 或 Linux 上生成基于云的新式 Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="bbda5-106">ASP.NET Core is an open-source, cross-platform framework for building modern, cloud-based web applications on Windows, macOS, or Linux.</span></span>

## <a name="aspnet"></a><span data-ttu-id="bbda5-107">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="bbda5-107">ASP.NET</span></span>

<span data-ttu-id="bbda5-108">ASP.NET 是一个成熟的框架，提供在 Windows 上生成基于服务器的企业级 Web 应用程序所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="bbda5-108">ASP.NET is a mature framework that provides all the services needed to build enterprise-class, server-based web applications on Windows.</span></span>

## <a name="which-one-is-right-for-me"></a><span data-ttu-id="bbda5-109">哪一个适合我？</span><span class="sxs-lookup"><span data-stu-id="bbda5-109">Which one is right for me?</span></span>

| <span data-ttu-id="bbda5-110">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bbda5-110">ASP.NET Core</span></span> | <span data-ttu-id="bbda5-111">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="bbda5-111">ASP.NET</span></span> |
|---|---|
|<span data-ttu-id="bbda5-112">针对 Windows、macOS 或 Linux 进行生成</span><span class="sxs-lookup"><span data-stu-id="bbda5-112">Build for Windows, macOS, or Linux</span></span>|<span data-ttu-id="bbda5-113">针对 Windows 进行生成</span><span class="sxs-lookup"><span data-stu-id="bbda5-113">Build for Windows</span></span>|
|<span data-ttu-id="bbda5-114">[Razor 页面](xref:mvc/razor-pages/index) 是使用 ASP.NET Core 2.0 创建 Web UI 时建议使用的方法。</span><span class="sxs-lookup"><span data-stu-id="bbda5-114">[Razor Pages](xref:mvc/razor-pages/index) is the recommended approach to create a Web UI with ASP.NET Core 2.0.</span></span> <span data-ttu-id="bbda5-115">另请参阅 [MVC](xref:mvc/overview) 和 [Web API](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="bbda5-115">See also [MVC](xref:mvc/overview) and [Web API](xref:tutorials/first-web-api)</span></span>|<span data-ttu-id="bbda5-116">使用 [Web 窗体](https://docs.microsoft.com/aspnet/web-forms)、[SignalR](https://docs.microsoft.com/aspnet/signalr)、[MVC](https://docs.microsoft.com/aspnet/mvc)、[Web API](https://docs.microsoft.com/aspnet/web-api/) 或[网页](https://docs.microsoft.com/aspnet/web-pages)</span><span class="sxs-lookup"><span data-stu-id="bbda5-116">Use [Web Forms](https://docs.microsoft.com/aspnet/web-forms), [SignalR](https://docs.microsoft.com/aspnet/signalr), [MVC](https://docs.microsoft.com/aspnet/mvc), [Web API](https://docs.microsoft.com/aspnet/web-api/), or [Web Pages](https://docs.microsoft.com/aspnet/web-pages)</span></span>|
|<span data-ttu-id="bbda5-117">每个计算机多个版本</span><span class="sxs-lookup"><span data-stu-id="bbda5-117">Multiple versions per machine</span></span>|<span data-ttu-id="bbda5-118">每个计算机一个版本</span><span class="sxs-lookup"><span data-stu-id="bbda5-118">One version per machine</span></span>|
|<span data-ttu-id="bbda5-119">使用 C# 或 F# 通过 Visual Studio、[Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/) 或 [Visual Studio Code](https://code.visualstudio.com/) 进行开发</span><span class="sxs-lookup"><span data-stu-id="bbda5-119">Develop with Visual Studio, [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/), or [Visual Studio Code](https://code.visualstudio.com/) using C# or F#</span></span>|<span data-ttu-id="bbda5-120">使用 C#、VB 或 F# 通过 Visual Studio 进行开发</span><span class="sxs-lookup"><span data-stu-id="bbda5-120">Develop with Visual Studio using C#, VB, or F#</span></span>|
|<span data-ttu-id="bbda5-121">比 ASP.NET 性能更高</span><span class="sxs-lookup"><span data-stu-id="bbda5-121">Higher performance than ASP.NET</span></span>|<span data-ttu-id="bbda5-122">良好的性能</span><span class="sxs-lookup"><span data-stu-id="bbda5-122">Good performance</span></span>|
|[<span data-ttu-id="bbda5-123">选择 .NET Framework 或 .NET Core 运行时</span><span class="sxs-lookup"><span data-stu-id="bbda5-123">Choose .NET Framework or .NET Core runtime</span></span>](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server)|<span data-ttu-id="bbda5-124">使用 .NET Framework 运行时</span><span class="sxs-lookup"><span data-stu-id="bbda5-124">Use .NET Framework runtime</span></span>|

## <a name="aspnet-core-scenarios"></a><span data-ttu-id="bbda5-125">ASP.NET Core 方案</span><span class="sxs-lookup"><span data-stu-id="bbda5-125">ASP.NET Core scenarios</span></span>

<!-- update link to Razor Pages mvc movie series when done -->
* <span data-ttu-id="bbda5-126">[Razor 页面](xref:mvc/razor-pages/index) 是使用 ASP.NET Core 2.0 创建 Web UI 时建议使用的方法。</span><span class="sxs-lookup"><span data-stu-id="bbda5-126">[Razor Pages](xref:mvc/razor-pages/index) is the recommended approach to create a Web UI with ASP.NET Core 2.0.</span></span>
* [<span data-ttu-id="bbda5-127">网站</span><span class="sxs-lookup"><span data-stu-id="bbda5-127">Websites</span></span>](xref:tutorials/first-mvc-app/index)
* [<span data-ttu-id="bbda5-128">API</span><span class="sxs-lookup"><span data-stu-id="bbda5-128">APIs</span></span>](xref:tutorials/first-web-api)

## <a name="aspnet-scenarios"></a><span data-ttu-id="bbda5-129">ASP.NET 方案</span><span class="sxs-lookup"><span data-stu-id="bbda5-129">ASP.NET scenarios</span></span>

* [<span data-ttu-id="bbda5-130">网站</span><span class="sxs-lookup"><span data-stu-id="bbda5-130">Websites</span></span>](https://docs.microsoft.com/aspnet/mvc)
* [<span data-ttu-id="bbda5-131">API</span><span class="sxs-lookup"><span data-stu-id="bbda5-131">APIs</span></span>](https://docs.microsoft.com/aspnet/web-api)
* [<span data-ttu-id="bbda5-132">实时</span><span class="sxs-lookup"><span data-stu-id="bbda5-132">Real-time</span></span>](https://docs.microsoft.com/aspnet/signalr)

## <a name="resources"></a><span data-ttu-id="bbda5-133">资源</span><span class="sxs-lookup"><span data-stu-id="bbda5-133">Resources</span></span>

* [<span data-ttu-id="bbda5-134">ASP.NET 简介</span><span class="sxs-lookup"><span data-stu-id="bbda5-134">Introduction to ASP.NET</span></span>](https://docs.microsoft.com/aspnet/overview)
* [<span data-ttu-id="bbda5-135">ASP.NET Core 简介</span><span class="sxs-lookup"><span data-stu-id="bbda5-135">Introduction to ASP.NET Core</span></span>](xref:index)
