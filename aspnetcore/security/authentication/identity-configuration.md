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
# <a name="configure-identity"></a>配置标识

ASP.NET 核心标识具有一些可以在应用程序的启动类中轻松地重写的默认行为。

## <a name="passwords-policy"></a>密码策略

默认情况下，标识要求密码包含大写字符、 小写字符和数字。 此外，还有一些其他限制。 如果你想要简化密码限制，你可以在你的应用程序的启动类中执行该操作。

[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=60-65)]

## <a name="applications-cookie-settings"></a>应用程序的 cookie 设置

密码策略，如可以在启动类更改的应用程序的 cookie 的所有设置。

[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=72-80)]

## <a name="users-lockout"></a>用户的锁定

[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=67-70)]
