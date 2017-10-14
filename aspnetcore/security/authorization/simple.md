---
title: "简单的授权"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 391bcaad-205f-43e4-badc-fa592d6f79f3
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/simple
ms.openlocfilehash: 91f402b1cbf73e212418d197a8a7230ce22e9e1d
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="simple-authorization"></a><span data-ttu-id="86cf6-103">简单的授权</span><span class="sxs-lookup"><span data-stu-id="86cf6-103">Simple Authorization</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="86cf6-104">通过控制在 MVC 中的授权`AuthorizeAttribute`属性和其各种参数。</span><span class="sxs-lookup"><span data-stu-id="86cf6-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="86cf6-105">在其最简单的应用`AuthorizeAttribute`属性设为到控制器的控制器或操作限制访问或对任何经过身份验证的用户的操作。</span><span class="sxs-lookup"><span data-stu-id="86cf6-105">At its simplest applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="86cf6-106">例如，下面的代码可访问的限制`AccountController`到任何经过身份验证的用户。</span><span class="sxs-lookup"><span data-stu-id="86cf6-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

```csharp
[Authorize]
   public class AccountController : Controller
   {
       public ActionResult Login()
       {
       }

       public ActionResult Logout()
       {
       }
   }
   ```

<span data-ttu-id="86cf6-107">如果你想要只适用于操作，而不是控制器的授权应用`AuthorizeAttribute`属性设为的操作，然后重试。</span><span class="sxs-lookup"><span data-stu-id="86cf6-107">If you want to apply authorization to an action rather than the controller simply apply the `AuthorizeAttribute` attribute to the action itself;</span></span>

```csharp
public class AccountController : Controller
   {
       public ActionResult Login()
       {
       }

       [Authorize]
       public ActionResult Logout()
       {
       }
   }
   ```

<span data-ttu-id="86cf6-108">现在只有经过身份验证的用户才能访问注销函数。</span><span class="sxs-lookup"><span data-stu-id="86cf6-108">Now only authenticated users can access the logout function.</span></span>

<span data-ttu-id="86cf6-109">你还可以使用`AllowAnonymousAttribute`特性以允许对各个操作; 未经身份验证的用户访问例如</span><span class="sxs-lookup"><span data-stu-id="86cf6-109">You can also use the `AllowAnonymousAttribute` attribute to allow access by non-authenticated users to individual actions; for example</span></span>

```csharp
[Authorize]
   public class AccountController : Controller
   {
       [AllowAnonymous]
       public ActionResult Login()
       {
       }

       public ActionResult Logout()
       {
       }
   }
   ```

<span data-ttu-id="86cf6-110">这将允许仅经过身份验证的用户到`AccountController`，除`Login`操作，这是可访问的所有用户，而不考虑其经过身份验证或未经身份验证 / 匿名状态。</span><span class="sxs-lookup"><span data-stu-id="86cf6-110">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

>[!WARNING]
> <span data-ttu-id="86cf6-111">`[AllowAnonymous]`绕过所有授权语句。</span><span class="sxs-lookup"><span data-stu-id="86cf6-111">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="86cf6-112">如果将应用组合`[AllowAnonymous]`和任何`[Authorize]`属性然后 Authorize 属性将始终忽略。</span><span class="sxs-lookup"><span data-stu-id="86cf6-112">If you apply combine `[AllowAnonymous]` and any `[Authorize]` attribute then the Authorize attributes will always be ignored.</span></span> <span data-ttu-id="86cf6-113">例如，如果将应用`[AllowAnonymous]`在控制器级别任何`[Authorize]`特性在同一个控制器上，或其中的任何操作都将被忽略。</span><span class="sxs-lookup"><span data-stu-id="86cf6-113">For example if you apply `[AllowAnonymous]` at the controller level any `[Authorize]` attributes on the same controller, or on any action within it will be ignored.</span></span>
