---
title: "自定义的基于策略的授权"
author: rick-anderson
description: 
keywords: "ASP.NET 核心"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: e422a1b2-dc4a-4bcc-b8d9-7ee62009b6a3
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/policies
ms.openlocfilehash: dd7187f67887bb39a5ff425dcbae0927c7565cb8
ms.sourcegitcommit: 41e3e007512c175a42910bc69678f3f0403cab04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2017
---
# <a name="custom-policy-based-authorization"></a><span data-ttu-id="e9edc-103">自定义的基于策略的授权</span><span class="sxs-lookup"><span data-stu-id="e9edc-103">Custom Policy-Based Authorization</span></span>

<a name=security-authorization-policies-based></a>

<span data-ttu-id="e9edc-104">实际上[角色授权](roles.md#security-authorization-role-based)和[声明授权](claims.md#security-authorization-claims-based)使使用的要求、 的处理程序的要求并预配置的策略。</span><span class="sxs-lookup"><span data-stu-id="e9edc-104">Underneath the covers the [role authorization](roles.md#security-authorization-role-based) and [claims authorization](claims.md#security-authorization-claims-based) make use of a requirement, a handler for the requirement and a pre-configured policy.</span></span> <span data-ttu-id="e9edc-105">这些构建基块，可以快速、 在代码中允许的更丰富且可重复使用，可轻松地测试授权结构的授权评估。</span><span class="sxs-lookup"><span data-stu-id="e9edc-105">These building blocks allow you to express authorization evaluations in code, allowing for a richer, reusable, and easily testable authorization structure.</span></span>

<span data-ttu-id="e9edc-106">组成一个或多个要求和注册在应用程序启动授权服务配置的一部分，在授权策略`ConfigureServices`中*Startup.cs*文件。</span><span class="sxs-lookup"><span data-stu-id="e9edc-106">An authorization policy is made up of one or more requirements and registered at application startup as part of the Authorization service configuration, in `ConfigureServices` in the *Startup.cs* file.</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

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

<span data-ttu-id="e9edc-107">此处你可以看到"Over21"策略创建单个要求，的最小存在时间，这作为参数传递到要求。</span><span class="sxs-lookup"><span data-stu-id="e9edc-107">Here you can see an "Over21" policy is created with a single requirement, that of a minimum age, which is passed as a parameter to the requirement.</span></span>

<span data-ttu-id="e9edc-108">策略使用应用`Authorize`通过指定策略名称，例如; 的属性</span><span class="sxs-lookup"><span data-stu-id="e9edc-108">Policies are applied using the `Authorize` attribute by specifying the policy name, for example;</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

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

## <a name="requirements"></a><span data-ttu-id="e9edc-109">要求</span><span class="sxs-lookup"><span data-stu-id="e9edc-109">Requirements</span></span>

<span data-ttu-id="e9edc-110">授权要求是一个策略可用于评估当前的用户主体的数据参数的集合。</span><span class="sxs-lookup"><span data-stu-id="e9edc-110">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="e9edc-111">在我们的最小存在时间策略我们的要求是单个参数，最小存在时间。</span><span class="sxs-lookup"><span data-stu-id="e9edc-111">In our Minimum Age policy the requirement we have is a single parameter, the minimum age.</span></span> <span data-ttu-id="e9edc-112">要求必须实现`IAuthorizationRequirement`。</span><span class="sxs-lookup"><span data-stu-id="e9edc-112">A requirement must implement `IAuthorizationRequirement`.</span></span> <span data-ttu-id="e9edc-113">这是一个空的的标记接口。</span><span class="sxs-lookup"><span data-stu-id="e9edc-113">This is an empty, marker interface.</span></span> <span data-ttu-id="e9edc-114">参数化的最小年龄要求可能会实现，如下所示;</span><span class="sxs-lookup"><span data-stu-id="e9edc-114">A parameterized minimum age requirement might be implemented as follows;</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

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

<span data-ttu-id="e9edc-115">一项要求不需要具有数据或属性。</span><span class="sxs-lookup"><span data-stu-id="e9edc-115">A requirement doesn't need to have data or properties.</span></span>

<a name=security-authorization-policies-based-authorization-handler></a>

## <a name="authorization-handlers"></a><span data-ttu-id="e9edc-116">授权处理程序</span><span class="sxs-lookup"><span data-stu-id="e9edc-116">Authorization Handlers</span></span>

<span data-ttu-id="e9edc-117">授权处理程序负责的要求的任何属性的计算。</span><span class="sxs-lookup"><span data-stu-id="e9edc-117">An authorization handler is responsible for the evaluation of any properties of a requirement.</span></span> <span data-ttu-id="e9edc-118">授权处理程序必须对它们进行评估针对提供`AuthorizationHandlerContext`以确定是否允许授权。</span><span class="sxs-lookup"><span data-stu-id="e9edc-118">The  authorization handler must evaluate them against a provided `AuthorizationHandlerContext` to decide if authorization is allowed.</span></span> <span data-ttu-id="e9edc-119">可以有一项要求[多个处理程序](policies.md#security-authorization-policies-based-multiple-handlers)。</span><span class="sxs-lookup"><span data-stu-id="e9edc-119">A requirement can have [multiple handlers](policies.md#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="e9edc-120">处理程序必须继承`AuthorizationHandler<T>`其中 T 是它处理的要求。</span><span class="sxs-lookup"><span data-stu-id="e9edc-120">Handlers must inherit `AuthorizationHandler<T>` where T is the requirement it handles.</span></span>

<a name=security-authorization-handler-example></a>

<span data-ttu-id="e9edc-121">最小存在时间处理程序可能如下所示：</span><span class="sxs-lookup"><span data-stu-id="e9edc-121">The minimum age handler might look like this:</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

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

<span data-ttu-id="e9edc-122">在上面的代码中我们首先查找以确定是否当前的用户主体已声明的已发出我们知道的颁发者和信任的出生日期。</span><span class="sxs-lookup"><span data-stu-id="e9edc-122">In the code above we first look to see if the current user principal has a date of birth claim which has been issued by an Issuer we know and trust.</span></span> <span data-ttu-id="e9edc-123">如果声明是缺少我们无法授权以便我们返回。</span><span class="sxs-lookup"><span data-stu-id="e9edc-123">If the claim is missing we can't authorize so we return.</span></span> <span data-ttu-id="e9edc-124">如果我们有声明，我们找出用户已存在多长，并且它们是否符合由要求传入的最小存在时间然后授权已被成功。</span><span class="sxs-lookup"><span data-stu-id="e9edc-124">If we have a claim, we figure out how old the user is, and if they meet the minimum age passed in by the requirement then authorization has been successful.</span></span> <span data-ttu-id="e9edc-125">授权成功后我们调用`context.Succeed()`要求已成功作为参数传递。</span><span class="sxs-lookup"><span data-stu-id="e9edc-125">Once authorization is successful we call `context.Succeed()` passing in the requirement that has been successful as a parameter.</span></span>

<a name=security-authorization-policies-based-handler-registration></a>

<span data-ttu-id="e9edc-126">在配置期间，服务集合中必须例如; 注册处理程序</span><span class="sxs-lookup"><span data-stu-id="e9edc-126">Handlers must be registered in the services collection during configuration, for example;</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

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

<span data-ttu-id="e9edc-127">每个处理程序添加到服务集合，通过使用`services.AddSingleton<IAuthorizationHandler, YourHandlerClass>();`传入处理程序类。</span><span class="sxs-lookup"><span data-stu-id="e9edc-127">Each handler is added to the services collection by using `services.AddSingleton<IAuthorizationHandler, YourHandlerClass>();` passing in your handler class.</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="e9edc-128">处理程序应返回？</span><span class="sxs-lookup"><span data-stu-id="e9edc-128">What should a handler return?</span></span>

<span data-ttu-id="e9edc-129">你可在看到我们[处理程序示例](policies.md#security-authorization-handler-example)，`Handle()`方法具有没有返回值，因此如何执行我们指示成功或失败？</span><span class="sxs-lookup"><span data-stu-id="e9edc-129">You can see in our [handler example](policies.md#security-authorization-handler-example) that the `Handle()` method has no return value, so how do we indicate success or failure?</span></span>

* <span data-ttu-id="e9edc-130">处理程序通过调用中表示成功`context.Succeed(IAuthorizationRequirement requirement)`，将要求传递成功验证。</span><span class="sxs-lookup"><span data-stu-id="e9edc-130">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="e9edc-131">处理程序不需要来处理故障通常情况下，如其他处理程序相同的要求可能会成功。</span><span class="sxs-lookup"><span data-stu-id="e9edc-131">A handler does not need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="e9edc-132">若要确保失败，即使其他处理程序要求成功，调用`context.Fail`。</span><span class="sxs-lookup"><span data-stu-id="e9edc-132">To guarantee failure even if other handlers for a requirement succeed, call `context.Fail`.</span></span>

<span data-ttu-id="e9edc-133">无论你在你的处理程序调用的策略要求要求时，将调用要求的所有处理程序。</span><span class="sxs-lookup"><span data-stu-id="e9edc-133">Regardless of what you call inside your handler all handlers for a requirement will be called when a policy requires the requirement.</span></span> <span data-ttu-id="e9edc-134">这样要求产生副作用，如日志记录，始终会进行即使`context.Fail()`已在另一个处理程序调用。</span><span class="sxs-lookup"><span data-stu-id="e9edc-134">This allows requirements to have side effects, such as logging, which will always take place even if `context.Fail()` has been called in another handler.</span></span>

<a name=security-authorization-policies-based-multiple-handlers></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="e9edc-135">为什么将需要一项要求的多个处理程序？</span><span class="sxs-lookup"><span data-stu-id="e9edc-135">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="e9edc-136">在要评估上的情况下**或**实现单个要求的多个处理程序的基础。</span><span class="sxs-lookup"><span data-stu-id="e9edc-136">In cases where you want evaluation to be on an **OR** basis you implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="e9edc-137">例如，Microsoft 已使用密钥卡仅打开的门。</span><span class="sxs-lookup"><span data-stu-id="e9edc-137">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="e9edc-138">如果你忘记你密钥卡随身携带接线员打印临时不干胶标签，并为你打开大门。</span><span class="sxs-lookup"><span data-stu-id="e9edc-138">If you leave your key card at home the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="e9edc-139">在此方案中您将需要单个， *EnterBuilding*，但多个处理程序，每个检查单个要求。</span><span class="sxs-lookup"><span data-stu-id="e9edc-139">In this scenario you'd have a single requirement, *EnterBuilding*, but multiple handlers, each one examining a single requirement.</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

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

<span data-ttu-id="e9edc-140">现在，假设这两个处理程序都[注册](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)当策略的计算结果`EnterBuildingRequirement`如果任一处理程序成功策略评估将会成功。</span><span class="sxs-lookup"><span data-stu-id="e9edc-140">Now, assuming both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration) when a policy evaluates the `EnterBuildingRequirement` if either handler succeeds the policy evaluation will succeed.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="e9edc-141">使用 func 来实现策略</span><span class="sxs-lookup"><span data-stu-id="e9edc-141">Using a func to fulfill a policy</span></span>

<span data-ttu-id="e9edc-142">可能有情况下实现策略所在简单来表示在代码中。</span><span class="sxs-lookup"><span data-stu-id="e9edc-142">There may be occasions where fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="e9edc-143">可以只需提供`Func<AuthorizationHandlerContext, bool>`配置与你的策略时`RequireAssertion`策略生成器。</span><span class="sxs-lookup"><span data-stu-id="e9edc-143">It is possible to simply supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="e9edc-144">例如以前`BadgeEntryHandler`，如下所示; 可重写</span><span class="sxs-lookup"><span data-stu-id="e9edc-144">For example the previous `BadgeEntryHandler` could be rewritten as follows;</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

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

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="e9edc-145">访问在处理程序的 MVC 请求上下文</span><span class="sxs-lookup"><span data-stu-id="e9edc-145">Accessing MVC Request Context In Handlers</span></span>

<span data-ttu-id="e9edc-146">`Handle`必须在授权处理程序中实现的方法具有两个参数，`AuthorizationContext`和`Requirement`正在处理。</span><span class="sxs-lookup"><span data-stu-id="e9edc-146">The `Handle` method you must implement in an authorization handler has two parameters, an `AuthorizationContext` and the `Requirement` you are handling.</span></span> <span data-ttu-id="e9edc-147">框架，例如 MVC 或 Jabbr 可用于任何将对象添加到`Resource`属性`AuthorizationContext`通过额外的信息。</span><span class="sxs-lookup"><span data-stu-id="e9edc-147">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationContext` to pass through extra information.</span></span>

<span data-ttu-id="e9edc-148">例如 MVC 传递的实例的`Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext`其他 MVC 提供中用于访问 HttpContext、 RouteData 和所有内容的资源属性。</span><span class="sxs-lookup"><span data-stu-id="e9edc-148">For example MVC passes an instance of `Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext` in the resource property which is used to access HttpContext, RouteData and everything else MVC provides.</span></span>

<span data-ttu-id="e9edc-149">使用`Resource`属性是特定于框架。</span><span class="sxs-lookup"><span data-stu-id="e9edc-149">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="e9edc-150">使用中的信息`Resource`属性将你授权将策略限制为特定的框架。</span><span class="sxs-lookup"><span data-stu-id="e9edc-150">Using information in the `Resource` property will limit your authorization policies to particular frameworks.</span></span> <span data-ttu-id="e9edc-151">应强制转换`Resource`属性使用`as`关键字，并检查该强制转换具有成功以确保你的代码不崩溃与`InvalidCastExceptions`其他框架; 上运行时</span><span class="sxs-lookup"><span data-stu-id="e9edc-151">You should cast the `Resource` property using the `as` keyword, and then check the cast has succeed to ensure your code doesn't crash with `InvalidCastExceptions` when run on other frameworks;</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
if (context.Resource is Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext mvcContext)
{
    // Examine MVC specific things like routing data.
}
```
