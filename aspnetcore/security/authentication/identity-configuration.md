---
title: "配置 ASP.NET 核心标识"
author: AdrienTorris
description: "了解 ASP.NET 核心标识默认值，并配置要使用自定义值的各种标识属性。"
keywords: "ASP.NET 核心，标识、 身份验证安全性"
ms.author: scaddie
manager: wpickett
ms.date: 09/18/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/identity-configuration
ms.openlocfilehash: adf577ae1e1c752c3b1a332ec94a7a7627a7a4b4
ms.sourcegitcommit: 76d42f09f3e0dd2f2105493eca6b29994aa47706
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2017
---
# <a name="configure-identity"></a><span data-ttu-id="3b586-104">配置标识</span><span class="sxs-lookup"><span data-stu-id="3b586-104">Configure Identity</span></span>

<span data-ttu-id="3b586-105">ASP.NET 核心标识具有一些可以在你的应用程序中轻松地重写的默认行为`Startup`类。</span><span class="sxs-lookup"><span data-stu-id="3b586-105">ASP.NET Core Identity has some default behaviors that you can override easily in your application's `Startup` class.</span></span>

## <a name="passwords-policy"></a><span data-ttu-id="3b586-106">密码策略</span><span class="sxs-lookup"><span data-stu-id="3b586-106">Passwords policy</span></span>

<span data-ttu-id="3b586-107">默认情况下，标识要求密码包含大写字符、 小写字符、 数字和字母数字字符。</span><span class="sxs-lookup"><span data-stu-id="3b586-107">By default, Identity requires that passwords contain an uppercase character, lowercase character, a digit, and an alphanumeric character.</span></span> <span data-ttu-id="3b586-108">此外，还有一些其他限制。</span><span class="sxs-lookup"><span data-stu-id="3b586-108">There are also some other restrictions.</span></span> <span data-ttu-id="3b586-109">如果你想要简化密码限制，可以执行该操作`Startup`的你的应用程序的类。</span><span class="sxs-lookup"><span data-stu-id="3b586-109">If you want to simplify password restrictions, you can do that in the `Startup` class of your application.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="3b586-110">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="3b586-110">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="3b586-111">ASP.NET 核心 2.0 增加`RequiredUniqueChars`属性。</span><span class="sxs-lookup"><span data-stu-id="3b586-111">ASP.NET Core 2.0 added the `RequiredUniqueChars` property.</span></span> <span data-ttu-id="3b586-112">否则，选项是从 ASP.NET Core 相同 1.x。</span><span class="sxs-lookup"><span data-stu-id="3b586-112">Otherwise, the options are the same from ASP.NET Core 1.x.</span></span>

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="3b586-113">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="3b586-113">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

---

