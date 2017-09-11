---
title: "托管和部署概述 - ASP.NET Core"
author: tdykstra
description: "有关如何设置托管环境并对其部署 ASP.NET Core 应用的概述。"
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: article
ms.assetid: f0930c68-4d17-4748-adbf-801e17601eb6
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/index
ms.openlocfilehash: d030b4f16727080488056c9cde48c31a14a166bf
ms.sourcegitcommit: bd05f7ea8f87ad076ef6e8b704698ebcba5ca80c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2017
---
# <a name="hosting-and-deployment-overview-for-aspnet-core-apps"></a><span data-ttu-id="6a59a-104">ASP.NET Core 应用的托管和部署概述</span><span class="sxs-lookup"><span data-stu-id="6a59a-104">Hosting and deployment overview for ASP.NET Core apps</span></span>

<span data-ttu-id="6a59a-105">以下是向托管环境部署 ASP.NET Core 应用需要执行的主要步骤：</span><span class="sxs-lookup"><span data-stu-id="6a59a-105">Here are the main steps you perform to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="6a59a-106">将应用发布到托管服务器上的文件夹。</span><span class="sxs-lookup"><span data-stu-id="6a59a-106">Publish the app to a folder on the hosting server.</span></span>
* <span data-ttu-id="6a59a-107">设置进程管理器，该管理器在请求传入时启动应用并在应用发生故障或服务器重启后重新启动应用。</span><span class="sxs-lookup"><span data-stu-id="6a59a-107">Set up a process manager that starts the app when requests come in and restarts it after it crashes or the server reboots.</span></span>
* <span data-ttu-id="6a59a-108">设置将请求转发到应用的反向代理。</span><span class="sxs-lookup"><span data-stu-id="6a59a-108">Set up a reverse proxy that forwards requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="6a59a-109">发布到文件夹</span><span class="sxs-lookup"><span data-stu-id="6a59a-109">Publish to a folder</span></span> 

