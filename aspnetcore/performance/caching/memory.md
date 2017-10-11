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
# <a name="introduction-to-in-memory-caching-in-aspnet-core"></a><span data-ttu-id="e821e-104">内存中缓存中 ASP.NET Core 简介</span><span class="sxs-lookup"><span data-stu-id="e821e-104">Introduction to in-memory caching in ASP.NET Core</span></span>

<span data-ttu-id="e821e-105">通过[Rick Anderson](https://twitter.com/RickAndMSFT)， [John Luo](https://github.com/JunTaoLuo)，和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="e821e-105">By [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="e821e-106">[查看或下载的示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/memory/sample)([如何下载](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e821e-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="caching-basics"></a><span data-ttu-id="e821e-107">缓存的基础知识</span><span class="sxs-lookup"><span data-stu-id="e821e-107">Caching basics</span></span>

<span data-ttu-id="e821e-108">缓存可以显著提高性能和可伸缩性的应用程序通过减少生成内容所需的工作量。</span><span class="sxs-lookup"><span data-stu-id="e821e-108">Caching can significantly improve the performance and scalability of an app by reducing the work required to generate content.</span></span> <span data-ttu-id="e821e-109">最适合于不经常更改的数据的缓存工作方式。</span><span class="sxs-lookup"><span data-stu-id="e821e-109">Caching works best with data that changes infrequently.</span></span> <span data-ttu-id="e821e-110">缓存可一份很多可以返回的数据比从原始源更快。</span><span class="sxs-lookup"><span data-stu-id="e821e-110">Caching makes a copy of data that can be returned much faster than from the original source.</span></span> <span data-ttu-id="e821e-111">你应编写并测试你的应用程序永远不会依赖于缓存的数据。</span><span class="sxs-lookup"><span data-stu-id="e821e-111">You should write and test your app to never depend on cached data.</span></span>

<span data-ttu-id="e821e-112">ASP.NET 核心支持几个不同的缓存。</span><span class="sxs-lookup"><span data-stu-id="e821e-112">ASP.NET Core supports several different caches.</span></span> <span data-ttu-id="e821e-113">最简单的缓存基于[IMemoryCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.imemorycache)，它表示存储在内存中的 web 服务器的缓存。</span><span class="sxs-lookup"><span data-stu-id="e821e-113">The simplest cache is based on the [IMemoryCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.imemorycache), which represents a cache stored in the memory of the web server.</span></span> <span data-ttu-id="e821e-114">在服务器场的多个服务器运行的应用程序应确保使用内存中缓存时，会粘性会话。</span><span class="sxs-lookup"><span data-stu-id="e821e-114">Apps which run on a server farm of multiple servers should ensure that sessions are sticky when using the in-memory cache.</span></span> <span data-ttu-id="e821e-115">粘性会话确保所有客户端的后续请求转到同一台服务器。</span><span class="sxs-lookup"><span data-stu-id="e821e-115">Sticky sessions ensure that subsequent requests from a client all go to the same server.</span></span> <span data-ttu-id="e821e-116">例如，Azure Web 应用使用[应用程序请求路由](https://www.iis.net/learn/extensions/planning-for-arr)(ARR) 将所有的后续请求路由到同一台服务器。</span><span class="sxs-lookup"><span data-stu-id="e821e-116">For example, Azure Web apps use [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) to route all subsequent requests to the same server.</span></span>

<span data-ttu-id="e821e-117">Web 场中的非粘性会话需要[分布式缓存](distributed.md)以避免缓存一致性问题。</span><span class="sxs-lookup"><span data-stu-id="e821e-117">Non-sticky sessions in a web farm require a [distributed cache](distributed.md) to avoid cache consistency problems.</span></span> <span data-ttu-id="e821e-118">对于某些应用，分布式的缓存可以支持更高版本横向扩展比内存中缓存。</span><span class="sxs-lookup"><span data-stu-id="e821e-118">For some apps, a distributed cache can support higher scale out than an in-memory cache.</span></span> <span data-ttu-id="e821e-119">使用分布式的缓存将卸载到外部进程缓存内存。</span><span class="sxs-lookup"><span data-stu-id="e821e-119">Using a distributed cache offloads the cache memory to an external process.</span></span> 

<span data-ttu-id="e821e-120">`IMemoryCache`缓存将逐出缓存条目内存压力下的，除非[缓存优先级](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheitempriority)设置为`CacheItemPriority.NeverRemove`。</span><span class="sxs-lookup"><span data-stu-id="e821e-120">The `IMemoryCache` cache will evict cache entries under memory pressure unless the [cache priority](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheitempriority) is set to `CacheItemPriority.NeverRemove`.</span></span> <span data-ttu-id="e821e-121">你可以设置`CacheItemPriority`调整优先级缓存逐出内存压力下的项。</span><span class="sxs-lookup"><span data-stu-id="e821e-121">You can set the `CacheItemPriority` to adjust the priority the cache evicts items under memory pressure.</span></span>

<span data-ttu-id="e821e-122">内存中缓存中可以存储任何对象;分布式的缓存接口仅限于`byte[]`。</span><span class="sxs-lookup"><span data-stu-id="e821e-122">The in-memory cache can store any object; the distributed cache interface is limited to `byte[]`.</span></span>

## <a name="using-imemorycache"></a><span data-ttu-id="e821e-123">使用 IMemoryCache</span><span class="sxs-lookup"><span data-stu-id="e821e-123">Using IMemoryCache</span></span>

<span data-ttu-id="e821e-124">内存中缓存是*服务*引用从你的应用使用[依赖关系注入](../../fundamentals/dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="e821e-124">In-memory caching is a *service* that is referenced from your app using [Dependency Injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="e821e-125">调用`AddMemoryCache`中`ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e821e-125">Call `AddMemoryCache` in `ConfigureServices`:</span></span>

[!code-csharp[Main](memory/sample/WebCache/Startup.cs?highlight=8)] 

<span data-ttu-id="e821e-126">请求`IMemoryCache`构造函数中的实例：</span><span class="sxs-lookup"><span data-stu-id="e821e-126">Request the `IMemoryCache` instance in the constructor:</span></span>

[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor&highlight=3,5-)] 

<span data-ttu-id="e821e-127">`IMemoryCache`需要 NuGet 包"Microsoft.Extensions.Caching.Memory"。</span><span class="sxs-lookup"><span data-stu-id="e821e-127">`IMemoryCache` requires NuGet package "Microsoft.Extensions.Caching.Memory".</span></span>

<span data-ttu-id="e821e-128">下面的代码使用[TryGetValue](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.imemorycache#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__)检查当前时间是否在缓存中。</span><span class="sxs-lookup"><span data-stu-id="e821e-128">The following code uses [TryGetValue](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.imemorycache#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) to check if the current time is in the cache.</span></span> <span data-ttu-id="e821e-129">如果未缓存项，创建并添加到缓存中，使用新的条目[设置](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_)。</span><span class="sxs-lookup"><span data-stu-id="e821e-129">If the item is not cached, a new entry is created and added to the cache with [Set](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_).</span></span>

[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="e821e-130">显示当前时间和缓存的时间：</span><span class="sxs-lookup"><span data-stu-id="e821e-130">The current time and the cached time is displayed:</span></span>

[!code-html[Main](memory/sample/WebCache/Views/Home/Cache.cshtml)]

<span data-ttu-id="e821e-131">已缓存`DateTime`值将保留在缓存中，尽管在超时期限 （和由于内存压力没有逐出） 的请求。</span><span class="sxs-lookup"><span data-stu-id="e821e-131">The cached `DateTime` value will remain in the cache while there are requests within the timeout period (and no eviction due to memory pressure).</span></span> <span data-ttu-id="e821e-132">下图显示当前时间和较旧时间从缓存中检索：</span><span class="sxs-lookup"><span data-stu-id="e821e-132">The image below shows the current time and an older time retrieved from cache:</span></span>

![具有两个不同的时间显示的索引视图](memory/_static/time.png)

<span data-ttu-id="e821e-134">下面的代码使用[GetOrCreate](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__)和[GetOrCreateAsync](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___)缓存数据。</span><span class="sxs-lookup"><span data-stu-id="e821e-134">The following code uses [GetOrCreate](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) and [GetOrCreateAsync](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) to cache data.</span></span> 

[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

<span data-ttu-id="e821e-135">下面的代码调用[获取](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)提取缓存的时间：</span><span class="sxs-lookup"><span data-stu-id="e821e-135">The following code calls [Get](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) to fetch the cached time:</span></span>

[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<span data-ttu-id="e821e-136">请参阅[IMemoryCache 方法](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.imemorycache)和[CacheExtensions 方法](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions)有关缓存方法的说明。</span><span class="sxs-lookup"><span data-stu-id="e821e-136">See [IMemoryCache methods](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.imemorycache) and [CacheExtensions methods](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions) for a description of the cache methods.</span></span>

## <a name="using-memorycacheentryoptions"></a><span data-ttu-id="e821e-137">使用 MemoryCacheEntryOptions</span><span class="sxs-lookup"><span data-stu-id="e821e-137">Using MemoryCacheEntryOptions</span></span>

<span data-ttu-id="e821e-138">下面的示例：</span><span class="sxs-lookup"><span data-stu-id="e821e-138">The following sample:</span></span>

- <span data-ttu-id="e821e-139">设置绝对到期时间。</span><span class="sxs-lookup"><span data-stu-id="e821e-139">Sets the absolute expiration time.</span></span> <span data-ttu-id="e821e-140">这是可以缓存条目的最长时间，可防止项持续续订滑动过期时变得太陈旧。</span><span class="sxs-lookup"><span data-stu-id="e821e-140">This is the maximum time the entry can be cached and prevents the item from becoming too stale when the sliding expiration is continuously renewed.</span></span>
- <span data-ttu-id="e821e-141">设置滑动过期时间。</span><span class="sxs-lookup"><span data-stu-id="e821e-141">Sets a sliding expiration time.</span></span> <span data-ttu-id="e821e-142">访问此缓存的项的请求将重置滑动到期时钟。</span><span class="sxs-lookup"><span data-stu-id="e821e-142">Requests that access this cached item will reset the sliding expiration clock.</span></span>
- <span data-ttu-id="e821e-143">将缓存优先级设置为`CacheItemPriority.NeverRemove`。</span><span class="sxs-lookup"><span data-stu-id="e821e-143">Sets the cache priority to `CacheItemPriority.NeverRemove`.</span></span> 
- <span data-ttu-id="e821e-144">集[PostEvictionDelegate](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.postevictiondelegate)后从缓存逐出项，将调用的。</span><span class="sxs-lookup"><span data-stu-id="e821e-144">Sets a [PostEvictionDelegate](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.postevictiondelegate) that will be called after the entry is evicted from the cache.</span></span> <span data-ttu-id="e821e-145">回调是在从缓存中移除的项的代码与不同的线程上运行。</span><span class="sxs-lookup"><span data-stu-id="e821e-145">The callback is run on a different thread from the code that removes the item from the cache.</span></span>

[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-20)]

## <a name="cache-dependencies"></a><span data-ttu-id="e821e-146">缓存依赖项</span><span class="sxs-lookup"><span data-stu-id="e821e-146">Cache dependencies</span></span>

<span data-ttu-id="e821e-147">下面的示例演示如何过期的缓存项，如果依赖项将过期。</span><span class="sxs-lookup"><span data-stu-id="e821e-147">The following sample shows how to expire a cache entry if a dependent entry expires.</span></span> <span data-ttu-id="e821e-148">A`CancellationChangeToken`添加到缓存的项。</span><span class="sxs-lookup"><span data-stu-id="e821e-148">A `CancellationChangeToken` is added to the cached item.</span></span> <span data-ttu-id="e821e-149">当`Cancel`上调用`CancellationTokenSource`，这两个缓存条目被逐出。</span><span class="sxs-lookup"><span data-stu-id="e821e-149">When `Cancel` is called on the `CancellationTokenSource`, both cache entries are evicted.</span></span> 

[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

<span data-ttu-id="e821e-150">使用`CancellationTokenSource`允许多个缓存条目被逐出作为一个组。</span><span class="sxs-lookup"><span data-stu-id="e821e-150">Using a `CancellationTokenSource` allows multiple cache entries to be evicted as a group.</span></span> <span data-ttu-id="e821e-151">与`using`在上面的代码的模式，在内部创建的缓存条目`using`块将继承触发器和过期时间设置。</span><span class="sxs-lookup"><span data-stu-id="e821e-151">With the `using` pattern in the code above, cache entries created inside the `using` block will inherit triggers and expiration settings.</span></span>

### <a name="additional-notes"></a><span data-ttu-id="e821e-152">附加说明</span><span class="sxs-lookup"><span data-stu-id="e821e-152">Additional notes</span></span>

- <span data-ttu-id="e821e-153">当使用的回调以重新填充缓存项：</span><span class="sxs-lookup"><span data-stu-id="e821e-153">When using a callback to repopulate a cache item:</span></span>

  - <span data-ttu-id="e821e-154">多个请求可以查找缓存的键值空因为尚未完成的回调。</span><span class="sxs-lookup"><span data-stu-id="e821e-154">Multiple requests can find the cached key value empty because the callback hasn't completed.</span></span> 
  - <span data-ttu-id="e821e-155">这可能导致重新填充缓存的项的多个线程。</span><span class="sxs-lookup"><span data-stu-id="e821e-155">This can result in several threads repopulating the cached item.</span></span>

- <span data-ttu-id="e821e-156">当一个缓存条目用于创建另一个时，子复制父项的过期的令牌和基于时间的过期时间设置。</span><span class="sxs-lookup"><span data-stu-id="e821e-156">When one cache entry is used to create another, the child copies the parent entry's expiration tokens and time-based expiration settings.</span></span> <span data-ttu-id="e821e-157">子级不是通过手动删除过期的或更新的父项。</span><span class="sxs-lookup"><span data-stu-id="e821e-157">The child is not expired by manual removal or updating of the parent entry.</span></span>

### <a name="other-resources"></a><span data-ttu-id="e821e-158">其他资源</span><span class="sxs-lookup"><span data-stu-id="e821e-158">Other Resources</span></span>

* [<span data-ttu-id="e821e-159">使用分布式缓存</span><span class="sxs-lookup"><span data-stu-id="e821e-159">Working with a Distributed Cache</span></span>](distributed.md)
* [<span data-ttu-id="e821e-160">响应缓存中间件</span><span class="sxs-lookup"><span data-stu-id="e821e-160">Response caching middleware</span></span>](middleware.md)
