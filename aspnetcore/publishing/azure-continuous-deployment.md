---
title: "使用 Visual Studio 和 Git 持续部署到 Azure"
author: rick-anderson
description: "了解如何使用 Visual Studio 创建 ASP.NET Core Web 应用并使用 Git 将它部署到 Azure App Service 以实现持续部署。"
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 2707c7a8-2350-4304-9856-fda58e5c0a16
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/azure-continuous-deployment
ms.openlocfilehash: a9efad38b1c75bd3a186b4ec85861357ecf744b9
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="continuous-deployment-to-azure-for-aspnet-core-with-visual-studio-and-git"></a>使用 Visual Studio 和 Git 将 ASP.NET Core 持续部署到 Azure

作者：[Erik Reitan](https://github.com/Erikre)

本教程演示如何使用 Visual Studio 创建 ASP.NET Core Web 应用并使用持续部署将其从 Visual Studio 部署到 Azure App Service。 

另请参阅 [Use VSTS to Build and Publish to an Azure Web App with Continuous Deployment](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)（使用 VSTS 生成并通过持续部署发布到 Azure Web 应用），其中演示如何使用 Visual Studio Team Services 为 [Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-changes-existing-services/) 配置持续交付 (CD) 工作流。 Team Services 中的 Azure 持续交付简化了用于将应用更新发布到 Azure App Service 的可靠部署管道的设置。 可以从 Azure 门户配置管道以生成、运行测试、部署到过渡槽，然后部署到生产。

> [!NOTE]
> 若要完成本教程，需要一个 Microsoft Azure 帐户。 如果没有帐户，可[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)或[注册免费试用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

本教程假定已经安装以下各组件：

* [Visual Studio](https://www.visualstudio.com)

* [ASP.NET Core](https://download.microsoft.com/download/F/6/E/F6ECBBCC-B02F-424E-8E03-D47E9FA631B7/DotNetCore.1.0.1-VS2015Tools.Preview2.0.3.exe)（运行时和工具）

* 用于 Windows 的 [Git](https://git-scm.com/downloads)

## <a name="create-an-aspnet-core-web-app"></a>创建 ASP.NET Core Web 应用

1. 启动 Visual Studio。

2. 从“文件”菜单中选择“新建” > “项目”。

3. 选择“ASP.NET Web 应用程序”项目模板。 它出现在“已安装” > “模板” > “Visual C#” > “Web”下。 将项目命名为 `SampleWebAppDemo`。 选择“创建新 Git 存储库”选项，然后单击“确定”。

   ![“新建项目”对话框](azure-continuous-deployment/_static/01-new-project.png)

4. 在“新建 ASP.NET 项目”对话框中，选择 ASP.NET Core 的“空”模板，然后单击“确定”。

   ![“新建 ASP.NET 项目”对话框](azure-continuous-deployment/_static/02-web-site-template.png)


### <a name="running-the-web-app-locally"></a>本地运行 Web 应用

1. Visual Studio 完成创建应用后，请选择“调试” -> “启动调试”以运行该应用。 作为替代方法，也可以按 F5。

   可能需要一些时间对 Visual Studio 和新应用进行初始化。 完成后，浏览器将显示正在运行的应用。

   ![显示正在运行的应用程序（显示“Hello World!”）的浏览器窗口](azure-continuous-deployment/_static/04-browser-runapp.png)

2. 查看完正在运行的 Web 应用后，关闭浏览器，然后单击 Visual Studio 工具栏中的“停止调试”图标以停止应用。

## <a name="create-a-web-app-in-the-azure-portal"></a>在 Azure 门户中创建 Web 应用

以下步骤将演示如何在 Azure 门户中创建 Web 应用。

1. 登录到 [Azure 门户](https://portal.azure.com)
2. 点击门户左上方的“新建”
3. 点击“Web + 移动” > “Web 应用”

    ![Microsoft Azure 门户：“新建”按钮：Marketplace 下的“Web + 移动”：“特别推荐的应用”下的“Web 应用”按钮](azure-continuous-deployment/_static/05-azure-newwebapp.png)

4.  在“Web 应用”边栏选项卡中，输入“应用服务名称”的唯一值。

    ![“Web 应用”边栏选项卡](azure-continuous-deployment/_static/06-azure-newappblade.png)

    >[!NOTE]
    >“应用服务名称”的名称必须是唯一的。 尝试输入名称时，门户将强制执行此规则。 在输入一个不同的值后，需要为在本教程中每次出现的“SampleWebAppDemo”替换该值。

    &nbsp;
    
    另外，在“Web 应用”边栏选项卡中，选择现有“应用服务计划/位置”或新建一个。 如果创建新的计划，请选择定价层、位置和其他选项。 有关应用服务计划的详细信息，请参阅 [Azure App Service 计划深入概述](https://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/)。

5.  单击 **“创建”**。 Azure 将预配并启动 Web 应用。

    ![Azure 门户：示例 Web 应用演示 01 Essentials 边栏选项卡](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>为新 Web 应用启用 Git 发布

Git 是一个分布式版本控制系统，可用来部署 Azure App Service Web 应用。 将为 Web 应用编写的代码将存储到本地 Git 存储库中，并通过推送到远程存储库将代码部署到 Azure。

1. 如果尚未登录，请先登录 [Azure 门户](https://portal.azure.com)。

2. 单击位于导航窗格底部的“浏览” 。

3. 单击“Web 应用”查看与 Azure 订阅关联的 Web 应用列表。

4. 选择在本教程的前一部分中创建的 Web 应用。

5. 如果未显示“设置”边栏选项卡，请选择“Web 应用”边栏选项卡中的“设置”。

6. 在“设置”边栏选项卡中，选择“部署源” > “选择源” > “本地 Git 存储库”。

   ![“设置”边栏选项卡：“部署源”边栏选项卡：“选择源”边栏选项卡](azure-continuous-deployment/_static/08-azure-localrepository.png)

7. 单击“确定”。

8. 如果事先未设置部署凭据用于发布 Web 应用或其他应用服务应用，请立即设置：

   * 单击“设置” > “部署凭据”。 “设置部署凭据”边栏选项卡将显示。

   * 创建用户名和密码。  稍后设置 Git 时需要此密码。

   * 单击“保存” 。

9. 在“Web 应用”边栏选项卡中，单击“设置” > “属性”。 将部署到的远程 Git 存储库的 URL 会显示在“GIT URL”下。

10. 复制“GIT URL”的值，稍后将在本教程中用到。

   ![Azure 门户：应用程序“属性”边栏选项卡](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-your-web-app-to-azure-app-service"></a>将 Web 应用发布到 Azure App Service

在本部分中，将使用 Visual Studio 创建本地 Git 存储库并从该存储库推送到 Azure 以部署 Web 应用。 涉及到的步骤如下：

   * 使用 GIT URL 值添加远程存储库设置，以将本地存储库部署到 Azure。

   * 提交项目更改。

   * 将项目更改从本地存储库推送到 Azure.上的远程存储库。

&nbsp;
   
1.  在“解决方案资源管理器”中右键单击“解决方案 ‘SampleWebAppDemo’”并选择“提交”。 “团队资源管理器”将显示。

    ![“团队资源管理器连接”选项卡](azure-continuous-deployment/_static/10-team-explorer.png)

2.  在“团队资源管理器”中，选择“主页”（主页图标）>“设置” > “存储库设置”。

3.  在“存储库设置”的“远程”部分中选择“添加”。 “添加远程”对话框将显示。

4.  将远程的“名称”设置为“Azure-SampleApp”。

5.  将“提取”的值设置为在本教程前面部分从 Azure 复制的“Git URL”。 请注意，此 URL 是以 .git 结尾的。

    ![“编辑远程”对话框](azure-continuous-deployment/_static/11-add-remote.png)

    >[!NOTE]
    >作为替代方法，可以通过打开“命令窗口”、更改为项目目录并输入命令从“命令窗口”指定远程存储库。 例如：`git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

6.  选择“主页”（主页图标）>“设置” > “全局设置”。 请确保已设置了姓名和电子邮件地址。 可能还需要选择“更新”。

7.  选择“主页” > “更改”以返回到“更改”视图。

8.  输入提交消息，如“初始推送 #1”，然后单击“提交”。 此操作将本地创建“提交”。 接下来，需要与 Azure 进行同步。

    ![“团队资源管理器连接”选项卡](azure-continuous-deployment/_static/12-initial-commit.png)

    >[!NOTE]
    >作为替代方法，可以通过打开“命令窗口”、更改为项目目录并输入 git 命令从“命令窗口”提交更改。 例如: 
    >
    >`git add .`
    >
    >`git commit -am "Initial Push #1"`

9.  选择“主页” > “同步” > “操作” > “打开命令提示符”。 命令提示符将针对项目目录打开。

10.  在命令窗口中输入以下命令：

    `git push -u Azure-SampleApp master`

11.  输入之前在 Azure 中创建的 Azure“部署凭据”密码。

    >[!NOTE]
    >输入时密码将不可见。

    此命令将启动将本地项目文件推送到 Azure 的进程。 上述命令的输出以部署成功的消息结尾。
        
    ```
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
    * [new branch]      master -> master
    Branch master set up to track remote branch master from Azure-SampleApp.
    ```
    > [!NOTE]
    > 如果需要协作处理项目，则应考虑在推送到 Azure 时推送到 [GitHub](https://github.com)。
 
### <a name="verify-the-active-deployment"></a>验证活动的部署

可以验证是否已成功将 Web 应用从本地环境传输到了 Azure。 你将看到列出的成功部署。

1. 在 [Azure 门户](https://portal.azure.com)中，选择 Web 应用。 然后，选择“设置” > “持续部署”。

   ![Azure 门户：“设置”边栏选项卡：显示成功部署的“部署”边栏选项卡](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a>在 Azure 中运行此应用

在将 Web 应用部署到 Azure 后就可以运行此应用了。

有两种方法可以做到这一点：

* 在 Azure 门户中，找到 Web 应用的“Web 应用”边栏选项卡，然后单击“浏览”在默认浏览器中查看应用。

* 打开浏览器并输入 Web 应用的 URL。 例如: 

  `http://SampleWebAppDemo.azurewebsites.net`

## <a name="update-your-web-app-and-republish"></a>更新 Web 应用并重新发布

对本地代码进行更改后，可以重新发布。

1.  在 Visual Studio 的“解决方案资源管理器”中，打开 Startup.cs 文件。

2.  在 `Configure` 方法中，修改 `Response.WriteAsync` 方法，使它显示以下内容：

    ```aspx-cs
    await context.Response.WriteAsync("Hello World! Deploy to Azure.");
    ```
3.  将保存更改到 Startup.cs。

4.  在“解决方案资源管理器”中右键单击“解决方案 ‘SampleWebAppDemo’”并选择“提交”。 “团队资源管理器”将显示。

5.  输入提交消息，如：

    ```none
    Update #2
    ```

6.  按“提交”按钮以提交项目更改。

7.  选择“主页” > “同步” > “操作” > “推送”。

>[!NOTE]
>作为替代方法，可以通过打开“命令窗口”、更改为项目目录并输入 git 命令从“命令窗口”推送更改。 例如: 
>
>`git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a>在 Azure 中查看更新的 Web 应用

通过从 Azure 门户中的“Web 应用”边栏选项卡选择“浏览”，或通过打开浏览器并输入 Web 应用的 URL 以查看更新的 Web 应用。 例如: 

   `http://SampleWebAppDemo.azurewebsites.net`

## <a name="additional-resources"></a>其他资源

* [发布和部署](index.md)

* [项目 Kudu](https://github.com/projectkudu/kudu/wiki)
