---
title: "使用 dotnet watch 开发 ASP.NET Core 应用"
author: rick-anderson
description: "本教程演示如何在 ASP.NET Core 应用程序中安装和使用 .NET Core CLI 的文件观察程序 (dotnet watch) 工具。"
keywords: "ASP.NET Core, 使用 dotnet watch"
ms.author: riande
manager: wpickett
ms.date: 10/05/2017
ms.topic: article
ms.assetid: 563ffb3f-d369-4aa5-bf0a-7300b4e7832c
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/dotnet-watch
ms.openlocfilehash: 9baf2ce2a1270a728616a8a2ab45deca9a9cde6f
ms.sourcegitcommit: e7f01a649f240b6b57118c53314ab82f7f36f2eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2017
---
# <a name="developing-aspnet-core-apps-using-dotnet-watch"></a><span data-ttu-id="15735-104">使用 dotnet watch 开发 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="15735-104">Developing ASP.NET Core apps using dotnet watch</span></span>

<span data-ttu-id="15735-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="15735-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="15735-106">`dotnet watch` 是一种在源文件更改时运行 [.NET Core CLI](/dotnet/core/tools) 命令的工具。</span><span class="sxs-lookup"><span data-stu-id="15735-106">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="15735-107">例如，文件更改可能触发编译、测试执行或部署。</span><span class="sxs-lookup"><span data-stu-id="15735-107">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="15735-108">本教程中使用现有 Web API 应用，它具有两个终结点：分别返回总计和产品。</span><span class="sxs-lookup"><span data-stu-id="15735-108">In this tutorial, we use an existing Web API app with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="15735-109">产品方法中有一个 bug，我们将在本教程中修复它。</span><span class="sxs-lookup"><span data-stu-id="15735-109">The product method contains a bug that we'll fix as part of this tutorial.</span></span>

<span data-ttu-id="15735-110">下载[示例应用](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample)。</span><span class="sxs-lookup"><span data-stu-id="15735-110">Download the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="15735-111">它包含两个项目：WebApp（一种 ASP.NET Core Web API）和 WebAppTests（Web API 的单元测试）。</span><span class="sxs-lookup"><span data-stu-id="15735-111">It contains two projects: *WebApp* (an ASP.NET Core Web API) and *WebAppTests* (unit tests for the Web API).</span></span>

<span data-ttu-id="15735-112">在命令外壳中，导航到“WebApp”文件夹并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="15735-112">In a command shell, navigate to the *WebApp* folder and run the following command:</span></span>

```console
dotnet run
```

<span data-ttu-id="15735-113">控制台输出会显示如下类似的消息（表示应用正在运行且正在等待请求）：</span><span class="sxs-lookup"><span data-stu-id="15735-113">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="15735-114">在 Web 浏览器中，导航到 `http://localhost:<port number>/api/math/sum?a=4&b=5`。</span><span class="sxs-lookup"><span data-stu-id="15735-114">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="15735-115">应该会显示结果 `9`。</span><span class="sxs-lookup"><span data-stu-id="15735-115">You should see the result of `9`.</span></span>

<span data-ttu-id="15735-116">导航到产品 API (`http://localhost:<port number>/api/math/product?a=4&b=5`)。</span><span class="sxs-lookup"><span data-stu-id="15735-116">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="15735-117">它会返回 `9`，而不是所预期的 `20`。</span><span class="sxs-lookup"><span data-stu-id="15735-117">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="15735-118">本教程稍后将对其进行修复。</span><span class="sxs-lookup"><span data-stu-id="15735-118">We'll fix that later in the tutorial.</span></span>

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="15735-119">将 `dotnet watch` 添加到项目</span><span class="sxs-lookup"><span data-stu-id="15735-119">Add `dotnet watch` to a project</span></span>

