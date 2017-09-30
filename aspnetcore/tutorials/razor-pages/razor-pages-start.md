---
title: "在 ASP.NET Core 中开始使用 Razor 页面"
author: rick-anderson
description: "在 ASP.NET Core 中开始使用 Razor 页面"
keywords: "ASP.NET Core, Razor 页面, Razor, MVC"
ms.author: riande
manager: wpickett
ms.date: 08/27/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 1d8d7805aafbf28fef044d09369a1dc76108b141
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2017
---
# <a name="getting-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="a9dd3-104">在 ASP.NET Core 中开始使用 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="a9dd3-104">Getting started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="a9dd3-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a9dd3-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a9dd3-106">本教程介绍构建 ASP.NET Core Razor 页面 Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="a9dd3-106">This tutorial teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span> <span data-ttu-id="a9dd3-107">我们建议在学习本教程前先阅读 [Razor 页面介绍](xref:mvc/razor-pages/index)。</span><span class="sxs-lookup"><span data-stu-id="a9dd3-107">We recommend you complete [Introduction to Razor Pages](xref:mvc/razor-pages/index) before starting this tutorial.</span></span> <span data-ttu-id="a9dd3-108">Razor 页面是在 ASP.NET Core 中为 Web 应用程序生成 UI 时建议使用的方法。</span><span class="sxs-lookup"><span data-stu-id="a9dd3-108">Razor Pages is the recommended way to build UI for web applications in ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a9dd3-109">先决条件</span><span class="sxs-lookup"><span data-stu-id="a9dd3-109">Prerequisites</span></span>

[!INCLUDE[install 2.0](../../includes/install2.0.md)]

## <a name="create-a-razor-web-app"></a><span data-ttu-id="a9dd3-110">创建 Razor Web 应用</span><span class="sxs-lookup"><span data-stu-id="a9dd3-110">Create a Razor web app</span></span>

* <span data-ttu-id="a9dd3-111">从 Visual Studio“文件”菜单中选择“新建” > “项目”。</span><span class="sxs-lookup"><span data-stu-id="a9dd3-111">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="a9dd3-112">创建新的 ASP.NET Core Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="a9dd3-112">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="a9dd3-113">将项目命名为“RazorPagesMovie”。</span><span class="sxs-lookup"><span data-stu-id="a9dd3-113">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="a9dd3-114">将项目命名为“RazorPagesMovie”，以便命名空间在你复制/粘贴代码时相互匹配。</span><span class="sxs-lookup"><span data-stu-id="a9dd3-114">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy/paste code.</span></span>
  <span data-ttu-id="a9dd3-115">![新建 ASP.NET Core Web 应用程序](../../mvc/razor-pages/index/_static/np.png)</span><span class="sxs-lookup"><span data-stu-id="a9dd3-115">![new ASP.NET Core Web Application](../../mvc/razor-pages/index/_static/np.png)</span></span>
* <span data-ttu-id="a9dd3-116">在下拉列表中选择“ASP.NET Core 2.0”，然后选择“Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="a9dd3-116">Select **ASP.NET Core 2.0** in the dropdown, and then select **Web Application**.</span></span>
  <span data-ttu-id="a9dd3-117">![Web 应用程序（Razor 页面）](../../mvc/razor-pages/index/_static/np2.png)</span><span class="sxs-lookup"><span data-stu-id="a9dd3-117">![Web Application (Razor Pages)](../../mvc/razor-pages/index/_static/np2.png)</span></span>

<span data-ttu-id="a9dd3-118">Visual Studio 模板创建初学者项目：</span><span class="sxs-lookup"><span data-stu-id="a9dd3-118">The Visual Studio template creates a starter project:</span></span>

![“解决方案资源管理器”](razor-pages-start/_static/se.png)

<span data-ttu-id="a9dd3-120">按 F5 在调试模式下运行应用，或按 Ctrl-F5 在运行（不附加调试器）</span><span class="sxs-lookup"><span data-stu-id="a9dd3-120">Press **F5** to run the app in debug mode or **Ctrl-F5** to run without attaching the debugger</span></span>

![主页或索引页](razor-pages-start/_static/home.png)

* <span data-ttu-id="a9dd3-122">Visual Studio 启动 [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行你的应用。</span><span class="sxs-lookup"><span data-stu-id="a9dd3-122">Visual Studio starts [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs your app.</span></span> <span data-ttu-id="a9dd3-123">地址栏显示 `localhost:port#`，而不显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="a9dd3-123">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a9dd3-124">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="a9dd3-124">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="a9dd3-125">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="a9dd3-125">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="a9dd3-126">Visual Studio 创建 Web 项目时，为 Web 服务器使用随机端口。</span><span class="sxs-lookup"><span data-stu-id="a9dd3-126">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="a9dd3-127">在上图中，端口号为 5000。</span><span class="sxs-lookup"><span data-stu-id="a9dd3-127">In the preceding image, the port number is 5000.</span></span> <span data-ttu-id="a9dd3-128">运行应用时，会看到不同的端口号。</span><span class="sxs-lookup"><span data-stu-id="a9dd3-128">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="a9dd3-129">使用“Ctrl+F5”启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="a9dd3-129">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="a9dd3-130">许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。</span><span class="sxs-lookup"><span data-stu-id="a9dd3-130">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

[!INCLUDE[razor-pages-start](../../includes/RP/razor-pages-start.md)]

>[!div class="step-by-step"]
[<span data-ttu-id="a9dd3-131">下一篇：添加模型</span><span class="sxs-lookup"><span data-stu-id="a9dd3-131">Next: Adding a model</span></span>](xref:tutorials/razor-pages/modelz)
