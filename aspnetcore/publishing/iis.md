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
ms.openlocfilehash: 75fc1edec9050a4690a39d37307f2f95f5d534a5
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2017
---
# <a name="host-aspnet-core-on-windows-with-iis"></a>使用 IIS 在 Windows 上托管 ASP.NET Core

作者：[Luke Latham](https://github.com/guardrex) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="supported-operating-systems"></a>支持的操作系统

支持下列操作系统：

* Windows 7 和更新版本
* Windows Server 2008 R2 和 更新版本&#8224;

&#8224;在概念上，本文档描述的 IIS 配置也适用于在 Nano Server IIS 上托管 ASP.NET Core 应用程序，但有关具体说明，请参阅 [ASP.NET Core with IIS on Nano Server](xref:tutorials/nano-server)（Nano Server 上使用 IIS 的 ASP.NET Core）。

[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 [WebListener](xref:fundamentals/servers/weblistener)）在使用 IIS 的反向代理配置中不起作用。 必须使用 [Kestrel 服务器](xref:fundamentals/servers/kestrel)。

## <a name="iis-configuration"></a>IIS 配置

启用 Web 服务器 (IIS) 角色并建立角色服务。

### <a name="windows-desktop-operating-systems"></a>Windows 桌面操作系统

导航到“控制面板” > “程序” > “程序和功能” > “打开或关闭 Windows 功能”（位于屏幕左侧）。 打开“Internet Information Services”组和“Web 管理工具”。 选中“IIS 管理控制台”框。 选中“万维网服务”框。 接受“万维网服务”的默认功能，或根据需求自定义 IIS 功能。

![在“Windows 功能”中选择了“IIS 管理控制台”和“万维网服务”。](iis/_static/windows-features-win10.png)

### <a name="windows-server-operating-systems"></a>Windows Server 操作系统

对于服务器操作系统，通过“管理”菜单或“服务器管理器”中的链接使用“添加角色和功能”向导。 在“服务器角色”步骤中，选中“Web 服务器(IIS)”框。

![在选择服务器角色步骤中选择了“Web 服务器 IIS”角色。](iis/_static/server-roles-ws2016.png)

在“角色服务”步骤中，选择所需 IIS 角色服务，或接受提供的默认角色服务。

![在选择角色服务步骤中选择了默认角色服务。](iis/_static/role-services-ws2016.png)

继续执行“确认”步骤，安装 Web 服务器角色和服务。 安装 Web 服务器 (IIS) 角色后无需重启服务器/IIS。

## <a name="install-the-net-core-windows-server-hosting-bundle"></a>安装 .NET Core Windows Server 托管捆绑包

1. 在托管系统上安装 [.NET Core Windows Server 托管捆绑包](https://aka.ms/dotnetcore.2.0.0-windowshosting)。 捆绑包可安装 .NET Core 运行时、.NET Core 库和 [ASP.NET Core 模块](xref:fundamentals/servers/aspnet-core-module)。 该模块创建 IIS 与 Kestrel 服务器之间的反向代理。 如果系统没有 Internet 连接，请先获取并安装 [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840)，再安装 .NET Core Windows Server 托管捆绑包。

2. 重启系统，或在命令提示符处依次执行 net stop was /y 和 net start w3svc，了解系统路径的更改。

> [!NOTE]
> 如果使用 IIS 共享配置，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:hosting/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>使用 Visual Studio 进行发布时安装 Web 部署

如果要使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)在 [Visual Studio](https://www.visualstudio.com/vs/) 中部署应用程序，请在托管系统上安装最新版本的 Web 部署。 要安装 Web 部署，可以使用 [Web 平台安装程序 (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或直接从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=43717)获取安装程序。 建议使用 WebPI。 WebPI 为托管提供程序提供独立的安装程序和配置。

## <a name="application-configuration"></a>应用程序配置

### <a name="enabling-the-iisintegration-components"></a>启用 IISIntegration 组件

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

典型的 Program.cs 调用 [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) 以开始设置主机。 `CreateDefaultBuilder` 将 [Kestrel](xref:fundamentals/servers/kestrel) 配置为 Web 服务器，并通过配置 [ASP.NET Core 模块](xref:fundamentals/servers/aspnet-core-module)的基路径和端口来实现 IIS 集成：

```csharp
public static IWebHost BuildWebHost(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

在应用依赖项中加入对 [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/) 包的依赖项。 通过向 WebHostBuilder 添加 UseIISIntegration 扩展方法，将 IIS 集成中间件合并到应用中：

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .UseIISIntegration()
    ...
```

`UseKestrel` 和 `UseIISIntegration` 均为必需。 调用 UseIISIntegration 的代码不会影响代码可移植性。 如果应用不以 IIS 为背景运行（例如，应用直接在 Kestrel 上运行），则 `UseIISIntegration` 无操作。

---

有关托管的详细信息，请参阅 [ASP.NET Core 中的托管](xref:fundamentals/hosting)。

### <a name="iis-options"></a>IIS 选项

要配置 IISIntegration 服务选项，请在 ConfigureServices 中加入适用于 IISOptions 的服务配置：

```csharp
services.Configure<IISOptions>(options => 
{
    ...
});
```

| 选项                         | 默认 | 设置 |
| ------------------------------ | ------- | ------- |
| `AutomaticAuthentication`      | `true`  | 若为 `true`，身份验证中间件将设置 `HttpContext.User` 并响应一般质询。 若为 `false`，身份验证中间件仅提供标识 (`HttpContext.User`) 并在 `AuthenticationScheme` 显式请求时响应质询。 必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。 |
| `AuthenticationDisplayName`    | `null`  | 设置在登录页上向用户显示的显示名。 |
| `ForwardClientCertificate`     | `true`  | 若为 `true`，且存在 `MS-ASPNETCORE-CLIENTCERT` 请求头，则填充 `HttpContext.Connection.ClientCertificate`。 |

### <a name="webconfig"></a>web.config

web.config 文件可配置 ASP.NET Core 模块并提供其他 IIS 配置。 web.config 的创建、转换和发布由在项目 (.csproj) 文件 `<Project Sdk="Microsoft.NET.Sdk.Web">` 顶部设置项目 SDK 时所含的 `Microsoft.NET.Sdk.Web` 处理。 要防止 MSBuild 目标转换 web.config 文件，请将 \<IsTransformWebConfigDisabled> 属性添加到项目文件，并将其设置为 `true`：

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

当使用 dotnet publish 或 Visual Studio 的发布功能进行发布时，如果项目中没有 web.config 文件，则在[已发布的输出](xref:hosting/directory-structure)中创建该文件。 如果项目中有“web.config”文件，则会使用正确 processPath 和参数转换该文件，以便配置 [ASP.NET Core 模块](xref:fundamentals/servers/aspnet-core-module)，并将该文件移动到已发布的输出。 转换不会影响文件中已包含的 IIS 配置设置。

## <a name="create-the-iis-website"></a>创建 IIS 网站

1. 在目标 IIS 系统上，创建一个文件夹，将应用的已发布文件夹和文件包含在其中，如 [目录结构](xref:hosting/directory-structure)中所述。

2. 在创建的文件夹中，创建用于保存 stdout 日志的日志文件夹（如果计划启用日志记录以便解决启动问题）。 如果计划部署的应用程序的有效负载中包含日志文件夹，可跳过此步骤。 [存在未解决问题，无法自动创建文件夹](https://github.com/aspnet/AspNetCoreModule/issues/30)。 如果希望 MSBuild 创建此日志文件夹，请将如下 `Target` 添加至项目文件：

   ```xml
   <Target Name="CreateLogsFolder" AfterTargets="AfterPublish">
     <MakeDir Directories="$(PublishDir)logs" Condition="!Exists('$(PublishDir)logs')" />
     <MakeDir Directories="$(PublishUrl)logs" Condition="!Exists('$(PublishUrl)logs')" />
   </Target>
   ```

3. 在 IIS 管理器中创建新网站。 提供网站名称，并将“物理路径”设置为所创建应用的部署文件夹。 提供“绑定”配置并创建网站。

4. 将“应用程序池”设置为“无托管代码”。 ASP.NET Core 在单独的进程中运行，并管理运行时。

5. 打开“添加网站”窗口。

   ![单击“站点”上下文菜单中的“添加网站”。](iis/_static/add-website-context-menu-ws2016.png)

6. 配置网站。

   ![在“添加网站”步骤中提供网站名称、物理路径和主机名。](iis/_static/add-website-ws2016.png)

7. 在“应用程序池”面板中，右键单击网站的应用池，并从弹出菜单中选择“基本设置...”，打开“编辑应用程序池”窗口。

   ![从应用程序池的上下文菜单中选择“基本设置”。](iis/_static/apppools-basic-settings-ws2016.png)

8. 将“.NET CLR 版本”设置为“无托管代码”。

   ![将“.NET CLR 版本”设置为“无托管代码”。](iis/_static/edit-apppool-ws2016.png)
     
    请注意：将“.NET CLR 版本”设置为“无托管代码”是可选步骤。 ASP.NET Core 不依赖加载桌面 CLR。

9. 确认进程模型标识拥有适当的权限。

   如果将应用池的默认标识（“进程模型” > “标识”）从 ApplicationPoolIdentity 更改为另一标识，请验证新标识拥有所需的权限，可访问应用的文件夹、数据库和其他所需资源。
   
## <a name="deploy-the-application"></a>部署应用程序
将应用程序部署到在目标 IIS 系统上创建的文件夹。 建议使用的部署机制是 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)。 下面列出了 Web 部署的替代方法。

确认要部署的已发布应用未运行。 如果应用正在运行，publish 文件夹中的文件会被锁定。 不会进行部署，因为无法复制锁定的文件。

### <a name="web-deploy-with-visual-studio"></a>在 Visual Studio 内使用 Web 部署
要了解如何创建用于 Web 部署的发布配置文件，请参阅主题[创建 Visual Studio 和 MSBuild 的发布配置文件以部署 ASP.NET Core 应用程序](xref:publishing/web-publishing-vs#publish-profiles)。 如果托管提供程序提供了发布配置文件或支持创建发布配置文件，请下载配置文件并使用 Visual Studio 的“发布”对话框进行导入。

![“发布”对话框页](iis/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>在 Visual Studio 之外使用 Web 部署
也可以在 Visual Studio 之外从命令行使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)。 有关详细信息，请参阅 [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool)（Web 部署工具）。

### <a name="alternatives-to-web-deploy"></a>Web 部署的替代方法
如果不希望使用 Web 部署或未使用 Visual Studio，可使用多种其他方法将应用程序移动到托管系统，如 Xcopy、Robocopy 或 PowerShell。 Visual Studio 用户可以使用[发布示例](https://github.com/aspnet/vsweb-publish/blob/master/samples/samples.md)。

## <a name="browse-the-website"></a>浏览网站
![Microsoft Edge 浏览器已加载 IIS 启动页。](iis/_static/browsewebsite.png)
   
> [!WARNING]
> .NET Core 应用通过 IIS 与 Kestrel 服务器之间的反向代理托管。 为了创建反向代理，web.config 文件必须存在于已部署应用程序的内容根路径（通常为应用基路径）中，该路径是向 IIS 提供的网站物理路径。 敏感文件存在于应用的物理路径中，包括子文件夹，如 my_application.runtimeconfig.json、my_application.xml（XML 文档注释）和 my_application.deps.json。 创建 Kestrel 的反向代理需要 web.config 文件，它可以防止 IIS 为这些敏感文件和其他敏感文件提供服务。 因此，切勿意外重命名 web.config 文件或将其从部署中删除，这一点非常重要。

## <a name="data-protection"></a>数据保护

在以下情形下，ASP.NET Core 应用程序在内存中存储 keyring：

* 网站托管在 IIS 之后。
* 未将数据保护堆栈配置为在持久性存储中存储 keyring。

如果 keyring 存储于内存中，则当应用重启时：

* 所有 Forms 身份验证令牌都无效。 
* 用户需要在下一次请求时再次登录。 
* 使用 keyring 保护的任何数据不再受到保护。

> [!WARNING]
> 数据保护由多个 ASP.NET 中间件使用，包括用于身份验证的中间件。 即使不从自己的代码中调用数据保护 API，也应使用部署脚本或自己的代码配置数据保护。 如果未配置数据保护，则当应用重启时，密钥默认保存在内存中并会被丢弃。 重启会使 cookie 身份验证中间件写入的 cookie 失效，用户必须再次登录。

要在 IIS 下配置数据保护，必须使用以下方法之一：

* 运行 [Powershell 脚本](https://github.com/aspnet/DataProtection/blob/dev/Provision-AutoGenKeys.ps1)，创建合适的注册表项（如 `.\Provision-AutoGenKeys.ps1 DefaultAppPool`）。 这会将密钥存储在注册表中，并使用 DPAPI 和计算机范围的密钥进行保护。
* 配置 IIS 应用程序池以加载用户配置文件。 此设置位于应用程序池“高级设置”下的“进程模型”部分。 将“加载用户配置文件”设置为 `True`。 这会将密钥存储在用户配置文件目录下，并使用 DPAPI 和特定于用户帐户（用于应用池）的密钥的进行保护。
* 调整应用代码，[将文件系统用作密钥环存储](xref:security/data-protection/configuration/overview)。 使用 X509 证书保护密钥环，并确保该证书是受信任的证书。 如果它是自签名证书，则必须将它放置于受信任的根存储中。

当在 Web 场中使用 IIS 时：

* 使用所有计算机都可以访问的文件共享。
* 将 X509 证书部署到每台计算机。 [通过代码配置数据保护](https://docs.microsoft.com/aspnet/core/security/data-protection/configuration/overview)。

### <a name="1-create-a-data-protection-registry-hive"></a>1.创建数据保护注册表配置单元

ASP.NET 应用程序使用的数据保护密钥存储在应用程序外部的注册表配置单元中。 要持久保存给定应用程序的密钥，必须为应用的应用程序池创建注册表配置单元。

对于独立的 IIS 安装，可以对用于 ASP.NET Core 应用的应用池使用[数据保护 Provision-AutoGenKeys.ps1 PowerShell 脚本](https://github.com/aspnet/DataProtection/blob/dev/Provision-AutoGenKeys.ps1)。 此脚本在 HKLM 注册表中创建一个特殊的注册表项（该注册表仅列在工作进程帐户的 ACL 中）。 使用 DPAPI 对密钥静态加密。

在 Web 场方案中，可以将应用配置为使用 UNC 路径存储其数据保护密钥环。 默认情况下，数据保护密钥未加密。 应确保这种共享的文件权限仅限于运行应用所用的 Windows 帐户。 此外，可以选择使用 X509 证书保护静态密钥。 你可能希望考虑这样一种机制：通过该机制，用户可以上传证书、将证书放置在用户信任的证书存储中，并确保在运行用户应用的所有计算机上都可使用这些证书。 有关详细信息，请参阅[配置数据保护](xref:security/data-protection/configuration/overview#data-protection-configuring)。

### <a name="2-configure-the-iis-application-pool-to-load-the-user-profile"></a>2.配置 IIS 应用程序池以加载用户配置文件

此设置位于应用池“高级设置”下的“进程模型”部分。 将“加载用户配置文件”设置为 `True`。 这会将密钥存储在用户配置文件目录下，并使用 DPAPI 和特定于用户帐户（用于应用池）的密钥的进行保护。

### <a name="3-machine-wide-policy-for-data-protection"></a>3.数据保护的计算机范围的策略

数据保护系统对以下操作提供有限支持：为使用数据保护 API 的所有应用设置默认[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy#data-protection-configuration-machinewidepolicy)。 有关详细信息，请参阅[数据保护](xref:security/data-protection/index)文档。

## <a name="configuration-of-sub-applications"></a>配置子应用程序

在根应用程序下添加的子应用程序不应包含 ASP.NET Core 模块作为处理程序。 如果在子应用程序的 web.config 文件中将该模块添加为处理程序，则尝试浏览子应用时会收到 500.19（内部服务器错误），即引用错误的配置文件。 以下示例显示 ASP.NET Core 子应用的已发布 web.config 文件的内容：

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

如果想要在 ASP.NET Core 应用下托管非 ASP.NET Core 子应用，必须在子应用的 web.config 文件中显式删除继承的处理程序：

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

有关使用 web.config 文件配置 ASP.NET Core 模块的详细信息，请参阅 [Introduction to ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module)（ASP.NET Core 模块简介）主题和 [ASP.NET Core Module configuration reference](xref:hosting/aspnet-core-module)（ASP.NET Core 模块配置参考）。

## <a name="configuration-of-iis-with-webconfig"></a>使用 web.config 配置 IIS

对于那些应用于反向代理配置的 IIS 功能，IIS 配置仍受 web.config 的 `<system.webServer>` 部分影响。 例如，用户可能在系统级别配置了 IIS 以便使用动态压缩，但可通过应用的 web.config 文件中的 `<urlCompression>` 元素为应用禁用该设置。 有关详细信息，请参阅 [`<system.webServer>` 的配置参考](https://docs.microsoft.com/iis/configuration/system.webServer/)、[ASP.NET Core 模块配置参考](xref:hosting/aspnet-core-module)和 [配合使用 IIS 模块与 ASP.NET Core](xref:hosting/iis-modules)。 如果需要为在独立应用程序池中运行的单个应用设置环境变量（IIS 10.0+ 中支持此操作），请参阅 IIS 参考文档的[环境变量 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题中的“AppCmd.exe 命令”部分。

## <a name="configuration-sections-of-webconfig"></a>web.config 的配置节

.NET Framework 应用程序是使用 web.config 中的 `<system.web>`、`<appSettings>`、`<connectionStrings>` 和 `<location>` 元素配置的，ASP.NET Core 应用与其不同，是使用其他配置提供程序配置的。 有关详细信息，请参阅[配置](xref:fundamentals/configuration)。

## <a name="application-pools"></a>应用程序池

在单个系统上托管多个网站时，应在每个应用自己的应用程序池中运行各应用，以隔离各应用。 IIS“添加网站”对话框默认执行此行为。 提供站点名称时，该文本可自动传输到“应用程序池”文本框。 添加网站时，会使用该站点名称创建新的应用程序池。

## <a name="application-pool-identity"></a>应用程序池标识

通过应用程序池标识帐户，可以在唯一帐户下运行应用程序，而无需创建和管理域或本地帐户。 在 IIS 8.0+ 上，IIS 管理员工作进程 (WAS) 使用新应用程序池的名称创建一个虚拟帐户，并默认在此帐户下运行应用池的工作进程。 在 IIS 管理控制台中，确保应用池“高级设置”下的“标识”设置为使用 ApplicationPoolIdentity，如下图所示。

![应用程序池“高级设置”对话框](iis/_static/apppool-identity.png)

IIS 管理进程使用 Windows 安全系统中应用池的名称创建安全标识符。 可使用此标识保护资源；但此标识不是真实的用户帐户，不会在 Windows 用户管理控制台中显示。

如果需要向 IIS 工作进程授予对应用的提升的访问权限，必须为包含应用程序的目录修改访问控制列表 (ACL)。

1. 打开 Windows 资源管理器并导航到目录。

2. 右键单击相应目录并单击“属性”。

3. 在“安全”选项卡下，单击“编辑”按钮，然后单击“添加”按钮。

4. 单击“位置”按钮并确保选择你的系统。

5. 在“输入要选择的对象名称”文本框中输入“IIS AppPool\DefaultAppPool”。

  ![应用程序文件夹的“选择用户或组”对话框](iis/_static/select-users-or-groups-1.png)

6. 单击“检查名称”按钮，然后单击“确定”。

  ![应用程序文件夹的“选择用户或组”对话框](iis/_static/select-users-or-groups-2.png)

也可以使用 ICACLS 工具通过命令提示符执行此操作：

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

## <a name="troubleshooting-tips"></a>疑难解答提示

诊断 IIS 部署问题：

* 查看浏览器输出。
* 通过“事件查看器”检查系统的“应用程序”日志。
* 启用 `stdout` 日志记录 ASP.NET Core 模块日志位于 web.config 中 `<aspNetCore>` 元素的 stdoutLogFile 属性提供的路径上。属性值中提供的路径上的任何文件夹都必须存在于部署中。 还必须设置 stdoutLogEnabled ="true"。 使用 `Microsoft.NET.Sdk.Web` SDK 创建 web.config 文件的应用默认 stdoutLogEnabled 设置为“false”，因此必须手动提供 web.config 文件或修改文件，以便启用 `stdout` 日志记录。

多种常见错误只有在经过模块的 startupTimeLimit（默认值：120 秒）和 startupRetryCount（默认值：2 次）后，才会在浏览器、应用程序日志和 ASP.NET Core 模块日志中显示。 因此，请先等足六分钟，然后再推断出模块无法为应用启动进程。

一种快速确定应用是否正常工作的方法是直接在 Kestrel 上运行应用。 如果应用作为依赖框架的部署 (FDD) 发布，则在部署文件夹（应用的 IIS 物理路径）中执行 `dotnet my_application.dll`。 如果应用作为独立部署 (SCD) 发布，则通过命令提示符在部署文件夹中直接运行应用的可执行文件 `my_application.exe`。 如果 Kestrel 在默认端口 5000 上侦听，用户就应能够在 `http://localhost:5000/` 上浏览应用。 如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与 IIS-ASP.NET Core Module-Kestrel 配置相关，而不太可能在于应用本身。

确定 Kestrel 服务器的 IIS 反向代理是否正常运行的一种方法是：使用[静态文件中间件](xref:fundamentals/static-files)针对 wwwroot 中应用静态文件的样式表、脚本或映像执行简单的静态文件请求。 如果应用可为静态文件提供服务，但 MVC 视图和其他终结点无法工作，则问题不太可能与 IIS-ASP.NET Core Module-Kestrel 配置相关，更可能在于应用本身（如 MVC 路由或 500 内部服务器错误）。

当 Kestrel 在 IIS 之后正常启动，但应用在本地成功运行后无法在系统上运行时，可以临时将环境变量添加到 web.config，以便将 `ASPNETCORE_ENVIRONMENT` 设置为 `Development`。 通过此操作，只要不替代应用启动中的环境，应用在系统上运行时就会显示[开发者异常页](xref:fundamentals/error-handling)。 仅建议对未向 Internet 公开的暂存/测试系统使用此方式设置 `ASPNETCORE_ENVIRONMENT` 的环境变量。 确保在完成时从 web.config 文件中删除环境变量。 有关通过 web.config 为反向代理设置环境变量的信息，请参阅 [aspNetCore 的 environmentVariables 子元素](xref:hosting/aspnet-core-module#setting-environment-variables)。

在大多数情况下，启用应用程序日志记录有助于排查应用问题或反向代理问题。 有关详细信息，请参阅[日志记录](xref:fundamentals/logging)。

最后一项疑难解答提示涉及在开发计算机上升级 .NET Core SDK 或在应用内升级包版本后无法运行的应用。 在某些情况下，不同的包可能在执行主要升级时中断应用。 可通过以下方式解决大多数问题：删除项目中的 `bin` 和 `obj` 文件夹、清除 `%UserProfile%\.nuget\packages\` 和 `%LocalAppData%\Nuget\v3-cache` 的包缓存、还原项目和确认已在重新部署应用前完全删除系统上以前的部署。

> [!TIP]
> 清除包缓存的一种便捷方法是从 [NuGet.org](https://www.nuget.org/) 获取 NuGet.exe 工具，将其添加到系统路径，然后从命令提示符执行 `nuget locals all -clear`。 还可从命令提示符执行 `dotnet nuget locals all --clear` 而无需获取 NuGet.exe。

## <a name="common-errors"></a>常见错误

以下不是错误的完整列表。 如果遇到此处未列出的错误，请在下方的注释部分留下详细的错误消息。

### <a name="installer-unable-to-obtain-vc-redistributable"></a>安装程序无法获取 VC++ Redistributable

* **安装程序异常：**0x80072efd 或 0x80072f76 - 未指定的错误

* **安装程序日志异常&#8224;：**0x80072efd 或 0x80072f76 错误：未能执行 EXE 包

  &#8224;此日志位于 C:\Users\\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{timestamp}.log。

疑难解答：

* 如果在安装服务器托管捆绑包时系统无法访问 Internet，则在系统阻止安装程序获取 Microsoft Visual C++ 2015 Redistributable 时会出现此异常。 可从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=53840)获取安装程序。 如果安装程序失败，可能不会收到托管依赖框架的部署 (FDD) 所需的 .NET Core 运行时。 如果计划托管 FDD，请在“程序和功能”中确认安装了运行时。 可从 [.NET 下载](https://www.microsoft.com/net/download/core)获取运行时安装程序。 安装运行时后，重启系统，或通过从命令提示符依次执行 net stop was /y 和 net start w3svc 来重启 IIS。

### <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>OS 升级删除了 32 位 ASP.NET Core 模块

* **应用程序日志：**未能加载模块 DLL C:\WINDOWS\system32\inetsrv\aspnetcore.dll。 该数据是个错误。

疑难解答：

* OS 升级期间不会保留 C:\Windows\SysWOW64\inetsrv 目录中的非 OS 文件。 如果在 OS 升级前已安装 ASP.NET Core 模块，且随后在 OS 升级后尝试在 32 位模式下运行任何应用池，则会遇到此问题。 在 OS 升级后修复 ASP.NET Core 模块。 请参阅[安装 .NET Core Windows Server 托管捆绑包](#install-the-net-core-windows-server-hosting-bundle)。 运行安装程序时，选择“修复”。

### <a name="platform-conflicts-with-rid"></a>平台与 RID 冲突

* **浏览器：**HTTP 错误 502.5 - 进程失败

* **应用程序日志：**物理根路径为 'C:\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' 未能通过 '"C:\\{PATH}\my_application.{exe|dll}" ' 命令行启动进程，ErrorCode = '0x80004005 : ff。

* **ASP.NET Core 模块日志：**未经处理的异常: System.BadImageFormatException: 无法加载文件或程序集 "my_application.dll"。 试图加载的程序的格式不正确。

疑难解答：

* 确认应用程序在 Kestrel 上本地运行。 进程失败可能是由应用程序的内部问题导致的。 有关详细信息，请参阅[疑难解答提示](#troubleshooting-tips)。

* 确认未在 .csproj 中设置与 RID 冲突的 `<PlatformTarget>`。 例如，请勿通过使用 dotnet publish -c Release -r win10-x64 或通过在 .csproj 中将 `<RuntimeIdentifiers>` 设置为 `win10-x64` 来指定 `x86` 的 `<PlatformTarget>` 和以 `win10-x64` 的 RID 进行发布。 项目在不显示任何警告或错误的情况下发布，但会失败，且系统上出现上述记录的异常。

* 如果在升级应用程序和部署更新的程序集时，Azure 应用部署出现此异常，请从先前的部署中手动删除所有文件。 部署升级的应用时，延迟的不兼容程序集可能造成 `System.BadImageFormatException` 异常。

### <a name="uri-endpoint-wrong-or-stopped-website"></a>URI 终结点错误或网站已停止

* **浏览器：**ERR_CONNECTION_REFUSED

* **应用程序日志：**没有任何条目

* **ASP.NET Core 模块日志：**未创建日志文件

疑难解答：

* 确认对应用程序使用的 URI 终结点正确无误。 检查绑定。

* 确认 IIS 网站未处于“已停止”状态。

### <a name="corewebengine-or-w3svc-server-features-disabled"></a>已禁用 CoreWebEngine 或 W3SVC 服务器功能

* **OS 异常：**必须安装 IIS 7.0 CoreWebEngine 和 W3SVC 功能，以便使用 ASP.NET Core 模块。

疑难解答：

* 确认启用了适当的角色和功能。 请参阅 [IIS 配置](#iis-configuration)。

### <a name="incorrect-website-physical-path-or-application-missing"></a>网站物理路径不正确或缺少应用程序

* **浏览器：**403 禁止访问 - 访问被拒绝 --或者-- 403.14 禁止访问 - Web 服务器被配置为不列出此目录的内容。

* **应用程序日志：**没有任何条目

* **ASP.NET Core 模块日志：**未创建日志文件

疑难解答：

* 检查 IIS 网站的“基本设置”和物理应用程序文件夹。 确认应用程序所在的文件夹位于 IIS 网站的物理路径中。

### <a name="incorrect-role-module-not-installed-or-incorrect-permissions"></a>角色不正确、未安装模块或权限不正确

* **浏览器：**500.19 内部服务器错误 - 无法访问请求的页面，因为该页面的相关配置数据无效。

* **应用程序日志：**没有任何条目

* **ASP.NET Core 模块日志：**未创建日志文件

疑难解答：

* 确认启用了适当的角色。 请参阅 [IIS 配置](#iis-configuration)。

* 检查“程序和功能”，确认已安装Microsoft ASP.NET Core 模块。 如果已安装程序列表中没有 Microsoft ASP.NET Core 模块，请安装该模块。 请参阅[安装 .NET Core Windows Server 托管捆绑包](#install-the-net-core-windows-server-hosting-bundle)。

* 请确保将“应用程序池” > “进程模型” > “标识”设置为 ApplicationPoolIdentity，或确保自定义标识具有访问应用部署文件夹的相应权限。

### <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>processPath 不正确、缺少 PATH 变量、未安装托管捆绑包、未重启系统/IIS、未安装 VC++ Redistributable 或 dotnet.exe 访问冲突

* **浏览器：**HTTP 错误 502.5 - 进程失败

* **应用程序日志：**物理根路径为 'C:\\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' 应用程序未能通过 '".\my_application.exe" ' 命令行启动进程，ErrorCode = '0x80070002 : 0。

* **ASP.NET Core 模块日志：**已创建日志文件，但该日志文件为空

疑难解答：

* 确认应用程序在 Kestrel 上本地运行。 进程失败可能是由应用程序的内部问题导致的。 有关详细信息，请参阅[疑难解答提示](#troubleshooting-tips)。

* 检查 web.config 中 `<aspNetCore>` 元素的 processPath 属性，对于依赖框架的部署 (FDD)，确保它 为 dotnet，对于独立部署 (SCD)，确保它为 .\my_application.exe。

* 对于 FDD，可能无法通过路径设置访问 dotnet.exe。 确认系统路径设置中存在 *C:\Program Files\dotnet\*。

* 对于 FDD，应用程序池的用户标识可能无法访问 dotnet.exe。 确认应用程序池用户标识具有访问 C:\Program Files\dotnet 目录的权限。 确认没有为应用程序池用户标识配置针对 C:\Program Files\dotnet 和应用程序目录的拒绝规则。

* 你可能已部署 FDD 并安装了 .NET Core，但未重启 IIS。 重启服务器，或通过从命令提示符依次执行 net stop was /y 和 net start w3svc 来重启 IIS。

* 你可能已部署 FDD，但未在托管系统上安装 .NET Core 运行时。 如果尝试部署 FDD 且尚未安装 .NET Core 运行时，请在系统上运行 .NET Core Windows Server 托管捆绑包安装程序。 请参阅[安装 .NET Core Windows Server 托管捆绑包](#install-the-net-core-windows-server-hosting-bundle)。 如果在没有 Internet 连接的情况下尝试在系统上安装 .NET Core 运行时，请从 [.NET 下载](https://www.microsoft.com/net/download/core)获取运行时并运行托管捆绑包安装程序，以安装 ASP.NET Core 模块。 重启系统，或通过从命令提示符依次执行 net stop was /y 和 net start w3svc 来重启 IIS，完成安装。

* 你可能已部署 FDD 并安装了 .NET Core，但未重启系统/IIS。 重启系统，或通过从命令提示符依次执行 net stop was /y 和 net start w3svc 来重启 IIS。

* 你可能已部署 FDD，但系统上未安装 Microsoft Visual C++ 2015 Redistributable (x64)。 可从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=53840)获取安装程序。

### <a name="incorrect-arguments-of-aspnetcore-element"></a>\<aspNetCore\> 元素的参数不正确

* **浏览器：**HTTP 错误 502.5 - 进程失败

* **应用程序日志：**物理根路径为 'C:\\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' 未能通过 '"dotnet" .\my_application.dll' 命令行启动进程，ErrorCode = '0x80004005 : 80008081。

* **ASP.NET Core 模块日志：**要执行的应用程序不存在: "PATH\my_application.dll"

疑难解答：

* 确认应用程序在 Kestrel 上本地运行。 进程失败可能是由应用程序的内部问题导致的。 有关详细信息，请参阅[疑难解答提示](#troubleshooting-tips)。

* 检查 web.config 中 `<aspNetCore>` 元素的 arguments 属性，确认该属性：(a) 对于依赖框架的部署 (FDD) 为 .\my_applciation.dll；或 (b) 对于独立部署 (SCD)，则为不存在、为空字符串 (arguments="")，或为应用参数列表 (arguments="arg1, arg2, ...")。

### <a name="missing-net-framework-version"></a>缺少 .NET Framework 版本

* **浏览器：**502.3 错误网关 - 尝试路由请求时出现连接错误。

* **应用程序日志：**ErrorCode = 物理根路径为 'C:\\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' 未能通过 '"dotnet" .\my_application.dll' 命令行启动进程，ErrorCode = '0x80004005 : 80008081。

* **ASP.NET Core 模块日志：**缺少方法、文件或程序集异常。 在异常中指定的方法、文件或程序集是 .NET Framework 方法、文件或程序集。

疑难解答：

* 安装系统缺少的 .NET Framework 版本。

* 对于依赖框架的部署 (FDD)，确认已在系统上安装正确的运行时。 如果将项目从 1.1 升级到 2.0，部署到托管系统，并收到此异常，请确保在托管系统上安装 2.0 框架。

### <a name="stopped-application-pool"></a>应用程序池已停止

* **浏览器：**503 服务不可用

* **应用程序日志：**没有任何条目

* **ASP.NET Core 模块日志：**未创建日志文件

疑难解答

* 确认应用程序池未处于“已停止”状态。

### <a name="iis-integration-middleware-not-implemented"></a>未实现 IIS 集成中间件

* **浏览器：**HTTP 错误 502.5 - 进程失败

* **应用程序日志：**物理根路径为 'C:\\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' 通过 '"C:\\{PATH}\my_application.{exe|dll}" ' 命令行创建了进程，但该进程出现故障、未响应或未在给定的端口 '{PORT}' 上侦听，ErrorCode = '0x800705b4'

* **ASP.NET Core 模块日志：**已创建日志文件，且该日志文件显示正常操作。

疑难解答

* 确认应用在 Kestrel 上本地运行。 进程失败可能是由应用的内部问题导致的。 有关详细信息，请参阅[疑难解答提示](#troubleshooting-tips)。

* 在应用程序的 WebHostBuilder() (ASP.NET Core 1.x) 上调用 .UseIISIntegration() 方法，确认已正确引用 IIS 集成中间件或使用 `CreateDefaultBuilder` 方法 (ASP.NET Core 2.x)。 有关详细信息，请参阅 [ASP.NET Core 中的承载](xref:fundamentals/hosting)。

### <a name="sub-application-includes-a-handlers-section"></a>子应用程序包括 \<handlers\> 部分

* **浏览器：**HTTP 错误 500.19 - 内部服务器错误

* **应用程序日志：**没有任何条目

* **ASP.NET Core 模块日志：**已创建日志文件，且该日志文件显示根应用程序的正常操作。 没有为子应用程序创建日志文件。

疑难解答

* 确认子应用的 web.config 文件不包括 `<handlers>` 部分。

### <a name="application-configuration-general-issue"></a>应用程序配置常见问题

* **浏览器：**HTTP 错误 502.5 - 进程失败

* **应用程序日志：**物理根路径为 'C:\\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' 通过 '"C:\\{PATH}\my_application.{exe|dll}" ' 命令行创建了进程，但该进程出现故障、未响应或未在给定的端口 '{PORT}' 上侦听，ErrorCode = '0x800705b4'

* **ASP.NET Core 模块日志：**已创建日志文件，但该日志文件为空

疑难解答

* 此一般异常表明进程未能启动，这很可能是由应用程序配置问题引起的。 请参阅 [目录结构](xref:hosting/directory-structure)，确认应用的已部署文件和文件夹适当，应用的配置文件存在且包含适用于应用和环境的正确设置。 有关详细信息，请参阅[疑难解答提示](#troubleshooting-tips)。

## <a name="resources"></a>资源

* [ASP.NET Core 模块简介](xref:fundamentals/servers/aspnet-core-module)
* [ASP.NET Core 模块配置参考](xref:hosting/aspnet-core-module)
* [配合使用 IIS 模块与 ASP.NET Core](xref:hosting/iis-modules)
* [ASP.NET Core 简介](../index.md)
* [Microsoft IIS 官方网站](https://www.iis.net/)
* [Microsoft TechNet 库：Windows Server](https://docs.microsoft.com/windows-server/windows-server-versions)
