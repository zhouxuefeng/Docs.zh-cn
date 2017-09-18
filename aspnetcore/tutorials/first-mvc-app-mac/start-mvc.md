---
title: "ASP.NET Core MVC 和 Visual Studio for Mac 入门"
author: rick-anderson
description: "ASP.NET Core MVC 和 Visual Studio 入门"
keywords: ASP.NET Core, MVC, Visual Studio for Mac, Entity Framework
ms.author: riande
manager: wpickett
ms.date: 8/23/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app-mac/start-mvc
ms.openlocfilehash: 028d6d3246908a9cd44a6834449d2fdbc9cae0b8
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="getting-started-with-aspnet-core-mvc-and-visual-studio-for-mac"></a><span data-ttu-id="bfb0c-104">ASP.NET Core MVC 和 Visual Studio for Mac 入门</span><span class="sxs-lookup"><span data-stu-id="bfb0c-104">Getting started with ASP.NET Core MVC and Visual Studio for Mac</span></span>

<span data-ttu-id="bfb0c-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bfb0c-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bfb0c-106">本教程介绍使用 [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/) 生成 ASP.NET Core MVC Web 应用所涉及的基础知识。</span><span class="sxs-lookup"><span data-stu-id="bfb0c-106">This tutorial teaches you the basics of building an ASP.NET Core MVC web app using [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/).</span></span> [!INCLUDE[consider RP](../../includes/razor.md)]

<span data-ttu-id="bfb0c-107">本教程提供 3 个版本：</span><span class="sxs-lookup"><span data-stu-id="bfb0c-107">There are 3 versions of this tutorial:</span></span>

* <span data-ttu-id="bfb0c-108">macOS：[使用 Visual Studio for Mac 生成 ASP.NET Core MVC 应用](xref:tutorials/first-mvc-app-mac/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="bfb0c-108">macOS: [Build an ASP.NET Core MVC app with Visual Studio for Mac](xref:tutorials/first-mvc-app-mac/start-mvc)</span></span>
* <span data-ttu-id="bfb0c-109">Windows：[使用 Visual Studio 生成 ASP.NET Core MVC 应用](xref:tutorials/first-mvc-app/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="bfb0c-109">Windows: [Build an ASP.NET Core MVC app with Visual Studio](xref:tutorials/first-mvc-app/start-mvc)</span></span>
* <span data-ttu-id="bfb0c-110">Linux、macOS和 Windows：[使用 Visual Studio Code 生成 ASP.NET Core MVC 应用](xref:tutorials/first-mvc-app-xplat/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="bfb0c-110">Linux, macOS, and Windows: [Build an ASP.NET Core MVC app with Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bfb0c-111">先决条件</span><span class="sxs-lookup"><span data-stu-id="bfb0c-111">Prerequisites</span></span>

<span data-ttu-id="bfb0c-112">本教程需要 [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="bfb0c-112">This tutorial requires the [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) or later.</span></span> <span data-ttu-id="bfb0c-113">请参阅适用于 ASP.NET Core 1.1 版本的 [PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-mvc-app-mac/start-mvc/8-23-17.pdf)。</span><span class="sxs-lookup"><span data-stu-id="bfb0c-113">See [the pdf](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-mvc-app-mac/start-mvc/8-23-17.pdf) for the ASP.NET Core 1.1 version.</span></span>

<span data-ttu-id="bfb0c-114">安装以下组件：</span><span class="sxs-lookup"><span data-stu-id="bfb0c-114">Install the following:</span></span>

- <span data-ttu-id="bfb0c-115">[.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 或更高版本</span><span class="sxs-lookup"><span data-stu-id="bfb0c-115">[.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) or later</span></span>
- [<span data-ttu-id="bfb0c-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="bfb0c-116">Visual Studio for Mac</span></span>](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="create-a-web-app"></a><span data-ttu-id="bfb0c-117">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="bfb0c-117">Create a web app</span></span>

<span data-ttu-id="bfb0c-118">在 Visual Studio 中，选择“文件”>“新建解决方案”。</span><span class="sxs-lookup"><span data-stu-id="bfb0c-118">From Visual Studio, select **File > New Solution**.</span></span>

![macOS 新建解决方案](../first-web-api-mac/_static/sln.png)

<span data-ttu-id="bfb0c-120">选择“.NET Core App”>“ASP.NET Core”>“Web 应用”>“下一步”。</span><span class="sxs-lookup"><span data-stu-id="bfb0c-120">Select **.NET Core App >  ASP.NET Core > Web App > Next**.</span></span>

![macOS“新建项目”对话框](start-mvc/1.png)

<span data-ttu-id="bfb0c-122">将项目命名为“MvcMovie”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="bfb0c-122">Name the project **MvcMovie**, and then select **Create**.</span></span>

![macOS“新建项目”对话框](start-mvc/2.png)

### <a name="launch-the-app"></a><span data-ttu-id="bfb0c-124">启动应用</span><span class="sxs-lookup"><span data-stu-id="bfb0c-124">Launch the app</span></span>

<span data-ttu-id="bfb0c-125">在 Visual Studio 中，选择“运行”>“开始执行(不调试)”以启动应用。</span><span class="sxs-lookup"><span data-stu-id="bfb0c-125">In Visual Studio, select **Run > Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="bfb0c-126">Visual Studio 启动 [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview)，启动浏览器并导航到 `http://localhost:port`，其中的“端口”是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="bfb0c-126">Visual Studio starts [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview), launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

![具有新项目的浏览器](start-mvc/b1.png)

* <span data-ttu-id="bfb0c-128">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="bfb0c-128">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="bfb0c-129">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="bfb0c-129">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="bfb0c-130">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="bfb0c-130">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="bfb0c-131">运行应用时，将看到不同的端口号。</span><span class="sxs-lookup"><span data-stu-id="bfb0c-131">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="bfb0c-132">可以从“运行”菜单中以调试或非调试模式启动应用。</span><span class="sxs-lookup"><span data-stu-id="bfb0c-132">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="bfb0c-133">默认模板提供了“主页”、“关于”和“联系人”的链接。</span><span class="sxs-lookup"><span data-stu-id="bfb0c-133">The default template gives you **Home, About** and **Contact** links.</span></span> <span data-ttu-id="bfb0c-134">上面的浏览器图像未显示这些链接。</span><span class="sxs-lookup"><span data-stu-id="bfb0c-134">The browser image above doesn't show these links.</span></span> <span data-ttu-id="bfb0c-135">根据浏览器的大小，可能需要单击导航图标才能显示这些链接。</span><span class="sxs-lookup"><span data-stu-id="bfb0c-135">Depending on the size of your browser, you might need to click the navigation icon to show them.</span></span>

![具有新项目的浏览器](start-mvc/b2.png)

<span data-ttu-id="bfb0c-137">在本教程的下一部分中，你将了解 MVC 并开始撰写一些代码。</span><span class="sxs-lookup"><span data-stu-id="bfb0c-137">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

>[!div class="step-by-step"]
[<span data-ttu-id="bfb0c-138">下一篇</span><span class="sxs-lookup"><span data-stu-id="bfb0c-138">Next</span></span>](adding-controller.md)  
