---
title: "检查详细信息和删除方法"
author: rick-anderson
description: "简单 ASP.NET Core MVC 中的详细信息控制器方法和视图。"
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: get-started-article
ms.assetid: 870192b4-8d4f-45c7-8c14-83d02bc0ad79
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: bab93a2faa122d9d6d2e71367519baa09bd76bd1
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="examining-the-details-and-delete-methods"></a><span data-ttu-id="a318b-104">检查详细信息和删除方法</span><span class="sxs-lookup"><span data-stu-id="a318b-104">Examining the Details and Delete methods</span></span>

<span data-ttu-id="a318b-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a318b-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a318b-106">打开电影控制器，并检查 `Details` 方法：</span><span class="sxs-lookup"><span data-stu-id="a318b-106">Open the Movie controller and examine the `Details` method:</span></span>

<span data-ttu-id="a318b-107">[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_details)]</span><span class="sxs-lookup"><span data-stu-id="a318b-107">[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_details)]</span></span>

<span data-ttu-id="a318b-108">创建此操作方法的 MVC 基架引擎添加显示调用方法的 HTTP 请求的注释。</span><span class="sxs-lookup"><span data-stu-id="a318b-108">The MVC scaffolding engine that created this action method adds a comment showing an HTTP request that invokes the method.</span></span> <span data-ttu-id="a318b-109">在此情况下，它是包含三个 URL 段的 GET 请求，这三个段为 `Movies` 控制器、`Details` 方法和 `id` 值。</span><span class="sxs-lookup"><span data-stu-id="a318b-109">In this case it's a GET request with three URL segments, the `Movies` controller, the `Details` method and an `id` value.</span></span> <span data-ttu-id="a318b-110">回顾这些在 Startup.cs 中定义的段。</span><span class="sxs-lookup"><span data-stu-id="a318b-110">Recall these segments are defined in *Startup.cs*.</span></span>