1. <span data-ttu-id="15735-120">将 `Microsoft.DotNet.Watcher.Tools` 包引用添加到 .csproj 文件：</span><span class="sxs-lookup"><span data-stu-id="15735-120">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup> 
    ```

1. <span data-ttu-id="15735-121">运行以下命令，安装 `Microsoft.DotNet.Watcher.Tools` 包：</span><span class="sxs-lookup"><span data-stu-id="15735-121">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>
    
    ```console
    dotnet restore
    ```

## <a name="running-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="15735-122">使用 `dotnet watch` 运行 .NET Core CLI 命令</span><span class="sxs-lookup"><span data-stu-id="15735-122">Running .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="15735-123">`dotnet watch` 可用于运行任何 [.NET Core CLI 命令](/dotnet/core/tools#cli-commands)</span><span class="sxs-lookup"><span data-stu-id="15735-123">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="15735-124">例如: </span><span class="sxs-lookup"><span data-stu-id="15735-124">For example:</span></span>

| <span data-ttu-id="15735-125">命令</span><span class="sxs-lookup"><span data-stu-id="15735-125">Command</span></span> | <span data-ttu-id="15735-126">带 watch 的命令</span><span class="sxs-lookup"><span data-stu-id="15735-126">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="15735-127">dotnet 运行</span><span class="sxs-lookup"><span data-stu-id="15735-127">dotnet run</span></span> | <span data-ttu-id="15735-128">dotnet watch run</span><span class="sxs-lookup"><span data-stu-id="15735-128">dotnet watch run</span></span> |
| <span data-ttu-id="15735-129">dotnet run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="15735-129">dotnet run -f netcoreapp2.0</span></span> | <span data-ttu-id="15735-130">dotnet watch run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="15735-130">dotnet watch run -f netcoreapp2.0</span></span> |
| <span data-ttu-id="15735-131">dotnet run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="15735-131">dotnet run -f netcoreapp2.0 -- --arg1</span></span> | <span data-ttu-id="15735-132">dotnet watch run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="15735-132">dotnet watch run -f netcoreapp2.0 -- --arg1</span></span> |
| <span data-ttu-id="15735-133">dotnet test</span><span class="sxs-lookup"><span data-stu-id="15735-133">dotnet test</span></span> | <span data-ttu-id="15735-134">dotnet watch test</span><span class="sxs-lookup"><span data-stu-id="15735-134">dotnet watch test</span></span> |

<span data-ttu-id="15735-135">运行“WebApp”文件夹中的 `dotnet watch run`。</span><span class="sxs-lookup"><span data-stu-id="15735-135">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="15735-136">控制台输出指示 `watch` 已启动。</span><span class="sxs-lookup"><span data-stu-id="15735-136">The console output indicates `watch` has started.</span></span>

## <a name="making-changes-with-dotnet-watch"></a><span data-ttu-id="15735-137">通过 `dotnet watch` 进行更改</span><span class="sxs-lookup"><span data-stu-id="15735-137">Making changes with `dotnet watch`</span></span>

<span data-ttu-id="15735-138">确保 `dotnet watch` 正在运行。</span><span class="sxs-lookup"><span data-stu-id="15735-138">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="15735-139">修复 MathController 的 `Product` 方法中的 bug，使其返回产品而非总和。</span><span class="sxs-lookup"><span data-stu-id="15735-139">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
  return a * b;
} 
```

<span data-ttu-id="15735-140">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="15735-140">Save the file.</span></span> <span data-ttu-id="15735-141">控制台输出指示 `dotnet watch` 已检测到文件更改并已重启应用。</span><span class="sxs-lookup"><span data-stu-id="15735-141">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="15735-142">验证 `http://localhost:<port number>/api/math/product?a=4&b=5` 是否返回正确结果。</span><span class="sxs-lookup"><span data-stu-id="15735-142">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="running-tests-using-dotnet-watch"></a><span data-ttu-id="15735-143">使用 `dotnet watch` 运行测试</span><span class="sxs-lookup"><span data-stu-id="15735-143">Running tests using `dotnet watch`</span></span>

1. <span data-ttu-id="15735-144">将 MathController.cs 的 `Product` 方法改回返回总和并保存文件。</span><span class="sxs-lookup"><span data-stu-id="15735-144">Change the `Product` method of *MathController.cs* back to returning the sum and save the file.</span></span>
1. <span data-ttu-id="15735-145">在命令外壳中，导航到“WebAppTests”文件夹。</span><span class="sxs-lookup"><span data-stu-id="15735-145">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="15735-146">运行 `dotnet restore`。</span><span class="sxs-lookup"><span data-stu-id="15735-146">Run `dotnet restore`.</span></span>
1. <span data-ttu-id="15735-147">运行 `dotnet watch test`。</span><span class="sxs-lookup"><span data-stu-id="15735-147">Run `dotnet watch test`.</span></span> <span data-ttu-id="15735-148">其输出指示测试失败且观察程序正在等待文件更改：</span><span class="sxs-lookup"><span data-stu-id="15735-148">Its output indicates that a test failed and that watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="15735-149">修复 `Product` 方法代码，使其返回产品。</span><span class="sxs-lookup"><span data-stu-id="15735-149">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="15735-150">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="15735-150">Save the file.</span></span>

<span data-ttu-id="15735-151">`dotnet watch` 检测到文件更改并重新运行测试。</span><span class="sxs-lookup"><span data-stu-id="15735-151">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="15735-152">控制台输出指示测试通过。</span><span class="sxs-lookup"><span data-stu-id="15735-152">The console output indicates the tests passed.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="15735-153">GitHub 中的 dotnet-watch</span><span class="sxs-lookup"><span data-stu-id="15735-153">dotnet-watch in GitHub</span></span>

<span data-ttu-id="15735-154">dotnet-watch 是 GitHub [DotNetTools 存储库](https://github.com/aspnet/DotNetTools/tree/dev/src/Microsoft.DotNet.Watcher.Tools)的一部分。</span><span class="sxs-lookup"><span data-stu-id="15735-154">dotnet-watch is part of the GitHub [DotNetTools repository](https://github.com/aspnet/DotNetTools/tree/dev/src/Microsoft.DotNet.Watcher.Tools).</span></span>

<span data-ttu-id="15735-155">[dotnet-watch 自述文件](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md)的 [MSBuild 部分](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md#msbuild)阐释了如何通过被监视的 MSBuild 项目文件配置 dotnet-watch。</span><span class="sxs-lookup"><span data-stu-id="15735-155">The [MSBuild section](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md#msbuild) of the [dotnet-watch ReadMe](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md) explains how dotnet-watch can be configured from the MSBuild project file being watched.</span></span> <span data-ttu-id="15735-156">[dotnet-watch 自述文件](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md)介绍了本教程中没有的 dotnet-watch 相关信息。</span><span class="sxs-lookup"><span data-stu-id="15735-156">The [dotnet-watch ReadMe](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md) contains information on dotnet-watch not covered in this tutorial.</span></span>
