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
# <a name="introduction-to-browser-link-in-aspnet-core"></a><span data-ttu-id="63ea5-104">在 ASP.NET 核心中的浏览器链接简介</span><span class="sxs-lookup"><span data-stu-id="63ea5-104">Introduction to Browser Link in ASP.NET Core</span></span> 

<span data-ttu-id="63ea5-105">通过[Nicolò Carandini](https://github.com/ncarandini)， [Mike Wasson](https://github.com/MikeWasson)，和[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="63ea5-105">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="63ea5-106">浏览器链接是创建开发环境和一个或多个 web 浏览器之间的通信通道的 Visual Studio 中的功能。</span><span class="sxs-lookup"><span data-stu-id="63ea5-106">Browser Link is a feature in Visual Studio that creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="63ea5-107">你可以使用浏览器链接刷新 web 应用程序在多个浏览器中的，这是适用于跨浏览器测试。</span><span class="sxs-lookup"><span data-stu-id="63ea5-107">You can use Browser Link to refresh your web application in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="63ea5-108">浏览器链接安装程序</span><span class="sxs-lookup"><span data-stu-id="63ea5-108">Browser Link setup</span></span>

<span data-ttu-id="63ea5-109">ASP.NET 核心**Web 应用程序**项目模板在 Visual Studio 2015 和更高版本包括所需的浏览器链接一切。</span><span class="sxs-lookup"><span data-stu-id="63ea5-109">The ASP.NET Core **Web Application** project templates in Visual Studio 2015 and later include everything needed for Browser Link.</span></span>

<span data-ttu-id="63ea5-110">若要添加到使用 ASP.NET Core 创建的项目，浏览器链接**空**或**Web API**模板，请按照下列步骤：</span><span class="sxs-lookup"><span data-stu-id="63ea5-110">To add Browser Link to a project that you created by using the ASP.NET Core **Empty** or **Web API** template, follow these steps:</span></span>

1. <span data-ttu-id="63ea5-111">添加*Microsoft.VisualStudio.Web.BrowserLink.Loader*包</span><span class="sxs-lookup"><span data-stu-id="63ea5-111">Add the *Microsoft.VisualStudio.Web.BrowserLink.Loader* package</span></span> 
2. <span data-ttu-id="63ea5-112">将配置代码中的添加*Startup.cs*文件。</span><span class="sxs-lookup"><span data-stu-id="63ea5-112">Add configuration code in the *Startup.cs* file.</span></span>

### <a name="add-the-package"></a><span data-ttu-id="63ea5-113">将包添加</span><span class="sxs-lookup"><span data-stu-id="63ea5-113">Add the package</span></span>

<span data-ttu-id="63ea5-114">由于这是 Visual Studio 功能，将包添加的最简单方法是打开**程序包管理器控制台**(**视图 > 其他 Windows > 程序包管理器控制台**) 并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="63ea5-114">Since this is a Visual Studio feature, the easiest way to add the package is to open the **Package Manager Console** (**View > Other Windows > Package Manager Console**) and run the following command:</span></span>

```console
install-package Microsoft.VisualStudio.Web.BrowserLink.Loader
```

<span data-ttu-id="63ea5-115">或者，可以使用**NuGet 包管理器**。</span><span class="sxs-lookup"><span data-stu-id="63ea5-115">Alternatively, you can use **NuGet Package Manager**.</span></span>  <span data-ttu-id="63ea5-116">右键单击中的项目名称**解决方案资源管理器**，然后选择**管理 NuGet 包**。</span><span class="sxs-lookup"><span data-stu-id="63ea5-116">Right-click the project name in **Solution Explorer**, and choose **Manage NuGet Packages**.</span></span> 

![打开 NuGet 包管理器](using-browserlink/_static/open-nuget-package-manager.png)

<span data-ttu-id="63ea5-118">然后找到并安装包。</span><span class="sxs-lookup"><span data-stu-id="63ea5-118">Then find and install the package.</span></span>

![添加包使用 NuGet 包管理器](using-browserlink/_static/add-package-with-nuget-package-manager.png)

### <a name="add-configuration-code"></a><span data-ttu-id="63ea5-120">将配置代码添加</span><span class="sxs-lookup"><span data-stu-id="63ea5-120">Add configuration code</span></span>

<span data-ttu-id="63ea5-121">打开*Startup.cs*文件，然后在`Configure`方法添加以下代码：</span><span class="sxs-lookup"><span data-stu-id="63ea5-121">Open the *Startup.cs* file, and in the `Configure` method add the following code:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="63ea5-122">该代码通常位于`if`块只在开发环境，使浏览器链接，如下所示：</span><span class="sxs-lookup"><span data-stu-id="63ea5-122">Usually that code is inside an `if` block that enables Browser Link only in the Development environment, as shown here:</span></span>

<span data-ttu-id="63ea5-123">[!code-csharp[Main](./using-browserlink/sample/BrowserLinkSample/src/BrowserLinkSample/Startup.cs?highlight=1,4&range=40-44)]</span><span class="sxs-lookup"><span data-stu-id="63ea5-123">[!code-csharp[Main](./using-browserlink/sample/BrowserLinkSample/src/BrowserLinkSample/Startup.cs?highlight=1,4&range=40-44)]</span></span>

<span data-ttu-id="63ea5-124">有关详细信息，请参阅[使用多个环境](../fundamentals/environments.md)。</span><span class="sxs-lookup"><span data-stu-id="63ea5-124">For more information, see [Working with Multiple Environments](../fundamentals/environments.md).</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="63ea5-125">如何使用浏览器链接</span><span class="sxs-lookup"><span data-stu-id="63ea5-125">How to use Browser Link</span></span>

<span data-ttu-id="63ea5-126">如果你拥有打开 ASP.NET Core 项目，Visual Studio 将旁边显示浏览器链接工具栏控件**调试目标**工具栏控件：</span><span class="sxs-lookup"><span data-stu-id="63ea5-126">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![浏览器链接下拉列表菜单](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="63ea5-128">通过浏览器链接工具栏控件，你可以：</span><span class="sxs-lookup"><span data-stu-id="63ea5-128">From the Browser Link toolbar control, you can:</span></span>

- <span data-ttu-id="63ea5-129">刷新 web 应用程序在多个浏览器中的一次</span><span class="sxs-lookup"><span data-stu-id="63ea5-129">Refresh the web application in several browsers at once</span></span>
- <span data-ttu-id="63ea5-130">打开**浏览器链接仪表板**</span><span class="sxs-lookup"><span data-stu-id="63ea5-130">Open the **Browser Link Dashboard**</span></span>
- <span data-ttu-id="63ea5-131">启用或禁用**浏览器链接**</span><span class="sxs-lookup"><span data-stu-id="63ea5-131">Enable or disable **Browser Link**</span></span>
- <span data-ttu-id="63ea5-132">启用或禁用 CSS 自动同步</span><span class="sxs-lookup"><span data-stu-id="63ea5-132">Enable or disable CSS Auto-Sync</span></span>

> [!NOTE]
> <span data-ttu-id="63ea5-133">某些 Visual Studio 插件，最值得注意的是*Web 扩展包 2015年*和*Web 扩展包 2017年*，提供扩展的功能的浏览器链接，但某些其他功能不与 ASP 配合使用。NET 核心项目。</span><span class="sxs-lookup"><span data-stu-id="63ea5-133">Some Visual Studio plug-ins, most notably *Web Extension Pack 2015* and *Web Extension Pack 2017*, offer extended functionality for Browser Link, but some of the additional features don't work with ASP.NET Core projects.</span></span>

## <a name="refresh-the-web-application-in-several-browsers-at-once"></a><span data-ttu-id="63ea5-134">刷新 web 应用程序在多个浏览器中的一次</span><span class="sxs-lookup"><span data-stu-id="63ea5-134">Refresh the web application in several browsers at once</span></span>

<span data-ttu-id="63ea5-135">若要选择单个 web 浏览器以启动启动项目时，使用中的下拉列表菜单**调试目标**工具栏控件：</span><span class="sxs-lookup"><span data-stu-id="63ea5-135">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![F5 下拉列表菜单](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="63ea5-137">若要同时打开多个浏览器，选择**浏览与...**从相同的下拉列表。</span><span class="sxs-lookup"><span data-stu-id="63ea5-137">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span>  <span data-ttu-id="63ea5-138">按住 CTRL 键以选择所需的浏览器，然后单击**浏览**:</span><span class="sxs-lookup"><span data-stu-id="63ea5-138">Hold down the CTRL key to select the browsers you want, and then click **Browse**:</span></span>

![同时打开许多浏览器](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="63ea5-140">下面是示例屏幕快照显示与索引视图的 Visual Studio 打开和两个打开的浏览器：</span><span class="sxs-lookup"><span data-stu-id="63ea5-140">Here's a sample screenshot showing Visual Studio with the Index view open and two open browsers:</span></span>

![两个浏览器的示例与同步](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="63ea5-142">将鼠标悬停在该浏览器链接工具栏控件，以查看连接到的项目的浏览器：</span><span class="sxs-lookup"><span data-stu-id="63ea5-142">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![将鼠标悬停提示](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="63ea5-144">更改索引视图中，并单击浏览器链接刷新按钮后，将更新所有连接的浏览器：</span><span class="sxs-lookup"><span data-stu-id="63ea5-144">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![浏览器更改同步](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="63ea5-146">浏览器链接也适用于浏览器，从 Visual Studio 外部启动并导航到应用程序 URL。</span><span class="sxs-lookup"><span data-stu-id="63ea5-146">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the application URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="63ea5-147">浏览器链接仪表板</span><span class="sxs-lookup"><span data-stu-id="63ea5-147">The Browser Link Dashboard</span></span>

<span data-ttu-id="63ea5-148">从浏览器链接下拉菜单来管理与打开的浏览器的连接打开浏览器链接仪表板：</span><span class="sxs-lookup"><span data-stu-id="63ea5-148">Open the Browser Link Dashboard from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![仪表-板 browserslink 打开](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="63ea5-150">如果连接没有浏览器，你可以启动非调试会话单击_用浏览器查看_链接：</span><span class="sxs-lookup"><span data-stu-id="63ea5-150">If no browser is connected, you can start a non debugging session clicking the _View in Browser_ link:</span></span>

![浏览器链接仪表板-无连接](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="63ea5-152">否则，连接的浏览器将显示，替换为显示每个浏览器的页面的路径：</span><span class="sxs-lookup"><span data-stu-id="63ea5-152">Otherwise, the connected browsers are shown, with the path to the page that each browser is showing:</span></span>

![浏览器链接仪表板的两个连接](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="63ea5-154">如果您愿意，你可以单击要刷新该单个浏览器的列出的浏览器名称。</span><span class="sxs-lookup"><span data-stu-id="63ea5-154">If you like, you can click on a listed browser name to refresh that single browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="63ea5-155">启用或禁用浏览器链接</span><span class="sxs-lookup"><span data-stu-id="63ea5-155">Enable or disable Browser Link</span></span>

<span data-ttu-id="63ea5-156">当重新启用 Browser Link 禁用它之后时，你必须刷新浏览器以将它们重新连接。</span><span class="sxs-lookup"><span data-stu-id="63ea5-156">When you re-enable Browser Link after disabling it, you have to refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="63ea5-157">启用或禁用 CSS 自动同步</span><span class="sxs-lookup"><span data-stu-id="63ea5-157">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="63ea5-158">启用 CSS 自动同步后，对 CSS 文件进行任何更改时自动刷新连接的浏览器。</span><span class="sxs-lookup"><span data-stu-id="63ea5-158">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-does-it-work"></a><span data-ttu-id="63ea5-159">Application Insights 如何工作？</span><span class="sxs-lookup"><span data-stu-id="63ea5-159">How does it work?</span></span>

<span data-ttu-id="63ea5-160">浏览器链接使用 SignalR 来创建 Visual Studio 和浏览器之间的通信通道。</span><span class="sxs-lookup"><span data-stu-id="63ea5-160">Browser Link uses SignalR to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="63ea5-161">启用浏览器链接后，Visual Studio 将充当多个客户端 （浏览器） 可以连接到的 SignalR 服务器。</span><span class="sxs-lookup"><span data-stu-id="63ea5-161">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="63ea5-162">浏览器链接也在 ASP.NET 请求管道中注册的中间件组件。</span><span class="sxs-lookup"><span data-stu-id="63ea5-162">Browser Link also registers a middleware component in the ASP.NET request pipeline.</span></span> <span data-ttu-id="63ea5-163">此组件将插入特殊`<script>`到每个页请求从服务器的引用。</span><span class="sxs-lookup"><span data-stu-id="63ea5-163">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="63ea5-164">您可以通过选择查看脚本引用**查看源**在浏览器和左边缘滚动`<body>`标记内容：</span><span class="sxs-lookup"><span data-stu-id="63ea5-164">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```javascript
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="63ea5-165">不会修改你的源文件。</span><span class="sxs-lookup"><span data-stu-id="63ea5-165">Your source files are not modified.</span></span> <span data-ttu-id="63ea5-166">中间件组件动态插入脚本引用。</span><span class="sxs-lookup"><span data-stu-id="63ea5-166">The middleware component injects the script references dynamically.</span></span> 

<span data-ttu-id="63ea5-167">由于浏览器端代码是所有 JavaScript，则它将适用于 SignalR 支持，而无需任何浏览器插件的所有浏览器。</span><span class="sxs-lookup"><span data-stu-id="63ea5-167">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports, without requiring any browser plug-in.</span></span>
