---
title: "使用应用程序模型"
author: ardalis
description: 
keywords: "ASP.NET Core,ASP.NET 核心 MVC，应用程序模型"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 4eb7e52f-5665-41a4-a3e3-e348d07337f2
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/application-model
ms.openlocfilehash: 3c35184921dbe26cde100fd3d5124e38ea0d06cf
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="working-with-the-application-model"></a>使用应用程序模型

通过[Steve Smith](https://ardalis.com/)

ASP.NET 核心 MVC 定义*应用程序模型*表示的 MVC 应用程序的组件。 你可以读取和处理此模型来修改 MVC 元素的行为方式。 默认情况下，MVC 遵循特定的约定来确定哪些类被视为控制器，这些类的方法是操作，以及参数和路由的行为方式。 你可以自定义此行为，以满足您的应用程序需要通过创建你自己的约定和全局或作为属性应用它们。

## <a name="models-and-providers"></a>模型和提供程序

ASP.NET 核心 MVC 应用程序模型包括抽象的接口和描述的 MVC 应用程序的具体实现类。 此模型是 MVC 发现应用程序的控制器、 操作、 操作参数、 路由以及根据默认的约定的筛选器的结果。 通过使用应用程序模型，你可以修改应用以遵循不同于默认 MVC 行为的约定。 参数、 名称、 路由和筛选器是所有用作配置数据操作和控制器。

ASP.NET 核心 MVC 应用程序模型具有以下结构：

* ApplicationModel
    * 控制器 (ControllerModel)
        * 操作 (ActionModel)
            * 参数 (ParameterModel)

每个级别的模型有权访问一组公共`Properties`集合和较低的级别可以访问并覆盖由层次结构中更高级别的设置的属性值。 属性将保存到`ActionDescriptor.Properties`何时创建操作。 然后时处理请求时，任何属性添加或修改的约定可通过访问`ActionContext.ActionDescriptor.Properties`。 使用属性是基于每个操作配置你的筛选器、 模型联编程序等的好办法。

> [!NOTE]
> `ActionDescriptor.Properties`完成后应用程序启动后，集合不是线程安全 （对于写入）。 约定是以安全地将数据添加到此集合的最佳方式。

### <a name="iapplicationmodelprovider"></a>IApplicationModelProvider

ASP.NET 核心 MVC 加载应用程序模型时使用的提供程序模式，通过定义[IApplicationModelProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider)接口。 本部分介绍如何的内部实现详细信息的一些此提供程序函数。 这是一个高级的主题-利用应用程序模型的大多数应用程序应使用约定执行此操作。

实现`IApplicationModelProvider`接口"包装"另一个，与每个实现调用`OnProvidersExecuting`以升序基于其`Order`属性。 `OnProvidersExecuted`然后按相反的顺序调用方法。 框架定义多个提供商：

第一个 (`Order=-1000`):

* [`DefaultApplicationModelProvider`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.internal.defaultapplicationmodelprovider)

然后 (`Order=-990`):

* [`AuthorizationApplicationModelProvider`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.internal.authorizationapplicationmodelprovider)
* [`CorsApplicationModelProvider`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.cors.internal.corsapplicationmodelprovider)

> [!NOTE]
> 使用相同的值的两个提供程序中的顺序`Order`称为未定义，，因此应不能依靠它。

> [!NOTE]
> `IApplicationModelProvider`是一个高级的概念工作 framework 作者为扩展。 一般情况下，应用应使用约定，并且框架应使用提供程序。 主要不同之处是提供程序始终约定之前运行。

`DefaultApplicationModelProvider`建立许多由 ASP.NET 核心 MVC 的默认行为。 其职责包括：

* 将全局筛选器添加到上下文
* 将控制器添加到上下文
* 作为操作添加公共控制器方法
* 添加到上下文的操作方法参数
* 正在应用路由和其他特性

某些内置行为实现的`DefaultApplicationModelProvider`。 此提供程序负责构造[ `ControllerModel` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel)，它又引用[ `ActionModel` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel#Microsoft_AspNetCore_Mvc_ApplicationModels_ActionModel)， [ `PropertyModel` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel)，和[`ParameterModel` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel#Microsoft_AspNetCore_Mvc_ApplicationModels_ParameterModel)实例。 `DefaultApplicationModelProvider`类是内部框架实现详细信息，可以将在以后发生更改。 

`AuthorizationApplicationModelProvider`负责应用与关联的行为`AuthorizeFilter`和`AllowAnonymousFilter`属性。 [了解有关这些属性的详细信息](xref:security/authorization/simple)。

`CorsApplicationModelProvider`实现与关联的行为`IEnableCorsAttribute`和`IDisableCorsAttribute`，和`DisableCorsAuthorizationFilter`。 [了解有关 CORS 的详细信息](xref:security/cors)。

## <a name="conventions"></a>约定

应用程序模型定义约定抽象，提供更简单的方法自定义比重写整个模型或提供程序模型的行为。 这些抽象映射是推荐的方法，以修改您的应用程序的行为。 约定提供让你可以编写将动态应用自定义项的代码的方法。 虽然[筛选器](xref:mvc/controllers/filters)提供一种修改框架的行为，自定义设置让你能够控制整个应用连接在一起。

提供了以下约定：

* [`IApplicationModelConvention`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelconvention)
* [`IControllerModelConvention`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.icontrollermodelconvention)
* [`IActionModelConvention`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.iactionmodelconvention)
* [`IParameterModelConvention`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.iparametermodelconvention)

通过将它们添加到 MVC 选项或通过实现应用约定`Attribute`s 并将它们应用到控制器、 操作或操作参数 (类似于[ `Filters` ](xref:mvc/controllers/filters))。 与筛选器，不同应用程序正在启动，不每个请求的一部分时，将仅执行约定。

### <a name="sample-modifying-the-applicationmodel"></a>示例： 修改 ApplicationModel

以下约定用于将属性添加到应用程序模型。 

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

应用程序模型约定将 MVC 添加中时应用作为选项`ConfigureServices`中`Startup`。

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

属性是可从访问`ActionDescriptor`控制器操作中的属性集合：

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

### <a name="sample-modifying-the-controllermodel-description"></a>示例： 修改 ControllerModel 说明

与前面的示例中，控制器模型还可以修改为包含自定义属性。 这些将具有相同名称的应用程序模型中指定覆盖现有的属性。 以下约定属性添加在控制器级别的说明：

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

此约定将应用作为在控制器上的属性。

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

如前面的示例所示相同的方式访问"description"属性。

### <a name="sample-modifying-the-actionmodel-description"></a>示例： 修改 ActionModel 说明

单独的属性约定可以应用于各项操作，重写已在应用程序或控制器级别应用的行为。

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

将此应用于前面的示例控制器中的一个操作演示它将控制器级别约定重写：

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

### <a name="sample-modifying-the-parametermodel"></a>示例： 修改 ParameterModel

以下约定可以应用于操作参数来修改其`BindingInfo`。 以下约定要求参数是路由参数;其他潜在的绑定源 （例如查询字符串值） 将被忽略。

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

特性可以应用于任何 action 参数：

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

### <a name="sample-modifying-the-actionmodel-name"></a>示例： 修改 ActionModel 名称

以下约定修改`ActionModel`更新*名称*应用到的操作。 作为参数传递给该属性提供了新的名称。 此新的名称使用路由，因此它将会影响使用来访问此操作方法的路由。

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

此特性应用于中的操作方法`HomeController`:

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

即使方法名称`SomeName`，该属性将覆盖使用的方法名称的 MVC 约定并替换与操作名称`MyCoolAction`。 因此，使用来访问此操作的路由是`/Home/MyCoolAction`。

> [!NOTE]
> 此示例是实质上是不同于使用内置[ActionName](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.actionnameattribute)属性。

### <a name="sample-custom-routing-convention"></a>示例： 自定义路由约定

你可以使用`IApplicationModelConvention`以自定义路由的工作方式。 例如，以下约定将将控制器的命名空间合并到其路由，替换`.`命名空间具有`/`路线中：

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

约定添加为中启动的一个选项。

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> 你可以添加到约定你[中间件](xref:fundamentals/middleware)通过访问`MvcOptions`使用`services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`

此示例适用于未使用其中控制器的名称中包含"Namespace"的属性路由的路由此约定。 以下控制器演示了此约定：

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

## <a name="application-model-usage-in-webapicompatshim"></a>应用程序模型中 WebApiCompatShim 的使用情况

ASP.NET 核心 MVC 使用 ASP.NET Web API 2 中不同的约定集。 使用自定义的约定，可以修改 ASP.NET 核心 MVC 应用程序的行为，使其与 Web API 应用的一致。 Microsoft 附带[WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/)专为此目的。

> [!NOTE]
> 详细了解[从 ASP.NET Web API 迁移](xref:migration/webapi)。

若要使用 Web API 兼容性填充码，你需要将包添加到你的项目，然后通过调用将约定添加到 MVC`AddWebApiConventions`中`Startup`:

```c#
services.AddMvc().AddWebApiConventions();
```

提供的填充码的约定仅适用于部分应用程序已应用于它们的某些属性。 控制哪些控制器应具有由填充码的约定修改其约定使用以下四个属性：

* [UseWebApiActionConventionsAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiactionconventionsattribute)
* [UseWebApiOverloadingAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapioverloadingattribute)
* [UseWebApiParameterConventionsAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiparameterconventionsattribute)
* [UseWebApiRoutesAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiroutesattribute)

### <a name="action-conventions"></a>操作约定

`UseWebApiActionConventionsAttribute`用于将 HTTP 方法映射到操作根据其名称 (例如，`Get`将映射为`HttpGet`)。 它仅适用于不使用的属性路由的操作。

### <a name="overloading"></a>重载

`UseWebApiOverloadingAttribute`用于将应用`WebApiOverloadingApplicationModelConvention`约定。 此约定将添加`OverloadActionConstraint`到操作选择过程中，这就限制了为其请求满足所有的非可选参数与候选操作。

### <a name="parameter-conventions"></a>参数约定

`UseWebApiParameterConventionsAttribute`用于将应用`WebApiParameterConventionsApplicationModelConvention`操作约定。 此约定指定，使用作为操作参数的简单类型绑定的 URI 中默认情况下，而复杂类型从请求正文绑定。

### <a name="routes"></a>路由

`UseWebApiRoutesAttribute`控件是否`WebApiApplicationModelConvention`应用控制器约定。 启用时，此约定用于添加对支持[区域](xref:mvc/controllers/areas)为该路由。

兼容性包包括一组的约定，除了`System.Web.Http.ApiController`基类来替换提供的 Web API。 这允许你为 Web API 和继承编写的控制器从其`ApiController`以按照已设计会在 ASP.NET 核心 MVC 运行时的工作。 此基本控制器类修饰的所有`UseWebApi*`上面列出的属性。 `ApiController`公开属性、 方法和与 Web API 中兼容的结果类型。

## <a name="using-apiexplorer-to-document-your-app"></a>使用 ApiExplorer 记录您的应用程序

应用程序模型公开了[ `ApiExplorer` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel)可用于遍历应用程序的结构每个级别的属性。 这可用于[为你的 Web Api 使用 Swagger 之类的工具生成的帮助页](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger)。 `ApiExplorer`属性可公开`IsVisible`属性，可设置为指定应公开您的应用程序模型中的哪些部分。 你可以配置此设置使用约定：

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

使用此方法 （和其他的约定，如果需要），可以启用或禁用在您的应用程序中的 API 在任何级别的可见性。 
