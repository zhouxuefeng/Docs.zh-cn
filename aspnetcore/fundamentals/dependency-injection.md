---
title: "在 ASP.NET 核心中的依赖关系注入"
author: ardalis
description: "了解如何 ASP.NET Core 实现依赖关系注入和如何使用它。"
keywords: "ASP.NET 核心，依赖关系注入、 di"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: fccd69be-7ad1-47fb-b203-b3633b6b9a9b
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/dependency-injection
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d2e191a7395110cde7ab5b2f19b6154c96fb496e
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="introduction-to-dependency-injection-in-aspnet-core"></a>在 ASP.NET 核心中的依赖关系注入简介

<a name=fundamentals-dependency-injection></a>

通过[Steve Smith](http://ardalis.com)和[Scott Addie](https://scottaddie.com)

ASP.NET 核心旨在从一开始向上支持，并利用依赖关系注入。 ASP.NET 核心应用程序可以通过让用户启动类中的方法中注入利用内置 framework 服务和应用程序服务可以配置为也注入。 由 ASP.NET Core 提供的默认服务容器提供最小功能集，不用于替换其他容器。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/sample)

## <a name="what-is-dependency-injection"></a>什么是依赖关系注入？

依赖关系注入 (DI) 是一种技术有助于实现对象及其协作者或依赖项之间的松散耦合。 而不是直接实例化协作者，或使用的静态引用，到以某种方式类提供了一个类执行其操作所需的对象。 大多数情况下，类将声明通过其构造函数，从而使它们可以按照其依赖项[显式依赖关系原则](http://deviq.com/explicit-dependencies-principle/)。 此方法被称为"构造函数注入"。

类旨在与 DI 一起记住，当它们更松散耦合因为它们没有直接的硬编码的依赖关系上其协作者。 这需要遵循[依赖反向原则](http://deviq.com/dependency-inversion-principle/)，其中指出*"高级别模块不应依赖于低级别模块; 同时应依赖于抽象"。* 引用特定的实现，而不是类请求抽象 (通常`interfaces`) 其提供给其构造类时。 提取到接口的依赖关系并提供实现这些接口作为参数也是一种[策略设计模式](http://deviq.com/strategy-design-pattern/)。

当系统设计为使用 DI 时，具有多个请求通过其构造函数 （或属性），其依赖关系的类很有用具有专用于创建这些类具有其关联的依赖关系的类。 这些类统称为*容器*，或更具体地说，[控制反向 (IoC)](http://deviq.com/inversion-of-control/)容器或依赖关系注入 (DI) 容器。 容器是实质上是一个工厂，它负责提供从它请求的类型的实例。 如果给定的类型已声明它具有依赖关系，并配置容器提供依赖关系类型，它将创建创建请求的实例的过程的依赖关系。 这种方式，可以向无需任何硬编码对象构造的类提供复杂的依赖项关系图。 除了创建它们的依赖项对象，通常管理容器应用程序中的对象生存期。

ASP.NET 核心包括简单的内置容器 (由表示`IServiceProvider`接口)，默认情况下，支持构造函数注入和 ASP.NET 使某些服务可通过 DI。 ASP。NET 的容器引用的类型将作为管理*服务*。 本文中，其余*服务*将引用由 ASP.NET Core IoC 容器的类型。 配置中的内置容器的服务`ConfigureServices`方法在应用程序的`Startup`类。

> [!NOTE]
> Martin Fowler 上编写大量文章[反转的控件容器和依赖关系注入模式](http://www.martinfowler.com/articles/injection.html)。 Microsoft 模式和实践还具有很好的 description[依赖关系注入](https://msdn.microsoft.com/library/dn178469(v=pandp.30).aspx)。

> [!NOTE]
> 本文介绍如何依赖关系注入，适用于所有 ASP.NET 应用程序。 中介绍了在 MVC 控制器中的依赖关系注入[依赖关系注入和控制器](../mvc/controllers/dependency-injection.md)。

### <a name="constructor-injection-behavior"></a>构造函数注入行为

构造函数注入需要问题的构造函数将*公共*。 否则，你的应用程序会引发`InvalidOperationException`:

> 找不到类型 YourType 合适的构造函数。 请确保该类型是具体的服务注册的所有参数的公共构造函数。


构造函数注入要求存在该只有一个适用的构造函数。 构造函数重载都受支持，但只有一个重载可以存在该形参的实参可以所有完成的依赖关系注入。 如果存在多个，你的应用程序将引发`InvalidOperationException`:

> 已在类型 YourType 中找到多个构造函数接受所有给定的自变量类型。 只应为一个适用的构造函数。

构造函数可以接受没有提供的依赖关系注入的参数，但它们必须支持默认值。 例如: 

```csharp
// throws InvalidOperationException: Unable to resolve service for type 'System.String'...
public CharactersController(ICharacterRepository characterRepository, string title)
{
    _characterRepository = characterRepository;
    _title = title;
}

// runs without error
public CharactersController(ICharacterRepository characterRepository, string title = "Characters")
{
    _characterRepository = characterRepository;
    _title = title;
}
```

## <a name="using-framework-provided-services"></a>使用框架提供的服务

`ConfigureServices`中的方法`Startup`类负责定义应用程序将使用，包括实体框架核心和 ASP.NET 核心 MVC 等平台功能的服务。 最初，`IServiceCollection`提供给`ConfigureServices`已经安装了以下服务定义 (具体取决于[配置主机的方式](xref:fundamentals/hosting)):

| 服务类型 | 生存期 |
| ----- | ------- |
| [Microsoft.AspNetCore.Hosting.IHostingEnvironment](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.ihostingenvironment) | 单一实例 |
| [Microsoft.Extensions.Logging.ILoggerFactory](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.iloggerfactory) | 单一实例 |
| [Microsoft.Extensions.Logging.ILogger&lt;T&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) | 单一实例 |
| [Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.builder.iapplicationbuilderfactory) | 暂时性故障 |
| [Microsoft.AspNetCore.Http.IHttpContextFactory](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.ihttpcontextfactory) | 暂时性故障 |
| [Microsoft.Extensions.Options.IOptions&lt;T&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.options.ioptions-1) | 单一实例 |
| [System.Diagnostics.DiagnosticSource](https://docs.microsoft.com/dotnet/core/api/system.diagnostics.diagnosticsource) | 单一实例 |
| [System.Diagnostics.DiagnosticListener](https://docs.microsoft.com/dotnet/core/api/system.diagnostics.diagnosticlistener) | 单一实例 |
| [Microsoft.AspNetCore.Hosting.IStartupFilter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.istartupfilter) | 暂时性故障 |
| [Microsoft.Extensions.ObjectPool.ObjectPoolProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.objectpool.objectpoolprovider) | 单一实例 |
| [Microsoft.Extensions.Options.IConfigureOptions&lt;T&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.options.iconfigureoptions-1) | 暂时性故障 |
| [Microsoft.AspNetCore.Hosting.Server.IServer](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.server.iserver) | 单一实例 |
| [Microsoft.AspNetCore.Hosting.IStartup](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.istartup) | 单一实例 |
| [Microsoft.AspNetCore.Hosting.IApplicationLifetime](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.iapplicationlifetime) | 单一实例 |

下面是如何使用数等扩展方法的容器中添加其他服务的一个示例`AddDbContext`， `AddIdentity`，和`AddMvc`。

[!code-csharp[Main](../common/samples/WebApplication1/Startup.cs?highlight=5-6,8-10,12&range=39-56)]

功能和 ASP.NET MVC，如提供的中间件遵循的约定的使用单个添加*ServiceName*扩展方法注册的所有服务该功能所必需的。

>[!TIP]
> 你可以请求中某些框架提供的服务`Startup`通过其参数列表的方法请参阅[应用程序启动](startup.md)有关详细信息。

## <a name="registering-your-own-services"></a>注册你自己的服务

可以按如下方式注册你自己的应用程序服务。 第一种泛型类型表示将从容器请求的类型 （通常接口）。 第二个泛型类型表示将由容器实例化和用来满足此类请求的具体类型。

[!code-csharp[Main](../common/samples/WebApplication1/Startup.cs?range=53-54)]

> [!NOTE]
> 每个`services.Add<ServiceName>`扩展方法将添加 （和可能配置） 服务。 例如，`services.AddMvc()`添加 MVC 需要的服务。 建议你遵循此约定，放置中的扩展方法`Microsoft.Extensions.DependencyInjection`命名空间，封装的服务注册的组。

`AddTransient`方法用于将抽象类型映射到为需要它的每个对象单独实例化的具体服务。 这称为服务的*生存期*，和其他生存期选项如下所述。 请务必选择相应的生存期内，每个你注册的服务。 应提供给请求，则每个类的服务的新实例？ 应使用整个的给定的 web 请求的一个实例？ 或者，应单个实例使用的应用程序生存期内？

在本文示例中，没有显示字符名称，调用的简单控制器`CharactersController`。 其`Index`方法会显示的字符的已存储在应用程序中，当前列表并初始化具有少量的字符的集合，如果不存在。 请注意，尽管此应用程序使用实体框架核心和`ApplicationDbContext`为其暂留，，不明显控制器中的类。 在接口，后面的特定数据访问机制抽象化已相反， `ICharacterRepository`，它遵循[存储库模式](http://deviq.com/repository-pattern/)。 实例`ICharacterRepository`，请求通过构造函数和分配给私有字段，然后将其用于将根据需要访问的字符。

[!code-csharp[Main](../fundamentals/dependency-injection/sample/DependencyInjectionSample/Controllers/CharactersController.cs?highlight=3,5,6,7,8,14,21-27&range=8-36)]

`ICharacterRepository`定义两种方法在控制器需要使用`Character`实例。

[!code-csharp[Main](../fundamentals/dependency-injection/sample/DependencyInjectionSample/Interfaces/ICharacterRepository.cs?highlight=8,9)]

此接口的具体类型，反过来实现`CharacterRepository`，即在运行时使用。

> [!NOTE]
> 与使用的方式 DI`CharacterRepository`类是你可以按照你的应用程序服务，而不仅仅是在"存储库"或数据访问类中的所有常规模型。

[!code-csharp[Main](../fundamentals/dependency-injection/sample/DependencyInjectionSample/Models/CharacterRepository.cs?highlight=9,11,12,13,14)]

请注意，`CharacterRepository`请求`ApplicationDbContext`其构造函数中。 并非要在每个请求的依赖关系，反过来请求其自身的依赖关系如下，以链接形式中使用的依赖关系注入异常。 容器将负责解决所有依赖项关系图中，并返回完全解析的服务。

> [!NOTE]
> 创建请求的对象，和的所有要求的对象以及所有对象的那些需要，有时称为*对象图*。 同样，必须先解决的依赖项集通常称为*依赖关系树*或*依赖项关系图*。

在这种情况下，同时`ICharacterRepository`和反过来`ApplicationDbContext`必须向服务容器注册`ConfigureServices`中`Startup`。 `ApplicationDbContext`配置了对扩展方法的调用`AddDbContext<T>`。 下面的代码演示的注册`CharacterRepository`类型。

[!code-csharp[Main](dependency-injection/sample/DependencyInjectionSample/Startup.cs?highlight=3-5,11&range=16-32)]

实体框架上下文应添加到服务容器使用`Scoped`生存期。 这是处理自动如果你使用的帮助器方法，如上所示。 将实体框架使用的存储库应使用相同的生存期。

>[!WARNING]
> 解决主要的危险，要谨慎`Scoped`服务中，从单一实例。 很可能在此类情况下，在处理后续请求时，服务将具有不正确的状态。

具有依赖关系服务应在容器中注册它们。 如果服务的构造函数需要一个基元，例如`string`，这可以通过使用插入[选项模式和配置](configuration.md)。

## <a name="service-lifetimes-and-registration-options"></a>服务生命周期和注册选项

使用以下的生存期，可以配置 ASP.NET 服务：

**暂时性故障**

用户请求每次都创建暂时性生存期服务。 此生存期适合轻量、 无状态服务。

**作用域**

每个请求，指定了作用域的生存期服务创建一次。

**单一实例**

单一实例生存期服务创建第一次会请求 (时，或者当`ConfigureServices`如果指定实例存在，则运行)，然后每个后续请求将使用同一个实例。 如果你的应用程序需要单一实例行为，而不是实现单独设计模式和自行管理的类中的对象的生存期建议允许服务容器以管理服务的生存期。

可以使用多种方式容器注册服务。 我们已经看到了如何通过指定要使用的具体类型使用给定类型注册服务实现。 此外，可以指定工厂，然后将用于按需创建实例。 第三种方法是类型的直接指定要使用的实例，在其中用例容器将永远不会尝试创建实例 （也不会释放的实例）。

若要显示这些生存期和注册选项之间的差异，请考虑一个简单接口，表示一个或多个任务，如*操作*具有唯一标识符， `OperationId`。 根据我们配置此服务的生存期的方式，容器将提供服务在对请求的类相同或不同的实例。 若要清除正在请求的生存期，我们将创建每个生存期选项的一种类型：

[!code-csharp[Main](../fundamentals/dependency-injection/sample/DependencyInjectionSample/Interfaces/IOperation.cs?highlight=5-8)]

我们实现使用单个类，这些接口`Operation`，它接受`Guid`在其构造函数或使用新`Guid`如果未提供。

接下来，在`ConfigureServices`，每个类型添加到根据生存期命名容器：

[!code-csharp[Main](dependency-injection/sample/DependencyInjectionSample/Startup.cs?range=26-32)]

请注意，`IOperationSingletonInstance`服务使用的特定实例的已知 ID 以及`Guid.Empty`以便它时，将清除此类型是在使用 （其 Guid 将所有 0）。 我们已注册`OperationService`这取决于每个其他`Operation`类型，以便它将对请求中清除此服务是否获取与控制器或一个新的活动，为每个操作类型相同的实例。 所有此服务的作用是作为属性公开其依赖项，以便它们可以在视图中显示。

[!code-csharp[Main](dependency-injection/sample/DependencyInjectionSample/Services/OperationService.cs)]

为了演示对象生存期内和应用程序的单独各个请求之间，此示例包括`OperationsController`请求每种`IOperation`类型以及`OperationService`。 `Index`随后操作显示的所有控制器的和服务的`OperationId`值。

[!code-csharp[Main](dependency-injection/sample/DependencyInjectionSample/Controllers/OperationsController.cs)]

现在两个单独的请求都对此控制器操作：

![显示暂时性、 作用域、 转变成 Singleton，和实例控制器和操作的第一个请求上的服务操作的操作 ID 值 (GUID) 的 Microsoft Edge 中运行的依赖关系注入示例 web 应用程序操作视图中。](dependency-injection/_static/lifetimes_request1.png)

![操作查看显示第二个请求的操作 ID 值。](dependency-injection/_static/lifetimes_request2.png)

观察哪个`OperationId`值会不同请求之间以及在请求中。

* *暂时性*对象始终是不同的; 为每个控制器和每个服务提供的新实例。

* *作用域*对象是否相同请求，但具有不同跨不同请求中

* *Singleton*对象都是相同的每个对象和每个请求 (而不考虑是否的实例提供在`ConfigureServices`)

## <a name="request-services"></a>请求服务

在 ASP.NET 内可用的服务请求从`HttpContext`通过公开`RequestServices`集合。

![HttpContext 请求服务 Intellisense 上下文对话框指出请求服务获取或设置 IServiceProvider 提供请求的服务容器的访问权限。](dependency-injection/_static/request-services.png)

请求服务表示的服务配置和请求你的应用程序的一部分。 当你的对象指定的依赖关系时，这些满足中找到的类型`RequestServices`，而不`ApplicationServices`。

通常情况下，不应使用这些属性直接，那些喜欢改为类似请求您需要通过您的类的构造函数的类的类型和允许将这些依赖关系注入框架。 这将生成更易于测试的类 (请参阅[测试](../testing/index.md)) 和更松散耦合。

> [!NOTE]
> 希望访问的构造函数参数作为请求的依赖关系`RequestServices`集合。

## <a name="designing-your-services-for-dependency-injection"></a>设计你的服务依赖关系注入

应设计你的服务以使用依赖关系注入获取其协作者。 这意味着避免有状态的静态方法调用使用 (这会导致称为代码告知[静态粘贴](http://deviq.com/static-cling/)) 和你的服务中的相关类的直接实例化。 它可帮助你记住这个短语：[新增是粘附](http://ardalis.com/new-is-glue)，当选择是否若要实例化一个类型或请求通过依赖关系注入。 按照[纯色原则的对象面向设计](http://deviq.com/solid/)，您的类将自然倾向于小型、 分解，和轻松测试。

到你的类往往具有太多的依赖关系注入的方式怎么办？ 这通常是登录您的类尝试执行的操作过多，而且可能违反 SRP-[单个责任原则](http://deviq.com/single-responsibility-principle/)。 如果你可以通过将某些其职责移动到新类重构类，请参阅。 请记住，你`Controller`类应关注用户界面问题，因此业务规则和数据访问实现详细信息应保存在适用于这些类中[分离关注事项](http://deviq.com/separation-of-concerns/)。

数据访问方面具体而言，可以插入`DbContext`到你的控制器 (假设你已向服务容器中添加 EF `ConfigureServices`)。 一些开发人员更愿意使用数据库的存储库接口而不是将注入`DbContext`直接。 使用接口来封装数据在一个位置访问逻辑可以最大程度减少必须将你的数据库发生更改时更改的多少位。

### <a name="disposing-of-services"></a>释放服务

容器将调用`Dispose`为`IDisposable`它创建的类型。 但是，如果你将一个实例添加到容器自己，它将不能释放。

示例:

```csharp
// Services implement IDisposable:
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}
public class Service3 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    // container will create the instance(s) of these types and will dispose them
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();

    // container did not create instance so it will NOT dispose it
    services.AddSingleton<Service3>(new Service3());
    services.AddSingleton(new Service3());
}
```

> [!NOTE]
> 在 1.0 版中，容器还调用的 dispose*所有*`IDisposable`对象，包括那些未创建。

## <a name="replacing-the-default-services-container"></a>替换默认服务容器

内置的服务容器旨在提供框架和在其上生成的大多数使用者应用程序的基本需求。 但是，开发人员可以使用其首选容器替换内置的容器。 `ConfigureServices`方法通常返回`void`，但是，如果更改其签名，以便返回`IServiceProvider`，可以配置不同的容器，并将其返回。 没有可用于.NET 的多个 IOC 容器。 在此示例中， [Autofac](http://autofac.org/)使用包。

首先，安装适当的容器程序包：

* `Autofac`
* `Autofac.Extensions.DependencyInjection`

接下来，配置中的容器`ConfigureServices`并返回`IServiceProvider`:

```csharp
public IServiceProvider ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    // Add other framework services

    // Add Autofac
    var containerBuilder = new ContainerBuilder();
    containerBuilder.RegisterModule<DefaultModule>();
    containerBuilder.Populate(services);
    var container = containerBuilder.Build();
    return new AutofacServiceProvider(container);
}
```

> [!NOTE]
> 在使用第三方 DI 容器时，必须更改`ConfigureServices`，以便它返回`IServiceProvider`而不是`void`。

最后，将 Autofac 配置为在正常`DefaultModule`:

```csharp
public class DefaultModule : Module
{
    protected override void Load(ContainerBuilder builder)
    {
        builder.RegisterType<CharacterRepository>().As<ICharacterRepository>();
    }
}
```

在运行时，将使用 Autofac 来解决类型，并将依赖关系注入。 [了解有关使用 Autofac 和 ASP.NET Core](http://docs.autofac.org/en/latest/integration/aspnetcore.html)。

### <a name="thread-safety"></a>线程安全

需要是线程安全的单一实例服务。 如果单一实例服务暂时服务有依赖项，可能还需要暂时服务是线程安全具体取决于使用单一实例的方式。

## <a name="recommendations"></a>建议

在使用依赖关系注入，请记住以下建议：

* DI 是的具有复杂的依赖关系的对象。 控制器、 服务、 适配器和存储库是的可能添加到 DI 的对象的所有示例。

* 避免在 DI 中直接存储数据和配置。 例如，用户的购物车通常不应添加到服务容器。 配置应使用[选项模型](configuration.md#options-config-objects)。 同样，避免仅存在以允许访问某些其他对象的"数据持有者"对象。 它是更好的做法请求通过 DI，如有可能需要的实际项目。

* 避免静态访问服务。

* 避免应用程序代码中的服务位置。

* 避免静态访问`HttpContext`。

> [!NOTE]
> 建议的所有组，如可能会遇到的情况下忽略一个必需。 我们发现异常很少见-框架本身中的主要非常特殊用例。

请记住，依赖关系注入是*备用*到静态/全局对象访问模式。 你将不能实现 DI 的优点，如果混合使用静态对象访问权限。

## <a name="additional-resources"></a>其他资源

* [应用程序启动](startup.md)

* [测试](../testing/index.md)

* [在 ASP.NET 内核，它们有依赖关系注入 (MSDN) 中编写清理代码](https://msdn.microsoft.com/magazine/mt703433.aspx)

* [容器管理应用程序设计，作： 其中？ 容器属于](http://blogs.msdn.com/b/nblumhardt/archive/2008/12/27/container-managed-application-design-prelude-where-does-the-container-belong.aspx)

* [显式依赖关系原则](http://deviq.com/explicit-dependencies-principle/)

* [反向的控件容器和依赖关系注入模式](http://www.martinfowler.com/articles/injection.html)(Fowler)
