---
title: "ASP.NET 核心标识的自定义的存储提供程序 |Microsoft 文档"
author: ardalis
description: "如何配置 ASP.NET 核心标识的自定义存储提供程序。"
keywords: "ASP.NET 核心，标识，自定义存储提供程序"
ms.author: riande
manager: wpickett
ms.date: 05/24/2017
ms.topic: article
ms.assetid: b2ace545-ecf6-4664-b31e-b65bd4a6b025
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: 214343c9b964baca3c436966d39caede1a58aba2
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a><span data-ttu-id="ecbaf-104">ASP.NET 核心标识的自定义的存储提供程序</span><span class="sxs-lookup"><span data-stu-id="ecbaf-104">Custom storage providers for ASP.NET Core Identity</span></span>

<span data-ttu-id="ecbaf-105">通过[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ecbaf-105">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ecbaf-106">ASP.NET 核心标识是一种可扩展系统，可用于创建自定义存储提供程序并将其连接到你的应用。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-106">ASP.NET Core Identity is an extensible system which enables you to create a custom storage provider and connect it to your app.</span></span> <span data-ttu-id="ecbaf-107">本主题介绍如何创建 ASP.NET 核心标识的自定义的存储提供程序。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-107">This topic describes how to create a customized storage provider for ASP.NET Core Identity.</span></span> <span data-ttu-id="ecbaf-108">它介绍如何创建你自己的存储提供程序的重要概念，但不是的分步演练。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-108">It covers the important concepts for creating your own storage provider, but is not a step-by-step walkthrough.</span></span>

<span data-ttu-id="ecbaf-109">[查看或从 GitHub 下载示例](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/identity/sample)。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-109">[View or download sample from GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/identity/sample).</span></span>

## <a name="introduction"></a><span data-ttu-id="ecbaf-110">介绍</span><span class="sxs-lookup"><span data-stu-id="ecbaf-110">Introduction</span></span>

<span data-ttu-id="ecbaf-111">默认情况下，ASP.NET 核心标识系统中使用实体框架核心的 SQL Server 数据库存储用户信息。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-111">By default, the ASP.NET Core Identity system stores user information in a SQL Server database using Entity Framework Core.</span></span> <span data-ttu-id="ecbaf-112">对于许多应用程序，此方法也适用。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-112">For many apps, this approach works well.</span></span> <span data-ttu-id="ecbaf-113">但是，你可能希望使用不同的持久性机制或数据架构。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-113">However, you may prefer to use a different persistence mechanism or data schema.</span></span> <span data-ttu-id="ecbaf-114">例如: </span><span class="sxs-lookup"><span data-stu-id="ecbaf-114">For example:</span></span>

* <span data-ttu-id="ecbaf-115">你使用[Azure 表存储](https://docs.microsoft.com/azure/storage/)或其他数据存储。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-115">You use [Azure Table Storage](https://docs.microsoft.com/azure/storage/) or another data store.</span></span>
* <span data-ttu-id="ecbaf-116">数据库表具有不同的结构。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-116">Your database tables have a different structure.</span></span> 
* <span data-ttu-id="ecbaf-117">你可能想要使用不同的数据访问方法，如[Dapper](https://github.com/StackExchange/Dapper)。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-117">You may wish to use a different data access approach, such as [Dapper](https://github.com/StackExchange/Dapper).</span></span> 

<span data-ttu-id="ecbaf-118">在每个这种情况下，你可以为你的存储机制编写自定义提供程序并将该提供程序插入您的应用程序。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-118">In each of these cases, you can write a customized provider for your storage mechanism and plug that provider into your app.</span></span>

<span data-ttu-id="ecbaf-119">ASP.NET 核心标识包含在 Visual Studio 中使用"单个用户帐户"选项的项目模板中。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-119">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="ecbaf-120">在使用.NET 核心 CLI 时，添加`-au Individual`:</span><span class="sxs-lookup"><span data-stu-id="ecbaf-120">When using the .NET Core CLI, add `-au Individual`:</span></span>

```
dotnet new mvc -au Individual
dotnet new webapi -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a><span data-ttu-id="ecbaf-121">ASP.NET 核心标识体系结构</span><span class="sxs-lookup"><span data-stu-id="ecbaf-121">The ASP.NET Core Identity architecture</span></span>

<span data-ttu-id="ecbaf-122">ASP.NET 核心标识包含类称为管理器和存储区。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-122">ASP.NET Core Identity consists of classes called managers and stores.</span></span> <span data-ttu-id="ecbaf-123">*管理器*是高级类应用程序开发人员用于执行操作，如创建标识用户。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-123">*Managers* are high-level classes which an app developer uses to perform operations, such as creating an Identity user.</span></span> <span data-ttu-id="ecbaf-124">*存储*是用于指定如何保持实体，如用户和角色，较低级别类。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-124">*Stores* are lower-level classes that specify how entities, such as users and roles, are persisted.</span></span> <span data-ttu-id="ecbaf-125">存储按照[存储库模式](http://deviq.com/repository-pattern/)和紧密耦合持久性的机制。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-125">Stores follow the [repository pattern](http://deviq.com/repository-pattern/) and are closely coupled with the persistence mechanism.</span></span> <span data-ttu-id="ecbaf-126">管理器是相互脱耦存储区，这意味着你可以将持久性机制，而无需更改应用程序代码 （除外配置）。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-126">Managers are decoupled from stores, which means you can replace the persistence mechanism without changing your application code (except for configuration).</span></span>

<span data-ttu-id="ecbaf-127">下图显示的 web 应用程序的交互方式具有管理器中，当与数据访问层的存储区交互时。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-127">The following diagram shows how a web app interacts with the managers, while stores interact with the data access layer.</span></span>

![ASP.NET Core 应用适用于管理器 （例如，UserManager、 RoleManager）。](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

<span data-ttu-id="ecbaf-130">若要创建自定义的存储提供程序，请使用此数据访问层 （绿色和灰色框在上图中） 创建数据源、 数据访问层和交互的存储类。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-130">To create a custom storage provider, create the data source, the data access layer, and the store classes that interact with this data access layer (the green and grey boxes in the diagram above).</span></span> <span data-ttu-id="ecbaf-131">你不需要自定义管理器或与它们 （蓝色上面的框） 进行交互的应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-131">You don't need to customize the managers or your app code that interacts with them (the blue boxes above).</span></span>

<span data-ttu-id="ecbaf-132">创建的新实例时`UserManager`或`RoleManager`提供用户类的类型和作为参数传递存储类的实例。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-132">When creating a new instance of `UserManager` or `RoleManager` you provide the type of the user class and pass an instance of the store class as an argument.</span></span> <span data-ttu-id="ecbaf-133">此方法使您可以插入到 ASP.NET 核心的自定义的类。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-133">This approach enables you to plug your customized classes into ASP.NET Core.</span></span> 

<span data-ttu-id="ecbaf-134">[重新配置应用程序以使用新存储提供程序](#reconfigure-app-to-use-new-storage-provider)演示如何实例化`UserManager`和`RoleManager`使用自定义存储。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-134">[Reconfigure app to use new storage provider](#reconfigure-app-to-use-new-storage-provider) shows how to instantiate `UserManager` and `RoleManager` with a customized store.</span></span>

## <a name="aspnet-core-identity-stores-data-types"></a><span data-ttu-id="ecbaf-135">ASP.NET 核心标识将存储的数据类型</span><span class="sxs-lookup"><span data-stu-id="ecbaf-135">ASP.NET Core Identity stores data types</span></span>

<span data-ttu-id="ecbaf-136">[ASP.NET 核心标识](https://github.com/aspnet/identity)数据类型进行详细介绍下列各节：</span><span class="sxs-lookup"><span data-stu-id="ecbaf-136">[ASP.NET Core Identity](https://github.com/aspnet/identity) data types are detailed in the following sections:</span></span>

### <a name="users"></a><span data-ttu-id="ecbaf-137">Users</span><span class="sxs-lookup"><span data-stu-id="ecbaf-137">Users</span></span>

<span data-ttu-id="ecbaf-138">注册用户的网站的用户。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-138">Registered users of your web site.</span></span> <span data-ttu-id="ecbaf-139">[IdentityUser](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.identityuser)可能扩展类型，或将其用作自定义类型的示例。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-139">The [IdentityUser](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.identityuser) type may be extended or used as an example for your own custom type.</span></span> <span data-ttu-id="ecbaf-140">不需要从特定的类型来实现自定义标识的存储解决方案继承。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-140">You do not need to inherit from a particular type to implement your own custom identity storage solution.</span></span>

### <a name="user-claims"></a><span data-ttu-id="ecbaf-141">用户声明</span><span class="sxs-lookup"><span data-stu-id="ecbaf-141">User Claims</span></span>

<span data-ttu-id="ecbaf-142">一组语句 (或[声明](https://docs.microsoft.com//dotnet/api/system.security.claims.claim)有关的用户的表示用户的标识。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-142">A set of statements (or [Claims](https://docs.microsoft.com//dotnet/api/system.security.claims.claim) about the user that represent the user's identity.</span></span> <span data-ttu-id="ecbaf-143">可以启用的用户的标识不是可以通过角色实现更大的表达式。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-143">Can enable greater expression of the user's identity than can be achieved through roles.</span></span>

### <a name="user-logins"></a><span data-ttu-id="ecbaf-144">用户登录名</span><span class="sxs-lookup"><span data-stu-id="ecbaf-144">User Logins</span></span>

<span data-ttu-id="ecbaf-145">有关外部身份验证提供程序 （如 Facebook 或 Microsoft 帐户） 的信息在用户日志记录时使用。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-145">Information about the external authentication provider (like Facebook or a Microsoft account) to use when logging in a user.</span></span> [<span data-ttu-id="ecbaf-146">示例</span><span class="sxs-lookup"><span data-stu-id="ecbaf-146">Example</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a><span data-ttu-id="ecbaf-147">角色</span><span class="sxs-lookup"><span data-stu-id="ecbaf-147">Roles</span></span>

<span data-ttu-id="ecbaf-148">你的站点的授权组。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-148">Authorization groups for your site.</span></span> <span data-ttu-id="ecbaf-149">包括 （如"Admin"或"Employee"） 的角色 Id 和角色名称。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-149">Includes the role Id and role name (like "Admin" or "Employee").</span></span> [<span data-ttu-id="ecbaf-150">示例</span><span class="sxs-lookup"><span data-stu-id="ecbaf-150">Example</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a><span data-ttu-id="ecbaf-151">数据访问层</span><span class="sxs-lookup"><span data-stu-id="ecbaf-151">The data access layer</span></span>

<span data-ttu-id="ecbaf-152">本主题假定你熟悉要使用的持久性机制以及如何创建该机制的实体。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-152">This topic assumes you are familiar with the persistence mechanism that you are going to use and how to create entities for that mechanism.</span></span> <span data-ttu-id="ecbaf-153">本主题不提供有关如何创建存储库或数据访问类; 详细信息使用 ASP.NET 核心标识时，它提供了有关设计决策的一些建议。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-153">This topic does not provide details about how to create the repositories or data access classes; it provides some suggestions about design decisions when working with ASP.NET Core Identity.</span></span>

<span data-ttu-id="ecbaf-154">为自定义的存储提供程序设计的数据访问层时，你有大量的自由。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-154">You have a lot of freedom when designing the data access layer for a customized store provider.</span></span> <span data-ttu-id="ecbaf-155">只需创建想要使用它在你的应用程序的功能的持久性机制。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-155">You only need to create persistence mechanisms for features that you intend to use in your app.</span></span> <span data-ttu-id="ecbaf-156">例如，如果不使用你的应用程序中的角色，你不必创建角色或用户角色关联的存储。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-156">For example, if you are not using roles in your app, you do not need to create storage for roles or user role associations.</span></span> <span data-ttu-id="ecbaf-157">你的技术和现有基础结构可能需要的 ASP.NET 核心标识的默认实现不同的结构。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-157">Your technology and existing infrastructure may require a structure that is very different from the default implementation of ASP.NET Core Identity.</span></span> <span data-ttu-id="ecbaf-158">你的数据访问层，可以提供要使用的存储实现结构的逻辑。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-158">In your data access layer, you provide the logic to work with the structure of your storage implementation.</span></span>

<span data-ttu-id="ecbaf-159">数据访问层提供逻辑来将数据从 ASP.NET 核心标识保存到数据源。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-159">The data access layer provides the logic to save the data from ASP.NET Core Identity to a data source.</span></span> <span data-ttu-id="ecbaf-160">自定义的存储提供程序的数据访问层可能包括以下类用于存储用户和角色信息。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-160">The data access layer for your customized storage provider might include the following classes to store user and role information.</span></span>

### <a name="context-class"></a><span data-ttu-id="ecbaf-161">Context 类</span><span class="sxs-lookup"><span data-stu-id="ecbaf-161">Context class</span></span>

<span data-ttu-id="ecbaf-162">封装用于连接到持久性机制和执行查询的信息。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-162">Encapsulates the information to connect to your persistence mechanism and execute queries.</span></span> <span data-ttu-id="ecbaf-163">多个数据类需要通过依赖关系注入通常提供此类的实例。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-163">Several data classes require an instance of this class, typically provided through dependency injection.</span></span> <span data-ttu-id="ecbaf-164">[示例](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1)。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-164">[Example](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1).</span></span>

### <a name="user-storage"></a><span data-ttu-id="ecbaf-165">用户存储</span><span class="sxs-lookup"><span data-stu-id="ecbaf-165">User Storage</span></span>

<span data-ttu-id="ecbaf-166">存储和检索用户信息 （如用户名称和密码哈希）。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-166">Stores and retrieves user information (such as user name and password hash).</span></span> [<span data-ttu-id="ecbaf-167">示例</span><span class="sxs-lookup"><span data-stu-id="ecbaf-167">Example</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a><span data-ttu-id="ecbaf-168">角色存储</span><span class="sxs-lookup"><span data-stu-id="ecbaf-168">Role Storage</span></span>

<span data-ttu-id="ecbaf-169">存储和检索角色信息 （如角色名称中）。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-169">Stores and retrieves role information (such as the role name).</span></span> [<span data-ttu-id="ecbaf-170">示例</span><span class="sxs-lookup"><span data-stu-id="ecbaf-170">Example</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a><span data-ttu-id="ecbaf-171">UserClaims 存储</span><span class="sxs-lookup"><span data-stu-id="ecbaf-171">UserClaims Storage</span></span>

<span data-ttu-id="ecbaf-172">存储和检索用户声明信息 （如的声明类型和值）。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-172">Stores and retrieves user claim information (such as the claim type and value).</span></span> [<span data-ttu-id="ecbaf-173">示例</span><span class="sxs-lookup"><span data-stu-id="ecbaf-173">Example</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a><span data-ttu-id="ecbaf-174">UserLogins 存储</span><span class="sxs-lookup"><span data-stu-id="ecbaf-174">UserLogins Storage</span></span>

<span data-ttu-id="ecbaf-175">存储和检索用户登录信息 （例如外部身份验证提供程序）。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-175">Stores and retrieves user login information (such as an external authentication provider).</span></span> [<span data-ttu-id="ecbaf-176">示例</span><span class="sxs-lookup"><span data-stu-id="ecbaf-176">Example</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a><span data-ttu-id="ecbaf-177">UserRole 存储</span><span class="sxs-lookup"><span data-stu-id="ecbaf-177">UserRole Storage</span></span>

<span data-ttu-id="ecbaf-178">存储和检索哪些角色分配给哪些用户。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-178">Stores and retrieves which roles are assigned to which users.</span></span> [<span data-ttu-id="ecbaf-179">示例</span><span class="sxs-lookup"><span data-stu-id="ecbaf-179">Example</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.userstore-1)

<span data-ttu-id="ecbaf-180">**提示：**仅实现你要在应用程序中使用的类。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-180">**TIP:** Only implement the classes you intend to use in your app.</span></span>

<span data-ttu-id="ecbaf-181">在数据访问类中，提供的代码来执行持久性机制的数据操作。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-181">In the data access classes, provide code to perform data operations for your persistence mechanism.</span></span> <span data-ttu-id="ecbaf-182">例如，在自定义提供程序，你可能具有以下代码以创建中的新用户*存储*类：</span><span class="sxs-lookup"><span data-stu-id="ecbaf-182">For example, within a custom provider, you might have the following code to create a new user in the *store* class:</span></span>

[!code-csharp[Main](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

<span data-ttu-id="ecbaf-183">实现逻辑用于创建用户处于``_usersTable.CreateAsync``方法，如下所示。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-183">The implementation logic for creating the user is in the ``_usersTable.CreateAsync`` method, shown below.</span></span>

## <a name="customize-the-user-class"></a><span data-ttu-id="ecbaf-184">自定义用户类</span><span class="sxs-lookup"><span data-stu-id="ecbaf-184">Customize the user class</span></span>

<span data-ttu-id="ecbaf-185">时实现存储提供程序，创建一个用户类，这等效于[`IdentityUser`类](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.identityuser)。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-185">When implementing a storage provider, create a user class which is equivalent to the [`IdentityUser` class](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.identityuser).</span></span>

<span data-ttu-id="ecbaf-186">您的用户类必须包含至少`Id`和`UserName`属性。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-186">At a minimum, your user class must include an `Id` and a `UserName` property.</span></span>

<span data-ttu-id="ecbaf-187">`IdentityUser`类定义的属性，``UserManager``调用时执行请求的操作。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-187">The `IdentityUser` class defines the properties that the ``UserManager`` calls when performing requested operations.</span></span> <span data-ttu-id="ecbaf-188">默认类型`Id`属性是一个字符串，但可以继承`IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>`并指定不同的类型。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-188">The default type of the `Id` property is a string, but you can inherit from `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` and specify a different type.</span></span> <span data-ttu-id="ecbaf-189">框架需要要处理的数据类型转换的存储实现。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-189">The framework expects the storage implementation to handle data type conversions.</span></span>

## <a name="customize-the-user-store"></a><span data-ttu-id="ecbaf-190">自定义用户存储区</span><span class="sxs-lookup"><span data-stu-id="ecbaf-190">Customize the user store</span></span>

<span data-ttu-id="ecbaf-191">创建`UserStore`提供用户的所有数据操作的方法的类。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-191">Create a `UserStore` class that provides the methods for all data operations on the user.</span></span> <span data-ttu-id="ecbaf-192">此类是等效于[UserStore<TUser> ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1)类。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-192">This class is equivalent to the [UserStore<TUser>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) class.</span></span> <span data-ttu-id="ecbaf-193">在你`UserStore`类中，实现`IUserStore<TUser>`和所需的可选接口。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-193">In your `UserStore` class, implement `IUserStore<TUser>` and the optional interfaces required.</span></span> <span data-ttu-id="ecbaf-194">你选择的可选接口，以实现基于应用程序中提供的功能。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-194">You select which optional interfaces to implement based on on the functionality provided in your app.</span></span>

### <a name="optional-interfaces"></a><span data-ttu-id="ecbaf-195">可选接口</span><span class="sxs-lookup"><span data-stu-id="ecbaf-195">Optional interfaces</span></span>

- <span data-ttu-id="ecbaf-196">IUserRoleStore https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserrolestore-1</span><span class="sxs-lookup"><span data-stu-id="ecbaf-196">IUserRoleStore https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserrolestore-1</span></span>
- <span data-ttu-id="ecbaf-197">IUserClaimStore https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserclaimstore-1</span><span class="sxs-lookup"><span data-stu-id="ecbaf-197">IUserClaimStore https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserclaimstore-1</span></span>
- <span data-ttu-id="ecbaf-198">IUserPasswordStore https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserpasswordstore-1</span><span class="sxs-lookup"><span data-stu-id="ecbaf-198">IUserPasswordStore https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserpasswordstore-1</span></span>
- <span data-ttu-id="ecbaf-199">IUserSecurityStampStore<!-- make these all links and remove / --></span><span class="sxs-lookup"><span data-stu-id="ecbaf-199">IUserSecurityStampStore <!-- make these all links and remove / --></span></span>
- <span data-ttu-id="ecbaf-200">IUserEmailStore</span><span class="sxs-lookup"><span data-stu-id="ecbaf-200">IUserEmailStore</span></span>
- <span data-ttu-id="ecbaf-201">IPhoneNumberStore</span><span class="sxs-lookup"><span data-stu-id="ecbaf-201">IPhoneNumberStore</span></span>
- <span data-ttu-id="ecbaf-202">IQueryableUserStore</span><span class="sxs-lookup"><span data-stu-id="ecbaf-202">IQueryableUserStore</span></span>
- <span data-ttu-id="ecbaf-203">IUserLoginStore</span><span class="sxs-lookup"><span data-stu-id="ecbaf-203">IUserLoginStore</span></span>
- <span data-ttu-id="ecbaf-204">IUserTwoFactorStore</span><span class="sxs-lookup"><span data-stu-id="ecbaf-204">IUserTwoFactorStore</span></span>
- <span data-ttu-id="ecbaf-205">IUserLockoutStore</span><span class="sxs-lookup"><span data-stu-id="ecbaf-205">IUserLockoutStore</span></span>

<span data-ttu-id="ecbaf-206">可选接口继承自`IUserStore`。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-206">The optional interfaces inherit from `IUserStore`.</span></span> <span data-ttu-id="ecbaf-207">你可以看到存储部分实现的示例用户[此处](https://github.com/aspnet/Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs)。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-207">You can see a partially implemented sample user store [here](https://github.com/aspnet/Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs).</span></span>

<span data-ttu-id="ecbaf-208">在`UserStore`类，你将使用你创建用于执行操作的数据访问类。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-208">Within the `UserStore` class, you use the data access classes that you created to perform operations.</span></span> <span data-ttu-id="ecbaf-209">它们将传递中使用依赖关系注入。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-209">These are passed in using dependency injection.</span></span> <span data-ttu-id="ecbaf-210">例如，在 SQL Server 使用 Dapper 实现，`UserStore`类具有`CreateAsync`使用的实例方法`DapperUsersTable`以插入新记录：</span><span class="sxs-lookup"><span data-stu-id="ecbaf-210">For example, in the SQL Server with Dapper implementation, the `UserStore` class has the `CreateAsync` method which uses an instance of `DapperUsersTable` to insert a new record:</span></span>

[!code-csharp[Main](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a><span data-ttu-id="ecbaf-211">要实现自定义用户存储时的接口</span><span class="sxs-lookup"><span data-stu-id="ecbaf-211">Interfaces to implement when customizing user store</span></span>

- <span data-ttu-id="ecbaf-212">**IUserStore**</span><span class="sxs-lookup"><span data-stu-id="ecbaf-212">**IUserStore**</span></span>  
 <span data-ttu-id="ecbaf-213">[IUserStore&lt;热熔器&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserstore-1)接口是在用户存储中必须实现的唯一接口。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-213">The [IUserStore&lt;TUser&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserstore-1) interface is the only interface you must implement in the user store.</span></span> <span data-ttu-id="ecbaf-214">它定义了用于创建、 更新、 删除和检索用户的方法。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-214">It defines methods for creating, updating, deleting, and retrieving users.</span></span>
- <span data-ttu-id="ecbaf-215">**IUserClaimStore**</span><span class="sxs-lookup"><span data-stu-id="ecbaf-215">**IUserClaimStore**</span></span>  
 <span data-ttu-id="ecbaf-216">[IUserClaimStore&lt;热熔器&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserclaimstore-1)接口定义实现以启用用户声明的方法。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-216">The [IUserClaimStore&lt;TUser&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserclaimstore-1) interface defines the methods you implement to enable user claims.</span></span> <span data-ttu-id="ecbaf-217">它包含用于添加、 删除和检索用户声明的方法。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-217">It contains methods for adding, removing and retrieving user claims.</span></span>
- <span data-ttu-id="ecbaf-218">**IUserLoginStore**</span><span class="sxs-lookup"><span data-stu-id="ecbaf-218">**IUserLoginStore**</span></span>  
 <span data-ttu-id="ecbaf-219">[IUserLoginStore&lt;热熔器&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserloginstore-1)定义实现以启用外部身份验证提供程序的方法。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-219">The [IUserLoginStore&lt;TUser&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserloginstore-1) defines the methods you implement to enable external authentication providers.</span></span> <span data-ttu-id="ecbaf-220">它包含用于添加、 删除和检索用户登录名和用于检索用户的登录信息基于方法的方法。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-220">It contains methods for adding, removing and retrieving user logins, and a method for retrieving a user based on the login information.</span></span>
- <span data-ttu-id="ecbaf-221">**IUserRoleStore**</span><span class="sxs-lookup"><span data-stu-id="ecbaf-221">**IUserRoleStore**</span></span>  
 <span data-ttu-id="ecbaf-222">[IUserRoleStore&lt;热熔器&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserrolestore-1)接口定义实现的用户映射到角色的方法。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-222">The [IUserRoleStore&lt;TUser&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserrolestore-1) interface defines the methods you implement to map a user to a role.</span></span> <span data-ttu-id="ecbaf-223">它包含要添加、 删除和检索用户的角色和方法来检查是否将用户分配到角色的方法。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-223">It contains methods to add, remove, and retrieve a user's roles, and a method to check if a user is assigned to a role.</span></span>
- <span data-ttu-id="ecbaf-224">**IUserPasswordStore**</span><span class="sxs-lookup"><span data-stu-id="ecbaf-224">**IUserPasswordStore**</span></span>  
 <span data-ttu-id="ecbaf-225">[IUserPasswordStore&lt;热熔器&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)接口定义实现以保持经过哈希处理的密码的方法。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-225">The [IUserPasswordStore&lt;TUser&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) interface defines the methods you implement to persist hashed passwords.</span></span> <span data-ttu-id="ecbaf-226">它包含用于获取和设置工作经过哈希处理的密码，以及用于指示用户是否已设置密码的方法的方法。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-226">It contains methods for getting and setting the hashed password, and a method that indicates whether the user has set a password.</span></span>
- <span data-ttu-id="ecbaf-227">**IUserSecurityStampStore**</span><span class="sxs-lookup"><span data-stu-id="ecbaf-227">**IUserSecurityStampStore**</span></span>  
 <span data-ttu-id="ecbaf-228">[IUserSecurityStampStore&lt;热熔器&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)接口定义的方法实现用于安全戳，该值指示是否已更改用户的帐户信息。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-228">The [IUserSecurityStampStore&lt;TUser&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) interface defines the methods you implement to use a security stamp for indicating whether the user's account information has changed.</span></span> <span data-ttu-id="ecbaf-229">当用户更改密码，或添加或删除登录名，将更新此 stamp。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-229">This stamp is updated when a user changes the password, or adds or removes logins.</span></span> <span data-ttu-id="ecbaf-230">它包含用于获取和设置安全戳的方法。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-230">It contains methods for getting and setting the security stamp.</span></span>
- <span data-ttu-id="ecbaf-231">**IUserTwoFactorStore**</span><span class="sxs-lookup"><span data-stu-id="ecbaf-231">**IUserTwoFactorStore**</span></span>  
 <span data-ttu-id="ecbaf-232">[IUserTwoFactorStore&lt;热熔器&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)接口定义实现以支持两个因素身份验证的方法。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-232">The [IUserTwoFactorStore&lt;TUser&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) interface defines the methods you implement to support two factor authentication.</span></span> <span data-ttu-id="ecbaf-233">它包含用于获取和设置是否为用户启用双重身份验证的方法。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-233">It contains methods for getting and setting whether two factor authentication is enabled for a user.</span></span>
- <span data-ttu-id="ecbaf-234">**IUserPhoneNumberStore**</span><span class="sxs-lookup"><span data-stu-id="ecbaf-234">**IUserPhoneNumberStore**</span></span>  
 <span data-ttu-id="ecbaf-235">[IUserPhoneNumberStore&lt;热熔器&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)接口定义实现以存储用户电话号码的方法。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-235">The [IUserPhoneNumberStore&lt;TUser&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) interface defines the methods you implement to store user phone numbers.</span></span> <span data-ttu-id="ecbaf-236">它包含用于获取和设置的电话号码和是否确认的电话号码的方法。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-236">It contains methods for getting and setting the phone number and whether the phone number is confirmed.</span></span>
- <span data-ttu-id="ecbaf-237">**IUserEmailStore**</span><span class="sxs-lookup"><span data-stu-id="ecbaf-237">**IUserEmailStore**</span></span>  
 <span data-ttu-id="ecbaf-238">[IUserEmailStore&lt;热熔器&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuseremailstore-1)接口定义实现以存储用户电子邮件地址的方法。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-238">The [IUserEmailStore&lt;TUser&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuseremailstore-1) interface defines the methods you implement to store user email addresses.</span></span> <span data-ttu-id="ecbaf-239">它包含用于获取和设置的电子邮件地址和是否确认电子邮件的方法。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-239">It contains methods for getting and setting the email address and whether the email is confirmed.</span></span>
- <span data-ttu-id="ecbaf-240">**IUserLockoutStore**</span><span class="sxs-lookup"><span data-stu-id="ecbaf-240">**IUserLockoutStore**</span></span>  
 <span data-ttu-id="ecbaf-241">[IUserLockoutStore&lt;热熔器&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)接口定义为存储帐户的锁定信息而实现的方法。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-241">The [IUserLockoutStore&lt;TUser&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) interface defines the methods you implement to store information about locking an account.</span></span> <span data-ttu-id="ecbaf-242">它包含用于跟踪未成功的访问和锁定方法。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-242">It contains methods for tracking failed access attempts and lockouts.</span></span>
- <span data-ttu-id="ecbaf-243">**IQueryableUserStore**</span><span class="sxs-lookup"><span data-stu-id="ecbaf-243">**IQueryableUserStore**</span></span>  
 <span data-ttu-id="ecbaf-244">[IQueryableUserStore&lt;热熔器&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)接口定义成员实现此方法可以提供可查询的用户存储区。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-244">The [IQueryableUserStore&lt;TUser&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) interface defines the members implement to provide a queryable user store.</span></span>

<span data-ttu-id="ecbaf-245">应用程序中实现所需的接口。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-245">You implement only the interfaces that are needed in your app.</span></span> <span data-ttu-id="ecbaf-246">例如: </span><span class="sxs-lookup"><span data-stu-id="ecbaf-246">For example:</span></span>

```csharp
public class UserStore : IUserStore<IdentityUser>,
                         IUserClaimStore<IdentityUser>,
                         IUserLoginStore<IdentityUser>,
                         IUserRoleStore<IdentityUser>,
                         IUserPasswordStore<IdentityUser>,
                         IUserSecurityStampStore<IdentityUser>
{
    // interface implementations not shown
}
```

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a><span data-ttu-id="ecbaf-247">IdentityUserClaim、 IdentityUserLogin 和 IdentityUserRole</span><span class="sxs-lookup"><span data-stu-id="ecbaf-247">IdentityUserClaim, IdentityUserLogin, and IdentityUserRole</span></span>

<span data-ttu-id="ecbaf-248">``Microsoft.AspNet.Identity.EntityFramework``命名空间包含的实现[IdentityUserClaim](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1)， [IdentityUserLogin](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.identityuserlogin)，和[IdentityUserRole](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1)类。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-248">The ``Microsoft.AspNet.Identity.EntityFramework`` namespace contains implementations of the [IdentityUserClaim](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [IdentityUserLogin](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.identityuserlogin), and [IdentityUserRole](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) classes.</span></span> <span data-ttu-id="ecbaf-249">如果你正使用这些功能，你可能想要创建你自己版本的这些类并定义你的应用程序的属性。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-249">If you are using these features, you may want to create your own versions of these classes and define the properties for your app.</span></span> <span data-ttu-id="ecbaf-250">但是，有时它会更加高效，若要执行基本操作 （如添加或删除用户的声明） 时不将这些实体加载到内存。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-250">However, sometimes it is more efficient to not load these entities into memory when performing basic operations (such as adding or removing a user's claim).</span></span> <span data-ttu-id="ecbaf-251">相反后, 端存储类可以执行这些操作直接在数据源上。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-251">Instead, the backend store classes can execute these operations directly on the data source.</span></span> <span data-ttu-id="ecbaf-252">例如，``UserStore.GetClaimsAsync``方法可以调用``userClaimTable.FindByUserId(user.Id)``方法上执行查询，它直接表并返回的声明列表。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-252">For example, the ``UserStore.GetClaimsAsync`` method can call the ``userClaimTable.FindByUserId(user.Id)`` method to execute a query on that table directly and return a list of claims.</span></span>

## <a name="customize-the-role-class"></a><span data-ttu-id="ecbaf-253">自定义角色类</span><span class="sxs-lookup"><span data-stu-id="ecbaf-253">Customize the role class</span></span>

<span data-ttu-id="ecbaf-254">在实现角色存储提供程序时，你可以创建自定义角色类型。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-254">When implementing a role storage provider, you can create a custom role type.</span></span> <span data-ttu-id="ecbaf-255">它不需要实现特定接口，但是它必须具有`Id`，其中包含通常`Name`属性。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-255">It need not implement a particular interface, but it must have an `Id` and typically it will have a `Name` property.</span></span>

<span data-ttu-id="ecbaf-256">以下是一个示例角色类：</span><span class="sxs-lookup"><span data-stu-id="ecbaf-256">The following is an example role class:</span></span>

[!code-csharp[Main](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a><span data-ttu-id="ecbaf-257">自定义角色存储</span><span class="sxs-lookup"><span data-stu-id="ecbaf-257">Customize the role store</span></span>

<span data-ttu-id="ecbaf-258">你可以创建``RoleStore``提供在角色上的所有数据操作的方法的类。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-258">You can create a ``RoleStore`` class that provides the methods for all data operations on roles.</span></span> <span data-ttu-id="ecbaf-259">此类是等效于[RoleStore<TRole> ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)类。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-259">This class is equivalent to the [RoleStore<TRole>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) class.</span></span> <span data-ttu-id="ecbaf-260">在`RoleStore`类时，实现``IRoleStore<TRole>``和 （可选）``IQueryableRoleStore<TRole>``接口。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-260">In the `RoleStore` class, you implement the ``IRoleStore<TRole>`` and optionally the ``IQueryableRoleStore<TRole>`` interface.</span></span>

- <span data-ttu-id="ecbaf-261">**IRoleStore&lt;TRole&gt;**</span><span class="sxs-lookup"><span data-stu-id="ecbaf-261">**IRoleStore&lt;TRole&gt;**</span></span>  
 <span data-ttu-id="ecbaf-262">[IRoleStore](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.irolestore-1)接口定义角色存储类中实现的方法。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-262">The [IRoleStore](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.irolestore-1) interface defines the methods to implement in the role store class.</span></span> <span data-ttu-id="ecbaf-263">它包含用于创建、 更新、 删除和检索角色的方法。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-263">It contains methods for creating, updating, deleting and retrieving roles.</span></span>
- <span data-ttu-id="ecbaf-264">**RoleStore&lt;TRole&gt;**</span><span class="sxs-lookup"><span data-stu-id="ecbaf-264">**RoleStore&lt;TRole&gt;**</span></span>  
 <span data-ttu-id="ecbaf-265">若要自定义`RoleStore`，创建一个类以实现`IRoleStore`接口。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-265">To customize `RoleStore`, create a class that implements the `IRoleStore` interface.</span></span> 

## <a name="reconfigure-app-to-use-new-storage-provider"></a><span data-ttu-id="ecbaf-266">重新配置应用程序以使用新存储提供程序</span><span class="sxs-lookup"><span data-stu-id="ecbaf-266">Reconfigure app to use new storage provider</span></span>

<span data-ttu-id="ecbaf-267">一旦你已实现的存储提供程序，你将配置您的应用程序使用它。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-267">Once you have implemented a storage provider, you configure your app to use it.</span></span> <span data-ttu-id="ecbaf-268">如果你的应用程序使用默认的提供程序，可将其替换你自定义提供程序上。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-268">If your app used the default provider, replace it with your custom provider.</span></span>

1. <span data-ttu-id="ecbaf-269">删除`Microsoft.AspNetCore.EntityFramework.Identity`NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-269">Remove the `Microsoft.AspNetCore.EntityFramework.Identity` NuGet package.</span></span>
1. <span data-ttu-id="ecbaf-270">如果存储提供程序驻留在单独的项目或包，添加对它的引用。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-270">If the storage provider resides in a separate project or package, add a reference to it.</span></span>
1. <span data-ttu-id="ecbaf-271">将对所有引用`Microsoft.AspNetCore.EntityFramework.Identity`使用的命名空间的存储提供程序的语句。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-271">Replace all references to `Microsoft.AspNetCore.EntityFramework.Identity` with a using statement for the namespace of your storage provider.</span></span>
1. <span data-ttu-id="ecbaf-272">在``ConfigureServices``方法，更改`AddIdentity`要使用自定义类型的方法。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-272">In the ``ConfigureServices`` method, change the `AddIdentity` method to use your custom types.</span></span> <span data-ttu-id="ecbaf-273">你可以创建你自己的扩展方法实现此目的。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-273">You can create your own extension methods for this purpose.</span></span> <span data-ttu-id="ecbaf-274">请参阅[IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs)有关示例。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-274">See [IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) for an example.</span></span>
1. <span data-ttu-id="ecbaf-275">如果你使用的角色，更新`RoleManager`用于你`RoleStore`类。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-275">If you are using Roles, update the `RoleManager` to use your `RoleStore` class.</span></span>
1. <span data-ttu-id="ecbaf-276">到你的应用的配置更新的连接字符串和凭据。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-276">Update the connection string and credentials to your app's configuration.</span></span>

<span data-ttu-id="ecbaf-277">示例:</span><span class="sxs-lookup"><span data-stu-id="ecbaf-277">Example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add identity types
    services.AddIdentity<ApplicationUser, ApplicationRole>()
        .AddDefaultTokenProviders();

    // Identity Services
    services.AddTransient<IUserStore<ApplicationUser>, CustomUserStore>();
    services.AddTransient<IRoleStore<ApplicationRole>, CustomRoleStore>();
    string connectionString = Configuration.GetConnectionString("DefaultConnection");
    services.AddTransient<SqlConnection>(e => new SqlConnection(connectionString));
    services.AddTransient<DapperUsersTable>();

    // additional configuration
}
```

## <a name="references"></a><span data-ttu-id="ecbaf-278">参考资料</span><span class="sxs-lookup"><span data-stu-id="ecbaf-278">References</span></span>

- [<span data-ttu-id="ecbaf-279">ASP.NET 标识的自定义的存储提供程序</span><span class="sxs-lookup"><span data-stu-id="ecbaf-279">Custom Storage Providers for ASP.NET Identity</span></span>](https://docs.microsoft.com/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
- <span data-ttu-id="ecbaf-280">[ASP.NET 核心标识](https://github.com/aspnet/identity)-此存储库包含指向社区维护存储提供程序。</span><span class="sxs-lookup"><span data-stu-id="ecbaf-280">[ASP.NET Core Identity](https://github.com/aspnet/identity) - This repository includes links to community maintained store providers.</span></span>
