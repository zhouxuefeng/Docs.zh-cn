---
title: "ASP.NET 核心 MVC 的概述"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 89af38d1-52e0-4db7-b791-dbce909b0714
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/overview
ms.openlocfilehash: 67394b066c18a149a97b957d6478ba8301ea8147
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="overview-of-aspnet-core-mvc"></a><span data-ttu-id="fc1dd-103">ASP.NET 核心 MVC 的概述</span><span class="sxs-lookup"><span data-stu-id="fc1dd-103">Overview of ASP.NET Core MVC</span></span>

<span data-ttu-id="fc1dd-104">通过[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="fc1dd-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="fc1dd-105">ASP.NET 核心 MVC 是一个丰富的框架，用于生成 web 应用和 Api 使用模型-视图-控制器设计模式。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-105">ASP.NET Core MVC is a rich framework for building web apps and APIs using the Model-View-Controller design pattern.</span></span>

## <a name="what-is-the-mvc-pattern"></a><span data-ttu-id="fc1dd-106">什么是 MVC 模式？</span><span class="sxs-lookup"><span data-stu-id="fc1dd-106">What is the MVC pattern?</span></span>

<span data-ttu-id="fc1dd-107">模型-视图-控制器 (MVC) 体系结构模式将应用程序分成三个组件的主要组： 模型、 视图和控制器。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-107">The Model-View-Controller (MVC) architectural pattern separates an application into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="fc1dd-108">此模式有助于达到[关注点分离](http://deviq.com/separation-of-concerns/)。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-108">This pattern helps to achieve [separation of concerns](http://deviq.com/separation-of-concerns/).</span></span> <span data-ttu-id="fc1dd-109">使用此模式，用户请求路由到负责使用模型来执行用户操作和/或检索查询结果的控制器。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-109">Using this pattern, user requests are routed to a Controller which is responsible for working with the Model to perform user actions and/or retrieve results of queries.</span></span> <span data-ttu-id="fc1dd-110">控制器选择视图以显示给用户，然后将其提供使用它要求任何模型数据。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-110">The Controller chooses the View to display to the user, and provides it with any Model data it requires.</span></span>

<span data-ttu-id="fc1dd-111">下图显示了三个主要组件以及哪些引用其他：</span><span class="sxs-lookup"><span data-stu-id="fc1dd-111">The following diagram shows the three main components and which ones reference the others:</span></span>

![MVC 模式](overview/_static/mvc.png)

<span data-ttu-id="fc1dd-113">此说明职责可帮助你缩放应用程序的复杂性，因为它是更轻松地代码、 调试和测试拥有的内容 （模型、 视图或控制器） 的单个作业 (并遵循[单个责任原则](http://deviq.com/single-responsibility-principle/)).</span><span class="sxs-lookup"><span data-stu-id="fc1dd-113">This delineation of responsibilities helps you scale the application in terms of complexity because it’s easier to code, debug, and test something (model, view, or controller) that has a single job (and follows the [Single Responsibility Principle](http://deviq.com/single-responsibility-principle/)).</span></span> <span data-ttu-id="fc1dd-114">它会为更新、 测试和调试代码有依赖项分布在两个或多个上述三个方面更加困难。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-114">It's more difficult to update, test, and debug code that has dependencies spread across two or more of these three areas.</span></span> <span data-ttu-id="fc1dd-115">例如，用户界面逻辑往往需要更改频率高于业务逻辑。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-115">For example, user interface logic tends to change more frequently than business logic.</span></span> <span data-ttu-id="fc1dd-116">如果演示文稿代码和业务逻辑必须合并在单个对象，你必须修改包含业务逻辑，每次更改用户界面的对象。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-116">If presentation code and business logic are combined in a single object, you have to modify an object containing business logic every time you change the user interface.</span></span> <span data-ttu-id="fc1dd-117">这是可能会将错误引入并需要重新测试的所有业务逻辑在每个最小用户界面更改后。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-117">This is likely to introduce errors and require the retesting of all business logic after every minimal user interface change.</span></span>

> [!NOTE]
> <span data-ttu-id="fc1dd-118">视图和控制器取决于模型。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-118">Both the view and the controller depend on the model.</span></span> <span data-ttu-id="fc1dd-119">但是，该模型取决于视图和控制器都不。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-119">However, the model depends on neither the view nor the controller.</span></span> <span data-ttu-id="fc1dd-120">这是分离的主要优点之一。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-120">This is one of the key benefits of the separation.</span></span> <span data-ttu-id="fc1dd-121">这种分离使模型来生成和测试独立于可视化表示形式。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-121">This separation allows the model to be built and tested independent of the visual presentation.</span></span>

### <a name="model-responsibilities"></a><span data-ttu-id="fc1dd-122">模型职责</span><span class="sxs-lookup"><span data-stu-id="fc1dd-122">Model Responsibilities</span></span>

<span data-ttu-id="fc1dd-123">一个 MVC 应用程序中的模型表示的应用程序和任何业务逻辑或由它执行的操作的状态。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-123">The Model in an MVC application represents the state of the application and any business logic or operations that should be performed by it.</span></span> <span data-ttu-id="fc1dd-124">应在模型中，以及用于永久保存应用程序的状态的任何实现逻辑封装业务逻辑。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-124">Business logic should be encapsulated in the model, along with any implementation logic for persisting the state of the application.</span></span> <span data-ttu-id="fc1dd-125">强类型化视图通常将使用专门设计为包含数据的视图模型类型在该视图; 上显示控制器将创建并填充这些 ViewModel 实例从模型。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-125">Strongly-typed views will typically use ViewModel types specifically designed to contain the data to display on that view; the controller will create and populate these ViewModel instances from the model.</span></span>

> [!NOTE]
> <span data-ttu-id="fc1dd-126">有多种方法来组织中使用 MVC 体系结构模式的应用程序的模型。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-126">There are many ways to organize the model in an app that uses the MVC architectural pattern.</span></span> <span data-ttu-id="fc1dd-127">深入了解某些[不同种类的模型类型](http://deviq.com/kinds-of-models/)。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-127">Learn more about some [different kinds of model types](http://deviq.com/kinds-of-models/).</span></span>

### <a name="view-responsibilities"></a><span data-ttu-id="fc1dd-128">视图职责</span><span class="sxs-lookup"><span data-stu-id="fc1dd-128">View Responsibilities</span></span>

<span data-ttu-id="fc1dd-129">视图是负责通过用户界面提供内容。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-129">Views are responsible for presenting content through the user interface.</span></span> <span data-ttu-id="fc1dd-130">它们使用[Razor 视图引擎](#razor-view-engine)若要将.NET 代码嵌入在 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-130">They use the [Razor view engine](#razor-view-engine) to embed .NET code in HTML markup.</span></span> <span data-ttu-id="fc1dd-131">应在视图内，最小逻辑，并且它们中的任何逻辑应与提供内容。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-131">There should be minimal logic within views, and any logic in them should relate to presenting content.</span></span> <span data-ttu-id="fc1dd-132">如果你发现需要执行大量的逻辑视图中为了显示复杂模型中的数据文件，请考虑使用[视图组件](views/view-components.md)，ViewModel，或查看模板，以简化视图。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-132">If you find the need to perform a great deal of logic in view files in order to display data from a complex model, consider using a [View Component](views/view-components.md), ViewModel, or view template to simplify the view.</span></span>

### <a name="controller-responsibilities"></a><span data-ttu-id="fc1dd-133">控制器职责</span><span class="sxs-lookup"><span data-stu-id="fc1dd-133">Controller Responsibilities</span></span>

<span data-ttu-id="fc1dd-134">控制器在处理用户交互，使用的模型，并最终选择要呈现的视图的组件。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-134">Controllers are the components that handle user interaction, work with the model, and ultimately select a view to render.</span></span> <span data-ttu-id="fc1dd-135">在 MVC 应用程序，该视图仅显示信息;控制器处理，并响应用户输入和交互。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-135">In an MVC application, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="fc1dd-136">在 MVC 模式中，该控制器已是初始入口点，并且负责选择要使用哪个模型类型和要呈现的视图 (因此它控制的其名称中的应用程序如何响应的给定的请求)。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-136">In the MVC pattern, the controller is the initial entry point, and is responsible for selecting which model types to work with and which view to render (hence its name - it controls how the app responds to a given request).</span></span>

> [!NOTE]
> <span data-ttu-id="fc1dd-137">控制器应不过于复杂，过多的职责。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-137">Controllers should not be overly complicated by too many responsibilities.</span></span> <span data-ttu-id="fc1dd-138">若要阻止控制器逻辑变得过于复杂，使用[单个责任原则](http://deviq.com/single-responsibility-principle/)控制器出来放入域模型中的推送的业务逻辑。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-138">To keep controller logic from becoming overly complex, use the [Single Responsibility Principle](http://deviq.com/single-responsibility-principle/) to push business logic out of the controller and into the domain model.</span></span>

>[!TIP]
> <span data-ttu-id="fc1dd-139">如果你发现你的控制器操作频繁地执行操作相同种类的操作，则可以按照[切勿重复原则](http://deviq.com/don-t-repeat-yourself/)通过移动到这些常见操作[筛选器](#filters)。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-139">If you find that your controller actions frequently perform the same kinds of actions, you can follow the [Don't Repeat Yourself principle](http://deviq.com/don-t-repeat-yourself/) by moving these common actions into [filters](#filters).</span></span>

## <a name="what-is-aspnet-core-mvc"></a><span data-ttu-id="fc1dd-140">ASP.NET 核心 MVC 是什么</span><span class="sxs-lookup"><span data-stu-id="fc1dd-140">What is ASP.NET Core MVC</span></span>

<span data-ttu-id="fc1dd-141">ASP.NET 核心 MVC 框架是轻量、 打开的源，用于 ASP.NET Core 优化的高度可测试的演示文稿 framework。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-141">The ASP.NET Core MVC framework is a lightweight, open source, highly testable presentation framework optimized for use with ASP.NET Core.</span></span>

<span data-ttu-id="fc1dd-142">ASP.NET 核心 MVC 提供了一种基于模式的方法来构建实现完全分离关注点的动态网站。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-142">ASP.NET Core MVC provides a patterns-based way to build dynamic websites that enables a clean separation of concerns.</span></span> <span data-ttu-id="fc1dd-143">它让你完全控制标记，支持 TDD 友好的开发，并且使用最新的 web 标准。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-143">It gives you full control over markup, supports TDD-friendly development and uses the latest web standards.</span></span>

## <a name="features"></a><span data-ttu-id="fc1dd-144">功能</span><span class="sxs-lookup"><span data-stu-id="fc1dd-144">Features</span></span>

<span data-ttu-id="fc1dd-145">ASP.NET 核心 MVC 包括以下部分：</span><span class="sxs-lookup"><span data-stu-id="fc1dd-145">ASP.NET Core MVC includes the following:</span></span>

* [<span data-ttu-id="fc1dd-146">路由</span><span class="sxs-lookup"><span data-stu-id="fc1dd-146">Routing</span></span>](#routing)
* [<span data-ttu-id="fc1dd-147">模型绑定</span><span class="sxs-lookup"><span data-stu-id="fc1dd-147">Model binding</span></span>](#model-binding)
* [<span data-ttu-id="fc1dd-148">模型验证</span><span class="sxs-lookup"><span data-stu-id="fc1dd-148">Model validation</span></span>](#model-validation)
* [<span data-ttu-id="fc1dd-149">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="fc1dd-149">Dependency injection</span></span>](../fundamentals/dependency-injection.md)
* [<span data-ttu-id="fc1dd-150">筛选器</span><span class="sxs-lookup"><span data-stu-id="fc1dd-150">Filters</span></span>](#filters)
* [<span data-ttu-id="fc1dd-151">区域</span><span class="sxs-lookup"><span data-stu-id="fc1dd-151">Areas</span></span>](#areas)
* [<span data-ttu-id="fc1dd-152">Web Api</span><span class="sxs-lookup"><span data-stu-id="fc1dd-152">Web APIs</span></span>](#web-apis)
* [<span data-ttu-id="fc1dd-153">可测试性</span><span class="sxs-lookup"><span data-stu-id="fc1dd-153">Testability</span></span>](#testability)
* [<span data-ttu-id="fc1dd-154">Razor 视图引擎</span><span class="sxs-lookup"><span data-stu-id="fc1dd-154">Razor view engine</span></span>](#razor-view-engine)
* [<span data-ttu-id="fc1dd-155">强类型化的视图</span><span class="sxs-lookup"><span data-stu-id="fc1dd-155">Strongly typed views</span></span>](#strongly-typed-views)
* [<span data-ttu-id="fc1dd-156">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="fc1dd-156">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="fc1dd-157">查看组件</span><span class="sxs-lookup"><span data-stu-id="fc1dd-157">View Components</span></span>](#view-components)

### <a name="routing"></a><span data-ttu-id="fc1dd-158">路由</span><span class="sxs-lookup"><span data-stu-id="fc1dd-158">Routing</span></span>

<span data-ttu-id="fc1dd-159">ASP.NET 核心 MVC 构建的顶部[ASP.NET Core 路由](../fundamentals/routing.md)，一个功能强大的 URL 映射组件，用于你构建的应用程序具有易于理解和搜索的 Url。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-159">ASP.NET Core MVC is built on top of [ASP.NET Core's routing](../fundamentals/routing.md), a powerful URL-mapping component that lets you build applications that have comprehensible and searchable URLs.</span></span> <span data-ttu-id="fc1dd-160">这使你可以定义应用程序的 URL 命名模式适用于搜索引擎优化 (SEO) 和链接生成，而不考虑你的 web 服务器上的文件的组织方式。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-160">This enables you to define your application's URL naming patterns that work well for search engine optimization (SEO) and for link generation, without regard for how the files on your web server are organized.</span></span> <span data-ttu-id="fc1dd-161">你可以定义路由使用一种方便的路由模板语法支持路由值约束、 默认值和可选值。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-161">You can define your routes using a convenient route template syntax that supports route value constraints, defaults and optional values.</span></span>

<span data-ttu-id="fc1dd-162">*基于约定的路由*可以全局定义的 URL 格式的启用你的应用程序接受并的每个格式的方式将映射到特定的操作方法在给定的控制器。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-162">*Convention-based routing* enables you to globally define the URL formats that your application accepts and how each of those formats maps to a specific action method on given controller.</span></span> <span data-ttu-id="fc1dd-163">当收到传入的请求时，路由引擎将分析该 URL 和匹配到一种定义的 URL 格式，然后调用关联的控制器操作方法。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-163">When an incoming request is received, the routing engine parses the URL and matches it to one of the defined URL formats, and then calls the associated controller's action method.</span></span>

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="fc1dd-164">*属性路由*使您能够指定的修饰控制器和操作具有定义应用程序的路由的属性的路由信息。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-164">*Attribute routing* enables you to specify routing information by decorating your controllers and actions with attributes that define your application's routes.</span></span> <span data-ttu-id="fc1dd-165">这意味着旁边的控制器和操作与它们关联放置的 route 定义。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-165">This means that your route definitions are placed next to the controller and action with which they're associated.</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [1, 4]}} -->

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
  [HttpGet("{id}")]
  public IActionResult GetProduct(int id)
  {
    ...
  }
}
```

### <a name="model-binding"></a><span data-ttu-id="fc1dd-166">模型绑定</span><span class="sxs-lookup"><span data-stu-id="fc1dd-166">Model binding</span></span>

<span data-ttu-id="fc1dd-167">ASP.NET 核心 MVC[模型绑定](models/model-binding.md)将 （窗体值、 路由数据、 查询字符串参数、 HTTP 标头） 的客户端请求数据转换为控制器可以处理的对象。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-167">ASP.NET Core MVC [model binding](models/model-binding.md) converts client request data  (form values, route data, query string parameters, HTTP headers) into objects that the controller can handle.</span></span> <span data-ttu-id="fc1dd-168">因此，控制器逻辑无需执行的工作成果找出传入的请求数据;它只需具有作为其操作方法的参数的数据。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-168">As a result, your controller logic doesn't have to do the work of figuring out the incoming request data; it simply has the data as parameters to its action methods.</span></span>

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
   ```

### <a name="model-validation"></a><span data-ttu-id="fc1dd-169">模型验证</span><span class="sxs-lookup"><span data-stu-id="fc1dd-169">Model validation</span></span>

<span data-ttu-id="fc1dd-170">ASP.NET 核心 MVC 支持[验证](models/validation.md)的修饰模型对象具有数据批注验证属性。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-170">ASP.NET Core MVC supports [validation](models/validation.md) by decorating your model object with data annotation validation attributes.</span></span> <span data-ttu-id="fc1dd-171">验证特性之前值发布到服务器，客户端检查以及调用之前的控制器操作在服务器上。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-171">The validation attributes are checked on the client side before values are posted to the server, as well as on the server before the controller action is called.</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [4, 5, 8, 9]}} -->

```csharp
using System.ComponentModel.DataAnnotations;
public class LoginViewModel
{
    [Required]
    [EmailAddress]
    public string Email { get; set; }

    [Required]
    [DataType(DataType.Password)]
    public string Password { get; set; }

    [Display(Name = "Remember me?")]
    public bool RememberMe { get; set; }
}
```

<span data-ttu-id="fc1dd-172">进行的控制器操作：</span><span class="sxs-lookup"><span data-stu-id="fc1dd-172">A controller action:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [3]}} -->

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null)
{
    if (ModelState.IsValid)
    {
      // work with the model
    }
    // If we got this far, something failed, redisplay form
    return View(model);
}
```

<span data-ttu-id="fc1dd-173">框架将处理验证请求数据在客户端和服务器上。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-173">The framework will handle validating request data both on the client and on the server.</span></span> <span data-ttu-id="fc1dd-174">模型类型上指定的验证逻辑添加到以非介入式批注的形式呈现的视图和浏览器中使用采用强制执行[jQuery 验证](https://jqueryvalidation.org/)。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-174">Validation logic specified on model types is added to the rendered views as unobtrusive annotations and is enforced in the browser with [jQuery Validation](https://jqueryvalidation.org/).</span></span>

### <a name="dependency-injection"></a><span data-ttu-id="fc1dd-175">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="fc1dd-175">Dependency injection</span></span>

<span data-ttu-id="fc1dd-176">ASP.NET Core 提供的内置支持[依赖关系注入 (DI)](../fundamentals/dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-176">ASP.NET Core has built-in support for [dependency injection (DI)](../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="fc1dd-177">ASP.NET 核心 mvc[控制器](controllers/dependency-injection.md)请求所需通过其构造函数，从而使它们可以按照服务[显式依赖关系原则](http://deviq.com/explicit-dependencies-principle/)。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-177">In ASP.NET Core MVC, [controllers](controllers/dependency-injection.md) can request needed services through their constructors, allowing them to follow the [Explicit Dependencies Principle](http://deviq.com/explicit-dependencies-principle/).</span></span>

<span data-ttu-id="fc1dd-178">你的应用程序还可以使用[依赖关系注入视图中文件](views/dependency-injection.md)，使用`@inject`指令：</span><span class="sxs-lookup"><span data-stu-id="fc1dd-178">Your app can also use [dependency injection in view files](views/dependency-injection.md), using the `@inject` directive:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [1]}} -->

```html
@inject SomeService ServiceName
<!DOCTYPE html>
<html>
<head>
  <title>@ServiceName.GetTitle</title>
</head>
<body>
  <h1>@ServiceName.GetTitle</h1>
</body>
</html>
```

### <a name="filters"></a><span data-ttu-id="fc1dd-179">筛选器</span><span class="sxs-lookup"><span data-stu-id="fc1dd-179">Filters</span></span>

<span data-ttu-id="fc1dd-180">[筛选器](controllers/filters.md)帮助开发人员封装跨领域问题，如异常处理或授权。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-180">[Filters](controllers/filters.md) help developers encapsulate cross-cutting concerns, like exception handling or authorization.</span></span> <span data-ttu-id="fc1dd-181">筛选器启用操作方法的运行自定义前期和后期处理逻辑，并可以配置为在某些点在给定的请求执行管道中运行。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-181">Filters enable running custom pre- and post-processing logic for action methods, and can be configured to run at certain points within the execution pipeline for a given request.</span></span> <span data-ttu-id="fc1dd-182">筛选器可应用于控制器或作为属性的操作 （或可以全局运行）。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-182">Filters can be applied to controllers or actions as attributes (or can be run globally).</span></span> <span data-ttu-id="fc1dd-183">多个筛选器 (如`Authorize`) 包含在 framework。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-183">Several filters (such as `Authorize`) are included in the framework.</span></span>


```csharp
[Authorize]
   public class AccountController : Controller
   {

```

### <a name="areas"></a><span data-ttu-id="fc1dd-184">区域</span><span class="sxs-lookup"><span data-stu-id="fc1dd-184">Areas</span></span>

<span data-ttu-id="fc1dd-185">[区域](controllers/areas.md)提供一种方法进行分区到较小功能分组中的大型 ASP.NET 核心 MVC Web 应用。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-185">[Areas](controllers/areas.md) provide a way to partition a large ASP.NET Core MVC Web app into smaller functional groupings.</span></span> <span data-ttu-id="fc1dd-186">一个区域实际上是在应用程序内的 MVC 结构。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-186">An area is effectively an MVC structure inside an application.</span></span> <span data-ttu-id="fc1dd-187">在 MVC 项目中，逻辑组件，如模型、 控制器和视图保留在不同的文件夹和 MVC 使用命名约定来创建这些组件之间的关系。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-187">In an MVC project, logical components like Model, Controller, and View are kept in different folders, and MVC uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="fc1dd-188">对于大型应用，它可能会更有利分区成单独的功能的高级别区域的应用程序。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-188">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="fc1dd-189">例如，电子商务应用程序与多个业务单位，例如签出、 计费和搜索等等。这些部门的每个具有其自己的逻辑组件视图、 控制器和模型。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-189">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search etc. Each of these units have their own logical component views, controllers, and models.</span></span>

### <a name="web-apis"></a><span data-ttu-id="fc1dd-190">Web Api</span><span class="sxs-lookup"><span data-stu-id="fc1dd-190">Web APIs</span></span>

<span data-ttu-id="fc1dd-191">除了正在很好的平台，用于构建网站，ASP.NET 核心 MVC 还具有很好的生成 Web Api 的支持。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-191">In addition to being a great platform for building web sites, ASP.NET Core MVC has great support for building Web APIs.</span></span> <span data-ttu-id="fc1dd-192">你可以构建可以覆盖广泛的客户端包括浏览器和移动设备的服务。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-192">You can build services that can reach a broad range of clients including browsers and mobile devices.</span></span>

<span data-ttu-id="fc1dd-193">Framework 包括内置的支持通过 HTTP 内容协商支持[格式设置数据](models/formatting.md)作为 JSON 或 XML。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-193">The framework includes support for HTTP content-negotiation with built-in support for [formatting data](models/formatting.md) as JSON or XML.</span></span> <span data-ttu-id="fc1dd-194">编写[自定义格式化程序](advanced/custom-formatters.md)添加您自己的格式的支持。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-194">Write [custom formatters](advanced/custom-formatters.md) to add support for your own formats.</span></span>

<span data-ttu-id="fc1dd-195">使用链接生成方法来启用对超媒体的支持。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-195">Use link generation to enable support for hypermedia.</span></span> <span data-ttu-id="fc1dd-196">轻松启用对支持[跨域资源共享 (CORS)](http://www.w3.org/TR/cors/) ，以便可以跨多个 Web 应用程序共享你的 Web Api。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-196">Easily enable support for [cross-origin resource sharing (CORS)](http://www.w3.org/TR/cors/) so that your Web APIs can be shared across multiple Web applications.</span></span>

### <a name="testability"></a><span data-ttu-id="fc1dd-197">可测试性</span><span class="sxs-lookup"><span data-stu-id="fc1dd-197">Testability</span></span>

<span data-ttu-id="fc1dd-198">接口和依赖关系注入框架的使用使其适合对单元测试，和框架包括功能 （如 TestHost 和 InMemory 实体框架提供程序），使[集成测试](../testing/integration-testing.md)快速便捷以及。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-198">The framework's use of interfaces and dependency injection make it well-suited to unit testing, and the framework includes features (like a TestHost and InMemory provider for Entity Framework) that make [integration testing](../testing/integration-testing.md) quick and easy as well.</span></span> <span data-ttu-id="fc1dd-199">详细了解[测试控制器逻辑](controllers/testing.md)。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-199">Learn more about [testing controller logic](controllers/testing.md).</span></span>

### <a name="razor-view-engine"></a><span data-ttu-id="fc1dd-200">Razor 视图引擎</span><span class="sxs-lookup"><span data-stu-id="fc1dd-200">Razor view engine</span></span>

<span data-ttu-id="fc1dd-201">[ASP.NET 核心 MVC 视图](views/overview.md)使用[Razor 视图引擎](views/razor.md)来呈现视图。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-201">[ASP.NET Core MVC views](views/overview.md) use the [Razor view engine](views/razor.md) to render views.</span></span> <span data-ttu-id="fc1dd-202">Razor 是用于定义使用嵌入的 C# 代码的视图的 compact、 表现力和流畅的模板标记语言。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-202">Razor is a compact, expressive and fluid template markup language for defining views using embedded C# code.</span></span> <span data-ttu-id="fc1dd-203">Razor 用于动态生成服务器上的 web 内容。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-203">Razor is used to dynamically generate web content on the server.</span></span> <span data-ttu-id="fc1dd-204">你完全可以混合服务器代码和客户端内容和代码。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-204">You can cleanly mix server code with client side content and code.</span></span>

```text
<ul>
  @for (int i = 0; i < 5; i++) {
    <li>List item @i</li>
  }
</ul>
```

<span data-ttu-id="fc1dd-205">使用 Razor 视图引擎可以定义[布局](views/layout.md)，[分部视图](views/partial.md)和可替换的部分。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-205">Using the Razor view engine you can define [layouts](views/layout.md), [partial views](views/partial.md) and replaceable sections.</span></span>

### <a name="strongly-typed-views"></a><span data-ttu-id="fc1dd-206">强类型化的视图</span><span class="sxs-lookup"><span data-stu-id="fc1dd-206">Strongly typed views</span></span>

<span data-ttu-id="fc1dd-207">在 MVC razor 视图可以为强类型根据您的模型。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-207">Razor views in MVC can be strongly typed based on your model.</span></span> <span data-ttu-id="fc1dd-208">控制器可以将强类型化的模型传递给启用您的视图具有类型检查和 IntelliSense 支持的视图。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-208">Controllers can pass a strongly typed model to views enabling your views to have type checking and IntelliSense support.</span></span>

<span data-ttu-id="fc1dd-209">例如，下面的视图定义的类型的模型`IEnumerable<Product>`:</span><span class="sxs-lookup"><span data-stu-id="fc1dd-209">For example, the following view defines a model of type `IEnumerable<Product>`:</span></span>

```html
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a><span data-ttu-id="fc1dd-210">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="fc1dd-210">Tag Helpers</span></span>

<span data-ttu-id="fc1dd-211">[标记帮助程序](views/tag-helpers/intro.md)启用服务器端代码，以参与创建和呈现 Razor 文件中的 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-211">[Tag Helpers](views/tag-helpers/intro.md) enable server side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="fc1dd-212">你可以使用标记帮助程序来定义自定义标记 (例如， `<environment>`) 或修改现有标记的行为 (例如， `<label>`)。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-212">You can use tag helpers to define custom tags (for example, `<environment>`) or to modify the behavior of existing tags (for example, `<label>`).</span></span> <span data-ttu-id="fc1dd-213">标记帮助程序将绑定到根据元素名称和其属性的特定元素。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-213">Tag Helpers bind to specific elements based on the element name and its attributes.</span></span> <span data-ttu-id="fc1dd-214">它们提供的同时仍然保留 HTML 编辑体验的服务器端呈现的好处。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-214">They provide the benefits of server-side rendering while still preserving an HTML editing experience.</span></span>

<span data-ttu-id="fc1dd-215">有许多内置的标记帮助器常见任务-例如，创建窗体、 链接、 加载资产和详细的和甚至更多的可用在公共 GitHub 存储库并作为 NuGet 程序包。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-215">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="fc1dd-216">在使用 C# 中，创作标记帮助程序和它们目标基于元素名称、 属性名称或父标记的 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-216">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="fc1dd-217">例如，内置 LinkTagHelper 可以用于创建链接到`Login`操作`AccountsController`:</span><span class="sxs-lookup"><span data-stu-id="fc1dd-217">For example, the built-in LinkTagHelper can be used to create a link to the `Login` action of the `AccountsController`:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [3]}} -->

```html
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

<span data-ttu-id="fc1dd-218">`EnvironmentTagHelper`可用来在你的运行时环境，如开发、 过渡或生产的视图 （例如，原始或缩减） 中包括不同的脚本：</span><span class="sxs-lookup"><span data-stu-id="fc1dd-218">The `EnvironmentTagHelper` can be used to include different scripts in your views (for example, raw or minified) based on the runtime environment, such as Development, Staging, or Production:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [1, 3, 4, 9]}} -->

```html
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.1.4.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery">
    </script>
</environment>
```

<span data-ttu-id="fc1dd-219">标记帮助器提供 HTML 友好开发体验和用于创建 HTML 和 Razor 标记的丰富智能感知环境。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-219">Tag Helpers provide an HTML-friendly development experience and a rich IntelliSense environment for creating HTML and Razor markup.</span></span> <span data-ttu-id="fc1dd-220">大部分内置标记帮助器目标现有 HTML 元素，而为的元素提供服务器端属性。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-220">Most of the built-in Tag Helpers target existing HTML elements and provide server-side attributes for the element.</span></span>

### <a name="view-components"></a><span data-ttu-id="fc1dd-221">查看组件</span><span class="sxs-lookup"><span data-stu-id="fc1dd-221">View Components</span></span>

<span data-ttu-id="fc1dd-222">[查看组件](views/view-components.md)可以打包呈现逻辑和整个应用程序中重用它。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-222">[View Components](views/view-components.md) allow you to package rendering logic and reuse it throughout the application.</span></span> <span data-ttu-id="fc1dd-223">它们类似于[分部视图](views/partial.md)，但具有关联的逻辑。</span><span class="sxs-lookup"><span data-stu-id="fc1dd-223">They're similar to [partial views](views/partial.md), but with associated logic.</span></span>
