---
title: "ASP.NET 核心中的路由"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: bbbcf9e4-3c4c-4f50-b91e-175fe9cae4e2
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/routing
ms.openlocfilehash: 431b837dc93abdf305b77615409883fd54b99455
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="routing-in-aspnet-core"></a>ASP.NET 核心中的路由

通过[Ryan Nowak](https://github.com/rynowak)， [Steve Smith](https://ardalis.com/)，和[Rick Anderson](https://twitter.com/RickAndMSFT)

路由功能是负责将传入的请求映射到路由处理程序。 路由在 ASP.NET 应用程序中定义并配置应用程序启动时。 路由 （可选） 可以从包含在请求中，URL 中提取值，这些值则可以用于处理请求。 使用 ASP.NET 应用程序中的路由信息，就还能够生成映射到路由处理程序的 Url 的路由的功能。 因此，路由可以查找基于一个 URL 或对应于给定的路由处理程序基于路由处理程序信息的 URL 路由处理程序。

>[!IMPORTANT]
> 本文档介绍了较低级别的 ASP.NET Core 路由。 有关 ASP.NET 核心 MVC 路由，请参阅[路由到控制器操作](../mvc/controllers/routing.md)

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/routing/sample)

## <a name="routing-basics"></a>路由的基础知识

路由将使用*路由*(的实现[IRouter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.routing.irouter)) 到：

* 映射到的传入请求*路由处理程序*

* 生成在响应中使用的 Url

通常情况下，应用程序必须具有单个路由的集合。 当请求到达时，按顺序进行处理的路由集合。 通过调用匹配请求 URL 的路由查找传入请求`RouteAsync`路由集合中每个可用路由方法。 与此相反，响应可以使用路由以生成基于路由信息 （例如，用于重定向或链接） 的 Url，并因此避免硬编码 url，它可帮助可维护性。

