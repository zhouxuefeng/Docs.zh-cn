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
ms.openlocfilehash: 39e76b14af85de34b8443bf4e04d18d13ad2aa90
ms.sourcegitcommit: fb518f856f31fe53c09196a13309eacb85b37a22
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2017
---
<a name=fundamentals-configuration></a>

  # <a name="configuration-in-aspnet-core"></a><span data-ttu-id="9c16c-104">ASP.NET 核心中配置</span><span class="sxs-lookup"><span data-stu-id="9c16c-104">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="9c16c-105">[Rick Anderson](https://twitter.com/RickAndMSFT)，[标记 Michaelis](http://intellitect.com/author/mark-michaelis/)， [Steve Smith](http://ardalis.com)，和[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="9c16c-105">[Rick Anderson](https://twitter.com/RickAndMSFT), [Mark Michaelis](http://intellitect.com/author/mark-michaelis/), [Steve Smith](http://ardalis.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="9c16c-106">配置 API 提供一种方法配置应用程序中基于名称-值对的列表。</span><span class="sxs-lookup"><span data-stu-id="9c16c-106">The Configuration API provides a way of configuring an app based on a list of name-value pairs.</span></span> <span data-ttu-id="9c16c-107">在运行时从多个源读取配置。</span><span class="sxs-lookup"><span data-stu-id="9c16c-107">Configuration is read at runtime from multiple sources.</span></span> <span data-ttu-id="9c16c-108">名称-值对可以分组到多级的层次结构。</span><span class="sxs-lookup"><span data-stu-id="9c16c-108">The name-value pairs can be grouped into a multi-level hierarchy.</span></span> <span data-ttu-id="9c16c-109">有配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="9c16c-109">There are configuration providers for:</span></span>

