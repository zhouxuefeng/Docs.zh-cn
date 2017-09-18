---
title: "ASP.NET Core 内置标记帮助程序"
author: pkellner
description: "ASP.NET Core 内置标记帮助程序"
keywords: "ASP.NET Core, 标记帮助程序"
ms.author: riande
manager: wpickett
ms.date: 09/13/2017
ms.topic: article
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/Index
ms.openlocfilehash: e7c8c64283ca3740698300689b10497f984cfd3e
ms.sourcegitcommit: d022d4b96795ee473fa3847a1d8a8c7430423a86
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2017
---
# <a name="aspnet-core-built-in-tag-helpers"></a><span data-ttu-id="3837e-104">ASP.NET Core 内置标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="3837e-104">ASP.NET Core built-in Tag Helpers</span></span>

<span data-ttu-id="3837e-105">作者：[Peter Kellner](http://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="3837e-105">By [Peter Kellner](http://peterkellner.net)</span></span> 

<span data-ttu-id="3837e-106">ASP.NET Core 包括了许多内置标记帮助程序以用于提高生产力。</span><span class="sxs-lookup"><span data-stu-id="3837e-106">ASP.NET Core includes many built-in Tag Helpers to boost your productivity.</span></span> <span data-ttu-id="3837e-107">本部分对内置标记帮助程序进行了概述。</span><span class="sxs-lookup"><span data-stu-id="3837e-107">This section provides an overview of the built-in Tag Helpers.</span></span>

> [!NOTE]
> <span data-ttu-id="3837e-108">有些内置标记帮助程序未在此处讨论，因为它们由 [Razor](xref:mvc/views/razor) 视图引擎在内部使用。</span><span class="sxs-lookup"><span data-stu-id="3837e-108">There are built-in Tag Helpers which aren't discussed, since they're used internally by the [Razor](xref:mvc/views/razor) view engine.</span></span> <span data-ttu-id="3837e-109">这包括针对扩展到网站根路径的 ~ 字符所适用的标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="3837e-109">This includes a Tag Helper for the ~ character, which expands to the root path of the website.</span></span>

## <a name="built-in-aspnet-core-tag-helpers"></a><span data-ttu-id="3837e-110">内置 ASP.NET Core 标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="3837e-110">Built-in ASP.NET Core Tag Helpers</span></span>

<span data-ttu-id="3837e-111">**[定位点标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper)**</span><span class="sxs-lookup"><span data-stu-id="3837e-111">**[Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper)**</span></span>

<span data-ttu-id="3837e-112">**[缓存标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/CacheTagHelper)**</span><span class="sxs-lookup"><span data-stu-id="3837e-112">**[Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/CacheTagHelper)**</span></span>

<span data-ttu-id="3837e-113">**[分布式缓存帮助程序](xref:mvc/views/tag-helpers/builtin-th/DistributedCacheTagHelper)**</span><span class="sxs-lookup"><span data-stu-id="3837e-113">**[Distributed Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/DistributedCacheTagHelper)**</span></span>

<span data-ttu-id="3837e-114">**[环境标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/EnvironmentTagHelper)**</span><span class="sxs-lookup"><span data-stu-id="3837e-114">**[Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/EnvironmentTagHelper)**</span></span>

[comment]: **[FormActionTagHelper](xref:mvc/views/tag-helpers/builtin-th/FormActionTagHelper)**

<span data-ttu-id="3837e-115">**[表单标记帮助程序](xref:mvc/views/working-with-forms#the-form-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="3837e-115">**[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper)**</span></span>

<span data-ttu-id="3837e-116">**[图像标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/ImageTagHelper)**</span><span class="sxs-lookup"><span data-stu-id="3837e-116">**[Image Tag Helper](xref:mvc/views/tag-helpers/builtin-th/ImageTagHelper)**</span></span>

<span data-ttu-id="3837e-117">**[输入标记帮助程序](xref:mvc/views/working-with-forms#the-input-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="3837e-117">**[Input Tag Helper](xref:mvc/views/working-with-forms#the-input-tag-helper)**</span></span>

<span data-ttu-id="3837e-118">**[标签标记帮助程序](xref:mvc/views/working-with-forms#the-label-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="3837e-118">**[Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper)**</span></span>

[comment]: **[LinkTagHelper](xref:mvc/views/tag-helpers/builtin-th/LinkTagHelper)**

[comment]: **[OptionTagHelper](xref:mvc/views/tag-helpers/builtin-th/OptionTagHelper)**

[comment]: **[ScriptTagHelper](xref:mvc/views/tag-helpers/builtin-th/ScriptTagTagHelper)**

<span data-ttu-id="3837e-119">**[选择标记帮助程序](xref:mvc/views/working-with-forms#the-select-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="3837e-119">**[Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper)**</span></span>

<span data-ttu-id="3837e-120">**[文本区标记帮助程序](xref:mvc/views/working-with-forms#the-textarea-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="3837e-120">**[Textarea Tag Helper](xref:mvc/views/working-with-forms#the-textarea-tag-helper)**</span></span>

<span data-ttu-id="3837e-121">**[验证消息标记帮助程序](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="3837e-121">**[Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)**</span></span>

<span data-ttu-id="3837e-122">**[验证摘要标记帮助程序](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="3837e-122">**[Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)**</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3837e-123">其他资源</span><span class="sxs-lookup"><span data-stu-id="3837e-123">Additional resources</span></span>

* [<span data-ttu-id="3837e-124">客户端开发</span><span class="sxs-lookup"><span data-stu-id="3837e-124">Client-Side Development</span></span>](xref:client-side/index)
* [<span data-ttu-id="3837e-125">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="3837e-125">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
