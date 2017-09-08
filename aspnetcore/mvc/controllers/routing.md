---
title: "路由到控制器操作"
author: rick-anderson
description: 
keywords: "ASP.NET 核心"
ms.author: riande
manager: wpickett
ms.date: 03/14/2017
ms.topic: article
ms.assetid: 26250a4d-bf62-4d45-8549-26801cf956e9
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/routing
ms.openlocfilehash: da67124ffc874c4f83fff077c6429e9f3e571587
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="routing-to-controller-actions"></a><span data-ttu-id="dad32-103">路由到控制器操作</span><span class="sxs-lookup"><span data-stu-id="dad32-103">Routing to Controller Actions</span></span>

<span data-ttu-id="dad32-104">通过[Ryan Nowak](https://github.com/rynowak)和[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="dad32-104">By [Ryan Nowak](https://github.com/rynowak) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="dad32-105">ASP.NET 核心 MVC 使用路由[中间件](../../fundamentals/middleware.md)匹配的传入请求的 Url，并将它们映射到操作。</span><span class="sxs-lookup"><span data-stu-id="dad32-105">ASP.NET Core MVC uses the Routing [middleware](../../fundamentals/middleware.md) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="dad32-106">启动代码或属性中定义路由。</span><span class="sxs-lookup"><span data-stu-id="dad32-106">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="dad32-107">路由描述应如何操作到匹配的 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="dad32-107">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="dad32-108">路由还用于生成在响应中发送 （用于链接） 的 Url。</span><span class="sxs-lookup"><span data-stu-id="dad32-108">Routes are also used to generate URLs (for links) sent out in responses.</span></span> 

<span data-ttu-id="dad32-109">操作或者通常路由或路由的属性。</span><span class="sxs-lookup"><span data-stu-id="dad32-109">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="dad32-110">在控制器或操作上放置一个路由，使其路由的属性。</span><span class="sxs-lookup"><span data-stu-id="dad32-110">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="dad32-111">请参阅[混合路由](#routing-mixed-ref-label)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="dad32-111">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="dad32-112">本文档将介绍 MVC 和路由，以及如何典型的 MVC 应用程序生成之间的交互使用的路由功能。</span><span class="sxs-lookup"><span data-stu-id="dad32-112">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="dad32-113">请参阅[路由](xref:fundamentals/routing)有关高级路由的详细信息。</span><span class="sxs-lookup"><span data-stu-id="dad32-113">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="dad32-114">设置路由的中间件</span><span class="sxs-lookup"><span data-stu-id="dad32-114">Setting up Routing Middleware</span></span>

<span data-ttu-id="dad32-115">在你*配置*方法可能会看到类似于代码：</span><span class="sxs-lookup"><span data-stu-id="dad32-115">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="dad32-116">在调用`UseMvc`，`MapRoute`用于创建一个路由，我们将称为`default`路由。</span><span class="sxs-lookup"><span data-stu-id="dad32-116">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="dad32-117">大多数 MVC 应用程序将使用模板类似于使用的路由`default`路由。</span><span class="sxs-lookup"><span data-stu-id="dad32-117">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="dad32-118">路由模板`"{controller=Home}/{action=Index}/{id?}"`可以匹配类似 URL 路径`/Products/Details/5`并将提取路由值`{ controller = Products, action = Details, id = 5 }`通过将拆分为标记的路径。</span><span class="sxs-lookup"><span data-stu-id="dad32-118">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="dad32-119">MVC 将尝试查找名为的控制器`ProductsController`和运行操作`Details`:</span><span class="sxs-lookup"><span data-stu-id="dad32-119">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="dad32-120">请注意，在此示例中，模型绑定将使用的值`id = 5`设置`id`参数`5`时调用此操作。</span><span class="sxs-lookup"><span data-stu-id="dad32-120">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="dad32-121">请参阅[模型绑定](../models/model-binding.md)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="dad32-121">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="dad32-122">使用`default`路由：</span><span class="sxs-lookup"><span data-stu-id="dad32-122">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="dad32-123">路由模板中：</span><span class="sxs-lookup"><span data-stu-id="dad32-123">The route template:</span></span>

* <span data-ttu-id="dad32-124">`{controller=Home}`定义`Home`作为默认值`controller`</span><span class="sxs-lookup"><span data-stu-id="dad32-124">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="dad32-125">`{action=Index}`定义`Index`作为默认值`action`</span><span class="sxs-lookup"><span data-stu-id="dad32-125">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="dad32-126">`{id?}`定义`id`为可选</span><span class="sxs-lookup"><span data-stu-id="dad32-126">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="dad32-127">默认和可选的路由参数不需要必须包含在匹配项的 URL 路径中。</span><span class="sxs-lookup"><span data-stu-id="dad32-127">Default and optional route parameters do not need to be present in the URL path for a match.</span></span> <span data-ttu-id="dad32-128">请参阅[路由模板参考](../../fundamentals/routing.md#route-template-reference)有关路由模板语法的详细说明。</span><span class="sxs-lookup"><span data-stu-id="dad32-128">See [Route Template Reference](../../fundamentals/routing.md#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="dad32-129">`"{controller=Home}/{action=Index}/{id?}"`可以与匹配的 URL 路径`/`并将生成路由值`{ controller = Home, action = Index }`。</span><span class="sxs-lookup"><span data-stu-id="dad32-129">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="dad32-130">值`controller`和`action`进行的默认值，使用`id`不会生成一个值，因为 URL 路径中没有相应的段。</span><span class="sxs-lookup"><span data-stu-id="dad32-130">The values for `controller` and `action` make use of the default values, `id` does not produce a value since there is no corresponding segment in the URL path.</span></span> <span data-ttu-id="dad32-131">MVC 将使用这些路由值选择`HomeController`和`Index`操作：</span><span class="sxs-lookup"><span data-stu-id="dad32-131">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="dad32-132">使用此控制器定义和路由模板`HomeController.Index`操作就会执行任何以下 URL 路径：</span><span class="sxs-lookup"><span data-stu-id="dad32-132">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="dad32-133">便捷方法`UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="dad32-133">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="dad32-134">可以用于替换：</span><span class="sxs-lookup"><span data-stu-id="dad32-134">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="dad32-135">`UseMvc`和`UseMvcWithDefaultRoute`实例添加`RouterMiddleware`到中间件管道。</span><span class="sxs-lookup"><span data-stu-id="dad32-135">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="dad32-136">MVC 不会直接与中间件，交互，并使用路由处理请求。</span><span class="sxs-lookup"><span data-stu-id="dad32-136">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="dad32-137">MVC 连接到的实例通过路由`MvcRouteHandler`。</span><span class="sxs-lookup"><span data-stu-id="dad32-137">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="dad32-138">内的代码`UseMvc`类似于以下：</span><span class="sxs-lookup"><span data-stu-id="dad32-138">The code inside of `UseMvc` is similar to the following:</span></span>

<!-- literal_block {"ids": [], "names": [], "backrefs": [], "dupnames": [], "xml:space": "preserve", "classes": []} -->

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="dad32-139">`UseMvc`未直接定义任何路由，它将占位符添加到的路由集合`attribute`路由。</span><span class="sxs-lookup"><span data-stu-id="dad32-139">`UseMvc` does not directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="dad32-140">重载`UseMvc(Action<IRouteBuilder>)`可通过添加你自己的路由，同时支持的属性路由。</span><span class="sxs-lookup"><span data-stu-id="dad32-140">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="dad32-141">`UseMvc`和所有其变体将添加的属性路由的占位符-始终无论你如何配置可用的属性路由是`UseMvc`。</span><span class="sxs-lookup"><span data-stu-id="dad32-141">`UseMvc` and all of its variations adds a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="dad32-142">`UseMvcWithDefaultRoute`定义一个默认路由，并支持的属性路由。</span><span class="sxs-lookup"><span data-stu-id="dad32-142">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="dad32-143">[的属性路由](#attribute-routing-ref-label)部分包括更多详细信息的属性路由。</span><span class="sxs-lookup"><span data-stu-id="dad32-143">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name=routing-conventional-ref-label></a>

## <a name="conventional-routing"></a><span data-ttu-id="dad32-144">传统的路由</span><span class="sxs-lookup"><span data-stu-id="dad32-144">Conventional routing</span></span>

<span data-ttu-id="dad32-145">`default`路由：</span><span class="sxs-lookup"><span data-stu-id="dad32-145">The `default` route:</span></span>

<!-- literal_block {"ids": [], "names": [], "backrefs": [], "dupnames": [], "xml:space": "preserve", "classes": []} -->

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="dad32-146">是一种*传统路由*。</span><span class="sxs-lookup"><span data-stu-id="dad32-146">is an example of a *conventional routing*.</span></span> <span data-ttu-id="dad32-147">我们将此样式*传统路由*因为它在建立*约定*的 URL 路径：</span><span class="sxs-lookup"><span data-stu-id="dad32-147">We call this style *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="dad32-148">第一个路径段映射到的控制器名称</span><span class="sxs-lookup"><span data-stu-id="dad32-148">the first path segment maps to the controller name</span></span>

* <span data-ttu-id="dad32-149">第二个映射到的操作名称。</span><span class="sxs-lookup"><span data-stu-id="dad32-149">the second maps to the action name.</span></span>

* <span data-ttu-id="dad32-150">第三个段适用于一个可选`id`用于将映射到模型实体</span><span class="sxs-lookup"><span data-stu-id="dad32-150">the third segment is used for an optional `id` used to map to a model entity</span></span>

<span data-ttu-id="dad32-151">使用此`default`路由、 URL path`/Products/List`映射到`ProductsController.List`操作，和`/Blog/Article/17`映射到`BlogController.Article`。</span><span class="sxs-lookup"><span data-stu-id="dad32-151">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="dad32-152">此映射基于控制器和操作名称**仅**和不基于命名空间、 源文件位置或方法参数。</span><span class="sxs-lookup"><span data-stu-id="dad32-152">This mapping is based on the controller and action names **only** and is not based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="dad32-153">使用传统路由与默认路由，可快速生成应用程序，而无需附带你定义每个操作的新 URL 模式。</span><span class="sxs-lookup"><span data-stu-id="dad32-153">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="dad32-154">包含 CRUD 样式操作的应用程序，在你的控制器之间的 url 具有一致性可以帮助简化你的代码，使你的 UI 更可预测。</span><span class="sxs-lookup"><span data-stu-id="dad32-154">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="dad32-155">`id`由路由模板，这意味着你的操作可以执行而作为 URL 的一部分提供的 ID 没有定义为可选。</span><span class="sxs-lookup"><span data-stu-id="dad32-155">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="dad32-156">通常将发生如果`id`URL 中省略是将设置为`0`模型绑定，以及因此导致的任何实体将位于数据库匹配`id == 0`。</span><span class="sxs-lookup"><span data-stu-id="dad32-156">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="dad32-157">属性路由可以为你提供细化的控制，以使某些操作而不是针对其他所需的 ID。</span><span class="sxs-lookup"><span data-stu-id="dad32-157">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="dad32-158">按照约定，文档将包括可选参数喜欢`id`它们是大可能出现的正确用法。</span><span class="sxs-lookup"><span data-stu-id="dad32-158">By convention the documentation will include optional parameters like `id` when they are likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="dad32-159">多个路由</span><span class="sxs-lookup"><span data-stu-id="dad32-159">Multiple routes</span></span>

<span data-ttu-id="dad32-160">你可以添加多个路由内的`UseMvc`通过添加更多调用到`MapRoute`。</span><span class="sxs-lookup"><span data-stu-id="dad32-160">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="dad32-161">这样做将使你可以定义多个约定，或添加专用于特定的操作，如的常规路由：</span><span class="sxs-lookup"><span data-stu-id="dad32-161">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

<!-- literal_block {"ids": [], "names": [], "backrefs": [], "dupnames": [], "xml:space": "preserve", "classes": []} -->

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
}
```

<span data-ttu-id="dad32-162">`blog`路由此处*专用的常规路由*，这意味着它使用与传统的路由系统，但专用于特定的操作。</span><span class="sxs-lookup"><span data-stu-id="dad32-162">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="dad32-163">由于`controller`和`action`不在路由模板中显示为参数，它们只能有默认值，并且因此此路由将始终映射到操作`BlogController.Article`。</span><span class="sxs-lookup"><span data-stu-id="dad32-163">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="dad32-164">路由集合中的路由进行排序，并将它们添加的顺序处理。</span><span class="sxs-lookup"><span data-stu-id="dad32-164">Routes in the route collection are ordered, and will be processed in the order they are added.</span></span> <span data-ttu-id="dad32-165">因此，在此示例中，`blog`路由将尝试之前`default`路由。</span><span class="sxs-lookup"><span data-stu-id="dad32-165">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="dad32-166">*专用传统路由*通常使用类似的全方位路由参数`{*article}`捕获的 URL 路径的剩余部分。</span><span class="sxs-lookup"><span data-stu-id="dad32-166">*Dedicated conventional routes* often use catch-all route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="dad32-167">这可以使路由太贪婪这意味着它匹配你想要匹配的其他路由的 Url。</span><span class="sxs-lookup"><span data-stu-id="dad32-167">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="dad32-168">将贪婪路由放更高版本中的路由表，为了解决此问题。</span><span class="sxs-lookup"><span data-stu-id="dad32-168">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="dad32-169">回退</span><span class="sxs-lookup"><span data-stu-id="dad32-169">Fallback</span></span>

<span data-ttu-id="dad32-170">作为请求处理过程中，MVC 将验证路由值可以用于在你的应用程序中查找控制器和操作。</span><span class="sxs-lookup"><span data-stu-id="dad32-170">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="dad32-171">如果路由值不匹配操作然后路由不被视为匹配项，并将尝试下一个路由。</span><span class="sxs-lookup"><span data-stu-id="dad32-171">If the route values don't match an action then the route is not considered a match, and the next route will be tried.</span></span> <span data-ttu-id="dad32-172">这称为*回退*，并且它具有旨在简化其中传统路由重叠的情况。</span><span class="sxs-lookup"><span data-stu-id="dad32-172">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="dad32-173">消除歧义操作</span><span class="sxs-lookup"><span data-stu-id="dad32-173">Disambiguating actions</span></span>

<span data-ttu-id="dad32-174">通过路由匹配两个操作，必须区分 MVC 来选择最佳候选或者引发异常。</span><span class="sxs-lookup"><span data-stu-id="dad32-174">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="dad32-175">例如: </span><span class="sxs-lookup"><span data-stu-id="dad32-175">For example:</span></span>

<!-- literal_block {"ids": [], "names": [], "backrefs": [], "dupnames": [], "xml:space": "preserve", "classes": []} -->

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="dad32-176">此控制器定义将匹配的 URL 路径的两个操作`/Products/Edit/17`和将数据路由`{ controller = Products, action = Edit, id = 17 }`。</span><span class="sxs-lookup"><span data-stu-id="dad32-176">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="dad32-177">这是 MVC 控制器的典型模式其中`Edit(int)`显示要编辑某一产品的窗体和`Edit(int, Product)`处理已发布的窗体。</span><span class="sxs-lookup"><span data-stu-id="dad32-177">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="dad32-178">为了实现这一点 MVC 将需要选择`Edit(int, Product)`请求时 HTTP`POST`和`Edit(int)`的 HTTP 谓词时任何其他内容。</span><span class="sxs-lookup"><span data-stu-id="dad32-178">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="dad32-179">`HttpPostAttribute` ( `[HttpPost]` ) 实现的`IActionConstraint`，将只允许的操作的 HTTP 谓词时选择`POST`。</span><span class="sxs-lookup"><span data-stu-id="dad32-179">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="dad32-180">是否存在`IActionConstraint`使`Edit(int, Product)`好匹配比`Edit(int)`，因此`Edit(int, Product)`将首先尝试。</span><span class="sxs-lookup"><span data-stu-id="dad32-180">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="dad32-181">你只需编写自定义`IActionConstraint`中专业的方案，但它实现的重要了解这样的属性的角色`HttpPostAttribute`-类似特性定义用于其他 HTTP 谓词。</span><span class="sxs-lookup"><span data-stu-id="dad32-181">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="dad32-182">传统的路由中很常见的操作时要使用相同的操作名称是它们属于`show form -> submit form`工作流。</span><span class="sxs-lookup"><span data-stu-id="dad32-182">In conventional routing it's common for actions to use the same action name when they are part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="dad32-183">此模式的便利性将变得更加明显复查[了解 IActionConstraint](#understanding-iactionconstraint)部分。</span><span class="sxs-lookup"><span data-stu-id="dad32-183">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="dad32-184">如果多个路由都不匹配，并且 MVC 找不到最佳路由，它会引发`AmbiguousActionException`。</span><span class="sxs-lookup"><span data-stu-id="dad32-184">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name=routing-route-name-ref-label></a>

### <a name="route-names"></a><span data-ttu-id="dad32-185">路由名称</span><span class="sxs-lookup"><span data-stu-id="dad32-185">Route names</span></span>

<span data-ttu-id="dad32-186">字符串`"blog"`和`"default"`在下面的示例都是路由名称：</span><span class="sxs-lookup"><span data-stu-id="dad32-186">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>


```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="dad32-187">路由名称给予路由，以便命名的路由可以用于 URL 生成，此逻辑名称。</span><span class="sxs-lookup"><span data-stu-id="dad32-187">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="dad32-188">这极大地简化了 URL 创建，当路由的顺序可能使复杂的 URL 生成。</span><span class="sxs-lookup"><span data-stu-id="dad32-188">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="dad32-189">路由名称必须是唯一的应用程序范围。</span><span class="sxs-lookup"><span data-stu-id="dad32-189">Routes names must be unique application-wide.</span></span>

<span data-ttu-id="dad32-190">路由名称将不会影响对 URL 匹配或处理的请求;它们仅用于 URL 生成。</span><span class="sxs-lookup"><span data-stu-id="dad32-190">Route names have no impact on URL matching or handling of requests; they are used only for URL generation.</span></span> <span data-ttu-id="dad32-191">[路由](xref:fundamentals/routing)的更多详细信息包括特定于 MVC 的帮助器中的 URL 生成的 URL 生成。</span><span class="sxs-lookup"><span data-stu-id="dad32-191">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name=attribute-routing-ref-label></a>

## <a name="attribute-routing"></a><span data-ttu-id="dad32-192">属性路由</span><span class="sxs-lookup"><span data-stu-id="dad32-192">Attribute routing</span></span>

<span data-ttu-id="dad32-193">属性路由使用一的组特性操作直接映射到路由模板。</span><span class="sxs-lookup"><span data-stu-id="dad32-193">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="dad32-194">在下面的示例中，`app.UseMvc();`中使用`Configure`传递方法和路由。</span><span class="sxs-lookup"><span data-stu-id="dad32-194">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="dad32-195">`HomeController`将匹配的一组 Url 类似于默认路由`{controller=Home}/{action=Index}/{id?}`将匹配：</span><span class="sxs-lookup"><span data-stu-id="dad32-195">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="dad32-196">`HomeController.Index()`操作将执行的 URL 路径的任何`/`， `/Home`，或`/Home/Index`。</span><span class="sxs-lookup"><span data-stu-id="dad32-196">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="dad32-197">此示例重点介绍的属性路由和传统路由的主要编程区别。</span><span class="sxs-lookup"><span data-stu-id="dad32-197">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="dad32-198">属性路由需要更多输入指定的路由;传统的默认路由更简洁地采用处理路由。</span><span class="sxs-lookup"><span data-stu-id="dad32-198">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="dad32-199">但是，属性路由允许 （和需要） 的其路由模板应用于每个操作的精确控制。</span><span class="sxs-lookup"><span data-stu-id="dad32-199">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="dad32-200">使用路由的控制器名称和操作名称的特性播放**没有**角色选择操作。</span><span class="sxs-lookup"><span data-stu-id="dad32-200">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="dad32-201">此示例将匹配与上述示例相同的 Url。</span><span class="sxs-lookup"><span data-stu-id="dad32-201">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="dad32-202">上面的路由模板未定义路由的参数`action`， `area`，和`controller`。</span><span class="sxs-lookup"><span data-stu-id="dad32-202">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="dad32-203">事实上，属性路由中不允许使用这些路由参数。</span><span class="sxs-lookup"><span data-stu-id="dad32-203">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="dad32-204">路由模板已与某个操作关联，因为它没有任何意义分析从 URL 的操作名称。</span><span class="sxs-lookup"><span data-stu-id="dad32-204">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="dad32-205">属性路由与 Http [动词] 属性</span><span class="sxs-lookup"><span data-stu-id="dad32-205">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="dad32-206">此外可以进行的属性路由的使用`Http[Verb]`特性例如`HttpPostAttribute`。</span><span class="sxs-lookup"><span data-stu-id="dad32-206">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="dad32-207">所有这些属性可以接受路由模板。</span><span class="sxs-lookup"><span data-stu-id="dad32-207">All of these attributes can accept a route template.</span></span> <span data-ttu-id="dad32-208">此示例演示与相同的路由模板匹配的两个操作：</span><span class="sxs-lookup"><span data-stu-id="dad32-208">This example shows two actions that match the same route template:</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="dad32-209">URL 路径类似`/products``ProductsApi.ListProducts`将执行操作，当 HTTP 谓词为`GET`和`ProductsApi.CreateProduct`的 HTTP 谓词时将执行`POST`。</span><span class="sxs-lookup"><span data-stu-id="dad32-209">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="dad32-210">第一次的属性路由匹配针对路由特性定义的路由模板的集合的 URL。</span><span class="sxs-lookup"><span data-stu-id="dad32-210">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="dad32-211">一旦与匹配的路由模板，`IActionConstraint`应用约束的目的是确定可以执行哪些操作。</span><span class="sxs-lookup"><span data-stu-id="dad32-211">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="dad32-212">在生成 REST API 时，很少要使用`[Route(...)]`上操作方法。</span><span class="sxs-lookup"><span data-stu-id="dad32-212">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method.</span></span> <span data-ttu-id="dad32-213">最好使用多个特定`Http*Verb*Attributes`要精确有关你的 API 的支持。</span><span class="sxs-lookup"><span data-stu-id="dad32-213">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="dad32-214">REST api 的客户端需要知道什么路径和 HTTP 谓词将映射到特定逻辑操作。</span><span class="sxs-lookup"><span data-stu-id="dad32-214">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="dad32-215">由于属性路由适用于特定的操作，是很容易地路由模板定义的一部分所需参数。</span><span class="sxs-lookup"><span data-stu-id="dad32-215">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="dad32-216">在此示例中，`id`是必要的 URL 路径的一部分。</span><span class="sxs-lookup"><span data-stu-id="dad32-216">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="dad32-217">`ProductsApi.GetProduct(int)`操作将针对类似的 URL 路径执行`/products/3`而不是类似的 URL 路径`/products`。</span><span class="sxs-lookup"><span data-stu-id="dad32-217">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="dad32-218">请参阅[路由](../../fundamentals/routing.md)有关路由模板和相关的选项的完整说明。</span><span class="sxs-lookup"><span data-stu-id="dad32-218">See [Routing](../../fundamentals/routing.md) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="dad32-219">路由名称</span><span class="sxs-lookup"><span data-stu-id="dad32-219">Route Name</span></span>

<span data-ttu-id="dad32-220">下面的代码定义*路由名称*的`Products_List`:</span><span class="sxs-lookup"><span data-stu-id="dad32-220">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="dad32-221">可以使用路由名称，以便生成基于特定的路由的 URL。</span><span class="sxs-lookup"><span data-stu-id="dad32-221">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="dad32-222">路由名称匹配的路由的行为在 URL 上没有任何影响，并且仅用于 URL 生成。</span><span class="sxs-lookup"><span data-stu-id="dad32-222">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="dad32-223">路由名称必须是唯一的应用程序范围。</span><span class="sxs-lookup"><span data-stu-id="dad32-223">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="dad32-224">与此相反传统*默认路由*，后者定义了`id`参数为可选 (`{id?}`)。</span><span class="sxs-lookup"><span data-stu-id="dad32-224">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="dad32-225">数据透视表能够精确地指定 Api 有优点，例如允许`/products`和`/products/5`要被调度到不同的操作。</span><span class="sxs-lookup"><span data-stu-id="dad32-225">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name=routing-combining-ref-label></a>

### <a name="combining-routes"></a><span data-ttu-id="dad32-226">组合路由</span><span class="sxs-lookup"><span data-stu-id="dad32-226">Combining routes</span></span>

<span data-ttu-id="dad32-227">若要使的属性路由重复更少，在控制器上的路由属性与上的各项操作的路由属性相结合。</span><span class="sxs-lookup"><span data-stu-id="dad32-227">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="dad32-228">在控制器上定义的任何路由模板为前缀的操作的路由模板。</span><span class="sxs-lookup"><span data-stu-id="dad32-228">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="dad32-229">在控制器上放置一个路由特性使**所有**控制器中的操作使用的属性路由。</span><span class="sxs-lookup"><span data-stu-id="dad32-229">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="dad32-230">在此示例中的 URL 路径`/products`可以匹配`ProductsApi.ListProducts`，和的 URL 路径`/products/5`可以匹配`ProductsApi.GetProduct(int)`。</span><span class="sxs-lookup"><span data-stu-id="dad32-230">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="dad32-231">这两种操作只匹配 HTTP`GET`因为使用了修饰`HttpGetAttribute`。</span><span class="sxs-lookup"><span data-stu-id="dad32-231">Both of these actions only match HTTP `GET` because they are decorated with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="dad32-232">将路由应用于开头的操作的模板`/`执行不获取结合应用到控制器的路由模板。</span><span class="sxs-lookup"><span data-stu-id="dad32-232">Route templates applied to an action that begin with a `/` do not get combined with route templates applied to the controller.</span></span> <span data-ttu-id="dad32-233">此示例匹配一组的 URL 路径类似于*默认路由*。</span><span class="sxs-lookup"><span data-stu-id="dad32-233">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Does not combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name=routing-ordering-ref-label></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="dad32-234">排序的属性路由</span><span class="sxs-lookup"><span data-stu-id="dad32-234">Ordering attribute routes</span></span>

<span data-ttu-id="dad32-235">与传统的路由，这按照定义的顺序执行，不同的属性路由生成树，然后同时匹配所有路由。</span><span class="sxs-lookup"><span data-stu-id="dad32-235">In contrast to conventional routes which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="dad32-236">此行为方式会如同-如果路由条目已被放在非理想进行排序;最特定路由有机会执行之前的更多常规路由。</span><span class="sxs-lookup"><span data-stu-id="dad32-236">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="dad32-237">例如，一个路由，如`blog/search/{topic}`比类似的路由更具体`blog/{*article}`。</span><span class="sxs-lookup"><span data-stu-id="dad32-237">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="dad32-238">逻辑上讲`blog/search/{topic}`路由运行首先，默认情况下，因为这是仅有意义的排序。</span><span class="sxs-lookup"><span data-stu-id="dad32-238">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="dad32-239">使用传统的路由，开发人员负责将路由放置在所需的顺序。</span><span class="sxs-lookup"><span data-stu-id="dad32-239">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="dad32-240">属性路由可以配置订单时，使用`Order`的所有提供的框架路由属性的属性。</span><span class="sxs-lookup"><span data-stu-id="dad32-240">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="dad32-241">根据升序处理路由有点`Order`属性。</span><span class="sxs-lookup"><span data-stu-id="dad32-241">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="dad32-242">默认顺序是`0`。</span><span class="sxs-lookup"><span data-stu-id="dad32-242">The default order is `0`.</span></span> <span data-ttu-id="dad32-243">设置路由使用`Order = -1`之前未设置订单的路由将运行。</span><span class="sxs-lookup"><span data-stu-id="dad32-243">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="dad32-244">设置路由使用`Order = 1`将默认路由排序后运行。</span><span class="sxs-lookup"><span data-stu-id="dad32-244">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="dad32-245">具体取决于避免`Order`。</span><span class="sxs-lookup"><span data-stu-id="dad32-245">Avoid depending on `Order`.</span></span> <span data-ttu-id="dad32-246">如果你的 URL 空间需要显式顺序值正确路由，它会向客户端也可能造成混淆。</span><span class="sxs-lookup"><span data-stu-id="dad32-246">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="dad32-247">一般情况下的属性路由将选择正确的路由与 URL 匹配。</span><span class="sxs-lookup"><span data-stu-id="dad32-247">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="dad32-248">如果 URL 生成使用的默认顺序不能正常工作，使用路由名称，因为替代通常比应用更简单`Order`属性。</span><span class="sxs-lookup"><span data-stu-id="dad32-248">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<a name=routing-token-replacement-templates-ref-label></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="dad32-249">令牌在路由模板中的替换 ([controller] [操作] [区域])</span><span class="sxs-lookup"><span data-stu-id="dad32-249">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="dad32-250">为方便起见，属性路由支持*令牌替换*括在大括号方形的令牌 (`[`， `]`)。</span><span class="sxs-lookup"><span data-stu-id="dad32-250">For convenience, attribute routes support *token replacement* by enclosing a token in square-braces (`[`, `]`).</span></span> <span data-ttu-id="dad32-251">令牌`[action]`， `[area]`，和`[controller]`将替换为操作名称、 区域名称和其中定义路由的操作的控制器名称的值。</span><span class="sxs-lookup"><span data-stu-id="dad32-251">The tokens `[action]`, `[area]`, and `[controller]` will be replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="dad32-252">在此示例中的注释中所述操作可以与匹配的 URL 路径：</span><span class="sxs-lookup"><span data-stu-id="dad32-252">In this example the actions can match URL paths as described in the comments:</span></span>

<span data-ttu-id="dad32-253">[!code-csharp[Main](routing/sample/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]</span><span class="sxs-lookup"><span data-stu-id="dad32-253">[!code-csharp[Main](routing/sample/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]</span></span>

<span data-ttu-id="dad32-254">作为生成的属性路由的最后一个步骤中发生，标记替换。</span><span class="sxs-lookup"><span data-stu-id="dad32-254">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="dad32-255">上面的示例将与下面的代码相同的行为：</span><span class="sxs-lookup"><span data-stu-id="dad32-255">The above example will behave the same as the following code:</span></span>

<span data-ttu-id="dad32-256">[!code-csharp[Main](routing/sample/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]</span><span class="sxs-lookup"><span data-stu-id="dad32-256">[!code-csharp[Main](routing/sample/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]</span></span>

<span data-ttu-id="dad32-257">此外可以与继承组合属性路由。</span><span class="sxs-lookup"><span data-stu-id="dad32-257">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="dad32-258">这是特别有用结合标记替换。</span><span class="sxs-lookup"><span data-stu-id="dad32-258">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPost("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="dad32-259">标记替换也适用于定义的属性路由的路由名称。</span><span class="sxs-lookup"><span data-stu-id="dad32-259">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="dad32-260">`[Route("[controller]/[action]", Name="[controller]_[action]")]`将生成每个操作的唯一路由名称。</span><span class="sxs-lookup"><span data-stu-id="dad32-260">`[Route("[controller]/[action]", Name="[controller]_[action]")]` will generate a unique route name for each action.</span></span>

<span data-ttu-id="dad32-261">若要匹配的文本的标记替换分隔符`[`或`]`，通过重复字符对其进行转义 (`[[`或`]]`)。</span><span class="sxs-lookup"><span data-stu-id="dad32-261">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name=routing-multiple-routes-ref-label></a>

### <a name="multiple-routes"></a><span data-ttu-id="dad32-262">多个路由</span><span class="sxs-lookup"><span data-stu-id="dad32-262">Multiple Routes</span></span>

<span data-ttu-id="dad32-263">属性定义访问相同的操作的多个路由的路由支持。</span><span class="sxs-lookup"><span data-stu-id="dad32-263">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="dad32-264">此的最常见用法是模拟的行为*默认传统路由*下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="dad32-264">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="dad32-265">在控制器上放置多个路由属性意味着每个将合并与每个路由属性上的操作方法。</span><span class="sxs-lookup"><span data-stu-id="dad32-265">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="dad32-266">当多个路由属性 (该实现`IActionConstraint`) 位于采取某一操作，则每个操作约束将与定义它的属性路由模板相结合。</span><span class="sxs-lookup"><span data-stu-id="dad32-266">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="dad32-267">操作使用多个路由可能看起来非常强大，而是更好的做法保持简单和定义完善的应用程序的 URL 空间。</span><span class="sxs-lookup"><span data-stu-id="dad32-267">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="dad32-268">仅在需要时，例如若要支持现有客户端，请在操作中使用多个路由。</span><span class="sxs-lookup"><span data-stu-id="dad32-268">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name=routing-attr-options></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="dad32-269">指定的属性路由可选参数、 默认值和约束</span><span class="sxs-lookup"><span data-stu-id="dad32-269">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="dad32-270">属性路由支持内联语法与传统的路由，以便指定可选参数、 默认值和约束相同。</span><span class="sxs-lookup"><span data-stu-id="dad32-270">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="dad32-271">请参阅[路由模板参考](../../fundamentals/routing.md#route-template-reference)有关路由模板语法的详细说明。</span><span class="sxs-lookup"><span data-stu-id="dad32-271">See [Route Template Reference](../../fundamentals/routing.md#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name=routing-cust-rt-attr-irt-ref-label></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="dad32-272">使用自定义路由属性`IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="dad32-272">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="dad32-273">所有框架中提供的路由属性 ( `[Route(...)]`，`[HttpGet(...)]`等) 实现`IRouteTemplateProvider`接口。</span><span class="sxs-lookup"><span data-stu-id="dad32-273">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="dad32-274">当应用程序启动并使用实现的 MVC 控制器类和操作方法上的属性查找`IRouteTemplateProvider`生成路由的初始集。</span><span class="sxs-lookup"><span data-stu-id="dad32-274">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="dad32-275">你可以实现`IRouteTemplateProvider`来定义你自己的路由特性。</span><span class="sxs-lookup"><span data-stu-id="dad32-275">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="dad32-276">每个`IRouteTemplateProvider`允许你定义自定义路由模板后，一个路由顺序，并将命名：</span><span class="sxs-lookup"><span data-stu-id="dad32-276">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="dad32-277">从上面的示例中的属性自动设置`Template`到`"api/[controller]"`时`[MyApiController]`应用。</span><span class="sxs-lookup"><span data-stu-id="dad32-277">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name=routing-app-model-ref-label></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="dad32-278">使用应用程序模型自定义属性的路由</span><span class="sxs-lookup"><span data-stu-id="dad32-278">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="dad32-279">*应用程序模型*是所有的 MVC 中用于路由并执行你的操作的元数据在启动时创建的对象模型。</span><span class="sxs-lookup"><span data-stu-id="dad32-279">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="dad32-280">*应用程序模型*包括所有路由属性从收集的数据 (通过`IRouteTemplateProvider`)。</span><span class="sxs-lookup"><span data-stu-id="dad32-280">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="dad32-281">你可以编写*约定*在启动时若要自定义路由的行为方式修改应用程序模型。</span><span class="sxs-lookup"><span data-stu-id="dad32-281">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="dad32-282">本部分说明自定义使用应用程序模型进行路由的简单示例。</span><span class="sxs-lookup"><span data-stu-id="dad32-282">This section shows a simple example of customizing routing using application model.</span></span>

<span data-ttu-id="dad32-283">[!code-csharp[Main](routing/sample/main/NamespaceRoutingConvention.cs)]</span><span class="sxs-lookup"><span data-stu-id="dad32-283">[!code-csharp[Main](routing/sample/main/NamespaceRoutingConvention.cs)]</span></span>

<a name=routing-mixed-ref-label></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="dad32-284">混合路由： 属性路由 vs 传统路由</span><span class="sxs-lookup"><span data-stu-id="dad32-284">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="dad32-285">MVC 应用程序可以混合使用传统的路由和的属性路由。</span><span class="sxs-lookup"><span data-stu-id="dad32-285">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="dad32-286">它是典型用于为 HTML 页的浏览器中，提供服务的控制器的常规路由和属性为 REST Api 提供服务的控制器的路由。</span><span class="sxs-lookup"><span data-stu-id="dad32-286">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="dad32-287">操作或者通常路由或路由的属性。</span><span class="sxs-lookup"><span data-stu-id="dad32-287">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="dad32-288">在控制器或操作上放置一个路由，使其路由的属性。</span><span class="sxs-lookup"><span data-stu-id="dad32-288">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="dad32-289">定义属性的路由的操作无法达到通过传统的路由，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="dad32-289">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="dad32-290">**任何**在控制器上的路由特性使路由的控制器特性中的所有操作。</span><span class="sxs-lookup"><span data-stu-id="dad32-290">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="dad32-291">路由的系统的两种类型的区别是 URL 与匹配的路由模板后，将应用的过程。</span><span class="sxs-lookup"><span data-stu-id="dad32-291">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="dad32-292">在传统的路由，路由值匹配中的用于查找表中的所有传统的路由操作选择的操作和控制器。</span><span class="sxs-lookup"><span data-stu-id="dad32-292">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="dad32-293">中的属性路由，每个模板已与某个操作关联，并且需要任何进一步的查找。</span><span class="sxs-lookup"><span data-stu-id="dad32-293">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

<a name=routing-url-gen-ref-label></a>

## <a name="url-generation"></a><span data-ttu-id="dad32-294">URL 生成</span><span class="sxs-lookup"><span data-stu-id="dad32-294">URL Generation</span></span>

<span data-ttu-id="dad32-295">MVC 应用程序可以使用路由的 URL 生成功能生成操作的 URL 链接。</span><span class="sxs-lookup"><span data-stu-id="dad32-295">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="dad32-296">生成 Url 消除了硬编码 Url，从而使代码更稳定、 更易于维护。</span><span class="sxs-lookup"><span data-stu-id="dad32-296">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="dad32-297">本部分重点介绍 MVC 提供的 URL 生成功能，并只包括 URL 生成的工作原理的基础知识。</span><span class="sxs-lookup"><span data-stu-id="dad32-297">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="dad32-298">请参阅[路由](../../fundamentals/routing.md)有关 URL 生成的详细说明。</span><span class="sxs-lookup"><span data-stu-id="dad32-298">See [Routing](../../fundamentals/routing.md) for a detailed description of URL generation.</span></span>

<span data-ttu-id="dad32-299">`IUrlHelper`接口是 MVC 和路由 URL 生成之间的基础结构的基础部分。</span><span class="sxs-lookup"><span data-stu-id="dad32-299">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="dad32-300">你将找到的实例`IUrlHelper`可通过`Url`控制器、 视图和视图组件中的属性。</span><span class="sxs-lookup"><span data-stu-id="dad32-300">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="dad32-301">在此示例中，`IUrlHelper`通过使用接口`Controller.Url`属性来生成指向另一项操作的 URL。</span><span class="sxs-lookup"><span data-stu-id="dad32-301">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

<span data-ttu-id="dad32-302">[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationController.cs?name=snippet_1)]</span><span class="sxs-lookup"><span data-stu-id="dad32-302">[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationController.cs?name=snippet_1)]</span></span>

<span data-ttu-id="dad32-303">如果应用程序使用传统默认路由的值`url`变量将为 URL 路径字符串`/UrlGeneration/Destination`。</span><span class="sxs-lookup"><span data-stu-id="dad32-303">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="dad32-304">通过路由通过组合当前请求 （环境值），从路由值传递到的值创建此 URL 路径`Url.Action`并替换到路由模板为这些值：</span><span class="sxs-lookup"><span data-stu-id="dad32-304">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="dad32-305">路由模板中的每个路由参数具有匹配名称的值和环境的值替换其值。</span><span class="sxs-lookup"><span data-stu-id="dad32-305">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="dad32-306">不具有值的路由参数，如果它有一个，或如果它是可选的跳过，也可以使用默认值 (一样的情况下`id`在此示例中)。</span><span class="sxs-lookup"><span data-stu-id="dad32-306">A route parameter that does not have a value can use a default value if it has one, or be skipped if it is optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="dad32-307">URL 生成将失败，如果任何所需的路由参数没有对应的值。</span><span class="sxs-lookup"><span data-stu-id="dad32-307">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="dad32-308">如果 URL 生成失败，则为路由，会尝试下一个路由，直到尝试所有路由或找到匹配项。</span><span class="sxs-lookup"><span data-stu-id="dad32-308">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="dad32-309">示例`Url.Action`更高版本假定传统路由，但 URL 生成同样可与配合使用的属性路由，尽管概念都是不同。</span><span class="sxs-lookup"><span data-stu-id="dad32-309">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="dad32-310">使用传统路由一样，路由值用于展开模板和的路由值`controller`和`action`通常出现在该模板中-这样做的原因由路由匹配的 Url 遵守*约定*.</span><span class="sxs-lookup"><span data-stu-id="dad32-310">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="dad32-311">中的属性路由，路由值`controller`和`action`不允许出现在模板中-它们而是用于查找要使用的模板。</span><span class="sxs-lookup"><span data-stu-id="dad32-311">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they are instead used to look up which template to use.</span></span>

<span data-ttu-id="dad32-312">此示例使用的属性路由：</span><span class="sxs-lookup"><span data-stu-id="dad32-312">This example uses attribute routing:</span></span>

<span data-ttu-id="dad32-313">[!code-csharp[Main](routing/sample/main/StartupUseMvc.cs?name=snippet_1)]</span><span class="sxs-lookup"><span data-stu-id="dad32-313">[!code-csharp[Main](routing/sample/main/StartupUseMvc.cs?name=snippet_1)]</span></span>

<span data-ttu-id="dad32-314">[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]</span><span class="sxs-lookup"><span data-stu-id="dad32-314">[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]</span></span>

<span data-ttu-id="dad32-315">MVC 生成查找表的所有属性路由操作，并将匹配`controller`和`action`值，以选择要用于生成 URL 的路由模板。</span><span class="sxs-lookup"><span data-stu-id="dad32-315">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="dad32-316">在此示例中更高版本，`custom/url/to/destination`生成。</span><span class="sxs-lookup"><span data-stu-id="dad32-316">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="dad32-317">操作名称生成 Url</span><span class="sxs-lookup"><span data-stu-id="dad32-317">Generating URLs by action name</span></span>

<span data-ttu-id="dad32-318">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="dad32-318">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="dad32-319">`Action`) 以及所有相关的重载所有基于该了解你想要指定什么如果链接到通过指定控制器名称和操作名称。</span><span class="sxs-lookup"><span data-stu-id="dad32-319">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="dad32-320">使用时`Url.Action`，当前路由值`controller`和`action`为你的值指定`controller`和`action`属于这两者的*环境值***和***值*。</span><span class="sxs-lookup"><span data-stu-id="dad32-320">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="dad32-321">该方法`Url.Action`，始终使用的当前值`action`和`controller`并将生成将路由到当前操作的 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="dad32-321">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="dad32-322">路由尝试在环境的值中使用值来填充时生成 URL 未提供的信息。</span><span class="sxs-lookup"><span data-stu-id="dad32-322">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="dad32-323">使用类似的路由`{a}/{b}/{c}/{d}`和环境值`{ a = Alice, b = Bob, c = Carol, d = David }`，路由具有足够的信息来生成无需任何其他值的 URL-由于所有路由形参具有一个值。</span><span class="sxs-lookup"><span data-stu-id="dad32-323">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="dad32-324">如果你添加值`{ d = Donovan }`，值`{ d = David }`将被忽略，并且生成的 URL 路径将为`Alice/Bob/Carol/Donovan`。</span><span class="sxs-lookup"><span data-stu-id="dad32-324">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="dad32-325">URL 路径是层次结构。</span><span class="sxs-lookup"><span data-stu-id="dad32-325">URL paths are hierarchical.</span></span> <span data-ttu-id="dad32-326">在以下示例中，如果你添加值`{ c = Cheryl }`，两个值`{ c = Carol, d = David }`将被忽略。</span><span class="sxs-lookup"><span data-stu-id="dad32-326">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="dad32-327">在这种情况下我们不再提供的值`d`和 URL 的生成将失败。</span><span class="sxs-lookup"><span data-stu-id="dad32-327">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="dad32-328">需要指定的所需的值`c`和`d`。</span><span class="sxs-lookup"><span data-stu-id="dad32-328">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="dad32-329">您可能希望命中此问题的默认路由 (`{controller}/{action}/{id?}`)-但你将很少遇到此行为在实践中作为`Url.Action`将始终显式指定`controller`和`action`值。</span><span class="sxs-lookup"><span data-stu-id="dad32-329">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="dad32-330">较长的重载`Url.Action`还采用额外*路由值*对象以外的路由参数提供值`controller`和`action`。</span><span class="sxs-lookup"><span data-stu-id="dad32-330">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="dad32-331">将最常看到这与使用`id`如`Url.Action("Buy", "Products", new { id = 17 })`。</span><span class="sxs-lookup"><span data-stu-id="dad32-331">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="dad32-332">按照约定*路由值*对象通常是匿名类型的对象，但它也可以是`IDictionary<>`或*无格式传统的.NET 对象*。</span><span class="sxs-lookup"><span data-stu-id="dad32-332">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="dad32-333">路由参数不匹配任何其他路由值都将置于查询字符串。</span><span class="sxs-lookup"><span data-stu-id="dad32-333">Any additional route values that don't match route parameters are put in the query string.</span></span>

<span data-ttu-id="dad32-334">[!code-csharp[Main](routing/sample/main/Controllers/TestController.cs)]</span><span class="sxs-lookup"><span data-stu-id="dad32-334">[!code-csharp[Main](routing/sample/main/Controllers/TestController.cs)]</span></span>

> [!TIP]
> <span data-ttu-id="dad32-335">若要创建的绝对 URL，请使用接受重载`protocol`:`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="dad32-335">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name=routing-gen-urls-route-ref-label></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="dad32-336">生成 Url 的路由</span><span class="sxs-lookup"><span data-stu-id="dad32-336">Generating URLs by route</span></span>

<span data-ttu-id="dad32-337">上面的代码演示了通过在控制器和操作名称的传入生成 URL。</span><span class="sxs-lookup"><span data-stu-id="dad32-337">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="dad32-338">`IUrlHelper`此外提供了`Url.RouteUrl`系列的方法。</span><span class="sxs-lookup"><span data-stu-id="dad32-338">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="dad32-339">这些方法是类似于`Url.Action`，但不是将复制的当前值`action`和`controller`为路由的值。</span><span class="sxs-lookup"><span data-stu-id="dad32-339">These methods are similar to `Url.Action`, but they do not copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="dad32-340">最常见用法是指定要使用特定的路由来生成 URL，通常的路由名称*而无需*指定控制器或操作名称。</span><span class="sxs-lookup"><span data-stu-id="dad32-340">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

<span data-ttu-id="dad32-341">[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]</span><span class="sxs-lookup"><span data-stu-id="dad32-341">[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]</span></span>

<a name=routing-gen-urls-html-ref-label></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="dad32-342">在 HTML 中生成 Url</span><span class="sxs-lookup"><span data-stu-id="dad32-342">Generating URLs in HTML</span></span>

<span data-ttu-id="dad32-343">`IHtmlHelper`提供`HtmlHelper`方法`Html.BeginForm`和`Html.ActionLink`生成`<form>`和`<a>`元素分别。</span><span class="sxs-lookup"><span data-stu-id="dad32-343">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="dad32-344">这些方法使用`Url.Action`方法来生成的 URL 和接受类似的自变量。</span><span class="sxs-lookup"><span data-stu-id="dad32-344">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="dad32-345">`Url.RouteUrl`为伴侣`HtmlHelper`是`Html.BeginRouteForm`和`Html.RouteLink`其提供了类似的功能。</span><span class="sxs-lookup"><span data-stu-id="dad32-345">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="dad32-346">TagHelpers 生成 Url 通过`form`TagHelper 和`<a>`TagHelper。</span><span class="sxs-lookup"><span data-stu-id="dad32-346">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="dad32-347">这两种使用`IUrlHelper`为其实现。</span><span class="sxs-lookup"><span data-stu-id="dad32-347">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="dad32-348">请参阅[使用窗体](../views/working-with-forms.md)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="dad32-348">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="dad32-349">在视图内`IUrlHelper`可通过`Url`未涵盖的上述任何临时 URL 生成的属性。</span><span class="sxs-lookup"><span data-stu-id="dad32-349">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name=routing-gen-urls-action-ref-label></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="dad32-350">在操作结果中生成 URL</span><span class="sxs-lookup"><span data-stu-id="dad32-350">Generating URLS in Action Results</span></span>

<span data-ttu-id="dad32-351">上面示例已演示了使用`IUrlHelper`控制器，虽然控制器中的最常见用法是作为操作结果的一部分生成的 URL 中。</span><span class="sxs-lookup"><span data-stu-id="dad32-351">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="dad32-352">`ControllerBase`和`Controller`基类提供了引用另一项操作的操作结果的简便方法。</span><span class="sxs-lookup"><span data-stu-id="dad32-352">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="dad32-353">一种典型用法是接受用户输入后重定向。</span><span class="sxs-lookup"><span data-stu-id="dad32-353">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public Task<IActionResult> Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
}
```

<span data-ttu-id="dad32-354">操作结果工厂方法至的方法遵循类似的模式`IUrlHelper`。</span><span class="sxs-lookup"><span data-stu-id="dad32-354">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name=routing-dedicated-ref-label></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="dad32-355">专用的常规路由的特殊情况</span><span class="sxs-lookup"><span data-stu-id="dad32-355">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="dad32-356">传统路由可以使用一种特殊的路由定义调用*专用的常规路由*。</span><span class="sxs-lookup"><span data-stu-id="dad32-356">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="dad32-357">在下面的示例中，路由名为`blog`是专用的常规路由。</span><span class="sxs-lookup"><span data-stu-id="dad32-357">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="dad32-358">使用这些 route 定义，`Url.Action("Index", "Home")`将生成的 URL 路径`/`与`default`路由，但原因？</span><span class="sxs-lookup"><span data-stu-id="dad32-358">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="dad32-359">您可能认为路由值`{ controller = Home, action = Index }`将会足以生成的 URL 使用`blog`，并且结果将为`/blog?action=Index&controller=Home`。</span><span class="sxs-lookup"><span data-stu-id="dad32-359">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="dad32-360">专用的常规路由依赖于不具有相应的路由参数，则会使路由的默认值的特殊行为"太贪婪"与 URL 代。</span><span class="sxs-lookup"><span data-stu-id="dad32-360">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="dad32-361">在这种情况下的默认值是`{ controller = Blog, action = Article }`，并且不`controller`也不`action`将显示为路由参数。</span><span class="sxs-lookup"><span data-stu-id="dad32-361">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="dad32-362">当路由执行 URL 生成时，提供的值必须匹配的默认值。</span><span class="sxs-lookup"><span data-stu-id="dad32-362">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="dad32-363">URL 生成使用`blog`将失败，因为值`{ controller = Home, action = Index }`不匹配`{ controller = Blog, action = Article }`。</span><span class="sxs-lookup"><span data-stu-id="dad32-363">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="dad32-364">路由然后将回退，尝试`default`，其成功。</span><span class="sxs-lookup"><span data-stu-id="dad32-364">Routing then falls back to try `default`, which succeeds.</span></span>

<a name=routing-areas-ref-label></a>

## <a name="areas"></a><span data-ttu-id="dad32-365">区域</span><span class="sxs-lookup"><span data-stu-id="dad32-365">Areas</span></span>

<span data-ttu-id="dad32-366">[区域](areas.md)是 MVC 功能用于为单独路由的命名空间 （适用于控制器操作） 和 （适用于视图） 的文件夹结构组织到组的相关的功能。</span><span class="sxs-lookup"><span data-stu-id="dad32-366">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="dad32-367">使用区域允许应用程序具有多个具有相同名称的控制器，只要它们具有不同*区域*。</span><span class="sxs-lookup"><span data-stu-id="dad32-367">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="dad32-368">使用区域创建层次结构用于通过添加另一个路由参数，路由`area`到`controller`和`action`。</span><span class="sxs-lookup"><span data-stu-id="dad32-368">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="dad32-369">本部分将讨论如何路由相互作用区域-请参阅[区域](areas.md)有关如何将区域用于视图的详细信息。</span><span class="sxs-lookup"><span data-stu-id="dad32-369">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="dad32-370">下面的示例将配置为使用默认的常规路由的 MVC 和*区域路由*的名为的区域`Blog`:</span><span class="sxs-lookup"><span data-stu-id="dad32-370">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

<span data-ttu-id="dad32-371">[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="dad32-371">[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet1)]</span></span>

<span data-ttu-id="dad32-372">匹配类似 URL 路径时`/Manage/Users/AddUser`，第一个路由将生成路由值`{ area = Blog, controller = Users, action = AddUser }`。</span><span class="sxs-lookup"><span data-stu-id="dad32-372">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="dad32-373">`area`路由值由默认值为`area`、 事实上通过创建的路由`MapAreaRoute`等效于以下：</span><span class="sxs-lookup"><span data-stu-id="dad32-373">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

<span data-ttu-id="dad32-374">[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet2)]</span><span class="sxs-lookup"><span data-stu-id="dad32-374">[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet2)]</span></span>

<span data-ttu-id="dad32-375">`MapAreaRoute`创建使用的默认值和约束的路由`area`使用提供的区域的名称，在这种情况下`Blog`。</span><span class="sxs-lookup"><span data-stu-id="dad32-375">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="dad32-376">默认值可确保路由始终生成`{ area = Blog, ... }`，约束要求值`{ area = Blog, ... }`URL 生成的。</span><span class="sxs-lookup"><span data-stu-id="dad32-376">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="dad32-377">传统的路由与顺序相关。</span><span class="sxs-lookup"><span data-stu-id="dad32-377">Conventional routing is order-dependent.</span></span> <span data-ttu-id="dad32-378">一般情况下，具有区域路由应放在路由表的前面部分中因为它们是更具体相比没有区域的路由。</span><span class="sxs-lookup"><span data-stu-id="dad32-378">In general, routes with areas should be placed earlier in the route table as they are more specific than routes without an area.</span></span>

<span data-ttu-id="dad32-379">使用上面的示例中，路由值将匹配的以下操作：</span><span class="sxs-lookup"><span data-stu-id="dad32-379">Using the above example, the route values would match the following action:</span></span>

<span data-ttu-id="dad32-380">[!code-csharp[Main](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]</span><span class="sxs-lookup"><span data-stu-id="dad32-380">[!code-csharp[Main](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]</span></span>

<span data-ttu-id="dad32-381">`AreaAttribute`是什么表示控制器作为一个区域的一部分，我们假设此控制器处于`Blog`区域。</span><span class="sxs-lookup"><span data-stu-id="dad32-381">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="dad32-382">控制器而无需`[Area]`属性不是成员的任何区域中，并将**不**匹配时`area`由路由提供路由值。</span><span class="sxs-lookup"><span data-stu-id="dad32-382">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="dad32-383">在下面的示例中，仅列出的第一个控制器可以匹配路由值`{ area = Blog, controller = Users, action = AddUser }`。</span><span class="sxs-lookup"><span data-stu-id="dad32-383">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

<span data-ttu-id="dad32-384">[!code-csharp[Main](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]</span><span class="sxs-lookup"><span data-stu-id="dad32-384">[!code-csharp[Main](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]</span></span>

<span data-ttu-id="dad32-385">[!code-csharp[Main](routing/sample/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]</span><span class="sxs-lookup"><span data-stu-id="dad32-385">[!code-csharp[Main](routing/sample/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]</span></span>

<span data-ttu-id="dad32-386">[!code-csharp[Main](routing/sample/AreasRouting/Controllers/UsersController.cs)]</span><span class="sxs-lookup"><span data-stu-id="dad32-386">[!code-csharp[Main](routing/sample/AreasRouting/Controllers/UsersController.cs)]</span></span>

> [!NOTE]
> <span data-ttu-id="dad32-387">出于完整性考虑此处显示的每个控制器的命名空间-否则控制器必须命名冲突并生成编译器错误。</span><span class="sxs-lookup"><span data-stu-id="dad32-387">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="dad32-388">类命名空间具有对 MVC 的路由没有影响。</span><span class="sxs-lookup"><span data-stu-id="dad32-388">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="dad32-389">前两个控制器是区域的成员，仅匹配时提供其各自领域名`area`路由值。</span><span class="sxs-lookup"><span data-stu-id="dad32-389">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="dad32-390">第三个控制器不是任何区域中和可以唯一匹配项的任何值时的成员`area`提供的路由。</span><span class="sxs-lookup"><span data-stu-id="dad32-390">The third controller is not a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="dad32-391">根据匹配*没有值*，缺少`area`值均相同就像的值`area`了 null 或空字符串。</span><span class="sxs-lookup"><span data-stu-id="dad32-391">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="dad32-392">在执行某一区域内的操作时，路由值，则为`area`将可以用作*环境值*用于路由以用于 URL 生成。</span><span class="sxs-lookup"><span data-stu-id="dad32-392">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="dad32-393">这意味着，默认情况下区域执行操作*粘性*URL 生成下面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="dad32-393">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

<span data-ttu-id="dad32-394">[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet3)]</span><span class="sxs-lookup"><span data-stu-id="dad32-394">[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet3)]</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {"linenostart": 1}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#", "source": "/Users/shirhatti/src/Docs/aspnet/mvc/controllers/routing/sample/AreasRouting/Areas/Duck/Controllers/UsersController.cs"} -->

<span data-ttu-id="dad32-395">[!code-csharp[Main](routing/sample/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]</span><span class="sxs-lookup"><span data-stu-id="dad32-395">[!code-csharp[Main](routing/sample/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]</span></span>

<a name=iactionconstraint-ref-label></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="dad32-396">了解 IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="dad32-396">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="dad32-397">本节是深入 framework 内部结构和 MVC 如何选择要执行的操作。</span><span class="sxs-lookup"><span data-stu-id="dad32-397">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="dad32-398">典型的应用程序不需要自定义`IActionConstraint`</span><span class="sxs-lookup"><span data-stu-id="dad32-398">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="dad32-399">你可能已使用`IActionConstraint`即使你不熟悉的界面。</span><span class="sxs-lookup"><span data-stu-id="dad32-399">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="dad32-400">`[HttpGet]`属性和类似`[Http-VERB]`属性实现`IActionConstraint`以便限制操作方法的执行。</span><span class="sxs-lookup"><span data-stu-id="dad32-400">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="dad32-401">假定默认传统路由的 URL 路径`/Products/Edit`会产生值`{ controller = Products, action = Edit }`，将匹配的**同时**此处显示的操作。</span><span class="sxs-lookup"><span data-stu-id="dad32-401">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="dad32-402">在`IActionConstraint`术语我们会说，这两种操作都视为候选项-因为它们都匹配的路由数据。</span><span class="sxs-lookup"><span data-stu-id="dad32-402">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="dad32-403">当`HttpGetAttribute`执行，它将说*edit （)*是的匹配项*获取*并且不是任何其他 HTTP 谓词的匹配项。</span><span class="sxs-lookup"><span data-stu-id="dad32-403">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and is not a match for any other HTTP verb.</span></span> <span data-ttu-id="dad32-404">`Edit(...)`操作没有任何约束定义，并因此将匹配的任何 HTTP 谓词。</span><span class="sxs-lookup"><span data-stu-id="dad32-404">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="dad32-405">因此假设`POST`-仅`Edit(...)`匹配。</span><span class="sxs-lookup"><span data-stu-id="dad32-405">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="dad32-406">但对于`GET`这两种操作仍可以与匹配-但是，操作与`IActionConstraint`始终被认为是*更好*比操作而无需。</span><span class="sxs-lookup"><span data-stu-id="dad32-406">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="dad32-407">因此因为`Edit()`具有`[HttpGet]`它被视为更具体，并且如果这两种操作可以匹配将选择。</span><span class="sxs-lookup"><span data-stu-id="dad32-407">So because `Edit()` has `[HttpGet]` it is considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="dad32-408">从概念上讲，`IActionConstraint`是一种形式*重载*，但而不重载具有相同名称的方法，它重载之间的相同 URL 匹配的操作。</span><span class="sxs-lookup"><span data-stu-id="dad32-408">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it is overloading between actions that match the same URL.</span></span> <span data-ttu-id="dad32-409">属性路由还使用`IActionConstraint`并且可能会导致在操作的不同控制器从这两个正在考虑的候选项。</span><span class="sxs-lookup"><span data-stu-id="dad32-409">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name=iactionconstraint-impl-ref-label></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="dad32-410">实现 IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="dad32-410">Implementing IActionConstraint</span></span>

<span data-ttu-id="dad32-411">若要实现的最简单方法`IActionConstraint`是创建派生自的类`System.Attribute`并将其置于你的操作和控制器。</span><span class="sxs-lookup"><span data-stu-id="dad32-411">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="dad32-412">MVC 将自动发现任何`IActionConstraint`，应用作为属性。</span><span class="sxs-lookup"><span data-stu-id="dad32-412">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="dad32-413">你可用于应用程序模型应用约束，并且这可能是最灵活的方法，因为它允许您如何应用的好处。</span><span class="sxs-lookup"><span data-stu-id="dad32-413">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they are applied.</span></span>

<span data-ttu-id="dad32-414">在下面的示例约束选择相应的措施*国家/地区代码*从路线数据。</span><span class="sxs-lookup"><span data-stu-id="dad32-414">In the following example a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="dad32-415">[GitHub 上完整示例](https://github.com/aspnet/Entropy/blob/dev/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs)。</span><span class="sxs-lookup"><span data-stu-id="dad32-415">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/dev/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="dad32-416">你负责实现`Accept`方法并选择顺序约束的执行。</span><span class="sxs-lookup"><span data-stu-id="dad32-416">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="dad32-417">在这种情况下，`Accept`方法返回`true`来表示的操作是匹配项时`country`路由值匹配。</span><span class="sxs-lookup"><span data-stu-id="dad32-417">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="dad32-418">这是不同于`RouteValueAttribute`在于前者允许回退到非特性化的操作。</span><span class="sxs-lookup"><span data-stu-id="dad32-418">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="dad32-419">该示例演示是否你定义`en-US`操作再如国家/地区代码`fr-FR`将回退到不具有的更通用控制器`[CountrySpecific(...)]`应用。</span><span class="sxs-lookup"><span data-stu-id="dad32-419">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that does not have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="dad32-420">`Order`属性决定哪些*阶段*约束是的一部分。</span><span class="sxs-lookup"><span data-stu-id="dad32-420">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="dad32-421">在基于组中运行操作约束`Order`。</span><span class="sxs-lookup"><span data-stu-id="dad32-421">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="dad32-422">例如，所有框架提供的 HTTP 方法特性，请使用相同`Order`值，以使它们运行在相同的阶段。</span><span class="sxs-lookup"><span data-stu-id="dad32-422">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="dad32-423">你可以根据需要来实现所需的策略的任意多个阶段。</span><span class="sxs-lookup"><span data-stu-id="dad32-423">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="dad32-424">若要确定的值`Order`思考之前 HTTP 方法中，是否应该应用约束。</span><span class="sxs-lookup"><span data-stu-id="dad32-424">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="dad32-425">首先运行较低的数值。</span><span class="sxs-lookup"><span data-stu-id="dad32-425">Lower numbers run first.</span></span>
