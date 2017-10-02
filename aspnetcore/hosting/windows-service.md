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
ms.openlocfilehash: ca3b98f0b0405fcd5751cb7d9bc7a40257739084
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2017
---
# <a name="host-an-aspnet-core-app-in-a-windows-service"></a><span data-ttu-id="fea8c-104">ASP.NET Core 应用托管在 Windows 服务</span><span class="sxs-lookup"><span data-stu-id="fea8c-104">Host an ASP.NET Core app in a Windows Service</span></span>

<span data-ttu-id="fea8c-105">通过[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="fea8c-105">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="fea8c-106">当不使用 IIS 承载 ASP.NET Core 应用在 Windows 上的推荐的方式是运行[Windows 服务](https://docs.microsoft.com/dotnet/framework/windows-services/introduction-to-windows-service-applications)。</span><span class="sxs-lookup"><span data-stu-id="fea8c-106">The recommended way to host an ASP.NET Core app on Windows when you don't use IIS is to run it in a [Windows Service](https://docs.microsoft.com/dotnet/framework/windows-services/introduction-to-windows-service-applications).</span></span> <span data-ttu-id="fea8c-107">这样就可以自动启动后重新启动和崩溃，而无需等待某人登录。</span><span class="sxs-lookup"><span data-stu-id="fea8c-107">That way it can automatically start after reboots and crashes, without waiting for someone to log in.</span></span>

<span data-ttu-id="fea8c-108">[查看或下载的示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/hosting/windows-service/sample)([如何下载](xref:tutorials/index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="fea8c-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/hosting/windows-service/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample)).</span></span> <span data-ttu-id="fea8c-109">请参阅[后续步骤](#next-steps)部分以了解有关如何运行它。</span><span class="sxs-lookup"><span data-stu-id="fea8c-109">See the [Next Steps](#next-steps) section for instructions on how to run it.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fea8c-110">先决条件</span><span class="sxs-lookup"><span data-stu-id="fea8c-110">Prerequisites</span></span>

* <span data-ttu-id="fea8c-111">应用程序必须在.NET framework 运行时上运行。</span><span class="sxs-lookup"><span data-stu-id="fea8c-111">The app must run on the .NET framework runtime.</span></span>  <span data-ttu-id="fea8c-112">在*.csproj*文件中，指定为相应值[TargetFramework](https://docs.microsoft.com/nuget/schema/target-frameworks)和[RuntimeIdentifier](https://docs.microsoft.com/dotnet/articles/core/rid-catalog)。</span><span class="sxs-lookup"><span data-stu-id="fea8c-112">In the *.csproj* file, specify appropriate values for [TargetFramework](https://docs.microsoft.com/nuget/schema/target-frameworks) and [RuntimeIdentifier](https://docs.microsoft.com/dotnet/articles/core/rid-catalog).</span></span> <span data-ttu-id="fea8c-113">以下是一个示例：</span><span class="sxs-lookup"><span data-stu-id="fea8c-113">Here's an example:</span></span>

  [!code-xml[](windows-service/sample/AspNetCoreService.csproj?range=3-6)]

  <span data-ttu-id="fea8c-114">当在 Visual Studio 中创建项目，请使用**ASP.NET 核心应用程序 (.NET Framework)**模板。</span><span class="sxs-lookup"><span data-stu-id="fea8c-114">When creating a project in Visual Studio, use the **ASP.NET Core Application (.NET Framework)** template.</span></span>

* <span data-ttu-id="fea8c-115">如果应用程序将从 internet （而不仅仅是从内部网络） 中获取请求，它必须使用[WebListener](xref:fundamentals/servers/weblistener) web 服务器而非[Kestrel](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="fea8c-115">If the app will get requests from the internet (not just from an internal network), it must use the [WebListener](xref:fundamentals/servers/weblistener) web server rather than [Kestrel](xref:fundamentals/servers/kestrel).</span></span>  <span data-ttu-id="fea8c-116">边缘部署，必须向 IIS 使用 kestrel。</span><span class="sxs-lookup"><span data-stu-id="fea8c-116">Kestrel must be used with IIS for edge deployments.</span></span>  <span data-ttu-id="fea8c-117">有关详细信息，请参阅[何时结合使用 Kestrel 和反向代理](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy)。</span><span class="sxs-lookup"><span data-stu-id="fea8c-117">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="getting-started"></a><span data-ttu-id="fea8c-118">入门</span><span class="sxs-lookup"><span data-stu-id="fea8c-118">Getting started</span></span>

<span data-ttu-id="fea8c-119">本部分介绍将现有的 ASP.NET Core 项目设置为在服务中运行所需的最小更改。</span><span class="sxs-lookup"><span data-stu-id="fea8c-119">This section explains the minimum changes required to set up an existing ASP.NET Core project to run in a service.</span></span>

* <span data-ttu-id="fea8c-120">安装 NuGet 程序包[Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices/)。</span><span class="sxs-lookup"><span data-stu-id="fea8c-120">Install the NuGet package [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices/).</span></span>

* <span data-ttu-id="fea8c-121">进行中的以下更改`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="fea8c-121">Make the following changes in `Program.Main`:</span></span>
  
  * <span data-ttu-id="fea8c-122">调用`host.RunAsService`而不是`host.Run`。</span><span class="sxs-lookup"><span data-stu-id="fea8c-122">Call `host.RunAsService` instead of `host.Run`.</span></span>
  
  * <span data-ttu-id="fea8c-123">如果你的代码调用`UseContentRoot`，使用而不是发布位置的路径`Directory.GetCurrentDirectory()`</span><span class="sxs-lookup"><span data-stu-id="fea8c-123">If your code calls `UseContentRoot`, use a path to the publish location instead of `Directory.GetCurrentDirectory()`</span></span> 
  
  [!code-csharp[](windows-service/sample/Program.cs?name=ServiceOnly&highlight=3-4,8,14)]

* <span data-ttu-id="fea8c-124">发布应用程序的文件夹。</span><span class="sxs-lookup"><span data-stu-id="fea8c-124">Publish the application to a folder.</span></span>

  <span data-ttu-id="fea8c-125">使用[dotnet 发布](https://docs.microsoft.com/dotnet/articles/core/tools/dotnet-publish)或[Visual Studio 发布配置文件](xref:publishing/web-publishing-vs)，它发布到的文件夹。</span><span class="sxs-lookup"><span data-stu-id="fea8c-125">Use [dotnet publish](https://docs.microsoft.com/dotnet/articles/core/tools/dotnet-publish) or a [Visual Studio publish profile](xref:publishing/web-publishing-vs) that publishes to a folder.</span></span>

* <span data-ttu-id="fea8c-126">通过创建和启动服务测试。</span><span class="sxs-lookup"><span data-stu-id="fea8c-126">Test by creating and starting the service.</span></span>

  <span data-ttu-id="fea8c-127">打开管理员命令提示符窗口，以使用[sc.exe](https://technet.microsoft.com/library/bb490995)命令行工具来创建和启动服务。</span><span class="sxs-lookup"><span data-stu-id="fea8c-127">Open an administrator command prompt window to use the [sc.exe](https://technet.microsoft.com/library/bb490995) command-line tool to create and start a service.</span></span>  
  
  <span data-ttu-id="fea8c-128">如果你的服务 MyService 提供的名称，你应用程序发布到`c:\svc`，并在应用程序名为 AspNetCoreService，命令将如下所示：</span><span class="sxs-lookup"><span data-stu-id="fea8c-128">If you name your service MyService, you publish your app to `c:\svc`, and the app itself is named AspNetCoreService, the commands would look like this:</span></span>

  ```console
  sc create MyService binPath="C:\Svc\AspNetCoreService.exe"
  sc start MyService
  ```
  <span data-ttu-id="fea8c-129">`binPath`值是你的应用的可执行文件，包括可执行文件名本身的路径。</span><span class="sxs-lookup"><span data-stu-id="fea8c-129">The `binPath` value is the path to your app's executable, including the executable filename itself.</span></span>

  ![控制台窗口创建并启动示例](windows-service/_static/create-start.png)

  <span data-ttu-id="fea8c-131">在这些命令完成后，你可以浏览到你作为控制台应用程序的运行时所在的路径 (默认情况下， `http://localhost:5000`)</span><span class="sxs-lookup"><span data-stu-id="fea8c-131">When these commands finish, you can browse to the same path as when you run as a console app (by default, `http://localhost:5000`)</span></span>

  ![服务中运行](windows-service/_static/running-in-service.png)


## <a name="provide-a-way-to-run-outside-of-a-service"></a><span data-ttu-id="fea8c-133">提供服务的外部运行的方法</span><span class="sxs-lookup"><span data-stu-id="fea8c-133">Provide a way to run outside of a service</span></span>

<span data-ttu-id="fea8c-134">很容易地测试和调试运行时外部服务，因此通常将调用的代码添加`host.RunAsService`仅在某些情况下。</span><span class="sxs-lookup"><span data-stu-id="fea8c-134">It's easier to test and debug when you're running outside of a service, so it's customary to add code that calls `host.RunAsService` only under certain conditions.</span></span>  <span data-ttu-id="fea8c-135">例如，你可作为运行控制台应用程序如果收到`--console`命令行自变量，或如果附加调试器。</span><span class="sxs-lookup"><span data-stu-id="fea8c-135">For example, you could run as a console app if you get a `--console` command-line argument or if the debugger is attached.</span></span>

[!code-csharp[](windows-service/sample/Program.cs?name=ServiceOrConsole)]

## <a name="handle-stopping-and-starting-events"></a><span data-ttu-id="fea8c-136">处理停止和启动事件</span><span class="sxs-lookup"><span data-stu-id="fea8c-136">Handle stopping and starting events</span></span>

<span data-ttu-id="fea8c-137">如果你想要处理`OnStarting`， `OnStarted`，和`OnStopping`事件，进行以下其他更改：</span><span class="sxs-lookup"><span data-stu-id="fea8c-137">If you want to handle `OnStarting`, `OnStarted`, and `OnStopping` events, make the following additional changes:</span></span>

* <span data-ttu-id="fea8c-138">创建一个从 `WebHostService` 派生的类。</span><span class="sxs-lookup"><span data-stu-id="fea8c-138">Create a class that derives from `WebHostService`.</span></span>

  [!code-csharp[](windows-service/sample/CustomWebHostService.cs?name=NoLogging)]

* <span data-ttu-id="fea8c-139">创建扩展方法`IWebHost`通过您的自定义`WebHostService`到`ServiceBase.Run`。</span><span class="sxs-lookup"><span data-stu-id="fea8c-139">Create an extension method for `IWebHost` that passes your custom `WebHostService` to `ServiceBase.Run`.</span></span>

  [!code-csharp[](windows-service/sample/WebHostServiceExtensions.cs?name=ExtensionsClass)]

* <span data-ttu-id="fea8c-140">在`Program.Main`更改调用新的扩展方法，而不是`host.RunAsService`。</span><span class="sxs-lookup"><span data-stu-id="fea8c-140">In `Program.Main` change call the new extension method instead of `host.RunAsService`.</span></span>

  [!code-csharp[](windows-service/sample/Program.cs?name=HandleStopStart&highlight=26)]

<span data-ttu-id="fea8c-141">如果您的自定义`WebHostService`代码需要从 （例如记录器） 的依赖关系注入获取服务，你可以获取从`Services`属性`IWebHost`。</span><span class="sxs-lookup"><span data-stu-id="fea8c-141">If your custom `WebHostService` code needs to get a service from dependency injection (such as a logger), you can get it from the `Services` property of `IWebHost`.</span></span>

[!code-csharp[](windows-service/sample/CustomWebHostService.cs?name=Logging&highlight=7)]

## <a name="next-steps"></a><span data-ttu-id="fea8c-142">后续步骤</span><span class="sxs-lookup"><span data-stu-id="fea8c-142">Next steps</span></span>

<span data-ttu-id="fea8c-143">[示例应用程序](https://github.com/aspnet/Docs/tree/master/aspnetcore/hosting/windows-service/sample)附带此文章是已被修改，前面的代码示例中所示的简单 MVC web 应用。</span><span class="sxs-lookup"><span data-stu-id="fea8c-143">The [sample application](https://github.com/aspnet/Docs/tree/master/aspnetcore/hosting/windows-service/sample) that accompanies this article is a simple MVC web app that has been modified as shown in preceding code examples.</span></span>  <span data-ttu-id="fea8c-144">若要在服务中运行它，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="fea8c-144">To run it in a service, do the following steps:</span></span>

* <span data-ttu-id="fea8c-145">将发布到*c:\svc*。</span><span class="sxs-lookup"><span data-stu-id="fea8c-145">Publish to *c:\svc*.</span></span>

* <span data-ttu-id="fea8c-146">打开管理员窗口。</span><span class="sxs-lookup"><span data-stu-id="fea8c-146">Open an administrator window.</span></span>

* <span data-ttu-id="fea8c-147">输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="fea8c-147">Enter the following commands:</span></span>

  ```console
  sc create MyService binPath="c:\svc\aspnetcoreservice.exe"
  sc start MyService
  ```

  * <span data-ttu-id="fea8c-148">在浏览器中，转到 http://localhost:5000/ 以验证它正在运行。</span><span class="sxs-lookup"><span data-stu-id="fea8c-148">In a browser, go to http://localhost:5000 to verify that it's running.</span></span>

<span data-ttu-id="fea8c-149">如果不按预期运行时服务中运行启动应用程序，以使错误消息可访问的捷径是添加日志记录提供程序，例如[Windows 事件日志提供程序](xref:fundamentals/logging#eventlog)。</span><span class="sxs-lookup"><span data-stu-id="fea8c-149">If the app doesn't start up as expected when running in a service, a quick way to make error messages accessible is to add a logging provider such as the [Windows EventLog provider](xref:fundamentals/logging#eventlog).</span></span>

## <a name="acknowledgments"></a><span data-ttu-id="fea8c-150">确认</span><span class="sxs-lookup"><span data-stu-id="fea8c-150">Acknowledgments</span></span>

<span data-ttu-id="fea8c-151">撰写本文时的已发布的源的帮助。</span><span class="sxs-lookup"><span data-stu-id="fea8c-151">This article was written with the help of sources that were already published.</span></span> <span data-ttu-id="fea8c-152">最早和其中最有用了这些：</span><span class="sxs-lookup"><span data-stu-id="fea8c-152">The earliest and most useful of them were these:</span></span>

* [<span data-ttu-id="fea8c-153">作为 Windows 服务承载 ASP.NET 核心</span><span class="sxs-lookup"><span data-stu-id="fea8c-153">Hosting ASP.NET Core as Windows service</span></span>](https://stackoverflow.com/questions/37346383/hosting-asp-net-core-as-windows-service/37464074)
* [<span data-ttu-id="fea8c-154">如何托管 Windows 服务中将 ASP.NET 核心</span><span class="sxs-lookup"><span data-stu-id="fea8c-154">How to host your ASP.NET Core in a Windows Service</span></span>](https://dotnetthoughts.net/how-to-host-your-aspnet-core-in-a-windows-service/)
