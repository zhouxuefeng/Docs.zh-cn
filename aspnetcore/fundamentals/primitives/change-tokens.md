---
title: "在 ASP.NET Core 检测与更改令牌更改"
author: guardrex
description: "了解如何使用更改令牌来跟踪更改。"
manager: wpickett
ms.author: riande
ms.date: 11/10/2017
ms.devlang: csharp
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: fundamentals/primitives/change-tokens
ms.openlocfilehash: a9479e3d676ed4dc880996a4a77de30d82b84cd5
ms.sourcegitcommit: 216dfac27542f10a79274a9ce60dc449e888ed20
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a>在 ASP.NET Core 检测与更改令牌更改

作者：[Luke Latham](https://github.com/guardrex)

A*更改令牌*是用于跟踪更改的通用、 低级别构造块。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/primitives/change-tokens/sample/)（[如何下载](xref:tutorials/index#how-to-download-a-sample)）

## <a name="ichangetoken-interface"></a>IChangeToken 接口

[IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken)传播已发生更改的通知。 `IChangeToken`驻留在[Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives)命名空间。 不使用的应用程序[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All/) metapackage，引用[Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/)项目文件中的 NuGet 包。

`IChangeToken`具有两个属性：

* [ActiveChangedCallbacks](/dotnet/api/microsoft.extensions.primitives.ichangetoken.activechangecallbacks)指示是否令牌主动引发回调。 如果`ActiveChangedCallbacks`设置为`false`，永远不会调用的回调，并且应用程序必须轮询`HasChanged`的更改。 还有可能适用于令牌，如果未发生任何更改或释放或禁用基础的更改侦听器永远不会取消。
* [HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged)获取一个值，该值指示是否发生了更改。

此接口具有一个方法， [RegisterChangeCallback (操作&lt;对象&gt;，对象)](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback)，这会注册令牌已发生更改时调用的回调。 `HasChanged`调用回调之前，必须先设置。

## <a name="changetoken-class"></a>ChangeToken 类

`ChangeToken`是静态的类，用于传播通知已发生更改。 `ChangeToken`驻留在[Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives)命名空间。 不使用的应用程序[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All/) metapackage，引用[Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/)项目文件中的 NuGet 包。

`ChangeToken` [OnChange (Func&lt;IChangeToken&gt;，操作)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action_)方法寄存器`Action`调用令牌发生更改时：
* `Func<IChangeToken>`生成令牌。
* `Action`令牌更改时调用。

`ChangeToken`具有[OnChange&lt;TState&gt;(Func&lt;IChangeToken&gt;，操作&lt;TState&gt;，TState)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange__1_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action___0____0_)重载采用额外`TState`参数传递到令牌使用者`Action`。

`OnChange`返回[IDisposable](/dotnet/api/system.idisposable)。 调用[释放](/dotnet/api/system.idisposable.dispose)停止侦听做进一步更改中的令牌，释放标记的资源。

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>ASP.NET 核心中的更改标记的示例使用

更改令牌使用 ASP.NET 核心监视对对象的更改的突出方面：

* 用于监视更改的文件， [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider)的[监视](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch)方法创建`IChangeToken`指定的文件或要监视的文件夹。
* `IChangeToken`令牌可以添加到要触发更改的缓存逐出缓存项。
* 有关`TOptions`将默认值更改[OptionsMonitor](/dotnet/api/microsoft.extensions.options.optionsmonitor-1)实现[IOptionsMonitor](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1)具有一个接受一个或多个重载[IOptionsChangeTokenSource](/dotnet/api/microsoft.extensions.options.ioptionschangetokensource-1)实例。 每个实例返回`IChangeToken`跟踪选项更改用于注册更改通知回调。

## <a name="monitoring-for-configuration-changes"></a>监视配置更改

默认情况下，使用 ASP.NET Core 模板[JSON 配置文件](xref:fundamentals/configuration/index#json-configuration)(*appsettings.json*， *appsettings。Development.json*，和*appsettings。Production.json*) 来加载应用程序配置设置。

这些文件被配置使用[AddJsonFile （IConfigurationBuilder、 字符串、 布尔值、 布尔值）](/dotnet/api/microsoft.extensions.configuration.jsonconfigurationextensions.addjsonfile?view=aspnetcore-2.0#Microsoft_Extensions_Configuration_JsonConfigurationExtensions_AddJsonFile_Microsoft_Extensions_Configuration_IConfigurationBuilder_System_String_System_Boolean_System_Boolean_)上的扩展方法[ConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.configurationbuilder)接受`reloadOnChange`参数 (ASP.NET核心 1.1 和更高版本）。 `reloadOnChange`指示是否在文件更改，应重新加载配置。 请参阅此设置在[WebHost](/dotnet/api/microsoft.aspnetcore.webhost)便捷方法[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) ([引用源](https://github.com/aspnet/MetaPackages/blob/rel/2.0.3/src/Microsoft.AspNetCore/WebHost.cs#L152-L193)):

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, reloadOnChange: true);
```

基于文件的配置由[FileConfigurationSource](/dotnet/api/microsoft.extensions.configuration.fileconfigurationsource)。 `FileConfigurationSource`使用[IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) ([引用源](https://github.com/aspnet/FileSystem/blob/patch/2.0.1/src/Microsoft.Extensions.FileProviders.Abstractions/IFileProvider.cs)) 若要监视的文件。

默认情况下，`IFileMonitor`由[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider) ([引用源](https://github.com/aspnet/Configuration/blob/patch/2.0.1/src/Microsoft.Extensions.Configuration.FileExtensions/FileConfigurationSource.cs#L82))，它使用[FileSystemWatcher](/dotnet/api/system.io.filesystemwatcher)监视配置文件更改。

示例应用程序演示了用于监视配置更改的两个实现。 如果任一*appsettings.json*环境版本的文件或文件更改，每个实现将执行自定义代码。 示例应用程序将消息写入控制台。

配置文件的`FileSystemWatcher`可以触发单个配置文件更改的多个令牌回调。 示例的实现防止此问题，通过检查配置文件上的文件哈希值。 检查文件哈希值可确保在运行的自定义代码之前至少一个配置文件已更改。 此示例使用 SHA1 哈希文件 (*Utilities/Utilities.cs*):

   [!code-csharp[Main](change-tokens/sample/Utilities/Utilities.cs?name=snippet1)]

   重试使用指数退让实现。 重试不存在，因为文件锁定可能会发生，暂时禁止计算一个新的哈希上的文件之一。

### <a name="simple-startup-change-token"></a>简单启动更改令牌

注册令牌使用者`Action`回调到配置重新加载令牌更改通知 (*Startup.cs*):

[!code-csharp[Main](change-tokens/sample/Startup.cs?name=snippet2)]

`config.GetReloadToken()`提供了令牌。 是回调`InvokeChanged`方法：

[!code-csharp[Main](change-tokens/sample/Startup.cs?name=snippet3)]

`state`的回调用来中传递`IHostingEnvironment`。 这可用于确定正确*appsettings* JSON 配置文件若要监视， *appsettings。&lt;环境&gt;.json*。 文件哈希值用于防止`WriteConsole`多次由于多个令牌的回调配置文件仅一次更改时才运行的语句。

此系统在运行，只要该应用程序正在运行并且无法由用户禁用。

### <a name="monitoring-configuration-changes-as-a-service"></a>作为一项服务的监视配置更改

此示例实现：

* 基本启动令牌监视。
* 监视作为一项服务。
* 一种机制来启用和禁用监视。

此示例建立`IConfigurationMonitor`接口 (*Extensions/ConfigurationMonitor.cs*):

[!code-csharp[Main](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet1)]

实现的类，构造函数`ConfigurationMonitor`，为更改通知注册回调：

[!code-csharp[Main](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()`提供该令牌。 `InvokeChanged`是一个回调方法。 `state`此实例中是一个字符串，描述的监视状态。 使用两个属性：

* `MonitoringEnabled`指示回调是否应运行其自定义代码。
* `CurrentState`描述在 UI 中使用的当前监视状态。

`InvokeChanged`方法类似于前面方法中，但它是：

* 不运行其代码，除非`MonitoringEnabled`是`true`。
* 集`CurrentState`属性字符串与记录代码运行时间的描述性消息。
* 说明当前`state`中其`WriteConsole`输出。

[!code-csharp[Main](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet3)]

实例`ConfigurationMonitor`注册中的服务为`ConfigureServices`的*Startup.cs*:

[!code-csharp[Main](change-tokens/sample/Startup.cs?name=snippet1)]

索引页提供对配置监视的用户控制。 实例`IConfigurationMonitor`注入到`IndexModel`:

[!code-csharp[Main](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet1)]

按钮启用和禁用监视：

[!code-cshtml[Main](change-tokens/sample/Pages/Index.cshtml?range=35)]

[!code-csharp[Main](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet2)]

当`OnPostStartMonitoring`是触发，启用了监视，并且会清除当前状态。 当`OnPostStopMonitoring`是触发，监视处于禁用状态，并且状态将设置以反映未进行监视。

## <a name="monitoring-cached-file-changes"></a>监视缓存的文件更改

文件内容可以是缓存内存中使用[IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)。 内存中缓存所述[内存中缓存](xref:performance/caching/memory)主题。 而无需采取其他步骤，如下面所述，实现*陈旧*如果源数据更改 （过时） 的数据从缓存返回。

不考虑缓存的源文件的状态时续订[相对过期机制](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration)段会导致过时的缓存数据。 数据的每个请求续订滑动过期时间，但该文件永远不会重新加载到缓存。 可能接收过时的内容受到使用该文件的缓存的内容的任何应用程序功能。

在文件缓存方案中使用更改令牌可防止缓存中的陈旧文件内容。 示例应用演示如何方法的实现。

此示例使用`GetFileContent`到：

* 返回文件内容。
* 实现使用指数退让到其中的文件锁定暂时防止文件被读取的封面情况下重试算法。

*Utilities/Utilities.cs*:

[!code-csharp[Main](change-tokens/sample/Utilities/Utilities.cs?name=snippet2)]

A`FileService`创建以处理查找缓存的文件。 `GetFileContent`方法调用的服务将尝试从内存中缓存中获取文件内容并将其返回给调用方 (*Services/FileService.cs*)。

如果没有找到缓存的内容，使用的缓存密钥，会采取以下操作：

1. 使用获取文件内容`GetFileContent`。
1. 从使用文件提供程序获取更改令牌[IFileProviders.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch)。 修改该文件时，会触发令牌的回调。
1. 文件内容进行缓存[相对过期机制](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration)段。 与连接的更改令牌[MemoryCacheEntryExtensions.AddExpirationToken](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.addexpirationtoken)逐出缓存项，如果文件发生更改时进行缓存。

[!code-csharp[Main](change-tokens/sample/Services/FileService.cs?name=snippet1)]

`FileService`在以及内存中缓存服务的服务容器中注册 (*Startup.cs*):

[!code-csharp[Main](change-tokens/sample/Startup.cs?name=snippet4)]

页模型加载该文件的内容使用服务 (*Pages/Index.cshtml.cs*):

[!code-csharp[Main](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>CompositeChangeToken 类

用于表示一个或多个`IChangeToken`中单个对象的实例使用[CompositeChangeToken](/dotnet/api/microsoft.extensions.primitives.compositechangetoken)类 ([引用源](https://github.com/aspnet/Common/blob/patch/2.0.1/src/Microsoft.Extensions.Primitives/CompositeChangeToken.cs))。

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        { 
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

`HasChanged`在复合令牌报告`true`如果任何表示令牌`HasChanged`是`true`。 `ActiveChangeCallbacks`在复合令牌报告`true`如果任何表示令牌`ActiveChangeCallbacks`是`true`。 如果多个并发更改事件发生，复合更改回调调用恰好一次。

## <a name="see-also"></a>请参阅

* [内存中缓存](xref:performance/caching/memory)
* [使用分布式缓存](xref:performance/caching/distributed)
* [检测更改令牌更改](xref:fundamentals/primitives/change-tokens)
* [响应缓存](xref:performance/caching/response)
* [响应缓存中间件](xref:performance/caching/middleware)
* [缓存标记帮助器](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)
* [分布式的缓存标记帮助器](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)
