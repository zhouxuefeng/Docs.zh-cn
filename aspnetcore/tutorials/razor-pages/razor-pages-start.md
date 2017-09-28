---
title: "在 ASP.NET Core 中开始使用 Razor 页面"
author: rick-anderson
description: "在 ASP.NET Core 中开始使用 Razor 页面"
keywords: "ASP.NET Core, Razor 页面, Razor, MVC"
ms.author: riande
manager: wpickett
ms.date: 08/27/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 8c6e281e761e69908fc742d1f19c14a00de4bd46
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2017
---
# <a name="getting-started-with-razor-pages-in-aspnet-core"></a>在 ASP.NET Core 中开始使用 Razor 页面

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

本教程介绍构建 ASP.NET Core Razor 页面 Web 应用的基础知识。 我们建议在学习本教程前先阅读 [Razor 页面介绍](xref:mvc/razor-pages/index)。 Razor 页面是在 ASP.NET Core 中为 Web 应用程序生成 UI 时建议使用的方法。

## <a name="prerequisites"></a>先决条件

[!INCLUDE[install 2.0](../../includes/install2.0.md)]

## <a name="create-a-razor-web-app"></a>创建 Razor Web 应用

* 从 Visual Studio“文件”菜单中选择“新建”>“项目”。
* 创建新的 ASP.NET Core Web 应用程序。 将项目命名为“RazorPagesMovie”。 将项目命名为“RazorPagesMovie”，以便命名空间在你复制/粘贴代码时相互匹配。
 ![新建 ASP.NET Core Web 应用程序](../../mvc/razor-pages/index/_static/np.png)
* 在下拉列表中选择“ASP.NET Core 2.0”，然后选择“Web 应用程序”。
 ![Web 应用程序（Razor 页面）](../../mvc/razor-pages/index/_static/np2.png)

Visual Studio 模板创建初学者项目：

![“解决方案资源管理器”](razor-pages-start/_static/se.png)

按 F5 在调试模式下运行应用，或按 Ctrl-F5 在运行（不附加调试器）

![主页或索引页](razor-pages-start/_static/home.png)

* Visual Studio 启动 [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行你的应用。 地址栏显示 `localhost:port#`，而不显示 `example.com`。 这是因为 `localhost` 是本地计算机的标准主机名。 Localhost 仅为来自本地计算机的 Web 请求提供服务。 Visual Studio 创建 Web 项目时，为 Web 服务器使用随机端口。 在上图中，端口号为 5000。 运行应用时，会看到不同的端口号。
* 使用“Ctrl+F5”启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。 许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。

[!INCLUDE[razor-pages-start](../../includes/RP/razor-pages-start.md)]

>[!div class="step-by-step"]
[下一篇：添加模型](xref:tutorials/razor-pages/modelz)  