<span data-ttu-id="6a59a-110">[dotnet 发布](https://docs.microsoft.com/dotnet/articles/core/tools/dotnet-publish) CLI 命令编译应用程序代码并复制所需的文件，以便在“发布”文件夹中运行应用程序。</span><span class="sxs-lookup"><span data-stu-id="6a59a-110">The [dotnet publish](https://docs.microsoft.com/dotnet/articles/core/tools/dotnet-publish) CLI command compiles application code and copies the files needed to run the application into a *publish* folder.</span></span> <span data-ttu-id="6a59a-111">从 Visual Studio 进行部署时，在文件被复制到部署目标前，`dotnet publish` 步骤已自动执行。</span><span class="sxs-lookup"><span data-stu-id="6a59a-111">When you deploy from Visual Studio the `dotnet publish` step is done for you automatically before files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="6a59a-112">文件夹内容</span><span class="sxs-lookup"><span data-stu-id="6a59a-112">Folder contents</span></span>

<span data-ttu-id="6a59a-113">“发布”文件夹包含应用程序的“.exe”和“.dll”文件、应用程序依赖项以及 .NET 运行时（根据需要）。</span><span class="sxs-lookup"><span data-stu-id="6a59a-113">The *publish* folder contains *.exe* and *.dll* files for the application, its dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="6a59a-114">.NET Core 应用可以作为“独立”应用或“依赖于框架”的应用进行发布。</span><span class="sxs-lookup"><span data-stu-id="6a59a-114">A .NET Core app can be published as *self-contained* or *framework-dependent*.</span></span> <span data-ttu-id="6a59a-115">如果应用是独立应用，则包含 .NET 运行时的“.dll”文件会包括在“发布”文件夹内。</span><span class="sxs-lookup"><span data-stu-id="6a59a-115">If the app is self-contained, the *.dll* files that contain the .NET runtime are included in the *publish* folder.</span></span>  <span data-ttu-id="6a59a-116">如果应用依赖于框架，则不会将 .NET 运行时文件包含在内，因为应用包含对安装在计算机上的 .NET 版本的引用。</span><span class="sxs-lookup"><span data-stu-id="6a59a-116">If the app is framework-dependent, the .NET runtime files are not included because the app has a reference to a version of .NET that is installed on the computer.</span></span> <span data-ttu-id="6a59a-117">默认部署模型是依赖于框架的模型。</span><span class="sxs-lookup"><span data-stu-id="6a59a-117">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="6a59a-118">有关详细信息，请参阅 [.NET Core 应用程序部署](https://docs.microsoft.com/dotnet/articles/core/deploying/index)。</span><span class="sxs-lookup"><span data-stu-id="6a59a-118">For more information, see [.NET Core application deployment](https://docs.microsoft.com/dotnet/articles/core/deploying/index).</span></span>

<span data-ttu-id="6a59a-119">除了“.exe”和“.dll”文件以外，ASP.NET Core 应用的“发布”文件夹通常包含配置文件、静态资产和 MVC 视图。</span><span class="sxs-lookup"><span data-stu-id="6a59a-119">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span>  <span data-ttu-id="6a59a-120">有关详细信息，请参阅[目录结构](xref:hosting/directory-structure)。</span><span class="sxs-lookup"><span data-stu-id="6a59a-120">For more information, see [Directory structure](xref:hosting/directory-structure).</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="6a59a-121">设置进程管理器</span><span class="sxs-lookup"><span data-stu-id="6a59a-121">Set up a process manager</span></span>

<span data-ttu-id="6a59a-122">ASP.NET Core 应用是一个控制台应用，在服务器启动时必须启动该应用，并且在出现故障后必须重新启动它。</span><span class="sxs-lookup"><span data-stu-id="6a59a-122">An ASP.NET Core app is a console app that has to be started when a server boots and restarted after crashes.</span></span> <span data-ttu-id="6a59a-123">若要自动启动和重新启动，需要使用进程管理器。</span><span class="sxs-lookup"><span data-stu-id="6a59a-123">To automate starts and restarts you need a process manager.</span></span> <span data-ttu-id="6a59a-124">适用于 ASP.NET Core 的最常见的进程管理器是 Linux 上的 [Nginx](xref:publishing/linuxproduction) 和 [Apache](xref:publishing/apache-proxy)，以及 Windows 上的 [IIS](xref:publishing/iis)和 [Windows Service](xref:hosting/windows-service)。</span><span class="sxs-lookup"><span data-stu-id="6a59a-124">The most common process managers for ASP.NET Core are [Nginx](xref:publishing/linuxproduction) and [Apache](xref:publishing/apache-proxy) on Linux, and [IIS](xref:publishing/iis) and [Windows Service](xref:hosting/windows-service) on Windows.</span></span>

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="6a59a-125">设置反向代理</span><span class="sxs-lookup"><span data-stu-id="6a59a-125">Set up a reverse proxy</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="6a59a-126">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="6a59a-126">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="6a59a-127">如果你的应用使用 [Kestrel](xref:fundamentals/servers/kestrel) Web 服务器，则可以将 [Nginx](xref:publishing/linuxproduction)、[Apache](xref:publishing/apache-proxy) 或 [IIS](xref:publishing/iis) 用作反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="6a59a-127">If your app uses the [Kestrel](xref:fundamentals/servers/kestrel) web server, you can use [Nginx](xref:publishing/linuxproduction), [Apache](xref:publishing/apache-proxy), or [IIS](xref:publishing/iis) as a reverse proxy server.</span></span> <span data-ttu-id="6a59a-128">反向代理服务器接收到来自 Internet 的 HTTP 请求，并在进行一些初步处理后将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="6a59a-128">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel after some preliminary handling.</span></span> <span data-ttu-id="6a59a-129">有关详细信息，请参阅[何时结合使用 Kestrel 和反向代理](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#when-to-use-kestrel-with-a-reverse-proxy)。</span><span class="sxs-lookup"><span data-stu-id="6a59a-129">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="6a59a-130">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="6a59a-130">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="6a59a-131">如果你的应用使用 [Kestrel](xref:fundamentals/servers/kestrel) Web 服务器并将被公开到 Internet，则必须将 [Nginx](xref:publishing/linuxproduction)、[Apache](xref:publishing/apache-proxy) 或 [IIS](xref:publishing/iis) 用作反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="6a59a-131">If your app uses the [Kestrel](xref:fundamentals/servers/kestrel) web server and will be exposed to the Internet, you must use [Nginx](xref:publishing/linuxproduction), [Apache](xref:publishing/apache-proxy), or [IIS](xref:publishing/iis) as a reverse proxy server.</span></span> <span data-ttu-id="6a59a-132">反向代理服务器接收到来自 Internet 的 HTTP 请求，并在进行一些初步处理后将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="6a59a-132">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel after some preliminary handling.</span></span> <span data-ttu-id="6a59a-133">使用反向代理的主要原因是出于安全考虑。</span><span class="sxs-lookup"><span data-stu-id="6a59a-133">The main reason for using a reverse proxy is security.</span></span> <span data-ttu-id="6a59a-134">有关详细信息，请参阅[何时结合使用 Kestrel 和反向代理](xref:fundamentals/servers/kestrel?tabs=aspnetcore1x#when-to-use-kestrel-with-a-reverse-proxy)。</span><span class="sxs-lookup"><span data-stu-id="6a59a-134">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel?tabs=aspnetcore1x#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

---

## <a name="using-visual-studio-and-msbuild-to-automate-deployment"></a><span data-ttu-id="6a59a-135">使用 Visual Studio 和 MSBuild 自动执行部署</span><span class="sxs-lookup"><span data-stu-id="6a59a-135">Using Visual Studio and MSBuild to automate deployment</span></span>

<span data-ttu-id="6a59a-136">除了将输出从 `dotnet publish` 复制到服务器以外，部署通常还需要其他任务。</span><span class="sxs-lookup"><span data-stu-id="6a59a-136">Deployment often requires additional tasks besides copying the output from `dotnet publish` to a server.</span></span> <span data-ttu-id="6a59a-137">例如，你可能想要在“发布”文件夹中包含其他文件，或者从文件夹中排除一些文件。</span><span class="sxs-lookup"><span data-stu-id="6a59a-137">For example, you might want to include extra files in the *publish* folder, or exclude files from it.</span></span> <span data-ttu-id="6a59a-138">Visual Studio 使用 MSBuild 进行 Web 部署，用户可以自定义 MSBuild 以在部署期间执行其他任务。</span><span class="sxs-lookup"><span data-stu-id="6a59a-138">Visual Studio uses MSBuild for web deployment, and you can customize MSBuild to do many other tasks during deployment.</span></span> <span data-ttu-id="6a59a-139">有关详细信息，请参阅 [Visual Studio 中的发布配置文件](xref:publishing/web-publishing-vs)和[使用 MSBuild 和 Team Foundation Build](http://msbuildbook.com/) 一书。</span><span class="sxs-lookup"><span data-stu-id="6a59a-139">For more information, see [Publish profiles in Visual Studio](xref:publishing/web-publishing-vs) and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="6a59a-140">可以使用[发布 Web 功能](xref:tutorials/publish-to-azure-webapp-using-vs)或使用[内置 Git 支持](xref:publishing/azure-continuous-deployment)从 Visual Studio 直接部署到 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="6a59a-140">You can deploy directly from Visual Studio to Azure App Service by using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or by using [built-in Git support](xref:publishing/azure-continuous-deployment).</span></span> <span data-ttu-id="6a59a-141">Visual Studio Team Services 支持[对 Azure App Service 进行持续部署](https://www.visualstudio.com/en-us/docs/build/aspnet/core/quick-to-azure)。</span><span class="sxs-lookup"><span data-stu-id="6a59a-141">Visual Studio Team Services supports [continuous deployment to Azure App Service](https://www.visualstudio.com/en-us/docs/build/aspnet/core/quick-to-azure).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6a59a-142">其他资源</span><span class="sxs-lookup"><span data-stu-id="6a59a-142">Additional resources</span></span>

<span data-ttu-id="6a59a-143">有关将 Docker 用作托管环境的详细信息，请参阅[在 Docker 中托管 ASP.NET Core 应用](xref:publishing/docker)。</span><span class="sxs-lookup"><span data-stu-id="6a59a-143">For information about using Docker as a hosting environment, see [Host ASP.NET Core apps in Docker](xref:publishing/docker).</span></span>
