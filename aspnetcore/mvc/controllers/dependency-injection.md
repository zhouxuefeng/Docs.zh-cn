---
title: "依赖关系注入到控制器"
author: ardalis
description: 
keywords: "ASP.NET 核心"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: bc8b4ba3-e9ba-48fd-b1eb-cd48ff6bc7a1
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 371fb0f721797e4d8f7a26858ae0a709cb5cd39e
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="dependency-injection-into-controllers"></a><span data-ttu-id="4fb61-103">依赖关系注入到控制器</span><span class="sxs-lookup"><span data-stu-id="4fb61-103">Dependency injection into controllers</span></span>

<a name=dependency-injection-controllers></a>

<span data-ttu-id="4fb61-104">通过[Steve Smith](http://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="4fb61-104">By [Steve Smith](http://ardalis.com)</span></span>

<span data-ttu-id="4fb61-105">ASP.NET 核心 MVC 控制器应请求其构造函数通过显式及其依赖项。</span><span class="sxs-lookup"><span data-stu-id="4fb61-105">ASP.NET Core MVC controllers should request their dependencies explicitly via their constructors.</span></span> <span data-ttu-id="4fb61-106">在某些情况下，单个控制器操作可能需要一种服务，并且它不会使有意义的控制器级别请求。</span><span class="sxs-lookup"><span data-stu-id="4fb61-106">In some instances, individual controller actions may require a service, and it may not make sense to request at the controller level.</span></span> <span data-ttu-id="4fb61-107">在这种情况下，你还可以选择中插入一项服务作为上的操作方法的参数。</span><span class="sxs-lookup"><span data-stu-id="4fb61-107">In this case, you can also choose to inject a service as a parameter on the action method.</span></span>

[<span data-ttu-id="4fb61-108">查看或下载示例代码</span><span class="sxs-lookup"><span data-stu-id="4fb61-108">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample)

## <a name="dependency-injection"></a><span data-ttu-id="4fb61-109">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="4fb61-109">Dependency Injection</span></span>

<span data-ttu-id="4fb61-110">依赖关系注入是一种遵循[依赖反向原则](http://deviq.com/dependency-inversion-principle)，并允许应用程序由组成的松散耦合的模块。</span><span class="sxs-lookup"><span data-stu-id="4fb61-110">Dependency injection is a technique that follows the [Dependency Inversion Principle](http://deviq.com/dependency-inversion-principle), allowing for applications to be composed of loosely coupled modules.</span></span> <span data-ttu-id="4fb61-111">ASP.NET Core 提供的内置支持[依赖关系注入](../../fundamentals/dependency-injection.md)，这使应用程序更易于测试和维护。</span><span class="sxs-lookup"><span data-stu-id="4fb61-111">ASP.NET Core has built-in support for [dependency injection](../../fundamentals/dependency-injection.md), which makes applications easier to test and maintain.</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="4fb61-112">构造函数注入</span><span class="sxs-lookup"><span data-stu-id="4fb61-112">Constructor Injection</span></span>

<span data-ttu-id="4fb61-113">对基于构造函数的依赖关系注入 ASP.NET 核心的内置支持扩展到了 MVC 控制器。</span><span class="sxs-lookup"><span data-stu-id="4fb61-113">ASP.NET Core's built-in support for constructor-based dependency injection extends to MVC controllers.</span></span> <span data-ttu-id="4fb61-114">通过只需将服务类型添加到你的控制器作为构造函数参数中，ASP.NET Core 将尝试解决该类型使用其内置服务容器。</span><span class="sxs-lookup"><span data-stu-id="4fb61-114">By simply adding a service type to your controller as a constructor parameter, ASP.NET Core will attempt to resolve that type using its built in service container.</span></span> <span data-ttu-id="4fb61-115">定义服务是通常情况下，但并非总是使用接口。</span><span class="sxs-lookup"><span data-stu-id="4fb61-115">Services are typically, but not always, defined using interfaces.</span></span> <span data-ttu-id="4fb61-116">例如，如果应用程序依赖于当前时间的业务逻辑，可以插入检索时间 （而不是硬编码），这将使你的测试中使用的设定的时间实现，传递的服务。</span><span class="sxs-lookup"><span data-stu-id="4fb61-116">For example, if your application has business logic that depends on the current time, you can inject a service that retrieves the time (rather than hard-coding it), which would allow your tests to pass in implementations that use a set time.</span></span>

<span data-ttu-id="4fb61-117">[!code-csharp[Main](dependency-injection/sample/src/ControllerDI/Interfaces/IDateTime.cs)]</span><span class="sxs-lookup"><span data-stu-id="4fb61-117">[!code-csharp[Main](dependency-injection/sample/src/ControllerDI/Interfaces/IDateTime.cs)]</span></span>


<span data-ttu-id="4fb61-118">实现类似于此接口，以便它在运行时使用的系统时钟很简单了：</span><span class="sxs-lookup"><span data-stu-id="4fb61-118">Implementing an interface like this one so that it uses the system clock at runtime is trivial:</span></span>

<span data-ttu-id="4fb61-119">[!code-csharp[Main](dependency-injection/sample/src/ControllerDI/Services/SystemDateTime.cs)]</span><span class="sxs-lookup"><span data-stu-id="4fb61-119">[!code-csharp[Main](dependency-injection/sample/src/ControllerDI/Services/SystemDateTime.cs)]</span></span>


<span data-ttu-id="4fb61-120">与此就地，我们可以在控制器中使用服务。</span><span class="sxs-lookup"><span data-stu-id="4fb61-120">With this in place, we can use the service in our controller.</span></span> <span data-ttu-id="4fb61-121">在这种情况下，我们已添加到某些逻辑`HomeController``Index`方法可向用户显示问候语基于一天的时间。</span><span class="sxs-lookup"><span data-stu-id="4fb61-121">In this case, we have added some logic to the `HomeController` `Index` method to display a greeting to the user based on the time of day.</span></span>

<span data-ttu-id="4fb61-122">[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=8,10,12,17,18,19,20,21,22,23,24,25,26,27,28,29,30&range=1-31,51-52)]</span><span class="sxs-lookup"><span data-stu-id="4fb61-122">[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=8,10,12,17,18,19,20,21,22,23,24,25,26,27,28,29,30&range=1-31,51-52)]</span></span>

