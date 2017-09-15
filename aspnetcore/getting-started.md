---
title: "ASP.NET Core 2.0 入门"
author: rick-anderson
description: "介绍如何使用 ASP.NET Core 创建并运行简单的 Hello World 应用的快速教程。"
keywords: "ASP.NET Core, 教程, 入门"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: get-started-article
ms.assetid: 73543e9d-d9d5-47d6-9664-17a9beea6cd3
ms.technology: aspnet
ms.prod: asp.net-core
uid: getting-started
ms.openlocfilehash: 3399df3958093da9117b013736b1cb370fd6beb2
ms.sourcegitcommit: 297ee5d2f3b3b24eb8a2c4a25195c9e2973cb91b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2017
---
# <a name="getting-started-with-aspnet-core"></a><span data-ttu-id="6fe9a-104">ASP.NET Core 入门</span><span class="sxs-lookup"><span data-stu-id="6fe9a-104">Getting Started with ASP.NET Core</span></span>

> [!NOTE]
> <span data-ttu-id="6fe9a-105">这些说明适用于 ASP.NET Core 的最新版本。</span><span class="sxs-lookup"><span data-stu-id="6fe9a-105">These instructions are for the latest version of ASP.NET Core.</span></span> <span data-ttu-id="6fe9a-106">想要从早期版本开始？</span><span class="sxs-lookup"><span data-stu-id="6fe9a-106">Looking to get started with an earlier version?</span></span> <span data-ttu-id="6fe9a-107">请参阅[本教程的 1.1 版本](xref:getting-started-1.1)。</span><span class="sxs-lookup"><span data-stu-id="6fe9a-107">See [the 1.1 version of this tutorial](xref:getting-started-1.1).</span></span>

1. <span data-ttu-id="6fe9a-108">安装 [.NET Core](https://microsoft.com/net/core/)。</span><span class="sxs-lookup"><span data-stu-id="6fe9a-108">Install [.NET Core](https://microsoft.com/net/core/).</span></span>

2. <span data-ttu-id="6fe9a-109">创建新的 .NET Core 项目。</span><span class="sxs-lookup"><span data-stu-id="6fe9a-109">Create a new .NET Core project.</span></span>

   <span data-ttu-id="6fe9a-110">在 macOS 和 Linux 上，打开终端窗口。</span><span class="sxs-lookup"><span data-stu-id="6fe9a-110">On macOS and Linux, open a terminal window.</span></span> <span data-ttu-id="6fe9a-111">在 Windows 上，打开命令提示符。</span><span class="sxs-lookup"><span data-stu-id="6fe9a-111">On Windows, open a command prompt.</span></span>

   ```terminal
   mkdir aspnetcoreapp
   cd aspnetcoreapp
   dotnet new web
   ```
    
4. <span data-ttu-id="6fe9a-112">运行应用。</span><span class="sxs-lookup"><span data-stu-id="6fe9a-112">Run the app.</span></span>

   <span data-ttu-id="6fe9a-113">如有需要，`dotnet run` 命令会首先生成应用。</span><span class="sxs-lookup"><span data-stu-id="6fe9a-113">The `dotnet run` command builds the app first if needed.</span></span>

   ```terminal
   dotnet run
   ```

5. <span data-ttu-id="6fe9a-114">浏览到 `http://localhost:5000`</span><span class="sxs-lookup"><span data-stu-id="6fe9a-114">Browse to `http://localhost:5000`</span></span>

### <a name="next-steps"></a><span data-ttu-id="6fe9a-115">后续步骤</span><span class="sxs-lookup"><span data-stu-id="6fe9a-115">Next steps</span></span>

<span data-ttu-id="6fe9a-116">有关入门教程，请参阅 [ASP.NET Core 教程](tutorials/index.md)</span><span class="sxs-lookup"><span data-stu-id="6fe9a-116">For getting-started tutorials, see [ASP.NET Core Tutorials](tutorials/index.md)</span></span>

<span data-ttu-id="6fe9a-117">有关 ASP.NET Core 概念和体系结构的简介，请参阅 [ASP.NET Core 简介](index.md)和 [ASP.NET Core 基础知识](fundamentals/index.md)。</span><span class="sxs-lookup"><span data-stu-id="6fe9a-117">For an introduction to ASP.NET Core concepts and architecture, see [ASP.NET Core Introduction](index.md) and [ASP.NET Core Fundamentals](fundamentals/index.md).</span></span>

<span data-ttu-id="6fe9a-118">ASP.NET Core 应用可使用 .NET Core 或.NET Framework 基类库和运行时。</span><span class="sxs-lookup"><span data-stu-id="6fe9a-118">An ASP.NET Core app can use the .NET Core or .NET Framework Base Class Library and runtime.</span></span> <span data-ttu-id="6fe9a-119">有关详细信息，请参阅[在 .NET Core 和 .NET Framework 之间进行选择](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server)。</span><span class="sxs-lookup"><span data-stu-id="6fe9a-119">For more information, see [Choosing between .NET Core and .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server).</span></span>
