---
title: "筛选器"
author: ardalis
description: "了解如何 * 筛选器 * 工作以及如何在 ASP.NET 核心 MVC 中使用它们。"
keywords: "ASP.NET 核心，筛选器、 mvc 筛选器、 操作筛选器、 授权筛选器，资源筛选器，结果筛选器，异常筛选器"
ms.author: tdykstra
manager: wpickett
ms.date: 12/12/2016
ms.topic: article
ms.assetid: 531bda08-aa5b-4471-8f08-96add22c8683
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/filters
ms.openlocfilehash: 215d165d0029bbf38ef37cdde57b80a15bff9ef9
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="filters"></a><span data-ttu-id="02fda-104">筛选器</span><span class="sxs-lookup"><span data-stu-id="02fda-104">Filters</span></span>

<span data-ttu-id="02fda-105">通过[Tom Dykstra](https://github.com/tdykstra/)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="02fda-105">By [Tom Dykstra](https://github.com/tdykstra/) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="02fda-106">*筛选器*在 ASP.NET 核心 MVC 允许你运行代码之前或之后请求处理管道中的某些阶段。</span><span class="sxs-lookup"><span data-stu-id="02fda-106">*Filters* in ASP.NET Core MVC allow you to run code before or after certain stages in the request processing pipeline.</span></span>

 <span data-ttu-id="02fda-107">内置筛选器句柄任务，例如授权 （防止对资源的用户未获得授权的访问），确保所有请求都使用 HTTPS 和响应缓存 （短路要返回的缓存的响应的请求管线）。</span><span class="sxs-lookup"><span data-stu-id="02fda-107">Built-in filters handle tasks such as authorization (preventing access to resources a user isn't authorized for), ensuring that all requests use HTTPS, and response caching (short-circuiting the request pipeline to return a cached response).</span></span> 

<span data-ttu-id="02fda-108">你可以创建自定义筛选器来处理你的应用程序的跨领域问题。</span><span class="sxs-lookup"><span data-stu-id="02fda-108">You can create custom filters to handle cross-cutting concerns for your application.</span></span> <span data-ttu-id="02fda-109">每当你想要避免跨操作重复代码，则筛选器是该解决方案。</span><span class="sxs-lookup"><span data-stu-id="02fda-109">Anytime you want to avoid duplicating code across actions, filters are the solution.</span></span> <span data-ttu-id="02fda-110">例如，你可以合并错误处理代码异常筛选器中。</span><span class="sxs-lookup"><span data-stu-id="02fda-110">For example, you can consolidate error handling code in a exception filter.</span></span>

<span data-ttu-id="02fda-111">[查看或从 GitHub 下载示例](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)。</span><span class="sxs-lookup"><span data-stu-id="02fda-111">[View or download sample from GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

## <a name="how-do-filters-work"></a><span data-ttu-id="02fda-112">筛选器是如何工作的？</span><span class="sxs-lookup"><span data-stu-id="02fda-112">How do filters work?</span></span>

<span data-ttu-id="02fda-113">筛选器内运行*MVC 操作调用管道*，有时称为*筛选器管道*。</span><span class="sxs-lookup"><span data-stu-id="02fda-113">Filters run within the *MVC action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="02fda-114">筛选器管道运行后 MVC 选择要执行的操作。</span><span class="sxs-lookup"><span data-stu-id="02fda-114">The filter pipeline runs after MVC selects the action to execute.</span></span>

![通过其他中间件、 路由中间件、 操作选择和 MVC 操作调用管道处理该请求。](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="02fda-117">筛选器类型</span><span class="sxs-lookup"><span data-stu-id="02fda-117">Filter types</span></span>

<span data-ttu-id="02fda-118">在筛选器管道中的不同阶段执行每个筛选器类型。</span><span class="sxs-lookup"><span data-stu-id="02fda-118">Each filter type is executed at a different stage in the filter pipeline.</span></span>

* <span data-ttu-id="02fda-119">[授权筛选器](#authorization-filters)运行第一个，用于确定当前用户是否被授权为当前请求。</span><span class="sxs-lookup"><span data-stu-id="02fda-119">[Authorization filters](#authorization-filters) run first and are used to determine whether the current user is authorized for the current request.</span></span> <span data-ttu-id="02fda-120">如果请求未获授权，它们可以绕过管道。</span><span class="sxs-lookup"><span data-stu-id="02fda-120">They can short-circuit the pipeline if a request is unauthorized.</span></span> 

* <span data-ttu-id="02fda-121">[资源筛选器](#resource-filters)是最先授权后处理请求。</span><span class="sxs-lookup"><span data-stu-id="02fda-121">[Resource filters](#resource-filters) are the first to handle a request after authorization.</span></span>  <span data-ttu-id="02fda-122">它们可以运行之前和之后的管道的其余部分已完成的筛选器管道的其余部分的代码。</span><span class="sxs-lookup"><span data-stu-id="02fda-122">They can run code before the rest of the filter pipeline, and after the rest of the pipeline has completed.</span></span> <span data-ttu-id="02fda-123">这些控件来实施缓存或否则短路筛选器管道出于性能原因，很有用。</span><span class="sxs-lookup"><span data-stu-id="02fda-123">They're useful to implement caching or otherwise short-circuit the filter pipeline for performance reasons.</span></span> <span data-ttu-id="02fda-124">因为它们运行在模型绑定之前，它们可用于任何需要来影响模型绑定的内容。</span><span class="sxs-lookup"><span data-stu-id="02fda-124">Since they run before model binding, they're useful for anything that needs to influence model binding.</span></span>

* <span data-ttu-id="02fda-125">[操作筛选器](#action-filters)立即之前和之后调用了各个操作的方法，可以运行代码。</span><span class="sxs-lookup"><span data-stu-id="02fda-125">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="02fda-126">它们可以用于操作转换为一个操作传递的自变量和返回的操作的结果。</span><span class="sxs-lookup"><span data-stu-id="02fda-126">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span>

* <span data-ttu-id="02fda-127">[异常筛选器](#exception-filters)用于将全局策略应用到已向响应正文写入任何内容之前发生的未经处理异常。</span><span class="sxs-lookup"><span data-stu-id="02fda-127">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="02fda-128">[导致筛选器](#result-filters)立即之前和之后的单个操作结果的执行，可以运行代码。</span><span class="sxs-lookup"><span data-stu-id="02fda-128">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="02fda-129">它们仅在已成功执行的操作方法时，才运行，并且可用于必须括起视图或格式化程序执行的逻辑。</span><span class="sxs-lookup"><span data-stu-id="02fda-129">They run only when the action method has executed successfully and are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="02fda-130">下图显示了这些筛选器类型在筛选器管道中的交互方式。</span><span class="sxs-lookup"><span data-stu-id="02fda-130">The following diagram shows how these filter types interact in the filter pipeline.</span></span>

![通过授权筛选器、 资源筛选器、 模型绑定，操作筛选器、 操作执行和操作结果转换、 异常筛选器，结果筛选器和结果执行处理该请求。](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="02fda-133">实现</span><span class="sxs-lookup"><span data-stu-id="02fda-133">Implementation</span></span>

<span data-ttu-id="02fda-134">筛选器支持通过不同的接口定义的同步和异步实现。</span><span class="sxs-lookup"><span data-stu-id="02fda-134">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span> <span data-ttu-id="02fda-135">选择的同步或异步的变体，具体取决于你需要执行的任务的类型。</span><span class="sxs-lookup"><span data-stu-id="02fda-135">Choose either the sync or async variant depending on the kind of task you need to perform.</span></span> 

<span data-ttu-id="02fda-136">可以运行的同步筛选器的代码同时之前和之后对其管道阶段定义*阶段*执行并在*阶段*执行方法。</span><span class="sxs-lookup"><span data-stu-id="02fda-136">Synchronous filters that can run code both before and after their pipeline stage define On*Stage*Executing and On*Stage*Executed methods.</span></span> <span data-ttu-id="02fda-137">例如，`OnActionExecuting`之前调用的操作方法，调用和`OnActionExecuted`后操作方法会返回调用。</span><span class="sxs-lookup"><span data-stu-id="02fda-137">For example, `OnActionExecuting` is called before the action method is called, and `OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/SampleActionFilter.cs?highlight=6,8,13)]

<span data-ttu-id="02fda-138">异步筛选器上定义单个*阶段*ExecutionAsync 方法。</span><span class="sxs-lookup"><span data-stu-id="02fda-138">Asynchronous filters define a single On*Stage*ExecutionAsync method.</span></span> <span data-ttu-id="02fda-139">此方法采用*FilterType*ExecutionDelegate 委托，它执行筛选器的管道阶段。</span><span class="sxs-lookup"><span data-stu-id="02fda-139">This method takes a *FilterType*ExecutionDelegate delegate which executes the filter's pipeline stage.</span></span> <span data-ttu-id="02fda-140">例如，`ActionExecutionDelegate`操作方法中，并且你可以执行的调用的代码之前和之后调用它。</span><span class="sxs-lookup"><span data-stu-id="02fda-140">For example, `ActionExecutionDelegate` calls the action method, and you can execute code before and after you call it.</span></span>

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/SampleAsyncActionFilter.cs?highlight=6,8-10,13)]

<span data-ttu-id="02fda-141">单个类中的多个筛选器阶段，你可以实现接口。</span><span class="sxs-lookup"><span data-stu-id="02fda-141">You can implement interfaces for multiple filter stages in a single class.</span></span> <span data-ttu-id="02fda-142">例如， [ActionFilterAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionfilterattribute)抽象类同时实现`IActionFilter`和`IResultFilter`，以及它们的异步等效项。</span><span class="sxs-lookup"><span data-stu-id="02fda-142">For example, the [ActionFilterAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionfilterattribute) abstract class implements both `IActionFilter` and `IResultFilter`, as well as their async equivalents.</span></span>

> [!NOTE]
> <span data-ttu-id="02fda-143">实现**任一**同步或筛选器接口，不能同时的异步版本。</span><span class="sxs-lookup"><span data-stu-id="02fda-143">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="02fda-144">框架会首先检查以查看如果筛选器实现异步接口，并且如果是这样，它调用的。</span><span class="sxs-lookup"><span data-stu-id="02fda-144">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="02fda-145">否则，它将调用同步接口的方法。</span><span class="sxs-lookup"><span data-stu-id="02fda-145">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="02fda-146">如果你是在一个类中实现这两个接口，将调用仅异步方法。</span><span class="sxs-lookup"><span data-stu-id="02fda-146">If you were to implement both interfaces on one class, only the async method would be called.</span></span> <span data-ttu-id="02fda-147">使用抽象类，如时[ActionFilterAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionfilterattribute)仅同步的方法或其异步方法的每个筛选器类型时，将重写。</span><span class="sxs-lookup"><span data-stu-id="02fda-147">When using abstract classes like [ActionFilterAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionfilterattribute) you would override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="ifilterfactory"></a><span data-ttu-id="02fda-148">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="02fda-148">IFilterFactory</span></span>

<span data-ttu-id="02fda-149">`IFilterFactory` 可实现 `IFilter`。</span><span class="sxs-lookup"><span data-stu-id="02fda-149">`IFilterFactory` implements `IFilter`.</span></span> <span data-ttu-id="02fda-150">因此，`IFilterFactory`实例可以用作`IFilter`筛选器管道中的任意位置的实例。</span><span class="sxs-lookup"><span data-stu-id="02fda-150">Therefore, an `IFilterFactory` instance can be used as an `IFilter` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="02fda-151">当框架准备调用筛选器时，它尝试将它转换到`IFilterFactory`。</span><span class="sxs-lookup"><span data-stu-id="02fda-151">When the framework prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="02fda-152">如果该强制转换成功，`CreateInstance`调用方法来创建`IFilter`将调用的实例。</span><span class="sxs-lookup"><span data-stu-id="02fda-152">If that cast succeeds, the `CreateInstance` method is called to create the `IFilter` instance that will be invoked.</span></span> <span data-ttu-id="02fda-153">这提供了非常灵活的设计，因为无需在应用程序启动时显式设置精确的筛选器管道。</span><span class="sxs-lookup"><span data-stu-id="02fda-153">This provides a very flexible design, since the precise filter pipeline does not need to be set explicitly when the application starts.</span></span>

<span data-ttu-id="02fda-154">你可以实现`IFilterFactory`上创建筛选器的另一种方法为您自己属性的实现：</span><span class="sxs-lookup"><span data-stu-id="02fda-154">You can implement `IFilterFactory` on your own attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

### <a name="built-in-filter-attributes"></a><span data-ttu-id="02fda-155">内置筛选器属性</span><span class="sxs-lookup"><span data-stu-id="02fda-155">Built-in filter attributes</span></span>

<span data-ttu-id="02fda-156">Framework 包括内置基于属性的筛选器可以子类化和自定义。</span><span class="sxs-lookup"><span data-stu-id="02fda-156">The framework includes built-in attribute-based filters that you can subclass and customize.</span></span> <span data-ttu-id="02fda-157">例如，以下结果筛选器添加到响应的标头。</span><span class="sxs-lookup"><span data-stu-id="02fda-157">For example, the following Result filter adds a header to the response.</span></span>

<a name=add-header-attribute></a>

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/AddHeaderAttribute.cs?highlight=5,16)]

<span data-ttu-id="02fda-158">属性允许筛选器以接受自变量，如上面的示例中所示。</span><span class="sxs-lookup"><span data-stu-id="02fda-158">Attributes allow filters to accept arguments, as shown in the example above.</span></span> <span data-ttu-id="02fda-159">会将此属性添加到控制器或操作的方法和指定的名称和 HTTP 标头的值：</span><span class="sxs-lookup"><span data-stu-id="02fda-159">You would add this attribute to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[Main](./filters/sample/src/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="02fda-160">结果`Index`操作如下所示-响应标头显示在右下角。</span><span class="sxs-lookup"><span data-stu-id="02fda-160">The result of the `Index` action is shown below - the response headers are displayed on the bottom right.</span></span>

![开发人员工具的 Microsoft Edge 显示响应标头，包括作者 Steve Smith@ardalis](filters/_static/add-header.png)

<span data-ttu-id="02fda-162">多个筛选器接口具有可以用于自定义实现作为基类的相应属性。</span><span class="sxs-lookup"><span data-stu-id="02fda-162">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="02fda-163">筛选器属性：</span><span class="sxs-lookup"><span data-stu-id="02fda-163">Filter attributes:</span></span>

* `ActionFilterAttribute`
* `ExceptionFilterAttribute`
* `ResultFilterAttribute`
* `FormatFilterAttribute`
* `ServiceFilterAttribute`
* `TypeFilterAttribute`

<span data-ttu-id="02fda-164">`TypeFilterAttribute`和`ServiceFilterAttribute`解释了[本文稍后的](#dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="02fda-164">`TypeFilterAttribute` and `ServiceFilterAttribute` are explained [later in this article](#dependency-injection).</span></span>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="02fda-165">筛选器作用域和执行顺序</span><span class="sxs-lookup"><span data-stu-id="02fda-165">Filter scopes and order of execution</span></span>

<span data-ttu-id="02fda-166">筛选器可以添加到在三个管道*作用域*。</span><span class="sxs-lookup"><span data-stu-id="02fda-166">A filter can be added to the pipeline at one of three *scopes*.</span></span> <span data-ttu-id="02fda-167">你可以使用属性，特定的操作方法或引用了控制器类添加筛选器。</span><span class="sxs-lookup"><span data-stu-id="02fda-167">You can add a filter to a particular action method or to a controller class by using an attribute.</span></span> <span data-ttu-id="02fda-168">或者，也可以通过将其添加到注册全局 （用于所有控制器和操作） 的筛选器`MvcOptions.Filters`中的集合`ConfigureServices`中的方法`Startup`类：</span><span class="sxs-lookup"><span data-stu-id="02fda-168">Or you can register a filter globally (for all controllers and actions) by adding it to the `MvcOptions.Filters` collection in the `ConfigureServices` method in the `Startup` class:</span></span>

[!code-csharp[Main](./filters/sample/src/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

### <a name="default-order-of-execution"></a><span data-ttu-id="02fda-169">默认执行顺序</span><span class="sxs-lookup"><span data-stu-id="02fda-169">Default order of execution</span></span>

<span data-ttu-id="02fda-170">当管道一特定阶段存在多个筛选器时，作用域确定筛选器执行的默认顺序。</span><span class="sxs-lookup"><span data-stu-id="02fda-170">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="02fda-171">全局筛选器括住类筛选器，反过来环绕方法筛选器。</span><span class="sxs-lookup"><span data-stu-id="02fda-171">Global filters surround class filters, which in turn surround method filters.</span></span> <span data-ttu-id="02fda-172">这有时称为"俄语玩具"嵌套层，如作用域中的每一项增加环绕以前的范围内，如[嵌套玩具](https://wikipedia.org/wiki/Matryoshka_doll)。</span><span class="sxs-lookup"><span data-stu-id="02fda-172">This is sometimes referred to as "Russian doll" nesting, as each increase in scope is wrapped around the previous scope, like a [nesting doll](https://wikipedia.org/wiki/Matryoshka_doll).</span></span> <span data-ttu-id="02fda-173">通常情况下，而无需显式确定排序需要获得所需的重写行为。</span><span class="sxs-lookup"><span data-stu-id="02fda-173">You generally get the desired overriding behavior without having to explicitly determine ordering.</span></span>

<span data-ttu-id="02fda-174">由于这种嵌套，*后*的筛选器的代码在相反的顺序运行*之前*代码。</span><span class="sxs-lookup"><span data-stu-id="02fda-174">As a result of this nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="02fda-175">序列如下所示：</span><span class="sxs-lookup"><span data-stu-id="02fda-175">The sequence looks like this:</span></span>

* <span data-ttu-id="02fda-176">*之前*全局应用的筛选器的代码</span><span class="sxs-lookup"><span data-stu-id="02fda-176">The *before* code of filters applied globally</span></span>
  * <span data-ttu-id="02fda-177">*之前*应用到控制器的筛选器的代码</span><span class="sxs-lookup"><span data-stu-id="02fda-177">The *before* code of filters applied to controllers</span></span>
    * <span data-ttu-id="02fda-178">*之前*的筛选器应用于操作方法的代码</span><span class="sxs-lookup"><span data-stu-id="02fda-178">The *before* code of filters applied to action methods</span></span>
    * <span data-ttu-id="02fda-179">*后*的筛选器应用于操作方法的代码</span><span class="sxs-lookup"><span data-stu-id="02fda-179">The *after* code of filters applied to action methods</span></span>
  * <span data-ttu-id="02fda-180">*后*应用到控制器的筛选器的代码</span><span class="sxs-lookup"><span data-stu-id="02fda-180">The *after* code of filters applied to controllers</span></span>
* <span data-ttu-id="02fda-181">*后*全局应用的筛选器的代码</span><span class="sxs-lookup"><span data-stu-id="02fda-181">The *after* code of filters applied globally</span></span>
  
<span data-ttu-id="02fda-182">下面是一个示例，说明了为同步操作筛选器的筛选器中调用方法的顺序。</span><span class="sxs-lookup"><span data-stu-id="02fda-182">Here's an example that illustrates the order in which filter methods are called for synchronous Action filters.</span></span>

| <span data-ttu-id="02fda-183">序列</span><span class="sxs-lookup"><span data-stu-id="02fda-183">Sequence</span></span> | <span data-ttu-id="02fda-184">筛选器作用域</span><span class="sxs-lookup"><span data-stu-id="02fda-184">Filter scope</span></span> | <span data-ttu-id="02fda-185">Filter 方法</span><span class="sxs-lookup"><span data-stu-id="02fda-185">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="02fda-186">1</span><span class="sxs-lookup"><span data-stu-id="02fda-186">1</span></span> | <span data-ttu-id="02fda-187">Global</span><span class="sxs-lookup"><span data-stu-id="02fda-187">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="02fda-188">2</span><span class="sxs-lookup"><span data-stu-id="02fda-188">2</span></span> | <span data-ttu-id="02fda-189">控制器</span><span class="sxs-lookup"><span data-stu-id="02fda-189">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="02fda-190">3</span><span class="sxs-lookup"><span data-stu-id="02fda-190">3</span></span> | <span data-ttu-id="02fda-191">方法</span><span class="sxs-lookup"><span data-stu-id="02fda-191">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="02fda-192">4</span><span class="sxs-lookup"><span data-stu-id="02fda-192">4</span></span> | <span data-ttu-id="02fda-193">方法</span><span class="sxs-lookup"><span data-stu-id="02fda-193">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="02fda-194">5</span><span class="sxs-lookup"><span data-stu-id="02fda-194">5</span></span> | <span data-ttu-id="02fda-195">控制器</span><span class="sxs-lookup"><span data-stu-id="02fda-195">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="02fda-196">6</span><span class="sxs-lookup"><span data-stu-id="02fda-196">6</span></span> | <span data-ttu-id="02fda-197">Global</span><span class="sxs-lookup"><span data-stu-id="02fda-197">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="02fda-198">此序列显示中的控制器筛选器，嵌套方法筛选器和控制器筛选器嵌套在全局筛选器。</span><span class="sxs-lookup"><span data-stu-id="02fda-198">This sequence shows that the method filter is nested within the controller filter, and the controller filter is nested within the global filter.</span></span> <span data-ttu-id="02fda-199">将其置于另一个方法，如果你在异步筛选器的*阶段*ExecutionAsync 方法的所有具有更严格的作用域筛选器时，运行你的代码在堆栈上。</span><span class="sxs-lookup"><span data-stu-id="02fda-199">To put it another way, if you're inside an async filter's On*Stage*ExecutionAsync method, all of the filters with a tighter scope run while your code is on the stack.</span></span>

> [!NOTE]
> <span data-ttu-id="02fda-200">继承自的每个控制器`Controller`基类包括`OnActionExecuting`和`OnActionExecuted`方法。</span><span class="sxs-lookup"><span data-stu-id="02fda-200">Every controller that inherits from the `Controller` base class includes `OnActionExecuting` and `OnActionExecuted` methods.</span></span> <span data-ttu-id="02fda-201">这些方法会包装为某个给定操作运行的筛选器：`OnActionExecuting`称为筛选器，在任何之前和`OnActionExecuted`后的所有筛选器，将调用。</span><span class="sxs-lookup"><span data-stu-id="02fda-201">These methods wrap the filters that run for a given action:  `OnActionExecuting` is called before any of the filters, and `OnActionExecuted` is called after all of the filters.</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="02fda-202">重写默认顺序</span><span class="sxs-lookup"><span data-stu-id="02fda-202">Overriding the default order</span></span>

<span data-ttu-id="02fda-203">可以通过实现重写默认的序列的执行`IOrderedFilter`。</span><span class="sxs-lookup"><span data-stu-id="02fda-203">You can override the default sequence of execution by implementing `IOrderedFilter`.</span></span> <span data-ttu-id="02fda-204">此接口公开`Order`优先于范围，以确定执行顺序的属性。</span><span class="sxs-lookup"><span data-stu-id="02fda-204">This interface exposes an `Order` property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="02fda-205">具有较低的筛选器`Order`值将具有其*之前*早于具有较高值的筛选器执行代码`Order`。</span><span class="sxs-lookup"><span data-stu-id="02fda-205">A filter with a lower `Order` value will have its *before* code executed before that of a filter with a higher value of `Order`.</span></span> <span data-ttu-id="02fda-206">具有较低的筛选器`Order`值将具有其*后*之后，具有更高的筛选器执行的代码`Order`值。</span><span class="sxs-lookup"><span data-stu-id="02fda-206">A filter with a lower `Order` value will have its *after* code executed after that of a filter with a higher `Order` value.</span></span> <span data-ttu-id="02fda-207">你可以设置`Order`通过使用构造函数参数的属性：</span><span class="sxs-lookup"><span data-stu-id="02fda-207">You can set the `Order` property by using a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="02fda-208">如果前面的示例，但组中所示的 3 操作筛选器具有相同`Order`属性的控制器和全局到 1 和 2 分别筛选时，将反转的执行顺序。</span><span class="sxs-lookup"><span data-stu-id="02fda-208">If you have the same 3 Action filters shown in the preceding example but set the `Order` property of the controller and global filters to 1 and 2 respectively, the order of execution would be reversed.</span></span>

| <span data-ttu-id="02fda-209">序列</span><span class="sxs-lookup"><span data-stu-id="02fda-209">Sequence</span></span> | <span data-ttu-id="02fda-210">筛选器作用域</span><span class="sxs-lookup"><span data-stu-id="02fda-210">Filter scope</span></span> | <span data-ttu-id="02fda-211">`Order` 属性</span><span class="sxs-lookup"><span data-stu-id="02fda-211">`Order` property</span></span> | <span data-ttu-id="02fda-212">Filter 方法</span><span class="sxs-lookup"><span data-stu-id="02fda-212">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="02fda-213">1</span><span class="sxs-lookup"><span data-stu-id="02fda-213">1</span></span> | <span data-ttu-id="02fda-214">方法</span><span class="sxs-lookup"><span data-stu-id="02fda-214">Method</span></span> | <span data-ttu-id="02fda-215">0</span><span class="sxs-lookup"><span data-stu-id="02fda-215">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="02fda-216">2</span><span class="sxs-lookup"><span data-stu-id="02fda-216">2</span></span> | <span data-ttu-id="02fda-217">控制器</span><span class="sxs-lookup"><span data-stu-id="02fda-217">Controller</span></span> | <span data-ttu-id="02fda-218">1</span><span class="sxs-lookup"><span data-stu-id="02fda-218">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="02fda-219">3</span><span class="sxs-lookup"><span data-stu-id="02fda-219">3</span></span> | <span data-ttu-id="02fda-220">Global</span><span class="sxs-lookup"><span data-stu-id="02fda-220">Global</span></span> | <span data-ttu-id="02fda-221">2</span><span class="sxs-lookup"><span data-stu-id="02fda-221">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="02fda-222">4</span><span class="sxs-lookup"><span data-stu-id="02fda-222">4</span></span> | <span data-ttu-id="02fda-223">Global</span><span class="sxs-lookup"><span data-stu-id="02fda-223">Global</span></span> | <span data-ttu-id="02fda-224">2</span><span class="sxs-lookup"><span data-stu-id="02fda-224">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="02fda-225">5</span><span class="sxs-lookup"><span data-stu-id="02fda-225">5</span></span> | <span data-ttu-id="02fda-226">控制器</span><span class="sxs-lookup"><span data-stu-id="02fda-226">Controller</span></span> | <span data-ttu-id="02fda-227">1</span><span class="sxs-lookup"><span data-stu-id="02fda-227">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="02fda-228">6</span><span class="sxs-lookup"><span data-stu-id="02fda-228">6</span></span> | <span data-ttu-id="02fda-229">方法</span><span class="sxs-lookup"><span data-stu-id="02fda-229">Method</span></span> | <span data-ttu-id="02fda-230">0</span><span class="sxs-lookup"><span data-stu-id="02fda-230">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="02fda-231">`Order`属性优于作用域时确定筛选器将在其中运行的顺序。</span><span class="sxs-lookup"><span data-stu-id="02fda-231">The `Order` property trumps scope when determining the order in which filters will run.</span></span> <span data-ttu-id="02fda-232">按顺序，筛选器进行第一次排序，则将使用范围中消除并列。</span><span class="sxs-lookup"><span data-stu-id="02fda-232">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="02fda-233">所有内置筛选器实现`IOrderedFilter`和设置默认`Order`值为 0，因此作用域确定顺序，除非你设置`Order`为非零值。</span><span class="sxs-lookup"><span data-stu-id="02fda-233">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0, so scope determines order unless you set `Order` to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="02fda-234">取消和执行短路</span><span class="sxs-lookup"><span data-stu-id="02fda-234">Cancellation and short circuiting</span></span>

<span data-ttu-id="02fda-235">你可以通过设置短路随时筛选器管道`Result`属性`context`提供给筛选器方法的参数。</span><span class="sxs-lookup"><span data-stu-id="02fda-235">You can short-circuit the filter pipeline at any point by setting the `Result` property on the `context` parameter provided to the filter method.</span></span> <span data-ttu-id="02fda-236">例如，以下资源筛选器将阻止执行管道的其余部分。</span><span class="sxs-lookup"><span data-stu-id="02fda-236">For instance, the following Resource filter prevents the rest of the pipeline from executing.</span></span>

<a name=short-circuiting-resource-filter></a>

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?highlight=12,13,14,15)]

<span data-ttu-id="02fda-237">在下面的代码中，同时`ShortCircuitingResourceFilter`和`AddHeader`筛选器目标`SomeResource`操作方法。</span><span class="sxs-lookup"><span data-stu-id="02fda-237">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="02fda-238">但是，因为`ShortCircuitingResourceFilter`首先运行 (因为它是资源筛选器和`AddHeader`是操作筛选器) 和会使短路的管道，rest`AddHeader`筛选器永远不会运行`SomeResource`操作。</span><span class="sxs-lookup"><span data-stu-id="02fda-238">However, because the `ShortCircuitingResourceFilter` runs first (because it is a Resource Filter and `AddHeader` is an Action Filter) and short-circuits the rest of the pipeline, the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="02fda-239">如果两个筛选器已在提供的操作方法级别上应用此行为将相同`ShortCircuitingResourceFilter`运行第一个 (由于其筛选器类型，或显式使用`Order`属性，例如)。</span><span class="sxs-lookup"><span data-stu-id="02fda-239">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first (because of its filter type, or explicit use of `Order` property, for instance).</span></span>

[!code-csharp[Main](./filters/sample/src/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="02fda-240">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="02fda-240">Dependency injection</span></span>

<span data-ttu-id="02fda-241">按类型或实例，可以添加筛选器。</span><span class="sxs-lookup"><span data-stu-id="02fda-241">Filters can be added by type or by instance.</span></span> <span data-ttu-id="02fda-242">如果你添加的实例，该实例将用于每个请求。</span><span class="sxs-lookup"><span data-stu-id="02fda-242">If you add an instance, that instance will be used for every request.</span></span> <span data-ttu-id="02fda-243">如果你添加一种类型，它会将类型激活，这意味着将为每个请求创建的实例，并且任何构造函数依赖关系将用来填充[依赖关系注入](../../fundamentals/dependency-injection.md)(DI)。</span><span class="sxs-lookup"><span data-stu-id="02fda-243">If you add a type, it will be type-activated, meaning an instance will be created for each request and any constructor dependencies will be populated by [dependency injection](../../fundamentals/dependency-injection.md) (DI).</span></span> <span data-ttu-id="02fda-244">添加按类型筛选器相当于`filters.Add(new TypeFilterAttribute(typeof(MyFilter)))`。</span><span class="sxs-lookup"><span data-stu-id="02fda-244">Adding a filter by type is equivalent to `filters.Add(new TypeFilterAttribute(typeof(MyFilter)))`.</span></span>

<span data-ttu-id="02fda-245">作为属性实现并直接添加到控制器类或操作方法筛选器不能由提供的构造函数依赖关系[依赖关系注入](../../fundamentals/dependency-injection.md)(DI)。</span><span class="sxs-lookup"><span data-stu-id="02fda-245">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](../../fundamentals/dependency-injection.md) (DI).</span></span> <span data-ttu-id="02fda-246">这是因为属性必须提供在其中应用其构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="02fda-246">This is because attributes must have their constructor parameters supplied where they are applied.</span></span> <span data-ttu-id="02fda-247">这是属性的工作原理的限制。</span><span class="sxs-lookup"><span data-stu-id="02fda-247">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="02fda-248">如果你的筛选器从 DI 访问所需的依赖项，有几种受支持的方法。</span><span class="sxs-lookup"><span data-stu-id="02fda-248">If your filters have dependencies that you need to access from DI, there are several supported approaches.</span></span> <span data-ttu-id="02fda-249">你可以将筛选器应用到类或操作的方法，使用以下项之一：</span><span class="sxs-lookup"><span data-stu-id="02fda-249">You can apply your filter to a class or action method using one of the following:</span></span>

* `ServiceFilterAttribute`
* `TypeFilterAttribute`
* <span data-ttu-id="02fda-250">`IFilterFactory`在特性上实现</span><span class="sxs-lookup"><span data-stu-id="02fda-250">`IFilterFactory` implemented on your attribute</span></span>

> [!NOTE]
> <span data-ttu-id="02fda-251">一个依赖关系，你可能想要获得 DI 是一个记录器。</span><span class="sxs-lookup"><span data-stu-id="02fda-251">One dependency you might want to get from DI is a logger.</span></span> <span data-ttu-id="02fda-252">但是，避免创建和纯粹用于日志记录，因为使用筛选器[内置框架，日志记录功能](../../fundamentals/logging.md)可能已经提供了你的需要。</span><span class="sxs-lookup"><span data-stu-id="02fda-252">However, avoid creating and using filters purely for logging purposes, since the [built-in framework logging features](../../fundamentals/logging.md) may already provide what you need.</span></span> <span data-ttu-id="02fda-253">如果你要将日志记录添加到你的筛选器，则它应将重点放在业务域关注点或特定于你的筛选器，而不是 MVC 操作或其他框架的事件的行为。</span><span class="sxs-lookup"><span data-stu-id="02fda-253">If you're going to add logging to your filters, it should focus on business domain concerns or behavior specific to your filter, rather than MVC actions or other framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="02fda-254">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="02fda-254">ServiceFilterAttribute</span></span>

<span data-ttu-id="02fda-255">A`ServiceFilter`从 DI 检索筛选器实例。</span><span class="sxs-lookup"><span data-stu-id="02fda-255">A `ServiceFilter` retrieves an instance of the filter from DI.</span></span> <span data-ttu-id="02fda-256">中的容器中添加筛选器`ConfigureServices`，并引用它在`ServiceFilter`属性</span><span class="sxs-lookup"><span data-stu-id="02fda-256">You add the filter to the container in `ConfigureServices`, and reference it in a `ServiceFilter` attribute</span></span>

[!code-csharp[Main](./filters/sample/src/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=11)]

[!code-csharp[Main](../../mvc/controllers/filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="02fda-257">使用`ServiceFilter`而无需注册引发异常的筛选器类型结果：</span><span class="sxs-lookup"><span data-stu-id="02fda-257">Using `ServiceFilter` without registering the filter type results in an exception:</span></span>

```
System.InvalidOperationException: No service for type
'FiltersSample.Filters.AddHeaderFilterWithDI' has been registered.
```

<span data-ttu-id="02fda-258">`ServiceFilterAttribute`实现`IFilterFactory`，从而将公开用于创建的单一方法`IFilter`实例。</span><span class="sxs-lookup"><span data-stu-id="02fda-258">`ServiceFilterAttribute` implements `IFilterFactory`, which exposes a single method for creating an `IFilter` instance.</span></span> <span data-ttu-id="02fda-259">情况下`ServiceFilterAttribute`、`IFilterFactory`接口的`CreateInstance`实现方法是为了从服务容器 (DI) 加载指定的类型。</span><span class="sxs-lookup"><span data-stu-id="02fda-259">In the case of `ServiceFilterAttribute`, the `IFilterFactory` interface's `CreateInstance` method is implemented to load the specified type from the services container (DI).</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="02fda-260">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="02fda-260">TypeFilterAttribute</span></span>

<span data-ttu-id="02fda-261">`TypeFilterAttribute`非常类似于`ServiceFilterAttribute`(还会实现`IFilterFactory`)，但其类型不能解决直接从 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="02fda-261">`TypeFilterAttribute` is very similar to `ServiceFilterAttribute` (and also implements `IFilterFactory`), but its type is not resolved directly from the DI container.</span></span> <span data-ttu-id="02fda-262">相反，它通过实例化类型使用`Microsoft.Extensions.DependencyInjection.ObjectFactory`。</span><span class="sxs-lookup"><span data-stu-id="02fda-262">Instead, it instantiates the type by using `Microsoft.Extensions.DependencyInjection.ObjectFactory`.</span></span>

<span data-ttu-id="02fda-263">因为这一区别，使用引用的类型`TypeFilterAttribute`无需首先注册与该容器 （但仍将由容器满足其依赖项）。</span><span class="sxs-lookup"><span data-stu-id="02fda-263">Because of this difference, types that are referenced using the `TypeFilterAttribute` do not need to be registered with the container first (but they will still have their dependencies fulfilled by the container).</span></span> <span data-ttu-id="02fda-264">此外，`TypeFilterAttribute`可以选择接受所涉及的类型的构造函数自变量。</span><span class="sxs-lookup"><span data-stu-id="02fda-264">Also, `TypeFilterAttribute` can optionally accept constructor arguments for the type in question.</span></span> <span data-ttu-id="02fda-265">下面的示例演示如何将自变量传递给类型使用`TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="02fda-265">The following example demonstrates how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[Main](../../mvc/controllers/filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<span data-ttu-id="02fda-266">如果具有筛选器不需要任何参数，但其具有需要由 DI 填写的构造函数依赖关系，你可以类和方法而不是使用你自己的命名的属性`[TypeFilter(typeof(FilterType))]`)。</span><span class="sxs-lookup"><span data-stu-id="02fda-266">If you have a filter that doesn't require any arguments, but which has constructor dependencies that need to be filled by DI, you can use your own named attribute on classes and methods instead of `[TypeFilter(typeof(FilterType))]`).</span></span> <span data-ttu-id="02fda-267">下面的筛选器演示如何实现此：</span><span class="sxs-lookup"><span data-stu-id="02fda-267">The following filter shows how this can be implemented:</span></span>

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="02fda-268">此筛选器可以应用于类或方法使用`[SampleActionFilter]`语法，而不必使用`[TypeFilter]`或`[ServiceFilter]`。</span><span class="sxs-lookup"><span data-stu-id="02fda-268">This filter can be applied to classes or methods using the `[SampleActionFilter]` syntax, instead of having to use `[TypeFilter]` or `[ServiceFilter]`.</span></span>

## <a name="authorization-filters"></a><span data-ttu-id="02fda-269">授权筛选器</span><span class="sxs-lookup"><span data-stu-id="02fda-269">Authorization filters</span></span>

<span data-ttu-id="02fda-270">*授权筛选器*控制对操作方法的访问，并且要在筛选器管道内执行的第一个筛选器。</span><span class="sxs-lookup"><span data-stu-id="02fda-270">*Authorization filters* control access to action methods and are the first filters to be executed within the filter pipeline.</span></span> <span data-ttu-id="02fda-271">它们只具有方法，与不同的是支持之前和之后方法的大多数筛选器之前。</span><span class="sxs-lookup"><span data-stu-id="02fda-271">They have only a before method, unlike most filters that support before and after methods.</span></span> <span data-ttu-id="02fda-272">仅应编写自定义授权筛选器如果你正在编写你自己的授权框架。</span><span class="sxs-lookup"><span data-stu-id="02fda-272">You should only write a custom authorization filter if you are writing your own authorization framework.</span></span> <span data-ttu-id="02fda-273">首选配置授权策略，或通过编写自定义筛选器编写一个自定义授权策略。</span><span class="sxs-lookup"><span data-stu-id="02fda-273">Prefer configuring your authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="02fda-274">内置筛选器实现都只需负责调用授权系统。</span><span class="sxs-lookup"><span data-stu-id="02fda-274">The built-in filter implementation is just responsible for calling the authorization system.</span></span>

<span data-ttu-id="02fda-275">请注意，你不应引发异常中授权筛选器，因为执行任何操作将处理的异常 （异常筛选器不会处理这些）。</span><span class="sxs-lookup"><span data-stu-id="02fda-275">Note that you should not throw exceptions within authorization filters, since nothing will handle the exception (exception filters won't handle them).</span></span> <span data-ttu-id="02fda-276">请改为发出质询，或者查找另一种方法。</span><span class="sxs-lookup"><span data-stu-id="02fda-276">Instead, issue a challenge or find another way.</span></span>

<span data-ttu-id="02fda-277">详细了解[授权](../../security/authorization/index.md)。</span><span class="sxs-lookup"><span data-stu-id="02fda-277">Learn more about [Authorization](../../security/authorization/index.md).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="02fda-278">资源筛选器</span><span class="sxs-lookup"><span data-stu-id="02fda-278">Resource filters</span></span>

<span data-ttu-id="02fda-279">*资源筛选器*实现`IResourceFilter`或`IAsyncResourceFilter`接口，并且其执行包装的筛选器管道的大多数。</span><span class="sxs-lookup"><span data-stu-id="02fda-279">*Resource filters* implement either the `IResourceFilter` or `IAsyncResourceFilter` interface, and their execution wraps most of the filter pipeline.</span></span> <span data-ttu-id="02fda-280">(仅[授权筛选器](#authorization-filters)在它们之前运行。)资源筛选器是工作的特别有用，如果你需要短路大部分做请求。</span><span class="sxs-lookup"><span data-stu-id="02fda-280">(Only [Authorization filters](#authorization-filters) run before them.) Resource filters are especially useful if you need to short-circuit most of the work a request is doing.</span></span> <span data-ttu-id="02fda-281">例如，如果响应已在缓存中缓存的筛选器，就可避免管道的其余部分。</span><span class="sxs-lookup"><span data-stu-id="02fda-281">For example, a caching filter can avoid the rest of the pipeline if the response is already in the cache.</span></span>

<span data-ttu-id="02fda-282">[短短路资源筛选器](#short-circuiting-resource-filter)前面所示是资源筛选器的一个示例。</span><span class="sxs-lookup"><span data-stu-id="02fda-282">The [short circuiting resource filter](#short-circuiting-resource-filter) shown earlier is one example of a resource filter.</span></span> <span data-ttu-id="02fda-283">另一个示例是[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/1.1.1/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs)，这将阻止访问窗体数据模型绑定。</span><span class="sxs-lookup"><span data-stu-id="02fda-283">Another example is [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/1.1.1/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs), which prevents model binding from accessing the form data.</span></span> <span data-ttu-id="02fda-284">它可用于你知道仍然要接收的大型文件上载并想要防止该窗体读入内存的情况。</span><span class="sxs-lookup"><span data-stu-id="02fda-284">It's useful for cases where you know that you're going to receive large file uploads and want to prevent the form from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="02fda-285">操作筛选器</span><span class="sxs-lookup"><span data-stu-id="02fda-285">Action filters</span></span>

<span data-ttu-id="02fda-286">*操作筛选器*实现`IActionFilter`或`IAsyncActionFilter`接口，并且其执行环绕操作方法的执行。</span><span class="sxs-lookup"><span data-stu-id="02fda-286">*Action filters* implement either the `IActionFilter` or `IAsyncActionFilter` interface, and their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="02fda-287">下面是示例操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="02fda-287">Here's a sample action filter:</span></span>

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/SampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="02fda-288">[ActionExecutingContext](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionexecutingcontext)提供的以下属性：</span><span class="sxs-lookup"><span data-stu-id="02fda-288">The [ActionExecutingContext](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionexecutingcontext) provides the following properties:</span></span>

* <span data-ttu-id="02fda-289">`ActionArguments`-可操作操作的输入。</span><span class="sxs-lookup"><span data-stu-id="02fda-289">`ActionArguments` - lets you manipulate the inputs to the action.</span></span>
* <span data-ttu-id="02fda-290">`Controller`-可操作的控制器实例。</span><span class="sxs-lookup"><span data-stu-id="02fda-290">`Controller` - lets you manipulate the controller instance.</span></span> 
* <span data-ttu-id="02fda-291">`Result`-将此值设置会使短路执行的操作方法和后续操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="02fda-291">`Result` - setting this short-circuits execution of the action method and subsequent action filters.</span></span> <span data-ttu-id="02fda-292">引发异常也可阻止执行的操作方法和后续的筛选器，但将被视为失败而不是一个成功结果。</span><span class="sxs-lookup"><span data-stu-id="02fda-292">Throwing an exception also prevents execution of the action method and subsequent filters, but is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="02fda-293">[ActionExecutedContext](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionexecutedcontext)提供`Controller`和`Result`加上以下属性：</span><span class="sxs-lookup"><span data-stu-id="02fda-293">The [ActionExecutedContext](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionexecutedcontext) provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="02fda-294">`Canceled`-将为 true，如果操作执行已短路另一个筛选器。</span><span class="sxs-lookup"><span data-stu-id="02fda-294">`Canceled` - will be true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="02fda-295">`Exception`-将为非 null，如果该操作或后续操作筛选器引发了异常。</span><span class="sxs-lookup"><span data-stu-id="02fda-295">`Exception` - will be non-null if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="02fda-296">设置此属性为 null，有效地 handles 异常，和`Result`就像它已从返回的操作方法通常将执行。</span><span class="sxs-lookup"><span data-stu-id="02fda-296">Setting this property to null effectively 'handles' an exception, and `Result` will be executed as if it were returned from the action method normally.</span></span>

<span data-ttu-id="02fda-297">有关`IAsyncActionFilter`，调用`ActionExecutionDelegate`执行后续操作的任何筛选器和操作方法，从而返回`ActionExecutedContext`。</span><span class="sxs-lookup"><span data-stu-id="02fda-297">For an `IAsyncActionFilter`, a call to the `ActionExecutionDelegate` executes any subsequent action filters and the action method, returning an `ActionExecutedContext`.</span></span> <span data-ttu-id="02fda-298">短路、 分配`ActionExecutingContext.Result`到某些结果实例并且不调用`ActionExecutionDelegate`。</span><span class="sxs-lookup"><span data-stu-id="02fda-298">To short-circuit, assign `ActionExecutingContext.Result` to some result instance and do not call the `ActionExecutionDelegate`.</span></span>

<span data-ttu-id="02fda-299">该框架提供一个抽象`ActionFilterAttribute`可以子类化。</span><span class="sxs-lookup"><span data-stu-id="02fda-299">The framework provides an abstract `ActionFilterAttribute` that you can subclass.</span></span> 

<span data-ttu-id="02fda-300">操作筛选器可用于自动验证模型状态并返回任何错误，如果的状态无效：</span><span class="sxs-lookup"><span data-stu-id="02fda-300">You can use an action filter to automatically validate model state and return any errors if the state is invalid:</span></span>

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/ValidateModelAttribute.cs)]

<span data-ttu-id="02fda-301">`OnActionExecuted`方法运行之后的操作方法和可以查看和处理通过操作的结果`ActionExecutedContext.Result`属性。</span><span class="sxs-lookup"><span data-stu-id="02fda-301">The `OnActionExecuted` method runs after the action method and can see and manipulate the results of the action through the `ActionExecutedContext.Result` property.</span></span> <span data-ttu-id="02fda-302">`ActionExecutedContext.Canceled`将设置为 true，如果操作执行已短路另一个筛选器。</span><span class="sxs-lookup"><span data-stu-id="02fda-302">`ActionExecutedContext.Canceled` will be set to true if the action execution was short-circuited by another filter.</span></span> <span data-ttu-id="02fda-303">`ActionExecutedContext.Exception`将设置为非 null 值的操作或后续操作筛选器的任务引发异常。</span><span class="sxs-lookup"><span data-stu-id="02fda-303">`ActionExecutedContext.Exception` will be set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="02fda-304">设置`ActionExecutedContext.Exception`为 null，有效地 handles 异常，和`ActionExectedContext.Result`随后将如同它通常返回的操作方法的执行。</span><span class="sxs-lookup"><span data-stu-id="02fda-304">Setting `ActionExecutedContext.Exception` to null effectively 'handles' an exception, and `ActionExectedContext.Result` will then be executed as if it were returned from the action method normally.</span></span>

## <a name="exception-filters"></a><span data-ttu-id="02fda-305">异常筛选器</span><span class="sxs-lookup"><span data-stu-id="02fda-305">Exception filters</span></span>

<span data-ttu-id="02fda-306">*异常筛选器*实现`IExceptionFilter`或`IAsyncExceptionFilter`接口。</span><span class="sxs-lookup"><span data-stu-id="02fda-306">*Exception filters* implement either the `IExceptionFilter` or `IAsyncExceptionFilter` interface.</span></span> <span data-ttu-id="02fda-307">它们可以用于实现常见的错误处理的应用的策略。</span><span class="sxs-lookup"><span data-stu-id="02fda-307">They can be used to implement common error handling policies for an app.</span></span> 

<span data-ttu-id="02fda-308">下面的示例异常筛选器将使用自定义开发人员错误视图来显示有关在开发应用程序时，可能发生的异常详细信息：</span><span class="sxs-lookup"><span data-stu-id="02fda-308">The following sample exception filter uses a custom developer error view to display details about exceptions that occur when the application is in development:</span></span>

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/CustomExceptionFilterAttribute.cs?name=snippet_ExceptionFilter&highlight=1,14)]

<span data-ttu-id="02fda-309">异常筛选器不具有两个事件 （对于之前和之后）-它们仅实现`OnException`(或`OnExceptionAsync`)。</span><span class="sxs-lookup"><span data-stu-id="02fda-309">Exception filters do not have two events (for before and after) - they only implement `OnException` (or `OnExceptionAsync`).</span></span> 

<span data-ttu-id="02fda-310">异常筛选器处理控制器创建中发生的未经处理的异常[模型绑定](../models/model-binding.md)，操作筛选器或操作方法。</span><span class="sxs-lookup"><span data-stu-id="02fda-310">Exception filters handle unhandled exceptions that occur in controller creation, [model binding](../models/model-binding.md), action filters, or action methods.</span></span> <span data-ttu-id="02fda-311">它们不会捕获资源筛选器、 结果筛选器或 MVC 结果执行中出现的异常。</span><span class="sxs-lookup"><span data-stu-id="02fda-311">They won't catch exceptions that occur in Resource filters, Result filters, or MVC Result execution.</span></span>

<span data-ttu-id="02fda-312">若要处理的异常，设置`ExceptionContext.ExceptionHandled`属性为 true，或者编写响应。</span><span class="sxs-lookup"><span data-stu-id="02fda-312">To handle an exception, set the `ExceptionContext.ExceptionHandled` property to true or write a response.</span></span> <span data-ttu-id="02fda-313">这将停止异常的传播。</span><span class="sxs-lookup"><span data-stu-id="02fda-313">This stops propagation of the exception.</span></span> <span data-ttu-id="02fda-314">请注意异常筛选器无法打开到"成功"异常。</span><span class="sxs-lookup"><span data-stu-id="02fda-314">Note that an Exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="02fda-315">操作筛选器可以执行该操作。</span><span class="sxs-lookup"><span data-stu-id="02fda-315">Only an Action filter can do that.</span></span>

> [!NOTE]
> <span data-ttu-id="02fda-316">在 ASP.NET 1.1 版中，响应将不发送如果你设置`ExceptionHandled`为 true**和**编写响应。</span><span class="sxs-lookup"><span data-stu-id="02fda-316">In ASP.NET 1.1, the response is not sent if you set `ExceptionHandled` to true **and** write a response.</span></span> <span data-ttu-id="02fda-317">在这种情况下，ASP.NET Core 1.0 未发送响应，和 ASP.NET Core 1.1.2 将返回到 1.0 的行为。</span><span class="sxs-lookup"><span data-stu-id="02fda-317">In that scenario, ASP.NET Core 1.0 does send the response, and ASP.NET Core 1.1.2 will return to the 1.0 behavior.</span></span> <span data-ttu-id="02fda-318">有关详细信息，请参阅[发出 #5594](https://github.com/aspnet/Mvc/issues/5594) GitHub 存储库中。</span><span class="sxs-lookup"><span data-stu-id="02fda-318">For more information, see [issue #5594](https://github.com/aspnet/Mvc/issues/5594) in the GitHub repository.</span></span> 

<span data-ttu-id="02fda-319">异常筛选器非常适用于捕获 MVC 操作内出现的异常，但它们不是灵活性不如错误处理中间件。</span><span class="sxs-lookup"><span data-stu-id="02fda-319">Exception filters are good for trapping exceptions that occur within MVC actions, but they're not as flexible as error handling middleware.</span></span> <span data-ttu-id="02fda-320">首选中间件一般情况下，并使用筛选器仅需要在其中执行错误处理*以不同方式*基于的 MVC 操作已被选。</span><span class="sxs-lookup"><span data-stu-id="02fda-320">Prefer middleware for the general case, and use filters only where you need to do error handling *differently* based on which MVC action was chosen.</span></span> <span data-ttu-id="02fda-321">例如，你的应用程序可能具有两个 API 终结点和视图/HTML 的操作方法。</span><span class="sxs-lookup"><span data-stu-id="02fda-321">For example, your app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="02fda-322">基于视图的操作可能返回为 HTML 错误页时，API 终结点无法返回为 JSON 的错误信息。</span><span class="sxs-lookup"><span data-stu-id="02fda-322">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

<span data-ttu-id="02fda-323">该框架提供一个抽象`ExceptionFilterAttribute`可以子类化。</span><span class="sxs-lookup"><span data-stu-id="02fda-323">The framework provides an abstract `ExceptionFilterAttribute` that you can subclass.</span></span> 

## <a name="result-filters"></a><span data-ttu-id="02fda-324">结果筛选器</span><span class="sxs-lookup"><span data-stu-id="02fda-324">Result filters</span></span>

<span data-ttu-id="02fda-325">*导致筛选器*实现`IResultFilter`或`IAsyncResultFilter`接口，并且其执行环绕操作结果的执行。</span><span class="sxs-lookup"><span data-stu-id="02fda-325">*Result filters* implement either the `IResultFilter` or `IAsyncResultFilter` interface, and their execution surrounds the execution of action results.</span></span> 

<span data-ttu-id="02fda-326">下面是结果筛选器添加一个 HTTP 标头的示例。</span><span class="sxs-lookup"><span data-stu-id="02fda-326">Here's an example of a Result filter that adds an HTTP header.</span></span>

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="02fda-327">正在执行的结果的类型取决于问题的操作。</span><span class="sxs-lookup"><span data-stu-id="02fda-327">The kind of result being executed depends on the action in question.</span></span> <span data-ttu-id="02fda-328">返回视图的 MVC 操作将包括所有 razor 作为的一部分处理`ViewResult`正在执行。</span><span class="sxs-lookup"><span data-stu-id="02fda-328">An MVC action returning a view would include all razor processing as part of the `ViewResult` being executed.</span></span> <span data-ttu-id="02fda-329">API 方法可能会执行某些序列化的结果的执行过程。</span><span class="sxs-lookup"><span data-stu-id="02fda-329">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="02fda-330">详细了解[操作结果](actions.md)</span><span class="sxs-lookup"><span data-stu-id="02fda-330">Learn more about [action results](actions.md)</span></span>

<span data-ttu-id="02fda-331">仅为成功结果的执行结果筛选器时的操作或操作筛选器生成操作结果。</span><span class="sxs-lookup"><span data-stu-id="02fda-331">Result filters are only executed for successful results - when the action or action filters produce an action result.</span></span> <span data-ttu-id="02fda-332">异常筛选器处理的异常时，将不会执行结果筛选器。</span><span class="sxs-lookup"><span data-stu-id="02fda-332">Result filters are not executed when exception filters handle an exception.</span></span>

<span data-ttu-id="02fda-333">`OnResultExecuting`方法可以通过设置短路执行的操作结果和后续结果筛选器`ResultExecutingContext.Cancel`为 true。</span><span class="sxs-lookup"><span data-stu-id="02fda-333">The `OnResultExecuting` method can short-circuit execution of the action result and subsequent result filters by setting `ResultExecutingContext.Cancel` to true.</span></span> <span data-ttu-id="02fda-334">短路以避免生成了空响应时，通常应编写到响应对象。</span><span class="sxs-lookup"><span data-stu-id="02fda-334">You should generally write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="02fda-335">引发异常也将阻止执行的操作结果和后续的筛选器，但将被视为失败而不是一个成功结果。</span><span class="sxs-lookup"><span data-stu-id="02fda-335">Throwing an exception will also prevent execution of the action result and subsequent filters, but will be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="02fda-336">当`OnResultExecuted`方法运行时，响应可能已发送到客户端，而且不能进一步更改，（除非引发了异常）。</span><span class="sxs-lookup"><span data-stu-id="02fda-336">When the `OnResultExecuted` method runs, the response has likely been sent to the client and cannot be changed further (unless an exception was thrown).</span></span> <span data-ttu-id="02fda-337">`ResultExecutedContext.Canceled`将设置为 true，如果操作结果执行已短路另一个筛选器。</span><span class="sxs-lookup"><span data-stu-id="02fda-337">`ResultExecutedContext.Canceled` will be set to true if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="02fda-338">`ResultExecutedContext.Exception`将设置为非 null 值的操作结果或后续结果筛选器的任务引发异常。</span><span class="sxs-lookup"><span data-stu-id="02fda-338">`ResultExecutedContext.Exception` will be set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="02fda-339">设置`Exception`到 null 有效地处理的异常和可防止在管道中更高版本中被重新引发由 MVC 异常。</span><span class="sxs-lookup"><span data-stu-id="02fda-339">Setting `Exception` to null effectively 'handles' an exception and prevents the exception from being rethrown by MVC later in the pipeline.</span></span> <span data-ttu-id="02fda-340">当处理结果筛选器中的出现异常时，你可能不能将任何数据写入到响应。</span><span class="sxs-lookup"><span data-stu-id="02fda-340">When you're handling an exception in a result filter, you might not be able to write any data to the response.</span></span> <span data-ttu-id="02fda-341">如果通过其执行的过程引发的操作结果和标头已刷新到客户端，则没有可靠的机制，可用于发送为失败代码。</span><span class="sxs-lookup"><span data-stu-id="02fda-341">If the action result throws partway through its execution, and the headers have already been flushed to the client, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="02fda-342">有关`IAsyncResultFilter`调用`await next()`上`ResultExecutionDelegate`执行其余的结果的任何筛选器和操作结果。</span><span class="sxs-lookup"><span data-stu-id="02fda-342">For an `IAsyncResultFilter` a call to `await next()` on the `ResultExecutionDelegate` executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="02fda-343">短路、 设置`ResultExecutingContext.Cancel`到 true 并且不调用`ResultExectionDelegate`。</span><span class="sxs-lookup"><span data-stu-id="02fda-343">To short-circuit, set `ResultExecutingContext.Cancel` to true and do not call the `ResultExectionDelegate`.</span></span>

<span data-ttu-id="02fda-344">该框架提供一个抽象`ResultFilterAttribute`可以子类化。</span><span class="sxs-lookup"><span data-stu-id="02fda-344">The framework provides an abstract `ResultFilterAttribute` that you can subclass.</span></span> <span data-ttu-id="02fda-345">[AddHeaderAttribute](#add-header-attribute)类前面所示是结果筛选器特性的一个示例。</span><span class="sxs-lookup"><span data-stu-id="02fda-345">The [AddHeaderAttribute](#add-header-attribute) class shown earlier is an example of a result filter attribute.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="02fda-346">在筛选器管道中使用中间件</span><span class="sxs-lookup"><span data-stu-id="02fda-346">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="02fda-347">资源筛选器工作方式与类似[中间件](../../fundamentals/middleware.md)在于它们括起的所有内容的管道在更高版本附带的执行。</span><span class="sxs-lookup"><span data-stu-id="02fda-347">Resource filters work like [middleware](../../fundamentals/middleware.md) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="02fda-348">但筛选器不同于中间件，因为它们是 MVC，这意味着他们有权 MVC 上下文和构造的一部分。</span><span class="sxs-lookup"><span data-stu-id="02fda-348">But filters differ from middleware in that they are part of MVC, which means that they have access to MVC context and constructs.</span></span>

<span data-ttu-id="02fda-349">在 ASP.NET 核心 1.1，可以在筛选器管道中使用中间件。</span><span class="sxs-lookup"><span data-stu-id="02fda-349">In ASP.NET Core 1.1, you can use middleware in the filter pipeline.</span></span> <span data-ttu-id="02fda-350">你可能想要执行此操作，如果你有需要访问 MVC 路由数据，或其中一个控制器或操作，应只为某些运行的中间件组件。</span><span class="sxs-lookup"><span data-stu-id="02fda-350">You might want to do that if you have a middleware component that needs access to MVC route data, or one that should run only for certain controllers or actions.</span></span>

<span data-ttu-id="02fda-351">若要用作筛选器的中间件，创建一个与`Configure`方法，它指定你想要将注入到筛选器管道的中间件。</span><span class="sxs-lookup"><span data-stu-id="02fda-351">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware that you want to inject into the filter pipeline.</span></span> <span data-ttu-id="02fda-352">下面是用于建立请求的当前区域性的本地化中间件示例：</span><span class="sxs-lookup"><span data-stu-id="02fda-352">Here's an example that uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,21)]

<span data-ttu-id="02fda-353">然后，可以使用`MiddlewareFilterAttribute`运行的中间件的所选的控制器或操作或全局范围内：</span><span class="sxs-lookup"><span data-stu-id="02fda-353">You can then use the `MiddlewareFilterAttribute` to run the middleware for a selected controller or action or globally:</span></span>

[!code-csharp[Main](./filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="02fda-354">在资源筛选器，在模型绑定之前和之后的管道的其余部分相同的筛选器管道阶段运行的中间件筛选器。</span><span class="sxs-lookup"><span data-stu-id="02fda-354">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="02fda-355">下一步操作</span><span class="sxs-lookup"><span data-stu-id="02fda-355">Next actions</span></span>

<span data-ttu-id="02fda-356">尝试使用筛选器，[下载、 测试和修改的示例](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)。</span><span class="sxs-lookup"><span data-stu-id="02fda-356">To experiment with filters, [download, test and modify the sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>
