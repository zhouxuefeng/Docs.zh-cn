---
title: "从 ASP.NET Core 1.x 迁移到 2.0"
author: scottaddie
description: "本文概述了将 ASP.NET Core 1.x 项目迁移到 ASP.NET Core 2.0 的先决条件和最常见步骤。"
keywords: "ASP.NET Core, 迁移"
ms.author: scaddie
manager: wpickett
ms.date: 08/01/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/1x-to-2x/index
ms.openlocfilehash: db277ee6b079eab973a565983d6661bf95fce2e3
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="migrating-from-aspnet-core-1x-to-aspnet-core-20"></a><span data-ttu-id="6cb32-104">从 ASP.NET Core 1.x 迁移到 ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="6cb32-104">Migrating from ASP.NET Core 1.x to ASP.NET Core 2.0</span></span>

<span data-ttu-id="6cb32-105">作者：[Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="6cb32-105">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="6cb32-106">本文将演示如何将现有 ASP.NET Core 1.x 项目更新到 ASP.NET Core 2.0。</span><span class="sxs-lookup"><span data-stu-id="6cb32-106">In this article, we'll walk you through updating an existing ASP.NET Core 1.x project to ASP.NET Core 2.0.</span></span> <span data-ttu-id="6cb32-107">通过将应用程序迁移到 ASP.NET Core 2.0，可利用[大量新功能和改进功能](https://docs.microsoft.com/aspnet/core/aspnetcore-2.0)。</span><span class="sxs-lookup"><span data-stu-id="6cb32-107">Migrating your application to ASP.NET Core 2.0 enables you to take advantage of [many new features and performance improvements](https://docs.microsoft.com/aspnet/core/aspnetcore-2.0).</span></span> 

<span data-ttu-id="6cb32-108">现有 ASP.NET Core 1.x 应用程序基于版本特定的项目模板。</span><span class="sxs-lookup"><span data-stu-id="6cb32-108">Existing ASP.NET Core 1.x applications are based off of version-specific project templates.</span></span> <span data-ttu-id="6cb32-109">随着 ASP.NET Core 框架不断演变，其中的项目模板和起始代码也在变化。</span><span class="sxs-lookup"><span data-stu-id="6cb32-109">As the ASP.NET Core framework evolves, so do the project templates and the starter code contained within them.</span></span> <span data-ttu-id="6cb32-110">除了更新 ASP.NET Core 框架外，还需要为应用程序更新代码。</span><span class="sxs-lookup"><span data-stu-id="6cb32-110">In addition to updating the ASP.NET Core framework, you need to update the code for your application.</span></span>

<a name="prerequisites"></a>

## <a name="prerequisites"></a><span data-ttu-id="6cb32-111">先决条件</span><span class="sxs-lookup"><span data-stu-id="6cb32-111">Prerequisites</span></span>
<span data-ttu-id="6cb32-112">请参阅 [ASP.NET Core 入门](xref:getting-started)。</span><span class="sxs-lookup"><span data-stu-id="6cb32-112">Please see [Getting Started with ASP.NET Core](xref:getting-started).</span></span>

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a><span data-ttu-id="6cb32-113">更新目标框架名字对象 (TFM)</span><span class="sxs-lookup"><span data-stu-id="6cb32-113">Update Target Framework Moniker (TFM)</span></span>
<span data-ttu-id="6cb32-114">面向 .NET Core 的项目需使用大于或等于 .NET Core 2.0 版本的 [TFM](/dotnet/standard/frameworks#referring-to-frameworks)。</span><span class="sxs-lookup"><span data-stu-id="6cb32-114">Projects targeting .NET Core should use the [TFM](/dotnet/standard/frameworks#referring-to-frameworks) of a version greater than or equal to .NET Core 2.0.</span></span> <span data-ttu-id="6cb32-115">在“.csproj”文件中搜索 `<TargetFramework>` 节点，并将其内部文本替换为 `netcoreapp2.0`：</span><span class="sxs-lookup"><span data-stu-id="6cb32-115">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `netcoreapp2.0`:</span></span>

<span data-ttu-id="6cb32-116">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=3)]</span><span class="sxs-lookup"><span data-stu-id="6cb32-116">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=3)]</span></span>

