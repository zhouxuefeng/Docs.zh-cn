---
title: "使用 Visual Studio 将 ASP.NET Core 应用发布到 Azure"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: get-started-article
ms.assetid: 78571e4a-a143-452d-9cf2-0860f85972e6
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: c4df5f4551760fbc4ed4b11362d249b24f186e00
ms.sourcegitcommit: 8f5277871eff86134ebf68d3737196cfd4a62c2c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2017
---
# <a name="publish-an-aspnet-core-web-app-to-azure-app-service-using-visual-studio"></a><span data-ttu-id="c56cb-103">使用 Visual Studio 将 ASP.NET Core Web 应用发布到 Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c56cb-103">Publish an ASP.NET Core web app to Azure App Service using Visual Studio</span></span>

<span data-ttu-id="c56cb-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Cesar Blum Silveira](https://github.com/cesarbs)</span><span class="sxs-lookup"><span data-stu-id="c56cb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Cesar Blum Silveira](https://github.com/cesarbs)</span></span>

## <a name="set-up-the-development-environment"></a><span data-ttu-id="c56cb-105">设置开发环境</span><span class="sxs-lookup"><span data-stu-id="c56cb-105">Set up the development environment</span></span>

* <span data-ttu-id="c56cb-106">安装最新版本的 [Azure SDK for Visual Studio](https://www.visualstudio.com/features/azure-tools-vs)。</span><span class="sxs-lookup"><span data-stu-id="c56cb-106">Install the latest [Azure SDK for Visual Studio](https://www.visualstudio.com/features/azure-tools-vs).</span></span> <span data-ttu-id="c56cb-107">如果没有 Visual Studio，SDK 将进行安装。</span><span class="sxs-lookup"><span data-stu-id="c56cb-107">The SDK installs Visual Studio if you don't already have it.</span></span>

> [!NOTE]
> <span data-ttu-id="c56cb-108">如果计算机缺少部分依赖项，SDK 安装可能耗时超过 30 分钟。</span><span class="sxs-lookup"><span data-stu-id="c56cb-108">The SDK installation can take more than 30 minutes if your machine doesn't have many of the dependencies.</span></span>

* <span data-ttu-id="c56cb-109">安装 [.NET Core 和 Visual Studio 工具](http://go.microsoft.com/fwlink/?LinkID=798306)</span><span class="sxs-lookup"><span data-stu-id="c56cb-109">Install [.NET Core + Visual Studio tooling](http://go.microsoft.com/fwlink/?LinkID=798306)</span></span>

* <span data-ttu-id="c56cb-110">验证 [Azure 帐户](https://portal.azure.com/)。</span><span class="sxs-lookup"><span data-stu-id="c56cb-110">Verify your [Azure account](https://portal.azure.com/).</span></span> <span data-ttu-id="c56cb-111">可[创建免费的 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)或[激活 Visual Studio 订阅者权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。</span><span class="sxs-lookup"><span data-stu-id="c56cb-111">You can [open a free Azure account](https://azure.microsoft.com/pricing/free-trial/) or [Activate Visual Studio subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).</span></span>

## <a name="create-a-web-app"></a><span data-ttu-id="c56cb-112">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="c56cb-112">Create a web app</span></span>

<span data-ttu-id="c56cb-113">在 Visual Studio 启动页中，点击“新建项目...”。</span><span class="sxs-lookup"><span data-stu-id="c56cb-113">In the Visual Studio Start Page, tap **New Project...**.</span></span>

![启动页](publish-to-azure-webapp-using-vs/_static/new_project.png)

<span data-ttu-id="c56cb-115">或者可使用菜单创建新项目。</span><span class="sxs-lookup"><span data-stu-id="c56cb-115">Alternatively, you can use the menus to create a new project.</span></span> <span data-ttu-id="c56cb-116">点击“文件”>“新建”>“项目...”。</span><span class="sxs-lookup"><span data-stu-id="c56cb-116">Tap **File > New > Project...**.</span></span>

![“文件”菜单](publish-to-azure-webapp-using-vs/_static/alt_new_project.png)

<span data-ttu-id="c56cb-118">完成“新建项目”对话框：</span><span class="sxs-lookup"><span data-stu-id="c56cb-118">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="c56cb-119">在左窗格中，点击“Web”</span><span class="sxs-lookup"><span data-stu-id="c56cb-119">In the left pane, tap **Web**</span></span>

* <span data-ttu-id="c56cb-120">在中间窗格中，点击“ASP.NET Core Web 应用程序(.NET Core)”</span><span class="sxs-lookup"><span data-stu-id="c56cb-120">In the center pane, tap **ASP.NET Core Web Application (.NET Core)**</span></span>

* <span data-ttu-id="c56cb-121">点击“确定”</span><span class="sxs-lookup"><span data-stu-id="c56cb-121">Tap **OK**</span></span>

![“新建项目”对话框](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="c56cb-123">在“新建 ASP.NET Core Web 应用程序(.NET Core)”对话框中：</span><span class="sxs-lookup"><span data-stu-id="c56cb-123">In the **New ASP.NET Core Web Application (.NET Core)** dialog:</span></span>

* <span data-ttu-id="c56cb-124">点击“Web 应用程序”</span><span class="sxs-lookup"><span data-stu-id="c56cb-124">Tap **Web Application**</span></span>

* <span data-ttu-id="c56cb-125">验证“身份验证”是否设置为“单个用户帐户”</span><span class="sxs-lookup"><span data-stu-id="c56cb-125">Verify **Authentication** is set to **Individual User Accounts**</span></span>

* <span data-ttu-id="c56cb-126">验证确保未勾选“在云中托管”</span><span class="sxs-lookup"><span data-stu-id="c56cb-126">Verify **Host in the cloud** is **not** checked</span></span>

* <span data-ttu-id="c56cb-127">点击“确定”</span><span class="sxs-lookup"><span data-stu-id="c56cb-127">Tap **OK**</span></span>

![“新建 ASP.NET Core Web 应用程序(.NET Core)”对话框](publish-to-azure-webapp-using-vs/_static/noath.png)

## <a name="test-the-app-locally"></a><span data-ttu-id="c56cb-129">本地测试应用</span><span class="sxs-lookup"><span data-stu-id="c56cb-129">Test the app locally</span></span>

* <span data-ttu-id="c56cb-130">按“Ctrl-F5”在本地运行应用</span><span class="sxs-lookup"><span data-stu-id="c56cb-130">Press **Ctrl-F5** to run the app locally</span></span>

* <span data-ttu-id="c56cb-131">点击“关于”和“联系”链接。</span><span class="sxs-lookup"><span data-stu-id="c56cb-131">Tap the **About** and **Contact** links.</span></span> <span data-ttu-id="c56cb-132">根据设备的大小，可能需要点击导航图标以显示链接</span><span class="sxs-lookup"><span data-stu-id="c56cb-132">Depending on the size of your device, you might need to tap the navigation icon to show the links</span></span>

![localhost 上的 Microsoft Edge 中打开的 Web 应用程序](publish-to-azure-webapp-using-vs/_static/show.png)

* <span data-ttu-id="c56cb-134">点击“注册”并注册新用户。</span><span class="sxs-lookup"><span data-stu-id="c56cb-134">Tap **Register** and register a new user.</span></span> <span data-ttu-id="c56cb-135">可使用虚构电子邮件地址。</span><span class="sxs-lookup"><span data-stu-id="c56cb-135">You can use a fictitious email address.</span></span> <span data-ttu-id="c56cb-136">提交时，会遇到以下错误：</span><span class="sxs-lookup"><span data-stu-id="c56cb-136">When you submit, you'll get the following error:</span></span>

![内部服务器错误: 处理请求时，数据库操作失败。](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="c56cb-140">可通过两种方法修复此问题：</span><span class="sxs-lookup"><span data-stu-id="c56cb-140">You can fix the problem in two different ways:</span></span>

* <span data-ttu-id="c56cb-141">点击“应用迁移”，并在页面更新后刷新页面；或者</span><span class="sxs-lookup"><span data-stu-id="c56cb-141">Tap **Apply Migrations** and, once the page updates, refresh the page; or</span></span>

* <span data-ttu-id="c56cb-142">从项目目录中的命令提示符处运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c56cb-142">Run the following from a command prompt in the project's directory:</span></span>

  <!-- literal_block {"ids": [], "xml:space": "preserve"} -->

  ```
  dotnet ef database update
     ```

<span data-ttu-id="c56cb-143">应用将显示用于注册新用户的电子邮件和一个“注销”链接。</span><span class="sxs-lookup"><span data-stu-id="c56cb-143">The app displays the email used to register the new user and a **Log off** link.</span></span>

![Microsoft Edge 中打开的 Web 应用程序。](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="c56cb-146">将应用部署到 Azure</span><span class="sxs-lookup"><span data-stu-id="c56cb-146">Deploy the app to Azure</span></span>

<span data-ttu-id="c56cb-147">确认要部署的已发布应用未运行。</span><span class="sxs-lookup"><span data-stu-id="c56cb-147">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="c56cb-148">如果应用正在运行，publish 文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="c56cb-148">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="c56cb-149">不会进行部署，因为无法复制锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="c56cb-149">Deployment can't occur because locked files can't be copied.</span></span>

<span data-ttu-id="c56cb-150">在解决方案资源管理器中右键单击该项目，然后选择“发布...”。</span><span class="sxs-lookup"><span data-stu-id="c56cb-150">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![打开且突出显示“发布”链接的上下文菜单](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="c56cb-152">在“发布”对话框中，点击“Microsoft Azure App Service”。</span><span class="sxs-lookup"><span data-stu-id="c56cb-152">In the **Publish** dialog, tap **Microsoft Azure App Service**.</span></span>

![“发布”对话框](publish-to-azure-webapp-using-vs/_static/maas1.png)

<span data-ttu-id="c56cb-154">点击“新建...”来创建新的资源组。</span><span class="sxs-lookup"><span data-stu-id="c56cb-154">Tap **New...** to create a new resource group.</span></span> <span data-ttu-id="c56cb-155">通过新建资源组，可更轻松地删除本教程中创建的所有 Azure 资源。</span><span class="sxs-lookup"><span data-stu-id="c56cb-155">Creating a new resource group will make it easier to delete all the Azure resources you create in this tutorial.</span></span>

![“应用服务”对话框](publish-to-azure-webapp-using-vs/_static/newrg1.png)

<span data-ttu-id="c56cb-157">创建新的资源组和应用服务计划：</span><span class="sxs-lookup"><span data-stu-id="c56cb-157">Create a new resource group and app service plan:</span></span>

* <span data-ttu-id="c56cb-158">点击“新建...”来创建资源组，并输入新资源组的名称</span><span class="sxs-lookup"><span data-stu-id="c56cb-158">Tap **New...** for the resource group and enter a name for the new resource group</span></span>

* <span data-ttu-id="c56cb-159">点击“新建...”来创建应用服务计划，并选择你附近的位置。</span><span class="sxs-lookup"><span data-stu-id="c56cb-159">Tap **New...** for the  app service plan and select a location near you.</span></span> <span data-ttu-id="c56cb-160">可保留默认生成的名称</span><span class="sxs-lookup"><span data-stu-id="c56cb-160">You can keep the default generated name</span></span>

* <span data-ttu-id="c56cb-161">点击“浏览其他 Azure 服务”来创建新的数据库</span><span class="sxs-lookup"><span data-stu-id="c56cb-161">Tap **Explore additional Azure services** to create a new database</span></span>

![“新建资源组”对话框：托管面板](publish-to-azure-webapp-using-vs/_static/cas.png)

* <span data-ttu-id="c56cb-163">点击绿色的 + 图标，创建新的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="c56cb-163">Tap the green **+** icon to create a new SQL Database</span></span>

![“新建资源组”对话框：服务面板](publish-to-azure-webapp-using-vs/_static/sql.png)

* <span data-ttu-id="c56cb-165">在“配置 SQL 数据库”对话框上点击“新建...”来创建新数据库服务器。</span><span class="sxs-lookup"><span data-stu-id="c56cb-165">Tap **New...** on the **Configure SQL Database** dialog to create a new database server.</span></span>

![“配置 SQL 数据库”对话框](publish-to-azure-webapp-using-vs/_static/conf.png)

* <span data-ttu-id="c56cb-167">输入管理员用户名和密码，然后点击“确定”。</span><span class="sxs-lookup"><span data-stu-id="c56cb-167">Enter an administrator user name and password, and then tap **OK**.</span></span> <span data-ttu-id="c56cb-168">请牢记本步骤中创建的用户名和密码。</span><span class="sxs-lookup"><span data-stu-id="c56cb-168">Don't forget the user name and password you create in this step.</span></span> <span data-ttu-id="c56cb-169">可保留默认的“服务器名称”</span><span class="sxs-lookup"><span data-stu-id="c56cb-169">You can keep the default **Server Name**</span></span>

![“配置 SQL Server”对话框](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

> [!NOTE]
> <span data-ttu-id="c56cb-171">不可使用“admin”作为管理员用户名。</span><span class="sxs-lookup"><span data-stu-id="c56cb-171">"admin" is not allowed as the administrator user name.</span></span>

* <span data-ttu-id="c56cb-172">点击“配置 SQL 数据库”对话框上的“确定”</span><span class="sxs-lookup"><span data-stu-id="c56cb-172">Tap **OK** on the  **Configure SQL Database** dialog</span></span>

![“配置 SQL 数据库”对话框](publish-to-azure-webapp-using-vs/_static/conf_final.png)

* <span data-ttu-id="c56cb-174">点击“创建应用服务”对话框上的“创建”</span><span class="sxs-lookup"><span data-stu-id="c56cb-174">Tap **Create** on the **Create App Service** dialog</span></span>

![“创建应用服务”对话框](publish-to-azure-webapp-using-vs/_static/create_as.png)

* <span data-ttu-id="c56cb-176">在“发布”对话框中点击“下一步”</span><span class="sxs-lookup"><span data-stu-id="c56cb-176">Tap **Next** in the **Publish** dialog</span></span>

![“发布”对话框：连接面板](publish-to-azure-webapp-using-vs/_static/pubc.png)

* <span data-ttu-id="c56cb-178">在“发布”对话框的“设置”阶段：</span><span class="sxs-lookup"><span data-stu-id="c56cb-178">On the **Settings** stage of the **Publish** dialog:</span></span>

  * <span data-ttu-id="c56cb-179">展开“数据库”并选中“在运行时使用此连接字符串”</span><span class="sxs-lookup"><span data-stu-id="c56cb-179">Expand **Databases** and check **Use this connection string at runtime**</span></span>

  * <span data-ttu-id="c56cb-180">展开“Entity Framework 迁移”并选中“在发布时应用此迁移”</span><span class="sxs-lookup"><span data-stu-id="c56cb-180">Expand **Entity Framework Migrations** and check **Apply this migration on publish**</span></span>

* <span data-ttu-id="c56cb-181">点击“发布”，并在 Visual Studio 完成应用发布前稍作等待</span><span class="sxs-lookup"><span data-stu-id="c56cb-181">Tap **Publish** and wait until Visual Studio finishes publishing your app</span></span>

![“发布”对话框：设置面板](publish-to-azure-webapp-using-vs/_static/pubs.png)

<span data-ttu-id="c56cb-183">Visual Studio 会将应用发布到 Azure，并在浏览器中启动云应用。</span><span class="sxs-lookup"><span data-stu-id="c56cb-183">Visual Studio will publish your app to Azure and launch the cloud app in your browser.</span></span>

### <a name="test-your-app-in-azure"></a><span data-ttu-id="c56cb-184">在 Azure 中测试应用</span><span class="sxs-lookup"><span data-stu-id="c56cb-184">Test your app in Azure</span></span>

* <span data-ttu-id="c56cb-185">测试“关于”和“联系”链接</span><span class="sxs-lookup"><span data-stu-id="c56cb-185">Test the **About** and **Contact** links</span></span>

* <span data-ttu-id="c56cb-186">注册新用户</span><span class="sxs-lookup"><span data-stu-id="c56cb-186">Register a new user</span></span>

![Microsoft Edge 中 Azure App Service 上打开的 Web 应用程序](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="update-the-app"></a><span data-ttu-id="c56cb-188">更新应用</span><span class="sxs-lookup"><span data-stu-id="c56cb-188">Update the app</span></span>

* <span data-ttu-id="c56cb-189">编辑 `Views/Home/About.cshtml` Razor 视图文件并更改其内容。</span><span class="sxs-lookup"><span data-stu-id="c56cb-189">Edit the `Views/Home/About.cshtml` Razor view file and change its contents.</span></span> <span data-ttu-id="c56cb-190">例如：</span><span class="sxs-lookup"><span data-stu-id="c56cb-190">For example:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [7]}} -->

```html
@{
       ViewData["Title"] = "About";
   }
   <h2>@ViewData["Title"].</h2>
   <h3>@ViewData["Message"]</h3>

   <p>My updated about page.</p>
   ```

* <span data-ttu-id="c56cb-191">右键单击项目，然后再次点击“发布...”</span><span class="sxs-lookup"><span data-stu-id="c56cb-191">Right-click on the project and tap **Publish...** again</span></span>

![打开且突出显示“发布”链接的上下文菜单](publish-to-azure-webapp-using-vs/_static/pub.png)

* <span data-ttu-id="c56cb-193">应用发布后，验证所作更改在 Azure 上是否可用</span><span class="sxs-lookup"><span data-stu-id="c56cb-193">After the app is published, verify the changes you made are available on Azure</span></span>

### <a name="clean-up"></a><span data-ttu-id="c56cb-194">清理</span><span class="sxs-lookup"><span data-stu-id="c56cb-194">Clean up</span></span>

<span data-ttu-id="c56cb-195">完成应用测试后，转到 [Azure 门户](https://portal.azure.com/)并删除该应用。</span><span class="sxs-lookup"><span data-stu-id="c56cb-195">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="c56cb-196">选择“资源组”，然后点击创建的资源组</span><span class="sxs-lookup"><span data-stu-id="c56cb-196">Select **Resource groups**, then tap the resource group you created</span></span>

![Azure 门户：侧边栏菜单中的资源组](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="c56cb-198">在“资源组”边栏选项卡中，点击“删除”</span><span class="sxs-lookup"><span data-stu-id="c56cb-198">In the **Resource group** blade, tap **Delete**</span></span>

![Azure 门户：“资源组”边栏选项卡](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="c56cb-200">输入资源组的名称并点击“删除”。</span><span class="sxs-lookup"><span data-stu-id="c56cb-200">Enter the name of the resource group and tap **Delete**.</span></span> <span data-ttu-id="c56cb-201">将从 Azure 中删除本教程中创建的应用和其他所有资源</span><span class="sxs-lookup"><span data-stu-id="c56cb-201">Your app and all other resources created in this tutorial are now deleted from Azure</span></span>

### <a name="next-steps"></a><span data-ttu-id="c56cb-202">后续步骤</span><span class="sxs-lookup"><span data-stu-id="c56cb-202">Next steps</span></span>

* [<span data-ttu-id="c56cb-203">ASP.NET Core MVC 和 Visual Studio 入门</span><span class="sxs-lookup"><span data-stu-id="c56cb-203">Getting started with ASP.NET Core MVC and Visual Studio</span></span>](first-mvc-app/start-mvc.md)

* [<span data-ttu-id="c56cb-204">ASP.NET Core 简介</span><span class="sxs-lookup"><span data-stu-id="c56cb-204">Introduction to ASP.NET Core</span></span>](../index.md)

* [<span data-ttu-id="c56cb-205">基础知识</span><span class="sxs-lookup"><span data-stu-id="c56cb-205">Fundamentals</span></span>](../fundamentals/index.md)
