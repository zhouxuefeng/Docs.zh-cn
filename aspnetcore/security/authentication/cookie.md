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
ms.openlocfilehash: ea9c93e34a3242b5b3716404228edb8902baf625
ms.sourcegitcommit: e3b1726cc04e80dc28464c35259edbd3bc39a438
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2017
---
# <a name="using-cookie-authentication-without-aspnet-core-identity"></a>使用 Cookie 而无需 ASP.NET 核心标识的身份验证

<a name="security-authentication-cookie-middleware"></a>

ASP.NET 核心 1.x 提供 cookie[中间件](../../fundamentals/middleware.md#fundamentals-middleware)其用户主体序列化为的加密 cookie，然后，在后续请求中，将验证该 cookie，将重新创建主体，并将它分配给`HttpContext.User`属性. 如果你想要提供自己的登录屏幕和用户数据库，你可以作为独立功能使用 cookie 中间件。

一个重要更改在 ASP.NET Core 2.x 是 cookie 中间件不存在。 相反，`UseAuthentication`方法调用中的`Configure`方法*Startup.cs*添加 AuthenticationMiddleware 将设置`HttpContext.User`属性。

<a name="security-authentication-cookie-middleware-configuring"></a>

## <a name="adding-and-configuring"></a>添加和配置

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

完成以下步骤：

- 如果不使用`Microsoft.AspNetCore.All` [metapackage](xref:fundamentals/metapackage)，安装 2.0 以上版本的`Microsoft.AspNetCore.Authentication.Cookies`项目中的 NuGet 包。

- 调用`UseAuthentication`中的方法`Configure`方法*Startup.cs*文件：

    ```csharp
    app.UseAuthentication();
    ```

- 调用`AddAuthentication`和`AddCookie`中的方法`ConfigureServices`方法*Startup.cs*文件：

    ```csharp
    services.AddAuthentication("MyCookieAuthenticationScheme")
            .AddCookie("MyCookieAuthenticationScheme", options => {
                options.AccessDeniedPath = "/Account/Forbidden/";
                options.LoginPath = "/Account/Unauthorized/";
            });
    ```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

完成以下步骤：

- 安装`Microsoft.AspNetCore.Authentication.Cookies`项目中的 NuGet 包。 此程序包包含 cookie 中间件。

- 添加以下行`Configure`方法在你*Startup.cs*文件，然后`app.UseMvc()`语句：

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

上面的代码段配置部分或全部的以下选项：

* `AccessDeniedPath`-这是请求重当用户尝试访问的资源，但不能通过任何定向到的相对路径[授权策略](xref:security/authorization/policies#security-authorization-policies-based)对该资源。

* `AuthenticationScheme`-这是一个值来识别特定 cookie 身份验证方案。 Cookie 身份验证和需要将应用程序的多个实例时，此设置很有用[限制到一个实例的授权](xref:security/authorization/limitingidentitybyscheme)。

* `AutomaticAuthenticate`-此标志是仅适用于的 ASP.NET Core 1.x。 它表示应在每个请求上运行，尝试验证并重新构造它创建的任何序列化的主体 cookie 身份验证。

* `AutomaticChallenge`-此标志是仅适用于的 ASP.NET Core 1.x。 它指示 1.x cookie 身份验证应重定向到浏览器`LoginPath`或`AccessDeniedPath`时授权失败。

* `LoginPath`-这是该请求将重定向到当用户尝试访问的资源，但未经身份验证的相对路径。

[其他选项](xref:security/authentication/cookie#security-authentication-cookie-options)包括能够设置 cookie 身份验证创建，任何声明的颁发者的身份验证的 cookie 名称下降，cookie 和各种安全属性，该 cookie 的域。 默认情况下，cookie 身份验证将为它创建，如任何 cookie，使用适当的安全选项：
- 此 HttpOnly 标志设置为阻止 cookie 在客户端 JavaScript 中的访问
- 如果请求具有通过 HTTPS 的行程，限制为 HTTPS cookie

<a name="security-authentication-cookie-middleware-creating-a-cookie"></a>

## <a name="creating-an-identity-cookie"></a>创建标识 cookie

若要创建一个保存你的用户信息的 cookie，您必须先构造[ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal)保存你想要在 cookie 进行序列化信息。 一旦合适`ClaimsPrincipal`对象，请调用以下命令，在控制器方法：

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
await HttpContext.SignInAsync("MyCookieAuthenticationScheme", principal);
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignInAsync("MyCookieAuthenticationScheme", principal);
```

---

这将创建一个加密的 cookie，并将其添加到当前响应。 `AuthenticationScheme`期间指定[配置](xref:security/authentication/cookie#security-authentication-cookie-middleware-configuring)时调用，必须使用`SignInAsync`。

事实上，使用的加密是 ASP.NET Core[数据保护](xref:security/data-protection/using-data-protection#security-data-protection-getting-started)系统。 如果您要承载的多台计算机，负载平衡，或使用 web 场，然后，你需要[配置数据保护](xref:security/data-protection/configuration/overview#data-protection-configuring)使用同一密钥链和应用程序标识符。

## <a name="signing-out"></a>注销

若要注销当前用户，并删除其 cookie，调用以下命令，在你的控制器：

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
await HttpContext.SignOutAsync("MyCookieAuthenticationScheme");
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignOutAsync("MyCookieAuthenticationScheme");
```

---

## <a name="reacting-to-back-end-changes"></a>对后端更改的作出反应

>[!WARNING]
> 一旦创建主体 cookie 后，它将成为标识的单个来源。 即使在后端系统中禁用用户，cookie 身份验证不了解，并且用户一直保持登录，只要其 cookie 的有效期。

Cookie 身份验证提供了一系列其选项类中的事件。 `ValidateAsync()`事件可以用于截获和重写的 cookie 身份验证。

请考虑一个可能有"LastChanged"列的后端用户数据库。 为了使 cookie 无效数据库更改时，你应首先，当[创建 cookie](xref:security/authentication/cookie#security-authentication-cookie-middleware-creating-a-cookie)，添加"LastChanged"声明包含的当前值。 当数据库更改时，应更新的"LastChanged"值。

若要实现的替代`ValidateAsync()`事件，你必须编写具有以下签名的方法：

```csharp
Task ValidateAsync(CookieValidatePrincipalContext context);
```

ASP.NET 核心标识作为的一部分实现这一检查其`SecurityStampValidator`。 示例如下所示：

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

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

这将然后向上在过程中连接 cookie 中的服务注册`ConfigureServices`方法*Startup.cs*:

```csharp
services.AddAuthentication("MyCookieAuthenticationScheme")
        .AddCookie("MyCookieAuthenticationScheme", options =>
        {
            options.Events = new CookieAuthenticationEvents
            {
                OnValidatePrincipal = LastChangedValidator.ValidateAsync
            };
        });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

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

这将然后向上在过程中连接中的 cookie 的身份验证配置`Configure`方法*Startup.cs*:

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

例如，在其中更新其名称&mdash;决策这不会影响其以任何方式的安全性。 如果你想要非破坏性地更新的用户主体，则可以调用`context.ReplacePrincipal()`并设置`context.ShouldRenew`属性`true`。

<a name="security-authentication-cookie-options"></a>

## <a name="controlling-cookie-options"></a>控制 cookie 选项

[CookieAuthenticationOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.cookieauthenticationoptions)类附带了各种配置选项，以微调正在创建的 cookie。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

ASP.NET 核心 2.x 统一的 Api，用于配置 cookie。 Api 标记为过时，1.x 和新`Cookie`类型的属性`CookieBuilder`中引入了`CookieAuthenticationOptions`类。 建议你迁移到 2.x Api。

* `ClaimsIssuer`是要用于的颁发者[颁发者](https://docs.microsoft.com/dotnet/api/system.security.claims.claim.issuer)创建 cookie 身份验证的任何声明上的属性。

* `CookieBuilder.Domain`是向其提供 cookie 的域名。 默认情况下，这是请求发送到的主机名。 浏览器只为服务为匹配的主机名称的 cookie。 你可能希望调整这在你的域中具有可用于任何主机的 cookie。 例如，将 cookie 域设置为`.contoso.com`使其可供`contoso.com`， `www.contoso.com`， `staging.www.contoso.com`，等等。

* `CookieBuilder.HttpOnly`一个标志指示是否 cookie 应只允许到服务器可访问。 这将默认为`true`。 更改此值可能会打开到 cookie 被盗的应用程序应你的应用程序具有跨站点脚本的 bug。

* `CookieBuilder.Path`可用来将在相同的主机名上运行的应用程序隔离。 如果必须运行的应用程序`/app1`和想要限制为只发送到该应用程序，而发出的 cookie，则应将设置`CookiePath`属性`/app1`。 通过这样做，cookie 才可用请求到`/app1`或其下的任何内容。

* `CookieBuilder.SameSite`该值指示浏览器应允许要附加到同一站点或跨站点请求的 cookie。 这将默认为`SameSiteMode.Lax`。

* `CookieBuilder.SecurePolicy`一个标志指示是否创建的 cookie 应限于 HTTPS、 HTTP 或 HTTPS 或请求的同一个协议。 这将默认为`SameAsRequest`。

* `ExpireTimeSpan`是`TimeSpan`直到 cookie 到期。 它将添加到要创建的 cookie 的到期日期的当前日期和时间。

* `SlidingExpiration`是一个标志，指示是否重置时超过一半的 cookie 的到期日期`ExpireTimeSpan`时间间隔。 向前移动在新的到期日期为当前日期加上`ExpireTimespan`。 [绝对到期时间](xref:security/authentication/cookie#security-authentication-absolute-expiry)可以通过使用设置`AuthenticationProperties`类调用时`SignInAsync`。 绝对到期时间可通过限制的身份验证 cookie 的有效时间量来提高你的应用程序的安全性。

使用的示例`CookieAuthenticationOptions`中`ConfigureServices`方法*Startup.cs*遵循：

```csharp
services.AddAuthentication("MyCookieAuthenticationScheme")
        .AddCookie("MyCookieAuthenticationScheme", options =>
        {
            options.Cookie.Name = "AuthCookie";
            options.Cookie.Domain = "contoso.com";
            options.Cookie.Path = "/";
            options.Cookie.HttpOnly = true;
            options.Cookie.SameSite = SameSiteMode.Lax;
            options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

* `ClaimsIssuer`是要用于的颁发者[颁发者](https://docs.microsoft.com/dotnet/api/system.security.claims.claim.issuer)上创建的中间件任何声明的属性。

* `CookieDomain`是向其提供 cookie 的域名。 默认情况下，这是请求发送到的主机名。 浏览器只为服务为匹配的主机名称的 cookie。 你可能希望调整这在你的域中具有可用于任何主机的 cookie。 例如，将 cookie 域设置为`.contoso.com`使其可供`contoso.com`， `www.contoso.com`， `staging.www.contoso.com`，等等。

* `CookieHttpOnly`一个标志指示是否 cookie 应只允许到服务器可访问。 这将默认为`true`。 更改此值可能会打开到 cookie 被盗的应用程序应你的应用程序具有跨站点脚本的 bug。

* `CookiePath`可用来将在相同的主机名上运行的应用程序隔离。 如果必须运行的应用程序`/app1`和想要限制为只发送到该应用程序，而发出的 cookie，则应将设置`CookiePath`属性`/app1`。 通过这样做，cookie 才可用请求到`/app1`或其下的任何内容。

* `CookieSecure`一个标志指示是否创建的 cookie 应限于 HTTPS、 HTTP 或 HTTPS 或请求的同一个协议。 这将默认为`SameAsRequest`。

* `ExpireTimeSpan`是`TimeSpan`直到 cookie 到期。 它将添加到要创建的 cookie 的到期日期的当前日期和时间。

* `SlidingExpiration`是一个标志，指示是否重置时超过一半的 cookie 的到期日期`ExpireTimeSpan`时间间隔。 向前移动在新的到期日期为当前日期加上`ExpireTimespan`。 [绝对到期时间](xref:security/authentication/cookie#security-authentication-absolute-expiry)可以通过使用设置`AuthenticationProperties`类调用时`SignInAsync`。 绝对到期时间可通过限制的身份验证 cookie 的有效时间量来提高你的应用程序的安全性。

使用的示例`CookieAuthenticationOptions`中`Configure`方法*Startup.cs*遵循：

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

## <a name="persistent-cookies-and-absolute-expiry-times"></a>永久 cookie 和绝对到期时间

你可能希望在浏览器会话之间保持并希望身份识别和将其传输的 cookie 的绝对过期时间的 cookie 到期时间。 此持久性仅应使用显式用户同意的情况下，通过"记住我"复选框上登录名或类似机制来启用。 你可以通过执行以下操作`AuthenticationProperties`参数`SignInAsync`时，调用方法[签名标识中和创建 cookie](xref:security/authentication/cookie#security-authentication-cookie-middleware-creating-a-cookie)。 例如: 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
await HttpContext.SignInAsync(
    "MyCookieAuthenticationScheme",
    principal,
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

`AuthenticationProperties`类，在前面的代码段中，使用驻留在`Microsoft.AspNetCore.Authentication`命名空间。

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignInAsync(
    "MyCookieAuthenticationScheme",
    principal,
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

`AuthenticationProperties`类，在前面的代码段中，使用驻留在`Microsoft.AspNetCore.Http.Authentication`命名空间。

---

前面的代码段将创建的标识和相应的 cookie，这可以通过浏览器闭包。 之前通过配置任何滑动过期设置[cookie 选项](xref:security/authentication/cookie#security-authentication-cookie-options)将仍然起作用。 如果 cookie 过期同时关闭浏览器，浏览器它重新启动后清除它。

<a name="security-authentication-absolute-expiry"></a>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
await HttpContext.SignInAsync(
    "MyCookieAuthenticationScheme",
    principal,
    new AuthenticationProperties
    {
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

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

前面的代码段将创建的标识和相应的 cookie 即持续 20 分钟。 这将忽略之前通过配置任何滑动过期设置[cookie 选项](xref:security/authentication/cookie#security-authentication-cookie-options)。

`ExpiresUtc`和`IsPersistent`属性是互相排斥。
