---
title: "在 ASP.NET Core 环境标记帮助器"
author: pkellner
description: "ASP.Net 核心环境标记帮助器定义包括所有的属性"
keywords: "ASP.NET 核心，标记帮助器"
ms.author: riande
manager: wpickett
ms.date: 07/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/EnvironmentTagHelper
ms.openlocfilehash: 5870d9ebd02becf29f892c91310022d3b9a6b7af
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="environment-tag-helper-in-aspnet-core"></a>在 ASP.NET Core 环境标记帮助器

通过[Peter Kellner](http://peterkellner.net)和[Hisham Bin Ateya](https://twitter.com/hishambinateya)

环境标记帮助器有条件地呈现其包含的内容，根据当前的托管环境。 其单一属性`names`是环境的逗号分隔列表名称，如果任何匹配到当前的环境，将触发要呈现的封闭的内容。

## <a name="environment-tag-helper-attributes"></a>环境标记帮助器属性

### <a name="names"></a>名称

接受单个宿主环境名称或宿主触发的包含的内容呈现的环境名称的逗号分隔列表。

这些值与从 ASP.NET Core 静态属性返回的当前值进行比较`HostingEnvironment.EnvironmentName`。  此值是以下之一：**过渡**;**开发**或**生产**。 比较不区分大小写。

一个有效的示例`environment`标记帮助程序是：

```cshtml
<environment names="Staging,Production">
  <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

## <a name="include-and-exclude-attributes"></a>包括和排除属性

ASP.NET 核心 2.x 添加`include`  &  `exclude`属性。 这些特性控制以下方面呈现基于包含或排除宿主环境名称包含的内容。

### <a name="include-aspnet-core-20-and-later"></a>包括 ASP.NET Core 2.0 及更高版本

`include`属性具有类似的行为的`names`在 ASP.NET 核心 1.0 中的属性。

```cshtml
<environment include="Staging,Production">
  <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude-aspnet-core-20-and-later"></a>排除 ASP.NET Core 2.0 及更高版本

与此相反，`exclude`属性允许`EnvironmentTagHelper`呈现的除你指定的密钥的所有宿主环境名称包含的内容。

```cshtml
<environment exclude="Development">
  <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/environments>
* <xref:fundamentals/dependency-injection#service-lifetimes-and-registration-options>