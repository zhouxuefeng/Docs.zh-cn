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
ms.openlocfilehash: af3b2174c73583832868d2062e6c7ab4689a1229
ms.sourcegitcommit: 9d3f27a1ee5b7014fb40e4f2ec9b2a9cd744751c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2017
---
# <a name="custom-formatters-in-aspnet-core-mvc-web-apis"></a><span data-ttu-id="76128-104">ASP.NET 核心 MVC web Api 中的自定义格式化程序</span><span class="sxs-lookup"><span data-stu-id="76128-104">Custom formatters in ASP.NET Core MVC web APIs</span></span>

<span data-ttu-id="76128-105">通过[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="76128-105">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="76128-106">ASP.NET 核心 MVC 通过使用 JSON、 XML 或纯文本格式，web Api 中具有对数据交换的内置支持。</span><span class="sxs-lookup"><span data-stu-id="76128-106">ASP.NET Core MVC has built-in support for data exchange in web APIs by using JSON, XML, or plain text formats.</span></span> <span data-ttu-id="76128-107">这篇文章演示如何通过创建自定义格式化程序添加对其他格式的支持。</span><span class="sxs-lookup"><span data-stu-id="76128-107">This article shows how to add support for additional formats by creating custom formatters.</span></span>

<span data-ttu-id="76128-108">[查看或从 GitHub 下载示例](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/Sample)。</span><span class="sxs-lookup"><span data-stu-id="76128-108">[View or download sample from GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/Sample).</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="76128-109">何时使用自定义格式化程序</span><span class="sxs-lookup"><span data-stu-id="76128-109">When to use custom formatters</span></span>

<span data-ttu-id="76128-110">如果你希望使用自定义格式化程序[内容协商](xref:mvc/models/formatting)过程，以支持所不支持内置的格式化程序 （JSON、 XML 和纯文本） 的内容类型。</span><span class="sxs-lookup"><span data-stu-id="76128-110">Use a custom formatter when you want the [content negotiation](xref:mvc/models/formatting) process to support a content type that isn't supported by the built-in formatters (JSON, XML, and plain text).</span></span>

<span data-ttu-id="76128-111">例如，如果你的 web API 的客户端的一些可以处理[Protobuf](https://github.com/google/protobuf)格式，你可能想要与这些客户端使用 Protobuf，因为它是更高效。</span><span class="sxs-lookup"><span data-stu-id="76128-111">For example, if some of the clients for your web API can handle the [Protobuf](https://github.com/google/protobuf) format, you might want to use Protobuf with those clients because it's more efficient.</span></span>  <span data-ttu-id="76128-112">或者，你可能希望你的 web API 发送联系人姓名和地址中的[vCard](https://en.wikipedia.org/wiki/VCard)格式、 用于交换联系人数据的常用的格式。</span><span class="sxs-lookup"><span data-stu-id="76128-112">Or you might want your web API to send contact names and addresses in [vCard](https://en.wikipedia.org/wiki/VCard) format, a commonly used format for exchanging contact data.</span></span> <span data-ttu-id="76128-113">提供与本文的示例应用程序实现简单 vCard 格式化程序。</span><span class="sxs-lookup"><span data-stu-id="76128-113">The sample app provided with this article implements a simple vCard formatter.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="76128-114">如何使用自定义格式化程序概述</span><span class="sxs-lookup"><span data-stu-id="76128-114">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="76128-115">下面是创建和使用自定义格式化程序的步骤：</span><span class="sxs-lookup"><span data-stu-id="76128-115">Here are the steps to create and use a custom formatter:</span></span>

* <span data-ttu-id="76128-116">如果你想要序列化数据发送到客户端，请创建一个输出格式化程序类。</span><span class="sxs-lookup"><span data-stu-id="76128-116">Create an output formatter class if you want to serialize data to send to the client.</span></span>
* <span data-ttu-id="76128-117">如果你想要反序列化从客户端接收的数据，请创建一个输入的格式化程序类。</span><span class="sxs-lookup"><span data-stu-id="76128-117">Create an input formatter class if you want to deserialize data received from the client.</span></span> 
* <span data-ttu-id="76128-118">添加到你格式化程序的实例`InputFormatters`和`OutputFormatters`中的集合[MvcOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.mvcoptions)。</span><span class="sxs-lookup"><span data-stu-id="76128-118">Add instances of your formatters to the `InputFormatters` and `OutputFormatters` collections in [MvcOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.mvcoptions).</span></span>

<span data-ttu-id="76128-119">下列各节提供有关每个步骤指南和代码示例。</span><span class="sxs-lookup"><span data-stu-id="76128-119">The following sections provide guidance and code examples for each of these steps.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="76128-120">如何创建自定义格式化程序类</span><span class="sxs-lookup"><span data-stu-id="76128-120">How to create a custom formatter class</span></span>

<span data-ttu-id="76128-121">若要创建一个格式化程序：</span><span class="sxs-lookup"><span data-stu-id="76128-121">To create a formatter:</span></span>

* <span data-ttu-id="76128-122">从适当的基类派生的类。</span><span class="sxs-lookup"><span data-stu-id="76128-122">Derive the class from the appropriate base class.</span></span>
* <span data-ttu-id="76128-123">构造函数中指定有效的媒体类型和编码。</span><span class="sxs-lookup"><span data-stu-id="76128-123">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="76128-124">重写`CanReadType` / `CanWriteType`方法</span><span class="sxs-lookup"><span data-stu-id="76128-124">Override `CanReadType`/`CanWriteType` methods</span></span>
* <span data-ttu-id="76128-125">重写`ReadRequestBodyAsync` / `WriteResponseBodyAsync`方法</span><span class="sxs-lookup"><span data-stu-id="76128-125">Override `ReadRequestBodyAsync`/`WriteResponseBodyAsync` methods</span></span>
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="76128-126">派生自适当的基类</span><span class="sxs-lookup"><span data-stu-id="76128-126">Derive from the appropriate base class</span></span>

<span data-ttu-id="76128-127">对于文本媒体类型 (例如，vCard)，派生自[TextInputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textinputformatter)或[TextOutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter)基类。</span><span class="sxs-lookup"><span data-stu-id="76128-127">For text media types (for example, vCard), derive from the [TextInputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) or [TextOutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) base class.</span></span>

<span data-ttu-id="76128-128">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]</span><span class="sxs-lookup"><span data-stu-id="76128-128">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]</span></span>

