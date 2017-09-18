---
title: "帐户确认和 ASP.NET Core 中的密码恢复"
author: rick-anderson
description: "演示如何生成具有电子邮件确认及密码重置的 ASP.NET Core 应用。"
keywords: "ASP.NET 核心，密码重置，电子邮件确认，安全"
ms.author: riande
manager: wpickett
ms.date: 07/19/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/accconfirm
ms.openlocfilehash: 8fe21b1a1ccb93c124dbd12a540b195400d45ef6
ms.sourcegitcommit: f8f6b5934bd071a349f5bc1e389365c52b1c00fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2017
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="73be8-104">帐户确认和 ASP.NET Core 中的密码恢复</span><span class="sxs-lookup"><span data-stu-id="73be8-104">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="73be8-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="73be8-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="73be8-106">本教程演示了如何生成具有电子邮件确认及密码重置的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="73be8-106">This tutorial shows you how to build an ASP.NET Core app with email confirmation and password reset.</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="73be8-107">创建新的 ASP.NET Core 项目</span><span class="sxs-lookup"><span data-stu-id="73be8-107">Create a New ASP.NET Core Project</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="73be8-108">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="73be8-108">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="73be8-109">此步骤适用于 Windows 上的 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="73be8-109">This step applies to Visual Studio on Windows.</span></span> <span data-ttu-id="73be8-110">请参阅下一节有关 CLI 的说明。</span><span class="sxs-lookup"><span data-stu-id="73be8-110">See the next section for CLI instructions.</span></span>

<span data-ttu-id="73be8-111">本教程需要 Visual Studio 2017 预览版 2 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="73be8-111">The tutorial requires Visual Studio 2017 Preview 2 or later.</span></span>

* <span data-ttu-id="73be8-112">在 Visual Studio 中，创建新的 Web 应用程序项目。</span><span class="sxs-lookup"><span data-stu-id="73be8-112">In Visual Studio, create a New Web Application Project.</span></span>
* <span data-ttu-id="73be8-113">选择**ASP.NET Core 2.0**。</span><span class="sxs-lookup"><span data-stu-id="73be8-113">Select **ASP.NET Core 2.0**.</span></span> <span data-ttu-id="73be8-114">下图显示**.NET 核心**选择，但你可以选择**.NET Framework**。</span><span class="sxs-lookup"><span data-stu-id="73be8-114">The following image show **.NET Core** selected, but you can select **.NET Framework**.</span></span>
* <span data-ttu-id="73be8-115">选择**更改身份验证**并将设置为**单个用户帐户**。</span><span class="sxs-lookup"><span data-stu-id="73be8-115">Select **Change Authentication** and set to **Individual User Accounts**.</span></span>
* <span data-ttu-id="73be8-116">保留默认值**存储用户帐户在应用程序**。</span><span class="sxs-lookup"><span data-stu-id="73be8-116">Keep the default **Store user accounts in-app**.</span></span>

![显示"单个用户帐户单选"所选的新建项目对话框](accconfirm/_static/2.png)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="73be8-118">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="73be8-118">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="73be8-119">本教程需要 Visual Studio 2017 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="73be8-119">The tutorial requires Visual Studio 2017 or later.</span></span>

* <span data-ttu-id="73be8-120">在 Visual Studio 中，创建新的 Web 应用程序项目。</span><span class="sxs-lookup"><span data-stu-id="73be8-120">In Visual Studio, create a New Web Application Project.</span></span>
* <span data-ttu-id="73be8-121">选择**更改身份验证**并将设置为**单个用户帐户**。</span><span class="sxs-lookup"><span data-stu-id="73be8-121">Select **Change Authentication** and set to **Individual User Accounts**.</span></span>

![显示"单个用户帐户单选"所选的新建项目对话框](accconfirm/_static/indiv.png)

---

### <a name="net-core-cli-project-creation-for-macos-and-linux"></a><span data-ttu-id="73be8-123">.NET 核心 CLI macOS 和 Linux 的项目创建</span><span class="sxs-lookup"><span data-stu-id="73be8-123">.NET Core CLI project creation for macOS and Linux</span></span>

<span data-ttu-id="73be8-124">如果你使用 CLI 或 SQLite，运行以下命令在命令窗口中：</span><span class="sxs-lookup"><span data-stu-id="73be8-124">If you're using the CLI or SQLite, run the following in a command window:</span></span>

