---
title: "ASP.NET 核心 MVC 中的格式设置响应数据"
author: ardalis
description: "了解如何设置 ASP.NET 核心 mvc 响应数据的格式。"
keywords: "ASP.NET 核心、 IOutputFormatter、 IActionResult 响应数据"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: c056df45-d013-4826-91a1-4a092bae1ea5
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/models/formatting
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 08135aa40153ab7f24aba15e0bf97aa01ef8fa96
ms.sourcegitcommit: 275a5381b6172b4f0b5fcd1d252aff03d3dae166
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2017
---
# <a name="introduction-to-formatting-response-data-in-aspnet-core-mvc"></a>ASP.NET 核心 MVC 中的格式设置响应数据简介

通过[Steve Smith](http://ardalis.com)

ASP.NET 核心 MVC 具有用于格式设置响应数据，使用固定的格式或以响应客户端规范的内置支持。

[查看或从 GitHub 下载示例](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/formatting/sample)。

## <a name="format-specific-action-results"></a>格式特定的操作结果

某些操作结果类型是特定于特定的格式，如`JsonResult`和`ContentResult`。 操作可以返回特定结果始终格式化以特定方式。 例如，返回`JsonResult`将返回 JSON 格式的数据，而不考虑客户端首选项。 同样，返回`ContentResult`（如将只返回一个字符串） 将返回纯文本格式的字符串数据。

> [!NOTE]
> 操作不需要返回任何特定的类型;MVC 支持任何对象的返回值。 如果操作返回`IActionResult`实现和控制器继承自`Controller`，开发人员有许多与许多选项对应的帮助器方法。 从返回对象的操作的结果并非`IActionResult`将使用相应的序列化类型`IOutputFormatter`实现。

若要从控制器继承自特定的格式返回数据`Controller`基类，可以使用内置的帮助器方法`Json`返回 JSON 和`Content`纯文本。 操作方法应返回特定结果类型 (例如， `JsonResult`) 或`IActionResult`。

返回 JSON 格式的数据：

[!code-csharp[Main](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=21-26)]

此操作的示例响应：

![网络选项卡中显示的响应的内容类型的 Microsoft Edge 开发人员工具为 application/json](formatting/_static/json-response.png)

请注意，响应的内容类型是`application/json`，同时在列表中的网络请求和响应标头部分中所示。 另请注意 （在此情况下，Microsoft Edge） 中的请求标头部分的 Accept 标头中的浏览器呈现选项的列表。 当前技术正在忽略此标头;下面将讨论 obeying 它。

若要返回格式化的纯文本数据，使用`ContentResult`和`Content`帮助器：

[!code-csharp[Main](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=47-52)]

来自此操作的响应：

![网络选项卡中显示的内容类型的 Microsoft Edge 开发人员工具是响应的文本/无格式](formatting/_static/text-response.png)

请注意这种情况下`Content-Type`返回`text/plain`。 也可以实现此相同的行为使用只是一种字符串响应类型：

[!code-csharp[Main](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=54-59)]

>[!TIP]
> 对于具有多个重要操作返回类型或选项 （例如，根据执行的操作的结果的不同 HTTP 状态代码），应尽量使用`IActionResult`用作返回类型。

## <a name="content-negotiation"></a>内容协商

内容协商 (*conneg*简称) 客户端指定时发生[Accept 标头](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)。 使用 ASP.NET 核心 MVC 的默认格式为 JSON。 由实现内容协商`ObjectResult`。 它还将内置在特定的操作结果的帮助器方法从返回的状态代码 (的所有基于`ObjectResult`)。 你还可以返回的模型类型 （已定义为你的数据传输类型的类） 和框架将自动换行在`ObjectResult`为你。

下面的操作方法使用`Ok`和`NotFound`帮助器方法：

[!code-csharp[Main](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=8,10&range=28-38)]

将返回 JSON 格式的响应，除非另一种格式请求和服务器可以返回请求的格式。 你可以使用之类的工具[Fiddler](http://www.telerik.com/fiddler)若要创建包含 Accept 标头的请求，并指定另一种格式。 在此情况下，如果服务器具有*格式化程序*可以生成能产生中请求的格式的响应，将返回的结果中的客户端首选格式。

![Fiddler 控制台显示手动创建获取请求的 Accept 标头值为应用程序/xml](formatting/_static/fiddler-composer.png)

在上面的屏幕截图，Fiddler Composer 已用于生成请求，指定`Accept: application/xml`。 默认情况下，ASP.NET 核心 MVC 仅支持 JSON，因此即使指定另一种格式，则返回的结果时，仍 JSON 格式。 你将了解如何在下一部分中添加其他格式化程序。

控制器操作可以返回 POCOs （普通旧 CLR 对象），在这种情况下将自动创建 ASP.NET MVC`ObjectResult`为你包装对象。 客户端将获得格式化的序列化的对象 （JSON 格式是默认设置; 你可以配置 XML 或其他格式）。 如果对象被返回为`null`，则框架将返回`204 No Content`响应。

返回的对象类型：

[!code-csharp[Main](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3&range=40-45)]

在示例中，有效作者别名的请求将收到一个 200 OK 响应使用作者的数据。 无效的别名的请求将收到 204 无内容的响应。 显示在 XML 和 JSON 格式的响应的屏幕截图所示。

### <a name="content-negotiation-process"></a>内容协商过程

内容*协商*才会进行如果`Accept`标头将显示在请求中。 当请求包含 accept 标头时，框架将枚举按优先顺序 accept 标头中的媒体类型，并将尝试查找可以生成指定 accept 标头的格式之一的响应的格式化程序。 以防没有格式化程序找到的可以满足客户端的请求，则框架将尝试找到的第一个格式化程序可以生成响应 (除非开发人员机构上配置了选项`MvcOptions`返回 406 不可接受相反)。 如果此请求指定的 XML，但尚未配置 XML 格式化程序，则将使用 JSON 格式化程序。 一般来说，如果配置未格式化程序可以提供请求的格式，则使用第一个格式化程序不是可以设置对象的格式。 如果未不提供任何标头，则将使用的第一个格式化程序可以处理要返回的对象要序列化响应。 在这种情况下，没有任何协商发生-服务器确定它将使用何种格式。

> [!NOTE]
> 如果 Accept 标头包含`*/*`，将忽略标头，除非`RespectBrowserAcceptHeader`设置为 true 上`MvcOptions`。

### <a name="browsers-and-content-negotiation"></a>浏览器和内容协商

与典型的 API 客户端不同 web 浏览器倾向于提供`Accept`包括多种不同的格式，其中包括通配符的标头。 默认情况下，当框架检测到该请求是否来自浏览器中，它将忽略`Accept`标头和改为返回应用程序中的内容配置的默认格式 (JSON 除非另外配置)。 使用不同的浏览器要使用 Api 时，这会提供更一致的体验。

如果您希望使用应用程序 honor 浏览器接受标头，则可以配置此 MVC 的配置过程中通过设置`RespectBrowserAcceptHeader`到`true`中`ConfigureServices`中的方法*Startup.cs*。

```csharp
services.AddMvc(options =>
{
    options.RespectBrowserAcceptHeader = true; // false by default
}
```

## <a name="configuring-formatters"></a>配置格式化程序

如果你的应用程序需要支持 JSON 的默认值之外的其他格式，你可以添加 NuGet 包和配置 MVC 可支持它们。 没有为输入和输出的单独格式化程序。 输入的格式化程序使用的[模型绑定](model-binding.md); 输出格式化程序用于格式化响应。 你还可以配置[自定义格式化程序](../advanced/custom-formatters.md)。

### <a name="adding-xml-format-support"></a>添加 XML 格式的支持

若要添加的 XML 格式的支持，请安装`Microsoft.AspNetCore.Mvc.Formatters.Xml`NuGet 包。

向 MVC 的配置中添加 XmlSerializerFormatters *Startup.cs*:

[!code-csharp[Main](./formatting/sample/Startup.cs?name=snippet1&highlight=2)]

或者，你可以添加只输出格式化程序：

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.Add(new XmlSerializerOutputFormatter());
});
```

这两种方法将序列化结果使用`System.Xml.Serialization.XmlSerializer`。 如果你愿意，你可以使用`System.Runtime.Serialization.DataContractSerializer`加上其关联的格式化程序：

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.Add(new XmlDataContractSerializerOutputFormatter());
});
```

