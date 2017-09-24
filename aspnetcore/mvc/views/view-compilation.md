---
title: "Razor 视图编译和预编译"
author: rick-anderson
description: "参考文档，以解释如何启用 MVC Razor 视图编译和 ASP.NET Core 应用程序中的预编译。"
keywords: "ASP.NET 核心，Razor 视图编译、 Razor 预编译、 Razor 预编译"
ms.author: riande
manager: wpickett
ms.date: 08/16/2017
ms.topic: article
ms.assetid: ab4705b7-1638-1638-bc97-ea7f292fe92a
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/view-compilation
ms.openlocfilehash: bfee2e5e8f71c99465be79589a77f0e173097b23
ms.sourcegitcommit: 8005eb4051e568d88ee58d48424f39916052e6e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2017
---
# <a name="razor-view-compilation-and-precompilation-in-aspnet-core"></a>Razor 视图编译和 ASP.NET Core 中的预编译

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

调用视图时，则 razor 视图是在运行时编译。 ASP.NET 核心 1.1.0 和更高版本可以根据需要编译 Razor 视图以及如何将它们部署与应用&mdash;称为预编译的过程。 默认情况下，ASP.NET Core 2.x 项目模板启用预编译。

> [!NOTE]
> 在执行时，razor 视图预编译是当前不可用[独立的部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) ASP.NET 核心 2.0 中。 2.1 版本时，此功能将被用于 Scd。 有关详细信息，请参阅[视图编译失败适用于 Windows 上的 Linux 跨编译时](https://github.com/aspnet/MvcPrecompilation/issues/102)。

预编译的注意事项：

* 预编译视图生成较小的已发布的捆绑和更快的启动时间。
* 预编译视图后，无法编辑 Razor 文件。 编辑的视图将不会出现在已发布的捆绑包。 

部署预编译的视图：

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

如果你的项目以.NET Framework 为目标，包括对的包引用`Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`:

```xml
<PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.ViewCompilation" Version="2.0.0" PrivateAssets="All" />
```

如果你的项目面向.NET 核心，不不需要进行任何更改。

ASP.NET 核心 2.x 项目模板隐式设置`MvcRazorCompileOnPublish`到`true`默认情况下，这意味着此节点可以安全地删除从*.csproj*文件。 如果想要为显式，没有设置任何影响`MvcRazorCompileOnPublish`属性`true`。 以下*.csproj*示例重点介绍了此设置：

[!code-xml[Main](view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=5)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

设置`MvcRazorCompileOnPublish`到`true`，并包含对的包引用`Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`。 以下*.csproj*示例重点介绍了这些设置：

[!code-xml[Main](view-compilation\sample\MvcRazorCompileOnPublish.csproj?highlight=5,12)]

---
