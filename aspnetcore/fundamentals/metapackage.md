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
ms.openlocfilehash: 23a07867874eb534c75c4e7b3be00c4a376f8a8b
ms.sourcegitcommit: 4e45fd4e3f1374cd51cc931cee93c9d72631d9fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2017
---
#<a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-2x"></a><span data-ttu-id="a93c9-104">有关 ASP.NET 核心 Microsoft.AspNetCore.All metapackage 2.x</span><span class="sxs-lookup"><span data-stu-id="a93c9-104">Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.x</span></span>

<span data-ttu-id="a93c9-105">此功能需要 ASP.NET Core 2.x。</span><span class="sxs-lookup"><span data-stu-id="a93c9-105">This feature requires ASP.NET Core 2.x.</span></span>

<span data-ttu-id="a93c9-106">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) ASP.NET Core 的 metapackage 包括：</span><span class="sxs-lookup"><span data-stu-id="a93c9-106">The [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) metapackage for ASP.NET Core includes:</span></span>

* <span data-ttu-id="a93c9-107">所有受 ASP.NET Core 团队支持包。</span><span class="sxs-lookup"><span data-stu-id="a93c9-107">All supported packages by the ASP.NET Core team.</span></span>
* <span data-ttu-id="a93c9-108">所有支持由实体框架核心的包。</span><span class="sxs-lookup"><span data-stu-id="a93c9-108">All supported packages by the Entity Framework Core.</span></span> 
* <span data-ttu-id="a93c9-109">内部和第三方依赖关系，使用 ASP.NET Core 和实体框架核心。</span><span class="sxs-lookup"><span data-stu-id="a93c9-109">Internal and 3rd-party dependencies used by ASP.NET Core and Entity Framework Core.</span></span> 

<span data-ttu-id="a93c9-110">ASP.NET 核心的所有功能 2.x 并且实体框架核心 2.x 包含在`Microsoft.AspNetCore.All`包。</span><span class="sxs-lookup"><span data-stu-id="a93c9-110">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.All` package.</span></span> <span data-ttu-id="a93c9-111">默认的项目模板使用此程序包。</span><span class="sxs-lookup"><span data-stu-id="a93c9-111">The default project templates use this package.</span></span>

<span data-ttu-id="a93c9-112">版本数`Microsoft.AspNetCore.All`metapackage 表示 ASP.NET Core 版本和实体框架 Core 版本 （.NET Core 版本与对齐）。</span><span class="sxs-lookup"><span data-stu-id="a93c9-112">The version number of the `Microsoft.AspNetCore.All` metapackage represents the ASP.NET Core version and Entity Framework Core version (aligned with the .NET Core version).</span></span>

<span data-ttu-id="a93c9-113">应用程序使用`Microsoft.AspNetCore.All`metapackage 自动充分利用[.NET 核心运行时存储](https://docs.microsoft.com/dotnet/core/deploying/runtime-store)。</span><span class="sxs-lookup"><span data-stu-id="a93c9-113">Applications that use the `Microsoft.AspNetCore.All` metapackage automatically take advantage of the [.NET Core Runtime Store](https://docs.microsoft.com/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="a93c9-114">运行时存储包含运行 ASP.NET Core 2.x 应用程序所需的所有运行时资产。</span><span class="sxs-lookup"><span data-stu-id="a93c9-114">The Runtime Store contains all the runtime assets needed to run ASP.NET Core 2.x applications.</span></span> <span data-ttu-id="a93c9-115">当你使用`Microsoft.AspNetCore.All`metapackage，**没有**资产从引用的 ASP.NET Core NuGet 包与应用程序部署&mdash;.NET 核心运行时存储区包含这些资产。</span><span class="sxs-lookup"><span data-stu-id="a93c9-115">When you use the `Microsoft.AspNetCore.All` metapackage, **no** assets from the referenced ASP.NET Core NuGet packages are deployed with the application &mdash; the .NET Core Runtime Store contains these assets.</span></span> <span data-ttu-id="a93c9-116">预编译运行时存储中的资产以提高应用程序启动时间。</span><span class="sxs-lookup"><span data-stu-id="a93c9-116">The assets in the Runtime Store are precompiled to improve application startup time.</span></span>

<span data-ttu-id="a93c9-117">你可以使用包修整过程删除不使用的包。</span><span class="sxs-lookup"><span data-stu-id="a93c9-117">You can use the package trimming process to remove packages that you don't use.</span></span> <span data-ttu-id="a93c9-118">裁剪后的包不在发布的应用程序输出中。</span><span class="sxs-lookup"><span data-stu-id="a93c9-118">Trimmed packages are excluded in published application output.</span></span>

<span data-ttu-id="a93c9-119">以下*.csproj*文件引用`Microsoft.AspNetCore.All`metapackage 有关 ASP.NET 核心：</span><span class="sxs-lookup"><span data-stu-id="a93c9-119">The following *.csproj* file references the `Microsoft.AspNetCore.All` metapackage for ASP.NET Core:</span></span>

[!code-xml[Main](..\mvc\views\view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=9)]