<span data-ttu-id="3b586-114">`IdentityOptions.Password`具有以下属性：</span><span class="sxs-lookup"><span data-stu-id="3b586-114">`IdentityOptions.Password` has the following properties:</span></span>
* <span data-ttu-id="3b586-115">`RequireDigit`： 需要密码中的 0-9 之间的数字。</span><span class="sxs-lookup"><span data-stu-id="3b586-115">`RequireDigit`: Requires a number between 0-9 in the password.</span></span> <span data-ttu-id="3b586-116">默认值为 true。</span><span class="sxs-lookup"><span data-stu-id="3b586-116">Defaults to true.</span></span>
* <span data-ttu-id="3b586-117">`RequiredLength`： 密码最小长度。</span><span class="sxs-lookup"><span data-stu-id="3b586-117">`RequiredLength`: The minimum length of the password.</span></span> <span data-ttu-id="3b586-118">默认值为 6。</span><span class="sxs-lookup"><span data-stu-id="3b586-118">Defaults to 6.</span></span>
* <span data-ttu-id="3b586-119">`RequireNonAlphanumeric`： 需要密码中的非字母数字字符。</span><span class="sxs-lookup"><span data-stu-id="3b586-119">`RequireNonAlphanumeric`: Requires a non-alphanumeric character in the password.</span></span> <span data-ttu-id="3b586-120">默认值为 true。</span><span class="sxs-lookup"><span data-stu-id="3b586-120">Defaults to true.</span></span>
* <span data-ttu-id="3b586-121">`RequireUppercase`： 需要密码中的大写字符。</span><span class="sxs-lookup"><span data-stu-id="3b586-121">`RequireUppercase`: Requires an upper case character in the password.</span></span> <span data-ttu-id="3b586-122">默认值为 true。</span><span class="sxs-lookup"><span data-stu-id="3b586-122">Defaults to true.</span></span>
* <span data-ttu-id="3b586-123">`RequireLowercase`： 需要密码中的小写字符。</span><span class="sxs-lookup"><span data-stu-id="3b586-123">`RequireLowercase`: Requires a lower case character in the password.</span></span> <span data-ttu-id="3b586-124">默认值为 true。</span><span class="sxs-lookup"><span data-stu-id="3b586-124">Defaults to true.</span></span>
* <span data-ttu-id="3b586-125">`RequiredUniqueChars`： 需要密码中的非重复字符的数。</span><span class="sxs-lookup"><span data-stu-id="3b586-125">`RequiredUniqueChars`: Requires the number of distinct characters in the password.</span></span> <span data-ttu-id="3b586-126">默认值为 1。</span><span class="sxs-lookup"><span data-stu-id="3b586-126">Defaults to 1.</span></span>


## <a name="users-lockout"></a><span data-ttu-id="3b586-127">用户的锁定</span><span class="sxs-lookup"><span data-stu-id="3b586-127">User's lockout</span></span>

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,39-42,50-52)]

<span data-ttu-id="3b586-128">`IdentityOptions.Lockout`具有以下属性：</span><span class="sxs-lookup"><span data-stu-id="3b586-128">`IdentityOptions.Lockout` has the following properties:</span></span>
* <span data-ttu-id="3b586-129">`DefaultLockoutTimeSpan`： 的时间量锁定用户锁定发生时。</span><span class="sxs-lookup"><span data-stu-id="3b586-129">`DefaultLockoutTimeSpan`: The amount of time a user is locked out when a lockout occurs.</span></span> <span data-ttu-id="3b586-130">默认值为 5 分钟。</span><span class="sxs-lookup"><span data-stu-id="3b586-130">Defaults to 5 minutes.</span></span>
* <span data-ttu-id="3b586-131">`MaxFailedAccessAttempts`: 失败的访问尝试次数之前用户已被锁定，如果启用了锁定。</span><span class="sxs-lookup"><span data-stu-id="3b586-131">`MaxFailedAccessAttempts`: The number of failed access attempts until a user is locked out, if lockout is enabled.</span></span> <span data-ttu-id="3b586-132">默认值为 5。</span><span class="sxs-lookup"><span data-stu-id="3b586-132">Defaults to 5.</span></span>
* <span data-ttu-id="3b586-133">`AllowedForNewUsers`： 确定是否新用户会被锁定。默认值为 true。</span><span class="sxs-lookup"><span data-stu-id="3b586-133">`AllowedForNewUsers`: Determines if a new user can be locked out. Defaults to true.</span></span>


## <a name="sign-in-settings"></a><span data-ttu-id="3b586-134">登录设置</span><span class="sxs-lookup"><span data-stu-id="3b586-134">Sign in settings</span></span>

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)]

<span data-ttu-id="3b586-135">`IdentityOptions.SignIn`具有以下属性：</span><span class="sxs-lookup"><span data-stu-id="3b586-135">`IdentityOptions.SignIn` has the following properties:</span></span>
* <span data-ttu-id="3b586-136">`RequireConfirmedEmail`： 需要已确认的电子邮件，以登录。</span><span class="sxs-lookup"><span data-stu-id="3b586-136">`RequireConfirmedEmail`: Requires a confirmed email to sign in.</span></span> <span data-ttu-id="3b586-137">默认值为 false。</span><span class="sxs-lookup"><span data-stu-id="3b586-137">Defaults to false.</span></span>
* <span data-ttu-id="3b586-138">`RequireConfirmedPhoneNumber`： 需要已确认的电话号码登录。</span><span class="sxs-lookup"><span data-stu-id="3b586-138">`RequireConfirmedPhoneNumber`: Requires a confirmed phone number to sign in.</span></span> <span data-ttu-id="3b586-139">默认值为 false。</span><span class="sxs-lookup"><span data-stu-id="3b586-139">Defaults to false.</span></span>


