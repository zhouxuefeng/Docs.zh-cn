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
# <a name="getting-started-with-aspnet-core-and-entity-framework-6"></a>开始使用 ASP.NET Core 和 Entity Framework 6

通过[Paweł Grudzień](https://github.com/pgrudzien12)， [Damien Pontifex](https://github.com/DamienPontifex)，和[Tom Dykstra](https://github.com/tdykstra)

这篇文章演示如何在 ASP.NET Core 应用程序中使用 Entity Framework 6。

## <a name="overview"></a>概述

若要使用 Entity Framework 6，你的项目具有编译针对.NET Framework 中，因为 Entity Framework 6 不支持.NET 核心。 如果您需要跨平台功能将需要升级到[实体框架核心](https://docs.microsoft.com/ef/)。

在 ASP.NET Core 应用程序中使用 Entity Framework 6 的建议的方法是将从 EF6 上下文和类库中的模型类项目面向 framework 全功能版。 添加对类库中 ASP.NET Core 项目的引用。 请参见示例[具有从 EF6 和 ASP.NET Core 项目的 Visual Studio 解决方案](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/entity-framework-6/sample/)。

不能将从 EF6 上下文放在 ASP.NET Core 项目，因为.NET 核心项目不支持的所有功能，如命令从 EF6 *Enable-migrations*需要。

无论何种项目类型在其中找到从 EF6 上下文，仅从 EF6 命令行工具使用 EF6 上下文。 例如，`Scaffold-DbContext`选项仅适用于实体框架核心。 如果你需要执行到从 EF6 模型反向工程的数据库的影响，请参阅[到现有数据库 Code First](https://msdn.microsoft.com/jj200620)。

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a>引用完整框架和 ASP.NET Core 项目中的从 EF6

ASP.NET 核心项目需要引用.NET framework 和 ef6 更高版本。 例如， *.csproj* ASP.NET Core 项目文件将类似于下面的示例 （显示的文件的仅相关部分）。

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]

如果你要创建新项目，使用**ASP.NET 核心 Web 应用程序 (.NET Framework)**模板。

## <a name="handle-connection-strings"></a>处理连接字符串

你将使用 EF6 类库项目中从 EF6 命令行工具需要默认构造函数，因此它们可以实例化上下文。 但你可能需要指定要在 ASP.NET Core 项目中，在此情况下使用上下文构造函数的连接字符串必须具有允许你在连接字符串中传递的参数。 下面是一个示例。

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]

由于从 EF6 上下文不具有无参数构造函数，因此你 ef6 更高版本的项目具有提供的一个实现[IDbContextFactory](https://msdn.microsoft.com/library/hh506876)。 从 EF6 命令行工具可将查找和使用该实现，因此它们可以实例化上下文。 下面是一个示例。

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]

在此示例代码中，`IDbContextFactory`实现将硬编码的连接字符串中传递。 这是命令行工具将使用的连接字符串。 你将想要实施策略以确保类库使用相同的连接字符串，调用应用程序使用。 例如，你无法从这两个项目中的环境变量中获取的值。

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a>设置 ASP.NET Core 项目中的依赖关系注入

在核心项目的*Startup.cs*文件中，设置的依赖关系注入 (DI) 从 EF6 上下文`ConfigureServices`。 EF 上下文对象应为每个请求生存期的范围。

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

然后可以使用 DI 在控制器中获取上下文的实例。 代码将类似于你将编写为 EF 核心上下文：

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]

## <a name="sample-application"></a>示例应用程序

有关工作示例应用程序，请参阅[示例 Visual Studio 解决方案](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/entity-framework-6/sample/)附带这篇文章。

通过 Visual Studio 中的以下步骤，可以从头创建此示例：

* 创建一个解决方案。

* **添加新项目 > Web > ASP.NET 核心 Web 应用程序 (.NET Framework)**

* **添加新项目 > Windows 经典桌面 > 类库 (.NET Framework)**

* 在**程序包管理器控制台**(PMC) 对于这两个项目中，运行命令`Install-Package Entityframework`。

* 在类库项目，创建数据模型类和上下文类，并实现`IDbContextFactory`。

* 在类库项目的 PMC，运行命令`Enable-Migrations`和`Add-Migration Initial`。 如果已将 ASP.NET Core 项目设置为启动项目，添加`-StartupProjectName EF6`对这些命令。

* 在核心项目中，添加对类库项目的项目引用。

* 在核心项目中，在*Startup.cs*，注册 DI 上下文。

* 在核心项目中，在*appsettings.json*，添加连接字符串。

* 在核心项目中，添加一个控制器和视图，以验证你可以读取和写入数据。 （请注意，ASP.NET 核心 MVC 基架使用 EF6 上下文从类库引用不起作用。）

## <a name="summary"></a>摘要

本文章提供了 ASP.NET Core 应用程序中使用 Entity Framework 6 的基本指南。

## <a name="additional-resources"></a>其他资源

* [实体框架的基于代码的配置](https://msdn.microsoft.com/data/jj680699.aspx)
