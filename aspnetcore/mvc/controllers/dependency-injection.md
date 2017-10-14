---
title: "依赖关系注入到控制器"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: bc8b4ba3-e9ba-48fd-b1eb-cd48ff6bc7a1
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: ff0a1a34ee6b025be6312a81f1a0bcdd07026adb
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="dependency-injection-into-controllers"></a>依赖关系注入到控制器

<a name="dependency-injection-controllers"></a>

通过[Steve Smith](https://ardalis.com/)

ASP.NET 核心 MVC 控制器应请求其构造函数通过显式及其依赖项。 在某些情况下，单个控制器操作可能需要一种服务，并且它不会使有意义的控制器级别请求。 在这种情况下，你还可以选择中插入一项服务作为上的操作方法的参数。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample)（[如何下载](xref:tutorials/index#how-to-download-a-sample)）

## <a name="dependency-injection"></a>依赖关系注入

依赖关系注入是一种遵循[依赖反向原则](http://deviq.com/dependency-inversion-principle/)，并允许应用程序由组成的松散耦合的模块。 ASP.NET Core 提供的内置支持[依赖关系注入](../../fundamentals/dependency-injection.md)，这使应用程序更易于测试和维护。

## <a name="constructor-injection"></a>构造函数注入

对基于构造函数的依赖关系注入 ASP.NET 核心的内置支持扩展到了 MVC 控制器。 通过只需将服务类型添加到你的控制器作为构造函数参数中，ASP.NET Core 将尝试解决该类型使用其内置服务容器。 定义服务是通常情况下，但并非总是使用接口。 例如，如果应用程序依赖于当前时间的业务逻辑，可以插入检索时间 （而不是硬编码），这将使你的测试中使用的设定的时间实现，传递的服务。

[!code-csharp[Main](dependency-injection/sample/src/ControllerDI/Interfaces/IDateTime.cs)]


实现类似于此接口，以便它在运行时使用的系统时钟很简单了：

[!code-csharp[Main](dependency-injection/sample/src/ControllerDI/Services/SystemDateTime.cs)]


与此就地，我们可以在控制器中使用服务。 在这种情况下，我们已添加到某些逻辑`HomeController``Index`方法可向用户显示问候语基于一天的时间。

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=8,10,12,17,18,19,20,21,22,23,24,25,26,27,28,29,30&range=1-31,51-52)]

如果我们运行应用程序现在，我们将很可能会遇到错误：

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Unable to resolve service for type 'ControllerDI.Interfaces.IDateTime' while attempting to activate 'ControllerDI.Controllers.HomeController'.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.GetService(IServiceProvider sp, Type type, Type requiredBy, Boolean isDefaultParameterRequired)
```

我们尚未配置中的服务时发生该错误`ConfigureServices`方法中的我们`Startup`类。 若要指定的请求`IDateTime`应使用的实例解析`SystemDateTime`，将突出显示的行将添加到下面列表中你`ConfigureServices`方法：

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=4&range=26-27,42-44)]

> [!NOTE]
> 无法使用任意多个不同的生存期选项来实现此特定服务 (`Transient`， `Scoped`，或`Singleton`)。 请参阅[依赖关系注入](../../fundamentals/dependency-injection.md)了解其中每个作用域选项将如何影响你的服务的行为。

服务已配置后，运行应用程序和导航到主页页面应显示基于时间的消息按预期方式：

![服务器问候语](dependency-injection/_static/server-greeting.png)

>[!TIP]
> 请参阅[测试控制器逻辑](testing.md)若要了解如何显式请求依赖关系[http://deviq.com/explicit-dependencies-principle/](http://deviq.com/explicit-dependencies-principle/)控制器中使代码可以轻松地测试。

ASP.NET 核心内置的依赖关系注入支持拥有仅请求服务的类的单个构造函数。 如果你有多个构造函数，您会收到一个异常内容如下：

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Multiple constructors accepting all given argument types have been found in type 'ControllerDI.Controllers.HomeController'. There should only be one applicable constructor.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.FindApplicableConstructor(Type instanceType, Type[] argumentTypes, ConstructorInfo& matchingConstructor, Nullable`1[]& parameterMap)
```

正如所指出的错误消息，你可以更正此问题具有只需单个构造函数。 你还可以[默认依赖关系注入支持替换第三方实现](../../fundamentals/dependency-injection.md#replacing-the-default-services-container)，许多这样才能支持多个构造函数。

## <a name="action-injection-with-fromservices"></a>与 FromServices 操作注入

有时你不需要多个操作的服务在你的控制器。 在这种情况下，可能有必要将服务注入作为参数传递给的操作方法。 这可通过将属性参数标记`[FromServices]`如下所示：

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=1&range=33-38)]

## <a name="accessing-settings-from-a-controller"></a>从控制器的访问设置

访问应用程序或配置设置从控制器中的是通用模式。 此访问都应该使用中所述的选项模式[配置](../../fundamentals/configuration.md)。 你通常应该不会请求设置直接从你使用依赖关系注入的控制器。 更好的方法是请求`IOptions<T>`实例，其中`T`是你需要配置类。

若要使用的选项模式，你需要创建一个类，表示的选项，例如这个：

[!code-csharp[Main](dependency-injection/sample/src/ControllerDI/Model/SampleWebSettings.cs)]

然后你需要配置应用程序使用选项模型并将您的配置类添加到中的服务集合`ConfigureServices`:

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=3,4,5,6,9,16,19&range=14-44)]

> [!NOTE]
> 在上面的列表中，我们在配置应用程序读取从 JSON 格式的文件的设置。 如上面的注释代码中所示，还可以完全在代码中，配置设置。 请参阅[配置](../../fundamentals/configuration.md)有关进一步的配置选项。

一旦你指定的强类型的配置对象 (在这种情况下， `SampleWebSettings`) 并将它添加到服务集合中，你可以从那里请求任何控制器或操作的方法请求的实例，通过`IOptions<T>`(在这种情况下， `IOptions<SampleWebSettings>`). 下面的代码演示如何一个将请求从控制器的设置：

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Controllers/SettingsController.cs?highlight=3,5,7&range=7-22)]

遵循选项模式允许设置和配置从另一个，相互脱耦，并确保以下控制器[关注点分离](http://deviq.com/separation-of-concerns/)，因为它不需要知道如何或位置来查找的设置信息。 这还使得控制器到单元测试更易于[测试控制器逻辑](testing.md)，因为没有任何[静态粘贴](http://deviq.com/static-cling/)或控制器类中的设置类的直接实例化。
