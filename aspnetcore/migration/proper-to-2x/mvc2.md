---
title: "迁移 ASP.NET ASP.NET 核心 2.0"
author: isaac2004
description: "本参考文档提供了从现有的 ASP.NET MVC 或 Web API 应用程序迁移到 ASP.NET Core 2.0 的指南。"
keywords: "ASP.NET Core, MVC, 迁移"
ms.author: scaddie
manager: wpickett
ms.date: 08/27/2017
ms.topic: article
ms.assetid: 3155cc9e-d0c9-424b-886c-35c0ec6f9f4e
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/mvc2
ms.openlocfilehash: e0691b276b63ee12d3163ac48d1392696fb97aa6
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="migrating-from-aspnet-to-aspnet-core-20"></a>迁移 ASP.NET ASP.NET 核心 2.0

作者：[Isaac Levin](https://isaaclevin.com)

本文可作为从 ASP.NET 应用程序迁移到 ASP.NET Core 2.0 的参考指南。

## <a name="prerequisites"></a>先决条件

* [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 或更高版本。

## <a name="target-frameworks"></a>目标框架
ASP.NET Core 2.0 项目为开发人员提供了面向 .NET Core、.NET Framework 或同时面向这两者的灵活性。 若要确定最合适的目标框架，请参阅[为服务器应用选择 .NET Core 或 .NET Framework](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server)。

面向 .NET Framework 时，项目需要引用单个 NuGet 包。

得益于有 ASP.NET Core 2.0 [元包](xref:fundamentals/metapackage)，面向 .NET Core 时可以避免进行大量的显式包引用。 在项目中安装 `Microsoft.AspNetCore.All` 元包：

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
</ItemGroup>
```

使用此元包时，应用不会部署元包中引用的任何包。 .NET Core 运行时存储中包含这些资产，并且为提高性能已对它们进行预编译。 请参阅 [ASP.NET Core 2.x 的 Microsoft.AspNetCore.All 元包](xref:fundamentals/metapackage)了解详细信息。

## <a name="project-structure-differences"></a>项目结构差异
ASP.NET Core 中简化了 .csproj 文件格式。 下面是一些显著的更改：
- 若要将文件作为项目的组成部分，无需显示包含它们。 服务于大型团队时，这可减少出现 XML 合并冲突的风险。
- 没有对其他项目的基于 GUID 的引用，这可以提高文件的可读性。
- 无需在 Visual Studio 中卸载文件即可对它进行编辑：

    ![Visual Studio 2017 中的“编辑 CSPROJ”上下文菜单选项](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a>Global.asax 文件替换
ASP.NET Core 引入了启动应用的新机制。 ASP.NET 应用程序的入口点是 Global.asax 文件。 路由配置及筛选器和区域注册等任务在 Global.asax 文件中进行处理。

[!code-csharp[Main](samples/globalasax-sample.cs)]

此方法会将应用程序和应用程序要部署到的服务器耦合在一起，并且它们的耦合方式会干扰实现。 为了将它们分离，引入了 [OWIN](http://owin.org/) 来提供一种更为简便的同时使用多个框架的方法。 OWIN 提供了一个管道，可以只添加所需的模块。 托管环境使用 [Startup](xref:fundamentals/startup) 函数配置服务和应用的请求管道。 `Startup` 在应用程序中注册一组中间件。 对于每个请求，应用程序使用现有处理程序集的链接列表的头指针调用各个中间件组件。 每个中间件组件可以向请求处理管道添加一个或多个处理程序。 此过程通过返回对作为列表新头的处理程序的引用来完成。 每个处理程序负责记住并调用列表中的下一个处理程序。 使用 ASP.NET Core 时，应用程序的入口点是 `Startup`，不再具有 Global.asax 的依赖关系。 结合使用 OWIN 和 .NET Framework 时，使用的管道应如下所示：

[!code-csharp[Main](samples/webapi-owin.cs)]

这会配置默认路由，默认为 XmlSerialization 而不是 Json。 根据需要向此管道添加其他中间件（加载服务、配置设置、静态文件等）。

ASP.NET Core 使用相似的方法，但是不依赖 OWIN 处理条目。 相反，它通过 Program.cs `Main` 方法（类似于控制台应用程序）完成该操作，并加载 `Startup`。

[!code-csharp[Main](samples/program.cs)]

`Startup` 必须包含 `Configure` 方法。 在 `Configure` 中，向管道添加必要的中间件。 在下列示例（位于默认的网站模板）中，使用了多个扩展方法为管道配置对以下内容的支持：

* [BrowserLink](http://vswebessentials.com/features/browserlink)
* 错误页
* 静态文件
* ASP.NET Core MVC
* 标识

[!code-csharp[Main](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

现在主机和应用程序已分离，这样将来就可以灵活地迁移到其他平台。

注意：若要获取 ASP.NET Core Startup 和中间件的更深入的参考信息，请参阅 [ASP.NET Core 中的 Startup](xref:fundamentals/startup)

## <a name="storing-configurations"></a>存储配置
ASP.NET 支持存储设置。 这些设置可用于支持应用程序已部署到的环境（以此用途为例）。 常见做法是将所有的自定义键值对存储在 Web.config 文件的 `<appSettings>` 部分中：

[!code-xml[Main](samples/webconfig-sample.xml)]

应用程序使用 `System.Configuration` 命名空间中的 `ConfigurationManager.AppSettings` 集合读取这些设置：

[!code-csharp[Main](samples/read-webconfig.cs)]

ASP.NET Core 可以将应用程序的配置数据存储在任何文件中，并可在启动中间件的过程中加载它们。 项目模板中使用的默认文件是 appsettings.json：

[!code-json[Main](samples/appsettings-sample.json)]

将此文件加载到应用程序内的 `IConfiguration` 的实例的过程在 Startup.cs 中完成：

[!code-csharp[Main](samples/startup-builder.cs)]

应用读取 `Configuration` 来获得这些设置：

[!code-csharp[Main](samples/read-appsettings.cs)]

此方法有扩展项，它们可使此过程更加可靠，例如使用[依存关系注入](xref:fundamentals/dependency-injection) (DI) 来加载使用这些值的服务。 DI 方法提供了一组强类型的配置对象。

````csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
````

注意：若要获取 ASP.NET Core 配置的更深入的参考信息，请参阅 [ASP.NET Core 中的配置](xref:fundamentals/configuration)。

## <a name="native-dependency-injection"></a>本机依存关系注入
生成大型可缩放应用程序时，一个重要的目标是将组件和服务松散耦合。 [依存关系注入](xref:fundamentals/dependency-injection)是用于实现此目标的热门技术，并且它是 ASP.NET Core 的本机组件。

在 ASP.NET 应用程序中，开发人员依赖第三方库实现依存关系注入。 其中的一个库是 Microsoft 模式和做法提供的 [Unity](https://github.com/unitycontainer/unity)。 

实现打包 `UnityContainer` 的 `IDependencyResolver` 是使用 Unity 设置依存关系注入的一个示例：

[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample8.cs)]

创建 `UnityContainer` 的实例，注册服务，然后将 `HttpConfiguration` 的依赖关系解析程序设置为容器的 `UnityResolver` 新实例：

[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample9.cs)]

在必要时注入 `IProductRepository`：

[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample5.cs)]

由于依存关系注入是 ASP.NET Core 的组成部分，因此可以在 Startup.cs 的 `ConfigureServices` 方法中添加你的服务：

[!code-csharp[Main](samples/configure-services.cs)]

可在任意位置注入存储库，Unity 亦是如此。

注意：若要获取 ASP.NET Core 中的依存关系注入的深入的参考信息，请参阅 [ASP.NET Core 中的依存关系注入](xref:fundamentals/dependency-injection#replacing-the-default-services-container)

## <a name="serving-static-files"></a>提供静态文件
Web 开发的一个重要环节是提供客户端静态资产的功能。 HTML、CSS、Javascript 和图像是最常见的静态文件示例。 这些文件需要保存在应用（或 CDN）的发布位置中，并且需要引用它们，以便请求可以加载这些文件。 在 ASP.NET Core 中，此过程发生了变化。

在 ASP.NET 中，静态文件存储在各种目录中，并在视图中进行引用。

在 ASP.NET Core 中，静态文件存储在“Web 根”（&lt;内容根&gt;/wwwroot）中，除非另有配置。 通过从 `Startup.Configure` 调用 `UseStaticFiles` 扩展方法将这些文件加载到请求管道中：

[!code-csharp[Main](../../fundamentals/static-files/sample/StartupStaticFiles.cs?highlight=3&name=snippet1)]

注意：如果面向 .NET Framework，则安装 NuGet 包 `Microsoft.AspNetCore.StaticFiles`。

例如，可以通过浏览器从类似 `http://<app>/images/<imageFileName>` 的位置访问 wwwroot/images 文件夹中的图像资产。

注意：若要获取在 ASP.NET Core 中提供静态文件的更深入的参考信息，请参阅[关于在 ASP.NET Core 中使用静态文件的说明](xref:fundamentals/static-files)。

## <a name="additional-resources"></a>其他资源
* [将库移植到 .NET Core](https://docs.microsoft.com/dotnet/core/porting/libraries)
