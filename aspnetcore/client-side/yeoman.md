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
# <a name="introduction-to-building-projects-with-yeoman-in-aspnet-core"></a><span data-ttu-id="1121f-104">在 ASP.NET Core Yeoman 后生成项目的简介</span><span class="sxs-lookup"><span data-stu-id="1121f-104">Introduction to building projects with Yeoman in ASP.NET Core</span></span>

<span data-ttu-id="1121f-105">[Yeoman](http://yeoman.io/)是用于创建许多类型的应用程序的项目基架系统。</span><span class="sxs-lookup"><span data-stu-id="1121f-105">[Yeoman](http://yeoman.io/) is a project scaffolding system for creating many kinds of applications.</span></span> <span data-ttu-id="1121f-106">Yeoman ASP.NET Core 的生成器包含各种用于启动新的 web、 MVC 或控制台应用程序的项目模板。</span><span class="sxs-lookup"><span data-stu-id="1121f-106">The Yeoman generator for ASP.NET Core contains a variety of project templates for starting a new web, MVC, or console application.</span></span>

## <a name="install-nodejs-npm-and-yeoman"></a><span data-ttu-id="1121f-107">安装 Node.js、 npm 和 Yeoman</span><span class="sxs-lookup"><span data-stu-id="1121f-107">Install Node.js, npm, and Yeoman</span></span>

### <a name="prerequisites"></a><span data-ttu-id="1121f-108">先决条件</span><span class="sxs-lookup"><span data-stu-id="1121f-108">Prerequisites</span></span>

<span data-ttu-id="1121f-109">Node.js 和 npm 所需的 Yeoman。</span><span class="sxs-lookup"><span data-stu-id="1121f-109">Node.js and npm are required for Yeoman.</span></span> <span data-ttu-id="1121f-110">从下载[Node.js](https://nodejs.org/)。</span><span class="sxs-lookup"><span data-stu-id="1121f-110">Download from [Node.js](https://nodejs.org/).</span></span> <span data-ttu-id="1121f-111">安装程序包括[Node.js](https://nodejs.org/)和[npm](https://www.npmjs.com/)。</span><span class="sxs-lookup"><span data-stu-id="1121f-111">The installer includes [Node.js](https://nodejs.org/) and [npm](https://www.npmjs.com/).</span></span> <span data-ttu-id="1121f-112">Bower，也需要安装如 Bootstrap 的 UI 框架。</span><span class="sxs-lookup"><span data-stu-id="1121f-112">Bower is also required for installing UI frameworks like Bootstrap.</span></span>

<span data-ttu-id="1121f-113">若要安装 Yeoman 和 Bower，运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1121f-113">To install Yeoman and Bower, run the following command:</span></span>

```console
npm install -g yo bower
```

>[!Note]
><span data-ttu-id="1121f-114">如果你将收到错误`npm ERR! Please try running this command again as root/Administrator.`上 macOS，运行以下命令使用[sudo](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/sudo.8.html):`sudo npm install -g yo bower`</span><span class="sxs-lookup"><span data-stu-id="1121f-114">If you get the error `npm ERR! Please try running this command again as root/Administrator.` on macOS, run the following command using [sudo](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/sudo.8.html): `sudo npm install -g yo bower`</span></span>

<span data-ttu-id="1121f-115">在命令提示符下安装 ASP.NET 生成器：</span><span class="sxs-lookup"><span data-stu-id="1121f-115">From a command prompt, install the ASP.NET generator:</span></span>

```console
npm install -g generator-aspnet
```

> [!NOTE]
> <span data-ttu-id="1121f-116">如果收到权限错误时，运行下的命令`sudo`上文所述。</span><span class="sxs-lookup"><span data-stu-id="1121f-116">If you get a permission error, run the command under `sudo` as described above.</span></span>

<span data-ttu-id="1121f-117">`–g`标志生成器将全局安装，以便它可从任何路径。</span><span class="sxs-lookup"><span data-stu-id="1121f-117">The `–g` flag installs the generator globally, so that it can be used from any path.</span></span>

## <a name="create-an-aspnet-app"></a><span data-ttu-id="1121f-118">创建 ASP.NET 应用程序</span><span class="sxs-lookup"><span data-stu-id="1121f-118">Create an ASP.NET app</span></span>

<span data-ttu-id="1121f-119">运行基于 Yeoman ASP.NET 生成器：</span><span class="sxs-lookup"><span data-stu-id="1121f-119">Run the Yeoman-based ASP.NET generator:</span></span>

```console
yo aspnet
```

<span data-ttu-id="1121f-120">生成器显示菜单。</span><span class="sxs-lookup"><span data-stu-id="1121f-120">The generator displays a menu.</span></span> <span data-ttu-id="1121f-121">向下箭头**基本 [没有成员身份和授权] 的应用程序 Web**项目，然后点击**Enter**:</span><span class="sxs-lookup"><span data-stu-id="1121f-121">Arrow down to the **Web Application Basic [without Membership and Authorization]** project and tap **Enter**:</span></span>

![命令窗口： 你想要创建什么类型的应用程序？](yeoman/_static/yeoman-yo-aspnet.png)

<span data-ttu-id="1121f-124">选择 Bootstrap 作为 UI 框架，然后点击**Enter**。</span><span class="sxs-lookup"><span data-stu-id="1121f-124">Select Bootstrap as the UI Framework and tap **Enter**.</span></span>

<span data-ttu-id="1121f-125">使用"**MyWebApp**"为应用程序名称，然后点击**Enter**。</span><span class="sxs-lookup"><span data-stu-id="1121f-125">Use "**MyWebApp**" for the app name and then tap **Enter**.</span></span>

<span data-ttu-id="1121f-126">项目和及其支持文件，yeoman 将创建基架。</span><span class="sxs-lookup"><span data-stu-id="1121f-126">Yeoman will scaffold the project and its supporting files.</span></span> <span data-ttu-id="1121f-127">形式的命令中还提供的建议下一步的步骤。</span><span class="sxs-lookup"><span data-stu-id="1121f-127">Suggested next steps are also provided in the form of commands.</span></span>

![命令窗口： ASP.NET 应用程序的名称是什么？](yeoman/_static/yeoman-yo-aspnet-created.png)

<span data-ttu-id="1121f-130">[ASP.NET 生成器](https://www.npmjs.com/package/generator-aspnet)创建项目可加载到 Visual Studio 代码，Visual Studio 中，或从命令行运行的 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="1121f-130">The [ASP.NET generator](https://www.npmjs.com/package/generator-aspnet) creates ASP.NET Core projects that can be loaded into Visual Studio Code, Visual Studio, or run from the command line.</span></span>

## <a name="restore-build-and-run"></a><span data-ttu-id="1121f-131">还原、 生成和运行</span><span class="sxs-lookup"><span data-stu-id="1121f-131">Restore, build, and run</span></span>

<span data-ttu-id="1121f-132">通过更改目录，以按照建议的命令`MyWebApp`目录。</span><span class="sxs-lookup"><span data-stu-id="1121f-132">Follow the suggested commands by changing directories to the `MyWebApp` directory.</span></span> <span data-ttu-id="1121f-133">然后运行`dotnet restore`。</span><span class="sxs-lookup"><span data-stu-id="1121f-133">Then run `dotnet restore`.</span></span>

![“命令”窗口](yeoman/_static/dotnet-restore.png)

<span data-ttu-id="1121f-135">生成并运行应用程序使用`dotnet build`和`dotnet run`:</span><span class="sxs-lookup"><span data-stu-id="1121f-135">Build and run the app using `dotnet build` and `dotnet run`:</span></span>

![“命令”窗口](yeoman/_static/dotnet-build-run.png)

<span data-ttu-id="1121f-137">此时，你可以导航到显示要测试新创建的 ASP.NET Core 应用的 URL。</span><span class="sxs-lookup"><span data-stu-id="1121f-137">At this point, you can navigate to the URL shown to test the newly-created ASP.NET Core app.</span></span>

## <a name="client-side-packages"></a><span data-ttu-id="1121f-138">客户端包</span><span class="sxs-lookup"><span data-stu-id="1121f-138">Client-side packages</span></span>

<span data-ttu-id="1121f-139">从 Yeoman 模板提供前端资源生成器使用[Bower](xref:client-side/bower)客户端包管理器中，添加*bower.json*和*.bowerrc*要还原使用 Bower 的客户端包的文件。</span><span class="sxs-lookup"><span data-stu-id="1121f-139">The front-end resources are provided by the templates from the Yeoman generator using the [Bower](xref:client-side/bower) client-side package manager, adding *bower.json* and *.bowerrc* files to restore client-side packages using Bower.</span></span>

<span data-ttu-id="1121f-140">[BundlerMinifier](xref:client-side/bundling-and-minification)组件还包含默认情况下，对于串联 （绑定） 的易用性和缩减的 CSS、 JavaScript 和 HTML。</span><span class="sxs-lookup"><span data-stu-id="1121f-140">The [BundlerMinifier](xref:client-side/bundling-and-minification) component is also included by default for ease of concatenation (bundling) and minification of CSS, JavaScript, and HTML.</span></span>

## <a name="building-and-running-from-visual-studio"></a><span data-ttu-id="1121f-141">生成并运行从 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1121f-141">Building and running from Visual Studio</span></span>

<span data-ttu-id="1121f-142">你可以生成的 ASP.NET 核心 web 项目直接加载到 Visual Studio 中，然后生成并从那里运行您的项目。</span><span class="sxs-lookup"><span data-stu-id="1121f-142">You can load your generated ASP.NET Core web project directly into Visual Studio, then build and run your project from there.</span></span> <span data-ttu-id="1121f-143">遵循上面的说明来创建新的 ASP.NET Core 应用程序使用 Yeoman 的基架。</span><span class="sxs-lookup"><span data-stu-id="1121f-143">Follow the instructions above to scaffold a new ASP.NET Core app using Yeoman.</span></span> <span data-ttu-id="1121f-144">此时，选择**Web 应用程序**从菜单和名称应用`MyWebApp`。</span><span class="sxs-lookup"><span data-stu-id="1121f-144">This time, choose **Web Application** from the menu and name the app `MyWebApp`.</span></span>

<span data-ttu-id="1121f-145">打开 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="1121f-145">Open Visual Studio.</span></span> <span data-ttu-id="1121f-146">从文件菜单中，选择打开 ‣ 项目/解决方案。</span><span class="sxs-lookup"><span data-stu-id="1121f-146">From the File menu, select Open ‣ Project/Solution.</span></span>

<span data-ttu-id="1121f-147">在打开项目对话框中，导航到*.csproj*文件，选择它，然后单击**打开**按钮。</span><span class="sxs-lookup"><span data-stu-id="1121f-147">In the Open Project dialog, navigate to the *.csproj* file, select it, and click the **Open** button.</span></span> <span data-ttu-id="1121f-148">在解决方案资源管理器，该项目应类似于下面的屏幕截图。</span><span class="sxs-lookup"><span data-stu-id="1121f-148">In the Solution Explorer, the project should look something like the screenshot below.</span></span>

![文件和文件夹的解决方案资源管理器中的新项目](yeoman/_static/yeoman-solution.png)

<span data-ttu-id="1121f-150">Yeoman scaffolds MVC web 应用程序，完成两个服务器和客户端生成的支持。</span><span class="sxs-lookup"><span data-stu-id="1121f-150">Yeoman scaffolds a MVC web application, complete with both server- and client-side build support.</span></span> <span data-ttu-id="1121f-151">服务器端依赖关系下列出**依赖关系/NuGet**节点，然后客户端中的依赖关系**依赖关系/Bower**的解决方案资源管理器的节点。</span><span class="sxs-lookup"><span data-stu-id="1121f-151">Server-side dependencies are listed under the **Dependencies/NuGet** node, and client-side dependencies in the **Dependencies/Bower** node of Solution Explorer.</span></span> <span data-ttu-id="1121f-152">加载项目时，将自动还原依赖关系。</span><span class="sxs-lookup"><span data-stu-id="1121f-152">Dependencies are restored automatically when the project is loaded.</span></span>

![在解决方案资源管理器树视图中的依赖项节点中，下的 Bower 文件夹已打开列出其依赖项。](yeoman/_static/yeoman-loading-dependencies.png)

<span data-ttu-id="1121f-154">当还原所有依赖项时，请按**F5**以运行该项目。</span><span class="sxs-lookup"><span data-stu-id="1121f-154">When all the dependencies are restored, press **F5** to run the project.</span></span> <span data-ttu-id="1121f-155">在浏览器中显示默认的主页。</span><span class="sxs-lookup"><span data-stu-id="1121f-155">The default home page displays in the browser.</span></span>

![Web 应用程序在 Microsoft Edge 中打开](yeoman/_static/yeoman-home-page.png)

## <a name="restoring-building-and-hosting-from-a-command-line"></a><span data-ttu-id="1121f-157">还原、 构建和托管从命令行</span><span class="sxs-lookup"><span data-stu-id="1121f-157">Restoring, building, and hosting from a command line</span></span>

<span data-ttu-id="1121f-158">你可以准备和承载 web 应用程序使用.NET 核心 CLI。</span><span class="sxs-lookup"><span data-stu-id="1121f-158">You can prepare and host your web application using the .NET Core CLI.</span></span>

<span data-ttu-id="1121f-159">在命令提示符下，将当前目录更改到包含项目的文件夹 (即，此文件夹包含*.csproj*文件):</span><span class="sxs-lookup"><span data-stu-id="1121f-159">At a command prompt, change the current directory to the folder containing the project (that is, the folder containing the *.csproj* file):</span></span>

```console
cd src\MyWebApp
```

<span data-ttu-id="1121f-160">还原项目的 NuGet 包依赖项：</span><span class="sxs-lookup"><span data-stu-id="1121f-160">Restore the project's NuGet package dependencies:</span></span>

```console
dotnet restore
```

<span data-ttu-id="1121f-161">运行应用程序：</span><span class="sxs-lookup"><span data-stu-id="1121f-161">Run the application:</span></span>

```console
dotnet run
```

<span data-ttu-id="1121f-162">跨平台[Kestrel](xref:fundamentals/servers/kestrel) web 服务器将开始在端口 5000 上侦听。</span><span class="sxs-lookup"><span data-stu-id="1121f-162">The cross-platform [Kestrel](xref:fundamentals/servers/kestrel) web server will begin listening on port 5000.</span></span>

<span data-ttu-id="1121f-163">打开 web 浏览器，并导航到`http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="1121f-163">Open a web browser, and navigate to `http://localhost:5000`.</span></span>

![Web 应用程序在 Microsoft Edge 中打开](yeoman/_static/yeoman-home-page_5000.png)

## <a name="adding-to-your-project-with-sub-generators"></a><span data-ttu-id="1121f-165">与子生成器将添加到你的项目</span><span class="sxs-lookup"><span data-stu-id="1121f-165">Adding to your project with sub generators</span></span>

<span data-ttu-id="1121f-166">使用 Yeoman [sub 生成器](https://github.com/omnisharp/generator-aspnet)，则可添加`nuget.config`或`web.config`创建项目之后。</span><span class="sxs-lookup"><span data-stu-id="1121f-166">Using Yeoman [sub generators](https://github.com/omnisharp/generator-aspnet), you can add either a `nuget.config` or a `web.config` after the project is created.</span></span> <span data-ttu-id="1121f-167">例如，从应在其中创建该文件的目录执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1121f-167">For example, execute the following command from the directory in which the file should be created:</span></span>

```console
yo aspnet:nugetconfig
```

<span data-ttu-id="1121f-168">结果是名为的 NuGet 配置文件`nuget.config`包含以下内容：</span><span class="sxs-lookup"><span data-stu-id="1121f-168">The result is a NuGet configuration file named `nuget.config` with the following content:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
 <packageSources>
    <!--To inherit the global NuGet package sources remove the <clear/> line below -->
    <clear />
 </packageSources>
</configuration>
```

## <a name="additional-resources"></a><span data-ttu-id="1121f-169">其他资源</span><span class="sxs-lookup"><span data-stu-id="1121f-169">Additional resources</span></span>

* [<span data-ttu-id="1121f-170">服务器 （Kestrel 和 WebListener）</span><span class="sxs-lookup"><span data-stu-id="1121f-170">Servers (Kestrel and WebListener)</span></span>](xref:fundamentals/servers/index)
* [<span data-ttu-id="1121f-171">基础知识</span><span class="sxs-lookup"><span data-stu-id="1121f-171">Fundamentals</span></span>](xref:fundamentals/index)
