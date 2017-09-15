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
# <a name="publish-an-aspnet-core-web-app-to-azure-app-service-using-visual-studio"></a>使用 Visual Studio 将 ASP.NET Core Web 应用发布到 Azure App Service

作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Cesar Blum Silveira](https://github.com/cesarbs)

## <a name="set-up-the-development-environment"></a>设置开发环境

* 安装最新版本的 [Azure SDK for Visual Studio](https://www.visualstudio.com/features/azure-tools-vs)。 如果没有 Visual Studio，SDK 将进行安装。

> [!NOTE]
> 如果计算机缺少部分依赖项，SDK 安装可能耗时超过 30 分钟。

* 安装 [.NET Core 和 Visual Studio 工具](http://go.microsoft.com/fwlink/?LinkID=798306)

* 验证 [Azure 帐户](https://portal.azure.com/)。 可[创建免费的 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)或[激活 Visual Studio 订阅者权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。

## <a name="create-a-web-app"></a>创建 Web 应用

在 Visual Studio 启动页中，点击“新建项目...”。

![启动页](publish-to-azure-webapp-using-vs/_static/new_project.png)

或者可使用菜单创建新项目。 点击“文件”>“新建”>“项目...”。

![“文件”菜单](publish-to-azure-webapp-using-vs/_static/alt_new_project.png)

完成“新建项目”对话框：

* 在左窗格中，点击“Web”

* 在中间窗格中，点击“ASP.NET Core Web 应用程序(.NET Core)”

* 点击“确定”

![“新建项目”对话框](publish-to-azure-webapp-using-vs/_static/new_prj.png)

在“新建 ASP.NET Core Web 应用程序(.NET Core)”对话框中：

* 点击“Web 应用程序”

* 验证“身份验证”是否设置为“单个用户帐户”

* 验证确保未勾选“在云中托管”

* 点击“确定”

![“新建 ASP.NET Core Web 应用程序(.NET Core)”对话框](publish-to-azure-webapp-using-vs/_static/noath.png)

## <a name="test-the-app-locally"></a>本地测试应用

* 按“Ctrl-F5”在本地运行应用

* 点击“关于”和“联系”链接。 根据设备的大小，可能需要点击导航图标以显示链接

![localhost 上的 Microsoft Edge 中打开的 Web 应用程序](publish-to-azure-webapp-using-vs/_static/show.png)

* 点击“注册”并注册新用户。 可使用虚构电子邮件地址。 提交时，会遇到以下错误：

![内部服务器错误: 处理请求时，数据库操作失败。 SQL 异常: 无法打开数据库。 可能可通过向应用程序数据库上下文应用现有迁移解决此问题。](publish-to-azure-webapp-using-vs/_static/mig.png)

可通过两种方法修复此问题：

* 点击“应用迁移”，并在页面更新后刷新页面；或者

* 从项目目录中的命令提示符处运行以下命令：

  <!-- literal_block {"ids": [], "xml:space": "preserve"} -->

  ```
  dotnet ef database update
     ```

应用将显示用于注册新用户的电子邮件和一个“注销”链接。

![Microsoft Edge 中打开的 Web 应用程序。 注册链接会替换为文本“Hello abc@example.com!”](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>将应用部署到 Azure

确认要部署的已发布应用未运行。 如果应用正在运行，publish 文件夹中的文件会被锁定。 不会进行部署，因为无法复制锁定的文件。

在解决方案资源管理器中右键单击该项目，然后选择“发布...”。

![打开且突出显示“发布”链接的上下文菜单](publish-to-azure-webapp-using-vs/_static/pub.png)

在“发布”对话框中，点击“Microsoft Azure App Service”。

![“发布”对话框](publish-to-azure-webapp-using-vs/_static/maas1.png)

点击“新建...”来创建新的资源组。 通过新建资源组，可更轻松地删除本教程中创建的所有 Azure 资源。

![“应用服务”对话框](publish-to-azure-webapp-using-vs/_static/newrg1.png)

创建新的资源组和应用服务计划：

* 点击“新建...”来创建资源组，并输入新资源组的名称

* 点击“新建...”来创建应用服务计划，并选择你附近的位置。 可保留默认生成的名称

* 点击“浏览其他 Azure 服务”来创建新的数据库

![“新建资源组”对话框：托管面板](publish-to-azure-webapp-using-vs/_static/cas.png)

* 点击绿色的 + 图标，创建新的 SQL 数据库

![“新建资源组”对话框：服务面板](publish-to-azure-webapp-using-vs/_static/sql.png)

* 在“配置 SQL 数据库”对话框上点击“新建...”来创建新数据库服务器。

![“配置 SQL 数据库”对话框](publish-to-azure-webapp-using-vs/_static/conf.png)

* 输入管理员用户名和密码，然后点击“确定”。 请牢记本步骤中创建的用户名和密码。 可保留默认的“服务器名称”

![“配置 SQL Server”对话框](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

> [!NOTE]
> 不可使用“admin”作为管理员用户名。

* 点击“配置 SQL 数据库”对话框上的“确定”

![“配置 SQL 数据库”对话框](publish-to-azure-webapp-using-vs/_static/conf_final.png)

* 点击“创建应用服务”对话框上的“创建”

![“创建应用服务”对话框](publish-to-azure-webapp-using-vs/_static/create_as.png)

* 在“发布”对话框中点击“下一步”

![“发布”对话框：连接面板](publish-to-azure-webapp-using-vs/_static/pubc.png)

* 在“发布”对话框的“设置”阶段：

  * 展开“数据库”并选中“在运行时使用此连接字符串”

  * 展开“Entity Framework 迁移”并选中“在发布时应用此迁移”

* 点击“发布”，并在 Visual Studio 完成应用发布前稍作等待

![“发布”对话框：设置面板](publish-to-azure-webapp-using-vs/_static/pubs.png)

Visual Studio 会将应用发布到 Azure，并在浏览器中启动云应用。

### <a name="test-your-app-in-azure"></a>在 Azure 中测试应用

* 测试“关于”和“联系”链接

* 注册新用户

![Microsoft Edge 中 Azure App Service 上打开的 Web 应用程序](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="update-the-app"></a>更新应用

* 编辑 `Views/Home/About.cshtml` Razor 视图文件并更改其内容。 例如：

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [7]}} -->

```html
@{
       ViewData["Title"] = "About";
   }
   <h2>@ViewData["Title"].</h2>
   <h3>@ViewData["Message"]</h3>

   <p>My updated about page.</p>
   ```

* 右键单击项目，然后再次点击“发布...”

![打开且突出显示“发布”链接的上下文菜单](publish-to-azure-webapp-using-vs/_static/pub.png)

* 应用发布后，验证所作更改在 Azure 上是否可用

### <a name="clean-up"></a>清理

完成应用测试后，转到 [Azure 门户](https://portal.azure.com/)并删除该应用。

* 选择“资源组”，然后点击创建的资源组

![Azure 门户：侧边栏菜单中的资源组](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* 在“资源组”边栏选项卡中，点击“删除”

![Azure 门户：“资源组”边栏选项卡](publish-to-azure-webapp-using-vs/_static/rgd.png)

* 输入资源组的名称并点击“删除”。 将从 Azure 中删除本教程中创建的应用和其他所有资源

### <a name="next-steps"></a>后续步骤

* [ASP.NET Core MVC 和 Visual Studio 入门](first-mvc-app/start-mvc.md)

* [ASP.NET Core 简介](../index.md)

* [基础知识](../fundamentals/index.md)
