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
ms.openlocfilehash: 999c4f76354ef6ce07733bbb8b0094be90c03c26
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2017
---
# <a name="custom-model-binding"></a>自定义模型绑定

通过[Steve Smith](https://ardalis.com/)

模型绑定允许要直接使用模型 （传递类型中作为方法自变量），而是比 HTTP 请求的控制器操作。 模型联编程序由处理传入的请求数据和应用程序模型之间的映射。 开发人员可以通过实现自定义模型联编程序 （但通常情况下，你无需编写自己的提供程序） 扩展的内置模型绑定功能。

[查看或从 GitHub 下载示例](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/)

## <a name="default-model-binder-limitations"></a>默认模型联编程序限制

默认模型联编程序支持大多数常见.NET 核心数据类型，并且应满足大多数开发人员的需求。 他们希望基于文本的输入请求将直接绑定到模型类型。 你可能需要转换之前将其绑定的输入。 例如，如果你具有可用于查找模型数据的密钥。 自定义模型联编程序可用于获取基于键的数据。

## <a name="model-binding-review"></a>模型绑定评审

模型绑定使用它进行操作的类型特定的定义。 A*简单类型*从输入中的单个字符串转换。 A*复杂类型*转换从多个输入值。 框架确定基于是否存在的差异`TypeConverter`。 我们建议你创建的类型转换器，如果提供了一个简单`string`  ->  `SomeType`不需要外部资源的映射。

在创建之前你自己的自定义模型联编程序，它是活页夹外还实现值得查看如何将现有模型。 请考虑[ByteArrayModelBinder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinder)这可以用于将 base64 编码字符串转换为字节数组。 字节数组通常存储为文件或数据库 BLOB 字段中。

### <a name="working-with-the-bytearraymodelbinder"></a>使用 ByteArrayModelBinder

Base64 编码字符串可以用于表示二进制数据。 例如下, 图可以编码为字符串。

![dotnet bot](custom-model-binding/images/bot.png "dotnet bot")

已编码的字符串的一小部分如下图所示：

![编码的 dotnet bot](custom-model-binding/images/encoded-bot.png "dotnet bot 编码")

按照中的说明[示例的自述文件](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/sample/CustomModelBindingSample/README.md)将转换文件的 base64 编码字符串。

ASP.NET 核心 MVC 可以采用 base64 编码字符串，并使用`ByteArrayModelBinder`以将其转换为字节数组。 [ByteArrayModelBinderProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinderprovider)实现[IModelBinderProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.imodelbinderprovider)映射`byte[]`自变量`ByteArrayModelBinder`:

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

在创建你自己的自定义模型联编程序时，你可以实现你自己`IModelBinderProvider`类型，或者使用[ModelBinderAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinderattribute)。

下面的示例演示如何使用`ByteArrayModelBinder`要转换的 base64 编码字符串`byte[]`并将结果保存到文件：

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post1&highlight=3)]

你可以发布使用对此 api 方法之类的工具是 base64 编码的字符串[Postman](https://www.getpostman.com/):

![postman](custom-model-binding/images/postman.png "postman")

只要联编程序可以将请求数据绑定到的相应命名的属性或自变量，将会成功模型绑定。 下面的示例演示如何使用`ByteArrayModelBinder`与视图模型：

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a>自定义模型联编程序示例

在本部分中，我们将实施自定义模型联编程序的：

- 将传入的请求数据转换为强类型化的关键参数。
- 使用实体框架核心来提取关联的实体。
- 将关联的实体作为自变量传递给的操作方法。

下面的示例使用`ModelBinder`属性`Author`模型：

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Data/Author.cs?highlight=10)]

在前面的代码中，`ModelBinder`属性指定的一种`IModelBinder`应该用于将绑定`Author`操作参数。 

`AuthorEntityBinder`用于绑定`Author`参数从使用实体框架核心的数据源提取实体和`authorId`:

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

下面的代码演示如何使用`AuthorEntityBinder`操作方法中：

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

`ModelBinder`属性可以用于应用`AuthorEntityBinder`为不使用默认的约定的参数：

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

在此示例中，因为自变量的名称不是默认`authorId`，指定参数使用`ModelBinder`属性。 请注意，控制器和操作方法简化相比查找中的操作方法的实体。 用于提取使用实体框架核心的作者的逻辑将移动到模型联编程序。 当必须将绑定到作者模型中，并可以帮助你要遵循的几种方法时，这可能是相当大的简化[干原则](http://deviq.com/don-t-repeat-yourself/)。

你可以将应用`ModelBinder`属性设为各个模型属性 (如视图模型上) 或到操作方法参数以指定的某些模型联编程序或只是该类型或操作的模型名称。

### <a name="implementing-a-modelbinderprovider"></a>实现 ModelBinderProvider

应用特性，而是可以实现`IModelBinderProvider`。 这是如何实现内置框架联编程序。 当指定的类型你联编程序进行操作，指定的类型自变量生成，**不**输入你联编程序接受。 以下联编程序提供程序，适用于`AuthorEntityBinder`。 它是添加到提供程序的 MVC 的集合，你无需使用`ModelBinder`属性`Author`或`Author`类型参数。

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> 注意： 上述代码返回`BinderTypeModelBinder`。 `BinderTypeModelBinder`模型联编程序为充当一个工厂，并提供依赖关系注入 (DI)。 `AuthorEntityBinder`需要 DI 访问 EF 核心。 使用`BinderTypeModelBinder`如果模型联编程序需要 DI 中的服务。

若要使用自定义模型联编程序提供程序，将添加在`ConfigureServices`:

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

当评估模型联编程序，按顺序检查提供程序的集合。 使用返回联编程序的第一个提供程序。

下图显示默认值从调试器的模型联编程序。

![默认模型联编程序](custom-model-binding/images/default-model-binders.png "默认模型联编程序")

将你的提供程序添加到集合的末尾，则可能会导致你自定义的联编程序有机会之前调用内置模型联编程序。 在此示例中，自定义提供程序添加到集合，以确保它用于开头`Author`操作参数。

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

## <a name="recommendations-and-best-practices"></a>建议和最佳做法

自定义模型联编程序：
- 不应尝试设置状态代码或返回结果 （例如，404 未找到）。 如果模型绑定失败，[操作筛选器](xref:mvc/controllers/filters)或中的操作方法本身逻辑应处理的错误。
- 是用于消除重复代码，并从操作方法的跨领域问题最有用。
- 通常不应将字符串转换为自定义类型， [ `TypeConverter` ](https://docs.microsoft.com//dotnet/api/system.componentmodel.typeconverter)通常是更好的选择。