<span data-ttu-id="76128-129">对于二进制类型，派生自[InputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.inputformatter)或[OutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformatter)基类。</span><span class="sxs-lookup"><span data-stu-id="76128-129">For binary types, derive from the [InputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.inputformatter) or [OutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformatter) base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="76128-130">请指定有效的媒体类型和编码</span><span class="sxs-lookup"><span data-stu-id="76128-130">Specify valid media types and encodings</span></span>

<span data-ttu-id="76128-131">在构造函数中，指定有效的媒体类型和编码中通过将添加到`SupportedMediaTypes`和`SupportedEncodings`集合。</span><span class="sxs-lookup"><span data-stu-id="76128-131">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

<span data-ttu-id="76128-132">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]</span><span class="sxs-lookup"><span data-stu-id="76128-132">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]</span></span>

> [!NOTE]  
> <span data-ttu-id="76128-133">无法执行操作格式化程序类中的构造函数依赖关系注入。</span><span class="sxs-lookup"><span data-stu-id="76128-133">You can't do constructor dependency injection in a formatter class.</span></span> <span data-ttu-id="76128-134">例如，不能通过将记录器参数添加到构造函数来获得一个记录器。</span><span class="sxs-lookup"><span data-stu-id="76128-134">For example, you can't get a logger by adding a logger parameter to the constructor.</span></span> <span data-ttu-id="76128-135">若要访问服务，你必须使用获取传递给你的方法的上下文对象。</span><span class="sxs-lookup"><span data-stu-id="76128-135">To access services, you have to use the context object that gets passed in to your methods.</span></span> <span data-ttu-id="76128-136">代码示例[下面](#read-write)演示如何执行此操作。</span><span class="sxs-lookup"><span data-stu-id="76128-136">A code example [below](#read-write) shows how to do this.</span></span>

### <a name="override-canreadtypecanwritetype"></a><span data-ttu-id="76128-137">重写 CanReadType/CanWriteType</span><span class="sxs-lookup"><span data-stu-id="76128-137">Override CanReadType/CanWriteType</span></span> 

<span data-ttu-id="76128-138">指定的类型可以反序列化为，也可以通过重写从序列化`CanReadType`或`CanWriteType`方法。</span><span class="sxs-lookup"><span data-stu-id="76128-138">Specify the type you can deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="76128-139">例如，你可能仅能以创建从 vCard 文本`Contact`类型，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="76128-139">For example, you might only be able to create vCard text from a `Contact` type and vice versa.</span></span>

<span data-ttu-id="76128-140">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]</span><span class="sxs-lookup"><span data-stu-id="76128-140">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]</span></span>

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="76128-141">CanWriteResult 方法</span><span class="sxs-lookup"><span data-stu-id="76128-141">The CanWriteResult method</span></span>

<span data-ttu-id="76128-142">在某些情况下您必须重写`CanWriteResult`而不是`CanWriteType`。</span><span class="sxs-lookup"><span data-stu-id="76128-142">In some scenarios you have to override `CanWriteResult` instead of `CanWriteType`.</span></span> <span data-ttu-id="76128-143">使用`CanWriteResult`如果满足以下条件：</span><span class="sxs-lookup"><span data-stu-id="76128-143">Use `CanWriteResult` if the following conditions are true:</span></span>

  * <span data-ttu-id="76128-144">你的操作方法会返回一个模型类。</span><span class="sxs-lookup"><span data-stu-id="76128-144">Your action method returns a model class.</span></span>
  * <span data-ttu-id="76128-145">有可能在运行时返回的派生的类。</span><span class="sxs-lookup"><span data-stu-id="76128-145">There are derived classes which might be returned at runtime.</span></span>
  * <span data-ttu-id="76128-146">你需要知道在运行时该派生类返回由该操作。</span><span class="sxs-lookup"><span data-stu-id="76128-146">You need to know at runtime which derived class was returned by the action.</span></span>  

