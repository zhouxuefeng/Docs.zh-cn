---
title: "配置 ASP.NET 核心标识"
author: AdrienTorris
description: "了解 ASP.NET 核心标识默认值，并配置要使用自定义值的各种标识属性。"
keywords: "ASP.NET 核心，标识、 身份验证安全性"
ms.author: scaddie
manager: wpickett
ms.date: 09/18/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/identity-configuration
ms.openlocfilehash: 629fcc2941b2d2fda9604a3eac04be3d0f5294b2
ms.sourcegitcommit: ddefc78270bd9b5ae0b1bd8de6c45f6977e7dceb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2017
---
# <a name="configure-identity"></a><span data-ttu-id="0683e-104">配置标识</span><span class="sxs-lookup"><span data-stu-id="0683e-104">Configure Identity</span></span>

<span data-ttu-id="0683e-105">ASP.NET 核心标识具有一些可以在应用程序的启动类中轻松地重写的默认行为。</span><span class="sxs-lookup"><span data-stu-id="0683e-105">ASP.NET Core Identity has some default behaviors that you can override easily in your application's startup class.</span></span>

## <a name="passwords-policy"></a><span data-ttu-id="0683e-106">密码策略</span><span class="sxs-lookup"><span data-stu-id="0683e-106">Passwords policy</span></span>

<span data-ttu-id="0683e-107">默认情况下，标识要求密码包含大写字符、 小写字符和数字。</span><span class="sxs-lookup"><span data-stu-id="0683e-107">By default, Identity requires that passwords contain an uppercase character, lowercase character, and digits.</span></span> <span data-ttu-id="0683e-108">此外，还有一些其他限制。</span><span class="sxs-lookup"><span data-stu-id="0683e-108">There are also some other restrictions.</span></span> <span data-ttu-id="0683e-109">如果你想要简化密码限制，你可以在你的应用程序的启动类中执行该操作。</span><span class="sxs-lookup"><span data-stu-id="0683e-109">If you want to simplify password restrictions, you can do that in the startup class of your application.</span></span>

[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=60-65)]

## <a name="applications-cookie-settings"></a><span data-ttu-id="0683e-110">应用程序的 cookie 设置</span><span class="sxs-lookup"><span data-stu-id="0683e-110">Application's cookie settings</span></span>

<span data-ttu-id="0683e-111">密码策略，如可以在启动类更改的应用程序的 cookie 的所有设置。</span><span class="sxs-lookup"><span data-stu-id="0683e-111">Like the passwords policy, all the settings of the application's cookie can be changed in the startup class.</span></span>

[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=72-80)]

## <a name="users-lockout"></a><span data-ttu-id="0683e-112">用户的锁定</span><span class="sxs-lookup"><span data-stu-id="0683e-112">User's lockout</span></span>

[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=67-70)]
