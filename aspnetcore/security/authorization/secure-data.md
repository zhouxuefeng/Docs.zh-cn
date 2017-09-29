---
title: "使用受授权的用户数据创建 ASP.NET Core 应用"
author: rick-anderson
keywords: "ASP.NET 核心、 MVC，授权、 角色、 安全性、 管理员"
ms.author: riande
manager: wpickett
ms.date: 05/22/2017
ms.topic: article
ms.assetid: abeb2f8e-dfbf-4398-a04c-338a613a65bc
ms.technology: aspnet
ms.prod: aspnet-core
uid: security/authorization/secure-data
ms.openlocfilehash: 036ff9a682dc17ead991c85a9d5dd9c4b6a7d0c7
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2017
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="ac7fa-103">使用受授权的用户数据创建 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="ac7fa-103">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="ac7fa-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="ac7fa-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="ac7fa-105">本教程演示如何创建使用受授权的用户数据的 web 应用。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-105">This tutorial shows how to create a web app with user data protected by authorization.</span></span> <span data-ttu-id="ac7fa-106">它显示的身份验证的 （注册） 的用户的联系人列表已创建。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-106">It  displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="ac7fa-107">有三个安全组：</span><span class="sxs-lookup"><span data-stu-id="ac7fa-107">There are three security groups:</span></span>

* <span data-ttu-id="ac7fa-108">已注册的用户可以查看所有已批准的联系人数据。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-108">Registered users can view all the approved contact data.</span></span>
* <span data-ttu-id="ac7fa-109">已注册的用户可以编辑/删除其自己的数据。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-109">Registered users can edit/delete their own data.</span></span> 
* <span data-ttu-id="ac7fa-110">经理可以批准或拒绝联系人数据。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-110">Managers can approve or reject contact data.</span></span> <span data-ttu-id="ac7fa-111">仅批准的联系人是对用户可见。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-111">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="ac7fa-112">管理员可以批准/拒绝和编辑/删除任何数据。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-112">Administrators can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="ac7fa-113">在下图中，用户 Rick (`rick@example.com`) 登录。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-113">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="ac7fa-114">用户 Rick 只能查看批准联系人和编辑/删除其联系人。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-114">User Rick can only view approved contacts and edit/delete his contacts.</span></span> <span data-ttu-id="ac7fa-115">仅最新的记录，创建由 Rick、 显示编辑和删除链接</span><span class="sxs-lookup"><span data-stu-id="ac7fa-115">Only the last record, created by Rick, displays edit and delete links</span></span>

![上面所述的映像](secure-data/_static/rick.png)

<span data-ttu-id="ac7fa-117">在下图中，`manager@contoso.com`进行签名和管理员角色中。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-117">In the following image, `manager@contoso.com` is signed in and in the managers role.</span></span> 

![上面所述的映像](secure-data/_static/manager1.png)

<span data-ttu-id="ac7fa-119">下图显示了管理器的联系人的详细信息视图。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-119">The following image shows the  managers details view of a contact.</span></span>

![上面所述的映像](secure-data/_static/manager.png)

<span data-ttu-id="ac7fa-121">仅经理和管理员已批准和拒绝按钮。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-121">Only managers and administrators have the approve and reject buttons.</span></span>

<span data-ttu-id="ac7fa-122">在下图中，`admin@contoso.com`进行签名和管理员的角色中。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-122">In the following image, `admin@contoso.com` is signed in and in the administrator’s role.</span></span> 

![上面所述的映像](secure-data/_static/admin.png)

