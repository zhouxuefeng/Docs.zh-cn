---
title: "在 Windows 服务中的主机"
author: tdykstra
description: "了解如何托管的 ASP.NET Core 应用程序在 Windows 服务。"
keywords: "ASP.NET 核心，Windows 服务承载"
ms.author: tdykstra
manager: wpickett
ms.date: 03/30/2017
ms.topic: article
ms.assetid: d9a65066-d7cb-47df-b046-64629c4d2c6f
ms.technology: aspnet
ms.prod: aspnet-core
uid: hosting/windows-service
ms.openlocfilehash: a6d1acf5ab8f40b0b4d487a6f34cd83d13907852
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="host-an-aspnet-core-app-in-a-windows-service"></a>ASP.NET Core 应用托管在 Windows 服务

通过[Tom Dykstra](https://github.com/tdykstra)

当不使用 IIS 承载 ASP.NET Core 应用在 Windows 上的推荐的方式是运行[Windows 服务](https://docs.microsoft.com/dotnet/framework/windows-services/introduction-to-windows-service-applications)。 这样就可以自动启动后重新启动和崩溃，而无需等待某人登录。

[查看或下载的示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/hosting/windows-service/sample)([如何下载](xref:tutorials/index#how-to-download-a-sample))。 请参阅[后续步骤](#next-steps)部分以了解有关如何运行它。

## <a name="prerequisites"></a>先决条件

* 应用程序必须在.NET Framework 运行时上运行。  在*.csproj*文件中，指定为相应值[TargetFramework](https://docs.microsoft.com/nuget/schema/target-frameworks)和[RuntimeIdentifier](https://docs.microsoft.com/dotnet/articles/core/rid-catalog)。 以下是一个示例：

  [!code-xml[](windows-service/sample/AspNetCoreService.csproj?range=3-6)]

  当在 Visual Studio 中创建项目，请使用**ASP.NET 核心应用程序 (.NET Framework)**模板。

* 如果应用程序将从 internet （而不仅仅是从内部网络） 中获取请求，它必须使用[WebListener](xref:fundamentals/servers/weblistener) web 服务器而非[Kestrel](xref:fundamentals/servers/kestrel)。  边缘部署，必须向 IIS 使用 kestrel。  有关详细信息，请参阅[何时结合使用 Kestrel 和反向代理](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy)。

## <a name="getting-started"></a>入门

本部分介绍将现有的 ASP.NET Core 项目设置为在服务中运行所需的最小更改。

* 安装 NuGet 程序包[Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices/)。

* 进行中的以下更改`Program.Main`:
  
  * 调用`host.RunAsService`而不是`host.Run`。
  
  * 如果你的代码调用`UseContentRoot`，使用而不是发布位置的路径`Directory.GetCurrentDirectory()` 
  
  [!code-csharp[](windows-service/sample/Program.cs?name=ServiceOnly&highlight=3-4,8,14)]

* 发布应用程序的文件夹。

  使用[dotnet 发布](https://docs.microsoft.com/dotnet/articles/core/tools/dotnet-publish)或[Visual Studio 发布配置文件](xref:publishing/web-publishing-vs)，它发布到的文件夹。

* 通过创建和启动服务测试。

  打开管理员命令提示符窗口，以使用[sc.exe](https://technet.microsoft.com/library/bb490995)命令行工具来创建和启动服务。  
  
  如果你的服务 MyService 提供的名称，你应用程序发布到`c:\svc`，并在应用程序名为 AspNetCoreService，命令将如下所示：

  ```console
  sc create MyService binPath="C:\Svc\AspNetCoreService.exe"
  sc start MyService
  ```
  `binPath`值是你的应用的可执行文件，包括可执行文件名本身的路径。

  ![控制台窗口创建并启动示例](windows-service/_static/create-start.png)

  在这些命令完成后，你可以浏览到你作为控制台应用程序的运行时所在的路径 (默认情况下， `http://localhost:5000`)

  ![服务中运行](windows-service/_static/running-in-service.png)


## <a name="provide-a-way-to-run-outside-of-a-service"></a>提供服务的外部运行的方法

很容易地测试和调试运行时外部服务，因此通常将调用的代码添加`host.RunAsService`仅在某些情况下。  例如，你可作为运行控制台应用程序如果收到`--console`命令行自变量，或如果附加调试器。

[!code-csharp[](windows-service/sample/Program.cs?name=ServiceOrConsole)]

## <a name="handle-stopping-and-starting-events"></a>处理停止和启动事件

如果你想要处理`OnStarting`， `OnStarted`，和`OnStopping`事件，进行以下其他更改：

* 创建一个从 `WebHostService` 派生的类。

  [!code-csharp[](windows-service/sample/CustomWebHostService.cs?name=NoLogging)]

* 创建扩展方法`IWebHost`通过您的自定义`WebHostService`到`ServiceBase.Run`。

  [!code-csharp[](windows-service/sample/WebHostServiceExtensions.cs?name=ExtensionsClass)]

* 在`Program.Main`更改调用新的扩展方法，而不是`host.RunAsService`。

  [!code-csharp[](windows-service/sample/Program.cs?name=HandleStopStart&highlight=26)]

如果您的自定义`WebHostService`代码需要从 （例如记录器） 的依赖关系注入获取服务，你可以获取从`Services`属性`IWebHost`。

[!code-csharp[](windows-service/sample/CustomWebHostService.cs?name=Logging&highlight=7)]

## <a name="next-steps"></a>后续步骤

[示例应用程序](https://github.com/aspnet/Docs/tree/master/aspnetcore/hosting/windows-service/sample)附带此文章是已被修改，前面的代码示例中所示的简单 MVC web 应用。  若要在服务中运行它，请执行以下步骤：

* 将发布到*c:\svc*。

* 打开管理员窗口。

* 输入以下命令：

  ```console
  sc create MyService binPath="c:\svc\aspnetcoreservice.exe"
  sc start MyService
  ```

  * 在浏览器中，转到 http://localhost:5000/ 以验证它正在运行。

如果不按预期运行时服务中运行启动应用程序，以使错误消息可访问的捷径是添加日志记录提供程序，例如[Windows 事件日志提供程序](xref:fundamentals/logging/index#eventlog)。

## <a name="acknowledgments"></a>确认

撰写本文时的已发布的源的帮助。 最早和其中最有用了这些：

* [作为 Windows 服务承载 ASP.NET 核心](https://stackoverflow.com/questions/37346383/hosting-asp-net-core-as-windows-service/37464074)
* [如何托管 Windows 服务中将 ASP.NET 核心](https://dotnetthoughts.net/how-to-host-your-aspnet-core-in-a-windows-service/)
