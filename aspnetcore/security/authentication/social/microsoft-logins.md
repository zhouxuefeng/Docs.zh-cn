---
title: "Microsoft 帐户外部登录设置"
author: rick-anderson
description: 
keywords: "ASP.NET 核心"
ms.author: riande
manager: wpickett
ms.date: 08/24/2017
ms.topic: article
ms.assetid: 66DB4B94-C78C-4005-BA03-3D982B87C268
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 25ada568a8629b82eb55ca27a55a9ac0c5fb38b2
ms.sourcegitcommit: 74e22e08e3b08cb576e5184d16f4af5656c13c0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2017
---
# <a name="configuring-microsoft-account-authentication"></a><span data-ttu-id="8ffb2-103">配置 Microsoft 帐户身份验证</span><span class="sxs-lookup"><span data-stu-id="8ffb2-103">Configuring Microsoft Account authentication</span></span>

<a name=security-authentication-microsoft-logins></a>

<span data-ttu-id="8ffb2-104">通过[Valeriy Novytskyy](https://github.com/01binary)和[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8ffb2-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8ffb2-105">本教程演示如何使用户可以使用示例 ASP.NET 核心 2.0 项目上创建其 Microsoft 帐户登录[上一页](index.md)。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-105">This tutorial shows you how to enable your users to sign in with their Microsoft account using a sample ASP.NET Core 2.0 project created on the [previous page](index.md).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="8ffb2-106">在 Microsoft 开发人员门户中创建应用程序</span><span class="sxs-lookup"><span data-stu-id="8ffb2-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="8ffb2-107">导航到[https://apps.dev.microsoft.com](https://apps.dev.microsoft.com)和创建或登录到 Microsoft 帐户：</span><span class="sxs-lookup"><span data-stu-id="8ffb2-107">Navigate to [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com) and create or sign into a Microsoft account:</span></span>

![登录对话框](index/_static/MicrosoftDevLogin.png)

<span data-ttu-id="8ffb2-109">如果你尚没有 Microsoft 帐户，请点击**[创建一个 ！](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=13&ct=1478151035&rver=6.7.6643.0&wp=SAPI_LONG&wreply=https%3a%2f%2fapps.dev.microsoft.com%2fLoginPostBack&id=293053&aadredir=1&contextid=D70D4F21246BAB50&bk=1478151036&uiflavor=web&uaid=f0c3de863a914c358b8dc01b1ff49e85&mkt=EN-US&lc=1033&lic=1)**</span><span class="sxs-lookup"><span data-stu-id="8ffb2-109">If you don't already have a Microsoft account, tap **[Create one!](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=13&ct=1478151035&rver=6.7.6643.0&wp=SAPI_LONG&wreply=https%3a%2f%2fapps.dev.microsoft.com%2fLoginPostBack&id=293053&aadredir=1&contextid=D70D4F21246BAB50&bk=1478151036&uiflavor=web&uaid=f0c3de863a914c358b8dc01b1ff49e85&mkt=EN-US&lc=1033&lic=1)**</span></span> <span data-ttu-id="8ffb2-110">在登录后你将重定向到**我的应用程序**页：</span><span class="sxs-lookup"><span data-stu-id="8ffb2-110">After signing in you are redirected to **My applications** page:</span></span>

![Microsoft 开发人员门户中，打开 Microsoft Edge 中](index/_static/MicrosoftDev.png)

* <span data-ttu-id="8ffb2-112">点击**添加应用程序**在右上角，然后输入你**应用程序名称**和**联系人电子邮件**:</span><span class="sxs-lookup"><span data-stu-id="8ffb2-112">Tap **Add an app** in the upper right corner and enter your **Application Name** and **Contact Email**:</span></span>

![新的应用程序注册对话框](index/_static/MicrosoftDevAppCreate.png)

* <span data-ttu-id="8ffb2-114">对于此教程的目的，清除**指导安装程序**复选框。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-114">For the purposes of this tutorial, clear the **Guided Setup** check box.</span></span>

* <span data-ttu-id="8ffb2-115">点击**创建**继续**注册**页：</span><span class="sxs-lookup"><span data-stu-id="8ffb2-115">Tap **Create** to continue to the **Registration** page:</span></span>

![注册页](index/_static/MicrosoftDevAppReg.png)

* <span data-ttu-id="8ffb2-117">点击**添加平台**中**平台**部分，并选择**Web**平台：</span><span class="sxs-lookup"><span data-stu-id="8ffb2-117">Tap **Add Platform** in the **Platforms** section and select the **Web** platform:</span></span>

![添加平台对话框](index/_static/MicrosoftDevAppPlatform.png)

* <span data-ttu-id="8ffb2-119">在新**Web**平台部分中，输入与你开发的 URL */signin-microsoft*追加到**重定向 Url**字段 (例如： `https://localhost:44320/signin-microsoft`)。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-119">In the new **Web** platform section, enter your development URL with */signin-microsoft* appended into the **Redirect URLs** field (for example: `https://localhost:44320/signin-microsoft`).</span></span> <span data-ttu-id="8ffb2-120">本教程中稍后配置的 Microsoft 身份验证方案将自动处理请求在*/signin-microsoft*要实现的 OAuth 流路由：</span><span class="sxs-lookup"><span data-stu-id="8ffb2-120">The Microsoft authentication scheme configured later in this tutorial will automatically handle requests at */signin-microsoft* route to implement the OAuth flow:</span></span>

![Web 平台部分](index/_static/MicrosoftRedirectUri.png)

* <span data-ttu-id="8ffb2-122">点击**添加 URL**以确保该 URL 已添加。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-122">Tap **Add URL** to ensure the URL was added.</span></span>

* <span data-ttu-id="8ffb2-123">填写应用程序的任何其他设置，如有必要，并点击**保存**底部的页后，可以将更改保存到应用配置。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-123">Fill out any other application settings if necessary and tap **Save** at the bottom of the page to save changes to app configuration.</span></span>

* <span data-ttu-id="8ffb2-124">部署站点时你将需要重新访问**注册**页，并设置新的公共 URL。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-124">When deploying the site you'll need to revisit the **Registration** page and set a new public URL.</span></span>

## <a name="store-microsoft-application-id-and-password"></a><span data-ttu-id="8ffb2-125">存储 Microsoft 应用程序 Id 和密码</span><span class="sxs-lookup"><span data-stu-id="8ffb2-125">Store Microsoft Application Id and Password</span></span>

* <span data-ttu-id="8ffb2-126">请注意`Application Id`上显示**注册**页。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-126">Note the `Application Id` displayed on the **Registration** page.</span></span>

* <span data-ttu-id="8ffb2-127">点击**生成新密码**中**应用程序机密**部分。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-127">Tap **Generate New Password** in the **Application Secrets** section.</span></span> <span data-ttu-id="8ffb2-128">此时将显示一个框，其中你可以将复制的应用程序密码：</span><span class="sxs-lookup"><span data-stu-id="8ffb2-128">This displays a box where you can copy the application password:</span></span>

![新建生成的密码对话框](index/_static/MicrosoftDevPassword.png)

<span data-ttu-id="8ffb2-130">链接敏感设置，例如 Microsoft`Application ID`和`Password`到你应用程序配置中使用[机密 Manager](../../app-secrets.md)。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-130">Link sensitive settings like Microsoft `Application ID` and `Password` to your application configuration using the [Secret Manager](../../app-secrets.md).</span></span> <span data-ttu-id="8ffb2-131">对于此教程的目的，命名为令牌`Authentication:Microsoft:ApplicationId`和`Authentication:Microsoft:Password`。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-131">For the purposes of this tutorial, name the tokens `Authentication:Microsoft:ApplicationId` and `Authentication:Microsoft:Password`.</span></span>

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="8ffb2-132">配置 Microsoft 帐户身份验证</span><span class="sxs-lookup"><span data-stu-id="8ffb2-132">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="8ffb2-133">在本教程使用的项目模板可确保[Microsoft.AspNetCore.Authentication.Microsoft](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Microsoft)已安装包。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-133">The project template used in this tutorial ensures that [Microsoft.AspNetCore.Authentication.Microsoft](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Microsoft) package is already installed.</span></span>

* <span data-ttu-id="8ffb2-134">若要使用 Visual Studio 2017 安装此包，请右键单击项目并选择**管理 NuGet 包**。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-134">To install this package with Visual Studio 2017, right-click on the project and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="8ffb2-135">若要使用.NET 核心 CLI 安装，请在项目目录中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="8ffb2-135">To install with .NET Core CLI, execute the following in your project directory:</span></span>

   `dotnet add package Microsoft.AspNetCore.Authentication.Microsoft`

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="8ffb2-136">ASP.NET 核心 2.x</span><span class="sxs-lookup"><span data-stu-id="8ffb2-136">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="8ffb2-137">添加 Microsoft 帐户服务`ConfigureServices`中的方法*Startup.cs*文件：</span><span class="sxs-lookup"><span data-stu-id="8ffb2-137">Add the Microsoft Account service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddMicrosoftAccount(microsoftOptions =>
{
    microsoftOptions.ClientId = Configuration["Authentication:Microsoft:ApplicationId"];
    microsoftOptions.ClientSecret = Configuration["Authentication:Microsoft:Password"];
});
```

<span data-ttu-id="8ffb2-138">`AddAuthentication`方法仅应调用即可一次时添加多个身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-138">The `AddAuthentication` method should only be called once when adding multiple authentication providers.</span></span> <span data-ttu-id="8ffb2-139">对它的后续调用也可能会覆盖任何以前配置的[AuthenticationOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.authenticationoptions)属性。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-139">Subsequent calls to it have the potential of overriding any previously configured [AuthenticationOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.authenticationoptions) properties.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="8ffb2-140">ASP.NET 核心 1.x</span><span class="sxs-lookup"><span data-stu-id="8ffb2-140">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="8ffb2-141">添加在 Microsoft 帐户中间件`Configure`中的方法*Startup.cs*文件：</span><span class="sxs-lookup"><span data-stu-id="8ffb2-141">Add the Microsoft Account middleware in the `Configure` method in *Startup.cs* file:</span></span>

```csharp
app.UseMicrosoftAccountAuthentication(new MicrosoftAccountOptions()
{
    ClientId = Configuration["Authentication:Microsoft:ApplicationId"],
    ClientSecret = Configuration["Authentication:Microsoft:Password"]
});
```

---

<span data-ttu-id="8ffb2-142">尽管在 Microsoft 开发人员门户上使用的术语名称这些令牌`ApplicationId`和`Password`，它们会作为公开`ClientId`和`ClientSecret`到配置 API。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-142">Although the terminology used on Microsoft Developer Portal names these tokens `ApplicationId` and `Password`, they are exposed as `ClientId` and `ClientSecret` to the configuration API.</span></span>

<span data-ttu-id="8ffb2-143">请参阅[MicrosoftAccountOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.microsoftaccountoptions)支持 Microsoft 帐户身份验证的配置选项的详细信息的 API 参考。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-143">See the [MicrosoftAccountOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference for more information on configuration options supported by Microsoft Account authentication.</span></span> <span data-ttu-id="8ffb2-144">这可以用于请求有关用户的不同信息。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-144">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="8ffb2-145">使用 Microsoft 帐户登录</span><span class="sxs-lookup"><span data-stu-id="8ffb2-145">Sign in with Microsoft Account</span></span>

<span data-ttu-id="8ffb2-146">运行你的应用程序，然后单击**登录**。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-146">Run your application and click **Log in**.</span></span> <span data-ttu-id="8ffb2-147">将显示一个选项以使用 Microsoft 登录：</span><span class="sxs-lookup"><span data-stu-id="8ffb2-147">An option to sign in with Microsoft appears:</span></span>

![Web 应用程序日志中页： 未经过身份验证的用户](index/_static/DoneMicrosoft.png)

<span data-ttu-id="8ffb2-149">当你单击 on Microsoft 时，你将重定向到 Microsoft 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-149">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="8ffb2-150">（如果尚未登录），使用你的 Microsoft 帐户登录后你将会提示您允许此应用访问你的信息：</span><span class="sxs-lookup"><span data-stu-id="8ffb2-150">After signing in with your Microsoft Account (if not already signed in) you will be prompted to let the app access your info:</span></span>

![Microsoft 身份验证对话框](index/_static/MicrosoftLogin.png)

<span data-ttu-id="8ffb2-152">点击**是**，将重定向回 web 站点，你可以设置你的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-152">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="8ffb2-153">现在已在使用您的 Microsoft 凭据进行登录：</span><span class="sxs-lookup"><span data-stu-id="8ffb2-153">You are now logged in using your Microsoft credentials:</span></span>

![Web 应用程序： 身份验证的用户](index/_static/Done.png)

## <a name="troubleshooting"></a><span data-ttu-id="8ffb2-155">疑难解答</span><span class="sxs-lookup"><span data-stu-id="8ffb2-155">Troubleshooting</span></span>

* <span data-ttu-id="8ffb2-156">如果 Microsoft 帐户提供程序将你重定向到登录错误页，请注意错误标题和说明查询字符串参数紧接`#`（哈希标记） 的 Uri 中。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-156">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="8ffb2-157">尽管错误消息看起来，以指示 Microsoft 身份验证问题，但最常见的原因是你的应用程序与任何不匹配的 Uri**重定向 Uri**指定的**Web**平台.</span><span class="sxs-lookup"><span data-stu-id="8ffb2-157">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="8ffb2-158">**ASP.NET 核心 2.x 仅：**如果标识未通过调用配置`services.AddIdentity`中`ConfigureServices`，尝试进行身份验证将导致*ArgumentException： 必须提供 SignInScheme 选项*。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-158">**ASP.NET Core 2.x only:** If Identity is not configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="8ffb2-159">在本教程使用的项目模板可确保，这完成的。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-159">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="8ffb2-160">如果尚未通过应用初始迁移创建站点数据库，则会出现*处理请求时，数据库操作失败*错误。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-160">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="8ffb2-161">点击**应用迁移**创建数据库和刷新可跳过错误。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-161">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8ffb2-162">后续步骤</span><span class="sxs-lookup"><span data-stu-id="8ffb2-162">Next steps</span></span>

* <span data-ttu-id="8ffb2-163">本文介绍了你与 Microsoft 可以进行的验证。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-163">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="8ffb2-164">你可以遵循类似的方法进行身份验证使用其他提供商上列出[上一页](index.md)。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-164">You can follow a similar approach to authenticate with other providers listed on the [previous page](index.md).</span></span>

* <span data-ttu-id="8ffb2-165">一旦您的网站发布到 Azure web 应用时，你应该创建一个新`Password`Microsoft 开发人员门户中。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-165">Once you publish your web site to Azure web app, you should create a new `Password` in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="8ffb2-166">设置`Authentication:Microsoft:ApplicationId`和`Authentication:Microsoft:Password`作为在 Azure 门户中的应用程序设置。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-166">Set the `Authentication:Microsoft:ApplicationId` and `Authentication:Microsoft:Password` as application settings in the Azure portal.</span></span> <span data-ttu-id="8ffb2-167">配置系统设置以从环境变量中读取项。</span><span class="sxs-lookup"><span data-stu-id="8ffb2-167">The configuration system is set up to read keys from environment variables.</span></span>
