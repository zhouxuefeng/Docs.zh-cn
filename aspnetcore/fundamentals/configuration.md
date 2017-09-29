---
title: "ASP.NET Core 中的配置"
author: rick-anderson
description: "了解如何使用配置 API 将来自多个源 ASP.NET Core 应用配置。"
keywords: "ASP.NET 核心，配置中，JSON 配置"
ms.author: riande
manager: wpickett
ms.date: 6/24/2017
ms.topic: article
ms.assetid: b3a5984d-e172-42eb-8a48-547e4acb6806
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/configuration
ms.openlocfilehash: 379030df4ca91a38fce251aeaab9c5dfaf11e915
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2017
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="a778a-104">ASP.NET Core 中的配置</span><span class="sxs-lookup"><span data-stu-id="a778a-104">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="a778a-105">[Rick Anderson](https://twitter.com/RickAndMSFT)，[标记 Michaelis](http://intellitect.com/author/mark-michaelis/)， [Steve Smith](https://ardalis.com/)， [Daniel Roth](https://github.com/danroth27)，和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a778a-105">[Rick Anderson](https://twitter.com/RickAndMSFT), [Mark Michaelis](http://intellitect.com/author/mark-michaelis/), [Steve Smith](https://ardalis.com/), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a778a-106">配置 API 提供一种方法配置应用程序中基于名称-值对的列表。</span><span class="sxs-lookup"><span data-stu-id="a778a-106">The Configuration API provides a way of configuring an app based on a list of name-value pairs.</span></span> <span data-ttu-id="a778a-107">在运行时从多个源读取配置。</span><span class="sxs-lookup"><span data-stu-id="a778a-107">Configuration is read at runtime from multiple sources.</span></span> <span data-ttu-id="a778a-108">名称-值对可以分组到多级的层次结构。</span><span class="sxs-lookup"><span data-stu-id="a778a-108">The name-value pairs can be grouped into a multi-level hierarchy.</span></span> <span data-ttu-id="a778a-109">有配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="a778a-109">There are configuration providers for:</span></span>

* <span data-ttu-id="a778a-110">文件格式 （INI、 JSON 和 XML）</span><span class="sxs-lookup"><span data-stu-id="a778a-110">File formats (INI, JSON, and XML)</span></span>
* <span data-ttu-id="a778a-111">命令行自变量</span><span class="sxs-lookup"><span data-stu-id="a778a-111">Command-line arguments</span></span>
* <span data-ttu-id="a778a-112">环境变量</span><span class="sxs-lookup"><span data-stu-id="a778a-112">Environment variables</span></span>
* <span data-ttu-id="a778a-113">内存中的.NET 对象</span><span class="sxs-lookup"><span data-stu-id="a778a-113">In-memory .NET objects</span></span>
* <span data-ttu-id="a778a-114">一个加密的用户存储区</span><span class="sxs-lookup"><span data-stu-id="a778a-114">An encrypted user store</span></span>
* [<span data-ttu-id="a778a-115">Azure 密钥保管库</span><span class="sxs-lookup"><span data-stu-id="a778a-115">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
* <span data-ttu-id="a778a-116">自定义提供程序，你安装或在创建</span><span class="sxs-lookup"><span data-stu-id="a778a-116">Custom providers, which you install or create</span></span>

<span data-ttu-id="a778a-117">每个配置值将映射到字符串键。</span><span class="sxs-lookup"><span data-stu-id="a778a-117">Each configuration value maps to a string key.</span></span> <span data-ttu-id="a778a-118">若要反序列化到自定义设置的内置绑定支持[POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object)对象 （具有属性的简单.NET 类）。</span><span class="sxs-lookup"><span data-stu-id="a778a-118">There's built-in binding support to deserialize settings into a custom [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) object (a simple .NET class with properties).</span></span>

[<span data-ttu-id="a778a-119">查看或下载示例代码</span><span class="sxs-lookup"><span data-stu-id="a778a-119">View or download sample code</span></span>](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/sample)

## <a name="simple-configuration"></a><span data-ttu-id="a778a-120">简单的配置</span><span class="sxs-lookup"><span data-stu-id="a778a-120">Simple configuration</span></span>

<span data-ttu-id="a778a-121">以下控制台应用程序使用 JSON 配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="a778a-121">The following console app uses the JSON configuration provider:</span></span>

[!code-csharp[Main](configuration/sample/ConfigJson/Program.cs)]

<span data-ttu-id="a778a-122">应用程序读取，并显示以下配置设置：</span><span class="sxs-lookup"><span data-stu-id="a778a-122">The app reads and displays the following configuration settings:</span></span>

[!code-json[Main](configuration/sample/ConfigJson/appsettings.json)]

<span data-ttu-id="a778a-123">配置由在其中节点以冒号分隔的名称-值对的分层列表组成。</span><span class="sxs-lookup"><span data-stu-id="a778a-123">Configuration consists of a hierarchical list of name-value pairs in which the nodes are separated by a colon.</span></span> <span data-ttu-id="a778a-124">若要检索一个值，请访问`Configuration`与相应的项的键的索引器：</span><span class="sxs-lookup"><span data-stu-id="a778a-124">To retrieve a value, access the `Configuration` indexer with the corresponding item's key:</span></span>

```csharp
Console.WriteLine($"option1 = {Configuration["subsection:suboption1"]}");
```

<span data-ttu-id="a778a-125">若要使用 JSON 格式配置源中的数组，使用作为一部分的冒号分隔的字符串的数组索引。</span><span class="sxs-lookup"><span data-stu-id="a778a-125">To work with arrays in JSON-formatted configuration sources, use a array index as part of the colon-separated string.</span></span> <span data-ttu-id="a778a-126">下面的示例获取在前面的第一项的名称`wizards`数组：</span><span class="sxs-lookup"><span data-stu-id="a778a-126">The following example gets the name of the first item in the preceding `wizards` array:</span></span>

```csharp
Console.Write($"{Configuration["wizards:0:Name"]}, ");
```

<span data-ttu-id="a778a-127">写入到生成的名称-值对中`Configuration`提供程序是**不**保持不变，但是，你可以创建的自定义提供程序将保存值。</span><span class="sxs-lookup"><span data-stu-id="a778a-127">Name-value pairs written to the built in `Configuration` providers are **not** persisted, however, you can create a custom provider that saves values.</span></span> <span data-ttu-id="a778a-128">请参阅[自定义配置提供程序](xref:fundamentals/configuration#custom-config-providers)。</span><span class="sxs-lookup"><span data-stu-id="a778a-128">See [custom configuration provider](xref:fundamentals/configuration#custom-config-providers).</span></span>

<span data-ttu-id="a778a-129">上面的示例使用配置索引器可以读取值。</span><span class="sxs-lookup"><span data-stu-id="a778a-129">The preceding sample uses the configuration indexer to read values.</span></span> <span data-ttu-id="a778a-130">外部访问配置`Startup`，使用[选项模式](xref:fundamentals/configuration#options-config-objects)。</span><span class="sxs-lookup"><span data-stu-id="a778a-130">To access configuration outside of `Startup`, use the [options pattern](xref:fundamentals/configuration#options-config-objects).</span></span> <span data-ttu-id="a778a-131">*选项模式*本文后面所示。</span><span class="sxs-lookup"><span data-stu-id="a778a-131">The *options pattern* is shown later in this article.</span></span>

<span data-ttu-id="a778a-132">它是通常具有不同的配置设置的不同的环境，例如，开发、 测试和生产。</span><span class="sxs-lookup"><span data-stu-id="a778a-132">It's typical to have different configuration settings for different environments, for example, development, test, and production.</span></span> <span data-ttu-id="a778a-133">`CreateDefaultBuilder` ASP.NET Core 2.x 应用程序中的扩展方法 (或使用`AddJsonFile`和`AddEnvironmentVariables`直接在 ASP.NET Core 1.x 应用程序) 将配置提供程序添加用于读取 JSON 文件和系统配置源：</span><span class="sxs-lookup"><span data-stu-id="a778a-133">The `CreateDefaultBuilder` extension method in an ASP.NET Core 2.x app (or using `AddJsonFile` and `AddEnvironmentVariables` directly in an ASP.NET Core 1.x app) adds configuration providers for reading JSON files and system configuration sources:</span></span>

* <span data-ttu-id="a778a-134">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="a778a-134">*appsettings.json*</span></span>
* <span data-ttu-id="a778a-135">*appsettings。\<EnvironmentName >.json*</span><span class="sxs-lookup"><span data-stu-id="a778a-135">*appsettings.\<EnvironmentName>.json*</span></span>
* <span data-ttu-id="a778a-136">环境变量</span><span class="sxs-lookup"><span data-stu-id="a778a-136">environment variables</span></span>

<span data-ttu-id="a778a-137">请参阅[AddJsonFile](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.jsonconfigurationextensions)有关参数的说明。</span><span class="sxs-lookup"><span data-stu-id="a778a-137">See [AddJsonFile](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.jsonconfigurationextensions) for an explanation of the parameters.</span></span> <span data-ttu-id="a778a-138">`reloadOnChange`仅支持在 ASP.NET 核心 1.1 和更高版本。</span><span class="sxs-lookup"><span data-stu-id="a778a-138">`reloadOnChange` is only supported in ASP.NET Core 1.1 and higher.</span></span> 

<span data-ttu-id="a778a-139">配置源中指定它们的顺序读取。</span><span class="sxs-lookup"><span data-stu-id="a778a-139">Configuration sources are read in the order they are specified.</span></span> <span data-ttu-id="a778a-140">在上面的代码中，最后读取环境变量。</span><span class="sxs-lookup"><span data-stu-id="a778a-140">In the code above, the environment variables are read last.</span></span> <span data-ttu-id="a778a-141">任何通过环境设置的配置值会替换在两个以前的提供程序中设置。</span><span class="sxs-lookup"><span data-stu-id="a778a-141">Any configuration values set through the environment would replace those set in the two previous providers.</span></span>

<span data-ttu-id="a778a-142">环境通常设置为其中一个`Development`， `Staging`，或`Production`。</span><span class="sxs-lookup"><span data-stu-id="a778a-142">The environment is typically set to one of `Development`, `Staging`, or `Production`.</span></span> <span data-ttu-id="a778a-143">请参阅[使用多个环境](environments.md)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="a778a-143">See [Working with Multiple Environments](environments.md) for more information.</span></span>

<span data-ttu-id="a778a-144">配置注意事项：</span><span class="sxs-lookup"><span data-stu-id="a778a-144">Configuration considerations:</span></span>

* <span data-ttu-id="a778a-145">`IOptionsSnapshot`当更改时，可以重新加载配置数据。</span><span class="sxs-lookup"><span data-stu-id="a778a-145">`IOptionsSnapshot` can reload configuration data when it changes.</span></span> <span data-ttu-id="a778a-146">使用`IOptionsSnapshot`如果需要重新加载配置数据。</span><span class="sxs-lookup"><span data-stu-id="a778a-146">Use `IOptionsSnapshot` if you need to reload configuration data.</span></span>  <span data-ttu-id="a778a-147">请参阅[IOptionsSnapshot](#ioptionssnapshot)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="a778a-147">See [IOptionsSnapshot](#ioptionssnapshot) for more information.</span></span>
* <span data-ttu-id="a778a-148">配置密钥是区分大小写。</span><span class="sxs-lookup"><span data-stu-id="a778a-148">Configuration keys are case insensitive.</span></span>
* <span data-ttu-id="a778a-149">一种最佳做法是上一次，指定环境变量，使本地环境可以重写在已部署的配置文件中设置的任何内容。</span><span class="sxs-lookup"><span data-stu-id="a778a-149">A best practice is to specify environment variables last, so that the local environment can override anything set in deployed configuration files.</span></span>
* <span data-ttu-id="a778a-150">**永远不会**在配置提供程序代码或纯文本配置文件中存储密码或其他敏感数据。</span><span class="sxs-lookup"><span data-stu-id="a778a-150">**Never** store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="a778a-151">请不要在你的开发中使用生产机密或测试环境。</span><span class="sxs-lookup"><span data-stu-id="a778a-151">Don't use production secrets in your development or test environments.</span></span> <span data-ttu-id="a778a-152">相反，指定外部项目树中，机密，以便它们不会意外地提交到你的存储库。</span><span class="sxs-lookup"><span data-stu-id="a778a-152">Instead, specify secrets outside the project tree, so they cannot be accidentally committed into your repository.</span></span> <span data-ttu-id="a778a-153">详细了解[使用多个环境](environments.md)和管理[安全存储在开发过程中的应用程序机密](../security/app-secrets.md)。</span><span class="sxs-lookup"><span data-stu-id="a778a-153">Learn more about [Working with Multiple Environments](environments.md) and managing [safe storage of app secrets during development](../security/app-secrets.md).</span></span>
* <span data-ttu-id="a778a-154">如果`:`不能为系统中所用环境变量中，替换`:`与`__`（双下划线）。</span><span class="sxs-lookup"><span data-stu-id="a778a-154">If `:` cannot be used in environment variables in your system,  replace `:`  with `__` (double underscore).</span></span>

<a name=options-config-objects></a>

## <a name="using-options-and-configuration-objects"></a><span data-ttu-id="a778a-155">使用选项和配置对象</span><span class="sxs-lookup"><span data-stu-id="a778a-155">Using Options and configuration objects</span></span>

<span data-ttu-id="a778a-156">选项模式使用自定义选项类来表示一组相关的设置。</span><span class="sxs-lookup"><span data-stu-id="a778a-156">The options pattern uses custom options classes to represent a group of related settings.</span></span> <span data-ttu-id="a778a-157">我们建议在你的应用程序中创建分离的类为每个功能。</span><span class="sxs-lookup"><span data-stu-id="a778a-157">We recommended that you create decoupled classes for each feature within your app.</span></span> <span data-ttu-id="a778a-158">分离的类遵循：</span><span class="sxs-lookup"><span data-stu-id="a778a-158">Decoupled classes follow:</span></span>

* <span data-ttu-id="a778a-159">[接口分隔原则 (ISP)](http://deviq.com/interface-segregation-principle/) ： 类仅取决于它们使用的配置设置。</span><span class="sxs-lookup"><span data-stu-id="a778a-159">The [Interface Segregation Principle (ISP)](http://deviq.com/interface-segregation-principle/) : Classes depend only on the configuration settings they use.</span></span>
* <span data-ttu-id="a778a-160">[关注点分离](http://deviq.com/separation-of-concerns/)： 你的应用程序的不同部分的设置不是依赖或与另一个耦合。</span><span class="sxs-lookup"><span data-stu-id="a778a-160">[Separation of Concerns](http://deviq.com/separation-of-concerns/) : Settings for different parts of your app are not dependent or coupled with one another.</span></span>

<span data-ttu-id="a778a-161">选项类必须为非抽象具有一个公共的无参数构造函数。</span><span class="sxs-lookup"><span data-stu-id="a778a-161">The options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="a778a-162">例如: </span><span class="sxs-lookup"><span data-stu-id="a778a-162">For example:</span></span>

[!code-csharp[Main](configuration/sample/UsingOptions/Models/MyOptions.cs)]

<a name=options-example></a>

<span data-ttu-id="a778a-163">在下面的代码中，启用 JSON 配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="a778a-163">In the following code, the JSON configuration provider is enabled.</span></span> <span data-ttu-id="a778a-164">`MyOptions`类是已添加到服务容器并绑定到配置。</span><span class="sxs-lookup"><span data-stu-id="a778a-164">The `MyOptions` class is added to the service container and bound to configuration.</span></span>

[!code-csharp[Main](configuration/sample/UsingOptions/Startup.cs?name=snippet1&highlight=8,20-21)]

<span data-ttu-id="a778a-165">以下[控制器](../mvc/controllers/index.md)使用[构造函数依赖关系注入](xref:fundamentals/dependency-injection#what-is-dependency-injection)上[ `IOptions<TOptions>` ](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.options.ioptions-1)访问设置：</span><span class="sxs-lookup"><span data-stu-id="a778a-165">The following [controller](../mvc/controllers/index.md)  uses [constructor Dependency Injection](xref:fundamentals/dependency-injection#what-is-dependency-injection) on [`IOptions<TOptions>`](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.options.ioptions-1) to access settings:</span></span>

[!code-csharp[Main](configuration/sample/UsingOptions/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="a778a-166">替换为以下*appsettings.json*文件：</span><span class="sxs-lookup"><span data-stu-id="a778a-166">With the following *appsettings.json* file:</span></span>

[!code-json[Main](configuration/sample/UsingOptions/appsettings1.json)]

<span data-ttu-id="a778a-167">`HomeController.Index`方法返回`option1 = value1_from_json, option2 = 2`。</span><span class="sxs-lookup"><span data-stu-id="a778a-167">The `HomeController.Index` method returns `option1 = value1_from_json, option2 = 2`.</span></span>

<span data-ttu-id="a778a-168">典型的应用程序不会将整个配置绑定到单个选项文件。</span><span class="sxs-lookup"><span data-stu-id="a778a-168">Typical apps won't bind the entire configuration to a single options file.</span></span> <span data-ttu-id="a778a-169">稍后我将介绍如何使用`GetSection`将绑定到一个部分。</span><span class="sxs-lookup"><span data-stu-id="a778a-169">Later on I'll show how to use `GetSection` to bind to a section.</span></span>

<span data-ttu-id="a778a-170">在下面的代码中，第二个`IConfigureOptions<TOptions>`服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="a778a-170">In the following code, a second `IConfigureOptions<TOptions>` service is added to the service container.</span></span> <span data-ttu-id="a778a-171">它使用委托来配置的绑定`MyOptions`。</span><span class="sxs-lookup"><span data-stu-id="a778a-171">It uses a delegate to configure the binding with `MyOptions`.</span></span>

[!code-csharp[Main](configuration/sample/UsingOptions/Startup2.cs?name=snippet1&highlight=9-13)]

<span data-ttu-id="a778a-172">你可以添加多个配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="a778a-172">You can add multiple configuration providers.</span></span> <span data-ttu-id="a778a-173">配置提供程序 NuGet 包中。</span><span class="sxs-lookup"><span data-stu-id="a778a-173">Configuration providers are available in NuGet packages.</span></span> <span data-ttu-id="a778a-174">它们会按它们已注册的顺序应用。</span><span class="sxs-lookup"><span data-stu-id="a778a-174">They are applied in order they are registered.</span></span>

<span data-ttu-id="a778a-175">每次调用`Configure<TOptions>`添加`IConfigureOptions<TOptions>`服务到服务容器。</span><span class="sxs-lookup"><span data-stu-id="a778a-175">Each call to `Configure<TOptions>` adds an `IConfigureOptions<TOptions>` service to the service container.</span></span> <span data-ttu-id="a778a-176">在前面的示例中，值`Option1`和`Option2`同时中指定*appsettings.json* -但的值`Option1`配置委托来重写。</span><span class="sxs-lookup"><span data-stu-id="a778a-176">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json* -- but the value of `Option1` is overridden by the configured delegate.</span></span> 

<span data-ttu-id="a778a-177">启用多个配置服务后，最后一个配置源指定"wins"（设置配置值）。</span><span class="sxs-lookup"><span data-stu-id="a778a-177">When more than one configuration service is enabled, the last configuration source specified "wins" (sets the configuration value).</span></span> <span data-ttu-id="a778a-178">在前面的代码中，`HomeController.Index`方法返回`option1 = value1_from_action, option2 = 2`。</span><span class="sxs-lookup"><span data-stu-id="a778a-178">In the preceding code, the `HomeController.Index` method returns `option1 = value1_from_action, option2 = 2`.</span></span>

<span data-ttu-id="a778a-179">绑定到配置的选项时, 选项类型中的每个属性绑定到窗体的配置键`property[:sub-property:]`。</span><span class="sxs-lookup"><span data-stu-id="a778a-179">When you bind options to configuration, each property in your options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="a778a-180">例如，`MyOptions.Option1`属性绑定到键`Option1`，这从读取`option1`中的属性*appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="a778a-180">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span> <span data-ttu-id="a778a-181">在本文后面显示了子属性示例。</span><span class="sxs-lookup"><span data-stu-id="a778a-181">A sub-property sample is shown later in this article.</span></span>

<span data-ttu-id="a778a-182">在下面的代码中，第三个`IConfigureOptions<TOptions>`服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="a778a-182">In the following code, a third `IConfigureOptions<TOptions>` service is added to the service container.</span></span> <span data-ttu-id="a778a-183">它将绑定`MySubOptions`的部分`subsection`的*appsettings.json*文件：</span><span class="sxs-lookup"><span data-stu-id="a778a-183">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[Main](configuration/sample/UsingOptions/Startup3.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="a778a-184">注意： 此扩展方法要求`Microsoft.Extensions.Options.ConfigurationExtensions`NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="a778a-184">Note: This extension method requires the `Microsoft.Extensions.Options.ConfigurationExtensions` NuGet package.</span></span>

<span data-ttu-id="a778a-185">使用以下*appsettings.json*文件：</span><span class="sxs-lookup"><span data-stu-id="a778a-185">Using the following *appsettings.json* file:</span></span>

[!code-json[Main](configuration/sample/UsingOptions/appsettings.json)]

<span data-ttu-id="a778a-186">`MySubOptions`类：</span><span class="sxs-lookup"><span data-stu-id="a778a-186">The `MySubOptions` class:</span></span>

[!code-csharp[Main](configuration/sample/UsingOptions/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="a778a-187">替换为以下`Controller`:</span><span class="sxs-lookup"><span data-stu-id="a778a-187">With the following `Controller`:</span></span>

[!code-csharp[Main](configuration/sample/UsingOptions/Controllers/HomeController2.cs?name=snippet1)]

<span data-ttu-id="a778a-188">`subOption1 = subvalue1_from_json, subOption2 = 200`返回。</span><span class="sxs-lookup"><span data-stu-id="a778a-188">`subOption1 = subvalue1_from_json, subOption2 = 200` is returned.</span></span>

<span data-ttu-id="a778a-189">也可以提供视图模型中的选项，或将注入`IOptions<TOptions>`直接到视图：</span><span class="sxs-lookup"><span data-stu-id="a778a-189">You can also supply options in a view model or inject `IOptions<TOptions>` directly into a view:</span></span>

[!code-html[Main](configuration/sample/UsingOptions/Views/Home/Index.cshtml?highlight=3-4,16-17,20-21)]

<a name=in-memory-provider></a>

## <a name="ioptionssnapshot"></a><span data-ttu-id="a778a-190">IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="a778a-190">IOptionsSnapshot</span></span>

<span data-ttu-id="a778a-191">*需要 ASP.NET Core 1.1 或更高版本。*</span><span class="sxs-lookup"><span data-stu-id="a778a-191">*Requires ASP.NET Core 1.1 or higher.*</span></span>

<span data-ttu-id="a778a-192">`IOptionsSnapshot`当配置文件发生更改时重新加载配置数据的支持。</span><span class="sxs-lookup"><span data-stu-id="a778a-192">`IOptionsSnapshot` supports reloading configuration data when the configuration file has changed.</span></span> <span data-ttu-id="a778a-193">它具有最小的开销。</span><span class="sxs-lookup"><span data-stu-id="a778a-193">It has minimal overhead.</span></span> <span data-ttu-id="a778a-194">使用`IOptionsSnapshot`与`reloadOnChange: true`，选项绑定到`IConfiguration`并更改时重新加载。</span><span class="sxs-lookup"><span data-stu-id="a778a-194">Using `IOptionsSnapshot` with `reloadOnChange: true`, the options are bound to `IConfiguration` and reloaded when changed.</span></span>

<span data-ttu-id="a778a-195">下面的示例演示如何新`IOptionsSnapshot`后创建*config.json*更改。</span><span class="sxs-lookup"><span data-stu-id="a778a-195">The following sample demonstrates how a new `IOptionsSnapshot` is created after *config.json* changes.</span></span> <span data-ttu-id="a778a-196">对服务器请求将返回相同时间*config.json*具有**不**更改。</span><span class="sxs-lookup"><span data-stu-id="a778a-196">Requests to the server will return the same time when *config.json* has **not** changed.</span></span> <span data-ttu-id="a778a-197">之后的第一个请求*config.json*做的更改将显示新的时间。</span><span class="sxs-lookup"><span data-stu-id="a778a-197">The first request after *config.json* changes will show a new time.</span></span>

[!code-csharp[Main](configuration/sample/IOptionsSnapshot2/Startup.cs?name=snippet1&highlight=1-9,13-18,32,33,52,53)]

<span data-ttu-id="a778a-198">下图显示服务器输出：</span><span class="sxs-lookup"><span data-stu-id="a778a-198">The following image shows the server output:</span></span>

![浏览器图像显示"上次更新时间： 2016 年 11 月 22 日下午 4:43"](configuration/_static/first.png)

<span data-ttu-id="a778a-200">刷新浏览器不会更改消息值或显示的时间 (时*config.json*仍是如此)。</span><span class="sxs-lookup"><span data-stu-id="a778a-200">Refreshing the browser doesn't change the message value or time displayed (when *config.json* has not changed).</span></span>

<span data-ttu-id="a778a-201">进行更改并保存*config.json*然后刷新浏览器：</span><span class="sxs-lookup"><span data-stu-id="a778a-201">Change and save the  *config.json* and then refresh the browser:</span></span>

![浏览器图像显示"上次更新到 e: 2016 年 11 月 22 日下午 4:53"](configuration/_static/change.png)

## <a name="in-memory-provider-and-binding-to-a-poco-class"></a><span data-ttu-id="a778a-203">内存中提供程序并绑定到 POCO 类</span><span class="sxs-lookup"><span data-stu-id="a778a-203">In-memory provider and binding to a POCO class</span></span>

<span data-ttu-id="a778a-204">下面的示例演示如何使用内存中提供程序并将绑定到类：</span><span class="sxs-lookup"><span data-stu-id="a778a-204">The following sample shows how to use the in-memory provider and bind to a class:</span></span>

[!code-csharp[Main](configuration/sample/InMemory/Program.cs)]

<span data-ttu-id="a778a-205">配置值将返回为字符串，但绑定可使对象的构造。</span><span class="sxs-lookup"><span data-stu-id="a778a-205">Configuration values are returned as strings, but binding enables the construction of objects.</span></span> <span data-ttu-id="a778a-206">绑定允许您检索 POCO 对象或甚至是整个对象图。</span><span class="sxs-lookup"><span data-stu-id="a778a-206">Binding allows you to retrieve POCO objects or even entire object graphs.</span></span> <span data-ttu-id="a778a-207">下面的示例演示如何将绑定到`MyWindow`，并且与 ASP.NET 核心 MVC 应用程序使用选项模式：</span><span class="sxs-lookup"><span data-stu-id="a778a-207">The following sample shows how to bind to `MyWindow` and use the options pattern with a ASP.NET Core MVC app:</span></span>

[!code-csharp[Main](configuration/sample/WebConfigBind/MyWindow.cs)]

[!code-json[Main](configuration/sample/WebConfigBind/appsettings.json)]

<span data-ttu-id="a778a-208">绑定中的自定义类`ConfigureServices`生成主机时：</span><span class="sxs-lookup"><span data-stu-id="a778a-208">Bind the custom class in `ConfigureServices` when building the host:</span></span>

[!code-csharp[Main](configuration/sample/WebConfigBind/Program.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="a778a-209">显示从设置`HomeController`:</span><span class="sxs-lookup"><span data-stu-id="a778a-209">Display the settings from the `HomeController`:</span></span>

[!code-csharp[Main](configuration/sample/WebConfigBind/Controllers/HomeController.cs)]

### <a name="getvalue"></a><span data-ttu-id="a778a-210">GetValue</span><span class="sxs-lookup"><span data-stu-id="a778a-210">GetValue</span></span>

<span data-ttu-id="a778a-211">下面的示例演示如何[GetValue<T> ](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationbinder#Microsoft_Extensions_Configuration_ConfigurationBinder_GetValue_Microsoft_Extensions_Configuration_IConfiguration_System_Type_System_String_System_Object_)扩展方法：</span><span class="sxs-lookup"><span data-stu-id="a778a-211">The following sample demonstrates the [GetValue<T>](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationbinder#Microsoft_Extensions_Configuration_ConfigurationBinder_GetValue_Microsoft_Extensions_Configuration_IConfiguration_System_Type_System_String_System_Object_) extension method:</span></span>

[!code-csharp[Main](configuration/sample/InMemoryGetValue/Program.cs?highlight=27-29)]

<span data-ttu-id="a778a-212">ConfigurationBinder`GetValue<T>`方法允许你指定默认值 (在此示例中为 80)。</span><span class="sxs-lookup"><span data-stu-id="a778a-212">The ConfigurationBinder's `GetValue<T>` method allows you to specify a default value (80 in the sample).</span></span> <span data-ttu-id="a778a-213">`GetValue<T>`用于简单的方案，不会绑定到整个部分。</span><span class="sxs-lookup"><span data-stu-id="a778a-213">`GetValue<T>` is for simple scenarios and does not bind to entire sections.</span></span> <span data-ttu-id="a778a-214">`GetValue<T>`获取标量值从`GetSection(key).Value`转换为特定类型。</span><span class="sxs-lookup"><span data-stu-id="a778a-214">`GetValue<T>` gets scalar values from `GetSection(key).Value` converted to a specific type.</span></span>

## <a name="binding-to-an-object-graph"></a><span data-ttu-id="a778a-215">绑定到对象图</span><span class="sxs-lookup"><span data-stu-id="a778a-215">Binding to an object graph</span></span>

<span data-ttu-id="a778a-216">你可以以递归方式绑定到类中每个对象。</span><span class="sxs-lookup"><span data-stu-id="a778a-216">You can recursively bind to each object in a class.</span></span> <span data-ttu-id="a778a-217">请考虑以下`AppOptions`类：</span><span class="sxs-lookup"><span data-stu-id="a778a-217">Consider the following `AppOptions` class:</span></span>

[!code-csharp[Main](configuration/sample/ObjectGraph/AppOptions.cs)]

<span data-ttu-id="a778a-218">下面的示例将绑定到`AppOptions`类：</span><span class="sxs-lookup"><span data-stu-id="a778a-218">The following sample binds to the `AppOptions` class:</span></span>

[!code-csharp[Main](configuration/sample/ObjectGraph/Program.cs?highlight=15-16)]

<span data-ttu-id="a778a-219">**ASP.NET 核心 1.1**和更高版本可以使用`Get<T>`，这适用于整个部分。</span><span class="sxs-lookup"><span data-stu-id="a778a-219">**ASP.NET Core 1.1** and higher can use  `Get<T>`, which works with entire sections.</span></span> <span data-ttu-id="a778a-220">`Get<T>`可以是比使用多个 convienent `Bind`。</span><span class="sxs-lookup"><span data-stu-id="a778a-220">`Get<T>` can be more convienent than using `Bind`.</span></span> <span data-ttu-id="a778a-221">下面的代码演示如何使用`Get<T>`与上面的示例：</span><span class="sxs-lookup"><span data-stu-id="a778a-221">The following code shows how to use `Get<T>` with the sample above:</span></span>

```csharp
var appConfig = config.GetSection("App").Get<AppOptions>();
```

<span data-ttu-id="a778a-222">使用以下*appsettings.json*文件：</span><span class="sxs-lookup"><span data-stu-id="a778a-222">Using the following *appsettings.json* file:</span></span>

[!code-json[Main](configuration/sample/ObjectGraph/appsettings.json)]

<span data-ttu-id="a778a-223">该程序显示`Height 11`。</span><span class="sxs-lookup"><span data-stu-id="a778a-223">The program displays `Height 11`.</span></span>

<span data-ttu-id="a778a-224">下面的代码可以用于单元测试配置：</span><span class="sxs-lookup"><span data-stu-id="a778a-224">The following code can be used to unit test the configuration:</span></span>

```csharp
[Fact]
public void CanBindObjectTree()
{
    var dict = new Dictionary<string, string>
        {
            {"App:Profile:Machine", "Rick"},
            {"App:Connection:Value", "connectionstring"},
            {"App:Window:Height", "11"},
            {"App:Window:Width", "11"}
        };
    var builder = new ConfigurationBuilder();
    builder.AddInMemoryCollection(dict);
    var config = builder.Build();

    var options = new AppOptions();
    config.GetSection("App").Bind(options);

    Assert.Equal("Rick", options.Profile.Machine);
    Assert.Equal(11, options.Window.Height);
    Assert.Equal(11, options.Window.Width);
    Assert.Equal("connectionstring", options.Connection.Value);
}
```

<a name=custom-config-providers></a>

## <a name="basic-sample-of-entity-framework-custom-provider"></a><span data-ttu-id="a778a-225">实体框架自定义提供程序的基本示例</span><span class="sxs-lookup"><span data-stu-id="a778a-225">Basic sample of Entity Framework custom provider</span></span>

<span data-ttu-id="a778a-226">在本部分中，创建的基本配置提供程序从使用 EF 数据库中读取名称-值对。</span><span class="sxs-lookup"><span data-stu-id="a778a-226">In this section, a basic configuration provider that reads name-value pairs from a database using EF is created.</span></span> 

<span data-ttu-id="a778a-227">定义`ConfigurationValue`在数据库中存储配置值的实体：</span><span class="sxs-lookup"><span data-stu-id="a778a-227">Define a `ConfigurationValue` entity for storing configuration values in the database:</span></span>

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/ConfigurationValue.cs)]

<span data-ttu-id="a778a-228">添加`ConfigurationContext`存储和访问配置的值：</span><span class="sxs-lookup"><span data-stu-id="a778a-228">Add a `ConfigurationContext` to store and access the configured values:</span></span>

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/ConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="a778a-229">创建一个类，实现[IConfigurationSource](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.iconfigurationsource):</span><span class="sxs-lookup"><span data-stu-id="a778a-229">Create an class that implements [IConfigurationSource](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.iconfigurationsource):</span></span>

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkConfigurationSource.cs?highlight=7)]

<span data-ttu-id="a778a-230">创建自定义配置提供程序通过继承[ConfigurationProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationprovider)。</span><span class="sxs-lookup"><span data-stu-id="a778a-230">Create the custom configuration provider by inheriting from [ConfigurationProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationprovider).</span></span>  <span data-ttu-id="a778a-231">为空时，配置提供程序初始化数据库：</span><span class="sxs-lookup"><span data-stu-id="a778a-231">The configuration provider initializes the database when it's empty:</span></span>

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkConfigurationProvider.cs?highlight=9,18-31,38-39)]

<span data-ttu-id="a778a-232">运行示例时，会显示数据库 （"value_from_ef_1"和"value_from_ef_2"） 中突出显示的值。</span><span class="sxs-lookup"><span data-stu-id="a778a-232">The highlighted values from the database ("value_from_ef_1" and "value_from_ef_2") are displayed when the sample is run.</span></span>

<span data-ttu-id="a778a-233">你可以添加`EFConfigSource`将该配置源添加的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="a778a-233">You can add an `EFConfigSource` extension method for adding the configuration source:</span></span>

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkExtensions.cs?highlight=12)]

<span data-ttu-id="a778a-234">下面的代码演示如何使用自定义`EFConfigProvider`:</span><span class="sxs-lookup"><span data-stu-id="a778a-234">The following code shows how to use the custom `EFConfigProvider`:</span></span>

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/Program.cs?highlight=21-26)]

<span data-ttu-id="a778a-235">请注意此示例将添加自定义`EFConfigProvider`后 JSON 提供程序，因此数据库中的任何设置将替代设置从*appsettings.json*文件。</span><span class="sxs-lookup"><span data-stu-id="a778a-235">Note the sample adds the custom `EFConfigProvider` after the JSON provider, so any settings from the database will override settings from the *appsettings.json* file.</span></span>

<span data-ttu-id="a778a-236">使用以下*appsettings.json*文件：</span><span class="sxs-lookup"><span data-stu-id="a778a-236">Using the following *appsettings.json* file:</span></span>

[!code-json[Main](configuration/sample/CustomConfigurationProvider/appsettings.json)]

<span data-ttu-id="a778a-237">将显示以下内容：</span><span class="sxs-lookup"><span data-stu-id="a778a-237">The following is displayed:</span></span>

```console
key1=value_from_ef_1
key2=value_from_ef_2
key3=value_from_json_3
```

## <a name="commandline-configuration-provider"></a><span data-ttu-id="a778a-238">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="a778a-238">CommandLine configuration provider</span></span>

<span data-ttu-id="a778a-239">[命令行配置提供程序](/aspnet/core/api/microsoft.extensions.configuration.commandline.commandlineconfigurationprovider)接收命令行自变量在运行时配置的键 / 值对。</span><span class="sxs-lookup"><span data-stu-id="a778a-239">The [CommandLine configuration provider](/aspnet/core/api/microsoft.extensions.configuration.commandline.commandlineconfigurationprovider) receives command-line argument key-value pairs for configuration at runtime.</span></span>

[<span data-ttu-id="a778a-240">查看或下载命令行配置示例</span><span class="sxs-lookup"><span data-stu-id="a778a-240">View or download the CommandLine configuration sample</span></span>](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/sample/CommandLine)

### <a name="setting-up-the-provider"></a><span data-ttu-id="a778a-241">设置提供程序</span><span class="sxs-lookup"><span data-stu-id="a778a-241">Setting up the provider</span></span>

# <a name="basic-configurationtabbasicconfiguration"></a>[<span data-ttu-id="a778a-242">基本配置</span><span class="sxs-lookup"><span data-stu-id="a778a-242">Basic Configuration</span></span>](#tab/basicconfiguration)

<span data-ttu-id="a778a-243">若要激活命令行配置，请调用`AddCommandLine`实例上的扩展方法[ConfigurationBuilder](/api/microsoft.extensions.configuration.configurationbuilder):</span><span class="sxs-lookup"><span data-stu-id="a778a-243">To activate command-line configuration, call the `AddCommandLine` extension method on an instance of [ConfigurationBuilder](/api/microsoft.extensions.configuration.configurationbuilder):</span></span>

[!code-csharp[Main](configuration/sample_snapshot/CommandLine/Program.cs?highlight=18,21)]

<span data-ttu-id="a778a-244">运行代码，将显示以下输出：</span><span class="sxs-lookup"><span data-stu-id="a778a-244">Running the code, the following output is displayed:</span></span>

```console
MachineName: MairaPC
Left: 1980
```

<span data-ttu-id="a778a-245">键 / 值对的自变量传递命令行上更改的值`Profile:MachineName`和`App:MainWindow:Left`:</span><span class="sxs-lookup"><span data-stu-id="a778a-245">Passing argument key-value pairs on the command line changes the values of `Profile:MachineName` and `App:MainWindow:Left`:</span></span>

```console
dotnet run Profile:MachineName=BartPC App:MainWindow:Left=1979
```

<span data-ttu-id="a778a-246">控制台窗口会显示：</span><span class="sxs-lookup"><span data-stu-id="a778a-246">The console window displays:</span></span>

```console
MachineName: BartPC
Left: 1979
```

<span data-ttu-id="a778a-247">若要重写使用命令行配置提供其他配置提供程序配置，调用`AddCommandLine`中的最后一个`ConfigurationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="a778a-247">To override configuration provided by other configuration providers with command-line configuration, call `AddCommandLine` last on `ConfigurationBuilder`:</span></span>

[!code-csharp[Main](configuration/sample_snapshot/CommandLine/Program2.cs?range=11-16&highlight=1,5)]

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a778a-248">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="a778a-248">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="a778a-249">典型的 ASP.NET Core 2.x 应用程序使用的静态简便方法`CreateDefaultBuilder`来生成主机：</span><span class="sxs-lookup"><span data-stu-id="a778a-249">Typical ASP.NET Core 2.x apps use the static convenience method `CreateDefaultBuilder` to build the host:</span></span>

[!code-csharp[Main](configuration/sample_snapshot/Program.cs?highlight=12)]

<span data-ttu-id="a778a-250">`CreateDefaultBuilder`加载从的可选配置*appsettings.json*， *appsettings。 {环境}.json*，[用户机密](xref:security/app-secrets)(在`Development`环境)，环境变量和命令行参数。</span><span class="sxs-lookup"><span data-stu-id="a778a-250">`CreateDefaultBuilder` loads optional configuration from *appsettings.json*, *appsettings.{Environment}.json*, [user secrets](xref:security/app-secrets) (in the `Development` environment), environment variables, and command-line arguments.</span></span> <span data-ttu-id="a778a-251">最后调用命令行配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="a778a-251">The CommandLine configuration provider is called last.</span></span> <span data-ttu-id="a778a-252">上次调用提供程序允许在运行时以替代配置设置的其他配置提供程序传递的命令行自变量调用更早版本。</span><span class="sxs-lookup"><span data-stu-id="a778a-252">Calling the provider last allows the command-line arguments passed at runtime to override configuration set by the other configuration providers called earlier.</span></span>

<span data-ttu-id="a778a-253">请注意，对于*appsettings*文件`reloadOnChange`已启用。</span><span class="sxs-lookup"><span data-stu-id="a778a-253">Note that for *appsettings* files that `reloadOnChange` is enabled.</span></span> <span data-ttu-id="a778a-254">命令行自变量被重写，如果匹配的配置值在*appsettings*文件在应用启动后已更改。</span><span class="sxs-lookup"><span data-stu-id="a778a-254">Command-line arguments are overridden if a matching configuration value in an *appsettings* file is changed after the app starts.</span></span>

> [!NOTE]
> <span data-ttu-id="a778a-255">作为使用的替代方法`CreateDefaultBuilder`方法，使用创建主机[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder)和手动生成配置[ConfigurationBuilder](/api/microsoft.extensions.configuration.configurationbuilder)中 ASP.NET Core 支持 2.x。</span><span class="sxs-lookup"><span data-stu-id="a778a-255">As an alternative to using the `CreateDefaultBuilder` method, creating a host using [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) and manually building configuration with [ConfigurationBuilder](/api/microsoft.extensions.configuration.configurationbuilder) is supported in ASP.NET Core 2.x.</span></span> <span data-ttu-id="a778a-256">请参阅 ASP.NET Core 1.x 选项卡的详细信息。</span><span class="sxs-lookup"><span data-stu-id="a778a-256">See the ASP.NET Core 1.x tab for more information.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a778a-257">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a778a-257">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="a778a-258">创建[ConfigurationBuilder](/api/microsoft.extensions.configuration.configurationbuilder)并调用`AddCommandLine`要使用命令行配置提供程序方法。</span><span class="sxs-lookup"><span data-stu-id="a778a-258">Create a [ConfigurationBuilder](/api/microsoft.extensions.configuration.configurationbuilder) and call the `AddCommandLine` method to use the CommandLine configuration provider.</span></span> <span data-ttu-id="a778a-259">上次调用提供程序允许在运行时以替代配置设置的其他配置提供程序传递的命令行自变量调用更早版本。</span><span class="sxs-lookup"><span data-stu-id="a778a-259">Calling the provider last allows the command-line arguments passed at runtime to override configuration set by the other configuration providers called earlier.</span></span> <span data-ttu-id="a778a-260">向其应用配置[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder)与`UseConfiguration`方法：</span><span class="sxs-lookup"><span data-stu-id="a778a-260">Apply the configuration to [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) with the `UseConfiguration` method:</span></span>

[!code-csharp[Main](configuration/sample_snapshot/CommandLine/Program2.cs?highlight=11,15,19)]

---

### <a name="arguments"></a><span data-ttu-id="a778a-261">参数</span><span class="sxs-lookup"><span data-stu-id="a778a-261">Arguments</span></span>

<span data-ttu-id="a778a-262">在命令行上传递自变量必须符合以下表所示的两种格式之一。</span><span class="sxs-lookup"><span data-stu-id="a778a-262">Arguments passed on the command line must conform to one of two formats shown in the following table.</span></span>

| <span data-ttu-id="a778a-263">参数格式</span><span class="sxs-lookup"><span data-stu-id="a778a-263">Argument format</span></span>                                                     | <span data-ttu-id="a778a-264">示例</span><span class="sxs-lookup"><span data-stu-id="a778a-264">Example</span></span>        |
| ------------------------------------------------------------------- | :------------: |
| <span data-ttu-id="a778a-265">单个参数： 用一个等号分隔的键-值对 (`=`)</span><span class="sxs-lookup"><span data-stu-id="a778a-265">Single argument: a key-value pair separated by an equals sign (`=`)</span></span> | `key1=value`   |
| <span data-ttu-id="a778a-266">两个参数的序列： 键 / 值对，并用空格分隔</span><span class="sxs-lookup"><span data-stu-id="a778a-266">Sequence of two arguments: a key-value pair separated by a space</span></span>    | `/key1 value1` |

<span data-ttu-id="a778a-267">**单个自变量**</span><span class="sxs-lookup"><span data-stu-id="a778a-267">**Single argument**</span></span>

<span data-ttu-id="a778a-268">值必须跟一个等号 (`=`)。</span><span class="sxs-lookup"><span data-stu-id="a778a-268">The value must follow an equals sign (`=`).</span></span> <span data-ttu-id="a778a-269">该值可以为 null (例如， `mykey=`)。</span><span class="sxs-lookup"><span data-stu-id="a778a-269">The value can be null (for example, `mykey=`).</span></span>

<span data-ttu-id="a778a-270">键可能具有的前缀。</span><span class="sxs-lookup"><span data-stu-id="a778a-270">The key may have a prefix.</span></span>

| <span data-ttu-id="a778a-271">键前缀</span><span class="sxs-lookup"><span data-stu-id="a778a-271">Key prefix</span></span>               | <span data-ttu-id="a778a-272">示例</span><span class="sxs-lookup"><span data-stu-id="a778a-272">Example</span></span>         |
| ------------------------ | :-------------: |
| <span data-ttu-id="a778a-273">没有任何前缀</span><span class="sxs-lookup"><span data-stu-id="a778a-273">No prefix</span></span>                | `key1=value1`   |
| <span data-ttu-id="a778a-274">单一短划线 (`-`) &#8224;</span><span class="sxs-lookup"><span data-stu-id="a778a-274">Single dash (`-`)&#8224;</span></span> | `-key2=value2`  |
| <span data-ttu-id="a778a-275">两个短划线 (`--`)</span><span class="sxs-lookup"><span data-stu-id="a778a-275">Two dashes (`--`)</span></span>        | `--key3=value3` |
| <span data-ttu-id="a778a-276">正斜杠 (`/`)</span><span class="sxs-lookup"><span data-stu-id="a778a-276">Forward slash (`/`)</span></span>      | `/key4=value4`  |

<span data-ttu-id="a778a-277">&#8224;具有单个仪表前缀的键 (`-`) 必须在提供[切换映射](#switch-mappings)、 下面所述。</span><span class="sxs-lookup"><span data-stu-id="a778a-277">&#8224;A key with a single dash prefix (`-`) must be provided in [switch mappings](#switch-mappings), described below.</span></span>

<span data-ttu-id="a778a-278">示例命令：</span><span class="sxs-lookup"><span data-stu-id="a778a-278">Example command:</span></span>

```console
dotnet run key1=value1 -key2=value2 --key3=value3 /key4=value4
```

<span data-ttu-id="a778a-279">注意： 如果`-key1`不存在于[切换映射](#switch-mappings)赋予配置提供程序，`FormatException`引发。</span><span class="sxs-lookup"><span data-stu-id="a778a-279">Note: If `-key1` isn't present in the [switch mappings](#switch-mappings) given to the configuration provider, a `FormatException` is thrown.</span></span>

<span data-ttu-id="a778a-280">**两个自变量的序列**</span><span class="sxs-lookup"><span data-stu-id="a778a-280">**Sequence of two arguments**</span></span>

<span data-ttu-id="a778a-281">值不能为 null，并且必须符合以空格分隔的密钥。</span><span class="sxs-lookup"><span data-stu-id="a778a-281">The value can't be null and must follow the key separated by a space.</span></span>

<span data-ttu-id="a778a-282">密钥必须具有的前缀。</span><span class="sxs-lookup"><span data-stu-id="a778a-282">The key must have a prefix.</span></span>

| <span data-ttu-id="a778a-283">键前缀</span><span class="sxs-lookup"><span data-stu-id="a778a-283">Key prefix</span></span>               | <span data-ttu-id="a778a-284">示例</span><span class="sxs-lookup"><span data-stu-id="a778a-284">Example</span></span>         |
| ------------------------ | :-------------: |
| <span data-ttu-id="a778a-285">单一短划线 (`-`) &#8224;</span><span class="sxs-lookup"><span data-stu-id="a778a-285">Single dash (`-`)&#8224;</span></span> | `-key1 value1`  |
| <span data-ttu-id="a778a-286">两个短划线 (`--`)</span><span class="sxs-lookup"><span data-stu-id="a778a-286">Two dashes (`--`)</span></span>        | `--key2 value2` |
| <span data-ttu-id="a778a-287">正斜杠 (`/`)</span><span class="sxs-lookup"><span data-stu-id="a778a-287">Forward slash (`/`)</span></span>      | `/key3 value3`  |

<span data-ttu-id="a778a-288">&#8224;具有单个仪表前缀的键 (`-`) 必须在提供[切换映射](#switch-mappings)、 下面所述。</span><span class="sxs-lookup"><span data-stu-id="a778a-288">&#8224;A key with a single dash prefix (`-`) must be provided in [switch mappings](#switch-mappings), described below.</span></span>

<span data-ttu-id="a778a-289">示例命令：</span><span class="sxs-lookup"><span data-stu-id="a778a-289">Example command:</span></span>

```console
dotnet run -key1 value1 --key2 value2 /key3 value3
```

<span data-ttu-id="a778a-290">注意： 如果`-key1`不存在于[切换映射](#switch-mappings)赋予配置提供程序，`FormatException`引发。</span><span class="sxs-lookup"><span data-stu-id="a778a-290">Note: If `-key1` isn't present in the [switch mappings](#switch-mappings) given to the configuration provider, a `FormatException` is thrown.</span></span>

### <a name="duplicate-keys"></a><span data-ttu-id="a778a-291">重复键</span><span class="sxs-lookup"><span data-stu-id="a778a-291">Duplicate keys</span></span>

<span data-ttu-id="a778a-292">如果提供了重复的键，则使用最后一个键 / 值对。</span><span class="sxs-lookup"><span data-stu-id="a778a-292">If duplicate keys are provided, the last key-value pair is used.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="a778a-293">切换映射</span><span class="sxs-lookup"><span data-stu-id="a778a-293">Switch mappings</span></span>

<span data-ttu-id="a778a-294">手动生成配置时`ConfigurationBuilder`，你可以根据需要提供交换机映射字典`AddCommandLine`方法。</span><span class="sxs-lookup"><span data-stu-id="a778a-294">When manually building configuration with `ConfigurationBuilder`, you can optionally provide a switch mappings dictionary to the `AddCommandLine` method.</span></span> <span data-ttu-id="a778a-295">交换机映射可用于提供密钥名称更换逻辑。</span><span class="sxs-lookup"><span data-stu-id="a778a-295">Switch mappings allow you to provide key name replacement logic.</span></span>

<span data-ttu-id="a778a-296">当使用交换机映射字典时，字典将检查与提供的命令行自变量的键匹配的键。</span><span class="sxs-lookup"><span data-stu-id="a778a-296">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="a778a-297">如果在字典中找到命令行的键，字典值 （密钥更换） 传递回来，以便将配置设置。</span><span class="sxs-lookup"><span data-stu-id="a778a-297">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the configuration.</span></span> <span data-ttu-id="a778a-298">交换机映射是所必需的任何命令行的键，加上单个短划线 (`-`)。</span><span class="sxs-lookup"><span data-stu-id="a778a-298">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="a778a-299">切换映射字典键规则：</span><span class="sxs-lookup"><span data-stu-id="a778a-299">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="a778a-300">交换机必须以短划线开头 (`-`) 或双短划线 (`--`)。</span><span class="sxs-lookup"><span data-stu-id="a778a-300">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="a778a-301">交换机映射字典不能包含重复键。</span><span class="sxs-lookup"><span data-stu-id="a778a-301">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="a778a-302">在下面的示例中，`GetSwitchMappings`方法允许你使用单个仪表的命令行自变量 (`-`) 密钥前缀，并避免前导子项的前缀。</span><span class="sxs-lookup"><span data-stu-id="a778a-302">In the following example, the `GetSwitchMappings` method allows your command-line arguments to use a single dash (`-`) key prefix and avoid leading subkey prefixes.</span></span>

[!code-csharp[Main](configuration/sample/CommandLine/Program.cs?highlight=10-19,32)]

<span data-ttu-id="a778a-303">未提供命令行参数，字典提供给`AddInMemoryCollection`设置配置值。</span><span class="sxs-lookup"><span data-stu-id="a778a-303">Without providing command-line arguments, the dictionary provided to `AddInMemoryCollection` sets the configuration values.</span></span> <span data-ttu-id="a778a-304">使用以下命令运行该应用程序：</span><span class="sxs-lookup"><span data-stu-id="a778a-304">Run the app with the following command:</span></span>

```console
dotnet run
```

<span data-ttu-id="a778a-305">控制台窗口会显示：</span><span class="sxs-lookup"><span data-stu-id="a778a-305">The console window displays:</span></span>

```console
MachineName: RickPC
Left: 1980
```

<span data-ttu-id="a778a-306">使用以下方法来配置设置中传递：</span><span class="sxs-lookup"><span data-stu-id="a778a-306">Use the following to pass in configuration settings:</span></span>

```console
dotnet run /Profile:MachineName=DahliaPC /App:MainWindow:Left=1984
```

<span data-ttu-id="a778a-307">控制台窗口会显示：</span><span class="sxs-lookup"><span data-stu-id="a778a-307">The console window displays:</span></span>

```console
MachineName: DahliaPC
Left: 1984
```

<span data-ttu-id="a778a-308">创建交换机映射字典后，它包含下表中显示的数据。</span><span class="sxs-lookup"><span data-stu-id="a778a-308">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="a778a-309">键</span><span class="sxs-lookup"><span data-stu-id="a778a-309">Key</span></span>            | <span data-ttu-id="a778a-310">值</span><span class="sxs-lookup"><span data-stu-id="a778a-310">Value</span></span>                 |
| -------------- | --------------------- |
| `-MachineName` | `Profile:MachineName` |
| `-Left`        | `App:MainWindow:Left` |

<span data-ttu-id="a778a-311">若要演示密钥切换使用字典，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="a778a-311">To demonstrate key switching using the dictionary, run the following command:</span></span>

```console
dotnet run -MachineName=ChadPC -Left=1988
```

<span data-ttu-id="a778a-312">将交换的命令行的键。</span><span class="sxs-lookup"><span data-stu-id="a778a-312">The command-line keys are swapped.</span></span> <span data-ttu-id="a778a-313">控制台窗口会显示的配置值`Profile:MachineName`和`App:MainWindow:Left`:</span><span class="sxs-lookup"><span data-stu-id="a778a-313">The console window displays the configuration values for `Profile:MachineName` and `App:MainWindow:Left`:</span></span>

```console
MachineName: ChadPC
Left: 1988
```

## <a name="the-webconfig-file"></a><span data-ttu-id="a778a-314">Web.config 文件</span><span class="sxs-lookup"><span data-stu-id="a778a-314">The web.config file</span></span>

<span data-ttu-id="a778a-315">A *web.config*文件是必需的当你在 IIS 或 IIS Express 应用程序承载。</span><span class="sxs-lookup"><span data-stu-id="a778a-315">A *web.config* file is required when you host the app in IIS or IIS-Express.</span></span> <span data-ttu-id="a778a-316">*web.config*开启 AspNetCoreModule IIS 启动你的应用中。</span><span class="sxs-lookup"><span data-stu-id="a778a-316">*web.config* turns on the AspNetCoreModule in IIS to launch your app.</span></span> <span data-ttu-id="a778a-317">中的设置*web.config*启用 IIS 来启动你的应用和配置其他 IIS 设置和模块中 AspNetCoreModule。</span><span class="sxs-lookup"><span data-stu-id="a778a-317">Settings in *web.config* enable the AspNetCoreModule in IIS to launch your app and configure other IIS settings and modules.</span></span> <span data-ttu-id="a778a-318">如果你使用的 Visual Studio 将删除*web.config*，Visual Studio 将创建一个新。</span><span class="sxs-lookup"><span data-stu-id="a778a-318">If you are using Visual Studio and delete *web.config*, Visual Studio will create a new one.</span></span>

## <a name="additional-notes"></a><span data-ttu-id="a778a-319">附加说明</span><span class="sxs-lookup"><span data-stu-id="a778a-319">Additional notes</span></span>

* <span data-ttu-id="a778a-320">依赖关系注入 (DI) 未设置截止日期之后`ConfigureServices`调用。</span><span class="sxs-lookup"><span data-stu-id="a778a-320">Dependency Injection (DI) is not set up until after `ConfigureServices` is invoked.</span></span>
* <span data-ttu-id="a778a-321">配置系统不是 DI 感知。</span><span class="sxs-lookup"><span data-stu-id="a778a-321">The configuration system is not DI aware.</span></span>
* <span data-ttu-id="a778a-322">`IConfiguration`具有两个专用化：</span><span class="sxs-lookup"><span data-stu-id="a778a-322">`IConfiguration` has two specializations:</span></span>
  * <span data-ttu-id="a778a-323">`IConfigurationRoot`用于根节点。</span><span class="sxs-lookup"><span data-stu-id="a778a-323">`IConfigurationRoot`  Used for the root node.</span></span> <span data-ttu-id="a778a-324">可以触发重新加载。</span><span class="sxs-lookup"><span data-stu-id="a778a-324">Can trigger a reload.</span></span>
  * <span data-ttu-id="a778a-325">`IConfigurationSection`表示一个节的配置值。</span><span class="sxs-lookup"><span data-stu-id="a778a-325">`IConfigurationSection`  Represents a section of configuration values.</span></span> <span data-ttu-id="a778a-326">`GetSection`和`GetChildren`方法返回`IConfigurationSection`。</span><span class="sxs-lookup"><span data-stu-id="a778a-326">The `GetSection` and `GetChildren` methods return an `IConfigurationSection`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a778a-327">其他资源</span><span class="sxs-lookup"><span data-stu-id="a778a-327">Additional resources</span></span>

* [<span data-ttu-id="a778a-328">使用多个环境</span><span class="sxs-lookup"><span data-stu-id="a778a-328">Working with Multiple Environments</span></span>](environments.md)
* [<span data-ttu-id="a778a-329">在开发期间安全存储应用密钥</span><span class="sxs-lookup"><span data-stu-id="a778a-329">Safe storage of app secrets during development</span></span>](../security/app-secrets.md)
* [<span data-ttu-id="a778a-330">在 ASP.NET Core 中承载</span><span class="sxs-lookup"><span data-stu-id="a778a-330">Hosting in ASP.NET Core</span></span>](xref:fundamentals/hosting)
* [<span data-ttu-id="a778a-331">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="a778a-331">Dependency Injection</span></span>](dependency-injection.md)
* [<span data-ttu-id="a778a-332">Azure Key Vault 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="a778a-332">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration)