<span data-ttu-id="6cb32-117">面向 .NET Framework 的项目需使用大于或等于 .NET Framework 4.6.1 版本的 TFM。</span><span class="sxs-lookup"><span data-stu-id="6cb32-117">Projects targeting .NET Framework should use the TFM of a version greater than or equal to .NET Framework 4.6.1.</span></span> <span data-ttu-id="6cb32-118">在“.csproj”文件中搜索 `<TargetFramework>` 节点，并将其内部文本替换为 `net461`：</span><span class="sxs-lookup"><span data-stu-id="6cb32-118">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `net461`:</span></span>

<span data-ttu-id="6cb32-119">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]</span><span class="sxs-lookup"><span data-stu-id="6cb32-119">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]</span></span>

> [!NOTE]
> <span data-ttu-id="6cb32-120">相比于 .NET Core 1.x，.NET Core 2.0 提供更多的外围应用。</span><span class="sxs-lookup"><span data-stu-id="6cb32-120">.NET Core 2.0 offers a much larger surface area than .NET Core 1.x.</span></span> <span data-ttu-id="6cb32-121">如果仅因为 .NET Core 1.x 中缺少 API 而要面向 .NET Framework，则定向于 .NET Core 2.0 可能有用。</span><span class="sxs-lookup"><span data-stu-id="6cb32-121">If you're targeting .NET Framework solely because of missing APIs in .NET Core 1.x, targeting .NET Core 2.0 is likely to work.</span></span>

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a><span data-ttu-id="6cb32-122">在 global.json 中更新 .NET Core SDK 版本</span><span class="sxs-lookup"><span data-stu-id="6cb32-122">Update .NET Core SDK version in global.json</span></span>
<span data-ttu-id="6cb32-123">如果解决方案依靠 [global.json](https://docs.microsoft.com/dotnet/core/tools/global-json) 文件来定向于特定 .NET Core SDK 版本，请更新其 `version` 属性以使用计算机上安装的 2.0 版本：</span><span class="sxs-lookup"><span data-stu-id="6cb32-123">If your solution relies upon a [*global.json*](https://docs.microsoft.com/dotnet/core/tools/global-json) file to target a specific .NET Core SDK version, update its `version` property to use the 2.0 version installed on your machine:</span></span>

<span data-ttu-id="6cb32-124">[!code-json[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/global.json?highlight=3)]</span><span class="sxs-lookup"><span data-stu-id="6cb32-124">[!code-json[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/global.json?highlight=3)]</span></span>

<a name="package-reference"></a>

## <a name="update-package-references"></a><span data-ttu-id="6cb32-125">更新包引用</span><span class="sxs-lookup"><span data-stu-id="6cb32-125">Update package references</span></span>
<span data-ttu-id="6cb32-126">1.x 项目中的“.csproj”文件列出了该项目使用的每个 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="6cb32-126">The *.csproj* file in a 1.x project lists each NuGet package used by the project.</span></span>

<span data-ttu-id="6cb32-127">在面向 .NET Core 2.0 的 ASP.NET Core 2.0 项目中，“.csproj”文件中的单个 [metapackage](xref:fundamentals/metapackage) 引用将替换包的集合：</span><span class="sxs-lookup"><span data-stu-id="6cb32-127">In an ASP.NET Core 2.0 project targeting .NET Core 2.0, a single [metapackage](xref:fundamentals/metapackage) reference in the *.csproj* file replaces the collection of packages:</span></span>

<span data-ttu-id="6cb32-128">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=9-11)]</span><span class="sxs-lookup"><span data-stu-id="6cb32-128">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=9-11)]</span></span>

<span data-ttu-id="6cb32-129">该元包中具备 ASP.NET Core 2.0 和 Entity Framework Core 2.0 的所有功能。</span><span class="sxs-lookup"><span data-stu-id="6cb32-129">All the features of ASP.NET Core 2.0 and Entity Framework Core 2.0 are included in the metapackage.</span></span>

