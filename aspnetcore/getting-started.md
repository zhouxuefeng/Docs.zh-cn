---
title: "ASP.NET Core 2.0 入门"
author: rick-anderson
description: "介绍如何使用 ASP.NET Core 创建并运行简单的 Hello World 应用的快速教程。"
keywords: "ASP.NET Core, 教程, 入门"
ms.author: riande
manager: wpickett
ms.date: 08/30/2017
ms.topic: get-started-article
ms.assetid: 73543e9d-d9d5-47d6-9664-17a9beea6cd3
ms.technology: aspnet
ms.prod: asp.net-core
uid: getting-started
ms.openlocfilehash: f7852f0dddb0585089f5ccd8f4c865f5b87b049b
ms.sourcegitcommit: fb518f856f31fe53c09196a13309eacb85b37a22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2017
---
# <a name="getting-started-with-aspnet-core"></a><span data-ttu-id="57190-104">ASP.NET Core 入门</span><span class="sxs-lookup"><span data-stu-id="57190-104">Getting Started with ASP.NET Core</span></span>

> [!NOTE]
> <span data-ttu-id="57190-105">这些说明适用于 ASP.NET Core 的最新版本。</span><span class="sxs-lookup"><span data-stu-id="57190-105">These instructions are for the latest version of ASP.NET Core.</span></span> <span data-ttu-id="57190-106">想要从早期版本开始？</span><span class="sxs-lookup"><span data-stu-id="57190-106">Looking to get started with an earlier version?</span></span> <span data-ttu-id="57190-107">请参阅[本教程的 1.1 版本](xref:getting-started-1.1)。</span><span class="sxs-lookup"><span data-stu-id="57190-107">See [the 1.1 version of this tutorial](xref:getting-started-1.1).</span></span>

1. <span data-ttu-id="57190-108">安装 [.NET Core](https://microsoft.com/net/core/)。</span><span class="sxs-lookup"><span data-stu-id="57190-108">Install [.NET Core](https://microsoft.com/net/core/).</span></span>

2. <span data-ttu-id="57190-109">创建新的 .NET Core 项目。</span><span class="sxs-lookup"><span data-stu-id="57190-109">Create a new .NET Core project.</span></span>

   <span data-ttu-id="57190-110">在 macOS 和 Linux 上，打开终端窗口。</span><span class="sxs-lookup"><span data-stu-id="57190-110">On macOS and Linux, open a terminal window.</span></span> <span data-ttu-id="57190-111">在 Windows 上，打开命令提示符。</span><span class="sxs-lookup"><span data-stu-id="57190-111">On Windows, open a command prompt.</span></span>

    ```terminal
    dotnet new razor -o aspnetcoreapp
    ```
    
4. <span data-ttu-id="57190-112">运行应用。</span><span class="sxs-lookup"><span data-stu-id="57190-112">Run the app.</span></span>

    <span data-ttu-id="57190-113">使用以下命令运行应用：</span><span class="sxs-lookup"><span data-stu-id="57190-113">Use the following commands to run the app:</span></span>

    ```terminal
    cd aspnetcoreapp
    dotnet run
    ```

5. <span data-ttu-id="57190-114">浏览到 [http://localhost:5000](http://localhost:5000)</span><span class="sxs-lookup"><span data-stu-id="57190-114">Browse to [http://localhost:5000](http://localhost:5000)</span></span>

6. <span data-ttu-id="57190-115">打开 Pages/About.cshtml 并将页面修改为显示消息“Hello, world!</span><span class="sxs-lookup"><span data-stu-id="57190-115">Open *Pages/About.cshtml* and modify the page to display the message "Hello, world!</span></span> <span data-ttu-id="57190-116">The time on the server is @DateTime.Now”：</span><span class="sxs-lookup"><span data-stu-id="57190-116">The time on the server is @DateTime.Now":</span></span>

    <span data-ttu-id="57190-117">[!code-html[Main](getting-started/sample/getting-started/about.cshtml?highlight=9&range=1-9)]</span><span class="sxs-lookup"><span data-stu-id="57190-117">[!code-html[Main](getting-started/sample/getting-started/about.cshtml?highlight=9&range=1-9)]</span></span>

7. <span data-ttu-id="57190-118">浏览到 [http://localhost:5000/About](http://localhost:5000/About) 并验证更改。</span><span class="sxs-lookup"><span data-stu-id="57190-118">Browse to [http://localhost:5000/About](http://localhost:5000/About) and verify the changes.</span></span>

### <a name="next-steps"></a><span data-ttu-id="57190-119">后续步骤</span><span class="sxs-lookup"><span data-stu-id="57190-119">Next steps</span></span>

<span data-ttu-id="57190-120">有关入门教程，请参阅 [ASP.NET Core 教程](tutorials/index.md)</span><span class="sxs-lookup"><span data-stu-id="57190-120">For getting-started tutorials, see [ASP.NET Core Tutorials](tutorials/index.md)</span></span>

<span data-ttu-id="57190-121">有关 ASP.NET Core 概念和体系结构的简介，请参阅 [ASP.NET Core 简介](index.md)和 [ASP.NET Core 基础知识](fundamentals/index.md)。</span><span class="sxs-lookup"><span data-stu-id="57190-121">For an introduction to ASP.NET Core concepts and architecture, see [ASP.NET Core Introduction](index.md) and [ASP.NET Core Fundamentals](fundamentals/index.md).</span></span>

<span data-ttu-id="57190-122">ASP.NET Core 应用可使用 .NET Core 或.NET Framework 基类库和运行时。</span><span class="sxs-lookup"><span data-stu-id="57190-122">An ASP.NET Core app can use the .NET Core or .NET Framework Base Class Library and runtime.</span></span> <span data-ttu-id="57190-123">有关详细信息，请参阅[在 .NET Core 和 .NET Framework 之间进行选择](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server)。</span><span class="sxs-lookup"><span data-stu-id="57190-123">For more information, see [Choosing between .NET Core and .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server).</span></span>
