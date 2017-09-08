---
title: "ASP.NET 核心中配置"
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
ms.openlocfilehash: dae7ac6e377d2c17bc8f86e5b6da98107366cc73
ms.sourcegitcommit: 418e6aa4ab79474ecc4d0a6af573a3759b113fe4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2017
---
<a name=fundamentals-configuration></a>

  # <a name="configuration-in-aspnet-core"></a><span data-ttu-id="d5120-104">ASP.NET 核心中配置</span><span class="sxs-lookup"><span data-stu-id="d5120-104">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="d5120-105">[Rick Anderson](https://twitter.com/RickAndMSFT)，[标记 Michaelis](http://intellitect.com/author/mark-michaelis/)， [Steve Smith](http://ardalis.com)，和[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="d5120-105">[Rick Anderson](https://twitter.com/RickAndMSFT), [Mark Michaelis](http://intellitect.com/author/mark-michaelis/), [Steve Smith](http://ardalis.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="d5120-106">配置 API 提供一种方法配置应用程序中基于名称-值对的列表。</span><span class="sxs-lookup"><span data-stu-id="d5120-106">The Configuration API provides a way of configuring an app based on a list of name-value pairs.</span></span> <span data-ttu-id="d5120-107">在运行时从多个源读取配置。</span><span class="sxs-lookup"><span data-stu-id="d5120-107">Configuration is read at runtime from multiple sources.</span></span> <span data-ttu-id="d5120-108">名称-值对可以分组到多级的层次结构。</span><span class="sxs-lookup"><span data-stu-id="d5120-108">The name-value pairs can be grouped into a multi-level hierarchy.</span></span> <span data-ttu-id="d5120-109">有配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="d5120-109">There are configuration providers for:</span></span>

