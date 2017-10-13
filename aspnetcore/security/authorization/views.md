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
# <a name="view-based-authorization"></a>视图基于授权

<a name=security-authorization-views></a>

通常，开发人员将想要显示、 隐藏或修改基于当前的用户标识的用户界面。 你可以访问授权服务在服务内通过的 MVC 视图[依赖关系注入](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection)。 若要将授权服务注入到 Razor 视图使用`@inject`指令，例如`@inject IAuthorizationService AuthorizationService`(需要`@using Microsoft.AspNetCore.Authorization`)。 如果所需的每个视图中的授权服务则放置`@inject`指令插入`_ViewImports.cshtml`文件中`Views`目录。 有关依赖关系注入到视图的详细信息请参阅[到视图的依赖关系注入](../../mvc/views/dependency-injection.md)。

一旦你将具有插入的授权服务就使用它通过调用`AuthorizeAsync`中完全一样的方式将检查期间方法[基于资源的授权](resourcebased.md#security-authorization-resource-based-imperative)。

```cshtml
@if (await AuthorizationService.AuthorizeAsync(User, "PolicyName"))
   {
       <p>This paragraph is displayed because you fulfilled PolicyName.</p>
   }
   ```

在某些情况下的资源将为你的视图模型，并且你可以调用`AuthorizeAsync`中完全一样的方式将检查期间[基于资源的授权](resourcebased.md#security-authorization-resource-based-imperative);

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```cshtml
   @if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
   {
       <p><a class="btn btn-default" role="button"
           href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
   }
   ```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```cshtml
   @if (await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit))
   {
       <p><a class="btn btn-default" role="button"
           href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
   }
   ```
---

你可以看到模型作为资源授权应当考虑传递。

>[!WARNING]
>不要依赖于显示或隐藏你的 UI 作为唯一授权方法的部分。 隐藏用户界面元素并不意味着用户无法访问它。 你还必须向用户授权控制器代码中。
