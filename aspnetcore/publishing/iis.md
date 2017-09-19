---
title: "使用 IIS 在 Windows 上托管 ASP.NET Core"
author: guardrex
description: "ASP.NET Core 应用程序的 Windows Server Internet Information Services (IIS) 配置和部署。"
keywords: "ASP.NET Core, internet information services, iis, windows server, 托管捆绑包, asp.net core 模块, web 部署"
ms.author: riande
manager: wpickett
ms.date: 03/13/2017
ms.topic: article
ms.assetid: a4449ad3-5bad-410c-afa7-dc32d832b552
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/iis
ms.openlocfilehash: 48e67add785fc1d7e79c659565afb1ec68c1defb
ms.sourcegitcommit: f531d90646b9d261c5fbbffcecd6ded9185ae292
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2017
---
# <a name="set-up-a-hosting-environment-for-aspnet-core-on-windows-with-iis-and-deploy-to-it"></a><span data-ttu-id="50b70-104">使用 IIS 在 Windows 上为 ASP.NET Core 设置托管环境，并对其进行部署</span><span class="sxs-lookup"><span data-stu-id="50b70-104">Set up a hosting environment for ASP.NET Core on Windows with IIS, and deploy to it</span></span>

<span data-ttu-id="50b70-105">作者：[Luke Latham](https://github.com/GuardRex) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="50b70-105">By [Luke Latham](https://github.com/GuardRex) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="supported-operating-systems"></a><span data-ttu-id="50b70-106">支持的操作系统</span><span class="sxs-lookup"><span data-stu-id="50b70-106">Supported operating systems</span></span>

<span data-ttu-id="50b70-107">支持下列操作系统：</span><span class="sxs-lookup"><span data-stu-id="50b70-107">The following operating systems are supported:</span></span>

* <span data-ttu-id="50b70-108">Windows 7 和更新版本</span><span class="sxs-lookup"><span data-stu-id="50b70-108">Windows 7 and newer</span></span>

* <span data-ttu-id="50b70-109">Windows Server 2008 R2 和 更新版本&#8224;</span><span class="sxs-lookup"><span data-stu-id="50b70-109">Windows Server 2008 R2 and newer&#8224;</span></span>

<span data-ttu-id="50b70-110">&#8224;在概念上，本文档描述的 IIS 配置也适用于在 Nano Server IIS 上托管 ASP.NET Core 应用程序，但有关具体说明，请参阅 [ASP.NET Core with IIS on Nano Server](xref:tutorials/nano-server)（Nano Server 上使用 IIS 的 ASP.NET Core）。</span><span class="sxs-lookup"><span data-stu-id="50b70-110">&#8224;Conceptually, the IIS configuration described in this document also applies to hosting ASP.NET Core applications on Nano Server IIS, but refer to [ASP.NET Core with IIS on Nano Server](xref:tutorials/nano-server) for specific instructions.</span></span>

<span data-ttu-id="50b70-111">使用 IIS 的反向代理配置时，[WebListener 服务器](xref:fundamentals/servers/weblistener)无法运行。</span><span class="sxs-lookup"><span data-stu-id="50b70-111">[WebListener server](xref:fundamentals/servers/weblistener) will not work in a reverse-proxy configuration with IIS.</span></span> <span data-ttu-id="50b70-112">必须使用 [Kestrel 服务器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="50b70-112">You must use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="50b70-113">IIS 配置</span><span class="sxs-lookup"><span data-stu-id="50b70-113">IIS configuration</span></span>

<span data-ttu-id="50b70-114">启用 Web 服务器 (IIS) 角色并建立角色服务。</span><span class="sxs-lookup"><span data-stu-id="50b70-114">Enable the **Web Server (IIS)** role and establish role services.</span></span>

### <a name="windows-desktop-operating-systems"></a><span data-ttu-id="50b70-115">Windows 桌面操作系统</span><span class="sxs-lookup"><span data-stu-id="50b70-115">Windows desktop operating systems</span></span>

<span data-ttu-id="50b70-116">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）。</span><span class="sxs-lookup"><span data-stu-id="50b70-116">Navigate to **Control Panel > Programs > Programs and Features > Turn Windows features on or off** (left side of the screen).</span></span> <span data-ttu-id="50b70-117">打开“Internet Information Services”组和“Web 管理工具”。</span><span class="sxs-lookup"><span data-stu-id="50b70-117">Open the group for **Internet Information Services** and **Web Management Tools**.</span></span> <span data-ttu-id="50b70-118">选中“IIS 管理控制台”框。</span><span class="sxs-lookup"><span data-stu-id="50b70-118">Check the box for **IIS Management Console**.</span></span> <span data-ttu-id="50b70-119">选中“万维网服务”框。</span><span class="sxs-lookup"><span data-stu-id="50b70-119">Check the box for **World Wide Web Services**.</span></span> <span data-ttu-id="50b70-120">接受“万维网服务”的默认功能，或根据需求自定义 IIS 功能。</span><span class="sxs-lookup"><span data-stu-id="50b70-120">Accept the default features for **World Wide Web Services** or customize the IIS features to suit your needs.</span></span>

![在“Windows 功能”中选择了“IIS 管理控制台”和“万维网服务”。](iis/_static/windows-features-win10.png)

### <a name="windows-server-operating-systems"></a><span data-ttu-id="50b70-122">Windows Server 操作系统</span><span class="sxs-lookup"><span data-stu-id="50b70-122">Windows Server operating systems</span></span>

<span data-ttu-id="50b70-123">对于服务器操作系统，通过“管理”菜单或“服务器管理器”中的链接使用“添加角色和功能”向导。</span><span class="sxs-lookup"><span data-stu-id="50b70-123">For server operating systems, use the **Add Roles and Features** wizard via the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="50b70-124">在“服务器角色”步骤中，选中“Web 服务器(IIS)”框。</span><span class="sxs-lookup"><span data-stu-id="50b70-124">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

![在选择服务器角色步骤中选择了“Web 服务器 IIS”角色。](iis/_static/server-roles-ws2016.png)

<span data-ttu-id="50b70-126">在“角色服务”步骤中，选择所需 IIS 角色服务，或接受提供的默认角色服务。</span><span class="sxs-lookup"><span data-stu-id="50b70-126">On the **Role services** step, select the IIS role services you desire or accept the default role services provided.</span></span>

![在选择角色服务步骤中选择了默认角色服务。](iis/_static/role-services-ws2016.png)

<span data-ttu-id="50b70-128">继续执行“确认”步骤，安装 Web 服务器角色和服务。</span><span class="sxs-lookup"><span data-stu-id="50b70-128">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="50b70-129">安装 Web 服务器 (IIS) 角色后无需重启服务器/IIS。</span><span class="sxs-lookup"><span data-stu-id="50b70-129">A server/IIS restart is not required after installing the Web Server (IIS) role.</span></span>

## <a name="install-the-net-core-windows-server-hosting-bundle"></a><span data-ttu-id="50b70-130">安装 .NET Core Windows Server 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="50b70-130">Install the .NET Core Windows Server Hosting bundle</span></span>

1. <span data-ttu-id="50b70-131">在托管系统上安装 [.NET Core Windows Server 托管捆绑包](https://aka.ms/dotnetcore.2.0.0-windowshosting)。</span><span class="sxs-lookup"><span data-stu-id="50b70-131">Install the [.NET Core Windows Server Hosting bundle](https://aka.ms/dotnetcore.2.0.0-windowshosting) on the hosting system.</span></span> <span data-ttu-id="50b70-132">捆绑包可安装 .NET Core 运行时、.NET Core 库和 [ASP.NET Core 模块](xref:fundamentals/servers/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="50b70-132">The bundle will install the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span></span> <span data-ttu-id="50b70-133">该模块创建 IIS 与 Kestrel 服务器之间的反向代理。</span><span class="sxs-lookup"><span data-stu-id="50b70-133">The module creates the reverse-proxy between IIS and the Kestrel server.</span></span> <span data-ttu-id="50b70-134">请注意：如果系统没有 Internet 连接，请先获取并安装 [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840)，再安装 .NET Core Windows Server 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="50b70-134">Note: If the system doesn't have an Internet connection, obtain and install the *[Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840)* before installing the .NET Core Windows Server Hosting bundle.</span></span>

2. <span data-ttu-id="50b70-135">重启系统，或在命令提示符处依次执行 net stop was /y 和 net start w3svc，了解系统路径的更改。</span><span class="sxs-lookup"><span data-stu-id="50b70-135">Restart the system or execute **net stop was /y** followed by **net start w3svc** from a command prompt to pick up a change to the system PATH.</span></span>

> [!NOTE]
> <span data-ttu-id="50b70-136">如果使用 IIS 共享配置，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:hosting/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="50b70-136">If you use an IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:hosting/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="50b70-137">使用 Visual Studio 进行发布时安装 Web 部署</span><span class="sxs-lookup"><span data-stu-id="50b70-137">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="50b70-138">如果要使用 Web 部署在 Visual Studio 中部署应用程序，请在托管系统上安装最新版本的 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="50b70-138">If you intend to deploy your applications with Web Deploy in Visual Studio, install the latest version of Web Deploy on the hosting system.</span></span> <span data-ttu-id="50b70-139">要安装 Web 部署，可以使用 [Web 平台安装程序 (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或直接从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=43717)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="50b70-139">To install Web Deploy, you can use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="50b70-140">建议使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="50b70-140">The preferred method is to use WebPI.</span></span> <span data-ttu-id="50b70-141">WebPI 为托管提供程序提供独立的安装程序和配置。</span><span class="sxs-lookup"><span data-stu-id="50b70-141">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="50b70-142">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="50b70-142">Application configuration</span></span>

### <a name="enabling-the-iisintegration-components"></a><span data-ttu-id="50b70-143">启用 IISIntegration 组件</span><span class="sxs-lookup"><span data-stu-id="50b70-143">Enabling the IISIntegration components</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="50b70-144">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="50b70-144">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="50b70-145">典型的 Program.cs 调用 [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) 以开始设置主机。</span><span class="sxs-lookup"><span data-stu-id="50b70-145">A typical *Program.cs* calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to begin setting up a host.</span></span> <span data-ttu-id="50b70-146">`CreateDefaultBuilder` 将 [Kestrel](xref:fundamentals/servers/kestrel) 配置为 Web 服务器，并通过配置 [ASP.NET Core 模块](xref:fundamentals/servers/aspnet-core-module)的基路径和端口来实现 IIS 集成：</span><span class="sxs-lookup"><span data-stu-id="50b70-146">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) as the web server and enables IIS integration by configuring the base path and port for the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module):</span></span>

```csharp
public static IWebHost BuildWebHost(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="50b70-147">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="50b70-147">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="50b70-148">在应用程序依赖项中加入对 Microsoft.AspNetCore.Server.IISIntegration 包的依赖项[](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)。</span><span class="sxs-lookup"><span data-stu-id="50b70-148">Include a dependency on the [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/) package in the application dependencies.</span></span> <span data-ttu-id="50b70-149">通过向 WebHostBuilder 添加 UseIISIntegration 扩展方法，将 IIS 集成中间件合并到应用程序中：</span><span class="sxs-lookup"><span data-stu-id="50b70-149">Incorporate IIS Integration middleware into the application by adding the *UseIISIntegration* extension method to *WebHostBuilder*:</span></span>

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .UseIISIntegration()
    ...
```

<span data-ttu-id="50b70-150">`UseKestrel` 和 `UseIISIntegration` 均为必需。</span><span class="sxs-lookup"><span data-stu-id="50b70-150">Both `UseKestrel` and `UseIISIntegration` are required.</span></span> <span data-ttu-id="50b70-151">调用 UseIISIntegration 的代码不会影响代码可移植性。</span><span class="sxs-lookup"><span data-stu-id="50b70-151">Code calling *UseIISIntegration* doesn't affect code portability.</span></span> <span data-ttu-id="50b70-152">如果应用不以 IIS 为背景运行（例如，应用直接在 Kestrel 上运行），则 `UseIISIntegration` 无操作。</span><span class="sxs-lookup"><span data-stu-id="50b70-152">If the app isn't run behind IIS (for example, the app is run directly on Kestrel), `UseIISIntegration` no-ops.</span></span>

---

<span data-ttu-id="50b70-153">有关托管的详细信息，请参阅 [ASP.NET Core 中的托管](xref:fundamentals/hosting)。</span><span class="sxs-lookup"><span data-stu-id="50b70-153">For more information on hosting, see [Hosting in ASP.NET Core](xref:fundamentals/hosting).</span></span>

### <a name="setting-iisoptions-for-the-iisintegration-service"></a><span data-ttu-id="50b70-154">为 IISIntegration 服务设置 IISOptions</span><span class="sxs-lookup"><span data-stu-id="50b70-154">Setting IISOptions for the IISIntegration service</span></span>

<span data-ttu-id="50b70-155">要配置 IISIntegration 服务选项，请在 ConfigureServices 中加入适用于 IISOptions 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="50b70-155">To configure *IISIntegration* service options, include a service configuration for *IISOptions* in *ConfigureServices*.</span></span>

```csharp
services.Configure<IISOptions>(options => {
  ...
});
```

| <span data-ttu-id="50b70-156">选项</span><span class="sxs-lookup"><span data-stu-id="50b70-156">Option</span></span> | <span data-ttu-id="50b70-157">设置</span><span class="sxs-lookup"><span data-stu-id="50b70-157">Setting</span></span>|
| --- | --- | 
| <span data-ttu-id="50b70-158">AutomaticAuthentication</span><span class="sxs-lookup"><span data-stu-id="50b70-158">AutomaticAuthentication</span></span> | <span data-ttu-id="50b70-159">如果为“true”，身份验证中间件更改到达的请求用户，并响应一般挑战。</span><span class="sxs-lookup"><span data-stu-id="50b70-159">If true, the authentication middleware will alter the request user arriving and respond to generic challenges.</span></span> <span data-ttu-id="50b70-160">如果为“false”，身份验证中间件仅在 theAuthenticationScheme 显式指示时提供标识和响应挑战</span><span class="sxs-lookup"><span data-stu-id="50b70-160">If false,the authentication middleware will only provide identity and respond to challenges when explicitly indicated by theAuthenticationScheme</span></span> |
| <span data-ttu-id="50b70-161">ForwardClientCertificate</span><span class="sxs-lookup"><span data-stu-id="50b70-161">ForwardClientCertificate</span></span> | <span data-ttu-id="50b70-162">如果为“true”，且存在 `MS-ASPNETCORE-CLIENTCERT` 请求头，则填充 `ITLSConnectionFeature`。</span><span class="sxs-lookup"><span data-stu-id="50b70-162">If true and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `ITLSConnectionFeature` will be populated.</span></span> |
| <span data-ttu-id="50b70-163">ForwardWindowsAuthentication</span><span class="sxs-lookup"><span data-stu-id="50b70-163">ForwardWindowsAuthentication</span></span> | <span data-ttu-id="50b70-164">如果为“true”，身份验证中间件尝试使用平台处理程序 Windows 身份验证进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="50b70-164">If true, authentication middleware will attempt to authenticate using platform handler windows authentication.</span></span> <span data-ttu-id="50b70-165">如果为“false”，不会添加身份验证中间件。</span><span class="sxs-lookup"><span data-stu-id="50b70-165">If false, authentication middleware won’t be added.</span></span> |

### <a name="webconfig"></a><span data-ttu-id="50b70-166">web.config</span><span class="sxs-lookup"><span data-stu-id="50b70-166">web.config</span></span>

<span data-ttu-id="50b70-167">web.config 文件可配置 ASP.NET Core 模块并提供其他 IIS 配置。</span><span class="sxs-lookup"><span data-stu-id="50b70-167">The *web.config* file configures the ASP.NET Core Module and provides other IIS configuration.</span></span> <span data-ttu-id="50b70-168">web.config 的创建、转换和发布由在 .csproj 文件 `<Project Sdk="Microsoft.NET.Sdk.Web">` 顶部设置项目 SDK 时所含的 `Microsoft.NET.Sdk.Web` 处理。</span><span class="sxs-lookup"><span data-stu-id="50b70-168">Creating, transforming, and publishing *web.config* is handled by `Microsoft.NET.Sdk.Web`, which is included when you set your project's SDK at the top of your *.csproj* file, `<Project Sdk="Microsoft.NET.Sdk.Web">`.</span></span> <span data-ttu-id="50b70-169">要防止 MSBuild 目标转换 web.config 文件，请将 \<IsTransformWebConfigDisabled> 属性添加到项目文件，并将其设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="50b70-169">To prevent the MSBuild target from transforming your *web.config* file, add the **\<IsTransformWebConfigDisabled>** property to your project file with a setting of `true`:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="50b70-170">当使用 dotnet publish 或 Visual Studio 的发布功能进行发布时，如果项目中没有 web.config 文件，则在已发布的输出中创建该文件。</span><span class="sxs-lookup"><span data-stu-id="50b70-170">If you don't have a *web.config* file in the project when you publish with *dotnet publish* or with Visual Studio publish, the file is created for you in published output.</span></span> <span data-ttu-id="50b70-171">如果项目中有该文件，则会使用正确 processPath 和参数转换该文件，以便配置 ASP.NET Core 模块，并将该文件移动到已发布的输出。</span><span class="sxs-lookup"><span data-stu-id="50b70-171">If you have the file in your project, it's transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="50b70-172">转换不会影响文件中已包含的 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="50b70-172">The transformation doesn't touch IIS configuration settings that you've included in the file.</span></span>

## <a name="create-the-iis-website"></a><span data-ttu-id="50b70-173">创建 IIS 网站</span><span class="sxs-lookup"><span data-stu-id="50b70-173">Create the IIS Website</span></span>

1. <span data-ttu-id="50b70-174">在目标 IIS 系统上，创建一个文件夹，将应用程序的已发布文件夹和文件包含在其中，如 [Directory Structure](xref:hosting/directory-structure)（目录结构）中所述。</span><span class="sxs-lookup"><span data-stu-id="50b70-174">On the target IIS system, create a folder to contain the application's published folders and files, which are described in [Directory Structure](xref:hosting/directory-structure).</span></span>

2. <span data-ttu-id="50b70-175">在创建的文件夹中，创建用于保存应用程序日志的日志文件夹（如果计划启用日志记录）。</span><span class="sxs-lookup"><span data-stu-id="50b70-175">Within the folder you created, create a *logs* folder to hold application logs (if you plan to enable logging).</span></span> <span data-ttu-id="50b70-176">如果计划部署的应用程序的有效负载中包含日志文件夹，可跳过此步骤。</span><span class="sxs-lookup"><span data-stu-id="50b70-176">If you plan to deploy your application with a *logs* folder in the payload, you may skip this step.</span></span>

3. <span data-ttu-id="50b70-177">在 IIS 管理器中创建新网站。</span><span class="sxs-lookup"><span data-stu-id="50b70-177">In **IIS Manager**, create a new website.</span></span> <span data-ttu-id="50b70-178">提供网站名称，并将“物理路径”设置为所创建应用程序的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="50b70-178">Provide a **Site name** and set the **Physical path** to the application's deployment folder that you created.</span></span> <span data-ttu-id="50b70-179">提供“绑定”配置并创建网站。</span><span class="sxs-lookup"><span data-stu-id="50b70-179">Provide the **Binding** configuration and create the website.</span></span>

4. <span data-ttu-id="50b70-180">将“应用程序池”设置为“无托管代码”。</span><span class="sxs-lookup"><span data-stu-id="50b70-180">Set the application pool to **No Managed Code**.</span></span> <span data-ttu-id="50b70-181">ASP.NET Core 在单独的进程中运行，并管理运行时。</span><span class="sxs-lookup"><span data-stu-id="50b70-181">ASP.NET Core runs in a separate process and manages the runtime.</span></span>

5. <span data-ttu-id="50b70-182">打开“添加网站”窗口。</span><span class="sxs-lookup"><span data-stu-id="50b70-182">Open the **Add Website** window.</span></span>

   ![单击“站点”上下文菜单中的“添加网站”。](iis/_static/add-website-context-menu-ws2016.png)

6. <span data-ttu-id="50b70-184">配置网站。</span><span class="sxs-lookup"><span data-stu-id="50b70-184">Configure the website.</span></span>

   ![在“添加网站”步骤中提供网站名称、物理路径和主机名。](iis/_static/add-website-ws2016.png)

7. <span data-ttu-id="50b70-186">在“应用程序池”面板中，右键单击网站的应用程序池，并从弹出菜单中选择“基本设置...”，打开“编辑应用程序池”窗口。</span><span class="sxs-lookup"><span data-stu-id="50b70-186">In the **Application Pools** panel, open the **Edit Application Pool** window by right-clicking on the website's application pool and selecting **Basic Settings...** from the popup menu.</span></span>

   ![从应用程序池的上下文菜单中选择“基本设置”。](iis/_static/apppools-basic-settings-ws2016.png)

8. <span data-ttu-id="50b70-188">将“.NET CLR 版本”设置为“无托管代码”。</span><span class="sxs-lookup"><span data-stu-id="50b70-188">Set the **.NET CLR version** to **No Managed Code**.</span></span>

   ![将“.NET CLR 版本”设置为“无托管代码”。](iis/_static/edit-apppool-ws2016.png)
     
    <span data-ttu-id="50b70-190">请注意：将“.NET CLR 版本”设置为“无托管代码”是可选步骤。</span><span class="sxs-lookup"><span data-stu-id="50b70-190">Note: Setting the **.NET CLR version** to **No Managed Code** is optional.</span></span> <span data-ttu-id="50b70-191">ASP.NET Core 不依赖加载桌面 CLR。</span><span class="sxs-lookup"><span data-stu-id="50b70-191">ASP.NET Core doesn't rely on loading the desktop CLR.</span></span>

9. <span data-ttu-id="50b70-192">确认进程模型标识拥有适当的权限。</span><span class="sxs-lookup"><span data-stu-id="50b70-192">Confirm the process model identity has the proper permissions.</span></span>

    <span data-ttu-id="50b70-193">如果将应用程序池的默认标识（“进程模型” > “标识”）从 ApplicationPoolIdentity 更改为另一标识，请验证新标识拥有所需的权限，可访问应用程序的文件夹、数据库和其他所需资源。</span><span class="sxs-lookup"><span data-stu-id="50b70-193">If you change the default identity of the application pool (**Process Model** > **Identity**) from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the application's folder, database, and other required resources.</span></span>
   
## <a name="deploy-the-application"></a><span data-ttu-id="50b70-194">部署应用程序</span><span class="sxs-lookup"><span data-stu-id="50b70-194">Deploy the application</span></span>
<span data-ttu-id="50b70-195">将应用程序部署到在目标 IIS 系统上创建的文件夹。</span><span class="sxs-lookup"><span data-stu-id="50b70-195">Deploy the application to the folder you created on the target IIS system.</span></span> <span data-ttu-id="50b70-196">建议使用的部署机制是 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="50b70-196">Web Deploy is the recommended mechanism for deployment.</span></span> <span data-ttu-id="50b70-197">下面列出了 Web 部署的替代方法。</span><span class="sxs-lookup"><span data-stu-id="50b70-197">Alternatives to Web Deploy are listed below.</span></span>

<span data-ttu-id="50b70-198">确认要部署的已发布应用未运行。</span><span class="sxs-lookup"><span data-stu-id="50b70-198">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="50b70-199">如果应用正在运行，publish 文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="50b70-199">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="50b70-200">不会进行部署，因为无法复制锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="50b70-200">Deployment can't occur because locked files can't be copied.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="50b70-201">在 Visual Studio 内使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="50b70-201">Web Deploy with Visual Studio</span></span>
<span data-ttu-id="50b70-202">要了解如何创建用于 Web 部署的发布配置文件，请参阅主题[创建 Visual Studio 和 MSBuild 的发布配置文件以部署 ASP.NET Core 应用程序](xref:publishing/web-publishing-vs#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="50b70-202">See [Create publish profiles for Visual Studio and MSBuild, to deploy ASP.NET Core apps](xref:publishing/web-publishing-vs#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="50b70-203">如果托管提供程序提供了发布配置文件或支持创建发布配置文件，请下载配置文件并使用 Visual Studio 的“发布”对话框进行导入。</span><span class="sxs-lookup"><span data-stu-id="50b70-203">If your hosting provider supplies a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog.</span></span>

![“发布”对话框页](iis/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="50b70-205">在 Visual Studio 之外使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="50b70-205">Web Deploy outside of Visual Studio</span></span>
<span data-ttu-id="50b70-206">也可以在 Visual Studio 之外从命令行使用 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="50b70-206">You can also use Web Deploy outside of Visual Studio from the command line.</span></span> <span data-ttu-id="50b70-207">有关详细信息，请参阅 [Web Deployment Tool](https://docs.microsoft.com/iis/publish/using-web-deploy/use-the-web-deployment-tool)（Web 部署工具）。</span><span class="sxs-lookup"><span data-stu-id="50b70-207">For more information, see [Web Deployment Tool](https://docs.microsoft.com/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="50b70-208">Web 部署的替代方法</span><span class="sxs-lookup"><span data-stu-id="50b70-208">Alternatives to Web Deploy</span></span>
<span data-ttu-id="50b70-209">如果不希望使用 Web 部署或未使用 Visual Studio，可使用多种其他方法将应用程序移动到托管系统，如 Xcopy、Robocopy 或 PowerShell。</span><span class="sxs-lookup"><span data-stu-id="50b70-209">If you don't wish to use Web Deploy or are not using Visual Studio, you may use any of several methods to move the application to the hosting system, such as Xcopy, Robocopy, or PowerShell.</span></span> <span data-ttu-id="50b70-210">Visual Studio 用户可以使用[发布示例](https://github.com/aspnet/vsweb-publish/blob/master/samples/samples.md)。</span><span class="sxs-lookup"><span data-stu-id="50b70-210">Visual Studio users may use the [Publish Samples](https://github.com/aspnet/vsweb-publish/blob/master/samples/samples.md).</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="50b70-211">浏览网站</span><span class="sxs-lookup"><span data-stu-id="50b70-211">Browse the website</span></span>
![Microsoft Edge 浏览器已加载 IIS 启动页。](iis/_static/browsewebsite.png)
   
>[!WARNING]
> <span data-ttu-id="50b70-213">.NET Core 应用程序通过 IIS 与 Kestrel 服务器之间的反向代理托管。</span><span class="sxs-lookup"><span data-stu-id="50b70-213">.NET Core applications are hosted via a reverse-proxy between IIS and the Kestrel server.</span></span> <span data-ttu-id="50b70-214">为了创建反向代理，web.config 文件必须存在于已部署应用程序的内容根路径（通常为应用基路径）中，该路径是向 IIS 提供的网站物理路径。</span><span class="sxs-lookup"><span data-stu-id="50b70-214">In order to create the reverse-proxy, the *web.config* file must be present at the content root path (typically the app base path) of the deployed application, which is the website physical path provided to IIS.</span></span> <span data-ttu-id="50b70-215">敏感文件存在于应用的物理路径中，包括子文件夹，如 my_application.runtimeconfig.json、my_application.xml（XML 文档注释）和 my_application.deps.json。</span><span class="sxs-lookup"><span data-stu-id="50b70-215">Sensitive files exist on the app's physical path, including subfolders, such as *my_application.runtimeconfig.json*, *my_application.xml* (XML Documentation comments), and *my_application.deps.json*.</span></span> <span data-ttu-id="50b70-216">创建 Kestrel 的反向代理需要 web.config 文件，它可以防止 IIS 为这些敏感文件和其他敏感文件提供服务。</span><span class="sxs-lookup"><span data-stu-id="50b70-216">The *web.config* file is required to create the reverse proxy to Kestrel, which prevents IIS from serving these and other sensitive files.</span></span> <span data-ttu-id="50b70-217">因此，切勿意外重命名 web.config 文件或将其从部署中删除，这一点非常重要。</span><span class="sxs-lookup"><span data-stu-id="50b70-217">**Therefore, it is important that the *web.config* file is never accidently renamed or removed from the deployment.**</span></span>

## <a name="data-protection"></a><span data-ttu-id="50b70-218">数据保护</span><span class="sxs-lookup"><span data-stu-id="50b70-218">Data protection</span></span>

<span data-ttu-id="50b70-219">在以下情形下，ASP.NET Core 应用程序在内存中存储 keyring：</span><span class="sxs-lookup"><span data-stu-id="50b70-219">An ASP.NET Core application will store the keyring in memory under the following condition:</span></span>

* <span data-ttu-id="50b70-220">网站托管在 IIS 之后。</span><span class="sxs-lookup"><span data-stu-id="50b70-220">A website is hosted behind IIS.</span></span>
* <span data-ttu-id="50b70-221">未将数据保护堆栈配置为在持久性存储中存储 keyring。</span><span class="sxs-lookup"><span data-stu-id="50b70-221">The Data Protection stack has not been configured to store the keyring in a persistent store.</span></span>

<span data-ttu-id="50b70-222">如果 keyring 存储于内存中，则当应用重启时：</span><span class="sxs-lookup"><span data-stu-id="50b70-222">If the keyring is stored in memory, when the app restarts:</span></span>

* <span data-ttu-id="50b70-223">所有 Forms 身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="50b70-223">All forms authentication tokens will be invalid.</span></span> 
* <span data-ttu-id="50b70-224">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="50b70-224">Users will need to login again on their next request.</span></span> 
* <span data-ttu-id="50b70-225">使用 keyring 保护的任何数据可一直受到保护。</span><span class="sxs-lookup"><span data-stu-id="50b70-225">Any data you protected with the keyring will no longer be unprotected.</span></span>

> [!WARNING]
> <span data-ttu-id="50b70-226">数据保护由多个 ASP.NET 中间件使用，包括用于身份验证的中间件。</span><span class="sxs-lookup"><span data-stu-id="50b70-226">Data Protection is used by several ASP.NET middlewares, including those used in authentication.</span></span> <span data-ttu-id="50b70-227">即使不从自己的代码中专门调用任何数据保护 API，也应使用部署脚本或自己的代码配置数据保护。</span><span class="sxs-lookup"><span data-stu-id="50b70-227">Even if you do not specifically call any Data Protection APIs from your own code you should configure Data Protection with a deployment script or in your own code.</span></span> <span data-ttu-id="50b70-228">如果未配置数据保护，则当应用重启时，密钥默认保存在内存中并会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="50b70-228">If you do not configure data protection, by default the keys will be held in memory and discarded when your app restarts.</span></span> <span data-ttu-id="50b70-229">重启会使 cookie 身份验证写入的任何 cookie 都失效，用户需要再次登录。</span><span class="sxs-lookup"><span data-stu-id="50b70-229">Restarting will invalidate any cookies written by the cookie authentication and users will have to login again.</span></span>

<span data-ttu-id="50b70-230">要在 IIS 下配置数据保护，必须使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="50b70-230">To configure Data Protection under IIS you must use one of the following approaches:</span></span>

* <span data-ttu-id="50b70-231">运行 [Powershell 脚本](https://github.com/aspnet/DataProtection/blob/dev/Provision-AutoGenKeys.ps1)，创建合适的注册表项（如 `.\Provision-AutoGenKeys.ps1 DefaultAppPool`）。</span><span class="sxs-lookup"><span data-stu-id="50b70-231">Run a [powershell script](https://github.com/aspnet/DataProtection/blob/dev/Provision-AutoGenKeys.ps1) to create suitable registry entries (For example,  `.\Provision-AutoGenKeys.ps1 DefaultAppPool`).</span></span> <span data-ttu-id="50b70-232">这会将密钥存储在注册表中，并使用 DPAPI 和计算机范围的密钥进行保护。</span><span class="sxs-lookup"><span data-stu-id="50b70-232">This will store keys in the registry, protected using DPAPI with a machine wide key.</span></span>
* <span data-ttu-id="50b70-233">配置 IIS 应用程序池以加载用户配置文件。</span><span class="sxs-lookup"><span data-stu-id="50b70-233">Configure the IIS Application Pool to load the user profile.</span></span> <span data-ttu-id="50b70-234">此设置位于应用程序池“高级设置”下的“进程模型”部分。</span><span class="sxs-lookup"><span data-stu-id="50b70-234">This setting is in the **Process Model** section under the **Advanced Settings** for the application pool.</span></span> <span data-ttu-id="50b70-235">将“加载用户配置文件”设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="50b70-235">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="50b70-236">这会将密钥存储在用户配置文件目录下，并使用 DPAPI 和特定于用户帐户（用于应用池）的密钥的进行保护。</span><span class="sxs-lookup"><span data-stu-id="50b70-236">This will store keys under the user profile directory, and protected using DPAPI with a key specific to the user account used for the app pool.</span></span>
* <span data-ttu-id="50b70-237">调整应用程序代码，[将文件系统用作密钥环存储](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="50b70-237">Adjust your application code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="50b70-238">使用 X509 证书保护密钥环，并确保该证书是受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="50b70-238">Use an X509 certificate to protect the key ring and ensure it is a trusted certificate.</span></span> <span data-ttu-id="50b70-239">例如，如果它是自签名证书，则必须将它放置于受信任的根存储中。</span><span class="sxs-lookup"><span data-stu-id="50b70-239">For example, if it is a self signed certificate you must place it in the Trusted Root store.</span></span>

<span data-ttu-id="50b70-240">当在 Web 场中使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="50b70-240">When using IIS in a web farm:</span></span>

* <span data-ttu-id="50b70-241">使用所有计算机都可以访问的文件共享。</span><span class="sxs-lookup"><span data-stu-id="50b70-241">Use a file share all machines can access.</span></span>
* <span data-ttu-id="50b70-242">将 X509 证书部署到每台计算机。</span><span class="sxs-lookup"><span data-stu-id="50b70-242">Deploy an X509 certificate to each machine.</span></span>  <span data-ttu-id="50b70-243">[通过代码配置数据保护](https://docs.microsoft.com/aspnet/core/security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="50b70-243">Configure [data protection in code](https://docs.microsoft.com/aspnet/core/security/data-protection/configuration/overview).</span></span>

### <a name="1-create-a-data-protection-registry-hive"></a><span data-ttu-id="50b70-244">1.创建数据保护注册表配置单元</span><span class="sxs-lookup"><span data-stu-id="50b70-244">1. Create a Data Protection Registry Hive</span></span>

<span data-ttu-id="50b70-245">ASP.NET 应用程序使用的数据保护密钥存储在应用程序外部的注册表配置单元中。</span><span class="sxs-lookup"><span data-stu-id="50b70-245">Data Protection keys used by ASP.NET applications are stored in registry hives external to the applications.</span></span> <span data-ttu-id="50b70-246">要持久保存给定应用程序的密钥，必须为应用程序的应用程序池创建注册表配置单元。</span><span class="sxs-lookup"><span data-stu-id="50b70-246">To persist the keys for a given application, you must create a registry hive for the application's application pool.</span></span>

<span data-ttu-id="50b70-247">对于独立的 IIS 安装，可以对用于 ASP.NET Core 应用程序的应用程序池使用[数据保护 Provision-AutoGenKeys.ps1 PowerShell 脚本](https://github.com/aspnet/DataProtection/blob/dev/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="50b70-247">For standalone IIS installations, you may use the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/aspnet/DataProtection/blob/dev/Provision-AutoGenKeys.ps1) for each application pool used with an ASP.NET Core application.</span></span> <span data-ttu-id="50b70-248">此脚本在 HKLM 注册表中创建一个特殊的注册表项（该注册表仅列在工作进程帐户的 ACL 中）。</span><span class="sxs-lookup"><span data-stu-id="50b70-248">This script will create a special registry key in the HKLM registry that is ACLed only to the worker process account.</span></span> <span data-ttu-id="50b70-249">使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="50b70-249">Keys are encrypted at rest using DPAPI.</span></span>

<span data-ttu-id="50b70-250">在 Web 场方案中，可以将应用程序配置为使用 UNC 路径存储其数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="50b70-250">In web farm scenarios, an application can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="50b70-251">默认情况下，数据保护密钥未加密。</span><span class="sxs-lookup"><span data-stu-id="50b70-251">By default, the data protection keys are not encrypted.</span></span> <span data-ttu-id="50b70-252">应确保这种共享的文件权限仅限于运行应用程序所用的 Windows 帐户。</span><span class="sxs-lookup"><span data-stu-id="50b70-252">You should ensure that the file permissions for such a share are limited to the Windows account the application runs as.</span></span> <span data-ttu-id="50b70-253">此外，可以选择使用 X509 证书保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="50b70-253">In addition you may choose to protect keys at rest using an X509 certificate.</span></span> <span data-ttu-id="50b70-254">你可能希望考虑这样一种机制：通过该机制，用户可以上传证书、将证书放置在用户信任的证书存储中，并确保在运行用户应用程序的所有计算机上都可使用这些证书。</span><span class="sxs-lookup"><span data-stu-id="50b70-254">You may wish to consider a mechanism to allow users to upload certificates, place them into the user's trusted certificate store, and ensure they are available on all machines the user's application will run on.</span></span> <span data-ttu-id="50b70-255">有关详细信息，请参阅[配置数据保护](xref:security/data-protection/configuration/overview#data-protection-configuring)。</span><span class="sxs-lookup"><span data-stu-id="50b70-255">See [Configuring Data Protection](xref:security/data-protection/configuration/overview#data-protection-configuring) for details.</span></span>

### <a name="2-configure-the-iis-application-pool-to-load-the-user-profile"></a><span data-ttu-id="50b70-256">2.配置 IIS 应用程序池以加载用户配置文件</span><span class="sxs-lookup"><span data-stu-id="50b70-256">2. Configure the IIS Application Pool to load the user profile</span></span>
<span data-ttu-id="50b70-257">此设置位于应用程序池“高级设置”下的“进程模型”部分。</span><span class="sxs-lookup"><span data-stu-id="50b70-257">This setting is in the Process Model section under the Advanced Settings for the application pool.</span></span> <span data-ttu-id="50b70-258">将“加载用户配置文件”设置为 True。</span><span class="sxs-lookup"><span data-stu-id="50b70-258">Set Load User Profile to True.</span></span> <span data-ttu-id="50b70-259">这会将密钥存储在用户配置文件目录下，并使用 DPAPI 和特定于用户帐户（用于应用池）的密钥的进行保护。</span><span class="sxs-lookup"><span data-stu-id="50b70-259">This will store keys under the user profile directory, and protected using DPAPI with a key specific to the user account used for the app pool.</span></span>

### <a name="3-machine-wide-policy-for-data-protection"></a><span data-ttu-id="50b70-260">3.数据保护的计算机范围的策略</span><span class="sxs-lookup"><span data-stu-id="50b70-260">3. Machine-wide policy for data protection</span></span>

<span data-ttu-id="50b70-261">数据保护系统对以下操作提供有限支持：为使用数据保护 API 的所有应用程序设置默认[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy#data-protection-configuration-machinewidepolicy)。</span><span class="sxs-lookup"><span data-stu-id="50b70-261">The data protection system has limited support for setting default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy#data-protection-configuration-machinewidepolicy) for all applications that consume the data protection APIs.</span></span> <span data-ttu-id="50b70-262">有关详细信息，请参阅[数据保护](xref:security/data-protection/index)文档。</span><span class="sxs-lookup"><span data-stu-id="50b70-262">See the [data protection](xref:security/data-protection/index) documentation for more details.</span></span>

## <a name="configuration-of-sub-applications"></a><span data-ttu-id="50b70-263">配置子应用程序</span><span class="sxs-lookup"><span data-stu-id="50b70-263">Configuration of sub-applications</span></span>

<span data-ttu-id="50b70-264">在根应用程序下添加的子应用程序不应包含 ASP.NET Core 模块作为处理程序。</span><span class="sxs-lookup"><span data-stu-id="50b70-264">Sub applications added under the root application shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="50b70-265">如果在子应用程序的 web.config 文件中将该模块添加为处理程序，则尝试浏览子应用时会收到 500.19（内部服务器错误），即引用错误的配置文件。</span><span class="sxs-lookup"><span data-stu-id="50b70-265">If you add the module as a handler in a sub-application's *web.config* file, you receive a 500.19 (Internal Server Error) referencing the faulty config file when you attempt to browse the sub app.</span></span> <span data-ttu-id="50b70-266">以下示例显示 ASP.NET Core 子应用的已发布 web.config 文件的内容：</span><span class="sxs-lookup"><span data-stu-id="50b70-266">The following example shows the contents of a published *web.config* file for an ASP.NET Core sub app:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
        arguments=".\MyApp.dll" 
        stdoutLogEnabled="false" 
        stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="50b70-267">如果想要在 ASP.NET Core 应用下托管非 ASP.NET Core 子应用，必须在子应用的 web.config 文件中显式删除继承的处理程序：</span><span class="sxs-lookup"><span data-stu-id="50b70-267">If you intend to host a non-ASP.NET Core sub app underneath an ASP.NET Core app, you must explicitly remove the inherited handler in the sub app *web.config* file:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore"/>
    </handlers>
    <aspNetCore processPath="dotnet" 
        arguments=".\MyApp.dll" 
        stdoutLogEnabled="false" 
        stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="50b70-268">有关使用 web.config 文件配置 ASP.NET Core 模块的详细信息，请参阅 [Introduction to ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module)（ASP.NET Core 模块简介）主题和 [ASP.NET Core Module configuration reference](xref:hosting/aspnet-core-module)（ASP.NET Core 模块配置参考）。</span><span class="sxs-lookup"><span data-stu-id="50b70-268">For more information on configuring the ASP.NET Core Module with the *web.config* file, see the [Introduction to ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) topic and the [ASP.NET Core Module configuration reference](xref:hosting/aspnet-core-module).</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="50b70-269">使用 web.config 配置 IIS</span><span class="sxs-lookup"><span data-stu-id="50b70-269">Configuration of IIS with web.config</span></span>

<span data-ttu-id="50b70-270">对于那些应用于反向代理配置的 IIS 功能，IIS 配置仍受 web.config 的 `<system.webServer>` 部分影响。</span><span class="sxs-lookup"><span data-stu-id="50b70-270">IIS configuration is still influenced by the `<system.webServer>` section of *web.config* for those IIS features that apply to a reverse proxy configuration.</span></span> <span data-ttu-id="50b70-271">例如，用户可能在系统级别配置了 IIS 以便使用动态压缩，但可通过应用的 web.config 文件中的 `<urlCompression>` 元素为应用禁用该设置。</span><span class="sxs-lookup"><span data-stu-id="50b70-271">For example, you may have IIS configured at the system level to use dynamic compression, but you could disable that setting for an app with the `<urlCompression>` element in the app's *web.config* file.</span></span> <span data-ttu-id="50b70-272">有关详细信息，请参阅 [`<system.webServer>` 的配置参考](https://docs.microsoft.com/iis/configuration/system.webServer/)、[ASP.NET Core Module Configuration Reference](xref:hosting/aspnet-core-module)（ASP.NET Core 模块配置参考）和 [Using IIS Modules with ASP.NET Core](xref:hosting/iis-modules)（配合使用 IIS 模块与 ASP.NET Core）。</span><span class="sxs-lookup"><span data-stu-id="50b70-272">For more information, see the [configuration reference for `<system.webServer>`](https://docs.microsoft.com/iis/configuration/system.webServer/), [ASP.NET Core Module Configuration Reference](xref:hosting/aspnet-core-module), and [Using IIS Modules with ASP.NET Core](xref:hosting/iis-modules).</span></span> <span data-ttu-id="50b70-273">如果需要为在独立应用程序池中运行的单个应用设置环境变量（IIS 10.0+ 中支持此操作），请参阅 IIS 参考文档的[环境变量 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题中的“AppCmd.exe 命令”部分。</span><span class="sxs-lookup"><span data-stu-id="50b70-273">If you need to set environment variables for individual apps running in isolated Application Pools (supported on IIS 10.0+), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="50b70-274">web.config 的配置节</span><span class="sxs-lookup"><span data-stu-id="50b70-274">Configuration sections of web.config</span></span>

<span data-ttu-id="50b70-275">.NET Framework 应用程序是使用 web.config 中的 `<system.web>`、`<appSettings>`、`<connectionStrings>` 和 `<location>` 元素配置的，ASP.NET Core 应用与其不同，是使用其他配置提供程序配置的。</span><span class="sxs-lookup"><span data-stu-id="50b70-275">Unlike .NET Framework applications that are configured with the `<system.web>`, `<appSettings>`, `<connectionStrings>`, and `<location>` elements in *web.config*, ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="50b70-276">有关详细信息，请参阅[配置](xref:fundamentals/configuration)。</span><span class="sxs-lookup"><span data-stu-id="50b70-276">For more information, see [Configuration](xref:fundamentals/configuration).</span></span>

## <a name="application-pools"></a><span data-ttu-id="50b70-277">应用程序池</span><span class="sxs-lookup"><span data-stu-id="50b70-277">Application Pools</span></span>

<span data-ttu-id="50b70-278">在单个系统上托管多个网站时，应在每个应用自己的应用程序池中运行各应用，以隔离各应用程序。</span><span class="sxs-lookup"><span data-stu-id="50b70-278">When hosting multiple websites on a single system, you should isolate the applications from each other by running each app in its own application pool.</span></span> <span data-ttu-id="50b70-279">IIS“添加网站”对话框默认执行此行为。</span><span class="sxs-lookup"><span data-stu-id="50b70-279">The IIS **Add Website** dialog defaults to this behavior.</span></span> <span data-ttu-id="50b70-280">提供站点名称时，该文本可自动传输到“应用程序池”文本框。</span><span class="sxs-lookup"><span data-stu-id="50b70-280">When you provide a **Site name**, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="50b70-281">添加网站时，会使用该站点名称创建新的应用程序池。</span><span class="sxs-lookup"><span data-stu-id="50b70-281">A new application pool will be created using the site name when you add the website.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="50b70-282">应用程序池标识</span><span class="sxs-lookup"><span data-stu-id="50b70-282">Application Pool Identity</span></span>

<span data-ttu-id="50b70-283">通过应用程序池标识帐户，可以在唯一帐户下运行应用程序，而无需创建和管理域或本地帐户。</span><span class="sxs-lookup"><span data-stu-id="50b70-283">An application pool identity account allows you to run an application under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="50b70-284">在 IIS 8.0+ 上，IIS 管理员工作进程 (WAS) 使用新应用程序池的名称创建一个虚拟帐户，并默认在此帐户下运行应用程序池的工作进程。</span><span class="sxs-lookup"><span data-stu-id="50b70-284">On IIS 8.0+, the IIS Admin Worker Process (WAS) will create a virtual account with the name of the new application pool and run the application pool's worker processes under this account by default.</span></span> <span data-ttu-id="50b70-285">在 IIS 管理控制台中，确保应用程序池“高级设置”下的“标识”设置为使用 ApplicationPoolIdentity，如下图所示。</span><span class="sxs-lookup"><span data-stu-id="50b70-285">In the IIS Management Console, under Advanced Settings for your application pool, ensure that the Identity is set to use **ApplicationPoolIdentity** as shown in the image below.</span></span>

![应用程序池“高级设置”对话框](iis/_static/apppool-identity.png)

<span data-ttu-id="50b70-287">IIS 管理进程使用 Windows 安全系统中应用程序池的名称创建安全标识符。</span><span class="sxs-lookup"><span data-stu-id="50b70-287">The IIS management process creates a secure identifier with the name of the application pool in the Windows Security System.</span></span> <span data-ttu-id="50b70-288">可使用此标识保护资源；但此标识不是真实的用户帐户，不会在 Windows 用户管理控制台中显示。</span><span class="sxs-lookup"><span data-stu-id="50b70-288">Resources can be secured by using this identity; however, this identity is not a real user account and won't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="50b70-289">如果需要向 IIS 工作进程授予对应用程序的提升的访问权限，需要为包含应用程序的目录修改访问控制列表 (ACL)。</span><span class="sxs-lookup"><span data-stu-id="50b70-289">If you need to grant the IIS worker process elevated access to your application, you will need to modify the Access Control List (ACL) for the directory containing your application.</span></span>

1. <span data-ttu-id="50b70-290">打开 Windows 资源管理器并导航到目录。</span><span class="sxs-lookup"><span data-stu-id="50b70-290">Open Windows Explorer and navigate to the directory.</span></span>

2. <span data-ttu-id="50b70-291">右键单击相应目录并单击“属性”。</span><span class="sxs-lookup"><span data-stu-id="50b70-291">Right click on the directory and click **Properties**.</span></span>

3. <span data-ttu-id="50b70-292">在“安全”选项卡下，单击“编辑”按钮，然后单击“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="50b70-292">Under the **Security** tab, click the **Edit** button and then the **Add** button.</span></span>

4. <span data-ttu-id="50b70-293">单击“位置”按钮并确保选择你的系统。</span><span class="sxs-lookup"><span data-stu-id="50b70-293">Click the **Locations** button and make sure you select your system.</span></span>

5. <span data-ttu-id="50b70-294">在“输入要选择的对象名称”文本框中输入“IIS AppPool\DefaultAppPool”。</span><span class="sxs-lookup"><span data-stu-id="50b70-294">Enter **IIS AppPool\DefaultAppPool** in **Enter the object names to select** textbox.</span></span>

  ![应用程序文件夹的“选择用户或组”对话框](iis/_static/select-users-or-groups-1.png)

6. <span data-ttu-id="50b70-296">单击“检查名称”按钮，然后单击“确定”。</span><span class="sxs-lookup"><span data-stu-id="50b70-296">Click the **Check Names** button and then click **OK**.</span></span>

  ![应用程序文件夹的“选择用户或组”对话框](iis/_static/select-users-or-groups-2.png)

<span data-ttu-id="50b70-298">也可以使用 ICACLS 工具通过命令提示符执行此操作：</span><span class="sxs-lookup"><span data-stu-id="50b70-298">You can also do this via a command prompt using **ICACLS** tool:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

## <a name="troubleshooting-tips"></a><span data-ttu-id="50b70-299">疑难解答提示</span><span class="sxs-lookup"><span data-stu-id="50b70-299">Troubleshooting tips</span></span>

<span data-ttu-id="50b70-300">要诊断 IIS 部署问题，请研究浏览器输出、通过“事件查看器”检查系统的应用程序日志，并启用 `stdout` 日志记录。</span><span class="sxs-lookup"><span data-stu-id="50b70-300">To diagnose problems with IIS deployments, study browser output, examine the system's **Application** log through **Event Viewer**, and enable `stdout` logging.</span></span> <span data-ttu-id="50b70-301">ASP.NET Core 模块日志位于 web.config 中 `<aspNetCore>` 元素的 stdoutLogFile 属性提供的路径上。属性值中提供的路径上的任何文件夹都必须存在于部署中。</span><span class="sxs-lookup"><span data-stu-id="50b70-301">The **ASP.NET Core Module** log will be found on the path provided in the *stdoutLogFile* attribute of the `<aspNetCore>` element in *web.config*. Any folders on the path provided in the attribute value must exist in the deployment.</span></span> <span data-ttu-id="50b70-302">还必须设置 stdoutLogEnabled ="true"。</span><span class="sxs-lookup"><span data-stu-id="50b70-302">You must also set *stdoutLogEnabled="true"*.</span></span> <span data-ttu-id="50b70-303">使用 `Microsoft.NET.Sdk.Web` SDK 创建 web.config 文件的应用程序默认 stdoutLogEnabled 设置为“false”，因此必须手动提供 web.config 文件或修改文件，以便启用 `stdout`日志记录。</span><span class="sxs-lookup"><span data-stu-id="50b70-303">Applications that use the `Microsoft.NET.Sdk.Web` SDK to create the *web.config* file will default the *stdoutLogEnabled* setting to *false*, so you must manually provide the *web.config* file or modify the file in order to enable `stdout` logging.</span></span>

<span data-ttu-id="50b70-304">多种常见错误只有在经过模块的 startupTimeLimit（默认值：120 秒）和 startupRetryCount（默认值：2 次）后，才会在浏览器、应用程序日志和 ASP.NET Core 模块日志中显示。</span><span class="sxs-lookup"><span data-stu-id="50b70-304">Several of the common errors do not appear in the browser, Application Log, and ASP.NET Core Module Log until the module *startupTimeLimit* (default: 120 seconds) and *startupRetryCount* (default: 2) have passed.</span></span> <span data-ttu-id="50b70-305">因此，请先等足六分钟，然后再推断出模块无法为应用程序启动进程。</span><span class="sxs-lookup"><span data-stu-id="50b70-305">Therefore, wait a full six minutes before deducing that the module has failed to start a process for the application.</span></span>

<span data-ttu-id="50b70-306">一种快速确定应用程序是否正常工作的方法是直接在 Kestrel 上运行应用程序。</span><span class="sxs-lookup"><span data-stu-id="50b70-306">One quick way to determine if the application is working properly is to run the application directly on Kestrel.</span></span> <span data-ttu-id="50b70-307">如果应用程序作为依赖框架的部署发布，则在部署文件夹（应用程序的 IIS 物理路径）中执行 dotnet my_application.dll。</span><span class="sxs-lookup"><span data-stu-id="50b70-307">If the application was published as a framework-dependent deployment, execute **dotnet my_application.dll** in the deployment folder, which is the IIS physical path to the application.</span></span> <span data-ttu-id="50b70-308">如果应用程序作为独立部署发布，则通过命令提示符在部署文件夹中直接运行应用程序的可执行文件 my_application.exe。</span><span class="sxs-lookup"><span data-stu-id="50b70-308">If the application was published as a self-contained deployment, run the application's executable directly from a command prompt, **my_application.exe**, in the deployment folder.</span></span> <span data-ttu-id="50b70-309">如果 Kestrel 在默认端口 5000 上侦听，用户就应能够在 `http://localhost:5000/` 上浏览应用程序。</span><span class="sxs-lookup"><span data-stu-id="50b70-309">If Kestrel is listening on default port 5000, you should be able to browse the application at `http://localhost:5000/`.</span></span> <span data-ttu-id="50b70-310">如果应用程序在 Kestrel 终结点地址处正常响应，则问题更可能与 IIS-ASP.NET Core Module-Kestrel 配置相关，而不太可能在于应用程序本身。</span><span class="sxs-lookup"><span data-stu-id="50b70-310">If the application responds normally at the Kestrel endpoint address, the problem is more likely related to the IIS-ASP.NET Core Module-Kestrel configuration and less likely within the application itself.</span></span>

<span data-ttu-id="50b70-311">确定 Kestrel 服务器的 IIS 反向代理是否正常运行的一种方法是：使用[静态文件中间件](xref:fundamentals/static-files)针对 wwwroot 中应用程序静态文件的样式表、脚本或映像执行简单的静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="50b70-311">One way to determine if the IIS reverse proxy to the Kestrel server is working properly is to perform a simple static file request for a stylesheet, script, or image from the application's static files in *wwwroot* using [Static File middleware](xref:fundamentals/static-files).</span></span> <span data-ttu-id="50b70-312">如果应用程序可为静态文件提供服务，但 MVC 视图和其他终结点无法工作，则问题不太可能与 IIS-ASP.NET Core Module-Kestrel 配置相关，更可能在于应用程序本身（如 MVC 路由或 500 内部服务器错误）。</span><span class="sxs-lookup"><span data-stu-id="50b70-312">If the application can serve static files but MVC Views and other endpoints are failing, the problem is less likely related to the IIS-ASP.NET Core Module-Kestrel configuration and more likely within the application itself (for example, MVC routing or 500 Internal Server Error).</span></span>

<span data-ttu-id="50b70-313">当 Kestrel 在 IIS 之后正常启动，但应用在本地成功运行后无法在系统上运行时，可以临时将环境变量添加到 web.config，以便将 `ASPNETCORE_ENVIRONMENT` 设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="50b70-313">When Kestrel starts normally behind IIS but the app won't run on the system after successfully running locally, you can temporarily add an environment variable to *web.config* to set the `ASPNETCORE_ENVIRONMENT` to `Development`.</span></span> <span data-ttu-id="50b70-314">通过此操作，只要不替代应用启动中的环境，应用在系统上运行时就会显示[开发者异常页](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="50b70-314">As long as you don't override the environment in app startup, this will allow the [developer exception page](xref:fundamentals/error-handling) to appear when the app is run on the system.</span></span> <span data-ttu-id="50b70-315">仅建议对未向 Internet 公开的暂存/测试系统使用此方式设置 `ASPNETCORE_ENVIRONMENT` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="50b70-315">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` in this way is only recommended for staging/testing systems that are not exposed to the Internet.</span></span> <span data-ttu-id="50b70-316">确保在完成时从 web.config 文件中删除环境变量。</span><span class="sxs-lookup"><span data-stu-id="50b70-316">Be sure you remove the environment variable from the *web.config* file when finished.</span></span> <span data-ttu-id="50b70-317">有关通过 web.config 为反向代理设置环境变量的信息，请参阅 [aspNetCore 的 environmentVariables 子元素](xref:hosting/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="50b70-317">For information on setting environment variables via *web.config* for the reverse proxy, see [environmentVariables child element of aspNetCore](xref:hosting/aspnet-core-module#setting-environment-variables).</span></span>

<span data-ttu-id="50b70-318">在大多数情况下，启用应用程序日志记录有助于排查应用程序问题或反向代理问题。</span><span class="sxs-lookup"><span data-stu-id="50b70-318">In most cases, enabling application logging will assist in troubleshooting problems with application or the reverse proxy.</span></span> <span data-ttu-id="50b70-319">有关详细信息，请参阅[日志记录](xref:fundamentals/logging)。</span><span class="sxs-lookup"><span data-stu-id="50b70-319">See [Logging](xref:fundamentals/logging) for more information.</span></span>

<span data-ttu-id="50b70-320">最后一项疑难解答提示涉及在开发计算机上升级 .NET Core SDK 或在应用内升级包版本后无法运行的应用。</span><span class="sxs-lookup"><span data-stu-id="50b70-320">Our last troubleshooting tip pertains to apps that fail to run after upgrading either the .NET Core SDK on the development machine or package versions within the app.</span></span> <span data-ttu-id="50b70-321">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="50b70-321">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="50b70-322">可通过以下方式解决大多数问题：删除项目中的 `bin` 和 `obj` 文件夹、清除 `%UserProfile%\.nuget\packages\` 和 `%LocalAppData%\Nuget\v3-cache` 的包缓存、还原项目和确认已在重新部署应用前完全删除系统上以前的部署。</span><span class="sxs-lookup"><span data-stu-id="50b70-322">You can fix most of these issues by deleting the `bin` and `obj` folders in the project, clearing package caches at `%UserProfile%\.nuget\packages\` and `%LocalAppData%\Nuget\v3-cache`, restoring the project, and confirming that your prior deployment on the system has been completely deleted prior to re-deploying the app.</span></span>

>[!TIP]
> <span data-ttu-id="50b70-323">清除包缓存的一种便捷方法是从 [NuGet.org](https://www.nuget.org/) 获取 `NuGet.exe` 工具，将其添加到系统路径，然后从命令提示符执行 `nuget locals all -clear`。</span><span class="sxs-lookup"><span data-stu-id="50b70-323">A convenient way to clear package caches is to obtain the `NuGet.exe` tool from [NuGet.org](https://www.nuget.org/), add it to your system PATH, and execute `nuget locals all -clear` from a command prompt.</span></span>

## <a name="common-errors"></a><span data-ttu-id="50b70-324">常见错误</span><span class="sxs-lookup"><span data-stu-id="50b70-324">Common errors</span></span>

<span data-ttu-id="50b70-325">以下不是错误的完整列表。</span><span class="sxs-lookup"><span data-stu-id="50b70-325">The following is not a complete list of errors.</span></span> <span data-ttu-id="50b70-326">如果遇到此处未列出的错误，请在下方的注释部分留下详细的错误消息。</span><span class="sxs-lookup"><span data-stu-id="50b70-326">Should you encounter an error not listed here, please leave a detailed error message in the comments section below.</span></span>

### <a name="installer-unable-to-obtain-vc-redistributable"></a><span data-ttu-id="50b70-327">安装程序无法获取 VC++ Redistributable</span><span class="sxs-lookup"><span data-stu-id="50b70-327">Installer unable to obtain VC++ Redistributable</span></span>

* <span data-ttu-id="50b70-328">**安装程序异常：**0x80072efd 或 0x80072f76 - 未指定的错误</span><span class="sxs-lookup"><span data-stu-id="50b70-328">**Installer Exception:** 0x80072efd or 0x80072f76 - Unspecified error</span></span>

* <span data-ttu-id="50b70-329">**安装程序日志异常&#8224;：**0x80072efd 或 0x80072f76 错误：未能执行 EXE 包</span><span class="sxs-lookup"><span data-stu-id="50b70-329">**Installer Log Exception&#8224;:** Error 0x80072efd or 0x80072f76: Failed to execute EXE package</span></span>

  <span data-ttu-id="50b70-330">&#8224;此日志位于 C:\Users\\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{timestamp}.log。</span><span class="sxs-lookup"><span data-stu-id="50b70-330">&#8224;The log is located at C:\Users\\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{timestamp}.log.</span></span>

<span data-ttu-id="50b70-331">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="50b70-331">Troubleshooting:</span></span>

* <span data-ttu-id="50b70-332">如果在安装服务器托管捆绑包时系统无法访问 Internet，则在系统阻止安装程序获取 Microsoft Visual C++ 2015 Redistributable 时会出现此异常。</span><span class="sxs-lookup"><span data-stu-id="50b70-332">If the system does not have Internet access while installing the server hosting bundle, this exception will occur when the installer is prevented from obtaining the *Microsoft Visual C++ 2015 Redistributable*.</span></span> <span data-ttu-id="50b70-333">可从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=53840)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="50b70-333">You may obtain an installer from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span></span> <span data-ttu-id="50b70-334">如果安装程序失败，可能不会收到托管依赖框架的部署所需的 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="50b70-334">If the installer fails, you may not receive the .NET Core runtime required to host framework-dependent deployments.</span></span> <span data-ttu-id="50b70-335">如果计划托管依赖框架的部署，请在“程序和功能”中确认安装了运行时。</span><span class="sxs-lookup"><span data-stu-id="50b70-335">If you plan to host framework-dependent deployments, confirm that the runtime is installed in Programs &amp; Features.</span></span> <span data-ttu-id="50b70-336">可从 [.NET 下载](https://www.microsoft.com/net/download/core)获取运行时安装程序。</span><span class="sxs-lookup"><span data-stu-id="50b70-336">You may obtain a runtime installer from [.NET Downloads](https://www.microsoft.com/net/download/core).</span></span> <span data-ttu-id="50b70-337">安装运行时后，重启系统，或通过从命令提示符依次执行 net stop was /y 和 net start w3svc 来重启 IIS。</span><span class="sxs-lookup"><span data-stu-id="50b70-337">After installing the runtime, restart the system or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

### <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a><span data-ttu-id="50b70-338">OS 升级删除了 32 位 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="50b70-338">OS upgrade removed the 32-bit ASP.NET Core Module</span></span>

* <span data-ttu-id="50b70-339">**应用程序日志：**未能加载模块 DLL C:\WINDOWS\system32\inetsrv\aspnetcore.dll。</span><span class="sxs-lookup"><span data-stu-id="50b70-339">**Application Log:** The Module DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** failed to load.</span></span> <span data-ttu-id="50b70-340">该数据是个错误。</span><span class="sxs-lookup"><span data-stu-id="50b70-340">The data is the error.</span></span>

<span data-ttu-id="50b70-341">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="50b70-341">Troubleshooting:</span></span>

* <span data-ttu-id="50b70-342">OS 升级期间不会保留 C:\Windows\SysWOW64\inetsrv 目录中的非 OS 文件。</span><span class="sxs-lookup"><span data-stu-id="50b70-342">Non-OS files in the **C:\Windows\SysWOW64\inetsrv** directory are not preserved during an OS upgrade.</span></span> <span data-ttu-id="50b70-343">如果在 OS 升级前已安装 ASP.NET Core 模块，且随后在 OS 升级后尝试在 32 位模式下运行任何应用程序池，则会遇到此问题。</span><span class="sxs-lookup"><span data-stu-id="50b70-343">If you have the ASP.NET Core Module installed prior to an OS upgrade and then try to run any AppPool in 32-bit mode after an OS upgrade, you will encounter this issue.</span></span> <span data-ttu-id="50b70-344">在 OS 升级后修复 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="50b70-344">After an OS upgrade, repair the ASP.NET Core Module.</span></span> <span data-ttu-id="50b70-345">请参阅[安装 .NET Core Windows Server 托管捆绑包](#install-the-net-core-windows-server-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="50b70-345">See [Install the .NET Core Windows Server Hosting bundle](#install-the-net-core-windows-server-hosting-bundle).</span></span> <span data-ttu-id="50b70-346">运行安装程序时，选择“修复”。</span><span class="sxs-lookup"><span data-stu-id="50b70-346">Select **Repair** when you run the installer.</span></span>

### <a name="platform-conflicts-with-rid"></a><span data-ttu-id="50b70-347">平台与 RID 冲突</span><span class="sxs-lookup"><span data-stu-id="50b70-347">Platform conflicts with RID</span></span>

* <span data-ttu-id="50b70-348">**浏览器：**HTTP 错误 502.5 - 进程失败</span><span class="sxs-lookup"><span data-stu-id="50b70-348">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="50b70-349">**应用程序日志：**物理根路径为 'C:\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' 未能通过 '"C:\\{PATH}\my_application.{exe|dll}" ' 命令行启动进程，ErrorCode = '0x80004005 : ff。</span><span class="sxs-lookup"><span data-stu-id="50b70-349">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' with physical root 'C:\{PATH}\' failed to start process with commandline '"C:\\{PATH}\my_application.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span></span>

* <span data-ttu-id="50b70-350">**ASP.NET Core 模块日志：**未经处理的异常: System.BadImageFormatException: 无法加载文件或程序集 "my_application.dll"。</span><span class="sxs-lookup"><span data-stu-id="50b70-350">**ASP.NET Core Module Log:** Unhandled Exception: System.BadImageFormatException: Could not load file or assembly 'my_application.dll'.</span></span> <span data-ttu-id="50b70-351">试图加载的程序的格式不正确。</span><span class="sxs-lookup"><span data-stu-id="50b70-351">An attempt was made to load a program with an incorrect format.</span></span>

<span data-ttu-id="50b70-352">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="50b70-352">Troubleshooting:</span></span>

* <span data-ttu-id="50b70-353">确认应用程序在 Kestrel 上本地运行。</span><span class="sxs-lookup"><span data-stu-id="50b70-353">Confirm that the application runs locally on Kestrel.</span></span> <span data-ttu-id="50b70-354">进程失败可能是由应用程序的内部问题导致的。</span><span class="sxs-lookup"><span data-stu-id="50b70-354">A process failure might be the result of a problem within the application.</span></span> <span data-ttu-id="50b70-355">有关详细信息，请参阅[疑难解答提示](#troubleshooting-tips)。</span><span class="sxs-lookup"><span data-stu-id="50b70-355">For more information, see [Troubleshooting tips](#troubleshooting-tips).</span></span>

* <span data-ttu-id="50b70-356">确认未在 .csproj 中设置与 RID 冲突的 `<PlatformTarget>`。</span><span class="sxs-lookup"><span data-stu-id="50b70-356">Confirm that you didn't set a `<PlatformTarget>` in your *.csproj* that conflicts with the RID.</span></span> <span data-ttu-id="50b70-357">例如，请勿通过使用 dotnet publish -c Release -r win10-x64 或通过在 .csproj 中将 `<RuntimeIdentifiers>` 设置为 `win10-x64` 来指定 `x86` 的 `<PlatformTarget>` 和以 `win10-x64` 的 RID 进行发布。</span><span class="sxs-lookup"><span data-stu-id="50b70-357">For example, don't specify a `<PlatformTarget>` of `x86` and publish with an RID of `win10-x64`, either by using *dotnet publish -c Release -r win10-x64* or by setting the `<RuntimeIdentifiers>` in your *.csproj* to `win10-x64`.</span></span> <span data-ttu-id="50b70-358">项目在不显示任何警告或错误的情况下发布，但会失败，且系统上出现上述记录的异常。</span><span class="sxs-lookup"><span data-stu-id="50b70-358">The project will publish without warning or error but fail with the above logged exceptions on the system.</span></span>

* <span data-ttu-id="50b70-359">如果在升级应用程序和部署更新的程序集时，Azure 应用部署出现此异常，请从先前的部署中手动删除所有文件。</span><span class="sxs-lookup"><span data-stu-id="50b70-359">If this exception occurs for an Azure Apps deployment when upgrading an application and deploying newer assemblies, manually delete all files from the prior deployment.</span></span> <span data-ttu-id="50b70-360">部署升级的应用时，延迟的不兼容程序集可能造成 `System.BadImageFormatException` 异常。</span><span class="sxs-lookup"><span data-stu-id="50b70-360">Lingering incompatible assemblies can result in a `System.BadImageFormatException` exception when deploying an upgraded app.</span></span>

### <a name="uri-endpoint-wrong-or-stopped-website"></a><span data-ttu-id="50b70-361">URI 终结点错误或网站已停止</span><span class="sxs-lookup"><span data-stu-id="50b70-361">URI endpoint wrong or stopped website</span></span>

* <span data-ttu-id="50b70-362">**浏览器：**ERR_CONNECTION_REFUSED</span><span class="sxs-lookup"><span data-stu-id="50b70-362">**Browser:** ERR_CONNECTION_REFUSED</span></span>

* <span data-ttu-id="50b70-363">**应用程序日志：**没有任何条目</span><span class="sxs-lookup"><span data-stu-id="50b70-363">**Application Log:** No entry</span></span>

* <span data-ttu-id="50b70-364">**ASP.NET Core 模块日志：**未创建日志文件</span><span class="sxs-lookup"><span data-stu-id="50b70-364">**ASP.NET Core Module Log:** Log file not created</span></span>

<span data-ttu-id="50b70-365">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="50b70-365">Troubleshooting:</span></span>

* <span data-ttu-id="50b70-366">确认对应用程序使用的 URI 终结点正确无误。</span><span class="sxs-lookup"><span data-stu-id="50b70-366">Confirm you are using the correct URI endpoint for the application.</span></span> <span data-ttu-id="50b70-367">检查绑定。</span><span class="sxs-lookup"><span data-stu-id="50b70-367">Check your bindings.</span></span>

* <span data-ttu-id="50b70-368">确认 IIS 网站未处于“已停止”状态。</span><span class="sxs-lookup"><span data-stu-id="50b70-368">Confirm that the IIS website is not in the *Stopped* state.</span></span>

### <a name="corewebengine-or-w3svc-server-features-disabled"></a><span data-ttu-id="50b70-369">已禁用 CoreWebEngine 或 W3SVC 服务器功能</span><span class="sxs-lookup"><span data-stu-id="50b70-369">CoreWebEngine or W3SVC server features disabled</span></span>

* <span data-ttu-id="50b70-370">**OS 异常：**必须安装 IIS 7.0 CoreWebEngine 和 W3SVC 功能，以便使用 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="50b70-370">**OS Exception:** The IIS 7.0 CoreWebEngine and W3SVC features must be installed to use the ASP.NET Core Module.</span></span>

<span data-ttu-id="50b70-371">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="50b70-371">Troubleshooting:</span></span>

* <span data-ttu-id="50b70-372">确认启用了适当的角色和功能。</span><span class="sxs-lookup"><span data-stu-id="50b70-372">Confirm that you have enabled the proper role and features.</span></span> <span data-ttu-id="50b70-373">请参阅 [IIS 配置](#iis-configuration)。</span><span class="sxs-lookup"><span data-stu-id="50b70-373">See [IIS Configuration](#iis-configuration).</span></span>

### <a name="incorrect-website-physical-path-or-application-missing"></a><span data-ttu-id="50b70-374">网站物理路径不正确或缺少应用程序</span><span class="sxs-lookup"><span data-stu-id="50b70-374">Incorrect website physical path or application missing</span></span>

* <span data-ttu-id="50b70-375">**浏览器：**403 禁止访问 - 访问被拒绝 --或者-- 403.14 禁止访问 - Web 服务器被配置为不列出此目录的内容。</span><span class="sxs-lookup"><span data-stu-id="50b70-375">**Browser:** 403 Forbidden - Access is denied **--OR--** 403.14 Forbidden - The Web server is configured to not list the contents of this directory.</span></span>

* <span data-ttu-id="50b70-376">**应用程序日志：**没有任何条目</span><span class="sxs-lookup"><span data-stu-id="50b70-376">**Application Log:** No entry</span></span>

* <span data-ttu-id="50b70-377">**ASP.NET Core 模块日志：**未创建日志文件</span><span class="sxs-lookup"><span data-stu-id="50b70-377">**ASP.NET Core Module Log:** Log file not created</span></span>

<span data-ttu-id="50b70-378">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="50b70-378">Troubleshooting:</span></span>

* <span data-ttu-id="50b70-379">检查 IIS 网站的“基本设置”和物理应用程序文件夹。</span><span class="sxs-lookup"><span data-stu-id="50b70-379">Check the IIS website **Basic Settings** and the physical application folder.</span></span> <span data-ttu-id="50b70-380">确认应用程序所在的文件夹位于 IIS 网站的物理路径中。</span><span class="sxs-lookup"><span data-stu-id="50b70-380">Confirm that the application is in the folder at the IIS website **Physical path**.</span></span>

### <a name="incorrect-role-module-not-installed-or-incorrect-permissions"></a><span data-ttu-id="50b70-381">角色不正确、未安装模块或权限不正确</span><span class="sxs-lookup"><span data-stu-id="50b70-381">Incorrect role, module not installed, or incorrect permissions</span></span>

* <span data-ttu-id="50b70-382">**浏览器：**500.19 内部服务器错误 - 无法访问请求的页面，因为该页面的相关配置数据无效。</span><span class="sxs-lookup"><span data-stu-id="50b70-382">**Browser:** 500.19 Internal Server Error - The requested page cannot be accessed because the related configuration data for the page is invalid.</span></span>

* <span data-ttu-id="50b70-383">**应用程序日志：**没有任何条目</span><span class="sxs-lookup"><span data-stu-id="50b70-383">**Application Log:** No entry</span></span>

* <span data-ttu-id="50b70-384">**ASP.NET Core 模块日志：**未创建日志文件</span><span class="sxs-lookup"><span data-stu-id="50b70-384">**ASP.NET Core Module Log:** Log file not created</span></span>

<span data-ttu-id="50b70-385">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="50b70-385">Troubleshooting:</span></span>

* <span data-ttu-id="50b70-386">确认启用了适当的角色。</span><span class="sxs-lookup"><span data-stu-id="50b70-386">Confirm that you have enabled the proper role.</span></span> <span data-ttu-id="50b70-387">请参阅 [IIS 配置](#iis-configuration)。</span><span class="sxs-lookup"><span data-stu-id="50b70-387">See [IIS Configuration](#iis-configuration).</span></span>

* <span data-ttu-id="50b70-388">检查“程序和功能”，确认已安装Microsoft ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="50b70-388">Check **Programs &amp; Features** and confirm that the **Microsoft ASP.NET Core Module** has been installed.</span></span> <span data-ttu-id="50b70-389">如果已安装程序列表中没有 Microsoft ASP.NET Core 模块，请安装该模块。</span><span class="sxs-lookup"><span data-stu-id="50b70-389">If the **Microsoft ASP.NET Core Module** is not present in the list of installed programs, install the module.</span></span> <span data-ttu-id="50b70-390">请参阅[安装 .NET Core Windows Server 托管捆绑包](#install-the-net-core-windows-server-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="50b70-390">See [Install the .NET Core Windows Server Hosting bundle](#install-the-net-core-windows-server-hosting-bundle).</span></span>

* <span data-ttu-id="50b70-391">请确保将“应用程序池”>“进程模型”>“标识”设置为 ApplicationPoolIdentity，或确保自定义标识具有访问应用程序部署文件夹的相应权限。</span><span class="sxs-lookup"><span data-stu-id="50b70-391">Make sure that the **Application Pool > Process Model > Identity** is set to **ApplicationPoolIdentity** or your custom identity has the correct permissions to access the application's deployment folder.</span></span>

### <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a><span data-ttu-id="50b70-392">processPath 不正确、缺少 PATH 变量、未安装托管捆绑包、未重启系统/IIS、未安装 VC++ Redistributable 或 dotnet.exe 访问冲突</span><span class="sxs-lookup"><span data-stu-id="50b70-392">Incorrect processPath, missing PATH variable, hosting bundle not installed, system/IIS not restarted, VC++ Redistributable not installed, or dotnet.exe access violation</span></span>

* <span data-ttu-id="50b70-393">**浏览器：**HTTP 错误 502.5 - 进程失败</span><span class="sxs-lookup"><span data-stu-id="50b70-393">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="50b70-394">**应用程序日志：**物理根路径为 'C:\\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' 应用程序未能通过 '".\my_application.exe" ' 命令行启动进程，ErrorCode = '0x80070002 : 0。</span><span class="sxs-lookup"><span data-stu-id="50b70-394">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' with physical root 'C:\\{PATH}\' failed to start process with commandline '".\my_application.exe" ', ErrorCode = '0x80070002 : 0.</span></span>

* <span data-ttu-id="50b70-395">**ASP.NET Core 模块日志：**已创建日志文件，但该日志文件为空</span><span class="sxs-lookup"><span data-stu-id="50b70-395">**ASP.NET Core Module Log:** Log file created but empty</span></span>

<span data-ttu-id="50b70-396">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="50b70-396">Troubleshooting:</span></span>

* <span data-ttu-id="50b70-397">确认应用程序在 Kestrel 上本地运行。</span><span class="sxs-lookup"><span data-stu-id="50b70-397">Confirm that the application runs locally on Kestrel.</span></span> <span data-ttu-id="50b70-398">进程失败可能是由应用程序的内部问题导致的。</span><span class="sxs-lookup"><span data-stu-id="50b70-398">A process failure might be the result of a problem within the application.</span></span> <span data-ttu-id="50b70-399">有关详细信息，请参阅[疑难解答提示](#troubleshooting-tips)。</span><span class="sxs-lookup"><span data-stu-id="50b70-399">For more information, see [Troubleshooting tips](#troubleshooting-tips).</span></span>

* <span data-ttu-id="50b70-400">检查 web.config 中 `<aspNetCore>` 元素的 processPath 属性，确认它为 dotnet（对于依赖框架的部署）或 .\my_application.exe（对于独立部署）。</span><span class="sxs-lookup"><span data-stu-id="50b70-400">Check the *processPath* attribute on the `<aspNetCore>` element in *web.config* to confirm that it is *dotnet* for a framework-dependent deployment or *.\my_application.exe* for a self-contained deployment.</span></span>

* <span data-ttu-id="50b70-401">对于依赖框架的部署，可能无法通过路径设置访问 dotnet.exe。</span><span class="sxs-lookup"><span data-stu-id="50b70-401">For a framework-dependent deployment, *dotnet.exe* might not be accessible via the PATH settings.</span></span> <span data-ttu-id="50b70-402">确认系统路径设置中存在 *C:\Program Files\dotnet\*。</span><span class="sxs-lookup"><span data-stu-id="50b70-402">Confirm that *C:\Program Files\dotnet\* exists in the System PATH settings.</span></span>

* <span data-ttu-id="50b70-403">对于依赖框架的部署，应用程序池的用户标识可能无法访问 dotnet.exe。</span><span class="sxs-lookup"><span data-stu-id="50b70-403">For a framework-dependent deployment, *dotnet.exe* might not be accessible for the user identity of the Application Pool.</span></span> <span data-ttu-id="50b70-404">确认应用程序池用户标识具有访问 C:\Program Files\dotnet 目录的权限。</span><span class="sxs-lookup"><span data-stu-id="50b70-404">Confirm that the AppPool user identity has access to the *C:\Program Files\dotnet* directory.</span></span> <span data-ttu-id="50b70-405">确认没有为应用程序池用户标识配置针对 C:\Program Files\dotnet 和应用程序目录的拒绝规则。</span><span class="sxs-lookup"><span data-stu-id="50b70-405">Confirm that there are no deny rules configured for the AppPool user identity on the *C:\Program Files\dotnet* and application directories.</span></span>

* <span data-ttu-id="50b70-406">你可能已部署依赖框架的部署并安装了 .NET Core，但未重启 IIS。</span><span class="sxs-lookup"><span data-stu-id="50b70-406">You may have deployed a framework-dependent deployment and installed .NET Core without restarting IIS.</span></span> <span data-ttu-id="50b70-407">重启服务器，或通过从命令提示符依次执行 net stop was /y 和 net start w3svc 来重启 IIS。</span><span class="sxs-lookup"><span data-stu-id="50b70-407">Either restart the server or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="50b70-408">你可能已部署依赖框架的部署，但未在托管系统上安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="50b70-408">You may have deployed a framework-dependent deployment without installing the .NET Core runtime on the hosting system.</span></span> <span data-ttu-id="50b70-409">如果尝试部署依赖框架的部署且尚未安装 .NET Core 运行时，请在系统上运行 .NET Core Windows Server 托管捆绑包安装程序。</span><span class="sxs-lookup"><span data-stu-id="50b70-409">If you're attempting to deploy a framework-dependent deployment and have not installed the .NET Core runtime, run the **.NET Core Windows Server Hosting bundle installer** on the system.</span></span> <span data-ttu-id="50b70-410">请参阅[安装 .NET Core Windows Server 托管捆绑包](#install-the-net-core-windows-server-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="50b70-410">See [Install the .NET Core Windows Server Hosting bundle](#install-the-net-core-windows-server-hosting-bundle).</span></span> <span data-ttu-id="50b70-411">如果在没有 Internet 连接的情况下尝试在系统上安装 .NET Core 运行时，请从 [.NET 下载](https://www.microsoft.com/net/download/core)获取运行时并运行托管捆绑包安装程序，以安装 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="50b70-411">If you are attempting to install the .NET Core runtime on a system without an Internet connection, obtain the runtime from [.NET Downloads](https://www.microsoft.com/net/download/core) and run the hosting bundle installer to install the ASP.NET Core Module.</span></span> <span data-ttu-id="50b70-412">重启系统，或通过从命令提示符依次执行 net stop was /y 和 net start w3svc 来重启 IIS，完成安装。</span><span class="sxs-lookup"><span data-stu-id="50b70-412">Complete the installation by restarting the system or restarting IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="50b70-413">你可能已部署依赖框架的部署并安装了 .NET Core，但未重启系统/IIS。</span><span class="sxs-lookup"><span data-stu-id="50b70-413">You may have deployed a framework-dependent deployment and installed .NET Core without restarting the system/IIS.</span></span> <span data-ttu-id="50b70-414">重启系统，或通过从命令提示符依次执行 net stop was /y 和 net start w3svc 来重启 IIS。</span><span class="sxs-lookup"><span data-stu-id="50b70-414">Either restart the system or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="50b70-415">你可能已部署依赖框架的部署，但系统上未安装 Microsoft Visual C++ 2015 Redistributable (x64)。</span><span class="sxs-lookup"><span data-stu-id="50b70-415">You may have deployed a framework-dependent deployment and the *Microsoft Visual C++ 2015 Redistributable (x64)* is not installed on the system.</span></span> <span data-ttu-id="50b70-416">可从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=53840)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="50b70-416">You may obtain an installer from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span></span>

### <a name="incorrect-arguments-of-aspnetcore-element"></a><span data-ttu-id="50b70-417">\<aspNetCore\> 元素的参数不正确</span><span class="sxs-lookup"><span data-stu-id="50b70-417">Incorrect arguments of \<aspNetCore\> element</span></span>

* <span data-ttu-id="50b70-418">**浏览器：**HTTP 错误 502.5 - 进程失败</span><span class="sxs-lookup"><span data-stu-id="50b70-418">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="50b70-419">**应用程序日志：**物理根路径为 'C:\\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' 未能通过 '"dotnet" .\my_application.dll' 命令行启动进程，ErrorCode = '0x80004005 : 80008081。</span><span class="sxs-lookup"><span data-stu-id="50b70-419">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' with physical root 'C:\\{PATH}\' failed to start process with commandline '"dotnet" .\my_application.dll', ErrorCode = '0x80004005 : 80008081.</span></span>

* <span data-ttu-id="50b70-420">**ASP.NET Core 模块日志：**要执行的应用程序不存在: "PATH\my_application.dll"</span><span class="sxs-lookup"><span data-stu-id="50b70-420">**ASP.NET Core Module Log:** The application to execute does not exist: 'PATH\my_application.dll'</span></span>

<span data-ttu-id="50b70-421">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="50b70-421">Troubleshooting:</span></span>

* <span data-ttu-id="50b70-422">确认应用程序在 Kestrel 上本地运行。</span><span class="sxs-lookup"><span data-stu-id="50b70-422">Confirm that the application runs locally on Kestrel.</span></span> <span data-ttu-id="50b70-423">进程失败可能是由应用程序的内部问题导致的。</span><span class="sxs-lookup"><span data-stu-id="50b70-423">A process failure might be the result of a problem within the application.</span></span> <span data-ttu-id="50b70-424">有关详细信息，请参阅[疑难解答提示](#troubleshooting-tips)。</span><span class="sxs-lookup"><span data-stu-id="50b70-424">For more information, see [Troubleshooting tips](#troubleshooting-tips).</span></span>

* <span data-ttu-id="50b70-425">检查 web.config 中 `<aspNetCore>` 元素的 arguments 属性，确认该属性 (a) 为 .\my_applciation.dll（对于依赖框架的部署）；或 (b) 不存在、为空字符串 (arguments="")，或为应用程序参数列表 (arguments="arg1, arg2, ...")（对于独立部署）。</span><span class="sxs-lookup"><span data-stu-id="50b70-425">Examine the *arguments* attribute on the `<aspNetCore>` element in *web.config* to confirm that it is either (a) *.\my_applciation.dll* for a framework-dependent deployment; or (b) not present, an empty string (*arguments=""*), or a list of your application's arguments (*arguments="arg1, arg2, ..."*) for a self-contained deployment.</span></span>

### <a name="missing-net-framework-version"></a><span data-ttu-id="50b70-426">缺少 .NET Framework 版本</span><span class="sxs-lookup"><span data-stu-id="50b70-426">Missing .NET Framework version</span></span>

* <span data-ttu-id="50b70-427">**浏览器：**502.3 错误网关 - 尝试路由请求时出现连接错误。</span><span class="sxs-lookup"><span data-stu-id="50b70-427">**Browser:** 502.3 Bad Gateway - There was a connection error while trying to route the request.</span></span>

* <span data-ttu-id="50b70-428">**应用程序日志：**ErrorCode = 物理根路径为 'C:\\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' 未能通过 '"dotnet" .\my_application.dll' 命令行启动进程，ErrorCode = '0x80004005 : 80008081。</span><span class="sxs-lookup"><span data-stu-id="50b70-428">**Application Log:** ErrorCode = Application 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' with physical root 'C:\\{PATH}\' failed to start process with commandline '"dotnet" .\my_application.dll', ErrorCode = '0x80004005 : 80008081.</span></span>

* <span data-ttu-id="50b70-429">**ASP.NET Core 模块日志：**缺少方法、文件或程序集异常。</span><span class="sxs-lookup"><span data-stu-id="50b70-429">**ASP.NET Core Module Log:** Missing method, file, or assembly exception.</span></span> <span data-ttu-id="50b70-430">在异常中指定的方法、文件或程序集是 .NET Framework 方法、文件或程序集。</span><span class="sxs-lookup"><span data-stu-id="50b70-430">The method, file, or assembly specified in the exception is a .NET Framework method, file, or assembly.</span></span>

<span data-ttu-id="50b70-431">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="50b70-431">Troubleshooting:</span></span>

* <span data-ttu-id="50b70-432">安装系统缺少的 .NET Framework 版本。</span><span class="sxs-lookup"><span data-stu-id="50b70-432">Install the .NET Framework version missing from the system.</span></span>

* <span data-ttu-id="50b70-433">对于依赖框架的部署，确认已在系统上安装正确的运行时。</span><span class="sxs-lookup"><span data-stu-id="50b70-433">For a framework-dependent deployment, confirm that you have the correct runtime installed on the system.</span></span> <span data-ttu-id="50b70-434">例如，如果将项目从 1.0 升级到 1.1，部署到托管系统，并收到此异常，请确保在托管系统上安装 1.1 框架。</span><span class="sxs-lookup"><span data-stu-id="50b70-434">For example if you upgrade a project from 1.0 to 1.1, deploy to the hosting system, and receive this exception, ensure you install the 1.1 framework on the hosting system.</span></span>

### <a name="stopped-application-pool"></a><span data-ttu-id="50b70-435">应用程序池已停止</span><span class="sxs-lookup"><span data-stu-id="50b70-435">Stopped Application Pool</span></span>

* <span data-ttu-id="50b70-436">**浏览器：**503 服务不可用</span><span class="sxs-lookup"><span data-stu-id="50b70-436">**Browser:** 503 Service Unavailable</span></span>

* <span data-ttu-id="50b70-437">**应用程序日志：**没有任何条目</span><span class="sxs-lookup"><span data-stu-id="50b70-437">**Application Log:** No entry</span></span>

* <span data-ttu-id="50b70-438">**ASP.NET Core 模块日志：**未创建日志文件</span><span class="sxs-lookup"><span data-stu-id="50b70-438">**ASP.NET Core Module Log:** Log file not created</span></span>

<span data-ttu-id="50b70-439">疑难解答</span><span class="sxs-lookup"><span data-stu-id="50b70-439">Troubleshooting</span></span>

* <span data-ttu-id="50b70-440">确认应用程序池未处于“已停止”状态。</span><span class="sxs-lookup"><span data-stu-id="50b70-440">Confirm that the Application Pool is not in the *Stopped* state.</span></span>

### <a name="iis-integration-middleware-not-implemented"></a><span data-ttu-id="50b70-441">未实现 IIS 集成中间件</span><span class="sxs-lookup"><span data-stu-id="50b70-441">IIS Integration middleware not implemented</span></span>

* <span data-ttu-id="50b70-442">**浏览器：**HTTP 错误 502.5 - 进程失败</span><span class="sxs-lookup"><span data-stu-id="50b70-442">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="50b70-443">**应用程序日志：**物理根路径为 'C:\\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' 通过 '"C:\\{PATH}\my_application.{exe|dll}" ' 命令行创建了进程，但该进程出现故障、未响应或未在给定的端口 '{PORT}' 上侦听，ErrorCode = '0x800705b4'</span><span class="sxs-lookup"><span data-stu-id="50b70-443">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' with physical root 'C:\\{PATH}\' created process with commandline '"C:\\{PATH}\my_application.{exe|dll}" ' but either crashed or did not reponse or did not listen on the given port '{PORT}', ErrorCode = '0x800705b4'</span></span>

* <span data-ttu-id="50b70-444">**ASP.NET Core 模块日志：**已创建日志文件，且该日志文件显示正常操作。</span><span class="sxs-lookup"><span data-stu-id="50b70-444">**ASP.NET Core Module Log:** Log file created and shows normal operation.</span></span>

<span data-ttu-id="50b70-445">疑难解答</span><span class="sxs-lookup"><span data-stu-id="50b70-445">Troubleshooting</span></span>

* <span data-ttu-id="50b70-446">确认应用程序在 Kestrel 上本地运行。</span><span class="sxs-lookup"><span data-stu-id="50b70-446">Confirm that the application runs locally on Kestrel.</span></span> <span data-ttu-id="50b70-447">进程失败可能是由应用程序的内部问题导致的。</span><span class="sxs-lookup"><span data-stu-id="50b70-447">A process failure might be the result of a problem within the application.</span></span> <span data-ttu-id="50b70-448">有关详细信息，请参阅[疑难解答提示](#troubleshooting-tips)。</span><span class="sxs-lookup"><span data-stu-id="50b70-448">For more information, see [Troubleshooting tips](#troubleshooting-tips).</span></span>

* <span data-ttu-id="50b70-449">在应用程序的 WebHostBuilder() 上调用 .UseIISIntegration() 方法，确认已正确引用 IIS 集成中间件。</span><span class="sxs-lookup"><span data-stu-id="50b70-449">Confirm that you have correctly referenced the IIS Integration middleware by calling the *.UseIISIntegration()* method on the application's *WebHostBuilder()*.</span></span>

### <a name="sub-application-includes-a-handlers-section"></a><span data-ttu-id="50b70-450">子应用程序包括 \<handlers\> 部分</span><span class="sxs-lookup"><span data-stu-id="50b70-450">Sub-application includes a \<handlers\> section</span></span>

* <span data-ttu-id="50b70-451">**浏览器：**HTTP 错误 500.19 - 内部服务器错误</span><span class="sxs-lookup"><span data-stu-id="50b70-451">**Browser:** HTTP Error 500.19 - Internal Server Error</span></span>

* <span data-ttu-id="50b70-452">**应用程序日志：**没有任何条目</span><span class="sxs-lookup"><span data-stu-id="50b70-452">**Application Log:** No entry</span></span>

* <span data-ttu-id="50b70-453">**ASP.NET Core 模块日志：**已创建日志文件，且该日志文件显示根应用程序的正常操作。</span><span class="sxs-lookup"><span data-stu-id="50b70-453">**ASP.NET Core Module Log:** Log file created and shows normal operation for the root application.</span></span> <span data-ttu-id="50b70-454">没有为子应用程序创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="50b70-454">Log file not created for the sub-application.</span></span>

<span data-ttu-id="50b70-455">疑难解答</span><span class="sxs-lookup"><span data-stu-id="50b70-455">Troubleshooting</span></span>

* <span data-ttu-id="50b70-456">确认子应用程序的 web.config 文件不包括 `<handlers>` 部分。</span><span class="sxs-lookup"><span data-stu-id="50b70-456">Confirm that the sub-application's *web.config* file doesn't include a `<handlers>` section.</span></span>

### <a name="application-configuration-general-issue"></a><span data-ttu-id="50b70-457">应用程序配置常见问题</span><span class="sxs-lookup"><span data-stu-id="50b70-457">Application configuration general issue</span></span>

* <span data-ttu-id="50b70-458">**浏览器：**HTTP 错误 502.5 - 进程失败</span><span class="sxs-lookup"><span data-stu-id="50b70-458">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="50b70-459">**应用程序日志：**物理根路径为 'C:\\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' 通过 '"C:\\{PATH}\my_application.{exe|dll}" ' 命令行创建了进程，但该进程出现故障、未响应或未在给定的端口 '{PORT}' 上侦听，ErrorCode = '0x800705b4'</span><span class="sxs-lookup"><span data-stu-id="50b70-459">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' with physical root 'C:\\{PATH}\' created process with commandline '"C:\\{PATH}\my_application.{exe|dll}" ' but either crashed or did not reponse or did not listen on the given port '{PORT}', ErrorCode = '0x800705b4'</span></span>

* <span data-ttu-id="50b70-460">**ASP.NET Core 模块日志：**已创建日志文件，但该日志文件为空</span><span class="sxs-lookup"><span data-stu-id="50b70-460">**ASP.NET Core Module Log:** Log file created but empty</span></span>

<span data-ttu-id="50b70-461">疑难解答</span><span class="sxs-lookup"><span data-stu-id="50b70-461">Troubleshooting</span></span>

* <span data-ttu-id="50b70-462">此一般异常表明进程未能启动，这很可能是由应用程序配置问题引起的。</span><span class="sxs-lookup"><span data-stu-id="50b70-462">This general exception indicates that the process failed to start, most likely due to an application configuration issue.</span></span> <span data-ttu-id="50b70-463">请参阅 [Directory Structure](xref:hosting/directory-structure)（目录结构），确认应用程序的已部署文件和文件夹适当，应用程序的配置文件存在且包含适用于应用和环境的正确设置。</span><span class="sxs-lookup"><span data-stu-id="50b70-463">Referring to [Directory Structure](xref:hosting/directory-structure), confirm that your application's deployed files and folders are appropriate and that your application's configuration files are present and contain the correct settings for your app and environment.</span></span> <span data-ttu-id="50b70-464">有关详细信息，请参阅[疑难解答提示](#troubleshooting-tips)。</span><span class="sxs-lookup"><span data-stu-id="50b70-464">For more information, see [Troubleshooting tips](#troubleshooting-tips).</span></span>

## <a name="resources"></a><span data-ttu-id="50b70-465">资源</span><span class="sxs-lookup"><span data-stu-id="50b70-465">Resources</span></span>

* [<span data-ttu-id="50b70-466">ASP.NET Core 模块简介</span><span class="sxs-lookup"><span data-stu-id="50b70-466">Introduction to ASP.NET Core Module</span></span>](xref:fundamentals/servers/aspnet-core-module)

* [<span data-ttu-id="50b70-467">ASP.NET Core 模块配置参考</span><span class="sxs-lookup"><span data-stu-id="50b70-467">ASP.NET Core Module configuration reference</span></span>](xref:hosting/aspnet-core-module)

* [<span data-ttu-id="50b70-468">配合使用 IIS 模块与 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="50b70-468">Using IIS Modules with ASP.NET Core</span></span>](xref:hosting/iis-modules)

* [<span data-ttu-id="50b70-469">ASP.NET Core 简介</span><span class="sxs-lookup"><span data-stu-id="50b70-469">Introduction to ASP.NET Core</span></span>](../index.md)

* [<span data-ttu-id="50b70-470">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="50b70-470">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)

* [<span data-ttu-id="50b70-471">Microsoft TechNet 库：Windows Server</span><span class="sxs-lookup"><span data-stu-id="50b70-471">Microsoft TechNet Library: Windows Server</span></span>](https://docs.microsoft.com/windows-server/windows-server-versions)