* <span data-ttu-id="d5120-110">文件格式 （INI、 JSON 和 XML）</span><span class="sxs-lookup"><span data-stu-id="d5120-110">File formats (INI, JSON, and XML)</span></span>
* <span data-ttu-id="d5120-111">命令行自变量</span><span class="sxs-lookup"><span data-stu-id="d5120-111">Command-line arguments</span></span>
* <span data-ttu-id="d5120-112">环境变量</span><span class="sxs-lookup"><span data-stu-id="d5120-112">Environment variables</span></span>
* <span data-ttu-id="d5120-113">内存中的.NET 对象</span><span class="sxs-lookup"><span data-stu-id="d5120-113">In-memory .NET objects</span></span>
* <span data-ttu-id="d5120-114">一个加密的用户存储区</span><span class="sxs-lookup"><span data-stu-id="d5120-114">An encrypted user store</span></span>
* [<span data-ttu-id="d5120-115">Azure 密钥保管库</span><span class="sxs-lookup"><span data-stu-id="d5120-115">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
* <span data-ttu-id="d5120-116">自定义提供程序，你安装或在创建</span><span class="sxs-lookup"><span data-stu-id="d5120-116">Custom providers, which you install or create</span></span>

<span data-ttu-id="d5120-117">每个配置值将映射到字符串键。</span><span class="sxs-lookup"><span data-stu-id="d5120-117">Each configuration value maps to a string key.</span></span> <span data-ttu-id="d5120-118">若要反序列化到自定义设置的内置绑定支持[POCO](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)对象 （具有属性的简单.NET 类）。</span><span class="sxs-lookup"><span data-stu-id="d5120-118">There's built-in binding support to deserialize settings into a custom [POCO](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object) object (a simple .NET class with properties).</span></span>

[<span data-ttu-id="d5120-119">查看或下载示例代码</span><span class="sxs-lookup"><span data-stu-id="d5120-119">View or download sample code</span></span>](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/sample)

## <a name="simple-configuration"></a><span data-ttu-id="d5120-120">简单的配置</span><span class="sxs-lookup"><span data-stu-id="d5120-120">Simple configuration</span></span>

<span data-ttu-id="d5120-121">以下控制台应用程序使用 JSON 配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="d5120-121">The following console app uses the JSON configuration provider:</span></span>

<span data-ttu-id="d5120-122">[!code-csharp[Main](configuration/sample/src/ConfigJson/Program.cs)]</span><span class="sxs-lookup"><span data-stu-id="d5120-122">[!code-csharp[Main](configuration/sample/src/ConfigJson/Program.cs)]</span></span>

<span data-ttu-id="d5120-123">应用程序读取，并显示以下配置设置：</span><span class="sxs-lookup"><span data-stu-id="d5120-123">The app reads and displays the following configuration settings:</span></span>

<span data-ttu-id="d5120-124">[!code-json[Main](configuration/sample/src/ConfigJson/appsettings.json)]</span><span class="sxs-lookup"><span data-stu-id="d5120-124">[!code-json[Main](configuration/sample/src/ConfigJson/appsettings.json)]</span></span>

<span data-ttu-id="d5120-125">配置由在其中节点以冒号分隔的名称-值对的分层列表组成。</span><span class="sxs-lookup"><span data-stu-id="d5120-125">Configuration consists of a hierarchical list of name-value pairs in which the nodes are separated by a colon.</span></span> <span data-ttu-id="d5120-126">若要检索一个值，请访问`Configuration`与相应的项的键的索引器：</span><span class="sxs-lookup"><span data-stu-id="d5120-126">To retrieve a value, access the `Configuration` indexer with the corresponding item's key:</span></span>

```csharp
Console.WriteLine($"option1 = {Configuration["subsection:suboption1"]}");
```

<span data-ttu-id="d5120-127">若要使用 JSON 格式配置源中的数组，使用作为一部分的冒号分隔的字符串的数组索引。</span><span class="sxs-lookup"><span data-stu-id="d5120-127">To work with arrays in JSON-formatted configuration sources, use a array index as part of the colon-separated string.</span></span> <span data-ttu-id="d5120-128">下面的示例获取在前面的第一项的名称`wizards`数组：</span><span class="sxs-lookup"><span data-stu-id="d5120-128">The following example gets the name of the first item in the preceding `wizards` array:</span></span>

```csharp
Console.Write($"{Configuration["wizards:0:Name"]}, ");
```

<span data-ttu-id="d5120-129">写入到生成的名称-值对中`Configuration`提供程序是**不**保持不变，但是，你可以创建的自定义提供程序将保存值。</span><span class="sxs-lookup"><span data-stu-id="d5120-129">Name-value pairs written to the built in `Configuration` providers are **not** persisted, however, you can create a custom provider that saves values.</span></span> <span data-ttu-id="d5120-130">请参阅[自定义配置提供程序](xref:fundamentals/configuration#custom-config-providers)。</span><span class="sxs-lookup"><span data-stu-id="d5120-130">See [custom configuration provider](xref:fundamentals/configuration#custom-config-providers).</span></span>

<span data-ttu-id="d5120-131">上面的示例使用配置索引器可以读取值。</span><span class="sxs-lookup"><span data-stu-id="d5120-131">The preceding sample uses the configuration indexer to read values.</span></span> <span data-ttu-id="d5120-132">外部访问配置`Startup`，使用[选项模式](xref:fundamentals/configuration#options-config-objects)。</span><span class="sxs-lookup"><span data-stu-id="d5120-132">To access configuration outside of `Startup`, use the [options pattern](xref:fundamentals/configuration#options-config-objects).</span></span> <span data-ttu-id="d5120-133">*选项模式*本文后面所示。</span><span class="sxs-lookup"><span data-stu-id="d5120-133">The *options pattern* is shown later in this article.</span></span>

<span data-ttu-id="d5120-134">它是通常具有不同的配置设置的不同的环境，例如，开发、 测试和生产。</span><span class="sxs-lookup"><span data-stu-id="d5120-134">It's typical to have different configuration settings for different environments, for example, development, test and production.</span></span> <span data-ttu-id="d5120-135">以下突出显示的代码将两个配置提供程序添加到三种来源：</span><span class="sxs-lookup"><span data-stu-id="d5120-135">The following highlighted code adds two configuration providers to three sources:</span></span>

1. <span data-ttu-id="d5120-136">JSON 提供程序，读取*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="d5120-136">JSON provider, reading *appsettings.json*</span></span>
2. <span data-ttu-id="d5120-137">JSON 提供程序，读取*appsettings。\<EnvironmentName >.json*</span><span class="sxs-lookup"><span data-stu-id="d5120-137">JSON provider, reading *appsettings.\<EnvironmentName>.json*</span></span>
3. <span data-ttu-id="d5120-138">环境变量提供程序</span><span class="sxs-lookup"><span data-stu-id="d5120-138">Environment variables provider</span></span>

<span data-ttu-id="d5120-139">[!code-csharp[Main](configuration/sample/src/WebConfigBind/Startup.cs?name=snippet2&highlight=7-9)]</span><span class="sxs-lookup"><span data-stu-id="d5120-139">[!code-csharp[Main](configuration/sample/src/WebConfigBind/Startup.cs?name=snippet2&highlight=7-9)]</span></span>

<span data-ttu-id="d5120-140">请参阅[AddJsonFile](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.jsonconfigurationextensions)有关参数的说明。</span><span class="sxs-lookup"><span data-stu-id="d5120-140">See [AddJsonFile](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.jsonconfigurationextensions) for an explanation of the parameters.</span></span> <span data-ttu-id="d5120-141">`reloadOnChange`仅支持在 ASP.NET 核心 1.1 和更高版本。</span><span class="sxs-lookup"><span data-stu-id="d5120-141">`reloadOnChange` is only supported in ASP.NET Core 1.1 and higher.</span></span> 

<span data-ttu-id="d5120-142">配置源中指定它们的顺序读取。</span><span class="sxs-lookup"><span data-stu-id="d5120-142">Configuration sources are read in the order they are specified.</span></span> <span data-ttu-id="d5120-143">在上面的代码中，最后读取环境变量。</span><span class="sxs-lookup"><span data-stu-id="d5120-143">In the code above, the environment variables are read last.</span></span> <span data-ttu-id="d5120-144">任何通过环境设置的配置值会替换在两个以前的提供程序中设置。</span><span class="sxs-lookup"><span data-stu-id="d5120-144">Any configuration values set through the environment would replace those set in the two previous providers.</span></span>

<span data-ttu-id="d5120-145">环境通常设置为其中一个`Development`， `Staging`，或`Production`。</span><span class="sxs-lookup"><span data-stu-id="d5120-145">The environment is typically set to one of `Development`, `Staging`, or `Production`.</span></span> <span data-ttu-id="d5120-146">请参阅[使用多个环境](environments.md)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="d5120-146">See [Working with Multiple Environments](environments.md) for more information.</span></span>

<span data-ttu-id="d5120-147">配置注意事项：</span><span class="sxs-lookup"><span data-stu-id="d5120-147">Configuration considerations:</span></span>

* <span data-ttu-id="d5120-148">`IOptionsSnapshot`当更改时，可以重新加载配置数据。</span><span class="sxs-lookup"><span data-stu-id="d5120-148">`IOptionsSnapshot` can reload configuration data when it changes.</span></span> <span data-ttu-id="d5120-149">使用`IOptionsSnapshot`如果需要重新加载配置数据。</span><span class="sxs-lookup"><span data-stu-id="d5120-149">Use `IOptionsSnapshot` if you need to reload configuration data.</span></span>  <span data-ttu-id="d5120-150">请参阅[IOptionsSnapshot](#ioptionssnapshot)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="d5120-150">See [IOptionsSnapshot](#ioptionssnapshot) for more information.</span></span>
* <span data-ttu-id="d5120-151">配置密钥是区分大小写。</span><span class="sxs-lookup"><span data-stu-id="d5120-151">Configuration keys are case insensitive.</span></span>
* <span data-ttu-id="d5120-152">一种最佳做法是上一次，指定环境变量，使本地环境可以重写在已部署的配置文件中设置的任何内容。</span><span class="sxs-lookup"><span data-stu-id="d5120-152">A best practice is to specify environment variables last, so that the local environment can override anything set in deployed configuration files.</span></span>
* <span data-ttu-id="d5120-153">**永远不会**在配置提供程序代码或纯文本配置文件中存储密码或其他敏感数据。</span><span class="sxs-lookup"><span data-stu-id="d5120-153">**Never** store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="d5120-154">请不要在你的开发中使用生产机密或测试环境。</span><span class="sxs-lookup"><span data-stu-id="d5120-154">Don't use production secrets in your development or test environments.</span></span> <span data-ttu-id="d5120-155">相反，指定外部项目树中，机密，以便它们不会意外地提交到你的存储库。</span><span class="sxs-lookup"><span data-stu-id="d5120-155">Instead, specify secrets outside the project tree, so they cannot be accidentally committed into your repository.</span></span> <span data-ttu-id="d5120-156">详细了解[使用多个环境](environments.md)和管理[安全存储在开发过程中的应用程序机密](../security/app-secrets.md)。</span><span class="sxs-lookup"><span data-stu-id="d5120-156">Learn more about [Working with Multiple Environments](environments.md) and managing [safe storage of app secrets during development](../security/app-secrets.md).</span></span>
* <span data-ttu-id="d5120-157">如果`:`不能为系统中所用环境变量中，替换`:`与`__`（双下划线）。</span><span class="sxs-lookup"><span data-stu-id="d5120-157">If `:` cannot be used in environment variables in your system,  replace `:`  with `__` (double underscore).</span></span>

<a name=options-config-objects></a>

## <a name="using-options-and-configuration-objects"></a><span data-ttu-id="d5120-158">使用选项和配置对象</span><span class="sxs-lookup"><span data-stu-id="d5120-158">Using Options and configuration objects</span></span>

<span data-ttu-id="d5120-159">选项模式使用自定义选项类来表示一组相关的设置。</span><span class="sxs-lookup"><span data-stu-id="d5120-159">The options pattern uses custom options classes to represent a group of related settings.</span></span> <span data-ttu-id="d5120-160">我们建议在你的应用程序中创建分离的类为每个功能。</span><span class="sxs-lookup"><span data-stu-id="d5120-160">We recommended that you create decoupled classes for each feature within your app.</span></span> <span data-ttu-id="d5120-161">分离的类遵循：</span><span class="sxs-lookup"><span data-stu-id="d5120-161">Decoupled classes follow:</span></span>

* <span data-ttu-id="d5120-162">[接口分隔原则 (ISP)](http://deviq.com/interface-segregation-principle/) ： 类仅取决于它们使用的配置设置。</span><span class="sxs-lookup"><span data-stu-id="d5120-162">The [Interface Segregation Principle (ISP)](http://deviq.com/interface-segregation-principle/) : Classes depend only on the configuration settings they use.</span></span>
* <span data-ttu-id="d5120-163">[关注点分离](http://deviq.com/separation-of-concerns/)： 你的应用程序的不同部分的设置不是依赖或与另一个耦合。</span><span class="sxs-lookup"><span data-stu-id="d5120-163">[Separation of Concerns](http://deviq.com/separation-of-concerns/) : Settings for different parts of your app are not dependent or coupled with one another.</span></span>

<span data-ttu-id="d5120-164">选项类必须为非抽象具有一个公共的无参数构造函数。</span><span class="sxs-lookup"><span data-stu-id="d5120-164">The options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="d5120-165">例如: </span><span class="sxs-lookup"><span data-stu-id="d5120-165">For example:</span></span>

<span data-ttu-id="d5120-166">[!code-csharp[Main](configuration/sample/src/UsingOptions/Models/MyOptions.cs)]</span><span class="sxs-lookup"><span data-stu-id="d5120-166">[!code-csharp[Main](configuration/sample/src/UsingOptions/Models/MyOptions.cs)]</span></span>

<a name=options-example></a>

<span data-ttu-id="d5120-167">在下面的代码中，启用 JSON 配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="d5120-167">In the following code, the JSON configuration provider is enabled.</span></span> <span data-ttu-id="d5120-168">`MyOptions`类是已添加到服务容器并绑定到配置。</span><span class="sxs-lookup"><span data-stu-id="d5120-168">The `MyOptions` class is added to the service container and bound to configuration.</span></span>

<span data-ttu-id="d5120-169">[!code-csharp[Main](configuration/sample/src/UsingOptions/Startup.cs?name=snippet1&highlight=8,20-22)]</span><span class="sxs-lookup"><span data-stu-id="d5120-169">[!code-csharp[Main](configuration/sample/src/UsingOptions/Startup.cs?name=snippet1&highlight=8,20-22)]</span></span>

<span data-ttu-id="d5120-170">以下[控制器](../mvc/controllers/index.md)使用[构造函数依赖关系注入](xref:fundamentals/dependency-injection#what-is-dependency-injection)上[ `IOptions<TOptions>` ](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.options.ioptions-1)访问设置：</span><span class="sxs-lookup"><span data-stu-id="d5120-170">The following [controller](../mvc/controllers/index.md)  uses [constructor Dependency Injection](xref:fundamentals/dependency-injection#what-is-dependency-injection) on [`IOptions<TOptions>`](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.options.ioptions-1) to access settings:</span></span>

<span data-ttu-id="d5120-171">[!code-csharp[Main](configuration/sample/src/UsingOptions/Controllers/HomeController.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="d5120-171">[!code-csharp[Main](configuration/sample/src/UsingOptions/Controllers/HomeController.cs?name=snippet1)]</span></span>

<span data-ttu-id="d5120-172">替换为以下*appsettings.json*文件：</span><span class="sxs-lookup"><span data-stu-id="d5120-172">With the following *appsettings.json* file:</span></span>

<span data-ttu-id="d5120-173">[!code-json[Main](configuration/sample/src/UsingOptions/appsettings1.json)]</span><span class="sxs-lookup"><span data-stu-id="d5120-173">[!code-json[Main](configuration/sample/src/UsingOptions/appsettings1.json)]</span></span>

<span data-ttu-id="d5120-174">`HomeController.Index`方法返回`option1 = value1_from_json, option2 = 2`。</span><span class="sxs-lookup"><span data-stu-id="d5120-174">The `HomeController.Index` method returns `option1 = value1_from_json, option2 = 2`.</span></span>

<span data-ttu-id="d5120-175">典型的应用程序不会将整个配置绑定到单个选项文件。</span><span class="sxs-lookup"><span data-stu-id="d5120-175">Typical apps won't bind the entire configuration to a single options file.</span></span> <span data-ttu-id="d5120-176">稍后我将介绍如何使用`GetSection`将绑定到一个部分。</span><span class="sxs-lookup"><span data-stu-id="d5120-176">Later on I'll show how to use `GetSection` to bind to a section.</span></span>

<span data-ttu-id="d5120-177">在下面的代码中，第二个`IConfigureOptions<TOptions>`服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="d5120-177">In the following code, a second `IConfigureOptions<TOptions>` service is added to the service container.</span></span> <span data-ttu-id="d5120-178">它使用委托来配置的绑定`MyOptions`。</span><span class="sxs-lookup"><span data-stu-id="d5120-178">It uses a delegate to configure the binding with `MyOptions`.</span></span>

<span data-ttu-id="d5120-179">[!code-csharp[Main](configuration/sample/src/UsingOptions/Startup2.cs?name=snippet1&highlight=9-13)]</span><span class="sxs-lookup"><span data-stu-id="d5120-179">[!code-csharp[Main](configuration/sample/src/UsingOptions/Startup2.cs?name=snippet1&highlight=9-13)]</span></span>

<span data-ttu-id="d5120-180">你可以添加多个配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="d5120-180">You can add multiple configuration providers.</span></span> <span data-ttu-id="d5120-181">配置提供程序 NuGet 包中。</span><span class="sxs-lookup"><span data-stu-id="d5120-181">Configuration providers are available in NuGet packages.</span></span> <span data-ttu-id="d5120-182">它们会按它们已注册的顺序应用。</span><span class="sxs-lookup"><span data-stu-id="d5120-182">They are applied in order they are registered.</span></span>

<span data-ttu-id="d5120-183">每次调用`Configure<TOptions>`添加`IConfigureOptions<TOptions>`服务到服务容器。</span><span class="sxs-lookup"><span data-stu-id="d5120-183">Each call to `Configure<TOptions>` adds an `IConfigureOptions<TOptions>` service to the service container.</span></span> <span data-ttu-id="d5120-184">在前面的示例中，值`Option1`和`Option2`同时中指定*appsettings.json* -但的值`Option1`配置委托来重写。</span><span class="sxs-lookup"><span data-stu-id="d5120-184">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json* -- but the value of `Option1` is overridden by the configured delegate.</span></span> 

<span data-ttu-id="d5120-185">启用多个配置服务后，最后一个配置源指定"wins"（设置配置值）。</span><span class="sxs-lookup"><span data-stu-id="d5120-185">When more than one configuration service is enabled, the last configuration source specified "wins" (sets the configuration value).</span></span> <span data-ttu-id="d5120-186">在前面的代码中，`HomeController.Index`方法返回`option1 = value1_from_action, option2 = 2`。</span><span class="sxs-lookup"><span data-stu-id="d5120-186">In the preceding code, the `HomeController.Index` method returns `option1 = value1_from_action, option2 = 2`.</span></span>

<span data-ttu-id="d5120-187">绑定到配置的选项时, 选项类型中的每个属性绑定到窗体的配置键`property[:sub-property:]`。</span><span class="sxs-lookup"><span data-stu-id="d5120-187">When you bind options to configuration, each property in your options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="d5120-188">例如，`MyOptions.Option1`属性绑定到键`Option1`，这从读取`option1`中的属性*appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="d5120-188">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span> <span data-ttu-id="d5120-189">在本文后面显示了子属性示例。</span><span class="sxs-lookup"><span data-stu-id="d5120-189">A sub-property sample is shown later in this article.</span></span>

<span data-ttu-id="d5120-190">在下面的代码中，第三个`IConfigureOptions<TOptions>`服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="d5120-190">In the following code, a third `IConfigureOptions<TOptions>` service is added to the service container.</span></span> <span data-ttu-id="d5120-191">它将绑定`MySubOptions`的部分`subsection`的*appsettings.json*文件：</span><span class="sxs-lookup"><span data-stu-id="d5120-191">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

<span data-ttu-id="d5120-192">[!code-csharp[Main](configuration/sample/src/UsingOptions/Startup3.cs?name=snippet1&highlight=16-17)]</span><span class="sxs-lookup"><span data-stu-id="d5120-192">[!code-csharp[Main](configuration/sample/src/UsingOptions/Startup3.cs?name=snippet1&highlight=16-17)]</span></span>

<span data-ttu-id="d5120-193">注意： 此扩展方法要求`Microsoft.Extensions.Options.ConfigurationExtensions`NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="d5120-193">Note: This extension method requires the `Microsoft.Extensions.Options.ConfigurationExtensions` NuGet package.</span></span>

<span data-ttu-id="d5120-194">使用以下*appsettings.json*文件：</span><span class="sxs-lookup"><span data-stu-id="d5120-194">Using the following *appsettings.json* file:</span></span>

<span data-ttu-id="d5120-195">[!code-json[Main](configuration/sample/src/UsingOptions/appsettings.json)]</span><span class="sxs-lookup"><span data-stu-id="d5120-195">[!code-json[Main](configuration/sample/src/UsingOptions/appsettings.json)]</span></span>

<span data-ttu-id="d5120-196">`MySubOptions`类：</span><span class="sxs-lookup"><span data-stu-id="d5120-196">The `MySubOptions` class:</span></span>

<span data-ttu-id="d5120-197">[!code-csharp[Main](configuration/sample/src/UsingOptions/Models/MySubOptions.cs)]</span><span class="sxs-lookup"><span data-stu-id="d5120-197">[!code-csharp[Main](configuration/sample/src/UsingOptions/Models/MySubOptions.cs)]</span></span>

<span data-ttu-id="d5120-198">替换为以下`Controller`:</span><span class="sxs-lookup"><span data-stu-id="d5120-198">With the following `Controller`:</span></span>

<span data-ttu-id="d5120-199">[!code-csharp[Main](configuration/sample/src/UsingOptions/Controllers/HomeController2.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="d5120-199">[!code-csharp[Main](configuration/sample/src/UsingOptions/Controllers/HomeController2.cs?name=snippet1)]</span></span>

<span data-ttu-id="d5120-200">`subOption1 = subvalue1_from_json, subOption2 = 200`返回。</span><span class="sxs-lookup"><span data-stu-id="d5120-200">`subOption1 = subvalue1_from_json, subOption2 = 200` is returned.</span></span>

<a name=in-memory-provider></a>

## <a name="ioptionssnapshot"></a><span data-ttu-id="d5120-201">IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="d5120-201">IOptionsSnapshot</span></span>

<span data-ttu-id="d5120-202">*需要 ASP.NET Core 1.1 或更高版本。*</span><span class="sxs-lookup"><span data-stu-id="d5120-202">*Requires ASP.NET Core 1.1 or higher.*</span></span>

<span data-ttu-id="d5120-203">`IOptionsSnapshot`当配置文件发生更改时重新加载配置数据的支持。</span><span class="sxs-lookup"><span data-stu-id="d5120-203">`IOptionsSnapshot` supports reloading configuration data when the configuration file has changed.</span></span> <span data-ttu-id="d5120-204">它具有最小的开销。</span><span class="sxs-lookup"><span data-stu-id="d5120-204">It has minimal overhead.</span></span> <span data-ttu-id="d5120-205">使用`IOptionsSnapshot`与`reloadOnChange: true`，选项绑定到`IConfiguration`并更改时重新加载。</span><span class="sxs-lookup"><span data-stu-id="d5120-205">Using `IOptionsSnapshot` with `reloadOnChange: true`, the options are bound to `IConfiguration` and reloaded when changed.</span></span>

<span data-ttu-id="d5120-206">下面的示例演示如何新`IOptionsSnapshot`后创建*config.json*更改。</span><span class="sxs-lookup"><span data-stu-id="d5120-206">The following sample demonstrates how a new `IOptionsSnapshot` is created after *config.json* changes.</span></span> <span data-ttu-id="d5120-207">对服务器请求将返回相同时间*config.json*具有**不**更改。</span><span class="sxs-lookup"><span data-stu-id="d5120-207">Requests to the server will return the same time when *config.json* has **not** changed.</span></span> <span data-ttu-id="d5120-208">之后的第一个请求*config.json*做的更改将显示新的时间。</span><span class="sxs-lookup"><span data-stu-id="d5120-208">The first request after *config.json* changes will show a new time.</span></span>

<span data-ttu-id="d5120-209">[!code-csharp[Main](configuration/sample/IOptionsSnapshot2/Startup.cs?name=snippet1&highlight=1-9,13-18,32,33,52,53)]</span><span class="sxs-lookup"><span data-stu-id="d5120-209">[!code-csharp[Main](configuration/sample/IOptionsSnapshot2/Startup.cs?name=snippet1&highlight=1-9,13-18,32,33,52,53)]</span></span>

<span data-ttu-id="d5120-210">下图显示服务器输出：</span><span class="sxs-lookup"><span data-stu-id="d5120-210">The following image shows the server output:</span></span>

![浏览器图像显示"上次更新时间： 2016 年 11 月 22 日下午 4:43"](configuration/_static/first.png)

<span data-ttu-id="d5120-212">刷新浏览器不会更改消息值或显示的时间 (时*config.json*仍是如此)。</span><span class="sxs-lookup"><span data-stu-id="d5120-212">Refreshing the browser doesn't change the message value or time displayed (when *config.json* has not changed).</span></span>

<span data-ttu-id="d5120-213">进行更改并保存*config.json*然后刷新浏览器：</span><span class="sxs-lookup"><span data-stu-id="d5120-213">Change and save the  *config.json* and then refresh the browser:</span></span>

![浏览器图像显示"上次更新到 e: 2016 年 11 月 22 日下午 4:53"](configuration/_static/change.png)

## <a name="in-memory-provider-and-binding-to-a-poco-class"></a><span data-ttu-id="d5120-215">内存中提供程序并绑定到 POCO 类</span><span class="sxs-lookup"><span data-stu-id="d5120-215">In-memory provider and binding to a POCO class</span></span>

<span data-ttu-id="d5120-216">下面的示例演示如何使用内存中提供程序并将绑定到类：</span><span class="sxs-lookup"><span data-stu-id="d5120-216">The following sample shows how to use the in-memory provider and bind to a class:</span></span>

<span data-ttu-id="d5120-217">[!code-csharp[Main](configuration/sample/src/InMemory/Program.cs)]</span><span class="sxs-lookup"><span data-stu-id="d5120-217">[!code-csharp[Main](configuration/sample/src/InMemory/Program.cs)]</span></span>

<span data-ttu-id="d5120-218">配置值将返回为字符串，但绑定可使对象的构造。</span><span class="sxs-lookup"><span data-stu-id="d5120-218">Configuration values are returned as strings, but binding enables the construction of objects.</span></span> <span data-ttu-id="d5120-219">绑定允许您检索 POCO 对象或甚至是整个对象图。</span><span class="sxs-lookup"><span data-stu-id="d5120-219">Binding allows you to retrieve POCO objects or even entire object graphs.</span></span> <span data-ttu-id="d5120-220">下面的示例演示如何将绑定到`MyWindow`，并且与 ASP.NET 核心 MVC 应用程序使用选项模式：</span><span class="sxs-lookup"><span data-stu-id="d5120-220">The following sample shows how to bind to `MyWindow` and use the options pattern with a ASP.NET Core MVC app:</span></span>

<span data-ttu-id="d5120-221">[!code-csharp[Main](configuration/sample/src/WebConfigBind/MyWindow.cs)]</span><span class="sxs-lookup"><span data-stu-id="d5120-221">[!code-csharp[Main](configuration/sample/src/WebConfigBind/MyWindow.cs)]</span></span>

<span data-ttu-id="d5120-222">[!code-json[Main](configuration/sample/src/WebConfigBind/appsettings.json)]</span><span class="sxs-lookup"><span data-stu-id="d5120-222">[!code-json[Main](configuration/sample/src/WebConfigBind/appsettings.json)]</span></span>

<span data-ttu-id="d5120-223">绑定中的自定义类`ConfigureServices`中`Startup`类：</span><span class="sxs-lookup"><span data-stu-id="d5120-223">Bind the custom class in `ConfigureServices` in the `Startup` class:</span></span>

<span data-ttu-id="d5120-224">[!code-csharp[Main](configuration/sample/src/WebConfigBind/Startup.cs?name=snippet1&highlight=3,4)]</span><span class="sxs-lookup"><span data-stu-id="d5120-224">[!code-csharp[Main](configuration/sample/src/WebConfigBind/Startup.cs?name=snippet1&highlight=3,4)]</span></span>

<span data-ttu-id="d5120-225">显示从设置`HomeController`:</span><span class="sxs-lookup"><span data-stu-id="d5120-225">Display the settings from the `HomeController`:</span></span>

<span data-ttu-id="d5120-226">[!code-csharp[Main](configuration/sample/src/WebConfigBind/Controllers/HomeController.cs)]</span><span class="sxs-lookup"><span data-stu-id="d5120-226">[!code-csharp[Main](configuration/sample/src/WebConfigBind/Controllers/HomeController.cs)]</span></span>

### <a name="getvalue"></a><span data-ttu-id="d5120-227">GetValue</span><span class="sxs-lookup"><span data-stu-id="d5120-227">GetValue</span></span>

<span data-ttu-id="d5120-228">下面的示例演示如何[GetValue<T> ](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationbinder#Microsoft_Extensions_Configuration_ConfigurationBinder_GetValue_Microsoft_Extensions_Configuration_IConfiguration_System_Type_System_String_System_Object_)扩展方法：</span><span class="sxs-lookup"><span data-stu-id="d5120-228">The following sample demonstrates the [GetValue<T>](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationbinder#Microsoft_Extensions_Configuration_ConfigurationBinder_GetValue_Microsoft_Extensions_Configuration_IConfiguration_System_Type_System_String_System_Object_) extension method:</span></span>

<span data-ttu-id="d5120-229">[!code-csharp[Main](configuration/sample/src/InMemoryGetValue/Program.cs?highlight=27-29)]</span><span class="sxs-lookup"><span data-stu-id="d5120-229">[!code-csharp[Main](configuration/sample/src/InMemoryGetValue/Program.cs?highlight=27-29)]</span></span>

<span data-ttu-id="d5120-230">ConfigurationBinder`GetValue<T>`方法允许你指定默认值 (在此示例中为 80)。</span><span class="sxs-lookup"><span data-stu-id="d5120-230">The ConfigurationBinder's `GetValue<T>` method allows you to specify a default value (80 in the sample).</span></span> <span data-ttu-id="d5120-231">`GetValue<T>`用于简单的方案，不会绑定到整个部分。</span><span class="sxs-lookup"><span data-stu-id="d5120-231">`GetValue<T>` is for simple scenarios and does not bind to entire sections.</span></span> <span data-ttu-id="d5120-232">`GetValue<T>`获取标量值从`GetSection(key).Value`转换为特定类型。</span><span class="sxs-lookup"><span data-stu-id="d5120-232">`GetValue<T>` gets scalar values from `GetSection(key).Value` converted to a specific type.</span></span>

## <a name="binding-to-an-object-graph"></a><span data-ttu-id="d5120-233">绑定到对象图</span><span class="sxs-lookup"><span data-stu-id="d5120-233">Binding to an object graph</span></span>

<span data-ttu-id="d5120-234">你可以以递归方式绑定到类中每个对象。</span><span class="sxs-lookup"><span data-stu-id="d5120-234">You can recursively bind to each object in a class.</span></span> <span data-ttu-id="d5120-235">请考虑以下`AppOptions`类：</span><span class="sxs-lookup"><span data-stu-id="d5120-235">Consider the following `AppOptions` class:</span></span>

<span data-ttu-id="d5120-236">[!code-csharp[Main](configuration/sample/src/ObjectGraph/AppOptions.cs)]</span><span class="sxs-lookup"><span data-stu-id="d5120-236">[!code-csharp[Main](configuration/sample/src/ObjectGraph/AppOptions.cs)]</span></span>

<span data-ttu-id="d5120-237">下面的示例将绑定到`AppOptions`类：</span><span class="sxs-lookup"><span data-stu-id="d5120-237">The following sample binds to the `AppOptions` class:</span></span>

<span data-ttu-id="d5120-238">[!code-csharp[Main](configuration/sample/src/ObjectGraph/Program.cs?highlight=15-16)]</span><span class="sxs-lookup"><span data-stu-id="d5120-238">[!code-csharp[Main](configuration/sample/src/ObjectGraph/Program.cs?highlight=15-16)]</span></span>

<span data-ttu-id="d5120-239">**ASP.NET 核心 1.1**和更高版本可以使用`Get<T>`，这适用于整个部分。</span><span class="sxs-lookup"><span data-stu-id="d5120-239">**ASP.NET Core 1.1** and higher can use  `Get<T>`, which works with entire sections.</span></span> <span data-ttu-id="d5120-240">`Get<T>`可以是比使用多个 convienent `Bind`。</span><span class="sxs-lookup"><span data-stu-id="d5120-240">`Get<T>` can be more convienent than using `Bind`.</span></span> <span data-ttu-id="d5120-241">下面的代码演示如何使用`Get<T>`与上面的示例：</span><span class="sxs-lookup"><span data-stu-id="d5120-241">The following code shows how to use `Get<T>` with the sample above:</span></span>

```csharp
var appConfig = config.GetSection("App").Get<AppOptions>();
```

<span data-ttu-id="d5120-242">使用以下*appsettings.json*文件：</span><span class="sxs-lookup"><span data-stu-id="d5120-242">Using the following *appsettings.json* file:</span></span>

<span data-ttu-id="d5120-243">[!code-json[Main](configuration/sample/src/ObjectGraph/appsettings.json)]</span><span class="sxs-lookup"><span data-stu-id="d5120-243">[!code-json[Main](configuration/sample/src/ObjectGraph/appsettings.json)]</span></span>

<span data-ttu-id="d5120-244">该程序显示`Height 11`。</span><span class="sxs-lookup"><span data-stu-id="d5120-244">The program displays `Height 11`.</span></span>

<span data-ttu-id="d5120-245">下面的代码可以用于单元测试配置：</span><span class="sxs-lookup"><span data-stu-id="d5120-245">The following code can be used to unit test the configuration:</span></span>

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

## <a name="basic-sample-of-entity-framework-custom-provider"></a><span data-ttu-id="d5120-246">实体框架自定义提供程序的基本示例</span><span class="sxs-lookup"><span data-stu-id="d5120-246">Basic sample of Entity Framework custom provider</span></span>

<span data-ttu-id="d5120-247">在本部分中，创建的基本配置提供程序从使用 EF 数据库中读取名称-值对。</span><span class="sxs-lookup"><span data-stu-id="d5120-247">In this section, a basic configuration provider that reads name-value pairs from a database using EF is created.</span></span> 

<span data-ttu-id="d5120-248">定义`ConfigurationValue`在数据库中存储配置值的实体：</span><span class="sxs-lookup"><span data-stu-id="d5120-248">Define a `ConfigurationValue` entity for storing configuration values in the database:</span></span>

<span data-ttu-id="d5120-249">[!code-csharp[Main](configuration/sample/src/CustomConfigurationProvider/ConfigurationValue.cs)]</span><span class="sxs-lookup"><span data-stu-id="d5120-249">[!code-csharp[Main](configuration/sample/src/CustomConfigurationProvider/ConfigurationValue.cs)]</span></span>

<span data-ttu-id="d5120-250">添加`ConfigurationContext`存储和访问配置的值：</span><span class="sxs-lookup"><span data-stu-id="d5120-250">Add a `ConfigurationContext` to store and access the configured values:</span></span>

<span data-ttu-id="d5120-251">[!code-csharp[Main](configuration/sample/src/CustomConfigurationProvider/ConfigurationContext.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="d5120-251">[!code-csharp[Main](configuration/sample/src/CustomConfigurationProvider/ConfigurationContext.cs?name=snippet1)]</span></span>

<span data-ttu-id="d5120-252">创建一个类，实现[IConfigurationSource](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.iconfigurationsource):</span><span class="sxs-lookup"><span data-stu-id="d5120-252">Create an class that implements [IConfigurationSource](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.iconfigurationsource):</span></span>

<span data-ttu-id="d5120-253">[!code-csharp[Main](configuration/sample/src/CustomConfigurationProvider/EntityFrameworkConfigurationSource.cs?highlight=7)]</span><span class="sxs-lookup"><span data-stu-id="d5120-253">[!code-csharp[Main](configuration/sample/src/CustomConfigurationProvider/EntityFrameworkConfigurationSource.cs?highlight=7)]</span></span>

<span data-ttu-id="d5120-254">创建自定义配置提供程序通过继承[ConfigurationProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationprovider)。</span><span class="sxs-lookup"><span data-stu-id="d5120-254">Create the custom configuration provider by inheriting from [ConfigurationProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationprovider).</span></span>  <span data-ttu-id="d5120-255">为空时，配置提供程序初始化数据库：</span><span class="sxs-lookup"><span data-stu-id="d5120-255">The configuration provider initializes the database when it's empty:</span></span>

<span data-ttu-id="d5120-256">[!code-csharp[Main](configuration/sample/src/CustomConfigurationProvider/EntityFrameworkConfigurationProvider.cs?highlight=9,18-31,38-39)]</span><span class="sxs-lookup"><span data-stu-id="d5120-256">[!code-csharp[Main](configuration/sample/src/CustomConfigurationProvider/EntityFrameworkConfigurationProvider.cs?highlight=9,18-31,38-39)]</span></span>

<span data-ttu-id="d5120-257">运行示例时，会显示数据库 （"value_from_ef_1"和"value_from_ef_2"） 中突出显示的值。</span><span class="sxs-lookup"><span data-stu-id="d5120-257">The highlighted values from the database ("value_from_ef_1" and "value_from_ef_2") are displayed when the sample is run.</span></span>

<span data-ttu-id="d5120-258">你可以添加`EFConfigSource`将该配置源添加的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="d5120-258">You can add an `EFConfigSource` extension method for adding the configuration source:</span></span>

<span data-ttu-id="d5120-259">[!code-csharp[Main](configuration/sample/src/CustomConfigurationProvider/EntityFrameworkExtensions.cs?highlight=12)]</span><span class="sxs-lookup"><span data-stu-id="d5120-259">[!code-csharp[Main](configuration/sample/src/CustomConfigurationProvider/EntityFrameworkExtensions.cs?highlight=12)]</span></span>

<span data-ttu-id="d5120-260">下面的代码演示如何使用自定义`EFConfigProvider`:</span><span class="sxs-lookup"><span data-stu-id="d5120-260">The following code shows how to use the custom `EFConfigProvider`:</span></span>

<span data-ttu-id="d5120-261">[!code-csharp[Main](configuration/sample/src/CustomConfigurationProvider/Program.cs?highlight=20-25)]</span><span class="sxs-lookup"><span data-stu-id="d5120-261">[!code-csharp[Main](configuration/sample/src/CustomConfigurationProvider/Program.cs?highlight=20-25)]</span></span>

<span data-ttu-id="d5120-262">请注意此示例将添加自定义`EFConfigProvider`后 JSON 提供程序，因此数据库中的任何设置将替代设置从*appsettings.json*文件。</span><span class="sxs-lookup"><span data-stu-id="d5120-262">Note the sample adds the custom `EFConfigProvider` after the JSON provider, so any settings from the database will override settings from the *appsettings.json* file.</span></span>

<span data-ttu-id="d5120-263">使用以下*appsettings.json*文件：</span><span class="sxs-lookup"><span data-stu-id="d5120-263">Using the following *appsettings.json* file:</span></span>

<span data-ttu-id="d5120-264">[!code-json[Main](configuration/sample/src/CustomConfigurationProvider/appsettings.json)]</span><span class="sxs-lookup"><span data-stu-id="d5120-264">[!code-json[Main](configuration/sample/src/CustomConfigurationProvider/appsettings.json)]</span></span>

<span data-ttu-id="d5120-265">显示以下消息：</span><span class="sxs-lookup"><span data-stu-id="d5120-265">The following is displayed:</span></span>

```console
key1=value_from_ef_1
key2=value_from_ef_2
key3=value_from_json_3
```

## <a name="commandline-configuration-provider"></a><span data-ttu-id="d5120-266">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d5120-266">CommandLine configuration provider</span></span>

<span data-ttu-id="d5120-267">下面的示例使最后一个命令行配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="d5120-267">The following sample enables the CommandLine configuration provider last:</span></span>

<span data-ttu-id="d5120-268">[!code-csharp[Main](configuration/sample/src/CommandLine/Program.cs)]</span><span class="sxs-lookup"><span data-stu-id="d5120-268">[!code-csharp[Main](configuration/sample/src/CommandLine/Program.cs)]</span></span>

<span data-ttu-id="d5120-269">使用以下方法来配置设置中传递：</span><span class="sxs-lookup"><span data-stu-id="d5120-269">Use the following to pass in configuration settings:</span></span>

```console
dotnet run /Profile:MachineName=Bob /App:MainWindow:Left=1234
```

<span data-ttu-id="d5120-270">这将显示：</span><span class="sxs-lookup"><span data-stu-id="d5120-270">Which displays:</span></span>

```console
Hello Bob
Left 1234
```

<span data-ttu-id="d5120-271">`GetSwitchMappings`方法允许你使用`-`而非`/`和它中抽出前导子项前缀。</span><span class="sxs-lookup"><span data-stu-id="d5120-271">The `GetSwitchMappings` method allows you to use `-` rather than `/` and it strips the leading subkey prefixes.</span></span> <span data-ttu-id="d5120-272">例如: </span><span class="sxs-lookup"><span data-stu-id="d5120-272">For example:</span></span>

```console
dotnet run -MachineName=Bob -Left=7734
```

<span data-ttu-id="d5120-273">显示：</span><span class="sxs-lookup"><span data-stu-id="d5120-273">Displays:</span></span>

```console
Hello Bob
Left 7734
```

<span data-ttu-id="d5120-274">命令行自变量必须包含 （它可以为 null） 的值。</span><span class="sxs-lookup"><span data-stu-id="d5120-274">Command-line arguments must include a value (it can be null).</span></span> <span data-ttu-id="d5120-275">例如: </span><span class="sxs-lookup"><span data-stu-id="d5120-275">For example:</span></span>

```console
dotnet run /Profile:MachineName=
```

<span data-ttu-id="d5120-276">是可以的但</span><span class="sxs-lookup"><span data-stu-id="d5120-276">Is OK, but</span></span>

```console
dotnet run /Profile:MachineName
```

<span data-ttu-id="d5120-277">引发异常的结果。</span><span class="sxs-lookup"><span data-stu-id="d5120-277">results in an exception.</span></span> <span data-ttu-id="d5120-278">如果你指定为其中没有相应的交换机映射的-或--命令行开关前缀，则将引发异常。</span><span class="sxs-lookup"><span data-stu-id="d5120-278">An exception will be thrown if you specify a command-line switch prefix of - or -- for which there's no corresponding switch mapping.</span></span>

## <a name="the-webconfig-file"></a><span data-ttu-id="d5120-279">Web.config 文件</span><span class="sxs-lookup"><span data-stu-id="d5120-279">The web.config file</span></span>

<span data-ttu-id="d5120-280">A *web.config*文件是必需的当你在 IIS 或 IIS Express 应用程序承载。</span><span class="sxs-lookup"><span data-stu-id="d5120-280">A *web.config* file is required when you host the app in IIS or IIS-Express.</span></span> <span data-ttu-id="d5120-281">*web.config*开启 AspNetCoreModule IIS 启动你的应用中。</span><span class="sxs-lookup"><span data-stu-id="d5120-281">*web.config* turns on the AspNetCoreModule in IIS to launch your app.</span></span> <span data-ttu-id="d5120-282">中的设置*web.config*启用 IIS 来启动你的应用和配置其他 IIS 设置和模块中 AspNetCoreModule。</span><span class="sxs-lookup"><span data-stu-id="d5120-282">Settings in *web.config* enable the AspNetCoreModule in IIS to launch your app and configure other IIS settings and modules.</span></span> <span data-ttu-id="d5120-283">如果你使用的 Visual Studio 将删除*web.config*，Visual Studio 将创建一个新。</span><span class="sxs-lookup"><span data-stu-id="d5120-283">If you are using Visual Studio and delete *web.config*, Visual Studio will create a new one.</span></span>

### <a name="additional-notes"></a><span data-ttu-id="d5120-284">附加说明</span><span class="sxs-lookup"><span data-stu-id="d5120-284">Additional notes</span></span>

* <span data-ttu-id="d5120-285">依赖关系注入 (DI) 未设置截止日期之后`ConfigureServices`调用。</span><span class="sxs-lookup"><span data-stu-id="d5120-285">Dependency Injection (DI) is not set up until after `ConfigureServices` is invoked.</span></span>
* <span data-ttu-id="d5120-286">配置系统不是 DI 感知。</span><span class="sxs-lookup"><span data-stu-id="d5120-286">The configuration system is not DI aware.</span></span>
* <span data-ttu-id="d5120-287">`IConfiguration`具有两个专用化：</span><span class="sxs-lookup"><span data-stu-id="d5120-287">`IConfiguration` has two specializations:</span></span>
  * <span data-ttu-id="d5120-288">`IConfigurationRoot`用于根节点。</span><span class="sxs-lookup"><span data-stu-id="d5120-288">`IConfigurationRoot`  Used for the root node.</span></span> <span data-ttu-id="d5120-289">可以触发重新加载。</span><span class="sxs-lookup"><span data-stu-id="d5120-289">Can trigger a reload.</span></span>
  * <span data-ttu-id="d5120-290">`IConfigurationSection`表示一个节的配置值。</span><span class="sxs-lookup"><span data-stu-id="d5120-290">`IConfigurationSection`  Represents a section of configuration values.</span></span> <span data-ttu-id="d5120-291">`GetSection`和`GetChildren`方法返回`IConfigurationSection`。</span><span class="sxs-lookup"><span data-stu-id="d5120-291">The `GetSection` and `GetChildren` methods return an `IConfigurationSection`.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="d5120-292">其他资源</span><span class="sxs-lookup"><span data-stu-id="d5120-292">Additional resources</span></span>

* [<span data-ttu-id="d5120-293">使用多个环境</span><span class="sxs-lookup"><span data-stu-id="d5120-293">Working with Multiple Environments</span></span>](environments.md)
* [<span data-ttu-id="d5120-294">安全存储在开发过程中的应用程序机密</span><span class="sxs-lookup"><span data-stu-id="d5120-294">Safe storage of app secrets during development</span></span>](../security/app-secrets.md)
* [<span data-ttu-id="d5120-295">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="d5120-295">Dependency Injection</span></span>](dependency-injection.md)
* [<span data-ttu-id="d5120-296">Azure 密钥保管库配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d5120-296">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration)
