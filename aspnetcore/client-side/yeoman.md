---
title: "与在 ASP.NET Core Yeoman 生成项目"
author: spboyer
description: "本文将指导完成生成使用 Yeoman web 应用程序的 ASP.NET Core 在 macOS 上的生成器。"
keywords: "ASP.NET 核心、 Yeoman、 跨平台，则 aspnet"
ms.author: spboyer
manager: wpickett
ms.date: 07/05/2017
ms.topic: article
ms.assetid: fda0c2a8-1743-4505-be1a-7f8ceeef8647
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/yeoman
ms.openlocfilehash: 61561b55774faf375090c92b574a64f1a12f9647
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="introduction-to-building-projects-with-yeoman-in-aspnet-core"></a>在 ASP.NET Core Yeoman 后生成项目的简介

[Yeoman](http://yeoman.io/)是用于创建许多类型的应用程序的项目基架系统。 Yeoman ASP.NET Core 的生成器包含各种用于启动新的 web、 MVC 或控制台应用程序的项目模板。

## <a name="install-nodejs-npm-and-yeoman"></a>安装 Node.js、 npm 和 Yeoman

### <a name="prerequisites"></a>先决条件

Node.js 和 npm 所需的 Yeoman。 从下载[Node.js](https://nodejs.org/)。 安装程序包括[Node.js](https://nodejs.org/)和[npm](https://www.npmjs.com/)。 Bower，也需要安装如 Bootstrap 的 UI 框架。

若要安装 Yeoman 和 Bower，运行以下命令：

```console
npm install -g yo bower
```

>[!Note]
>如果你将收到错误`npm ERR! Please try running this command again as root/Administrator.`上 macOS，运行以下命令使用[sudo](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/sudo.8.html):`sudo npm install -g yo bower`

在命令提示符下安装 ASP.NET 生成器：

```console
npm install -g generator-aspnet
```

> [!NOTE]
> 如果收到权限错误时，运行下的命令`sudo`上文所述。

`–g`标志生成器将全局安装，以便它可从任何路径。

## <a name="create-an-aspnet-app"></a>创建 ASP.NET 应用程序

运行基于 Yeoman ASP.NET 生成器：

```console
yo aspnet
```

生成器显示菜单。 向下箭头**基本 [没有成员身份和授权] 的应用程序 Web**项目，然后点击**Enter**:

![命令窗口： 你想要创建什么类型的应用程序？ 菜单上的应用程序类型](yeoman/_static/yeoman-yo-aspnet.png)

选择 Bootstrap 作为 UI 框架，然后点击**Enter**。

使用"**MyWebApp**"为应用程序名称，然后点击**Enter**。

项目和及其支持文件，yeoman 将创建基架。 形式的命令中还提供的建议下一步的步骤。

![命令窗口： ASP.NET 应用程序的名称是什么？ 命令提示符](yeoman/_static/yeoman-yo-aspnet-created.png)

[ASP.NET 生成器](https://www.npmjs.com/package/generator-aspnet)创建项目可加载到 Visual Studio 代码，Visual Studio 中，或从命令行运行的 ASP.NET Core。

## <a name="restore-build-and-run"></a>还原、 生成和运行

通过更改目录，以按照建议的命令`MyWebApp`目录。 然后运行`dotnet restore`。

![“命令”窗口](yeoman/_static/dotnet-restore.png)

生成并运行应用程序使用`dotnet build`和`dotnet run`:

![“命令”窗口](yeoman/_static/dotnet-build-run.png)

此时，你可以导航到显示要测试新创建的 ASP.NET Core 应用的 URL。

## <a name="client-side-packages"></a>客户端包

从 Yeoman 模板提供前端资源生成器使用[Bower](xref:client-side/bower)客户端包管理器中，添加*bower.json*和*.bowerrc*要还原使用 Bower 的客户端包的文件。

[BundlerMinifier](xref:client-side/bundling-and-minification)组件还包含默认情况下，对于串联 （绑定） 的易用性和缩减的 CSS、 JavaScript 和 HTML。

## <a name="building-and-running-from-visual-studio"></a>生成并运行从 Visual Studio

你可以生成的 ASP.NET 核心 web 项目直接加载到 Visual Studio 中，然后生成并从那里运行您的项目。 遵循上面的说明来创建新的 ASP.NET Core 应用程序使用 Yeoman 的基架。 此时，选择**Web 应用程序**从菜单和名称应用`MyWebApp`。

打开 Visual Studio。 从文件菜单中，选择打开 ‣ 项目/解决方案。

在打开项目对话框中，导航到*.csproj*文件，选择它，然后单击**打开**按钮。 在解决方案资源管理器，该项目应类似于下面的屏幕截图。

![文件和文件夹的解决方案资源管理器中的新项目](yeoman/_static/yeoman-solution.png)

Yeoman scaffolds MVC web 应用程序，完成两个服务器和客户端生成的支持。 服务器端依赖关系下列出**依赖关系/NuGet**节点，然后客户端中的依赖关系**依赖关系/Bower**的解决方案资源管理器的节点。 加载项目时，将自动还原依赖关系。

![在解决方案资源管理器树视图中的依赖项节点中，下的 Bower 文件夹已打开列出其依赖项。](yeoman/_static/yeoman-loading-dependencies.png)

当还原所有依赖项时，请按**F5**以运行该项目。 在浏览器中显示默认的主页。

![Web 应用程序在 Microsoft Edge 中打开](yeoman/_static/yeoman-home-page.png)

## <a name="restoring-building-and-hosting-from-a-command-line"></a>还原、 构建和托管从命令行

你可以准备和承载 web 应用程序使用.NET 核心 CLI。

在命令提示符下，将当前目录更改到包含项目的文件夹 (即，此文件夹包含*.csproj*文件):

```console
cd src\MyWebApp
```

还原项目的 NuGet 包依赖项：

```console
dotnet restore
```

运行应用程序：

```console
dotnet run
```

跨平台[Kestrel](xref:fundamentals/servers/kestrel) web 服务器将开始在端口 5000 上侦听。

打开 web 浏览器，并导航到`http://localhost:5000`。

![Web 应用程序在 Microsoft Edge 中打开](yeoman/_static/yeoman-home-page_5000.png)

## <a name="adding-to-your-project-with-sub-generators"></a>与子生成器将添加到你的项目

使用 Yeoman [sub 生成器](https://github.com/omnisharp/generator-aspnet)，则可添加`nuget.config`或`web.config`创建项目之后。 例如，从应在其中创建该文件的目录执行以下命令：

```console
yo aspnet:nugetconfig
```

结果是名为的 NuGet 配置文件`nuget.config`包含以下内容：

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
 <packageSources>
    <!--To inherit the global NuGet package sources remove the <clear/> line below -->
    <clear />
 </packageSources>
</configuration>
```

## <a name="additional-resources"></a>其他资源

* [服务器 （Kestrel 和 WebListener）](xref:fundamentals/servers/index)
* [基础知识](xref:fundamentals/index)
