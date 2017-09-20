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
ms.openlocfilehash: df3c1f0c2768b89c3ea5dc901782170c530a542e
ms.sourcegitcommit: 74a8ad9c1ba5c155d7c4303e67632a0922c38e86
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2017
---
# <a name="hosting-and-deployment-overview-for-aspnet-core-apps"></a>ASP.NET Core 应用的托管和部署概述

以下是向托管环境部署 ASP.NET Core 应用需要执行的主要步骤：

* 将应用发布到托管服务器上的文件夹。
* 设置进程管理器，该管理器在请求传入时启动应用并在应用发生故障或服务器重启后重新启动应用。
* 设置将请求转发到应用的反向代理。

## <a name="publish-to-a-folder"></a>发布到文件夹 

[dotnet 发布](https://docs.microsoft.com/dotnet/articles/core/tools/dotnet-publish) CLI 命令编译应用程序代码并复制所需的文件，以便在“发布”文件夹中运行应用程序。 从 Visual Studio 进行部署时，在文件被复制到部署目标前，`dotnet publish` 步骤已自动执行。

### <a name="folder-contents"></a>文件夹内容

“发布”文件夹包含应用程序的“.exe”和“.dll”文件、应用程序依赖项以及 .NET 运行时（根据需要）。

.NET Core 应用可以作为“独立”应用或“依赖于框架”的应用进行发布。 如果应用是独立应用，则包含 .NET 运行时的“.dll”文件会包括在“发布”文件夹内。  如果应用依赖于框架，则不会将 .NET 运行时文件包含在内，因为应用包含对安装在计算机上的 .NET 版本的引用。 默认部署模型是依赖于框架的模型。 有关详细信息，请参阅 [.NET Core 应用程序部署](https://docs.microsoft.com/dotnet/articles/core/deploying/index)。

除了“.exe”和“.dll”文件以外，ASP.NET Core 应用的“发布”文件夹通常包含配置文件、静态资产和 MVC 视图。  有关详细信息，请参阅[目录结构](xref:hosting/directory-structure)。

## <a name="set-up-a-process-manager"></a>设置进程管理器

ASP.NET Core 应用是一个控制台应用，在服务器启动时必须启动该应用，并且在出现故障后必须重新启动它。 若要自动启动和重新启动，需要使用进程管理器。 适用于 ASP.NET Core 的最常见的进程管理器是 Linux 上的 [Nginx](xref:publishing/linuxproduction) 和 [Apache](xref:publishing/apache-proxy)，以及 Windows 上的 [IIS](xref:publishing/iis)和 [Windows Service](xref:hosting/windows-service)。

## <a name="set-up-a-reverse-proxy"></a>设置反向代理

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

如果你的应用使用 [Kestrel](xref:fundamentals/servers/kestrel) Web 服务器，则可以将 [Nginx](xref:publishing/linuxproduction)、[Apache](xref:publishing/apache-proxy) 或 [IIS](xref:publishing/iis) 用作反向代理服务器。 反向代理服务器接收到来自 Internet 的 HTTP 请求，并在进行一些初步处理后将这些请求转发到 Kestrel。 有关详细信息，请参阅[何时结合使用 Kestrel 和反向代理](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#when-to-use-kestrel-with-a-reverse-proxy)。

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

如果你的应用使用 [Kestrel](xref:fundamentals/servers/kestrel) Web 服务器并将被公开到 Internet，则必须将 [Nginx](xref:publishing/linuxproduction)、[Apache](xref:publishing/apache-proxy) 或 [IIS](xref:publishing/iis) 用作反向代理服务器。 反向代理服务器接收到来自 Internet 的 HTTP 请求，并在进行一些初步处理后将这些请求转发到 Kestrel。 使用反向代理的主要原因是出于安全考虑。 有关详细信息，请参阅[何时结合使用 Kestrel 和反向代理](xref:fundamentals/servers/kestrel?tabs=aspnetcore1x#when-to-use-kestrel-with-a-reverse-proxy)。

---

## <a name="using-visual-studio-and-msbuild-to-automate-deployment"></a>使用 Visual Studio 和 MSBuild 自动执行部署

除了将输出从 `dotnet publish` 复制到服务器以外，部署通常还需要其他任务。 例如，你可能想要在“发布”文件夹中包含其他文件，或者从文件夹中排除一些文件。 Visual Studio 使用 MSBuild 进行 Web 部署，用户可以自定义 MSBuild 以在部署期间执行其他任务。 有关详细信息，请参阅 [Visual Studio 中的发布配置文件](xref:publishing/web-publishing-vs)和[使用 MSBuild 和 Team Foundation Build](http://msbuildbook.com/) 一书。

可以使用[发布 Web 功能](xref:tutorials/publish-to-azure-webapp-using-vs)或使用[内置 Git 支持](xref:publishing/azure-continuous-deployment)从 Visual Studio 直接部署到 Azure App Service。 Visual Studio Team Services 支持[对 Azure App Service 进行持续部署](https://www.visualstudio.com/docs/build/aspnet/core/quick-to-azure)。

## <a name="additional-resources"></a>其他资源

有关将 Docker 用作托管环境的详细信息，请参阅[在 Docker 中托管 ASP.NET Core 应用](xref:publishing/docker)。
