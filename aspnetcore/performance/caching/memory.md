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
ms.openlocfilehash: f872cd0c355f7961ae8628c28c62d3b51c8db2c5
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="introduction-to-in-memory-caching-in-aspnet-core"></a><span data-ttu-id="73783-104">内存中缓存中 ASP.NET Core 简介</span><span class="sxs-lookup"><span data-stu-id="73783-104">Introduction to in-memory caching in ASP.NET Core</span></span>

<span data-ttu-id="73783-105">通过[Rick Anderson](https://twitter.com/RickAndMSFT)， [John Luo](https://github.com/JunTaoLuo)，和[Steve Smith](http://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="73783-105">By [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), and [Steve Smith](http://ardalis.com)</span></span>

[<span data-ttu-id="73783-106">查看或下载示例代码</span><span class="sxs-lookup"><span data-stu-id="73783-106">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/memory/sample)

## <a name="caching-basics"></a><span data-ttu-id="73783-107">缓存的基础知识</span><span class="sxs-lookup"><span data-stu-id="73783-107">Caching basics</span></span>

<span data-ttu-id="73783-108">缓存可以显著提高性能和可伸缩性的应用程序通过减少生成内容所需的工作量。</span><span class="sxs-lookup"><span data-stu-id="73783-108">Caching can significantly improve the performance and scalability of an app by reducing the work required to generate content.</span></span> <span data-ttu-id="73783-109">最适合于不经常更改的数据的缓存工作方式。</span><span class="sxs-lookup"><span data-stu-id="73783-109">Caching works best with data that changes infrequently.</span></span> <span data-ttu-id="73783-110">缓存可一份很多可以返回的数据比从原始源更快。</span><span class="sxs-lookup"><span data-stu-id="73783-110">Caching makes a copy of data that can be returned much faster than from the original source.</span></span> <span data-ttu-id="73783-111">你应编写并测试你的应用程序永远不会依赖于缓存的数据。</span><span class="sxs-lookup"><span data-stu-id="73783-111">You should write and test your app to never depend on cached data.</span></span>

<span data-ttu-id="73783-112">ASP.NET 核心支持几个不同的缓存。</span><span class="sxs-lookup"><span data-stu-id="73783-112">ASP.NET Core supports several different caches.</span></span> <span data-ttu-id="73783-113">最简单的缓存基于[IMemoryCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.imemorycache)，它表示存储在内存中的 web 服务器的缓存。</span><span class="sxs-lookup"><span data-stu-id="73783-113">The simplest cache is based on the [IMemoryCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.imemorycache), which represents a cache stored in the memory of the web server.</span></span> <span data-ttu-id="73783-114">在服务器场的多个服务器运行的应用程序应确保使用内存中缓存时，会粘性会话。</span><span class="sxs-lookup"><span data-stu-id="73783-114">Apps which run on a server farm of multiple servers should ensure that sessions are sticky when using the in-memory cache.</span></span> <span data-ttu-id="73783-115">粘性会话确保所有客户端的后续请求转到同一台服务器。</span><span class="sxs-lookup"><span data-stu-id="73783-115">Sticky sessions ensure that subsequent requests from a client all go to the same server.</span></span> <span data-ttu-id="73783-116">例如，Azure Web 应用使用[应用程序请求路由](http://www.iis.net/learn/extensions/planning-for-arr)(ARR) 将所有的后续请求路由到同一台服务器。</span><span class="sxs-lookup"><span data-stu-id="73783-116">For example, Azure Web apps use [Application Request Routing](http://www.iis.net/learn/extensions/planning-for-arr) (ARR) to route all subsequent requests to the same server.</span></span>

<span data-ttu-id="73783-117">Web 场中的非粘性会话需要[分布式缓存](distributed.md)以避免缓存一致性问题。</span><span class="sxs-lookup"><span data-stu-id="73783-117">Non-sticky sessions in a web farm require a [distributed cache](distributed.md) to avoid cache consistency problems.</span></span> <span data-ttu-id="73783-118">对于某些应用，分布式的缓存可以支持更高版本横向扩展比内存中缓存。</span><span class="sxs-lookup"><span data-stu-id="73783-118">For some apps, a distributed cache can support higher scale out than an in-memory cache.</span></span> <span data-ttu-id="73783-119">使用分布式的缓存将卸载到外部进程缓存内存。</span><span class="sxs-lookup"><span data-stu-id="73783-119">Using a distributed cache offloads the cache memory to an external process.</span></span> 

<span data-ttu-id="73783-120">`IMemoryCache`缓存将逐出缓存条目内存压力下的，除非[缓存优先级](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheitempriority)设置为`CacheItemPriority.NeverRemove`。</span><span class="sxs-lookup"><span data-stu-id="73783-120">The `IMemoryCache` cache will evict cache entries under memory pressure unless the [cache priority](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheitempriority) is set to `CacheItemPriority.NeverRemove`.</span></span> <span data-ttu-id="73783-121">你可以设置`CacheItemPriority`调整优先级缓存逐出内存压力下的项。</span><span class="sxs-lookup"><span data-stu-id="73783-121">You can set the `CacheItemPriority` to adjust the priority the cache evicts items under memory pressure.</span></span>

<span data-ttu-id="73783-122">内存中缓存中可以存储任何对象;分布式的缓存接口仅限于`byte[]`。</span><span class="sxs-lookup"><span data-stu-id="73783-122">The in-memory cache can store any object; the distributed cache interface is limited to `byte[]`.</span></span>

## <a name="using-imemorycache"></a><span data-ttu-id="73783-123">使用 IMemoryCache</span><span class="sxs-lookup"><span data-stu-id="73783-123">Using IMemoryCache</span></span>

<span data-ttu-id="73783-124">内存中缓存是*服务*引用从你的应用使用[依赖关系注入](../../fundamentals/dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="73783-124">In-memory caching is a *service* that is referenced from your app using [Dependency Injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="73783-125">调用`AddMemoryCache`中`ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="73783-125">Call `AddMemoryCache` in `ConfigureServices`:</span></span>

<span data-ttu-id="73783-126">[!code-csharp[Main](memory/sample/WebCache/Startup.cs?highlight=8)]</span><span class="sxs-lookup"><span data-stu-id="73783-126">[!code-csharp[Main](memory/sample/WebCache/Startup.cs?highlight=8)]</span></span> 

<span data-ttu-id="73783-127">请求`IMemoryCache`构造函数中的实例：</span><span class="sxs-lookup"><span data-stu-id="73783-127">Request the `IMemoryCache` instance in the constructor:</span></span>

<span data-ttu-id="73783-128">[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor&highlight=3,5-)]</span><span class="sxs-lookup"><span data-stu-id="73783-128">[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor&highlight=3,5-)]</span></span> 

<span data-ttu-id="73783-129">`IMemoryCache`需要 NuGet 包"Microsoft.Extensions.Caching.Memory"。</span><span class="sxs-lookup"><span data-stu-id="73783-129">`IMemoryCache` requires NuGet package "Microsoft.Extensions.Caching.Memory".</span></span>

<span data-ttu-id="73783-130">下面的代码使用[TryGetValue](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.imemorycache#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__)检查当前时间是否在缓存中。</span><span class="sxs-lookup"><span data-stu-id="73783-130">The following code uses [TryGetValue](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.imemorycache#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) to check if the current time is in the cache.</span></span> <span data-ttu-id="73783-131">如果未缓存项，创建并添加到缓存中，使用新的条目[设置](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_)。</span><span class="sxs-lookup"><span data-stu-id="73783-131">If the item is not cached, a new entry is created and added to the cache with [Set](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_).</span></span>

<span data-ttu-id="73783-132">[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="73783-132">[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]</span></span>

<span data-ttu-id="73783-133">显示当前时间和缓存的时间：</span><span class="sxs-lookup"><span data-stu-id="73783-133">The current time and the cached time is displayed:</span></span>

<span data-ttu-id="73783-134">[!code-html[Main](memory/sample/WebCache/Views/Home/Cache.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="73783-134">[!code-html[Main](memory/sample/WebCache/Views/Home/Cache.cshtml)]</span></span>

<span data-ttu-id="73783-135">已缓存`DateTime`值将保留在缓存中，尽管在超时期限 （和由于内存压力没有逐出） 的请求。</span><span class="sxs-lookup"><span data-stu-id="73783-135">The cached `DateTime` value will remain in the cache while there are requests within the timeout period (and no eviction due to memory pressure).</span></span> <span data-ttu-id="73783-136">下图显示当前时间和较旧时间从缓存中检索：</span><span class="sxs-lookup"><span data-stu-id="73783-136">The image below shows the current time and an older time retrieved from cache:</span></span>

![具有两个不同的时间显示的索引视图](memory/_static/time.png)

<span data-ttu-id="73783-138">下面的代码使用[GetOrCreate](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__)和[GetOrCreateAsync](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___)缓存数据。</span><span class="sxs-lookup"><span data-stu-id="73783-138">The following code uses [GetOrCreate](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) and [GetOrCreateAsync](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) to cache data.</span></span> 

<span data-ttu-id="73783-139">[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]</span><span class="sxs-lookup"><span data-stu-id="73783-139">[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]</span></span>

<span data-ttu-id="73783-140">下面的代码调用[获取](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)提取缓存的时间：</span><span class="sxs-lookup"><span data-stu-id="73783-140">The following code calls [Get](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) to fetch the cached time:</span></span>

<span data-ttu-id="73783-141">[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]</span><span class="sxs-lookup"><span data-stu-id="73783-141">[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]</span></span>

<span data-ttu-id="73783-142">请参阅[IMemoryCache 方法](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.imemorycache)和[CacheExtensions 方法](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions)有关缓存方法的说明。</span><span class="sxs-lookup"><span data-stu-id="73783-142">See [IMemoryCache methods](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.imemorycache) and [CacheExtensions methods](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions) for a description of the cache methods.</span></span>

## <a name="using-memorycacheentryoptions"></a><span data-ttu-id="73783-143">使用 MemoryCacheEntryOptions</span><span class="sxs-lookup"><span data-stu-id="73783-143">Using MemoryCacheEntryOptions</span></span>

<span data-ttu-id="73783-144">下面的示例：</span><span class="sxs-lookup"><span data-stu-id="73783-144">The following sample:</span></span>

- <span data-ttu-id="73783-145">设置绝对到期时间。</span><span class="sxs-lookup"><span data-stu-id="73783-145">Sets the absolute expiration time.</span></span> <span data-ttu-id="73783-146">这是可以缓存条目的最长时间，可防止项持续续订滑动过期时变得太陈旧。</span><span class="sxs-lookup"><span data-stu-id="73783-146">This is the maximum time the entry can be cached and prevents the item from becoming too stale when the sliding expiration is continuously renewed.</span></span>
- <span data-ttu-id="73783-147">设置滑动过期时间。</span><span class="sxs-lookup"><span data-stu-id="73783-147">Sets a sliding expiration time.</span></span> <span data-ttu-id="73783-148">访问此缓存的项的请求将重置滑动到期时钟。</span><span class="sxs-lookup"><span data-stu-id="73783-148">Requests that access this cached item will reset the sliding expiration clock.</span></span>
- <span data-ttu-id="73783-149">将缓存优先级设置为`CacheItemPriority.NeverRemove`。</span><span class="sxs-lookup"><span data-stu-id="73783-149">Sets the cache priority to `CacheItemPriority.NeverRemove`.</span></span> 
- <span data-ttu-id="73783-150">集[PostEvictionDelegate](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.postevictiondelegate)后从缓存逐出项，将调用的。</span><span class="sxs-lookup"><span data-stu-id="73783-150">Sets a [PostEvictionDelegate](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.postevictiondelegate) that will be called after the entry is evicted from the cache.</span></span> <span data-ttu-id="73783-151">回调是在从缓存中移除的项的代码与不同的线程上运行。</span><span class="sxs-lookup"><span data-stu-id="73783-151">The callback is run on a different thread from the code that removes the item from the cache.</span></span>

<span data-ttu-id="73783-152">[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-20)]</span><span class="sxs-lookup"><span data-stu-id="73783-152">[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-20)]</span></span>

## <a name="cache-dependencies"></a><span data-ttu-id="73783-153">缓存依赖项</span><span class="sxs-lookup"><span data-stu-id="73783-153">Cache dependencies</span></span>

<span data-ttu-id="73783-154">下面的示例演示如何过期的缓存项，如果依赖项将过期。</span><span class="sxs-lookup"><span data-stu-id="73783-154">The following sample shows how to expire a cache entry if a dependent entry expires.</span></span> <span data-ttu-id="73783-155">A`CancellationChangeToken`添加到缓存的项。</span><span class="sxs-lookup"><span data-stu-id="73783-155">A `CancellationChangeToken` is added to the cached item.</span></span> <span data-ttu-id="73783-156">当`Cancel`上调用`CancellationTokenSource`，这两个缓存条目被逐出。</span><span class="sxs-lookup"><span data-stu-id="73783-156">When `Cancel` is called on the `CancellationTokenSource`, both cache entries are evicted.</span></span> 

<span data-ttu-id="73783-157">[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]</span><span class="sxs-lookup"><span data-stu-id="73783-157">[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]</span></span>

<span data-ttu-id="73783-158">使用`CancellationTokenSource`允许多个缓存条目被逐出作为一个组。</span><span class="sxs-lookup"><span data-stu-id="73783-158">Using a `CancellationTokenSource` allows multiple cache entries to be evicted as a group.</span></span> <span data-ttu-id="73783-159">与`using`在上面的代码的模式，在内部创建的缓存条目`using`块将继承触发器和过期时间设置。</span><span class="sxs-lookup"><span data-stu-id="73783-159">With the `using` pattern in the code above, cache entries created inside the `using` block will inherit triggers and expiration settings.</span></span>

### <a name="additional-notes"></a><span data-ttu-id="73783-160">附加说明</span><span class="sxs-lookup"><span data-stu-id="73783-160">Additional notes</span></span>

- <span data-ttu-id="73783-161">当使用的回调以重新填充缓存项：</span><span class="sxs-lookup"><span data-stu-id="73783-161">When using a callback to repopulate a cache item:</span></span>

  - <span data-ttu-id="73783-162">多个请求可以查找缓存的键值空因为尚未完成的回调。</span><span class="sxs-lookup"><span data-stu-id="73783-162">Multiple requests can find the cached key value empty because the callback hasn't completed.</span></span> 
  - <span data-ttu-id="73783-163">这可能导致重新填充缓存的项的多个线程。</span><span class="sxs-lookup"><span data-stu-id="73783-163">This can result in several threads repopulating the cached item.</span></span>

- <span data-ttu-id="73783-164">当一个缓存条目用于创建另一个时，子复制父项的过期的令牌和基于时间的过期时间设置。</span><span class="sxs-lookup"><span data-stu-id="73783-164">When one cache entry is used to create another, the child copies the parent entry's expiration tokens and time-based expiration settings.</span></span> <span data-ttu-id="73783-165">子级不是通过手动删除过期的或更新的父项。</span><span class="sxs-lookup"><span data-stu-id="73783-165">The child is not expired by manual removal or updating of the parent entry.</span></span>

### <a name="other-resources"></a><span data-ttu-id="73783-166">其他资源</span><span class="sxs-lookup"><span data-stu-id="73783-166">Other Resources</span></span>

* [<span data-ttu-id="73783-167">使用分布式缓存</span><span class="sxs-lookup"><span data-stu-id="73783-167">Working with a Distributed Cache</span></span>](distributed.md)
* [<span data-ttu-id="73783-168">响应缓存中间件</span><span class="sxs-lookup"><span data-stu-id="73783-168">Response caching middleware</span></span>](middleware.md)