<span data-ttu-id="6cb32-130">面向 .NET Framework 的 ASP.NET Core 2.0 项目应继续引用单个 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="6cb32-130">ASP.NET Core 2.0 projects targeting .NET Framework should continue to reference individual NuGet packages.</span></span> <span data-ttu-id="6cb32-131">将每个 `<PackageReference />` 节点的 `Version` 特性更新至 2.0.0。</span><span class="sxs-lookup"><span data-stu-id="6cb32-131">Update the `Version` attribute of each `<PackageReference />` node to 2.0.0.</span></span>

<span data-ttu-id="6cb32-132">例如，下述列表列出了面向 .NET Framework 的典型 ASP.NET Core 2.0 项目中使用的 `<PackageReference />` 节点：</span><span class="sxs-lookup"><span data-stu-id="6cb32-132">For example, here's the list of `<PackageReference />` nodes used in a typical ASP.NET Core 2.0 project targeting .NET Framework:</span></span>

<span data-ttu-id="6cb32-133">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]</span><span class="sxs-lookup"><span data-stu-id="6cb32-133">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]</span></span>

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a><span data-ttu-id="6cb32-134">更新 .NET Core CLI 工具</span><span class="sxs-lookup"><span data-stu-id="6cb32-134">Update .NET Core CLI tools</span></span>
<span data-ttu-id="6cb32-135">在“.csproj”文件中，将每个 `<DotNetCliToolReference />` 节点的 `Version` 特性更新至 2.0.0。</span><span class="sxs-lookup"><span data-stu-id="6cb32-135">In the *.csproj* file, update the `Version` attribute of each `<DotNetCliToolReference />` node to 2.0.0.</span></span>

<span data-ttu-id="6cb32-136">例如，下述列表列出了面向 .NET Core 2.0 的典型 ASP.NET Core 2.0 项目中使用的 CLI 工具：</span><span class="sxs-lookup"><span data-stu-id="6cb32-136">For example, here's the list of CLI tools used in a typical ASP.NET Core 2.0 project targeting .NET Core 2.0:</span></span>

<span data-ttu-id="6cb32-137">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=13-17)]</span><span class="sxs-lookup"><span data-stu-id="6cb32-137">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=13-17)]</span></span>

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a><span data-ttu-id="6cb32-138">重命名“包目标回退”属性</span><span class="sxs-lookup"><span data-stu-id="6cb32-138">Rename Package Target Fallback property</span></span>
<span data-ttu-id="6cb32-139">1.x 项目的“.csproj”文件使用了 `PackageTargetFallback` 节点和变量：</span><span class="sxs-lookup"><span data-stu-id="6cb32-139">The *.csproj* file of a 1.x project used a `PackageTargetFallback` node and variable:</span></span>

<span data-ttu-id="6cb32-140">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App.csproj?range=5)]</span><span class="sxs-lookup"><span data-stu-id="6cb32-140">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App.csproj?range=5)]</span></span>

<span data-ttu-id="6cb32-141">将节点和变量重命名为 `AssetTargetFallback`：</span><span class="sxs-lookup"><span data-stu-id="6cb32-141">Rename both the node and variable to `AssetTargetFallback`:</span></span>

<span data-ttu-id="6cb32-142">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=5)]</span><span class="sxs-lookup"><span data-stu-id="6cb32-142">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=5)]</span></span>

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a><span data-ttu-id="6cb32-143">更新 Program.cs 中的 Main 方法</span><span class="sxs-lookup"><span data-stu-id="6cb32-143">Update Main method in Program.cs</span></span>
<span data-ttu-id="6cb32-144">在 1.x 项目中，“Program.cs”的 `Main` 方法如下所示：</span><span class="sxs-lookup"><span data-stu-id="6cb32-144">In 1.x projects, the `Main` method of *Program.cs* looked like this:</span></span>

<span data-ttu-id="6cb32-145">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]</span><span class="sxs-lookup"><span data-stu-id="6cb32-145">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]</span></span>

<span data-ttu-id="6cb32-146">在 2.0 项目中，简化了“Program.cs”的 `Main` 方法：</span><span class="sxs-lookup"><span data-stu-id="6cb32-146">In 2.0 projects, the `Main` method of *Program.cs* has been simplified:</span></span>

