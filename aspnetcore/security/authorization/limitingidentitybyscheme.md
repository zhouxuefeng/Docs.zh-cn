---
title: "具有特定的方案-授权 ASP.NET 核心"
author: rick-anderson
description: "此文章介绍了如何使用多个身份验证方法时限制为特定方案的标识。"
keywords: "ASP.NET 核心，标识、 身份验证方案"
ms.author: riande
manager: wpickett
ms.date: 10/12/2017
ms.topic: article
ms.assetid: d3d6ca1b-b4b5-4bf7-898e-dcd90ec1bf8c
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: cf3259f206b8d970cc6f2b0b9e52e233c30d6df3
ms.sourcegitcommit: e3b1726cc04e80dc28464c35259edbd3bc39a438
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2017
---
# <a name="authorize-with-a-specific-scheme"></a><span data-ttu-id="6344e-104">具有特定方案授权</span><span class="sxs-lookup"><span data-stu-id="6344e-104">Authorize with a specific scheme</span></span>

<span data-ttu-id="6344e-105">在某些情况下，如单页面应用程序 (Spa) 很常见的是使用多个身份验证方法。</span><span class="sxs-lookup"><span data-stu-id="6344e-105">In some scenarios, such as Single Page Applications (SPAs), it's common to use multiple authentication methods.</span></span> <span data-ttu-id="6344e-106">例如，应用可能会使用基于 cookie 的身份验证登录和 JWT 持有者身份验证的 JavaScript 请求。</span><span class="sxs-lookup"><span data-stu-id="6344e-106">For example, the app may use cookie-based authentication to log in and JWT bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="6344e-107">在某些情况下，应用程序可能具有身份验证处理程序的多个的实例。</span><span class="sxs-lookup"><span data-stu-id="6344e-107">In some cases, the app may have multiple instances of an authentication handler.</span></span> <span data-ttu-id="6344e-108">例如，其中一个包含基本标识的两个 cookie 处理程序，另一个时创建已触发多因素身份验证 (MFA)。</span><span class="sxs-lookup"><span data-stu-id="6344e-108">For example, two cookie handlers where one contains a basic identity and one is created when a multi-factor authentication (MFA) has been triggered.</span></span> <span data-ttu-id="6344e-109">用户请求要求额外的安全的操作，因此，可能会触发 MFA。</span><span class="sxs-lookup"><span data-stu-id="6344e-109">MFA may be triggered because the user requested an operation that requires extra security.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="6344e-110">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="6344e-110">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="6344e-111">当在身份验证过程中配置身份验证服务，名为身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="6344e-111">An authentication scheme is named when the authentication service is configured during authentication.</span></span> <span data-ttu-id="6344e-112">例如: </span><span class="sxs-lookup"><span data-stu-id="6344e-112">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .AddCookie(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

<span data-ttu-id="6344e-113">已在前面的代码中，添加两个身份验证处理程序： 一个用于 cookie，一个用于持有者。</span><span class="sxs-lookup"><span data-stu-id="6344e-113">In the preceding code, two authentication handlers have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="6344e-114">指定的默认方案会导致`HttpContext.User`属性设置为该标识。</span><span class="sxs-lookup"><span data-stu-id="6344e-114">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="6344e-115">如果不需要该行为，禁用调用的无参数形式`AddAuthentication`。</span><span class="sxs-lookup"><span data-stu-id="6344e-115">If that behavior isn't desired, disable it by invoking the parameterless form of `AddAuthentication`.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="6344e-116">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="6344e-116">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="6344e-117">身份验证方案进行命名时在身份验证过程配置身份验证中间件。</span><span class="sxs-lookup"><span data-stu-id="6344e-117">Authentication schemes are named when authentication middlewares are configured during authentication.</span></span> <span data-ttu-id="6344e-118">例如: </span><span class="sxs-lookup"><span data-stu-id="6344e-118">For example:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
    // Code omitted for brevity

    app.UseCookieAuthentication(new CookieAuthenticationOptions()
    {
        AuthenticationScheme = "Cookie",
        LoginPath = "/Account/Unauthorized/",
        AccessDeniedPath = "/Account/Forbidden/",
        AutomaticAuthenticate = false
    });
    
    app.UseJwtBearerAuthentication(new JwtBearerOptions()
    {
        AuthenticationScheme = "Bearer",
        AutomaticAuthenticate = false,
        Audience = "http://localhost:5001/",
        Authority = "http://localhost:5000/",
        RequireHttpsMetadata = false
    });
