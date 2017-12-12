---
title: "Razor 页路由和应用程序中 ASP.NET Core 的约定功能"
author: guardrex
description: "发现如何路由和应用程序模型提供程序约定功能帮助你控制页路由、 发现和处理。"
keywords: "ASP.NET 核心、 Razor 页、 约定，AddFolderRouteModelConvention、 AddPageRouteModelConvention、 AddPageRoute、 AddFolderApplicationModelConvention、 AddPageApplicationModelConvention，ConfigureFilter，筛选器"
ms.author: riande
manager: wpickett
ms.date: 10/23/2017
ms.topic: article
ms.assetid: 6b60514c-81ad-485b-bb22-9b71416dff08
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/razor-pages/razor-pages-convention-features
ms.openlocfilehash: 81fe5198e25c4275f5cf0a123536a9130be8c1d9
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="razor-pages-route-and-app-convention-features-in-aspnet-core"></a>Razor 页路由和应用程序中 ASP.NET Core 的约定功能

作者：[Luke Latham](https://github.com/guardrex)

了解如何使用页路由和应用程序模型提供程序约定功能来控制页路由、 发现和 Razor 页应用中的处理。 当你需要进行自定义页为配置路由各个页时，配置路由至页面[AddPageRoute 约定](#configure-a-page-route)本主题稍后所述。

使用[示例应用程序](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/razor-pages/razor-pages-convention-features/sample)([如何下载](xref:tutorials/index#how-to-download-a-sample)) 来浏览本主题中所述的功能。

| 功能 | 此示例演示如何... |
| -------- | --------------------------- |
| [路由和应用程序模型约定](#add-route-and-app-model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li></ul> | 将路由模板和标头添加到应用程序的页面。 |
| [页路由操作约定](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | 将路由模板添加到文件夹中的页和单个页面。 |
| [页模型操作约定](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter （筛选器类、 lambda 表达式或筛选器工厂）</li></ul> | 将一个标头添加到文件夹中的页，将一个标头添加到单个页面，和配置[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)将标头添加到应用程序的页面。 |
| [默认页应用程序模型提供程序](#replace-the-default-page-app-model-provider) | 替换默认页模型提供程序来更改处理程序命名约定。 |

## <a name="add-route-and-app-model-conventions"></a>添加路由和应用程序模型约定

添加的委托[IPageConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageconvention)添加路由和应用程序将应用于 Razor 页面的模型约定。

**将路由模型约定添加到所有页面**

使用[约定](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.razorpagesoptions.conventions)创建并添加[IPageRouteModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageroutemodelconvention)到的集合[IPageConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageconvention)在路由和页模型过程中应用的实例构造。

示例应用添加`{globalTemplate?}`到所有应用程序中的页的路由模板：

[!code-csharp[Main](razor-pages-convention-features/sample/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

> [!NOTE]
> `Order`属性`AttributeRouteModel`设置为`0`（零）。 这可确保提供了一个路由值时，指定此模板的第一个路由数据值位置的优先级。 例如，此示例将添加`{aboutTemplate?}`在本主题后面的路由模板。 `{aboutTemplate?}`模板提供`Order`的`1`。 关于页面上的请求时`/About/RouteDataValue`，"RouteDataValue"加载到`RouteData.Values["globalTemplate"]`(`Order = 0`) 而不`RouteData.Values["aboutTemplate"]`(`Order = 1`) 由于设置`Order`属性。

*Startup.cs*:

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet1)]

请求在示例的有关页`localhost:5000/About/GlobalRouteValue`并检查结果：

![关于页面，其中使用 GlobalRouteValue 路由段请求。 呈现的页面显示，在页面的 OnGet 方法捕获路由数据值。](razor-pages-convention-features/_static/about-page-global-template.png)

**将应用程序模型约定添加到所有页面**

使用[约定](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.razorpagesoptions.conventions)创建并添加[IPageApplicationModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageapplicationmodelconvention)到的集合[IPageConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageconvention)在路由和页过程中应用的实例模型构建。

为了演示这和在本主题后面的其他约定，示例应用程序包括`AddHeaderAttribute`类。 类构造函数接受`name`字符串和`values`字符串数组。 在使用这些值其`OnResultExecuting`方法以设置响应标头。 完整类所示[页上模型操作约定](#page-model-action-conventions)在本主题后面的部分。

此示例应用程序使用`AddHeaderAttribute`类添加标头， `GlobalHeader`，对所有应用程序中的页：

[!code-csharp[Main](razor-pages-convention-features/sample/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet2)]

请求在示例的有关页`localhost:5000/About`并检查要查看的结果的标头：

![响应标头的关于页面显示 GlobalHeader 验证已添加。](razor-pages-convention-features/_static/about-page-global-header.png)

## <a name="page-route-action-conventions"></a>页路由操作约定

默认路由模型提供程序派生自[IPageRouteModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageroutemodelprovider)调用约定，它们可为配置页的路由提供扩展点。

**文件夹路由模型的约定**

使用[AddFolderRouteModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageconventioncollection.addfolderroutemodelconvention)创建并添加[IPageRouteModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageroutemodelconvention) ，它在调用操作[PageRouteModel](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageroutemodel)下的页面的所有指定的文件夹。

此示例应用程序使用`AddFolderRouteModelConvention`添加`{otherPagesTemplate?}`路由模板中的页*OtherPages*文件夹：

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet3)]

> [!NOTE]
> `Order`属性`AttributeRouteModel`设置为`1`。 这就保证了模板`{globalTemplate?}`（在本主题中前面设置） 分配的优先级为第一个路由数据值位置时提供一个路由值。 如果在请求页 1 该页`/OtherPages/Page1/RouteDataValue`，"RouteDataValue"加载到`RouteData.Values["globalTemplate"]`(`Order = 0`) 而不`RouteData.Values["otherPagesTemplate"]`(`Order = 1`) 由于设置`Order`属性。

请求的示例页 1 页在`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue`并检查结果：

![带 GlobalRouteValue 和 OtherPagesRouteValue 路由段请求页 1 OtherPages 文件夹中。 呈现的页面显示路由数据值将在页面的 OnGet 方法中捕获。](razor-pages-convention-features/_static/otherpages-page1-global-and-otherpages-templates.png)

**页路由模型的约定**

使用[AddPageRouteModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageconventioncollection.addpageroutemodelconvention)创建并添加[IPageRouteModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageroutemodelconvention) ，它在调用操作[PageRouteModel](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageroutemodel)具有指定页名称。

此示例应用程序使用`AddPageRouteModelConvention`添加`{aboutTemplate?}`路由到关于页面的模板：

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet4)]

> [!NOTE]
> `Order`属性`AttributeRouteModel`设置为`1`。 这就保证了模板`{globalTemplate?}`（在本主题中前面设置） 分配的优先级为第一个路由数据值位置时提供一个路由值。 如果关于页面请求在`/About/RouteDataValue`，"RouteDataValue"加载到`RouteData.Values["globalTemplate"]`(`Order = 0`) 而不`RouteData.Values["aboutTemplate"]`(`Order = 1`) 由于设置`Order`属性。

请求在示例的有关页`localhost:5000/About/GlobalRouteValue/AboutRouteValue`并检查结果：

![有关页面请求路由段 GlobalRouteValue 和 AboutRouteValue。 呈现的页面显示路由数据值将在页面的 OnGet 方法中捕获。](razor-pages-convention-features/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a>配置页路由

使用[AddPageRoute](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.addpageroute)若要配置路由到页上，在指定的页面的路径。 生成的链接到的页面使用你指定的路由。 `AddPageRoute`使用`AddPageRouteModelConvention`来建立路由。

示例应用程序创建的路由`/TheContactPage`为*Contact.cshtml*:

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet5)]

在也能访问的联系人页`/Contact`通过其默认路由。

示例应用程序的自定义路由到联系人页面允许为可选`text`路由段 (`{text?}`)。 该页面还包括在此可选段其`@page`以防在距访客访问第一页的指令其`/Contact`路由：

[!code-cshtml[Main](razor-pages-convention-features/sample/Pages/Contact.cshtml?highlight=1)]

请注意，URL 为生成**联系人**中呈现的页面的链接反映更新的路由：

![在导航栏中的示例应用联系人链接](razor-pages-convention-features/_static/contact-link.png)

![检查呈现的 HTML 中的联系人链接指示 href 是否设置为 / TheContactPage](razor-pages-convention-features/_static/contact-link-source.png)

请在联系人页访问其普通的路由， `/Contact`，或自定义路由， `/TheContactPage`。 如果你提供附加`text`路由段，该页面显示的 HTML 编码的段，你提供：

![提供的 URL 中的 TextValue 可选 'text' 路由段的边缘浏览器示例。 呈现的页面显示 'text' 段值。](razor-pages-convention-features/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>页模型操作约定

默认页模型实现的提供程序[IPageApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageapplicationmodelprovider)调用约定，它们可用于配置页模型提供扩展点。 这些约定可在生成和修改页发现和处理功能时。

本部分中的示例，示例应用使用`AddHeaderAttribute`类，该类是[ResultFilterAttribute](/dotnet/api/microsoft.aspnetcore.mvc.filters.resultfilterattribute)，，它将应用的响应标头：

[!code-csharp[Main](razor-pages-convention-features/sample/Filters/AddHeader.cs?name=snippet1)]

使用约定，此示例演示如何将该属性应用到所有文件夹中的网页和单个页面。

**文件夹应用模型的约定**

使用[AddFolderApplicationModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageconventioncollection.addfolderapplicationmodelconvention)创建并添加[IPageApplicationModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageapplicationmodelconvention) ，它在调用操作[PageApplicationModel](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageapplicationmodel)的实例指定的文件夹下的所有页面。

此示例演示如何使用`AddFolderApplicationModelConvention`通过添加标头， `OtherPagesHeader`，到内的页面具有*OtherPages*的应用程序的文件夹：

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet6)]

请求的示例页 1 页在`localhost:5000/OtherPages/Page1`并检查要查看的结果的标头：

![OtherPages/页 1 页的响应标头显示 OtherPagesHeader 验证已添加。](razor-pages-convention-features/_static/page1-otherpages-header.png)

**页应用模型的约定**

使用[AddPageApplicationModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageconventioncollection.addpageapplicationmodelconvention)创建并添加[IPageApplicationModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageapplicationmodelconvention) ，它在调用操作[PageApplicationModel](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageapplicationmodel)页speciifed 同名。

此示例演示如何使用`AddPageApplicationModelConvention`通过添加标头， `AboutHeader`，到关于页面：

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet7)]

请求在示例的有关页`localhost:5000/About`并检查要查看的结果的标头：

![响应标头的关于页面显示 AboutHeader 验证已添加。](razor-pages-convention-features/_static/about-page-about-header.png)

**配置一个筛选器**

[ConfigureFilter](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.configurefilter)配置指定的筛选器应用。 你可以实现的筛选器类，但示例应用演示如何实现在 lambda 表达式中，实现幕后作为一个工厂，它返回的筛选器的筛选器：

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet8)]

