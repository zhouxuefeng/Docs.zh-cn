---
title: "使用 dotnet watch 开发 ASP.NET Core 应用"
author: rick-anderson
description: "演示如何使用 dotnet watch。"
keywords: "ASP.NET Core, 使用 dotnet watch"
ms.author: riande
manager: wpickett
ms.date: 03/09/2017
ms.topic: article
ms.assetid: 563ffb3f-d369-4aa5-bf0a-7300b4e7832c
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/dotnet-watch
ms.openlocfilehash: 2ddcbfc30a839ed8dd72a632644bf73dcea777ac
ms.sourcegitcommit: b02db6da115e55140da91b67355aaf56aae1703f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2017
---
# <a name="developing-aspnet-core-apps-using-dotnet-watch"></a><span data-ttu-id="5df25-104">使用 dotnet watch 开发 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="5df25-104">Developing ASP.NET Core apps using dotnet watch</span></span>


<span data-ttu-id="5df25-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="5df25-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="5df25-106">`dotnet watch` 是一种在源文件更改时运行 `dotnet` 命令的工具。</span><span class="sxs-lookup"><span data-stu-id="5df25-106">`dotnet watch` is a tool that runs a `dotnet` command when source files change.</span></span> <span data-ttu-id="5df25-107">例如，文件更改可能触发编译、测试或部署。</span><span class="sxs-lookup"><span data-stu-id="5df25-107">For example, a file change can trigger compilation, tests, or deployment.</span></span>

<span data-ttu-id="5df25-108">本教程中使用现有 Web API 应用，它具有两个终结点：分别返回总计和产品。</span><span class="sxs-lookup"><span data-stu-id="5df25-108">In this tutorial, we use an existing Web API app with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="5df25-109">产品方法中有一个 bug，我们将在本教程中修复它。</span><span class="sxs-lookup"><span data-stu-id="5df25-109">The product method contains a bug that we'll fix as part of this tutorial.</span></span>

<span data-ttu-id="5df25-110">下载[示例应用](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample)。</span><span class="sxs-lookup"><span data-stu-id="5df25-110">Download the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="5df25-111">它包含两个项目：`WebApp`（Web 应用）和 `WebAppTests`（Web 应用的单元测试）。</span><span class="sxs-lookup"><span data-stu-id="5df25-111">It contains two projects, `WebApp` (a web app) and `WebAppTests` (unit tests for the web app).</span></span>

<span data-ttu-id="5df25-112">在控制台中，导航到“WebApp”文件夹并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5df25-112">In a console, navigate to the WebApp folder and run the following commands:</span></span>

- `dotnet restore`
- `dotnet run`

<span data-ttu-id="5df25-113">控制台输出将显示类似如下的消息（表示应用正在运行且正在等待请求）：</span><span class="sxs-lookup"><span data-stu-id="5df25-113">The console output will show messages similar to the following (indicating that the app is running and waiting for requests):</span></span>

```console
$ dotnet run
Hosting environment: Production
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="5df25-114">在 Web 浏览器中，导航到 `http://localhost:5000/api/math/sum?a=4&b=5`，应该会看到结果 `9`。</span><span class="sxs-lookup"><span data-stu-id="5df25-114">In a web browser, navigate to `http://localhost:5000/api/math/sum?a=4&b=5`, you should see the result `9`.</span></span>

<span data-ttu-id="5df25-115">导航到产品 API (`http://localhost:5000/api/math/product?a=4&b=5`)，它将如你所料返回 `9` 而非 `20`。</span><span class="sxs-lookup"><span data-stu-id="5df25-115">Navigate to the product API (`http://localhost:5000/api/math/product?a=4&b=5`), it returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="5df25-116">本教程稍后将对其进行修复。</span><span class="sxs-lookup"><span data-stu-id="5df25-116">We'll fix that later in the tutorial.</span></span>

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="5df25-117">将 `dotnet watch` 添加到项目</span><span class="sxs-lookup"><span data-stu-id="5df25-117">Add `dotnet watch` to a project</span></span>

