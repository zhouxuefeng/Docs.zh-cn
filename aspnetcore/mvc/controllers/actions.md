---
title: "具有 ASP.NET 核心 mvc 控制器处理请求"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 07/03/2017
ms.topic: article
ms.assetid: 9da9eb52-8583-4069-af91-155ba3529d7f
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/actions
ms.openlocfilehash: 5dc6c7dc70027bb79875f389d535119a2543b873
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="handling-requests-with-controllers-in-aspnet-core-mvc"></a>具有 ASP.NET 核心 mvc 控制器处理请求

通过[Steve Smith](https://ardalis.com/)和[Scott Addie](https://github.com/scottaddie)

控制器、 操作和操作结果是开发人员如何构建使用 ASP.NET 核心 MVC 应用程序的基本组成部分。

## <a name="what-is-a-controller"></a>什么是控制器？

控制器用于定义和一组操作。 操作 (或*操作方法*) 是一种方法在处理请求的控制器上。 控制器按逻辑组类似的操作。 这种操作的聚合允许组通用的规则，如路由、 缓存和授权，共同应用。 请求映射到操作通过[路由](xref:mvc/controllers/routing)。

按照约定，控制器类：
* 驻留在项目的根级别*控制器*文件夹
* 继承自`Microsoft.AspNetCore.Mvc.Controller`

控制器是至少一个以下条件为 true 可实例化类：
* 类名称后缀"controller"
* 此类从其名称后缀"controller"的类继承
* 类用修饰`[Controller]`属性

控制器类必须具有关联`[NonController]`属性。

控制器应遵循[显式依赖关系原则](http://deviq.com/explicit-dependencies-principle/)。 有几个方法来实现这一原则。 如果多个控制器操作需要相同的服务，请考虑使用[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)请求这些依赖关系。 如果仅的单个操作方法必须使用该服务，请考虑使用[操作注入](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)请求依赖项。

在**M**odel-**V**查看-**C**ontroller 模式中，控制器负责初始处理的请求，并实例化的模型。 通常情况下，应在模型内执行业务决策。

控制器采用模型的处理 （如果有） 的结果，并返回适当的视图和其关联的视图数据或 API 调用的结果。 了解更多信息，请访问[ASP.NET 核心 MVC 的概述](xref:mvc/overview)和[ASP.NET 核心 MVC 和 Visual Studio 入门](xref:tutorials/first-mvc-app/start-mvc)。

在控制器*UI 级别*抽象。 其职责是以确保请求数据有效并选择应返回哪些视图 （或 API 的结果）。 在分解应用中，它不直接包含数据访问或业务逻辑。 相反，控制器委托给处理这些职责的服务。

## <a name="defining-actions"></a>定义操作

在控制器上的公共方法，除使用修饰`[NonAction]`属性，操作。 操作的参数绑定到请求的数据和使用验证[模型绑定](xref:mvc/models/model-binding)。 为模型绑定的所有操作都将执行模型验证。 `ModelState.IsValid`属性值指示模型绑定和验证是否成功。

操作方法应包含将请求映射到业务问题的逻辑。 业务关注点通常应表示为服务，它通过访问控制器[依赖关系注入](xref:mvc/controllers/dependency-injection)。 操作则映射到应用程序状态的业务操作的结果。

操作可以返回任何内容，但经常返回的实例`IActionResult`(或`Task<IActionResult>`为异步方法)，生成响应。 操作方法负责选择*哪种类型的响应*。 操作结果*未响应*。

### <a name="controller-helper-methods"></a>控制器帮助器方法

控制器通常继承自[控制器](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller)，尽管这不是必需。 派生自`Controller`提供对三个类别的帮助器方法的访问：

#### <a name="1-methods-resulting-in-an-empty-response-body"></a>1.导致一个空的响应正文的方法

不`Content-Type`HTTP 响应标头是包括在内，因为响应正文中没有描述的内容。

有两种结果类型，此类别中： 重定向，HTTP 状态代码。

* **HTTP 状态代码**

    此类型返回 HTTP 状态代码。 此类型的几个帮助器方法是`BadRequest`， `NotFound`，和`Ok`。 例如，`return BadRequest();`产生 400 状态代码执行时。 当等方法`BadRequest`， `NotFound`，和`Ok`是重载，它们不再符合条件作为 HTTP 状态代码的响应方，因为内容协商正在进行。

* **重定向**

    此类型返回到的重定向操作或目标 (使用`Redirect`， `LocalRedirect`， `RedirectToAction`，或`RedirectToRoute`)。 例如，`return RedirectToAction("Complete", new {id = 123});`将重定向到`Complete`，传递一个匿名的对象。

    重定向结果类型不同于主要中添加的 HTTP 状态代码类型`Location`HTTP 响应标头。

#### <a name="2-methods-resulting-in-a-non-empty-response-body-with-a-predefined-content-type"></a>2.导致与预定义的内容类型的非空响应正文的方法

此类别中的大多数帮助器方法包括`ContentType`属性，允许您设置`Content-Type`响应标头来描述响应正文。

此类别中的两种结果类型：[视图](xref:mvc/views/overview)和[格式化响应](xref:mvc/models/formatting)。

* **视图**

    此类型返回的视图模型用于呈现 HTML。 例如，`return View(customer);`将模型传递给数据绑定的视图。

* **格式的响应**

    此类型返回 JSON 或类似的数据交换格式以特定方式表示的对象。 例如，`return Json(customer);`所提供的对象序列化为 JSON 格式。
    
    此类型的其他常见方法包括`File`， `PhysicalFile`，和`VirtualFile`。 例如，`return PhysicalFile(customerFilePath, "text/xml");`返回由描述 XML 文件`Content-Type`响应标头值为"text/xml"。

#### <a name="3-methods-resulting-in-a-non-empty-response-body-formatted-in-a-content-type-negotiated-with-the-client"></a>3.非空响应正文中生成的方法进行格式化的内容类型与客户端协商

此类别是更好地称为**内容协商**。 [内容协商](xref:mvc/models/formatting#content-negotiation)每当操作返回[ObjectResult](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.objectresult)类型或以外的其他[IActionResult](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.iactionresult)实现。 返回一个非操作`IActionResult`实现 (例如， `object`) 也会返回格式的响应。

此类型的一些帮助器方法包括`BadRequest`， `CreatedAtRoute`，和`Ok`。 这些方法的示例包括`return BadRequest(modelState);`， `return CreatedAtRoute("routename", values, newobject);`，和`return Ok(value);`分别。 请注意，`BadRequest`和`Ok`执行内容协商仅时传递了一个值，而无需传递一个值，它们而是充当 HTTP 状态代码结果类型。 `CreatedAtRoute`方法，另一方面，始终执行内容协商以来所有要求，将值传递其重载。

### <a name="cross-cutting-concerns"></a>跨领域问题

应用程序通常共享其流的各个部分。 示例包括的应用程序需要身份验证才能访问购物车或在某些页缓存数据的应用。 若要执行逻辑之前, 或之后的操作方法，使用*筛选器*。 使用[筛选器](xref:mvc/controllers/filters)上的跨领域问题可以减少重复项，从而使它们可以按照[不重复自己 （模拟） 原则](http://deviq.com/don-t-repeat-yourself/)。

最筛选属性，如`[Authorize]`，可以在控制器或操作级别取决于所需的粒度级别应用。

错误处理和响应缓存通常是跨领域问题：
   * [错误处理](xref:mvc/controllers/filters#exception-filters)
   * [响应缓存](xref:performance/caching/response)

可以使用筛选器或自定义处理许多跨领域问题[中间件](xref:fundamentals/middleware)。
