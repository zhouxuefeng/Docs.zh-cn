---
title: "迁移的身份验证和标识到 ASP.NET 核心 2.0"
author: scottaddie
description: "本文概述了迁移 ASP.NET Core 1.x 身份验证和标识为 ASP.NET 核心 2.0 的最常见步骤。"
keywords: "ASP.NET 核心，标识、 身份验证"
ms.author: scaddie
manager: wpickett
ms.date: 08/02/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: b4e67e7cfea3c01e3ca8c0d5df2a04e789749932
ms.sourcegitcommit: f8f6b5934bd071a349f5bc1e389365c52b1c00fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2017
---
# <a name="migrating-authentication-and-identity-to-aspnet-core-20"></a><span data-ttu-id="9bcb7-104">迁移的身份验证和标识到 ASP.NET 核心 2.0</span><span class="sxs-lookup"><span data-stu-id="9bcb7-104">Migrating Authentication and Identity to ASP.NET Core 2.0</span></span>

<span data-ttu-id="9bcb7-105">通过[Scott Addie](https://github.com/scottaddie)和[Hao 永远](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="9bcb7-105">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="9bcb7-106">ASP.NET 核心 2.0 具有用于身份验证的新模型和[标识](xref:security/authentication/identity)这简化了使用服务的配置。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-106">ASP.NET Core 2.0 has a new model for authentication and [Identity](xref:security/authentication/identity) which simplifies configuration by using services.</span></span> <span data-ttu-id="9bcb7-107">ASP.NET 核心 1.x 应用程序使用身份验证或标识可以更新以使用新的模型，如下所述。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-107">ASP.NET Core 1.x applications that use authentication or Identity can be updated to use the new model as outlined below.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="9bcb7-108">身份验证中间件和服务</span><span class="sxs-lookup"><span data-stu-id="9bcb7-108">Authentication Middleware and Services</span></span>
<span data-ttu-id="9bcb7-109">在 1.x 项目中，通过中间件配置身份验证。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-109">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="9bcb7-110">中间件方法调用每个你想要支持的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-110">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="9bcb7-111">下面的 1.x 示例将 Facebook 身份验证配置中标识*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bcb7-111">The following 1.x example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions { 
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
} 
```

<span data-ttu-id="9bcb7-112">在 2.0 项目中，通过服务配置身份验证。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-112">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="9bcb7-113">在中注册每个身份验证方案`ConfigureServices`方法*Startup.cs*。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-113">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs*.</span></span> <span data-ttu-id="9bcb7-114">`UseIdentity`方法将替换`UseAuthentication`。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-114">The `UseIdentity` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="9bcb7-115">下面的 2.0 示例将 Facebook 身份验证配置中标识*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bcb7-115">The following 2.0 example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options => {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

<span data-ttu-id="9bcb7-116">`UseAuthentication`方法将添加一个单一的身份验证中间件组件，它负责自动身份验证和远程身份验证请求的处理。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-116">The `UseAuthentication` method adds a single authentication middleware component which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="9bcb7-117">它将替换所有单独的中间件组件与单个，常见的中间件组件。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-117">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="9bcb7-118">以下是每个主要身份验证方案的 2.0 迁移说明。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-118">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="9bcb7-119">基于 cookie 的身份验证</span><span class="sxs-lookup"><span data-stu-id="9bcb7-119">Cookie-based Authentication</span></span>
<span data-ttu-id="9bcb7-120">选择以下两个选项之一，然后进行必要的更改在*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bcb7-120">Select one of the two options below, and make the necessary changes in *Startup.cs*:</span></span>

1. <span data-ttu-id="9bcb7-121">标识与使用 cookie</span><span class="sxs-lookup"><span data-stu-id="9bcb7-121">Use cookies with Identity</span></span>
    - <span data-ttu-id="9bcb7-122">替换`UseIdentity`与`UseAuthentication`中`Configure`方法：</span><span class="sxs-lookup"><span data-stu-id="9bcb7-122">Replace `UseIdentity` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="9bcb7-123">调用`AddIdentity`中的方法`ConfigureServices`方法将添加 cookie 身份验证服务。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-123">Invoke the `AddIdentity` method in the `ConfigureServices` method to add the cookie authentication services.</span></span>
    - <span data-ttu-id="9bcb7-124">（可选） 调用`ConfigureApplicationCookie`或`ConfigureExternalCookie`中的方法`ConfigureServices`方法来调整标识 cookie 设置。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-124">Optionally, invoke the `ConfigureApplicationCookie` or `ConfigureExternalCookie` method in the `ConfigureServices` method to tweak the Identity cookie settings.</span></span>

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();
    
        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="9bcb7-125">使用 cookie，而无需标识</span><span class="sxs-lookup"><span data-stu-id="9bcb7-125">Use cookies without Identity</span></span>
    - <span data-ttu-id="9bcb7-126">替换`UseCookieAuthentication`方法调用`Configure`方法替换`UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="9bcb7-126">Replace the `UseCookieAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>
  
        ```csharp
        app.UseAuthentication();
        ```
 
    - <span data-ttu-id="9bcb7-127">调用`AddAuthentication`和`AddCookie`中的方法`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="9bcb7-127">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method:</span></span>

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User, 
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options => {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="9bcb7-128">JWT 持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="9bcb7-128">JWT Bearer Authentication</span></span>
<span data-ttu-id="9bcb7-129">进行中的以下更改*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bcb7-129">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="9bcb7-130">替换`UseJwtBearerAuthentication`方法调用`Configure`方法替换`UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="9bcb7-130">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>
 
    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="9bcb7-131">调用`AddJwtBearer`中的方法`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="9bcb7-131">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options => {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="9bcb7-132">此代码段不使用标识，因此应通过将传递设置的默认方案`JwtBearerDefaults.AuthenticationScheme`到`AddAuthentication`方法。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-132">This code snippet doesn't use Identity, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="9bcb7-133">OpenID Connect (OIDC) 身份验证</span><span class="sxs-lookup"><span data-stu-id="9bcb7-133">OpenID Connect (OIDC) Authentication</span></span>
<span data-ttu-id="9bcb7-134">进行中的以下更改*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bcb7-134">Make the following changes in *Startup.cs*:</span></span>

- <span data-ttu-id="9bcb7-135">替换`UseOpenIdConnectAuthentication`方法调用`Configure`方法替换`UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="9bcb7-135">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="9bcb7-136">调用`AddOpenIdConnect`中的方法`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="9bcb7-136">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(options => {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options => {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

### <a name="facebook-authentication"></a><span data-ttu-id="9bcb7-137">Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="9bcb7-137">Facebook Authentication</span></span>
<span data-ttu-id="9bcb7-138">进行中的以下更改*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bcb7-138">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="9bcb7-139">替换`UseFacebookAuthentication`方法调用`Configure`方法替换`UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="9bcb7-139">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>
 
    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="9bcb7-140">调用`AddFacebook`中的方法`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="9bcb7-140">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddAuthentication()
            .AddFacebook(options => {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="9bcb7-141">Google 身份验证</span><span class="sxs-lookup"><span data-stu-id="9bcb7-141">Google Authentication</span></span>
<span data-ttu-id="9bcb7-142">进行中的以下更改*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bcb7-142">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="9bcb7-143">替换`UseGoogleAuthentication`方法调用`Configure`方法替换`UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="9bcb7-143">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>
 
    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="9bcb7-144">调用`AddGoogle`中的方法`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="9bcb7-144">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options => {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });    
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="9bcb7-145">Microsoft 帐户身份验证</span><span class="sxs-lookup"><span data-stu-id="9bcb7-145">Microsoft Account Authentication</span></span>
<span data-ttu-id="9bcb7-146">进行中的以下更改*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bcb7-146">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="9bcb7-147">替换`UseMicrosoftAccountAuthentication`方法调用`Configure`方法替换`UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="9bcb7-147">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="9bcb7-148">调用`AddMicrosoftAccount`中的方法`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="9bcb7-148">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options => {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ``` 

### <a name="twitter-authentication"></a><span data-ttu-id="9bcb7-149">Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="9bcb7-149">Twitter Authentication</span></span>
<span data-ttu-id="9bcb7-150">进行中的以下更改*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bcb7-150">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="9bcb7-151">替换`UseTwitterAuthentication`方法调用`Configure`方法替换`UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="9bcb7-151">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>
 
    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="9bcb7-152">调用`AddTwitter`中的方法`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="9bcb7-152">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options => {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="9bcb7-153">设置默认身份验证方案</span><span class="sxs-lookup"><span data-stu-id="9bcb7-153">Setting Default Authentication Schemes</span></span>
<span data-ttu-id="9bcb7-154">在 1.x`AutomaticAuthenticate`和`AutomaticChallenge`属性用于设置在单一身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-154">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="9bcb7-155">没有良好方法来强制执行此。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-155">There was no good way to enforce this.</span></span>

<span data-ttu-id="9bcb7-156">在 2.0 中，这两个属性已删除为对各个标志`AuthenticationOptions`实例并已移至基[AuthenticationOptions](/aspnet/core/api/microsoft.aspnetcore.builder.authenticationoptions)类。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-156">In 2.0, these two properties have been removed as flags on the individual `AuthenticationOptions` instance and have moved into the base [AuthenticationOptions](/aspnet/core/api/microsoft.aspnetcore.builder.authenticationoptions) class.</span></span> <span data-ttu-id="9bcb7-157">可以在中配置属性`AddAuthentication`内的方法调用`ConfigureServices`方法*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bcb7-157">The properties can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="9bcb7-158">或者，使用一个重载的版本的`AddAuthentication`方法以设置多个属性。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-158">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="9bcb7-159">在下面的重载的方法示例中，默认方案设置为`CookieAuthenticationDefaults.AuthenticationScheme`。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-159">In the following overloaded method example, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="9bcb7-160">或者可能在个人中指定的身份验证方案`[Authorize]`属性或授权策略。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-160">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options => {
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="9bcb7-161">如果下列条件之一为 true，请在 2.0 中定义了默认的方案：</span><span class="sxs-lookup"><span data-stu-id="9bcb7-161">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="9bcb7-162">你希望用户可自动登录</span><span class="sxs-lookup"><span data-stu-id="9bcb7-162">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="9bcb7-163">你使用`[Authorize]`而无需指定方案的属性或授权策略</span><span class="sxs-lookup"><span data-stu-id="9bcb7-163">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="9bcb7-164">此规则的唯一例外是`AddIdentity`方法。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-164">An exception to this rule is the `AddIdentity` method.</span></span> <span data-ttu-id="9bcb7-165">此方法将为您和设置默认值进行身份验证以及与应用程序 cookie 质询方案添加 cookie `IdentityConstants.ApplicationScheme`。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-165">This method adds cookies for you and sets the default authenticate and challenge schemes to the application cookie `IdentityConstants.ApplicationScheme`.</span></span> <span data-ttu-id="9bcb7-166">此外，它将默认登录方案设置为外部 cookie `IdentityConstants.ExternalScheme`。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-166">Additionally, it sets the default sign-in scheme to the external cookie `IdentityConstants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="9bcb7-167">使用 HttpContext 身份验证扩展插件</span><span class="sxs-lookup"><span data-stu-id="9bcb7-167">Use HttpContext Authentication Extensions</span></span>
<span data-ttu-id="9bcb7-168">`IAuthenticationManager`接口是 1.x 身份验证系统的主入口点。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-168">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="9bcb7-169">它已替换为一组新的`HttpContext`中的扩展方法`Microsoft.AspNetCore.Authentication`命名空间。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-169">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="9bcb7-170">例如，1.x 项目引用`Authentication`属性：</span><span class="sxs-lookup"><span data-stu-id="9bcb7-170">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="9bcb7-171">在 2.0 项目中，导入`Microsoft.AspNetCore.Authentication`命名空间，并删除`Authentication`属性引用：</span><span class="sxs-lookup"><span data-stu-id="9bcb7-171">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="9bcb7-172">Windows 身份验证 (HTTP.sys / IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="9bcb7-172">Windows Authentication (HTTP.sys / IISIntegration)</span></span>
<span data-ttu-id="9bcb7-173">有两种变体 Windows 身份验证：</span><span class="sxs-lookup"><span data-stu-id="9bcb7-173">There are two variations of Windows authentication:</span></span>
1. <span data-ttu-id="9bcb7-174">主机仅允许经过身份验证的用户</span><span class="sxs-lookup"><span data-stu-id="9bcb7-174">The host only allows authenticated users</span></span>
2. <span data-ttu-id="9bcb7-175">主机允许同时匿名和身份验证的用户</span><span class="sxs-lookup"><span data-stu-id="9bcb7-175">The host allows both anonymous and authenticated users</span></span>

<span data-ttu-id="9bcb7-176">上面所述的第一个变体不受 2.0 的更改。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-176">The first variation described above is unaffected by the 2.0 changes.</span></span>

<span data-ttu-id="9bcb7-177">2.0 更改的情况下，会影响上面所述的第二个变体。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-177">The second variation described above is affected by the 2.0 changes.</span></span> <span data-ttu-id="9bcb7-178">例如，你可能将以允许匿名用户到你的应用程序在 IIS 或[HTTP.sys](xref:fundamentals/servers/weblistener)层在控制器级别但授权用户。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-178">As an example, you may be allowing anonymous users into your application at the IIS or [HTTP.sys](xref:fundamentals/servers/weblistener) layer but authorizing users at the Controller level.</span></span> <span data-ttu-id="9bcb7-179">在此方案中，设置默认方案为`IISDefaults.AuthenticationScheme`中`ConfigureServices`方法*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bcb7-179">In this scenario, set the default scheme to `IISDefaults.AuthenticationScheme` in the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

<span data-ttu-id="9bcb7-180">相应地，如果设置的默认方案会使质询无法正常工作的授权请求。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-180">Failure to set the default scheme accordingly prevents the authorize request to challenge from working.</span></span>

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a><span data-ttu-id="9bcb7-181">IdentityCookieOptions 实例</span><span class="sxs-lookup"><span data-stu-id="9bcb7-181">IdentityCookieOptions Instances</span></span>
<span data-ttu-id="9bcb7-182">2.0 更改的副作用是时切换到使用名为而不是 cookie 选项实例的选项。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-182">A side effect of the 2.0 changes is the switch to using named options instead of cookie options instances.</span></span> <span data-ttu-id="9bcb7-183">删除自定义标识 cookie 方案名称的能力。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-183">The ability to customize the Identity cookie scheme names is removed.</span></span>

<span data-ttu-id="9bcb7-184">例如，1.x 项目使用[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)传递`IdentityCookieOptions`参数转换*AccountController.cs*。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-184">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `IdentityCookieOptions` parameter into *AccountController.cs*.</span></span> <span data-ttu-id="9bcb7-185">从提供的实例访问外部 cookie 身份验证方案：</span><span class="sxs-lookup"><span data-stu-id="9bcb7-185">The external cookie authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="9bcb7-186">前面提到的构造函数注入将成为在 2.0 项目中，不必要和`_externalCookieScheme`可以删除字段：</span><span class="sxs-lookup"><span data-stu-id="9bcb7-186">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_externalCookieScheme` field can be deleted:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="9bcb7-187">`IdentityConstants.ExternalScheme`可以直接使用常量：</span><span class="sxs-lookup"><span data-stu-id="9bcb7-187">The `IdentityConstants.ExternalScheme` constant can be used directly:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a><span data-ttu-id="9bcb7-188">添加 IdentityUser POCO 导航属性</span><span class="sxs-lookup"><span data-stu-id="9bcb7-188">Add IdentityUser POCO Navigation Properties</span></span>
<span data-ttu-id="9bcb7-189">基的 Entity Framework (EF) 核心导航属性`IdentityUser`POCO （普通旧 CLR 对象） 已被删除。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-189">The Entity Framework (EF) Core navigation properties of the base `IdentityUser` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="9bcb7-190">如果你 1.x 的项目使用这些属性，手动将它们添加回 2.0 项目：</span><span class="sxs-lookup"><span data-stu-id="9bcb7-190">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

<span data-ttu-id="9bcb7-191">若要防止重复的外键，运行 EF 核心迁移时，将以下代码添加到你`IdentityDbContext`类的`OnModelCreating`方法 (后`base.OnModelCreating();`调用):</span><span class="sxs-lookup"><span data-stu-id="9bcb7-191">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `IdentityDbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Identity table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="9bcb7-192">替换 GetExternalAuthenticationSchemes</span><span class="sxs-lookup"><span data-stu-id="9bcb7-192">Replace GetExternalAuthenticationSchemes</span></span>
<span data-ttu-id="9bcb7-193">同步方法`GetExternalAuthenticationSchemes`已删除为支持的异步版本。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-193">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="9bcb7-194">1.x 项目有以下代码*ManageController.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bcb7-194">1.x projects have the following code in *ManageController.cs*:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="9bcb7-195">此方法将出现在*Login.cshtml*太：</span><span class="sxs-lookup"><span data-stu-id="9bcb7-195">This method appears in *Login.cshtml* too:</span></span>

[!code-cshtml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?range=62,75-84)]

<span data-ttu-id="9bcb7-196">在 2.0 项目中，使用`GetExternalAuthenticationSchemesAsync`方法：</span><span class="sxs-lookup"><span data-stu-id="9bcb7-196">In 2.0 projects, use the `GetExternalAuthenticationSchemesAsync` method:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="9bcb7-197">在*Login.cshtml*、`AuthenticationScheme`中访问属性`foreach`循环更改为`Name`:</span><span class="sxs-lookup"><span data-stu-id="9bcb7-197">In *Login.cshtml*, the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?range=62,75-84)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="9bcb7-198">ManageLoginsViewModel 属性更改</span><span class="sxs-lookup"><span data-stu-id="9bcb7-198">ManageLoginsViewModel Property Change</span></span>
<span data-ttu-id="9bcb7-199">A`ManageLoginsViewModel`对象将用于`ManageLogins`操作*ManageController.cs*。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-199">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs*.</span></span> <span data-ttu-id="9bcb7-200">1.x 项目，该对象中`OtherLogins`属性的返回类型是`IList<AuthenticationDescription>`。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-200">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="9bcb7-201">此返回类型需要导入`Microsoft.AspNetCore.Http.Authentication`:</span><span class="sxs-lookup"><span data-stu-id="9bcb7-201">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="9bcb7-202">在 2.0 项目中，返回类型更改为`IList<AuthenticationScheme>`。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-202">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="9bcb7-203">此新的返回类型需要替换`Microsoft.AspNetCore.Http.Authentication`使用导入`Microsoft.AspNetCore.Authentication`导入。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-203">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="9bcb7-204">其他资源</span><span class="sxs-lookup"><span data-stu-id="9bcb7-204">Additional Resources</span></span>
<span data-ttu-id="9bcb7-205">有关其他详细信息和讨论，请参阅[针对身份验证 2.0 讨论](https://github.com/aspnet/Security/issues/1338)GitHub 上的问题。</span><span class="sxs-lookup"><span data-stu-id="9bcb7-205">For additional details and discussion, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>
