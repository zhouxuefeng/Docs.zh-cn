---
title: "使用 Cookie 而无需 ASP.NET 核心标识的身份验证"
author: rick-anderson
description: "使用 cookie 而无需 ASP.NET 核心标识的身份验证的说明"
keywords: "ASP.NET 核心 cookie"
ms.author: riande
manager: wpickett
ms.date: 08/14/2017
ms.topic: article
ms.assetid: 2bdcbf95-8d9d-4537-a4a0-a5ee439dcb62
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/cookie
ms.openlocfilehash: b728c3d62b59f28f1d020b6f3732918a1fcdf4eb
ms.sourcegitcommit: 74a8ad9c1ba5c155d7c4303e67632a0922c38e86
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2017
---
# <a name="using-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="139ed-104">使用 Cookie 而无需 ASP.NET 核心标识的身份验证</span><span class="sxs-lookup"><span data-stu-id="139ed-104">Using Cookie Authentication without ASP.NET Core Identity</span></span>

<a name="security-authentication-cookie-middleware"></a>

<span data-ttu-id="139ed-105">ASP.NET 核心 1.x 提供 cookie[中间件](../../fundamentals/middleware.md#fundamentals-middleware)其用户主体序列化为的加密 cookie，然后，在后续请求中，将验证该 cookie，将重新创建主体，并将它分配给`HttpContext.User`属性.</span><span class="sxs-lookup"><span data-stu-id="139ed-105">ASP.NET Core 1.x provides cookie [middleware](../../fundamentals/middleware.md#fundamentals-middleware) which serializes a user principal into an encrypted cookie and then, on subsequent requests, validates the cookie, recreates the principal, and assigns it to the `HttpContext.User` property.</span></span> <span data-ttu-id="139ed-106">如果你想要提供自己的登录屏幕和用户数据库，你可以作为独立功能使用 cookie 中间件。</span><span class="sxs-lookup"><span data-stu-id="139ed-106">If you want to provide your own login screens and user databases, you can use the cookie middleware as a standalone feature.</span></span>

<span data-ttu-id="139ed-107">一个重要更改在 ASP.NET Core 2.x 是 cookie 中间件不存在。</span><span class="sxs-lookup"><span data-stu-id="139ed-107">A major change in ASP.NET Core 2.x is that the cookie middleware is absent.</span></span> <span data-ttu-id="139ed-108">相反，`UseAuthentication`方法调用中的`Configure`方法*Startup.cs*添加 AuthenticationMiddleware 将设置`HttpContext.User`属性。</span><span class="sxs-lookup"><span data-stu-id="139ed-108">Instead, the `UseAuthentication` method invocation in the `Configure` method of *Startup.cs* adds the AuthenticationMiddleware which sets the `HttpContext.User` property.</span></span>

<a name="security-authentication-cookie-middleware-configuring"></a>

## <a name="adding-and-configuring"></a><span data-ttu-id="139ed-109">添加和配置</span><span class="sxs-lookup"><span data-stu-id="139ed-109">Adding and configuring</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="139ed-110">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="139ed-110">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="139ed-111">完成以下步骤：</span><span class="sxs-lookup"><span data-stu-id="139ed-111">Complete the following steps:</span></span>

- <span data-ttu-id="139ed-112">如果不使用`Microsoft.AspNetCore.All` [metapackage](xref:fundamentals/metapackage)，安装 2.0 以上版本的`Microsoft.AspNetCore.Authentication.Cookies`项目中的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="139ed-112">If not using the `Microsoft.AspNetCore.All` [metapackage](xref:fundamentals/metapackage), install version 2.0+ of the `Microsoft.AspNetCore.Authentication.Cookies` NuGet package in your project.</span></span>

- <span data-ttu-id="139ed-113">调用`UseAuthentication`中的方法`Configure`方法*Startup.cs*文件：</span><span class="sxs-lookup"><span data-stu-id="139ed-113">Invoke the `UseAuthentication` method in the `Configure` method of the *Startup.cs* file:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="139ed-114">调用`AddAuthentication`和`AddCookie`中的方法`ConfigureServices`方法*Startup.cs*文件：</span><span class="sxs-lookup"><span data-stu-id="139ed-114">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method of the *Startup.cs* file:</span></span>

    ```csharp
    services.AddAuthentication("MyCookieAuthenticationScheme")
            .AddCookie(options => {
                options.AccessDeniedPath = "/Account/Forbidden/";
                options.LoginPath = "/Account/Unauthorized/";
            });
    ```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="139ed-115">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="139ed-115">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="139ed-116">完成以下步骤：</span><span class="sxs-lookup"><span data-stu-id="139ed-116">Complete the following steps:</span></span>

- <span data-ttu-id="139ed-117">安装`Microsoft.AspNetCore.Authentication.Cookies`项目中的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="139ed-117">Install the `Microsoft.AspNetCore.Authentication.Cookies` NuGet package in your project.</span></span> <span data-ttu-id="139ed-118">此程序包包含 cookie 中间件。</span><span class="sxs-lookup"><span data-stu-id="139ed-118">This package contains the cookie middleware.</span></span>

- <span data-ttu-id="139ed-119">添加以下行`Configure`方法在你*Startup.cs*文件，然后`app.UseMvc()`语句：</span><span class="sxs-lookup"><span data-stu-id="139ed-119">Add the following lines to the `Configure` method in your *Startup.cs* file before the `app.UseMvc()` statement:</span></span>

    ```csharp
    app.UseCookieAuthentication(new CookieAuthenticationOptions()
    {
        AccessDeniedPath = "/Account/Forbidden/",
        AuthenticationScheme = "MyCookieAuthenticationScheme",
        AutomaticAuthenticate = true,
        AutomaticChallenge = true,
        LoginPath = "/Account/Unauthorized/"
    });
    ```

---

<span data-ttu-id="139ed-120">上面的代码段配置部分或全部的以下选项：</span><span class="sxs-lookup"><span data-stu-id="139ed-120">The code snippets above configure some or all of the following options:</span></span>

* <span data-ttu-id="139ed-121">`AccessDeniedPath`-这是请求重当用户尝试访问的资源，但不能通过任何定向到的相对路径[授权策略](xref:security/authorization/policies#security-authorization-policies-based)对该资源。</span><span class="sxs-lookup"><span data-stu-id="139ed-121">`AccessDeniedPath` - This is the relative path to which requests redirect when a user attempts to access a resource but does not pass any [authorization policies](xref:security/authorization/policies#security-authorization-policies-based) for that resource.</span></span>

* <span data-ttu-id="139ed-122">`AuthenticationScheme`-这是一个值来识别特定 cookie 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="139ed-122">`AuthenticationScheme` - This is a value by which a particular cookie authentication scheme is known.</span></span> <span data-ttu-id="139ed-123">这是有用的如果有多个实例的 cookie 身份验证，并且你希望到[限制到一个实例的授权](xref:security/authorization/limitingidentitybyscheme#security-authorization-limiting-by-scheme)。</span><span class="sxs-lookup"><span data-stu-id="139ed-123">This is useful when there are multiple instances of cookie authentication and you want to [limit authorization to one instance](xref:security/authorization/limitingidentitybyscheme#security-authorization-limiting-by-scheme).</span></span>

* <span data-ttu-id="139ed-124">`AutomaticAuthenticate`-此标志是仅适用于的 ASP.NET Core 1.x。</span><span class="sxs-lookup"><span data-stu-id="139ed-124">`AutomaticAuthenticate` - This flag is relevant only for ASP.NET Core 1.x.</span></span> <span data-ttu-id="139ed-125">它表示应在每个请求上运行，尝试验证并重新构造它创建的任何序列化的主体 cookie 身份验证。</span><span class="sxs-lookup"><span data-stu-id="139ed-125">It indicates that the cookie authentication should run on every request and attempt to validate and reconstruct any serialized principal it created.</span></span>

* <span data-ttu-id="139ed-126">`AutomaticChallenge`-此标志是仅适用于的 ASP.NET Core 1.x。</span><span class="sxs-lookup"><span data-stu-id="139ed-126">`AutomaticChallenge` - This flag is relevant only for ASP.NET Core 1.x.</span></span> <span data-ttu-id="139ed-127">它指示 1.x cookie 身份验证应重定向到浏览器`LoginPath`或`AccessDeniedPath`时授权失败。</span><span class="sxs-lookup"><span data-stu-id="139ed-127">It indicates that the 1.x cookie authentication should redirect the browser to the `LoginPath` or `AccessDeniedPath` when authorization fails.</span></span>

* <span data-ttu-id="139ed-128">`LoginPath`-这是该请求将重定向到当用户尝试访问的资源，但未经身份验证的相对路径。</span><span class="sxs-lookup"><span data-stu-id="139ed-128">`LoginPath` - This is the relative path to which requests redirect when a user attempts to access a resource but has not been authenticated.</span></span>

<span data-ttu-id="139ed-129">[其他选项](xref:security/authentication/cookie#security-authentication-cookie-options)包括能够设置 cookie 身份验证创建，任何声明的颁发者的身份验证的 cookie 名称下降，cookie 和各种安全属性，该 cookie 的域。</span><span class="sxs-lookup"><span data-stu-id="139ed-129">[Other options](xref:security/authentication/cookie#security-authentication-cookie-options) include the ability to set the issuer for any claims the cookie authentication creates, the name of the cookie the authentication drops, the domain for the cookie and various security properties on the cookie.</span></span> <span data-ttu-id="139ed-130">默认情况下，cookie 身份验证将为它创建，如任何 cookie，使用适当的安全选项：</span><span class="sxs-lookup"><span data-stu-id="139ed-130">By default, the cookie authentication uses appropriate security options for any cookies it creates, such as:</span></span>
- <span data-ttu-id="139ed-131">此 HttpOnly 标志设置为阻止 cookie 在客户端 JavaScript 中的访问</span><span class="sxs-lookup"><span data-stu-id="139ed-131">Setting the HttpOnly flag to prevent cookie access in client-side JavaScript</span></span>
- <span data-ttu-id="139ed-132">如果请求具有通过 HTTPS 的行程，限制为 HTTPS cookie</span><span class="sxs-lookup"><span data-stu-id="139ed-132">Limiting the cookie to HTTPS if a request has traveled over HTTPS</span></span>

<a name="security-authentication-cookie-middleware-creating-a-cookie"></a>

## <a name="creating-an-identity-cookie"></a><span data-ttu-id="139ed-133">创建标识 cookie</span><span class="sxs-lookup"><span data-stu-id="139ed-133">Creating an Identity cookie</span></span>

<span data-ttu-id="139ed-134">若要创建一个保存你的用户信息的 cookie，您必须先构造[ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal)保存你想要在 cookie 进行序列化信息。</span><span class="sxs-lookup"><span data-stu-id="139ed-134">To create a cookie holding your user information, you must construct a [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal) holding the information you wish to be serialized in the cookie.</span></span> <span data-ttu-id="139ed-135">一旦合适`ClaimsPrincipal`对象，请调用以下命令，在控制器方法：</span><span class="sxs-lookup"><span data-stu-id="139ed-135">Once you have a suitable `ClaimsPrincipal` object, call the following inside your controller method:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="139ed-136">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="139ed-136">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
await HttpContext.SignInAsync("MyCookieAuthenticationScheme", principal);
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="139ed-137">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="139ed-137">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignInAsync("MyCookieAuthenticationScheme", principal);
```

---

<span data-ttu-id="139ed-138">这将创建一个加密的 cookie，并将其添加到当前响应。</span><span class="sxs-lookup"><span data-stu-id="139ed-138">This creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="139ed-139">`AuthenticationScheme`期间指定[配置](xref:security/authentication/cookie#security-authentication-cookie-middleware-configuring)时调用，必须使用`SignInAsync`。</span><span class="sxs-lookup"><span data-stu-id="139ed-139">The `AuthenticationScheme` specified during [configuration](xref:security/authentication/cookie#security-authentication-cookie-middleware-configuring) must be used when calling `SignInAsync`.</span></span>

<span data-ttu-id="139ed-140">事实上，使用的加密是 ASP.NET Core[数据保护](xref:security/data-protection/using-data-protection#security-data-protection-getting-started)系统。</span><span class="sxs-lookup"><span data-stu-id="139ed-140">Under the covers, the encryption used is ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection#security-data-protection-getting-started) system.</span></span> <span data-ttu-id="139ed-141">如果您要承载的多台计算机，负载平衡，或使用 web 场，然后，你需要[配置数据保护](xref:security/data-protection/configuration/overview#data-protection-configuring)使用同一密钥链和应用程序标识符。</span><span class="sxs-lookup"><span data-stu-id="139ed-141">If you are hosting on multiple machines, load balancing, or using a web farm, then you need to [configure data protection](xref:security/data-protection/configuration/overview#data-protection-configuring) to use the same key ring and application identifier.</span></span>

## <a name="signing-out"></a><span data-ttu-id="139ed-142">注销</span><span class="sxs-lookup"><span data-stu-id="139ed-142">Signing out</span></span>

<span data-ttu-id="139ed-143">若要注销当前用户，并删除其 cookie，调用以下命令，在你的控制器：</span><span class="sxs-lookup"><span data-stu-id="139ed-143">To sign out the current user and delete their cookie, call the following inside your controller:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="139ed-144">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="139ed-144">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
await HttpContext.SignOutAsync("MyCookieAuthenticationScheme");
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="139ed-145">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="139ed-145">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignOutAsync("MyCookieAuthenticationScheme");
```

---

## <a name="reacting-to-back-end-changes"></a><span data-ttu-id="139ed-146">对后端更改的作出反应</span><span class="sxs-lookup"><span data-stu-id="139ed-146">Reacting to back-end changes</span></span>

>[!WARNING]
> <span data-ttu-id="139ed-147">一旦创建主体 cookie 后，它将成为标识的单个来源。</span><span class="sxs-lookup"><span data-stu-id="139ed-147">Once a principal cookie has been created, it becomes the single source of identity.</span></span> <span data-ttu-id="139ed-148">即使在后端系统中禁用用户，cookie 身份验证不了解，并且用户一直保持登录，只要其 cookie 的有效期。</span><span class="sxs-lookup"><span data-stu-id="139ed-148">Even if you disable a user in your back-end systems, the cookie authentication has no knowledge of this, and a user stays logged in as long as their cookie is valid.</span></span>

<span data-ttu-id="139ed-149">Cookie 身份验证提供了一系列其选项类中的事件。</span><span class="sxs-lookup"><span data-stu-id="139ed-149">The cookie authentication provides a series of events in its option class.</span></span> <span data-ttu-id="139ed-150">`ValidateAsync()`事件可以用于截获和重写的 cookie 身份验证。</span><span class="sxs-lookup"><span data-stu-id="139ed-150">The `ValidateAsync()` event can be used to intercept and override validation of the cookie identity.</span></span>

<span data-ttu-id="139ed-151">请考虑一个可能有"LastChanged"列的后端用户数据库。</span><span class="sxs-lookup"><span data-stu-id="139ed-151">Consider a back-end user database that may have a "LastChanged" column.</span></span> <span data-ttu-id="139ed-152">为了使 cookie 无效数据库更改时，你应首先，当[创建 cookie](xref:security/authentication/cookie#security-authentication-cookie-middleware-creating-a-cookie)，添加"LastChanged"声明包含的当前值。</span><span class="sxs-lookup"><span data-stu-id="139ed-152">In order to invalidate a cookie when the database changes, you should first, when [creating the cookie](xref:security/authentication/cookie#security-authentication-cookie-middleware-creating-a-cookie), add a "LastChanged" claim containing the current value.</span></span> <span data-ttu-id="139ed-153">当数据库更改时，应更新的"LastChanged"值。</span><span class="sxs-lookup"><span data-stu-id="139ed-153">When the database changes, the "LastChanged" value should be updated.</span></span>

<span data-ttu-id="139ed-154">若要实现的替代`ValidateAsync()`事件，你必须编写具有以下签名的方法：</span><span class="sxs-lookup"><span data-stu-id="139ed-154">To implement an override for the `ValidateAsync()` event, you must write a method with the following signature:</span></span>

```csharp
Task ValidateAsync(CookieValidatePrincipalContext context);
```

<span data-ttu-id="139ed-155">ASP.NET 核心标识作为的一部分实现这一检查其`SecurityStampValidator`。</span><span class="sxs-lookup"><span data-stu-id="139ed-155">ASP.NET Core Identity implements this check as part of its `SecurityStampValidator`.</span></span> <span data-ttu-id="139ed-156">示例如下所示：</span><span class="sxs-lookup"><span data-stu-id="139ed-156">An example looks like the following:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="139ed-157">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="139ed-157">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
public static class LastChangedValidator
{
    public static async Task ValidateAsync(CookieValidatePrincipalContext context)
    {
        // Pull database from registered DI services.
        var userRepository = context.HttpContext.RequestServices.GetRequiredService<IUserRepository>();
        var userPrincipal = context.Principal;

        // Look for the last changed claim.
        string lastChanged;
        lastChanged = (from c in userPrincipal.Claims
                        where c.Type == "LastUpdated"
                        select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !userRepository.ValidateLastChanged(userPrincipal, lastChanged))
        {
            context.RejectPrincipal();
            await context.HttpContext.SignOutAsync("MyCookieAuthenticationScheme");
        }
    }
}
```

<span data-ttu-id="139ed-158">这将然后向上在过程中连接 cookie 中的服务注册`ConfigureServices`方法*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="139ed-158">This would then be wired up during cookie service registration in the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication("MyCookieAuthenticationScheme")
        .AddCookie(options =>
        {
            options.Events = new CookieAuthenticationEvents
            {
                OnValidatePrincipal = LastChangedValidator.ValidateAsync
            };
        });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="139ed-159">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="139ed-159">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
public static class LastChangedValidator
{
    public static async Task ValidateAsync(CookieValidatePrincipalContext context)
    {
        // Pull database from registered DI services.
        var userRepository = context.HttpContext.RequestServices.GetRequiredService<IUserRepository>();
        var userPrincipal = context.Principal;

        // Look for the last changed claim.
        string lastChanged;
        lastChanged = (from c in userPrincipal.Claims
                        where c.Type == "LastUpdated"
                        select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !userRepository.ValidateLastChanged(userPrincipal, lastChanged))
        {
            context.RejectPrincipal();
            await context.HttpContext.Authentication.SignOutAsync("MyCookieAuthenticationScheme");
        }
    }
}
```

<span data-ttu-id="139ed-160">这将然后向上在过程中连接中的 cookie 的身份验证配置`Configure`方法*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="139ed-160">This would then be wired up during cookie authentication configuration in the `Configure` method of *Startup.cs*:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    Events = new CookieAuthenticationEvents
    {
        OnValidatePrincipal = LastChangedValidator.ValidateAsync
    }
});
```

---

<span data-ttu-id="139ed-161">例如，在其中更新其名称&mdash;决策这不会影响其以任何方式的安全性。</span><span class="sxs-lookup"><span data-stu-id="139ed-161">Consider the example in which their name has been updated &mdash; a decision which doesn't affect security in any way.</span></span> <span data-ttu-id="139ed-162">如果你想要非破坏性地更新的用户主体，则可以调用`context.ReplacePrincipal()`并设置`context.ShouldRenew`属性`true`。</span><span class="sxs-lookup"><span data-stu-id="139ed-162">If you want to non-destructively update the user principal, you can call `context.ReplacePrincipal()` and set the `context.ShouldRenew` property to `true`.</span></span>

<a name="security-authentication-cookie-options"></a>

## <a name="controlling-cookie-options"></a><span data-ttu-id="139ed-163">控制 cookie 选项</span><span class="sxs-lookup"><span data-stu-id="139ed-163">Controlling cookie options</span></span>

<span data-ttu-id="139ed-164">[CookieAuthenticationOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.cookieauthenticationoptions)类附带了各种配置选项，以微调正在创建的 cookie。</span><span class="sxs-lookup"><span data-stu-id="139ed-164">The [CookieAuthenticationOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.cookieauthenticationoptions) class comes with various configuration options to fine-tune the cookies being created.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="139ed-165">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="139ed-165">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="139ed-166">ASP.NET 核心 2.x 统一的 Api，用于配置 cookie。</span><span class="sxs-lookup"><span data-stu-id="139ed-166">ASP.NET Core 2.x unifies the APIs used for configuring cookies.</span></span> <span data-ttu-id="139ed-167">Api 标记为过时，1.x 和新`Cookie`类型的属性`CookieBuilder`中引入了`CookieAuthenticationOptions`类。</span><span class="sxs-lookup"><span data-stu-id="139ed-167">The 1.x APIs have been marked as obsolete, and a new `Cookie` property of type `CookieBuilder` has been introduced in the `CookieAuthenticationOptions` class.</span></span> <span data-ttu-id="139ed-168">建议你迁移到 2.x Api。</span><span class="sxs-lookup"><span data-stu-id="139ed-168">It's recommended that you migrate to the 2.x APIs.</span></span>

* <span data-ttu-id="139ed-169">`ClaimsIssuer`是要用于的颁发者[颁发者](https://docs.microsoft.com/dotnet/api/system.security.claims.claim.issuer)创建 cookie 身份验证的任何声明上的属性。</span><span class="sxs-lookup"><span data-stu-id="139ed-169">`ClaimsIssuer` is the issuer to be used for the [Issuer](https://docs.microsoft.com/dotnet/api/system.security.claims.claim.issuer) property on any claims created by the cookie authentication.</span></span>

* <span data-ttu-id="139ed-170">`CookieBuilder.Domain`是向其提供 cookie 的域名。</span><span class="sxs-lookup"><span data-stu-id="139ed-170">`CookieBuilder.Domain` is the domain name to which the cookie is served.</span></span> <span data-ttu-id="139ed-171">默认情况下，这是请求发送到的主机名。</span><span class="sxs-lookup"><span data-stu-id="139ed-171">By default, this is the host name the request was sent to.</span></span> <span data-ttu-id="139ed-172">浏览器只为服务为匹配的主机名称的 cookie。</span><span class="sxs-lookup"><span data-stu-id="139ed-172">The browser only serves the cookie to a matching host name.</span></span> <span data-ttu-id="139ed-173">你可能希望调整这在你的域中具有可用于任何主机的 cookie。</span><span class="sxs-lookup"><span data-stu-id="139ed-173">You may wish to adjust this to have cookies available to any host in your domain.</span></span> <span data-ttu-id="139ed-174">例如，将 cookie 域设置为`.contoso.com`使其可供`contoso.com`， `www.contoso.com`， `staging.www.contoso.com`，等等。</span><span class="sxs-lookup"><span data-stu-id="139ed-174">For example, setting the cookie domain to `.contoso.com` makes it available to `contoso.com`, `www.contoso.com`, `staging.www.contoso.com`, etc.</span></span>

* <span data-ttu-id="139ed-175">`CookieBuilder.HttpOnly`一个标志指示是否 cookie 应只允许到服务器可访问。</span><span class="sxs-lookup"><span data-stu-id="139ed-175">`CookieBuilder.HttpOnly` is a flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="139ed-176">这将默认为`true`。</span><span class="sxs-lookup"><span data-stu-id="139ed-176">This defaults to `true`.</span></span> <span data-ttu-id="139ed-177">更改此值可能会打开到 cookie 被盗的应用程序应你的应用程序具有跨站点脚本的 bug。</span><span class="sxs-lookup"><span data-stu-id="139ed-177">Changing this value may open your application to cookie theft should your application have a Cross-Site Scripting bug.</span></span>

* <span data-ttu-id="139ed-178">`CookieBuilder.Path`可用来将在相同的主机名上运行的应用程序隔离。</span><span class="sxs-lookup"><span data-stu-id="139ed-178">`CookieBuilder.Path` can be used to isolate applications running on the same host name.</span></span> <span data-ttu-id="139ed-179">如果必须运行的应用程序`/app1`和想要限制为只发送到该应用程序，而发出的 cookie，则应将设置`CookiePath`属性`/app1`。</span><span class="sxs-lookup"><span data-stu-id="139ed-179">If you have an app running in `/app1` and want to limit the cookies issued to just be sent to that application, then you should set the `CookiePath` property to `/app1`.</span></span> <span data-ttu-id="139ed-180">通过这样做，cookie 才可用请求到`/app1`或其下的任何内容。</span><span class="sxs-lookup"><span data-stu-id="139ed-180">By doing so, the cookie is only available to requests to `/app1` or anything underneath it.</span></span>

* <span data-ttu-id="139ed-181">`CookieBuilder.SameSite`该值指示浏览器应允许要附加到同一站点或跨站点请求的 cookie。</span><span class="sxs-lookup"><span data-stu-id="139ed-181">`CookieBuilder.SameSite` indicates whether the browser should allow the cookie to be attached to same-site or cross-site requests.</span></span> <span data-ttu-id="139ed-182">这将默认为`SameSiteMode.Lax`。</span><span class="sxs-lookup"><span data-stu-id="139ed-182">This defaults to `SameSiteMode.Lax`.</span></span>

* <span data-ttu-id="139ed-183">`CookieBuilder.SecurePolicy`一个标志指示是否创建的 cookie 应限于 HTTPS、 HTTP 或 HTTPS 或请求的同一个协议。</span><span class="sxs-lookup"><span data-stu-id="139ed-183">`CookieBuilder.SecurePolicy` is a flag indicating if the cookie created should be limited to HTTPS, HTTP or HTTPS, or the same protocol as the request.</span></span> <span data-ttu-id="139ed-184">这将默认为`SameAsRequest`。</span><span class="sxs-lookup"><span data-stu-id="139ed-184">This defaults to `SameAsRequest`.</span></span>

* <span data-ttu-id="139ed-185">`ExpireTimeSpan`是`TimeSpan`直到 cookie 到期。</span><span class="sxs-lookup"><span data-stu-id="139ed-185">`ExpireTimeSpan` is the `TimeSpan` after which the cookie expires.</span></span> <span data-ttu-id="139ed-186">它将添加到要创建的 cookie 的到期日期的当前日期和时间。</span><span class="sxs-lookup"><span data-stu-id="139ed-186">It's added to the current date and time to create the expiry date for the cookie.</span></span>

* <span data-ttu-id="139ed-187">`SlidingExpiration`是一个标志，指示是否重置时超过一半的 cookie 的到期日期`ExpireTimeSpan`时间间隔。</span><span class="sxs-lookup"><span data-stu-id="139ed-187">`SlidingExpiration` is a flag indicating whether the cookie expiration date resets when more than half of the `ExpireTimeSpan` interval has passed.</span></span> <span data-ttu-id="139ed-188">向前移动在新的到期日期为当前日期加上`ExpireTimespan`。</span><span class="sxs-lookup"><span data-stu-id="139ed-188">The new expiry date is moved forward to be the current date plus the `ExpireTimespan`.</span></span> <span data-ttu-id="139ed-189">[绝对到期时间](xref:security/authentication/cookie#security-authentication-absolute-expiry)可以通过使用设置`AuthenticationProperties`类调用时`SignInAsync`。</span><span class="sxs-lookup"><span data-stu-id="139ed-189">An [absolute expiry time](xref:security/authentication/cookie#security-authentication-absolute-expiry) can be set by using the `AuthenticationProperties` class when calling `SignInAsync`.</span></span> <span data-ttu-id="139ed-190">绝对到期时间可通过限制的身份验证 cookie 的有效时间量来提高你的应用程序的安全性。</span><span class="sxs-lookup"><span data-stu-id="139ed-190">An absolute expiry can improve the security of your application by limiting the amount of time for which the authentication cookie is valid.</span></span>

<span data-ttu-id="139ed-191">使用的示例`CookieAuthenticationOptions`中`ConfigureServices`方法*Startup.cs*遵循：</span><span class="sxs-lookup"><span data-stu-id="139ed-191">An example of using `CookieAuthenticationOptions` in the `ConfigureServices` method of *Startup.cs* follows:</span></span>

```csharp
services.AddAuthentication()
        .AddCookie(options =>
        {
            options.Cookie.Name = "AuthCookie";
            options.Cookie.Domain = "contoso.com";
            options.Cookie.Path = "/";
            options.Cookie.HttpOnly = true;
            options.Cookie.SameSite = SameSiteMode.Lax;
            options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="139ed-192">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="139ed-192">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

* <span data-ttu-id="139ed-193">`ClaimsIssuer`是要用于的颁发者[颁发者](https://docs.microsoft.com/dotnet/api/system.security.claims.claim.issuer)上创建的中间件任何声明的属性。</span><span class="sxs-lookup"><span data-stu-id="139ed-193">`ClaimsIssuer` is the issuer to be used for the [Issuer](https://docs.microsoft.com/dotnet/api/system.security.claims.claim.issuer) property on any claims created by the middleware.</span></span>

* <span data-ttu-id="139ed-194">`CookieDomain`是向其提供 cookie 的域名。</span><span class="sxs-lookup"><span data-stu-id="139ed-194">`CookieDomain` is the domain name to which the cookie is served.</span></span> <span data-ttu-id="139ed-195">默认情况下，这是请求发送到的主机名。</span><span class="sxs-lookup"><span data-stu-id="139ed-195">By default, this is the host name the request was sent to.</span></span> <span data-ttu-id="139ed-196">浏览器只为服务为匹配的主机名称的 cookie。</span><span class="sxs-lookup"><span data-stu-id="139ed-196">The browser only serves the cookie to a matching host name.</span></span> <span data-ttu-id="139ed-197">你可能希望调整这在你的域中具有可用于任何主机的 cookie。</span><span class="sxs-lookup"><span data-stu-id="139ed-197">You may wish to adjust this to have cookies available to any host in your domain.</span></span> <span data-ttu-id="139ed-198">例如，将 cookie 域设置为`.contoso.com`使其可供`contoso.com`， `www.contoso.com`， `staging.www.contoso.com`，等等。</span><span class="sxs-lookup"><span data-stu-id="139ed-198">For example, setting the cookie domain to `.contoso.com` makes it available to `contoso.com`, `www.contoso.com`, `staging.www.contoso.com`, etc.</span></span>

* <span data-ttu-id="139ed-199">`CookieHttpOnly`一个标志指示是否 cookie 应只允许到服务器可访问。</span><span class="sxs-lookup"><span data-stu-id="139ed-199">`CookieHttpOnly` is a flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="139ed-200">这将默认为`true`。</span><span class="sxs-lookup"><span data-stu-id="139ed-200">This defaults to `true`.</span></span> <span data-ttu-id="139ed-201">更改此值可能会打开到 cookie 被盗的应用程序应你的应用程序具有跨站点脚本的 bug。</span><span class="sxs-lookup"><span data-stu-id="139ed-201">Changing this value may open your application to cookie theft should your application have a Cross-Site Scripting bug.</span></span>

* <span data-ttu-id="139ed-202">`CookiePath`可用来将在相同的主机名上运行的应用程序隔离。</span><span class="sxs-lookup"><span data-stu-id="139ed-202">`CookiePath` can be used to isolate applications running on the same host name.</span></span> <span data-ttu-id="139ed-203">如果必须运行的应用程序`/app1`和想要限制为只发送到该应用程序，而发出的 cookie，则应将设置`CookiePath`属性`/app1`。</span><span class="sxs-lookup"><span data-stu-id="139ed-203">If you have an app running in `/app1` and want to limit the cookies issued to just be sent to that application, then you should set the `CookiePath` property to `/app1`.</span></span> <span data-ttu-id="139ed-204">通过这样做，cookie 才可用请求到`/app1`或其下的任何内容。</span><span class="sxs-lookup"><span data-stu-id="139ed-204">By doing so, the cookie is only available to requests to `/app1` or anything underneath it.</span></span>

* <span data-ttu-id="139ed-205">`CookieSecure`一个标志指示是否创建的 cookie 应限于 HTTPS、 HTTP 或 HTTPS 或请求的同一个协议。</span><span class="sxs-lookup"><span data-stu-id="139ed-205">`CookieSecure` is a flag indicating if the cookie created should be limited to HTTPS, HTTP or HTTPS, or the same protocol as the request.</span></span> <span data-ttu-id="139ed-206">这将默认为`SameAsRequest`。</span><span class="sxs-lookup"><span data-stu-id="139ed-206">This defaults to `SameAsRequest`.</span></span>

* <span data-ttu-id="139ed-207">`ExpireTimeSpan`是`TimeSpan`直到 cookie 到期。</span><span class="sxs-lookup"><span data-stu-id="139ed-207">`ExpireTimeSpan` is the `TimeSpan` after which the cookie expires.</span></span> <span data-ttu-id="139ed-208">它将添加到要创建的 cookie 的到期日期的当前日期和时间。</span><span class="sxs-lookup"><span data-stu-id="139ed-208">It's added to the current date and time to create the expiry date for the cookie.</span></span>

* <span data-ttu-id="139ed-209">`SlidingExpiration`是一个标志，指示是否重置时超过一半的 cookie 的到期日期`ExpireTimeSpan`时间间隔。</span><span class="sxs-lookup"><span data-stu-id="139ed-209">`SlidingExpiration` is a flag indicating whether the cookie expiration date resets when more than half of the `ExpireTimeSpan` interval has passed.</span></span> <span data-ttu-id="139ed-210">向前移动在新的到期日期为当前日期加上`ExpireTimespan`。</span><span class="sxs-lookup"><span data-stu-id="139ed-210">The new expiry date is moved forward to be the current date plus the `ExpireTimespan`.</span></span> <span data-ttu-id="139ed-211">[绝对到期时间](xref:security/authentication/cookie#security-authentication-absolute-expiry)可以通过使用设置`AuthenticationProperties`类调用时`SignInAsync`。</span><span class="sxs-lookup"><span data-stu-id="139ed-211">An [absolute expiry time](xref:security/authentication/cookie#security-authentication-absolute-expiry) can be set by using the `AuthenticationProperties` class when calling `SignInAsync`.</span></span> <span data-ttu-id="139ed-212">绝对到期时间可通过限制的身份验证 cookie 的有效时间量来提高你的应用程序的安全性。</span><span class="sxs-lookup"><span data-stu-id="139ed-212">An absolute expiry can improve the security of your application by limiting the amount of time for which the authentication cookie is valid.</span></span>

<span data-ttu-id="139ed-213">使用的示例`CookieAuthenticationOptions`中`Configure`方法*Startup.cs*遵循：</span><span class="sxs-lookup"><span data-stu-id="139ed-213">An example of using `CookieAuthenticationOptions` in the `Configure` method of *Startup.cs* follows:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    CookieName = "AuthCookie",
    CookieDomain = "contoso.com",
    CookiePath = "/",
    CookieHttpOnly = true,
    CookieSecure = CookieSecurePolicy.Always
});
```

---

## <a name="persistent-cookies-and-absolute-expiry-times"></a><span data-ttu-id="139ed-214">永久 cookie 和绝对到期时间</span><span class="sxs-lookup"><span data-stu-id="139ed-214">Persistent cookies and absolute expiry times</span></span>

<span data-ttu-id="139ed-215">你可能希望在浏览器会话之间保持并希望身份识别和将其传输的 cookie 的绝对过期时间的 cookie 到期时间。</span><span class="sxs-lookup"><span data-stu-id="139ed-215">You may want the cookie expiry to persist across browser sessions and want an absolute expiry to the identity and the cookie transporting it.</span></span> <span data-ttu-id="139ed-216">此持久性仅应使用显式用户同意的情况下，通过"记住我"复选框上登录名或类似机制来启用。</span><span class="sxs-lookup"><span data-stu-id="139ed-216">This persistence should only be enabled with explicit user consent, via a "Remember Me" checkbox on login or a similar mechanism.</span></span> <span data-ttu-id="139ed-217">你可以通过执行以下操作`AuthenticationProperties`参数`SignInAsync`时，调用方法[签名标识中和创建 cookie](xref:security/authentication/cookie#security-authentication-cookie-middleware-creating-a-cookie)。</span><span class="sxs-lookup"><span data-stu-id="139ed-217">You can do these things by using the `AuthenticationProperties` parameter on the `SignInAsync` method called when [signing in an identity and creating the cookie](xref:security/authentication/cookie#security-authentication-cookie-middleware-creating-a-cookie).</span></span> <span data-ttu-id="139ed-218">例如: </span><span class="sxs-lookup"><span data-stu-id="139ed-218">For example:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="139ed-219">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="139ed-219">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
await HttpContext.SignInAsync(
    "MyCookieAuthenticationScheme",
    principal,
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

<span data-ttu-id="139ed-220">`AuthenticationProperties`类，在前面的代码段中，使用驻留在`Microsoft.AspNetCore.Authentication`命名空间。</span><span class="sxs-lookup"><span data-stu-id="139ed-220">The `AuthenticationProperties` class, used in the preceding code snippet, resides in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="139ed-221">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="139ed-221">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignInAsync(
    "MyCookieAuthenticationScheme",
    principal,
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

<span data-ttu-id="139ed-222">`AuthenticationProperties`类，在前面的代码段中，使用驻留在`Microsoft.AspNetCore.Http.Authentication`命名空间。</span><span class="sxs-lookup"><span data-stu-id="139ed-222">The `AuthenticationProperties` class, used in the preceding code snippet, resides in the `Microsoft.AspNetCore.Http.Authentication` namespace.</span></span>

---

<span data-ttu-id="139ed-223">前面的代码段将创建的标识和相应的 cookie，这可以通过浏览器闭包。</span><span class="sxs-lookup"><span data-stu-id="139ed-223">The preceding code snippet creates an identity and corresponding cookie which survives through browser closures.</span></span> <span data-ttu-id="139ed-224">之前通过配置任何滑动过期设置[cookie 选项](xref:security/authentication/cookie#security-authentication-cookie-options)将仍然起作用。</span><span class="sxs-lookup"><span data-stu-id="139ed-224">Any sliding expiration settings previously configured via [cookie options](xref:security/authentication/cookie#security-authentication-cookie-options) are still honored.</span></span> <span data-ttu-id="139ed-225">如果 cookie 过期同时关闭浏览器，浏览器它重新启动后清除它。</span><span class="sxs-lookup"><span data-stu-id="139ed-225">If the cookie expires whilst the browser is closed, the browser clears it once it is restarted.</span></span>

<a name="security-authentication-absolute-expiry"></a>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="139ed-226">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="139ed-226">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
await HttpContext.SignInAsync(
    "MyCookieAuthenticationScheme",
    principal,
    new AuthenticationProperties
    {
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="139ed-227">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="139ed-227">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignInAsync(
    "MyCookieAuthenticationScheme",
    principal,
    new AuthenticationProperties
    {
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

---

<span data-ttu-id="139ed-228">前面的代码段将创建的标识和相应的 cookie 即持续 20 分钟。</span><span class="sxs-lookup"><span data-stu-id="139ed-228">The preceding code snippet creates an identity and corresponding cookie which lasts for 20 minutes.</span></span> <span data-ttu-id="139ed-229">这将忽略之前通过配置任何滑动过期设置[cookie 选项](xref:security/authentication/cookie#security-authentication-cookie-options)。</span><span class="sxs-lookup"><span data-stu-id="139ed-229">This ignores any sliding expiration settings previously configured via [cookie options](xref:security/authentication/cookie#security-authentication-cookie-options).</span></span>

<span data-ttu-id="139ed-230">`ExpiresUtc`和`IsPersistent`属性是互相排斥。</span><span class="sxs-lookup"><span data-stu-id="139ed-230">The `ExpiresUtc` and `IsPersistent` properties are mutually exclusive.</span></span>
