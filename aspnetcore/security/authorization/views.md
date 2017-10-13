---
title: "视图基于授权"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 24ce40d8-9b83-4bae-9d4c-a66350fcc8f8
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/views
ms.openlocfilehash: 82c0c7282de34e496f529d964f99121ae2805c5a
ms.sourcegitcommit: e3b1726cc04e80dc28464c35259edbd3bc39a438
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2017
---
# <a name="view-based-authorization"></a><span data-ttu-id="20c3e-103">视图基于授权</span><span class="sxs-lookup"><span data-stu-id="20c3e-103">View Based Authorization</span></span>

<a name=security-authorization-views></a>

<span data-ttu-id="20c3e-104">通常，开发人员将想要显示、 隐藏或修改基于当前的用户标识的用户界面。</span><span class="sxs-lookup"><span data-stu-id="20c3e-104">Often a developer will want to show, hide or otherwise modify a UI based on the current user identity.</span></span> <span data-ttu-id="20c3e-105">你可以访问授权服务在服务内通过的 MVC 视图[依赖关系注入](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="20c3e-105">You can access the authorization service within MVC views via [dependency injection](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection).</span></span> <span data-ttu-id="20c3e-106">若要将授权服务注入到 Razor 视图使用`@inject`指令，例如`@inject IAuthorizationService AuthorizationService`(需要`@using Microsoft.AspNetCore.Authorization`)。</span><span class="sxs-lookup"><span data-stu-id="20c3e-106">To inject the authorization service into a Razor view use the `@inject` directive, for example `@inject IAuthorizationService AuthorizationService` (requires `@using Microsoft.AspNetCore.Authorization`).</span></span> <span data-ttu-id="20c3e-107">如果所需的每个视图中的授权服务则放置`@inject`指令插入`_ViewImports.cshtml`文件中`Views`目录。</span><span class="sxs-lookup"><span data-stu-id="20c3e-107">If you want the authorization service in every view then place the `@inject` directive into the `_ViewImports.cshtml` file in the `Views` directory.</span></span> <span data-ttu-id="20c3e-108">有关依赖关系注入到视图的详细信息请参阅[到视图的依赖关系注入](../../mvc/views/dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="20c3e-108">For more information on dependency injection into views see [Dependency injection into views](../../mvc/views/dependency-injection.md).</span></span>

<span data-ttu-id="20c3e-109">一旦你将具有插入的授权服务就使用它通过调用`AuthorizeAsync`中完全一样的方式将检查期间方法[基于资源的授权](resourcebased.md#security-authorization-resource-based-imperative)。</span><span class="sxs-lookup"><span data-stu-id="20c3e-109">Once you have injected the authorization service you use it by calling the `AuthorizeAsync` method in exactly the same way as you would check during [resource based authorization](resourcebased.md#security-authorization-resource-based-imperative).</span></span>

```cshtml
@if (await AuthorizationService.AuthorizeAsync(User, "PolicyName"))
   {
       <p>This paragraph is displayed because you fulfilled PolicyName.</p>
   }
   ```

<span data-ttu-id="20c3e-110">在某些情况下的资源将为你的视图模型，并且你可以调用`AuthorizeAsync`中完全一样的方式将检查期间[基于资源的授权](resourcebased.md#security-authorization-resource-based-imperative);</span><span class="sxs-lookup"><span data-stu-id="20c3e-110">In some cases the resource will be your view model, and you can call `AuthorizeAsync` in exactly the same way as you would check during [resource based authorization](resourcebased.md#security-authorization-resource-based-imperative);</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="20c3e-111">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="20c3e-111">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```cshtml
   @if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
   {
       <p><a class="btn btn-default" role="button"
           href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
   }
   ```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="20c3e-112">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="20c3e-112">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```cshtml
   @if (await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit))
   {
       <p><a class="btn btn-default" role="button"
           href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
   }
   ```
---

<span data-ttu-id="20c3e-113">你可以看到模型作为资源授权应当考虑传递。</span><span class="sxs-lookup"><span data-stu-id="20c3e-113">Here you can see the model is passed as the resource authorization should take into consideration.</span></span>

>[!WARNING]
><span data-ttu-id="20c3e-114">不要依赖于显示或隐藏你的 UI 作为唯一授权方法的部分。</span><span class="sxs-lookup"><span data-stu-id="20c3e-114">Do not rely on showing or hiding parts of your UI as your only authorization method.</span></span> <span data-ttu-id="20c3e-115">隐藏用户界面元素并不意味着用户无法访问它。</span><span class="sxs-lookup"><span data-stu-id="20c3e-115">Hiding a UI element does not mean a user cannot access it.</span></span> <span data-ttu-id="20c3e-116">你还必须向用户授权控制器代码中。</span><span class="sxs-lookup"><span data-stu-id="20c3e-116">You must also authorize the user within your controller code.</span></span>
