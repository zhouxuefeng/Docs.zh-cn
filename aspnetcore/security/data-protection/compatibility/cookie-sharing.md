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
# <a name="sharing-cookies-between-applications"></a><span data-ttu-id="f493f-103">共享应用程序之间的 cookie</span><span class="sxs-lookup"><span data-stu-id="f493f-103">Sharing cookies between applications</span></span>

<span data-ttu-id="f493f-104">网站通常包含许多单独的 web 应用程序，所有工作一起 harmoniously。</span><span class="sxs-lookup"><span data-stu-id="f493f-104">Web sites commonly consist of many individual web applications, all working together harmoniously.</span></span> <span data-ttu-id="f493f-105">如果应用程序开发人员想要提供良好的单一登录体验，他们通常需要的所有站点中另一个 web 应用，共享彼此之间的身份验证票证。</span><span class="sxs-lookup"><span data-stu-id="f493f-105">If an application developer wants to provide a good single-sign-on experience, they'll often need all of the different web applications within the site to share authentication tickets between each other.</span></span>

<span data-ttu-id="f493f-106">若要支持这种情况下，数据保护堆栈允许在共享 Katana cookie 身份验证和 ASP.NET Core cookie 身份验证票证。</span><span class="sxs-lookup"><span data-stu-id="f493f-106">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

## <a name="sharing-authentication-cookies-between-applications"></a><span data-ttu-id="f493f-107">共享应用程序之间的身份验证 cookie</span><span class="sxs-lookup"><span data-stu-id="f493f-107">Sharing authentication cookies between applications</span></span>

<span data-ttu-id="f493f-108">若要共享两个不同的 ASP.NET Core 应用程序之间的身份验证 cookie，配置每个应用程序应共享 cookie，如下所示。</span><span class="sxs-lookup"><span data-stu-id="f493f-108">To share authentication cookies between two different ASP.NET Core applications, configure each application that should share cookies as follows.</span></span>

<span data-ttu-id="f493f-109">在你配置方法使用 CookieAuthenticationOptions 设置 cookie 数据保护服务和 AuthenticationScheme 以匹配 ASP.NET 4.X。</span><span class="sxs-lookup"><span data-stu-id="f493f-109">In your configure method use the CookieAuthenticationOptions to set up the data protection service for cookies and the AuthenticationScheme to match ASP.NET 4.X.</span></span>

<span data-ttu-id="f493f-110">如果你使用标识：</span><span class="sxs-lookup"><span data-stu-id="f493f-110">If you're using identity:</span></span>

```csharp
app.AddIdentity<ApplicationUser, IdentityRole>(options =>
   {
       options.Cookies.ApplicationCookie.AuthenticationScheme = "ApplicationCookie";
       var protectionProvider = DataProtectionProvider.Create(new DirectoryInfo(@"c:\shared-auth-ticket-keys\"));
       options.Cookies.ApplicationCookie.DataProtectionProvider = protectionProvider;
       options.Cookies.ApplicationCookie.TicketDataFormat = new TicketDataFormat(protectionProvider.CreateProtector("Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationMiddleware", "Cookies", "v2"));
   });
   ```