<span data-ttu-id="6cb32-147">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/Program.cs?highlight=8-11)]</span><span class="sxs-lookup"><span data-stu-id="6cb32-147">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/Program.cs?highlight=8-11)]</span></span>

<span data-ttu-id="6cb32-148">强烈建议采用新的 2.0 模式，[Entity Framework Core 迁移](xref:data/ef-mvc/migrations) 等产品功能需要此模式才能正常运行。</span><span class="sxs-lookup"><span data-stu-id="6cb32-148">The adoption of this new 2.0 pattern is highly recommended and is required for product features like [Entity Framework Core Migrations](xref:data/ef-mvc/migrations) to work.</span></span> <span data-ttu-id="6cb32-149">例如，从“包管理器控制台”窗口运行 `Update-Database`，或从命令行（位于转换为 ASP.NET Core 2.0 的项目上）运行 `dotnet ef database update`时，都会生成以下错误：</span><span class="sxs-lookup"><span data-stu-id="6cb32-149">For example, running `Update-Database` from the Package Manager Console window or `dotnet ef database update` from the command line (on projects converted to ASP.NET Core 2.0) generates the following error:</span></span>

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="view-compilation"></a>

## <a name="review-your-razor-view-compilation-setting"></a><span data-ttu-id="6cb32-150">查看 Razor 视图编译设置</span><span class="sxs-lookup"><span data-stu-id="6cb32-150">Review your Razor View Compilation setting</span></span>
<span data-ttu-id="6cb32-151">加快应用程序启动速度和缩小已发布的捆绑包至关重要。</span><span class="sxs-lookup"><span data-stu-id="6cb32-151">Faster application startup time and smaller published bundles are of utmost importance to you.</span></span> <span data-ttu-id="6cb32-152">为此，ASP.NET Core 2.0 中默认启用 [Razor 视图编译](xref:mvc/views/view-compilation)。</span><span class="sxs-lookup"><span data-stu-id="6cb32-152">For these reasons, [Razor view compilation](xref:mvc/views/view-compilation) is enabled by default in ASP.NET Core 2.0.</span></span>

<span data-ttu-id="6cb32-153">无需再将 `MvcRazorCompileOnPublish` 属性设置为 true。</span><span class="sxs-lookup"><span data-stu-id="6cb32-153">Setting the `MvcRazorCompileOnPublish` property to true is no longer required.</span></span> <span data-ttu-id="6cb32-154">若不禁用视图编译，可能会从“.csproj”文件中删除此属性。</span><span class="sxs-lookup"><span data-stu-id="6cb32-154">Unless you're disabling view compilation, the property may be removed from the *.csproj* file.</span></span>

<span data-ttu-id="6cb32-155">以 .NET Framework 为目标时，仍需显式引用“.csproj”文件中的 [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="6cb32-155">When targeting .NET Framework, you still need to explicitly reference the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) NuGet package in your *.csproj* file:</span></span>

<span data-ttu-id="6cb32-156">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]</span><span class="sxs-lookup"><span data-stu-id="6cb32-156">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]</span></span>

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a><span data-ttu-id="6cb32-157">依靠 Application Insights“启动”功能</span><span class="sxs-lookup"><span data-stu-id="6cb32-157">Rely on Application Insights "Light-Up" features</span></span>
<span data-ttu-id="6cb32-158">能够轻松设置应用程序性能检测非常重要。</span><span class="sxs-lookup"><span data-stu-id="6cb32-158">Effortless setup of application performance instrumentation is important.</span></span> <span data-ttu-id="6cb32-159">现可依靠 Visual Studio 2017 工具中推出的新的 [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)“启动”功能。</span><span class="sxs-lookup"><span data-stu-id="6cb32-159">You can now rely on the new [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) "light-up" features available in the Visual Studio 2017 tooling.</span></span>

