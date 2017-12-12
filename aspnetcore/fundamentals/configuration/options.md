---
title: "在 ASP.NET Core 选项模式"
author: guardrex
description: "了解如何使用选项模式来表示 ASP.NET Core 应用中的相关设置的组。"
ms.author: riande
manager: wpickett
ms.custom: mvc
ms.date: 11/28/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/configuration/options
ms.openlocfilehash: 7d89416626433bf737b63eda4b17e65b089ae142
ms.sourcegitcommit: 8f42ab93402c1b8044815e1e48d0bb84c81f8b59
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="options-pattern-in-aspnet-core"></a>在 ASP.NET Core 选项模式

作者：[Luke Latham](https://github.com/guardrex)

选项模式使用选项类代表一组相关的设置。 当配置设置按功能隔离到单独的选项类别时，应用程序遵循两个重要软件工程原则：

* [接口分隔原则 (ISP)](http://deviq.com/interface-segregation-principle/)： 依赖于配置设置的功能 （类） 仅取决于它们使用的配置设置。
* [关注点分离](http://deviq.com/separation-of-concerns/)： 应用程序的不同部分的设置不依赖或耦合到另一个。

[查看或下载的示例代码](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/options/sample)([如何下载](xref:tutorials/index#how-to-download-a-sample)) 此文章是更轻松地遵循与示例应用程序。

## <a name="basic-options-configuration"></a>基本选项配置

作为示例演示了基本选项配置&num;1[示例应用程序](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/options/sample)。

非抽象类必须是选项类具有一个公共的无参数构造函数。 下面的类， `MyOptions`，具有两个属性，`Option1`和`Option2`。 设置默认值是可选的但在以下示例中的类构造函数设置的默认值`Option1`。 `Option2`已通过直接初始化的属性设置了默认值 (*Models/MyOptions.cs*):

[!code-csharp[Main](options/sample/Models/MyOptions.cs?name=snippet1)]

`MyOptions`类添加到的服务容器[IConfigureOptions&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1)并将其绑定到配置：

[!code-csharp[Main](options/sample/Startup.cs?name=snippet_Example1)]

以下页上的模型使用[构造函数依赖关系注入](xref:fundamentals/dependency-injection#what-is-dependency-injection)与[IOptions&lt;TOptions&gt; ](/dotnet/api/Microsoft.Extensions.Options.IOptions-1)来访问这些设置 (*Pages/Index.cshtml.cs*):

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet_Example1)]

示例的*appsettings.json*文件指定的值`option1`和`option2`:

[!code-json[Main](options/sample/appsettings.json)]

运行该应用程序，页模型`OnGet`方法返回显示选项类值的字符串：

```html
option1 = value1_from_json, option2 = -1
```

## <a name="configure-simple-options-with-a-delegate"></a>使用委托配置简单的选项

使用委托配置简单的选项的示例演示&num;2 英寸[示例应用程序](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/options/sample)。

使用委托来设置选项值。 此示例应用程序使用`MyOptionsWithDelegateConfig`类 (*Models/MyOptionsWithDelegateConfig.cs*):

[!code-csharp[Main](options/sample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

在下面的代码中，第二个`IConfigureOptions<TOptions>`服务添加到服务容器。 它使用委托来配置的绑定`MyOptionsWithDelegateConfig`:

[!code-csharp[Main](options/sample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet_Example2)]

你可以添加多个配置提供程序。 配置提供程序 NuGet 包中。 以便将它们注册要应用这些策略。

每次调用[配置&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1.configure)添加`IConfigureOptions<TOptions>`服务到服务容器。 在前面的示例中，值`Option1`和`Option2`同时中指定*appsettings.json*，但值`Option1`和`Option2`重写了配置的委托。

当启用多个配置服务时，指定上次的配置源*wins*和设置配置值。 运行该应用程序，页模型`OnGet`方法返回显示选项类值的字符串：

```html
delegate_option1 = value1_configured_by_delgate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Suboptions 配置

作为示例演示了 suboptions 配置&num;中的 3[示例应用程序](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/options/sample)。

应用程序应创建适用于应用中的特定功能组 （类） 的选项类别。 部分应用程序需要配置值仅应有权访问他们使用的配置值。

当绑定到配置选项，在选项类型的每个属性绑定到窗体的配置键`property[:sub-property:]`。 例如，`MyOptions.Option1`属性绑定到键`Option1`，这从读取`option1`中的属性*appsettings.json*。

在下面的代码中，第三个`IConfigureOptions<TOptions>`服务添加到服务容器。 它将绑定`MySubOptions`的部分`subsection`的*appsettings.json*文件：

[!code-csharp[Main](options/sample/Startup.cs?name=snippet_Example3)]

`GetSection`扩展方法要求[Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet 包。 如果应用使用[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All/) metapackage，包将自动包含。

示例的*appsettings.json*文件定义`subsection`具有键成员`suboption1`和`suboption2`:

[!code-json[Main](options/sample/appsettings.json?highlight=4-7)]

`MySubOptions`类定义属性，属性`SubOption1`和`SubOption2`，以保留子选项值 (*Models/MySubOptions.cs*):

[!code-csharp[Main](options/sample/Models/MySubOptions.cs?name=snippet1)]

页模型`OnGet`方法返回与子选项值的字符串 (*Pages/Index.cshtml.cs*):

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet_Example3)]

当应用运行时，`OnGet`方法返回字符串，它显示类值的子选项：

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a>通过视图模型或直接视图注入与提供的选项

通过视图模型或直接视图注入与提供的选项的示例演示&num;4 英寸[示例应用程序](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/options/sample)。

可以提供选项，在视图模型或通过将注入`IOptions<TOptions>`直接到视图 (*Pages/Index.cshtml.cs*):

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet_Example4)]

为直接注入注入`IOptions<MyOptions>`与`@inject`指令：

[!code-cshtml[Main](options/sample/Pages/Index.cshtml?range=1-10&highlight=5)]

当应用运行时，选项值显示在呈现的页面：

![选项值选项 1: value1_from_json 和选项 2： 从模型而注入视图加载为-1。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>重新加载具有 IOptionsSnapshot 配置数据

重新加载配置数据与`IOptionsSnapshot`示例所示&num;5 中的[示例应用程序](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/options/sample)。

*需要 ASP.NET Core 1.1 或更高版本。*

[IOptionsSnapshot](/dotnet/api/microsoft.extensions.options.ioptionssnapshot-1)重新加载与最小的处理开销的选项的支持。 ASP.NET 核心 1.1 中`IOptionsSnapshot`是快照[IOptionsMonitor&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1)和更新自动每当监视器触发的更改基于数据源更改。 ASP.NET 核心 2.0 及更高版本，选项会计算一次每个请求时访问和缓存请求的生存期。

下面的示例演示如何新`IOptionsSnapshot`后创建*appsettings.json*更改 (*Pages/Index.cshtml.cs*)。 对服务器的多个请求返回常量的值由提供*appsettings.json*文件之前文件更改和配置重新加载。

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet_Example5)]

下图显示了初始`option1`和`option2`值加载从*appsettings.json*文件：

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

更改中的值*appsettings.json*文件为`value1_from_json UPDATED`和`200`。 保存*appsettings.json*文件。 刷新浏览器以查看更新的选项值：

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>名为 IConfigureNamedOptions 选项支持

名为选项支持[IConfigureNamedOptions](/dotnet/api/microsoft.extensions.options.iconfigurenamedoptions-1)作为示例演示了&num;中的第 6[示例应用程序](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/options/sample)。

*需要 ASP.NET Core 2.0 或更高版本。*

*名为选项*支持允许区分命名的选项配置此应用。 在示例应用中，命名的选项使用声明[ConfigureNamedOptions&lt;TOptions&gt;。配置](/dotnet/api/microsoft.extensions.options.configurenamedoptions-1.configure)方法：

[!code-csharp[Main](options/sample/Startup.cs?name=snippet_Example6)]

示例应用程序访问的命名的选项[IOptionsSnapshot&lt;TOptions&gt;。获取](/dotnet/api/microsoft.extensions.options.ioptionssnapshot-1.get)(*Pages/Index.cshtml.cs*):

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[Main](options/sample/Pages/Index.cshtml.cs?name=snippet_Example6)]

运行示例应用程序，将返回的已命名的选项：

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1`值提供从配置，其中加载从*appsettings.json*文件。 `named_options_2`通过提供值：

* `named_options_2`委托中`ConfigureServices`为`Option1`。
* 默认值为`Option2`由`MyOptions`类。

配置具有所有命名的选项实例[OptionsServiceCollectionExtensions.ConfigureAll](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.configureall)方法。 下面的代码将配置`Option1`所有名为具有公共值配置实例。 手动添加以下代码`Configure`方法：

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

添加代码后运行示例应用程序产生以下结果：

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> 在 ASP.NET 核心 2.0 和更高版本，所有选项的都命名实例。 现有`IConfigureOption`实例将被视为面向`Options.DefaultName`实例，即`string.Empty`。 `IConfigureNamedOptions`此外实现`IConfigureOptions`。 默认实现[IOptionsFactory&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ioptionsfactory-1) ([引用源](https://github.com/aspnet/Options/blob/release/2.0.0/src/Microsoft.Extensions.Options/OptionsFactory.cs)) 使用每个相应的逻辑。 `null`命名的选项用于目标的所有命名实例而不是特定的命名实例 ([ConfigureAll](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.configureall)和[PostConfigureAll](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.postconfigureall)使用此约定)。

## <a name="ipostconfigureoptions"></a>IPostConfigureOptions

*需要 ASP.NET Core 2.0 或更高版本。*

设置与 postconfiguration [IPostConfigureOptions&lt;TOptions&gt;](/dotnet/api/microsoft.extensions.options.ipostconfigureoptions-1)。 在所有运行 postconfiguration [IConfigureOptions&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1)进行配置：

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

[PostConfigure&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ipostconfigureoptions-1.postconfigure) ，可后配置名称的选项：

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

使用[PostConfigureAll&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.postconfigureall)后配置所有名为配置实例：

```csharp
services.PostConfigureAll<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="options-factory-monitoring-and-cache"></a>选项工厂、 监视和缓存

[IOptionsMonitor](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1)用于通知时`TOptions`实例更改。 `IOptionsMonitor`支持 reloadable 选项更改通知，和`IPostConfigureOptions`。

[IOptionsFactory&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ioptionsfactory-1) (ASP.NET Core 2.0 或更高版本) 负责，创建新选项实例。 它具有单个[创建](/dotnet/api/microsoft.extensions.options.ioptionsfactory-1.create)方法。 默认实现将所有已注册`IConfigureOptions`和`IPostConfigureOptions`并运行所有配置第一次后, 跟后配置。 它区分`IConfigureNamedOptions`和`IConfigureOptions`且仅调用适当的接口。

[IOptionsMonitorCache&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1) (ASP.NET Core 2.0 或更高版本) 由`IOptionsMonitor`缓存`TOptions`实例。 `IOptionsMonitorCache`失效监视器中的选项实例，以便重新计算值 ([TryRemove](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1.tryremove))。 值可以手动引入以及[TryAdd](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1.tryadd)。 [清除](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1.clear)时应该按需重新创建所有的命名的实例使用方法。

## <a name="see-also"></a>请参阅

* [配置](xref:fundamentals/configuration/index)
