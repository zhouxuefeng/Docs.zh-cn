---
title: "配置标识"
uid: security/authentication/identity-configuration
ms.openlocfilehash: 7ccd89360a8c7f5c8c6dfac76df42898e18a116a
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="configure-identity"></a><span data-ttu-id="8d1f3-102">配置标识</span><span class="sxs-lookup"><span data-stu-id="8d1f3-102">Configure Identity</span></span>

<span data-ttu-id="8d1f3-103">ASP.NET 核心标识具有一些可以在应用程序的启动类中轻松地重写的默认行为。</span><span class="sxs-lookup"><span data-stu-id="8d1f3-103">ASP.NET Core Identity has some default behaviors that you can override easily in your application's startup class.</span></span>

## <a name="passwords-policy"></a><span data-ttu-id="8d1f3-104">密码策略</span><span class="sxs-lookup"><span data-stu-id="8d1f3-104">Passwords policy</span></span>

<span data-ttu-id="8d1f3-105">默认情况下，标识要求密码包含大写字符、 小写字符和数字。</span><span class="sxs-lookup"><span data-stu-id="8d1f3-105">By default, Identity requires that passwords contain an uppercase character, lowercase character, and digits.</span></span> <span data-ttu-id="8d1f3-106">此外，还有一些其他限制。</span><span class="sxs-lookup"><span data-stu-id="8d1f3-106">There are also some other restrictions.</span></span> <span data-ttu-id="8d1f3-107">如果你想要简化密码限制，你可以在你的应用程序的启动类中执行该操作。</span><span class="sxs-lookup"><span data-stu-id="8d1f3-107">If you want to simplify password restrictions, you can do that in the startup class of your application.</span></span>

<span data-ttu-id="8d1f3-108">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=60-65)]</span><span class="sxs-lookup"><span data-stu-id="8d1f3-108">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=60-65)]</span></span>

## <a name="applications-cookie-settings"></a><span data-ttu-id="8d1f3-109">应用程序的 cookie 设置</span><span class="sxs-lookup"><span data-stu-id="8d1f3-109">Application's cookie settings</span></span>

<span data-ttu-id="8d1f3-110">密码策略，如可以在启动类更改的应用程序的 cookie 的所有设置。</span><span class="sxs-lookup"><span data-stu-id="8d1f3-110">Like the passwords policy, all the settings of the application's cookie can be changed in the startup class.</span></span>

<span data-ttu-id="8d1f3-111">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=72-80)]</span><span class="sxs-lookup"><span data-stu-id="8d1f3-111">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=72-80)]</span></span>

## <a name="users-lockout"></a><span data-ttu-id="8d1f3-112">用户的锁定</span><span class="sxs-lookup"><span data-stu-id="8d1f3-112">User's lockout</span></span>

<span data-ttu-id="8d1f3-113">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=67-70)]</span><span class="sxs-lookup"><span data-stu-id="8d1f3-113">[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=67-70)]</span></span>
