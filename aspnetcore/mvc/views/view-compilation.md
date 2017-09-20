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
ms.openlocfilehash: 0cb61315916d1b38f7cab3339e150c446fb69d98
ms.sourcegitcommit: 74a8ad9c1ba5c155d7c4303e67632a0922c38e86
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2017
---
# <a name="razor-view-compilation-and-precompilation-in-aspnet-core"></a><span data-ttu-id="c9326-104">Razor 视图编译和 ASP.NET Core 中的预编译</span><span class="sxs-lookup"><span data-stu-id="c9326-104">Razor view compilation and precompilation in ASP.NET Core</span></span>

<span data-ttu-id="c9326-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c9326-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c9326-106">调用视图时，则 razor 视图是在运行时编译。</span><span class="sxs-lookup"><span data-stu-id="c9326-106">Razor views are compiled at runtime when the view is invoked.</span></span> <span data-ttu-id="c9326-107">ASP.NET 核心 1.1.0 和更高版本可以根据需要编译 Razor 视图以及如何将它们部署与应用&mdash;称为预编译的过程。</span><span class="sxs-lookup"><span data-stu-id="c9326-107">ASP.NET Core 1.1.0 and higher can optionally compile Razor views and deploy them with the app &mdash; a process known as precompilation.</span></span> <span data-ttu-id="c9326-108">默认情况下，ASP.NET Core 2.x 项目模板启用预编译。</span><span class="sxs-lookup"><span data-stu-id="c9326-108">The ASP.NET Core 2.x project templates enable precompilation by default.</span></span>

> [!NOTE]
> <span data-ttu-id="c9326-109">如果执行操作，razor 视图预编译将不可用[Self-Contained 部署](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)在 ASP.NET Core 版本 2.0.0 及更早版本。</span><span class="sxs-lookup"><span data-stu-id="c9326-109">Razor view precompilation is unavailable when doing a [Self-Contained Deployment](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) in ASP.NET Core versions 2.0.0 and earlier.</span></span>

<span data-ttu-id="c9326-110">预编译的注意事项：</span><span class="sxs-lookup"><span data-stu-id="c9326-110">Precompilation considerations:</span></span>

* <span data-ttu-id="c9326-111">预编译视图生成较小的已发布的捆绑和更快的启动时间。</span><span class="sxs-lookup"><span data-stu-id="c9326-111">Precompiling views results in a smaller published bundle and faster startup time.</span></span>
* <span data-ttu-id="c9326-112">预编译视图后，无法编辑 Razor 文件。</span><span class="sxs-lookup"><span data-stu-id="c9326-112">You can't edit Razor files after you precompile views.</span></span> <span data-ttu-id="c9326-113">编辑的视图将不会出现在已发布的捆绑包。</span><span class="sxs-lookup"><span data-stu-id="c9326-113">The edited views won't be present in the published bundle.</span></span> 

<span data-ttu-id="c9326-114">部署预编译的视图：</span><span class="sxs-lookup"><span data-stu-id="c9326-114">To deploy precompiled views:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c9326-115">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c9326-115">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="c9326-116">如果你的项目以.NET Framework 为目标，包括对的包引用`Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`:</span><span class="sxs-lookup"><span data-stu-id="c9326-116">If your project targets .NET Framework, include a package reference to `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.ViewCompilation" Version="2.0.0" PrivateAssets="All" />
```

<span data-ttu-id="c9326-117">如果你的项目面向.NET 核心，不不需要进行任何更改。</span><span class="sxs-lookup"><span data-stu-id="c9326-117">If your project targets .NET Core, no changes are necessary.</span></span>

<span data-ttu-id="c9326-118">ASP.NET 核心 2.x 项目模板隐式设置`MvcRazorCompileOnPublish`到`true`默认情况下，这意味着此节点可以安全地删除从*.csproj*文件。</span><span class="sxs-lookup"><span data-stu-id="c9326-118">The ASP.NET Core 2.x project templates implicitly set `MvcRazorCompileOnPublish` to `true` by default, which means this node can be safely removed from the *.csproj* file.</span></span> <span data-ttu-id="c9326-119">如果想要为显式，没有设置任何影响`MvcRazorCompileOnPublish`属性`true`。</span><span class="sxs-lookup"><span data-stu-id="c9326-119">If you prefer to be explicit, there's no harm in setting the `MvcRazorCompileOnPublish` property to `true`.</span></span> <span data-ttu-id="c9326-120">以下*.csproj*示例重点介绍了此设置：</span><span class="sxs-lookup"><span data-stu-id="c9326-120">The following *.csproj* sample highlights this setting:</span></span>

[!code-xml[Main](view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=5)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c9326-121">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c9326-121">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="c9326-122">设置`MvcRazorCompileOnPublish`到`true`，并包含对的包引用`Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`。</span><span class="sxs-lookup"><span data-stu-id="c9326-122">Set `MvcRazorCompileOnPublish` to `true`, and include a package reference to `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`.</span></span> <span data-ttu-id="c9326-123">以下*.csproj*示例重点介绍了这些设置：</span><span class="sxs-lookup"><span data-stu-id="c9326-123">The following *.csproj* sample highlights these settings:</span></span>

[!code-xml[Main](view-compilation\sample\MvcRazorCompileOnPublish.csproj?highlight=5,12)]

---
