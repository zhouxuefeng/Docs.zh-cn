---
title: "使用 ASP.NET Core 和 VS Code 创建 Web API"
author: rick-anderson
description: "在 macOS、Linux 或 Windows 上使用 ASP.NET Core MVC 和 Visual Studio Code 构建 Web API"
keywords: "ASP.NET Core, WebAPI, Web API, REST, Mac, Linux, HTTP, 服务, HTTP 服务, VS Code"
ms.author: riande
manager: wpickett
ms.date: 5/24/2017
ms.topic: get-started-article
ms.assetid: 830b4bf5-dd14-423e-9f59-764a6f13a8f6
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/web-api-vsc
ms.openlocfilehash: abe088f2c9df94135209ce71540e6b345186ee70
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="create-a-web-api-with-aspnet-core-mvc-and-visual-studio-code-on-linux-macos-and-windows"></a><span data-ttu-id="3b734-104">在Linux、macOS 或 Windows 上使用 ASP.NET Core MVC 和 Visual Studio Code 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="3b734-104">Create a Web API with ASP.NET Core MVC and Visual Studio Code on Linux, macOS, and Windows</span></span>

<span data-ttu-id="3b734-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="3b734-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="3b734-106">在本教程中，将生成用于管理“待办事项”列表的 Web API。</span><span class="sxs-lookup"><span data-stu-id="3b734-106">In this tutorial, you’ll build a web API for managing a list of "to-do" items.</span></span> <span data-ttu-id="3b734-107">不会生成 UI。</span><span class="sxs-lookup"><span data-stu-id="3b734-107">You won’t build a UI.</span></span>

<span data-ttu-id="3b734-108">本教程提供 3 个版本：</span><span class="sxs-lookup"><span data-stu-id="3b734-108">There are 3 versions of this tutorial:</span></span>

