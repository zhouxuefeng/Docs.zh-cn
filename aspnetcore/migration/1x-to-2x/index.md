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
# <a name="migrating-from-aspnet-core-1x-to-aspnet-core-20"></a>从 ASP.NET Core 1.x 迁移到 ASP.NET Core 2.0

作者：[Scott Addie](https://github.com/scottaddie)

本文将演示如何将现有 ASP.NET Core 1.x 项目更新到 ASP.NET Core 2.0。 通过将应用程序迁移到 ASP.NET Core 2.0，可利用[大量新功能和改进功能](https://docs.microsoft.com/aspnet/core/aspnetcore-2.0)。 

现有 ASP.NET Core 1.x 应用程序基于版本特定的项目模板。 随着 ASP.NET Core 框架不断演变，其中的项目模板和起始代码也在变化。 除了更新 ASP.NET Core 框架外，还需要为应用程序更新代码。

<a name="prerequisites"></a>

## <a name="prerequisites"></a>先决条件
请参阅 [ASP.NET Core 入门](xref:getting-started)。

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a>更新目标框架名字对象 (TFM)
面向 .NET Core 的项目需使用大于或等于 .NET Core 2.0 版本的 [TFM](/dotnet/standard/frameworks#referring-to-frameworks)。 在“.csproj”文件中搜索 `<TargetFramework>` 节点，并将其内部文本替换为 `netcoreapp2.0`：

[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=3)]

面向 .NET Framework 的项目需使用大于或等于 .NET Framework 4.6.1 版本的 TFM。 在“.csproj”文件中搜索 `<TargetFramework>` 节点，并将其内部文本替换为 `net461`：

[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> 相比于 .NET Core 1.x，.NET Core 2.0 提供更多的外围应用。 如果仅因为 .NET Core 1.x 中缺少 API 而要面向 .NET Framework，则定向于 .NET Core 2.0 可能有用。

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a>在 global.json 中更新 .NET Core SDK 版本
如果解决方案依靠 [global.json](https://docs.microsoft.com/dotnet/core/tools/global-json) 文件来定向于特定 .NET Core SDK 版本，请更新其 `version` 属性以使用计算机上安装的 2.0 版本：

[!code-json[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a>更新包引用
1.x 项目中的“.csproj”文件列出了该项目使用的每个 NuGet 包。

在面向 .NET Core 2.0 的 ASP.NET Core 2.0 项目中，“.csproj”文件中的单个 [metapackage](xref:fundamentals/metapackage) 引用将替换包的集合：

[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=9-11)]

该元包中具备 ASP.NET Core 2.0 和 Entity Framework Core 2.0 的所有功能。

面向 .NET Framework 的 ASP.NET Core 2.0 项目应继续引用单个 NuGet 包。 将每个 `<PackageReference />` 节点的 `Version` 特性更新至 2.0.0。

例如，下述列表列出了面向 .NET Framework 的典型 ASP.NET Core 2.0 项目中使用的 `<PackageReference />` 节点：

[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a>更新 .NET Core CLI 工具
在“.csproj”文件中，将每个 `<DotNetCliToolReference />` 节点的 `Version` 特性更新至 2.0.0。

例如，下述列表列出了面向 .NET Core 2.0 的典型 ASP.NET Core 2.0 项目中使用的 CLI 工具：

[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=13-17)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a>重命名“包目标回退”属性
1.x 项目的“.csproj”文件使用了 `PackageTargetFallback` 节点和变量：

[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App.csproj?range=5)]

将节点和变量重命名为 `AssetTargetFallback`：

[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=5)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a>更新 Program.cs 中的 Main 方法
在 1.x 项目中，“Program.cs”的 `Main` 方法如下所示：

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

在 2.0 项目中，简化了“Program.cs”的 `Main` 方法：

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/Program.cs?highlight=8-11)]

强烈建议采用新的 2.0 模式，[Entity Framework Core 迁移](xref:data/ef-mvc/migrations) 等产品功能需要此模式才能正常运行。 例如，从“包管理器控制台”窗口运行 `Update-Database`，或从命令行（位于转换为 ASP.NET Core 2.0 的项目上）运行 `dotnet ef database update`时，都会生成以下错误：

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="view-compilation"></a>

## <a name="review-your-razor-view-compilation-setting"></a>查看 Razor 视图编译设置
加快应用程序启动速度和缩小已发布的捆绑包至关重要。 为此，ASP.NET Core 2.0 中默认启用 [Razor 视图编译](xref:mvc/views/view-compilation)。

无需再将 `MvcRazorCompileOnPublish` 属性设置为 true。 若不禁用视图编译，可能会从“.csproj”文件中删除此属性。

以 .NET Framework 为目标时，仍需显式引用“.csproj”文件中的 [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) NuGet 包：

[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a>依靠 Application Insights“启动”功能
能够轻松设置应用程序性能检测非常重要。 现可依靠 Visual Studio 2017 工具中推出的新的 [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)“启动”功能。

Visual Studio 2017 中创建的 ASP.NET Core 1.1 项目默认添加 Application Insights。 若不直接使用 Application Insights SDK，则除了执行“Program.cs”和“Startup.cs”，还请执行以下步骤：

1. 从“.csproj”文件中删除以下 `<PackageReference />` 节点：
    
    [!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App.csproj?range=10)]

2. 从“Program.cs”中删除 `UseApplicationInsights` 扩展方法调用：

    [!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. 从“_Layout.cshtml”中删除 Application Insights 客户端 API 调用。 它会比较以下两行代码：

    [!code-cshtml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Views/Shared/_Layout.cshtml?range=1,19)]

若要直接使用 Application Insights SDK，请继续此操作。 2.0 [元包](xref:fundamentals/metapackage)中具备最新版本的 Application Insights，因此如果引用较旧版本，将出现包降级错误。

<a name="auth-and-identity"></a>

## <a name="adopt-authentication--identity-improvements"></a>采用身份验证/标识改进
ASP.NET Core 2.0 具有新的身份验证模型和大量针对 ASP.NET Core 标识的重大更改。 如果在启用单个用户帐户的情况下创建项目，或者已手动添加身份验证或标识，请参阅[将身份验证和标识迁移到 ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x)。

## <a name="additional-resources"></a>其他资源
- [ASP.NET Core 2.0 中的重大更改](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
