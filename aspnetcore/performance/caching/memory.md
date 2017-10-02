---
title: "ASP.NET 核心中的内存中缓存"
author: rick-anderson
description: "演示如何在 ASP.NET Core 中的内存中缓存数据。"
keywords: "ASP.NET 核心，缓存中中的内存性能"
ms.author: riande
manager: wpickett
ms.date: 12/14/2016
ms.topic: article
ms.assetid: 819511cf-d33e-410a-b5a9-bef7fa64d2f3
ms.technology: aspnet
ms.prod: asp.net-core
uid: performance/caching/memory
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5ce865427b6ca44c76888908fdeea9cd45c881c4
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2017
---
# <a name="introduction-to-in-memory-caching-in-aspnet-core"></a>内存中缓存中 ASP.NET Core 简介

通过[Rick Anderson](https://twitter.com/RickAndMSFT)， [John Luo](https://github.com/JunTaoLuo)，和[Steve Smith](https://ardalis.com/)

[查看或下载的示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/memory/sample)([如何下载](xref:tutorials/index#how-to-download-a-sample))

## <a name="caching-basics"></a>缓存的基础知识

缓存可以显著提高性能和可伸缩性的应用程序通过减少生成内容所需的工作量。 最适合于不经常更改的数据的缓存工作方式。 缓存可一份很多可以返回的数据比从原始源更快。 你应编写并测试你的应用程序永远不会依赖于缓存的数据。

ASP.NET 核心支持几个不同的缓存。 最简单的缓存基于[IMemoryCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.imemorycache)，它表示存储在内存中的 web 服务器的缓存。 在服务器场的多个服务器运行的应用程序应确保使用内存中缓存时，会粘性会话。 粘性会话确保所有客户端的后续请求转到同一台服务器。 例如，Azure Web 应用使用[应用程序请求路由](https://www.iis.net/learn/extensions/planning-for-arr)(ARR) 将所有的后续请求路由到同一台服务器。

Web 场中的非粘性会话需要[分布式缓存](distributed.md)以避免缓存一致性问题。 对于某些应用，分布式的缓存可以支持更高版本横向扩展比内存中缓存。 使用分布式的缓存将卸载到外部进程缓存内存。 

`IMemoryCache`缓存将逐出缓存条目内存压力下的，除非[缓存优先级](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheitempriority)设置为`CacheItemPriority.NeverRemove`。 你可以设置`CacheItemPriority`调整优先级缓存逐出内存压力下的项。

内存中缓存中可以存储任何对象;分布式的缓存接口仅限于`byte[]`。

## <a name="using-imemorycache"></a>使用 IMemoryCache

内存中缓存是*服务*引用从你的应用使用[依赖关系注入](../../fundamentals/dependency-injection.md)。 调用`AddMemoryCache`中`ConfigureServices`:

[!code-csharp[Main](memory/sample/WebCache/Startup.cs?highlight=8)] 

请求`IMemoryCache`构造函数中的实例：

[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor&highlight=3,5-)] 

`IMemoryCache`需要 NuGet 包"Microsoft.Extensions.Caching.Memory"。

下面的代码使用[TryGetValue](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.imemorycache#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__)检查当前时间是否在缓存中。 如果未缓存项，创建并添加到缓存中，使用新的条目[设置](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_)。

[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

显示当前时间和缓存的时间：

[!code-html[Main](memory/sample/WebCache/Views/Home/Cache.cshtml)]

已缓存`DateTime`值将保留在缓存中，尽管在超时期限 （和由于内存压力没有逐出） 的请求。 下图显示当前时间和较旧时间从缓存中检索：

![具有两个不同的时间显示的索引视图](memory/_static/time.png)

下面的代码使用[GetOrCreate](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__)和[GetOrCreateAsync](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___)缓存数据。 

[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

下面的代码调用[获取](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)提取缓存的时间：

[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

请参阅[IMemoryCache 方法](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.imemorycache)和[CacheExtensions 方法](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions)有关缓存方法的说明。

## <a name="using-memorycacheentryoptions"></a>使用 MemoryCacheEntryOptions

下面的示例：

- 设置绝对到期时间。 这是可以缓存条目的最长时间，可防止项持续续订滑动过期时变得太陈旧。
- 设置滑动过期时间。 访问此缓存的项的请求将重置滑动到期时钟。
- 将缓存优先级设置为`CacheItemPriority.NeverRemove`。 
- 集[PostEvictionDelegate](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.postevictiondelegate)后从缓存逐出项，将调用的。 回调是在从缓存中移除的项的代码与不同的线程上运行。

[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-20)]

## <a name="cache-dependencies"></a>缓存依赖项

下面的示例演示如何过期的缓存项，如果依赖项将过期。 A`CancellationChangeToken`添加到缓存的项。 当`Cancel`上调用`CancellationTokenSource`，这两个缓存条目被逐出。 

[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

使用`CancellationTokenSource`允许多个缓存条目被逐出作为一个组。 与`using`在上面的代码的模式，在内部创建的缓存条目`using`块将继承触发器和过期时间设置。

### <a name="additional-notes"></a>附加说明

- 当使用的回调以重新填充缓存项：

  - 多个请求可以查找缓存的键值空因为尚未完成的回调。 
  - 这可能导致重新填充缓存的项的多个线程。

- 当一个缓存条目用于创建另一个时，子复制父项的过期的令牌和基于时间的过期时间设置。 子级不是通过手动删除过期的或更新的父项。

### <a name="other-resources"></a>其他资源

* [使用分布式缓存](distributed.md)
* [响应缓存中间件](middleware.md)