## <a name="user-validation-settings"></a><span data-ttu-id="3b586-140">用户验证设置</span><span class="sxs-lookup"><span data-stu-id="3b586-140">User validation settings</span></span>

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,48-52)]

<span data-ttu-id="3b586-141">`IdentityOptions.User`具有以下属性：</span><span class="sxs-lookup"><span data-stu-id="3b586-141">`IdentityOptions.User` has the following properties:</span></span>
* <span data-ttu-id="3b586-142">`RequireUniqueEmail`： 需要每个用户具有唯一的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="3b586-142">`RequireUniqueEmail`: Requires each User to have a unique email.</span></span> <span data-ttu-id="3b586-143">默认值为 false。</span><span class="sxs-lookup"><span data-stu-id="3b586-143">Defaults to false.</span></span>
* <span data-ttu-id="3b586-144">`AllowedUserNameCharacters`： 允许在用户名中的字符。</span><span class="sxs-lookup"><span data-stu-id="3b586-144">`AllowedUserNameCharacters`: Allowed characters in the username.</span></span> <span data-ttu-id="3b586-145">默认值为 abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789-._@+。</span><span class="sxs-lookup"><span data-stu-id="3b586-145">Defaults to abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789-._@+.</span></span>

## <a name="applications-cookie-settings"></a><span data-ttu-id="3b586-146">应用程序的 cookie 设置</span><span class="sxs-lookup"><span data-stu-id="3b586-146">Application's cookie settings</span></span>

<span data-ttu-id="3b586-147">密码策略，如应用程序的 cookie 的所有设置可以都更改在`Startup`类。</span><span class="sxs-lookup"><span data-stu-id="3b586-147">Like the passwords policy, all the settings of the application's cookie can be changed in the `Startup` class.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="3b586-148">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="3b586-148">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="3b586-149">下`ConfigureServices`中`Startup`类，你可以配置应用程序的 cookie。</span><span class="sxs-lookup"><span data-stu-id="3b586-149">Under `ConfigureServices` in the `Startup` class, you can configure the application's cookie.</span></span>

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="3b586-150">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="3b586-150">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

--- 