<span data-ttu-id="4fb61-123">如果我们运行应用程序现在，我们将很可能会遇到错误：</span><span class="sxs-lookup"><span data-stu-id="4fb61-123">If we run the application now, we will most likely encounter an error:</span></span>

<!-- literal_block {"ids": [], "xml:space": "preserve"} -->

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Unable to resolve service for type 'ControllerDI.Interfaces.IDateTime' while attempting to activate 'ControllerDI.Controllers.HomeController'.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.GetService(IServiceProvider sp, Type type, Type requiredBy, Boolean isDefaultParameterRequired)
```

<span data-ttu-id="4fb61-124">我们尚未配置中的服务时发生该错误`ConfigureServices`方法中的我们`Startup`类。</span><span class="sxs-lookup"><span data-stu-id="4fb61-124">This error occurs when we have not configured a service in the `ConfigureServices` method in our `Startup` class.</span></span> <span data-ttu-id="4fb61-125">若要指定的请求`IDateTime`应使用的实例解析`SystemDateTime`，将突出显示的行将添加到下面列表中你`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="4fb61-125">To specify that requests for `IDateTime` should be resolved using an instance of `SystemDateTime`, add the highlighted line in the listing below to your `ConfigureServices` method:</span></span>

<span data-ttu-id="4fb61-126">[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=4&range=26-27,42-44)]</span><span class="sxs-lookup"><span data-stu-id="4fb61-126">[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=4&range=26-27,42-44)]</span></span>

> [!NOTE]
> <span data-ttu-id="4fb61-127">无法使用任意多个不同的生存期选项来实现此特定服务 (`Transient`， `Scoped`，或`Singleton`)。</span><span class="sxs-lookup"><span data-stu-id="4fb61-127">This particular service could be implemented using any of several different lifetime options (`Transient`, `Scoped`, or `Singleton`).</span></span> <span data-ttu-id="4fb61-128">请参阅[依赖关系注入](../../fundamentals/dependency-injection.md)了解其中每个作用域选项将如何影响你的服务的行为。</span><span class="sxs-lookup"><span data-stu-id="4fb61-128">See [Dependency Injection](../../fundamentals/dependency-injection.md) to understand how each of these scope options will affect the behavior of your service.</span></span>

<span data-ttu-id="4fb61-129">服务已配置后，运行应用程序和导航到主页页面应显示基于时间的消息按预期方式：</span><span class="sxs-lookup"><span data-stu-id="4fb61-129">Once the service has been configured, running the application and navigating to the home page should display the time-based message as expected:</span></span>

