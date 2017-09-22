---
title: "Getting Started with ASP.NET Core 和 Entity Framework 6"
author: tdykstra
description: "这篇文章演示如何在 ASP.NET Core 应用程序中使用 Entity Framework 6。"
keywords: "ASP.NET 核心，实体框架中，EF 6"
ms.author: tdykstra
manager: wpickett
ms.date: 02/24/2017
ms.topic: article
ms.assetid: 016cc836-4c43-45a4-b9a7-9efaf53350df
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/entity-framework-6
ms.openlocfilehash: 8abec95c591f20069e20eec55fd21503e74f8606
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="getting-started-with-aspnet-core-and-entity-framework-6"></a><span data-ttu-id="7f4f9-104">开始使用 ASP.NET Core 和 Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="7f4f9-104">Getting started with ASP.NET Core and Entity Framework 6</span></span>

<span data-ttu-id="7f4f9-105">通过[Paweł Grudzień](https://github.com/pgrudzien12)， [Damien Pontifex](https://github.com/DamienPontifex)，和[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="7f4f9-105">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="7f4f9-106">这篇文章演示如何在 ASP.NET Core 应用程序中使用 Entity Framework 6。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-106">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>

## <a name="overview"></a><span data-ttu-id="7f4f9-107">概述</span><span class="sxs-lookup"><span data-stu-id="7f4f9-107">Overview</span></span>

<span data-ttu-id="7f4f9-108">若要使用 Entity Framework 6，你的项目具有编译针对.NET Framework 中，因为 Entity Framework 6 不支持.NET 核心。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-108">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 does not support .NET Core.</span></span> <span data-ttu-id="7f4f9-109">如果您需要跨平台功能将需要升级到[实体框架核心](https://docs.microsoft.com/ef/)。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-109">If you need cross-platform features you will need to upgrade to [Entity Framework Core](https://docs.microsoft.com/ef/).</span></span>

<span data-ttu-id="7f4f9-110">在 ASP.NET Core 应用程序中使用 Entity Framework 6 的建议的方法是将从 EF6 上下文和类库中的模型类项目面向 framework 全功能版。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-110">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets the full framework.</span></span> <span data-ttu-id="7f4f9-111">添加对类库中 ASP.NET Core 项目的引用。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-111">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="7f4f9-112">请参见示例[具有从 EF6 和 ASP.NET Core 项目的 Visual Studio 解决方案](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/entity-framework-6/sample/)。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-112">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span></span>

<span data-ttu-id="7f4f9-113">不能将从 EF6 上下文放在 ASP.NET Core 项目，因为.NET 核心项目不支持的所有功能，如命令从 EF6 *Enable-migrations*需要。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-113">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>

<span data-ttu-id="7f4f9-114">无论何种项目类型在其中找到从 EF6 上下文，仅从 EF6 命令行工具使用 EF6 上下文。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-114">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="7f4f9-115">例如，`Scaffold-DbContext`选项仅适用于实体框架核心。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-115">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="7f4f9-116">如果你需要执行到从 EF6 模型反向工程的数据库的影响，请参阅[到现有数据库 Code First](https://msdn.microsoft.com/jj200620)。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-116">If you need to do reverse engineering of a database into an EF6 model, see [Code First to an Existing Database](https://msdn.microsoft.com/jj200620).</span></span>

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="7f4f9-117">引用完整框架和 ASP.NET Core 项目中的从 EF6</span><span class="sxs-lookup"><span data-stu-id="7f4f9-117">Reference full framework and EF6 in the ASP.NET Core project</span></span>

<span data-ttu-id="7f4f9-118">ASP.NET 核心项目需要引用.NET framework 和 ef6 更高版本。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-118">Your ASP.NET Core project needs to reference .NET framework and EF6.</span></span> <span data-ttu-id="7f4f9-119">例如， *.csproj* ASP.NET Core 项目文件将类似于下面的示例 （显示的文件的仅相关部分）。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-119">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]

<span data-ttu-id="7f4f9-120">如果你要创建新项目，使用**ASP.NET 核心 Web 应用程序 (.NET Framework)**模板。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-120">If you’re creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>

## <a name="handle-connection-strings"></a><span data-ttu-id="7f4f9-121">处理连接字符串</span><span class="sxs-lookup"><span data-stu-id="7f4f9-121">Handle connection strings</span></span>

<span data-ttu-id="7f4f9-122">你将使用 EF6 类库项目中从 EF6 命令行工具需要默认构造函数，因此它们可以实例化上下文。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-122">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="7f4f9-123">但你可能需要指定要在 ASP.NET Core 项目中，在此情况下使用上下文构造函数的连接字符串必须具有允许你在连接字符串中传递的参数。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-123">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="7f4f9-124">下面是一个示例。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-124">Here's an example.</span></span>

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]

<span data-ttu-id="7f4f9-125">由于从 EF6 上下文不具有无参数构造函数，因此你 ef6 更高版本的项目具有提供的一个实现[IDbContextFactory](https://msdn.microsoft.com/library/hh506876)。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-125">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span></span> <span data-ttu-id="7f4f9-126">从 EF6 命令行工具可将查找和使用该实现，因此它们可以实例化上下文。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-126">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="7f4f9-127">下面是一个示例。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-127">Here's an example.</span></span>

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]

<span data-ttu-id="7f4f9-128">在此示例代码中，`IDbContextFactory`实现将硬编码的连接字符串中传递。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-128">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="7f4f9-129">这是命令行工具将使用的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-129">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="7f4f9-130">你将想要实施策略以确保类库使用相同的连接字符串，调用应用程序使用。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-130">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="7f4f9-131">例如，你无法从这两个项目中的环境变量中获取的值。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-131">For example, you could get the value from an environment variable in both projects.</span></span>

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="7f4f9-132">设置 ASP.NET Core 项目中的依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="7f4f9-132">Set up dependency injection in the ASP.NET Core project</span></span>

<span data-ttu-id="7f4f9-133">在核心项目的*Startup.cs*文件中，设置的依赖关系注入 (DI) 从 EF6 上下文`ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-133">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="7f4f9-134">EF 上下文对象应为每个请求生存期的范围。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-134">EF context objects should be scoped for a per-request lifetime.</span></span>

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

<span data-ttu-id="7f4f9-135">然后可以使用 DI 在控制器中获取上下文的实例。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-135">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="7f4f9-136">代码将类似于你将编写为 EF 核心上下文：</span><span class="sxs-lookup"><span data-stu-id="7f4f9-136">The code is similar to what you'd write for an EF Core context:</span></span>

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]

## <a name="sample-application"></a><span data-ttu-id="7f4f9-137">示例应用程序</span><span class="sxs-lookup"><span data-stu-id="7f4f9-137">Sample application</span></span>

<span data-ttu-id="7f4f9-138">有关工作示例应用程序，请参阅[示例 Visual Studio 解决方案](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/entity-framework-6/sample/)附带这篇文章。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-138">For a working sample application, see the [sample Visual Studio solution](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>

<span data-ttu-id="7f4f9-139">通过 Visual Studio 中的以下步骤，可以从头创建此示例：</span><span class="sxs-lookup"><span data-stu-id="7f4f9-139">This sample can be created from scratch by the following steps in Visual Studio:</span></span>

* <span data-ttu-id="7f4f9-140">创建一个解决方案。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-140">Create a solution.</span></span>

* <span data-ttu-id="7f4f9-141">**添加新项目 > Web > ASP.NET 核心 Web 应用程序 (.NET Framework)**</span><span class="sxs-lookup"><span data-stu-id="7f4f9-141">**Add New Project > Web > ASP.NET Core Web Application (.NET Framework)**</span></span>

* <span data-ttu-id="7f4f9-142">**添加新项目 > Windows 经典桌面 > 类库 (.NET Framework)**</span><span class="sxs-lookup"><span data-stu-id="7f4f9-142">**Add New Project > Windows Classic Desktop > Class Library (.NET Framework)**</span></span>

* <span data-ttu-id="7f4f9-143">在**程序包管理器控制台**(PMC) 对于这两个项目中，运行命令`Install-Package Entityframework`。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-143">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>

* <span data-ttu-id="7f4f9-144">在类库项目，创建数据模型类和上下文类，并实现`IDbContextFactory`。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-144">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>

* <span data-ttu-id="7f4f9-145">在类库项目的 PMC，运行命令`Enable-Migrations`和`Add-Migration Initial`。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-145">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="7f4f9-146">如果已将 ASP.NET Core 项目设置为启动项目，添加`-StartupProjectName EF6`对这些命令。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-146">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span>

* <span data-ttu-id="7f4f9-147">在核心项目中，添加对类库项目的项目引用。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-147">In the Core project, add a project reference to the class library project.</span></span>

* <span data-ttu-id="7f4f9-148">在核心项目中，在*Startup.cs*，注册 DI 上下文。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-148">In the Core project, in *Startup.cs*, register the context for DI.</span></span>

* <span data-ttu-id="7f4f9-149">在核心项目中，在*appsettings.json*，添加连接字符串。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-149">In the Core project, in *appsettings.json*, add the connection string.</span></span>

* <span data-ttu-id="7f4f9-150">在核心项目中，添加一个控制器和视图，以验证你可以读取和写入数据。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-150">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="7f4f9-151">（请注意，ASP.NET 核心 MVC 基架使用 EF6 上下文从类库引用不起作用。）</span><span class="sxs-lookup"><span data-stu-id="7f4f9-151">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

## <a name="summary"></a><span data-ttu-id="7f4f9-152">摘要</span><span class="sxs-lookup"><span data-stu-id="7f4f9-152">Summary</span></span>

<span data-ttu-id="7f4f9-153">本文章提供了 ASP.NET Core 应用程序中使用 Entity Framework 6 的基本指南。</span><span class="sxs-lookup"><span data-stu-id="7f4f9-153">This article has provided basic guidance for using Entity Framework 6 in an ASP.NET Core application.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7f4f9-154">其他资源</span><span class="sxs-lookup"><span data-stu-id="7f4f9-154">Additional Resources</span></span>

* [<span data-ttu-id="7f4f9-155">实体框架的基于代码的配置</span><span class="sxs-lookup"><span data-stu-id="7f4f9-155">Entity Framework - Code-Based Configuration</span></span>](https://msdn.microsoft.com/data/jj680699.aspx)
