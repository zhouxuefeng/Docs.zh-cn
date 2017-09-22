---
title: "自定义模型绑定"
author: ardalis
description: "自定义 ASP.NET 核心 mvc 模型绑定。"
keywords: "ASP.NET 核心、 模型绑定、 自定义模型联编程序"
ms.author: riande
manager: wpickett
ms.date: 04/10/2017
ms.topic: article
ms.assetid: ebd98159-a028-4a94-b06c-43981c79c6be
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: f3fc3d624c3b79d49a886dd85ca8b19147631e39
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="custom-model-binding"></a><span data-ttu-id="b389b-104">自定义模型绑定</span><span class="sxs-lookup"><span data-stu-id="b389b-104">Custom Model Binding</span></span>

<span data-ttu-id="b389b-105">通过[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="b389b-105">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b389b-106">模型绑定允许要直接使用模型 （传递类型中作为方法自变量），而是比 HTTP 请求的控制器操作。</span><span class="sxs-lookup"><span data-stu-id="b389b-106">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="b389b-107">模型联编程序由处理传入的请求数据和应用程序模型之间的映射。</span><span class="sxs-lookup"><span data-stu-id="b389b-107">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="b389b-108">开发人员可以通过实现自定义模型联编程序 （但通常情况下，你无需编写自己的提供程序） 扩展的内置模型绑定功能。</span><span class="sxs-lookup"><span data-stu-id="b389b-108">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

[<span data-ttu-id="b389b-109">查看或从 GitHub 下载示例</span><span class="sxs-lookup"><span data-stu-id="b389b-109">View or download sample from GitHub</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/)

## <a name="default-model-binder-limitations"></a><span data-ttu-id="b389b-110">默认模型联编程序限制</span><span class="sxs-lookup"><span data-stu-id="b389b-110">Default model binder limitations</span></span>

<span data-ttu-id="b389b-111">默认模型联编程序支持大多数常见.NET 核心数据类型，并且应满足大多数开发人员的需求。</span><span class="sxs-lookup"><span data-stu-id="b389b-111">The default model binders support most of the common .NET Core data types and should meet most developers\` needs.</span></span> <span data-ttu-id="b389b-112">他们希望基于文本的输入请求将直接绑定到模型类型。</span><span class="sxs-lookup"><span data-stu-id="b389b-112">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="b389b-113">你可能需要转换之前将其绑定的输入。</span><span class="sxs-lookup"><span data-stu-id="b389b-113">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="b389b-114">例如，如果你具有可用于查找模型数据的密钥。</span><span class="sxs-lookup"><span data-stu-id="b389b-114">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="b389b-115">自定义模型联编程序可用于获取基于键的数据。</span><span class="sxs-lookup"><span data-stu-id="b389b-115">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="b389b-116">模型绑定评审</span><span class="sxs-lookup"><span data-stu-id="b389b-116">Model binding review</span></span>

<span data-ttu-id="b389b-117">模型绑定使用它进行操作的类型特定的定义。</span><span class="sxs-lookup"><span data-stu-id="b389b-117">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="b389b-118">A*简单类型*从输入中的单个字符串转换。</span><span class="sxs-lookup"><span data-stu-id="b389b-118">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="b389b-119">A*复杂类型*转换从多个输入值。</span><span class="sxs-lookup"><span data-stu-id="b389b-119">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="b389b-120">框架确定基于是否存在的差异`TypeConverter`。</span><span class="sxs-lookup"><span data-stu-id="b389b-120">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="b389b-121">我们建议你创建的类型转换器，如果提供了一个简单`string`  ->  `SomeType`不需要外部资源的映射。</span><span class="sxs-lookup"><span data-stu-id="b389b-121">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="b389b-122">在创建之前你自己的自定义模型联编程序，它是活页夹外还实现值得查看如何将现有模型。</span><span class="sxs-lookup"><span data-stu-id="b389b-122">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="b389b-123">请考虑[ByteArrayModelBinder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinder)这可以用于将 base64 编码字符串转换为字节数组。</span><span class="sxs-lookup"><span data-stu-id="b389b-123">Consider the [ByteArrayModelBinder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinder) which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="b389b-124">字节数组通常存储为文件或数据库 BLOB 字段中。</span><span class="sxs-lookup"><span data-stu-id="b389b-124">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="b389b-125">使用 ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="b389b-125">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="b389b-126">Base64 编码字符串可以用于表示二进制数据。</span><span class="sxs-lookup"><span data-stu-id="b389b-126">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="b389b-127">例如下, 图可以编码为字符串。</span><span class="sxs-lookup"><span data-stu-id="b389b-127">For example, the following image can be encoded as a string.</span></span>

<span data-ttu-id="b389b-128">![dotnet bot](custom-model-binding/images/bot.png "dotnet bot")</span><span class="sxs-lookup"><span data-stu-id="b389b-128">![dotnet bot](custom-model-binding/images/bot.png "dotnet bot")</span></span>

<span data-ttu-id="b389b-129">已编码的字符串的一小部分如下图所示：</span><span class="sxs-lookup"><span data-stu-id="b389b-129">A small portion of the encoded string is shown in the following image:</span></span>

<span data-ttu-id="b389b-130">![编码的 dotnet bot](custom-model-binding/images/encoded-bot.png "dotnet bot 编码")</span><span class="sxs-lookup"><span data-stu-id="b389b-130">![dotnet bot encoded](custom-model-binding/images/encoded-bot.png "dotnet bot encoded")</span></span>

<span data-ttu-id="b389b-131">按照中的说明[示例的自述文件](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/sample/CustomModelBindingSample/README.md)将转换文件的 base64 编码字符串。</span><span class="sxs-lookup"><span data-stu-id="b389b-131">Follow the instructions in the [sample's README](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/sample/CustomModelBindingSample/README.md) to convert the base64-encoded string into a file.</span></span>

<span data-ttu-id="b389b-132">ASP.NET 核心 MVC 可以采用 base64 编码字符串，并使用`ByteArrayModelBinder`以将其转换为字节数组。</span><span class="sxs-lookup"><span data-stu-id="b389b-132">ASP.NET Core MVC can take a base64-encoded strings and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="b389b-133">[ByteArrayModelBinderProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinderprovider)实现[IModelBinderProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.imodelbinderprovider)映射`byte[]`自变量`ByteArrayModelBinder`:</span><span class="sxs-lookup"><span data-stu-id="b389b-133">The [ByteArrayModelBinderProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinderprovider) which implements [IModelBinderProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.imodelbinderprovider) maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        return new ByteArrayModelBinder();
    }

    return null;
}
```

<span data-ttu-id="b389b-134">在创建你自己的自定义模型联编程序时，你可以实现你自己`IModelBinderProvider`类型，或者使用[ModelBinderAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinderattribute)。</span><span class="sxs-lookup"><span data-stu-id="b389b-134">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the [ModelBinderAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinderattribute).</span></span>

<span data-ttu-id="b389b-135">下面的示例演示如何使用`ByteArrayModelBinder`要转换的 base64 编码字符串`byte[]`并将结果保存到文件：</span><span class="sxs-lookup"><span data-stu-id="b389b-135">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post1&highlight=3)]

