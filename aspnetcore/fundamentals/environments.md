---
title: "使用 ASP.NET Core 中的多个环境"
author: ardalis
description: "了解如何 ASP.NET Core 提供支持用于跨多个环境中控制应用行为。"
keywords: "ASP.NET 核心，环境设置，ASPNETCORE_ENVIRONMENT"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: b5bba985-be12-4464-9a01-df3599b2a6f1
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/environments
ms.openlocfilehash: 054b3e9f1e2bcfe1e4a75eca4d9dc6326ee6e44f
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2017
---
# <a name="working-with-multiple-environments"></a>使用多个环境

通过[Steve Smith](https://ardalis.com/)

ASP.NET Core 提供用于跨多个环境，如开发、 过渡和生产控制应用行为的支持。 使用环境变量以指示运行时环境中，允许应用程序配置为该环境。

[查看或下载的示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/environments/sample)([如何下载](xref:tutorials/index#how-to-download-a-sample))

## <a name="development-staging-production"></a>开发，暂存生产

ASP.NET 核心引用特定[环境变量](https://github.com/aspnet/Home/wiki)，`ASPNETCORE_ENVIRONMENT`来描述中当前运行应用程序的环境。 可以设置此变量您喜欢的任何值，而三个值由约定： `Development`， `Staging`，和`Production`。 你会发现以下示例中使用的值和 ASP.NET Core 提供模板。

当前的环境设置可以检测到以编程方式从应用程序中。 此外，你可以使用环境[标记帮助器](../mvc/views/tag-helpers/index.md)包括中的某些部分你[视图](../mvc/views/index.md)基于当前的应用程序环境。

注意： 在 Windows 和 macOS 上，指定的环境名称是区分大小写。 是否将变量设置为`Development`或`development`或`DEVELOPMENT`结果将是相同的。 但是，Linux 是**区分大小写**默认情况下的操作系统。 环境变量、 文件名和设置需要区分大小写。

### <a name="development"></a>开发

这应该是在开发应用程序时使用的环境。 它通常用于以启用你不想要应用在生产中，如运行时是可用的功能[开发人员异常页](xref:fundamentals/error-handling#the-developer-exception-page)。

如果你使用 Visual Studio，可以在你的项目的调试配置文件中配置环境。 调试配置文件指定[服务器](xref:fundamentals/servers/index)要设置启动应用程序和任何环境变量时要使用。 你的项目可以具有不同的设置环境变量的多个调试配置文件。 通过使用管理这些配置文件**调试**web 应用程序项目的选项卡**属性**菜单。 在项目属性中设置的值保留在*launchSettings.json*文件，并且你还可以通过直接编辑该文件来配置配置文件。

IIS Express 的配置文件如下所示：

![项目属性设置环境变量](environments/_static/project-properties-debug.png)

下面是`launchSettings.json`包括的配置文件的文件`Development`和`Staging`:

[!code-json[Main](../fundamentals/environments/sample/src/Environments/Properties/launchSettings.json?highlight=15,22)]

对项目配置文件所做更改可能会生效，直到重新启动使用的 web 服务器 （具体而言，Kestrel 必须重新启动之前它将检测到其环境所做的更改）。

>[!WARNING]
> 环境变量存储在*launchSettings.json*未以任何方式进行保护和如果你使用一个将是你的项目，源代码存储库的一部分。 **永远不会存储凭据或此文件中的其他机密数据。** 如果你需要一个位置来存储此类数据，使用*机密 Manager*工具中所述[安全存储在开发过程中的应用程序机密](../security/app-secrets.md#security-app-secrets)。

### <a name="staging"></a>过渡

按照约定，`Staging`环境是用于在部署到生产环境之前进行最终测试预生产环境。 理想情况下，其物理特征应镜像的生产环境，以便在生产环境中可能出现任何问题发生在过渡环境中，其中他们可以进行寻址而不会影响用户的第一次。

### <a name="production"></a>生产

`Production`环境是应用程序运行时实时的环境和最终用户使用。 此环境应配置为最大程度提高安全性、 性能和应用程序的可靠性。 将不同于开发生产环境可能具有一些常见设置包括：

* 打开缓存

* 确保客户端的所有资源捆绑在一起，缩减的并可能从 CDN 提供

* 请关闭诊断 ErrorPages

* 开启友好错误页

* 启用日志记录和监视生产 (例如， [Application Insights](https://azure.microsoft.com/documentation/articles/app-insights-asp-net-five/))

这不是要作为的完整列表。 若要避免分散在你的应用程序中的不同部分的环境检查最好。 相反，建议的方法是执行内应用程序的此类检查`Startup`类尽可能

## <a name="setting-the-environment"></a>将环境设置

将环境设置的方法取决于操作系统。

### <a name="windows"></a>Windows
若要设置`ASPNETCORE_ENVIRONMENT`当前的会话中，如果应用程序将启动使用`dotnet run`，可以使用以下命令

**命令行**
```
set ASPNETCORE_ENVIRONMENT=Development
```
**PowerShell**
```
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

这些命令将仅为当前窗口的生效。 当窗口已关闭时，ASPNETCORE_ENVIRONMENT 设置将恢复为默认设置或计算机的值。 为了上 Windows 打开全局设置的值**控制面板** > **系统** > **高级系统设置**和添加或编辑`ASPNETCORE_ENVIRONMENT`值。

![高级属性的系统](environments/_static/systemsetting_environment.png)

![ASPNET 核心环境变量](environments/_static/windows_aspnetcore_environment.png) 

**web.config**

请参阅*设置环境变量*部分[ASP.NET 核心模块配置引用](xref:hosting/aspnet-core-module#setting-environment-variables)主题。

**每个 IIS 应用程序池**

如果需要为在独立应用程序池中运行的单个应用设置环境变量（IIS 10.0+ 中支持此操作），请参阅 IIS 参考文档的[环境变量 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题中的“AppCmd.exe 命令”部分。

### <a name="macos"></a>macOS
设置 macOS 的当前环境，可以进行串联时运行该应用程序;

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```
也可以使用`export`若要在运行应用程序之前设置它。

```bash
export ASPNETCORE_ENVIRONMENT=Development
``` 
设置计算机级别的环境变量*.bashrc*或*.bash_profile*文件。 使用任何文本编辑器编辑该文件并添加以下语句。

```
export ASPNETCORE_ENVIRONMENT=Development
```  

### <a name="linux"></a>Linux
对于 Linux 发行版本，使用`export`基于会话的变量设置在命令行命令和*bash_profile*机级别的环境设置的文件。

## <a name="determining-the-environment-at-runtime"></a>确定在运行时环境

`IHostingEnvironment`服务提供用于使用环境的核心抽象。 此服务是前提是 asp.net 的承载层，并且可以被注入到你的启动逻辑通过[依赖关系注入](dependency-injection.md)。 ASP.NET 核心网站模板在 Visual Studio 中的使用这种方法来加载特定于环境的配置文件 （如果存在） 并自定义应用程序的错误处理设置。 在这两种情况下，此行为通过引用当前指定的环境，通过调用`EnvironmentName`或`IsEnvironment`的实例上`IHostingEnvironment`传递到相应的方法。

> [!NOTE]
> 如果你需要检查应用程序是否正在运行在特定环境中，使用`env.IsEnvironment("environmentname")`由于它正确将忽略大小写 (而不是检查是否`env.EnvironmentName == "Development"`例如)。

例如，可以在你配置的方法中使用下面的代码以设置环境特定的错误处理：

[!code-csharp[Main](environments/sample/src/Environments/Startup.cs?range=19-30)]

如果应用正在运行`Development`环境中，则它允许使用 Visual Studio、 开发特定的错误页 （这通常不应运行在生产环境中） 和特殊的数据库错误中的"浏览器链接"功能所需的运行时支持（这提供了如何应用迁移，并因此只应在开发过程中） 的页。 否则，如果在应用未运行在开发环境中，标准错误处理页面已配置为显示在任何未经处理的异常响应。

你可能需要确定哪些内容可以发送到客户端在运行时，具体取决于当前的环境。 例如，在开发环境中你通常将提供非最小化脚本和样式表，能使调试变得更容易。 生产和测试环境应提供缩减的版本和通常从 CDN。 你可以使用环境[标记帮助器](../mvc/views/tag-helpers/intro.md)。 如果当前环境与使用指定的环境之一相匹配，环境标记帮助器仅将呈现其内容`names`属性。

[!code-html[Main](environments/sample/src/Environments/Views/Shared/_Layout.cshtml?range=13-22)]

若要开始使用你应用程序，请参阅在使用标记帮助程序[简介标记帮助程序](../mvc/views/tag-helpers/intro.md)。

## <a name="startup-conventions"></a>启动约定

ASP.NET 核心支持基于约定的配置基于当前的环境的应用程序的启动方法。 你可以以编程方式控制你的应用程序的行为方式根据哪些环境到它在中，它允许你创建和管理你自己的约定。

ASP.NET Core 应用程序启动时，`Startup`类用于启动应用程序，加载其配置设置等 ([了解有关 ASP.NET 启动](startup.md))。 但是，如果存在的类名为`Startup{EnvironmentName}`(例如`StartupDevelopment`)，和`ASPNETCORE_ENVIRONMENT`环境变量与匹配该名称时，就在那时，`Startup`改为使用类。 因此，你可以配置`Startup`开发，但有一个单独`StartupProduction`，将在生产环境中运行应用时使用。 反之亦然。

> [!NOTE]
> 调用`WebHostBuilder.UseStartup<TStartup>()`重写配置节。

除了使用完全独立`Startup`类基于当前的环境中，你还可以调整应用程序中的配置方式`Startup`类。 `Configure()`和`ConfigureServices()`方法支持类似于特定于环境的版本`Startup`类本身，窗体的`Configure{EnvironmentName}()`和`Configure{EnvironmentName}Services()`。 如果定义一个方法`ConfigureDevelopment()`它将调用而不是`Configure()`当环境已设置为开发。 同样，`ConfigureDevelopmentServices()`将而不是调用`ConfigureServices()`的同一环境中。

## <a name="summary"></a>摘要

ASP.NET 内核提供了大量的功能和允许开发人员能够轻松地控制其应用程序在不同环境中的行为方式的约定。 在发布应用程序从开发到过渡环境到生产环境时，环境变量集适当环境允许对优化调试、 测试或生产使用，根据需要对应用程序。

## <a name="additional-resources"></a>其他资源

* [配置](configuration.md)

* [标记帮助程序简介](../mvc/views/tag-helpers/intro.md)
