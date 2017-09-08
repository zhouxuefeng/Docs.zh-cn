---
title: "响应缓存"
author: rick-anderson
description: "说明如何使用缓存来降低带宽并提高性能的响应。"
keywords: "ASP.NET 核心，缓存，HTTP 标头的响应"
ms.author: riande
manager: wpickett
ms.date: 7/10/2017
ms.topic: article
ms.assetid: cb42035a-60b0-472e-a614-cb79f443f654
ms.prod: asp.net-core
uid: performance/caching/response
ms.openlocfilehash: 97ca23d48763a96da917c993feb8aadebb450ab5
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="response-caching"></a>响应缓存

通过[John Luo](https://github.com/JunTaoLuo)， [Rick Anderson](https://twitter.com/RickAndMSFT)，和[Steve Smith](http://ardalis.com)

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/sample)

## <a name="what-is-response-caching"></a>什么是响应缓存

*响应缓存*提高响应的缓存相关的标头。 这些标头指定你希望客户端、 代理和缓存响应的中间件如何。 响应缓存可以减少客户端或代理向 web 服务器发出的请求数。 响应缓存可以减少量工作的 web 服务器执行以生成响应。 

用于缓存的主 HTTP 标头是`Cache-Control`。 请参阅[HTTP 1.1 缓存](https://tools.ietf.org/html/rfc7234#section-5.2)有关详细信息。 常见的缓存指令：

* [公用](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)
* [专用](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)
* [无缓存](https://tools.ietf.org/html/rfc7234#section-5.2.1.4)
* [杂注](https://tools.ietf.org/html/rfc7234#section-5.4)
* [改变](https://tools.ietf.org/html/rfc7231#section-7.1.4)

Web 服务器可以通过添加缓存中间件的响应缓存响应。 请参阅[响应缓存中间件](middleware.md)有关详细信息。

## <a name="distributed-cache-tag-helper"></a>分布式的缓存标记帮助器

[分布式缓存标记帮助器](xref:mvc/views/tag-helpers/builtin-th/DistributedCacheTagHelper)使地理上分散缓存。


## <a name="responsecache-attribute"></a>ResponseCache 属性

`ResponseCacheAttribute`指定在缓存中，响应设置适当的标头的所需的参数。 请参阅[ResponseCacheAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.responsecacheattribute)有关参数的说明。

>[!WARNING]
> 禁用缓存的内容，其中包含已经过身份验证的客户端的信息。 不会更改的内容根据用户的标识，或是否已登录用户应仅启用缓存。

`VaryByQueryKeys string[]`(需要 ASP.NET Core 1.1.0 和更高版本): 设置时，响应缓存中间件将因存储的响应的查询密钥的给定列表的值。 必须启用缓存中间件的响应设置`VaryByQueryKeys`属性，否则运行时将引发异常。 没有为没有相应的 HTTP 标头`VaryByQueryKeys`属性。 此属性是一项 HTTP 功能由缓存中间件的响应。 若要提供缓存的响应的中间件，对于查询字符串和查询字符串值必须匹配上一个请求。 例如，考虑以下序列：

| 请求          | 结果 |
| ----------------- | ------------ | 
| `http://example.com?key1=value1` | 从服务器返回 |
| `http://example.com?key1=value1` | 返回从中间件 |
| `http://example.com?key1=value2` | 从服务器返回 |

第一个请求是由服务器返回，并在中间件中缓冲。 由于查询字符串与上一个请求，中间件会返回第二个请求。 第三个请求都不处于中间件缓存中，因为查询字符串值不匹配的上一个请求。 

`ResponseCacheAttribute`用于配置和创建 (通过`IFilterFactory`) `ResponseCacheFilter`。 `ResponseCacheFilter`执行的工作的更新的相应 HTTP 标头和响应功能。 筛选器：

* 删除任何现有标头`Vary`， `Cache-Control`，和`Pragma`。 
* 写出相应的标头设置的属性上基于`ResponseCacheAttribute`。 
* 更新缓存中项 HTTP 功能，如果响应`VaryByQueryKeys`设置。

### <a name="vary"></a>改变

此标头只会写入时`VaryByHeader`属性设置。 设置为`Vary`属性的值。 下面的示例使用`VaryByHeader`属性。

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]

你可以使用你的浏览器的网络工具查看响应标头。 下图显示了输出上边缘 F12**网络**选项卡上时`About2`刷新操作方法。 

![在边缘 F12 输出 * * 网络 * * 选项卡的 About2 操作方法调用时](response/_static/vary.png)

### <a name="nostore-and-locationnone"></a>NoStore 和 Location.None

`NoStore`重写的大多数其他属性。 当此属性设置为`true`、`Cache-Control`标头将设置为"无存储"。 如果`Location`设置为`None`:

* 将 `Cache-Control` 设置为 `"no-store, no-cache"`。 
* 将 `Pragma` 设置为 `no-cache`。 

如果`NoStore`是`false`和`Location`是`None`，`Cache-Control`和`Pragma`将设置为`no-cache`。

通常情况下设置`NoStore`到`true`错误页上。 例如: 

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet1&highlight=1)]

这将导致以下标头：

```javascript
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>位置和持续时间

若要启用缓存，`Duration`必须设置为正值和`Location`必须是`Any`（默认值） 或`Client`。 在这种情况下，`Cache-Control`标头将设置为跟"最大有效期"的响应的位置值。

> [!NOTE]
> `Location`选项的`Any`和`Client`将转换`Cache-Control`的标头值`public`和`private`分别。 按前面所述，设置`Location`到`None`将同时设置`Cache-Control`和`Pragma`标头到`no-cache`。

下面生成通过设置一个示例，演示标头`Duration`并保留默认值`Location`值。

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]

产生以下标头：

```javascript
Cache-Control: public,max-age=60
   ```

### <a name="cache-profiles"></a>缓存配置文件

而不是复制`ResponseCache`设置 MVC 中时，可以将许多控制器操作属性而言，缓存配置文件上的设置配置为选项`ConfigureServices`中的方法`Startup`。 在引用的缓存配置文件中找到值将用作默认值由`ResponseCache`属性，然后在属性上指定任何属性将替代。

设置缓存配置文件：

[!code-csharp[Main](response/sample/Startup.cs?name=snippet1)] 

引用缓存配置文件：

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]

`ResponseCache`特性可应用于操作 （方法），以及控制器 （类）。 方法级别的属性将替代类级别特性中指定的设置。

在上面的示例中，类级别属性指定持续时间为 30 秒，而方法级别属性引用与设置为 60 秒的持续时间的缓存配置文件。

生成的标头：

```
Cache-Control: public,max-age=60
   ```

  ### <a name="additional-resources"></a>其他资源

* [在缓存中 HTTP 规范中](https://tools.ietf.org/html/rfc7234#section-3)
* [缓存控制](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
