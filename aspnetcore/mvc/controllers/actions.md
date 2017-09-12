---
title: "具有 ASP.NET 核心 mvc 控制器处理请求"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 07/03/2017
ms.topic: article
ms.assetid: 9da9eb52-8583-4069-af91-155ba3529d7f
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/actions
ms.openlocfilehash: 5dc6c7dc70027bb79875f389d535119a2543b873
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="handling-requests-with-controllers-in-aspnet-core-mvc"></a><span data-ttu-id="be5f6-103">具有 ASP.NET 核心 mvc 控制器处理请求</span><span class="sxs-lookup"><span data-stu-id="be5f6-103">Handling requests with controllers in ASP.NET Core MVC</span></span>

<span data-ttu-id="be5f6-104">通过[Steve Smith](https://ardalis.com/)和[Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="be5f6-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="be5f6-105">控制器、 操作和操作结果是开发人员如何构建使用 ASP.NET 核心 MVC 应用程序的基本组成部分。</span><span class="sxs-lookup"><span data-stu-id="be5f6-105">Controllers, actions, and action results are a fundamental part of how developers build apps using ASP.NET Core MVC.</span></span>

## <a name="what-is-a-controller"></a><span data-ttu-id="be5f6-106">什么是控制器？</span><span class="sxs-lookup"><span data-stu-id="be5f6-106">What is a Controller?</span></span>

<span data-ttu-id="be5f6-107">控制器用于定义和一组操作。</span><span class="sxs-lookup"><span data-stu-id="be5f6-107">A controller is used to define and group a set of actions.</span></span> <span data-ttu-id="be5f6-108">操作 (或*操作方法*) 是一种方法在处理请求的控制器上。</span><span class="sxs-lookup"><span data-stu-id="be5f6-108">An action (or *action method*) is a method on a controller which handles requests.</span></span> <span data-ttu-id="be5f6-109">控制器按逻辑组类似的操作。</span><span class="sxs-lookup"><span data-stu-id="be5f6-109">Controllers logically group similar actions together.</span></span> <span data-ttu-id="be5f6-110">这种操作的聚合允许组通用的规则，如路由、 缓存和授权，共同应用。</span><span class="sxs-lookup"><span data-stu-id="be5f6-110">This aggregation of actions allows common sets of rules, such as routing, caching, and authorization, to be applied collectively.</span></span> <span data-ttu-id="be5f6-111">请求映射到操作通过[路由](xref:mvc/controllers/routing)。</span><span class="sxs-lookup"><span data-stu-id="be5f6-111">Requests are mapped to actions through [routing](xref:mvc/controllers/routing).</span></span>

<span data-ttu-id="be5f6-112">按照约定，控制器类：</span><span class="sxs-lookup"><span data-stu-id="be5f6-112">By convention, controller classes:</span></span>
* <span data-ttu-id="be5f6-113">驻留在项目的根级别*控制器*文件夹</span><span class="sxs-lookup"><span data-stu-id="be5f6-113">Reside in the project's root-level *Controllers* folder</span></span>
* <span data-ttu-id="be5f6-114">继承自`Microsoft.AspNetCore.Mvc.Controller`</span><span class="sxs-lookup"><span data-stu-id="be5f6-114">Inherit from `Microsoft.AspNetCore.Mvc.Controller`</span></span>

<span data-ttu-id="be5f6-115">控制器是至少一个以下条件为 true 可实例化类：</span><span class="sxs-lookup"><span data-stu-id="be5f6-115">A controller is an instantiable class in which at least one of the following conditions is true:</span></span>
* <span data-ttu-id="be5f6-116">类名称后缀"controller"</span><span class="sxs-lookup"><span data-stu-id="be5f6-116">The class name is suffixed with "Controller"</span></span>
* <span data-ttu-id="be5f6-117">此类从其名称后缀"controller"的类继承</span><span class="sxs-lookup"><span data-stu-id="be5f6-117">The class inherits from a class whose name is suffixed with "Controller"</span></span>
* <span data-ttu-id="be5f6-118">类用修饰`[Controller]`属性</span><span class="sxs-lookup"><span data-stu-id="be5f6-118">The class is decorated with the `[Controller]` attribute</span></span>

<span data-ttu-id="be5f6-119">控制器类必须具有关联`[NonController]`属性。</span><span class="sxs-lookup"><span data-stu-id="be5f6-119">A controller class must not have an associated `[NonController]` attribute.</span></span>

<span data-ttu-id="be5f6-120">控制器应遵循[显式依赖关系原则](http://deviq.com/explicit-dependencies-principle/)。</span><span class="sxs-lookup"><span data-stu-id="be5f6-120">Controllers should follow the [Explicit Dependencies Principle](http://deviq.com/explicit-dependencies-principle/).</span></span> <span data-ttu-id="be5f6-121">有几个方法来实现这一原则。</span><span class="sxs-lookup"><span data-stu-id="be5f6-121">There are a couple approaches to implementing this principle.</span></span> <span data-ttu-id="be5f6-122">如果多个控制器操作需要相同的服务，请考虑使用[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)请求这些依赖关系。</span><span class="sxs-lookup"><span data-stu-id="be5f6-122">If multiple controller actions require the same service, consider using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to request those dependencies.</span></span> <span data-ttu-id="be5f6-123">如果仅的单个操作方法必须使用该服务，请考虑使用[操作注入](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)请求依赖项。</span><span class="sxs-lookup"><span data-stu-id="be5f6-123">If the service is needed by only a single action method, consider using [Action Injection](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) to request the dependency.</span></span>

<span data-ttu-id="be5f6-124">在**M**odel-**V**查看-**C**ontroller 模式中，控制器负责初始处理的请求，并实例化的模型。</span><span class="sxs-lookup"><span data-stu-id="be5f6-124">Within the **M**odel-**V**iew-**C**ontroller pattern, a controller is responsible for the initial processing of the request and instantiation of the model.</span></span> <span data-ttu-id="be5f6-125">通常情况下，应在模型内执行业务决策。</span><span class="sxs-lookup"><span data-stu-id="be5f6-125">Generally, business decisions should be performed within the model.</span></span>

<span data-ttu-id="be5f6-126">控制器采用模型的处理 （如果有） 的结果，并返回适当的视图和其关联的视图数据或 API 调用的结果。</span><span class="sxs-lookup"><span data-stu-id="be5f6-126">The controller takes the result of the model's processing (if any) and returns either the proper view and its associated view data or the result of the API call.</span></span> <span data-ttu-id="be5f6-127">了解更多信息，请访问[ASP.NET 核心 MVC 的概述](xref:mvc/overview)和[ASP.NET 核心 MVC 和 Visual Studio 入门](xref:tutorials/first-mvc-app/start-mvc)。</span><span class="sxs-lookup"><span data-stu-id="be5f6-127">Learn more at [Overview of ASP.NET Core MVC](xref:mvc/overview) and [Getting started with ASP.NET Core MVC and Visual Studio](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="be5f6-128">在控制器*UI 级别*抽象。</span><span class="sxs-lookup"><span data-stu-id="be5f6-128">The controller is a *UI-level* abstraction.</span></span> <span data-ttu-id="be5f6-129">其职责是以确保请求数据有效并选择应返回哪些视图 （或 API 的结果）。</span><span class="sxs-lookup"><span data-stu-id="be5f6-129">Its responsibilities are to ensure request data is valid and to choose which view (or result for an API) should be returned.</span></span> <span data-ttu-id="be5f6-130">在分解应用中，它不直接包含数据访问或业务逻辑。</span><span class="sxs-lookup"><span data-stu-id="be5f6-130">In well-factored apps, it does not directly include data access or business logic.</span></span> <span data-ttu-id="be5f6-131">相反，控制器委托给处理这些职责的服务。</span><span class="sxs-lookup"><span data-stu-id="be5f6-131">Instead, the controller delegates to services handling these responsibilities.</span></span>

## <a name="defining-actions"></a><span data-ttu-id="be5f6-132">定义操作</span><span class="sxs-lookup"><span data-stu-id="be5f6-132">Defining Actions</span></span>

<span data-ttu-id="be5f6-133">在控制器上的公共方法，除使用修饰`[NonAction]`属性，操作。</span><span class="sxs-lookup"><span data-stu-id="be5f6-133">Public methods on a controller, except those decorated with the `[NonAction]` attribute, are actions.</span></span> <span data-ttu-id="be5f6-134">操作的参数绑定到请求的数据和使用验证[模型绑定](xref:mvc/models/model-binding)。</span><span class="sxs-lookup"><span data-stu-id="be5f6-134">Parameters on actions are bound to request data and are validated using [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="be5f6-135">为模型绑定的所有操作都将执行模型验证。</span><span class="sxs-lookup"><span data-stu-id="be5f6-135">Model validation occurs for everything that's model-bound.</span></span> <span data-ttu-id="be5f6-136">`ModelState.IsValid`属性值指示模型绑定和验证是否成功。</span><span class="sxs-lookup"><span data-stu-id="be5f6-136">The `ModelState.IsValid` property value indicates whether model binding and validation succeeded.</span></span>

<span data-ttu-id="be5f6-137">操作方法应包含将请求映射到业务问题的逻辑。</span><span class="sxs-lookup"><span data-stu-id="be5f6-137">Action methods should contain logic for mapping a request to a business concern.</span></span> <span data-ttu-id="be5f6-138">业务关注点通常应表示为服务，它通过访问控制器[依赖关系注入](xref:mvc/controllers/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="be5f6-138">Business concerns should typically be represented as services that the controller accesses through [dependency injection](xref:mvc/controllers/dependency-injection).</span></span> <span data-ttu-id="be5f6-139">操作则映射到应用程序状态的业务操作的结果。</span><span class="sxs-lookup"><span data-stu-id="be5f6-139">Actions then map the result of the business action to an application state.</span></span>

<span data-ttu-id="be5f6-140">操作可以返回任何内容，但经常返回的实例`IActionResult`(或`Task<IActionResult>`为异步方法)，生成响应。</span><span class="sxs-lookup"><span data-stu-id="be5f6-140">Actions can return anything, but frequently return an instance of `IActionResult` (or `Task<IActionResult>` for async methods) that produces a response.</span></span> <span data-ttu-id="be5f6-141">操作方法负责选择*哪种类型的响应*。</span><span class="sxs-lookup"><span data-stu-id="be5f6-141">The action method is responsible for choosing *what kind of response*.</span></span> <span data-ttu-id="be5f6-142">操作结果*未响应*。</span><span class="sxs-lookup"><span data-stu-id="be5f6-142">The action result *does the responding*.</span></span>

### <a name="controller-helper-methods"></a><span data-ttu-id="be5f6-143">控制器帮助器方法</span><span class="sxs-lookup"><span data-stu-id="be5f6-143">Controller Helper Methods</span></span>

<span data-ttu-id="be5f6-144">控制器通常继承自[控制器](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller)，尽管这不是必需。</span><span class="sxs-lookup"><span data-stu-id="be5f6-144">Controllers usually inherit from [Controller](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller), although this is not required.</span></span> <span data-ttu-id="be5f6-145">派生自`Controller`提供对三个类别的帮助器方法的访问：</span><span class="sxs-lookup"><span data-stu-id="be5f6-145">Deriving from `Controller` provides access to three categories of helper methods:</span></span>

#### <a name="1-methods-resulting-in-an-empty-response-body"></a><span data-ttu-id="be5f6-146">1.导致一个空的响应正文的方法</span><span class="sxs-lookup"><span data-stu-id="be5f6-146">1. Methods resulting in an empty response body</span></span>

<span data-ttu-id="be5f6-147">不`Content-Type`HTTP 响应标头是包括在内，因为响应正文中没有描述的内容。</span><span class="sxs-lookup"><span data-stu-id="be5f6-147">No `Content-Type` HTTP response header is included, since the response body lacks content to describe.</span></span>

<span data-ttu-id="be5f6-148">有两种结果类型，此类别中： 重定向，HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="be5f6-148">There are two result types within this category: Redirect and HTTP Status Code.</span></span>

* <span data-ttu-id="be5f6-149">**HTTP 状态代码**</span><span class="sxs-lookup"><span data-stu-id="be5f6-149">**HTTP Status Code**</span></span>

    <span data-ttu-id="be5f6-150">此类型返回 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="be5f6-150">This type returns an HTTP status code.</span></span> <span data-ttu-id="be5f6-151">此类型的几个帮助器方法是`BadRequest`， `NotFound`，和`Ok`。</span><span class="sxs-lookup"><span data-stu-id="be5f6-151">A couple helper methods of this type are `BadRequest`, `NotFound`, and `Ok`.</span></span> <span data-ttu-id="be5f6-152">例如，`return BadRequest();`产生 400 状态代码执行时。</span><span class="sxs-lookup"><span data-stu-id="be5f6-152">For example, `return BadRequest();` produces a 400 status code when executed.</span></span> <span data-ttu-id="be5f6-153">当等方法`BadRequest`， `NotFound`，和`Ok`是重载，它们不再符合条件作为 HTTP 状态代码的响应方，因为内容协商正在进行。</span><span class="sxs-lookup"><span data-stu-id="be5f6-153">When methods such as `BadRequest`, `NotFound`, and `Ok` are overloaded, they no longer qualify as HTTP Status Code responders, since content negotiation is taking place.</span></span>

* <span data-ttu-id="be5f6-154">**重定向**</span><span class="sxs-lookup"><span data-stu-id="be5f6-154">**Redirect**</span></span>

    <span data-ttu-id="be5f6-155">此类型返回到的重定向操作或目标 (使用`Redirect`， `LocalRedirect`， `RedirectToAction`，或`RedirectToRoute`)。</span><span class="sxs-lookup"><span data-stu-id="be5f6-155">This type returns a redirect to an action or destination (using `Redirect`, `LocalRedirect`, `RedirectToAction`, or `RedirectToRoute`).</span></span> <span data-ttu-id="be5f6-156">例如，`return RedirectToAction("Complete", new {id = 123});`将重定向到`Complete`，传递一个匿名的对象。</span><span class="sxs-lookup"><span data-stu-id="be5f6-156">For example, `return RedirectToAction("Complete", new {id = 123});` redirects to `Complete`, passing an anonymous object.</span></span>

    <span data-ttu-id="be5f6-157">重定向结果类型不同于主要中添加的 HTTP 状态代码类型`Location`HTTP 响应标头。</span><span class="sxs-lookup"><span data-stu-id="be5f6-157">The Redirect result type differs from the HTTP Status Code type primarily in the addition of a `Location` HTTP response header.</span></span>

#### <a name="2-methods-resulting-in-a-non-empty-response-body-with-a-predefined-content-type"></a><span data-ttu-id="be5f6-158">2.导致与预定义的内容类型的非空响应正文的方法</span><span class="sxs-lookup"><span data-stu-id="be5f6-158">2. Methods resulting in a non-empty response body with a predefined content type</span></span>

<span data-ttu-id="be5f6-159">此类别中的大多数帮助器方法包括`ContentType`属性，允许您设置`Content-Type`响应标头来描述响应正文。</span><span class="sxs-lookup"><span data-stu-id="be5f6-159">Most helper methods in this category include a `ContentType` property, allowing you to set the `Content-Type` response header to describe the response body.</span></span>

<span data-ttu-id="be5f6-160">此类别中的两种结果类型：[视图](xref:mvc/views/overview)和[格式化响应](xref:mvc/models/formatting)。</span><span class="sxs-lookup"><span data-stu-id="be5f6-160">There are two result types within this category: [View](xref:mvc/views/overview) and [Formatted Response](xref:mvc/models/formatting).</span></span>

* <span data-ttu-id="be5f6-161">**视图**</span><span class="sxs-lookup"><span data-stu-id="be5f6-161">**View**</span></span>

    <span data-ttu-id="be5f6-162">此类型返回的视图模型用于呈现 HTML。</span><span class="sxs-lookup"><span data-stu-id="be5f6-162">This type returns a view which uses a model to render HTML.</span></span> <span data-ttu-id="be5f6-163">例如，`return View(customer);`将模型传递给数据绑定的视图。</span><span class="sxs-lookup"><span data-stu-id="be5f6-163">For example, `return View(customer);` passes a model to the view for data-binding.</span></span>

* <span data-ttu-id="be5f6-164">**格式的响应**</span><span class="sxs-lookup"><span data-stu-id="be5f6-164">**Formatted Response**</span></span>

    <span data-ttu-id="be5f6-165">此类型返回 JSON 或类似的数据交换格式以特定方式表示的对象。</span><span class="sxs-lookup"><span data-stu-id="be5f6-165">This type returns JSON or a similar data exchange format to represent an object in a specific manner.</span></span> <span data-ttu-id="be5f6-166">例如，`return Json(customer);`所提供的对象序列化为 JSON 格式。</span><span class="sxs-lookup"><span data-stu-id="be5f6-166">For example, `return Json(customer);` serializes the provided object into JSON format.</span></span>
    
    <span data-ttu-id="be5f6-167">此类型的其他常见方法包括`File`， `PhysicalFile`，和`VirtualFile`。</span><span class="sxs-lookup"><span data-stu-id="be5f6-167">Other common methods of this type include `File`, `PhysicalFile`, and `VirtualFile`.</span></span> <span data-ttu-id="be5f6-168">例如，`return PhysicalFile(customerFilePath, "text/xml");`返回由描述 XML 文件`Content-Type`响应标头值为"text/xml"。</span><span class="sxs-lookup"><span data-stu-id="be5f6-168">For example, `return PhysicalFile(customerFilePath, "text/xml");` returns an XML file described by a `Content-Type` response header value of "text/xml".</span></span>

#### <a name="3-methods-resulting-in-a-non-empty-response-body-formatted-in-a-content-type-negotiated-with-the-client"></a><span data-ttu-id="be5f6-169">3.非空响应正文中生成的方法进行格式化的内容类型与客户端协商</span><span class="sxs-lookup"><span data-stu-id="be5f6-169">3. Methods resulting in a non-empty response body formatted in a content type negotiated with the client</span></span>

<span data-ttu-id="be5f6-170">此类别是更好地称为**内容协商**。</span><span class="sxs-lookup"><span data-stu-id="be5f6-170">This category is better known as **Content Negotiation**.</span></span> <span data-ttu-id="be5f6-171">[内容协商](xref:mvc/models/formatting#content-negotiation)每当操作返回[ObjectResult](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.objectresult)类型或以外的其他[IActionResult](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.iactionresult)实现。</span><span class="sxs-lookup"><span data-stu-id="be5f6-171">[Content negotiation](xref:mvc/models/formatting#content-negotiation) applies whenever an action returns an [ObjectResult](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.objectresult) type or something other than an [IActionResult](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.iactionresult) implementation.</span></span> <span data-ttu-id="be5f6-172">返回一个非操作`IActionResult`实现 (例如， `object`) 也会返回格式的响应。</span><span class="sxs-lookup"><span data-stu-id="be5f6-172">An action that returns a non-`IActionResult` implementation (for example, `object`) also returns a Formatted Response.</span></span>

<span data-ttu-id="be5f6-173">此类型的一些帮助器方法包括`BadRequest`， `CreatedAtRoute`，和`Ok`。</span><span class="sxs-lookup"><span data-stu-id="be5f6-173">Some helper methods of this type include `BadRequest`, `CreatedAtRoute`, and `Ok`.</span></span> <span data-ttu-id="be5f6-174">这些方法的示例包括`return BadRequest(modelState);`， `return CreatedAtRoute("routename", values, newobject);`，和`return Ok(value);`分别。</span><span class="sxs-lookup"><span data-stu-id="be5f6-174">Examples of these methods include `return BadRequest(modelState);`, `return CreatedAtRoute("routename", values, newobject);`, and `return Ok(value);`, respectively.</span></span> <span data-ttu-id="be5f6-175">请注意，`BadRequest`和`Ok`执行内容协商仅时传递了一个值，而无需传递一个值，它们而是充当 HTTP 状态代码结果类型。</span><span class="sxs-lookup"><span data-stu-id="be5f6-175">Note that `BadRequest` and `Ok` perform content negotiation only when passed a value; without being passed a value, they instead serve as HTTP Status Code result types.</span></span> <span data-ttu-id="be5f6-176">`CreatedAtRoute`方法，另一方面，始终执行内容协商以来所有要求，将值传递其重载。</span><span class="sxs-lookup"><span data-stu-id="be5f6-176">The `CreatedAtRoute` method, on the other hand, always performs content negotiation since its overloads all require that a value be passed.</span></span>

### <a name="cross-cutting-concerns"></a><span data-ttu-id="be5f6-177">跨领域问题</span><span class="sxs-lookup"><span data-stu-id="be5f6-177">Cross-Cutting Concerns</span></span>

<span data-ttu-id="be5f6-178">应用程序通常共享其流的各个部分。</span><span class="sxs-lookup"><span data-stu-id="be5f6-178">Applications typically share parts of their workflow.</span></span> <span data-ttu-id="be5f6-179">示例包括的应用程序需要身份验证才能访问购物车或在某些页缓存数据的应用。</span><span class="sxs-lookup"><span data-stu-id="be5f6-179">Examples include an app that requires authentication to access the shopping cart, or an app that caches data on some pages.</span></span> <span data-ttu-id="be5f6-180">若要执行逻辑之前, 或之后的操作方法，使用*筛选器*。</span><span class="sxs-lookup"><span data-stu-id="be5f6-180">To perform logic before or after an action method, use a *filter*.</span></span> <span data-ttu-id="be5f6-181">使用[筛选器](xref:mvc/controllers/filters)上的跨领域问题可以减少重复项，从而使它们可以按照[不重复自己 （模拟） 原则](http://deviq.com/don-t-repeat-yourself/)。</span><span class="sxs-lookup"><span data-stu-id="be5f6-181">Using [Filters](xref:mvc/controllers/filters) on cross-cutting concerns can reduce duplication, allowing them to follow the [Don't Repeat Yourself (DRY) principle](http://deviq.com/don-t-repeat-yourself/).</span></span>

<span data-ttu-id="be5f6-182">最筛选属性，如`[Authorize]`，可以在控制器或操作级别取决于所需的粒度级别应用。</span><span class="sxs-lookup"><span data-stu-id="be5f6-182">Most filter attributes, such as `[Authorize]`, can be applied at the controller or action level depending upon the desired level of granularity.</span></span>

<span data-ttu-id="be5f6-183">错误处理和响应缓存通常是跨领域问题：</span><span class="sxs-lookup"><span data-stu-id="be5f6-183">Error handling and response caching are often cross-cutting concerns:</span></span>
   * [<span data-ttu-id="be5f6-184">错误处理</span><span class="sxs-lookup"><span data-stu-id="be5f6-184">Error handling</span></span>](xref:mvc/controllers/filters#exception-filters)
   * [<span data-ttu-id="be5f6-185">响应缓存</span><span class="sxs-lookup"><span data-stu-id="be5f6-185">Response Caching</span></span>](xref:performance/caching/response)

<span data-ttu-id="be5f6-186">可以使用筛选器或自定义处理许多跨领域问题[中间件](xref:fundamentals/middleware)。</span><span class="sxs-lookup"><span data-stu-id="be5f6-186">Many cross-cutting concerns can be handled using filters or custom [middleware](xref:fundamentals/middleware).</span></span>