<span data-ttu-id="b389b-136">你可以发布使用对此 api 方法之类的工具是 base64 编码的字符串[Postman](https://www.getpostman.com/):</span><span class="sxs-lookup"><span data-stu-id="b389b-136">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="b389b-137">![postman](custom-model-binding/images/postman.png "postman")</span><span class="sxs-lookup"><span data-stu-id="b389b-137">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="b389b-138">只要联编程序可以将请求数据绑定到的相应命名的属性或自变量，将会成功模型绑定。</span><span class="sxs-lookup"><span data-stu-id="b389b-138">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="b389b-139">下面的示例演示如何使用`ByteArrayModelBinder`与视图模型：</span><span class="sxs-lookup"><span data-stu-id="b389b-139">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="b389b-140">自定义模型联编程序示例</span><span class="sxs-lookup"><span data-stu-id="b389b-140">Custom model binder sample</span></span>

<span data-ttu-id="b389b-141">在本部分中，我们将实施自定义模型联编程序的：</span><span class="sxs-lookup"><span data-stu-id="b389b-141">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="b389b-142">将传入的请求数据转换为强类型化的关键参数。</span><span class="sxs-lookup"><span data-stu-id="b389b-142">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="b389b-143">使用实体框架核心来提取关联的实体。</span><span class="sxs-lookup"><span data-stu-id="b389b-143">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="b389b-144">将关联的实体作为自变量传递给的操作方法。</span><span class="sxs-lookup"><span data-stu-id="b389b-144">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="b389b-145">下面的示例使用`ModelBinder`属性`Author`模型：</span><span class="sxs-lookup"><span data-stu-id="b389b-145">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Data/Author.cs?highlight=10)]

