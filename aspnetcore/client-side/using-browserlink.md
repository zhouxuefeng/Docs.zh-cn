---
title: "在 ASP.NET 核心中的浏览器链接"
author: ncarandini
description: "了解如何浏览器链接是链接使用一个或多个 web 浏览器在开发环境的 Visual Studio 功能。"
keywords: "ASP.NET 核心，浏览器链接，CSS 同步"
ms.author: riande
manager: wpickett
ms.date: 09/22/2017
ms.topic: article
ms.assetid: 11813d4c-3f8a-445a-b23b-e4a57d001abc
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/using-browserlink
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 95062961877b8da843ce47fb1719ee85224fa8c8
ms.sourcegitcommit: 9c27fa0f0c57ad611aa43f63afb9b9c9571d4a94
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="762a1-104">在 ASP.NET 核心中的浏览器链接</span><span class="sxs-lookup"><span data-stu-id="762a1-104">Browser Link in ASP.NET Core</span></span> 

<span data-ttu-id="762a1-105">通过[Nicolò Carandini](https://github.com/ncarandini)， [Mike Wasson](https://github.com/MikeWasson)，和[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="762a1-105">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="762a1-106">浏览器链接是创建开发环境和一个或多个 web 浏览器之间的通信通道的 Visual Studio 中的功能。</span><span class="sxs-lookup"><span data-stu-id="762a1-106">Browser Link is a feature in Visual Studio that creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="762a1-107">你可以使用浏览器链接刷新 web 应用程序在多个浏览器中的，这是适用于跨浏览器测试。</span><span class="sxs-lookup"><span data-stu-id="762a1-107">You can use Browser Link to refresh your web application in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="762a1-108">浏览器链接安装程序</span><span class="sxs-lookup"><span data-stu-id="762a1-108">Browser Link setup</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="762a1-109">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="762a1-109">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="762a1-110">ASP.NET 核心 2.x **Web 应用程序**，**空**，和**Web API**模板项目使用[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All/)元包，其中包含的包引用[Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)。</span><span class="sxs-lookup"><span data-stu-id="762a1-110">The ASP.NET Core 2.x **Web Application**, **Empty**, and **Web API** template projects use the [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All/) meta-package, which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="762a1-111">因此，使用`Microsoft.AspNetCore.All`元包无需任何进一步的操作，以使浏览器链接可供使用。</span><span class="sxs-lookup"><span data-stu-id="762a1-111">Therefore, using the `Microsoft.AspNetCore.All` meta-package requires no further action to make Browser Link available for use.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="762a1-112">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="762a1-112">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="762a1-113">ASP.NET 核心 1.x **Web 应用程序**项目模板具有的包引用[Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)包。</span><span class="sxs-lookup"><span data-stu-id="762a1-113">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="762a1-114">**空**或**Web API**模板项目要求你添加到的包引用`Microsoft.VisualStudio.Web.BrowserLink`。</span><span class="sxs-lookup"><span data-stu-id="762a1-114">The **Empty** or **Web API** template projects require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

<span data-ttu-id="762a1-115">由于这是一种 Visual Studio 功能，最简单的方法添加到包**空**或**Web API**模板项目是打开**程序包管理器控制台**(**视图** > **其他窗口** > **程序包管理器控制台**) 并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="762a1-115">Since this is a Visual Studio feature, the easiest way to add the package to an **Empty** or **Web API** template project is to open the **Package Manager Console** (**View** > **Other Windows** > **Package Manager Console**) and run the following command:</span></span>

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

<span data-ttu-id="762a1-116">或者，可以使用**NuGet 包管理器**。</span><span class="sxs-lookup"><span data-stu-id="762a1-116">Alternatively, you can use **NuGet Package Manager**.</span></span> <span data-ttu-id="762a1-117">右键单击中的项目名称**解决方案资源管理器**选择**管理 NuGet 包**:</span><span class="sxs-lookup"><span data-stu-id="762a1-117">Right-click the project name in **Solution Explorer** and choose **Manage NuGet Packages**:</span></span>

![打开 NuGet 包管理器](using-browserlink/_static/open-nuget-package-manager.png)

<span data-ttu-id="762a1-119">查找和安装包：</span><span class="sxs-lookup"><span data-stu-id="762a1-119">Find and install the package:</span></span>

![添加包使用 NuGet 包管理器](using-browserlink/_static/add-package-with-nuget-package-manager.png)

---

### <a name="configuration"></a><span data-ttu-id="762a1-121">配置</span><span class="sxs-lookup"><span data-stu-id="762a1-121">Configuration</span></span>

<span data-ttu-id="762a1-122">在`Configure`方法*Startup.cs*文件：</span><span class="sxs-lookup"><span data-stu-id="762a1-122">In the `Configure` method of the *Startup.cs* file:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="762a1-123">代码通常位于`if`块只在开发环境中，启用浏览器链接，如下所示：</span><span class="sxs-lookup"><span data-stu-id="762a1-123">Usually the code is inside an `if` block that only enables Browser Link in the Development environment, as shown here:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="762a1-124">有关详细信息，请参阅[使用多个环境](xref:fundamentals/environments)。</span><span class="sxs-lookup"><span data-stu-id="762a1-124">For more information, see [Working with Multiple Environments](xref:fundamentals/environments).</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="762a1-125">如何使用浏览器链接</span><span class="sxs-lookup"><span data-stu-id="762a1-125">How to use Browser Link</span></span>

<span data-ttu-id="762a1-126">如果你拥有打开 ASP.NET Core 项目，Visual Studio 将旁边显示浏览器链接工具栏控件**调试目标**工具栏控件：</span><span class="sxs-lookup"><span data-stu-id="762a1-126">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![浏览器链接下拉列表菜单](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="762a1-128">通过浏览器链接工具栏控件，你可以：</span><span class="sxs-lookup"><span data-stu-id="762a1-128">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="762a1-129">刷新 web 应用程序在多个浏览器中的一次。</span><span class="sxs-lookup"><span data-stu-id="762a1-129">Refresh the web application in several browsers at once.</span></span>
* <span data-ttu-id="762a1-130">打开**浏览器链接仪表板**。</span><span class="sxs-lookup"><span data-stu-id="762a1-130">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="762a1-131">启用或禁用**Browser Link**。</span><span class="sxs-lookup"><span data-stu-id="762a1-131">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="762a1-132">注意： 默认情况下，Visual Studio 2017 (15.3) 中禁用浏览器链接。</span><span class="sxs-lookup"><span data-stu-id="762a1-132">Note: Browser Link is disabled by default in Visual Studio 2017 (15.3).</span></span>
* <span data-ttu-id="762a1-133">启用或禁用[CSS 自动同步](#enable-or-disable-css-auto-sync)。</span><span class="sxs-lookup"><span data-stu-id="762a1-133">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

> [!NOTE]
> <span data-ttu-id="762a1-134">某些 Visual Studio 插件，最值得注意的是*Web 扩展包 2015年*和*Web 扩展包 2017年*，提供扩展的功能的浏览器链接，但某些其他功能不与 ASP 配合使用。NET 核心项目。</span><span class="sxs-lookup"><span data-stu-id="762a1-134">Some Visual Studio plug-ins, most notably *Web Extension Pack 2015* and *Web Extension Pack 2017*, offer extended functionality for Browser Link, but some of the additional features don't work with ASP.NET Core projects.</span></span>

## <a name="refresh-the-web-application-in-several-browsers-at-once"></a><span data-ttu-id="762a1-135">刷新 web 应用程序在多个浏览器中的一次</span><span class="sxs-lookup"><span data-stu-id="762a1-135">Refresh the web application in several browsers at once</span></span>

<span data-ttu-id="762a1-136">若要选择单个 web 浏览器以启动启动项目时，使用中的下拉列表菜单**调试目标**工具栏控件：</span><span class="sxs-lookup"><span data-stu-id="762a1-136">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![F5 下拉列表菜单](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="762a1-138">若要同时打开多个浏览器，选择**浏览与...**从相同的下拉列表。</span><span class="sxs-lookup"><span data-stu-id="762a1-138">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="762a1-139">按住 CTRL 键以选择所需的浏览器，然后单击**浏览**:</span><span class="sxs-lookup"><span data-stu-id="762a1-139">Hold down the CTRL key to select the browsers you want, and then click **Browse**:</span></span>

![同时打开许多浏览器](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="762a1-141">下面是 Visual Studio 显示与索引视图打开的屏幕快照以及两个打开的浏览器：</span><span class="sxs-lookup"><span data-stu-id="762a1-141">Here's a screenshot showing Visual Studio with the Index view open and two open browsers:</span></span>

![两个浏览器的示例与同步](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="762a1-143">将鼠标悬停在该浏览器链接工具栏控件，以查看连接到的项目的浏览器：</span><span class="sxs-lookup"><span data-stu-id="762a1-143">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![将鼠标悬停提示](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="762a1-145">更改索引视图中，并单击浏览器链接刷新按钮后，将更新所有连接的浏览器：</span><span class="sxs-lookup"><span data-stu-id="762a1-145">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![浏览器更改同步](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="762a1-147">浏览器链接也适用于浏览器，从 Visual Studio 外部启动并导航到应用程序 URL。</span><span class="sxs-lookup"><span data-stu-id="762a1-147">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the application URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="762a1-148">浏览器链接仪表板</span><span class="sxs-lookup"><span data-stu-id="762a1-148">The Browser Link Dashboard</span></span>

<span data-ttu-id="762a1-149">从浏览器链接下拉菜单来管理与打开的浏览器的连接打开浏览器链接仪表板：</span><span class="sxs-lookup"><span data-stu-id="762a1-149">Open the Browser Link Dashboard from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![仪表-板 browserslink 打开](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="762a1-151">如果连接没有浏览器，您可以通过选择启动非调试会话*用浏览器查看*链接：</span><span class="sxs-lookup"><span data-stu-id="762a1-151">If no browser is connected, you can start a non-debugging session by selecting the *View in Browser* link:</span></span>

![浏览器链接仪表板-无连接](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="762a1-153">否则，连接的浏览器在显示时带有显示每个浏览器的页面的路径：</span><span class="sxs-lookup"><span data-stu-id="762a1-153">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![浏览器链接仪表板的两个连接](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="762a1-155">如果您愿意，你可以单击要刷新该单个浏览器的列出的浏览器名称。</span><span class="sxs-lookup"><span data-stu-id="762a1-155">If you like, you can click on a listed browser name to refresh that single browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="762a1-156">启用或禁用浏览器链接</span><span class="sxs-lookup"><span data-stu-id="762a1-156">Enable or disable Browser Link</span></span>

<span data-ttu-id="762a1-157">当重新启用 Browser Link 禁用它之后时，您必须刷新浏览器以将它们重新连接。</span><span class="sxs-lookup"><span data-stu-id="762a1-157">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="762a1-158">启用或禁用 CSS 自动同步</span><span class="sxs-lookup"><span data-stu-id="762a1-158">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="762a1-159">启用 CSS 自动同步后，对 CSS 文件进行任何更改时自动刷新连接的浏览器。</span><span class="sxs-lookup"><span data-stu-id="762a1-159">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-does-it-work"></a><span data-ttu-id="762a1-160">Application Insights 如何工作？</span><span class="sxs-lookup"><span data-stu-id="762a1-160">How does it work?</span></span>

<span data-ttu-id="762a1-161">浏览器链接使用 SignalR 来创建 Visual Studio 和浏览器之间的通信通道。</span><span class="sxs-lookup"><span data-stu-id="762a1-161">Browser Link uses SignalR to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="762a1-162">启用浏览器链接后，Visual Studio 将充当多个客户端 （浏览器） 可以连接到的 SignalR 服务器。</span><span class="sxs-lookup"><span data-stu-id="762a1-162">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="762a1-163">浏览器链接也在 ASP.NET 请求管道中注册的中间件组件。</span><span class="sxs-lookup"><span data-stu-id="762a1-163">Browser Link also registers a middleware component in the ASP.NET request pipeline.</span></span> <span data-ttu-id="762a1-164">此组件将插入特殊`<script>`到每个页请求从服务器的引用。</span><span class="sxs-lookup"><span data-stu-id="762a1-164">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="762a1-165">您可以通过选择查看脚本引用**查看源**在浏览器和左边缘滚动`<body>`标记内容：</span><span class="sxs-lookup"><span data-stu-id="762a1-165">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```javascript
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="762a1-166">不修改你的源文件。</span><span class="sxs-lookup"><span data-stu-id="762a1-166">Your source files aren't modified.</span></span> <span data-ttu-id="762a1-167">中间件组件动态插入脚本引用。</span><span class="sxs-lookup"><span data-stu-id="762a1-167">The middleware component injects the script references dynamically.</span></span> 

<span data-ttu-id="762a1-168">由于浏览器端代码是所有 JavaScript，则它将适用于 SignalR 支持而无需浏览器插件的所有浏览器。</span><span class="sxs-lookup"><span data-stu-id="762a1-168">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
