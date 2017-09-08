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
# <a name="limiting-identity-by-scheme"></a>限制标识方案

<a name=security-authorization-limiting-by-scheme></a>

在某些情况下，如单页面应用程序就可以得到多个身份验证方法。 例如，你的应用程序可以将基于 cookie 的身份验证登录和持有者身份验证用于 JavaScript 请求。 在某些情况下可能具有多个实例的身份验证中间件。 例如，两个 cookie 中间件其中一个包含一个基本的标识，因为需要额外的安全的操作，则用户要求具有触发多因素身份验证时将创建一个。

当身份验证中间件过程中配置身份验证，例如命名身份验证方案

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

在此配置中两个身份验证中间件已添加，一个 cookie，一个用于持有者。

>[!NOTE]
>添加多个身份验证中间件时你应确保任何中间件配置为自动运行。 执行此操作通过设置`AutomaticAuthenticate`选项属性设置为 false。 如果您不能执行此筛选由方案将不起作用。

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a>选择带有 Authorize 属性的方案

因为没有身份验证中间件配置为自动运行并创建你必须的标识，授权在选择将使用的中间件。 选择你想要授权使用的中间件的最简单方法是使用`ActiveAuthenticationSchemes`属性。 此属性接受要使用的身份验证方案的逗号分隔列表。 例如，

```csharp
[Authorize(ActiveAuthenticationSchemes = "Cookie,Bearer")]
public class MixedController : Controller
```

在上例中的 cookie 和持有者中间件将运行并有机会在创建并追加当前用户的标识。 通过指定一种方案仅指定的中间件将运行;

```csharp
[Authorize(ActiveAuthenticationSchemes = "Bearer")]
```

在这种情况下仅使用持有者方案中间件将运行，和基于 cookie 的所有标识将被都忽略。

## <a name="selecting-the-scheme-with-policies"></a>选择使用策略的方案

如果想要指定在所需的架构[策略](policies.md#security-authorization-policies-based)可以设置`AuthenticationSchemes`集合添加你的策略时。

```csharp
options.AddPolicy("Over18", policy =>
{
    policy.AuthenticationSchemes.Add("Bearer");
    policy.RequireAuthenticatedUser();
    policy.Requirements.Add(new Over18Requirement());
});
```

在此示例中 Over18 策略才会根据创建的标识运行`Bearer`中间件。