```

<span data-ttu-id="6344e-119">已在前面的代码中，添加两个身份验证中间件： 一个用于 cookie，一个用于持有者。</span><span class="sxs-lookup"><span data-stu-id="6344e-119">In the preceding code, two authentication middlewares have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="6344e-120">指定的默认方案会导致`HttpContext.User`属性设置为该标识。</span><span class="sxs-lookup"><span data-stu-id="6344e-120">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="6344e-121">如果不需要该行为，禁用设置`AuthenticationOptions.AutomaticAuthenticate`属性`false`。</span><span class="sxs-lookup"><span data-stu-id="6344e-121">If that behavior isn't desired, disable it by setting the `AuthenticationOptions.AutomaticAuthenticate` property to `false`.</span></span>

---

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="6344e-122">选择带有 Authorize 属性的方案</span><span class="sxs-lookup"><span data-stu-id="6344e-122">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="6344e-123">在授权，终端应用指示要使用的处理程序。</span><span class="sxs-lookup"><span data-stu-id="6344e-123">At the point of authorization, the app indicates the handler to be used.</span></span> <span data-ttu-id="6344e-124">选择与应用程序将授权通过传递到身份验证方案的以逗号分隔列表的处理程序`[Authorize]`。</span><span class="sxs-lookup"><span data-stu-id="6344e-124">Select the handler with which the app will authorize by passing a comma-delimited list of authentication schemes to `[Authorize]`.</span></span> <span data-ttu-id="6344e-125">`[Authorize]`属性指定的身份验证方案或方案，可使用无论配置默认值。</span><span class="sxs-lookup"><span data-stu-id="6344e-125">The `[Authorize]` attribute specifies the authentication scheme or schemes to use regardless of whether a default is configured.</span></span> <span data-ttu-id="6344e-126">例如: </span><span class="sxs-lookup"><span data-stu-id="6344e-126">For example:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="6344e-127">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="6344e-127">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
[Authorize(AuthenticationSchemes = "Cookie,Bearer")]
public class MixedController : Controller
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="6344e-128">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="6344e-128">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
[Authorize(ActiveAuthenticationSchemes = "Cookie,Bearer")]
public class MixedController : Controller
```

---

<span data-ttu-id="6344e-129">在前面的示例中，cookie 和持有者处理程序运行，并有机会在创建并追加当前用户的标识。</span><span class="sxs-lookup"><span data-stu-id="6344e-129">In the preceding example, both the cookie and bearer handlers run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="6344e-130">通过指定一种方案，相应的处理程序运行。</span><span class="sxs-lookup"><span data-stu-id="6344e-130">By specifying a single scheme only, the corresponding handler runs.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="6344e-131">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="6344e-131">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
[Authorize(AuthenticationSchemes = "Bearer")]
public class MixedController : Controller
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="6344e-132">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="6344e-132">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
[Authorize(ActiveAuthenticationSchemes = "Bearer")]
public class MixedController : Controller
```

---

<span data-ttu-id="6344e-133">在前面的代码中，仅具有"Bearer"方案处理程序运行。</span><span class="sxs-lookup"><span data-stu-id="6344e-133">In the preceding code, only the handler with the "Bearer" scheme runs.</span></span> <span data-ttu-id="6344e-134">任何基于 cookie 的标识将被忽略。</span><span class="sxs-lookup"><span data-stu-id="6344e-134">Any cookie-based identities are ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="6344e-135">选择使用策略的方案</span><span class="sxs-lookup"><span data-stu-id="6344e-135">Selecting the scheme with policies</span></span>

<span data-ttu-id="6344e-136">如果想要指定在所需的架构[策略](xref:security/authorization/policies#security-authorization-policies-based)，你可以设置`AuthenticationSchemes`集合添加你的策略时：</span><span class="sxs-lookup"><span data-stu-id="6344e-136">If you prefer to specify the desired schemes in [policy](xref:security/authorization/policies#security-authorization-policies-based), you can set the `AuthenticationSchemes` collection when adding your policy:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
    {
        policy.AuthenticationSchemes.Add("Bearer");
        policy.RequireAuthenticatedUser();
        policy.Requirements.Add(new MinimumAgeRequirement());
    });
});
```

<span data-ttu-id="6344e-137">在前面的示例中，"Over18"策略仅运行针对"Bearer"处理程序创建的标识。</span><span class="sxs-lookup"><span data-stu-id="6344e-137">In the preceding example, the "Over18" policy only runs against the identity created by the "Bearer" handler.</span></span> <span data-ttu-id="6344e-138">通过设置来使用策略`[Authorize]`特性的`Policy`属性：</span><span class="sxs-lookup"><span data-stu-id="6344e-138">Use the policy by setting the `[Authorize]` attribute's `Policy` property:</span></span>

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```