![服务器问候语](dependency-injection/_static/server-greeting.png)

>[!TIP]
> <span data-ttu-id="4fb61-131">请参阅[测试控制器逻辑](testing.md)若要了解如何显式请求依赖关系[http://deviq.com/explicit-dependencies-principle](http://deviq.com/explicit-dependencies-principle)控制器中使代码可以轻松地测试。</span><span class="sxs-lookup"><span data-stu-id="4fb61-131">See [Testing Controller Logic](testing.md) to learn how to explicitly request dependencies [http://deviq.com/explicit-dependencies-principle](http://deviq.com/explicit-dependencies-principle) in controllers makes code easier to test.</span></span>

<span data-ttu-id="4fb61-132">ASP.NET 核心内置的依赖关系注入支持拥有仅请求服务的类的单个构造函数。</span><span class="sxs-lookup"><span data-stu-id="4fb61-132">ASP.NET Core's built-in dependency injection supports having only a single constructor for classes requesting services.</span></span> <span data-ttu-id="4fb61-133">如果你有多个构造函数，您会收到一个异常内容如下：</span><span class="sxs-lookup"><span data-stu-id="4fb61-133">If you have more than one constructor, you may get an exception stating:</span></span>

<!-- literal_block {"ids": [], "xml:space": "preserve"} -->

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Multiple constructors accepting all given argument types have been found in type 'ControllerDI.Controllers.HomeController'. There should only be one applicable constructor.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.FindApplicableConstructor(Type instanceType, Type[] argumentTypes, ConstructorInfo& matchingConstructor, Nullable`1[]& parameterMap)
```

<span data-ttu-id="4fb61-134">正如所指出的错误消息，你可以更正此问题具有只需单个构造函数。</span><span class="sxs-lookup"><span data-stu-id="4fb61-134">As the error message states, you can correct this problem having just a single constructor.</span></span> <span data-ttu-id="4fb61-135">你还可以[默认依赖关系注入支持替换第三方实现](../../fundamentals/dependency-injection.md#replacing-the-default-services-container)，许多这样才能支持多个构造函数。</span><span class="sxs-lookup"><span data-stu-id="4fb61-135">You can also [replace the default dependency injection support with a third party implementation](../../fundamentals/dependency-injection.md#replacing-the-default-services-container), many of which support multiple constructors.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="4fb61-136">与 FromServices 操作注入</span><span class="sxs-lookup"><span data-stu-id="4fb61-136">Action Injection with FromServices</span></span>

<span data-ttu-id="4fb61-137">有时你不需要多个操作的服务在你的控制器。</span><span class="sxs-lookup"><span data-stu-id="4fb61-137">Sometimes you don't need a service for more than one action within your controller.</span></span> <span data-ttu-id="4fb61-138">在这种情况下，可能有必要将服务注入作为参数传递给的操作方法。</span><span class="sxs-lookup"><span data-stu-id="4fb61-138">In this case, it may make sense to inject the service as a parameter to the action method.</span></span> <span data-ttu-id="4fb61-139">这可通过将属性参数标记`[FromServices]`如下所示：</span><span class="sxs-lookup"><span data-stu-id="4fb61-139">This is done by marking the parameter with the attribute `[FromServices]` as shown here:</span></span>

<span data-ttu-id="4fb61-140">[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=1&range=33-38)]</span><span class="sxs-lookup"><span data-stu-id="4fb61-140">[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=1&range=33-38)]</span></span>

## <a name="accessing-settings-from-a-controller"></a><span data-ttu-id="4fb61-141">从控制器的访问设置</span><span class="sxs-lookup"><span data-stu-id="4fb61-141">Accessing Settings from a Controller</span></span>

<span data-ttu-id="4fb61-142">访问应用程序或配置设置从控制器中的是通用模式。</span><span class="sxs-lookup"><span data-stu-id="4fb61-142">Accessing application or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="4fb61-143">此访问都应该使用中所述的选项模式[配置](../../fundamentals/configuration.md)。</span><span class="sxs-lookup"><span data-stu-id="4fb61-143">This access should use the Options pattern described in [configuration](../../fundamentals/configuration.md).</span></span> <span data-ttu-id="4fb61-144">你通常应该不会请求设置直接从你使用依赖关系注入的控制器。</span><span class="sxs-lookup"><span data-stu-id="4fb61-144">You generally should not request settings directly from your controller using dependency injection.</span></span> <span data-ttu-id="4fb61-145">更好的方法是请求`IOptions<T>`实例，其中`T`是你需要配置类。</span><span class="sxs-lookup"><span data-stu-id="4fb61-145">A better approach is to request an `IOptions<T>` instance, where `T` is the configuration class you need.</span></span>

<span data-ttu-id="4fb61-146">若要使用的选项模式，你需要创建一个类，表示的选项，例如这个：</span><span class="sxs-lookup"><span data-stu-id="4fb61-146">To work with the options pattern, you need to create a class that represents the options, such as this one:</span></span>

<span data-ttu-id="4fb61-147">[!code-csharp[Main](dependency-injection/sample/src/ControllerDI/Model/SampleWebSettings.cs)]</span><span class="sxs-lookup"><span data-stu-id="4fb61-147">[!code-csharp[Main](dependency-injection/sample/src/ControllerDI/Model/SampleWebSettings.cs)]</span></span>

<span data-ttu-id="4fb61-148">然后你需要配置应用程序使用选项模型并将您的配置类添加到中的服务集合`ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4fb61-148">Then you need to configure the application to use the options model and add your configuration class to the services collection in `ConfigureServices`:</span></span>

<span data-ttu-id="4fb61-149">[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=3,4,5,6,9,16,19&range=14-44)]</span><span class="sxs-lookup"><span data-stu-id="4fb61-149">[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=3,4,5,6,9,16,19&range=14-44)]</span></span>

> [!NOTE]
> <span data-ttu-id="4fb61-150">在上面的列表中，我们在配置应用程序读取从 JSON 格式的文件的设置。</span><span class="sxs-lookup"><span data-stu-id="4fb61-150">In the above listing, we are configuring the application to read the settings from a JSON-formatted file.</span></span> <span data-ttu-id="4fb61-151">如上面的注释代码中所示，还可以完全在代码中，配置设置。</span><span class="sxs-lookup"><span data-stu-id="4fb61-151">You can also configure the settings entirely in code, as is shown in the commented code above.</span></span> <span data-ttu-id="4fb61-152">请参阅[配置](../../fundamentals/configuration.md)有关进一步的配置选项。</span><span class="sxs-lookup"><span data-stu-id="4fb61-152">See [Configuration](../../fundamentals/configuration.md) for further configuration options.</span></span>

<span data-ttu-id="4fb61-153">一旦你指定的强类型的配置对象 (在这种情况下， `SampleWebSettings`) 并将它添加到服务集合中，你可以从那里请求任何控制器或操作的方法请求的实例，通过`IOptions<T>`(在这种情况下， `IOptions<SampleWebSettings>`).</span><span class="sxs-lookup"><span data-stu-id="4fb61-153">Once you've specified a strongly-typed configuration object (in this case, `SampleWebSettings`) and added it to the services collection, you can request it from any Controller or Action method by requesting an instance of `IOptions<T>` (in this case, `IOptions<SampleWebSettings>`).</span></span> <span data-ttu-id="4fb61-154">下面的代码演示如何一个将请求从控制器的设置：</span><span class="sxs-lookup"><span data-stu-id="4fb61-154">The following code shows how one would request the settings from a controller:</span></span>

<span data-ttu-id="4fb61-155">[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Controllers/SettingsController.cs?highlight=3,5,7&range=7-22)]</span><span class="sxs-lookup"><span data-stu-id="4fb61-155">[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Controllers/SettingsController.cs?highlight=3,5,7&range=7-22)]</span></span>

<span data-ttu-id="4fb61-156">遵循选项模式允许设置和配置从另一个，相互脱耦，并确保以下控制器[关注点分离](http://deviq.com/separation-of-concerns/)，因为它不需要知道如何或位置来查找的设置信息。</span><span class="sxs-lookup"><span data-stu-id="4fb61-156">Following the Options pattern allows settings and configuration to be decoupled from one another, and ensures the controller is following [separation of concerns](http://deviq.com/separation-of-concerns/), since it doesn't need to know how or where to find the settings information.</span></span> <span data-ttu-id="4fb61-157">这还使得控制器到单元测试更易于[测试控制器逻辑](testing.md)，因为没有任何[静态粘贴](http://deviq.com/static-cling/)或控制器类中的设置类的直接实例化。</span><span class="sxs-lookup"><span data-stu-id="4fb61-157">It also makes the controller easier to unit test [Testing Controller Logic](testing.md), since there is no [static cling](http://deviq.com/static-cling/) or direct instantiation of settings classes within the controller class.</span></span>