```console
dotnet new mvc --auth Individual
```

* <span data-ttu-id="73be8-125">`--auth Individual`指定的单个用户帐户模板。</span><span class="sxs-lookup"><span data-stu-id="73be8-125">`--auth Individual` specifies the Individual User Accounts template.</span></span>
* <span data-ttu-id="73be8-126">在 Windows 上，添加`-uld`选项。</span><span class="sxs-lookup"><span data-stu-id="73be8-126">On Windows, add the `-uld` option.</span></span> <span data-ttu-id="73be8-127">`-uld`选项创建 LocalDB 连接字符串，而不是 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="73be8-127">The `-uld` option creates a LocalDB connection string rather than a SQLite DB.</span></span>
* <span data-ttu-id="73be8-128">运行`new mvc --help`以获取有关此命令帮助。</span><span class="sxs-lookup"><span data-stu-id="73be8-128">Run `new mvc --help` to get help on this command.</span></span>

## <a name="test-new-user-registration"></a><span data-ttu-id="73be8-129">测试新的用户注册</span><span class="sxs-lookup"><span data-stu-id="73be8-129">Test new user registration</span></span>

<span data-ttu-id="73be8-130">运行应用程序中，选择**注册**链接，并注册用户。</span><span class="sxs-lookup"><span data-stu-id="73be8-130">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="73be8-131">按照运行实体框架核心迁移的说明。</span><span class="sxs-lookup"><span data-stu-id="73be8-131">Follow the instructions to run Entity Framework Core migrations.</span></span> <span data-ttu-id="73be8-132">此时，电子邮件的唯一验证是使用[[EmailAddress]](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute)属性。</span><span class="sxs-lookup"><span data-stu-id="73be8-132">At this  point, the only validation on the email is with the [[EmailAddress]](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="73be8-133">在提交注册后，登录到应用程序。</span><span class="sxs-lookup"><span data-stu-id="73be8-133">After you submit the registration, you are logged into the app.</span></span> <span data-ttu-id="73be8-134">更高版本在教程中，我们将更改此以便验证其电子邮件之后才新用户无法登录。</span><span class="sxs-lookup"><span data-stu-id="73be8-134">Later in the tutorial, we'll change this so new users cannot log in until their email has been validated.</span></span>

## <a name="view-the-identity-database"></a><span data-ttu-id="73be8-135">查看标识数据库</span><span class="sxs-lookup"><span data-stu-id="73be8-135">View the Identity database</span></span>

# <a name="sql-servertabsql-server"></a>[<span data-ttu-id="73be8-136">SQL Server</span><span class="sxs-lookup"><span data-stu-id="73be8-136">SQL Server</span></span>](#tab/sql-server)

* <span data-ttu-id="73be8-137">从**视图**菜单上，选择**SQL Server 对象资源管理器**(SSOX)。</span><span class="sxs-lookup"><span data-stu-id="73be8-137">From the **View** menu, select **SQL Server Object Explorer** (SSOX).</span></span> 
* <span data-ttu-id="73be8-138">导航到**(localdb) MSSQLLocalDB (SQL Server 13)**。</span><span class="sxs-lookup"><span data-stu-id="73be8-138">Navigate to **(localdb)MSSQLLocalDB(SQL Server 13)**.</span></span> <span data-ttu-id="73be8-139">右键单击**dbo。AspNetUsers** > **查看数据**:</span><span class="sxs-lookup"><span data-stu-id="73be8-139">Right-click on **dbo.AspNetUsers** > **View Data**:</span></span>

![在 AspNetUsers 表中 SQL Server 对象资源管理器的上下文菜单](accconfirm/_static/ssox.png)

<span data-ttu-id="73be8-141">请注意`EmailConfirmed`字段是`False`。</span><span class="sxs-lookup"><span data-stu-id="73be8-141">Note the `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="73be8-142">你可能想要再次在下一步中使用此电子邮件，当应用程序发送确认电子邮件。</span><span class="sxs-lookup"><span data-stu-id="73be8-142">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="73be8-143">右键单击行并选择**删除**。</span><span class="sxs-lookup"><span data-stu-id="73be8-143">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="73be8-144">删除电子邮件别名现在将使容易在以下步骤。</span><span class="sxs-lookup"><span data-stu-id="73be8-144">Deleting the email alias now will make it easier in the following steps.</span></span>

# <a name="sqlitetabsqlite"></a>[<span data-ttu-id="73be8-145">SQLite</span><span class="sxs-lookup"><span data-stu-id="73be8-145">SQLite</span></span>](#tab/sqlite)

<span data-ttu-id="73be8-146">请参阅[ASP.NET 核心 MVC 项目中使用 SQLite](xref:tutorials/first-mvc-app-xplat/working-with-sql)有关说明如何查看 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="73be8-146">See [Working with SQLite in an ASP.NET Core MVC project](xref:tutorials/first-mvc-app-xplat/working-with-sql) for instructions on how to view the SQLite DB.</span></span> 

---

## <a name="require-ssl-and-setup-iis-express-for-ssl"></a><span data-ttu-id="73be8-147">需要 SSL 和 ssl 设置 IIS Express</span><span class="sxs-lookup"><span data-stu-id="73be8-147">Require SSL and setup IIS Express for SSL</span></span>

<span data-ttu-id="73be8-148">请参阅[强制实施 SSL](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="73be8-148">See [Enforcing SSL](xref:security/enforcing-ssl).</span></span>

<a name="prevent-login-at-registration"></a>
## <a name="require-email-confirmation"></a><span data-ttu-id="73be8-149">需要电子邮件确认</span><span class="sxs-lookup"><span data-stu-id="73be8-149">Require email confirmation</span></span>

<span data-ttu-id="73be8-150">它是一种最佳做法以确认新的用户注册，以验证它们不模拟其他人的电子邮件 （即，它们尚未注册与其他人的电子邮件）。</span><span class="sxs-lookup"><span data-stu-id="73be8-150">It's a best practice to confirm the email of a new user registration to verify they are not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="73be8-151">假设有论坛，并且你想阻止"yli@example.com"发件人将注册为"nolivetto@contoso.com。"</span><span class="sxs-lookup"><span data-stu-id="73be8-151">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com."</span></span> <span data-ttu-id="73be8-152">而无需电子邮件确认"nolivetto@contoso.com"无法从您的应用程序获取不需要的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="73be8-152">Without email confirmation, "nolivetto@contoso.com" could get unwanted email from your app.</span></span> <span data-ttu-id="73be8-153">假设用户意外注册为"ylo@example.com"和未注意到拼写错误的"yli"，它们将无法使用恢复密码，因为此应用程序没有正确的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="73be8-153">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli," they wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="73be8-154">电子邮件确认从机器人提供有限的保护，并不确定的垃圾邮件制造者具有许多工作电子邮件别名，它们可用于注册从提供保护。</span><span class="sxs-lookup"><span data-stu-id="73be8-154">Email confirmation provides only limited protection from bots and doesn't provide protection from determined spammers who have many working email aliases they can use to register.</span></span>

<span data-ttu-id="73be8-155">你通常想要使不能在有确认电子邮件之前发布到网站的任何数据的新用户。</span><span class="sxs-lookup"><span data-stu-id="73be8-155">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span> 

<span data-ttu-id="73be8-156">更新`ConfigureServices`需要确认电子邮件：</span><span class="sxs-lookup"><span data-stu-id="73be8-156">Update `ConfigureServices` to require a confirmed email:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="73be8-157">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="73be8-157">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

[!code-csharp[Main](accconfirm/sample/WebPW/Startup.cs?name=snippet1&highlight=6-9)]


# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="73be8-158">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="73be8-158">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[!code-csharp[Main](accconfirm/sample/WebApp1/Startup.cs?name=snippet1&highlight=13-16)]

---

 
```csharp
config.SignIn.RequireConfirmedEmail = true;
```
<span data-ttu-id="73be8-159">前面的行可防止已注册的用户正在登录，直到其电子邮件进行确认。</span><span class="sxs-lookup"><span data-stu-id="73be8-159">The preceding line prevents registered users from being logged in until their email is confirmed.</span></span> <span data-ttu-id="73be8-160">但是，该行不会阻止新用户登录后它们注册。</span><span class="sxs-lookup"><span data-stu-id="73be8-160">However, that line does not prevent new users from being logged in after they register.</span></span> <span data-ttu-id="73be8-161">用户注册后，该默认代码将记录在用户。</span><span class="sxs-lookup"><span data-stu-id="73be8-161">The default code logs in a user after they register.</span></span> <span data-ttu-id="73be8-162">一旦他们注销时，他们将不能再次登录直到它们注册。</span><span class="sxs-lookup"><span data-stu-id="73be8-162">Once they log out, they won't be able to log in again until they register.</span></span> <span data-ttu-id="73be8-163">更高版本在教程中我们将更改代码，因此新注册的用户是**不**登录。</span><span class="sxs-lookup"><span data-stu-id="73be8-163">Later in the tutorial we'll change the code so newly registered user are **not** logged in.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="73be8-164">配置电子邮件提供商</span><span class="sxs-lookup"><span data-stu-id="73be8-164">Configure email provider</span></span>

<span data-ttu-id="73be8-165">在本教程中，使用 SendGrid 发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="73be8-165">In this tutorial, SendGrid is used to send email.</span></span> <span data-ttu-id="73be8-166">你需要一个 SendGrid 帐户和密钥用于发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="73be8-166">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="73be8-167">你可以使用其他电子邮件提供商。</span><span class="sxs-lookup"><span data-stu-id="73be8-167">You can use other email providers.</span></span> <span data-ttu-id="73be8-168">ASP.NET 核心 2.x 包括`System.Net.Mail`，这允许你从你的应用程序发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="73be8-168">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="73be8-169">我们建议你使用 SendGrid 或另一个电子邮件服务发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="73be8-169">We recommend you use SendGrid or another email service to send email.</span></span>

<span data-ttu-id="73be8-170">[选项模式](xref:fundamentals/configuration#options-config-objects)用于访问的用户帐户和密钥设置。</span><span class="sxs-lookup"><span data-stu-id="73be8-170">The [Options pattern](xref:fundamentals/configuration#options-config-objects) is used to access the user account and key settings.</span></span> <span data-ttu-id="73be8-171">有关详细信息，请参阅[配置](xref:fundamentals/configuration)。</span><span class="sxs-lookup"><span data-stu-id="73be8-171">For more information, see [configuration](xref:fundamentals/configuration).</span></span>

<span data-ttu-id="73be8-172">创建一个类以提取安全的电子邮件密钥。</span><span class="sxs-lookup"><span data-stu-id="73be8-172">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="73be8-173">对于此示例，`AuthMessageSenderOptions`中创建类*Services/AuthMessageSenderOptions.cs*文件。</span><span class="sxs-lookup"><span data-stu-id="73be8-173">For this sample, the `AuthMessageSenderOptions` class is created in the *Services/AuthMessageSenderOptions.cs* file.</span></span>

[!code-csharp[Main](accconfirm/sample/WebApp1/Services/AuthMessageSenderOptions.cs?name=snippet1)]

<span data-ttu-id="73be8-174">设置`SendGridUser`和`SendGridKey`与[机密管理器工具](../app-secrets.md)。</span><span class="sxs-lookup"><span data-stu-id="73be8-174">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](../app-secrets.md).</span></span> <span data-ttu-id="73be8-175">例如: </span><span class="sxs-lookup"><span data-stu-id="73be8-175">For example:</span></span>

```none
C:\WebAppl\src\WebApp1>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="73be8-176">在 Windows 上，密钥管理器存储在你的键/值对*secrets.json* %APPDATA%/Microsoft/UserSecrets/ < WebAppName userSecretsId > 目录中的文件。</span><span class="sxs-lookup"><span data-stu-id="73be8-176">On Windows, Secret Manager stores your keys/value pairs in a *secrets.json* file in the %APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId> directory.</span></span>

<span data-ttu-id="73be8-177">内容*secrets.json*未加密文件。</span><span class="sxs-lookup"><span data-stu-id="73be8-177">The contents of the *secrets.json* file are not encrypted.</span></span> <span data-ttu-id="73be8-178">*Secrets.json*文件如下所示 (`SendGridKey`值已删除。)</span><span class="sxs-lookup"><span data-stu-id="73be8-178">The *secrets.json* file is shown below (the `SendGridKey` value has been removed.)</span></span>

  ```json
  {
    "SendGridUser": "RickAndMSFT",
    "SendGridKey": "<key removed>"
  }
  ```

### <a name="configure-startup-to-use-authmessagesenderoptions"></a><span data-ttu-id="73be8-179">配置启动要使用 AuthMessageSenderOptions</span><span class="sxs-lookup"><span data-stu-id="73be8-179">Configure startup to use AuthMessageSenderOptions</span></span>

<span data-ttu-id="73be8-180">添加`AuthMessageSenderOptions`到末尾的服务容器`ConfigureServices`中的方法*Startup.cs*文件：</span><span class="sxs-lookup"><span data-stu-id="73be8-180">Add `AuthMessageSenderOptions` to the service container at the end of the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="73be8-181">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="73be8-181">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

[!code-csharp[Main](accconfirm/sample/WebPW/Startup.cs?name=snippet1&highlight=18)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="73be8-182">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="73be8-182">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)
[!code-csharp[Main](accconfirm/sample/WebApp1/Startup.cs?name=snippet1&highlight=26)]

---

### <a name="configure-the-authmessagesender-class"></a><span data-ttu-id="73be8-183">配置 AuthMessageSender 类</span><span class="sxs-lookup"><span data-stu-id="73be8-183">Configure the AuthMessageSender class</span></span>

<span data-ttu-id="73be8-184">本教程演示如何添加通过电子邮件通知[SendGrid](https://sendgrid.com/)，但是你可以发送电子邮件使用 SMTP 和其他机制。</span><span class="sxs-lookup"><span data-stu-id="73be8-184">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

* <span data-ttu-id="73be8-185">安装`SendGrid`NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="73be8-185">Install the `SendGrid` NuGet package.</span></span> <span data-ttu-id="73be8-186">从程序包管理器控制台中，输入以下以下命令：</span><span class="sxs-lookup"><span data-stu-id="73be8-186">From the Package Manager Console,  enter the following the following command:</span></span>

  `Install-Package SendGrid`

* <span data-ttu-id="73be8-187">请参阅[免费开始使用 SendGrid](https://sendgrid.com/free/)注册一个免费的 SendGrid 帐户。</span><span class="sxs-lookup"><span data-stu-id="73be8-187">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

#### <a name="configure-sendgrid"></a><span data-ttu-id="73be8-188">配置了 SendGrid</span><span class="sxs-lookup"><span data-stu-id="73be8-188">Configure SendGrid</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="73be8-189">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="73be8-189">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

* <span data-ttu-id="73be8-190">中添加代码*Services/EmailSender.cs*类似于以下配置 SendGrid:</span><span class="sxs-lookup"><span data-stu-id="73be8-190">Add code in *Services/EmailSender.cs* similar to the following to configure SendGrid:</span></span>

[!code-csharp[Main](accconfirm/sample/WebPW/Services/EmailSender.cs)]


# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="73be8-191">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="73be8-191">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)
* <span data-ttu-id="73be8-192">中添加代码*Services/MessageServices.cs*类似于以下配置 SendGrid:</span><span class="sxs-lookup"><span data-stu-id="73be8-192">Add code in *Services/MessageServices.cs* similar to the following to configure SendGrid:</span></span>

[!code-csharp[Main](accconfirm/sample/WebApp1/Services/MessageServices.cs)]

---

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="73be8-193">启用帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="73be8-193">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="73be8-194">已为帐户确认和密码恢复代码的模板。</span><span class="sxs-lookup"><span data-stu-id="73be8-194">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="73be8-195">查找`[HttpPost] Register`中的方法*AccountController.cs*文件。</span><span class="sxs-lookup"><span data-stu-id="73be8-195">Find the `[HttpPost] Register` method in the  *AccountController.cs* file.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="73be8-196">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="73be8-196">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="73be8-197">可防止新注册的用户自动登录通过注释掉以下行：</span><span class="sxs-lookup"><span data-stu-id="73be8-197">Prevent newly registered users from being automatically logged on by commenting out the following line:</span></span>

```csharp 
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="73be8-198">突出显示的已更改行还会显示完整的方法：</span><span class="sxs-lookup"><span data-stu-id="73be8-198">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[Main](accconfirm/sample/WebPW/Controllers/AccountController.cs?highlight=19&name=snippet_Register)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="73be8-199">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="73be8-199">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="73be8-200">取消注释的代码，若要启用帐户确认。</span><span class="sxs-lookup"><span data-stu-id="73be8-200">Uncomment the code to enable account confirmation.</span></span>

[!code-csharp[Main](accconfirm/sample/WebApp1/Controllers/AccountController.cs?highlight=16-25&name=snippet_Register)]

<span data-ttu-id="73be8-201">注意： 我们要还阻止新注册的用户自动登录通过注释掉以下行：</span><span class="sxs-lookup"><span data-stu-id="73be8-201">Note: We're also preventing a newly-registered user from being automatically logged on by commenting out the following line:</span></span>

```csharp 
//await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="73be8-202">通过对取消注释中的代码中启用密码恢复`ForgotPassword`中的操作*controllers/Accountcontroller.cs*文件。</span><span class="sxs-lookup"><span data-stu-id="73be8-202">Enable password recovery by uncommenting the code in the `ForgotPassword` action in the *Controllers/AccountController.cs* file.</span></span>

[!code-csharp[Main](accconfirm/sample/WebApp1/Controllers/AccountController.cs?highlight=17-23&name=snippet_ForgotPassword)]

<span data-ttu-id="73be8-203">取消注释中的窗体元素*Views/Account/ForgotPassword.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="73be8-203">Uncomment the form element in *Views/Account/ForgotPassword.cshtml*.</span></span> <span data-ttu-id="73be8-204">你可能想要删除`<p> For more information on how to enable reset password ... </p>`元素，其中包含此文章的链接。</span><span class="sxs-lookup"><span data-stu-id="73be8-204">You might want to remove the `<p> For more information on how to enable reset password ... </p>` element which contains a link to this article.</span></span>

[!code-html[Main](accconfirm/sample/WebApp1/Views/Account/ForgotPassword.cshtml?highlight=7-10,12,28)]

---

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="73be8-205">注册、 确认电子邮件，以及重置密码</span><span class="sxs-lookup"><span data-stu-id="73be8-205">Register, confirm email, and reset password</span></span>

<span data-ttu-id="73be8-206">运行该 web 应用，并测试帐户确认和密码恢复流。</span><span class="sxs-lookup"><span data-stu-id="73be8-206">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="73be8-207">运行应用并注册新的用户</span><span class="sxs-lookup"><span data-stu-id="73be8-207">Run the app and register a new user</span></span>

 ![Web 应用程序帐户注册视图](accconfirm/_static/loginaccconfirm1.png)

* <span data-ttu-id="73be8-209">检查你的帐户确认链接的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="73be8-209">Check your email for the account confirmation link.</span></span> <span data-ttu-id="73be8-210">请参阅[调试电子邮件](#debug)如果你不会获得电子邮件。</span><span class="sxs-lookup"><span data-stu-id="73be8-210">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="73be8-211">单击链接以确认你的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="73be8-211">Click the link to confirm your email.</span></span>
* <span data-ttu-id="73be8-212">登录你的电子邮件和密码。</span><span class="sxs-lookup"><span data-stu-id="73be8-212">Log in with your email and password.</span></span>
* <span data-ttu-id="73be8-213">注销。</span><span class="sxs-lookup"><span data-stu-id="73be8-213">Log off.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="73be8-214">查看管理页</span><span class="sxs-lookup"><span data-stu-id="73be8-214">View the manage page</span></span>

<span data-ttu-id="73be8-215">在浏览器中选择你的用户名：![用户同名的浏览器窗口](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="73be8-215">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="73be8-216">你可能需要展开导航栏，以查看用户名称。</span><span class="sxs-lookup"><span data-stu-id="73be8-216">You might need to expand the navbar to see user name.</span></span>

![导航栏](accconfirm/_static/x.png)

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="73be8-218">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="73be8-218">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="73be8-219">管理页显示与**配置文件**选定选项卡。</span><span class="sxs-lookup"><span data-stu-id="73be8-219">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="73be8-220">**电子邮件**显示一个复选框，表明电子邮件已确认。</span><span class="sxs-lookup"><span data-stu-id="73be8-220">The **Email** shows a check box indicating the email has been confirmed.</span></span> 

![管理页](accconfirm/_static/rick2.png)


# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="73be8-222">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="73be8-222">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="73be8-223">我们将在本教程后面部分讨论此页。</span><span class="sxs-lookup"><span data-stu-id="73be8-223">We'll talk about this page later in the tutorial.</span></span>
<span data-ttu-id="73be8-224">![管理页](accconfirm/_static/rick2.png)</span><span class="sxs-lookup"><span data-stu-id="73be8-224">![manage page](accconfirm/_static/rick2.png)</span></span>

---

### <a name="test-password-reset"></a><span data-ttu-id="73be8-225">测试密码重置</span><span class="sxs-lookup"><span data-stu-id="73be8-225">Test password reset</span></span>

* <span data-ttu-id="73be8-226">如果你要登录，选择**注销**。</span><span class="sxs-lookup"><span data-stu-id="73be8-226">If you're logged in, select **Logout**.</span></span>  
* <span data-ttu-id="73be8-227">选择**登录**链接并选择**忘记了密码？**链接。</span><span class="sxs-lookup"><span data-stu-id="73be8-227">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="73be8-228">输入用于注册帐户的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="73be8-228">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="73be8-229">将发送一封电子邮件包含要重置密码的链接。</span><span class="sxs-lookup"><span data-stu-id="73be8-229">An email with a link to reset your password will be sent.</span></span> <span data-ttu-id="73be8-230">请检查你的电子邮件，单击链接以重置密码。</span><span class="sxs-lookup"><span data-stu-id="73be8-230">Check your email and click the link to reset your password.</span></span>  <span data-ttu-id="73be8-231">你的密码已成功重置后，你可以是使用你的电子邮件和新密码登录。</span><span class="sxs-lookup"><span data-stu-id="73be8-231">After your password has been successfully reset, you can login with your email and new password.</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="73be8-232">调试电子邮件</span><span class="sxs-lookup"><span data-stu-id="73be8-232">Debug email</span></span>

<span data-ttu-id="73be8-233">如果无法获取电子邮件工作：</span><span class="sxs-lookup"><span data-stu-id="73be8-233">If you can't get email working:</span></span>

* <span data-ttu-id="73be8-234">查看[电子邮件活动](https://sendgrid.com/docs/User_Guide/email_activity.html)页。</span><span class="sxs-lookup"><span data-stu-id="73be8-234">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="73be8-235">请检查垃圾邮件文件夹。</span><span class="sxs-lookup"><span data-stu-id="73be8-235">Check your spam folder.</span></span>
* <span data-ttu-id="73be8-236">请尝试其他电子邮件提供程序 （Microsoft、 Yahoo、 Gmail 等） 上的另一个电子邮件别名</span><span class="sxs-lookup"><span data-stu-id="73be8-236">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="73be8-237">创建[控制台应用程序发送电子邮件](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html)。</span><span class="sxs-lookup"><span data-stu-id="73be8-237">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html).</span></span>
* <span data-ttu-id="73be8-238">尝试发送到不同的电子邮件帐户。</span><span class="sxs-lookup"><span data-stu-id="73be8-238">Try sending to different email accounts.</span></span>

<span data-ttu-id="73be8-239">**注意：**安全最佳做法是不使用生产中测试和开发的机密。</span><span class="sxs-lookup"><span data-stu-id="73be8-239">**Note:** A security best practice is to not use production secrets in test and development.</span></span> <span data-ttu-id="73be8-240">如果将应用发布到 Azure，你可以设置 SendGrid 机密与 Azure Web 应用门户中的应用程序设置。</span><span class="sxs-lookup"><span data-stu-id="73be8-240">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="73be8-241">配置系统，则安装程序以从环境变量中读取项。</span><span class="sxs-lookup"><span data-stu-id="73be8-241">The configuration system is setup to read keys from environment variables.</span></span>

## <a name="prevent-login-at-registration"></a><span data-ttu-id="73be8-242">防止在注册的登录名</span><span class="sxs-lookup"><span data-stu-id="73be8-242">Prevent login at registration</span></span>

<span data-ttu-id="73be8-243">通过当前的模板，用户完成注册表单中，将它们记录在 （通过身份验证）。</span><span class="sxs-lookup"><span data-stu-id="73be8-243">With the current templates, once a user completes the registration form, they are logged in (authenticated).</span></span> <span data-ttu-id="73be8-244">你通常想要在中记录日志之前，请确认其电子邮件。</span><span class="sxs-lookup"><span data-stu-id="73be8-244">You generally want to confirm their email before logging them in.</span></span> <span data-ttu-id="73be8-245">在下面的部分中，我们将修改的代码需要新的用户具有的确认电子邮件，然后将它们记录在。</span><span class="sxs-lookup"><span data-stu-id="73be8-245">In the section below, we will modify the code to require new users have a confirmed email before they are logged in.</span></span> <span data-ttu-id="73be8-246">更新`[HttpPost] Login`中的操作*AccountController.cs*包含以下突出显示更改的文件。</span><span class="sxs-lookup"><span data-stu-id="73be8-246">Update the `[HttpPost] Login` action in the *AccountController.cs* file with the following highlighted changes.</span></span>

[!code-csharp[Main](accconfirm/sample/WebApp1/Controllers/AccountController.cs?highlight=11-21&name=snippet_Login)]

<span data-ttu-id="73be8-247">**注意：**安全最佳做法是不使用生产中测试和开发的机密。</span><span class="sxs-lookup"><span data-stu-id="73be8-247">**Note:** A security best practice is to not use production secrets in test and development.</span></span> <span data-ttu-id="73be8-248">如果将应用发布到 Azure，你可以设置 SendGrid 机密与 Azure Web 应用门户中的应用程序设置。</span><span class="sxs-lookup"><span data-stu-id="73be8-248">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="73be8-249">配置系统，则安装程序以从环境变量中读取项。</span><span class="sxs-lookup"><span data-stu-id="73be8-249">The configuration system is setup to read keys from environment variables.</span></span>


## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="73be8-250">合并社交和本地登录帐户</span><span class="sxs-lookup"><span data-stu-id="73be8-250">Combine social and local login accounts</span></span>

<span data-ttu-id="73be8-251">注意： 本部分仅适用于 ASP.NET Core 1.x。</span><span class="sxs-lookup"><span data-stu-id="73be8-251">Note: This section applies only to ASP.NET Core 1.x.</span></span> <span data-ttu-id="73be8-252">有关 ASP.NET 核心 2.x，请参阅[这](https://github.com/aspnet/Docs/issues/3753)问题。</span><span class="sxs-lookup"><span data-stu-id="73be8-252">For ASP.NET Core 2.x, see [this](https://github.com/aspnet/Docs/issues/3753) issue.</span></span>

<span data-ttu-id="73be8-253">若要完成此部分，必须先启用外部身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="73be8-253">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="73be8-254">请参阅[使用 Facebook、 Google 和其他外部提供程序启用身份验证](social/index.md)。</span><span class="sxs-lookup"><span data-stu-id="73be8-254">See [Enabling authentication using Facebook, Google and other external providers](social/index.md).</span></span>

<span data-ttu-id="73be8-255">你可以通过单击电子邮件链接组合本地和社交帐户。</span><span class="sxs-lookup"><span data-stu-id="73be8-255">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="73be8-256">按以下顺序"RickAndMSFT@gmail.com"首先创建为本地登录名; 但是，你可以创建帐户作为的社交登录名，然后再添加本地登录名。</span><span class="sxs-lookup"><span data-stu-id="73be8-256">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web 应用程序：RickAndMSFT@gmail.com身份验证的用户](accconfirm/_static/rick.png)

<span data-ttu-id="73be8-258">单击**管理**链接。</span><span class="sxs-lookup"><span data-stu-id="73be8-258">Click on the **Manage** link.</span></span> <span data-ttu-id="73be8-259">请注意与此帐户关联 0 外部 （社交登录名）。</span><span class="sxs-lookup"><span data-stu-id="73be8-259">Note the 0 external (social logins) associated with this account.</span></span>

![管理视图](accconfirm/_static/manage.png)

<span data-ttu-id="73be8-261">单击另一个登录服务的链接，接受应用程序请求。</span><span class="sxs-lookup"><span data-stu-id="73be8-261">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="73be8-262">下图中，在 Facebook 是外部身份验证提供程序：</span><span class="sxs-lookup"><span data-stu-id="73be8-262">In the image below, Facebook is the external authentication provider:</span></span>

![管理外部登录名视图列出 Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="73be8-264">已经合并两个帐户。</span><span class="sxs-lookup"><span data-stu-id="73be8-264">The two accounts have been combined.</span></span> <span data-ttu-id="73be8-265">你将能够使用任一帐户登录。</span><span class="sxs-lookup"><span data-stu-id="73be8-265">You will be able to log on with either account.</span></span> <span data-ttu-id="73be8-266">你可能希望用户身份验证服务中的其社交日志已关闭，或已对其社交帐户失去访问更有可能的情况下添加本地帐户。</span><span class="sxs-lookup"><span data-stu-id="73be8-266">You might want your users to add local accounts in case their social log in authentication service is down, or more likely they have lost access to their social account.</span></span>