* <span data-ttu-id="9c16c-110">文件格式 （INI、 JSON 和 XML）</span><span class="sxs-lookup"><span data-stu-id="9c16c-110">File formats (INI, JSON, and XML)</span></span>
* <span data-ttu-id="9c16c-111">命令行自变量</span><span class="sxs-lookup"><span data-stu-id="9c16c-111">Command-line arguments</span></span>
* <span data-ttu-id="9c16c-112">环境变量</span><span class="sxs-lookup"><span data-stu-id="9c16c-112">Environment variables</span></span>
* <span data-ttu-id="9c16c-113">内存中的.NET 对象</span><span class="sxs-lookup"><span data-stu-id="9c16c-113">In-memory .NET objects</span></span>
* <span data-ttu-id="9c16c-114">一个加密的用户存储区</span><span class="sxs-lookup"><span data-stu-id="9c16c-114">An encrypted user store</span></span>
* [<span data-ttu-id="9c16c-115">Azure 密钥保管库</span><span class="sxs-lookup"><span data-stu-id="9c16c-115">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
* <span data-ttu-id="9c16c-116">自定义提供程序，你安装或在创建</span><span class="sxs-lookup"><span data-stu-id="9c16c-116">Custom providers, which you install or create</span></span>

<span data-ttu-id="9c16c-117">每个配置值将映射到字符串键。</span><span class="sxs-lookup"><span data-stu-id="9c16c-117">Each configuration value maps to a string key.</span></span> <span data-ttu-id="9c16c-118">若要反序列化到自定义设置的内置绑定支持[POCO](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)对象 （具有属性的简单.NET 类）。</span><span class="sxs-lookup"><span data-stu-id="9c16c-118">There's built-in binding support to deserialize settings into a custom [POCO](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object) object (a simple .NET class with properties).</span></span>

[<span data-ttu-id="9c16c-119">查看或下载示例代码</span><span class="sxs-lookup"><span data-stu-id="9c16c-119">View or download sample code</span></span>](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/sample)

## <a name="simple-configuration"></a><span data-ttu-id="9c16c-120">简单的配置</span><span class="sxs-lookup"><span data-stu-id="9c16c-120">Simple configuration</span></span>

<span data-ttu-id="9c16c-121">以下控制台应用程序使用 JSON 配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="9c16c-121">The following console app uses the JSON configuration provider:</span></span>

<span data-ttu-id="9c16c-122">[!code-csharp[Main](configuration/sample/ConfigJson/Program.cs)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-122">[!code-csharp[Main](configuration/sample/ConfigJson/Program.cs)]</span></span>

<span data-ttu-id="9c16c-123">应用程序读取，并显示以下配置设置：</span><span class="sxs-lookup"><span data-stu-id="9c16c-123">The app reads and displays the following configuration settings:</span></span>

<span data-ttu-id="9c16c-124">[!code-json[Main](configuration/sample/ConfigJson/appsettings.json)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-124">[!code-json[Main](configuration/sample/ConfigJson/appsettings.json)]</span></span>

<span data-ttu-id="9c16c-125">配置由在其中节点以冒号分隔的名称-值对的分层列表组成。</span><span class="sxs-lookup"><span data-stu-id="9c16c-125">Configuration consists of a hierarchical list of name-value pairs in which the nodes are separated by a colon.</span></span> <span data-ttu-id="9c16c-126">若要检索一个值，请访问`Configuration`与相应的项的键的索引器：</span><span class="sxs-lookup"><span data-stu-id="9c16c-126">To retrieve a value, access the `Configuration` indexer with the corresponding item's key:</span></span>

```csharp
Console.WriteLine($"option1 = {Configuration["subsection:suboption1"]}");
```

<span data-ttu-id="9c16c-127">若要使用 JSON 格式配置源中的数组，使用作为一部分的冒号分隔的字符串的数组索引。</span><span class="sxs-lookup"><span data-stu-id="9c16c-127">To work with arrays in JSON-formatted configuration sources, use a array index as part of the colon-separated string.</span></span> <span data-ttu-id="9c16c-128">下面的示例获取在前面的第一项的名称`wizards`数组：</span><span class="sxs-lookup"><span data-stu-id="9c16c-128">The following example gets the name of the first item in the preceding `wizards` array:</span></span>

```csharp
Console.Write($"{Configuration["wizards:0:Name"]}, ");
```

<span data-ttu-id="9c16c-129">写入到生成的名称-值对中`Configuration`提供程序是**不**保持不变，但是，你可以创建的自定义提供程序将保存值。</span><span class="sxs-lookup"><span data-stu-id="9c16c-129">Name-value pairs written to the built in `Configuration` providers are **not** persisted, however, you can create a custom provider that saves values.</span></span> <span data-ttu-id="9c16c-130">请参阅[自定义配置提供程序](xref:fundamentals/configuration#custom-config-providers)。</span><span class="sxs-lookup"><span data-stu-id="9c16c-130">See [custom configuration provider](xref:fundamentals/configuration#custom-config-providers).</span></span>

<span data-ttu-id="9c16c-131">上面的示例使用配置索引器可以读取值。</span><span class="sxs-lookup"><span data-stu-id="9c16c-131">The preceding sample uses the configuration indexer to read values.</span></span> <span data-ttu-id="9c16c-132">外部访问配置`Startup`，使用[选项模式](xref:fundamentals/configuration#options-config-objects)。</span><span class="sxs-lookup"><span data-stu-id="9c16c-132">To access configuration outside of `Startup`, use the [options pattern](xref:fundamentals/configuration#options-config-objects).</span></span> <span data-ttu-id="9c16c-133">*选项模式*本文后面所示。</span><span class="sxs-lookup"><span data-stu-id="9c16c-133">The *options pattern* is shown later in this article.</span></span>

<span data-ttu-id="9c16c-134">它是通常具有不同的配置设置的不同的环境，例如，开发、 测试和生产。</span><span class="sxs-lookup"><span data-stu-id="9c16c-134">It's typical to have different configuration settings for different environments, for example, development, test, and production.</span></span> <span data-ttu-id="9c16c-135">`CreateDefaultBuilder` ASP.NET Core 2.x 应用程序中的扩展方法 (或使用`AddJsonFile`和`AddEnvironmentVariables`直接在 ASP.NET Core 1.x 应用程序) 将配置提供程序添加用于读取 JSON 文件和系统配置源：</span><span class="sxs-lookup"><span data-stu-id="9c16c-135">The `CreateDefaultBuilder` extension method in an ASP.NET Core 2.x app (or using `AddJsonFile` and `AddEnvironmentVariables` directly in an ASP.NET Core 1.x app) adds configuration providers for reading JSON files and system configuration sources:</span></span>

* <span data-ttu-id="9c16c-136">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="9c16c-136">*appsettings.json*</span></span>
* <span data-ttu-id="9c16c-137">* appsettings。\<EnvironmentName >.json</span><span class="sxs-lookup"><span data-stu-id="9c16c-137">*appsettings.\<EnvironmentName>.json</span></span>
* <span data-ttu-id="9c16c-138">环境变量</span><span class="sxs-lookup"><span data-stu-id="9c16c-138">environment variables</span></span>

<span data-ttu-id="9c16c-139">请参阅[AddJsonFile](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.jsonconfigurationextensions)有关参数的说明。</span><span class="sxs-lookup"><span data-stu-id="9c16c-139">See [AddJsonFile](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.jsonconfigurationextensions) for an explanation of the parameters.</span></span> <span data-ttu-id="9c16c-140">`reloadOnChange`仅支持在 ASP.NET 核心 1.1 和更高版本。</span><span class="sxs-lookup"><span data-stu-id="9c16c-140">`reloadOnChange` is only supported in ASP.NET Core 1.1 and higher.</span></span> 

<span data-ttu-id="9c16c-141">配置源中指定它们的顺序读取。</span><span class="sxs-lookup"><span data-stu-id="9c16c-141">Configuration sources are read in the order they are specified.</span></span> <span data-ttu-id="9c16c-142">在上面的代码中，最后读取环境变量。</span><span class="sxs-lookup"><span data-stu-id="9c16c-142">In the code above, the environment variables are read last.</span></span> <span data-ttu-id="9c16c-143">任何通过环境设置的配置值会替换在两个以前的提供程序中设置。</span><span class="sxs-lookup"><span data-stu-id="9c16c-143">Any configuration values set through the environment would replace those set in the two previous providers.</span></span>

<span data-ttu-id="9c16c-144">环境通常设置为其中一个`Development`， `Staging`，或`Production`。</span><span class="sxs-lookup"><span data-stu-id="9c16c-144">The environment is typically set to one of `Development`, `Staging`, or `Production`.</span></span> <span data-ttu-id="9c16c-145">请参阅[使用多个环境](environments.md)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="9c16c-145">See [Working with Multiple Environments](environments.md) for more information.</span></span>

<span data-ttu-id="9c16c-146">配置注意事项：</span><span class="sxs-lookup"><span data-stu-id="9c16c-146">Configuration considerations:</span></span>

* <span data-ttu-id="9c16c-147">`IOptionsSnapshot`当更改时，可以重新加载配置数据。</span><span class="sxs-lookup"><span data-stu-id="9c16c-147">`IOptionsSnapshot` can reload configuration data when it changes.</span></span> <span data-ttu-id="9c16c-148">使用`IOptionsSnapshot`如果需要重新加载配置数据。</span><span class="sxs-lookup"><span data-stu-id="9c16c-148">Use `IOptionsSnapshot` if you need to reload configuration data.</span></span>  <span data-ttu-id="9c16c-149">请参阅[IOptionsSnapshot](#ioptionssnapshot)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="9c16c-149">See [IOptionsSnapshot](#ioptionssnapshot) for more information.</span></span>
* <span data-ttu-id="9c16c-150">配置密钥是区分大小写。</span><span class="sxs-lookup"><span data-stu-id="9c16c-150">Configuration keys are case insensitive.</span></span>
* <span data-ttu-id="9c16c-151">一种最佳做法是上一次，指定环境变量，使本地环境可以重写在已部署的配置文件中设置的任何内容。</span><span class="sxs-lookup"><span data-stu-id="9c16c-151">A best practice is to specify environment variables last, so that the local environment can override anything set in deployed configuration files.</span></span>
* <span data-ttu-id="9c16c-152">**永远不会**在配置提供程序代码或纯文本配置文件中存储密码或其他敏感数据。</span><span class="sxs-lookup"><span data-stu-id="9c16c-152">**Never** store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="9c16c-153">请不要在你的开发中使用生产机密或测试环境。</span><span class="sxs-lookup"><span data-stu-id="9c16c-153">Don't use production secrets in your development or test environments.</span></span> <span data-ttu-id="9c16c-154">相反，指定外部项目树中，机密，以便它们不会意外地提交到你的存储库。</span><span class="sxs-lookup"><span data-stu-id="9c16c-154">Instead, specify secrets outside the project tree, so they cannot be accidentally committed into your repository.</span></span> <span data-ttu-id="9c16c-155">详细了解[使用多个环境](environments.md)和管理[安全存储在开发过程中的应用程序机密](../security/app-secrets.md)。</span><span class="sxs-lookup"><span data-stu-id="9c16c-155">Learn more about [Working with Multiple Environments](environments.md) and managing [safe storage of app secrets during development](../security/app-secrets.md).</span></span>
* <span data-ttu-id="9c16c-156">如果`:`不能为系统中所用环境变量中，替换`:`与`__`（双下划线）。</span><span class="sxs-lookup"><span data-stu-id="9c16c-156">If `:` cannot be used in environment variables in your system,  replace `:`  with `__` (double underscore).</span></span>

<a name=options-config-objects></a>

## <a name="using-options-and-configuration-objects"></a><span data-ttu-id="9c16c-157">使用选项和配置对象</span><span class="sxs-lookup"><span data-stu-id="9c16c-157">Using Options and configuration objects</span></span>

<span data-ttu-id="9c16c-158">选项模式使用自定义选项类来表示一组相关的设置。</span><span class="sxs-lookup"><span data-stu-id="9c16c-158">The options pattern uses custom options classes to represent a group of related settings.</span></span> <span data-ttu-id="9c16c-159">我们建议在你的应用程序中创建分离的类为每个功能。</span><span class="sxs-lookup"><span data-stu-id="9c16c-159">We recommended that you create decoupled classes for each feature within your app.</span></span> <span data-ttu-id="9c16c-160">分离的类遵循：</span><span class="sxs-lookup"><span data-stu-id="9c16c-160">Decoupled classes follow:</span></span>

* <span data-ttu-id="9c16c-161">[接口分隔原则 (ISP)](http://deviq.com/interface-segregation-principle/) ： 类仅取决于它们使用的配置设置。</span><span class="sxs-lookup"><span data-stu-id="9c16c-161">The [Interface Segregation Principle (ISP)](http://deviq.com/interface-segregation-principle/) : Classes depend only on the configuration settings they use.</span></span>
* <span data-ttu-id="9c16c-162">[关注点分离](http://deviq.com/separation-of-concerns/)： 你的应用程序的不同部分的设置不是依赖或与另一个耦合。</span><span class="sxs-lookup"><span data-stu-id="9c16c-162">[Separation of Concerns](http://deviq.com/separation-of-concerns/) : Settings for different parts of your app are not dependent or coupled with one another.</span></span>

<span data-ttu-id="9c16c-163">选项类必须为非抽象具有一个公共的无参数构造函数。</span><span class="sxs-lookup"><span data-stu-id="9c16c-163">The options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="9c16c-164">例如: </span><span class="sxs-lookup"><span data-stu-id="9c16c-164">For example:</span></span>

<span data-ttu-id="9c16c-165">[!code-csharp[Main](configuration/sample/UsingOptions/Models/MyOptions.cs)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-165">[!code-csharp[Main](configuration/sample/UsingOptions/Models/MyOptions.cs)]</span></span>

<a name=options-example></a>

<span data-ttu-id="9c16c-166">在下面的代码中，启用 JSON 配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="9c16c-166">In the following code, the JSON configuration provider is enabled.</span></span> <span data-ttu-id="9c16c-167">`MyOptions`类是已添加到服务容器并绑定到配置。</span><span class="sxs-lookup"><span data-stu-id="9c16c-167">The `MyOptions` class is added to the service container and bound to configuration.</span></span>

<span data-ttu-id="9c16c-168">[!code-csharp[Main](configuration/sample/UsingOptions/Startup.cs?name=snippet1&highlight=8,20-21)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-168">[!code-csharp[Main](configuration/sample/UsingOptions/Startup.cs?name=snippet1&highlight=8,20-21)]</span></span>

<span data-ttu-id="9c16c-169">以下[控制器](../mvc/controllers/index.md)使用[构造函数依赖关系注入](xref:fundamentals/dependency-injection#what-is-dependency-injection)上[ `IOptions<TOptions>` ](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.options.ioptions-1)访问设置：</span><span class="sxs-lookup"><span data-stu-id="9c16c-169">The following [controller](../mvc/controllers/index.md)  uses [constructor Dependency Injection](xref:fundamentals/dependency-injection#what-is-dependency-injection) on [`IOptions<TOptions>`](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.options.ioptions-1) to access settings:</span></span>

<span data-ttu-id="9c16c-170">[!code-csharp[Main](configuration/sample/UsingOptions/Controllers/HomeController.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-170">[!code-csharp[Main](configuration/sample/UsingOptions/Controllers/HomeController.cs?name=snippet1)]</span></span>

<span data-ttu-id="9c16c-171">替换为以下*appsettings.json*文件：</span><span class="sxs-lookup"><span data-stu-id="9c16c-171">With the following *appsettings.json* file:</span></span>

<span data-ttu-id="9c16c-172">[!code-json[Main](configuration/sample/UsingOptions/appsettings1.json)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-172">[!code-json[Main](configuration/sample/UsingOptions/appsettings1.json)]</span></span>

<span data-ttu-id="9c16c-173">`HomeController.Index`方法返回`option1 = value1_from_json, option2 = 2`。</span><span class="sxs-lookup"><span data-stu-id="9c16c-173">The `HomeController.Index` method returns `option1 = value1_from_json, option2 = 2`.</span></span>

<span data-ttu-id="9c16c-174">典型的应用程序不会将整个配置绑定到单个选项文件。</span><span class="sxs-lookup"><span data-stu-id="9c16c-174">Typical apps won't bind the entire configuration to a single options file.</span></span> <span data-ttu-id="9c16c-175">稍后我将介绍如何使用`GetSection`将绑定到一个部分。</span><span class="sxs-lookup"><span data-stu-id="9c16c-175">Later on I'll show how to use `GetSection` to bind to a section.</span></span>

<span data-ttu-id="9c16c-176">在下面的代码中，第二个`IConfigureOptions<TOptions>`服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="9c16c-176">In the following code, a second `IConfigureOptions<TOptions>` service is added to the service container.</span></span> <span data-ttu-id="9c16c-177">它使用委托来配置的绑定`MyOptions`。</span><span class="sxs-lookup"><span data-stu-id="9c16c-177">It uses a delegate to configure the binding with `MyOptions`.</span></span>

<span data-ttu-id="9c16c-178">[!code-csharp[Main](configuration/sample/UsingOptions/Startup2.cs?name=snippet1&highlight=9-13)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-178">[!code-csharp[Main](configuration/sample/UsingOptions/Startup2.cs?name=snippet1&highlight=9-13)]</span></span>

<span data-ttu-id="9c16c-179">你可以添加多个配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="9c16c-179">You can add multiple configuration providers.</span></span> <span data-ttu-id="9c16c-180">配置提供程序 NuGet 包中。</span><span class="sxs-lookup"><span data-stu-id="9c16c-180">Configuration providers are available in NuGet packages.</span></span> <span data-ttu-id="9c16c-181">它们会按它们已注册的顺序应用。</span><span class="sxs-lookup"><span data-stu-id="9c16c-181">They are applied in order they are registered.</span></span>

<span data-ttu-id="9c16c-182">每次调用`Configure<TOptions>`添加`IConfigureOptions<TOptions>`服务到服务容器。</span><span class="sxs-lookup"><span data-stu-id="9c16c-182">Each call to `Configure<TOptions>` adds an `IConfigureOptions<TOptions>` service to the service container.</span></span> <span data-ttu-id="9c16c-183">在前面的示例中，值`Option1`和`Option2`同时中指定*appsettings.json* -但的值`Option1`配置委托来重写。</span><span class="sxs-lookup"><span data-stu-id="9c16c-183">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json* -- but the value of `Option1` is overridden by the configured delegate.</span></span> 

<span data-ttu-id="9c16c-184">启用多个配置服务后，最后一个配置源指定"wins"（设置配置值）。</span><span class="sxs-lookup"><span data-stu-id="9c16c-184">When more than one configuration service is enabled, the last configuration source specified "wins" (sets the configuration value).</span></span> <span data-ttu-id="9c16c-185">在前面的代码中，`HomeController.Index`方法返回`option1 = value1_from_action, option2 = 2`。</span><span class="sxs-lookup"><span data-stu-id="9c16c-185">In the preceding code, the `HomeController.Index` method returns `option1 = value1_from_action, option2 = 2`.</span></span>

<span data-ttu-id="9c16c-186">绑定到配置的选项时, 选项类型中的每个属性绑定到窗体的配置键`property[:sub-property:]`。</span><span class="sxs-lookup"><span data-stu-id="9c16c-186">When you bind options to configuration, each property in your options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="9c16c-187">例如，`MyOptions.Option1`属性绑定到键`Option1`，这从读取`option1`中的属性*appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="9c16c-187">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span> <span data-ttu-id="9c16c-188">在本文后面显示了子属性示例。</span><span class="sxs-lookup"><span data-stu-id="9c16c-188">A sub-property sample is shown later in this article.</span></span>

<span data-ttu-id="9c16c-189">在下面的代码中，第三个`IConfigureOptions<TOptions>`服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="9c16c-189">In the following code, a third `IConfigureOptions<TOptions>` service is added to the service container.</span></span> <span data-ttu-id="9c16c-190">它将绑定`MySubOptions`的部分`subsection`的*appsettings.json*文件：</span><span class="sxs-lookup"><span data-stu-id="9c16c-190">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

<span data-ttu-id="9c16c-191">[!code-csharp[Main](configuration/sample/UsingOptions/Startup3.cs?name=snippet1&highlight=16-17)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-191">[!code-csharp[Main](configuration/sample/UsingOptions/Startup3.cs?name=snippet1&highlight=16-17)]</span></span>

<span data-ttu-id="9c16c-192">注意： 此扩展方法要求`Microsoft.Extensions.Options.ConfigurationExtensions`NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="9c16c-192">Note: This extension method requires the `Microsoft.Extensions.Options.ConfigurationExtensions` NuGet package.</span></span>

<span data-ttu-id="9c16c-193">使用以下*appsettings.json*文件：</span><span class="sxs-lookup"><span data-stu-id="9c16c-193">Using the following *appsettings.json* file:</span></span>

<span data-ttu-id="9c16c-194">[!code-json[Main](configuration/sample/UsingOptions/appsettings.json)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-194">[!code-json[Main](configuration/sample/UsingOptions/appsettings.json)]</span></span>

<span data-ttu-id="9c16c-195">`MySubOptions`类：</span><span class="sxs-lookup"><span data-stu-id="9c16c-195">The `MySubOptions` class:</span></span>

<span data-ttu-id="9c16c-196">[!code-csharp[Main](configuration/sample/UsingOptions/Models/MySubOptions.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-196">[!code-csharp[Main](configuration/sample/UsingOptions/Models/MySubOptions.cs?name=snippet1)]</span></span>

<span data-ttu-id="9c16c-197">替换为以下`Controller`:</span><span class="sxs-lookup"><span data-stu-id="9c16c-197">With the following `Controller`:</span></span>

<span data-ttu-id="9c16c-198">[!code-csharp[Main](configuration/sample/UsingOptions/Controllers/HomeController2.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-198">[!code-csharp[Main](configuration/sample/UsingOptions/Controllers/HomeController2.cs?name=snippet1)]</span></span>

<span data-ttu-id="9c16c-199">`subOption1 = subvalue1_from_json, subOption2 = 200`返回。</span><span class="sxs-lookup"><span data-stu-id="9c16c-199">`subOption1 = subvalue1_from_json, subOption2 = 200` is returned.</span></span>

<span data-ttu-id="9c16c-200">也可以提供视图模型中的选项，或将注入`IOptions<TOptions>`直接到视图：</span><span class="sxs-lookup"><span data-stu-id="9c16c-200">You can also supply options in a view model or inject `IOptions<TOptions>` directly into a view:</span></span>

<span data-ttu-id="9c16c-201">[!code-html[Main](configuration/sample/UsingOptions/Views/Home/Index.cshtml?highlight=3-4,16-17,20-21)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-201">[!code-html[Main](configuration/sample/UsingOptions/Views/Home/Index.cshtml?highlight=3-4,16-17,20-21)]</span></span>

<a name=in-memory-provider></a>

## <a name="ioptionssnapshot"></a><span data-ttu-id="9c16c-202">IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="9c16c-202">IOptionsSnapshot</span></span>

<span data-ttu-id="9c16c-203">*需要 ASP.NET Core 1.1 或更高版本。*</span><span class="sxs-lookup"><span data-stu-id="9c16c-203">*Requires ASP.NET Core 1.1 or higher.*</span></span>

<span data-ttu-id="9c16c-204">`IOptionsSnapshot`当配置文件发生更改时重新加载配置数据的支持。</span><span class="sxs-lookup"><span data-stu-id="9c16c-204">`IOptionsSnapshot` supports reloading configuration data when the configuration file has changed.</span></span> <span data-ttu-id="9c16c-205">它具有最小的开销。</span><span class="sxs-lookup"><span data-stu-id="9c16c-205">It has minimal overhead.</span></span> <span data-ttu-id="9c16c-206">使用`IOptionsSnapshot`与`reloadOnChange: true`，选项绑定到`IConfiguration`并更改时重新加载。</span><span class="sxs-lookup"><span data-stu-id="9c16c-206">Using `IOptionsSnapshot` with `reloadOnChange: true`, the options are bound to `IConfiguration` and reloaded when changed.</span></span>

<span data-ttu-id="9c16c-207">下面的示例演示如何新`IOptionsSnapshot`后创建*config.json*更改。</span><span class="sxs-lookup"><span data-stu-id="9c16c-207">The following sample demonstrates how a new `IOptionsSnapshot` is created after *config.json* changes.</span></span> <span data-ttu-id="9c16c-208">对服务器请求将返回相同时间*config.json*具有**不**更改。</span><span class="sxs-lookup"><span data-stu-id="9c16c-208">Requests to the server will return the same time when *config.json* has **not** changed.</span></span> <span data-ttu-id="9c16c-209">之后的第一个请求*config.json*做的更改将显示新的时间。</span><span class="sxs-lookup"><span data-stu-id="9c16c-209">The first request after *config.json* changes will show a new time.</span></span>

<span data-ttu-id="9c16c-210">[!code-csharp[Main](configuration/sample/IOptionsSnapshot2/Startup.cs?name=snippet1&highlight=1-9,13-18,32,33,52,53)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-210">[!code-csharp[Main](configuration/sample/IOptionsSnapshot2/Startup.cs?name=snippet1&highlight=1-9,13-18,32,33,52,53)]</span></span>

<span data-ttu-id="9c16c-211">下图显示服务器输出：</span><span class="sxs-lookup"><span data-stu-id="9c16c-211">The following image shows the server output:</span></span>

![浏览器图像显示"上次更新时间： 2016 年 11 月 22 日下午 4:43"](configuration/_static/first.png)

<span data-ttu-id="9c16c-213">刷新浏览器不会更改消息值或显示的时间 (时*config.json*仍是如此)。</span><span class="sxs-lookup"><span data-stu-id="9c16c-213">Refreshing the browser doesn't change the message value or time displayed (when *config.json* has not changed).</span></span>

<span data-ttu-id="9c16c-214">进行更改并保存*config.json*然后刷新浏览器：</span><span class="sxs-lookup"><span data-stu-id="9c16c-214">Change and save the  *config.json* and then refresh the browser:</span></span>

![浏览器图像显示"上次更新到 e: 2016 年 11 月 22 日下午 4:53"](configuration/_static/change.png)

## <a name="in-memory-provider-and-binding-to-a-poco-class"></a><span data-ttu-id="9c16c-216">内存中提供程序并绑定到 POCO 类</span><span class="sxs-lookup"><span data-stu-id="9c16c-216">In-memory provider and binding to a POCO class</span></span>

<span data-ttu-id="9c16c-217">下面的示例演示如何使用内存中提供程序并将绑定到类：</span><span class="sxs-lookup"><span data-stu-id="9c16c-217">The following sample shows how to use the in-memory provider and bind to a class:</span></span>

<span data-ttu-id="9c16c-218">[!code-csharp[Main](configuration/sample/InMemory/Program.cs)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-218">[!code-csharp[Main](configuration/sample/InMemory/Program.cs)]</span></span>

<span data-ttu-id="9c16c-219">配置值将返回为字符串，但绑定可使对象的构造。</span><span class="sxs-lookup"><span data-stu-id="9c16c-219">Configuration values are returned as strings, but binding enables the construction of objects.</span></span> <span data-ttu-id="9c16c-220">绑定允许您检索 POCO 对象或甚至是整个对象图。</span><span class="sxs-lookup"><span data-stu-id="9c16c-220">Binding allows you to retrieve POCO objects or even entire object graphs.</span></span> <span data-ttu-id="9c16c-221">下面的示例演示如何将绑定到`MyWindow`，并且与 ASP.NET 核心 MVC 应用程序使用选项模式：</span><span class="sxs-lookup"><span data-stu-id="9c16c-221">The following sample shows how to bind to `MyWindow` and use the options pattern with a ASP.NET Core MVC app:</span></span>

<span data-ttu-id="9c16c-222">[!code-csharp[Main](configuration/sample/WebConfigBind/MyWindow.cs)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-222">[!code-csharp[Main](configuration/sample/WebConfigBind/MyWindow.cs)]</span></span>

<span data-ttu-id="9c16c-223">[!code-json[Main](configuration/sample/WebConfigBind/appsettings.json)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-223">[!code-json[Main](configuration/sample/WebConfigBind/appsettings.json)]</span></span>

<span data-ttu-id="9c16c-224">绑定中的自定义类`ConfigureServices`生成主机时：</span><span class="sxs-lookup"><span data-stu-id="9c16c-224">Bind the custom class in `ConfigureServices` when building the host:</span></span>

<span data-ttu-id="9c16c-225">[!code-csharp[Main](configuration/sample/WebConfigBind/Program.cs?name=snippet1&highlight=3-4)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-225">[!code-csharp[Main](configuration/sample/WebConfigBind/Program.cs?name=snippet1&highlight=3-4)]</span></span>

<span data-ttu-id="9c16c-226">显示从设置`HomeController`:</span><span class="sxs-lookup"><span data-stu-id="9c16c-226">Display the settings from the `HomeController`:</span></span>

<span data-ttu-id="9c16c-227">[!code-csharp[Main](configuration/sample/WebConfigBind/Controllers/HomeController.cs)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-227">[!code-csharp[Main](configuration/sample/WebConfigBind/Controllers/HomeController.cs)]</span></span>

### <a name="getvalue"></a><span data-ttu-id="9c16c-228">GetValue</span><span class="sxs-lookup"><span data-stu-id="9c16c-228">GetValue</span></span>

<span data-ttu-id="9c16c-229">下面的示例演示如何[GetValue<T> ](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationbinder#Microsoft_Extensions_Configuration_ConfigurationBinder_GetValue_Microsoft_Extensions_Configuration_IConfiguration_System_Type_System_String_System_Object_)扩展方法：</span><span class="sxs-lookup"><span data-stu-id="9c16c-229">The following sample demonstrates the [GetValue<T>](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationbinder#Microsoft_Extensions_Configuration_ConfigurationBinder_GetValue_Microsoft_Extensions_Configuration_IConfiguration_System_Type_System_String_System_Object_) extension method:</span></span>

<span data-ttu-id="9c16c-230">[!code-csharp[Main](configuration/sample/InMemoryGetValue/Program.cs?highlight=27-29)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-230">[!code-csharp[Main](configuration/sample/InMemoryGetValue/Program.cs?highlight=27-29)]</span></span>

<span data-ttu-id="9c16c-231">ConfigurationBinder`GetValue<T>`方法允许你指定默认值 (在此示例中为 80)。</span><span class="sxs-lookup"><span data-stu-id="9c16c-231">The ConfigurationBinder's `GetValue<T>` method allows you to specify a default value (80 in the sample).</span></span> <span data-ttu-id="9c16c-232">`GetValue<T>`用于简单的方案，不会绑定到整个部分。</span><span class="sxs-lookup"><span data-stu-id="9c16c-232">`GetValue<T>` is for simple scenarios and does not bind to entire sections.</span></span> <span data-ttu-id="9c16c-233">`GetValue<T>`获取标量值从`GetSection(key).Value`转换为特定类型。</span><span class="sxs-lookup"><span data-stu-id="9c16c-233">`GetValue<T>` gets scalar values from `GetSection(key).Value` converted to a specific type.</span></span>

## <a name="binding-to-an-object-graph"></a><span data-ttu-id="9c16c-234">绑定到对象图</span><span class="sxs-lookup"><span data-stu-id="9c16c-234">Binding to an object graph</span></span>

<span data-ttu-id="9c16c-235">你可以以递归方式绑定到类中每个对象。</span><span class="sxs-lookup"><span data-stu-id="9c16c-235">You can recursively bind to each object in a class.</span></span> <span data-ttu-id="9c16c-236">请考虑以下`AppOptions`类：</span><span class="sxs-lookup"><span data-stu-id="9c16c-236">Consider the following `AppOptions` class:</span></span>

<span data-ttu-id="9c16c-237">[!code-csharp[Main](configuration/sample/ObjectGraph/AppOptions.cs)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-237">[!code-csharp[Main](configuration/sample/ObjectGraph/AppOptions.cs)]</span></span>

<span data-ttu-id="9c16c-238">下面的示例将绑定到`AppOptions`类：</span><span class="sxs-lookup"><span data-stu-id="9c16c-238">The following sample binds to the `AppOptions` class:</span></span>

<span data-ttu-id="9c16c-239">[!code-csharp[Main](configuration/sample/ObjectGraph/Program.cs?highlight=15-16)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-239">[!code-csharp[Main](configuration/sample/ObjectGraph/Program.cs?highlight=15-16)]</span></span>

<span data-ttu-id="9c16c-240">**ASP.NET 核心 1.1**和更高版本可以使用`Get<T>`，这适用于整个部分。</span><span class="sxs-lookup"><span data-stu-id="9c16c-240">**ASP.NET Core 1.1** and higher can use  `Get<T>`, which works with entire sections.</span></span> <span data-ttu-id="9c16c-241">`Get<T>`可以是比使用多个 convienent `Bind`。</span><span class="sxs-lookup"><span data-stu-id="9c16c-241">`Get<T>` can be more convienent than using `Bind`.</span></span> <span data-ttu-id="9c16c-242">下面的代码演示如何使用`Get<T>`与上面的示例：</span><span class="sxs-lookup"><span data-stu-id="9c16c-242">The following code shows how to use `Get<T>` with the sample above:</span></span>

```csharp
var appConfig = config.GetSection("App").Get<AppOptions>();
```

<span data-ttu-id="9c16c-243">使用以下*appsettings.json*文件：</span><span class="sxs-lookup"><span data-stu-id="9c16c-243">Using the following *appsettings.json* file:</span></span>

<span data-ttu-id="9c16c-244">[!code-json[Main](configuration/sample/ObjectGraph/appsettings.json)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-244">[!code-json[Main](configuration/sample/ObjectGraph/appsettings.json)]</span></span>

<span data-ttu-id="9c16c-245">该程序显示`Height 11`。</span><span class="sxs-lookup"><span data-stu-id="9c16c-245">The program displays `Height 11`.</span></span>

<span data-ttu-id="9c16c-246">下面的代码可以用于单元测试配置：</span><span class="sxs-lookup"><span data-stu-id="9c16c-246">The following code can be used to unit test the configuration:</span></span>

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

## <a name="basic-sample-of-entity-framework-custom-provider"></a><span data-ttu-id="9c16c-247">实体框架自定义提供程序的基本示例</span><span class="sxs-lookup"><span data-stu-id="9c16c-247">Basic sample of Entity Framework custom provider</span></span>

<span data-ttu-id="9c16c-248">在本部分中，创建的基本配置提供程序从使用 EF 数据库中读取名称-值对。</span><span class="sxs-lookup"><span data-stu-id="9c16c-248">In this section, a basic configuration provider that reads name-value pairs from a database using EF is created.</span></span> 

<span data-ttu-id="9c16c-249">定义`ConfigurationValue`在数据库中存储配置值的实体：</span><span class="sxs-lookup"><span data-stu-id="9c16c-249">Define a `ConfigurationValue` entity for storing configuration values in the database:</span></span>

<span data-ttu-id="9c16c-250">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/ConfigurationValue.cs)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-250">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/ConfigurationValue.cs)]</span></span>

<span data-ttu-id="9c16c-251">添加`ConfigurationContext`存储和访问配置的值：</span><span class="sxs-lookup"><span data-stu-id="9c16c-251">Add a `ConfigurationContext` to store and access the configured values:</span></span>

<span data-ttu-id="9c16c-252">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/ConfigurationContext.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-252">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/ConfigurationContext.cs?name=snippet1)]</span></span>

<span data-ttu-id="9c16c-253">创建一个类，实现[IConfigurationSource](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.iconfigurationsource):</span><span class="sxs-lookup"><span data-stu-id="9c16c-253">Create an class that implements [IConfigurationSource](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.iconfigurationsource):</span></span>

<span data-ttu-id="9c16c-254">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkConfigurationSource.cs?highlight=7)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-254">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkConfigurationSource.cs?highlight=7)]</span></span>

<span data-ttu-id="9c16c-255">创建自定义配置提供程序通过继承[ConfigurationProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationprovider)。</span><span class="sxs-lookup"><span data-stu-id="9c16c-255">Create the custom configuration provider by inheriting from [ConfigurationProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationprovider).</span></span>  <span data-ttu-id="9c16c-256">为空时，配置提供程序初始化数据库：</span><span class="sxs-lookup"><span data-stu-id="9c16c-256">The configuration provider initializes the database when it's empty:</span></span>

<span data-ttu-id="9c16c-257">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkConfigurationProvider.cs?highlight=9,18-31,38-39)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-257">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkConfigurationProvider.cs?highlight=9,18-31,38-39)]</span></span>

<span data-ttu-id="9c16c-258">运行示例时，会显示数据库 （"value_from_ef_1"和"value_from_ef_2"） 中突出显示的值。</span><span class="sxs-lookup"><span data-stu-id="9c16c-258">The highlighted values from the database ("value_from_ef_1" and "value_from_ef_2") are displayed when the sample is run.</span></span>

<span data-ttu-id="9c16c-259">你可以添加`EFConfigSource`将该配置源添加的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="9c16c-259">You can add an `EFConfigSource` extension method for adding the configuration source:</span></span>

<span data-ttu-id="9c16c-260">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkExtensions.cs?highlight=12)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-260">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkExtensions.cs?highlight=12)]</span></span>

<span data-ttu-id="9c16c-261">下面的代码演示如何使用自定义`EFConfigProvider`:</span><span class="sxs-lookup"><span data-stu-id="9c16c-261">The following code shows how to use the custom `EFConfigProvider`:</span></span>

<span data-ttu-id="9c16c-262">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/Program.cs?highlight=21-26)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-262">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/Program.cs?highlight=21-26)]</span></span>

<span data-ttu-id="9c16c-263">请注意此示例将添加自定义`EFConfigProvider`后 JSON 提供程序，因此数据库中的任何设置将替代设置从*appsettings.json*文件。</span><span class="sxs-lookup"><span data-stu-id="9c16c-263">Note the sample adds the custom `EFConfigProvider` after the JSON provider, so any settings from the database will override settings from the *appsettings.json* file.</span></span>

<span data-ttu-id="9c16c-264">使用以下*appsettings.json*文件：</span><span class="sxs-lookup"><span data-stu-id="9c16c-264">Using the following *appsettings.json* file:</span></span>

<span data-ttu-id="9c16c-265">[!code-json[Main](configuration/sample/CustomConfigurationProvider/appsettings.json)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-265">[!code-json[Main](configuration/sample/CustomConfigurationProvider/appsettings.json)]</span></span>

<span data-ttu-id="9c16c-266">显示以下消息：</span><span class="sxs-lookup"><span data-stu-id="9c16c-266">The following is displayed:</span></span>

```console
key1=value_from_ef_1
key2=value_from_ef_2
key3=value_from_json_3
```

## <a name="commandline-configuration-provider"></a><span data-ttu-id="9c16c-267">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="9c16c-267">CommandLine configuration provider</span></span>

<span data-ttu-id="9c16c-268">下面的示例使最后一个命令行配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="9c16c-268">The following sample enables the CommandLine configuration provider last:</span></span>

<span data-ttu-id="9c16c-269">[!code-csharp[Main](configuration/sample/CommandLine/Program.cs)]</span><span class="sxs-lookup"><span data-stu-id="9c16c-269">[!code-csharp[Main](configuration/sample/CommandLine/Program.cs)]</span></span>

<span data-ttu-id="9c16c-270">使用以下方法来配置设置中传递：</span><span class="sxs-lookup"><span data-stu-id="9c16c-270">Use the following to pass in configuration settings:</span></span>

```console
dotnet run /Profile:MachineName=Bob /App:MainWindow:Left=1234
```

<span data-ttu-id="9c16c-271">这将显示：</span><span class="sxs-lookup"><span data-stu-id="9c16c-271">Which displays:</span></span>

```console
Hello Bob
Left 1234
```

<span data-ttu-id="9c16c-272">`GetSwitchMappings`方法允许你使用`-`而非`/`和它中抽出前导子项前缀。</span><span class="sxs-lookup"><span data-stu-id="9c16c-272">The `GetSwitchMappings` method allows you to use `-` rather than `/` and it strips the leading subkey prefixes.</span></span> <span data-ttu-id="9c16c-273">例如: </span><span class="sxs-lookup"><span data-stu-id="9c16c-273">For example:</span></span>

```console
dotnet run -MachineName=Bob -Left=7734
```

<span data-ttu-id="9c16c-274">显示：</span><span class="sxs-lookup"><span data-stu-id="9c16c-274">Displays:</span></span>

```console
Hello Bob
Left 7734
```

<span data-ttu-id="9c16c-275">命令行自变量必须包含 （它可以为 null） 的值。</span><span class="sxs-lookup"><span data-stu-id="9c16c-275">Command-line arguments must include a value (it can be null).</span></span> <span data-ttu-id="9c16c-276">例如: </span><span class="sxs-lookup"><span data-stu-id="9c16c-276">For example:</span></span>

```console
dotnet run /Profile:MachineName=
```

<span data-ttu-id="9c16c-277">是可以的但</span><span class="sxs-lookup"><span data-stu-id="9c16c-277">Is OK, but</span></span>

```console
dotnet run /Profile:MachineName
```

<span data-ttu-id="9c16c-278">引发异常的结果。</span><span class="sxs-lookup"><span data-stu-id="9c16c-278">results in an exception.</span></span> <span data-ttu-id="9c16c-279">如果你指定为其中没有相应的交换机映射的-或--命令行开关前缀，则将引发异常。</span><span class="sxs-lookup"><span data-stu-id="9c16c-279">An exception will be thrown if you specify a command-line switch prefix of - or -- for which there's no corresponding switch mapping.</span></span>

## <a name="the-webconfig-file"></a><span data-ttu-id="9c16c-280">Web.config 文件</span><span class="sxs-lookup"><span data-stu-id="9c16c-280">The web.config file</span></span>

<span data-ttu-id="9c16c-281">A *web.config*文件是必需的当你在 IIS 或 IIS Express 应用程序承载。</span><span class="sxs-lookup"><span data-stu-id="9c16c-281">A *web.config* file is required when you host the app in IIS or IIS-Express.</span></span> <span data-ttu-id="9c16c-282">*web.config*开启 AspNetCoreModule IIS 启动你的应用中。</span><span class="sxs-lookup"><span data-stu-id="9c16c-282">*web.config* turns on the AspNetCoreModule in IIS to launch your app.</span></span> <span data-ttu-id="9c16c-283">中的设置*web.config*启用 IIS 来启动你的应用和配置其他 IIS 设置和模块中 AspNetCoreModule。</span><span class="sxs-lookup"><span data-stu-id="9c16c-283">Settings in *web.config* enable the AspNetCoreModule in IIS to launch your app and configure other IIS settings and modules.</span></span> <span data-ttu-id="9c16c-284">如果你使用的 Visual Studio 将删除*web.config*，Visual Studio 将创建一个新。</span><span class="sxs-lookup"><span data-stu-id="9c16c-284">If you are using Visual Studio and delete *web.config*, Visual Studio will create a new one.</span></span>

### <a name="additional-notes"></a><span data-ttu-id="9c16c-285">附加说明</span><span class="sxs-lookup"><span data-stu-id="9c16c-285">Additional notes</span></span>

* <span data-ttu-id="9c16c-286">依赖关系注入 (DI) 未设置截止日期之后`ConfigureServices`调用。</span><span class="sxs-lookup"><span data-stu-id="9c16c-286">Dependency Injection (DI) is not set up until after `ConfigureServices` is invoked.</span></span>
* <span data-ttu-id="9c16c-287">配置系统不是 DI 感知。</span><span class="sxs-lookup"><span data-stu-id="9c16c-287">The configuration system is not DI aware.</span></span>
* <span data-ttu-id="9c16c-288">`IConfiguration`具有两个专用化：</span><span class="sxs-lookup"><span data-stu-id="9c16c-288">`IConfiguration` has two specializations:</span></span>
  * <span data-ttu-id="9c16c-289">`IConfigurationRoot`用于根节点。</span><span class="sxs-lookup"><span data-stu-id="9c16c-289">`IConfigurationRoot`  Used for the root node.</span></span> <span data-ttu-id="9c16c-290">可以触发重新加载。</span><span class="sxs-lookup"><span data-stu-id="9c16c-290">Can trigger a reload.</span></span>
  * <span data-ttu-id="9c16c-291">`IConfigurationSection`表示一个节的配置值。</span><span class="sxs-lookup"><span data-stu-id="9c16c-291">`IConfigurationSection`  Represents a section of configuration values.</span></span> <span data-ttu-id="9c16c-292">`GetSection`和`GetChildren`方法返回`IConfigurationSection`。</span><span class="sxs-lookup"><span data-stu-id="9c16c-292">The `GetSection` and `GetChildren` methods return an `IConfigurationSection`.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="9c16c-293">其他资源</span><span class="sxs-lookup"><span data-stu-id="9c16c-293">Additional resources</span></span>

* [<span data-ttu-id="9c16c-294">使用多个环境</span><span class="sxs-lookup"><span data-stu-id="9c16c-294">Working with Multiple Environments</span></span>](environments.md)
* [<span data-ttu-id="9c16c-295">安全存储在开发过程中的应用程序机密</span><span class="sxs-lookup"><span data-stu-id="9c16c-295">Safe storage of app secrets during development</span></span>](../security/app-secrets.md)
* [<span data-ttu-id="9c16c-296">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="9c16c-296">Dependency Injection</span></span>](dependency-injection.md)
* [<span data-ttu-id="9c16c-297">Azure 密钥保管库配置提供程序</span><span class="sxs-lookup"><span data-stu-id="9c16c-297">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration)
