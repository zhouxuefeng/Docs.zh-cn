---
title: "使用多个环境"
author: ardalis
description: 
keywords: "ASP.NET 核心，环境设置，ASPNETCORE_ENVIRONMENT"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: b5bba985-be12-4464-9a01-df3599b2a6f1
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/environments
ms.openlocfilehash: 5e41920c9745f903d33fa25922727e920c1efc26
ms.sourcegitcommit: 8f5277871eff86134ebf68d3737196cfd4a62c2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2017
---
# <a name="working-with-multiple-environments"></a><span data-ttu-id="f868f-103">使用多个环境</span><span class="sxs-lookup"><span data-stu-id="f868f-103">Working with multiple environments</span></span>

<span data-ttu-id="f868f-104">通过[Steve Smith](http://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="f868f-104">By [Steve Smith](http://ardalis.com)</span></span>

<span data-ttu-id="f868f-105">ASP.NET Core 提供用于跨多个环境，如开发、 过渡和生产控制应用行为的支持。</span><span class="sxs-lookup"><span data-stu-id="f868f-105">ASP.NET Core provides support for controlling app behavior across multiple environments, such as development, staging, and production.</span></span> <span data-ttu-id="f868f-106">使用环境变量以指示运行时环境中，允许应用程序配置为该环境。</span><span class="sxs-lookup"><span data-stu-id="f868f-106">Environment variables are used to indicate the runtime environment, allowing the app to be configured for that environment.</span></span>

[<span data-ttu-id="f868f-107">查看或下载示例代码</span><span class="sxs-lookup"><span data-stu-id="f868f-107">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/environments/sample)

## <a name="development-staging-production"></a><span data-ttu-id="f868f-108">开发，暂存生产</span><span class="sxs-lookup"><span data-stu-id="f868f-108">Development, Staging, Production</span></span>

<span data-ttu-id="f868f-109">ASP.NET 核心引用特定[环境变量](https://github.com/aspnet/Home/wiki/Environment-Variables)，`ASPNETCORE_ENVIRONMENT`来描述中当前运行应用程序的环境。</span><span class="sxs-lookup"><span data-stu-id="f868f-109">ASP.NET Core references a particular [environment variable](https://github.com/aspnet/Home/wiki/Environment-Variables), `ASPNETCORE_ENVIRONMENT` to describe the environment the application is currently running in.</span></span> <span data-ttu-id="f868f-110">可以设置此变量您喜欢的任何值，而三个值由约定： `Development`， `Staging`，和`Production`。</span><span class="sxs-lookup"><span data-stu-id="f868f-110">This variable can be set to any value you like, but three values are used by convention: `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="f868f-111">你会发现以下示例中使用的值和 ASP.NET Core 提供模板。</span><span class="sxs-lookup"><span data-stu-id="f868f-111">You will find these values used in the samples and templates provided with ASP.NET Core.</span></span>

<span data-ttu-id="f868f-112">当前的环境设置可以检测到以编程方式从应用程序中。</span><span class="sxs-lookup"><span data-stu-id="f868f-112">The current environment setting can be detected programmatically from within your application.</span></span> <span data-ttu-id="f868f-113">此外，你可以使用环境[标记帮助器](../mvc/views/tag-helpers/index.md)包括中的某些部分你[视图](../mvc/views/index.md)基于当前的应用程序环境。</span><span class="sxs-lookup"><span data-stu-id="f868f-113">In addition, you can use the Environment [tag helper](../mvc/views/tag-helpers/index.md) to include certain sections in your [view](../mvc/views/index.md) based on the current application environment.</span></span>

<span data-ttu-id="f868f-114">注意： 在 Windows 和 macOS 上，指定的环境名称是区分大小写。</span><span class="sxs-lookup"><span data-stu-id="f868f-114">Note: On Windows and macOS, the specified environment name is case insensitive.</span></span> <span data-ttu-id="f868f-115">是否将变量设置为`Development`或`development`或`DEVELOPMENT`结果将是相同的。</span><span class="sxs-lookup"><span data-stu-id="f868f-115">Whether you set the variable to `Development` or `development` or `DEVELOPMENT` the results will be the same.</span></span> <span data-ttu-id="f868f-116">但是，Linux 是**区分大小写**默认情况下的操作系统。</span><span class="sxs-lookup"><span data-stu-id="f868f-116">However, Linux is a **case sensitive** OS by default.</span></span> <span data-ttu-id="f868f-117">环境变量、 文件名和设置需要区分大小写。</span><span class="sxs-lookup"><span data-stu-id="f868f-117">Environment variables, file names and settings require case sensitivity.</span></span>

### <a name="development"></a><span data-ttu-id="f868f-118">开发</span><span class="sxs-lookup"><span data-stu-id="f868f-118">Development</span></span>

<span data-ttu-id="f868f-119">这应该是在开发应用程序时使用的环境。</span><span class="sxs-lookup"><span data-stu-id="f868f-119">This should be the environment used when developing an application.</span></span> <span data-ttu-id="f868f-120">它通常用于以启用你不想要应用在生产中，如运行时是可用的功能[开发人员异常页](xref:fundamentals/error-handling#the-developer-exception-page)。</span><span class="sxs-lookup"><span data-stu-id="f868f-120">It is typically used to enable features that you wouldn't want to be available when the app runs in production, such as the [developer exception page](xref:fundamentals/error-handling#the-developer-exception-page).</span></span>

<span data-ttu-id="f868f-121">如果你使用 Visual Studio，可以在你的项目的调试配置文件中配置环境。</span><span class="sxs-lookup"><span data-stu-id="f868f-121">If you're using Visual Studio, the environment can be configured in your project's debug profiles.</span></span> <span data-ttu-id="f868f-122">调试配置文件指定[服务器](xref:fundamentals/servers/index)要设置启动应用程序和任何环境变量时要使用。</span><span class="sxs-lookup"><span data-stu-id="f868f-122">Debug profiles specify the [server](xref:fundamentals/servers/index) to use when launching the application and any environment variables to be set.</span></span> <span data-ttu-id="f868f-123">你的项目可以具有不同的设置环境变量的多个调试配置文件。</span><span class="sxs-lookup"><span data-stu-id="f868f-123">Your project can have multiple debug profiles that set environment variables differently.</span></span> <span data-ttu-id="f868f-124">通过使用管理这些配置文件**调试**web 应用程序项目的选项卡**属性**菜单。</span><span class="sxs-lookup"><span data-stu-id="f868f-124">You manage these profiles by using the **Debug** tab of your web application project's **Properties** menu.</span></span> <span data-ttu-id="f868f-125">在项目属性中设置的值保留在*launchSettings.json*文件，并且你还可以通过直接编辑该文件来配置配置文件。</span><span class="sxs-lookup"><span data-stu-id="f868f-125">The values you set in project properties are persisted in the *launchSettings.json* file, and you can also configure profiles by editing that file directly.</span></span>

<span data-ttu-id="f868f-126">IIS Express 的配置文件如下所示：</span><span class="sxs-lookup"><span data-stu-id="f868f-126">The profile for IIS Express is shown here:</span></span>

![项目属性设置环境变量](environments/_static/project-properties-debug.png)

<span data-ttu-id="f868f-128">下面是`launchSettings.json`包括的配置文件的文件`Development`和`Staging`:</span><span class="sxs-lookup"><span data-stu-id="f868f-128">Here is a `launchSettings.json` file that includes profiles for `Development` and `Staging`:</span></span>

<span data-ttu-id="f868f-129">[!code-json[Main](../fundamentals/environments/sample/src/Environments/Properties/launchSettings.json?highlight=15,22)]</span><span class="sxs-lookup"><span data-stu-id="f868f-129">[!code-json[Main](../fundamentals/environments/sample/src/Environments/Properties/launchSettings.json?highlight=15,22)]</span></span>

<span data-ttu-id="f868f-130">对项目配置文件所做更改可能会生效，直到重新启动使用的 web 服务器 （具体而言，Kestrel 必须重新启动之前它将检测到其环境所做的更改）。</span><span class="sxs-lookup"><span data-stu-id="f868f-130">Changes made to project profiles may not take effect until the web server used is restarted (in particular, Kestrel must be restarted before it will detect changes made to its environment).</span></span>

>[!WARNING]
> <span data-ttu-id="f868f-131">环境变量存储在*launchSettings.json*未以任何方式进行保护和如果你使用一个将是你的项目，源代码存储库的一部分。</span><span class="sxs-lookup"><span data-stu-id="f868f-131">Environment variables stored in *launchSettings.json* are not secured in any way and will be part of the source code repository for your project, if you use one.</span></span> <span data-ttu-id="f868f-132">**永远不会存储凭据或此文件中的其他机密数据。**</span><span class="sxs-lookup"><span data-stu-id="f868f-132">**Never store credentials or other secret data in this file.**</span></span> <span data-ttu-id="f868f-133">如果你需要一个位置来存储此类数据，使用*机密 Manager*工具中所述[安全存储在开发过程中的应用程序机密](../security/app-secrets.md#security-app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="f868f-133">If you need a place to store such data, use the *Secret Manager* tool described in [Safe storage of app secrets during development](../security/app-secrets.md#security-app-secrets).</span></span>

### <a name="staging"></a><span data-ttu-id="f868f-134">过渡</span><span class="sxs-lookup"><span data-stu-id="f868f-134">Staging</span></span>

<span data-ttu-id="f868f-135">按照约定，`Staging`环境是用于在部署到生产环境之前进行最终测试预生产环境。</span><span class="sxs-lookup"><span data-stu-id="f868f-135">By convention, a `Staging` environment is a pre-production environment used for final testing before deployment to production.</span></span> <span data-ttu-id="f868f-136">理想情况下，其物理特征应镜像的生产环境，以便在生产环境中可能出现任何问题发生在过渡环境中，其中他们可以进行寻址而不会影响用户的第一次。</span><span class="sxs-lookup"><span data-stu-id="f868f-136">Ideally, its physical characteristics should mirror that of production, so that any issues that may arise in production occur first in the staging environment, where they can be addressed without impact to users.</span></span>

### <a name="production"></a><span data-ttu-id="f868f-137">生产</span><span class="sxs-lookup"><span data-stu-id="f868f-137">Production</span></span>

<span data-ttu-id="f868f-138">`Production`环境是应用程序运行时实时的环境和最终用户使用。</span><span class="sxs-lookup"><span data-stu-id="f868f-138">The `Production` environment is the environment in which the application runs when it is live and being used by end users.</span></span> <span data-ttu-id="f868f-139">此环境应配置为最大程度提高安全性、 性能和应用程序的可靠性。</span><span class="sxs-lookup"><span data-stu-id="f868f-139">This environment should be configured to maximize security, performance, and application robustness.</span></span> <span data-ttu-id="f868f-140">将不同于开发生产环境可能具有一些常见设置包括：</span><span class="sxs-lookup"><span data-stu-id="f868f-140">Some common settings that a production environment might have that would differ from development include:</span></span>

* <span data-ttu-id="f868f-141">打开缓存</span><span class="sxs-lookup"><span data-stu-id="f868f-141">Turn on caching</span></span>

* <span data-ttu-id="f868f-142">确保客户端的所有资源捆绑在一起，缩减的并可能从 CDN 提供</span><span class="sxs-lookup"><span data-stu-id="f868f-142">Ensure all client-side resources are bundled, minified, and potentially served from a CDN</span></span>

* <span data-ttu-id="f868f-143">请关闭诊断 ErrorPages</span><span class="sxs-lookup"><span data-stu-id="f868f-143">Turn off diagnostic ErrorPages</span></span>

* <span data-ttu-id="f868f-144">开启友好错误页</span><span class="sxs-lookup"><span data-stu-id="f868f-144">Turn on friendly error pages</span></span>

* <span data-ttu-id="f868f-145">启用日志记录和监视生产 (例如， [Application Insights](https://azure.microsoft.com/documentation/articles/app-insights-asp-net-five/))</span><span class="sxs-lookup"><span data-stu-id="f868f-145">Enable production logging and monitoring (for example, [Application Insights](https://azure.microsoft.com/documentation/articles/app-insights-asp-net-five/))</span></span>

<span data-ttu-id="f868f-146">这不是要作为的完整列表。</span><span class="sxs-lookup"><span data-stu-id="f868f-146">This is by no means meant to be a complete list.</span></span> <span data-ttu-id="f868f-147">若要避免分散在你的应用程序中的不同部分的环境检查最好。</span><span class="sxs-lookup"><span data-stu-id="f868f-147">It's best to avoid scattering environment checks in many parts of your application.</span></span> <span data-ttu-id="f868f-148">相反，建议的方法是执行内应用程序的此类检查`Startup`类尽可能</span><span class="sxs-lookup"><span data-stu-id="f868f-148">Instead, the recommended approach is to perform such checks within the application's `Startup` class(es) wherever possible</span></span>

## <a name="setting-the-environment"></a><span data-ttu-id="f868f-149">将环境设置</span><span class="sxs-lookup"><span data-stu-id="f868f-149">Setting the environment</span></span>

<span data-ttu-id="f868f-150">将环境设置的方法取决于操作系统。</span><span class="sxs-lookup"><span data-stu-id="f868f-150">The method for setting the environment depends on the operating system.</span></span>

### <a name="windows"></a><span data-ttu-id="f868f-151">Windows</span><span class="sxs-lookup"><span data-stu-id="f868f-151">Windows</span></span>
<span data-ttu-id="f868f-152">若要设置`ASPNETCORE_ENVIRONMENT`当前的会话中，如果应用程序将启动使用`dotnet run`，可以使用以下命令</span><span class="sxs-lookup"><span data-stu-id="f868f-152">To set the `ASPNETCORE_ENVIRONMENT` for the current session, if the app is started using `dotnet run`, the following commands are used</span></span>

<span data-ttu-id="f868f-153">**命令行**</span><span class="sxs-lookup"><span data-stu-id="f868f-153">**Command line**</span></span>
```
set ASPNETCORE_ENVIRONMENT=Development
```
<span data-ttu-id="f868f-154">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="f868f-154">**PowerShell**</span></span>
```
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="f868f-155">这些命令将仅为当前窗口的生效。</span><span class="sxs-lookup"><span data-stu-id="f868f-155">These commands take effect only for the current window.</span></span> <span data-ttu-id="f868f-156">当窗口已关闭时，ASPNETCORE_ENVIRONMENT 设置将恢复为默认设置或计算机的值。</span><span class="sxs-lookup"><span data-stu-id="f868f-156">When the window is closed, the ASPNETCORE_ENVIRONMENT setting reverts to the default setting or machine value.</span></span> <span data-ttu-id="f868f-157">为了上 Windows 打开全局设置的值**控制面板** > **系统** > **高级系统设置**和添加或编辑`ASPNETCORE_ENVIRONMENT`值。</span><span class="sxs-lookup"><span data-stu-id="f868f-157">In order to set the value globally on Windows open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value.</span></span>

![高级属性的系统](environments/_static/systemsetting_environment.png)

![ASPNET 核心环境变量](environments/_static/windows_aspnetcore_environment.png) 

<span data-ttu-id="f868f-160">**web.config**</span><span class="sxs-lookup"><span data-stu-id="f868f-160">**web.config**</span></span>

<span data-ttu-id="f868f-161">请参阅*设置环境变量*部分[ASP.NET 核心模块配置引用](xref:hosting/aspnet-core-module#setting-environment-variables)主题。</span><span class="sxs-lookup"><span data-stu-id="f868f-161">See the *Setting environment variables* section of the [ASP.NET Core Module configuration reference](xref:hosting/aspnet-core-module#setting-environment-variables) topic.</span></span>

<span data-ttu-id="f868f-162">**每个 IIS 应用程序池**</span><span class="sxs-lookup"><span data-stu-id="f868f-162">**Per IIS Application Pool**</span></span>

<span data-ttu-id="f868f-163">如果你需要设置环境变量单个运行的应用程序在独立的应用程序池 （IIS 10.0 + 支持），请参阅*AppCmd.exe 命令*部分[环境变量\<environmentVariables >](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe)在 IIS 中的主题参考文档。</span><span class="sxs-lookup"><span data-stu-id="f868f-163">If you need to set environment variables for individual apps running in isolated Application Pools (supported on IIS 10.0+), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

### <a name="macos"></a><span data-ttu-id="f868f-164">macOS</span><span class="sxs-lookup"><span data-stu-id="f868f-164">macOS</span></span>
<span data-ttu-id="f868f-165">设置 macOS 的当前环境，可以进行串联时运行该应用程序;</span><span class="sxs-lookup"><span data-stu-id="f868f-165">Setting the current environment for macOS can be done in-line when running the application;</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```
<span data-ttu-id="f868f-166">也可以使用`export`若要在运行应用程序之前设置它。</span><span class="sxs-lookup"><span data-stu-id="f868f-166">or using `export` to set it prior to running the app.</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
``` 
<span data-ttu-id="f868f-167">设置计算机级别的环境变量*.bashrc*或*.bash_profile*文件。</span><span class="sxs-lookup"><span data-stu-id="f868f-167">Machine level environment variables are set in the *.bashrc*  or *.bash_profile* file.</span></span> <span data-ttu-id="f868f-168">使用任何文本编辑器编辑该文件并添加以下语句。</span><span class="sxs-lookup"><span data-stu-id="f868f-168">Edit the file using any text editor and add the following statment.</span></span>

```
export ASPNETCORE_ENVIRONMENT=Development
```  

### <a name="linux"></a><span data-ttu-id="f868f-169">Linux</span><span class="sxs-lookup"><span data-stu-id="f868f-169">Linux</span></span>
<span data-ttu-id="f868f-170">对于 Linux 发行版本，使用`export`基于会话的变量设置在命令行命令和*bash_profile*机级别的环境设置的文件。</span><span class="sxs-lookup"><span data-stu-id="f868f-170">For Linux distros, use the `export` command at the command line for session based variable settings and *bash_profile* file for machine level environment settings.</span></span>

## <a name="determining-the-environment-at-runtime"></a><span data-ttu-id="f868f-171">确定在运行时环境</span><span class="sxs-lookup"><span data-stu-id="f868f-171">Determining the environment at runtime</span></span>

<span data-ttu-id="f868f-172">`IHostingEnvironment`服务提供用于使用环境的核心抽象。</span><span class="sxs-lookup"><span data-stu-id="f868f-172">The `IHostingEnvironment` service provides the core abstraction for working with environments.</span></span> <span data-ttu-id="f868f-173">此服务是前提是 asp.net 的承载层，并且可以被注入到你的启动逻辑通过[依赖关系注入](dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="f868f-173">This service is provided by the ASP.NET hosting layer, and can be injected into your startup logic via [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="f868f-174">ASP.NET 核心网站模板在 Visual Studio 中的使用这种方法来加载特定于环境的配置文件 （如果存在） 并自定义应用程序的错误处理设置。</span><span class="sxs-lookup"><span data-stu-id="f868f-174">The ASP.NET Core web site template in Visual Studio uses this approach to load environment-specific configuration files (if present) and to customize the app's error handling settings.</span></span> <span data-ttu-id="f868f-175">在这两种情况下，此行为通过引用当前指定的环境，通过调用`EnvironmentName`或`IsEnvironment`的实例上`IHostingEnvironment`传递到相应的方法。</span><span class="sxs-lookup"><span data-stu-id="f868f-175">In both cases, this behavior is achieved by referring to the currently specified environment by calling `EnvironmentName` or `IsEnvironment` on the instance of `IHostingEnvironment` passed into the appropriate method.</span></span>

> [!NOTE]
> <span data-ttu-id="f868f-176">如果你需要检查应用程序是否正在运行在特定环境中，使用`env.IsEnvironment("environmentname")`由于它正确将忽略大小写 (而不是检查是否`env.EnvironmentName == "Development"`例如)。</span><span class="sxs-lookup"><span data-stu-id="f868f-176">If you need to check whether the application is running in a particular environment, use `env.IsEnvironment("environmentname")` since it will correctly ignore case (instead of checking if `env.EnvironmentName == "Development"` for example).</span></span>

<span data-ttu-id="f868f-177">例如，可以在你配置的方法中使用下面的代码以设置环境特定的错误处理：</span><span class="sxs-lookup"><span data-stu-id="f868f-177">For example, you can use the following code in your Configure method to setup environment specific error handling:</span></span>

<span data-ttu-id="f868f-178">[!code-csharp[Main](environments/sample/src/Environments/Startup.cs?range=19-30)]</span><span class="sxs-lookup"><span data-stu-id="f868f-178">[!code-csharp[Main](environments/sample/src/Environments/Startup.cs?range=19-30)]</span></span>

<span data-ttu-id="f868f-179">如果应用正在运行`Development`环境中，则它允许使用 Visual Studio、 开发特定的错误页 （这通常不应运行在生产环境中） 和特殊的数据库错误中的"浏览器链接"功能所需的运行时支持（这提供了如何应用迁移，并因此只应在开发过程中） 的页。</span><span class="sxs-lookup"><span data-stu-id="f868f-179">If the app is running in a `Development` environment, then it enables the runtime support necessary to use the "BrowserLink" feature in Visual Studio, development-specific error pages (which typically should not be run in production) and special database error pages (which provide a way to apply migrations and should therefore only be used in development).</span></span> <span data-ttu-id="f868f-180">否则，如果在应用未运行在开发环境中，标准错误处理页面已配置为显示在任何未经处理的异常响应。</span><span class="sxs-lookup"><span data-stu-id="f868f-180">Otherwise, if the app is not running in a development environment, a standard error handling page is configured to be displayed in response to any unhandled exceptions.</span></span>

<span data-ttu-id="f868f-181">你可能需要确定哪些内容可以发送到客户端在运行时，具体取决于当前的环境。</span><span class="sxs-lookup"><span data-stu-id="f868f-181">You may need to determine which content to send to the client at runtime, depending on the current environment.</span></span> <span data-ttu-id="f868f-182">例如，在开发环境中你通常将提供非最小化脚本和样式表，能使调试变得更容易。</span><span class="sxs-lookup"><span data-stu-id="f868f-182">For example, in a development environment you generally serve non-minimized scripts and style sheets, which makes debugging easier.</span></span> <span data-ttu-id="f868f-183">生产和测试环境应提供缩减的版本和通常从 CDN。</span><span class="sxs-lookup"><span data-stu-id="f868f-183">Production and test environments should serve the minified versions and generally from a CDN.</span></span> <span data-ttu-id="f868f-184">你可以使用环境[标记帮助器](../mvc/views/tag-helpers/intro.md)。</span><span class="sxs-lookup"><span data-stu-id="f868f-184">You can do this using the Environment [tag helper](../mvc/views/tag-helpers/intro.md).</span></span> <span data-ttu-id="f868f-185">如果当前环境与使用指定的环境之一相匹配，环境标记帮助器仅将呈现其内容`names`属性。</span><span class="sxs-lookup"><span data-stu-id="f868f-185">The Environment tag helper will only render its contents if the current environment matches one of the environments specified using the `names` attribute.</span></span>

<span data-ttu-id="f868f-186">[!code-html[Main](environments/sample/src/Environments/Views/Shared/_Layout.cshtml?range=13-22)]</span><span class="sxs-lookup"><span data-stu-id="f868f-186">[!code-html[Main](environments/sample/src/Environments/Views/Shared/_Layout.cshtml?range=13-22)]</span></span>

<span data-ttu-id="f868f-187">若要开始使用你应用程序，请参阅在使用标记帮助程序[简介标记帮助程序](../mvc/views/tag-helpers/intro.md)。</span><span class="sxs-lookup"><span data-stu-id="f868f-187">To get started with using tag helpers in your application see [Introduction to Tag Helpers](../mvc/views/tag-helpers/intro.md).</span></span>

## <a name="startup-conventions"></a><span data-ttu-id="f868f-188">启动约定</span><span class="sxs-lookup"><span data-stu-id="f868f-188">Startup conventions</span></span>

<span data-ttu-id="f868f-189">ASP.NET 核心支持基于约定的配置基于当前的环境的应用程序的启动方法。</span><span class="sxs-lookup"><span data-stu-id="f868f-189">ASP.NET Core supports a convention-based approach to configuring an application's startup based on the current environment.</span></span> <span data-ttu-id="f868f-190">你可以以编程方式控制你的应用程序的行为方式根据哪些环境到它在中，它允许你创建和管理你自己的约定。</span><span class="sxs-lookup"><span data-stu-id="f868f-190">You can also programmatically control how your application behaves according to which environment it is in, allowing you to create and manage your own conventions.</span></span>

<span data-ttu-id="f868f-191">ASP.NET Core 应用程序启动时，`Startup`类用于启动应用程序，加载其配置设置等 ([了解有关 ASP.NET 启动](startup.md))。</span><span class="sxs-lookup"><span data-stu-id="f868f-191">When an ASP.NET Core application starts, the `Startup` class is used to bootstrap the application, load its configuration settings, etc. ([learn more about ASP.NET startup](startup.md)).</span></span> <span data-ttu-id="f868f-192">但是，如果存在的类名为`Startup{EnvironmentName}`(例如`StartupDevelopment`)，和`ASPNETCORE_ENVIRONMENT`环境变量与匹配该名称时，就在那时，`Startup`改为使用类。</span><span class="sxs-lookup"><span data-stu-id="f868f-192">However, if a class exists named `Startup{EnvironmentName}` (for example `StartupDevelopment`), and the `ASPNETCORE_ENVIRONMENT` environment variable matches that name, then that `Startup` class is used instead.</span></span> <span data-ttu-id="f868f-193">因此，你可以配置`Startup`开发，但有一个单独`StartupProduction`，将在生产环境中运行应用时使用。</span><span class="sxs-lookup"><span data-stu-id="f868f-193">Thus, you could configure `Startup` for development, but have a separate `StartupProduction` that would be used when the app is run in production.</span></span> <span data-ttu-id="f868f-194">反之亦然。</span><span class="sxs-lookup"><span data-stu-id="f868f-194">Or vice versa.</span></span>

> [!NOTE]
> <span data-ttu-id="f868f-195">调用`WebHostBuilder.UseStartup<TStartup>()`重写配置节。</span><span class="sxs-lookup"><span data-stu-id="f868f-195">Calling `WebHostBuilder.UseStartup<TStartup>()` overrides configuration sections.</span></span>

<span data-ttu-id="f868f-196">除了使用完全独立`Startup`类基于当前的环境中，你还可以调整应用程序中的配置方式`Startup`类。</span><span class="sxs-lookup"><span data-stu-id="f868f-196">In addition to using an entirely separate `Startup` class based on the current environment, you can also make adjustments to how the application is configured within a `Startup` class.</span></span> <span data-ttu-id="f868f-197">`Configure()`和`ConfigureServices()`方法支持类似于特定于环境的版本`Startup`类本身，窗体的`Configure{EnvironmentName}()`和`Configure{EnvironmentName}Services()`。</span><span class="sxs-lookup"><span data-stu-id="f868f-197">The `Configure()` and `ConfigureServices()` methods support environment-specific versions similar to the `Startup` class itself, of the form `Configure{EnvironmentName}()` and `Configure{EnvironmentName}Services()`.</span></span> <span data-ttu-id="f868f-198">如果定义一个方法`ConfigureDevelopment()`它将调用而不是`Configure()`当环境已设置为开发。</span><span class="sxs-lookup"><span data-stu-id="f868f-198">If you define a method `ConfigureDevelopment()` it will be called instead of `Configure()` when the environment is set to development.</span></span> <span data-ttu-id="f868f-199">同样，`ConfigureDevelopmentServices()`将而不是调用`ConfigureServices()`的同一环境中。</span><span class="sxs-lookup"><span data-stu-id="f868f-199">Likewise, `ConfigureDevelopmentServices()` would be called instead of `ConfigureServices()` in the same environment.</span></span>

## <a name="summary"></a><span data-ttu-id="f868f-200">摘要</span><span class="sxs-lookup"><span data-stu-id="f868f-200">Summary</span></span>

<span data-ttu-id="f868f-201">ASP.NET 内核提供了大量的功能和允许开发人员能够轻松地控制其应用程序在不同环境中的行为方式的约定。</span><span class="sxs-lookup"><span data-stu-id="f868f-201">ASP.NET Core provides a number of features and conventions that allow developers to easily control how their applications behave in different environments.</span></span> <span data-ttu-id="f868f-202">在发布应用程序从开发到过渡环境到生产环境时，环境变量集适当环境允许对优化调试、 测试或生产使用，根据需要对应用程序。</span><span class="sxs-lookup"><span data-stu-id="f868f-202">When publishing an application from development to staging to production, environment variables set appropriately for the environment allow for optimization of the application for debugging, testing, or production use, as appropriate.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f868f-203">其他资源</span><span class="sxs-lookup"><span data-stu-id="f868f-203">Additional Resources</span></span>

* [<span data-ttu-id="f868f-204">配置</span><span class="sxs-lookup"><span data-stu-id="f868f-204">Configuration</span></span>](configuration.md)

* [<span data-ttu-id="f868f-205">标记帮助器简介</span><span class="sxs-lookup"><span data-stu-id="f868f-205">Introduction to Tag Helpers</span></span>](../mvc/views/tag-helpers/intro.md)