路由连接到[中间件](middleware.md)按管道`RouterMiddleware`类。 [ASP.NET MVC](../mvc/overview.md)添加路由到中间件管道，其配置的一部分。 若要了解有关使用路由作为独立组件，请参阅[使用的路由的中间件](#using-routing-middleware)。

<a name=url-matching-ref></a>

### <a name="url-matching"></a>匹配 URL

匹配 URL 是通过哪些路由调度传入请求到的过程*处理程序*。 此过程通常基于 URL 路径中的数据，但可以对其进行扩展，以考虑请求中的任何数据。 调度到单独的处理程序的请求的功能是用于缩放的大小和复杂性的应用程序密钥。

传入的请求输入`RouterMiddleware`，哪些调用`RouteAsync`序列中的每个路由方法。 `IRouter`实例选择是否*处理*通过设置请求`RouteContext``Handler`为非 null `RequestDelegate`。 如果路由设置的处理程序请求，将调用路由处理停止点和处理程序来处理该请求。 如果尝试所有路由并且没有处理程序发现请求，则该中间件将调用*下一步*和调用请求管道中的下一步中间件。

主要输入`RouteAsync`是`RouteContext``HttpContext`与当前请求相关联。 `RouteContext.Handler`和`RouteContext``RouteData`会将路由匹配后的输出。

在匹配`RouteAsync`还将设置的属性`RouteContext.RouteData`为适当的值根据到目前为止完成的请求处理。 如果路由与匹配的请求，`RouteContext.RouteData`将包含重要的状态信息有关*结果*。

`RouteData``Values`是一个字典的*路由值*路由时生成。 这些值通常由标记化 URL，并且可以用于接受用户输入，或进一步调度决定对应用程序中。

`RouteData``DataTokens`匹配路由到相关的其他数据是属性包。 `DataTokens`可用于支持将数据与每个路由，以便应用程序可以基于做出决策更高版本上哪个路由匹配的状态。 这些值是开发人员定义，并且**不**影响的行为的任何方式中的路由。 此外，存储在数据令牌中的值可以是任何类型，与路由值，必须能够轻松地转换到和从字符串。

`RouteData``Routers`是花费在成功匹配的请求的一部分的路由的列表。 路由可以嵌套在另一个，和`Routers`属性反映通过导致匹配的路由逻辑树的路径。 第一项通常`Routers`路由集合中，以及应该用于 URL 生成。 中的最后一项`Routers`是匹配的路由处理程序。

### <a name="url-generation"></a>URL 生成

URL 生成是过程通过哪个路由，则可以创建基于一组路由值的 URL 路径。 这使得您的处理程序和对其进行访问的 Url 之间的逻辑分隔。

URL 生成遵循类似的迭代过程，但开头用户或框架代码调用到`GetVirtualPath`的路由集合的方法。 每个*路由*都具有其`GetVirtualPath`序列中非 null 之前调用方法`VirtualPathData`返回。

主输入到`GetVirtualPath`是：

* `VirtualPathContext` `HttpContext`

* `VirtualPathContext` `Values`

* `VirtualPathContext` `AmbientValues`

路由主要使用路由值由提供`Values`和`AmbientValues`来确定其所在可以生成的 URL 和要包括的值。 `AmbientValues`是生成从匹配路由系统的当前请求的路由值的一组。 与此相反，`Values`是路由的值，指定如何生成当前操作所需的 URL。 `HttpContext`路由需要获取服务或与当前上下文关联的其他数据的情况下提供。

提示： 将`Values`为一组的替代`AmbientValues`。 URL 生成尝试重复使用从当前的请求，以便可以方便地生成使用相同的路由或路由值的链接的 Url 的路由值。

输出`GetVirtualPath`是`VirtualPathData`。 `VirtualPathData`是一种并行的`RouteData`; 它包含`VirtualPath`输出 URL 以及应该通过路由将设置中的某些其他属性。

`VirtualPathData` `VirtualPath`属性包含*虚拟路径*生成的路由。 具体取决于你的需求可能需要处理更多的路径。 例如，如果你想要呈现 HTML 中生成的 URL 需要预先计算的应用程序的基路径。

`VirtualPathData` `Router`是对已成功生成 URL 的路由的引用。

`VirtualPathData` `DataTokens`属性是生成 URL 的路由到相关的其他数据的字典。 这是并行的`RouteData.DataTokens`。

### <a name="creating-routes"></a>创建的路由

路由提供`Route`作为标准的实现类`IRouter`。 `Route`使用*路由模板*语法来定义将对 URL 路径匹配的模式时`RouteAsync`调用。 `Route`将使用相同的路由模板生成的 URL 时`GetVirtualPath`调用。

大多数应用程序将通过调用创建路由`MapRoute`或类似上定义的扩展方法之一`IRouteBuilder`。 所有这些方法将创建的实例`Route`并将其添加到路由集合。

注意：`MapRoute`不带路由处理程序参数-它只是添加将由处理的路由`DefaultHandler`。 由于默认处理程序是`IRouter`，它可能决定不处理该请求。 例如，ASP.NET MVC 通常配置为仅处理的默认处理请求该匹配项，可用控制器和操作。 若要了解有关路由到 MVC 的详细信息，请参阅[路由到控制器操作](../mvc/controllers/routing.md)。

这是一个示例的`MapRoute`使用典型的 ASP.NET MVC 路由定义的调用：

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

此模板将匹配类似 URL 路径`/Products/Details/17`和提取路由值`{ controller = Products, action = Details, id = 17 }`。 路由值确定通过将 URL 路径拆分为段，以及与每个段进行匹配*路由参数*路由模板中的名称。 路由参数的名称。 它们由括在大括号中的参数名称定义`{ }`。

上述模板还无法与匹配的 URL 路径`/`，会产生值`{ controller = Home, action = Index }`。 这是因为`{controller}`和`{action}`路由参数具有默认值和`id`路由参数是可选的。 等于`=`符号后路由参数名称定义参数的默认值后跟一个值。 问号`?`路由参数名称定义为可选参数之后。 将路由使用的默认值的参数*始终*当路由与匹配-可选参数将不会生成一个路由值，如果没有相应的 URL 路径段时产生路由值。

请参阅[路由模板引用](#route-template-reference)的路由模板功能和语法的详尽描述。

该示例包括*路由约束*:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

此模板将匹配类似 URL 路径`/Products/Details/17`，但不是`/Products/Details/Apples`。 路由参数定义`{id:int}`定义*路由约束*为`id`路由参数。 路由约束实现`IRouteConstraint`并检查路由以验证它们的值。 在此示例中的路由值`id`必须可转换为整数。 请参阅[路由约束引用](#route-constraint-reference)有关 framework 提供的路由约束的更多详细说明。

其他重载`MapRoute`接受值`constraints`， `dataTokens`，和`defaults`。 这些附加参数的`MapRoute`定义为类型`object`。 这些参数的典型用法是传递的匿名类型化的对象，其中的匿名类型匹配的属性名称将参数名称的路由。

下面的两个示例创建等效路由：

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

提示： 内联语法来定义约束和默认值可能会更方便简单路由。 但是，存在一些功能如内联语法不支持数据令牌。

此示例演示几个更多的功能：

```csharp
routes.MapRoute(
  name: "blog",
  template: "Blog/{*article}",
  defaults: new { controller = "Blog", action = "ReadArticle" });
```

此模板将匹配类似 URL 路径`/Blog/All-About-Routing/Introduction`和会提取值`{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`。 默认路由值`controller`和`action`即使模板中没有相应的路由参数生成的路由。 在路由模板中，可以指定默认值。 `article`路由参数定义为*全方位*星号的外观由`*`之前路由参数名称。 全部捕捉路由参数捕获的 URL 路径的其余部分，还可以匹配空字符串。

此示例将添加路由约束和数据令牌：

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

此模板将匹配类似 URL 路径`/Products/5`和会提取值`{ controller = Products, action = Details, id = 5 }`和数据令牌`{ locale = en-US }`。

![局部变量 Windows 令牌](routing/_static/tokens.png)

<a name=id1></a>

### <a name="url-generation"></a>URL 生成

`Route`类还可以通过将其路由模板与结合使用的一组路由值执行 URL 生成。 此逻辑上是匹配的 URL 路径的反向过程。

提示： 若要更好地了解 URL 生成，假设你想要生成，然后考虑如何路由模板将匹配该 URL 哪些的 URL。 将生成值？ 这是大致相当于 URL 生成中的工作方式`Route`类。

此示例使用一个基本的 ASP.NET MVC 样式路由：

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

使用路由值`{ controller = Products, action = List }`，此路由将生成 URL `/Products/List`。 路由值将替换为相应的路由参数，以形成的 URL 路径的。 由于`id`是可选的路由参数，则它不具有值没问题。

使用路由值`{ controller = Home, action = Index }`，此路由将生成 URL `/`。 路由值提供匹配的默认值，因此可以安全地忽略这些值所对应的段。 请注意，生成两个 Url 将与此路由定义往返，生成用于生成 URL 的路由相同的值。

提示： 使用 ASP.NET MVC 应用程序应使用`UrlHelper`生成 Url 而不是直接路由到的调用。

有关 URL 生成过程的详细信息，请参阅[url 生成引用](#url-generation-reference)。

## <a name="using-routing-middleware"></a>使用路由的中间件

添加 NuGet 包"Microsoft.AspNetCore.Routing"。

添加路由到的服务容器中*Startup.cs*:

[!code-csharp[Main](../fundamentals/routing/sample/RoutingSample/Startup.cs?highlight=3&start=11&end=14)]

必须在配置路由`Configure`中的方法`Startup`类。 下面的示例使用这些 Api:

* `RouteBuilder`
* `Build`
* `MapGet`匹配仅 HTTP GET 请求
* `UseRouter`

<!-- literal_block {"xml:space": "preserve", "source": "fundamentals/routing/sample/RoutingSample/Startup.cs", "ids": [], "linenos": false, "highlight_args": {"linenostart": 1}} -->

```csharp
public void Configure(IApplicationBuilder app, ILoggerFactory loggerFactory)
{
    var trackPackageRouteHandler = new RouteHandler(context =>
    {
        var routeValues = context.GetRouteData().Values;
        return context.Response.WriteAsync(
            $"Hello! Route values: {string.Join(", ", routeValues)}");
    });

    var routeBuilder = new RouteBuilder(app, trackPackageRouteHandler);

    routeBuilder.MapRoute(
        "Track Package Route",
        "package/{operation:regex(^(track|create|detonate)$)}/{id:int}");

    routeBuilder.MapGet("hello/{name}", context =>
    {
        var name = context.GetRouteValue("name");
        // This is the route handler when HTTP GET "hello/<anything>"  matches
        // To match HTTP GET "hello/<anything>/<anything>,
        // use routeBuilder.MapGet("hello/{*name}"
        return context.Response.WriteAsync($"Hi, {name}!");
    });

    var routes = routeBuilder.Build();
    app.UseRouter(routes);
}
```

下表显示了具有给定的 Uri 的响应。

| URI | 响应  |
| ------- | -------- |
| /package/create/3  | Hello! 将路由值: [操作，创建]，[id，3] |
| / 包/跟踪/3  | Hello! 将路由值: [操作，跟踪]，[id，-3] |
| / 包/跟踪/3 / | Hello! 将路由值: [操作，跟踪]，[id，-3]  |
| /package/跟踪 / | \<贯穿到任何匹配项 > |
| 获取 /hello/Joe | 您好，Joe ！ |
| POST /hello/Joe | \<贯穿，仅匹配 HTTP GET > |
| 获取 /hello/Joe/Smith | \<贯穿到任何匹配项 > |

如果你要配置一个路由，调用`app.UseRouter`传入`IRouter`实例。 你无需调用`RouteBuilder`。

框架提供了一套例如创建的路由的扩展方法：

* `MapRoute`
* `MapGet`
* `MapPost`
* `MapPut`
* `MapDelete`
* `MapVerb`

如这些方法的一些`MapGet`需要`RequestDelegate`提供。 `RequestDelegate`将用作*路由处理程序*路由匹配时。 此系列中的其他方法允许配置的中间件管道，后者将用作路由处理程序。 如果*映射*方法不接受一个处理程序，例如`MapRoute`，则它将使用`DefaultHandler`。

`Map[Verb]`方法使用约束来限制到中的方法名称的 HTTP 谓词的路由。 有关示例，请参阅[MapGet](https://github.com/aspnet/Routing/blob/1.0.0/src/Microsoft.AspNetCore.Routing/RequestDelegateRouteBuilderExtensions.cs#L85-L88)和[MapVerb](https://github.com/aspnet/Routing/blob/1.0.0/src/Microsoft.AspNetCore.Routing/RequestDelegateRouteBuilderExtensions.cs#L156-L180)。

## <a name="route-template-reference"></a>路由模板参考

在大括号内的令牌 (`{ }`) 定义*路由参数*后者将绑定当找到匹配项路由。 你可以定义多个路由参数路由段中，但它们必须按某一文字值分隔。 例如`{controller=Home}{action=Index}`将不是有效的路由，因为没有文本值之间`{controller}`和`{action}`。 这些路由参数必须具有一个名称，并且可以指定其他属性。

路由参数以外的文字文本 (例如， `{id}`) 和路径分隔符`/`必须匹配在 URL 中的文本。 文本匹配是不区分大小写并且基于的已解码的表示形式的 Url 路径。 若要匹配的文本的路由参数分隔符`{`或`}`，通过重复字符对其进行转义 (`{{`或`}}`)。

来尝试捕获带可选文件扩展名的文件名的 URL 模式有其他注意事项。 例如，使用模板`files/{filename}.{ext?}`-当同时`filename`和`ext`存在，则将填充这两个值。 如果仅`filename`因为 URL 路由匹配项中存在尾随句点`.`是可选的。 以下 Url 将与此路由匹配：

* `/files/myFile.txt`
* `/files/myFile.`
* `/files/myFile`

你可以使用`*`作为前缀字符到路由参数以绑定到的 URI 的 rest 调用此*全方位*参数。 例如，`blog/{*slug}`将匹配入门任何 URI`/blog`且必须跟在它后面的任何值 (其将分配到`slug`路由值)。 全部捕获参数还可以匹配空字符串。

路由参数可能具有*默认值*、 通过隔开的参数名称后指定默认值指定`=`。 例如，`{controller=Home}`将定义`Home`的默认值为`controller`。 如果没有值存在在 URL 中的参数，则使用默认值。 默认值，除了路由参数可能是可选的 (指定将追加`?`到参数名称，如下所示的末尾`id?`)。 可选之间的差异和"具有默认"是默认值的路由参数始终会生成一个值; 如果仅在一个提供时，一个可选参数将具有的值。

路由参数也可能具有必须与绑定从 URL 路由值匹配的约束。 添加冒号`:`和约束名称后的路由参数名称指定*内联约束*路由参数。 如果约束需要那些提供括在括号中的参数`( )`约束名称后面。 要指定多个内联约束，只需将追加另一个冒号`:`和约束名称。 约束名称传递给`IInlineConstraintResolver`服务创建的实例`IRouteConstraint`要在 URL 处理中使用。 例如，路由模板`blog/{article:minlength(10)}`指定`minlength`使用参数的约束`10`。 详细说明路由约束和由框架提供的约束的列表，请参阅[路由约束引用](#route-constraint-reference)。

下表演示某些路由模板和它们的行为。

| 路由模板 | 示例匹配 URL | 说明 |
| -------- | -------- | ------- |
| hello  | /hello  | 仅与单个路径相匹配`/hello` |
| {页主页 =} | / | 匹配和设置`Page`到`Home` |
| {页主页 =}  | / 联系人  | 匹配和设置`Page`到`Contact` |
| {controller} / {action} / {id}？ | / 产品/列表 | 映射到`Products`控制器和`List`操作 |
| {controller} / {action} / {id}？ | / 产品/详细信息/123  |  映射到`Products`控制器和`Details`操作。  `id`设置为 123 |
| {控制器主页 =} / {操作 = 索引} / {id}？ | /  |  映射到`Home`控制器和`Index`方法;`id`将被忽略。 |

使用模板通常是路由的最简单方法。 此外可以在路由模板外指定约束和默认值。

提示： 启用[日志记录](logging.md)若要查看如何在路由实现中，如生成`Route`，匹配的请求。

## <a name="route-constraint-reference"></a>路由约束引用

路由约束时执行`Route`具有匹配传入 URL 的语法和标记化为路由值的 URL 路径。 通常，路由约束检查通过路由模板关联的路由值，并且进行简单是/否决策有关的值是可接受。 某些路由约束使用的路由值之外的数据要考虑是否可以路由请求。 例如，`HttpMethodRouteConstraint`可以接受或拒绝基于其 HTTP 谓词的请求。

>[!WARNING]
> 避免使用约束**输入验证**，因为这样做意味着该无效的输入将导致 404 （未找到） 而不是与相应的错误消息显示 400。 路由约束应能用于对**消除歧义**之间相似的路由，无法验证为特定的路由的输入。

下表演示一些路由约束和其预期的行为。

| 约束 | 示例 | 示例匹配项 | 说明 |
| --------   | ------- | ------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789`  | 任何整数匹配 |
| `bool`  | `{active:bool}` | `true`, `FALSE` | 匹配`true`或`false`（不区分大小写） |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm`  | 匹配有效`DateTime`值 （在固定区域性的看到警告） |
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | 匹配有效`decimal`值 （在固定区域性的看到警告） |
| `double`  | `{weight:double}` | `1.234`, `-1,001.01e8` | 匹配有效`double`值 （在固定区域性的看到警告） |
| `float`  | `{weight:float}` | `1.234`, `-1,001.01e8` | 匹配有效`float`值 （在固定区域性的看到警告） |
| `guid`  | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | 匹配有效`Guid`值 |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | 匹配有效`long`值 |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | 字符串必须至少 4 个字符 |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | 字符串必须是不能超过 8 个字符 |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | 字符串必须是完全 12 个字符之间 |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | 字符串必须至少 8 并且不超过 16 个字符 |
| `min(value)` | `{age:min(18)}` | `19` | 整数值必须至少 18 |
| `max(value)` | `{age:max(120)}` |  `91` | 整数值必须不能超过 120 |
| `range(min,max)` | `{age:range(18,120)}` | `91` | 整数值必须至少 18 但不是能超过 120 |
| `alpha` | `{name:alpha}` | `Rick` | 字符串必须包含一个或多个字母字符 (`a`-`z`、 不区分大小写) |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | 字符串必须匹配正则表达式 （参见提示有关定义正则表达式） |
| `required`  | `{name:required}` | `Rick` |  用于强制非参数值在 URL 生成过程中存在 |

>[!WARNING]
> 验证的 URL 的路由约束可以转换为 CLR 类型 (如`int`或`DateTime`) 始终使用固定区域性-它们采用的 URL 是不可本地化。 框架提供的路由约束不要修改存储在路由值的值。 从 URL 解析所有路由值将都存储为字符串。 例如， [Float 路由约束](https://github.com/aspnet/Routing/blob/1.0.0/src/Microsoft.AspNetCore.Routing/Constraints/FloatRouteConstraint.cs#L44-L60)将尝试将路由值转换为浮点值，但转换后的值仅用于验证它可以转换为浮点数。

## <a name="regular-expressions"></a>正则表达式 

ASP.NET 核心框架就会将`RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant`到正则表达式构造函数。 请参阅[RegexOptions 枚举](https://docs.microsoft.com/dotnet/api/system.text.regularexpressions.regexoptions)有关这些成员的说明。

正则表达式使用分隔符和类似于所使用的路由和 C# 语言的令牌。 正则表达式令牌必须进行转义。 例如，若要使用的正则表达式`^\d{3}-\d{2}-\d{4}$`中路由，它需要用`\`以的键入的字符`\\`C# 源文件中进行转义`\`字符串转义字符 (除非使用[逐字字符串文本](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/string)。 `{` ， `}` ，[和] 字符是需要将它们路由参数分隔符字符进行转义加倍进行转义。  下表显示了正则表达式和转义的版本。

| Expression               | 说明 |
| ----------------- | ------------ | 
| `^\d{3}-\d{2}-\d{4}$` | 正则表达式 |
| `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` | 转义  |
| `^[a-z]{2}$` | 正则表达式 |
| `^[[a-z]]{{2}}$` | 转义  |

在路由中使用的正则表达式通常开头`^`字符 （匹配字符串的起始位置） 和以结尾`$`字符 （匹配结束的字符串的位置）。 `^`和`$`字符，请确保正则表达式匹配的整个路由参数值。 而无需`^`和`$`字符的正则表达式将匹配在字符串中，这通常不是你所希望的任何子字符串。 下表显示了一些示例，并说明为何它们匹配，否则失败以匹配。

| Expression               | 字符串 | 匹配 | 注释 |
| ----------------- | ------------ |  ------------ |  ------------ | 
| `[a-z]{2}` | hello | 是 | 匹配的子字符串 |
| `[a-z]{2}` | 123abc456 | 是 | 匹配的子字符串 |
| `[a-z]{2}` | mz | 是 | 匹配表达式 |
| `[a-z]{2}` | MZ | 是 | 不区分大小写 |
| `^[a-z]{2}$` |  hello | no | 请参阅`^`和`$`上面 |
| `^[a-z]{2}$` |  123abc456 | no | 请参阅`^`和`$`上面 |

请参阅[.NET Framework 正则表达式](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference)有关正则表达式语法的详细信息。

若要将限制为一组已知的可能的值的参数，使用正则表达式。 例如`{action:regex(^(list|get|create)$)}`仅匹配`action`将路由到的值`list`， `get`，或`create`。 如果传递到约束字典，字符串"^ (列表 | get | 创建) $"将等效。 将传入约束字典 （不在模板中的内联） 不匹配的已知的约束之一的约束也被视为正则表达式。

## <a name="url-generation-reference"></a>URL 生成引用

下面的示例演示如何生成给定路由值的字典的路由的链接和`RouteCollection`。

[!code-csharp[Main](../fundamentals/routing/sample/RoutingSample/Startup.cs?range=45-59)]

`VirtualPath`生成在上面的示例末尾`/package/create/123`。

第二个参数`VirtualPathContext`构造函数是一套*环境值*。 环境值通过限制一名开发人员必须在某些请求上下文中指定的值的数目提供方便。 当前请求的当前路由值被视为链接生成的环境的值。 例如，在 ASP.NET MVC 应用程序，如果你在`About`操作`HomeController`，不需要指定要链接到的控制器路由值`Index`操作 (的环境值`Home`将使用)。

环境不匹配参数的值将被忽略，和环境的值也将被忽略，当显式提供的值将重写它，将从左到右在 URL 中。

显式提供，但不匹配的任何值将添加到查询字符串。 下表显示结果时使用的路由模板`{controller}/{action}/{id?}`。

| 环境值 | 显式值 | 结果 |
| -------------   | -------------- | ------ |
| 控制器 ="主页" | 操作 ="关于" | `/Home/About` |
| 控制器 ="主页" | 控制器 ="Order"，操作 ="关于" | `/Order/About` |
| 控制器 ="主页"，颜色 ="Red" | 操作 ="关于" | `/Home/About` |
| 控制器 ="主页" | 操作 ="关于"，颜色 ="Red" | `/Home/About?color=Red`

如果路由不对应于参数的默认值而显式提供此值，它必须匹配的默认值。 例如: 

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
  defaults: new { controller = "Blog", action = "ReadPost" });
```

提供控制器和操作的匹配值时，链接生成仅会生成此路由链接。
