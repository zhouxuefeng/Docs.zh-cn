---
title: "使用 Visual Studio 将 ASP.NET Core 应用发布到 Azure"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 09/01/2017
ms.topic: get-started-article
ms.assetid: 78571e4a-a143-452d-9cf2-0860f85972e6
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 0c0ec1c7c1408b0460c594a47a3e5738cd170d5f
ms.sourcegitcommit: d022d4b96795ee473fa3847a1d8a8c7430423a86
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2017
---
# <a name="publish-an-aspnet-core-web-app-to-azure-app-service-using-visual-studio"></a><span data-ttu-id="5600f-103">使用 Visual Studio 将 ASP.NET Core Web 应用发布到 Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5600f-103">Publish an ASP.NET Core web app to Azure App Service using Visual Studio</span></span>

<span data-ttu-id="5600f-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 、[Cesar Blum Silveira](https://github.com/cesarbs) 和 [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="5600f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Cesar Blum Silveira](https://github.com/cesarbs), and [Rachel Appel](https://twitter.com/rachelappel)</span></span>

## <a name="set-up-the-development-environment"></a><span data-ttu-id="5600f-105">设置开发环境</span><span class="sxs-lookup"><span data-stu-id="5600f-105">Set up the development environment</span></span>

* <span data-ttu-id="5600f-106">安装最新版本的 [Azure SDK for Visual Studio](https://www.visualstudio.com/vs/azure-tools/)。</span><span class="sxs-lookup"><span data-stu-id="5600f-106">Install the latest [Azure SDK for Visual Studio](https://www.visualstudio.com/vs/azure-tools/).</span></span> <span data-ttu-id="5600f-107">如果没有 Visual Studio，SDK 将进行安装。</span><span class="sxs-lookup"><span data-stu-id="5600f-107">The SDK installs Visual Studio if you don't already have it.</span></span>

* <span data-ttu-id="5600f-108">验证 [Azure 帐户](https://portal.azure.com/)。</span><span class="sxs-lookup"><span data-stu-id="5600f-108">Verify your [Azure account](https://portal.azure.com/).</span></span> <span data-ttu-id="5600f-109">可[创建免费的 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)或[激活 Visual Studio 订阅者权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。</span><span class="sxs-lookup"><span data-stu-id="5600f-109">You can [open a free Azure account](https://azure.microsoft.com/pricing/free-trial/) or [Activate Visual Studio subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).</span></span>

## <a name="create-a-web-app"></a><span data-ttu-id="5600f-110">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="5600f-110">Create a web app</span></span>

<span data-ttu-id="5600f-111">在 Visual Studio 起始页中，选择“文件”>“新建”>“项目...”</span><span class="sxs-lookup"><span data-stu-id="5600f-111">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![“文件”菜单](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="5600f-113">完成“新建项目”对话框：</span><span class="sxs-lookup"><span data-stu-id="5600f-113">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="5600f-114">在左侧窗格中，选择“.NET Core”。</span><span class="sxs-lookup"><span data-stu-id="5600f-114">In the left pane, select **.NET Core**.</span></span>

* <span data-ttu-id="5600f-115">在中间窗格中，选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="5600f-115">In the center pane, select **ASP.NET Core Web Application**.</span></span>

* <span data-ttu-id="5600f-116">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="5600f-116">Select **OK**.</span></span>

![“新建项目”对话框](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="5600f-118">在“新建 ASP.NET Core Web 应用程序”对话框中：</span><span class="sxs-lookup"><span data-stu-id="5600f-118">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="5600f-119">选择“Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="5600f-119">Select **Web Application**.</span></span>

* <span data-ttu-id="5600f-120">选择“更改身份验证”。</span><span class="sxs-lookup"><span data-stu-id="5600f-120">Select **Change Authentication**.</span></span>

![“新建项目”对话框](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="5600f-122">“更改身份验证”对话框随即出现。</span><span class="sxs-lookup"><span data-stu-id="5600f-122">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="5600f-123">选择“个人用户帐户”。</span><span class="sxs-lookup"><span data-stu-id="5600f-123">Select **Individual User Accounts**.</span></span>

* <span data-ttu-id="5600f-124">选择“确定”返回到“新建 ASP.NET Core Web 应用程序”，然后再次选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="5600f-124">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **OK** again.</span></span>

![“新建 ASP.NET Core Web 身份验证”对话框](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="5600f-126">Visual Studio 随即创建解决方案。</span><span class="sxs-lookup"><span data-stu-id="5600f-126">Visual Studio creates the solution.</span></span>

## <a name="run-the-app-locally"></a><span data-ttu-id="5600f-127">本地运行应用</span><span class="sxs-lookup"><span data-stu-id="5600f-127">Run the app locally</span></span>

* <span data-ttu-id="5600f-128">选择“调试”，然后选择“开始执行(不调试)”以本地运行应用。</span><span class="sxs-lookup"><span data-stu-id="5600f-128">Choose **Debug** then **Start Without Debugging** to run the app locally.</span></span>

* <span data-ttu-id="5600f-129">单击“关于”和“联系”链接以验证 Web 应用程序是否正常工作。</span><span class="sxs-lookup"><span data-stu-id="5600f-129">Click the **About** and **Contact** links to verify the web application works.</span></span>

![localhost 上的 Microsoft Edge 中打开的 Web 应用程序](publish-to-azure-webapp-using-vs/_static/show.png)

* <span data-ttu-id="5600f-131">选择“注册”并注册新用户。</span><span class="sxs-lookup"><span data-stu-id="5600f-131">Select **Register** and register a new user.</span></span> <span data-ttu-id="5600f-132">可使用虚构电子邮件地址。</span><span class="sxs-lookup"><span data-stu-id="5600f-132">You can use a fictitious email address.</span></span> <span data-ttu-id="5600f-133">提交时，页面上会显示以下错误：</span><span class="sxs-lookup"><span data-stu-id="5600f-133">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="5600f-134">“内部服务器错误: 处理请求时，数据库操作失败。SQL 异常: 无法打开数据库。可通过向应用程序数据库上下文应用现有迁移解决此问题。”</span><span class="sxs-lookup"><span data-stu-id="5600f-134">*"Internal Server Error: A database operation failed while processing the request. SQL exception: Cannot open the database. Applying existing migrations for Application DB context may resolve this issue."*</span></span>

* <span data-ttu-id="5600f-135">选择“应用迁移”，并在页面更新后刷新页面。</span><span class="sxs-lookup"><span data-stu-id="5600f-135">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![内部服务器错误: 处理请求时，数据库操作失败。](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="5600f-139">应用将显示用于注册新用户的电子邮件和一个“注销”链接。</span><span class="sxs-lookup"><span data-stu-id="5600f-139">The app displays the email used to register the new user and a **Log out** link.</span></span>

![Microsoft Edge 中打开的 Web 应用程序。](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="5600f-142">将应用部署到 Azure</span><span class="sxs-lookup"><span data-stu-id="5600f-142">Deploy the app to Azure</span></span>

<span data-ttu-id="5600f-143">关闭网页，返回到 Visual Studio，然后从“调试”菜单中选择“停止调试”。</span><span class="sxs-lookup"><span data-stu-id="5600f-143">Close the web page, return to Visual Studio, and select **Stop Debugging** from the **Debug** menu.</span></span>

<span data-ttu-id="5600f-144">在解决方案资源管理器中右键单击该项目，然后选择“发布...”。</span><span class="sxs-lookup"><span data-stu-id="5600f-144">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![打开且突出显示“发布”链接的上下文菜单](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="5600f-146">在“发布”对话框中，选择“Microsoft Azure App Service”，然后单击“发布”。</span><span class="sxs-lookup"><span data-stu-id="5600f-146">In the **Publish** dialog, select **Microsoft Azure App Service** and click **Publish**.</span></span>

![“发布”对话框](publish-to-azure-webapp-using-vs/_static/maas1.png)

* <span data-ttu-id="5600f-148">为应用提供唯一名称。</span><span class="sxs-lookup"><span data-stu-id="5600f-148">Name the app a unique name.</span></span> 

* <span data-ttu-id="5600f-149">选择订阅。</span><span class="sxs-lookup"><span data-stu-id="5600f-149">Select a subscription.</span></span>

* <span data-ttu-id="5600f-150">选择“新建...”来创建资源组，并输入新资源组的名称。</span><span class="sxs-lookup"><span data-stu-id="5600f-150">Select **New...** for the resource group and enter a name for the new resource group.</span></span>

* <span data-ttu-id="5600f-151">选择“新建...”来创建应用服务计划，并选择你附近的一个位置。</span><span class="sxs-lookup"><span data-stu-id="5600f-151">Select **New...** for the app service plan and select a location near you.</span></span> <span data-ttu-id="5600f-152">可以保留默认生成的名称。</span><span class="sxs-lookup"><span data-stu-id="5600f-152">You can keep the name that is generated by default.</span></span>

![“应用服务”对话框](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* <span data-ttu-id="5600f-154">选择“服务”选项卡以创建新的数据库。</span><span class="sxs-lookup"><span data-stu-id="5600f-154">Select the **Services** tab to create a new database.</span></span>

* <span data-ttu-id="5600f-155">选择绿色的 + 图标以创建新的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="5600f-155">Select the green **+** icon to create a new SQL Database</span></span>

![新建 SQL 数据库](publish-to-azure-webapp-using-vs/_static/sql.png)

* <span data-ttu-id="5600f-157">在“配置 SQL 数据库”对话框中选择“新建...”以创建新的数据库。</span><span class="sxs-lookup"><span data-stu-id="5600f-157">Select **New...** on the **Configure SQL Database** dialog to create a new database.</span></span>

![新建 SQL 数据库和服务器](publish-to-azure-webapp-using-vs/_static/conf.png)

<span data-ttu-id="5600f-159">“配置 SQL Server”对话框随即出现。</span><span class="sxs-lookup"><span data-stu-id="5600f-159">The **Configure SQL Server** dialog appears.</span></span>

* <span data-ttu-id="5600f-160">输入管理员用户名和密码，然后选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="5600f-160">Enter an administrator user name and password, and then select **OK**.</span></span> <span data-ttu-id="5600f-161">请牢记本步骤中创建的用户名和密码。</span><span class="sxs-lookup"><span data-stu-id="5600f-161">Don't forget the user name and password you create in this step.</span></span> <span data-ttu-id="5600f-162">可保留默认的“服务器名称”。</span><span class="sxs-lookup"><span data-stu-id="5600f-162">You can keep the default **Server Name**.</span></span> 

* <span data-ttu-id="5600f-163">输入数据库和连接字符串的名称。</span><span class="sxs-lookup"><span data-stu-id="5600f-163">Enter names for the database and connection string.</span></span>

> [!NOTE]
> <span data-ttu-id="5600f-164">不可使用“admin”作为管理员用户名。</span><span class="sxs-lookup"><span data-stu-id="5600f-164">"admin" is not allowed as the administrator user name.</span></span>

![“配置 SQL Server”对话框](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* <span data-ttu-id="5600f-166">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="5600f-166">Select **OK**.</span></span>

<span data-ttu-id="5600f-167">Visual Studio 将返回到“创建应用服务”对话框。</span><span class="sxs-lookup"><span data-stu-id="5600f-167">Visual Studio returns to the **Create App Service** dialog.</span></span>

* <span data-ttu-id="5600f-168">选择“创建应用服务”对话框上的“创建”。</span><span class="sxs-lookup"><span data-stu-id="5600f-168">Select **Create** on the **Create App Service** dialog.</span></span>

![“配置 SQL 数据库”对话框](publish-to-azure-webapp-using-vs/_static/conf_final.png)

* <span data-ttu-id="5600f-170">在“发布”对话框中单击“设置”链接。</span><span class="sxs-lookup"><span data-stu-id="5600f-170">Click the **Settings** link in the **Publish** dialog.</span></span>

![“发布”对话框：连接面板](publish-to-azure-webapp-using-vs/_static/pubc.png)

<span data-ttu-id="5600f-172">在“发布”对话框的“设置”页面上：</span><span class="sxs-lookup"><span data-stu-id="5600f-172">On the **Settings** page of the **Publish** dialog:</span></span>

  * <span data-ttu-id="5600f-173">展开“数据库”并选中“在运行时使用此连接字符串”。</span><span class="sxs-lookup"><span data-stu-id="5600f-173">Expand **Databases** and check **Use this connection string at runtime**.</span></span>

  * <span data-ttu-id="5600f-174">展开“Entity Framework 迁移”并选中“在发布时应用此迁移”。</span><span class="sxs-lookup"><span data-stu-id="5600f-174">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="5600f-175">选择“保存”。</span><span class="sxs-lookup"><span data-stu-id="5600f-175">Select **Save**.</span></span> <span data-ttu-id="5600f-176">Visual Studio 将返回到“发布”对话框。</span><span class="sxs-lookup"><span data-stu-id="5600f-176">Visual Studio returns to the **Publish** dialog.</span></span> 

![“发布”对话框：设置面板](publish-to-azure-webapp-using-vs/_static/pubs.png)

<span data-ttu-id="5600f-178">单击“发布” 。</span><span class="sxs-lookup"><span data-stu-id="5600f-178">Click **Publish**.</span></span> <span data-ttu-id="5600f-179">Visual Studio 会将应用发布到 Azure，并在浏览器中启动云应用。</span><span class="sxs-lookup"><span data-stu-id="5600f-179">Visual Studio will publish your app to Azure and launch the cloud app in your browser.</span></span>

### <a name="test-your-app-in-azure"></a><span data-ttu-id="5600f-180">在 Azure 中测试应用</span><span class="sxs-lookup"><span data-stu-id="5600f-180">Test your app in Azure</span></span>

* <span data-ttu-id="5600f-181">测试“关于”和“联系”链接</span><span class="sxs-lookup"><span data-stu-id="5600f-181">Test the **About** and **Contact** links</span></span>

* <span data-ttu-id="5600f-182">注册新用户</span><span class="sxs-lookup"><span data-stu-id="5600f-182">Register a new user</span></span>

![Microsoft Edge 中 Azure App Service 上打开的 Web 应用程序](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a><span data-ttu-id="5600f-184">更新应用</span><span class="sxs-lookup"><span data-stu-id="5600f-184">Update the app</span></span>

* <span data-ttu-id="5600f-185">编辑“Pages/About.cshtml”Razor 页面并更改其内容。</span><span class="sxs-lookup"><span data-stu-id="5600f-185">Edit the *Pages/About.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="5600f-186">例如，可以将段落修改为显示“Hello ASP.NET Core!”：</span><span class="sxs-lookup"><span data-stu-id="5600f-186">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* <span data-ttu-id="5600f-187">右键单击项目，然后再次选择“发布...”。</span><span class="sxs-lookup"><span data-stu-id="5600f-187">Right-click on the project and select **Publish...** again.</span></span>

![打开且突出显示“发布”链接的上下文菜单](publish-to-azure-webapp-using-vs/_static/pub.png)

* <span data-ttu-id="5600f-189">应用发布后，验证所做的更改在 Azure 上是否可用。</span><span class="sxs-lookup"><span data-stu-id="5600f-189">After the app is published, verify the changes you made are available on Azure.</span></span>

![验证任务已完成](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="5600f-191">清理</span><span class="sxs-lookup"><span data-stu-id="5600f-191">Clean up</span></span>

<span data-ttu-id="5600f-192">完成应用测试后，转到 [Azure 门户](https://portal.azure.com/)并删除该应用。</span><span class="sxs-lookup"><span data-stu-id="5600f-192">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="5600f-193">选择“资源组”，然后选择所创建的资源组。</span><span class="sxs-lookup"><span data-stu-id="5600f-193">Select **Resource groups**, then select the resource group you created.</span></span>

![Azure 门户：侧边栏菜单中的资源组](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="5600f-195">在“资源组”页面中，选择“删除”。</span><span class="sxs-lookup"><span data-stu-id="5600f-195">In the **Resource groups** page, select **Delete**.</span></span>

![Azure 门户：“资源组”页面](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="5600f-197">输入资源组的名称并选择“删除”。</span><span class="sxs-lookup"><span data-stu-id="5600f-197">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="5600f-198">现已从 Azure 中删除了本教程中创建的应用和其他所有资源。</span><span class="sxs-lookup"><span data-stu-id="5600f-198">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="5600f-199">后续步骤</span><span class="sxs-lookup"><span data-stu-id="5600f-199">Next steps</span></span>

* [<span data-ttu-id="5600f-200">ASP.NET Core MVC 和 Visual Studio 入门</span><span class="sxs-lookup"><span data-stu-id="5600f-200">Getting started with ASP.NET Core MVC and Visual Studio</span></span>](first-mvc-app/start-mvc.md)

* [<span data-ttu-id="5600f-201">ASP.NET Core 简介</span><span class="sxs-lookup"><span data-stu-id="5600f-201">Introduction to ASP.NET Core</span></span>](../index.md)

* [<span data-ttu-id="5600f-202">基础知识</span><span class="sxs-lookup"><span data-stu-id="5600f-202">Fundamentals</span></span>](../fundamentals/index.md)