页应用模型用于检查导致的 Page2 页中的段的相对路径*OtherPages*文件夹。 如果条件通过，添加标头。 如果没有，则`EmptyFilter`应用。

`EmptyFilter`是[操作筛选器](xref:mvc/controllers/filters#action-filters)。 由于操作筛选器将忽略由 Razor 页`EmptyFilter`否 ops 按预期的路径不包含`OtherPages/Page2`。

请求在示例的 Page2 页`localhost:5000/OtherPages/Page2`并检查要查看的结果的标头：

![Page2 的情况下，OtherPagesPage2Header 添加到的响应。](razor-pages-convention-features/_static/page2-filter-header.png)

**配置筛选器工厂**

[ConfigureFilter](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.configurefilter?view=aspnetcore-2.0#Microsoft_Extensions_DependencyInjection_PageConventionCollectionExtensions_ConfigureFilter_Microsoft_AspNetCore_Mvc_ApplicationModels_PageConventionCollection_System_Func_Microsoft_AspNetCore_Mvc_ApplicationModels_PageApplicationModel_Microsoft_AspNetCore_Mvc_Filters_IFilterMetadata__)配置用于应用的指定的工厂[筛选器](xref:mvc/controllers/filters)到 Razor 的所有页面。

示例应用程序提供使用的示例[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)通过添加标头， `FilterFactoryHeader`，具有到应用程序的页面的两个值：

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[Main](razor-pages-convention-features/sample/Factories/AddHeaderWithFactory.cs?name=snippet1)]

请求在示例的有关页`localhost:5000/About`并检查要查看的结果的标头：

![响应标头的关于页面显示已添加了两个 FilterFactoryHeader 标头。](razor-pages-convention-features/_static/about-page-filter-factory-header.png)

## <a name="replace-the-default-page-app-model-provider"></a>替换默认页应用程序模型提供程序

使用 razor 页`IPageApplicationModelProvider`接口可创建[DefaultPageApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.internal.defaultpageapplicationmodelprovider)。 你可以从默认的模型提供程序，以提供您自己的实现逻辑处理程序发现和处理继承。 默认实现 ([引用源](https://github.com/aspnet/Mvc/blob/rel/2.0.1/src/Microsoft.AspNetCore.Mvc.RazorPages/Internal/DefaultPageApplicationModelProvider.cs)) 建立约定*未命名*和*名为*命名，即下面所述的处理程序。

**默认未命名的处理程序方法**

HTTP 谓词 （"未命名的"处理程序方法） 的处理程序方法都遵循约定： `On<HTTP verb>[Async]` (追加`Async`是可选但建议的异步方法)。

| 未命名的处理程序方法     | 操作                      |
| -------------------------- | ------------------------------ |
| `OnGet`/`OnGetAsync`       | 初始化页面状态。     |
| `OnPost`/`OnPostAsync`     | 处理 POST 请求。          |
| `OnDelete`/`OnDeleteAsync` | 句柄删除请求 （&#8224;。 |
| `OnPut`/`OnPutAsync`       | 句柄 PUT 请求 （&#8224;。    |
| `OnPatch`/`OnPatchAsync`   | 句柄 PATCH 请求 （&#8224;。  |

&#8224;用于进行到页面的 API 调用。

**默认命名处理程序方法**

提供由开发人员 （"名为"处理程序方法） 的处理程序方法遵循类似的约定。 处理程序名称出现的 HTTP 谓词之后或之间的 HTTP 谓词和`Async`: `On<HTTP verb><handler name>[Async]` (追加`Async`是可选但建议的异步方法)。 例如，处理消息的方法可能需要命名下表中所示。

| 名为处理程序方法的示例             | 示例操作        |
| ---------------------------------------- | ------------------------ |
| `OnGetMessage`/`OnGetMessageAsync`       | 获取一条消息。        |
| `OnPostMessage`/`OnPostMessageAsync`     | 将消息发送。          |
| `OnDeleteMessage`/`OnDeleteMessageAsync` | 删除一条消息 （&） #8224;。 |
| `OnPutMessage`/`OnPutMessageAsync`       | 将一条消息 （&） #8224;。    |
| `OnPatchMessage`/`OnPatchMessageAsync`   | 一条消息 （&） #8224; 的修补程序。  |

&#8224;用于进行到页面的 API 调用。

**自定义处理程序方法的名称**

假定您希望更改命名未命名和命名处理程序方法的方式。 可选的命名方案是避免启动与"On"的方法名称，并使用的第一个 word 段来确定的 HTTP 谓词。 你可以进行其他更改，例如为删除，将转换谓词，PUT 和 POST 到修补程序。 这种方案提供下表中所示的方法名称。

| 处理程序方法                       | 操作                      |
| ------------------------------------ | ------------------------------ |
| `Get`                                | 初始化页面状态。     |
| `Post`/`PostAsync`                   | 处理 POST 请求。          |
| `Delete`/`DeleteAsync`               | 句柄删除请求 （&#8224;。 |
| `Put`/`PutAsync`                     | 句柄 PUT 请求 （&#8224;。    |
| `Patch`/`PatchAsync`                 | 句柄 PATCH 请求 （&#8224;。  |
| `GetMessage`                         | 获取一条消息。              |
| `PostMessage`/`PostMessageAsync`     | 将消息发送。                |
| `DeleteMessage`/`DeleteMessageAsync` | 文章要删除的消息。      |
| `PutMessage`/`PutMessageAsync`       | 文章将一条消息。         |
| `PatchMessage`/`PatchMessageAsync`   | 将消息发布到修补程序。       |

&#8224;用于进行到页面的 API 调用。

若要建立此方案，请从继承`DefaultPageApplicationModelProvider`类并重写[CreateHandlerModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.internal.defaultpageapplicationmodelprovider.createhandlermodel)方法以提供用于解决的自定义逻辑[PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel)处理程序的名称。 示例应用演示如何做到这一点其`CustomPageApplicationModelProvider`类：

[!code-csharp[Main](razor-pages-convention-features/sample/CustomPageApplicationModelProvider.cs?name=snippet1&highlight=1-2,45-46,64-68,78-85,87,92,106)]

类的重要功能包括：

* 类继承自`DefaultPageApplicationModelProvider`。
* `TryParseHandlerMethod`处理处理程序，以确定的 HTTP 谓词 (`httpMethod`) 和命名处理程序名称 (`handlerName`) 创建时`PageHandlerModel`。
  * `Async`后缀将被忽略，如果存在。
  * 大小写用于分析与方法名称的 HTTP 谓词。
  * 当方法名称 (而无需`Async`) 是与名称相同的 HTTP 谓词，没有命名处理程序。 `handlerName`设置为`null`，并且方法名称为`Get`， `Post`， `Delete`， `Put`，或`Patch`。
  * 当方法名称 (而无需`Async`) 的长度超过 HTTP 谓词名称，没有命名处理程序。 `handlerName` 设置为 `<method name (less 'Async', if present)>`。 例如，"GetMessage"和"GetMessageAsync"产生"GetMessage"的处理程序名称。
  * 删除、 PUT 和修补程序的 HTTP 谓词将转换为 POST。

注册`CustomPageApplicationModelProvider`中`Startup`类：

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet10)]

代码隐藏文件*Index.cshtml.cs*演示普通的处理程序方法的命名约定中应用的页面的更改方式。 "On"前缀命名 Razor 页与使用普通被删除。 初始化页面状态的方法现在名为`Get`。 你可以查看当你打开的所有页面进行任何代码隐藏文件整个应用使用此约定。

每个其他方法开头描述其处理的 HTTP 谓词。 以开头的两个方法`Delete`将通常被视为 DELETE HTTP 谓词，但在逻辑`TryParseHandlerMethod`显式设置的谓词为 POST 为这两个处理程序。

请注意，`Async`是可选之间`DeleteAllMessages`和`DeleteMessageAsync`。 它们这两种异步方法，但你可以选择使用`Async`后缀或不; 我们建议你执行。 `DeleteAllMessages`出于演示目的，此处使用但我们建议你将此类方法`DeleteAllMessagesAsync`。 它不会影响处理的示例的实现，但使用`Async`后缀调出它是异步方法。

[!code-csharp[Main](razor-pages-convention-features/sample/Pages/Index.cshtml.cs?name=snippet1&highlight=1,6,16,29)]

请注意处理程序的名称中提供*Index.cshtml*匹配`DeleteAllMessages`和`DeleteMessageAsync`处理程序方法：

[!code-cshtml[Main](razor-pages-convention-features/sample/Pages/Index.cshtml?range=29-60&highlight=7-8,24-25)]

`Async`在处理程序方法名称`DeleteMessageAsync`通过注销分解`TryParseHandlerMethod`匹配的方法的 POST 请求的处理程序。 `asp-page-handler`名称`DeleteMessage`的处理程序方法与匹配`DeleteMessageAsync`。

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>MVC 筛选器和页面筛选器 (IPageFilter)

MVC[操作筛选器](xref:mvc/controllers/filters#action-filters)将被忽略由 Razor 页，因为 Razor 页使用处理程序方法。 并可供你使用其他类型的 MVC 筛选器：[授权](xref:mvc/controllers/filters#authorization-filters)，[异常](xref:mvc/controllers/filters#exception-filters)，[资源](xref:mvc/controllers/filters#resource-filters)，和[结果](xref:mvc/controllers/filters#result-filters)。 有关详细信息，请参阅[筛选器](xref:mvc/controllers/filters)主题。

页面筛选器 ([IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter)) 是适用于 Razor 页的筛选器。 它在页处理程序方法的执行。 它可以在页处理程序方法执行的阶段处理自定义代码。 下面是示例应用程序的一个示例：

[!code-csharp[Main](razor-pages-convention-features/sample/Filters/ReplaceRouteValueFilterAttribute.cs?name=snippet1)]

此筛选器检查`globalTemplate`中"ReplacementValue"路由"TriggerValue"和交换的值。

`ReplaceRouteValueFilter`特性可以直接应用`PageModel`代码隐藏文件中：

[!code-csharp[Main](razor-pages-convention-features/sample/Pages/OtherPages/Page3.cshtml.cs?range=10-12&highlight=1)]

从示例应用程序与在请求页面 3 页`localhost:5000/OtherPages/Page3/TriggerValue`。 请注意筛选器将路由值的替换：

![对 OtherPages/页面 3 的请求与在筛选器将路由值替换为 ReplacementValue TriggerValue 路径段结果。](razor-pages-convention-features/_static/otherpages-page3-filter-replacement-value.png)

## <a name="see-also"></a>请参阅

* [Razor 页授权约定](xref:security/authorization/razor-pages-authorization)
