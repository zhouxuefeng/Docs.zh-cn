---
title: "模型绑定"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: b355a48e-a15c-4d58-b69c-899763613a97
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/models/model-binding
ms.openlocfilehash: 597d4058a410e0b5991b1d5a74c9fc7bfe8171b8
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="model-binding"></a>模型绑定

通过[Rachel Appel](https://github.com/rachelappel)

## <a name="introduction-to-model-binding"></a>模型绑定简介

ASP.NET 核心 mvc 模型绑定将映射到操作方法参数从 HTTP 请求的数据。 参数可能是简单类型，如字符串、 整数或浮点数，或它们可能是复杂类型。 这是一项强大功能的 MVC，因为将传入数据映射到对应的是经常重复的方案中，而不考虑大小或数据的复杂性。 MVC 抽象化绑定使开发人员无需保留重写每个应用中相同的代码的略有不同版本，从而解决了此问题。 编写您自己的文本类型转换器代码单调乏味，而且容易出错。

## <a name="how-model-binding-works"></a>模型绑定的工作原理

当 MVC 收到 HTTP 请求时，它可以将它路由到控制器的特定操作方法。 它决定哪种操作方法，以基于运行上中的路由数据，然后将值从 HTTP 请求绑定到该操作方法的参数。 例如，考虑以下 URL:

`http://contoso.com/movies/edit/2`

由于路由模板如下所示， `{controller=Home}/{action=Index}/{id?}`，`movies/edit/2`将路由到`Movies`控制器，并将其`Edit`操作方法。 它还会接受可选参数调用`id`。 操作方法的代码应如下所示：

<!-- literal_block {"ids": [], "linenos": true, "xml:space": "preserve", "language": "csharp"} -->

```csharp
public IActionResult Edit(int? id)
   ```

注意： URL 路由中的字符串不区分大小写。

MVC 将尝试按名称绑定到的操作参数的请求数据。 MVC 将查找每个参数值使用参数名称和对其公共可设置属性的名称。 在上面的示例中，唯一的操作参数名为`id`，这是 MVC 将绑定到具有相同名称的路由值中的值。 除了路由值 MVC 将绑定数据从请求的各个部分，它会按一定顺序。 下面是模型绑定查找通过它们的顺序中的数据源的列表：

1. `Form values`： 这些是转到 HTTP 请求使用 POST 方法中的窗体值。 （包括 jQuery POST 请求）。

2. `Route values`： 的一套路由值由提供[路由](../../fundamentals/routing.md)

3. `Query strings`: 查询字符串的 URI 一部分。

<!-- DocFX BUG
The link works but generates an error when building with DocFX
@fundamentals/routing
[Routing](xref:fundamentals/routing)
-->

注意： 形成值、 路由数据和查询字符串以名称-值对的形式存储。

由于模型绑定索要名为的项`id`和无需进行任何名为`id`在窗体值，它移到路由值查找该注册表项。 在本示例中，它是一个匹配项。 绑定发生，并且的值转换为 2 的整数。 使用编辑 (字符串 id) 的同一请求会将转换为字符串"2"。

到目前为止的示例使用简单类型。 在 MVC 简单类型是任何.NET 基元类型或与字符串类型转换器的类型。 如果该操作方法的参数是一个类，如`Movie`类型，因为属性，MVC 的模型绑定将仍可以很好地处理包含简单和复杂类型。 它使用反射和递归遍历查找匹配项的复杂类型的属性。 模型绑定寻找模式*parameter_name.property_name*要绑定到属性的值。 如果找不到此窗体的匹配值，它将尝试绑定使用只是属性的名称。 如这些类型的`Collection`类型，模型绑定将查找匹配项到*parameter_name [index]*或仅仅称为*[index]*。 模型绑定视为`Dictionary`类型与此类似，要求你*parameter_name [key]*或仅仅称为*[key]*，只要的键是简单类型。 支持的密钥匹配的字段名称 HTML 和为相同的模型类型生成的标记帮助程序。 这样，往返值，以便窗体字段时，保持填充用户的输入为其方便起见，例如，从创建或编辑的绑定的数据未通过验证。

绑定发生顺序中此类必须具有公共默认构造函数和绑定的成员必须是公共的可写属性。 在模型绑定情况下将仅为类实例化使用公共默认构造函数，可以设置的属性。

当参数绑定时，模型绑定停止查找 /sample/2015-04-16/17 具有该名称的值和它将在移动下一步的参数绑定。 如果绑定将失败，MVC 将不会引发错误。 你可以通过检查来查询模型状态错误`ModelState.IsValid`属性。

注意： 控制器的每个条目`ModelState`属性是`ModelStateEntry`包含`Errors property`。 它很少需要自行查询此集合。 请改用 `ModelState.IsValid` 。

此外，有 MVC 执行模型绑定时必须考虑一些特殊的数据类型：

* `IFormFile``IEnumerable<IFormFile>`： 一个或多个已上载的文件的 HTTP 请求的一部分。

* `CancelationToken`： 用于取消异步控制器中的活动。

这些类型可以绑定到操作参数或属性上的类类型。

模型绑定完成后，[验证](validation.md)时发生。 默认模型绑定的工作方式非常适用于大多数开发方案。 它是也可扩展，因此如果你有唯一的需求，你可以自定义内置行为。

## <a name="customize-model-binding-behavior-with-attributes"></a>自定义模型具有属性的绑定行为

MVC 包含某些特性，可用于将定向到不同的源其默认模型绑定行为。 例如，可以指定绑定是否需要对于属性，或如果它不应该使用而根本发生`[BindRequired]`或`[BindNever]`属性。 或者，你可以重写默认的数据源，并指定模型联编程序的数据源。 下面是模型绑定特性的列表：

* `[BindRequired]`： 此属性将添加模型状态错误，如果绑定不能出现。

* `[BindNever]`： 指示模型联编程序永远不会将绑定到此参数。

* `[FromHeader]``[FromQuery]`， `[FromRoute]`， `[FromForm]`： 使用它们来指定你想要应用的确切绑定源。

* `[FromServices]`： 此属性使用[依赖关系注入](../../fundamentals/dependency-injection.md)若要将参数绑定从服务。

* `[FromBody]`： 用于配置格式化程序将数据绑定中请求正文。 根据请求内容类型，格式化程序处于选中状态。

* `[ModelBinder]`： 用于重写默认模型联编程序、 绑定源和名称。

属性是非常有用的工具时需要重写模型绑定的默认行为。

## <a name="binding-formatted-data-from-the-request-body"></a>从请求正文的格式的绑定数据

请求数据可以来自各种包括 JSON、 XML 和许多其他的格式。 当你使用 [FromBody] 属性以指示你想要将参数绑定到请求正文中的数据时，MVC 将使用一组配置的格式化程序来处理基于其内容类型的请求数据。 默认情况下 MVC 包括`JsonInputFormatter`类处理 JSON 数据，但你可以添加用于处理 XML 和其他自定义格式的其他格式化程序。

> [!NOTE]
> 可以有最多一个参数，每个操作使用修饰`[FromBody]`。 ASP.NET 核心 MVC 运行时委托责任的格式化程序读取请求流。 一旦参数读取请求流，它通常不是可以读取请求流再次为其他绑定`[FromBody]`参数。

> [!NOTE]
> `JsonInputFormatter`是默认的格式化程序和基于[Json.NET](https://www.newtonsoft.com/json)。

ASP.NET 选择基于输入格式化程序[内容类型](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)标头和类型的参数，除非没有应用于它否则指定的特性。 如果你想要使用的 XML 或另一种格式必须配置在*Startup.cs*文件，但你可能会首先必须获取对引用`Microsoft.AspNetCore.Mvc.Formatters.Xml`使用 NuGet。 启动代码应如下所示：

<!-- literal_block {"ids": [], "linenos": true, "xml:space": "preserve", "language": "csharp"} -->

```csharp
public void ConfigureServices(IServiceCollection services)
   {
       services.AddMvc()
          .AddXmlSerializerFormatters();
   }
   ```

中的代码*Startup.cs*文件包含`ConfigureServices`方法替换`services`可用于为 ASP.NET 应用程序的服务生成的自变量。 在示例中，我们要添加为 MVC 将为此应用程序提供服务的 XML 格式化程序。 `options`自变量传递给`AddMvc`方法可用于添加和管理筛选器、 格式化程序和其他系统选项利用 MVC，在应用启动后。 然后应用`Consumes`属性设为控制器类或操作方法要使用所需的格式。

### <a name="custom-model-binding"></a>自定义模型绑定

可以通过编写自己的自定义模型联编程序扩展模型绑定。 详细了解[自定义模型绑定](../advanced/custom-model-binding.md)。
