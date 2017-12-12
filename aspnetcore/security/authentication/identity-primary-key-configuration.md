---
title: "配置标识主键数据类型"
author: AdrienTorris
description: "本文概述了用于配置 ASP.NET 核心标识为主键使用的所需的数据类型的步骤。"
keywords: "ASP.NET 核心，标识，主键"
ms.author: scaddie
manager: wpickett
ms.date: 09/28/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/identity-primary-key-configuration
ms.openlocfilehash: 5734a9aa86fb2831bd054593ad41c3e3bda4729e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="configure-the-aspnet-core-identity-primary-key-data-type"></a>配置 ASP.NET 核心标识主键数据类型

ASP.NET 核心标识可以配置用于表示为主键的数据类型。 标识使用`string`默认的数据类型。 你可以重写此行为。

## <a name="customize-the-primary-key-data-type"></a>自定义的主键数据类型

1. 创建的自定义实现[IdentityUser](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuser-1)类。 它表示要用于创建用户对象的类型。 在下面的示例中，默认值`string`类型将替换`Guid`。

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationUser.cs?highlight=4&range=7-13)]

1. 创建的自定义实现[IdentityRole](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.identityrole-1)类。 它表示要用于创建角色对象的类型。 在下面的示例中，默认值`string`类型将替换`Guid`。
    
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationRole.cs?highlight=3&range=7-12)]
    
1. 创建一个自定义数据库上下文类。 它继承自用于标识的实体框架数据库上下文类。 `TUser`和`TRole`自变量引用分别在前一步骤中创建的自定义用户和角色类。 `Guid`为主键定义的数据类型。

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Data/ApplicationDbContext.cs?highlight=3&range=9-26)]
    
1. 在应用程序的启动类添加身份验证服务时，请注册自定义数据库上下文类。

    # <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)
    
    `AddEntityFrameworkStores`方法不接受`TKey`自变量，因为它未在 ASP.NET Core 1.x。 主键的数据类型推断通过分析`DbContext`对象。
    
    [!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=6-8&range=25-37)]
    
    # <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)
    
    `AddEntityFrameworkStores`方法接受`TKey`，该值指示主键的数据类型的自变量。
    
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=9-11&range=39-55)]
    
    ---

## <a name="test-the-changes"></a>测试更改

在完成配置更改，表示为主键的属性将反映新的数据类型。 下面的示例演示如何访问一个 MVC 控制器中的属性。

[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Controllers/AccountController.cs?name=snippet_GetCurrentUserId&highlight=6)]
