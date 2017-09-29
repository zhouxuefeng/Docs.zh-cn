---
title: "在 ASP.NET Core 环境标记帮助器"
author: pkellner
description: "ASP.Net 核心环境标记帮助器定义包括所有的属性"
keywords: "ASP.NET Core, 标记帮助程序"
ms.author: riande
manager: wpickett
ms.date: 07/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: 6da7840b84ae453483519e9610d9bb59c0e8fdb5
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2017
---
# <a name="environment-tag-helper-in-aspnet-core"></a><span data-ttu-id="ff836-104">在 ASP.NET Core 环境标记帮助器</span><span class="sxs-lookup"><span data-stu-id="ff836-104">Environment Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="ff836-105">通过[Peter Kellner](http://peterkellner.net)和[Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="ff836-105">By [Peter Kellner](http://peterkellner.net) and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="ff836-106">环境标记帮助器有条件地呈现其包含的内容，根据当前的托管环境。</span><span class="sxs-lookup"><span data-stu-id="ff836-106">The Environment Tag Helper conditionally renders its enclosed content based on the current hosting environment.</span></span> <span data-ttu-id="ff836-107">其单一属性`names`是环境的逗号分隔列表名称，如果任何匹配到当前的环境，将触发要呈现的封闭的内容。</span><span class="sxs-lookup"><span data-stu-id="ff836-107">Its single attribute `names` is a comma separated list of environment names, that if any match to the current environment, will trigger the enclosed content to be rendered.</span></span>

## <a name="environment-tag-helper-attributes"></a><span data-ttu-id="ff836-108">环境标记帮助器属性</span><span class="sxs-lookup"><span data-stu-id="ff836-108">Environment Tag Helper Attributes</span></span>

### <a name="names"></a><span data-ttu-id="ff836-109">名称</span><span class="sxs-lookup"><span data-stu-id="ff836-109">names</span></span>

<span data-ttu-id="ff836-110">接受单个宿主环境名称或宿主触发的包含的内容呈现的环境名称的逗号分隔列表。</span><span class="sxs-lookup"><span data-stu-id="ff836-110">Accepts a single hosting environment name or a comma-separated list of hosting environment names that trigger the rendering of the enclosed content.</span></span>

<span data-ttu-id="ff836-111">这些值与从 ASP.NET Core 静态属性返回的当前值进行比较`HostingEnvironment.EnvironmentName`。</span><span class="sxs-lookup"><span data-stu-id="ff836-111">These value(s) are compared to the current value returned from the ASP.NET Core static property `HostingEnvironment.EnvironmentName`.</span></span>  <span data-ttu-id="ff836-112">此值是以下之一：**过渡**;**开发**或**生产**。</span><span class="sxs-lookup"><span data-stu-id="ff836-112">This value is one of the following: **Staging**; **Development** or **Production**.</span></span> <span data-ttu-id="ff836-113">比较不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="ff836-113">The comparison ignores case.</span></span>

<span data-ttu-id="ff836-114">一个有效的示例`environment`标记帮助程序是：</span><span class="sxs-lookup"><span data-stu-id="ff836-114">An example of a valid `environment` tag helper is:</span></span>

```cshtml
<environment names="Staging,Production">
  <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

## <a name="include-and-exclude-attributes"></a><span data-ttu-id="ff836-115">包括和排除属性</span><span class="sxs-lookup"><span data-stu-id="ff836-115">include and exclude attributes</span></span>

<span data-ttu-id="ff836-116">ASP.NET 核心 2.x 添加`include`  &  `exclude`属性。</span><span class="sxs-lookup"><span data-stu-id="ff836-116">ASP.NET Core 2.x adds the `include` & `exclude` attributes.</span></span> <span data-ttu-id="ff836-117">这些特性控制以下方面呈现基于包含或排除宿主环境名称包含的内容。</span><span class="sxs-lookup"><span data-stu-id="ff836-117">These attributes control rendering the enclosed content based on the included or excluded hosting environment names.</span></span>

### <a name="include-aspnet-core-20-and-later"></a><span data-ttu-id="ff836-118">包括 ASP.NET Core 2.0 及更高版本</span><span class="sxs-lookup"><span data-stu-id="ff836-118">include ASP.NET Core 2.0 and later</span></span>

<span data-ttu-id="ff836-119">`include`属性具有类似的行为的`names`在 ASP.NET 核心 1.0 中的属性。</span><span class="sxs-lookup"><span data-stu-id="ff836-119">The `include` property has a similar behavior of the `names` attribute in ASP.NET Core 1.0.</span></span>

```cshtml
<environment include="Staging,Production">
  <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude-aspnet-core-20-and-later"></a><span data-ttu-id="ff836-120">排除 ASP.NET Core 2.0 及更高版本</span><span class="sxs-lookup"><span data-stu-id="ff836-120">exclude ASP.NET Core 2.0 and later</span></span>

<span data-ttu-id="ff836-121">与此相反，`exclude`属性允许`EnvironmentTagHelper`呈现的除你指定的密钥的所有宿主环境名称包含的内容。</span><span class="sxs-lookup"><span data-stu-id="ff836-121">In contrast, the `exclude` property lets the `EnvironmentTagHelper` render the enclosed content for all hosting environment names except the one(s) that you specified.</span></span>

```cshtml
<environment exclude="Development">
  <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

## <a name="additional-resources"></a><span data-ttu-id="ff836-122">其他资源</span><span class="sxs-lookup"><span data-stu-id="ff836-122">Additional resources</span></span>

* <xref:fundamentals/environments>
* <xref:fundamentals/dependency-injection#service-lifetimes-and-registration-options>
