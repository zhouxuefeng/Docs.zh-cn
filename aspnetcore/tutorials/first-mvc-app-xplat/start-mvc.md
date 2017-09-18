---
title: "Mac、Linux 或 Windows 上的 ASP.NET Core MVC 简介"
author: rick-anderson
description: "Mac、Linux 和 Windows 上的 ASP.NET Core MVC 和 Visual Studio Code 入门"
keywords: ASP.NET Core, MVC, VS Code, Visual Studio Code, Mac, Linux, Windows
ms.author: riande
manager: wpickett
ms.date: 07/07/2017
ms.topic: get-started-article
ms.assetid: 1d18b589-1638-4dc6-1638-fb0f41998d78
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app-xplat/start-mvc
ms.openlocfilehash: cfce91271ca21dd800fb68a14389606ce6d835f5
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="getting-started-with-aspnet-core-mvc--on-mac-linux-or-windows"></a><span data-ttu-id="6af05-104">Mac、Linux 或 Windows 上的 ASP.NET Core MVC 入门</span><span class="sxs-lookup"><span data-stu-id="6af05-104">Getting started with ASP.NET Core MVC  on Mac, Linux, or Windows</span></span>

<span data-ttu-id="6af05-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6af05-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6af05-106">本教程将介绍基础知识，指导你使用 [Visual Studio Code](https://code.visualstudio.com) (VS Code) 构建 ASP.NET Core MVC Web 应用。</span><span class="sxs-lookup"><span data-stu-id="6af05-106">This tutorial will teach you the basics of building an ASP.NET Core MVC web app using [Visual Studio Code](https://code.visualstudio.com) (VS Code).</span></span> <span data-ttu-id="6af05-107">本教程假定用户熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="6af05-107">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="6af05-108">有关详细信息，请参阅 [VS Code 入门](https://code.visualstudio.com/docs) 和 [Visual Studio Code 帮助](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="6af05-108">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span> [!INCLUDE[consider RP](../../includes/razor.md)]

<span data-ttu-id="6af05-109">本教程提供 3 个版本：</span><span class="sxs-lookup"><span data-stu-id="6af05-109">There are 3 versions of this tutorial:</span></span>

* <span data-ttu-id="6af05-110">macOS：[使用 Visual Studio for Mac 创建 ASP.NET Core MVC 应用](xref:tutorials/first-mvc-app-mac/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="6af05-110">macOS: [Create an ASP.NET Core MVC app with Visual Studio for Mac](xref:tutorials/first-mvc-app-mac/start-mvc)</span></span>
* <span data-ttu-id="6af05-111">Windows：[使用 Visual Studio 创建 ASP.NET Core MVC 应用](xref:tutorials/first-mvc-app/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="6af05-111">Windows: [Create an ASP.NET Core MVC app with Visual Studio](xref:tutorials/first-mvc-app/start-mvc)</span></span>
* <span data-ttu-id="6af05-112">macOS、Linux 和 Windows：[使用 Visual Studio Code 创建 ASP.NET Core MVC 应用](xref:tutorials/first-mvc-app-xplat/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="6af05-112">macOS, Linux, and Windows: [Create an ASP.NET Core MVC app with Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)</span></span> 

## <a name="install-vs-code-and-net-core"></a><span data-ttu-id="6af05-113">安装 VS Code 和 .NET Core</span><span class="sxs-lookup"><span data-stu-id="6af05-113">Install VS Code and .NET Core</span></span>

<span data-ttu-id="6af05-114">本教程需要 [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="6af05-114">This tutorial requires the [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) or later.</span></span> <span data-ttu-id="6af05-115">请参阅适用于 ASP.NET Core 1.1 版本的 [PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-mvc-app-mac/start-mvc/8-23-17.pdf)。</span><span class="sxs-lookup"><span data-stu-id="6af05-115">See [the pdf](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-mvc-app-mac/start-mvc/8-23-17.pdf) for the ASP.NET Core 1.1 version.</span></span>

<span data-ttu-id="6af05-116">安装以下组件：</span><span class="sxs-lookup"><span data-stu-id="6af05-116">Install the following:</span></span>

* <span data-ttu-id="6af05-117">[.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="6af05-117">[.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) or later.</span></span>
* [<span data-ttu-id="6af05-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6af05-118">Visual Studio Code</span></span>](https://code.visualstudio.com)
* <span data-ttu-id="6af05-119">VS Code [C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)</span><span class="sxs-lookup"><span data-stu-id="6af05-119">VS Code [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)</span></span> 

## <a name="create-a-web-app-with-dotnet"></a><span data-ttu-id="6af05-120">通过 dotnet 创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="6af05-120">Create a web app with dotnet</span></span>

<span data-ttu-id="6af05-121">从终端运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="6af05-121">From a terminal, run the following commands:</span></span>

```console
mkdir MvcMovie
cd MvcMovie
dotnet new mvc
```

<span data-ttu-id="6af05-122">在 Visual Studio Code (VS Code) 中打开“MvcMovie”文件夹并选择“Startup.cs”文件。</span><span class="sxs-lookup"><span data-stu-id="6af05-122">Open the *MvcMovie* folder in Visual Studio Code (VS Code) and select the *Startup.cs* file.</span></span>

- <span data-ttu-id="6af05-123">对于警告消息 -“"MvcMovie" 中缺少进行生成和调试所需的资产。是否添加它们?”，。</span><span class="sxs-lookup"><span data-stu-id="6af05-123">Select **Yes** to the **Warn** message "Required assets to build and debug are missing from 'MvcMovie'.</span></span> <span data-ttu-id="6af05-124">请选择“是”</span><span class="sxs-lookup"><span data-stu-id="6af05-124">Add them?"</span></span>
- <span data-ttu-id="6af05-125">对于信息性消息 -“存在未解析的依赖项”，请选择“还原”。</span><span class="sxs-lookup"><span data-stu-id="6af05-125">Select **Restore** to the **Info** message "There are unresolved dependencies".</span></span>

![带有“"MvcMovie" 中缺少进行生成和调试所需的资产。是否添加它们?”](../web-api-vsc/_static/vsc_restore.png)

<span data-ttu-id="6af05-129">按“调试”(F5) 生成并运行程序。</span><span class="sxs-lookup"><span data-stu-id="6af05-129">Press **Debug** (F5) to build and run the program.</span></span>

![正在运行的应用](../first-mvc-app/start-mvc/_static/1.png)

<span data-ttu-id="6af05-131">VS Code 启动 [Kestrel](xref:fundamentals/servers/kestrel) Web 服务器并运行应用。</span><span class="sxs-lookup"><span data-stu-id="6af05-131">VS Code starts the [Kestrel](xref:fundamentals/servers/kestrel) web server and runs your app.</span></span> <span data-ttu-id="6af05-132">请注意，地址栏显示 `localhost:5000` 而非 `example.com` 等内容。</span><span class="sxs-lookup"><span data-stu-id="6af05-132">Notice that the address bar shows `localhost:5000` and not something like `example.com`.</span></span> <span data-ttu-id="6af05-133">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="6af05-133">That's because `localhost` is the standard hostname for your local computer.</span></span>

<span data-ttu-id="6af05-134">默认模板提供可用的“主页”、“关于”和“联系”链接。</span><span class="sxs-lookup"><span data-stu-id="6af05-134">The default template gives you working **Home, About** and **Contact** links.</span></span> <span data-ttu-id="6af05-135">上面的浏览器图像未显示这些链接。</span><span class="sxs-lookup"><span data-stu-id="6af05-135">The browser image above doesn't show these links.</span></span> <span data-ttu-id="6af05-136">根据浏览器的大小，可能需要单击导航图标才能显示这些链接。</span><span class="sxs-lookup"><span data-stu-id="6af05-136">Depending on the size of your browser, you might need to click the navigation icon to show them.</span></span>

![右上角的导航图标](../first-mvc-app/start-mvc/_static/2.png)

<span data-ttu-id="6af05-138">在本教程的下一部分中，我们将了解 MVC 并开始编写一些代码。</span><span class="sxs-lookup"><span data-stu-id="6af05-138">In the next part of this tutorial, we'll learn about MVC and start writing some code.</span></span>

## <a name="visual-studio-code-help"></a><span data-ttu-id="6af05-139">Visual Studio Code 帮助</span><span class="sxs-lookup"><span data-stu-id="6af05-139">Visual Studio Code help</span></span>

- [<span data-ttu-id="6af05-140">入门</span><span class="sxs-lookup"><span data-stu-id="6af05-140">Getting started</span></span>](https://code.visualstudio.com/docs)
- [<span data-ttu-id="6af05-141">调试</span><span class="sxs-lookup"><span data-stu-id="6af05-141">Debugging</span></span>](https://code.visualstudio.com/docs/editor/debugging)
- [<span data-ttu-id="6af05-142">集成终端</span><span class="sxs-lookup"><span data-stu-id="6af05-142">Integrated terminal</span></span>](https://code.visualstudio.com/docs/editor/integrated-terminal)
- [<span data-ttu-id="6af05-143">键盘快捷键</span><span class="sxs-lookup"><span data-stu-id="6af05-143">Keyboard shortcuts</span></span>](https://code.visualstudio.com/docs/getstarted/keybindings#_keyboard-shortcuts-reference)

  - [<span data-ttu-id="6af05-144">Mac 键盘快捷键</span><span class="sxs-lookup"><span data-stu-id="6af05-144">Mac keyboard shortcuts</span></span>](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-macos.pdf)
  - [<span data-ttu-id="6af05-145">Linux 键盘快捷键</span><span class="sxs-lookup"><span data-stu-id="6af05-145">Linux keyboard shortcuts</span></span>](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-linux.pdf)
  - [<span data-ttu-id="6af05-146">Windows 键盘快捷键</span><span class="sxs-lookup"><span data-stu-id="6af05-146">Windows keyboard shortcuts</span></span>](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-windows.pdf)

>[!div class="step-by-step"]
[<span data-ttu-id="6af05-147">下一篇 - 添加控制器</span><span class="sxs-lookup"><span data-stu-id="6af05-147">Next - Add a controller</span></span>](adding-controller.md)
