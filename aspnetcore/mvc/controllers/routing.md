---
title: "路由到控制器操作"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 03/14/2017
ms.topic: article
ms.assetid: 26250a4d-bf62-4d45-8549-26801cf956e9
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/routing
ms.openlocfilehash: 5a0b5399f7441035cb1231a009681ca22b07ab4e
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2017
---
# <a name="routing-to-controller-actions"></a>路由到控制器操作

通过[Ryan Nowak](https://github.com/rynowak)和[Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET 核心 MVC 使用路由[中间件](../../fundamentals/middleware.md)匹配的传入请求的 Url，并将它们映射到操作。 启动代码或属性中定义路由。 路由描述应如何操作到匹配的 URL 路径。 路由还用于生成在响应中发送 （用于链接） 的 Url。 

操作或者通常路由或路由的属性。 在控制器或操作上放置一个路由，使其路由的属性。 请参阅[混合路由](#routing-mixed-ref-label)有关详细信息。

本文档将介绍 MVC 和路由，以及如何典型的 MVC 应用程序生成之间的交互使用的路由功能。 请参阅[路由](xref:fundamentals/routing)有关高级路由的详细信息。

## <a name="setting-up-routing-middleware"></a>设置路由的中间件

在你*配置*方法可能会看到类似于代码：

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

在调用`UseMvc`，`MapRoute`用于创建一个路由，我们将称为`default`路由。 大多数 MVC 应用程序将使用模板类似于使用的路由`default`路由。

路由模板`"{controller=Home}/{action=Index}/{id?}"`可以匹配类似 URL 路径`/Products/Details/5`并将提取路由值`{ controller = Products, action = Details, id = 5 }`通过将拆分为标记的路径。 MVC 将尝试查找名为的控制器`ProductsController`和运行操作`Details`:

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

请注意，在此示例中，模型绑定将使用的值`id = 5`设置`id`参数`5`时调用此操作。 请参阅[模型绑定](../models/model-binding.md)有关详细信息。

使用`default`路由：

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

路由模板中：

* `{controller=Home}`定义`Home`作为默认值`controller`

* `{action=Index}`定义`Index`作为默认值`action`

* `{id?}`定义`id`为可选

默认和可选的路由参数不需要必须包含在匹配项的 URL 路径中。 请参阅[路由模板参考](../../fundamentals/routing.md#route-template-reference)有关路由模板语法的详细说明。

`"{controller=Home}/{action=Index}/{id?}"`可以与匹配的 URL 路径`/`并将生成路由值`{ controller = Home, action = Index }`。 值`controller`和`action`进行的默认值，使用`id`不会生成一个值，因为 URL 路径中没有相应的段。 MVC 将使用这些路由值选择`HomeController`和`Index`操作：

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

使用此控制器定义和路由模板`HomeController.Index`操作就会执行任何以下 URL 路径：

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

便捷方法`UseMvcWithDefaultRoute`:

```csharp
app.UseMvcWithDefaultRoute();
```

可以用于替换：

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc`和`UseMvcWithDefaultRoute`实例添加`RouterMiddleware`到中间件管道。 MVC 不会直接与中间件，交互，并使用路由处理请求。 MVC 连接到的实例通过路由`MvcRouteHandler`。 内的代码`UseMvc`类似于以下：

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc`未直接定义任何路由，它将占位符添加到的路由集合`attribute`路由。 重载`UseMvc(Action<IRouteBuilder>)`可通过添加你自己的路由，同时支持的属性路由。  `UseMvc`和所有其变体将添加的属性路由的占位符-始终无论你如何配置可用的属性路由是`UseMvc`。 `UseMvcWithDefaultRoute`定义一个默认路由，并支持的属性路由。 [的属性路由](#attribute-routing-ref-label)部分包括更多详细信息的属性路由。

<a name=routing-conventional-ref-label></a>

## <a name="conventional-routing"></a>传统的路由

`default`路由：

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

是一种*传统路由*。 我们将此样式*传统路由*因为它在建立*约定*的 URL 路径：

* 第一个路径段映射到的控制器名称

* 第二个映射到的操作名称。

* 第三个段适用于一个可选`id`用于将映射到模型实体

使用此`default`路由、 URL path`/Products/List`映射到`ProductsController.List`操作，和`/Blog/Article/17`映射到`BlogController.Article`。 此映射基于控制器和操作名称**仅**和不基于命名空间、 源文件位置或方法参数。

> [!TIP]
> 使用传统路由与默认路由，可快速生成应用程序，而无需附带你定义每个操作的新 URL 模式。 包含 CRUD 样式操作的应用程序，在你的控制器之间的 url 具有一致性可以帮助简化你的代码，使你的 UI 更可预测。

> [!WARNING]
> `id`由路由模板，这意味着你的操作可以执行而作为 URL 的一部分提供的 ID 没有定义为可选。 通常将发生如果`id`URL 中省略是将设置为`0`模型绑定，以及因此导致的任何实体将位于数据库匹配`id == 0`。 属性路由可以为你提供细化的控制，以使某些操作而不是针对其他所需的 ID。 按照约定，文档将包括可选参数喜欢`id`它们是大可能出现的正确用法。

## <a name="multiple-routes"></a>多个路由

你可以添加多个路由内的`UseMvc`通过添加更多调用到`MapRoute`。 这样做将使你可以定义多个约定，或添加专用于特定的操作，如的常规路由：

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`blog`路由此处*专用的常规路由*，这意味着它使用与传统的路由系统，但专用于特定的操作。 由于`controller`和`action`不在路由模板中显示为参数，它们只能有默认值，并且因此此路由将始终映射到操作`BlogController.Article`。

路由集合中的路由进行排序，并将它们添加的顺序处理。 因此，在此示例中，`blog`路由将尝试之前`default`路由。

> [!NOTE]
> *专用传统路由*通常使用类似的全方位路由参数`{*article}`捕获的 URL 路径的剩余部分。 这可以使路由太贪婪这意味着它匹配你想要匹配的其他路由的 Url。 将贪婪路由放更高版本中的路由表，为了解决此问题。

### <a name="fallback"></a>回退

作为请求处理过程中，MVC 将验证路由值可以用于在你的应用程序中查找控制器和操作。 如果路由值不匹配操作然后路由不被视为匹配项，并将尝试下一个路由。 这称为*回退*，并且它具有旨在简化其中传统路由重叠的情况。

### <a name="disambiguating-actions"></a>消除歧义操作

通过路由匹配两个操作，必须区分 MVC 来选择最佳候选或者引发异常。 例如: 

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

此控制器定义将匹配的 URL 路径的两个操作`/Products/Edit/17`和将数据路由`{ controller = Products, action = Edit, id = 17 }`。 这是 MVC 控制器的典型模式其中`Edit(int)`显示要编辑某一产品的窗体和`Edit(int, Product)`处理已发布的窗体。 为了实现这一点 MVC 将需要选择`Edit(int, Product)`请求时 HTTP`POST`和`Edit(int)`的 HTTP 谓词时任何其他内容。

`HttpPostAttribute` ( `[HttpPost]` ) 实现的`IActionConstraint`，将只允许的操作的 HTTP 谓词时选择`POST`。 是否存在`IActionConstraint`使`Edit(int, Product)`好匹配比`Edit(int)`，因此`Edit(int, Product)`将首先尝试。

你只需编写自定义`IActionConstraint`中专业的方案，但它实现的重要了解这样的属性的角色`HttpPostAttribute`-类似特性定义用于其他 HTTP 谓词。 传统的路由中很常见的操作时要使用相同的操作名称是它们属于`show form -> submit form`工作流。 此模式的便利性将变得更加明显复查[了解 IActionConstraint](#understanding-iactionconstraint)部分。

如果多个路由都不匹配，并且 MVC 找不到最佳路由，它会引发`AmbiguousActionException`。

<a name=routing-route-name-ref-label></a>

### <a name="route-names"></a>路由名称

字符串`"blog"`和`"default"`在下面的示例都是路由名称：


```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

路由名称给予路由，以便命名的路由可以用于 URL 生成，此逻辑名称。 这极大地简化了 URL 创建，当路由的顺序可能使复杂的 URL 生成。 路由名称必须是唯一的应用程序范围。

路由名称将不会影响对 URL 匹配或处理的请求;它们仅用于 URL 生成。 [路由](xref:fundamentals/routing)的更多详细信息包括特定于 MVC 的帮助器中的 URL 生成的 URL 生成。

<a name=attribute-routing-ref-label></a>

## <a name="attribute-routing"></a>属性路由

属性路由使用一的组特性操作直接映射到路由模板。 在下面的示例中，`app.UseMvc();`中使用`Configure`传递方法和路由。 `HomeController`将匹配的一组 Url 类似于默认路由`{controller=Home}/{action=Index}/{id?}`将匹配：

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

`HomeController.Index()`操作将执行的 URL 路径的任何`/`， `/Home`，或`/Home/Index`。

> [!NOTE]
> 此示例重点介绍的属性路由和传统路由的主要编程区别。 属性路由需要更多输入指定的路由;传统的默认路由更简洁地采用处理路由。 但是，属性路由允许 （和需要） 的其路由模板应用于每个操作的精确控制。

使用路由的控制器名称和操作名称的特性播放**没有**角色选择操作。 此示例将匹配与上述示例相同的 Url。

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
> 上面的路由模板未定义路由的参数`action`， `area`，和`controller`。 事实上，属性路由中不允许使用这些路由参数。 路由模板已与某个操作关联，因为它没有任何意义分析从 URL 的操作名称。

## <a name="attribute-routing-with-httpverb-attributes"></a>属性路由与 Http [动词] 属性

此外可以进行的属性路由的使用`Http[Verb]`特性例如`HttpPostAttribute`。 所有这些属性可以接受路由模板。 此示例演示与相同的路由模板匹配的两个操作：

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

URL 路径类似`/products``ProductsApi.ListProducts`将执行操作，当 HTTP 谓词为`GET`和`ProductsApi.CreateProduct`的 HTTP 谓词时将执行`POST`。 第一次的属性路由匹配针对路由特性定义的路由模板的集合的 URL。 一旦与匹配的路由模板，`IActionConstraint`应用约束的目的是确定可以执行哪些操作。

> [!TIP]
> 在生成 REST API 时，很少要使用`[Route(...)]`上操作方法。 最好使用多个特定`Http*Verb*Attributes`要精确有关你的 API 的支持。 REST api 的客户端需要知道什么路径和 HTTP 谓词将映射到特定逻辑操作。

由于属性路由适用于特定的操作，是很容易地路由模板定义的一部分所需参数。 在此示例中，`id`是必要的 URL 路径的一部分。

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

`ProductsApi.GetProduct(int)`操作将针对类似的 URL 路径执行`/products/3`而不是类似的 URL 路径`/products`。 请参阅[路由](../../fundamentals/routing.md)有关路由模板和相关的选项的完整说明。

## <a name="route-name"></a>路由名称

下面的代码定义*路由名称*的`Products_List`:

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

可以使用路由名称，以便生成基于特定的路由的 URL。 路由名称匹配的路由的行为在 URL 上没有任何影响，并且仅用于 URL 生成。 路由名称必须是唯一的应用程序范围。

> [!NOTE]
> 与此相反传统*默认路由*，后者定义了`id`参数为可选 (`{id?}`)。 数据透视表能够精确地指定 Api 有优点，例如允许`/products`和`/products/5`要被调度到不同的操作。

<a name=routing-combining-ref-label></a>

### <a name="combining-routes"></a>组合路由

若要使的属性路由重复更少，在控制器上的路由属性与上的各项操作的路由属性相结合。 在控制器上定义的任何路由模板为前缀的操作的路由模板。 在控制器上放置一个路由特性使**所有**控制器中的操作使用的属性路由。

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

在此示例中的 URL 路径`/products`可以匹配`ProductsApi.ListProducts`，和的 URL 路径`/products/5`可以匹配`ProductsApi.GetProduct(int)`。 这两种操作只匹配 HTTP`GET`因为使用了修饰`HttpGetAttribute`。

将路由应用于开头的操作的模板`/`执行不获取结合应用到控制器的路由模板。 此示例匹配一组的 URL 路径类似于*默认路由*。

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

### <a name="ordering-attribute-routes"></a>排序的属性路由

与传统的路由，这按照定义的顺序执行，不同的属性路由生成树，然后同时匹配所有路由。 此行为方式会如同-如果路由条目已被放在非理想进行排序;最特定路由有机会执行之前的更多常规路由。

例如，一个路由，如`blog/search/{topic}`比类似的路由更具体`blog/{*article}`。 逻辑上讲`blog/search/{topic}`路由运行首先，默认情况下，因为这是仅有意义的排序。 使用传统的路由，开发人员负责将路由放置在所需的顺序。

属性路由可以配置订单时，使用`Order`的所有提供的框架路由属性的属性。 根据升序处理路由有点`Order`属性。 默认顺序是`0`。 设置路由使用`Order = -1`之前未设置订单的路由将运行。 设置路由使用`Order = 1`将默认路由排序后运行。

> [!TIP]
> 具体取决于避免`Order`。 如果你的 URL 空间需要显式顺序值正确路由，它会向客户端也可能造成混淆。 一般情况下的属性路由将选择正确的路由与 URL 匹配。 如果 URL 生成使用的默认顺序不能正常工作，使用路由名称，因为替代通常比应用更简单`Order`属性。

<a name=routing-token-replacement-templates-ref-label></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>令牌在路由模板中的替换 ([controller] [操作] [区域])

为方便起见，属性路由支持*令牌替换*括在大括号方形的令牌 (`[`， `]`)。 令牌`[action]`， `[area]`，和`[controller]`将替换为操作名称、 区域名称和其中定义路由的操作的控制器名称的值。 在此示例中的注释中所述操作可以与匹配的 URL 路径：

[!code-csharp[Main](routing/sample/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

作为生成的属性路由的最后一个步骤中发生，标记替换。 上面的示例将与下面的代码相同的行为：

[!code-csharp[Main](routing/sample/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

此外可以与继承组合属性路由。 这是特别有用结合标记替换。

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

标记替换也适用于定义的属性路由的路由名称。 `[Route("[controller]/[action]", Name="[controller]_[action]")]`将生成每个操作的唯一路由名称。

若要匹配的文本的标记替换分隔符`[`或`]`，通过重复字符对其进行转义 (`[[`或`]]`)。

<a name=routing-multiple-routes-ref-label></a>

### <a name="multiple-routes"></a>多个路由

属性定义访问相同的操作的多个路由的路由支持。 此的最常见用法是模拟的行为*默认传统路由*下面的示例中所示：

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

在控制器上放置多个路由属性意味着每个将合并与每个路由属性上的操作方法。

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

当多个路由属性 (该实现`IActionConstraint`) 位于采取某一操作，则每个操作约束将与定义它的属性路由模板相结合。

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
> 操作使用多个路由可能看起来非常强大，而是更好的做法保持简单和定义完善的应用程序的 URL 空间。 仅在需要时，例如若要支持现有客户端，请在操作中使用多个路由。

<a name=routing-attr-options></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>指定的属性路由可选参数、 默认值和约束

属性路由支持内联语法与传统的路由，以便指定可选参数、 默认值和约束相同。

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

请参阅[路由模板参考](../../fundamentals/routing.md#route-template-reference)有关路由模板语法的详细说明。

<a name=routing-cust-rt-attr-irt-ref-label></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>使用自定义路由属性`IRouteTemplateProvider`

所有框架中提供的路由属性 ( `[Route(...)]`，`[HttpGet(...)]`等) 实现`IRouteTemplateProvider`接口。 当应用程序启动并使用实现的 MVC 控制器类和操作方法上的属性查找`IRouteTemplateProvider`生成路由的初始集。

你可以实现`IRouteTemplateProvider`来定义你自己的路由特性。 每个`IRouteTemplateProvider`允许你定义自定义路由模板后，一个路由顺序，并将命名：

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

从上面的示例中的属性自动设置`Template`到`"api/[controller]"`时`[MyApiController]`应用。

<a name=routing-app-model-ref-label></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>使用应用程序模型自定义属性的路由

*应用程序模型*是所有的 MVC 中用于路由并执行你的操作的元数据在启动时创建的对象模型。 *应用程序模型*包括所有路由属性从收集的数据 (通过`IRouteTemplateProvider`)。 你可以编写*约定*在启动时若要自定义路由的行为方式修改应用程序模型。 本部分说明自定义使用应用程序模型进行路由的简单示例。

[!code-csharp[Main](routing/sample/main/NamespaceRoutingConvention.cs)]

<a name=routing-mixed-ref-label></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>混合路由： 属性路由 vs 传统路由

MVC 应用程序可以混合使用传统的路由和的属性路由。 它是典型用于为 HTML 页的浏览器中，提供服务的控制器的常规路由和属性为 REST Api 提供服务的控制器的路由。

操作或者通常路由或路由的属性。 在控制器或操作上放置一个路由，使其路由的属性。 定义属性的路由的操作无法达到通过传统的路由，反之亦然。 **任何**在控制器上的路由特性使路由的控制器特性中的所有操作。

> [!NOTE]
> 路由的系统的两种类型的区别是 URL 与匹配的路由模板后，将应用的过程。 在传统的路由，路由值匹配中的用于查找表中的所有传统的路由操作选择的操作和控制器。 中的属性路由，每个模板已与某个操作关联，并且需要任何进一步的查找。

<a name=routing-url-gen-ref-label></a>

## <a name="url-generation"></a>URL 生成

MVC 应用程序可以使用路由的 URL 生成功能生成操作的 URL 链接。 生成 Url 消除了硬编码 Url，从而使代码更稳定、 更易于维护。 本部分重点介绍 MVC 提供的 URL 生成功能，并只包括 URL 生成的工作原理的基础知识。 请参阅[路由](../../fundamentals/routing.md)有关 URL 生成的详细说明。

`IUrlHelper`接口是 MVC 和路由 URL 生成之间的基础结构的基础部分。 你将找到的实例`IUrlHelper`可通过`Url`控制器、 视图和视图组件中的属性。

在此示例中，`IUrlHelper`通过使用接口`Controller.Url`属性来生成指向另一项操作的 URL。

[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

如果应用程序使用传统默认路由的值`url`变量将为 URL 路径字符串`/UrlGeneration/Destination`。 通过路由通过组合当前请求 （环境值），从路由值传递到的值创建此 URL 路径`Url.Action`并替换到路由模板为这些值：

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

路由模板中的每个路由参数具有匹配名称的值和环境的值替换其值。 不具有值的路由参数，如果它有一个，或如果它是可选的跳过，也可以使用默认值 (一样的情况下`id`在此示例中)。 URL 生成将失败，如果任何所需的路由参数没有对应的值。 如果 URL 生成失败，则为路由，会尝试下一个路由，直到尝试所有路由或找到匹配项。

示例`Url.Action`更高版本假定传统路由，但 URL 生成同样可与配合使用的属性路由，尽管概念都是不同。 使用传统路由一样，路由值用于展开模板和的路由值`controller`和`action`通常出现在该模板中-这样做的原因由路由匹配的 Url 遵守*约定*. 中的属性路由，路由值`controller`和`action`不允许出现在模板中-它们而是用于查找要使用的模板。

此示例使用的属性路由：

[!code-csharp[Main](routing/sample/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC 生成查找表的所有属性路由操作，并将匹配`controller`和`action`值，以选择要用于生成 URL 的路由模板。 在此示例中更高版本，`custom/url/to/destination`生成。

### <a name="generating-urls-by-action-name"></a>操作名称生成 Url

`Url.Action` (`IUrlHelper` . `Action`) 以及所有相关的重载所有基于该了解你想要指定什么如果链接到通过指定控制器名称和操作名称。

> [!NOTE]
> 使用时`Url.Action`，当前路由值`controller`和`action`为你的值指定`controller`和`action`属于这两者的*环境值***和***值*。 该方法`Url.Action`，始终使用的当前值`action`和`controller`并将生成将路由到当前操作的 URL 路径。

路由尝试在环境的值中使用值来填充时生成 URL 未提供的信息。 使用类似的路由`{a}/{b}/{c}/{d}`和环境值`{ a = Alice, b = Bob, c = Carol, d = David }`，路由具有足够的信息来生成无需任何其他值的 URL-由于所有路由形参具有一个值。 如果你添加值`{ d = Donovan }`，值`{ d = David }`将被忽略，并且生成的 URL 路径将为`Alice/Bob/Carol/Donovan`。

> [!WARNING]
> URL 路径是层次结构。 在以下示例中，如果你添加值`{ c = Cheryl }`，两个值`{ c = Carol, d = David }`将被忽略。 在这种情况下我们不再提供的值`d`和 URL 的生成将失败。 需要指定的所需的值`c`和`d`。  您可能希望命中此问题的默认路由 (`{controller}/{action}/{id?}`)-但你将很少遇到此行为在实践中作为`Url.Action`将始终显式指定`controller`和`action`值。

较长的重载`Url.Action`还采用额外*路由值*对象以外的路由参数提供值`controller`和`action`。 将最常看到这与使用`id`如`Url.Action("Buy", "Products", new { id = 17 })`。 按照约定*路由值*对象通常是匿名类型的对象，但它也可以是`IDictionary<>`或*无格式传统的.NET 对象*。 路由参数不匹配任何其他路由值都将置于查询字符串。

[!code-csharp[Main](routing/sample/main/Controllers/TestController.cs)]

> [!TIP]
> 若要创建的绝对 URL，请使用接受重载`protocol`:`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name=routing-gen-urls-route-ref-label></a>

### <a name="generating-urls-by-route"></a>生成 Url 的路由

上面的代码演示了通过在控制器和操作名称的传入生成 URL。 `IUrlHelper`此外提供了`Url.RouteUrl`系列的方法。 这些方法是类似于`Url.Action`，但不是将复制的当前值`action`和`controller`为路由的值。 最常见用法是指定要使用特定的路由来生成 URL，通常的路由名称*而无需*指定控制器或操作名称。

[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name=routing-gen-urls-html-ref-label></a>

### <a name="generating-urls-in-html"></a>在 HTML 中生成 Url

`IHtmlHelper`提供`HtmlHelper`方法`Html.BeginForm`和`Html.ActionLink`生成`<form>`和`<a>`元素分别。 这些方法使用`Url.Action`方法来生成的 URL 和接受类似的自变量。 `Url.RouteUrl`为伴侣`HtmlHelper`是`Html.BeginRouteForm`和`Html.RouteLink`其提供了类似的功能。

TagHelpers 生成 Url 通过`form`TagHelper 和`<a>`TagHelper。 这两种使用`IUrlHelper`为其实现。 请参阅[使用窗体](../views/working-with-forms.md)有关详细信息。

在视图内`IUrlHelper`可通过`Url`未涵盖的上述任何临时 URL 生成的属性。

<a name=routing-gen-urls-action-ref-label></a>

### <a name="generating-urls-in-action-results"></a>在操作结果中生成 URL

上面示例已演示了使用`IUrlHelper`控制器，虽然控制器中的最常见用法是作为操作结果的一部分生成的 URL 中。

`ControllerBase`和`Controller`基类提供了引用另一项操作的操作结果的简便方法。 一种典型用法是接受用户输入后重定向。

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

操作结果工厂方法至的方法遵循类似的模式`IUrlHelper`。

<a name=routing-dedicated-ref-label></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>专用的常规路由的特殊情况

传统路由可以使用一种特殊的路由定义调用*专用的常规路由*。 在下面的示例中，路由名为`blog`是专用的常规路由。

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

使用这些 route 定义，`Url.Action("Index", "Home")`将生成的 URL 路径`/`与`default`路由，但原因？ 您可能认为路由值`{ controller = Home, action = Index }`将会足以生成的 URL 使用`blog`，并且结果将为`/blog?action=Index&controller=Home`。

专用的常规路由依赖于不具有相应的路由参数，则会使路由的默认值的特殊行为"太贪婪"与 URL 代。 在这种情况下的默认值是`{ controller = Blog, action = Article }`，并且不`controller`也不`action`将显示为路由参数。 当路由执行 URL 生成时，提供的值必须匹配的默认值。 URL 生成使用`blog`将失败，因为值`{ controller = Home, action = Index }`不匹配`{ controller = Blog, action = Article }`。 路由然后将回退，尝试`default`，其成功。

<a name=routing-areas-ref-label></a>

## <a name="areas"></a>区域

[区域](areas.md)是 MVC 功能用于为单独路由的命名空间 （适用于控制器操作） 和 （适用于视图） 的文件夹结构组织到组的相关的功能。 使用区域允许应用程序具有多个具有相同名称的控制器，只要它们具有不同*区域*。 使用区域创建层次结构用于通过添加另一个路由参数，路由`area`到`controller`和`action`。 本部分将讨论如何路由相互作用区域-请参阅[区域](areas.md)有关如何将区域用于视图的详细信息。

下面的示例将配置为使用默认的常规路由的 MVC 和*区域路由*的名为的区域`Blog`:

[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet1)]

匹配类似 URL 路径时`/Manage/Users/AddUser`，第一个路由将生成路由值`{ area = Blog, controller = Users, action = AddUser }`。 `area`路由值由默认值为`area`、 事实上通过创建的路由`MapAreaRoute`等效于以下：

[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute`创建使用的默认值和约束的路由`area`使用提供的区域的名称，在这种情况下`Blog`。 默认值可确保路由始终生成`{ area = Blog, ... }`，约束要求值`{ area = Blog, ... }`URL 生成的。

> [!TIP]
> 传统的路由与顺序相关。 一般情况下，具有区域路由应放在路由表的前面部分中因为它们是更具体相比没有区域的路由。

使用上面的示例中，路由值将匹配的以下操作：

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

`AreaAttribute`是什么表示控制器作为一个区域的一部分，我们假设此控制器处于`Blog`区域。 控制器而无需`[Area]`属性不是成员的任何区域中，并将**不**匹配时`area`由路由提供路由值。 在下面的示例中，仅列出的第一个控制器可以匹配路由值`{ area = Blog, controller = Users, action = AddUser }`。

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[Main](routing/sample/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> 出于完整性考虑此处显示的每个控制器的命名空间-否则控制器必须命名冲突并生成编译器错误。 类命名空间具有对 MVC 的路由没有影响。

前两个控制器是区域的成员，仅匹配时提供其各自领域名`area`路由值。 第三个控制器不是任何区域中和可以唯一匹配项的任何值时的成员`area`提供的路由。

> [!NOTE]
> 根据匹配*没有值*，缺少`area`值均相同就像的值`area`了 null 或空字符串。

在执行某一区域内的操作时，路由值，则为`area`将可以用作*环境值*用于路由以用于 URL 生成。 这意味着，默认情况下区域执行操作*粘性*URL 生成下面的示例所示。

[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name=iactionconstraint-ref-label></a>

## <a name="understanding-iactionconstraint"></a>了解 IActionConstraint

> [!NOTE]
> 本节是深入 framework 内部结构和 MVC 如何选择要执行的操作。 典型的应用程序不需要自定义`IActionConstraint`

你可能已使用`IActionConstraint`即使你不熟悉的界面。 `[HttpGet]`属性和类似`[Http-VERB]`属性实现`IActionConstraint`以便限制操作方法的执行。

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

假定默认传统路由的 URL 路径`/Products/Edit`会产生值`{ controller = Products, action = Edit }`，将匹配的**同时**此处显示的操作。 在`IActionConstraint`术语我们会说，这两种操作都视为候选项-因为它们都匹配的路由数据。

当`HttpGetAttribute`执行，它将说*edit （)*是的匹配项*获取*并且不是任何其他 HTTP 谓词的匹配项。 `Edit(...)`操作没有任何约束定义，并因此将匹配的任何 HTTP 谓词。 因此假设`POST`-仅`Edit(...)`匹配。 但对于`GET`这两种操作仍可以与匹配-但是，操作与`IActionConstraint`始终被认为是*更好*比操作而无需。 因此因为`Edit()`具有`[HttpGet]`它被视为更具体，并且如果这两种操作可以匹配将选择。

从概念上讲，`IActionConstraint`是一种形式*重载*，但而不重载具有相同名称的方法，它重载之间的相同 URL 匹配的操作。 属性路由还使用`IActionConstraint`并且可能会导致在操作的不同控制器从这两个正在考虑的候选项。

<a name=iactionconstraint-impl-ref-label></a>

### <a name="implementing-iactionconstraint"></a>实现 IActionConstraint

若要实现的最简单方法`IActionConstraint`是创建派生自的类`System.Attribute`并将其置于你的操作和控制器。 MVC 将自动发现任何`IActionConstraint`，应用作为属性。 你可用于应用程序模型应用约束，并且这可能是最灵活的方法，因为它允许您如何应用的好处。

在下面的示例约束选择相应的措施*国家/地区代码*从路线数据。 [GitHub 上完整示例](https://github.com/aspnet/Entropy/blob/dev/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs)。

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

你负责实现`Accept`方法并选择顺序约束的执行。 在这种情况下，`Accept`方法返回`true`来表示的操作是匹配项时`country`路由值匹配。 这是不同于`RouteValueAttribute`在于前者允许回退到非特性化的操作。 该示例演示是否你定义`en-US`操作再如国家/地区代码`fr-FR`将回退到不具有的更通用控制器`[CountrySpecific(...)]`应用。

`Order`属性决定哪些*阶段*约束是的一部分。 在基于组中运行操作约束`Order`。 例如，所有框架提供的 HTTP 方法特性，请使用相同`Order`值，以使它们运行在相同的阶段。 你可以根据需要来实现所需的策略的任意多个阶段。

> [!TIP]
> 若要确定的值`Order`思考之前 HTTP 方法中，是否应该应用约束。 首先运行较低的数值。
