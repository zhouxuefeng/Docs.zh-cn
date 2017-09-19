---
title: "在 ASP.NET 核心中的应用程序部分"
author: ardalis
description: "了解如何使用应用程序部件，即 abstrations 对应用程序中的资源，来配置应用程序发现或避免从程序集加载功能。"
keywords: "ASP.NET 核心，应用程序部件、 应用程序一部分"
ms.author: riande
manager: wpickett
ms.date: 01/04/2017
ms.topic: article
ms.assetid: b355a48e-a15c-4d58-b69c-899963613a98
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 77d3a58d58493bf1b0b760ab9037d2778ba23441
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2017
---
# <a name="application-parts-in-aspnet-core"></a>在 ASP.NET 核心中的应用程序部分

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample)

*应用程序部分*是上的应用程序，MVC 控制器，视图组件等从中的功能的资源的抽象或标记帮助程序可能被发现。 应用程序一部分的一个示例是 AssemblyPart，封装程序集引用和公开类型和编译引用。 *功能提供程序*适用于要填充的 ASP.NET 核心 MVC 应用程序的功能的应用程序部件。 应用程序部件的主要用例是允许你配置应用程序发现 （或避免加载） 从程序集的 MVC 功能。

## <a name="introducing-application-parts"></a>引入应用程序部分

MVC 应用程序加载从其功能[应用程序部件](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart)。 具体而言， [AssemblyPart](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart)类表示由程序集的应用程序部分。 可以使用这些类能够发现和加载 MVC 功能，例如控制器、 视图组件、 标记帮助程序和 razor 编译源。 [ApplicationPartManager](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager)负责跟踪对 MVC 应用程序的应用程序部件和可用功能提供程序。 你可以与交互`ApplicationPartManager`中`Startup`配置 MVC 时：

```csharp
// create an assembly part from a class's assembly
var assembly = typeof(Startup).GetTypeInfo().Assembly;
services.AddMvc()
    .AddApplicationPart(assembly);

// OR
var assembly = typeof(Startup).GetTypeInfo().Assembly;
var part = new AssemblyPart(assembly);
services.AddMvc()
    .ConfigureApplicationPartManager(apm => p.ApplicationParts.Add(part));
```

默认情况下 MVC 将搜索依赖关系树并查找控制器 （即使其他程序集）。 若要加载 （例如，来自不引用在编译时的插件） 的任意程序集，可以使用应用程序部分。

你可以使用应用程序部分*避免*查找特定的程序集或位置中的控制器。 你可以控制哪些部分 （或程序集） 可以应用可通过修改`ApplicationParts`集合`ApplicationPartManager`。 中的项的顺序`ApplicationParts`集合并不重要。 务必要完全配置`ApplicationPartManager`然后使用它来配置容器中的服务。 例如，你应完全配置`ApplicationPartManager`之前调用`AddControllersAsServices`。 未能这样做，请将意味着，应用程序部分中的控制器之后添加方法调用不会受到影响 （将不获取注册作为服务） 这可能导致不正确 bevavior 的你的应用程序。

如果你有该程序集包含不想要使用控制器，则将其从删除`ApplicationPartManager`:

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(p =>
    {
        var dependentLibrary = p.ApplicationParts
            .FirstOrDefault(part => part.Name == "DependentLibrary");

        if (dependentLibrary != null)
        {
           p.ApplicationParts.Remove(dependentLibrary);
        }
    })
```

除了你的项目的程序集和其依赖的程序集，`ApplicationPartManager`将包括的部件`Microsoft.AspNetCore.Mvc.TagHelpers`和`Microsoft.AspNetCore.Mvc.Razor`默认情况下。

## <a name="application-feature-providers"></a>应用程序功能提供程序

应用程序功能提供程序检查应用程序部分，并为那些部分中提供的功能。 有于以下 MVC 功能的内置功能提供程序：

* [控制器](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [元数据引用](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.razor.compilation.metadatareferencefeatureprovider)
* [标记帮助程序](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [查看组件](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

功能提供程序从继承`IApplicationFeatureProvider<T>`，其中`T`是一种功能。 你可以实现你自己上面列出的任一 MVC 的特征类型提供程序的功能。 功能中的提供程序的顺序`ApplicationPartManager.FeatureProviders`集合可能很重要，因为更高版本的提供程序可以响应以前的提供程序执行操作。

### <a name="sample-generic-controller-feature"></a>示例： 泛型控制器功能

默认情况下，ASP.NET 核心 MVC 会忽略泛型控制器 (例如， `SomeController<T>`)。 此示例使用控制器的功能提供程序，在默认的提供程序后运行，并添加有关指定列表的类型的泛型控制器实例 (在中定义`EntityTypes.Types`):

[!code-csharp[Main](./app-parts/sample/AppPartsSample/GenericControllerFeatureProvider.cs?highlight=13&range=18-36)]

实体类型中：

[!code-csharp[Main](./app-parts/sample/AppPartsSample/Model/EntityTypes.cs?range=6-16)]

在中添加功能提供程序`Startup`:

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(p => 
        p.FeatureProviders.Add(new GenericControllerFeatureProvider()));
```

默认情况下，用于路由的泛型控制器名称将在窗体*GenericController'1 [小组件]*而不是*小组件*。 以下属性用于修改要对应于控制器使用的泛型类型的名称：

[!code-csharp[Main](./app-parts/sample/AppPartsSample/GenericControllerNameConvention.cs)]

`GenericController`类：

[!code-csharp[Main](./app-parts/sample/AppPartsSample/GenericController.cs?highlight=5-6)]

这样，当请求匹配的路由：

![输出示例应用中的示例将读取，Hello from 泛型 Sproket 控制器。](app-parts/_static/generic-controller.png)

### <a name="sample-display-available-features"></a>示例： 显示可用功能

你可循环访问提供的填充功能向应用程序通过请求`ApplicationPartManager`通过[依赖关系注入](../../fundamentals/dependency-injection.md)并使用它来填充相应功能的实例：

[!code-csharp[Main](./app-parts/sample/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

示例输出：

![示例应用中的示例输出](app-parts/_static/available-features.png)
