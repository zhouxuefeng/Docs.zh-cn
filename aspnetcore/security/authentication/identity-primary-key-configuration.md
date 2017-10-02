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
ms.sourcegitcommit: 13291956ad858d465dfd46caa384df58f08e286b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2017
---
# <a name="configure-the-aspnet-core-identity-primary-key-data-type"></a><span data-ttu-id="191e2-104">配置 ASP.NET 核心标识主键数据类型</span><span class="sxs-lookup"><span data-stu-id="191e2-104">Configure the ASP.NET Core Identity primary key data type</span></span>

<span data-ttu-id="191e2-105">ASP.NET 核心标识可以配置用于表示为主键的数据类型。</span><span class="sxs-lookup"><span data-stu-id="191e2-105">ASP.NET Core Identity allows you to configure the data type used to represent a primary key.</span></span> <span data-ttu-id="191e2-106">标识使用`string`默认的数据类型。</span><span class="sxs-lookup"><span data-stu-id="191e2-106">Identity uses the `string` data type by default.</span></span> <span data-ttu-id="191e2-107">你可以重写此行为。</span><span class="sxs-lookup"><span data-stu-id="191e2-107">You can override this behavior.</span></span>

## <a name="customize-the-primary-key-data-type"></a><span data-ttu-id="191e2-108">自定义的主键数据类型</span><span class="sxs-lookup"><span data-stu-id="191e2-108">Customize the primary key data type</span></span>

1. <span data-ttu-id="191e2-109">创建的自定义实现[IdentityUser](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuser-1)类。</span><span class="sxs-lookup"><span data-stu-id="191e2-109">Create a custom implementation of the [IdentityUser](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuser-1) class.</span></span> <span data-ttu-id="191e2-110">它表示要用于创建用户对象的类型。</span><span class="sxs-lookup"><span data-stu-id="191e2-110">It represents the type to be used for creating user objects.</span></span> <span data-ttu-id="191e2-111">在下面的示例中，默认值`string`类型将替换`Guid`。</span><span class="sxs-lookup"><span data-stu-id="191e2-111">In the following example, the default `string` type is replaced with `Guid`.</span></span>

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationUser.cs?highlight=4&range=7-13)]

1. <span data-ttu-id="191e2-112">创建的自定义实现[IdentityRole](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.identityrole-1)类。</span><span class="sxs-lookup"><span data-stu-id="191e2-112">Create a custom implementation of the [IdentityRole](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.identityrole-1) class.</span></span> <span data-ttu-id="191e2-113">它表示要用于创建角色对象的类型。</span><span class="sxs-lookup"><span data-stu-id="191e2-113">It represents the type to be used for creating role objects.</span></span> <span data-ttu-id="191e2-114">在下面的示例中，默认值`string`类型将替换`Guid`。</span><span class="sxs-lookup"><span data-stu-id="191e2-114">In the following example, the default `string` type is replaced with `Guid`.</span></span>
    
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationRole.cs?highlight=3&range=7-12)]
    
1. <span data-ttu-id="191e2-115">创建一个自定义数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="191e2-115">Create a custom database context class.</span></span> <span data-ttu-id="191e2-116">它继承自用于标识的实体框架数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="191e2-116">It inherits from the Entity Framework database context class used for Identity.</span></span> <span data-ttu-id="191e2-117">`TUser`和`TRole`自变量引用分别在前一步骤中创建的自定义用户和角色类。</span><span class="sxs-lookup"><span data-stu-id="191e2-117">The `TUser` and `TRole` arguments reference the custom user and role classes created in the previous step, respectively.</span></span> <span data-ttu-id="191e2-118">`Guid`为主键定义的数据类型。</span><span class="sxs-lookup"><span data-stu-id="191e2-118">The `Guid` data type is defined for the primary key.</span></span>

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Data/ApplicationDbContext.cs?highlight=3&range=9-26)]
    
1. <span data-ttu-id="191e2-119">在应用程序的启动类添加身份验证服务时，请注册自定义数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="191e2-119">Register the custom database context class when adding the Identity service in the app's startup class.</span></span>

    # <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="191e2-120">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="191e2-120">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)
    
    <span data-ttu-id="191e2-121">`AddEntityFrameworkStores`方法不接受`TKey`自变量，因为它未在 ASP.NET Core 1.x。</span><span class="sxs-lookup"><span data-stu-id="191e2-121">The `AddEntityFrameworkStores` method doesn't accept a `TKey` argument as it did in ASP.NET Core 1.x.</span></span> <span data-ttu-id="191e2-122">主键的数据类型推断通过分析`DbContext`对象。</span><span class="sxs-lookup"><span data-stu-id="191e2-122">The primary key's data type is inferred by analyzing the `DbContext` object.</span></span>
    
    [!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=6-8&range=25-37)]
    
    # <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="191e2-123">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="191e2-123">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)
    
    <span data-ttu-id="191e2-124">`AddEntityFrameworkStores`方法接受`TKey`，该值指示主键的数据类型的自变量。</span><span class="sxs-lookup"><span data-stu-id="191e2-124">The `AddEntityFrameworkStores` method accepts a `TKey` argument indicating the primary key's data type.</span></span>
    
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=9-11&range=39-55)]
    
    ---

## <a name="test-the-changes"></a><span data-ttu-id="191e2-125">测试更改</span><span class="sxs-lookup"><span data-stu-id="191e2-125">Test the changes</span></span>

<span data-ttu-id="191e2-126">在完成配置更改，表示为主键的属性将反映新的数据类型。</span><span class="sxs-lookup"><span data-stu-id="191e2-126">Upon completion of the configuration changes, the property representing the primary key reflects the new data type.</span></span> <span data-ttu-id="191e2-127">下面的示例演示如何访问一个 MVC 控制器中的属性。</span><span class="sxs-lookup"><span data-stu-id="191e2-127">The following example demonstrates accessing the property in an MVC controller.</span></span>

[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Controllers/AccountController.cs?name=snippet_GetCurrentUserId&highlight=6)]
