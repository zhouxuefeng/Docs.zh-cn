---
title: "在 ASP.NET 核心的自定义基于策略的授权"
author: rick-anderson
description: "了解如何创建和使用自定义授权策略处理程序，用于实施 ASP.NET Core 应用程序中的授权要求。"
keywords: "ASP.NET 核心，授权、 自定义策略、 授权策略"
ms.author: riande
ms.custom: mvc
manager: wpickett
ms.date: 11/21/2017
ms.topic: article
ms.assetid: e422a1b2-dc4a-4bcc-b8d9-7ee62009b6a3
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/policies
ms.openlocfilehash: 280dd72b75e39546061d8455931f597f50c829fe
ms.sourcegitcommit: f1436107b4c022b26f5235dddef103cec5aa6bff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2017
---
# <a name="custom-policy-based-authorization"></a>自定义的基于策略的授权

实际上，[基于角色的授权](xref:security/authorization/roles)和[基于声明的授权](xref:security/authorization/claims)使用要求，要求处理程序，并预先配置的策略。 这些构建基块支持在代码中的授权评估表达式。 结果是一个更丰富、 可重复使用、 可测试授权结构。

授权策略包含一个或多个要求。 在中注册的授权服务配置中，一部分`ConfigureServices`方法`Startup`类：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=40-41,50-55,63,72)]

在前面的示例中，创建一个"AtLeast21"策略。 它具有一个要求，的最小存在时间，这作为参数提供到需求。

通过使用应用策略`[Authorize]`具有策略名称属性。 例如: 

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="requirements"></a>要求

授权要求是一个策略可用于评估当前的用户主体的数据参数的集合。 在我们的"AtLeast21"策略的要求是单个参数&mdash;最小存在时间。 要求实现`IAuthorizationRequirement`，这是一个空标记接口。 参数化的最小年龄要求无法实现，如下所示：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

> [!NOTE]
> 一项要求不需要具有数据或属性。

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>授权处理程序

授权处理程序负责的要求的属性求值。 授权处理程序会评估要求，针对提供`AuthorizationHandlerContext`以确定是否允许访问。 可以有一项要求[多个处理程序](#security-authorization-policies-based-multiple-handlers)。 处理程序继承`AuthorizationHandler<T>`，其中`T`是要处理的要求。

<a name="security-authorization-handler-example"></a>

最小存在时间处理程序可能如下所示：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

前面的代码确定当前的用户主体是否声明已知且受信任的颁发者已发出的出生日期。 授权不能出现缺少声明时，在这种情况下返回的已完成的任务。 在不存在声明，计算用户的年龄。 如果该用户满足定义的要求的最短期限，授权视为成功。 授权成功后，`context.Succeed`调用与作为参数满足要求。

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>处理程序注册

在配置期间服务集合中注册处理程序。 例如: 

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=40-41,50-55,63-65,72)]

每个处理程序添加到服务集合，通过调用`services.AddSingleton<IAuthorizationHandler, YourHandlerClass>();`。

## <a name="what-should-a-handler-return"></a>处理程序应返回？

请注意，`Handle`中的方法[处理程序示例](#security-authorization-handler-example)不返回值。 是的成功或失败指示的状态如何？

* 处理程序通过调用中表示成功`context.Succeed(IAuthorizationRequirement requirement)`，将要求传递成功验证。

* 处理程序不需要来处理故障通常情况下，如其他处理程序相同的要求可能会成功。

* 若要确保失败，即使其他要求处理程序成功，调用`context.Fail`。

无论什么调用在您的处理程序内的策略要求要求时，将调用要求的所有处理程序。 这样要求产生副作用，如日志记录，始终会进行即使`context.Fail()`已在另一个处理程序调用。

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>为什么将需要一项要求的多个处理程序？

在要评估上的情况下**或**基础，实现单个要求的多个处理程序。 例如，Microsoft 已使用密钥卡仅打开的门。 如果你在家离开你密钥卡，接线员打印临时不干胶标签，并为你打开大门。 在此方案中，您将需要单个， *BuildingEntry*，但多个处理程序，每个检查单个要求。

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

确保这两个处理程序[注册](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)。 如果任一处理程序成功时策略的计算结果`BuildingEntryRequirement`，策略评估成功。

## <a name="using-a-func-to-fulfill-a-policy"></a>使用 func 来实现策略

可能有哪些履行策略是简单代码中表示的情况。 可以提供`Func<AuthorizationHandlerContext, bool>`配置与你的策略时`RequireAssertion`策略生成器。

例如，以前`BadgeEntryHandler`无法，如下所示重写：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=52-53,57-63)]

## <a name="accessing-mvc-request-context-in-handlers"></a>访问在处理程序的 MVC 请求上下文

`HandleRequirementAsync`授权处理程序中实现的方法具有两个参数：`AuthorizationHandlerContext`和`TRequirement`正在处理。 框架，例如 MVC 或 Jabbr 可用于任何将对象添加到`Resource`属性`AuthorizationHandlerContext`传递额外信息。

例如，MVC 传递的实例的[AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext)中`Resource`属性。 此属性提供访问权限`HttpContext`， `RouteData`，以及其他和提供的 MVC Razor 页的所有内容。

使用`Resource`属性是特定于框架。 使用中的信息`Resource`属性限制到特定的框架你授权策略。 应强制转换`Resource`属性使用`as`关键字，然后确认该强制转换具有成功以确保你的代码不崩溃与`InvalidCastException`其他框架上运行时：

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```