<span data-ttu-id="3b586-151">`CookieAuthenticationOptions`具有以下属性：</span><span class="sxs-lookup"><span data-stu-id="3b586-151">`CookieAuthenticationOptions` has the following properties:</span></span>
* <span data-ttu-id="3b586-152">`Cookie.Name`： 的名称的 cookie。</span><span class="sxs-lookup"><span data-stu-id="3b586-152">`Cookie.Name`: The name of the cookie.</span></span> <span data-ttu-id="3b586-153">默认值为。AspNetCore.Cookies。</span><span class="sxs-lookup"><span data-stu-id="3b586-153">Defaults to .AspNetCore.Cookies.</span></span>
* <span data-ttu-id="3b586-154">`Cookie.HttpOnly`： 如果为 true，则 cookie 不能从客户端脚本访问。</span><span class="sxs-lookup"><span data-stu-id="3b586-154">`Cookie.HttpOnly`: When true, the cookie is not accessible from client-side scripts.</span></span> <span data-ttu-id="3b586-155">默认值为 true。</span><span class="sxs-lookup"><span data-stu-id="3b586-155">Defaults to true.</span></span>
* <span data-ttu-id="3b586-156">`ExpireTimeSpan`： 控制在 cookie 中存储身份验证票证的时间就会保持有效从它创建的点。</span><span class="sxs-lookup"><span data-stu-id="3b586-156">`ExpireTimeSpan`: Controls how much time the authentication ticket stored in the cookie will remain valid from the point it is created.</span></span> <span data-ttu-id="3b586-157">默认值为 14 天。</span><span class="sxs-lookup"><span data-stu-id="3b586-157">Defaults to 14 days.</span></span>
* <span data-ttu-id="3b586-158">`LoginPath`： 未经授权用户时，他们将被重定向到登录到此路径。</span><span class="sxs-lookup"><span data-stu-id="3b586-158">`LoginPath`: When a user is unauthorized, they will be redirected to this path to login.</span></span> <span data-ttu-id="3b586-159">默认值为/帐户/登录。</span><span class="sxs-lookup"><span data-stu-id="3b586-159">Defaults to /Account/Login.</span></span>
* <span data-ttu-id="3b586-160">`LogoutPath`： 当用户已注销，则将被重定向到此路径。</span><span class="sxs-lookup"><span data-stu-id="3b586-160">`LogoutPath`: When a user is logged out, they will be redirected to this path.</span></span> <span data-ttu-id="3b586-161">默认值为/帐户/注销。</span><span class="sxs-lookup"><span data-stu-id="3b586-161">Defaults to /Account/Logout.</span></span>
* <span data-ttu-id="3b586-162">`AccessDeniedPath`： 当用户失败时授权检查，则将被重定向到此路径。</span><span class="sxs-lookup"><span data-stu-id="3b586-162">`AccessDeniedPath`: When a user fails an authorization check, they will be redirected to this path.</span></span> <span data-ttu-id="3b586-163">默认值为/帐户/AccessDenied。</span><span class="sxs-lookup"><span data-stu-id="3b586-163">Defaults to /Account/AccessDenied.</span></span>
* <span data-ttu-id="3b586-164">`SlidingExpiration`： 如果为 true，将使用新的过期时间，当前 cookie 时通过到期窗口的多个中间颁发一个新的 cookie。</span><span class="sxs-lookup"><span data-stu-id="3b586-164">`SlidingExpiration`: When true, a new cookie will be issued with a new expiration time when the current cookie is more than halfway through the expiration window.</span></span> <span data-ttu-id="3b586-165">默认值为 true。</span><span class="sxs-lookup"><span data-stu-id="3b586-165">Defaults to true.</span></span>
* <span data-ttu-id="3b586-166">`ReturnUrlParameter`: ReturnUrlParameter 确定其 401 未授权的状态代码更改为 302 重定向到登录名路径时，该中间件会追加查询字符串参数的名称。</span><span class="sxs-lookup"><span data-stu-id="3b586-166">`ReturnUrlParameter`: The ReturnUrlParameter determines the name of the query string parameter which is appended by the middleware when a 401 Unauthorized status code is changed to a 302 redirect onto the login path.</span></span>
* <span data-ttu-id="3b586-167">`AuthenticationScheme`： 这是仅适用于 ASP.NET Core 1.x。</span><span class="sxs-lookup"><span data-stu-id="3b586-167">`AuthenticationScheme`: This is only relevant for ASP.NET Core 1.x.</span></span> <span data-ttu-id="3b586-168">特定的身份验证方案逻辑名称。</span><span class="sxs-lookup"><span data-stu-id="3b586-168">The logical name for a particular authentication scheme.</span></span>
* <span data-ttu-id="3b586-169">`AutomaticAuthenticate`： 此标志才适用于 ASP.NET Core 1.x。</span><span class="sxs-lookup"><span data-stu-id="3b586-169">`AutomaticAuthenticate`: This flag is only relevant for ASP.NET Core 1.x.</span></span> <span data-ttu-id="3b586-170">为 true 时，cookie 身份验证应在每个请求上运行并尝试验证并重新构造它创建的任何序列化的主体。</span><span class="sxs-lookup"><span data-stu-id="3b586-170">When true, cookie authentication should run on every request and attempt to validate and reconstruct any serialized principal it created.</span></span>

