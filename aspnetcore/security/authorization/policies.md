---
title: "自定义的基于策略的授权"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: e422a1b2-dc4a-4bcc-b8d9-7ee62009b6a3
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/policies
ms.openlocfilehash: 5021b5d20f6d9b9a4d8889f25b5e41f2c9306f64
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2017
---
# <a name="custom-policy-based-authorization"></a>自定义的基于策略的授权

<a name=security-authorization-policies-based></a>

实际上[角色授权](roles.md#security-authorization-role-based)和[声明授权](claims.md#security-authorization-claims-based)使使用的要求、 的处理程序的要求并预配置的策略。 这些构建基块，可以快速、 在代码中允许的更丰富且可重复使用，可轻松地测试授权结构的授权评估。

组成一个或多个要求和注册在应用程序启动授权服务配置的一部分，在授权策略`ConfigureServices`中*Startup.cs*文件。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Over21",
                          policy => policy.Requirements.Add(new MinimumAgeRequirement(21)));
    });
}
```

此处你可以看到"Over21"策略创建单个要求，的最小存在时间，这作为参数传递到要求。

策略使用应用`Authorize`通过指定策略名称，例如; 的属性

```csharp
[Authorize(Policy="Over21")]
public class AlcoholPurchaseRequirementsController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

## <a name="requirements"></a>要求

授权要求是一个策略可用于评估当前的用户主体的数据参数的集合。 在我们的最小存在时间策略我们的要求是单个参数，最小存在时间。 要求必须实现`IAuthorizationRequirement`。 这是一个空的的标记接口。 参数化的最小年龄要求可能会实现，如下所示;

```csharp
public class MinimumAgeRequirement : IAuthorizationRequirement
{
    public int MinimumAge { get; private set; }
    
    public MinimumAgeRequirement(int minimumAge)
    {
        MinimumAge = minimumAge;
    }
}
```

一项要求不需要具有数据或属性。

<a name=security-authorization-policies-based-authorization-handler></a>

## <a name="authorization-handlers"></a>授权处理程序

