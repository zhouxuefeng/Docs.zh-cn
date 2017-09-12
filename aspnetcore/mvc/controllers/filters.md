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
ms.openlocfilehash: b96a70a2446cab7b1af9bd689469584868980595
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="filters"></a>筛选器

通过[Tom Dykstra](https://github.com/tdykstra/)和[Steve Smith](https://ardalis.com/)

*筛选器*在 ASP.NET 核心 MVC 允许你运行代码之前或之后请求处理管道中的某些阶段。

 内置筛选器句柄任务，例如授权 （防止对资源的用户未获得授权的访问），确保所有请求都使用 HTTPS 和响应缓存 （短路要返回的缓存的响应的请求管线）。 

你可以创建自定义筛选器来处理你的应用程序的跨领域问题。 每当你想要避免跨操作重复代码，则筛选器是该解决方案。 例如，你可以合并错误处理代码异常筛选器中。

[查看或从 GitHub 下载示例](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)。

## <a name="how-do-filters-work"></a>筛选器是如何工作的？

筛选器内运行*MVC 操作调用管道*，有时称为*筛选器管道*。  筛选器管道运行后 MVC 选择要执行的操作。

![通过其他中间件、 路由中间件、 操作选择和 MVC 操作调用管道处理该请求。 请求处理过程继续返回到操作选择、 路由中间件和各种其他的中间件才会发送到客户端的响应。](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>筛选器类型

在筛选器管道中的不同阶段执行每个筛选器类型。

* [授权筛选器](#authorization-filters)运行第一个，用于确定当前用户是否被授权为当前请求。 如果请求未获授权，它们可以绕过管道。 

* [资源筛选器](#resource-filters)是最先授权后处理请求。  它们可以运行之前和之后的管道的其余部分已完成的筛选器管道的其余部分的代码。 这些控件来实施缓存或否则短路筛选器管道出于性能原因，很有用。 因为它们运行在模型绑定之前，它们可用于任何需要来影响模型绑定的内容。

* [操作筛选器](#action-filters)立即之前和之后调用了各个操作的方法，可以运行代码。 它们可以用于操作转换为一个操作传递的自变量和返回的操作的结果。

* [异常筛选器](#exception-filters)用于将全局策略应用到已向响应正文写入任何内容之前发生的未经处理异常。

* [导致筛选器](#result-filters)立即之前和之后的单个操作结果的执行，可以运行代码。 它们仅在已成功执行的操作方法时，才运行，并且可用于必须括起视图或格式化程序执行的逻辑。

下图显示了这些筛选器类型在筛选器管道中的交互方式。

![通过授权筛选器、 资源筛选器、 模型绑定，操作筛选器、 操作执行和操作结果转换、 异常筛选器，结果筛选器和结果执行处理该请求。 在路上，请求仅由处理结果筛选器和资源筛选器才会发送到客户端的响应。](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>实现

筛选器支持通过不同的接口定义的同步和异步实现。 选择的同步或异步的变体，具体取决于你需要执行的任务的类型。 

可以运行的同步筛选器的代码同时之前和之后对其管道阶段定义*阶段*执行并在*阶段*执行方法。 例如，`OnActionExecuting`之前调用的操作方法，调用和`OnActionExecuted`后操作方法会返回调用。

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/SampleActionFilter.cs?highlight=6,8,13)]

异步筛选器上定义单个*阶段*ExecutionAsync 方法。 此方法采用*FilterType*ExecutionDelegate 委托，它执行筛选器的管道阶段。 例如，`ActionExecutionDelegate`操作方法中，并且你可以执行的调用的代码之前和之后调用它。

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/SampleAsyncActionFilter.cs?highlight=6,8-10,13)]

单个类中的多个筛选器阶段，你可以实现接口。 例如， [ActionFilterAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionfilterattribute)抽象类同时实现`IActionFilter`和`IResultFilter`，以及它们的异步等效项。

> [!NOTE]
> 实现**任一**同步或筛选器接口，不能同时的异步版本。 框架会首先检查以查看如果筛选器实现异步接口，并且如果是这样，它调用的。 否则，它将调用同步接口的方法。 如果你是在一个类中实现这两个接口，将调用仅异步方法。 使用抽象类，如时[ActionFilterAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionfilterattribute)仅同步的方法或其异步方法的每个筛选器类型时，将重写。

### <a name="ifilterfactory"></a>IFilterFactory

`IFilterFactory` 可实现 `IFilter`。 因此，`IFilterFactory`实例可以用作`IFilter`筛选器管道中的任意位置的实例。 当框架准备调用筛选器时，它尝试将它转换到`IFilterFactory`。 如果该强制转换成功，`CreateInstance`调用方法来创建`IFilter`将调用的实例。 这提供了非常灵活的设计，因为无需在应用程序启动时显式设置精确的筛选器管道。

你可以实现`IFilterFactory`上创建筛选器的另一种方法为您自己属性的实现：

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

### <a name="built-in-filter-attributes"></a>内置筛选器属性

Framework 包括内置基于属性的筛选器可以子类化和自定义。 例如，以下结果筛选器添加到响应的标头。

<a name=add-header-attribute></a>

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/AddHeaderAttribute.cs?highlight=5,16)]

属性允许筛选器以接受自变量，如上面的示例中所示。 会将此属性添加到控制器或操作的方法和指定的名称和 HTTP 标头的值：

[!code-csharp[Main](./filters/sample/src/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

结果`Index`操作如下所示-响应标头显示在右下角。

![开发人员工具的 Microsoft Edge 显示响应标头，包括作者 Steve Smith@ardalis](filters/_static/add-header.png)

多个筛选器接口具有可以用于自定义实现作为基类的相应属性。

筛选器属性：

* `ActionFilterAttribute`
* `ExceptionFilterAttribute`
* `ResultFilterAttribute`
* `FormatFilterAttribute`
* `ServiceFilterAttribute`
* `TypeFilterAttribute`

`TypeFilterAttribute`和`ServiceFilterAttribute`解释了[本文稍后的](#dependency-injection)。

## <a name="filter-scopes-and-order-of-execution"></a>筛选器作用域和执行顺序

筛选器可以添加到在三个管道*作用域*。 你可以使用属性，特定的操作方法或引用了控制器类添加筛选器。 或者，也可以通过将其添加到注册全局 （用于所有控制器和操作） 的筛选器`MvcOptions.Filters`中的集合`ConfigureServices`中的方法`Startup`类：

[!code-csharp[Main](./filters/sample/src/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

### <a name="default-order-of-execution"></a>默认执行顺序

当管道一特定阶段存在多个筛选器时，作用域确定筛选器执行的默认顺序。  全局筛选器括住类筛选器，反过来环绕方法筛选器。 这有时称为"俄语玩具"嵌套层，如作用域中的每一项增加环绕以前的范围内，如[嵌套玩具](https://wikipedia.org/wiki/Matryoshka_doll)。 通常情况下，而无需显式确定排序需要获得所需的重写行为。

由于这种嵌套，*后*的筛选器的代码在相反的顺序运行*之前*代码。 序列如下所示：

* *之前*全局应用的筛选器的代码
  * *之前*应用到控制器的筛选器的代码
    * *之前*的筛选器应用于操作方法的代码
    * *后*的筛选器应用于操作方法的代码
  * *后*应用到控制器的筛选器的代码
* *后*全局应用的筛选器的代码
  
下面是一个示例，说明了为同步操作筛选器的筛选器中调用方法的顺序。

| 序列 | 筛选器作用域 | Filter 方法 |
|:--------:|:------------:|:-------------:|
| 1 | Global | `OnActionExecuting` |
| 2 | 控制器 | `OnActionExecuting` |
| 3 | 方法 | `OnActionExecuting` |
| 4 | 方法 | `OnActionExecuted` |
| 5 | 控制器 | `OnActionExecuted` |
| 6 | Global | `OnActionExecuted` |

此序列显示中的控制器筛选器，嵌套方法筛选器和控制器筛选器嵌套在全局筛选器。 将其置于另一个方法，如果你在异步筛选器的*阶段*ExecutionAsync 方法的所有具有更严格的作用域筛选器时，运行你的代码在堆栈上。

> [!NOTE]
> 继承自的每个控制器`Controller`基类包括`OnActionExecuting`和`OnActionExecuted`方法。 这些方法会包装为某个给定操作运行的筛选器：`OnActionExecuting`称为筛选器，在任何之前和`OnActionExecuted`后的所有筛选器，将调用。

### <a name="overriding-the-default-order"></a>重写默认顺序

可以通过实现重写默认的序列的执行`IOrderedFilter`。 此接口公开`Order`优先于范围，以确定执行顺序的属性。 具有较低的筛选器`Order`值将具有其*之前*早于具有较高值的筛选器执行代码`Order`。 具有较低的筛选器`Order`值将具有其*后*之后，具有更高的筛选器执行的代码`Order`值。 你可以设置`Order`通过使用构造函数参数的属性：

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

如果前面的示例，但组中所示的 3 操作筛选器具有相同`Order`属性的控制器和全局到 1 和 2 分别筛选时，将反转的执行顺序。

| 序列 | 筛选器作用域 | `Order` 属性 | Filter 方法 |
|:--------:|:------------:|:-----------------:|:-------------:|
| 1 | 方法 | 0 | `OnActionExecuting` |
| 2 | 控制器 | 1  | `OnActionExecuting` |
| 3 | Global | 2  | `OnActionExecuting` |
| 4 | Global | 2  | `OnActionExecuted` |
| 5 | 控制器 | 1  | `OnActionExecuted` |
| 6 | 方法 | 0  | `OnActionExecuted` |

`Order`属性优于作用域时确定筛选器将在其中运行的顺序。 按顺序，筛选器进行第一次排序，则将使用范围中消除并列。 所有内置筛选器实现`IOrderedFilter`和设置默认`Order`值为 0，因此作用域确定顺序，除非你设置`Order`为非零值。

## <a name="cancellation-and-short-circuiting"></a>取消和执行短路

你可以通过设置短路随时筛选器管道`Result`属性`context`提供给筛选器方法的参数。 例如，以下资源筛选器将阻止执行管道的其余部分。

<a name=short-circuiting-resource-filter></a>

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?highlight=12,13,14,15)]

在下面的代码中，同时`ShortCircuitingResourceFilter`和`AddHeader`筛选器目标`SomeResource`操作方法。 但是，因为`ShortCircuitingResourceFilter`首先运行 (因为它是资源筛选器和`AddHeader`是操作筛选器) 和会使短路的管道，rest`AddHeader`筛选器永远不会运行`SomeResource`操作。 如果两个筛选器已在提供的操作方法级别上应用此行为将相同`ShortCircuitingResourceFilter`运行第一个 (由于其筛选器类型，或显式使用`Order`属性，例如)。

[!code-csharp[Main](./filters/sample/src/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a>依赖关系注入

按类型或实例，可以添加筛选器。 如果你添加的实例，该实例将用于每个请求。 如果你添加一种类型，它会将类型激活，这意味着将为每个请求创建的实例，并且任何构造函数依赖关系将用来填充[依赖关系注入](../../fundamentals/dependency-injection.md)(DI)。 添加按类型筛选器相当于`filters.Add(new TypeFilterAttribute(typeof(MyFilter)))`。

作为属性实现并直接添加到控制器类或操作方法筛选器不能由提供的构造函数依赖关系[依赖关系注入](../../fundamentals/dependency-injection.md)(DI)。 这是因为属性必须提供在其中应用其构造函数参数。 这是属性的工作原理的限制。

如果你的筛选器从 DI 访问所需的依赖项，有几种受支持的方法。 你可以将筛选器应用到类或操作的方法，使用以下项之一：

* `ServiceFilterAttribute`
* `TypeFilterAttribute`
* `IFilterFactory`在特性上实现

> [!NOTE]
> 一个依赖关系，你可能想要获得 DI 是一个记录器。 但是，避免创建和纯粹用于日志记录，因为使用筛选器[内置框架，日志记录功能](../../fundamentals/logging.md)可能已经提供了你的需要。 如果你要将日志记录添加到你的筛选器，则它应将重点放在业务域关注点或特定于你的筛选器，而不是 MVC 操作或其他框架的事件的行为。

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

A`ServiceFilter`从 DI 检索筛选器实例。 中的容器中添加筛选器`ConfigureServices`，并引用它在`ServiceFilter`属性

[!code-csharp[Main](./filters/sample/src/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=11)]

[!code-csharp[Main](../../mvc/controllers/filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

使用`ServiceFilter`而无需注册引发异常的筛选器类型结果：

```
System.InvalidOperationException: No service for type
'FiltersSample.Filters.AddHeaderFilterWithDI' has been registered.
```

`ServiceFilterAttribute`实现`IFilterFactory`，从而将公开用于创建的单一方法`IFilter`实例。 情况下`ServiceFilterAttribute`、`IFilterFactory`接口的`CreateInstance`实现方法是为了从服务容器 (DI) 加载指定的类型。

### <a name="typefilterattribute"></a>TypeFilterAttribute

`TypeFilterAttribute`非常类似于`ServiceFilterAttribute`(还会实现`IFilterFactory`)，但其类型不能解决直接从 DI 容器。 相反，它通过实例化类型使用`Microsoft.Extensions.DependencyInjection.ObjectFactory`。

因为这一区别，使用引用的类型`TypeFilterAttribute`无需首先注册与该容器 （但仍将由容器满足其依赖项）。 此外，`TypeFilterAttribute`可以选择接受所涉及的类型的构造函数自变量。 下面的示例演示如何将自变量传递给类型使用`TypeFilterAttribute`:

[!code-csharp[Main](../../mvc/controllers/filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

如果具有筛选器不需要任何参数，但其具有需要由 DI 填写的构造函数依赖关系，你可以类和方法而不是使用你自己的命名的属性`[TypeFilter(typeof(FilterType))]`)。 下面的筛选器演示如何实现此：

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

此筛选器可以应用于类或方法使用`[SampleActionFilter]`语法，而不必使用`[TypeFilter]`或`[ServiceFilter]`。

## <a name="authorization-filters"></a>授权筛选器

*授权筛选器*控制对操作方法的访问，并且要在筛选器管道内执行的第一个筛选器。 它们只具有方法，与不同的是支持之前和之后方法的大多数筛选器之前。 仅应编写自定义授权筛选器如果你正在编写你自己的授权框架。 首选配置授权策略，或通过编写自定义筛选器编写一个自定义授权策略。 内置筛选器实现都只需负责调用授权系统。

请注意，你不应引发异常中授权筛选器，因为执行任何操作将处理的异常 （异常筛选器不会处理这些）。 请改为发出质询，或者查找另一种方法。

详细了解[授权](../../security/authorization/index.md)。

## <a name="resource-filters"></a>资源筛选器

*资源筛选器*实现`IResourceFilter`或`IAsyncResourceFilter`接口，并且其执行包装的筛选器管道的大多数。 (仅[授权筛选器](#authorization-filters)在它们之前运行。)资源筛选器是工作的特别有用，如果你需要短路大部分做请求。 例如，如果响应已在缓存中缓存的筛选器，就可避免管道的其余部分。

[短短路资源筛选器](#short-circuiting-resource-filter)前面所示是资源筛选器的一个示例。 另一个示例是[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/1.1.1/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs)，这将阻止访问窗体数据模型绑定。 它可用于你知道仍然要接收的大型文件上载并想要防止该窗体读入内存的情况。

## <a name="action-filters"></a>操作筛选器

*操作筛选器*实现`IActionFilter`或`IAsyncActionFilter`接口，并且其执行环绕操作方法的执行。

下面是示例操作筛选器：

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/SampleActionFilter.cs?name=snippet_ActionFilter)]

[ActionExecutingContext](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionexecutingcontext)提供的以下属性：

* `ActionArguments`-可操作操作的输入。
* `Controller`-可操作的控制器实例。 
* `Result`-将此值设置会使短路执行的操作方法和后续操作筛选器。 引发异常也可阻止执行的操作方法和后续的筛选器，但将被视为失败而不是一个成功结果。

[ActionExecutedContext](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionexecutedcontext)提供`Controller`和`Result`加上以下属性：

* `Canceled`-将为 true，如果操作执行已短路另一个筛选器。
* `Exception`-将为非 null，如果该操作或后续操作筛选器引发了异常。 设置此属性为 null，有效地 handles 异常，和`Result`就像它已从返回的操作方法通常将执行。

有关`IAsyncActionFilter`，调用`ActionExecutionDelegate`执行后续操作的任何筛选器和操作方法，从而返回`ActionExecutedContext`。 短路、 分配`ActionExecutingContext.Result`到某些结果实例并且不调用`ActionExecutionDelegate`。

该框架提供一个抽象`ActionFilterAttribute`可以子类化。 

操作筛选器可用于自动验证模型状态并返回任何错误，如果的状态无效：

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/ValidateModelAttribute.cs)]

`OnActionExecuted`方法运行之后的操作方法和可以查看和处理通过操作的结果`ActionExecutedContext.Result`属性。 `ActionExecutedContext.Canceled`将设置为 true，如果操作执行已短路另一个筛选器。 `ActionExecutedContext.Exception`将设置为非 null 值的操作或后续操作筛选器的任务引发异常。 设置`ActionExecutedContext.Exception`为 null，有效地 handles 异常，和`ActionExectedContext.Result`随后将如同它通常返回的操作方法的执行。

## <a name="exception-filters"></a>异常筛选器

*异常筛选器*实现`IExceptionFilter`或`IAsyncExceptionFilter`接口。 它们可以用于实现常见的错误处理的应用的策略。 

下面的示例异常筛选器将使用自定义开发人员错误视图来显示有关在开发应用程序时，可能发生的异常详细信息：

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/CustomExceptionFilterAttribute.cs?name=snippet_ExceptionFilter&highlight=1,14)]

异常筛选器不具有两个事件 （对于之前和之后）-它们仅实现`OnException`(或`OnExceptionAsync`)。 

异常筛选器处理控制器创建中发生的未经处理的异常[模型绑定](../models/model-binding.md)，操作筛选器或操作方法。 它们不会捕获资源筛选器、 结果筛选器或 MVC 结果执行中出现的异常。

若要处理的异常，设置`ExceptionContext.ExceptionHandled`属性为 true，或者编写响应。 这将停止异常的传播。 请注意异常筛选器无法打开到"成功"异常。 操作筛选器可以执行该操作。

> [!NOTE]
> 在 ASP.NET 1.1 版中，响应将不发送如果你设置`ExceptionHandled`为 true**和**编写响应。 在这种情况下，ASP.NET Core 1.0 未发送响应，和 ASP.NET Core 1.1.2 将返回到 1.0 的行为。 有关详细信息，请参阅[发出 #5594](https://github.com/aspnet/Mvc/issues/5594) GitHub 存储库中。 

异常筛选器非常适用于捕获 MVC 操作内出现的异常，但它们不是灵活性不如错误处理中间件。 首选中间件一般情况下，并使用筛选器仅需要在其中执行错误处理*以不同方式*基于的 MVC 操作已被选。 例如，你的应用程序可能具有两个 API 终结点和视图/HTML 的操作方法。 基于视图的操作可能返回为 HTML 错误页时，API 终结点无法返回为 JSON 的错误信息。

该框架提供一个抽象`ExceptionFilterAttribute`可以子类化。 

## <a name="result-filters"></a>结果筛选器

*导致筛选器*实现`IResultFilter`或`IAsyncResultFilter`接口，并且其执行环绕操作结果的执行。 

下面是结果筛选器添加一个 HTTP 标头的示例。

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

正在执行的结果的类型取决于问题的操作。 返回视图的 MVC 操作将包括所有 razor 作为的一部分处理`ViewResult`正在执行。 API 方法可能会执行某些序列化的结果的执行过程。 详细了解[操作结果](actions.md)

仅为成功结果的执行结果筛选器时的操作或操作筛选器生成操作结果。 异常筛选器处理的异常时，将不会执行结果筛选器。

`OnResultExecuting`方法可以通过设置短路执行的操作结果和后续结果筛选器`ResultExecutingContext.Cancel`为 true。 短路以避免生成了空响应时，通常应编写到响应对象。 引发异常也将阻止执行的操作结果和后续的筛选器，但将被视为失败而不是一个成功结果。

当`OnResultExecuted`方法运行时，响应可能已发送到客户端，而且不能进一步更改，（除非引发了异常）。 `ResultExecutedContext.Canceled`将设置为 true，如果操作结果执行已短路另一个筛选器。

`ResultExecutedContext.Exception`将设置为非 null 值的操作结果或后续结果筛选器的任务引发异常。 设置`Exception`到 null 有效地处理的异常和可防止在管道中更高版本中被重新引发由 MVC 异常。 当处理结果筛选器中的出现异常时，你可能不能将任何数据写入到响应。 如果通过其执行的过程引发的操作结果和标头已刷新到客户端，则没有可靠的机制，可用于发送为失败代码。

有关`IAsyncResultFilter`调用`await next()`上`ResultExecutionDelegate`执行其余的结果的任何筛选器和操作结果。 短路、 设置`ResultExecutingContext.Cancel`到 true 并且不调用`ResultExectionDelegate`。

该框架提供一个抽象`ResultFilterAttribute`可以子类化。 [AddHeaderAttribute](#add-header-attribute)类前面所示是结果筛选器特性的一个示例。

## <a name="using-middleware-in-the-filter-pipeline"></a>在筛选器管道中使用中间件

资源筛选器工作方式与类似[中间件](../../fundamentals/middleware.md)在于它们括起的所有内容的管道在更高版本附带的执行。 但筛选器不同于中间件，因为它们是 MVC，这意味着他们有权 MVC 上下文和构造的一部分。

在 ASP.NET 核心 1.1，可以在筛选器管道中使用中间件。 你可能想要执行此操作，如果你有需要访问 MVC 路由数据，或其中一个控制器或操作，应只为某些运行的中间件组件。

若要用作筛选器的中间件，创建一个与`Configure`方法，它指定你想要将注入到筛选器管道的中间件。 下面是用于建立请求的当前区域性的本地化中间件示例：

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,21)]

然后，可以使用`MiddlewareFilterAttribute`运行的中间件的所选的控制器或操作或全局范围内：

[!code-csharp[Main](./filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

在资源筛选器，在模型绑定之前和之后的管道的其余部分相同的筛选器管道阶段运行的中间件筛选器。

## <a name="next-actions"></a>下一步操作

尝试使用筛选器，[下载、 测试和修改的示例](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)。
