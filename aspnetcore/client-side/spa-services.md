---
title: "用于创建单页面应用程序使用 JavaScriptServices"
author: scottaddie
description: "了解有关使用 JavaScriptServices 创建由 ASP.NET Core 单页面应用程序 (SPA) 的好处。"
keywords: "ASP.NET 核心，角度、 SPA、 JavaScriptServices、 SpaServices"
ms.author: scaddie
manager: wpickett
ms.date: 08/02/2017
ms.topic: article
ms.assetid: 4b30576b-2718-4c39-9253-a59966747893
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/spa-services
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f3c38a1730e43586f37cd773bb8daa418736952f
ms.sourcegitcommit: b3d46df910fb679edb8dd47234db6b4da604eedb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2017
---
# <a name="using-javascriptservices-for-creating-single-page-applications-with-aspnet-core"></a><span data-ttu-id="8f2a0-104">用于创建具有 ASP.NET Core 的单页面应用程序使用 JavaScriptServices</span><span class="sxs-lookup"><span data-stu-id="8f2a0-104">Using JavaScriptServices for Creating Single Page Applications with ASP.NET Core</span></span>

<span data-ttu-id="8f2a0-105">通过[Scott Addie](https://github.com/scottaddie)和[Fiyaz Hasan](http://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="8f2a0-105">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](http://fiyazhasan.me/)</span></span>

<span data-ttu-id="8f2a0-106">单页面应用程序 (SPA) 是一种流行的 web 应用程序，因为其固有的丰富用户体验。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-106">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="8f2a0-107">将客户端 SPA 框架或库，如集成[角](https://angular.io/)或[做出反应](https://facebook.github.io/react/)，与服务器端框架，如 ASP.NET Core 可能很困难。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-107">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks like ASP.NET Core can be difficult.</span></span> <span data-ttu-id="8f2a0-108">[JavaScriptServices](https://github.com/aspnet/JavaScriptServices)开发的目的是减少在集成过程中的问题。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-108">[JavaScriptServices](https://github.com/aspnet/JavaScriptServices) was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="8f2a0-109">它可让在不同的客户端和服务器技术堆栈之间的无缝操作。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-109">It enables seamless operation between the different client and server technology stacks.</span></span>

<span data-ttu-id="8f2a0-110">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/spa-services/sample)（[如何下载](xref:tutorials/index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="8f2a0-110">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/spa-services/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

<a name="what-is-js-services"></a>

## <a name="what-is-javascriptservices"></a><span data-ttu-id="8f2a0-111">什么是 JavaScriptServices？</span><span class="sxs-lookup"><span data-stu-id="8f2a0-111">What is JavaScriptServices?</span></span>

<span data-ttu-id="8f2a0-112">JavaScriptServices 是 ASP.NET Core 的客户端技术的集合。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-112">JavaScriptServices is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="8f2a0-113">其目标是将 ASP.NET Core 定位为开发人员的首选服务器端平台，用于构建 Spa。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-113">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="8f2a0-114">JavaScriptServices 包含三个不同的 NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-114">JavaScriptServices consists of three distinct NuGet packages:</span></span>
* <span data-ttu-id="8f2a0-115">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="8f2a0-115">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="8f2a0-116">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="8f2a0-116">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>
* <span data-ttu-id="8f2a0-117">[Microsoft.AspNetCore.SpaTemplates](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaTemplates/) (SpaTemplates)</span><span class="sxs-lookup"><span data-stu-id="8f2a0-117">[Microsoft.AspNetCore.SpaTemplates](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaTemplates/) (SpaTemplates)</span></span>

<span data-ttu-id="8f2a0-118">这些包的作用是如果你：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-118">These packages are useful if you:</span></span>
* <span data-ttu-id="8f2a0-119">在服务器上运行 JavaScript</span><span class="sxs-lookup"><span data-stu-id="8f2a0-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="8f2a0-120">使用 SPA 框架或库</span><span class="sxs-lookup"><span data-stu-id="8f2a0-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="8f2a0-121">生成客户端 Webpack 资产</span><span class="sxs-lookup"><span data-stu-id="8f2a0-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="8f2a0-122">在本文中将焦点大部分位于使用 SpaServices 包。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

<a name="what-is-spa-services"></a>

## <a name="what-is-spaservices"></a><span data-ttu-id="8f2a0-123">什么是 SpaServices？</span><span class="sxs-lookup"><span data-stu-id="8f2a0-123">What is SpaServices?</span></span>

<span data-ttu-id="8f2a0-124">用于将 ASP.NET Core 定位为开发人员的首选服务器端平台，用于构建 Spa，SpaServices 而创建。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="8f2a0-125">SpaServices 不需要开发 Spa 与 ASP.NET 核心，并且它不会将您限制在一个特定的客户端框架。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-125">SpaServices is not required to develop SPAs with ASP.NET Core, and it doesn't lock you into a particular client framework.</span></span>

<span data-ttu-id="8f2a0-126">SpaServices 提供有用的基础结构，如所示：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-126">SpaServices provides useful infrastructure such as:</span></span>
* [<span data-ttu-id="8f2a0-127">服务器端预呈现</span><span class="sxs-lookup"><span data-stu-id="8f2a0-127">Server-side prerendering</span></span>](#server-prerendering)
* [<span data-ttu-id="8f2a0-128">Webpack 开发人员中间件</span><span class="sxs-lookup"><span data-stu-id="8f2a0-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="8f2a0-129">热模块更换</span><span class="sxs-lookup"><span data-stu-id="8f2a0-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="8f2a0-130">路由的帮助器</span><span class="sxs-lookup"><span data-stu-id="8f2a0-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="8f2a0-131">总体来说，这些基础结构组件来提高开发工作流和运行时体验。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="8f2a0-132">可以单独采用组件。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-132">The components can be adopted individually.</span></span>

<a name="spa-services-prereqs"></a>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="8f2a0-133">使用 SpaServices 的先决条件</span><span class="sxs-lookup"><span data-stu-id="8f2a0-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="8f2a0-134">若要使用 SpaServices，安装以下项：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-134">To work with SpaServices, install the following:</span></span>
* <span data-ttu-id="8f2a0-135">[Node.js](https://nodejs.org/) （6 或更高版本） 与 npm</span><span class="sxs-lookup"><span data-stu-id="8f2a0-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>
    * <span data-ttu-id="8f2a0-136">若要验证这些组件安装，并找不到，运行以下命令从命令行：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

<span data-ttu-id="8f2a0-137">注意： 如果你要部署到 Azure 网站，你不需要此处执行任何操作&mdash;Node.js 已安装并且可用的服务器环境中。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-137">Note: If you're deploying to an Azure web site, you don't need to do anything here &mdash; Node.js is installed and available in the server environments.</span></span>

* <span data-ttu-id="8f2a0-138">[.NET 核心 SDK](https://www.microsoft.com/net/download/core) 1.0 （或更高版本）</span><span class="sxs-lookup"><span data-stu-id="8f2a0-138">[.NET Core SDK](https://www.microsoft.com/net/download/core) 1.0 (or later)</span></span>
    * <span data-ttu-id="8f2a0-139">如果你在 Windows 上，这可以安装通过选择 Visual Studio 2017 **.NET 核心跨平台开发**工作负荷。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-139">If you're on Windows, this can be installed by selecting Visual Studio 2017's **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="8f2a0-140">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet 包</span><span class="sxs-lookup"><span data-stu-id="8f2a0-140">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

<a name="server-prerendering"></a>

## <a name="server-side-prerendering"></a><span data-ttu-id="8f2a0-141">服务器端预呈现</span><span class="sxs-lookup"><span data-stu-id="8f2a0-141">Server-side prerendering</span></span>

<span data-ttu-id="8f2a0-142">通用的 (也称为 isomorphic) 应用程序是能够在服务器和客户端上同时运行的 JavaScript 应用程序。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-142">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="8f2a0-143">角、 响应和其他常用框架提供一个通用平台此应用程序的开发风格。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-143">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="8f2a0-144">目的是第一次呈现 Node.js，通过在服务器上的 framework 组件，然后将进一步委托到客户端执行。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-144">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="8f2a0-145">ASP.NET 核心[标记帮助程序](xref:mvc/views/tag-helpers/intro)由 SpaServices 简化通过调用服务器上的 JavaScript 函数的服务器端预呈现的实现。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-145">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="8f2a0-146">先决条件</span><span class="sxs-lookup"><span data-stu-id="8f2a0-146">Prerequisites</span></span>

<span data-ttu-id="8f2a0-147">安装以下组件：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-147">Install the following:</span></span>
* <span data-ttu-id="8f2a0-148">[aspnet 预呈现](https://www.npmjs.com/package/aspnet-prerendering)npm 包：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-148">[aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

    ```console
    npm i -S aspnet-prerendering
    ```

### <a name="configuration"></a><span data-ttu-id="8f2a0-149">配置</span><span class="sxs-lookup"><span data-stu-id="8f2a0-149">Configuration</span></span>

<span data-ttu-id="8f2a0-150">标记帮助程序都在项目的命名空间注册通过可发现*_ViewImports.cshtml*文件：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-150">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-csharp[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="8f2a0-151">这些标记帮助程序抽象化通过利用在 Razor 视图类似于 HTML 的语法直接与低级别 Api 进行通信的复杂性：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-151">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-html[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="the-asp-prerender-module-tag-helper"></a><span data-ttu-id="8f2a0-152">`asp-prerender-module`标记帮助器</span><span class="sxs-lookup"><span data-stu-id="8f2a0-152">The `asp-prerender-module` Tag Helper</span></span>

<span data-ttu-id="8f2a0-153">`asp-prerender-module`标记帮助器，使用在前面的代码示例中，执行*ClientApp/dist/main-server.js*通过 Node.js 服务器上。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-153">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="8f2a0-154">为清晰起见， *main server.js*文件是 TypeScript JavaScript transpilation 任务中的项目[Webpack](http://webpack.github.io/)生成过程。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-154">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](http://webpack.github.io/) build process.</span></span> <span data-ttu-id="8f2a0-155">Webpack 定义的入口点别名`main-server`; 并且，在开始此别名的依赖项关系图的遍历*ClientApp/启动 server.ts*文件：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-155">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="8f2a0-156">在以下的角度示例中， *ClientApp/启动 server.ts*文件利用`createServerRenderer`函数和`RenderResult`类型`aspnet-prerendering`npm 包以配置通过 Node.js 服务器呈现。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-156">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="8f2a0-157">发送到服务器端呈现传递给解析函数调用，从而将包装在强类型化的 JavaScript 中的 HTML 标记`Promise`对象。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-157">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="8f2a0-158">`Promise`对象的基数是它以异步方式提供到 DOM 的占位符元素中注入的页的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-158">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="the-asp-prerender-data-tag-helper"></a><span data-ttu-id="8f2a0-159">`asp-prerender-data`标记帮助器</span><span class="sxs-lookup"><span data-stu-id="8f2a0-159">The `asp-prerender-data` Tag Helper</span></span>

<span data-ttu-id="8f2a0-160">结合了`asp-prerender-module`标记帮助器，`asp-prerender-data`标记帮助器可以用于将上下文信息从 Razor 视图传递到服务器端 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-160">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="8f2a0-161">例如，以下标记将传递到的用户数据`main-server`模块：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-161">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-html[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="8f2a0-162">接收`UserName`自变量使用内置的 JSON 序列化程序序列化和存储在`params.data`对象。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-162">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="8f2a0-163">在以下的角度示例中，数据用于构造中的个性化的问候语`h1`元素：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-163">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="8f2a0-164">注意： 在标记帮助程序中传递的属性名称使用表示**PascalCase**表示法。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-164">Note: Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="8f2a0-165">相比之下，到 JavaScript，相同的属性名称由**驼峰匹配**。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-165">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="8f2a0-166">默认 JSON 序列化配置负责这种差异。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-166">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="8f2a0-167">若要展开在前面的代码示例时，数据可以从服务器由传递给视图 hydrating`globals`属性提供给`resolve`函数：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-167">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="8f2a0-168">`postList`数组内部定义`globals`对象附加到浏览器的全局`window`对象。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-168">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="8f2a0-169">为全局作用域此变量提升消除重复的工作量，，尤其当它与加载一次在服务器上，再次在客户端上相同的数据。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-169">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![附加到窗口对象的全局 postList 变量](spa-services/_static/global_variable.png)

<a name="webpack-dev-middleware"></a>

## <a name="webpack-dev-middleware"></a><span data-ttu-id="8f2a0-171">Webpack 开发人员中间件</span><span class="sxs-lookup"><span data-stu-id="8f2a0-171">Webpack Dev Middleware</span></span>

<span data-ttu-id="8f2a0-172">[Webpack 开发人员中间件](https://webpack.github.io/docs/webpack-dev-middleware.html)引入了 Webpack 按需生成资源的凭此简化的开发工作流。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-172">[Webpack Dev Middleware](https://webpack.github.io/docs/webpack-dev-middleware.html) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="8f2a0-173">该中间件自动编译，并在浏览器中重新加载页面时提供客户端资源。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-173">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="8f2a0-174">一种替代方法是手动将 Webpack 通过项目的 npm 生成脚本调用，第三方依赖关系或自定义代码更改时。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-174">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="8f2a0-175">Npm 中生成脚本*package.json*文件显示在下面的示例：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-175">An npm build script in the *package.json* file is shown in the following example:</span></span>

[!code-json[Main](../client-side/spa-services/sample/SpaServicesSampleApp/package.json?range=5)]

### <a name="prerequisites"></a><span data-ttu-id="8f2a0-176">先决条件</span><span class="sxs-lookup"><span data-stu-id="8f2a0-176">Prerequisites</span></span>

<span data-ttu-id="8f2a0-177">安装以下组件：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-177">Install the following:</span></span>
* <span data-ttu-id="8f2a0-178">[aspnet webpack](https://www.npmjs.com/package/aspnet-webpack) npm 包：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-178">[aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

    ```console
    npm i -D aspnet-webpack
    ```

### <a name="configuration"></a><span data-ttu-id="8f2a0-179">配置</span><span class="sxs-lookup"><span data-stu-id="8f2a0-179">Configuration</span></span>

<span data-ttu-id="8f2a0-180">Webpack 开发人员中间件注册到中的以下代码通过 HTTP 请求管道*Startup.cs*文件的`Configure`方法：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-180">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=webpack-middleware-registration&highlight=4)]

<span data-ttu-id="8f2a0-181">`UseWebpackDevMiddleware`前，必须调用扩展方法[注册静态文件承载](xref:fundamentals/static-files)通过`UseStaticFiles`扩展方法。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-181">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="8f2a0-182">出于安全原因，注册该中间件，仅当应用程序在开发模式下运行时。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-182">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="8f2a0-183">*Webpack.config.js*文件的`output.publicPath`属性告知要监视的中间件`dist`更改的文件夹：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-183">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

<a name="hot-module-replacement"></a>

## <a name="hot-module-replacement"></a><span data-ttu-id="8f2a0-184">热模块更换</span><span class="sxs-lookup"><span data-stu-id="8f2a0-184">Hot Module Replacement</span></span>

<span data-ttu-id="8f2a0-185">思考的 Webpack 的[热模块更换](https://webpack.github.io/docs/hot-module-replacement-with-webpack.html)(HMR) 功能作为演变而来的[Webpack 开发人员中间件](#webpack-dev-middleware)。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-185">Think of Webpack's [Hot Module Replacement](https://webpack.github.io/docs/hot-module-replacement-with-webpack.html) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="8f2a0-186">HMR 引入了完全相同的好处，但它进一步，从而简化了开发工作流自动编译所做的更改后更新页面内容。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-186">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="8f2a0-187">不要混淆这与刷新浏览器中，这会干扰的当前内存中状态和 SPA 的调试会话。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-187">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="8f2a0-188">没有 Webpack 开发人员中间件服务与浏览器中，这意味着更改推送到浏览器之间的实时链接。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-188">There is a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="8f2a0-189">先决条件</span><span class="sxs-lookup"><span data-stu-id="8f2a0-189">Prerequisites</span></span>

<span data-ttu-id="8f2a0-190">安装以下组件：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-190">Install the following:</span></span>
* <span data-ttu-id="8f2a0-191">[webpack 热 middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm 包：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-191">[webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

    ```console
    npm i -D webpack-hot-middleware
    ```

### <a name="configuration"></a><span data-ttu-id="8f2a0-192">配置</span><span class="sxs-lookup"><span data-stu-id="8f2a0-192">Configuration</span></span>

<span data-ttu-id="8f2a0-193">HMR 组件必须注册到 MVC 的 HTTP 请求管道中`Configure`方法：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-193">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="8f2a0-194">为已对如此[Webpack 开发人员中间件](#webpack-dev-middleware)、`UseWebpackDevMiddleware`前，必须调用扩展方法`UseStaticFiles`扩展方法。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-194">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="8f2a0-195">出于安全原因，注册该中间件，仅当应用程序在开发模式下运行时。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-195">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="8f2a0-196">*Webpack.config.js*文件必须定义`plugins`，即使它保留为空数组：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-196">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="8f2a0-197">在加载浏览器中的应用程序之后, 的开发人员工具的控制台选项卡提供 HMR 激活的确认：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-197">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![热模块更换连接的消息](spa-services/_static/hmr_connected.png)

<a name="routing-helpers"></a>

## <a name="routing-helpers"></a><span data-ttu-id="8f2a0-199">路由的帮助器</span><span class="sxs-lookup"><span data-stu-id="8f2a0-199">Routing helpers</span></span>

<span data-ttu-id="8f2a0-200">在大多数基于 ASP.NET Core 的 Spa，您需要客户端路由除了服务器端路由。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-200">In most ASP.NET Core-based SPAs, you'll want client-side routing in addition to server-side routing.</span></span> <span data-ttu-id="8f2a0-201">SPA 和 MVC 路由系统可以独立处理不受干扰。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-201">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="8f2a0-202">不存在，但是，是否会造成问题的一个边缘情况： 标识 404 HTTP 响应。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-202">There is, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="8f2a0-203">请考虑在该方案中的无扩展名路由`/some/page`使用。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-203">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="8f2a0-204">假定该请求不模式匹配的服务器端路由，但其模式匹配的客户端路由。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-204">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="8f2a0-205">现在请考虑对的传入请求`/images/user-512.png`，它通常需要查找服务器上的图像文件。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-205">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="8f2a0-206">如果该请求的资源路径不匹配任何服务器端路由或静态文件，它不太客户端应用程序将处理它，你通常想要返回 HTTP 状态代码为 404。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-206">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it — you generally want to return a 404 HTTP status code.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="8f2a0-207">先决条件</span><span class="sxs-lookup"><span data-stu-id="8f2a0-207">Prerequisites</span></span>

<span data-ttu-id="8f2a0-208">安装以下组件：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-208">Install the following:</span></span>
* <span data-ttu-id="8f2a0-209">客户端路由 npm 包。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-209">The client-side routing npm package.</span></span> <span data-ttu-id="8f2a0-210">使用角作为示例：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-210">Using Angular as an example:</span></span>

    ```console
    npm i -S @angular/router
    ```

### <a name="configuration"></a><span data-ttu-id="8f2a0-211">配置</span><span class="sxs-lookup"><span data-stu-id="8f2a0-211">Configuration</span></span>

<span data-ttu-id="8f2a0-212">名为的扩展方法`MapSpaFallbackRoute`中使用`Configure`方法：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-212">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=mvc-routing-table&highlight=7-9)]

<span data-ttu-id="8f2a0-213">提示： 路由中配置它们的顺序进行计算。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-213">Tip: Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="8f2a0-214">因此，`default`模式匹配的第一次使用在前面的代码示例中的路由。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-214">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

<a name="new-project-creation"></a>

## <a name="creating-a-new-project"></a><span data-ttu-id="8f2a0-215">创建新项目</span><span class="sxs-lookup"><span data-stu-id="8f2a0-215">Creating a new project</span></span>

<span data-ttu-id="8f2a0-216">JavaScriptServices 提供了预配置的应用程序模板。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-216">JavaScriptServices provides pre-configured application templates.</span></span> <span data-ttu-id="8f2a0-217">SpaServices 中这些模板，与不同的框架和库如角、 Aurelia、 Knockout、 响应，和 Vue 结合使用。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-217">SpaServices is used in these templates, in conjunction with different frameworks and libraries such as Angular, Aurelia, Knockout, React, and Vue.</span></span>

<span data-ttu-id="8f2a0-218">可以通过.NET 核心 CLI 安装这些模板，通过运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-218">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```console
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="8f2a0-219">显示可用的 SPA 模板的列表：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-219">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="8f2a0-220">模板</span><span class="sxs-lookup"><span data-stu-id="8f2a0-220">Templates</span></span>                                 | <span data-ttu-id="8f2a0-221">短名称</span><span class="sxs-lookup"><span data-stu-id="8f2a0-221">Short Name</span></span> | <span data-ttu-id="8f2a0-222">语言</span><span class="sxs-lookup"><span data-stu-id="8f2a0-222">Language</span></span> | <span data-ttu-id="8f2a0-223">Tags</span><span class="sxs-lookup"><span data-stu-id="8f2a0-223">Tags</span></span>        |
|:------------------------------------------|:-----------|:---------|:------------|
| <span data-ttu-id="8f2a0-224">带有角度的 MVC ASP.NET 核心</span><span class="sxs-lookup"><span data-stu-id="8f2a0-224">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="8f2a0-225">angular</span><span class="sxs-lookup"><span data-stu-id="8f2a0-225">angular</span></span>    | <span data-ttu-id="8f2a0-226">[C#]</span><span class="sxs-lookup"><span data-stu-id="8f2a0-226">[C#]</span></span>     | <span data-ttu-id="8f2a0-227">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="8f2a0-227">Web/MVC/SPA</span></span> |
| <span data-ttu-id="8f2a0-228">带有 Aurelia 的 MVC ASP.NET 核心</span><span class="sxs-lookup"><span data-stu-id="8f2a0-228">MVC ASP.NET Core with Aurelia</span></span>             | <span data-ttu-id="8f2a0-229">aurelia</span><span class="sxs-lookup"><span data-stu-id="8f2a0-229">aurelia</span></span>    | <span data-ttu-id="8f2a0-230">[C#]</span><span class="sxs-lookup"><span data-stu-id="8f2a0-230">[C#]</span></span>     | <span data-ttu-id="8f2a0-231">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="8f2a0-231">Web/MVC/SPA</span></span> |
| <span data-ttu-id="8f2a0-232">带有 Knockout.js 的 MVC ASP.NET 核心</span><span class="sxs-lookup"><span data-stu-id="8f2a0-232">MVC ASP.NET Core with Knockout.js</span></span>         | <span data-ttu-id="8f2a0-233">knockout</span><span class="sxs-lookup"><span data-stu-id="8f2a0-233">knockout</span></span>   | <span data-ttu-id="8f2a0-234">[C#]</span><span class="sxs-lookup"><span data-stu-id="8f2a0-234">[C#]</span></span>     | <span data-ttu-id="8f2a0-235">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="8f2a0-235">Web/MVC/SPA</span></span> |
| <span data-ttu-id="8f2a0-236">带有 React.js 的 MVC ASP.NET 核心</span><span class="sxs-lookup"><span data-stu-id="8f2a0-236">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="8f2a0-237">react</span><span class="sxs-lookup"><span data-stu-id="8f2a0-237">react</span></span>      | <span data-ttu-id="8f2a0-238">[C#]</span><span class="sxs-lookup"><span data-stu-id="8f2a0-238">[C#]</span></span>     | <span data-ttu-id="8f2a0-239">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="8f2a0-239">Web/MVC/SPA</span></span> |
| <span data-ttu-id="8f2a0-240">MVC ASP.NET Core React.js 和回顾</span><span class="sxs-lookup"><span data-stu-id="8f2a0-240">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="8f2a0-241">reactredux</span><span class="sxs-lookup"><span data-stu-id="8f2a0-241">reactredux</span></span> | <span data-ttu-id="8f2a0-242">[C#]</span><span class="sxs-lookup"><span data-stu-id="8f2a0-242">[C#]</span></span>     | <span data-ttu-id="8f2a0-243">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="8f2a0-243">Web/MVC/SPA</span></span> |
| <span data-ttu-id="8f2a0-244">带有 Vue.js 的 MVC ASP.NET 核心</span><span class="sxs-lookup"><span data-stu-id="8f2a0-244">MVC ASP.NET Core with Vue.js</span></span>              | <span data-ttu-id="8f2a0-245">vue</span><span class="sxs-lookup"><span data-stu-id="8f2a0-245">vue</span></span>        | <span data-ttu-id="8f2a0-246">[C#]</span><span class="sxs-lookup"><span data-stu-id="8f2a0-246">[C#]</span></span>     | <span data-ttu-id="8f2a0-247">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="8f2a0-247">Web/MVC/SPA</span></span> | 

<span data-ttu-id="8f2a0-248">若要创建新项目使用 SPA 模板之一时，包含**短名称**中的模板的`dotnet new`命令。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-248">To create a new project using one of the SPA templates, include the **Short Name** of the template in the `dotnet new` command.</span></span> <span data-ttu-id="8f2a0-249">以下命令将创建与 ASP.NET 核心 MVC 配置为在服务器端角度的应用程序：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-249">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```console
dotnet new angular
```

<a name="runtime-config-mode"></a>

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="8f2a0-250">设置运行时配置模式</span><span class="sxs-lookup"><span data-stu-id="8f2a0-250">Set the runtime configuration mode</span></span>

<span data-ttu-id="8f2a0-251">存在两个主运行时配置模式：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-251">Two primary runtime configuration modes exist:</span></span>
* <span data-ttu-id="8f2a0-252">**开发**:</span><span class="sxs-lookup"><span data-stu-id="8f2a0-252">**Development**:</span></span>
    * <span data-ttu-id="8f2a0-253">包括源映射，以便于调试。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-253">Includes source maps to ease debugging.</span></span>
    * <span data-ttu-id="8f2a0-254">不优化性能的客户端代码。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-254">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="8f2a0-255">**生产**:</span><span class="sxs-lookup"><span data-stu-id="8f2a0-255">**Production**:</span></span>
    * <span data-ttu-id="8f2a0-256">排除源映射。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-256">Excludes source maps.</span></span>
    * <span data-ttu-id="8f2a0-257">优化通过绑定和缩减的客户端代码。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-257">Optimizes the client-side code via bundling & minification.</span></span>

<span data-ttu-id="8f2a0-258">ASP.NET 核心使用名为的环境变量`ASPNETCORE_ENVIRONMENT`来存储配置模式。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-258">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="8f2a0-259">请参阅**[将环境设置](xref:fundamentals/environments#setting-the-environment)**有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-259">See **[Setting the environment](xref:fundamentals/environments#setting-the-environment)** for more information.</span></span>

### <a name="running-with-net-core-cli"></a><span data-ttu-id="8f2a0-260">使用.NET Core CLI 运行</span><span class="sxs-lookup"><span data-stu-id="8f2a0-260">Running with .NET Core CLI</span></span>

<span data-ttu-id="8f2a0-261">在项目根目录中运行以下命令，以还原所需的 NuGet 和 npm 包：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-261">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```console
dotnet restore && npm i
```

<span data-ttu-id="8f2a0-262">生成并运行应用程序：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-262">Build and run the application:</span></span>

```console
dotnet run
```

<span data-ttu-id="8f2a0-263">在根据本地主机上的应用程序启动[运行时配置模式下](#runtime-config-mode)。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-263">The application starts on localhost according to the [runtime configuration mode](#runtime-config-mode).</span></span> <span data-ttu-id="8f2a0-264">导航到`http://localhost:5000`浏览器中显示登录页。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-264">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="running-with-visual-studio-2017"></a><span data-ttu-id="8f2a0-265">使用 Visual Studio 2017 运行</span><span class="sxs-lookup"><span data-stu-id="8f2a0-265">Running with Visual Studio 2017</span></span>

<span data-ttu-id="8f2a0-266">打开*.csproj*生成文件`dotnet new`命令。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-266">Open the *.csproj* file generated by the `dotnet new` command.</span></span> <span data-ttu-id="8f2a0-267">在项目打开时自动还原所需的 NuGet 和 npm 包。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-267">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="8f2a0-268">此还原过程可能需要几分钟时间，并已准备好它完成时要运行的应用程序。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-268">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="8f2a0-269">单击绿色运行的按钮或按`Ctrl + F5`，应用程序的登录页将打开浏览器。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-269">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="8f2a0-270">在根据本地主机上运行应用程序[运行时配置模式下](#runtime-config-mode)。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-270">The application runs on localhost according to the [runtime configuration mode](#runtime-config-mode).</span></span> 

<a name="app-testing"></a>

## <a name="testing-the-app"></a><span data-ttu-id="8f2a0-271">测试应用程序</span><span class="sxs-lookup"><span data-stu-id="8f2a0-271">Testing the app</span></span>

<span data-ttu-id="8f2a0-272">SpaServices 模板是预配置为运行客户端测试使用[Karma](https://karma-runner.github.io/1.0/index.html)和[Jasmine](https://jasmine.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-272">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="8f2a0-273">Jasmine 是常用于单元测试框架 JavaScript，而 Karma 是这些测试的测试运行程序。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-273">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="8f2a0-274">Karma 配置为使用[Webpack 开发人员中间件](#webpack-dev-middleware)以便无需停止并运行测试，每次进行更改。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-274">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that you don’t have to stop and run the test every time changes are made.</span></span> <span data-ttu-id="8f2a0-275">无论是针对测试用例或测试用例本身运行的代码，则测试将自动运行。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-275">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="8f2a0-276">使用作为示例的角度的应用程序，已为提供两个 Jasmine 测试用例`CounterComponent`中*counter.component.spec.ts*文件：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-276">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="8f2a0-277">打开命令提示符下，在项目根目录位置，并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-277">Open the command prompt at the project root, and run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="8f2a0-278">该脚本将启动 Karma 测试运行程序，读取中定义的设置*karma.conf.js*文件。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-278">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="8f2a0-279">除了其他设置以外， *karma.conf.js*标识要执行通过的测试文件其`files`数组：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-279">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

<a name="app-publishing"></a>

## <a name="publishing-the-application"></a><span data-ttu-id="8f2a0-280">发布应用程序</span><span class="sxs-lookup"><span data-stu-id="8f2a0-280">Publishing the application</span></span>

<span data-ttu-id="8f2a0-281">将生成的客户端资产和已发布的 ASP.NET Core 项目合并为准备就绪，可以部署包可能会很麻烦。</span><span class="sxs-lookup"><span data-stu-id="8f2a0-281">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="8f2a0-282">SpaServices 幸运的是，具有名为自定义 MSBuild 目标安排该整个发布进程`RunWebpack`:</span><span class="sxs-lookup"><span data-stu-id="8f2a0-282">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[Main](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="8f2a0-283">MSBuild 目标具有以下职责：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-283">The MSBuild target has the following responsibilities:</span></span>
1. <span data-ttu-id="8f2a0-284">还原 npm 包</span><span class="sxs-lookup"><span data-stu-id="8f2a0-284">Restore the npm packages</span></span>
1. <span data-ttu-id="8f2a0-285">创建第三方、 客户端资产的生产级版本</span><span class="sxs-lookup"><span data-stu-id="8f2a0-285">Create a production-grade build of the third-party, client-side assets</span></span>
1. <span data-ttu-id="8f2a0-286">创建自定义客户端资产的生产级版本</span><span class="sxs-lookup"><span data-stu-id="8f2a0-286">Create a production-grade build of the custom client-side assets</span></span>
1. <span data-ttu-id="8f2a0-287">将 Webpack 生成资产复制到发布文件夹</span><span class="sxs-lookup"><span data-stu-id="8f2a0-287">Copy the Webpack-generated assets to the publish folder</span></span>

<span data-ttu-id="8f2a0-288">运行时，将调用 MSBuild 目标：</span><span class="sxs-lookup"><span data-stu-id="8f2a0-288">The MSBuild target is invoked when running:</span></span>

```console
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="8f2a0-289">其他资源</span><span class="sxs-lookup"><span data-stu-id="8f2a0-289">Additional resources</span></span>

* [<span data-ttu-id="8f2a0-290">角度文档</span><span class="sxs-lookup"><span data-stu-id="8f2a0-290">Angular Docs</span></span>](https://angular.io/docs)