<span data-ttu-id="76128-147">例如，假设你的操作方法签名返回`Person`类型，但它可能返回`Student`或`Instructor`派生自的类型`Person`。</span><span class="sxs-lookup"><span data-stu-id="76128-147">For example, suppose your action method signature returns a `Person` type, but it may return a `Student` or `Instructor` type that derives from `Person`.</span></span> <span data-ttu-id="76128-148">如果你想将格式化程序来仅处理`Student`对象，请检查的一种[对象](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object)到提供的上下文对象中`CanWriteResult`方法。</span><span class="sxs-lookup"><span data-stu-id="76128-148">If you want your formatter to handle only `Student` objects, check the type of [Object](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="76128-149">请注意，不需要使用`CanWriteResult`操作方法返回时`IActionResult`; 在这种情况下，`CanWriteType`方法接收的运行时类型。</span><span class="sxs-lookup"><span data-stu-id="76128-149">Note that it's not necessary to use `CanWriteResult` when the action method returns `IActionResult`; in that case, the `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>
### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a><span data-ttu-id="76128-150">重写 ReadRequestBodyAsync/WriteResponseBodyAsync</span><span class="sxs-lookup"><span data-stu-id="76128-150">Override ReadRequestBodyAsync/WriteResponseBodyAsync</span></span> 

<span data-ttu-id="76128-151">执行反序列化或进行序列化的实际工作`ReadRequestBodyAsync`或`WriteResponseBodyAsync`。</span><span class="sxs-lookup"><span data-stu-id="76128-151">You do the actual work of deserializing or serializing in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span>  <span data-ttu-id="76128-152">突出显示的行，在下面的示例演示如何获取服务从依赖关系注入容器 （不能从构造函数参数获取它们）。</span><span class="sxs-lookup"><span data-stu-id="76128-152">The highlighted lines in the following example show how to get services from the dependency injection container (you can't get them from constructor parameters).</span></span>

<span data-ttu-id="76128-153">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]</span><span class="sxs-lookup"><span data-stu-id="76128-153">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]</span></span>

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="76128-154">如何配置以使用自定义格式化程序的 MVC</span><span class="sxs-lookup"><span data-stu-id="76128-154">How to configure MVC to use a custom formatter</span></span>
 
<span data-ttu-id="76128-155">若要使用自定义格式化程序，请将格式化程序类的一个实例添加`InputFormatters`或`OutputFormatters`集合。</span><span class="sxs-lookup"><span data-stu-id="76128-155">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

<span data-ttu-id="76128-156">[!code-csharp[Main](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]</span><span class="sxs-lookup"><span data-stu-id="76128-156">[!code-csharp[Main](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]</span></span>

<span data-ttu-id="76128-157">格式化程序将它们插入顺序计算。</span><span class="sxs-lookup"><span data-stu-id="76128-157">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="76128-158">第一个将优先。</span><span class="sxs-lookup"><span data-stu-id="76128-158">The first one takes precedence.</span></span> 

## <a name="next-steps"></a><span data-ttu-id="76128-159">后续步骤</span><span class="sxs-lookup"><span data-stu-id="76128-159">Next steps</span></span>

<span data-ttu-id="76128-160">请参阅[示例应用程序](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/Sample)，该类可实现简单 vCard 输入和输出格式化程序。</span><span class="sxs-lookup"><span data-stu-id="76128-160">See the [sample application](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/Sample), which implements simple vCard input and output formatters.</span></span>  <span data-ttu-id="76128-161">应用程序读取和写入名片类似下面的示例所示：</span><span class="sxs-lookup"><span data-stu-id="76128-161">The application reads and writes vCards that look like the following example:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
UID:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

<span data-ttu-id="76128-162">若要查看 vCard 输出，请运行应用程序，将接受的 Get 请求发送标头"文本/vcard"到`http://localhost:63313/api/contacts/`（Visual Studio 中运行） 时或`http://localhost:5000/api/contacts/`（当从命令行运行）。</span><span class="sxs-lookup"><span data-stu-id="76128-162">To see vCard output, run the application and send a Get request with Accept header "text/vcard" to `http://localhost:63313/api/contacts/` (when running from Visual Studio) or `http://localhost:5000/api/contacts/` (when running from the command line).</span></span>

<span data-ttu-id="76128-163">若要添加到内存中集合的联系人的 vCard，请向相同的 URL，内容类型标头"文本/vcard"和在正文中，如上面的示例格式 vCard 文本发送 Post 请求。</span><span class="sxs-lookup"><span data-stu-id="76128-163">To add a vCard to the in-memory collection of contacts, send a Post request to the same URL, with Content-Type header "text/vcard" and with vCard text in the body, formatted like the example above.</span></span>