<span data-ttu-id="ac7fa-124">管理员具有所有权限。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-124">The administrator has all privileges.</span></span> <span data-ttu-id="ac7fa-125">她可以读取、 编辑或删除任何联系人，并更改联系人的状态。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-125">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="ac7fa-126">应用程序由[基架](xref:tutorials/first-mvc-app-xplat/adding-model#scaffold-the-moviecontroller)以下`Contact`模型：</span><span class="sxs-lookup"><span data-stu-id="ac7fa-126">The app was created by [scaffolding](xref:tutorials/first-mvc-app-xplat/adding-model#scaffold-the-moviecontroller)  the following `Contact` model:</span></span>

[!code-csharp[Main](secure-data/samples/starter/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="ac7fa-127">A`ContactIsOwnerAuthorizationHandler`授权处理程序可确保用户只能编辑其数据。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-127">A `ContactIsOwnerAuthorizationHandler` authorization handler ensures that a user can only edit their data.</span></span> <span data-ttu-id="ac7fa-128">A`ContactManagerAuthorizationHandler`授权处理程序允许管理器批准或拒绝联系人。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-128">A `ContactManagerAuthorizationHandler` authorization handler allows managers to approve or reject contacts.</span></span>  <span data-ttu-id="ac7fa-129">A`ContactAdministratorsAuthorizationHandler`授权处理程序允许管理员批准或拒绝联系人，还可以编辑/删除联系人。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-129">A `ContactAdministratorsAuthorizationHandler` authorization handler allows administrators to approve or reject contacts and to edit/delete contacts.</span></span> 

## <a name="prerequisites"></a><span data-ttu-id="ac7fa-130">先决条件</span><span class="sxs-lookup"><span data-stu-id="ac7fa-130">Prerequisites</span></span>

<span data-ttu-id="ac7fa-131">这不是开始本教程。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-131">This is not a beginning tutorial.</span></span> <span data-ttu-id="ac7fa-132">你应熟悉：</span><span class="sxs-lookup"><span data-stu-id="ac7fa-132">You should be familiar with:</span></span>

* [<span data-ttu-id="ac7fa-133">ASP.NET 核心 MVC</span><span class="sxs-lookup"><span data-stu-id="ac7fa-133">ASP.NET Core MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="ac7fa-134">实体框架核心</span><span class="sxs-lookup"><span data-stu-id="ac7fa-134">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="ac7fa-135">初学者和已完成应用程序</span><span class="sxs-lookup"><span data-stu-id="ac7fa-135">The starter and completed app</span></span>

<span data-ttu-id="ac7fa-136">[下载](xref:tutorials/index#how-to-download-a-sample)[完成](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final)应用。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-136">[Download](xref:tutorials/index#how-to-download-a-sample) the [completed](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final) app.</span></span> <span data-ttu-id="ac7fa-137">[测试](#test-the-completed-app)已完成的应用程序使你熟悉其安全功能。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-137">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span> 

### <a name="the-starter-app"></a><span data-ttu-id="ac7fa-138">初学者应用</span><span class="sxs-lookup"><span data-stu-id="ac7fa-138">The starter app</span></span>

<span data-ttu-id="ac7fa-139">最好先比较已完成的示例代码。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-139">It's helpful to compare your code with the completed sample.</span></span>

<span data-ttu-id="ac7fa-140">[下载](xref:tutorials/index#how-to-download-a-sample)[初学者](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter)应用。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-140">[Download](xref:tutorials/index#how-to-download-a-sample) the [starter](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter) app.</span></span> 

<span data-ttu-id="ac7fa-141">请参阅[创建初学者应用](#create-the-starter-app)如果想要从头开始创建它。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-141">See [Create the starter app](#create-the-starter-app) if you'd like to create it from scratch.</span></span>

<span data-ttu-id="ac7fa-142">更新数据库：</span><span class="sxs-lookup"><span data-stu-id="ac7fa-142">Update the database:</span></span>

```none
   dotnet ef database update
```

<span data-ttu-id="ac7fa-143">运行应用，点击**ContactManager**链接，并验证是否可以创建、 编辑和删除联系人。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-143">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

<span data-ttu-id="ac7fa-144">本教程包含的所有主要的步骤可创建安全的用户数据应用程序。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-144">This tutorial has all the major steps to create the secure user data app.</span></span> <span data-ttu-id="ac7fa-145">你可能会发现已完成的项目是指很有帮助。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-145">You may find it helpful to refer to the completed project.</span></span>

## <a name="modify-the-app-to-secure-user-data"></a><span data-ttu-id="ac7fa-146">修改应用程序以保护用户数据</span><span class="sxs-lookup"><span data-stu-id="ac7fa-146">Modify the app to secure user data</span></span>

<span data-ttu-id="ac7fa-147">下列各节具有所有主要的步骤以创建安全的用户数据应用。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-147">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="ac7fa-148">你可能会发现已完成的项目是指很有帮助。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-148">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="ac7fa-149">将向用户的联系人数据</span><span class="sxs-lookup"><span data-stu-id="ac7fa-149">Tie the contact data to the user</span></span>

<span data-ttu-id="ac7fa-150">使用 ASP.NET[标识](xref:security/authentication/identity)用户 ID，以确保用户可以编辑其数据，而非其他用户数据。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-150">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="ac7fa-151">添加`OwnerID`到`Contact`模型：</span><span class="sxs-lookup"><span data-stu-id="ac7fa-151">Add `OwnerID` to the `Contact` model:</span></span>

[!code-csharp[Main](secure-data/samples/final/Models/Contact.cs?name=snippet1&highlight=5-6,16-)]

<span data-ttu-id="ac7fa-152">`OwnerID`是从用户的 ID`AspNetUser`表中[标识](xref:security/authentication/identity)数据库。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-152">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="ac7fa-153">`Status`字段确定是否可由常规用户查看联系人。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-153">The `Status` field determines if a contact is viewable by general users.</span></span> 

<span data-ttu-id="ac7fa-154">创建一个新迁移的基架，并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="ac7fa-154">Scaffold a new migration and update the database:</span></span>

```console
dotnet ef migrations add userID_Status
dotnet ef database update
 ```

### <a name="require-ssl-and-authenticated-users"></a><span data-ttu-id="ac7fa-155">需要 SSL 和身份验证的用户</span><span class="sxs-lookup"><span data-stu-id="ac7fa-155">Require SSL and authenticated users</span></span>

<span data-ttu-id="ac7fa-156">在`ConfigureServices`方法*Startup.cs*文件中，添加[RequireHttpsAttribute](/aspnet/core/api/microsoft.aspnetcore.mvc.requirehttpsattribute)授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="ac7fa-156">In the `ConfigureServices` method of the *Startup.cs* file, add the [RequireHttpsAttribute](/aspnet/core/api/microsoft.aspnetcore.mvc.requirehttpsattribute) authorization filter:</span></span>

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=snippet_SSL&highlight=1)]

<span data-ttu-id="ac7fa-157">若要将 HTTP 请求重定向到 HTTPS，请参阅[URL 重写中间件](xref:fundamentals/url-rewriting)。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-157">To redirect HTTP requests to HTTPS, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span> <span data-ttu-id="ac7fa-158">如果你使用的 Visual Studio Code 或在本地平台上测试，不包括测试证书用于 SSL:</span><span class="sxs-lookup"><span data-stu-id="ac7fa-158">If you are using Visual Studio Code or testing on local platform that doesn't include a test certificate for SSL:</span></span>

- <span data-ttu-id="ac7fa-159">设置`"LocalTest:skipSSL": true`中*appsettings.json*文件。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-159">Set `"LocalTest:skipSSL": true` in the *appsettings.json* file.</span></span>

### <a name="require-authenticated-users"></a><span data-ttu-id="ac7fa-160">要求经过身份验证的用户</span><span class="sxs-lookup"><span data-stu-id="ac7fa-160">Require authenticated users</span></span>

<span data-ttu-id="ac7fa-161">设置默认身份验证策略以要求用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-161">Set the default authentication policy to require users to be authenticated.</span></span> <span data-ttu-id="ac7fa-162">你可以选择退出时具有的控制器或操作方法身份验证`[AllowAnonymous]`属性。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-162">You can opt out of authentication at the controller or action method with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="ac7fa-163">使用此方法，添加任何新控制器将自动要求身份验证，这是比依赖于新的控制器，以包括更安全`[Authorize]`属性。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-163">With this approach, any new controllers added will automatically require authentication, which is safer than relying on new controllers to include the `[Authorize]` attribute.</span></span> <span data-ttu-id="ac7fa-164">添加到以下`ConfigureServices`方法*Startup.cs*文件：</span><span class="sxs-lookup"><span data-stu-id="ac7fa-164">Add the following to  the `ConfigureServices` method of the *Startup.cs* file:</span></span>

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=snippet_defaultPolicy)]

<span data-ttu-id="ac7fa-165">添加`[AllowAnonymous]`到主控制器，因此匿名用户可以注册获取有关该站点的信息。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-165">Add `[AllowAnonymous]` to the home controller so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[Main](secure-data/samples/final/Controllers/HomeController.cs?name=snippet1&highlight=2,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="ac7fa-166">配置测试帐户</span><span class="sxs-lookup"><span data-stu-id="ac7fa-166">Configure the test account</span></span>

<span data-ttu-id="ac7fa-167">`SeedData`类创建两个帐户、 管理员和管理器。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-167">The `SeedData` class creates two accounts,  administrator and manager.</span></span> <span data-ttu-id="ac7fa-168">使用[机密管理器工具](xref:security/app-secrets)设置这些帐户的密码。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-168">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="ac7fa-169">项目目录中执行此操作 (目录包含*Program.cs*)。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-169">Do this from the project directory (the directory containing *Program.cs*).</span></span>

```console
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="ac7fa-170">更新`Configure`使用测试密码：</span><span class="sxs-lookup"><span data-stu-id="ac7fa-170">Update `Configure` to use the test password:</span></span>

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=Configure&highlight=19-21)]

<span data-ttu-id="ac7fa-171">添加管理员的用户 ID 和`Status = ContactStatus.Approved`向联系人。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-171">Add the administrator user ID and `Status = ContactStatus.Approved` to the contacts.</span></span> <span data-ttu-id="ac7fa-172">只能有一个联系人所示，将用户 ID 添加到所有联系人：</span><span class="sxs-lookup"><span data-stu-id="ac7fa-172">Only one contact is shown, add the user ID to all contacts:</span></span>

[!code-csharp[Main](secure-data/samples/final/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="ac7fa-173">创建所有者、 管理器中，和管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="ac7fa-173">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="ac7fa-174">创建`ContactIsOwnerAuthorizationHandler`类*授权*文件夹。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-174">Create a `ContactIsOwnerAuthorizationHandler` class in the  *Authorization* folder.</span></span> <span data-ttu-id="ac7fa-175">`ContactIsOwnerAuthorizationHandler`将验证对资源进行操作的用户拥有的资源。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-175">The `ContactIsOwnerAuthorizationHandler` will verify the user acting on the resource owns the resource.</span></span>

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="ac7fa-176">`ContactIsOwnerAuthorizationHandler`调用`context.Succeed`当前经过身份验证的用户是否联系人的所有者。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-176">The `ContactIsOwnerAuthorizationHandler` calls `context.Succeed` if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="ac7fa-177">授权处理程序通常返回`context.Succeed`满足的要求。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-177">Authorization handlers generally return `context.Succeed` when the requirements are met.</span></span> <span data-ttu-id="ac7fa-178">它们返回`Task.FromResult(0)`不符合要求。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-178">They return `Task.FromResult(0)` when requirements are not met.</span></span> <span data-ttu-id="ac7fa-179">`Task.FromResult(0)`为既不成功或失败，它允许其他授权处理程序在运行。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-179">`Task.FromResult(0)` is neither success or failure, it allows other authorization handler to run.</span></span> <span data-ttu-id="ac7fa-180">如果你需要显式失败，返回`context.Fail()`。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-180">If you need to explicitly fail, return `context.Fail()`.</span></span>

<span data-ttu-id="ac7fa-181">我们允许联系人所有者编辑/删除其自己的数据，因此我们无需请检查在要求参数中传递的操作。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-181">We allow contact owners to edit/delete their own data, so we don't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="ac7fa-182">创建管理器授权处理程序</span><span class="sxs-lookup"><span data-stu-id="ac7fa-182">Create a manager authorization handler</span></span>

<span data-ttu-id="ac7fa-183">创建`ContactManagerAuthorizationHandler`类*授权*文件夹。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-183">Create a `ContactManagerAuthorizationHandler` class in the  *Authorization* folder.</span></span> <span data-ttu-id="ac7fa-184">`ContactManagerAuthorizationHandler`将验证用户对资源进行操作是一个管理器。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-184">The `ContactManagerAuthorizationHandler` will verify the user acting on the resource is a manager.</span></span> <span data-ttu-id="ac7fa-185">只有经理可以批准或拒绝内容更改 （新的或已更改的）。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-185">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="ac7fa-186">创建一个管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="ac7fa-186">Create an administrator authorization handler</span></span>

<span data-ttu-id="ac7fa-187">创建`ContactAdministratorsAuthorizationHandler`类*授权*文件夹。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-187">Create a `ContactAdministratorsAuthorizationHandler` class in the  *Authorization* folder.</span></span> <span data-ttu-id="ac7fa-188">`ContactAdministratorsAuthorizationHandler`将验证对资源进行操作的用户是管理员。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-188">The `ContactAdministratorsAuthorizationHandler` will verify the user acting on the resource is a administrator.</span></span> <span data-ttu-id="ac7fa-189">管理员可以执行所有操作。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-189">Administrator can do all operations.</span></span>

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="ac7fa-190">注册的授权处理程序</span><span class="sxs-lookup"><span data-stu-id="ac7fa-190">Register the authorization handlers</span></span>

<span data-ttu-id="ac7fa-191">必须为注册服务使用实体框架核心[依赖关系注入](xref:fundamentals/dependency-injection)使用[AddScoped](/aspnet/core/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-191">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/aspnet/core/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="ac7fa-192">`ContactIsOwnerAuthorizationHandler`使用 ASP.NET Core[标识](xref:security/authentication/identity)，这基于实体框架核心。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-192">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="ac7fa-193">注册服务集合的处理程序，因此它们将可供`ContactsController`通过[依赖关系注入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-193">Register the handlers with the service collection so they will be available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ac7fa-194">将以下代码添加到末尾`ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ac7fa-194">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=AuthorizationHandlers)]

<span data-ttu-id="ac7fa-195">`ContactAdministratorsAuthorizationHandler`和`ContactManagerAuthorizationHandler`添加为单一实例。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-195">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="ac7fa-196">它们是单一实例，因为它们不使用 EF 和所需的所有信息都，请参阅`Context`参数`HandleRequirementAsync`方法。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-196">They are singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

<span data-ttu-id="ac7fa-197">完整`ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ac7fa-197">The complete `ConfigureServices`:</span></span>

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=ConfigureServices)]

## <a name="update-the-code-to-support-authorization"></a><span data-ttu-id="ac7fa-198">更新代码以支持授权</span><span class="sxs-lookup"><span data-stu-id="ac7fa-198">Update the code to support authorization</span></span>

<span data-ttu-id="ac7fa-199">在本部分中，你将更新的控制器和视图，并添加操作要求类。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-199">In this section, you update the controller and views and add an operations requirements class.</span></span>

### <a name="update-the-contacts-controller"></a><span data-ttu-id="ac7fa-200">更新联系人控制器</span><span class="sxs-lookup"><span data-stu-id="ac7fa-200">Update the Contacts controller</span></span>

<span data-ttu-id="ac7fa-201">更新`ContactsController`构造函数：</span><span class="sxs-lookup"><span data-stu-id="ac7fa-201">Update the `ContactsController` constructor:</span></span>

* <span data-ttu-id="ac7fa-202">添加`IAuthorizationService`服务访问的授权处理程序。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-202">Add the `IAuthorizationService` service to  access to the authorization handlers.</span></span> 
* <span data-ttu-id="ac7fa-203">添加`Identity``UserManager`服务：</span><span class="sxs-lookup"><span data-stu-id="ac7fa-203">Add the `Identity` `UserManager` service:</span></span>

[!code-csharp[Main](secure-data/samples/final/Controllers/ContactsController.cs?name=snippet_ContactsControllerCtor)]

### <a name="add-a-contact-operations-requirements-class"></a><span data-ttu-id="ac7fa-204">添加联系人的操作要求类</span><span class="sxs-lookup"><span data-stu-id="ac7fa-204">Add a contact operations requirements class</span></span>

<span data-ttu-id="ac7fa-205">添加`ContactOperationsRequirements`类到*授权*文件夹。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-205">Add the `ContactOperationsRequirements` class to the *Authorization* folder.</span></span> <span data-ttu-id="ac7fa-206">此类包含要求我们的应用程序支持：</span><span class="sxs-lookup"><span data-stu-id="ac7fa-206">This class  contain the requirements our app supports:</span></span>

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactOperations.cs)]

### <a name="update-create"></a><span data-ttu-id="ac7fa-207">创建更新</span><span class="sxs-lookup"><span data-stu-id="ac7fa-207">Update Create</span></span>

<span data-ttu-id="ac7fa-208">更新`HTTP POST Create`方法：</span><span class="sxs-lookup"><span data-stu-id="ac7fa-208">Update the `HTTP POST Create` method to:</span></span>

* <span data-ttu-id="ac7fa-209">添加到的用户 ID`Contact`模型。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-209">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="ac7fa-210">调用要验证的用户拥有联系人的授权处理程序。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-210">Call the authorization handler to verify the user owns the contact.</span></span>

[!code-csharp[Main](secure-data/samples/final/Controllers/ContactsController.cs?name=snippet_Create)]

### <a name="update-edit"></a><span data-ttu-id="ac7fa-211">更新编辑</span><span class="sxs-lookup"><span data-stu-id="ac7fa-211">Update Edit</span></span>

<span data-ttu-id="ac7fa-212">同时更新`Edit`方法来使用授权处理程序来验证用户是否拥有联系人。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-212">Update both `Edit` methods to use the authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="ac7fa-213">由于我们正在执行资源授权我们不能使用`[Authorize]`属性。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-213">Because we are performing resource authorization we cannot use the `[Authorize]` attribute.</span></span> <span data-ttu-id="ac7fa-214">评估属性时，我们没有对资源的访问。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-214">We don't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="ac7fa-215">基于资源授权必须是命令性。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-215">Resource based authorization must be imperative.</span></span> <span data-ttu-id="ac7fa-216">一旦我们有权访问该资源，通过在我们控制器中加载或加载内处理程序本身，则必须执行检查。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-216">Checks must be performed once we have access to the resource, either by loading it in our controller, or by loading it within the handler itself.</span></span> <span data-ttu-id="ac7fa-217">经常将通过传入的资源键来访问资源。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-217">Frequently you will access the resource by passing in the resource key.</span></span>

[!code-csharp[Main](secure-data/samples/final/Controllers/ContactsController.cs?name=snippet_Edit)]

### <a name="update-the-delete-method"></a><span data-ttu-id="ac7fa-218">更新 Delete 方法</span><span class="sxs-lookup"><span data-stu-id="ac7fa-218">Update the Delete method</span></span>

<span data-ttu-id="ac7fa-219">同时更新`Delete`方法来使用授权处理程序来验证用户是否拥有联系人。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-219">Update both `Delete` methods to use the authorization handler to verify the user owns the contact.</span></span>

[!code-csharp[Main](secure-data/samples/final/Controllers/ContactsController.cs?name=snippet_Delete)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="ac7fa-220">将授权服务注入到视图</span><span class="sxs-lookup"><span data-stu-id="ac7fa-220">Inject the authorization service into the views</span></span>

<span data-ttu-id="ac7fa-221">当前的 UI 所示编辑和删除用户无法修改的数据的链接。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-221">Currently the UI shows edit and delete links for data the user cannot modify.</span></span> <span data-ttu-id="ac7fa-222">我们将通过应用到视图授权处理程序来修复此问题。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-222">We'll fix that by applying the authorization handler to the views.</span></span>

<span data-ttu-id="ac7fa-223">插入中的授权服务*Views/_ViewImports.cshtml*文件因此它将可供所有视图：</span><span class="sxs-lookup"><span data-stu-id="ac7fa-223">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it will be available to all views:</span></span>

[!code-html[Main](secure-data/samples/final/Views/_ViewImports.cshtml)]

<span data-ttu-id="ac7fa-224">更新*Views/Contacts/Index.cshtml* Razor 视图仅显示编辑和删除链接的用户可以编辑/删除联系人。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-224">Update the *Views/Contacts/Index.cshtml* Razor view to only display the edit and delete links for users who can edit/delete the contact.</span></span>

<span data-ttu-id="ac7fa-225">添加`@using ContactManager.Authorization;`</span><span class="sxs-lookup"><span data-stu-id="ac7fa-225">Add `@using ContactManager.Authorization;`</span></span>

<span data-ttu-id="ac7fa-226">更新`Edit`和`Delete`链接以便它们只呈现为具有权限的用户进行编辑和删除联系人。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-226">Update the `Edit` and `Delete` links so they are only rendered for users with permission to edit and delete the contact.</span></span>

[!code-html[Main](secure-data/samples/final/Views/Contacts/Index.cshtml?range=63-84)]

<span data-ttu-id="ac7fa-227">警告： 隐藏从没有权限来编辑或删除数据的用户的链接不会保护应用程序。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-227">Warning: Hiding links from users that do not have permission to edit or delete data does not secure the app.</span></span> <span data-ttu-id="ac7fa-228">隐藏链接使该应用更详细的用户友好通过显示唯一有效的链接。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-228">Hiding links makes the app more user friendly by displaying only valid links.</span></span> <span data-ttu-id="ac7fa-229">用户可以 hack 生成的 Url 来调用编辑和删除在它们自己的数据的操作。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-229">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span>  <span data-ttu-id="ac7fa-230">控制器必须重复执行访问检查才能保证安全。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-230">The controller must repeat the access checks to be secure.</span></span>

### <a name="update-the-details-view"></a><span data-ttu-id="ac7fa-231">更新的详细信息视图</span><span class="sxs-lookup"><span data-stu-id="ac7fa-231">Update the Details view</span></span>

<span data-ttu-id="ac7fa-232">更新的详细信息视图，以便经理可以批准或拒绝联系人：</span><span class="sxs-lookup"><span data-stu-id="ac7fa-232">Update the details view so managers can approve or reject contacts:</span></span>

[!code-html[Main](secure-data/samples/final/Views/Contacts/Details.cshtml?range=53-)]

## <a name="test-the-completed-app"></a><span data-ttu-id="ac7fa-233">测试已完成的应用程序</span><span class="sxs-lookup"><span data-stu-id="ac7fa-233">Test the completed app</span></span>

<span data-ttu-id="ac7fa-234">如果你使用的 Visual Studio Code 或在本地平台上测试，不包括测试证书用于 SSL:</span><span class="sxs-lookup"><span data-stu-id="ac7fa-234">If you are using Visual Studio Code or testing on local platform that doesn't include a test certificate for SSL:</span></span>

- <span data-ttu-id="ac7fa-235">设置`"LocalTest:skipSSL": true`中*appsettings.json*文件。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-235">Set `"LocalTest:skipSSL": true` in the *appsettings.json* file.</span></span>

<span data-ttu-id="ac7fa-236">如果您具有运行应用程序，并且有联系人，删除中的所有记录`Contact`表，然后重新启动应用植入到数据库。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-236">If you have run the app and have contacts, delete all the records in the `Contact` table and restart the app to seed the database.</span></span> <span data-ttu-id="ac7fa-237">如果你使用的 Visual Studio，你需要退出并重新启动以种子数据库的 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-237">If you are using Visual Studio, you need to exit and restart IIS Express to seed the database.</span></span>

<span data-ttu-id="ac7fa-238">注册用户若要浏览的联系人。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-238">Register a user to browse the contacts.</span></span>

<span data-ttu-id="ac7fa-239">若要测试已完成的应用程序的简单方法是以启动三个不同的浏览器 （或 incognito/InPrivate 版本）。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-239">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate versions).</span></span> <span data-ttu-id="ac7fa-240">例如，一个在浏览器中注册一个新用户， `test@example.com`。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-240">In one browser, register a new user, for example, `test@example.com`.</span></span> <span data-ttu-id="ac7fa-241">登录到每个浏览器了不同的用户。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-241">Sign in to each browser with a different user.</span></span> <span data-ttu-id="ac7fa-242">验证以下内容：</span><span class="sxs-lookup"><span data-stu-id="ac7fa-242">Verify the following:</span></span>

* <span data-ttu-id="ac7fa-243">已注册的用户可以查看所有已批准的联系人数据。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-243">Registered users can view all the approved contact data.</span></span>
* <span data-ttu-id="ac7fa-244">已注册的用户可以编辑/删除其自己的数据。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-244">Registered users can edit/delete their own data.</span></span> 
* <span data-ttu-id="ac7fa-245">经理可以批准或拒绝联系人数据。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-245">Managers can approve or reject contact data.</span></span> <span data-ttu-id="ac7fa-246">`Details`视图显示**批准**和**拒绝**按钮。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-246">The `Details` view shows **Approve** and **Reject** buttons.</span></span> 
* <span data-ttu-id="ac7fa-247">管理员可以批准/拒绝和编辑/删除任何数据。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-247">Administrators can approve/reject and edit/delete any data.</span></span>

| <span data-ttu-id="ac7fa-248">用户</span><span class="sxs-lookup"><span data-stu-id="ac7fa-248">User</span></span>| <span data-ttu-id="ac7fa-249">选项</span><span class="sxs-lookup"><span data-stu-id="ac7fa-249">Options</span></span> |
| ------------ | ---------|
| test@example.com | <span data-ttu-id="ac7fa-250">可以编辑/删除自己的数据</span><span class="sxs-lookup"><span data-stu-id="ac7fa-250">Can edit/delete own data</span></span> |
| manager@contoso.com | <span data-ttu-id="ac7fa-251">可以批准/拒绝和编辑/删除拥有数据</span><span class="sxs-lookup"><span data-stu-id="ac7fa-251">Can approve/reject and edit/delete own data</span></span>  |
| admin@contoso.com | <span data-ttu-id="ac7fa-252">可以编辑/删除和批准/拒绝的所有数据</span><span class="sxs-lookup"><span data-stu-id="ac7fa-252">Can edit/delete and approve/reject all data</span></span>|

<span data-ttu-id="ac7fa-253">在管理员浏览器中创建一个联系人。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-253">Create a contact in the administrators browser.</span></span> <span data-ttu-id="ac7fa-254">将删除该 URL 复制和编辑从管理员的联系信息。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-254">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="ac7fa-255">将这些链接粘贴到测试用户的浏览器以验证测试用户无法执行这些操作。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-255">Paste these links into the test user's browser to verify the test user cannot perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="ac7fa-256">创建初学者应用</span><span class="sxs-lookup"><span data-stu-id="ac7fa-256">Create the starter app</span></span>

<span data-ttu-id="ac7fa-257">按照这些说明创建初学者应用程序。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-257">Follow these instructions to create the starter app.</span></span>

* <span data-ttu-id="ac7fa-258">创建**ASP.NET 核心 Web 应用程序**使用[Visual Studio 2017](https://www.visualstudio.com/)名为"ContactManager"</span><span class="sxs-lookup"><span data-stu-id="ac7fa-258">Create an **ASP.NET Core Web Application** using [Visual Studio 2017](https://www.visualstudio.com/) named "ContactManager"</span></span>

  * <span data-ttu-id="ac7fa-259">创建应用程序与**单个用户帐户**。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-259">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="ac7fa-260">请将其命名"ContactManager"使你的命名空间将与在此示例中的命名空间使用相匹配。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-260">Name it "ContactManager" so your namespace will match the namespace use in the sample.</span></span>

* <span data-ttu-id="ac7fa-261">添加以下`Contact`模型：</span><span class="sxs-lookup"><span data-stu-id="ac7fa-261">Add the following `Contact` model:</span></span>

  [!code-csharp[Main](secure-data/samples/starter/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="ac7fa-262">基架`Contact`模型使用实体框架核心和`ApplicationDbContext`数据上下文。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-262">Scaffold the `Contact` model using Entity Framework Core and the `ApplicationDbContext` data context.</span></span> <span data-ttu-id="ac7fa-263">接受所有基架默认值。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-263">Accept all the scaffolding defaults.</span></span> <span data-ttu-id="ac7fa-264">使用`ApplicationDbContext`数据上下文类将联系人表放入[标识](xref:security/authentication/identity)数据库。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-264">Using `ApplicationDbContext` for the data context class  puts the contact table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="ac7fa-265">请参阅[添加模型](xref:tutorials/first-mvc-app/adding-model)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-265">See [Adding a model](xref:tutorials/first-mvc-app/adding-model) for more information.</span></span>

* <span data-ttu-id="ac7fa-266">更新**ContactManager**中锚定*Views/Shared/_Layout.cshtml*文件从`asp-controller="Home"`到`asp-controller="Contacts"`因此点击**ContactManager**链接将调用联系人控制器。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-266">Update the **ContactManager** anchor in the *Views/Shared/_Layout.cshtml* file from `asp-controller="Home"` to `asp-controller="Contacts"` so tapping the **ContactManager** link will invoke the Contacts controller.</span></span> <span data-ttu-id="ac7fa-267">原始的标记：</span><span class="sxs-lookup"><span data-stu-id="ac7fa-267">The original markup:</span></span>

```html
   <a asp-area="" asp-controller="Home" asp-action="Index" class="navbar-brand">ContactManager</a>
   ```

<span data-ttu-id="ac7fa-268">更新的标记：</span><span class="sxs-lookup"><span data-stu-id="ac7fa-268">The updated markup:</span></span>

```html
   <a asp-area="" asp-controller="Contacts" asp-action="Index" class="navbar-brand">ContactManager</a>
   ```

* <span data-ttu-id="ac7fa-269">构建基架初始迁移并更新数据库</span><span class="sxs-lookup"><span data-stu-id="ac7fa-269">Scaffold the initial migration and update the database</span></span>

```none
   dotnet ef migrations add initial
   dotnet ef database update
   ```

* <span data-ttu-id="ac7fa-270">测试应用程序通过创建、 编辑和删除联系人</span><span class="sxs-lookup"><span data-stu-id="ac7fa-270">Test the app by creating, editing and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="ac7fa-271">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="ac7fa-271">Seed the database</span></span>

<span data-ttu-id="ac7fa-272">添加`SeedData`类到*数据*文件夹。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-272">Add the `SeedData` class to the *Data* folder.</span></span> <span data-ttu-id="ac7fa-273">如果你已下载示例，你可以复制*SeedData.cs*文件为*数据*初学者项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-273">If you've downloaded the sample, you can copy the *SeedData.cs* file to the *Data* folder of the starter project.</span></span>

[!code-csharp[Main](secure-data/samples/starter/Data/SeedData.cs)]

<span data-ttu-id="ac7fa-274">将突出显示的代码添加到末尾`Configure`中的方法*Startup.cs*文件：</span><span class="sxs-lookup"><span data-stu-id="ac7fa-274">Add the highlighted code to the end of the `Configure` method in the *Startup.cs* file:</span></span>

[!code-csharp[Main](secure-data/samples/starter/Startup.cs?name=Configure&highlight=28-)]

<span data-ttu-id="ac7fa-275">测试应用程序设定种子的数据库。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-275">Test that the app seeded the database.</span></span> <span data-ttu-id="ac7fa-276">不运行 seed 方法，如果联系人 DB 中有任何行。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-276">The seed method does not run if there are any rows in the contact DB.</span></span>

### <a name="create-a-class-used-in-the-tutorial"></a><span data-ttu-id="ac7fa-277">创建本教程中使用的类</span><span class="sxs-lookup"><span data-stu-id="ac7fa-277">Create a class used in the tutorial</span></span>

* <span data-ttu-id="ac7fa-278">创建名为的文件夹*授权*。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-278">Create a folder named *Authorization*.</span></span>
* <span data-ttu-id="ac7fa-279">复制*Authorization\ContactOperations.cs*文件已完成的项目下载，或复制以下代码：</span><span class="sxs-lookup"><span data-stu-id="ac7fa-279">Copy the *Authorization\ContactOperations.cs* file from the completed project download, or copy the following code:</span></span>

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactOperations.cs)]

<a name=secure-data-add-resources-label></a>

### <a name="additional-resources"></a><span data-ttu-id="ac7fa-280">其他资源</span><span class="sxs-lookup"><span data-stu-id="ac7fa-280">Additional resources</span></span>

* <span data-ttu-id="ac7fa-281">[ASP.NET 核心授权实验室](https://github.com/blowdart/AspNetAuthorizationWorkshop)。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-281">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="ac7fa-282">在本教程中引入的安全功能，此实验室将进入更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="ac7fa-282">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* [<span data-ttu-id="ac7fa-283">在 ASP.NET Core 中的授权： 基于声明的和自定义的简单，角色</span><span class="sxs-lookup"><span data-stu-id="ac7fa-283">Authorization in ASP.NET Core : Simple, role, claims-based and custom</span></span>](index.md)
* [<span data-ttu-id="ac7fa-284">基于自定义策略的授权</span><span class="sxs-lookup"><span data-stu-id="ac7fa-284">Custom Policy-Based Authorization</span></span>](policies.md)
