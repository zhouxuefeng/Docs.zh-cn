---
title: "在 ASP.NET 核心中的浏览器链接"
author: ncarandini
description: "Visual Studio 功能，链接使用一个或多个 web 浏览器在开发环境"
keywords: "ASP.NET 核心，浏览器链接，CSS 同步"
ms.author: riande
manager: wpickett
ms.date: 12/28/2016
ms.topic: article
ms.assetid: 11813d4c-3f8a-445a-b23b-e4a57d001abc
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/using-browserlink
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b2ff38288cee3e9ca42a07c219521bb79a00a359
ms.sourcegitcommit: 4e84d8bf5f404bb77f3d41665cf7e7374fc39142
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2017
---
# <a name="introduction-to-browser-link-in-aspnet-core"></a>在 ASP.NET 核心中的浏览器链接简介 

通过[Nicolò Carandini](https://github.com/ncarandini)， [Mike Wasson](https://github.com/MikeWasson)，和[Tom Dykstra](https://github.com/tdykstra)

浏览器链接是创建开发环境和一个或多个 web 浏览器之间的通信通道的 Visual Studio 中的功能。 你可以使用浏览器链接刷新 web 应用程序在多个浏览器中的，这是适用于跨浏览器测试。

## <a name="browser-link-setup"></a>浏览器链接安装程序

ASP.NET 核心**Web 应用程序**项目模板在 Visual Studio 2015 和更高版本包括所需的浏览器链接一切。

若要添加到使用 ASP.NET Core 创建的项目，浏览器链接**空**或**Web API**模板，请按照下列步骤：

1. 添加*Microsoft.VisualStudio.Web.BrowserLink.Loader*包 
2. 将配置代码中的添加*Startup.cs*文件。

### <a name="add-the-package"></a>将包添加

由于这是 Visual Studio 功能，将包添加的最简单方法是打开**程序包管理器控制台**(**视图 > 其他 Windows > 程序包管理器控制台**) 并运行以下命令：

```console
install-package Microsoft.VisualStudio.Web.BrowserLink.Loader
```

或者，可以使用**NuGet 包管理器**。  右键单击中的项目名称**解决方案资源管理器**，然后选择**管理 NuGet 包**。 

![打开 NuGet 包管理器](using-browserlink/_static/open-nuget-package-manager.png)

然后找到并安装包。

![添加包使用 NuGet 包管理器](using-browserlink/_static/add-package-with-nuget-package-manager.png)

### <a name="add-configuration-code"></a>将配置代码添加

打开*Startup.cs*文件，然后在`Configure`方法添加以下代码：

```csharp
app.UseBrowserLink();
```

该代码通常位于`if`块只在开发环境，使浏览器链接，如下所示：

[!code-csharp[Main](./using-browserlink/sample/BrowserLinkSample/src/BrowserLinkSample/Startup.cs?highlight=1,4&range=40-44)]

有关详细信息，请参阅[使用多个环境](../fundamentals/environments.md)。

## <a name="how-to-use-browser-link"></a>如何使用浏览器链接

如果你拥有打开 ASP.NET Core 项目，Visual Studio 将旁边显示浏览器链接工具栏控件**调试目标**工具栏控件：

![浏览器链接下拉列表菜单](using-browserlink/_static/browserLink-dropdown-menu.png)

通过浏览器链接工具栏控件，你可以：

- 刷新 web 应用程序在多个浏览器中的一次
- 打开**浏览器链接仪表板**
- 启用或禁用**浏览器链接**
- 启用或禁用 CSS 自动同步

> [!NOTE]
> 某些 Visual Studio 插件，最值得注意的是*Web 扩展包 2015年*和*Web 扩展包 2017年*，提供扩展的功能的浏览器链接，但某些其他功能不与 ASP 配合使用。NET 核心项目。

## <a name="refresh-the-web-application-in-several-browsers-at-once"></a>刷新 web 应用程序在多个浏览器中的一次

若要选择单个 web 浏览器以启动启动项目时，使用中的下拉列表菜单**调试目标**工具栏控件：

![F5 下拉列表菜单](using-browserlink/_static/debug-target-dropdown-menu.png)

若要同时打开多个浏览器，选择**浏览与...**从相同的下拉列表。  按住 CTRL 键以选择所需的浏览器，然后单击**浏览**:

![同时打开许多浏览器](using-browserlink/_static/open-many-browsers-at-once.png)

下面是示例屏幕快照显示与索引视图的 Visual Studio 打开和两个打开的浏览器：

![两个浏览器的示例与同步](using-browserlink/_static/sync-with-two-browsers-example.png)

将鼠标悬停在该浏览器链接工具栏控件，以查看连接到的项目的浏览器：

![将鼠标悬停提示](using-browserlink/_static/hoover-tip.png)

更改索引视图中，并单击浏览器链接刷新按钮后，将更新所有连接的浏览器：

![浏览器更改同步](using-browserlink/_static/browsers-sync-to-changes.png)

浏览器链接也适用于浏览器，从 Visual Studio 外部启动并导航到应用程序 URL。

### <a name="the-browser-link-dashboard"></a>浏览器链接仪表板

从浏览器链接下拉菜单来管理与打开的浏览器的连接打开浏览器链接仪表板：

![仪表-板 browserslink 打开](using-browserlink/_static/open-browserlink-dashboard.png)

如果连接没有浏览器，你可以启动非调试会话单击_用浏览器查看_链接：

![浏览器链接仪表板-无连接](using-browserlink/_static/browserlink-dashboard-no-connections.png)

否则，连接的浏览器将显示，替换为显示每个浏览器的页面的路径：

![浏览器链接仪表板的两个连接](using-browserlink/_static/browserlink-dashboard-two-connections.png)

如果您愿意，你可以单击要刷新该单个浏览器的列出的浏览器名称。

### <a name="enable-or-disable-browser-link"></a>启用或禁用浏览器链接

当重新启用 Browser Link 禁用它之后时，你必须刷新浏览器以将它们重新连接。

### <a name="enable-or-disable-css-auto-sync"></a>启用或禁用 CSS 自动同步

启用 CSS 自动同步后，对 CSS 文件进行任何更改时自动刷新连接的浏览器。

## <a name="how-does-it-work"></a>Application Insights 如何工作？

浏览器链接使用 SignalR 来创建 Visual Studio 和浏览器之间的通信通道。 启用浏览器链接后，Visual Studio 将充当多个客户端 （浏览器） 可以连接到的 SignalR 服务器。 浏览器链接也在 ASP.NET 请求管道中注册的中间件组件。 此组件将插入特殊`<script>`到每个页请求从服务器的引用。 您可以通过选择查看脚本引用**查看源**在浏览器和左边缘滚动`<body>`标记内容：

```javascript
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

不会修改你的源文件。 中间件组件动态插入脚本引用。 

由于浏览器端代码是所有 JavaScript，则它将适用于 SignalR 支持，而无需任何浏览器插件的所有浏览器。
