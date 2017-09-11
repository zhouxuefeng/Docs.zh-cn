---
title: "在 ASP.NET Core on Mac 中开始使用 Razor 页面"
author: rick-anderson
description: "利用 Visual Studio for Mac 在 ASP.NET Core 中开始使用 Razor 页面"
keywords: "ASP.NET Core, Razor 页面, 基架, Entity Framework Core, EF, EF Core, 数据库, mac, macOS, Visual Studio for Mac"
ms.author: riande
manager: wpickett
ms.date: 7/27/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages-mac/razor-pages-start
ms.openlocfilehash: caadc3fcb3bb71abe0773aed4f6ff60a043e3a02
ms.sourcegitcommit: d9ec19e5452af83648074db5d96c0a0f4f9e7f9a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="getting-started-with-razor-pages-in-aspnet-core-with-visual-studio-for-mac"></a>借助 Visual Studio for Mac 在 ASP.NET Core 中开始使用 Razor 页面

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

本教程介绍构建 ASP.NET Core Razor 页面 Web 应用的基础知识。 我们建议在学习本教程前先阅读 [Razor 页面介绍](xref:mvc/razor-pages/index)。 Razor 页面是在 ASP.NET Core 中为 Web 应用程序生成 UI 时建议使用的方法。

## <a name="prerequisites"></a>先决条件

安装以下组件：

* [.NET Core 2.0.0 SDK](https://dot.net/core) 或更高版本
* [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="create-a-razor-web-app"></a>创建 Razor Web 应用

从终端运行以下命令：

```console
dotnet new razor -o RazorPagesMovie
cd RazorPagesMovie
dotnet run
```

上述命令使用 [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/dotnet) 创建并运行 Razor 页面项目。 打开浏览器，转到 http://localhost:5000 查看应用程序。

![主页或索引页](../razor-pages/razor-pages-start/_static/home.png)

[!INCLUDE[razor-pages-start](../../includes/RP/razor-pages-start.md)]

## <a name="open-the-project"></a>打开项目

按 Ctrl+C 关闭应用程序。

在 Visual Studio 中，选择“文件”>“打开”，然后选择“RazorPagesMovie.csproj”文件。

### <a name="launch-the-app"></a>启动应用

在 Visual Studio 中，选择“运行”>“启动但不调试”以启动应用。 Visual Studio 启动 [IIS Express](http://www.iis.net/learn/extensions/introduction-to-iis-express/iis-express-overview)、启动浏览器并导航到 `http://localhost:5000`。

在下一个教程中，我们将向项目添加模型。

>[!div class="step-by-step"]
[下一篇：添加模型](xref:tutorials/razor-pages-mac/model)
