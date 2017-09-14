---
title: "基于资源的授权"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 0902ba17-5304-4a12-a2d4-e0904569e988
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/resourcebased
ms.openlocfilehash: 7f7df52bf51a81558818836450997281a21b5839
ms.sourcegitcommit: f303a457644ed034a49aa89edecb4e79d9028cb1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="resource-based-authorization"></a>基于资源的授权

<a name=security-authorization-resource-based></a>

通常授权取决于要访问的资源。 例如，一个文档可能有 author 属性。 仅是文档作者就会允许进行更新，因此资源必须加载从文档存储库，然后才能进行授权评估。 这不能通过 Authorize 属性，如属性计算都需要将放在绑定数据之前，并在操作中运行你自己的代码来加载资源之前实现。 而不是声明性授权，属性方法中，我们必须使用命令性授权，开发人员其中调用 authorize 函数时在其自己的代码。

## <a name="authorizing-within-your-code"></a>授权代码中

作为一种服务，实现授权`IAuthorizationService`服务集合中已注册，可通过[依赖关系注入](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection)若要访问控制器。

```csharp
public class DocumentController : Controller
{
    IAuthorizationService _authorizationService;

    public DocumentController(IAuthorizationService authorizationService)
    {
        _authorizationService = authorizationService;
    }
}
```

`IAuthorizationService`有两种方法，一个在您将传递资源和策略名称和其他在您将传递资源和要求来评估的列表。

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

<a name=security-authorization-resource-based-imperative></a>

若要调用服务，加载在你的操作资源然后调用`AuthorizeAsync`需要的重载。 例如: 

```csharp
public async Task<IActionResult> Edit(Guid documentId)
{
    Document document = documentRepository.Find(documentId);

    if (document == null)
    {
        return new HttpNotFoundResult();
    }

    if (await _authorizationService.AuthorizeAsync(User, document, "EditPolicy"))
    {
        return View(document);
    }
    else
    {
        return new ChallengeResult();
    }
}
```

## <a name="writing-a-resource-based-handler"></a>编写资源基于处理程序

编写的处理程序基于资源授权并不那么到多大区别[编写纯要求处理程序](policies.md#security-authorization-policies-based-authorization-handler)。 你创建一项要求，，，然后实现的处理程序要求，指定之前的需求以及资源类型。 例如，可能会接受文档资源的处理将如下所示：

```csharp
public class DocumentAuthorizationHandler : AuthorizationHandler<MyRequirement, Document>
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
                                                MyRequirement requirement,
                                                Document resource)
    {
        // Validate the requirement against the resource and identity.

        return Task.CompletedTask;
    }
}
```

不要忘记你还需要注册你的处理程序中`ConfigureServices`方法：

```csharp
services.AddSingleton<IAuthorizationHandler, DocumentAuthorizationHandler>();
```

### <a name="operational-requirements"></a>操作要求

如果你有作出决策基于操作，如读取、 写入、 更新和删除，则可以使用`OperationAuthorizationRequirement`类`Microsoft.AspNetCore.Authorization.Infrastructure`命名空间。 此预构建的要求类可以编写单个处理程序具有一个参数化的操作名称，而不是创建每个操作的各个类。 若要使用此选项，提供一些操作名称：

```csharp
public static class Operations
{
    public static OperationAuthorizationRequirement Create =
        new OperationAuthorizationRequirement { Name = "Create" };
    public static OperationAuthorizationRequirement Read =
        new OperationAuthorizationRequirement   { Name = "Read" };
    public static OperationAuthorizationRequirement Update =
        new OperationAuthorizationRequirement { Name = "Update" };
    public static OperationAuthorizationRequirement Delete =
        new OperationAuthorizationRequirement { Name = "Delete" };
}
```

您的处理程序无法再使用来实现，如下所示，一个假想`Document`与资源的类：

```csharp
public class DocumentAuthorizationHandler :
    AuthorizationHandler<OperationAuthorizationRequirement, Document>
{
    public override Task HandleRequirementAsync(AuthorizationHandlerContext context,
                                                OperationAuthorizationRequirement requirement,
                                                Document resource)
    {
        // Validate the operation using the resource, the identity and
        // the Name property value from the requirement.

        return Task.CompletedTask;
    }
}
```

你可以看到处理程序工作原理上`OperationAuthorizationRequirement`。 使其评估时，该处理程序内的代码必须考虑到帐户提供的要求的 Name 属性。

若要调用你需要在调用时指定该操作的操作资源处理`AuthorizeAsync`中你的操作。 例如: 

```csharp
if (await _authorizationService.AuthorizeAsync(User, document, Operations.Read))
{
    return View(document);
}
else
{
    return new ChallengeResult();
}
```

此示例检查用户是否能够执行读取操作当前`document`实例。 如果授权成功将返回文档的视图。 如果授权失败返回`ChallengeResult`将告知任何身份验证，中间件授权已失败且该中间件可以采取适当的响应，例如返回 401 或 403 状态代码，或将用户重定向到登录页交互式浏览器客户端。
