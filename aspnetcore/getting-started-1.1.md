---
title: "ASP.NET Core 1.1 入门"
author: rick-anderson
description: "介绍如何使用 ASP.NET Core 1.1 创建并运行简单的 Hello World 应用的快速教程。"
keywords: "ASP.NET Core, 教程, 入门"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: get-started-article
ms.assetid: 73543e9d-d9d5-47d6-9664-17a9beea6cd3
ms.technology: aspnet
ms.prod: asp.net-core
uid: getting-started-1.1
ms.openlocfilehash: e8fd9ef60ebc1cff6ca0e03000ea50eebff0a9f9
ms.sourcegitcommit: 297ee5d2f3b3b24eb8a2c4a25195c9e2973cb91b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2017
---
# <a name="getting-started-with-aspnet-core-11"></a><span data-ttu-id="072b8-104">ASP.NET Core 1.1 入门</span><span class="sxs-lookup"><span data-stu-id="072b8-104">Getting Started with ASP.NET Core 1.1</span></span>

> [!NOTE]
> <span data-ttu-id="072b8-105">这些说明适用于 ASP.NET Core 1.1。</span><span class="sxs-lookup"><span data-stu-id="072b8-105">These instructions are for ASP.NET Core 1.1.</span></span> <span data-ttu-id="072b8-106">在寻找最新版本？</span><span class="sxs-lookup"><span data-stu-id="072b8-106">Looking for the latest version?</span></span> <span data-ttu-id="072b8-107">请参阅[本教程的当前版本](xref:getting-started)。</span><span class="sxs-lookup"><span data-stu-id="072b8-107">See [the current version of this tutorial](xref:getting-started).</span></span>

1. <span data-ttu-id="072b8-108">从 [.NET Core 1.0.5 & 1.1.2 SDK 1.0.4 下载页面](https://github.com/dotnet/core/blob/master/release-notes/download-archives/1.0.5-download.md)安装 SDK 1.0.4 的 .NET Core SDK 安装程序。</span><span class="sxs-lookup"><span data-stu-id="072b8-108">Install the .NET Core **SDK Installer** for SDK 1.0.4 from the [.NET Core 1.0.5 & 1.1.2 SDK 1.0.4 downloads page](https://github.com/dotnet/core/blob/master/release-notes/download-archives/1.0.5-download.md).</span></span>

2. <span data-ttu-id="072b8-109">为新 .NET Core 项目创建文件夹。</span><span class="sxs-lookup"><span data-stu-id="072b8-109">Create a folder for a new .NET Core project.</span></span>

   <span data-ttu-id="072b8-110">在 macOS 和 Linux 上，打开终端窗口。</span><span class="sxs-lookup"><span data-stu-id="072b8-110">On macOS and Linux, open a terminal window.</span></span> <span data-ttu-id="072b8-111">在 Windows 上，打开命令提示符。</span><span class="sxs-lookup"><span data-stu-id="072b8-111">On Windows, open a command prompt.</span></span>

   ```terminal
   mkdir aspnetcoreapp
   cd aspnetcoreapp
   ```

2. <span data-ttu-id="072b8-112">如果已计算机上安装了 SDK 的更高版本，则创建一个 global.json 文件以选择 1.0.4 SDK。</span><span class="sxs-lookup"><span data-stu-id="072b8-112">If you have installed a later SDK version on your machine, create a *global.json* file to select the 1.0.4 SDK.</span></span>

   ```json
   {
     "sdk": { "version": "1.0.4" }
   }
   ```

2. <span data-ttu-id="072b8-113">创建新的 .NET Core 项目。</span><span class="sxs-lookup"><span data-stu-id="072b8-113">Create a new .NET Core project.</span></span>

   ```terminal
   dotnet new web
   ```
   
3.  <span data-ttu-id="072b8-114">还原程序包。</span><span class="sxs-lookup"><span data-stu-id="072b8-114">Restore the packages.</span></span>

    ```terminal
    dotnet restore
    ```

4. <span data-ttu-id="072b8-115">运行应用。</span><span class="sxs-lookup"><span data-stu-id="072b8-115">Run the app.</span></span>

   <span data-ttu-id="072b8-116">如有需要，`dotnet run` 命令会首先生成应用。</span><span class="sxs-lookup"><span data-stu-id="072b8-116">The `dotnet run` command builds the app first if needed.</span></span>

   ```terminal
   dotnet run
   ```

5. <span data-ttu-id="072b8-117">浏览到 `http://localhost:5000`</span><span class="sxs-lookup"><span data-stu-id="072b8-117">Browse to `http://localhost:5000`</span></span>

<!-- H3 to avoid a single-entry internal TOC -->
### <a name="next-steps"></a><span data-ttu-id="072b8-118">后续步骤</span><span class="sxs-lookup"><span data-stu-id="072b8-118">Next steps</span></span>

<span data-ttu-id="072b8-119">有关入门教程，请参阅 [ASP.NET Core 教程](tutorials/index.md)</span><span class="sxs-lookup"><span data-stu-id="072b8-119">For getting-started tutorials, see [ASP.NET Core Tutorials](tutorials/index.md)</span></span>

<span data-ttu-id="072b8-120">有关 ASP.NET Core 概念和体系结构的简介，请参阅 [ASP.NET Core 简介](index.md)和 [ASP.NET Core 基础知识](fundamentals/index.md)。</span><span class="sxs-lookup"><span data-stu-id="072b8-120">For an introduction to ASP.NET Core concepts and architecture, see [ASP.NET Core Introduction](index.md) and [ASP.NET Core Fundamentals](fundamentals/index.md).</span></span>

<span data-ttu-id="072b8-121">ASP.NET Core 应用可使用 .NET Core 或.NET Framework 基类库和运行时。</span><span class="sxs-lookup"><span data-stu-id="072b8-121">An ASP.NET Core app can use the .NET Core or .NET Framework Base Class Library and runtime.</span></span> <span data-ttu-id="072b8-122">有关详细信息，请参阅[在 .NET Core 和 .NET Framework 之间进行选择](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server)。</span><span class="sxs-lookup"><span data-stu-id="072b8-122">For more information, see [Choosing between .NET Core and .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server).</span></span>
