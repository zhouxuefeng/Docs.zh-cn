---
title: "图像标记帮助器 |Microsoft 文档"
author: pkellner
description: "演示如何使用图像标记帮助器"
keywords: "ASP.NET Core, 标记帮助程序"
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: c045d485-d1dc-4cea-a675-46be83b7a013
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: 0d55514508b963ce05031f89a20af696f5d4a670
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2017
---
# <a name="imagetaghelper"></a>ImageTagHelper

作者：[Peter Kellner](http://peterkellner.net) 

图像标记帮助程序增强了`img`(`<img>`) 标记。 它需要`src`标记以及`boolean`属性`asp-append-version`。

如果图像源 (`src`) 文件是静态文件在主机 web 服务器上，清除功能字符串的唯一缓存追加作为图像源的查询参数。 这可确保，如果主机 web 服务器上的文件发生更改，将会生成唯一的请求 URL 包含更新的请求参数。 清除功能字符串缓存是表示静态图像文件的哈希的唯一值。

如果图像源 (`src`) 不是静态文件 （例如远程 URL 或文件服务器不存在），`<img>`标记的`src`与清除功能查询字符串参数不缓存生成的属性。

## <a name="image-tag-helper-attributes"></a>图像标记帮助器属性


### <a name="asp-append-version"></a>asp 追加版本

指定与时`src`，都会调用特性，图像标记帮助器。

一个有效的示例`img`标记帮助程序是：

```cshtml
<img src="~/images/asplogo.png" 
    asp-append-version="true"  />
```

如果静态文件存在于目录*...wwwroot/images/asplogo.png*生成的 html 是类似于以下 （哈希值将有所不同）：

```html
<img 
    src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM"/>
```

分配给该参数的值`v`是磁盘上的文件的哈希值。 如果 web 服务器无法获取对读取访问权限的静态文件引用，否`v`参数添加到`src`属性。

- - -

### <a name="src"></a>src

若要激活图像标记帮助器，src 属性都需要`<img>`元素。 

> [!NOTE]
> 使用图像标记帮助器`Cache`本地 web 服务器上的提供商联系，以存储计算`Sha512`给定文件。 如果对文件请求再次`Sha512`不需要重新计算。 缓存失效的文件观察程序附加到的文件时该文件的`Sha512`计算。

## <a name="additional-resources"></a>其他资源

* <xref:performance/caching/memory>