授权处理程序负责的要求的任何属性的计算。 授权处理程序必须对它们进行评估针对提供`AuthorizationHandlerContext`以确定是否允许授权。 可以有一项要求[多个处理程序](policies.md#security-authorization-policies-based-multiple-handlers)。 处理程序必须继承`AuthorizationHandler<T>`其中 T 是它处理的要求。

<a name=security-authorization-handler-example></a>

最小存在时间处理程序可能如下所示：

```csharp
public class MinimumAgeHandler : AuthorizationHandler<MinimumAgeRequirement>
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MinimumAgeRequirement requirement)
    {
        if (!context.User.HasClaim(c => c.Type == ClaimTypes.DateOfBirth &&
                                   c.Issuer == "http://contoso.com"))
        {
            // .NET 4.x -> return Task.FromResult(0);
            return Task.CompletedTask;
        }

        var dateOfBirth = Convert.ToDateTime(context.User.FindFirst(
            c => c.Type == ClaimTypes.DateOfBirth && c.Issuer == "http://contoso.com").Value);

        int calculatedAge = DateTime.Today.Year - dateOfBirth.Year;
        if (dateOfBirth > DateTime.Today.AddYears(-calculatedAge))
        {
            calculatedAge--;
        }

        if (calculatedAge >= requirement.MinimumAge)
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }
}
```

在上面的代码中我们首先查找以确定是否当前的用户主体已声明的已发出我们知道的颁发者和信任的出生日期。 如果声明是缺少我们无法授权以便我们返回。 如果我们有声明，我们找出用户已存在多长，并且它们是否符合由要求传入的最小存在时间然后授权已被成功。 授权成功后我们调用`context.Succeed()`要求已成功作为参数传递。

<a name=security-authorization-policies-based-handler-registration></a>

在配置期间，服务集合中必须例如; 注册处理程序

```csharp

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Over21",
                          policy => policy.Requirements.Add(new MinimumAgeRequirement(21)));
    });

    services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();
}
```

每个处理程序添加到服务集合，通过使用`services.AddSingleton<IAuthorizationHandler, YourHandlerClass>();`传入处理程序类。

## <a name="what-should-a-handler-return"></a>处理程序应返回？

你可在看到我们[处理程序示例](policies.md#security-authorization-handler-example)，`Handle()`方法具有没有返回值，因此如何执行我们指示成功或失败？

* 处理程序通过调用中表示成功`context.Succeed(IAuthorizationRequirement requirement)`，将要求传递成功验证。

* 处理程序不需要来处理故障通常情况下，如其他处理程序相同的要求可能会成功。

* 若要确保失败，即使其他处理程序要求成功，调用`context.Fail`。

无论你在你的处理程序调用的策略要求要求时，将调用要求的所有处理程序。 这样要求产生副作用，如日志记录，始终会进行即使`context.Fail()`已在另一个处理程序调用。

<a name=security-authorization-policies-based-multiple-handlers></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>为什么将需要一项要求的多个处理程序？

在要评估上的情况下**或**实现单个要求的多个处理程序的基础。 例如，Microsoft 已使用密钥卡仅打开的门。 如果你忘记你密钥卡随身携带接线员打印临时不干胶标签，并为你打开大门。 在此方案中您将需要单个， *EnterBuilding*，但多个处理程序，每个检查单个要求。

```csharp
public class EnterBuildingRequirement : IAuthorizationRequirement
{
}

public class BadgeEntryHandler : AuthorizationHandler<EnterBuildingRequirement>
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, EnterBuildingRequirement requirement)
    {
        if (context.User.HasClaim(c => c.Type == ClaimTypes.BadgeId &&
                                       c.Issuer == "http://microsoftsecurity"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }
}

public class HasTemporaryStickerHandler : AuthorizationHandler<EnterBuildingRequirement>
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, EnterBuildingRequirement requirement)
    {
        if (context.User.HasClaim(c => c.Type == ClaimTypes.TemporaryBadgeId &&
                                       c.Issuer == "https://microsoftsecurity"))
        {
            // We'd also check the expiration date on the sticker.
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }
}
```

现在，假设这两个处理程序都[注册](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)当策略的计算结果`EnterBuildingRequirement`如果任一处理程序成功策略评估将会成功。

## <a name="using-a-func-to-fulfill-a-policy"></a>使用 func 来实现策略

可能有情况下实现策略所在简单来表示在代码中。 可以只需提供`Func<AuthorizationHandlerContext, bool>`配置与你的策略时`RequireAssertion`策略生成器。

例如以前`BadgeEntryHandler`，如下所示; 可重写

```csharp
services.AddAuthorization(options =>
    {
        options.AddPolicy("BadgeEntry",
                          policy => policy.RequireAssertion(context =>
                                  context.User.HasClaim(c =>
                                     (c.Type == ClaimTypes.BadgeId ||
                                      c.Type == ClaimTypes.TemporaryBadgeId)
                                      && c.Issuer == "https://microsoftsecurity"));
                          }));
    }
 }
```

## <a name="accessing-mvc-request-context-in-handlers"></a>访问在处理程序的 MVC 请求上下文

`Handle`必须在授权处理程序中实现的方法具有两个参数，`AuthorizationContext`和`Requirement`正在处理。 框架，例如 MVC 或 Jabbr 可用于任何将对象添加到`Resource`属性`AuthorizationContext`通过额外的信息。

例如 MVC 传递的实例的`Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext`其他 MVC 提供中用于访问 HttpContext、 RouteData 和所有内容的资源属性。

使用`Resource`属性是特定于框架。 使用中的信息`Resource`属性将你授权将策略限制为特定的框架。 应强制转换`Resource`属性使用`as`关键字，并检查该强制转换具有成功以确保你的代码不崩溃与`InvalidCastExceptions`其他框架; 上运行时

```csharp
if (context.Resource is Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext mvcContext)
{
    // Examine MVC specific things like routing data.
}
```