<span data-ttu-id="b389b-146">在前面的代码中，`ModelBinder`属性指定的一种`IModelBinder`应该用于将绑定`Author`操作参数。</span><span class="sxs-lookup"><span data-stu-id="b389b-146">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span> 

<span data-ttu-id="b389b-147">`AuthorEntityBinder`用于绑定`Author`参数从使用实体框架核心的数据源提取实体和`authorId`:</span><span class="sxs-lookup"><span data-stu-id="b389b-147">The `AuthorEntityBinder` is used to bind an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

<span data-ttu-id="b389b-148">下面的代码演示如何使用`AuthorEntityBinder`操作方法中：</span><span class="sxs-lookup"><span data-stu-id="b389b-148">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

<span data-ttu-id="b389b-149">`ModelBinder`属性可以用于应用`AuthorEntityBinder`为不使用默认的约定的参数：</span><span class="sxs-lookup"><span data-stu-id="b389b-149">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that do not use default conventions:</span></span>

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

<span data-ttu-id="b389b-150">在此示例中，因为自变量的名称不是默认`authorId`，指定参数使用`ModelBinder`属性。</span><span class="sxs-lookup"><span data-stu-id="b389b-150">In this example, since the name of the argument is not the default `authorId`, it's specified on the parameter using `ModelBinder` attribute.</span></span> <span data-ttu-id="b389b-151">请注意，控制器和操作方法简化相比查找中的操作方法的实体。</span><span class="sxs-lookup"><span data-stu-id="b389b-151">Note that both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="b389b-152">用于提取使用实体框架核心的作者的逻辑将移动到模型联编程序。</span><span class="sxs-lookup"><span data-stu-id="b389b-152">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="b389b-153">当必须将绑定到作者模型中，并可以帮助你要遵循的几种方法时，这可能是相当大的简化[干原则](http://deviq.com/don-t-repeat-yourself/)。</span><span class="sxs-lookup"><span data-stu-id="b389b-153">This can be considerable simplification when you have several methods that bind to the author model, and can help you to follow the [DRY principle](http://deviq.com/don-t-repeat-yourself/).</span></span>

