---
title: "Visual Studio 中针对 ASP.NET Core 的开发时 IIS 支持"
author: shirhatti
description: "发现对调试 ASP.NET Core 应用程序的支持（在 Windows Server 上以 IIS 为背景运行时）。"
keywords: "ASP.NET Core,internet information services,iis,windows server,asp.net core 模块,调试"
ms.author: riande
manager: wpickett
ms.date: 09/13/2017
ms.topic: article
ms.assetid: 83d98477-9d10-4a78-a54a-f325ad67d13b
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/development-time-iis-support
ms.openlocfilehash: a35a6fd9896c4c110d1b6680b6aaf718d29a18ab
ms.sourcegitcommit: 74a8ad9c1ba5c155d7c4303e67632a0922c38e86
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2017
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="6bc60-104">Visual Studio 中针对 ASP.NET Core 的开发时 IIS 支持</span><span class="sxs-lookup"><span data-stu-id="6bc60-104">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="6bc60-105">作者：[Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="6bc60-105">By: [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="6bc60-106">本文介绍了对调试（在 Windows Server 上以 IIS 为背景运行的）ASP.NET Core 应用程序的 [Visual Studio](https://www.visualstudio.com/vs/) 支持。</span><span class="sxs-lookup"><span data-stu-id="6bc60-106">This article describes [Visual Studio](https://www.visualstudio.com/vs/) support for debugging ASP.NET Core applications running behind IIS on Windows Server.</span></span> <span data-ttu-id="6bc60-107">本主题逐步介绍如何启用此功能并设置项目。</span><span class="sxs-lookup"><span data-stu-id="6bc60-107">This topic walks you through enabling this feature and setting up your project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6bc60-108">先决条件</span><span class="sxs-lookup"><span data-stu-id="6bc60-108">Prerequisites</span></span>

* <span data-ttu-id="6bc60-109">Visual Studio（2017/版本 15.3 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="6bc60-109">Visual Studio (2017/version 15.3 or later)</span></span>
* <span data-ttu-id="6bc60-110">ASP.NET 和 Web 开发工作负荷或 .NET Core 跨平台开发工作负荷</span><span class="sxs-lookup"><span data-stu-id="6bc60-110">ASP.NET and web development workload *OR* the .NET Core cross-platform development workload</span></span>

## <a name="enable-iis"></a><span data-ttu-id="6bc60-111">启用 IIS</span><span class="sxs-lookup"><span data-stu-id="6bc60-111">Enable IIS</span></span>

<span data-ttu-id="6bc60-112">在系统上启用 IIS。</span><span class="sxs-lookup"><span data-stu-id="6bc60-112">Enable IIS on your system.</span></span> <span data-ttu-id="6bc60-113">导航到“控制面板” > “程序” > “程序和功能” > “打开或关闭 Windows 功能”（位于屏幕左侧）。</span><span class="sxs-lookup"><span data-stu-id="6bc60-113">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span> <span data-ttu-id="6bc60-114">选中“Internet Information Services”复选框。</span><span class="sxs-lookup"><span data-stu-id="6bc60-114">Select the **Internet Information Services** checkbox.</span></span>

![Windows 功能将选中的“Internet Information Services”复选框显示为实心方形（而不是复选标记），指示已启用某些 IIS 功能](development-time-iis-support/_static/enable_iis.png)

<span data-ttu-id="6bc60-116">根据 IIS 安装的要求重启系统。</span><span class="sxs-lookup"><span data-stu-id="6bc60-116">If your IIS installation requires a reboot, reboot your system.</span></span>

## <a name="enable-development-time-iis-support"></a><span data-ttu-id="6bc60-117">启用开发时 IIS 支持</span><span class="sxs-lookup"><span data-stu-id="6bc60-117">Enable development-time IIS support</span></span>

<span data-ttu-id="6bc60-118">安装 IIS 后，启动 Visual Studio 安装程序以修改现有 Visual Studio 安装。</span><span class="sxs-lookup"><span data-stu-id="6bc60-118">Once you've installed IIS, launch the Visual Studio installer to modify your existing Visual Studio installation.</span></span> <span data-ttu-id="6bc60-119">在安装程序中，选择“开发时 IIS 支持”组件。</span><span class="sxs-lookup"><span data-stu-id="6bc60-119">In the installer, select the **Development time IIS support** component.</span></span> <span data-ttu-id="6bc60-120">该组件在“ASP.NET 和 Web 开发”工作负荷的“摘要”面板中列为可选组件。</span><span class="sxs-lookup"><span data-stu-id="6bc60-120">The component is listed as an optional component in the **Summary** panel for the **ASP.NET and web development** workload.</span></span> <span data-ttu-id="6bc60-121">此组件将安装 [ASP.NET Core 模块](xref:fundamentals/servers/aspnet-core-module)，该模块是运行 ASP.NET Core 应用程序所需的本机 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="6bc60-121">This installs the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module), which is a native IIS module required to run ASP.NET Core applications.</span></span>

![修改 Visual Studio 功能：选择“工作负荷”选项卡。](development-time-iis-support/_static/development_time_support.png)

## <a name="configure-the-project"></a><span data-ttu-id="6bc60-125">配置项目</span><span class="sxs-lookup"><span data-stu-id="6bc60-125">Configure the project</span></span>

<span data-ttu-id="6bc60-126">创建新的启动配置文件以添加开发时 IIS 支持。</span><span class="sxs-lookup"><span data-stu-id="6bc60-126">Create a new launch profile to add development-time IIS support.</span></span> <span data-ttu-id="6bc60-127">在 Visual Studio 的“解决方案资源管理器”中，右键单击项目，然后选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="6bc60-127">In Visual Studio's **Solution Explorer**, right-click the project and select **Properties**.</span></span> <span data-ttu-id="6bc60-128">选择“调试”选项卡。从“启动”下拉列表中选择“IIS”。</span><span class="sxs-lookup"><span data-stu-id="6bc60-128">Select the **Debug** tab. Select **IIS** from the **Launch** dropdown.</span></span> <span data-ttu-id="6bc60-129">确认已为“启动浏览器”功能配置了正确的 URL。</span><span class="sxs-lookup"><span data-stu-id="6bc60-129">Confirm that the **Launch browser** feature is enabled with the correct URL.</span></span>

![选择了“调试”选项卡的“项目属性”窗口。](development-time-iis-support/_static/project_properties.png)

<span data-ttu-id="6bc60-134">或者，可以将启动配置文件手动添加到应用中的 [launchSettings.json](http://json.schemastore.org/launchsettings) 文件：</span><span class="sxs-lookup"><span data-stu-id="6bc60-134">Alternatively, you can manually add a launch profile to your [launchSettings.json](http://json.schemastore.org/launchsettings) file in the app:</span></span>

```json
{
    "iisSettings": {
        "windowsAuthentication": false,
        "anonymousAuthentication": true,
        "iis": {
            "applicationUrl": "http://localhost/WebApplication2",
            "sslPort": 0
        }
    },
    "profiles": {
        "IIS": {
            "commandName": "IIS",
            "launchBrowser": "true",
            "launchUrl": "http://localhost/WebApplication2",
            "environmentVariables": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    }
}
```

<span data-ttu-id="6bc60-135">如果不以管理员身份运行，系统可能提示重启 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="6bc60-135">You may be prompted to restart Visual Studio if you weren't running as an administrator.</span></span> <span data-ttu-id="6bc60-136">如果出现提示，请重启 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="6bc60-136">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="6bc60-137">祝贺你！</span><span class="sxs-lookup"><span data-stu-id="6bc60-137">Congratulations!</span></span> <span data-ttu-id="6bc60-138">此时，项目已配置开发时 IIS 支持。</span><span class="sxs-lookup"><span data-stu-id="6bc60-138">At this point, your project is configured for development-time IIS support.</span></span> 

## <a name="additional-resources"></a><span data-ttu-id="6bc60-139">其他资源</span><span class="sxs-lookup"><span data-stu-id="6bc60-139">Additional resources</span></span>

* [<span data-ttu-id="6bc60-140">使用 IIS 在 Windows 上托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6bc60-140">Host ASP.NET Core on Windows with IIS</span></span>](xref:publishing/iis)
* [<span data-ttu-id="6bc60-141">ASP.NET Core 模块简介</span><span class="sxs-lookup"><span data-stu-id="6bc60-141">Introduction to ASP.NET Core Module</span></span>](xref:fundamentals/servers/aspnet-core-module)
* [<span data-ttu-id="6bc60-142">ASP.NET Core 模块配置参考</span><span class="sxs-lookup"><span data-stu-id="6bc60-142">ASP.NET Core Module configuration reference</span></span>](xref:hosting/aspnet-core-module)
