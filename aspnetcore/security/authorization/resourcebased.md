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
# <a name="resource-based-authorization"></a><span data-ttu-id="eb53d-103">基于资源的授权</span><span class="sxs-lookup"><span data-stu-id="eb53d-103">Resource Based Authorization</span></span>

<a name=security-authorization-resource-based></a>

<span data-ttu-id="eb53d-104">通常授权取决于要访问的资源。</span><span class="sxs-lookup"><span data-stu-id="eb53d-104">Often authorization depends upon the resource being accessed.</span></span> <span data-ttu-id="eb53d-105">例如，一个文档可能有 author 属性。</span><span class="sxs-lookup"><span data-stu-id="eb53d-105">For example, a document may have an author property.</span></span> <span data-ttu-id="eb53d-106">仅是文档作者就会允许进行更新，因此资源必须加载从文档存储库，然后才能进行授权评估。</span><span class="sxs-lookup"><span data-stu-id="eb53d-106">Only the document author would be allowed to update it, so the resource must be loaded from the document repository before an authorization evaluation can be made.</span></span> <span data-ttu-id="eb53d-107">这不能通过 Authorize 属性，如属性计算都需要将放在绑定数据之前，并在操作中运行你自己的代码来加载资源之前实现。</span><span class="sxs-lookup"><span data-stu-id="eb53d-107">This cannot be done with an Authorize attribute, as attribute evaluation takes place before data binding and before your own code to load a resource runs inside an action.</span></span> <span data-ttu-id="eb53d-108">而不是声明性授权，属性方法中，我们必须使用命令性授权，开发人员其中调用 authorize 函数时在其自己的代码。</span><span class="sxs-lookup"><span data-stu-id="eb53d-108">Instead of declarative authorization, the attribute method, we must use imperative authorization, where a developer calls an authorize function within their own code.</span></span>

## <a name="authorizing-within-your-code"></a><span data-ttu-id="eb53d-109">授权代码中</span><span class="sxs-lookup"><span data-stu-id="eb53d-109">Authorizing within your code</span></span>

<span data-ttu-id="eb53d-110">作为一种服务，实现授权`IAuthorizationService`服务集合中已注册，可通过[依赖关系注入](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection)若要访问控制器。</span><span class="sxs-lookup"><span data-stu-id="eb53d-110">Authorization is implemented as a service, `IAuthorizationService`, registered in the service collection and available via [dependency injection](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection) for Controllers to access.</span></span>

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

<span data-ttu-id="eb53d-111">`IAuthorizationService`有两种方法，一个在您将传递资源和策略名称和其他在您将传递资源和要求来评估的列表。</span><span class="sxs-lookup"><span data-stu-id="eb53d-111">`IAuthorizationService` has two methods, one where you pass the resource and the policy name and the other where you pass the resource and a list of requirements to evaluate.</span></span>

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

<a name=security-authorization-resource-based-imperative></a>

<span data-ttu-id="eb53d-112">若要调用服务，加载在你的操作资源然后调用`AuthorizeAsync`需要的重载。</span><span class="sxs-lookup"><span data-stu-id="eb53d-112">To call the service, load your resource within your action then call the `AuthorizeAsync` overload you require.</span></span> <span data-ttu-id="eb53d-113">例如: </span><span class="sxs-lookup"><span data-stu-id="eb53d-113">For example:</span></span>

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

## <a name="writing-a-resource-based-handler"></a><span data-ttu-id="eb53d-114">编写资源基于处理程序</span><span class="sxs-lookup"><span data-stu-id="eb53d-114">Writing a resource based handler</span></span>

