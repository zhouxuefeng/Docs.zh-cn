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
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="aaa46-104">在 ASP.NET 核心中配置 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="aaa46-104">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="aaa46-105">通过[Steve Smith](https://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="aaa46-105">By [Steve Smith](https://ardalis.com)</span></span>

<span data-ttu-id="aaa46-106">可以为使用 IIS 或 WebListener 托管的 ASP.NET Core 应用程序配置 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="aaa46-106">Windows authentication can be configured for ASP.NET Core apps hosted with IIS or WebListener.</span></span>

## <a name="what-is-windows-authentication"></a><span data-ttu-id="aaa46-107">什么是 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="aaa46-107">What is Windows authentication</span></span>

<span data-ttu-id="aaa46-108">Windows 身份验证依赖于操作系统的 ASP.NET Core 应用的用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="aaa46-108">Windows authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="aaa46-109">在使用 Active Directory 域标识或其他 Windows 帐户来标识用户的企业网络上运行你的服务器时，你可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="aaa46-109">You can use Windows authentication when your server runs on a corporate network using Active Directory domain identities or other Windows accounts to identify users.</span></span> <span data-ttu-id="aaa46-110">Windows 身份验证是一种安全形式最佳的身份验证适合 intranet 环境用户、 客户端应用程序和 web 服务器属于同一 Windows 域。</span><span class="sxs-lookup"><span data-stu-id="aaa46-110">Windows authentication is a secure form of authentication best suited to intranet environments where users, client applications, and web servers belong to the same Windows domain.</span></span>

<span data-ttu-id="aaa46-111">[了解有关 Windows 身份验证的详细信息，并将它安装在 iis](https://docs.microsoft.com/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)。</span><span class="sxs-lookup"><span data-stu-id="aaa46-111">[Learn more about Windows Authentication and installing it for IIS](https://docs.microsoft.com/iis/configuration/system.webServer/security/authentication/windowsAuthentication/).</span></span>

## <a name="enabling-windows-authentication-in-an-aspnet-core-application"></a><span data-ttu-id="aaa46-112">启用 ASP.NET Core 应用程序中的 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="aaa46-112">Enabling Windows authentication in an ASP.NET Core application</span></span>

<span data-ttu-id="aaa46-113">Visual Studio Web 应用程序模板可以配置为支持 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="aaa46-113">The Visual Studio Web Application template can be configured to support Windows authentication.</span></span>

### <a name="using-the-windows-authentication-app-template"></a><span data-ttu-id="aaa46-114">使用 Windows 身份验证应用程序模板</span><span class="sxs-lookup"><span data-stu-id="aaa46-114">Using the Windows authentication app template</span></span>

<span data-ttu-id="aaa46-115">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="aaa46-115">In Visual Studio:</span></span>
* <span data-ttu-id="aaa46-116">创建新的 ASP.NET Core Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="aaa46-116">Create a new ASP.NET Core Web Application.</span></span> 
* <span data-ttu-id="aaa46-117">从模板列表中选择 Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="aaa46-117">Select Web Application from the list of templates.</span></span>
* <span data-ttu-id="aaa46-118">选择更改身份验证按钮，然后选择**Windows 身份验证**。</span><span class="sxs-lookup"><span data-stu-id="aaa46-118">Select the Change Authentication button and select **Windows Authentication**.</span></span> 

<span data-ttu-id="aaa46-119">运行该应用。</span><span class="sxs-lookup"><span data-stu-id="aaa46-119">Run the app.</span></span> <span data-ttu-id="aaa46-120">用户名已显示在顶部的应用程序的权限。</span><span class="sxs-lookup"><span data-stu-id="aaa46-120">The username appears in the top right of the app.</span></span>

![Windows 身份验证浏览器屏幕快照](windowsauth/_static/browser-screenshot.png)

<span data-ttu-id="aaa46-122">对于使用 IIS Express 的开发工作，该模板提供所有使用 Windows 身份验证所需的配置。</span><span class="sxs-lookup"><span data-stu-id="aaa46-122">For development work using IIS Express, the template provides all the configuration necessary to use Windows authentication.</span></span> <span data-ttu-id="aaa46-123">下一节演示如何配置 ASP.NET Core 应用手动为 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="aaa46-123">The next section shows how to configure an ASP.NET Core app manually for Windows authentication.</span></span>

### <a name="visual-studio-settings-for-windows-and-anonymous-authentication"></a><span data-ttu-id="aaa46-124">为 Windows 以及匿名身份验证的 visual Studio 设置</span><span class="sxs-lookup"><span data-stu-id="aaa46-124">Visual Studio settings for Windows and anonymous authentication</span></span>

<span data-ttu-id="aaa46-125">Visual Studio 属性页中，调试选项卡提供有关 Windows 身份验证和匿名身份验证复选框。</span><span class="sxs-lookup"><span data-stu-id="aaa46-125">The Visual Studio properties page, debug tab provides check boxes for Windows authentication and anonymous authentication.</span></span>

![Windows 身份验证浏览器屏幕快照](windowsauth/_static/vs-auth-property-menu.png)

<span data-ttu-id="aaa46-127">你还可以配置中的这些属性`launchSettings.json`文件：</span><span class="sxs-lookup"><span data-stu-id="aaa46-127">You can also configure these properties in the `launchSettings.json` file:</span></span>

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

## <a name="enabling-windows-authentication-with-iis"></a><span data-ttu-id="aaa46-128">启用与 IIS 的 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="aaa46-128">Enabling Windows Authentication with IIS</span></span>

<span data-ttu-id="aaa46-129">IIS 使用[ASP.NET 核心模块](xref:fundamentals/servers/aspnet-core-module)(ANCM) 来承载 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="aaa46-129">IIS uses the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) (ANCM) to host ASP.NET Core apps.</span></span> <span data-ttu-id="aaa46-130">ANCM 流 Windows 身份验证到 IIS 默认情况下。</span><span class="sxs-lookup"><span data-stu-id="aaa46-130">The ANCM flows Windows authentication to IIS by default.</span></span> <span data-ttu-id="aaa46-131">IIS，不是应用程序项目中进行配置的 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="aaa46-131">Configuration of Windows authentication is done within IIS, not the application project.</span></span> <span data-ttu-id="aaa46-132">以下各部分介绍如何使用 IIS 管理器来配置 ASP.NET Core 应用以使用 Windows 身份验证：</span><span class="sxs-lookup"><span data-stu-id="aaa46-132">The following sections show how to use IIS Manager to configure an ASP.NET Core app to use Windows authentication:</span></span>

### <a name="create-a-new-iis-site"></a><span data-ttu-id="aaa46-133">创建一个新的 IIS 站点</span><span class="sxs-lookup"><span data-stu-id="aaa46-133">Create a new IIS site</span></span>

<span data-ttu-id="aaa46-134">指定名称和文件夹，并允许它创建新的应用程序池。</span><span class="sxs-lookup"><span data-stu-id="aaa46-134">Specify a name and folder and allow it to create a new application pool.</span></span>

### <a name="customize-authentication"></a><span data-ttu-id="aaa46-135">自定义身份验证</span><span class="sxs-lookup"><span data-stu-id="aaa46-135">Customize Authentication</span></span>

<span data-ttu-id="aaa46-136">打开身份验证菜单上的站点。</span><span class="sxs-lookup"><span data-stu-id="aaa46-136">Open the Authentication menu for the site.</span></span>

![IIS 身份验证菜单](windowsauth/_static/iis-authentication-menu.png)

<span data-ttu-id="aaa46-138">禁用匿名身份验证并启用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="aaa46-138">Disable Anonymous Authentication and enable Windows Authentication.</span></span>

![IIS 身份验证设置](windowsauth/_static/iis-auth-settings.png)

### <a name="publish-your-project-to-the-iis-site-folder"></a><span data-ttu-id="aaa46-140">将你的项目发布到 IIS 站点文件夹</span><span class="sxs-lookup"><span data-stu-id="aaa46-140">Publish your project to the IIS site folder</span></span>

<span data-ttu-id="aaa46-141">使用 Visual Studio 或.NET 核心 CLI，*发布*你的应用到目标文件夹。</span><span class="sxs-lookup"><span data-stu-id="aaa46-141">Using Visual Studio or the .NET Core CLI, *publish* your app to the destination folder.</span></span>

![Visual Studio 发布对话框](windowsauth/_static/vs-publish-app.png)

<span data-ttu-id="aaa46-143">详细了解[发布到 IIS](xref:publishing/iis)。</span><span class="sxs-lookup"><span data-stu-id="aaa46-143">Learn more about [publishing to IIS](xref:publishing/iis).</span></span>

<span data-ttu-id="aaa46-144">启动应用程序来验证使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="aaa46-144">Launch the app to verify Windows authentication is working.</span></span>

## <a name="enabling-windows-authentication-with-weblistener"></a><span data-ttu-id="aaa46-145">启用 Windows 身份验证与 WebListener</span><span class="sxs-lookup"><span data-stu-id="aaa46-145">Enabling Windows authentication with WebListener</span></span>

<span data-ttu-id="aaa46-146">尽管 Kestrel 不支持 Windows 身份验证，你可以使用[WebListener](xref:fundamentals/servers/weblistener)以在 Windows 上支持自承载的方案。</span><span class="sxs-lookup"><span data-stu-id="aaa46-146">Although Kestrel doesn't support Windows authentication, you can use [WebListener](xref:fundamentals/servers/weblistener) to support self-hosted scenarios on Windows.</span></span> <span data-ttu-id="aaa46-147">下面的示例将配置应用程序的 web 主机 WebListener 用于 Windows 身份验证：</span><span class="sxs-lookup"><span data-stu-id="aaa46-147">The following example configures the app's web host to use WebListener with Windows authentication:</span></span>

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

## <a name="working-with-windows-authentication"></a><span data-ttu-id="aaa46-148">使用 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="aaa46-148">Working with Windows authentication</span></span>

<span data-ttu-id="aaa46-149">如果你的应用程序使用 Windows 身份验证和匿名访问，则可以使用``[Authorize]``和``[AllowAnonymous]``属性。</span><span class="sxs-lookup"><span data-stu-id="aaa46-149">If your app uses Windows authentication and anonymous access, you can use the ``[Authorize]`` and ``[AllowAnonymous]`` attributes.</span></span> <span data-ttu-id="aaa46-150">应用程序不具有不需要的匿名启用的执行``[Authorize]``; 应用程序将被视为要求身份验证，则会拒绝匿名请求。</span><span class="sxs-lookup"><span data-stu-id="aaa46-150">Apps that do not have anonymous enabled do not require ``[Authorize]``; the  app is treated as requiring authentication, anonymous requests are rejected.</span></span> <span data-ttu-id="aaa46-151">请注意，如果配置 IIS 站点**不**以允许匿名访问，``[AllowAnonymous]``特性**不**允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="aaa46-151">Note, if the IIS site is configured **not** to allow anonymous access, the ``[AllowAnonymous]`` attribute does **not** allow anonymous requests.</span></span> <span data-ttu-id="aaa46-152">``[AllowAnonymous]``特性重写``[Authorize]``属性中允许匿名访问的应用的使用情况。</span><span class="sxs-lookup"><span data-stu-id="aaa46-152">The ``[AllowAnonymous]`` attribute overrides ``[Authorize]`` attribute usage within apps that allow anonymous access.</span></span>

### <a name="impersonation"></a><span data-ttu-id="aaa46-153">模拟</span><span class="sxs-lookup"><span data-stu-id="aaa46-153">Impersonation</span></span>

<span data-ttu-id="aaa46-154">ASP.NET 核心不实现模拟。</span><span class="sxs-lookup"><span data-stu-id="aaa46-154">ASP.NET Core does not implement impersonation.</span></span> <span data-ttu-id="aaa46-155">使用应用程序池或进程标识的所有请求的应用程序标识使用运行应用。</span><span class="sxs-lookup"><span data-stu-id="aaa46-155">Apps run with the application identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="aaa46-156">如果你需要显式执行代表用户操作，使用``WindowsIdentity.RunImpersonated``。</span><span class="sxs-lookup"><span data-stu-id="aaa46-156">If you need to explicitly perform an action on behalf of a user, use ``WindowsIdentity.RunImpersonated``.</span></span> <span data-ttu-id="aaa46-157">在此上下文中运行的单个操作，然后关闭上下文。</span><span class="sxs-lookup"><span data-stu-id="aaa46-157">Run a single action in this context and then close the context.</span></span> <span data-ttu-id="aaa46-158">请注意，``RunImpersonated``不支持异步和不应该用于复杂的方案。</span><span class="sxs-lookup"><span data-stu-id="aaa46-158">Note that ``RunImpersonated`` does not support async and should not be used for complex scenarios.</span></span> <span data-ttu-id="aaa46-159">例如，包装整个请求或中间件链是不受支持或建议。</span><span class="sxs-lookup"><span data-stu-id="aaa46-159">For example, wrapping entire requests or middleware chains is not supported or recommended.</span></span>