一旦你已添加对 XML 格式的支持，控制器方法将返回基于请求的相应格式`Accept`标头，作为示例演示了此 Fiddler:

![Fiddler 控制台： 请求的原始选项卡显示 Accept 标头值为 application/xml。 响应原始选项卡显示应用程序/xml 的 Content-type 标头值。](formatting/_static/xml-response.png)

你可以将原始 GET 请求进行的检查器选项卡中看到`Accept: application/xml`标头集。 响应窗格显示`Content-Type: application/xml`标头，与`Author`对象序列化为 XML。

使用编辑器选项卡来修改指定的请求`application/json`中`Accept`标头。 执行请求，并且响应将被格式化为 JSON:

![Fiddler 控制台： 请求的原始选项卡显示 Accept 标头值为 application/json。 响应原始选项卡显示应用程序/json 的内容类型标头值。](formatting/_static/json-response-fiddler.png)

在此屏幕截图中，你可以看到请求设置标头的`Accept: application/json`并响应指定与相同其`Content-Type`。 `Author`对象显示 JSON 格式的响应正文中。

### <a name="forcing-a-particular-format"></a>强制某一特定格式

如果你想要限制特定操作的响应格式可以你可以将应用`[Produces]`筛选器。 `[Produces]`筛选器指定的特定操作 （或控制器） 的响应格式。 如大多数[筛选器](../controllers/filters.md)，这可以在操作、 控制器或全局范围内应用。

