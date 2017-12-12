---
title: "ASP.NET Core MVC 概述"
author: ardalis
description: "了解如何 ASP.NET 核心 MVC 是用于生成 web 应用的丰富的框架和 Api 使用模型-视图-控制器设计模式。"
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 89af38d1-52e0-4db7-b791-dbce909b0714
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/overview
ms.openlocfilehash: 2492b6aa4602dbbf3b9cd3dca00d40690c640cab
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="overview-of-aspnet-core-mvc"></a>ASP.NET Core MVC 概述

通过[Steve Smith](https://ardalis.com/)

ASP.NET 核心 MVC 是一个丰富的框架，用于生成 web 应用和 Api 使用模型-视图-控制器设计模式。

## <a name="what-is-the-mvc-pattern"></a>什么是 MVC 模式？

模型-视图-控制器 (MVC) 体系结构模式将应用程序分成三个组件的主要组： 模型、 视图和控制器。 此模式有助于达到[关注点分离](http://deviq.com/separation-of-concerns/)。 使用此模式，用户请求路由到负责使用模型来执行用户操作和/或检索查询结果的控制器。 控制器选择视图以显示给用户，然后将其提供使用它要求任何模型数据。

下图显示了三个主要组件以及哪些引用其他：

![MVC 模式](overview/_static/mvc.png)

此说明职责可帮助你缩放应用程序的复杂性，因为它是更轻松地代码、 调试和测试拥有的内容 （模型、 视图或控制器） 的单个作业 (并遵循[单个责任原则](http://deviq.com/single-responsibility-principle/)). 它会为更新、 测试和调试代码有依赖项分布在两个或多个上述三个方面更加困难。 例如，用户界面逻辑往往需要更改频率高于业务逻辑。 如果演示文稿代码和业务逻辑必须合并在单个对象，你必须修改包含业务逻辑，每次更改用户界面的对象。 这是可能会将错误引入并需要重新测试的所有业务逻辑在每个最小用户界面更改后。

> [!NOTE]
> 视图和控制器取决于模型。 但是，该模型取决于视图和控制器都不。 这是分离的主要优点之一。 这种分离使模型来生成和测试独立于可视化表示形式。

### <a name="model-responsibilities"></a>模型职责

一个 MVC 应用程序中的模型表示的应用程序和任何业务逻辑或由它执行的操作的状态。 应在模型中，以及用于永久保存应用程序的状态的任何实现逻辑封装业务逻辑。 强类型化视图通常将使用专门设计为包含数据的视图模型类型在该视图; 上显示控制器将创建并填充这些 ViewModel 实例从模型。

> [!NOTE]
> 有多种方法来组织中使用 MVC 体系结构模式的应用程序的模型。 深入了解某些[不同种类的模型类型](http://deviq.com/kinds-of-models/)。

### <a name="view-responsibilities"></a>视图职责

视图是负责通过用户界面提供内容。 它们使用[Razor 视图引擎](#razor-view-engine)若要将.NET 代码嵌入在 HTML 标记。 应在视图内，最小逻辑，并且它们中的任何逻辑应与提供内容。 如果你发现需要执行大量的逻辑视图中为了显示复杂模型中的数据文件，请考虑使用[视图组件](views/view-components.md)，ViewModel，或查看模板，以简化视图。

### <a name="controller-responsibilities"></a>控制器职责

控制器在处理用户交互，使用的模型，并最终选择要呈现的视图的组件。 在 MVC 应用程序，该视图仅显示信息;控制器处理，并响应用户输入和交互。 在 MVC 模式中，该控制器已是初始入口点，并且负责选择要使用哪个模型类型和要呈现的视图 (因此它控制的其名称中的应用程序如何响应的给定的请求)。

> [!NOTE]
> 控制器应不过于复杂，过多的职责。 若要阻止控制器逻辑变得过于复杂，使用[单个责任原则](http://deviq.com/single-responsibility-principle/)控制器出来放入域模型中的推送的业务逻辑。

>[!TIP]
> 如果你发现你的控制器操作频繁地执行操作相同种类的操作，则可以按照[切勿重复原则](http://deviq.com/don-t-repeat-yourself/)通过移动到这些常见操作[筛选器](#filters)。

## <a name="what-is-aspnet-core-mvc"></a>ASP.NET 核心 MVC 是什么

ASP.NET 核心 MVC 框架是轻量、 打开的源，用于 ASP.NET Core 优化的高度可测试的演示文稿 framework。

ASP.NET 核心 MVC 提供了一种基于模式的方法来构建实现完全分离关注点的动态网站。 它让你完全控制标记，支持 TDD 友好的开发，并且使用最新的 web 标准。

## <a name="features"></a>功能

ASP.NET 核心 MVC 包括以下部分：

* [路由](#routing)
* [模型绑定](#model-binding)
* [模型验证](#model-validation)
* [依赖关系注入](../fundamentals/dependency-injection.md)
* [筛选器](#filters)
* [区域](#areas)
* [Web Api](#web-apis)
* [可测试性](#testability)
* [Razor 视图引擎](#razor-view-engine)
* [强类型化的视图](#strongly-typed-views)
* [标记帮助程序](#tag-helpers)
* [查看组件](#view-components)

### <a name="routing"></a>路由

ASP.NET 核心 MVC 构建的顶部[ASP.NET Core 路由](../fundamentals/routing.md)，一个功能强大的 URL 映射组件，用于你构建的应用程序具有易于理解和搜索的 Url。 这使你可以定义应用程序的 URL 命名模式适用于搜索引擎优化 (SEO) 和链接生成，而不考虑你的 web 服务器上的文件的组织方式。 你可以定义路由使用一种方便的路由模板语法支持路由值约束、 默认值和可选值。

*基于约定的路由*可以全局定义的 URL 格式的启用你的应用程序接受并的每个格式的方式将映射到特定的操作方法在给定的控制器。 当收到传入的请求时，路由引擎将分析该 URL 和匹配到一种定义的 URL 格式，然后调用关联的控制器操作方法。

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

*属性路由*使您能够指定的修饰控制器和操作具有定义应用程序的路由的属性的路由信息。 这意味着旁边的控制器和操作与它们关联放置的 route 定义。

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

### <a name="model-binding"></a>模型绑定

ASP.NET 核心 MVC[模型绑定](models/model-binding.md)将 （窗体值、 路由数据、 查询字符串参数、 HTTP 标头） 的客户端请求数据转换为控制器可以处理的对象。 因此，控制器逻辑无需执行的工作成果找出传入的请求数据;它只需具有作为其操作方法的参数的数据。

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
   ```

### <a name="model-validation"></a>模型验证

ASP.NET 核心 MVC 支持[验证](models/validation.md)的修饰模型对象具有数据批注验证属性。 验证特性之前值发布到服务器，客户端检查以及调用之前的控制器操作在服务器上。

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

进行的控制器操作：

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

框架将处理验证请求数据在客户端和服务器上。 模型类型上指定的验证逻辑添加到以非介入式批注的形式呈现的视图和浏览器中使用采用强制执行[jQuery 验证](https://jqueryvalidation.org/)。

### <a name="dependency-injection"></a>依赖关系注入

ASP.NET Core 提供的内置支持[依赖关系注入 (DI)](../fundamentals/dependency-injection.md)。 ASP.NET 核心 mvc[控制器](controllers/dependency-injection.md)请求所需通过其构造函数，从而使它们可以按照服务[显式依赖关系原则](http://deviq.com/explicit-dependencies-principle/)。

你的应用程序还可以使用[依赖关系注入视图中文件](views/dependency-injection.md)，使用`@inject`指令：

```cshtml
@inject SomeService ServiceName
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ServiceName.GetTitle</title>
</head>
<body>
    <h1>@ServiceName.GetTitle</h1>
</body>
</html>
```

### <a name="filters"></a>筛选器

[筛选器](controllers/filters.md)帮助开发人员封装跨领域问题，如异常处理或授权。 筛选器启用操作方法的运行自定义前期和后期处理逻辑，并可以配置为在某些点在给定的请求执行管道中运行。 筛选器可应用于控制器或作为属性的操作 （或可以全局运行）。 多个筛选器 (如`Authorize`) 包含在 framework。


```csharp
[Authorize]
   public class AccountController : Controller
   {
```

### <a name="areas"></a>区域

[区域](controllers/areas.md)提供一种方法进行分区到较小功能分组中的大型 ASP.NET 核心 MVC Web 应用。 一个区域实际上是在应用程序内的 MVC 结构。 在 MVC 项目中，逻辑组件，如模型、 控制器和视图保留在不同的文件夹和 MVC 使用命名约定来创建这些组件之间的关系。 对于大型应用，它可能会更有利分区成单独的功能的高级别区域的应用程序。 例如，电子商务应用程序与多个业务单位，例如签出、 计费和搜索等等。这些部门的每个具有其自己的逻辑组件视图、 控制器和模型。

### <a name="web-apis"></a>Web API

除了正在很好的平台，用于构建网站，ASP.NET 核心 MVC 还具有很好的生成 Web Api 的支持。 你可以构建可以覆盖广泛的客户端包括浏览器和移动设备的服务。

Framework 包括内置的支持通过 HTTP 内容协商支持[格式设置数据](models/formatting.md)作为 JSON 或 XML。 编写[自定义格式化程序](advanced/custom-formatters.md)添加您自己的格式的支持。

使用链接生成方法来启用对超媒体的支持。 轻松启用对支持[跨域资源共享 (CORS)](http://www.w3.org/TR/cors/) ，以便可以跨多个 Web 应用程序共享你的 Web Api。

### <a name="testability"></a>可测试性

接口和依赖关系注入框架的使用使其适合对单元测试，和框架包括功能 （如 TestHost 和 InMemory 实体框架提供程序），使[集成测试](../testing/integration-testing.md)快速便捷以及。 详细了解[测试控制器逻辑](controllers/testing.md)。

### <a name="razor-view-engine"></a>Razor 视图引擎

[ASP.NET 核心 MVC 视图](views/overview.md)使用[Razor 视图引擎](views/razor.md)来呈现视图。 Razor 是用于定义使用嵌入的 C# 代码的视图的 compact、 表现力和流畅的模板标记语言。 Razor 用于动态生成服务器上的 web 内容。 你完全可以混合服务器代码和客户端内容和代码。

```text
<ul>
  @for (int i = 0; i < 5; i++) {
    <li>List item @i</li>
  }
</ul>
```

使用 Razor 视图引擎可以定义[布局](views/layout.md)，[分部视图](views/partial.md)和可替换的部分。

### <a name="strongly-typed-views"></a>强类型化的视图

在 MVC razor 视图可以为强类型根据您的模型。 控制器可以将强类型化的模型传递给启用您的视图具有类型检查和 IntelliSense 支持的视图。

例如，下面的视图定义的类型的模型`IEnumerable<Product>`:

```cshtml
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a>标记帮助程序

[标记帮助程序](views/tag-helpers/intro.md)启用服务器端代码，以参与创建和呈现 Razor 文件中的 HTML 元素。 你可以使用标记帮助程序来定义自定义标记 (例如， `<environment>`) 或修改现有标记的行为 (例如， `<label>`)。 标记帮助程序将绑定到根据元素名称和其属性的特定元素。 它们提供的同时仍然保留 HTML 编辑体验的服务器端呈现的好处。

有许多内置的标记帮助器常见任务-例如，创建窗体、 链接、 加载资产和详细的和甚至更多的可用在公共 GitHub 存储库并作为 NuGet 程序包。 在使用 C# 中，创作标记帮助程序和它们目标基于元素名称、 属性名称或父标记的 HTML 元素。 例如，内置 LinkTagHelper 可以用于创建链接到`Login`操作`AccountsController`:

```cshtml
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

`EnvironmentTagHelper`可用来在你的运行时环境，如开发、 过渡或生产的视图 （例如，原始或缩减） 中包括不同的脚本：

```cshtml
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

标记帮助器提供 HTML 友好开发体验和用于创建 HTML 和 Razor 标记的丰富智能感知环境。 大部分内置标记帮助器目标现有 HTML 元素，而为的元素提供服务器端属性。

### <a name="view-components"></a>查看组件

[查看组件](views/view-components.md)可以打包呈现逻辑和整个应用程序中重用它。 它们类似于[分部视图](views/partial.md)，但具有关联的逻辑。