<span data-ttu-id="eb53d-115">编写的处理程序基于资源授权并不那么到多大区别[编写纯要求处理程序](policies.md#security-authorization-policies-based-authorization-handler)。</span><span class="sxs-lookup"><span data-stu-id="eb53d-115">Writing a handler for resource based authorization is not that much different to [writing a plain requirements handler](policies.md#security-authorization-policies-based-authorization-handler).</span></span> <span data-ttu-id="eb53d-116">你创建一项要求，，，然后实现的处理程序要求，指定之前的需求以及资源类型。</span><span class="sxs-lookup"><span data-stu-id="eb53d-116">You create a requirement, and then implement a handler for the requirement, specifying the requirement as before and also the resource type.</span></span> <span data-ttu-id="eb53d-117">例如，可能会接受文档资源的处理将如下所示：</span><span class="sxs-lookup"><span data-stu-id="eb53d-117">For example, a handler which might accept a Document resource would look as follows:</span></span>

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

<span data-ttu-id="eb53d-118">不要忘记你还需要注册你的处理程序中`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="eb53d-118">Don't forget you also need to register your handler in the `ConfigureServices` method:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, DocumentAuthorizationHandler>();
```

### <a name="operational-requirements"></a><span data-ttu-id="eb53d-119">操作要求</span><span class="sxs-lookup"><span data-stu-id="eb53d-119">Operational Requirements</span></span>

<span data-ttu-id="eb53d-120">如果你有作出决策基于操作，如读取、 写入、 更新和删除，则可以使用`OperationAuthorizationRequirement`类`Microsoft.AspNetCore.Authorization.Infrastructure`命名空间。</span><span class="sxs-lookup"><span data-stu-id="eb53d-120">If you are making decisions based on operations such as read, write, update and delete, you can use the `OperationAuthorizationRequirement` class in the `Microsoft.AspNetCore.Authorization.Infrastructure` namespace.</span></span> <span data-ttu-id="eb53d-121">此预构建的要求类可以编写单个处理程序具有一个参数化的操作名称，而不是创建每个操作的各个类。</span><span class="sxs-lookup"><span data-stu-id="eb53d-121">This prebuilt requirement class enables you to write a single handler which has a parameterized operation name, rather than create individual classes for each operation.</span></span> <span data-ttu-id="eb53d-122">若要使用此选项，提供一些操作名称：</span><span class="sxs-lookup"><span data-stu-id="eb53d-122">To use it, provide some operation names:</span></span>

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

<span data-ttu-id="eb53d-123">您的处理程序无法再使用来实现，如下所示，一个假想`Document`与资源的类：</span><span class="sxs-lookup"><span data-stu-id="eb53d-123">Your handler could then be implemented as follows, using a hypothetical `Document` class as the resource:</span></span>

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

<span data-ttu-id="eb53d-124">你可以看到处理程序工作原理上`OperationAuthorizationRequirement`。</span><span class="sxs-lookup"><span data-stu-id="eb53d-124">You can see the handler works on `OperationAuthorizationRequirement`.</span></span> <span data-ttu-id="eb53d-125">使其评估时，该处理程序内的代码必须考虑到帐户提供的要求的 Name 属性。</span><span class="sxs-lookup"><span data-stu-id="eb53d-125">The code inside the handler must take the Name property of the supplied requirement into account when making its evaluations.</span></span>

<span data-ttu-id="eb53d-126">若要调用你需要在调用时指定该操作的操作资源处理`AuthorizeAsync`中你的操作。</span><span class="sxs-lookup"><span data-stu-id="eb53d-126">To call an operational resource handler you need to specify the operation when calling `AuthorizeAsync` in your action.</span></span> <span data-ttu-id="eb53d-127">例如: </span><span class="sxs-lookup"><span data-stu-id="eb53d-127">For example:</span></span>

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

<span data-ttu-id="eb53d-128">此示例检查用户是否能够执行读取操作当前`document`实例。</span><span class="sxs-lookup"><span data-stu-id="eb53d-128">This example checks if the User is able to perform the Read operation for the current `document` instance.</span></span> <span data-ttu-id="eb53d-129">如果授权成功将返回文档的视图。</span><span class="sxs-lookup"><span data-stu-id="eb53d-129">If authorization succeeds the view for the document will be returned.</span></span> <span data-ttu-id="eb53d-130">如果授权失败返回`ChallengeResult`将告知任何身份验证，中间件授权已失败且该中间件可以采取适当的响应，例如返回 401 或 403 状态代码，或将用户重定向到登录页交互式浏览器客户端。</span><span class="sxs-lookup"><span data-stu-id="eb53d-130">If authorization fails returning `ChallengeResult` will inform any authentication middleware authorization has failed and the middleware can take the appropriate response, for example returning a 401 or 403 status code, or redirecting the user to a login page for interactive browser clients.</span></span>
