---
title: "限制标识方案"
author: rick-anderson
description: 
keywords: "ASP.NET 核心"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: d3d6ca1b-b4b5-4bf7-898e-dcd90ec1bf8c
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 2483c441da317a5c29b611b3a4910eae3c01fd7a
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="limiting-identity-by-scheme"></a><span data-ttu-id="80cab-103">限制标识方案</span><span class="sxs-lookup"><span data-stu-id="80cab-103">Limiting identity by scheme</span></span>

<a name=security-authorization-limiting-by-scheme></a>

<span data-ttu-id="80cab-104">在某些情况下，如单页面应用程序就可以得到多个身份验证方法。</span><span class="sxs-lookup"><span data-stu-id="80cab-104">In some scenarios, such as Single Page Applications it is possible to end up with multiple authentication methods.</span></span> <span data-ttu-id="80cab-105">例如，你的应用程序可以将基于 cookie 的身份验证登录和持有者身份验证用于 JavaScript 请求。</span><span class="sxs-lookup"><span data-stu-id="80cab-105">For example, your application may use cookie-based authentication to log in and bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="80cab-106">在某些情况下可能具有多个实例的身份验证中间件。</span><span class="sxs-lookup"><span data-stu-id="80cab-106">In some cases you may have multiple instances of an authentication middleware.</span></span> <span data-ttu-id="80cab-107">例如，两个 cookie 中间件其中一个包含一个基本的标识，因为需要额外的安全的操作，则用户要求具有触发多因素身份验证时将创建一个。</span><span class="sxs-lookup"><span data-stu-id="80cab-107">For example, two cookie middlewares where one contains a basic identity and one is created when a multi-factor authentication has triggered because the user requested an operation that requires extra security.</span></span>

<span data-ttu-id="80cab-108">当身份验证中间件过程中配置身份验证，例如命名身份验证方案</span><span class="sxs-lookup"><span data-stu-id="80cab-108">Authentication schemes are named when authentication middleware is configured during authentication, for example</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions()
{
    AuthenticationScheme = "Cookie",
    LoginPath = new PathString("/Account/Unauthorized/"),
    AccessDeniedPath = new PathString("/Account/Forbidden/"),
    AutomaticAuthenticate = false
});

app.UseBearerAuthentication(options =>
{
    options.AuthenticationScheme = "Bearer";
    options.AutomaticAuthenticate = false;
});
```

<span data-ttu-id="80cab-109">在此配置中两个身份验证中间件已添加，一个 cookie，一个用于持有者。</span><span class="sxs-lookup"><span data-stu-id="80cab-109">In this configuration two authentication middlewares have been added, one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="80cab-110">添加多个身份验证中间件时你应确保任何中间件配置为自动运行。</span><span class="sxs-lookup"><span data-stu-id="80cab-110">When adding multiple authentication middleware you should ensure that no middleware is configured to run automatically.</span></span> <span data-ttu-id="80cab-111">执行此操作通过设置`AutomaticAuthenticate`选项属性设置为 false。</span><span class="sxs-lookup"><span data-stu-id="80cab-111">You do this by setting the `AutomaticAuthenticate` options property to false.</span></span> <span data-ttu-id="80cab-112">如果您不能执行此筛选由方案将不起作用。</span><span class="sxs-lookup"><span data-stu-id="80cab-112">If you fail to do this filtering by scheme will not work.</span></span>

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="80cab-113">选择带有 Authorize 属性的方案</span><span class="sxs-lookup"><span data-stu-id="80cab-113">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="80cab-114">因为没有身份验证中间件配置为自动运行并创建你必须的标识，授权在选择将使用的中间件。</span><span class="sxs-lookup"><span data-stu-id="80cab-114">As no authentication middleware is configured to automatically run and create an identity you must, at the point of authorization choose which middleware will be used.</span></span> <span data-ttu-id="80cab-115">选择你想要授权使用的中间件的最简单方法是使用`ActiveAuthenticationSchemes`属性。</span><span class="sxs-lookup"><span data-stu-id="80cab-115">The simplest way to select the middleware you wish to authorize with is to use the `ActiveAuthenticationSchemes` property.</span></span> <span data-ttu-id="80cab-116">此属性接受要使用的身份验证方案的逗号分隔列表。</span><span class="sxs-lookup"><span data-stu-id="80cab-116">This property accepts a comma delimited list of Authentication Schemes to use.</span></span> <span data-ttu-id="80cab-117">例如，</span><span class="sxs-lookup"><span data-stu-id="80cab-117">For example;</span></span>

```csharp
[Authorize(ActiveAuthenticationSchemes = "Cookie,Bearer")]
public class MixedController : Controller
```

<span data-ttu-id="80cab-118">在上例中的 cookie 和持有者中间件将运行并有机会在创建并追加当前用户的标识。</span><span class="sxs-lookup"><span data-stu-id="80cab-118">In the example above both the cookie and bearer middlewares will run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="80cab-119">通过指定一种方案仅指定的中间件将运行;</span><span class="sxs-lookup"><span data-stu-id="80cab-119">By specifying a single scheme only the specified middleware will run;</span></span>

```csharp
[Authorize(ActiveAuthenticationSchemes = "Bearer")]
```

<span data-ttu-id="80cab-120">在这种情况下仅使用持有者方案中间件将运行，和基于 cookie 的所有标识将被都忽略。</span><span class="sxs-lookup"><span data-stu-id="80cab-120">In this case only the middleware with the Bearer scheme would run, and any cookie based identities would be ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="80cab-121">选择使用策略的方案</span><span class="sxs-lookup"><span data-stu-id="80cab-121">Selecting the scheme with policies</span></span>

<span data-ttu-id="80cab-122">如果想要指定在所需的架构[策略](policies.md#security-authorization-policies-based)可以设置`AuthenticationSchemes`集合添加你的策略时。</span><span class="sxs-lookup"><span data-stu-id="80cab-122">If you prefer to specify the desired schemes in [policy](policies.md#security-authorization-policies-based) you can set the `AuthenticationSchemes` collection when adding your policy.</span></span>

```csharp
options.AddPolicy("Over18", policy =>
{
    policy.AuthenticationSchemes.Add("Bearer");
    policy.RequireAuthenticatedUser();
    policy.Requirements.Add(new Over18Requirement());
});
```

<span data-ttu-id="80cab-123">在此示例中 Over18 策略才会根据创建的标识运行`Bearer`中间件。</span><span class="sxs-lookup"><span data-stu-id="80cab-123">In this example the Over18 policy will only run against the identity created by the `Bearer` middleware.</span></span>