- <span data-ttu-id="5df25-118">将 `Microsoft.DotNet.Watcher.Tools` 添加到“.csproj”文件：</span><span class="sxs-lookup"><span data-stu-id="5df25-118">Add `Microsoft.DotNet.Watcher.Tools` to the *.csproj* file:</span></span>
 ```xml
 <ItemGroup>
   <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
 </ItemGroup> 
 ```

- <span data-ttu-id="5df25-119">运行 `dotnet restore`。</span><span class="sxs-lookup"><span data-stu-id="5df25-119">Run `dotnet restore`.</span></span>

## <a name="running-dotnet-commands-using-dotnet-watch"></a><span data-ttu-id="5df25-120">使用 `dotnet watch` 运行 `dotnet` 命令</span><span class="sxs-lookup"><span data-stu-id="5df25-120">Running `dotnet` commands using `dotnet watch`</span></span>

<span data-ttu-id="5df25-121">可使用 `dotnet watch` 运行任何 `dotnet` 命令，例如：</span><span class="sxs-lookup"><span data-stu-id="5df25-121">Any `dotnet` command can be run with `dotnet watch`, for example:</span></span>

| <span data-ttu-id="5df25-122">命令</span><span class="sxs-lookup"><span data-stu-id="5df25-122">Command</span></span> | <span data-ttu-id="5df25-123">带 watch 的命令</span><span class="sxs-lookup"><span data-stu-id="5df25-123">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="5df25-124">dotnet 运行</span><span class="sxs-lookup"><span data-stu-id="5df25-124">dotnet run</span></span> | <span data-ttu-id="5df25-125">dotnet watch run</span><span class="sxs-lookup"><span data-stu-id="5df25-125">dotnet watch run</span></span> |
| <span data-ttu-id="5df25-126">dotnet run -f net451</span><span class="sxs-lookup"><span data-stu-id="5df25-126">dotnet run -f net451</span></span> | <span data-ttu-id="5df25-127">dotnet watch run -f net451</span><span class="sxs-lookup"><span data-stu-id="5df25-127">dotnet watch run -f net451</span></span> |
| <span data-ttu-id="5df25-128">dotnet run -f net451 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="5df25-128">dotnet run -f net451 -- --arg1</span></span> | <span data-ttu-id="5df25-129">dotnet watch run -f net451 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="5df25-129">dotnet watch run -f net451 -- --arg1</span></span> |
| <span data-ttu-id="5df25-130">dotnet test</span><span class="sxs-lookup"><span data-stu-id="5df25-130">dotnet test</span></span> | <span data-ttu-id="5df25-131">dotnet watch test</span><span class="sxs-lookup"><span data-stu-id="5df25-131">dotnet watch test</span></span> |

<span data-ttu-id="5df25-132">在 `WebApp` 文件夹中运行 `dotnet watch run`。</span><span class="sxs-lookup"><span data-stu-id="5df25-132">Run `dotnet watch run` in the `WebApp` folder.</span></span> <span data-ttu-id="5df25-133">控制台输出将指示 `watch` 已启动。</span><span class="sxs-lookup"><span data-stu-id="5df25-133">The console output will indicate `watch` has started.</span></span>

## <a name="making-changes-with-dotnet-watch"></a><span data-ttu-id="5df25-134">通过 `dotnet watch` 进行更改</span><span class="sxs-lookup"><span data-stu-id="5df25-134">Making changes with `dotnet watch`</span></span>

<span data-ttu-id="5df25-135">确保 `dotnet watch` 正在运行。</span><span class="sxs-lookup"><span data-stu-id="5df25-135">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="5df25-136">在 `MathController` 的 `Product` 方法中修复该 bug，使其返回产品而非总和。</span><span class="sxs-lookup"><span data-stu-id="5df25-136">Fix the bug in the `Product` method of the `MathController` so it returns the product and not the sum.</span></span>

```csharp
public static int Product(int a, int b)
{
  return a * b;
} 
```

