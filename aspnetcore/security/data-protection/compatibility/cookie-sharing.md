---
title: "共享应用程序之间的 cookie"
author: rick-anderson
description: 
keywords: "共享的 ASP.NET Core,ASP.NET,cookies,Interop,cookie"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 9a7aae45-8e21-4c54-950c-3c29df6c1082
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/compatibility/cookie-sharing
ms.openlocfilehash: dbf52b0a990a3627b8eded22db033c45d51ba6ad
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="sharing-cookies-between-applications"></a>共享应用程序之间的 cookie

网站通常包含许多单独的 web 应用程序，所有工作一起 harmoniously。 如果应用程序开发人员想要提供良好的单一登录体验，他们通常需要的所有站点中另一个 web 应用，共享彼此之间的身份验证票证。

若要支持这种情况下，数据保护堆栈允许在共享 Katana cookie 身份验证和 ASP.NET Core cookie 身份验证票证。

## <a name="sharing-authentication-cookies-between-applications"></a>共享应用程序之间的身份验证 cookie

若要共享两个不同的 ASP.NET Core 应用程序之间的身份验证 cookie，配置每个应用程序应共享 cookie，如下所示。

在你配置方法使用 CookieAuthenticationOptions 设置 cookie 数据保护服务和 AuthenticationScheme 以匹配 ASP.NET 4.X。

如果你使用标识：

```csharp
app.AddIdentity<ApplicationUser, IdentityRole>(options =>
   {
       options.Cookies.ApplicationCookie.AuthenticationScheme = "ApplicationCookie";
       var protectionProvider = DataProtectionProvider.Create(new DirectoryInfo(@"c:\shared-auth-ticket-keys\"));
       options.Cookies.ApplicationCookie.DataProtectionProvider = protectionProvider;
       options.Cookies.ApplicationCookie.TicketDataFormat = new TicketDataFormat(protectionProvider.CreateProtector("Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationMiddleware", "Cookies", "v2"));
   });
   ```

如果你直接使用 cookie:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
   {
       DataProtectionProvider = DataProtectionProvider.Create(new DirectoryInfo(@"c:\shared-auth-ticket-keys\"))
   });
   ```
   
`DataProtectionProvider`需要`Microsoft.AspNetCore.DataProtection.Extensions`NuGet 包。

在这种方式使用时，DirectoryInfo 应指向专门为身份验证 cookie 设置保留的密钥存储位置。 Cookie 身份验证中间件将使用 DataProtectionProvider，即现在的显式提供的实现与使用的应用程序其他部分的数据保护系统隔离。 应用程序名称将被忽略 （有意这样，由于你正在尝试获取多个应用程序共享的负载）。

>[!WARNING]
>你应该考虑配置 DataProtectionProvider，以便为在中，密钥进行加密存放情况下，下面的示例。
>
>
>  ```csharp
>  app.UseCookieAuthentication(new CookieAuthenticationOptions
>  {
>      DataProtectionProvider = DataProtectionProvider.Create(
>          new DirectoryInfo(@"c:\shared-auth-ticket-keys\"),
>          configure =>
>          {
>              configure.ProtectKeysWithCertificate("thumbprint");
>          })
>  });
>  ```

## <a name="sharing-authentication-cookies-between-aspnet-4x-and-aspnet-core-applications"></a>共享身份验证 cookie 之间 ASP.NET 4.x 和 ASP.NET Core 应用程序

ASP.NET 4.x 应用程序使用 Katana cookie 身份验证中间件该对话框可以配置为生成与 ASP.NET Core cookie 身份验证中间件兼容的身份验证 cookie。 这样，同时仍提供平滑的单一登录体验站点内段落升级大型站点的单个应用程序。

>[!TIP]
> 你可以判断是否现有应用程序通过对在你的项目的 Startup.Auth.cs UseCookieAuthentication 的调用是否存在使用 Katana cookie 身份验证中间件。 ASP.NET 4.x web 应用程序项目创建使用 Visual Studio 2013 和更高版本默认情况下使用 Katana cookie 身份验证中间件。

> [!NOTE]
> ASP.NET 4.x 应用程序必须面向.NET Framework 4.5.1 或更高版本，否则所需的 NuGet 包将无法安装。

若要共享你的 ASP.NET 4.x 应用程序和 ASP.NET Core 应用程序之间的身份验证 cookie，如前所述，配置 ASP.NET Core 应用程序，然后通过执行以下步骤配置 ASP.NET 4.x 应用程序。

1.  将包 Microsoft.Owin.Security.Interop 安装到每个 ASP.NET 4.x 应用程序。

2.   在 Startup.Auth.cs，找到 UseCookieAuthentication，将通常如下所示调用。

    ```csharp
    app.UseCookieAuthentication(new CookieAuthenticationOptions
    {
        // ...
    });
    ```
    
3.  修改 UseCookieAuthentication 调用，如下所示，更改 CookieName 以匹配使用 ASP.NET Core cookie 的身份验证中间件，提供的已初始化到密钥存储位置，DataProtectionProvider 实例的名称和设置为互操作 ChunkingCookieManager CookieManager，因此块区格式不兼容。

    ```csharp
    app.UseCookieAuthentication(new CookieAuthenticationOptions
       {
           AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
           CookieName = ".AspNetCore.Cookies",
           // CookieName = ".AspNetCore.ApplicationCookie", (if you're using identity)
           // CookiePath = "...", (if necessary)
           // ...
           TicketDataFormat = new AspNetTicketDataFormat(
               new DataProtectorShim(
                   DataProtectionProvider.Create(new DirectoryInfo(@"c:\shared-auth-ticket-keys\"))
                   .CreateProtector("Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationMiddleware",
                   "Cookies", "v2"))),
           CookieManager = new ChunkingCookieManager()
       });
       ```
    DirectoryInfo 必须指向指向 ASP.NET Core 应用程序与和应该使用相同的设置进行配置的同一存储位置。

ASP.NET 4.x 和 ASP.NET Core 应用程序现已配置为共享的身份验证 cookie。

> [!NOTE]
> 你将需要确保每个应用程序的标识系统指向相同的用户数据库。 否则标识系统将生成在运行时失败，当它尝试匹配针对其数据库中的信息的身份验证 cookie 中的信息。
