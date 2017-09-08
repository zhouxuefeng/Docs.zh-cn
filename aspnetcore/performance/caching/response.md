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
# <a name="response-caching"></a><span data-ttu-id="0d543-104">响应缓存</span><span class="sxs-lookup"><span data-stu-id="0d543-104">Response Caching</span></span>

<span data-ttu-id="0d543-105">通过[John Luo](https://github.com/JunTaoLuo)， [Rick Anderson](https://twitter.com/RickAndMSFT)，和[Steve Smith](http://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="0d543-105">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](http://ardalis.com)</span></span>

[<span data-ttu-id="0d543-106">查看或下载示例代码</span><span class="sxs-lookup"><span data-stu-id="0d543-106">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/sample)

## <a name="what-is-response-caching"></a><span data-ttu-id="0d543-107">什么是响应缓存</span><span class="sxs-lookup"><span data-stu-id="0d543-107">What is Response Caching</span></span>

<span data-ttu-id="0d543-108">*响应缓存*提高响应的缓存相关的标头。</span><span class="sxs-lookup"><span data-stu-id="0d543-108">*Response caching* adds cache-related headers to responses.</span></span> <span data-ttu-id="0d543-109">这些标头指定你希望客户端、 代理和缓存响应的中间件如何。</span><span class="sxs-lookup"><span data-stu-id="0d543-109">These headers specify how you want client, proxy and middleware to cache responses.</span></span> <span data-ttu-id="0d543-110">响应缓存可以减少客户端或代理向 web 服务器发出的请求数。</span><span class="sxs-lookup"><span data-stu-id="0d543-110">Response caching can reduce the number of requests a client or proxy makes to the web server.</span></span> <span data-ttu-id="0d543-111">响应缓存可以减少量工作的 web 服务器执行以生成响应。</span><span class="sxs-lookup"><span data-stu-id="0d543-111">Response caching can also reduce the amount of work the web server performs to generate the response.</span></span> 

<span data-ttu-id="0d543-112">用于缓存的主 HTTP 标头是`Cache-Control`。</span><span class="sxs-lookup"><span data-stu-id="0d543-112">The primary HTTP header used for caching is `Cache-Control`.</span></span> <span data-ttu-id="0d543-113">请参阅[HTTP 1.1 缓存](https://tools.ietf.org/html/rfc7234#section-5.2)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="0d543-113">See the [HTTP 1.1 Caching](https://tools.ietf.org/html/rfc7234#section-5.2) for more information.</span></span> <span data-ttu-id="0d543-114">常见的缓存指令：</span><span class="sxs-lookup"><span data-stu-id="0d543-114">Common cache directives:</span></span>

* [<span data-ttu-id="0d543-115">公用</span><span class="sxs-lookup"><span data-stu-id="0d543-115">public</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)
* [<span data-ttu-id="0d543-116">专用</span><span class="sxs-lookup"><span data-stu-id="0d543-116">private</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)
* [<span data-ttu-id="0d543-117">无缓存</span><span class="sxs-lookup"><span data-stu-id="0d543-117">no-cache</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.4)
* [<span data-ttu-id="0d543-118">杂注</span><span class="sxs-lookup"><span data-stu-id="0d543-118">Pragma</span></span>](https://tools.ietf.org/html/rfc7234#section-5.4)
* [<span data-ttu-id="0d543-119">改变</span><span class="sxs-lookup"><span data-stu-id="0d543-119">Vary</span></span>](https://tools.ietf.org/html/rfc7231#section-7.1.4)

<span data-ttu-id="0d543-120">Web 服务器可以通过添加缓存中间件的响应缓存响应。</span><span class="sxs-lookup"><span data-stu-id="0d543-120">The web server can cache responses by adding the response caching middleware.</span></span> <span data-ttu-id="0d543-121">请参阅[响应缓存中间件](middleware.md)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="0d543-121">See [Response caching middleware](middleware.md) for more information.</span></span>

## <a name="distributed-cache-tag-helper"></a><span data-ttu-id="0d543-122">分布式的缓存标记帮助器</span><span class="sxs-lookup"><span data-stu-id="0d543-122">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="0d543-123">[分布式缓存标记帮助器](xref:mvc/views/tag-helpers/builtin-th/DistributedCacheTagHelper)使地理上分散缓存。</span><span class="sxs-lookup"><span data-stu-id="0d543-123">The [Distributed Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/DistributedCacheTagHelper) enables distributed caching.</span></span>


## <a name="responsecache-attribute"></a><span data-ttu-id="0d543-124">ResponseCache 属性</span><span class="sxs-lookup"><span data-stu-id="0d543-124">ResponseCache Attribute</span></span>

<span data-ttu-id="0d543-125">`ResponseCacheAttribute`指定在缓存中，响应设置适当的标头的所需的参数。</span><span class="sxs-lookup"><span data-stu-id="0d543-125">The `ResponseCacheAttribute` specifies the parameters necessary for setting appropriate headers in response caching.</span></span> <span data-ttu-id="0d543-126">请参阅[ResponseCacheAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.responsecacheattribute)有关参数的说明。</span><span class="sxs-lookup"><span data-stu-id="0d543-126">See [ResponseCacheAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.responsecacheattribute)  for a description of the parameters.</span></span>

>[!WARNING]
> <span data-ttu-id="0d543-127">禁用缓存的内容，其中包含已经过身份验证的客户端的信息。</span><span class="sxs-lookup"><span data-stu-id="0d543-127">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="0d543-128">不会更改的内容根据用户的标识，或是否已登录用户应仅启用缓存。</span><span class="sxs-lookup"><span data-stu-id="0d543-128">Caching should only be enabled for content that does not change based on a user's identity, or whether a user is logged in.</span></span>

<span data-ttu-id="0d543-129">`VaryByQueryKeys string[]`(需要 ASP.NET Core 1.1.0 和更高版本): 设置时，响应缓存中间件将因存储的响应的查询密钥的给定列表的值。</span><span class="sxs-lookup"><span data-stu-id="0d543-129">`VaryByQueryKeys string[]` (requires ASP.NET Core 1.1.0 and higher): When set, the response caching middleware will vary the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="0d543-130">必须启用缓存中间件的响应设置`VaryByQueryKeys`属性，否则运行时将引发异常。</span><span class="sxs-lookup"><span data-stu-id="0d543-130">The response caching middleware must be enabled to set the `VaryByQueryKeys` property, otherwise a runtime exception will be thrown.</span></span> <span data-ttu-id="0d543-131">没有为没有相应的 HTTP 标头`VaryByQueryKeys`属性。</span><span class="sxs-lookup"><span data-stu-id="0d543-131">There is no corresponding HTTP header for the `VaryByQueryKeys` property.</span></span> <span data-ttu-id="0d543-132">此属性是一项 HTTP 功能由缓存中间件的响应。</span><span class="sxs-lookup"><span data-stu-id="0d543-132">This property is an HTTP feature handled by the response caching middleware.</span></span> <span data-ttu-id="0d543-133">若要提供缓存的响应的中间件，对于查询字符串和查询字符串值必须匹配上一个请求。</span><span class="sxs-lookup"><span data-stu-id="0d543-133">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="0d543-134">例如，考虑以下序列：</span><span class="sxs-lookup"><span data-stu-id="0d543-134">For example, consider the following sequence:</span></span>

| <span data-ttu-id="0d543-135">请求</span><span class="sxs-lookup"><span data-stu-id="0d543-135">Request</span></span>          | <span data-ttu-id="0d543-136">结果</span><span class="sxs-lookup"><span data-stu-id="0d543-136">Result</span></span> |
| ----------------- | ------------ | 
| `http://example.com?key1=value1` | <span data-ttu-id="0d543-137">从服务器返回</span><span class="sxs-lookup"><span data-stu-id="0d543-137">returned from server</span></span> |
| `http://example.com?key1=value1` | <span data-ttu-id="0d543-138">返回从中间件</span><span class="sxs-lookup"><span data-stu-id="0d543-138">returned from middleware</span></span> |
| `http://example.com?key1=value2` | <span data-ttu-id="0d543-139">从服务器返回</span><span class="sxs-lookup"><span data-stu-id="0d543-139">returned from server</span></span> |

<span data-ttu-id="0d543-140">第一个请求是由服务器返回，并在中间件中缓冲。</span><span class="sxs-lookup"><span data-stu-id="0d543-140">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="0d543-141">由于查询字符串与上一个请求，中间件会返回第二个请求。</span><span class="sxs-lookup"><span data-stu-id="0d543-141">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="0d543-142">第三个请求都不处于中间件缓存中，因为查询字符串值不匹配的上一个请求。</span><span class="sxs-lookup"><span data-stu-id="0d543-142">The third request is not in the middleware cache because the query string value doesn't match a previous request.</span></span> 

<span data-ttu-id="0d543-143">`ResponseCacheAttribute`用于配置和创建 (通过`IFilterFactory`) `ResponseCacheFilter`。</span><span class="sxs-lookup"><span data-stu-id="0d543-143">The `ResponseCacheAttribute` is used to configure and create (via `IFilterFactory`) a `ResponseCacheFilter`.</span></span> <span data-ttu-id="0d543-144">`ResponseCacheFilter`执行的工作的更新的相应 HTTP 标头和响应功能。</span><span class="sxs-lookup"><span data-stu-id="0d543-144">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="0d543-145">筛选器：</span><span class="sxs-lookup"><span data-stu-id="0d543-145">The filter:</span></span>

* <span data-ttu-id="0d543-146">删除任何现有标头`Vary`， `Cache-Control`，和`Pragma`。</span><span class="sxs-lookup"><span data-stu-id="0d543-146">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span> 
* <span data-ttu-id="0d543-147">写出相应的标头设置的属性上基于`ResponseCacheAttribute`。</span><span class="sxs-lookup"><span data-stu-id="0d543-147">Writes out the appropriate headers based on the properties set in the `ResponseCacheAttribute`.</span></span> 
* <span data-ttu-id="0d543-148">更新缓存中项 HTTP 功能，如果响应`VaryByQueryKeys`设置。</span><span class="sxs-lookup"><span data-stu-id="0d543-148">Updates the response caching HTTP feature if `VaryByQueryKeys` is set.</span></span>

### <a name="vary"></a><span data-ttu-id="0d543-149">改变</span><span class="sxs-lookup"><span data-stu-id="0d543-149">Vary</span></span>

<span data-ttu-id="0d543-150">此标头只会写入时`VaryByHeader`属性设置。</span><span class="sxs-lookup"><span data-stu-id="0d543-150">This header is only written when the `VaryByHeader` property is set.</span></span> <span data-ttu-id="0d543-151">设置为`Vary`属性的值。</span><span class="sxs-lookup"><span data-stu-id="0d543-151">It is set to the `Vary` property's value.</span></span> <span data-ttu-id="0d543-152">下面的示例使用`VaryByHeader`属性。</span><span class="sxs-lookup"><span data-stu-id="0d543-152">The following sample uses the `VaryByHeader` property.</span></span>

<span data-ttu-id="0d543-153">[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]</span><span class="sxs-lookup"><span data-stu-id="0d543-153">[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]</span></span>

<span data-ttu-id="0d543-154">你可以使用你的浏览器的网络工具查看响应标头。</span><span class="sxs-lookup"><span data-stu-id="0d543-154">You can view the response headers with your browsers network tools.</span></span> <span data-ttu-id="0d543-155">下图显示了输出上边缘 F12**网络**选项卡上时`About2`刷新操作方法。</span><span class="sxs-lookup"><span data-stu-id="0d543-155">The following image shows the Edge F12 output on the **Network** tab when the `About2` action method is refreshed.</span></span> 

![在边缘 F12 输出 * * 网络 * * 选项卡的 About2 操作方法调用时](response/_static/vary.png)

### <a name="nostore-and-locationnone"></a><span data-ttu-id="0d543-157">NoStore 和 Location.None</span><span class="sxs-lookup"><span data-stu-id="0d543-157">NoStore and Location.None</span></span>

<span data-ttu-id="0d543-158">`NoStore`重写的大多数其他属性。</span><span class="sxs-lookup"><span data-stu-id="0d543-158">`NoStore` overrides most of the other properties.</span></span> <span data-ttu-id="0d543-159">当此属性设置为`true`、`Cache-Control`标头将设置为"无存储"。</span><span class="sxs-lookup"><span data-stu-id="0d543-159">When this property is set to `true`, the `Cache-Control` header will be set to "no-store".</span></span> <span data-ttu-id="0d543-160">如果`Location`设置为`None`:</span><span class="sxs-lookup"><span data-stu-id="0d543-160">If `Location` is set to `None`:</span></span>

* <span data-ttu-id="0d543-161">将 `Cache-Control` 设置为 `"no-store, no-cache"`。</span><span class="sxs-lookup"><span data-stu-id="0d543-161">`Cache-Control` is set to `"no-store, no-cache"`.</span></span> 
* <span data-ttu-id="0d543-162">将 `Pragma` 设置为 `no-cache`。</span><span class="sxs-lookup"><span data-stu-id="0d543-162">`Pragma` is set to `no-cache`.</span></span> 

<span data-ttu-id="0d543-163">如果`NoStore`是`false`和`Location`是`None`，`Cache-Control`和`Pragma`将设置为`no-cache`。</span><span class="sxs-lookup"><span data-stu-id="0d543-163">If `NoStore` is `false` and `Location` is `None`,  `Cache-Control` and `Pragma` will be set to `no-cache`.</span></span>

<span data-ttu-id="0d543-164">通常情况下设置`NoStore`到`true`错误页上。</span><span class="sxs-lookup"><span data-stu-id="0d543-164">You typically set `NoStore` to `true` on error pages.</span></span> <span data-ttu-id="0d543-165">例如: </span><span class="sxs-lookup"><span data-stu-id="0d543-165">For example:</span></span>

<span data-ttu-id="0d543-166">[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet1&highlight=1)]</span><span class="sxs-lookup"><span data-stu-id="0d543-166">[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet1&highlight=1)]</span></span>

<span data-ttu-id="0d543-167">这将导致以下标头：</span><span class="sxs-lookup"><span data-stu-id="0d543-167">This will result in the following headers:</span></span>

```javascript
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="0d543-168">位置和持续时间</span><span class="sxs-lookup"><span data-stu-id="0d543-168">Location and Duration</span></span>

<span data-ttu-id="0d543-169">若要启用缓存，`Duration`必须设置为正值和`Location`必须是`Any`（默认值） 或`Client`。</span><span class="sxs-lookup"><span data-stu-id="0d543-169">To enable caching, `Duration` must be set to a positive value and `Location` must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="0d543-170">在这种情况下，`Cache-Control`标头将设置为跟"最大有效期"的响应的位置值。</span><span class="sxs-lookup"><span data-stu-id="0d543-170">In this case, the `Cache-Control` header will be set to the location value followed by the "max-age" of the response.</span></span>

> [!NOTE]
> <span data-ttu-id="0d543-171">`Location`选项的`Any`和`Client`将转换`Cache-Control`的标头值`public`和`private`分别。</span><span class="sxs-lookup"><span data-stu-id="0d543-171">`Location`'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="0d543-172">按前面所述，设置`Location`到`None`将同时设置`Cache-Control`和`Pragma`标头到`no-cache`。</span><span class="sxs-lookup"><span data-stu-id="0d543-172">As noted previously, setting `Location` to `None` will set both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="0d543-173">下面生成通过设置一个示例，演示标头`Duration`并保留默认值`Location`值。</span><span class="sxs-lookup"><span data-stu-id="0d543-173">Below is an example showing the headers produced by setting `Duration` and leaving the default `Location` value.</span></span>

<span data-ttu-id="0d543-174">[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]</span><span class="sxs-lookup"><span data-stu-id="0d543-174">[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]</span></span>

<span data-ttu-id="0d543-175">产生以下标头：</span><span class="sxs-lookup"><span data-stu-id="0d543-175">Produces the following headers:</span></span>

```javascript
Cache-Control: public,max-age=60
   ```

### <a name="cache-profiles"></a><span data-ttu-id="0d543-176">缓存配置文件</span><span class="sxs-lookup"><span data-stu-id="0d543-176">Cache Profiles</span></span>

<span data-ttu-id="0d543-177">而不是复制`ResponseCache`设置 MVC 中时，可以将许多控制器操作属性而言，缓存配置文件上的设置配置为选项`ConfigureServices`中的方法`Startup`。</span><span class="sxs-lookup"><span data-stu-id="0d543-177">Instead of duplicating `ResponseCache` settings on many controller action attributes, cache profiles can be configured as options when setting up MVC in the `ConfigureServices` method in `Startup`.</span></span> <span data-ttu-id="0d543-178">在引用的缓存配置文件中找到值将用作默认值由`ResponseCache`属性，然后在属性上指定任何属性将替代。</span><span class="sxs-lookup"><span data-stu-id="0d543-178">Values found in a referenced cache profile will be used as the defaults by the `ResponseCache` attribute, and will be overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="0d543-179">设置缓存配置文件：</span><span class="sxs-lookup"><span data-stu-id="0d543-179">Setting up a cache profile:</span></span>

<span data-ttu-id="0d543-180">[!code-csharp[Main](response/sample/Startup.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="0d543-180">[!code-csharp[Main](response/sample/Startup.cs?name=snippet1)]</span></span> 

<span data-ttu-id="0d543-181">引用缓存配置文件：</span><span class="sxs-lookup"><span data-stu-id="0d543-181">Referencing a cache profile:</span></span>

<span data-ttu-id="0d543-182">[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]</span><span class="sxs-lookup"><span data-stu-id="0d543-182">[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]</span></span>

<span data-ttu-id="0d543-183">`ResponseCache`特性可应用于操作 （方法），以及控制器 （类）。</span><span class="sxs-lookup"><span data-stu-id="0d543-183">The `ResponseCache` attribute can be applied both to actions (methods) as well as controllers (classes).</span></span> <span data-ttu-id="0d543-184">方法级别的属性将替代类级别特性中指定的设置。</span><span class="sxs-lookup"><span data-stu-id="0d543-184">Method-level attributes will override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="0d543-185">在上面的示例中，类级别属性指定持续时间为 30 秒，而方法级别属性引用与设置为 60 秒的持续时间的缓存配置文件。</span><span class="sxs-lookup"><span data-stu-id="0d543-185">In the above example, a class-level attribute specifies a duration of 30 seconds while a method-level attributes references a cache profile with a duration set to 60 seconds.</span></span>

<span data-ttu-id="0d543-186">生成的标头：</span><span class="sxs-lookup"><span data-stu-id="0d543-186">The resulting header:</span></span>

```
Cache-Control: public,max-age=60
   ```

  ### <a name="additional-resources"></a><span data-ttu-id="0d543-187">其他资源</span><span class="sxs-lookup"><span data-stu-id="0d543-187">Additional Resources</span></span>

* [<span data-ttu-id="0d543-188">在缓存中 HTTP 规范中</span><span class="sxs-lookup"><span data-stu-id="0d543-188">Caching in HTTP from the specification</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="0d543-189">缓存控制</span><span class="sxs-lookup"><span data-stu-id="0d543-189">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
