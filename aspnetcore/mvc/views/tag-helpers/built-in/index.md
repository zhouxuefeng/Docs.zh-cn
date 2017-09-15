---
title: "ASP.NET Core 内置标记帮助程序"
author: pkellner
description: "ASP.NET Core 内置标记帮助程序"
keywords: "ASP.NET Core, 标记帮助程序"
ms.author: riande
manager: wpickett
ms.date: 7/11/2017
ms.topic: article
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/Index
ms.openlocfilehash: 3f47cc571eff0c522aaf6543de58f158835384d4
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="aspnet-core-built-in-tag-helpers"></a><span data-ttu-id="425a6-104">ASP.NET Core 内置标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="425a6-104">ASP.NET Core built-in Tag Helpers</span></span>

<span data-ttu-id="425a6-105">作者：[Peter Kellner](http://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="425a6-105">By [Peter Kellner](http://peterkellner.net)</span></span> 

<span data-ttu-id="425a6-106">ASP.NET Core 框架包括许多标记帮助程序，可帮助提高编写可靠代码的工作效率。</span><span class="sxs-lookup"><span data-stu-id="425a6-106">The ASP.NET Core framework includes many Tag Helpers that can help you be more productive in writing robust code.</span></span> <span data-ttu-id="425a6-107">本部分概述所有内置标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="425a6-107">This section provides an overview of all the built-in Tag Helpers.</span></span>

> [!NOTE]
> <span data-ttu-id="425a6-108">有些内置标记帮助程序未在此讨论，因为它们由 [Razor](xref:mvc/views/razor) 查看引擎在内部使用。</span><span class="sxs-lookup"><span data-stu-id="425a6-108">There are built-in Tag Helpers which are not discussed, since they are used internally by the [Razor](xref:mvc/views/razor) view engine.</span></span> <span data-ttu-id="425a6-109">这包括扩展到网站根路径的 ~ 字符的标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="425a6-109">This includes a Tag Helper for the ~ character which expands to the root path of the web site.</span></span>

## <a name="built-in-aspnet-core-tag-helpers"></a><span data-ttu-id="425a6-110">内置 ASP.NET Core 标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="425a6-110">Built-in ASP.NET Core Tag Helpers</span></span>

<span data-ttu-id="425a6-111">**[定位点标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper)**</span><span class="sxs-lookup"><span data-stu-id="425a6-111">**[Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper)**</span></span>

<span data-ttu-id="425a6-112">**[缓存标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/CacheTagHelper)**</span><span class="sxs-lookup"><span data-stu-id="425a6-112">**[Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/CacheTagHelper)**</span></span>

<span data-ttu-id="425a6-113">**[分布式缓存帮助程序](xref:mvc/views/tag-helpers/builtin-th/DistributedCacheTagHelper)**</span><span class="sxs-lookup"><span data-stu-id="425a6-113">**[Distributed Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/DistributedCacheTagHelper)**</span></span>

<span data-ttu-id="425a6-114">**[环境标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/EnvironmentTagHelper)**</span><span class="sxs-lookup"><span data-stu-id="425a6-114">**[Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/EnvironmentTagHelper)**</span></span>

[comment]: **[FormActionTagHelper](xref:mvc/views/tag-helpers/builtin-th/FormActionTagHelper)**

[comment]: **[FormTagTagHelper](xref:mvc/views/tag-helpers/builtin-th/FormTagHelper)**

<span data-ttu-id="425a6-115">**[图像标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/ImageTagHelper)**</span><span class="sxs-lookup"><span data-stu-id="425a6-115">**[Image Tag Helper](xref:mvc/views/tag-helpers/builtin-th/ImageTagHelper)**</span></span>

[comment]: **[InputTagHelper](xref:mvc/views/tag-helpers/builtin-th/InputTagHelper)**

[comment]: **[LabelTagHelper](xref:mvc/views/tag-helpers/builtin-th/LabelTagHelper)**

[comment]: **[LinkTagHelper](xref:mvc/views/tag-helpers/builtin-th/LinkTagHelper)**

[comment]: **[OptionTagHelper](xref:mvc/views/tag-helpers/builtin-th/OptionTagHelper)**

[comment]: **[ScriptTagHelper](xref:mvc/views/tag-helpers/builtin-th/ScriptTagTagHelper)**

[comment]: **[SelectTagHelper](xref:mvc/views/tag-helpers/builtin-th/SelectTagTagHelper)**

[comment]: **[TextAreaTagHelper](xref:mvc/views/tag-helpers/builtin-th/TextAreaTagHelper)**

[comment]: **[ValidationMessageTagHelper](xref:mvc/views/tag-helpers/builtin-th/ValidationMessageTagHelper)**

[comment]: **[ValidationSummaryTagHelper](xref:mvc/views/tag-helpers/builtin-th/ValidationSummaryTagHelper)**  
  
  
<!--

## Additional Resources

REQUIRED These must be xref links, not relative, that is ../../
* [Client-Side Development](../../../client-side/index.md)

* [Tag Helpers](xref:mvc/views/tag-helpers/intro)
-->
