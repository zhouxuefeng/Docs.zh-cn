---
title: "在 ASP.NET Core Google 外部登录安装程序"
author: rick-anderson
description: "在 ASP.NET Core Google 外部登录安装程序"
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 08/02/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/google-logins
ms.openlocfilehash: 7e37a8af4ae5a957483fa5f4a89ea4e8999a3d1d
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2017
---
# <a name="configuring-google-authentication-in-aspnet-core"></a><span data-ttu-id="04626-104">在 ASP.NET 核心中配置 Google 身份验证</span><span class="sxs-lookup"><span data-stu-id="04626-104">Configuring Google authentication in ASP.NET Core</span></span>

<a name=security-authentication-google-logins></a>

<span data-ttu-id="04626-105">作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="04626-105">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="04626-106">本教程演示如何使用户可以使用示例 ASP.NET 核心 2.0 项目上创建其 Google + 帐户登录[上一页](index.md)。</span><span class="sxs-lookup"><span data-stu-id="04626-106">This tutorial shows you how to enable your users to sign in with their Google+ account using a sample ASP.NET Core 2.0 project created on the [previous page](index.md).</span></span> <span data-ttu-id="04626-107">我们先执行[官方步骤](https://developers.google.com/identity/sign-in/web/devconsole-project)在 Google API 控制台中创建新的应用程序。</span><span class="sxs-lookup"><span data-stu-id="04626-107">We start by following the [official steps](https://developers.google.com/identity/sign-in/web/devconsole-project) to create a new app in Google API Console.</span></span>

## <a name="create-the-app-in-google-api-console"></a><span data-ttu-id="04626-108">在 Google API 控制台中创建应用程序</span><span class="sxs-lookup"><span data-stu-id="04626-108">Create the app in Google API Console</span></span>

* <span data-ttu-id="04626-109">导航到[https://console.developers.google.com/projectselector/apis/library](https://console.developers.google.com/projectselector/apis/library)并登录。</span><span class="sxs-lookup"><span data-stu-id="04626-109">Navigate to [https://console.developers.google.com/projectselector/apis/library](https://console.developers.google.com/projectselector/apis/library) and sign in.</span></span> <span data-ttu-id="04626-110">如果你还没有 Google 帐户，使用**更多选项** > **[创建帐户](https://accounts.google.com/SignUpWithoutGmail?service=cloudconsole&continue=https%3A%2F%2Fconsole.developers.google.com%2Fprojectselector%2Fapis%2Flibrary&ltmpl=api)**链接创建一个：</span><span class="sxs-lookup"><span data-stu-id="04626-110">If you don't already have a Google account, use **More options** > **[Create account](https://accounts.google.com/SignUpWithoutGmail?service=cloudconsole&continue=https%3A%2F%2Fconsole.developers.google.com%2Fprojectselector%2Fapis%2Flibrary&ltmpl=api)** link to create one:</span></span>

![Google API 控制台](index/_static/GoogleConsoleLogin.png)

* <span data-ttu-id="04626-112">重定向到**API Manager 库**页：</span><span class="sxs-lookup"><span data-stu-id="04626-112">You are redirected to **API Manager Library** page:</span></span>

![API 管理器库页](index/_static/GoogleConsoleSwitchboard.png)

* <span data-ttu-id="04626-114">点击**创建**并输入你**项目名称**:</span><span class="sxs-lookup"><span data-stu-id="04626-114">Tap **Create** and enter your **Project name**:</span></span>

![“新建项目”对话框](index/_static/GoogleConsoleNewProj.png)

* <span data-ttu-id="04626-116">接受对话框中之后, 你将重定向回库页允许你选择为新应用程序的功能。</span><span class="sxs-lookup"><span data-stu-id="04626-116">After accepting the dialog, you are redirected back to the Library page allowing you to choose features for your new app.</span></span> <span data-ttu-id="04626-117">查找**Google + API**在列表中，单击其链接来添加的 API 功能：</span><span class="sxs-lookup"><span data-stu-id="04626-117">Find **Google+ API** in the list and click on its link to add the API feature:</span></span>

![API 管理器库页](index/_static/GoogleConsoleChooseApi.png)

* <span data-ttu-id="04626-119">显示新添加的 API 的页。</span><span class="sxs-lookup"><span data-stu-id="04626-119">The page for the newly added API is displayed.</span></span> <span data-ttu-id="04626-120">点击**启用**将 Google + 登录功能中添加到你的应用程序：</span><span class="sxs-lookup"><span data-stu-id="04626-120">Tap **Enable** to add Google+ sign in feature to your app:</span></span>

![API 管理器 Google + API 页](index/_static/GoogleConsoleEnableApi.png)

* <span data-ttu-id="04626-122">启用 API 后, 点击**创建凭据**配置机密：</span><span class="sxs-lookup"><span data-stu-id="04626-122">After enabling the API, tap **Create credentials** to configure the secrets:</span></span>

![API 管理器 Google + API 页](index/_static/GoogleConsoleGoCredentials.png)

* <span data-ttu-id="04626-124">选择：</span><span class="sxs-lookup"><span data-stu-id="04626-124">Choose:</span></span>
   * <span data-ttu-id="04626-125">**Google + API**</span><span class="sxs-lookup"><span data-stu-id="04626-125">**Google+ API**</span></span>
   * <span data-ttu-id="04626-126">**Web 服务器 (例如 node.js，Tomcat)**，和</span><span class="sxs-lookup"><span data-stu-id="04626-126">**Web server (e.g. node.js, Tomcat)**, and</span></span>
   * <span data-ttu-id="04626-127">**用户数据**:</span><span class="sxs-lookup"><span data-stu-id="04626-127">**User data**:</span></span>

![API 管理器凭据页： 找到什么类型的凭据需要面板](index/_static/GoogleConsoleChooseCred.png)

* <span data-ttu-id="04626-129">点击**我需要什么凭据？**随即将跳转到应用配置的第二个步骤**创建 OAuth 2.0 客户端 ID**:</span><span class="sxs-lookup"><span data-stu-id="04626-129">Tap **What credentials do I need?** which takes you to the second step of app configuration, **Create an OAuth 2.0 client ID**:</span></span>

![API 管理器凭据页： 创建 OAuth 2.0 客户端 ID](index/_static/GoogleConsoleCreateClient.png)

* <span data-ttu-id="04626-131">因为我们将使用只对一个功能 （登录），我们可以输入相同创建 Google + 项目**名称**与我们的项目使用的 OAuth 2.0 客户端 id。</span><span class="sxs-lookup"><span data-stu-id="04626-131">Because we are creating a Google+ project with just one feature (sign in), we can enter the same **Name** for the OAuth 2.0 client ID as the one we used for the project.</span></span>

* <span data-ttu-id="04626-132">输入你的开发 URI 与*/signin-google*追加到**已授权重定向 Uri**字段 (例如： `https://localhost:44320/signin-google`)。</span><span class="sxs-lookup"><span data-stu-id="04626-132">Enter your development URI with */signin-google* appended into the **Authorized redirect URIs** field (for example: `https://localhost:44320/signin-google`).</span></span> <span data-ttu-id="04626-133">本教程中稍后配置 Google 身份验证将自动处理请求在*/signin-google*要实现的 OAuth 流路由。</span><span class="sxs-lookup"><span data-stu-id="04626-133">The Google authentication configured later in this tutorial will automatically handle requests at */signin-google* route to implement the OAuth flow.</span></span>

* <span data-ttu-id="04626-134">按 tab 键以添加**已授权重定向 Uri**条目。</span><span class="sxs-lookup"><span data-stu-id="04626-134">Press TAB to add the **Authorized redirect URIs** entry.</span></span>

* <span data-ttu-id="04626-135">点击**创建客户端 ID**，随即将跳转到第三个步骤中，**设置 OAuth 2.0 许可屏幕**:</span><span class="sxs-lookup"><span data-stu-id="04626-135">Tap **Create client ID**, which takes you to the third step, **Set up the OAuth 2.0 consent screen**:</span></span>

![API 管理器凭据页： 设置 OAuth 2.0 许可屏幕](index/_static/GoogleConsoleAddCred.png)

* <span data-ttu-id="04626-137">输入你的面向公众**电子邮件地址**和**产品名称**Google + 提示用户登录时显示为你的应用。</span><span class="sxs-lookup"><span data-stu-id="04626-137">Enter your public facing **Email address** and the **Product name** shown for your app when Google+ prompts the user to sign in.</span></span> <span data-ttu-id="04626-138">其他选项下有**更多的自定义选项**。</span><span class="sxs-lookup"><span data-stu-id="04626-138">Additional options are available under **More customization options**.</span></span>

* <span data-ttu-id="04626-139">点击**继续**继续到最后一个步骤中，**下载凭据**:</span><span class="sxs-lookup"><span data-stu-id="04626-139">Tap **Continue** to proceed to the last step, **Download credentials**:</span></span>

![API 管理器凭据页： 下载凭据](index/_static/GoogleConsoleFinish.png)

* <span data-ttu-id="04626-141">点击**下载**若要使用应用程序机密，保存 JSON 文件和**完成**以完成创建新的应用程序。</span><span class="sxs-lookup"><span data-stu-id="04626-141">Tap **Download** to save a JSON file with application secrets, and **Done** to complete creation of the new app.</span></span>

* <span data-ttu-id="04626-142">部署站点时你将需要重新访问**Google 控制台**和注册一个新的公共 url。</span><span class="sxs-lookup"><span data-stu-id="04626-142">When deploying the site you'll need to revisit the **Google Console** and register a new public url.</span></span>

## <a name="store-google-clientid-and-clientsecret"></a><span data-ttu-id="04626-143">应用商店 Google ClientID 和 ClientSecret</span><span class="sxs-lookup"><span data-stu-id="04626-143">Store Google ClientID and ClientSecret</span></span>

<span data-ttu-id="04626-144">链接敏感设置，例如 Google`Client ID`和`Client Secret`到你应用程序配置中使用[机密 Manager](../../app-secrets.md)。</span><span class="sxs-lookup"><span data-stu-id="04626-144">Link sensitive settings like Google `Client ID` and `Client Secret` to your application configuration using the [Secret Manager](../../app-secrets.md).</span></span> <span data-ttu-id="04626-145">对于此教程的目的，命名为令牌`Authentication:Google:ClientId`和`Authentication:Google:ClientSecret`。</span><span class="sxs-lookup"><span data-stu-id="04626-145">For the purposes of this tutorial, name the tokens `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`.</span></span>

<span data-ttu-id="04626-146">这些标记的值可以在下一步中下载的 JSON 文件中找到`web.client_id`和`web.client_secret`。</span><span class="sxs-lookup"><span data-stu-id="04626-146">The values for these tokens can be found in the JSON file downloaded in the previous step under `web.client_id` and `web.client_secret`.</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="04626-147">配置 Google 身份验证</span><span class="sxs-lookup"><span data-stu-id="04626-147">Configure Google Authentication</span></span>

<span data-ttu-id="04626-148">在本教程使用的项目模板可确保[Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google)安装包。</span><span class="sxs-lookup"><span data-stu-id="04626-148">The project template used in this tutorial ensures that [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) package is installed.</span></span>

 * <span data-ttu-id="04626-149">若要使用 Visual Studio 2017 安装此包，请右键单击项目并选择**管理 NuGet 包**。</span><span class="sxs-lookup"><span data-stu-id="04626-149">To install this package with Visual Studio 2017, right-click on the project and select **Manage NuGet Packages**.</span></span>
 * <span data-ttu-id="04626-150">若要使用.NET 核心 CLI 安装，请在项目目录中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="04626-150">To install with .NET Core CLI, execute the following in your project directory:</span></span>

   `dotnet add package Microsoft.AspNetCore.Authentication.Google`

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="04626-151">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="04626-151">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="04626-152">添加中的 Google 服务`ConfigureServices`中的方法*Startup.cs*文件：</span><span class="sxs-lookup"><span data-stu-id="04626-152">Add the Google service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddGoogle(googleOptions =>
{
    googleOptions.ClientId = Configuration["Authentication:Google:ClientId"];
    googleOptions.ClientSecret = Configuration["Authentication:Google:ClientSecret"];
});
```

<span data-ttu-id="04626-153">`AddAuthentication`方法仅应调用即可一次时添加多个身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="04626-153">The `AddAuthentication` method should only be called once when adding multiple authentication providers.</span></span> <span data-ttu-id="04626-154">对它的后续调用也可能会覆盖任何以前配置的[AuthenticationOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.authenticationoptions)属性。</span><span class="sxs-lookup"><span data-stu-id="04626-154">Subsequent calls to it have the potential of overriding any previously configured [AuthenticationOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.authenticationoptions) properties.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="04626-155">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="04626-155">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="04626-156">添加中的 Google 中间件`Configure`中的方法*Startup.cs*文件：</span><span class="sxs-lookup"><span data-stu-id="04626-156">Add the Google middleware in the `Configure` method in *Startup.cs* file:</span></span>

```csharp
app.UseGoogleAuthentication(new GoogleOptions()
{
    ClientId = Configuration["Authentication:Google:ClientId"],
    ClientSecret = Configuration["Authentication:Google:ClientSecret"]
});
```

---

<span data-ttu-id="04626-157">请参阅[GoogleOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.googleoptions) Google 身份验证支持的配置选项的详细信息的 API 参考。</span><span class="sxs-lookup"><span data-stu-id="04626-157">See the [GoogleOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.googleoptions) API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="04626-158">这可以用于请求有关用户的不同信息。</span><span class="sxs-lookup"><span data-stu-id="04626-158">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-google"></a><span data-ttu-id="04626-159">使用 Google 登录</span><span class="sxs-lookup"><span data-stu-id="04626-159">Sign in with Google</span></span>

<span data-ttu-id="04626-160">运行你的应用程序，然后单击**登录**。</span><span class="sxs-lookup"><span data-stu-id="04626-160">Run your application and click **Log in**.</span></span> <span data-ttu-id="04626-161">将显示一个选项以使用 Google 登录：</span><span class="sxs-lookup"><span data-stu-id="04626-161">An option to sign in with Google appears:</span></span>

![Microsoft Edge 中运行 web 应用程序： 用户未经过身份验证](index/_static/DoneGoogle.png)

<span data-ttu-id="04626-163">当你单击 Google 时，你将重定向到 Google 身份验证：</span><span class="sxs-lookup"><span data-stu-id="04626-163">When you click on Google, you are redirected to Google for authentication:</span></span>

![Google 身份验证对话框](index/_static/GoogleLogin.png)

<span data-ttu-id="04626-165">在输入你的 Google 凭据，然后你将重定向回 web 站点，你可以设置你的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="04626-165">After entering your Google credentials, then you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="04626-166">现在已在使用你的 Google 凭据进行登录：</span><span class="sxs-lookup"><span data-stu-id="04626-166">You are now logged in using your Google credentials:</span></span>

![Microsoft Edge 中运行 web 应用程序： 身份验证的用户](index/_static/Done.png)

## <a name="troubleshooting"></a><span data-ttu-id="04626-168">疑难解答</span><span class="sxs-lookup"><span data-stu-id="04626-168">Troubleshooting</span></span>

* <span data-ttu-id="04626-169">如果你收到`403 (Forbidden)`从你自己的应用，请确保在开发模式 （或中断到调试器中相同的错误），运行时的错误页**Google + API**已在中启用**API Manager 库**按照列出的步骤[更早版本在此页](#create-the-app-in-google-api-console)。</span><span class="sxs-lookup"><span data-stu-id="04626-169">If you receive a `403 (Forbidden)` error page from your own app when running in development mode (or break into the debugger with the same error), ensure that **Google+ API** has been enabled in the **API Manager Library** by following the steps listed [earlier on this page](#create-the-app-in-google-api-console).</span></span> <span data-ttu-id="04626-170">如果在登录不起作用，并且不能接收任何错误，切换到开发模式以使问题更易于调试。</span><span class="sxs-lookup"><span data-stu-id="04626-170">If the sign in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="04626-171">**ASP.NET 核心 2.x 仅：**如果标识未通过调用配置`services.AddIdentity`中`ConfigureServices`，尝试进行身份验证将导致*ArgumentException： 必须提供 SignInScheme 选项*。</span><span class="sxs-lookup"><span data-stu-id="04626-171">**ASP.NET Core 2.x only:** If Identity is not configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="04626-172">在本教程使用的项目模板可确保，这完成的。</span><span class="sxs-lookup"><span data-stu-id="04626-172">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="04626-173">如果尚未通过应用初始迁移创建站点数据库，则会出现*处理请求时，数据库操作失败*错误。</span><span class="sxs-lookup"><span data-stu-id="04626-173">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="04626-174">点击**应用迁移**创建数据库和刷新可跳过错误。</span><span class="sxs-lookup"><span data-stu-id="04626-174">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="04626-175">后续步骤</span><span class="sxs-lookup"><span data-stu-id="04626-175">Next steps</span></span>

* <span data-ttu-id="04626-176">本文介绍了你使用 Google 可以进行的验证。</span><span class="sxs-lookup"><span data-stu-id="04626-176">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="04626-177">你可以遵循类似的方法进行身份验证使用其他提供商上列出[上一页](index.md)。</span><span class="sxs-lookup"><span data-stu-id="04626-177">You can follow a similar approach to authenticate with other providers listed on the [previous page](index.md).</span></span>

* <span data-ttu-id="04626-178">一旦您的网站发布到 Azure web 应用时，您应重置`ClientSecret`在 Google API 控制台中。</span><span class="sxs-lookup"><span data-stu-id="04626-178">Once you publish your web site to Azure web app, you should reset the `ClientSecret` in the Google API Console.</span></span>

* <span data-ttu-id="04626-179">设置`Authentication:Google:ClientId`和`Authentication:Google:ClientSecret`作为在 Azure 门户中的应用程序设置。</span><span class="sxs-lookup"><span data-stu-id="04626-179">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="04626-180">配置系统设置以从环境变量中读取项。</span><span class="sxs-lookup"><span data-stu-id="04626-180">The configuration system is set up to read keys from environment variables.</span></span>