```csharp
[Produces("application/json")]
public class AuthorsController
```

`[Produces]`筛选器将强制中的所有操作`AuthorsController`返回 JSON 格式的响应，即使其他格式化程序已配置为应用程序和提供客户端`Accept`标头请求不同，可用格式。 请参阅[筛选器](../controllers/filters.md)若要了解详细信息，包括如何执行全局应用筛选器。

### <a name="special-case-formatters"></a>特殊大小写的格式化程序

某些特殊的情况下是使用内置的格式化程序实现的。 默认情况下，`string`返回类型将被格式化为*文本/无格式*(*文本/html*如果请求通过`Accept`标头)。 此行为可能会通过删除`TextOutputFormatter`。 删除中的格式化程序`Configure`中的方法*Startup.cs* （如下所示）。 模型对象的操作返回类型时将返回 204 无内容的响应返回`null`。 此行为可能会通过删除`HttpNoContentOutputFormatter`。 下面的代码中删除`TextOutputFormatter`和`HttpNoContentOutputFormatter`。

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.RemoveType<TextOutputFormatter>();
    options.OutputFormatters.RemoveType<HttpNoContentOutputFormatter>();
});
```

而无需`TextOutputFormatter`，`string`返回类型返回 406 不可接受，例如。 请注意，是否存在 XML 格式化程序，它将格式化`string`返回类型，如果`TextOutputFormatter`中删除。

而无需`HttpNoContentOutputFormatter`，null 对象的格式使用配置的格式化程序。 例如，JSON 格式化程序只需将返回的响应的正文`null`，而 XML 格式化程序将返回该属性的空 XML 元素`xsi:nil="true"`设置。

## <a name="response-format-url-mappings"></a>响应格式 URL 映射

客户端可以在查询字符串或一部分的路径，或使用特定格式的文件扩展名，如.xml 或.json 中特定格式的 URL，一部分请求如。 应使用该 API 的路由中指定请求路径中的映射。 例如: 

```csharp
[FormatFilter]
public class ProductsController
{
    [Route("[controller]/[action]/{id}.{format?}")]
    public Product GetById(int id)
```

此路由将允许请求的格式指定为可选的文件扩展名。 `[FormatFilter]`属性检查是否存在的中的格式值`RouteData`和创建响应时将映射到适当的格式化程序的响应格式。

| 路由                      | 格式化程序                          |
| -------------------------- | ---------------------------------- |
| `/products/GetById/5`      | 默认的输出格式化程序       |
| `/products/GetById/5.json` | JSON 格式化程序 （如果配置） |
| `/products/GetById/5.xml`  | XML 格式化程序 （如果配置）  |
