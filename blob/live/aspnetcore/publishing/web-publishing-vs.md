---
title: "创建 Visual Studio 和 MSBuild 的发布配置文件"
author: rick-anderson
description: "在 Visual Studio 中为 Web 发布提供说明。"
keywords: "ASP.NET Core, Web 发布, 发布, msbuild, Web 部署, dotnet 发布, Visual Studio 2017"
ms.author: riande
manager: wpickett
ms.date: 09/26/2017
ms.topic: article
ms.assetid: 0377a02d-8fda-47a5-929a-24a16e1d2c93
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/web-publishing-vs
ms.openlocfilehash: f010f9d90165ce4d6718fe1440e600985f21a01d
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="create-publish-profiles-for-visual-studio-and-msbuild-to-deploy-aspnet-core-apps"></a><span data-ttu-id="15696-104">创建 Visual Studio 和 MSBuild 的发布配置文件以部署 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="15696-104">Create publish profiles for Visual Studio and MSBuild, to deploy ASP.NET Core apps</span></span>

<span data-ttu-id="15696-105">作者：[Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="15696-105">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="15696-106">本文重点介绍如何使用 Visual Studio 2017 创建发布配置文件。</span><span class="sxs-lookup"><span data-stu-id="15696-106">This article focuses on using Visual Studio 2017 to create publish profiles.</span></span> <span data-ttu-id="15696-107">可以从 MSBuild 和 Visual Studio 2017 运行使用 Visual Studio 创建的发布配置文件。</span><span class="sxs-lookup"><span data-stu-id="15696-107">The publish profiles created with Visual Studio can be run from MSBuild and Visual Studio 2017.</span></span> <span data-ttu-id="15696-108">本文提供发布过程的详细信息。</span><span class="sxs-lookup"><span data-stu-id="15696-108">The article provides details of the publishing process.</span></span> <span data-ttu-id="15696-109">有关发布到 Azure 的说明，请参阅[使用 Visual Studio 将 ASP.NET Core Web 应用发布到 Azure App Service](xref:tutorials/publish-to-azure-webapp-using-vs)。</span><span class="sxs-lookup"><span data-stu-id="15696-109">See [Publish an ASP.NET Core web app to Azure App Service using Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs) for instructions on publishing to Azure.</span></span>

<span data-ttu-id="15696-110">使用命令 `dotnet new mvc` 创建以下 .csproj 文件：</span><span class="sxs-lookup"><span data-stu-id="15696-110">The following *.csproj* file was created with the command `dotnet new mvc`:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="15696-111">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="15696-111">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
  </ItemGroup>

  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.0" />
  </ItemGroup>

</Project>
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="15696-112">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="15696-112">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp1.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore" Version="1.1.1" />
    <PackageReference Include="Microsoft.AspNetCore.Mvc" Version="1.1.2" />
    <PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="1.1.1" />
  </ItemGroup>

</Project>
```

---

<span data-ttu-id="15696-113">上述标记的 `<Project>` 元素中的 `Sdk` 属性（位于第一行）执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="15696-113">The `Sdk` attribute in the `<Project>` element (in the first line) of the markup above does the following:</span></span>

* <span data-ttu-id="15696-114">在开始时从 $(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.Props 导入 `props` 文件。</span><span class="sxs-lookup"><span data-stu-id="15696-114">Imports the `props` file from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.Props* at the beginning.</span></span>
* <span data-ttu-id="15696-115">在结束时从 $(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets 导入目标文件。</span><span class="sxs-lookup"><span data-stu-id="15696-115">Imports the targets file from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets* at the end.</span></span>

<span data-ttu-id="15696-116">`MSBuildSDKsPath`（装有 Visual Studio 2017 Enterprise）的默认位置是 %programfiles(x86)%\Microsoft Visual Studio\2017\Enterprise\MSBuild\Sdks 文件夹。</span><span class="sxs-lookup"><span data-stu-id="15696-116">The default location for `MSBuildSDKsPath` (with Visual Studio 2017 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2017\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="15696-117">`Microsoft.NET.Sdk.Web` 依赖于：</span><span class="sxs-lookup"><span data-stu-id="15696-117">`Microsoft.NET.Sdk.Web` depends on:</span></span>

* <span data-ttu-id="15696-118">*Microsoft.NET.Sdk.Web.ProjectSystem*</span><span class="sxs-lookup"><span data-stu-id="15696-118">*Microsoft.NET.Sdk.Web.ProjectSystem*</span></span>
* <span data-ttu-id="15696-119">*Microsoft.NET.Sdk.Publish*</span><span class="sxs-lookup"><span data-stu-id="15696-119">*Microsoft.NET.Sdk.Publish*</span></span>

<span data-ttu-id="15696-120">这将导致导入以下 `props` 和 `targets`：</span><span class="sxs-lookup"><span data-stu-id="15696-120">Which causes the following `props` and `targets` to be imported:</span></span>

* <span data-ttu-id="15696-121">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.Props</span><span class="sxs-lookup"><span data-stu-id="15696-121">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.Props</span></span>
* <span data-ttu-id="15696-122">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.targets</span><span class="sxs-lookup"><span data-stu-id="15696-122">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.targets</span></span>
* <span data-ttu-id="15696-123">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.Props</span><span class="sxs-lookup"><span data-stu-id="15696-123">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.Props</span></span>
* <span data-ttu-id="15696-124">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.targets</span><span class="sxs-lookup"><span data-stu-id="15696-124">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.targets</span></span>

<span data-ttu-id="15696-125">发布目标将根据使用的发布方法，再次导入正确的目标集。</span><span class="sxs-lookup"><span data-stu-id="15696-125">Publish targets will again import the right set of targets based on the publish method used.</span></span>

<span data-ttu-id="15696-126">MSBuild 或 Visual Studio 加载项目时，执行下列高级别操作：</span><span class="sxs-lookup"><span data-stu-id="15696-126">When MSBuild or Visual Studio loads a project, the following high level actions are performed:</span></span>

* <span data-ttu-id="15696-127">生成项目</span><span class="sxs-lookup"><span data-stu-id="15696-127">Build project</span></span>
* <span data-ttu-id="15696-128">计算要发布的文件</span><span class="sxs-lookup"><span data-stu-id="15696-128">Compute files to publish</span></span>
* <span data-ttu-id="15696-129">将文件发布到目标</span><span class="sxs-lookup"><span data-stu-id="15696-129">Publish files to destination</span></span>

### <a name="compute-project-items"></a><span data-ttu-id="15696-130">计算项目项</span><span class="sxs-lookup"><span data-stu-id="15696-130">Compute project items</span></span>

<span data-ttu-id="15696-131">加载项目时，将计算项目项（文件）。</span><span class="sxs-lookup"><span data-stu-id="15696-131">When the project is loaded, the project items (files) are computed.</span></span> <span data-ttu-id="15696-132">`item type` 属性确定如何处理该文件。</span><span class="sxs-lookup"><span data-stu-id="15696-132">The `item type` attribute determines how the file is processed.</span></span> <span data-ttu-id="15696-133">默认情况下，.cs 文件包含在 `Compile` 项列表内。</span><span class="sxs-lookup"><span data-stu-id="15696-133">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="15696-134">会对 `Compile` 项列表中的文件进行编译。</span><span class="sxs-lookup"><span data-stu-id="15696-134">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="15696-135">除了生成输出，`Content` 项列表还包含将要发布的文件。</span><span class="sxs-lookup"><span data-stu-id="15696-135">The `Content` item list contains files that will be published in addition to the build outputs.</span></span> <span data-ttu-id="15696-136">默认情况下，匹配模式 wwwroot/** 的文件将包含在 `Content` 项内。</span><span class="sxs-lookup"><span data-stu-id="15696-136">By default, files matching the pattern wwwroot/** will be included in the `Content` item.</span></span> <span data-ttu-id="15696-137">[wwwroot/** 是一个通配模式](https://gruntjs.com/configuring-tasks#globbing-patterns)，它指定 wwwroot 文件夹和子文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="15696-137">[wwwroot/** is a globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) that specifies all files in the *wwwroot* folder **and** subfolders.</span></span> <span data-ttu-id="15696-138">若要将文件显式添加到发布列表，请直接在 .csproj 文件中添加文件，如[加入文件](#including-files)中所示。</span><span class="sxs-lookup"><span data-stu-id="15696-138">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in [Including Files](#including-files).</span></span>

<span data-ttu-id="15696-139">在 Visual Studio 中选择“发布”按钮时或从命令行发布时：</span><span class="sxs-lookup"><span data-stu-id="15696-139">When you select the **Publish** button in Visual Studio or when you publish from command line:</span></span>

- <span data-ttu-id="15696-140">计算属性/项目（需要生成的文件）。</span><span class="sxs-lookup"><span data-stu-id="15696-140">The properties/items are computed (the files that are needed to build).</span></span>
- <span data-ttu-id="15696-141">仅限 Visual Studio：还原 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="15696-141">Visual Studio only: NuGet packages are restored.</span></span>  <span data-ttu-id="15696-142">（用户需要在 CLI 上执行显式还原。）</span><span class="sxs-lookup"><span data-stu-id="15696-142">(Restore needs to be explicit by the user on the CLI.)</span></span>
- <span data-ttu-id="15696-143">生成项目。</span><span class="sxs-lookup"><span data-stu-id="15696-143">The project builds.</span></span>
- <span data-ttu-id="15696-144">计算发布项（需要发布的文件）。</span><span class="sxs-lookup"><span data-stu-id="15696-144">The publish items are computed (the files that are needed to publish).</span></span>
- <span data-ttu-id="15696-145">发布项目。</span><span class="sxs-lookup"><span data-stu-id="15696-145">The project is published.</span></span> <span data-ttu-id="15696-146">（计算的文件将被复制到发布目标。）</span><span class="sxs-lookup"><span data-stu-id="15696-146">(The computed files are copied to the publish destination.)</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="15696-147">基本命令行发布</span><span class="sxs-lookup"><span data-stu-id="15696-147">Basic command line publishing</span></span>

<span data-ttu-id="15696-148">本部分适用于所有支持 .NET Core 的平台，而且不需要 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="15696-148">This section works on all .NET Core supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="15696-149">在下面的示例中，从项目目录（其中包含 .csproj 文件）运行 `dotnet publish` 命令。</span><span class="sxs-lookup"><span data-stu-id="15696-149">In the samples below, the `dotnet publish` command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="15696-150">如果你不在项目文件夹中，可以在项目文件路径中显式传递。</span><span class="sxs-lookup"><span data-stu-id="15696-150">If you're not in the project folder, you can explicitly pass in the project file path.</span></span> <span data-ttu-id="15696-151">例如: </span><span class="sxs-lookup"><span data-stu-id="15696-151">For example:</span></span>

```console
dotnet publish  c:/webs/web1
```

<span data-ttu-id="15696-152">运行以下命令以创建并发布 Web 应用：</span><span class="sxs-lookup"><span data-stu-id="15696-152">Run the following commands to create and publish a web app:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="15696-153">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="15696-153">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```console
dotnet new mvc
dotnet publish
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="15696-154">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="15696-154">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```console
dotnet new mvc
dotnet restore
dotnet publish
```

--------------

<span data-ttu-id="15696-155">`dotnet publish` 生成类似下面的输出：</span><span class="sxs-lookup"><span data-stu-id="15696-155">The `dotnet publish` produces output similar to the following:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version 15.3.409.57025 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp2.0\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp2.0\publish\
```

<span data-ttu-id="15696-156">默认发布文件夹为 `bin\$(Configuration)\netcoreapp<version>\publish`。</span><span class="sxs-lookup"><span data-stu-id="15696-156">The default publish folder is `bin\$(Configuration)\netcoreapp<version>\publish`.</span></span> <span data-ttu-id="15696-157">`$(Configuration)` 的默认值为 Debug。</span><span class="sxs-lookup"><span data-stu-id="15696-157">The default for `$(Configuration)` is Debug.</span></span> <span data-ttu-id="15696-158">在上述示例中，`<TargetFramework>` 是 `netcoreapp2.0`。</span><span class="sxs-lookup"><span data-stu-id="15696-158">In the sample above, the `<TargetFramework>` is `netcoreapp2.0`.</span></span>

<span data-ttu-id="15696-159">`dotnet publish -h` 显示用于发布的帮助信息。</span><span class="sxs-lookup"><span data-stu-id="15696-159">`dotnet publish -h` displays help information for publish.</span></span>

<span data-ttu-id="15696-160">以下命令指定 `Release` 生成和发布目录：</span><span class="sxs-lookup"><span data-stu-id="15696-160">The following command specifies a `Release` build and the publishing directory:</span></span>

```console
dotnet publish -c Release -o C:/MyWebs/test
```

<span data-ttu-id="15696-161">`dotnet publish` 命令调用 `MSBuild`，从而调用 `Publish` 目标。</span><span class="sxs-lookup"><span data-stu-id="15696-161">The `dotnet publish` command calls `MSBuild` which invokes the `Publish` target.</span></span> <span data-ttu-id="15696-162">任何传递给 `dotnet publish` 的参数都将传递给 `MSBuild`。</span><span class="sxs-lookup"><span data-stu-id="15696-162">Any parameters passed to `dotnet publish` are passed to `MSBuild`.</span></span> <span data-ttu-id="15696-163">`-c` 参数映射到 `Configuration` MSBuild 属性。</span><span class="sxs-lookup"><span data-stu-id="15696-163">The `-c` parameter maps to the `Configuration` MSBuild property.</span></span> <span data-ttu-id="15696-164">`-o` 参数映射到 `OutputPath`。</span><span class="sxs-lookup"><span data-stu-id="15696-164">The `-o` parameter maps to `OutputPath`.</span></span>

<span data-ttu-id="15696-165">可以使用以下任一格式来传递 MSBuild 属性：</span><span class="sxs-lookup"><span data-stu-id="15696-165">You can pass MSBuild properties using either of the following formats:</span></span>

- ` p:<NAME>=<VALUE>`
- `/p:<NAME>=<VALUE>`

<span data-ttu-id="15696-166">以下命令将 `Release` 版本发布到网络共享：</span><span class="sxs-lookup"><span data-stu-id="15696-166">The following command publishes a `Release` build to a network share:</span></span>

`dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb`

<span data-ttu-id="15696-167">网络共享通过正斜杠指定 (//r8/) 并适用于所有支持 .NET Core 的平台。</span><span class="sxs-lookup"><span data-stu-id="15696-167">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

<span data-ttu-id="15696-168">确认用于部署的发布应用未在运行。</span><span class="sxs-lookup"><span data-stu-id="15696-168">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="15696-169">如果应用正在运行，publish 文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="15696-169">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="15696-170">部署不会发生，因为无法复制锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="15696-170">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="15696-171">发布配置文件</span><span class="sxs-lookup"><span data-stu-id="15696-171">Publish profiles</span></span>

<span data-ttu-id="15696-172">本部分使用 Visual Studio 2017 和更高版本创建发布配置文件。</span><span class="sxs-lookup"><span data-stu-id="15696-172">This section uses Visual Studio 2017 and higher to create publishing profiles.</span></span> <span data-ttu-id="15696-173">创建后，可以从 Visual Studio 或命令行中发布。</span><span class="sxs-lookup"><span data-stu-id="15696-173">Once created, you can publish from Visual Studio or the command line.</span></span>

<span data-ttu-id="15696-174">发布配置文件可以简化发布过程。</span><span class="sxs-lookup"><span data-stu-id="15696-174">Publish profiles can simplify the publishing process.</span></span> <span data-ttu-id="15696-175">可以拥有多个发布配置文件。</span><span class="sxs-lookup"><span data-stu-id="15696-175">You can have multiple publish profiles.</span></span> <span data-ttu-id="15696-176">若要在 Visual Studio 中创建发布配置文件，请右键单击“解决方案资源管理器”中的项目，然后选择“发布”。</span><span class="sxs-lookup"><span data-stu-id="15696-176">To create a publish profile in Visual Studio, right click on the project in Solution Explore and select **Publish**.</span></span> <span data-ttu-id="15696-177">或者，可以从生成菜单中选择“发布”\<“项目名称”>。</span><span class="sxs-lookup"><span data-stu-id="15696-177">Alternatively, you can select **Publish     \<project name>** from the build menu.</span></span> <span data-ttu-id="15696-178">随即显示应用程序容量页的“发布”选项卡。</span><span class="sxs-lookup"><span data-stu-id="15696-178">The **Publish** tab of the application capacities page is displayed.</span></span> <span data-ttu-id="15696-179">如果项目不包含发布配置文件，将显示以下页面：</span><span class="sxs-lookup"><span data-stu-id="15696-179">If the project doesn't contain a publish profile, the following page is displayed:</span></span>

![显示 Azure、IIS、FTB、文件夹以及已选择 Azure 的应用程序容量页的“发布”选项卡。](web-publishing-vs/_static/az.png)

<span data-ttu-id="15696-182">选中“文件夹”后，“发布”按钮会创建一个文件夹发布配置文件并进行发布。</span><span class="sxs-lookup"><span data-stu-id="15696-182">When **Folder** is selected, the **Publish** button creates a folder publish profile and publishes.</span></span>

![显示 Azure、IIS、FTB、文件夹的应用程序容量页的“发布”选项卡](web-publishing-vs/_static/pub1.png)

<span data-ttu-id="15696-184">创建发布配置文件后，“发布”选项卡将发生更改，可以选择“创建新的配置文件”来创建新的配置文件。</span><span class="sxs-lookup"><span data-stu-id="15696-184">Once a publish profile is created, the **Publish** tab changes, and you select **Create new profile** to create a new profile.</span></span>

![显示上一步骤中创建的 FolderProfile 和“创建新配置文件”链接的应用程序容量页的“发布”选项卡](web-publishing-vs/_static/create_new.png)

<span data-ttu-id="15696-186">发布向导支持以下发布目标：</span><span class="sxs-lookup"><span data-stu-id="15696-186">The Publish wizard supports the following publish targets:</span></span>

- <span data-ttu-id="15696-187">Microsoft Azure App Service</span><span class="sxs-lookup"><span data-stu-id="15696-187">Microsoft Azure App Service</span></span>
- <span data-ttu-id="15696-188">IIS、FTP 等（适用于任何 Web 服务器）</span><span class="sxs-lookup"><span data-stu-id="15696-188">IIS, FTP, etc (for any web server)</span></span>
- <span data-ttu-id="15696-189">文件夹</span><span class="sxs-lookup"><span data-stu-id="15696-189">Folder</span></span>
- <span data-ttu-id="15696-190">导入配置文件（实现导入配置文件）。</span><span class="sxs-lookup"><span data-stu-id="15696-190">Import profile (allows you to import a profile).</span></span>
- <span data-ttu-id="15696-191">Microsoft Azure 虚拟机</span><span class="sxs-lookup"><span data-stu-id="15696-191">Microsoft Azure Virtual Machines</span></span>

<span data-ttu-id="15696-192">有关详细信息，请参阅[哪些发布选项适合我？](https://docs.microsoft.com/visualstudio/ide/not-in-toc/web-publish-options)。</span><span class="sxs-lookup"><span data-stu-id="15696-192">See [What publishing options are right for me?](https://docs.microsoft.com/visualstudio/ide/not-in-toc/web-publish-options) for more information.</span></span>

<span data-ttu-id="15696-193">使用 Visual Studio 创建发布配置文件时，将创建 Properties/PublishProfiles/\<publish name>.pubxml MSBuild 文件。</span><span class="sxs-lookup"><span data-stu-id="15696-193">When you create a publish profile with Visual Studio, a *Properties/PublishProfiles/\<publish name>.pubxml* MSBuild file is created.</span></span> <span data-ttu-id="15696-194">此 .pubxml 文件为 MSBuild 文件，包含发布配置设置。</span><span class="sxs-lookup"><span data-stu-id="15696-194">This *.pubxml* file is a MSBuild file and contains publish configuration settings.</span></span> <span data-ttu-id="15696-195">可以更改此文件以自定义生成和发布过程。</span><span class="sxs-lookup"><span data-stu-id="15696-195">You can change this file to customize the build and publish process.</span></span> <span data-ttu-id="15696-196">通过发布过程读取此文件。</span><span class="sxs-lookup"><span data-stu-id="15696-196">This file is read by the publishing process.</span></span> <span data-ttu-id="15696-197">`<LastUsedBuildConfiguration>` 比较特殊，因为它是一个全局属性，不应出现在导入生成的任何文件中。</span><span class="sxs-lookup"><span data-stu-id="15696-197">`<LastUsedBuildConfiguration>` is special because it’s a global property and shouldn’t be in any file that’s imported in the build.</span></span> <span data-ttu-id="15696-198">有关详细信息，请参阅 [MSBuild：如何设置配置属性](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx)。</span><span class="sxs-lookup"><span data-stu-id="15696-198">See [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx) for more info.</span></span>
<span data-ttu-id="15696-199">不应将 .Pubxml 文件签入源控件，因为它依赖于 .user 文件。</span><span class="sxs-lookup"><span data-stu-id="15696-199">The *.pubxml* file should not be checked into source control because it depends on the *.user* file.</span></span> <span data-ttu-id="15696-200">切不可将 .user 文件签入源控件，因为它可能包含敏感信息，且仅对一个用户和一台计算机有效。</span><span class="sxs-lookup"><span data-stu-id="15696-200">The *.user* file should never be checked into source control because it can contain sensitive information and it's only valid for one user and machine.</span></span>

<span data-ttu-id="15696-201">敏感信息（如发布密码）在每个用户/计算机级别上加密，并存储在 Properties/PublishProfiles/\<publish name>.pubxml.user 文件中。</span><span class="sxs-lookup"><span data-stu-id="15696-201">Sensitive information (like the publish password) is encrypted on a per user/machine level and stored in the *Properties/PublishProfiles/\<publish name>.pubxml.user* file.</span></span> <span data-ttu-id="15696-202">由于此文件可能包含敏感信息，因此，不应将其签入源控件。</span><span class="sxs-lookup"><span data-stu-id="15696-202">Because this file can contain sensitive information, it should **not** be checked into source control.</span></span>

<span data-ttu-id="15696-203">有关如何在 ASP.NET Core 上发布 Web 应用的概述，请参阅[发布和部署](index.md)。</span><span class="sxs-lookup"><span data-stu-id="15696-203">For an overview of how to publish a web app on ASP.NET Core see [Publishing and Deployment](index.md).</span></span> <span data-ttu-id="15696-204">[发布和部署](index.md)是 https://github.com/aspnet/websdk 上的一个开放源代码项目。</span><span class="sxs-lookup"><span data-stu-id="15696-204">[Publishing and Deployment](index.md) is an open source project at https://github.com/aspnet/websdk.</span></span>

 <span data-ttu-id="15696-205">`dotnet publish` 可以使用文件夹、Msdeploy 和 [KUDU](https://github.com/projectkudu/kudu/wiki) 发布配置文件：</span><span class="sxs-lookup"><span data-stu-id="15696-205">`dotnet publish` can use folder, Msdeploy, and [KUDU](https://github.com/projectkudu/kudu/wiki) publish profiles:</span></span>
 
<span data-ttu-id="15696-206">文件夹（跨平台工作）`dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>`</span><span class="sxs-lookup"><span data-stu-id="15696-206">Folder (works cross-platform) `dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>`</span></span>

<span data-ttu-id="15696-207">Msdeploy（当前仅适用于 Windows，因为 Msdeploy 不是跨平台的）：`dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>`</span><span class="sxs-lookup"><span data-stu-id="15696-207">Msdeploy (currently this only works in windows since msdeploy is not cross-platform): `dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>`</span></span>

<span data-ttu-id="15696-208">Msdeploy 包（当前仅适用于 Windows，因为 Msdeploy 不是跨平台的）：`dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>`</span><span class="sxs-lookup"><span data-stu-id="15696-208">Msdeploy package(currently this only works in windows since msdeploy is not cross-platform): `dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>`</span></span>

<span data-ttu-id="15696-209">在前面示例中，不会将 `deployonbuild` 传递到 `dotnet publish`。</span><span class="sxs-lookup"><span data-stu-id="15696-209">In the preceeding samples, **don’t** pass `deployonbuild` to `dotnet publish`.</span></span>

<span data-ttu-id="15696-210">有关详细信息，请参阅 [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish)</span><span class="sxs-lookup"><span data-stu-id="15696-210">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish)</span></span>

<span data-ttu-id="15696-211">`dotnet publish` 支持 KUDU API 从任何平台发布到 Azure。</span><span class="sxs-lookup"><span data-stu-id="15696-211">`dotnet publish` supports KUDU apis to publish to Azure from any platform.</span></span> <span data-ttu-id="15696-212">Visual Studio 发布不支持 KUDU API，但 websdk 支持其跨平台发布到 Azure。</span><span class="sxs-lookup"><span data-stu-id="15696-212">Visual Studio publish does support the KUDU APIs but it is supported by websdk for cross plat publish to Azure.</span></span>

<span data-ttu-id="15696-213">向“属性/PublishProfiles”文件夹添加包含以下内容的发布配置文件：</span><span class="sxs-lookup"><span data-stu-id="15696-213">Add a publish profile to *Properties/PublishProfiles* folder with the following content:</span></span>

```xml
<Project>
<PropertyGroup>
                <PublishProtocol>Kudu</PublishProtocol>
                <PublishSiteName>nodewebapp</PublishSiteName>
                <UserName>username</UserName>
                <Password>password</Password>
</PropertyGroup>
</Project>
```

<span data-ttu-id="15696-214">运行以下命令将会压缩发布内容并将其发布到使用 KUDU API 的 Azure。</span><span class="sxs-lookup"><span data-stu-id="15696-214">Running the following command will zip up the publish contents and publish it to Azure using the KUDU APIs.</span></span>

`dotnet publish /p:PublishProfile=Azure /p:Configuration=Release`

<span data-ttu-id="15696-215">使用发布配置文件时，请设置以下 MSBuild 属性：</span><span class="sxs-lookup"><span data-stu-id="15696-215">Set the following MSBuild properties when using a publish profile:</span></span>

- `DeployOnBuild=true`
- `PublishProfile=<Publish profile name>`

<span data-ttu-id="15696-216">例如，发布名为 FolderProfile 的配置文件时，可以执行以下命令之一。</span><span class="sxs-lookup"><span data-stu-id="15696-216">For example, when publishing with a profile named *FolderProfile* you can execute either of the commands below.</span></span>

- `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
- `msbuild      /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="15696-217">调用 `dotnet build` 时，它将调用 `msbuild` 来运行生成和发布过程。</span><span class="sxs-lookup"><span data-stu-id="15696-217">When you invoke `dotnet build` it will call `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="15696-218">在文件夹配置文件中传递时，调用 `dotnet build` 或 `msbuild` 基本无差别。</span><span class="sxs-lookup"><span data-stu-id="15696-218">Calling `dotnet build` or `msbuild` is essentially equivalent when you pass in a folder profile.</span></span> <span data-ttu-id="15696-219">在 Windows 上直接调用 MSBuild 时，将获得 MSBuild 的 .NET Framework 版本。</span><span class="sxs-lookup"><span data-stu-id="15696-219">When calling MSBuild directly on Windows you get the .NET Framework version of MSBuild.</span></span>  <span data-ttu-id="15696-220">MSDeploy 目前仅限于在 Windows 计算机上进行发布。</span><span class="sxs-lookup"><span data-stu-id="15696-220">MSDeploy is currently limited to Windows machines for publishing.</span></span> <span data-ttu-id="15696-221">在非文件夹配置文件上调用 `dotnet build` 时，会调用 MSBuild，并且 MSBuild 在非文件夹配置文件上使用 MSDeploy。</span><span class="sxs-lookup"><span data-stu-id="15696-221">Calling `dotnet build` on a non-folder profile invokes MSBuild, and MSBuild uses MSDeploy on non-folder profiles.</span></span> <span data-ttu-id="15696-222">在非文件夹配置文件上调用 `dotnet build` 时，会调用 MSBuild（使用 MSDeploy）并导致失败（即使在 Windows 平台上运行也是如此）。</span><span class="sxs-lookup"><span data-stu-id="15696-222">Calling `dotnet build` on a non-folder profile invokes MSBuild (using MSDeploy) and results in a failure (even when running on a Windows platform).</span></span> <span data-ttu-id="15696-223">若要使用非文件夹配置文件进行发布，请直接调用 MSBuild。</span><span class="sxs-lookup"><span data-stu-id="15696-223">To publish with a non-folder profile, call MSBuild directly.</span></span>

<span data-ttu-id="15696-224">以下文件夹发布配置文件通过 Visual Studio 创建，并被发布到网络共享：</span><span class="sxs-lookup"><span data-stu-id="15696-224">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

[!code-xml[Main](web-publishing-vs/sample/FolderProfile.pubxml?highlight=5,9,11,18)]

<span data-ttu-id="15696-225">请注意，`<LastUsedBuildConfiguration>` 已设置为 `Release`。</span><span class="sxs-lookup"><span data-stu-id="15696-225">Note `<LastUsedBuildConfiguration>` is set to `Release`.</span></span>  <span data-ttu-id="15696-226">从 Visual Studio 发布时，在启动发布过程后将使用该值设置 `<LastUsedBuildConfiguration>` 配置属性值。</span><span class="sxs-lookup"><span data-stu-id="15696-226">When publishing from Visual Studio, the `<LastUsedBuildConfiguration>` configuration property value is set using the value when the publish process is started.</span></span> <span data-ttu-id="15696-227">`<LastUsedBuildConfiguration>` 配置属性比较特殊，不应在导入的 MSBuild 文件中覆盖该属性。</span><span class="sxs-lookup"><span data-stu-id="15696-227">The `<LastUsedBuildConfiguration>` configuration property is special and shouldn’t be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="15696-228">可以从命令行覆盖此属性。</span><span class="sxs-lookup"><span data-stu-id="15696-228">You can override this property from the command line.</span></span> <span data-ttu-id="15696-229">例如: </span><span class="sxs-lookup"><span data-stu-id="15696-229">For example:</span></span>

`dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="15696-230">使用 MSBuild：</span><span class="sxs-lookup"><span data-stu-id="15696-230">Using MSBuild:</span></span>

`msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="15696-231">从命令行发布到 MSDeploy 终结点</span><span class="sxs-lookup"><span data-stu-id="15696-231">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="15696-232">如前所述，可以使用 `dotnet publish` 或 `msbuild` 命令进行发布。</span><span class="sxs-lookup"><span data-stu-id="15696-232">As previously mentioned, you can publish using `dotnet publish` or the `msbuild` command.</span></span> <span data-ttu-id="15696-233">`dotnet publish` 在 .NET Core 的上下文中运行。</span><span class="sxs-lookup"><span data-stu-id="15696-233">`dotnet publish` runs in the context of .NET Core.</span></span> <span data-ttu-id="15696-234">`msbuild` 需要 .NET framework，因此仅限于 Windows 环境。</span><span class="sxs-lookup"><span data-stu-id="15696-234">`msbuild` requires .NET framework, and is therefore limited to Windows environments.</span></span>

<span data-ttu-id="15696-235">使用 MSDeploy 发布的最简单的方法是，首先在 Visual Studio 2017 中创建发布配置文件，然后从命令行中使用配置文件。</span><span class="sxs-lookup"><span data-stu-id="15696-235">The easiest way to publish with MSDeploy is to first create a publish profile in Visual Studio 2017 and use the profile from the command line.</span></span>

<span data-ttu-id="15696-236">下面的示例中创建了一个 ASP.NET Core Web（使用 `dotnet new mvc`），并且通过 Visual Studio 添加了一个 Azure 发布配置文件。</span><span class="sxs-lookup"><span data-stu-id="15696-236">In the following sample, an ASP.NET Core web app is created ( using `dotnet new mvc`) and an Azure publish profile is added with Visual Studio.</span></span>

<span data-ttu-id="15696-237">从 VS 2017 开发人员命令提示符中运行 `msbuild`。</span><span class="sxs-lookup"><span data-stu-id="15696-237">You run `msbuild` from a **Developer Command Prompt for VS 2017**.</span></span> <span data-ttu-id="15696-238">开发人员命令提示符在其路径中将具有正确的 msbuild.exe，并会设置一些 MSBuild 变量。</span><span class="sxs-lookup"><span data-stu-id="15696-238">The Developer Command Prompt will have the correct *msbuild.exe* in its path and set some MSBuild variables.</span></span>

<span data-ttu-id="15696-239">MSBuild 使用以下语法：</span><span class="sxs-lookup"><span data-stu-id="15696-239">MSBuild uses the following syntax:</span></span>

`msbuild <path-to-project-file> /p:DeployOnBuild=true /p:PublishProfile=<Publish Profile>  /p:Username=<USERNAME> /p:Password=<PASSWORD>`

<span data-ttu-id="15696-240">可以从 \<Publish name>.PublishSettings 文件获取 `Password`。</span><span class="sxs-lookup"><span data-stu-id="15696-240">You can get the `Password` from the *\<Publish name>.PublishSettings* file.</span></span> <span data-ttu-id="15696-241">可以在以下位置下载 .PublishSettings 文件：</span><span class="sxs-lookup"><span data-stu-id="15696-241">You can download the *.PublishSettings* file from:</span></span>

- <span data-ttu-id="15696-242">解决方案资源管理器：右键单击 Web 应用并选择“下载发布配置文件”。</span><span class="sxs-lookup"><span data-stu-id="15696-242">Solution Explorer: Right click on the Web App and select **Download Publish Profile**.</span></span>
- <span data-ttu-id="15696-243">Azure 管理门户：从 Web 应用边栏选项卡选择“获取发布配置文件”。</span><span class="sxs-lookup"><span data-stu-id="15696-243">The Azure Management Portal: Select **Get publish profile** from the  Web App blade.</span></span>

<span data-ttu-id="15696-244">可在发布配置文件中找到 `Username`。</span><span class="sxs-lookup"><span data-stu-id="15696-244">`Username` can be found in the publish profile.</span></span>

<span data-ttu-id="15696-245">以下示例使用“Web11112 - Web 部署”发布配置文件：</span><span class="sxs-lookup"><span data-stu-id="15696-245">The following sample uses the "Web11112 - Web Deploy" publish profile:</span></span>

```console
msbuild "C:\Webs\Web1\Web1.csproj" /p:DeployOnBuild=true
 /p:PublishProfile="Web11112 - Web Deploy"  /p:Username="$Web11112"
 /p:Password="<password removed>"
```

## <a name="excluding-files"></a><span data-ttu-id="15696-246">排除文件</span><span class="sxs-lookup"><span data-stu-id="15696-246">Excluding files</span></span>

<span data-ttu-id="15696-247">在发布 ASP.NET Core Web 应用时，生成项目和 wwwroot 文件夹的内容包括在内。</span><span class="sxs-lookup"><span data-stu-id="15696-247">When publishing ASP.NET Core web apps, the build artifacts and contents of the *wwwroot* folder are included.</span></span> <span data-ttu-id="15696-248">`msbuild` 支持[通配模式](https://gruntjs.com/configuring-tasks#globbing-patterns)。</span><span class="sxs-lookup"><span data-stu-id="15696-248">`msbuild` supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="15696-249">例如，以下 `<Content>` 元素标记将从 wwwroot/content 文件夹和所有子文件夹中排除所有文本 (.txt) 文件。</span><span class="sxs-lookup"><span data-stu-id="15696-249">For example, the following `<Content>` element markup will exclude all text (*.txt*) files from the *wwwroot/content* folder and all its subfolders.</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="15696-250">可以将上面的标记添加到发布配置文件或 .csproj 文件。</span><span class="sxs-lookup"><span data-stu-id="15696-250">The markup above can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="15696-251">添加到 .csproj 文件时，会将该规则添加到项目中的所有发布配置文件中。</span><span class="sxs-lookup"><span data-stu-id="15696-251">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="15696-252">以下 `<MsDeploySkipRules>` 元素标记不包括 wwwroot/content 文件夹中的所有文件：</span><span class="sxs-lookup"><span data-stu-id="15696-252">The following `<MsDeploySkipRules>` element markup exludes all files from the *wwwroot/content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="15696-253">`<MsDeploySkipRules>` 不会从部署站点中删除跳过目标。</span><span class="sxs-lookup"><span data-stu-id="15696-253">`<MsDeploySkipRules>`  will not delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="15696-254">将从部署站点中删除 `<Content>` 目标文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="15696-254">`<Content>` targeted files and folders will be deleted from the deployment site.</span></span> <span data-ttu-id="15696-255">例如，假设你已使用以下文件部署 Web 应用：</span><span class="sxs-lookup"><span data-stu-id="15696-255">For example, suppose you had deployed a web app with the following files:</span></span>

- <span data-ttu-id="15696-256">*Views/Home/About1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="15696-256">*Views/Home/About1.cshtml*</span></span>
- <span data-ttu-id="15696-257">*Views/Home/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="15696-257">*Views/Home/About2.cshtml*</span></span>
- <span data-ttu-id="15696-258">*Views/Home/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="15696-258">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="15696-259">如果添加了以下 `<MsDeploySkipRules>` 标记，则不会在部署站点上删除这些文件。</span><span class="sxs-lookup"><span data-stu-id="15696-259">If you added the following `<MsDeploySkipRules>` markup, those files would not be deleted on the deployment site.</span></span>

``` xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="15696-260">上面显示的 `<MsDeploySkipRules>` 标记阻止部署跳过文件，但会在部署后删除这些文件。</span><span class="sxs-lookup"><span data-stu-id="15696-260">The `<MsDeploySkipRules>` markup shown above prevents the *skipped* files from being depoyed, but will not delete those files once they are deployed.</span></span>

<span data-ttu-id="15696-261">以下 `<Content>` 标记将在部署站点中删除目标文件：</span><span class="sxs-lookup"><span data-stu-id="15696-261">The following `<Content>` markup would delete the targeted files at the deployment site:</span></span>

``` xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="15696-262">将命令行部署与上面的 `<Content>` 标记结合使用会生成类似以下内容的输出：</span><span class="sxs-lookup"><span data-stu-id="15696-262">Using command line deployment with the `<Content>` markup above would result in output similar to the following:</span></span>

``` console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\netcoreapp1.1\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="including-files"></a><span data-ttu-id="15696-263">包含文件</span><span class="sxs-lookup"><span data-stu-id="15696-263">Including files</span></span>

<span data-ttu-id="15696-264">可以使用以下标记将项目目录外的 images 文件夹包含到发布站点的 wwwroot/images 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="15696-264">The following markup can be used to include an *images* folder outside the project directory to the *wwwroot/images* folder of the publish site.</span></span>

``` xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotnetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotnetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="15696-265">可以将标记添加到 .csproj 文件或发布配置文件。</span><span class="sxs-lookup"><span data-stu-id="15696-265">The markup can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="15696-266">如果将其添加到 .csproj 文件，它将包含在项目的每个发布配置文件中。</span><span class="sxs-lookup"><span data-stu-id="15696-266">If it's added to the *.csproj* file, it will be included in each publish profile in the project.</span></span>

<span data-ttu-id="15696-267">以下突出显示的标记显示如何：</span><span class="sxs-lookup"><span data-stu-id="15696-267">The following highlighted markup shows how to:</span></span>

* <span data-ttu-id="15696-268">将文件从项目外部复制到 wwwroot 文件夹。</span><span class="sxs-lookup"><span data-stu-id="15696-268">Copy a file from outside the project into the *wwwroot* folder.</span></span>
* <span data-ttu-id="15696-269">排除 wwwroot\Content 文件夹。</span><span class="sxs-lookup"><span data-stu-id="15696-269">Exclude the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="15696-270">排除 Views\Home\About2.cshtml。</span><span class="sxs-lookup"><span data-stu-id="15696-270">Exclude *Views\Home\About2.cshtml*.</span></span>

[!code-xml[Main](web-publishing-vs/sample/FolderProfile2.pubxml?highlight=21-29)]

<span data-ttu-id="15696-271">请参阅 [WebSDK 自述文件](https://github.com/aspnet/websdk)，了解更多部署示例。</span><span class="sxs-lookup"><span data-stu-id="15696-271">See the [WebSDK Readme](https://github.com/aspnet/websdk) for more deployment samples.</span></span>

### <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="15696-272">在发布前或发布后运行目标</span><span class="sxs-lookup"><span data-stu-id="15696-272">Run a target before or after publishing</span></span>

<span data-ttu-id="15696-273">内置 `BeforePublish` 和 `AfterPublish` 目标可用于在发布目标前/后执行目标。</span><span class="sxs-lookup"><span data-stu-id="15696-273">The builtin `BeforePublish` and `AfterPublish` targets can be used to execute a target before or after the publish target.</span></span> <span data-ttu-id="15696-274">可以将以下标记添加到发布配置文件中，以便在发布前后将消息记录到控制台输出：</span><span class="sxs-lookup"><span data-stu-id="15696-274">The following markup can be added to the publish profile to log messages to the console output before and after publishing:</span></span>

``` xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="the-kudu-service"></a><span data-ttu-id="15696-275">Kudu 服务</span><span class="sxs-lookup"><span data-stu-id="15696-275">The Kudu service</span></span>

<span data-ttu-id="15696-276">若要查看 Azure Web 应用上的文件，请使用 [kudu 服务](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)。</span><span class="sxs-lookup"><span data-stu-id="15696-276">To view the files on your Azure Web App, use the [kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="15696-277">将 `scm` 令牌追加到名称或你的 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="15696-277">Append the `scm` token to the name or your Web App.</span></span> <span data-ttu-id="15696-278">例如: </span><span class="sxs-lookup"><span data-stu-id="15696-278">For example:</span></span>

| <span data-ttu-id="15696-279">URL</span><span class="sxs-lookup"><span data-stu-id="15696-279">URL</span></span>               | <span data-ttu-id="15696-280">结果</span><span class="sxs-lookup"><span data-stu-id="15696-280">Result</span></span>|
| ----------------- | ------------ |
| `http://mysite.azurewebsites.net/` | <span data-ttu-id="15696-281">Web 应用</span><span class="sxs-lookup"><span data-stu-id="15696-281">Web App</span></span> |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="15696-282">Kudu 服务</span><span class="sxs-lookup"><span data-stu-id="15696-282">Kudu sevice</span></span> |

<span data-ttu-id="15696-283">选择[调试控制台](https://github.com/projectkudu/kudu/wiki/Kudu-console)菜单项来查看/编辑/删除/添加文件。</span><span class="sxs-lookup"><span data-stu-id="15696-283">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view/edit/delete/add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="15696-284">其他资源</span><span class="sxs-lookup"><span data-stu-id="15696-284">Additional resources</span></span>

- <span data-ttu-id="15696-285">[Web 部署](https://www.iis.net/downloads/microsoft/web-deploy) (msdeploy) 简化了 Web 应用程序和网站到 IIS 服务器的部署。</span><span class="sxs-lookup"><span data-stu-id="15696-285">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy)  (msdeploy) simplifies deployment of Web applications and Web sites to IIS servers.</span></span>

- <span data-ttu-id="15696-286">[https://github.com/aspnet/websdk](https://github.com/aspnet/websdk/issues)：文件问题和部署的请求功能。</span><span class="sxs-lookup"><span data-stu-id="15696-286">[https://github.com/aspnet/websdk](https://github.com/aspnet/websdk/issues): File issues and request features for deployment.</span></span>