<span data-ttu-id="f493f-111">如果你直接使用 cookie:</span><span class="sxs-lookup"><span data-stu-id="f493f-111">If you're using cookies directly:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
   {
       DataProtectionProvider = DataProtectionProvider.Create(new DirectoryInfo(@"c:\shared-auth-ticket-keys\"))
   });
   ```
   
<span data-ttu-id="f493f-112">`DataProtectionProvider`需要`Microsoft.AspNetCore.DataProtection.Extensions`NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="f493f-112">The `DataProtectionProvider` requires the `Microsoft.AspNetCore.DataProtection.Extensions` NuGet package.</span></span>

<span data-ttu-id="f493f-113">在这种方式使用时，DirectoryInfo 应指向专门为身份验证 cookie 设置保留的密钥存储位置。</span><span class="sxs-lookup"><span data-stu-id="f493f-113">When used in this manner, the DirectoryInfo should point to a key storage location specifically set aside for authentication cookies.</span></span> <span data-ttu-id="f493f-114">Cookie 身份验证中间件将使用 DataProtectionProvider，即现在的显式提供的实现与使用的应用程序其他部分的数据保护系统隔离。</span><span class="sxs-lookup"><span data-stu-id="f493f-114">The cookie authentication middleware will use the explicitly provided implementation of the DataProtectionProvider, which is now isolated from the data protection system used by other parts of the application.</span></span> <span data-ttu-id="f493f-115">应用程序名称将被忽略 （有意这样，由于你正在尝试获取多个应用程序共享的负载）。</span><span class="sxs-lookup"><span data-stu-id="f493f-115">The application name is ignored (intentionally so, since you're trying to get multiple applications to share payloads).</span></span>

>[!WARNING]
><span data-ttu-id="f493f-116">你应该考虑配置 DataProtectionProvider，以便为在中，密钥进行加密存放情况下，下面的示例。</span><span class="sxs-lookup"><span data-stu-id="f493f-116">You should consider configuring the DataProtectionProvider such that keys are encrypted at rest, as in the below example.</span></span>
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

## <a name="sharing-authentication-cookies-between-aspnet-4x-and-aspnet-core-applications"></a><span data-ttu-id="f493f-117">共享身份验证 cookie 之间 ASP.NET 4.x 和 ASP.NET Core 应用程序</span><span class="sxs-lookup"><span data-stu-id="f493f-117">Sharing authentication cookies between ASP.NET 4.x and ASP.NET Core applications</span></span>

<span data-ttu-id="f493f-118">ASP.NET 4.x 应用程序使用 Katana cookie 身份验证中间件该对话框可以配置为生成与 ASP.NET Core cookie 身份验证中间件兼容的身份验证 cookie。</span><span class="sxs-lookup"><span data-stu-id="f493f-118">ASP.NET 4.x applications which use Katana cookie authentication middleware can be configured to generate authentication cookies which are compatible with the ASP.NET Core cookie authentication middleware.</span></span> <span data-ttu-id="f493f-119">这样，同时仍提供平滑的单一登录体验站点内段落升级大型站点的单个应用程序。</span><span class="sxs-lookup"><span data-stu-id="f493f-119">This allows upgrading a large site's individual applications piecemeal while still providing a smooth single sign on experience across the site.</span></span>

>[!TIP]
> <span data-ttu-id="f493f-120">你可以判断是否现有应用程序通过对在你的项目的 Startup.Auth.cs UseCookieAuthentication 的调用是否存在使用 Katana cookie 身份验证中间件。</span><span class="sxs-lookup"><span data-stu-id="f493f-120">You can tell if your existing application uses Katana cookie authentication middleware by the existence of a call to UseCookieAuthentication in your project's Startup.Auth.cs.</span></span> <span data-ttu-id="f493f-121">ASP.NET 4.x web 应用程序项目创建使用 Visual Studio 2013 和更高版本默认情况下使用 Katana cookie 身份验证中间件。</span><span class="sxs-lookup"><span data-stu-id="f493f-121">ASP.NET 4.x web application projects created with Visual Studio 2013 and later use the Katana cookie authentication middleware by default.</span></span>

> [!NOTE]
> <span data-ttu-id="f493f-122">ASP.NET 4.x 应用程序必须面向.NET Framework 4.5.1 或更高版本，否则所需的 NuGet 包将无法安装。</span><span class="sxs-lookup"><span data-stu-id="f493f-122">Your ASP.NET 4.x application must target .NET Framework 4.5.1 or higher, otherwise the necessary NuGet packages will fail to install.</span></span>

<span data-ttu-id="f493f-123">若要共享你的 ASP.NET 4.x 应用程序和 ASP.NET Core 应用程序之间的身份验证 cookie，如前所述，配置 ASP.NET Core 应用程序，然后通过执行以下步骤配置 ASP.NET 4.x 应用程序。</span><span class="sxs-lookup"><span data-stu-id="f493f-123">To share authentication cookies between your ASP.NET 4.x applications and your ASP.NET Core applications, configure the ASP.NET Core application as stated above, then configure your ASP.NET 4.x applications by following the steps below.</span></span>

1.  <span data-ttu-id="f493f-124">将包 Microsoft.Owin.Security.Interop 安装到每个 ASP.NET 4.x 应用程序。</span><span class="sxs-lookup"><span data-stu-id="f493f-124">Install the package Microsoft.Owin.Security.Interop into each of your ASP.NET 4.x applications.</span></span>

2.   <span data-ttu-id="f493f-125">在 Startup.Auth.cs，找到 UseCookieAuthentication，将通常如下所示调用。</span><span class="sxs-lookup"><span data-stu-id="f493f-125">In Startup.Auth.cs, locate the call to UseCookieAuthentication, which will generally look like the following.</span></span>

    ```csharp
    app.UseCookieAuthentication(new CookieAuthenticationOptions
    {
        // ...
    });
    ```
    
3.  <span data-ttu-id="f493f-126">修改 UseCookieAuthentication 调用，如下所示，更改 CookieName 以匹配使用 ASP.NET Core cookie 的身份验证中间件，提供的已初始化到密钥存储位置，DataProtectionProvider 实例的名称和设置为互操作 ChunkingCookieManager CookieManager，因此块区格式不兼容。</span><span class="sxs-lookup"><span data-stu-id="f493f-126">Modify the call to UseCookieAuthentication as follows, changing the CookieName to match the name used by the ASP.NET Core cookie authentication middleware, providing an instance of a DataProtectionProvider that has been initialized to a key storage location, and set CookieManager to interop ChunkingCookieManager so the chunking format is compatible.</span></span>

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
    <span data-ttu-id="f493f-127">DirectoryInfo 必须指向指向 ASP.NET Core 应用程序与和应该使用相同的设置进行配置的同一存储位置。</span><span class="sxs-lookup"><span data-stu-id="f493f-127">The DirectoryInfo has to point to the same storage location that you pointed your ASP.NET Core application to and should be configured using the same settings.</span></span>

<span data-ttu-id="f493f-128">ASP.NET 4.x 和 ASP.NET Core 应用程序现已配置为共享的身份验证 cookie。</span><span class="sxs-lookup"><span data-stu-id="f493f-128">The ASP.NET 4.x and ASP.NET Core applications are now configured to share authentication cookies.</span></span>

> [!NOTE]
> <span data-ttu-id="f493f-129">你将需要确保每个应用程序的标识系统指向相同的用户数据库。</span><span class="sxs-lookup"><span data-stu-id="f493f-129">You'll need to make sure that the identity system for each application is pointed at the same user database.</span></span> <span data-ttu-id="f493f-130">否则标识系统将生成在运行时失败，当它尝试匹配针对其数据库中的信息的身份验证 cookie 中的信息。</span><span class="sxs-lookup"><span data-stu-id="f493f-130">Otherwise the identity system will produce failures at runtime when it tries to match the information in the authentication cookie against the information in its database.</span></span>
