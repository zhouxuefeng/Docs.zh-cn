---
title: "在 ASP.NET Core Facebook 外部登录安装程序"
author: rick-anderson
description: "在 ASP.NET Core Facebook 外部登录安装程序"
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 08/01/2017
ms.topic: article
ms.assetid: 8c65179b-688c-4af1-8f5e-1862920cda95
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/facebook-logins
ms.openlocfilehash: 840513fc0b00c4aa478726faa6db8bdbffd561b1
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2017
---
# <a name="configuring-facebook-authentication"></a><span data-ttu-id="8f5bc-104">配置 Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="8f5bc-104">Configuring Facebook authentication</span></span>

<a name=security-authentication-facebook-logins></a>

<span data-ttu-id="8f5bc-105">作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8f5bc-105">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8f5bc-106">本教程演示如何使用户可以使用示例 ASP.NET 核心 2.0 项目上创建其 Facebook 帐户登录[上一页](index.md)。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-106">This tutorial shows you how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 2.0 project created on the [previous page](index.md).</span></span> <span data-ttu-id="8f5bc-107">我们首先按照创建 Facebook 应用程序 ID[官方步骤](https://www.facebook.com/unsupportedbrowser)。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-107">We start by creating a Facebook App ID by following the [official steps](https://www.facebook.com/unsupportedbrowser).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="8f5bc-108">在 Facebook 中创建应用程序</span><span class="sxs-lookup"><span data-stu-id="8f5bc-108">Create the app in Facebook</span></span>

*  <span data-ttu-id="8f5bc-109">导航到[开发人员的 Facebook](https://www.facebook.com/unsupportedbrowser)页上，并登录。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-109">Navigate to the [Facebook for Developers](https://www.facebook.com/unsupportedbrowser) page and sign in.</span></span> <span data-ttu-id="8f5bc-110">如果你还没有的 Facebook 帐户，使用**注册 Facebook**创建一个的登录页上的链接。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>

* <span data-ttu-id="8f5bc-111">点击**创建应用**创建一个新的应用程序 id。 右上角的按钮</span><span class="sxs-lookup"><span data-stu-id="8f5bc-111">Tap the **Create App** button in the upper right corner to create a new App ID.</span></span>

   ![Microsoft Edge 中打开 Facebook 开发人员门户](index/_static/FBMyApps.png)

* <span data-ttu-id="8f5bc-113">填写表单，然后点击**创建应用程序 ID**按钮。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-113">Fill out the form and tap the **Create App ID** button.</span></span>

   ![创建新的应用程序 ID 窗体](index/_static/FBNewAppId.png)

* <span data-ttu-id="8f5bc-115">当出现时**选择一个产品**提示，单击**Set Up**上**Facebook 登录名**卡。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-115">When presented with **Select a product** prompt, Click **Set Up** on the **Facebook Login** card.</span></span>

   ![产品安装程序页](index/_static/FBProductSetup.png)

* <span data-ttu-id="8f5bc-117">**快速入门**向导将启动与**选择一个平台**作为第一页。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-117">The **Quickstart** wizard will launch with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="8f5bc-118">现在跳过该向导，通过单击**设置**左侧菜单中的链接：</span><span class="sxs-lookup"><span data-stu-id="8f5bc-118">Bypass the wizard for now by clicking the **Settings** link in the menu on the left:</span></span>

   ![跳过快速启动](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="8f5bc-120">将向你提供**客户端的 OAuth 设置**页：</span><span class="sxs-lookup"><span data-stu-id="8f5bc-120">You are presented with the **Client OAuth Settings** page:</span></span>

![客户端的 OAuth 设置页](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="8f5bc-122">输入你的开发 URI 与*/signin-facebook*追加到**有效的 OAuth 重定向 Uri**字段 (例如： `https://localhost:44320/signin-facebook`)。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-122">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="8f5bc-123">本教程中稍后配置的 Facebook 身份验证将自动处理请求在*/signin-facebook*要实现的 OAuth 流路由。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-123">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

* <span data-ttu-id="8f5bc-124">单击**保存更改**。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-124">Click **Save Changes**.</span></span>

* <span data-ttu-id="8f5bc-125">单击**仪表板**左侧导航区域中的链接。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-125">Click the **Dashboard** link in the left navigation.</span></span> 

    <span data-ttu-id="8f5bc-126">在此页上，记下你`App ID`和你`App Secret`。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-126">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="8f5bc-127">你将添加到 ASP.NET 核心应用程序下一节中：</span><span class="sxs-lookup"><span data-stu-id="8f5bc-127">You will add both into your ASP.NET Core application in the next section:</span></span>

   ![Facebook 开发人员仪表板](index/_static/FBDashboard.png)

* <span data-ttu-id="8f5bc-129">部署站点时需要重新访问**Facebook 登录名**安装页并注册一个新的公共 URI。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-129">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-facebook-app-id-and-app-secret"></a><span data-ttu-id="8f5bc-130">存储 Facebook 应用程序 ID 和应用程序密码</span><span class="sxs-lookup"><span data-stu-id="8f5bc-130">Store Facebook App ID and App Secret</span></span>

<span data-ttu-id="8f5bc-131">链接敏感设置，例如 Facebook`App ID`和`App Secret`到你应用程序配置中使用[机密 Manager](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-131">Link sensitive settings like Facebook `App ID` and `App Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="8f5bc-132">对于此教程的目的，命名为令牌`Authentication:Facebook:AppId`和`Authentication:Facebook:AppSecret`。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-132">For the purposes of this tutorial, name the tokens `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`.</span></span>

## <a name="configure-facebook-authentication"></a><span data-ttu-id="8f5bc-133">配置 Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="8f5bc-133">Configure Facebook Authentication</span></span>

<span data-ttu-id="8f5bc-134">在本教程使用的项目模板可确保[Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook)已安装包。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-134">The project template used in this tutorial ensures that [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) package is already installed.</span></span>

* <span data-ttu-id="8f5bc-135">若要使用 Visual Studio 2017 安装此包，请右键单击项目并选择**管理 NuGet 包**。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-135">To install this package with Visual Studio 2017, right-click on the project and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="8f5bc-136">若要使用.NET 核心 CLI 安装，请在项目目录中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="8f5bc-136">To install with .NET Core CLI, execute the following in your project directory:</span></span>

   `dotnet add package Microsoft.AspNetCore.Authentication.Facebook`

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="8f5bc-137">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="8f5bc-137">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="8f5bc-138">添加中的 Facebook 服务`ConfigureServices`中的方法*Startup.cs*文件：</span><span class="sxs-lookup"><span data-stu-id="8f5bc-138">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

<span data-ttu-id="8f5bc-139">`AddAuthentication`方法仅应调用即可一次时添加多个身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-139">The `AddAuthentication` method should only be called once when adding multiple authentication providers.</span></span> <span data-ttu-id="8f5bc-140">对它的后续调用也可能会覆盖任何以前配置的[AuthenticationOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.authenticationoptions)属性。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-140">Subsequent calls to it have the potential of overriding any previously configured [AuthenticationOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.authenticationoptions) properties.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="8f5bc-141">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="8f5bc-141">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="8f5bc-142">添加在 Facebook 中间件`Configure`中的方法*Startup.cs*文件：</span><span class="sxs-lookup"><span data-stu-id="8f5bc-142">Add the Facebook middleware in the `Configure` method in *Startup.cs* file:</span></span>

```csharp
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

---

<span data-ttu-id="8f5bc-143">请参阅[FacebookOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.facebookoptions) Facebook 身份验证支持的配置选项的详细信息的 API 参考。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-143">See the [FacebookOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="8f5bc-144">配置选项可用于：</span><span class="sxs-lookup"><span data-stu-id="8f5bc-144">Configuration options can be used to:</span></span>

* <span data-ttu-id="8f5bc-145">请求有关用户的不同信息。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-145">Request different information about the user.</span></span>
* <span data-ttu-id="8f5bc-146">添加查询字符串参数，以自定义登录体验。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-146">Add query string arguments to customize the login experience.</span></span>

## <a name="sign-in-with-facebook"></a><span data-ttu-id="8f5bc-147">使用 Facebook 登录</span><span class="sxs-lookup"><span data-stu-id="8f5bc-147">Sign in with Facebook</span></span>

<span data-ttu-id="8f5bc-148">运行你的应用程序，然后单击**登录**。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-148">Run your application and click **Log in**.</span></span> <span data-ttu-id="8f5bc-149">你看到一个选项以使用 Facebook 登录。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-149">You see an option to sign in with Facebook.</span></span>

![Web 应用程序： 用户未经过身份验证](index/_static/DoneFacebook.png)

<span data-ttu-id="8f5bc-151">当你单击**Facebook**，你将重定向到 Facebook 进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="8f5bc-151">When you click on **Facebook**, you are redirected to Facebook for authentication:</span></span>

![Facebook 身份验证页面](index/_static/FBLogin.png)

<span data-ttu-id="8f5bc-153">默认情况下，Facebook 身份验证请求公共配置文件和电子邮件地址：</span><span class="sxs-lookup"><span data-stu-id="8f5bc-153">Facebook authentication requests public profile and email address by default:</span></span>

![Facebook 身份验证页面](index/_static/FBLoginDone.png)

<span data-ttu-id="8f5bc-155">输入你的 Facebook 凭据后你重定向回你的站点，你可以设置你的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-155">Once you enter your Facebook credentials you are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="8f5bc-156">现在已在使用你的 Facebook 凭据进行登录：</span><span class="sxs-lookup"><span data-stu-id="8f5bc-156">You are now logged in using your Facebook credentials:</span></span>

![Web 应用程序： 身份验证的用户](index/_static/Done.png)

## <a name="troubleshooting"></a><span data-ttu-id="8f5bc-158">疑难解答</span><span class="sxs-lookup"><span data-stu-id="8f5bc-158">Troubleshooting</span></span>

* <span data-ttu-id="8f5bc-159">**ASP.NET 核心 2.x 仅：**如果标识未通过调用配置`services.AddIdentity`中`ConfigureServices`，尝试进行身份验证将导致*ArgumentException： 必须提供 SignInScheme 选项*。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-159">**ASP.NET Core 2.x only:** If Identity is not configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="8f5bc-160">在本教程使用的项目模板可确保，这完成的。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-160">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="8f5bc-161">如果尚未通过应用初始迁移创建站点数据库，则获取*处理请求时，数据库操作失败*错误。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-161">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="8f5bc-162">点击**应用迁移**创建数据库和刷新可跳过错误。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-162">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8f5bc-163">后续步骤</span><span class="sxs-lookup"><span data-stu-id="8f5bc-163">Next steps</span></span>

* <span data-ttu-id="8f5bc-164">本文介绍了你使用 Facebook 可以进行的验证。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-164">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="8f5bc-165">你可以遵循类似的方法进行身份验证使用其他提供商上列出[上一页](index.md)。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-165">You can follow a similar approach to authenticate with other providers listed on the [previous page](index.md).</span></span>

* <span data-ttu-id="8f5bc-166">一旦您的网站发布到 Azure web 应用时，您应重置`AppSecret`Facebook 开发人员门户中。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-166">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="8f5bc-167">设置`Authentication:Facebook:AppId`和`Authentication:Facebook:AppSecret`作为在 Azure 门户中的应用程序设置。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-167">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="8f5bc-168">配置系统设置以从环境变量中读取项。</span><span class="sxs-lookup"><span data-stu-id="8f5bc-168">The configuration system is set up to read keys from environment variables.</span></span>