* <span data-ttu-id="3b734-109">macOS、Linux、Windows：使用 Visual Studio Code 创建 Web API（本教程）</span><span class="sxs-lookup"><span data-stu-id="3b734-109">macOS, Linux, Windows: Web API with Visual Studio Code (This tutorial)</span></span>
* <span data-ttu-id="3b734-110">macOS：[使用 Visual Studio for Mac 创建 Web API](xref:tutorials/first-web-api-mac)</span><span class="sxs-lookup"><span data-stu-id="3b734-110">macOS: [Web API with Visual Studio for Mac](xref:tutorials/first-web-api-mac)</span></span>
* <span data-ttu-id="3b734-111">Windows：[使用 Visual Studio for Windows 创建 Web API](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="3b734-111">Windows: [Web API with Visual Studio for Windows](xref:tutorials/first-web-api)</span></span>

<!-- WARNING: The code AND images in this doc are used by uid: tutorials/web-api-vsc, tutorials/first-web-api-mac and tutorials/first-web-api. If you change any code/images in this tutorial, update uid: tutorials/web-api-vsc -->

[!INCLUDE[template files](../includes/webApi/intro.md)]

## <a name="set-up-your-development-environment"></a><span data-ttu-id="3b734-112">设置开发环境</span><span class="sxs-lookup"><span data-stu-id="3b734-112">Set up your development environment</span></span>

<span data-ttu-id="3b734-113">下载和安装：</span><span class="sxs-lookup"><span data-stu-id="3b734-113">Download and install:</span></span>
- [<span data-ttu-id="3b734-114">.NET Core</span><span class="sxs-lookup"><span data-stu-id="3b734-114">.NET Core</span></span>](https://microsoft.com/net/core)
- [<span data-ttu-id="3b734-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b734-115">Visual Studio Code</span></span>](https://code.visualstudio.com)
- <span data-ttu-id="3b734-116">Visual Studio Code [C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)</span><span class="sxs-lookup"><span data-stu-id="3b734-116">Visual Studio Code [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)</span></span>

## <a name="create-the-project"></a><span data-ttu-id="3b734-117">创建项目</span><span class="sxs-lookup"><span data-stu-id="3b734-117">Create the project</span></span>

<span data-ttu-id="3b734-118">从控制台运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="3b734-118">From a console, run the following commands:</span></span>

```console
mkdir TodoApi
cd TodoApi
dotnet new webapi
```

<span data-ttu-id="3b734-119">在 Visual Studio Code (VS Code) 中打开“TodoApi”文件夹并选择“Startup.cs”文件。</span><span class="sxs-lookup"><span data-stu-id="3b734-119">Open the *TodoApi* folder in Visual Studio Code (VS Code) and select the *Startup.cs* file.</span></span>

- <span data-ttu-id="3b734-120">对于警告消息 -“"TodoApi" 中缺少进行生成和调试所需的资产。是否添加它们?”，。</span><span class="sxs-lookup"><span data-stu-id="3b734-120">Select **Yes** to the **Warn** message "Required assets to build and debug are missing from 'TodoApi'.</span></span> <span data-ttu-id="3b734-121">请选择“是”</span><span class="sxs-lookup"><span data-stu-id="3b734-121">Add them?"</span></span>
- <span data-ttu-id="3b734-122">对于信息性消息 -“存在未解析的依赖项”，请选择“还原”。</span><span class="sxs-lookup"><span data-stu-id="3b734-122">Select **Restore** to the **Info** message "There are unresolved dependencies".</span></span>

<!-- uid: tutorials/first-mvc-app-xplat/start-mvc uses the pic below. If you change it, make sure it's consistent -->

![带有“"TodoApi" 中缺少进行生成和调试所需的资产。是否添加它们?”](web-api-vsc/_static/vsc_restore.png)

<span data-ttu-id="3b734-126">按“调试”(F5) 生成并运行程序。</span><span class="sxs-lookup"><span data-stu-id="3b734-126">Press **Debug** (F5) to build and run the program.</span></span> <span data-ttu-id="3b734-127">在浏览器中，导航到 http://localhost:5000/api/values。</span><span class="sxs-lookup"><span data-stu-id="3b734-127">In a browser navigate to http://localhost:5000/api/values .</span></span> <span data-ttu-id="3b734-128">将显示以下内容：</span><span class="sxs-lookup"><span data-stu-id="3b734-128">The following is displayed:</span></span>

`["value1","value2"]`

<span data-ttu-id="3b734-129">有关 VS Code 的使用技巧，请参阅 [Visual Studio Code 帮助](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="3b734-129">See [Visual Studio Code help](#visual-studio-code-help) for tips on using VS Code.</span></span>

## <a name="add-support-for-entity-framework-core"></a><span data-ttu-id="3b734-130">添加对 Entity Framework Core 的支持</span><span class="sxs-lookup"><span data-stu-id="3b734-130">Add support for Entity Framework Core</span></span>

<span data-ttu-id="3b734-131">编辑“TodoApi.csproj”文件以安装 [Entity Framework Core InMemory](https://docs.microsoft.com/ef/core/providers/in-memory/) 数据库提供程序。</span><span class="sxs-lookup"><span data-stu-id="3b734-131">Edit the *TodoApi.csproj* file to install the [Entity Framework Core InMemory](https://docs.microsoft.com/ef/core/providers/in-memory/) database provider.</span></span> <span data-ttu-id="3b734-132">此数据库提供程序允许将 Entity Framework Core 和内存数据库一起使用。</span><span class="sxs-lookup"><span data-stu-id="3b734-132">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span>

<span data-ttu-id="3b734-133">[!code-xml[Main](web-api-vsc/sample/TodoApi/TodoApi.csproj?highlight=12)]</span><span class="sxs-lookup"><span data-stu-id="3b734-133">[!code-xml[Main](web-api-vsc/sample/TodoApi/TodoApi.csproj?highlight=12)]</span></span>

<span data-ttu-id="3b734-134">运行 `dotnet restore` 以下载和安装 EF Core InMemory DB 提供程序。</span><span class="sxs-lookup"><span data-stu-id="3b734-134">Run `dotnet restore` to download and install the EF Core InMemory DB provider.</span></span> <span data-ttu-id="3b734-135">可从终端运行 `dotnet restore`，或在 VS Code 中按 `⌘⇧P` (macOS) 或 `Ctrl+Shift+P` (Linux)，然后键入“.NET”。</span><span class="sxs-lookup"><span data-stu-id="3b734-135">You can run `dotnet restore` from the terminal or enter `⌘⇧P` (macOS) or `Ctrl+Shift+P` (Linux) in VS Code and then type **.NET**.</span></span> <span data-ttu-id="3b734-136">选择“.NET: 还原包”。</span><span class="sxs-lookup"><span data-stu-id="3b734-136">Select **.NET: Restore Packages**.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="3b734-137">添加模型类</span><span class="sxs-lookup"><span data-stu-id="3b734-137">Add a model class</span></span>

<span data-ttu-id="3b734-138">模型是表示应用程序中的数据的对象。</span><span class="sxs-lookup"><span data-stu-id="3b734-138">A model is an object that represents the data in your application.</span></span> <span data-ttu-id="3b734-139">在此示例中，唯一的模型是待办事项。</span><span class="sxs-lookup"><span data-stu-id="3b734-139">In this case, the only model is a to-do item.</span></span>

<span data-ttu-id="3b734-140">添加名为“模型”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="3b734-140">Add a folder named *Models*.</span></span> <span data-ttu-id="3b734-141">可将模型类置于项目的任意位置，但按照惯例会使用“模型”文件夹。</span><span class="sxs-lookup"><span data-stu-id="3b734-141">You can put model classes anywhere in your project, but the *Models* folder is used by convention.</span></span>

<span data-ttu-id="3b734-142">添加带有以下代码的 `TodoItem` 类：</span><span class="sxs-lookup"><span data-stu-id="3b734-142">Add a `TodoItem` class with the following code:</span></span>

<span data-ttu-id="3b734-143">[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoItem.cs)]</span><span class="sxs-lookup"><span data-stu-id="3b734-143">[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoItem.cs)]</span></span>

<span data-ttu-id="3b734-144">创建 `TodoItem` 时，数据库将生成 `Id`。</span><span class="sxs-lookup"><span data-stu-id="3b734-144">The database generates the `Id` when a `TodoItem` is created.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="3b734-145">创建数据库上下文</span><span class="sxs-lookup"><span data-stu-id="3b734-145">Create the database context</span></span>

<span data-ttu-id="3b734-146">数据库上下文是为给定数据模型协调 Entity Framework 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="3b734-146">The *database context* is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="3b734-147">将通过从 `Microsoft.EntityFrameworkCore.DbContext` 类派生的方式创建此类。</span><span class="sxs-lookup"><span data-stu-id="3b734-147">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

<span data-ttu-id="3b734-148">在“模型”文件夹中添加 `TodoContext` 类：</span><span class="sxs-lookup"><span data-stu-id="3b734-148">Add a `TodoContext` class in the *Models* folder:</span></span>

<span data-ttu-id="3b734-149">[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoContext.cs)]</span><span class="sxs-lookup"><span data-stu-id="3b734-149">[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoContext.cs)]</span></span>

[!INCLUDE[Register the database context](../includes/webApi/register_dbContext.md)]

## <a name="add-a-controller"></a><span data-ttu-id="3b734-150">添加控制器</span><span class="sxs-lookup"><span data-stu-id="3b734-150">Add a controller</span></span>

<span data-ttu-id="3b734-151">在“控制器”文件夹中，创建名为 `TodoController` 的类。</span><span class="sxs-lookup"><span data-stu-id="3b734-151">In the *Controllers* folder, create a class named `TodoController`.</span></span> <span data-ttu-id="3b734-152">添加以下代码：</span><span class="sxs-lookup"><span data-stu-id="3b734-152">Add the following code:</span></span>

[!INCLUDE[code and get todo items](../includes/webApi/getTodoItems.md)]

### <a name="launch-the-app"></a><span data-ttu-id="3b734-153">启动应用</span><span class="sxs-lookup"><span data-stu-id="3b734-153">Launch the app</span></span>

<span data-ttu-id="3b734-154">在 VS Code 中，按 F5 启动应用。</span><span class="sxs-lookup"><span data-stu-id="3b734-154">In VS Code, press F5 to launch the app.</span></span> <span data-ttu-id="3b734-155">导航到 http://localhost:5000/api/todo（之前创建的 `Todo` 控制器）。</span><span class="sxs-lookup"><span data-stu-id="3b734-155">Navigate to  http://localhost:5000/api/todo   (The `Todo` controller we just created).</span></span>

[!INCLUDE[last part of web API](../includes/webApi/end.md)]

## <a name="visual-studio-code-help"></a><span data-ttu-id="3b734-156">Visual Studio Code 帮助</span><span class="sxs-lookup"><span data-stu-id="3b734-156">Visual Studio Code help</span></span>

- [<span data-ttu-id="3b734-157">入门</span><span class="sxs-lookup"><span data-stu-id="3b734-157">Getting started</span></span>](https://code.visualstudio.com/docs)
- [<span data-ttu-id="3b734-158">调试</span><span class="sxs-lookup"><span data-stu-id="3b734-158">Debugging</span></span>](https://code.visualstudio.com/docs/editor/debugging)
- [<span data-ttu-id="3b734-159">集成终端</span><span class="sxs-lookup"><span data-stu-id="3b734-159">Integrated terminal</span></span>](https://code.visualstudio.com/docs/editor/integrated-terminal)
- [<span data-ttu-id="3b734-160">键盘快捷键</span><span class="sxs-lookup"><span data-stu-id="3b734-160">Keyboard shortcuts</span></span>](https://code.visualstudio.com/docs/getstarted/keybindings#_keyboard-shortcuts-reference)

  - [<span data-ttu-id="3b734-161">Mac 键盘快捷键</span><span class="sxs-lookup"><span data-stu-id="3b734-161">Mac keyboard shortcuts</span></span>](https://go.microsoft.com/fwlink/?linkid=832143)
  - [<span data-ttu-id="3b734-162">Linux 键盘快捷键</span><span class="sxs-lookup"><span data-stu-id="3b734-162">Linux keyboard shortcuts</span></span>](https://go.microsoft.com/fwlink/?linkid=832144)
  - [<span data-ttu-id="3b734-163">Windows 键盘快捷键</span><span class="sxs-lookup"><span data-stu-id="3b734-163">Windows keyboard shortcuts</span></span>](https://go.microsoft.com/fwlink/?linkid=832145)

[!INCLUDE[next steps](../includes/webApi/next.md)]