<span data-ttu-id="5df25-137">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="5df25-137">Save the file.</span></span> <span data-ttu-id="5df25-138">控制台输出将显示消息，表示 `dotnet watch` 已检测到文件更改并已重启应用。</span><span class="sxs-lookup"><span data-stu-id="5df25-138">The console output will show messages indicating that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="5df25-139">验证 `http://localhost:5000/api/math/product?a=4&b=5` 是否返回正确结果。</span><span class="sxs-lookup"><span data-stu-id="5df25-139">Verify `http://localhost:5000/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="running-tests-using-dotnet-watch"></a><span data-ttu-id="5df25-140">使用 `dotnet watch` 运行测试</span><span class="sxs-lookup"><span data-stu-id="5df25-140">Running tests using `dotnet watch`</span></span>

- <span data-ttu-id="5df25-141">将 `MathController` 的 `Product` 方法改回返回总和并保存文件。</span><span class="sxs-lookup"><span data-stu-id="5df25-141">Change the `Product` method of the `MathController` back to returning the sum and save the file.</span></span>
- <span data-ttu-id="5df25-142">在命令窗口中，导航到 `WebAppTests` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="5df25-142">In a command window, naviagate to the `WebAppTests` folder.</span></span>
- <span data-ttu-id="5df25-143">运行 `dotnet restore`</span><span class="sxs-lookup"><span data-stu-id="5df25-143">Run `dotnet restore`</span></span>
- <span data-ttu-id="5df25-144">运行 `dotnet watch test`。</span><span class="sxs-lookup"><span data-stu-id="5df25-144">Run `dotnet watch test`.</span></span> <span data-ttu-id="5df25-145">将看到输出，它表示测试失败且观察程序正在等待文件更改：</span><span class="sxs-lookup"><span data-stu-id="5df25-145">You see output indicating that a test failed and that watcher is waiting for file changes:</span></span>

 ```console
 Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
 Test Run Failed.
  ```
- <span data-ttu-id="5df25-146">修复 `Product` 方法代码，使其返回产品。</span><span class="sxs-lookup"><span data-stu-id="5df25-146">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="5df25-147">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="5df25-147">Save the file.</span></span>

<span data-ttu-id="5df25-148">`dotnet watch` 检测到文件更改并重新运行测试。</span><span class="sxs-lookup"><span data-stu-id="5df25-148">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="5df25-149">控制台输出将显示通过的测试。</span><span class="sxs-lookup"><span data-stu-id="5df25-149">The console output will show the tests passed.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="5df25-150">GitHub 中的 dotnet-watch</span><span class="sxs-lookup"><span data-stu-id="5df25-150">dotnet-watch in GitHub</span></span>

<span data-ttu-id="5df25-151">dotnet-watch 是 GitHub [DotNetTools 存储库](https://github.com/aspnet/DotNetTools/tree/dev/src/Microsoft.DotNet.Watcher.Tools)的一部分。</span><span class="sxs-lookup"><span data-stu-id="5df25-151">dotnet-watch is part of the GitHub [DotNetTools repository](https://github.com/aspnet/DotNetTools/tree/dev/src/Microsoft.DotNet.Watcher.Tools).</span></span>

<span data-ttu-id="5df25-152">[dotnet-watch 自述文件](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md)的 [MSBuild 部分](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md#msbuild)阐释了如何通过被监视的 MSBuild 项目文件配置 dotnet-watch。</span><span class="sxs-lookup"><span data-stu-id="5df25-152">The [MSBuild section](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md#msbuild) of the [dotnet-watch ReadMe](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md) explains how dotnet-watch can be configured from the MSBuild project file being watched.</span></span> <span data-ttu-id="5df25-153">[dotnet-watch 自述文件](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md)介绍了本教程中没有的 dotnet-watch 相关信息。</span><span class="sxs-lookup"><span data-stu-id="5df25-153">The [dotnet-watch ReadMe](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md) contains information on dotnet-watch not covered in this tutorial.</span></span>