<span data-ttu-id="b389b-154">你可以将应用`ModelBinder`属性设为各个模型属性 (如视图模型上) 或到操作方法参数以指定的某些模型联编程序或只是该类型或操作的模型名称。</span><span class="sxs-lookup"><span data-stu-id="b389b-154">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="b389b-155">实现 ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="b389b-155">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="b389b-156">应用特性，而是可以实现`IModelBinderProvider`。</span><span class="sxs-lookup"><span data-stu-id="b389b-156">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="b389b-157">这是如何实现内置框架联编程序。</span><span class="sxs-lookup"><span data-stu-id="b389b-157">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="b389b-158">当指定的类型你联编程序进行操作，指定的类型自变量生成，**不**输入你联编程序接受。</span><span class="sxs-lookup"><span data-stu-id="b389b-158">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="b389b-159">以下联编程序提供程序，适用于`AuthorEntityBinder`。</span><span class="sxs-lookup"><span data-stu-id="b389b-159">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="b389b-160">它是添加到提供程序的 MVC 的集合，你无需使用`ModelBinder`属性`Author`或`Author`类型参数。</span><span class="sxs-lookup"><span data-stu-id="b389b-160">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author` typed parameters.</span></span>

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="b389b-161">注意： 上述代码返回`BinderTypeModelBinder`。</span><span class="sxs-lookup"><span data-stu-id="b389b-161">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="b389b-162">`BinderTypeModelBinder`模型联编程序为充当一个工厂，并提供依赖关系注入 (DI)。</span><span class="sxs-lookup"><span data-stu-id="b389b-162">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="b389b-163">`AuthorEntityBinder`需要 DI 访问 EF 核心。</span><span class="sxs-lookup"><span data-stu-id="b389b-163">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="b389b-164">使用`BinderTypeModelBinder`如果模型联编程序需要 DI 中的服务。</span><span class="sxs-lookup"><span data-stu-id="b389b-164">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="b389b-165">若要使用自定义模型联编程序提供程序，将添加在`ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b389b-165">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

<span data-ttu-id="b389b-166">当评估模型联编程序，按顺序检查提供程序的集合。</span><span class="sxs-lookup"><span data-stu-id="b389b-166">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="b389b-167">使用返回联编程序的第一个提供程序。</span><span class="sxs-lookup"><span data-stu-id="b389b-167">The first provider that returns a binder is used.</span></span>

<span data-ttu-id="b389b-168">下图显示默认值从调试器的模型联编程序。</span><span class="sxs-lookup"><span data-stu-id="b389b-168">The following image shows the default model binders from the debugger.</span></span>

<span data-ttu-id="b389b-169">![默认模型联编程序](custom-model-binding/images/default-model-binders.png "默认模型联编程序")</span><span class="sxs-lookup"><span data-stu-id="b389b-169">![default model binders](custom-model-binding/images/default-model-binders.png "default model binders")</span></span>

<span data-ttu-id="b389b-170">将你的提供程序添加到集合的末尾，则可能会导致你自定义的联编程序有机会之前调用内置模型联编程序。</span><span class="sxs-lookup"><span data-stu-id="b389b-170">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="b389b-171">在此示例中，自定义提供程序添加到集合，以确保它用于开头`Author`操作参数。</span><span class="sxs-lookup"><span data-stu-id="b389b-171">In this example, the custom provider is added to the beginning of the collection to ensure it is used for `Author` action arguments.</span></span>

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="b389b-172">建议和最佳做法</span><span class="sxs-lookup"><span data-stu-id="b389b-172">Recommendations and best practices</span></span>

<span data-ttu-id="b389b-173">自定义模型联编程序：</span><span class="sxs-lookup"><span data-stu-id="b389b-173">Custom model binders:</span></span>
- <span data-ttu-id="b389b-174">不应尝试设置状态代码或返回结果 （例如，404 未找到）。</span><span class="sxs-lookup"><span data-stu-id="b389b-174">Should not attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="b389b-175">如果模型绑定失败，[操作筛选器](xref:mvc/controllers/filters)或中的操作方法本身逻辑应处理的错误。</span><span class="sxs-lookup"><span data-stu-id="b389b-175">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="b389b-176">是用于消除重复代码，并从操作方法的跨领域问题最有用。</span><span class="sxs-lookup"><span data-stu-id="b389b-176">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="b389b-177">通常不应将字符串转换为自定义类型， [ `TypeConverter` ](https://docs.microsoft.com//dotnet/api/system.componentmodel.typeconverter)通常是更好的选择。</span><span class="sxs-lookup"><span data-stu-id="b389b-177">Typically should not be used to convert a string into a custom type, a [`TypeConverter`](https://docs.microsoft.com//dotnet/api/system.componentmodel.typeconverter) is usually a better option.</span></span>