<span data-ttu-id="6cb32-160">Visual Studio 2017 中创建的 ASP.NET Core 1.1 项目默认添加 Application Insights。</span><span class="sxs-lookup"><span data-stu-id="6cb32-160">ASP.NET Core 1.1 projects created in Visual Studio 2017 added Application Insights by default.</span></span> <span data-ttu-id="6cb32-161">若不直接使用 Application Insights SDK，则除了执行“Program.cs”和“Startup.cs”，还请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="6cb32-161">If you're not using the Application Insights SDK directly, outside of *Program.cs* and *Startup.cs*, follow these steps:</span></span>

1. <span data-ttu-id="6cb32-162">从“.csproj”文件中删除以下 `<PackageReference />` 节点：</span><span class="sxs-lookup"><span data-stu-id="6cb32-162">Remove the following `<PackageReference />` node from the *.csproj* file:</span></span>
    
    <span data-ttu-id="6cb32-163">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App.csproj?range=10)]</span><span class="sxs-lookup"><span data-stu-id="6cb32-163">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App.csproj?range=10)]</span></span>

2. <span data-ttu-id="6cb32-164">从“Program.cs”中删除 `UseApplicationInsights` 扩展方法调用：</span><span class="sxs-lookup"><span data-stu-id="6cb32-164">Remove the `UseApplicationInsights` extension method invocation from *Program.cs*:</span></span>

    <span data-ttu-id="6cb32-165">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]</span><span class="sxs-lookup"><span data-stu-id="6cb32-165">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]</span></span>

3. <span data-ttu-id="6cb32-166">从“_Layout.cshtml”中删除 Application Insights 客户端 API 调用。</span><span class="sxs-lookup"><span data-stu-id="6cb32-166">Remove the Application Insights client-side API call from *_Layout.cshtml*.</span></span> <span data-ttu-id="6cb32-167">它会比较以下两行代码：</span><span class="sxs-lookup"><span data-stu-id="6cb32-167">It comprises the following two lines of code:</span></span>

    <span data-ttu-id="6cb32-168">[!code-cshtml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Views/Shared/_Layout.cshtml?range=1,19)]</span><span class="sxs-lookup"><span data-stu-id="6cb32-168">[!code-cshtml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Views/Shared/_Layout.cshtml?range=1,19)]</span></span>

<span data-ttu-id="6cb32-169">若要直接使用 Application Insights SDK，请继续此操作。</span><span class="sxs-lookup"><span data-stu-id="6cb32-169">If you are using the Application Insights SDK directly, continue to do so.</span></span> <span data-ttu-id="6cb32-170">2.0 [元包](xref:fundamentals/metapackage)中具备最新版本的 Application Insights，因此如果引用较旧版本，将出现包降级错误。</span><span class="sxs-lookup"><span data-stu-id="6cb32-170">The 2.0 [metapackage](xref:fundamentals/metapackage) includes the latest version of Application Insights, so a package downgrade error appears if you're referencing an older version.</span></span>

<a name="auth-and-identity"></a>

## <a name="adopt-authentication--identity-improvements"></a><span data-ttu-id="6cb32-171">采用身份验证/标识改进</span><span class="sxs-lookup"><span data-stu-id="6cb32-171">Adopt Authentication / Identity Improvements</span></span>
<span data-ttu-id="6cb32-172">ASP.NET Core 2.0 具有新的身份验证模型和大量针对 ASP.NET Core 标识的重大更改。</span><span class="sxs-lookup"><span data-stu-id="6cb32-172">ASP.NET Core 2.0 has a new authentication model and a number of significant changes to ASP.NET Core Identity.</span></span> <span data-ttu-id="6cb32-173">如果在启用单个用户帐户的情况下创建项目，或者已手动添加身份验证或标识，请参阅[将身份验证和标识迁移到 ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x)。</span><span class="sxs-lookup"><span data-stu-id="6cb32-173">If you created your project with Individual User Accounts enabled, or if you have manually added authentication or Identity, see [Migrating Authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6cb32-174">其他资源</span><span class="sxs-lookup"><span data-stu-id="6cb32-174">Additional Resources</span></span>
- [<span data-ttu-id="6cb32-175">ASP.NET Core 2.0 中的重大更改</span><span class="sxs-lookup"><span data-stu-id="6cb32-175">Breaking Changes in ASP.NET Core 2.0</span></span>](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
