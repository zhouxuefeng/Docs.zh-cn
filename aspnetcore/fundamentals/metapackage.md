---
title: "有关 ASP.NET 核心 Microsoft.AspNetCore.All metapackage 2.x 及更高版本"
author: Rick-Anderson
description: "Microsoft.AspNetCore.All metapackage 包括所有受支持的 ASP.NET Core 和实体框架核心包，以及其依赖项。"
keywords: ASP.NET Core,NuGet,package,Microsoft.AspNetCore.All,metapackage
ms.author: riande
manager: wpickett
ms.date: 09/20/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/metapackage
ms.openlocfilehash: ff25d80be907994f7ac3d64a8ffa39ae53278ba6
ms.sourcegitcommit: 73bf6b222474d9f1f6aba3feaca4e191069d2121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2017
---
#<a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-2x"></a><span data-ttu-id="3dbad-104">有关 ASP.NET 核心 Microsoft.AspNetCore.All metapackage 2.x</span><span class="sxs-lookup"><span data-stu-id="3dbad-104">Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.x</span></span>

<span data-ttu-id="3dbad-105">此功能需要 ASP.NET Core 2.x 目标.NET 核心 2.x。</span><span class="sxs-lookup"><span data-stu-id="3dbad-105">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="3dbad-106">ASP.NET Core 的 [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) 元包包括：</span><span class="sxs-lookup"><span data-stu-id="3dbad-106">The [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) metapackage for ASP.NET Core includes:</span></span>

* <span data-ttu-id="3dbad-107">ASP.NET Core 团队支持的所有包。</span><span class="sxs-lookup"><span data-stu-id="3dbad-107">All supported packages by the ASP.NET Core team.</span></span>
* <span data-ttu-id="3dbad-108">Entity Framework Core 支持的所有包。</span><span class="sxs-lookup"><span data-stu-id="3dbad-108">All supported packages by the Entity Framework Core.</span></span> 
* <span data-ttu-id="3dbad-109">ASP.NET Core 和 Entity Framework Core 使用的内部和第三方依赖关系。</span><span class="sxs-lookup"><span data-stu-id="3dbad-109">Internal and 3rd-party dependencies used by ASP.NET Core and Entity Framework Core.</span></span> 

<span data-ttu-id="3dbad-110">ASP.NET 核心的所有功能 2.x 并且实体框架核心 2.x 包含在`Microsoft.AspNetCore.All`包。</span><span class="sxs-lookup"><span data-stu-id="3dbad-110">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.All` package.</span></span> <span data-ttu-id="3dbad-111">默认的项目模板使用此程序包。</span><span class="sxs-lookup"><span data-stu-id="3dbad-111">The default project templates use this package.</span></span>

<span data-ttu-id="3dbad-112">版本数`Microsoft.AspNetCore.All`metapackage 表示 ASP.NET Core 版本和实体框架 Core 版本 （.NET Core 版本与对齐）。</span><span class="sxs-lookup"><span data-stu-id="3dbad-112">The version number of the `Microsoft.AspNetCore.All` metapackage represents the ASP.NET Core version and Entity Framework Core version (aligned with the .NET Core version).</span></span>

<span data-ttu-id="3dbad-113">应用程序使用`Microsoft.AspNetCore.All`metapackage 自动充分利用[.NET 核心运行时存储](https://docs.microsoft.com/dotnet/core/deploying/runtime-store)。</span><span class="sxs-lookup"><span data-stu-id="3dbad-113">Applications that use the `Microsoft.AspNetCore.All` metapackage automatically take advantage of the [.NET Core Runtime Store](https://docs.microsoft.com/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="3dbad-114">运行时存储包含运行 ASP.NET Core 2.x 应用程序所需的所有运行时资产。</span><span class="sxs-lookup"><span data-stu-id="3dbad-114">The Runtime Store contains all the runtime assets needed to run ASP.NET Core 2.x applications.</span></span> <span data-ttu-id="3dbad-115">当你使用`Microsoft.AspNetCore.All`metapackage，**没有**资产从引用的 ASP.NET Core NuGet 包与应用程序部署&mdash;.NET 核心运行时存储区包含这些资产。</span><span class="sxs-lookup"><span data-stu-id="3dbad-115">When you use the `Microsoft.AspNetCore.All` metapackage, **no** assets from the referenced ASP.NET Core NuGet packages are deployed with the application &mdash; the .NET Core Runtime Store contains these assets.</span></span> <span data-ttu-id="3dbad-116">预编译运行时存储中的资产以提高应用程序启动时间。</span><span class="sxs-lookup"><span data-stu-id="3dbad-116">The assets in the Runtime Store are precompiled to improve application startup time.</span></span>

<span data-ttu-id="3dbad-117">你可以使用包修整过程删除不使用的包。</span><span class="sxs-lookup"><span data-stu-id="3dbad-117">You can use the package trimming process to remove packages that you don't use.</span></span> <span data-ttu-id="3dbad-118">裁剪后的包不在发布的应用程序输出中。</span><span class="sxs-lookup"><span data-stu-id="3dbad-118">Trimmed packages are excluded in published application output.</span></span>

<span data-ttu-id="3dbad-119">以下*.csproj*文件引用`Microsoft.AspNetCore.All`metapackage 有关 ASP.NET 核心：</span><span class="sxs-lookup"><span data-stu-id="3dbad-119">The following *.csproj* file references the `Microsoft.AspNetCore.All` metapackage for ASP.NET Core:</span></span>

[!code-xml[Main](..\mvc\views\view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=9)]
