---
title: "缓存 ASP.NET Core MVC 中的标记帮助器"
author: pkellner
description: "演示如何使用缓存标记帮助器"
keywords: "ASP.NET Core, 标记帮助程序"
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: c045d485-d1dc-4cea-a675-46be83b7a012
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: da5b7b3bf1aa01ee22edf9bd003d8f79a00a5d0b
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2017
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a>缓存 ASP.NET Core MVC 中的标记帮助器

作者：[Peter Kellner](http://peterkellner.net) 


缓存标记帮助器提供的功能可以显著提高通过缓存其内容与内部 ASP.NET Core 缓存提供程序的 ASP.NET Core 应用的性能。

Razor 视图引擎设置的默认`expires-after`到 20 分钟。

以下 Razor 标记缓存日期/时间：

```cshtml
<Cache>@DateTime.Now<Cache>
```

对包含的页的第一个请求`CacheTagHelper`将显示当前日期/时间。 缓存过期 （默认值 20 分钟），或内存压力逐出之前，其他请求将显示缓存的值。

你可以设置具有以下属性将缓存持续时间：

## <a name="cache-tag-helper-attributes"></a>缓存标记帮助器属性

- - -

### <a name="enabled"></a>enabled    


| 特性类型    | 有效值      |
|----------------   |----------------   |
| boolean           | "true"（默认值）  |
|                   | “false”   |


确定是否缓存的缓存标记帮助程序括起来的内容。 默认值为 `true`。  如果设置为`false`此缓存标记帮助器将不起缓存上呈现的输出。

示例:

```cshtml
<Cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</Cache>
```

- - -

### <a name="expires-on"></a>过期上 

| 特性类型    | 示例值     |
|----------------   |----------------   |
| DateTimeOffset    | "@new DateTime(2025,1,29,17,02,0)"    |


设置一个绝对过期日期。 下面的示例将之前 2025 年 1 月 29，下午 5:02 缓存的缓存标记帮助程序中的内容。

示例:

```cshtml
<Cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</Cache>
```

- - -

### <a name="expires-after"></a>过期时间

| 特性类型    | 示例值     |
|----------------   |----------------   |
| TimeSpan    | " @TimeSpan.FromSeconds (120)"    |


从缓存中的内容的第一个请求时间设置时间的长度。 

示例:

```cshtml
<Cache expires-on="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</Cache>
```

- - -

### <a name="expires-sliding"></a>过期-滑动

| 特性类型    | 示例值     |
|----------------   |----------------   |
| TimeSpan    | " @TimeSpan.FromSeconds (60)"     |


设置应被逐出缓存项，如果未访问的时间。

示例:

```cshtml
<Cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</Cache>
```

- - -

### <a name="vary-by-header"></a>不同的标题

| 特性类型    | 示例值                |
|----------------   |----------------               |
| 字符串            | "用户代理"                  |
|                   | "用户代理，内容编码" |

接受的单个标头值或在更改时触发缓存刷新的标头值的以逗号分隔列表。 下面的示例监视标头值`User-Agent`。 该示例将缓存的内容的每个不同`User-Agent`向 web 服务器显示。

示例:

```cshtml
<Cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</Cache>
```

- - -

### <a name="vary-by-query"></a>不同的查询

| 特性类型    | 示例值                |
|----------------   |----------------               |
| 字符串            | "使"                |
|                   | "生成，模型" |

接受的单个标头值或以逗号分隔的标头值更改时触发缓存刷新的标头值的列表。 下面的示例查找值`Make`和`Model`。

示例:

```cshtml
<Cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</Cache>
```

- - -

### <a name="vary-by-route"></a>不同的路由

| 特性类型    | 示例值                |
|----------------   |----------------               |
| 字符串            | "使"                |
|                   | "生成，模型" |

接受的单个标头值或路由数据参数值更改时触发缓存刷新的标头值的以逗号分隔列表。 示例:

*Startup.cs* 

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```
  
Index.cshtml

```cshtml
<Cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</Cache>
```

- - -

### <a name="vary-by-cookie"></a>不同的 cookie

| 特性类型    | 示例值                |
|----------------   |----------------               |
| 字符串            | ".AspNetCore.Identity.Application"                |
|                   | ".AspNetCore.Identity.Application,HairColor" |

接受的单个标头值或在标头值 (s) 更改时触发缓存刷新的标头值的以逗号分隔列表。 下面的示例查找在与 ASP.NET 标识关联的 cookie。 用户进行身份验证时请求 cookie 设置随即将会触发缓存刷新。

示例:

```cshtml
<Cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</Cache>
```

- - -

### <a name="vary-by-user"></a>不同的用户

| 特性类型    | 示例值                |
|----------------   |----------------               |
| Boolean             | “true”                  |
|                     | "false"（默认值） |

指定的登录的用户 （或上下文主体） 更改时，缓存应重置。 当前用户是也称为请求上下文主体和可以在 Razor 视图中查看通过引用`@User.Identity.Name`。

下面的示例查找在当前登录用户。  

示例:

```cshtml
<Cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</Cache>
```

使用此属性可保持缓存通过日志和日志打卡周期中的内容。  使用时`vary-by-user="true"`，身份验证的用户的缓存失效的登录和注销操作。  因为在登录时会生成新的唯一的 cookie 值，缓存会失效。 在任何 cookie 或已过期时，缓存将保留匿名的状态。 这意味着如果没有用户登录，将维护缓存。

- - -

### <a name="vary-by"></a>变化因素

| 特性类型    | 示例值                |
|----------------   |----------------               |
| 字符串             | “ @Model ”                 |


允许获取缓存哪些数据的自定义项。 当更新属性的字符串值发生更改，缓存标记帮助器的内容所引用的对象。 通常模型值字符串串联分配给此属性。  实际上，这意味着对任何的串连值的更新令缓存失效。

下面的示例假定呈现的两个的路由参数的整数值的视图和控制器方法`myParam1`和`myParam2`，并作为单个模型属性返回的。 当此总和更改时，呈现和再次缓存的缓存标记帮助程序的内容。  

示例:

操作：

```csharp
public IActionResult Index(string myParam1,string myParam2,string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

Index.cshtml

```cshtml
<Cache vary-by="@Model"">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</Cache>
```

- - -

### <a name="priority"></a>priority

| 特性类型    | 示例值                |
|----------------   |----------------               |
| CacheItemPriority  | "高"                   |
|                    | "低" |
|                    | "NeverRemove" |
|                    | "Normal" |

提供内置的缓存提供程序的缓存逐出指导。 Web 服务器将逐出`Low`处于内存压力下时，第一次缓存条目。

示例:

```cshtml
<Cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</Cache>
```

`priority`属性不能保证特定级别的缓存保留。 `CacheItemPriority`是只是建议。 此属性设置为`NeverRemove`不保证始终将保留缓存。 请参阅[其他资源](#additional-resources)有关详细信息。

缓存标记帮助程序是依赖于[内存缓存服务](xref:performance/caching/memory)。 如果尚未添加，缓存标记帮助器将服务添加。

## <a name="additional-resources"></a>其他资源

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
