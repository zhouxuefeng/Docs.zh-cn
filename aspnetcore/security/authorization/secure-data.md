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
ms.openlocfilehash: 54a737f140a8434035e5fc5abfefa458fdb69321
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a>使用受授权的用户数据创建 ASP.NET Core 应用

作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Joe Audette](https://twitter.com/joeaudette)

本教程演示如何创建使用受授权的用户数据的 web 应用。 它显示的身份验证的 （注册） 的用户的联系人列表已创建。 有三个安全组：

* 已注册的用户可以查看所有已批准的联系人数据。
* 已注册的用户可以编辑/删除其自己的数据。 
* 经理可以批准或拒绝联系人数据。 仅批准的联系人是对用户可见。
* 管理员可以批准/拒绝和编辑/删除任何数据。

在下图中，用户 Rick (`rick@example.com`) 登录。 用户 Rick 只能查看批准联系人和编辑/删除其联系人。 仅最新的记录，创建由 Rick、 显示编辑和删除链接

![上面所述的映像](secure-data/_static/rick.png)

在下图中，`manager@contoso.com`进行签名和管理员角色中。 

![上面所述的映像](secure-data/_static/manager1.png)

下图显示了管理器的联系人的详细信息视图。

![上面所述的映像](secure-data/_static/manager.png)

仅经理和管理员已批准和拒绝按钮。

在下图中，`admin@contoso.com`进行签名和管理员的角色中。 

![上面所述的映像](secure-data/_static/admin.png)

管理员具有所有权限。 她可以读取、 编辑或删除任何联系人，并更改联系人的状态。

应用程序由[基架](xref:tutorials/first-mvc-app-xplat/adding-model#scaffold-the-moviecontroller)以下`Contact`模型：

[!code-csharp[Main](secure-data/samples/starter/Models/Contact.cs?name=snippet1)]

A`ContactIsOwnerAuthorizationHandler`授权处理程序可确保用户只能编辑其数据。 A`ContactManagerAuthorizationHandler`授权处理程序允许管理器批准或拒绝联系人。  A`ContactAdministratorsAuthorizationHandler`授权处理程序允许管理员批准或拒绝联系人，还可以编辑/删除联系人。 

## <a name="prerequisites"></a>先决条件

这不是开始本教程。 你应熟悉：

* [ASP.NET 核心 MVC](xref:tutorials/first-mvc-app/start-mvc)
* [实体框架核心](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>初学者和已完成应用程序

[下载](xref:tutorials/index#how-to-download-a-sample)[完成](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final)应用。 [测试](#test-the-completed-app)已完成的应用程序使你熟悉其安全功能。 

### <a name="the-starter-app"></a>初学者应用

最好先比较已完成的示例代码。

[下载](xref:tutorials/index#how-to-download-a-sample)[初学者](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter)应用。 

请参阅[创建初学者应用](#create-the-starter-app)如果想要从头开始创建它。

更新数据库：

```none
   dotnet ef database update
```

运行应用，点击**ContactManager**链接，并验证是否可以创建、 编辑和删除联系人。

本教程包含的所有主要的步骤可创建安全的用户数据应用程序。 你可能会发现已完成的项目是指很有帮助。

## <a name="modify-the-app-to-secure-user-data"></a>修改应用程序以保护用户数据

下列各节具有所有主要的步骤以创建安全的用户数据应用。 你可能会发现已完成的项目是指很有帮助。

### <a name="tie-the-contact-data-to-the-user"></a>将向用户的联系人数据

使用 ASP.NET[标识](xref:security/authentication/identity)用户 ID，以确保用户可以编辑其数据，而非其他用户数据。 添加`OwnerID`到`Contact`模型：

[!code-csharp[Main](secure-data/samples/final/Models/Contact.cs?name=snippet1&highlight=5-6,16-)]

`OwnerID`是从用户的 ID`AspNetUser`表中[标识](xref:security/authentication/identity)数据库。 `Status`字段确定是否可由常规用户查看联系人。 

创建一个新迁移的基架，并更新数据库：

```console
dotnet ef migrations add userID_Status
dotnet ef database update
 ```

### <a name="require-ssl-and-authenticated-users"></a>需要 SSL 和身份验证的用户

在`ConfigureServices`方法*Startup.cs*文件中，添加[RequireHttpsAttribute](/aspnet/core/api/microsoft.aspnetcore.mvc.requirehttpsattribute)授权筛选器：

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=snippet_SSL&highlight=1)]

如果你使用 Visual Studio，请参阅[为 SSL/HTTPS 设置了 IIS Express](xref:security/enforcing-ssl#set-up-iis-express-for-sslhttps)。 若要将 HTTP 请求重定向到 HTTPS，请参阅[URL 重写中间件](xref:fundamentals/url-rewriting)。 如果你使用的 Visual Studio Code 或在本地平台上测试，不包括测试证书用于 SSL:

- 设置`"LocalTest:skipSSL": true`中*appsettings.json*文件。

### <a name="require-authenticated-users"></a>要求经过身份验证的用户

设置默认身份验证策略以要求用户进行身份验证。 你可以选择退出时具有的控制器或操作方法身份验证`[AllowAnonymous]`属性。 使用此方法，添加任何新控制器将自动要求身份验证，这是比依赖于新的控制器，以包括更安全`[Authorize]`属性。 添加到以下`ConfigureServices`方法*Startup.cs*文件：

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=snippet_defaultPolicy)]

添加`[AllowAnonymous]`到主控制器，因此匿名用户可以注册获取有关该站点的信息。

[!code-csharp[Main](secure-data/samples/final/Controllers/HomeController.cs?name=snippet1&highlight=2,6)]

### <a name="configure-the-test-account"></a>配置测试帐户

`SeedData`类创建两个帐户、 管理员和管理器。 使用[机密管理器工具](xref:security/app-secrets)设置这些帐户的密码。 项目目录中执行此操作 (目录包含*Program.cs*)。

```console
dotnet user-secrets set SeedUserPW <PW>
```

更新`Configure`使用测试密码：

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=Configure&highlight=19-21)]

添加管理员的用户 ID 和`Status = ContactStatus.Approved`向联系人。 只能有一个联系人所示，将用户 ID 添加到所有联系人：

[!code-csharp[Main](secure-data/samples/final/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>创建所有者、 管理器中，和管理员授权处理程序

创建`ContactIsOwnerAuthorizationHandler`类*授权*文件夹。 `ContactIsOwnerAuthorizationHandler`将验证对资源进行操作的用户拥有的资源。

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler`调用`context.Succeed`当前经过身份验证的用户是否联系人的所有者。 授权处理程序通常返回`context.Succeed`满足的要求。 它们返回`Task.FromResult(0)`不符合要求。 `Task.FromResult(0)`为既不成功或失败，它允许其他授权处理程序在运行。 如果你需要显式失败，返回`context.Fail()`。

我们允许联系人所有者编辑/删除其自己的数据，因此我们无需请检查在要求参数中传递的操作。

### <a name="create-a-manager-authorization-handler"></a>创建管理器授权处理程序

创建`ContactManagerAuthorizationHandler`类*授权*文件夹。 `ContactManagerAuthorizationHandler`将验证用户对资源进行操作是一个管理器。 只有经理可以批准或拒绝内容更改 （新的或已更改的）。

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>创建一个管理员授权处理程序

创建`ContactAdministratorsAuthorizationHandler`类*授权*文件夹。 `ContactAdministratorsAuthorizationHandler`将验证对资源进行操作的用户是管理员。 管理员可以执行所有操作。

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>注册的授权处理程序

必须为注册服务使用实体框架核心[依赖关系注入](xref:fundamentals/dependency-injection)使用[AddScoped](/aspnet/core/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)。 `ContactIsOwnerAuthorizationHandler`使用 ASP.NET Core[标识](xref:security/authentication/identity)，这基于实体框架核心。 注册服务集合的处理程序，因此它们将可供`ContactsController`通过[依赖关系注入](xref:fundamentals/dependency-injection)。 将以下代码添加到末尾`ConfigureServices`:

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=AuthorizationHandlers)]

`ContactAdministratorsAuthorizationHandler`和`ContactManagerAuthorizationHandler`添加为单一实例。 它们是单一实例，因为它们不使用 EF 和所需的所有信息都，请参阅`Context`参数`HandleRequirementAsync`方法。

完整`ConfigureServices`:

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=ConfigureServices)]

## <a name="update-the-code-to-support-authorization"></a>更新代码以支持授权

在本部分中，你将更新的控制器和视图，并添加操作要求类。

### <a name="update-the-contacts-controller"></a>更新联系人控制器

更新`ContactsController`构造函数：

* 添加`IAuthorizationService`服务访问的授权处理程序。 
* 添加`Identity``UserManager`服务：

[!code-csharp[Main](secure-data/samples/final/Controllers/ContactsController.cs?name=snippet_ContactsControllerCtor)]

### <a name="add-a-contact-operations-requirements-class"></a>添加联系人的操作要求类

添加`ContactOperationsRequirements`类到*授权*文件夹。 此类包含要求我们的应用程序支持：

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactOperations.cs)]

### <a name="update-create"></a>创建更新

更新`HTTP POST Create`方法：

* 添加到的用户 ID`Contact`模型。
* 调用要验证的用户拥有联系人的授权处理程序。

[!code-csharp[Main](secure-data/samples/final/Controllers/ContactsController.cs?name=snippet_Create)]

### <a name="update-edit"></a>更新编辑

同时更新`Edit`方法来使用授权处理程序来验证用户是否拥有联系人。 由于我们正在执行资源授权我们不能使用`[Authorize]`属性。 评估属性时，我们没有对资源的访问。 基于资源授权必须是命令性。 一旦我们有权访问该资源，通过在我们控制器中加载或加载内处理程序本身，则必须执行检查。 经常将通过传入的资源键来访问资源。

[!code-csharp[Main](secure-data/samples/final/Controllers/ContactsController.cs?name=snippet_Edit)]

### <a name="update-the-delete-method"></a>更新 Delete 方法

同时更新`Delete`方法来使用授权处理程序来验证用户是否拥有联系人。

[!code-csharp[Main](secure-data/samples/final/Controllers/ContactsController.cs?name=snippet_Delete)]

## <a name="inject-the-authorization-service-into-the-views"></a>将授权服务注入到视图

当前的 UI 所示编辑和删除用户无法修改的数据的链接。 我们将通过应用到视图授权处理程序来修复此问题。

插入中的授权服务*Views/_ViewImports.cshtml*文件因此它将可供所有视图：

[!code-html[Main](secure-data/samples/final/Views/_ViewImports.cshtml)]

更新*Views/Contacts/Index.cshtml* Razor 视图仅显示编辑和删除链接的用户可以编辑/删除联系人。

添加`@using ContactManager.Authorization;`

更新`Edit`和`Delete`链接以便它们只呈现为具有权限的用户进行编辑和删除联系人。

[!code-html[Main](secure-data/samples/final/Views/Contacts/Index.cshtml?range=63-84)]

警告： 隐藏从没有权限来编辑或删除数据的用户的链接不会保护应用程序。 隐藏链接使该应用更详细的用户友好通过显示唯一有效的链接。 用户可以 hack 生成的 Url 来调用编辑和删除在它们自己的数据的操作。  控制器必须重复执行访问检查才能保证安全。

### <a name="update-the-details-view"></a>更新的详细信息视图

更新的详细信息视图，以便经理可以批准或拒绝联系人：

[!code-html[Main](secure-data/samples/final/Views/Contacts/Details.cshtml?range=53-)]

## <a name="test-the-completed-app"></a>测试已完成的应用程序

如果你使用的 Visual Studio Code 或在本地平台上测试，不包括测试证书用于 SSL:

- 设置`"LocalTest:skipSSL": true`中*appsettings.json*文件。

如果您具有运行应用程序，并且有联系人，删除中的所有记录`Contact`表，然后重新启动应用植入到数据库。 如果你使用的 Visual Studio，你需要退出并重新启动以种子数据库的 IIS Express。

注册用户若要浏览的联系人。

若要测试已完成的应用程序的简单方法是以启动三个不同的浏览器 （或 incognito/InPrivate 版本）。 例如，一个在浏览器中注册一个新用户， `test@example.com`。 登录到每个浏览器了不同的用户。 验证以下内容：

* 已注册的用户可以查看所有已批准的联系人数据。
* 已注册的用户可以编辑/删除其自己的数据。 
* 经理可以批准或拒绝联系人数据。 `Details`视图显示**批准**和**拒绝**按钮。 
* 管理员可以批准/拒绝和编辑/删除任何数据。

| 用户| 选项 |
| ------------ | ---------|
| test@example.com | 可以编辑/删除自己的数据 |
| manager@contoso.com | 可以批准/拒绝和编辑/删除拥有数据  |
| admin@contoso.com | 可以编辑/删除和批准/拒绝的所有数据|

在管理员浏览器中创建一个联系人。 将删除该 URL 复制和编辑从管理员的联系信息。 将这些链接粘贴到测试用户的浏览器以验证测试用户无法执行这些操作。

## <a name="create-the-starter-app"></a>创建初学者应用

按照这些说明创建初学者应用程序。

* 创建**ASP.NET 核心 Web 应用程序**使用[Visual Studio 2017](https://www.visualstudio.com/)名为"ContactManager"

  * 创建应用程序与**单个用户帐户**。
  * 请将其命名"ContactManager"使你的命名空间将与在此示例中的命名空间使用相匹配。

* 添加以下`Contact`模型：

  [!code-csharp[Main](secure-data/samples/starter/Models/Contact.cs?name=snippet1)]

* 基架`Contact`模型使用实体框架核心和`ApplicationDbContext`数据上下文。 接受所有基架默认值。 使用`ApplicationDbContext`数据上下文类将联系人表放入[标识](xref:security/authentication/identity)数据库。 请参阅[添加模型](xref:tutorials/first-mvc-app/adding-model)有关详细信息。

* 更新**ContactManager**中锚定*Views/Shared/_Layout.cshtml*文件从`asp-controller="Home"`到`asp-controller="Contacts"`因此点击**ContactManager**链接将调用联系人控制器。 原始的标记：

```html
   <a asp-area="" asp-controller="Home" asp-action="Index" class="navbar-brand">ContactManager</a>
   ```

更新的标记：

```html
   <a asp-area="" asp-controller="Contacts" asp-action="Index" class="navbar-brand">ContactManager</a>
   ```

* 构建基架初始迁移并更新数据库

```none
   dotnet ef migrations add initial
   dotnet ef database update
   ```

* 测试应用程序通过创建、 编辑和删除联系人

### <a name="seed-the-database"></a>设定数据库种子

添加`SeedData`类到*数据*文件夹。 如果你已下载示例，你可以复制*SeedData.cs*文件为*数据*初学者项目的文件夹。

[!code-csharp[Main](secure-data/samples/starter/Data/SeedData.cs)]

将突出显示的代码添加到末尾`Configure`中的方法*Startup.cs*文件：

[!code-csharp[Main](secure-data/samples/starter/Startup.cs?name=Configure&highlight=28-)]

测试应用程序设定种子的数据库。 不运行 seed 方法，如果联系人 DB 中有任何行。

### <a name="create-a-class-used-in-the-tutorial"></a>创建本教程中使用的类

* 创建名为的文件夹*授权*。
* 复制*Authorization\ContactOperations.cs*文件已完成的项目下载，或复制以下代码：

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactOperations.cs)]

<a name=secure-data-add-resources-label></a>

### <a name="additional-resources"></a>其他资源

* [ASP.NET 核心授权实验室](https://github.com/blowdart/AspNetAuthorizationWorkshop)。 在本教程中引入的安全功能，此实验室将进入更多详细信息。
* [在 ASP.NET Core 中的授权： 基于声明的和自定义的简单，角色](index.md)
* [基于自定义策略的授权](policies.md)
