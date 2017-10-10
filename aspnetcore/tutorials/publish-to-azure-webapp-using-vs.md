---
title: "使用 Visual Studio 将 ASP.NET Core 应用发布到 Azure"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/05/2017
ms.topic: get-started-article
ms.assetid: 78571e4a-a143-452d-9cf2-0860f85972e6
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: df22852d2daddb2a3faef8404d0d250a6a1697a5
ms.sourcegitcommit: e987c950caae7af9c4ece8a82228caa364e0a5df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2017
---
# <a name="publish-an-aspnet-core-web-app-to-azure-app-service-using-visual-studio"></a><span data-ttu-id="8d611-103">使用 Visual Studio 将 ASP.NET Core Web 应用发布到 Azure App Service</span><span class="sxs-lookup"><span data-stu-id="8d611-103">Publish an ASP.NET Core web app to Azure App Service using Visual Studio</span></span>

<span data-ttu-id="8d611-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 、[Cesar Blum Silveira](https://github.com/cesarbs) 和 [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="8d611-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Cesar Blum Silveira](https://github.com/cesarbs), and [Rachel Appel](https://twitter.com/rachelappel)</span></span>

## <a name="set-up"></a><span data-ttu-id="8d611-105">设置</span><span class="sxs-lookup"><span data-stu-id="8d611-105">Set up</span></span>

* <span data-ttu-id="8d611-106">如果没有，请打开[免费 Azure 帐户](https://aka.ms/K5y5yh)。</span><span class="sxs-lookup"><span data-stu-id="8d611-106">Open a [free Azure account](https://aka.ms/K5y5yh) if you do not have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="8d611-107">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="8d611-107">Create a web app</span></span>

<span data-ttu-id="8d611-108">在 Visual Studio 起始页中，选择“文件”>“新建”>“项目...”</span><span class="sxs-lookup"><span data-stu-id="8d611-108">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![“文件”菜单](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="8d611-110">完成“新建项目”对话框：</span><span class="sxs-lookup"><span data-stu-id="8d611-110">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="8d611-111">在左侧窗格中，选择“.NET Core”。</span><span class="sxs-lookup"><span data-stu-id="8d611-111">In the left pane, select **.NET Core**.</span></span>

* <span data-ttu-id="8d611-112">在中间窗格中，选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="8d611-112">In the center pane, select **ASP.NET Core Web Application**.</span></span>

* <span data-ttu-id="8d611-113">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="8d611-113">Select **OK**.</span></span>

![“新建项目”对话框](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="8d611-115">在“新建 ASP.NET Core Web 应用程序”对话框中：</span><span class="sxs-lookup"><span data-stu-id="8d611-115">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="8d611-116">选择“Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="8d611-116">Select **Web Application**.</span></span>

* <span data-ttu-id="8d611-117">选择“更改身份验证”。</span><span class="sxs-lookup"><span data-stu-id="8d611-117">Select **Change Authentication**.</span></span>

![“新建项目”对话框](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="8d611-119">“更改身份验证”对话框随即出现。</span><span class="sxs-lookup"><span data-stu-id="8d611-119">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="8d611-120">选择“个人用户帐户”。</span><span class="sxs-lookup"><span data-stu-id="8d611-120">Select **Individual User Accounts**.</span></span>

* <span data-ttu-id="8d611-121">选择“确定”返回到“新建 ASP.NET Core Web 应用程序”，然后再次选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="8d611-121">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **OK** again.</span></span>

![“新建 ASP.NET Core Web 身份验证”对话框](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="8d611-123">Visual Studio 随即创建解决方案。</span><span class="sxs-lookup"><span data-stu-id="8d611-123">Visual Studio creates the solution.</span></span>

## <a name="run-the-app-locally"></a><span data-ttu-id="8d611-124">本地运行应用</span><span class="sxs-lookup"><span data-stu-id="8d611-124">Run the app locally</span></span>

* <span data-ttu-id="8d611-125">选择“调试”，然后选择“开始执行(不调试)”以本地运行应用。</span><span class="sxs-lookup"><span data-stu-id="8d611-125">Choose **Debug** then **Start Without Debugging** to run the app locally.</span></span>

* <span data-ttu-id="8d611-126">单击“关于”和“联系”链接以验证 Web 应用程序是否正常工作。</span><span class="sxs-lookup"><span data-stu-id="8d611-126">Click the **About** and **Contact** links to verify the web application works.</span></span>

![localhost 上的 Microsoft Edge 中打开的 Web 应用程序](publish-to-azure-webapp-using-vs/_static/show.png)

* <span data-ttu-id="8d611-128">选择“注册”并注册新用户。</span><span class="sxs-lookup"><span data-stu-id="8d611-128">Select **Register** and register a new user.</span></span> <span data-ttu-id="8d611-129">可使用虚构电子邮件地址。</span><span class="sxs-lookup"><span data-stu-id="8d611-129">You can use a fictitious email address.</span></span> <span data-ttu-id="8d611-130">提交时，页面上会显示以下错误：</span><span class="sxs-lookup"><span data-stu-id="8d611-130">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="8d611-131">“内部服务器错误: 处理请求时，数据库操作失败。SQL 异常: 无法打开数据库。可通过向应用程序数据库上下文应用现有迁移解决此问题。”</span><span class="sxs-lookup"><span data-stu-id="8d611-131">*"Internal Server Error: A database operation failed while processing the request. SQL exception: Cannot open the database. Applying existing migrations for Application DB context may resolve this issue."*</span></span>

* <span data-ttu-id="8d611-132">选择“应用迁移”，并在页面更新后刷新页面。</span><span class="sxs-lookup"><span data-stu-id="8d611-132">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![内部服务器错误: 处理请求时，数据库操作失败。](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="8d611-136">应用将显示用于注册新用户的电子邮件和一个“注销”链接。</span><span class="sxs-lookup"><span data-stu-id="8d611-136">The app displays the email used to register the new user and a **Log out** link.</span></span>

![Microsoft Edge 中打开的 Web 应用程序。](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="8d611-139">将应用部署到 Azure</span><span class="sxs-lookup"><span data-stu-id="8d611-139">Deploy the app to Azure</span></span>

<span data-ttu-id="8d611-140">关闭网页，返回到 Visual Studio，然后从“调试”菜单中选择“停止调试”。</span><span class="sxs-lookup"><span data-stu-id="8d611-140">Close the web page, return to Visual Studio, and select **Stop Debugging** from the **Debug** menu.</span></span>

<span data-ttu-id="8d611-141">在解决方案资源管理器中右键单击该项目，然后选择“发布...”。</span><span class="sxs-lookup"><span data-stu-id="8d611-141">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![打开且突出显示“发布”链接的上下文菜单](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="8d611-143">在“发布”对话框中，选择“Microsoft Azure App Service”，然后单击“发布”。</span><span class="sxs-lookup"><span data-stu-id="8d611-143">In the **Publish** dialog, select **Microsoft Azure App Service** and click **Publish**.</span></span>

![“发布”对话框](publish-to-azure-webapp-using-vs/_static/maas1.png)

* <span data-ttu-id="8d611-145">为应用提供唯一名称。</span><span class="sxs-lookup"><span data-stu-id="8d611-145">Name the app a unique name.</span></span> 

* <span data-ttu-id="8d611-146">选择订阅。</span><span class="sxs-lookup"><span data-stu-id="8d611-146">Select a subscription.</span></span>

* <span data-ttu-id="8d611-147">选择“新建...”来创建资源组，并输入新资源组的名称。</span><span class="sxs-lookup"><span data-stu-id="8d611-147">Select **New...** for the resource group and enter a name for the new resource group.</span></span>

* <span data-ttu-id="8d611-148">选择“新建...”来创建应用服务计划，并选择你附近的一个位置。</span><span class="sxs-lookup"><span data-stu-id="8d611-148">Select **New...** for the app service plan and select a location near you.</span></span> <span data-ttu-id="8d611-149">可以保留默认生成的名称。</span><span class="sxs-lookup"><span data-stu-id="8d611-149">You can keep the name that is generated by default.</span></span>

![“应用服务”对话框](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* <span data-ttu-id="8d611-151">选择“服务”选项卡以创建新的数据库。</span><span class="sxs-lookup"><span data-stu-id="8d611-151">Select the **Services** tab to create a new database.</span></span>

* <span data-ttu-id="8d611-152">选择绿色的 + 图标以创建新的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="8d611-152">Select the green **+** icon to create a new SQL Database</span></span>

![新建 SQL 数据库](publish-to-azure-webapp-using-vs/_static/sql.png)

* <span data-ttu-id="8d611-154">在“配置 SQL 数据库”对话框中选择“新建...”以创建新的数据库。</span><span class="sxs-lookup"><span data-stu-id="8d611-154">Select **New...** on the **Configure SQL Database** dialog to create a new database.</span></span>

![新建 SQL 数据库和服务器](publish-to-azure-webapp-using-vs/_static/conf.png)

<span data-ttu-id="8d611-156">“配置 SQL Server”对话框随即出现。</span><span class="sxs-lookup"><span data-stu-id="8d611-156">The **Configure SQL Server** dialog appears.</span></span>

* <span data-ttu-id="8d611-157">输入管理员用户名和密码，然后选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="8d611-157">Enter an administrator user name and password, and then select **OK**.</span></span> <span data-ttu-id="8d611-158">请牢记本步骤中创建的用户名和密码。</span><span class="sxs-lookup"><span data-stu-id="8d611-158">Don't forget the user name and password you create in this step.</span></span> <span data-ttu-id="8d611-159">可保留默认的“服务器名称”。</span><span class="sxs-lookup"><span data-stu-id="8d611-159">You can keep the default **Server Name**.</span></span> 

* <span data-ttu-id="8d611-160">输入数据库和连接字符串的名称。</span><span class="sxs-lookup"><span data-stu-id="8d611-160">Enter names for the database and connection string.</span></span>

> [!NOTE]
> <span data-ttu-id="8d611-161">不可使用“admin”作为管理员用户名。</span><span class="sxs-lookup"><span data-stu-id="8d611-161">"admin" is not allowed as the administrator user name.</span></span>

![“配置 SQL Server”对话框](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* <span data-ttu-id="8d611-163">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="8d611-163">Select **OK**.</span></span>

<span data-ttu-id="8d611-164">Visual Studio 将返回到“创建应用服务”对话框。</span><span class="sxs-lookup"><span data-stu-id="8d611-164">Visual Studio returns to the **Create App Service** dialog.</span></span>

* <span data-ttu-id="8d611-165">选择“创建应用服务”对话框上的“创建”。</span><span class="sxs-lookup"><span data-stu-id="8d611-165">Select **Create** on the **Create App Service** dialog.</span></span>

![“配置 SQL 数据库”对话框](publish-to-azure-webapp-using-vs/_static/conf_final.png)

* <span data-ttu-id="8d611-167">在“发布”对话框中单击“设置”链接。</span><span class="sxs-lookup"><span data-stu-id="8d611-167">Click the **Settings** link in the **Publish** dialog.</span></span>

![“发布”对话框：连接面板](publish-to-azure-webapp-using-vs/_static/pubc.png)

<span data-ttu-id="8d611-169">在“发布”对话框的“设置”页面上：</span><span class="sxs-lookup"><span data-stu-id="8d611-169">On the **Settings** page of the **Publish** dialog:</span></span>

  * <span data-ttu-id="8d611-170">展开“数据库”并选中“在运行时使用此连接字符串”。</span><span class="sxs-lookup"><span data-stu-id="8d611-170">Expand **Databases** and check **Use this connection string at runtime**.</span></span>

  * <span data-ttu-id="8d611-171">展开“Entity Framework 迁移”并选中“在发布时应用此迁移”。</span><span class="sxs-lookup"><span data-stu-id="8d611-171">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="8d611-172">选择“保存”。</span><span class="sxs-lookup"><span data-stu-id="8d611-172">Select **Save**.</span></span> <span data-ttu-id="8d611-173">Visual Studio 将返回到“发布”对话框。</span><span class="sxs-lookup"><span data-stu-id="8d611-173">Visual Studio returns to the **Publish** dialog.</span></span> 

![“发布”对话框：设置面板](publish-to-azure-webapp-using-vs/_static/pubs.png)

<span data-ttu-id="8d611-175">单击“发布” 。</span><span class="sxs-lookup"><span data-stu-id="8d611-175">Click **Publish**.</span></span> <span data-ttu-id="8d611-176">Visual Studio 会将应用发布到 Azure，并在浏览器中启动云应用。</span><span class="sxs-lookup"><span data-stu-id="8d611-176">Visual Studio will publish your app to Azure and launch the cloud app in your browser.</span></span>

### <a name="test-your-app-in-azure"></a><span data-ttu-id="8d611-177">在 Azure 中测试应用</span><span class="sxs-lookup"><span data-stu-id="8d611-177">Test your app in Azure</span></span>

* <span data-ttu-id="8d611-178">测试“关于”和“联系”链接</span><span class="sxs-lookup"><span data-stu-id="8d611-178">Test the **About** and **Contact** links</span></span>

* <span data-ttu-id="8d611-179">注册新用户</span><span class="sxs-lookup"><span data-stu-id="8d611-179">Register a new user</span></span>

![Microsoft Edge 中 Azure App Service 上打开的 Web 应用程序](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a><span data-ttu-id="8d611-181">更新应用</span><span class="sxs-lookup"><span data-stu-id="8d611-181">Update the app</span></span>

* <span data-ttu-id="8d611-182">编辑“Pages/About.cshtml”Razor 页面并更改其内容。</span><span class="sxs-lookup"><span data-stu-id="8d611-182">Edit the *Pages/About.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="8d611-183">例如，可以将段落修改为显示“Hello ASP.NET Core!”：</span><span class="sxs-lookup"><span data-stu-id="8d611-183">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* <span data-ttu-id="8d611-184">右键单击项目，然后再次选择“发布...”。</span><span class="sxs-lookup"><span data-stu-id="8d611-184">Right-click on the project and select **Publish...** again.</span></span>

![打开且突出显示“发布”链接的上下文菜单](publish-to-azure-webapp-using-vs/_static/pub.png)

* <span data-ttu-id="8d611-186">应用发布后，验证所做的更改在 Azure 上是否可用。</span><span class="sxs-lookup"><span data-stu-id="8d611-186">After the app is published, verify the changes you made are available on Azure.</span></span>

![验证任务已完成](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="8d611-188">清理</span><span class="sxs-lookup"><span data-stu-id="8d611-188">Clean up</span></span>

<span data-ttu-id="8d611-189">完成应用测试后，转到 [Azure 门户](https://portal.azure.com/)并删除该应用。</span><span class="sxs-lookup"><span data-stu-id="8d611-189">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="8d611-190">选择“资源组”，然后选择所创建的资源组。</span><span class="sxs-lookup"><span data-stu-id="8d611-190">Select **Resource groups**, then select the resource group you created.</span></span>

![Azure 门户：侧边栏菜单中的资源组](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="8d611-192">在“资源组”页面中，选择“删除”。</span><span class="sxs-lookup"><span data-stu-id="8d611-192">In the **Resource groups** page, select **Delete**.</span></span>

![Azure 门户：“资源组”页面](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="8d611-194">输入资源组的名称并选择“删除”。</span><span class="sxs-lookup"><span data-stu-id="8d611-194">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="8d611-195">现已从 Azure 中删除了本教程中创建的应用和其他所有资源。</span><span class="sxs-lookup"><span data-stu-id="8d611-195">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="8d611-196">后续步骤</span><span class="sxs-lookup"><span data-stu-id="8d611-196">Next steps</span></span>

* [<span data-ttu-id="8d611-197">使用 Visual Studio 和 Git 持续部署到 Azure</span><span class="sxs-lookup"><span data-stu-id="8d611-197">Continuous Deployment to Azure with Visual Studio and Git</span></span>](../publishing/azure-continuous-deployment.md)