<span data-ttu-id="a318b-111">[!code-csharp[Main](start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]</span><span class="sxs-lookup"><span data-stu-id="a318b-111">[!code-csharp[Main](start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]</span></span>

<span data-ttu-id="a318b-112">EF 可以使用 `SingleOrDefaultAsync` 方法轻松搜索数据。</span><span class="sxs-lookup"><span data-stu-id="a318b-112">EF makes it easy to search for data using the `SingleOrDefaultAsync` method.</span></span> <span data-ttu-id="a318b-113">该方法中内置的一个重要安全功能是，代码会先验证搜索方法已经找到电影，然后再执行操作。</span><span class="sxs-lookup"><span data-stu-id="a318b-113">An important security feature built into the method is that the code verifies that the search method has found a movie before it tries to do anything with it.</span></span> <span data-ttu-id="a318b-114">例如，一个黑客可能通过将链接创建的 URL 从 `http://localhost:xxxx/Movies/Details/1` 更改为类似 `http://localhost:xxxx/Movies/Details/12345` 的值（或者不代表任何实际电影的其他值）将错误引入站点。</span><span class="sxs-lookup"><span data-stu-id="a318b-114">For example, a hacker could introduce errors into the site by changing the URL created by the links from `http://localhost:xxxx/Movies/Details/1` to something like  `http://localhost:xxxx/Movies/Details/12345` (or some other value that doesn't represent an actual movie).</span></span> <span data-ttu-id="a318b-115">如果没有检查是否有空电影，则应用可能引发异常。</span><span class="sxs-lookup"><span data-stu-id="a318b-115">If you did not check for a null movie, the app would throw an exception.</span></span>

<span data-ttu-id="a318b-116">检查 `Delete` 和 `DeleteConfirmed` 方法。</span><span class="sxs-lookup"><span data-stu-id="a318b-116">Examine the `Delete` and `DeleteConfirmed` methods.</span></span>

<span data-ttu-id="a318b-117">[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete)]</span><span class="sxs-lookup"><span data-stu-id="a318b-117">[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete)]</span></span>

<span data-ttu-id="a318b-118">请注意，`HTTP GET Delete` 方法不删除指定的电影，而是返回可在其中提交 (HttpPost) 删除的电影视图。</span><span class="sxs-lookup"><span data-stu-id="a318b-118">Note that the `HTTP GET Delete` method doesn't delete the specified movie, it returns a view of the movie where you can submit (HttpPost) the deletion.</span></span> <span data-ttu-id="a318b-119">执行删除操作以响应 GET 请求（或者说，执行编辑操作、创建操作或更改数据的任何其他操作）会打开安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="a318b-119">Performing a delete operation in response to a GET request (or for that matter, performing an edit operation, create operation, or any other operation that changes data) opens up a security hole.</span></span>

<span data-ttu-id="a318b-120">删除数据的 `[HttpPost]` 方法命名为 `DeleteConfirmed`，以便为 HTTP POST 方法提供一个唯一的签名或名称。</span><span class="sxs-lookup"><span data-stu-id="a318b-120">The `[HttpPost]` method that deletes the data is named `DeleteConfirmed` to give the HTTP POST method a unique signature or name.</span></span> <span data-ttu-id="a318b-121">下面显示了两个方法签名：</span><span class="sxs-lookup"><span data-stu-id="a318b-121">The two method signatures are shown below:</span></span>

<span data-ttu-id="a318b-122">[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]</span><span class="sxs-lookup"><span data-stu-id="a318b-122">[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]</span></span>

<span data-ttu-id="a318b-123">[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]</span><span class="sxs-lookup"><span data-stu-id="a318b-123">[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]</span></span>


<span data-ttu-id="a318b-124">公共语言运行时 (CLR) 需要重载方法拥有唯一的参数签名（相同的方法名称但不同的参数列表）。</span><span class="sxs-lookup"><span data-stu-id="a318b-124">The common language runtime (CLR) requires overloaded methods to have a unique parameter signature (same method name but different list of parameters).</span></span> <span data-ttu-id="a318b-125">但是，这里需要两个 `Delete` 方法 -- 一个用于 GET，另一个用于 POST -- 这两个方法拥有相同的参数签名。</span><span class="sxs-lookup"><span data-stu-id="a318b-125">However, here you need two `Delete` methods -- one for GET and one for POST -- that both have the same parameter signature.</span></span> <span data-ttu-id="a318b-126">（它们都需要接受单个整数作为参数。）</span><span class="sxs-lookup"><span data-stu-id="a318b-126">(They both need to accept a single integer as a parameter.)</span></span>

<span data-ttu-id="a318b-127">可通过两种方法解决此问题，一种是为方法提供不同的名称。</span><span class="sxs-lookup"><span data-stu-id="a318b-127">There are two approaches to this problem, one is to give the methods different names.</span></span> <span data-ttu-id="a318b-128">这正是前面的示例中的基架机制进行的操作。</span><span class="sxs-lookup"><span data-stu-id="a318b-128">That's what the scaffolding mechanism did in the preceding example.</span></span> <span data-ttu-id="a318b-129">但是，这会造成一个小问题：ASP.NET 按名称将 URL 段映射到操作方法，如果重命名方法，则路由通常无法找到该方法。</span><span class="sxs-lookup"><span data-stu-id="a318b-129">However, this introduces a small problem: ASP.NET maps segments of a URL to action methods by name, and if you rename a method, routing normally wouldn't be able to find that method.</span></span> <span data-ttu-id="a318b-130">该示例中也提供了解决方案，即向 `DeleteConfirmed` 方法添加 `ActionName("Delete")` 属性。</span><span class="sxs-lookup"><span data-stu-id="a318b-130">The solution is what you see in the example, which is to add the `ActionName("Delete")` attribute to the `DeleteConfirmed` method.</span></span> <span data-ttu-id="a318b-131">该属性对路由系统执行映射，以便包括 POST 请求的 /Delete/ 的 URL可找到 `DeleteConfirmed` 方法。</span><span class="sxs-lookup"><span data-stu-id="a318b-131">That attribute performs mapping for the routing system so that a URL that includes /Delete/ for a POST request will find the `DeleteConfirmed` method.</span></span>

<span data-ttu-id="a318b-132">对于名称和签名相同的方法，另一个常用解决方法是手动更改 POST 方法的签名以包括额外（未使用）的参数。</span><span class="sxs-lookup"><span data-stu-id="a318b-132">Another common work around for methods that have identical names and signatures is to artificially change the signature of the POST method to include an extra (unused) parameter.</span></span> <span data-ttu-id="a318b-133">这正是前面的文章中添加 `notUsed` 参数时进行的操作。</span><span class="sxs-lookup"><span data-stu-id="a318b-133">That's what we did in a previous post when we added the `notUsed` parameter.</span></span> <span data-ttu-id="a318b-134">这里为了 `[HttpPost] Delete` 方法可以执行同样的操作：</span><span class="sxs-lookup"><span data-stu-id="a318b-134">You could do the same thing here for the `[HttpPost] Delete` method:</span></span>

```csharp
// POST: Movies/Delete/6
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

<span data-ttu-id="a318b-135">感谢读完这篇 ASP.NET Core MVC 简介。</span><span class="sxs-lookup"><span data-stu-id="a318b-135">Thanks for completing this introduction to ASP.NET Core MVC.</span></span> <span data-ttu-id="a318b-136">我们期待你的意见。</span><span class="sxs-lookup"><span data-stu-id="a318b-136">We appreciate any comments you leave.</span></span> <span data-ttu-id="a318b-137">完成本教程后，大力推荐了解 [MVC 和 EF Core 入门](xref:data/ef-mvc/intro)。</span><span class="sxs-lookup"><span data-stu-id="a318b-137">[Getting started with MVC and EF Core](xref:data/ef-mvc/intro) is an excellent follow up to this tutorial.</span></span>

>[!div class="step-by-step"]
[<span data-ttu-id="a318b-138">上一篇</span><span class="sxs-lookup"><span data-stu-id="a318b-138">Previous</span></span>](validation.md)
