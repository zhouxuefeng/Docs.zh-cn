---
title: "ASP.NET 核心 MVC web Api 中的自定义格式化程序"
author: tdykstra
description: "了解如何创建和自定义格式化程序用于 ASP.NET Core 中的 web Api。"
keywords: "ASP.NET 核心 web api，自定义格式化程序"
ms.author: tdykstra
manager: wpickett
ms.date: 02/08/2017
ms.topic: article
ms.assetid: 1fb6fdc2-e199-4469-9012-b909d1913422
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/models/custom-formatters
ms.openlocfilehash: 0285b40cfacb79745d3a6488401677130f55a95b
ms.sourcegitcommit: 6ece943781d8a56784bb6160f14da85210d3fcea
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2017
---
# <a name="custom-formatters-in-aspnet-core-mvc-web-apis"></a>ASP.NET 核心 MVC web Api 中的自定义格式化程序

通过[Tom Dykstra](https://github.com/tdykstra)

ASP.NET 核心 MVC 通过使用 JSON、 XML 或纯文本格式，web Api 中具有对数据交换的内置支持。 这篇文章演示如何通过创建自定义格式化程序添加对其他格式的支持。

[查看或从 GitHub 下载示例](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/sample)。

## <a name="when-to-use-custom-formatters"></a>何时使用自定义格式化程序

如果你希望使用自定义格式化程序[内容协商](xref:mvc/models/formatting)过程，以支持所不支持内置的格式化程序 （JSON、 XML 和纯文本） 的内容类型。

例如，如果你的 web API 的客户端的一些可以处理[Protobuf](https://github.com/google/protobuf)格式，你可能想要与这些客户端使用 Protobuf，因为它是更高效。  或者，你可能希望你的 web API 发送联系人姓名和地址中的[vCard](https://en.wikipedia.org/wiki/VCard)格式、 用于交换联系人数据的常用的格式。 提供与本文的示例应用程序实现简单 vCard 格式化程序。

## <a name="overview-of-how-to-use-a-custom-formatter"></a>如何使用自定义格式化程序概述

下面是创建和使用自定义格式化程序的步骤：

* 如果你想要序列化数据发送到客户端，请创建一个输出格式化程序类。
* 如果你想要反序列化从客户端接收的数据，请创建一个输入的格式化程序类。 
* 添加到你格式化程序的实例`InputFormatters`和`OutputFormatters`中的集合[MvcOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.mvcoptions)。

下列各节提供有关每个步骤指南和代码示例。

## <a name="how-to-create-a-custom-formatter-class"></a>如何创建自定义格式化程序类

若要创建一个格式化程序：

* 从适当的基类派生的类。
* 构造函数中指定有效的媒体类型和编码。
* 重写`CanReadType` / `CanWriteType`方法
* 重写`ReadRequestBodyAsync` / `WriteResponseBodyAsync`方法
  
### <a name="derive-from-the-appropriate-base-class"></a>派生自适当的基类

对于文本媒体类型 (例如，vCard)，派生自[TextInputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textinputformatter)或[TextOutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter)基类。

[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

对于二进制类型，派生自[InputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.inputformatter)或[OutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformatter)基类。

### <a name="specify-valid-media-types-and-encodings"></a>请指定有效的媒体类型和编码

在构造函数中，指定有效的媒体类型和编码中通过将添加到`SupportedMediaTypes`和`SupportedEncodings`集合。

[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

> [!NOTE]  
> 无法执行操作格式化程序类中的构造函数依赖关系注入。 例如，不能通过将记录器参数添加到构造函数来获得一个记录器。 若要访问服务，你必须使用获取传递给你的方法的上下文对象。 代码示例[下面](#read-write)演示如何执行此操作。

### <a name="override-canreadtypecanwritetype"></a>重写 CanReadType/CanWriteType 

指定的类型可以反序列化为，也可以通过重写从序列化`CanReadType`或`CanWriteType`方法。 例如，你可能仅能以创建从 vCard 文本`Contact`类型，反之亦然。

[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

#### <a name="the-canwriteresult-method"></a>CanWriteResult 方法

在某些情况下您必须重写`CanWriteResult`而不是`CanWriteType`。 使用`CanWriteResult`如果满足以下条件：

  * 你的操作方法会返回一个模型类。
  * 有可能在运行时返回的派生的类。
  * 你需要知道在运行时该派生类返回由该操作。  

例如，假设你的操作方法签名返回`Person`类型，但它可能返回`Student`或`Instructor`派生自的类型`Person`。 如果你想将格式化程序来仅处理`Student`对象，请检查的一种[对象](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object)到提供的上下文对象中`CanWriteResult`方法。 请注意，不需要使用`CanWriteResult`操作方法返回时`IActionResult`; 在这种情况下，`CanWriteType`方法接收的运行时类型。

<a id="read-write"></a>
### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a>重写 ReadRequestBodyAsync/WriteResponseBodyAsync 

执行反序列化或进行序列化的实际工作`ReadRequestBodyAsync`或`WriteResponseBodyAsync`。  突出显示的行，在下面的示例演示如何获取服务从依赖关系注入容器 （不能从构造函数参数获取它们）。

[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>如何配置以使用自定义格式化程序的 MVC
 
若要使用自定义格式化程序，请将格式化程序类的一个实例添加`InputFormatters`或`OutputFormatters`集合。

[!code-csharp[Main](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

格式化程序将它们插入顺序计算。 第一个将优先。 

## <a name="next-steps"></a>后续步骤

请参阅[示例应用程序](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/sample)，该类可实现简单 vCard 输入和输出格式化程序。  应用程序读取和写入名片类似下面的示例所示：

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
UID:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

若要查看 vCard 输出，请运行应用程序，将接受的 Get 请求发送标头"文本/vcard"到`http://localhost:63313/api/contacts/`（Visual Studio 中运行） 时或`http://localhost:5000/api/contacts/`（当从命令行运行）。

若要添加到内存中集合的联系人的 vCard，请向相同的 URL，内容类型标头"文本/vcard"和在正文中，如上面的示例格式 vCard 文本发送 Post 请求。
