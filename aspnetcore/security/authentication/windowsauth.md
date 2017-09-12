---
title: "在 ASP.NET 核心中配置 Windows 身份验证"
author: ardalis
description: "如何在 ASP.NET 核心中配置 Windows 身份验证"
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 7/5/2017
ms.topic: article
ms.assetid: cf119f21-1a2b-49a2-b052-548ccb66ee83
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/windowsauth
ms.openlocfilehash: aa401f956d74680efd3964203af3e8866b129887
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>在 ASP.NET 核心中配置 Windows 身份验证

通过[Steve Smith](https://ardalis.com)

可以为使用 IIS 或 WebListener 托管的 ASP.NET Core 应用程序配置 Windows 身份验证。

## <a name="what-is-windows-authentication"></a>什么是 Windows 身份验证

Windows 身份验证依赖于操作系统的 ASP.NET Core 应用的用户进行身份验证。 在使用 Active Directory 域标识或其他 Windows 帐户来标识用户的企业网络上运行你的服务器时，你可以使用 Windows 身份验证。 Windows 身份验证是一种安全形式最佳的身份验证适合 intranet 环境用户、 客户端应用程序和 web 服务器属于同一 Windows 域。

[了解有关 Windows 身份验证的详细信息，并将它安装在 iis](https://docs.microsoft.com/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)。

## <a name="enabling-windows-authentication-in-an-aspnet-core-application"></a>启用 ASP.NET Core 应用程序中的 Windows 身份验证

Visual Studio Web 应用程序模板可以配置为支持 Windows 身份验证。

### <a name="using-the-windows-authentication-app-template"></a>使用 Windows 身份验证应用程序模板

在 Visual Studio 中：
* 创建新的 ASP.NET Core Web 应用程序。 
* 从模板列表中选择 Web 应用程序。
* 选择更改身份验证按钮，然后选择**Windows 身份验证**。 

运行该应用。 用户名已显示在顶部的应用程序的权限。

![Windows 身份验证浏览器屏幕快照](windowsauth/_static/browser-screenshot.png)

对于使用 IIS Express 的开发工作，该模板提供所有使用 Windows 身份验证所需的配置。 下一节演示如何配置 ASP.NET Core 应用手动为 Windows 身份验证。

### <a name="visual-studio-settings-for-windows-and-anonymous-authentication"></a>为 Windows 以及匿名身份验证的 visual Studio 设置

Visual Studio 属性页中，调试选项卡提供有关 Windows 身份验证和匿名身份验证复选框。

![Windows 身份验证浏览器屏幕快照](windowsauth/_static/vs-auth-property-menu.png)

你还可以配置中的这些属性`launchSettings.json`文件：

```json
{
  "iisSettings": {
    "windowsAuthentication": true,
    "anonymousAuthentication": false,
    "iisExpress": {
      "applicationUrl": "http://localhost:52171/",
      "sslPort": 0
    }
  } // additional options trimmed
}
```

## <a name="enabling-windows-authentication-with-iis"></a>启用与 IIS 的 Windows 身份验证

IIS 使用[ASP.NET 核心模块](xref:fundamentals/servers/aspnet-core-module)(ANCM) 来承载 ASP.NET Core 应用。 ANCM 流 Windows 身份验证到 IIS 默认情况下。 IIS，不是应用程序项目中进行配置的 Windows 身份验证。 以下各部分介绍如何使用 IIS 管理器来配置 ASP.NET Core 应用以使用 Windows 身份验证：

### <a name="create-a-new-iis-site"></a>创建一个新的 IIS 站点

指定名称和文件夹，并允许它创建新的应用程序池。

### <a name="customize-authentication"></a>自定义身份验证

打开身份验证菜单上的站点。

![IIS 身份验证菜单](windowsauth/_static/iis-authentication-menu.png)

禁用匿名身份验证并启用 Windows 身份验证。

![IIS 身份验证设置](windowsauth/_static/iis-auth-settings.png)

### <a name="publish-your-project-to-the-iis-site-folder"></a>将你的项目发布到 IIS 站点文件夹

使用 Visual Studio 或.NET 核心 CLI，*发布*你的应用到目标文件夹。

![Visual Studio 发布对话框](windowsauth/_static/vs-publish-app.png)

详细了解[发布到 IIS](xref:publishing/iis)。

启动应用程序来验证使用 Windows 身份验证。

## <a name="enabling-windows-authentication-with-weblistener"></a>启用 Windows 身份验证与 WebListener

尽管 Kestrel 不支持 Windows 身份验证，你可以使用[WebListener](xref:fundamentals/servers/weblistener)以在 Windows 上支持自承载的方案。 下面的示例将配置应用程序的 web 主机 WebListener 用于 Windows 身份验证：

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = new WebHostBuilder()
            .UseWebListener(options =>
            {
                options.ListenerSettings.Authentication.Schemes = 
                    AuthenticationSchemes.Negotiate | AuthenticationSchemes.NTLM;
                options.ListenerSettings.Authentication.AllowAnonymous = false;
            })
            .UseContentRoot(Directory.GetCurrentDirectory())
            .UseStartup<Startup>()
            .Build();

        host.Run();
    }
}
```

## <a name="working-with-windows-authentication"></a>使用 Windows 身份验证

如果你的应用程序使用 Windows 身份验证和匿名访问，则可以使用``[Authorize]``和``[AllowAnonymous]``属性。 应用程序不具有不需要的匿名启用的执行``[Authorize]``; 应用程序将被视为要求身份验证，则会拒绝匿名请求。 请注意，如果配置 IIS 站点**不**以允许匿名访问，``[AllowAnonymous]``特性**不**允许匿名请求。 ``[AllowAnonymous]``特性重写``[Authorize]``属性中允许匿名访问的应用的使用情况。

### <a name="impersonation"></a>模拟

ASP.NET 核心不实现模拟。 使用应用程序池或进程标识的所有请求的应用程序标识使用运行应用。 如果你需要显式执行代表用户操作，使用``WindowsIdentity.RunImpersonated``。 在此上下文中运行的单个操作，然后关闭上下文。 请注意，``RunImpersonated``不支持异步和不应该用于复杂的方案。 例如，包装整个请求或中间件链是不受支持或建议。
