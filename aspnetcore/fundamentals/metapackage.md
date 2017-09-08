---
title: "有关 ASP.NET 核心 Microsoft.AspNetCore.All metapackage 2.x 及更高版本"
author: Rick-Anderson
description: "Microsoft.AspNetCore.All metapackage 包括所有受支持的包。"
keywords: "ASP.NET 核心，NuGet 包，Microsoft.AspNetCore.All、 metapackage"
ms.author: riande
manager: wpickett
ms.date: 07/16/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/metapackage
ms.openlocfilehash: 255438a4ce36ce4978f8c8ee298388a25ac00d17
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
#<a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-2x"></a><span data-ttu-id="a9484-104">有关 ASP.NET 核心 Microsoft.AspNetCore.All metapackage 2.x</span><span class="sxs-lookup"><span data-stu-id="a9484-104">Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.x</span></span>

<span data-ttu-id="a9484-105">此功能需要 ASP.NET Core 2.x。</span><span class="sxs-lookup"><span data-stu-id="a9484-105">This feature requires ASP.NET Core 2.x.</span></span>

<span data-ttu-id="a9484-106">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) ASP.NET Core 的 metapackage 包括：</span><span class="sxs-lookup"><span data-stu-id="a9484-106">The [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) metapackage for ASP.NET Core includes:</span></span>

* <span data-ttu-id="a9484-107">所有受 ASP.NET Core 团队支持包。</span><span class="sxs-lookup"><span data-stu-id="a9484-107">All supported packages by the ASP.NET Core team.</span></span>
* <span data-ttu-id="a9484-108">所有支持由实体框架核心的包。</span><span class="sxs-lookup"><span data-stu-id="a9484-108">All supported packages by the Entity Framework Core.</span></span> 
* <span data-ttu-id="a9484-109">内部和第三方依赖关系，使用 ASP.NET Core 和实体框架核心。</span><span class="sxs-lookup"><span data-stu-id="a9484-109">Internal and 3rd-party dependencies used by ASP.NET Core and Entity Framework Core.</span></span> 

<span data-ttu-id="a9484-110">ASP.NET 核心的所有功能 2.x 并且实体框架核心 2.x 包含在`Microsoft.AspNetCore.All`包。</span><span class="sxs-lookup"><span data-stu-id="a9484-110">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.All` package.</span></span> <span data-ttu-id="a9484-111">默认的项目模板使用此程序包。</span><span class="sxs-lookup"><span data-stu-id="a9484-111">The default project templates use this package.</span></span>

<span data-ttu-id="a9484-112">版本数`Microsoft.AspNetCore.All`metapackage 表示 ASP.NET Core 版本和实体框架 Core 版本 （.NET Core 版本与对齐）。</span><span class="sxs-lookup"><span data-stu-id="a9484-112">The version number of the `Microsoft.AspNetCore.All` metapackage represents the ASP.NET Core version and Entity Framework Core version (aligned with the .NET Core version).</span></span>

<span data-ttu-id="a9484-113">应用程序使用`Microsoft.AspNetCore.All`metapackage 自动充分利用.NET 核心运行时存储。</span><span class="sxs-lookup"><span data-stu-id="a9484-113">Applications that use the `Microsoft.AspNetCore.All` metapackage automatically take advantage of the .NET Core Runtime Store.</span></span> <span data-ttu-id="a9484-114">运行时存储包含运行 ASP.NET Core 2.x 应用程序所需的所有运行时资产。</span><span class="sxs-lookup"><span data-stu-id="a9484-114">The Runtime Store contains all the runtime assets needed to run ASP.NET Core 2.x applications.</span></span> <span data-ttu-id="a9484-115">当你使用`Microsoft.AspNetCore.All`metapackage，**没有**资产从引用的 ASP.NET Core NuGet 包与应用程序部署&mdash;.NET 核心运行时存储区包含这些资产。</span><span class="sxs-lookup"><span data-stu-id="a9484-115">When you use the `Microsoft.AspNetCore.All` metapackage, **no** assets from the referenced ASP.NET Core NuGet packages are deployed with the application &mdash; the .NET Core Runtime Store contains these assets.</span></span> <span data-ttu-id="a9484-116"><!-- todo add link to Runtime store -->预编译运行时存储中的资产以提高应用程序启动时间。</span><span class="sxs-lookup"><span data-stu-id="a9484-116"><!-- todo add link to Runtime store --> The assets in the Runtime Store are precompiled to improve application startup time.</span></span>

<span data-ttu-id="a9484-117">你可以使用包修整过程删除不使用的包。</span><span class="sxs-lookup"><span data-stu-id="a9484-117">You can use the package trimming process to remove packages that you don't use.</span></span> <span data-ttu-id="a9484-118">裁剪后的包不在发布的应用程序输出中。</span><span class="sxs-lookup"><span data-stu-id="a9484-118">Trimmed packages are excluded in published application output.</span></span>

<span data-ttu-id="a9484-119">以下*.csproj*文件引用`Microsoft.AspNetCore.All`metapackage 有关 ASP.NET 核心：</span><span class="sxs-lookup"><span data-stu-id="a9484-119">The following *.csproj* file references the `Microsoft.AspNetCore.All` metapackage for ASP.NET Core:</span></span>

<span data-ttu-id="a9484-120">[!code-xml[Main](..\mvc\views\view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=9)]</span><span class="sxs-lookup"><span data-stu-id="a9484-120">[!code-xml[Main](..\mvc\views\view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=9)]</span></span>
