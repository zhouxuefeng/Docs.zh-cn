---
title: "利用 Visual Studio Code 在 ASP.NET Core 中开始使用 Razor 页面"
author: rick-anderson
description: "利用 Visual Studio Code 在 ASP.NET Core 中开始使用 Razor 页面"
keywords: "ASP.NET Core, Razor 页面, 搭建基架, Entity Framework Core, EF, EF Core, 数据库, mac, macOS, Visual Studio Code, Code"
ms.author: riande
manager: wpickett
ms.date: 08/27/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages-vsc/razor-pages-start
ms.openlocfilehash: 1b9dff14fa98314601fa44aa229aef6b73bb79d0
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2017
---
# <a name="getting-started-with-razor-pages-in-aspnet-core-with-visual-studio-code"></a><span data-ttu-id="d7af6-104">利用 Visual Studio Code 在 ASP.NET Core 中开始使用 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="d7af6-104">Getting started with Razor Pages in ASP.NET Core with Visual Studio Code</span></span>

<span data-ttu-id="d7af6-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d7af6-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d7af6-106">本教程介绍构建 ASP.NET Core Razor 页面 Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="d7af6-106">This tutorial teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span> <span data-ttu-id="d7af6-107">我们建议在学习本教程前先阅读 [Razor 页面介绍](xref:mvc/razor-pages/index)。</span><span class="sxs-lookup"><span data-stu-id="d7af6-107">We recommend you complete [Introduction to Razor Pages](xref:mvc/razor-pages/index) before starting this tutorial.</span></span> <span data-ttu-id="d7af6-108">Razor 页面是在 ASP.NET Core 中为 Web 应用程序生成 UI 时建议使用的方法。</span><span class="sxs-lookup"><span data-stu-id="d7af6-108">Razor Pages is the recommended way to build UI for web applications in ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d7af6-109">先决条件</span><span class="sxs-lookup"><span data-stu-id="d7af6-109">Prerequisites</span></span>

<span data-ttu-id="d7af6-110">安装以下组件：</span><span class="sxs-lookup"><span data-stu-id="d7af6-110">Install the following:</span></span>

* <span data-ttu-id="d7af6-111">[.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 或更高版本</span><span class="sxs-lookup"><span data-stu-id="d7af6-111">[.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) or later</span></span>
* [<span data-ttu-id="d7af6-112">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d7af6-112">Visual Studio Code</span></span>](https://code.visualstudio.com)
* <span data-ttu-id="d7af6-113">VS Code [C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)</span><span class="sxs-lookup"><span data-stu-id="d7af6-113">VS Code [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)</span></span> 

## <a name="create-a-razor-web-app"></a><span data-ttu-id="d7af6-114">创建 Razor Web 应用</span><span class="sxs-lookup"><span data-stu-id="d7af6-114">Create a Razor web app</span></span>

<span data-ttu-id="d7af6-115">从终端运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="d7af6-115">From a terminal, run the following commands:</span></span>

```console
dotnet new razor -o RazorPagesMovie
cd RazorPagesMovie
dotnet run
```

<span data-ttu-id="d7af6-116">上述命令使用 [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/dotnet) 创建并运行 Razor 页面项目。</span><span class="sxs-lookup"><span data-stu-id="d7af6-116">The preceding commands use the [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/dotnet) to create and run a Razor Pages project.</span></span> <span data-ttu-id="d7af6-117">打开浏览器，转到 http://localhost:5000 查看应用程序。</span><span class="sxs-lookup"><span data-stu-id="d7af6-117">Open a browser to http://localhost:5000 to view the application.</span></span>

![主页或索引页](../razor-pages/razor-pages-start/_static/home.png)

[!INCLUDE[razor-pages-start](../../includes/RP/razor-pages-start.md)]

## <a name="open-the-project"></a><span data-ttu-id="d7af6-119">打开项目</span><span class="sxs-lookup"><span data-stu-id="d7af6-119">Open the project</span></span>

<span data-ttu-id="d7af6-120">按 Ctrl+C 关闭应用程序。</span><span class="sxs-lookup"><span data-stu-id="d7af6-120">Press Ctrl+C to shut down the application.</span></span>

<span data-ttu-id="d7af6-121">在 Visual Studio Code (VS Code) 中，选择“文件”>“打开文件夹”，然后选择“RazorPagesMovie”文件夹。</span><span class="sxs-lookup"><span data-stu-id="d7af6-121">From Visual Studio Code (VS Code), select **File > Open Folder**, and then select the *RazorPagesMovie* folder.</span></span>

- <span data-ttu-id="d7af6-122">对警告消息“"RazorPagesMovie" 中缺少进行生成和调试所需的资产。是否添加它们?”选择“是”</span><span class="sxs-lookup"><span data-stu-id="d7af6-122">Select **Yes** to the **Warn** message "Required assets to build and debug are missing from 'RazorPagesMovie'.</span></span> <span data-ttu-id="d7af6-123">。</span><span class="sxs-lookup"><span data-stu-id="d7af6-123">Add them?"</span></span>
- <span data-ttu-id="d7af6-124">对信息性消息“存在未解析的依赖项”选择“还原”。</span><span class="sxs-lookup"><span data-stu-id="d7af6-124">Select **Restore** to the **Info** message "There are unresolved dependencies".</span></span>

### <a name="launch-the-app"></a><span data-ttu-id="d7af6-125">启动应用</span><span class="sxs-lookup"><span data-stu-id="d7af6-125">Launch the app</span></span>

<span data-ttu-id="d7af6-126">按 Ctrl+F5 启动应用而不进行调试。</span><span class="sxs-lookup"><span data-stu-id="d7af6-126">Press Ctrl+F5 to start the app without debugging.</span></span> <span data-ttu-id="d7af6-127">或者，从“调试”菜单中选择“开始执行(不调试)”。</span><span class="sxs-lookup"><span data-stu-id="d7af6-127">Alternatively, from the **Debug** menu, select **Start Without Debugging**.</span></span>

<span data-ttu-id="d7af6-128">在下一个教程中，我们将向项目添加模型。</span><span class="sxs-lookup"><span data-stu-id="d7af6-128">In the next tutorial, we add a model to the project.</span></span> 

>[!div class="step-by-step"]
[<span data-ttu-id="d7af6-129">下一篇：添加模型</span><span class="sxs-lookup"><span data-stu-id="d7af6-129">Next: Adding a model</span></span>](xref:tutorials/razor-pages-vsc/model)  
