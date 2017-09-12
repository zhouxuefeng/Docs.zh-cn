---
title: "绑定和缩减中 ASP.NET 核心"
author: spboyer
description: 
keywords: "ASP.NET 核心、 绑定和缩减、 CSS、 JavaScript，Minify，BuildBundlerMinifier"
ms.author: riande
manager: wpickett
ms.date: 02/28/2017
ms.topic: article
ms.assetid: d54230f9-8e5f-4861-a29c-1d3a14e0b0d9
ms.technology: aspnet
ms.prod: aspnet-core
uid: client-side/bundling-and-minification
ms.openlocfilehash: d8512bdd49b61019f22a49900bdd65086d821a6b
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="bundling-and-minification-in-aspnet-core"></a><span data-ttu-id="246c1-103">绑定和缩减中 ASP.NET 核心</span><span class="sxs-lookup"><span data-stu-id="246c1-103">Bundling and minification in ASP.NET Core</span></span>

<span data-ttu-id="246c1-104">绑定和缩减是两种技术可以在 ASP.NET 中使用，来提高 web 应用程序的页面加载性能。</span><span class="sxs-lookup"><span data-stu-id="246c1-104">Bundling and minification are two techniques you can use in ASP.NET to improve page load performance for your web application.</span></span> <span data-ttu-id="246c1-105">绑定将多个文件合并到单个文件。</span><span class="sxs-lookup"><span data-stu-id="246c1-105">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="246c1-106">缩减执行各种不同的代码优化到脚本和 CSS，这会导致较小的负载。</span><span class="sxs-lookup"><span data-stu-id="246c1-106">Minification performs a variety of different code optimizations to scripts and CSS, which results in smaller payloads.</span></span> <span data-ttu-id="246c1-107">一起使用时，绑定和缩减可以提高负载时间性能通过减少到服务器的请求数和减少的请求资产 （如 CSS 和 JavaScript 文件） 的大小。</span><span class="sxs-lookup"><span data-stu-id="246c1-107">Used together, bundling and minification improves load time performance by reducing the number of requests to the server and reducing the size of the requested assets (such as CSS and JavaScript files).</span></span>

<span data-ttu-id="246c1-108">此文章介绍了使用绑定和缩减，包括如何与 ASP.NET 核心应用程序使用这些功能的好处。</span><span class="sxs-lookup"><span data-stu-id="246c1-108">This article explains the benefits of using bundling and minification, including how these features can be used with ASP.NET Core applications.</span></span>

## <a name="overview"></a><span data-ttu-id="246c1-109">概述</span><span class="sxs-lookup"><span data-stu-id="246c1-109">Overview</span></span>

<span data-ttu-id="246c1-110">在 ASP.NET Core 应用中，有多个绑定和贴图层客户端资源选项。</span><span class="sxs-lookup"><span data-stu-id="246c1-110">In ASP.NET Core apps, there are multiple options for bundling and minifying client-side resources.</span></span> <span data-ttu-id="246c1-111">MVC 的核心模板提供现成可用解决方案使用配置文件和 BuildBundlerMinifier NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="246c1-111">The core templates for MVC provide an out-of-the-box solution using a configuration file and BuildBundlerMinifier NuGet package.</span></span> <span data-ttu-id="246c1-112">第三方工具，如[Gulp](using-gulp.md)和[Grunt](using-grunt.md)也已可完成相同任务应你的过程中需要的其他工作流或复杂性。</span><span class="sxs-lookup"><span data-stu-id="246c1-112">Third party tools, such as [Gulp](using-gulp.md) and [Grunt](using-grunt.md) are also available to accomplish the same tasks should your processes require additional workflow or complexities.</span></span> <span data-ttu-id="246c1-113">通过使用设计时绑定和缩减，在应用程序的部署之前创建缩减的文件。</span><span class="sxs-lookup"><span data-stu-id="246c1-113">By using design-time bundling and minification, the minified files are created prior to the application’s deployment.</span></span> <span data-ttu-id="246c1-114">绑定和在部署前贴图层提供减少的服务器负载的优点。</span><span class="sxs-lookup"><span data-stu-id="246c1-114">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="246c1-115">但是，务必要识别该设计时绑定，缩减会增加生成复杂性，并且仅适用于静态文件。</span><span class="sxs-lookup"><span data-stu-id="246c1-115">However, it’s important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

<span data-ttu-id="246c1-116">绑定和缩减主要提高第一个页面请求加载时间。</span><span class="sxs-lookup"><span data-stu-id="246c1-116">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="246c1-117">一旦已请求网页上，浏览器缓存的资产 （JavaScript、 CSS 和图像） 因此绑定和缩减在请求同一页面时，不会提供任何性能提升，或在同一站点请求相同的资产。</span><span class="sxs-lookup"><span data-stu-id="246c1-117">Once a web page has been requested, the browser caches the assets (JavaScript, CSS and images) so bundling and minification won’t provide any performance boost when requesting the same page, or pages on the same site requesting the same assets.</span></span> <span data-ttu-id="246c1-118">如果未设置过期标头在你的资产上正常和不使用绑定和缩减、 浏览器的新鲜度试探方法会将标记为资产陈旧在几天后和浏览器将需要为每个资产的验证请求。</span><span class="sxs-lookup"><span data-stu-id="246c1-118">If you don’t set the expires header correctly on your assets, and you don’t use bundling and minification, the browser's freshness heuristics will mark the assets stale after a few days and the browser will require a validation request for each asset.</span></span> <span data-ttu-id="246c1-119">在这种情况下，绑定和缩减性能提高了性能即使在第一个页面请求之后。</span><span class="sxs-lookup"><span data-stu-id="246c1-119">In this case, bundling and minification provide a performance increase even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="246c1-120">绑定</span><span class="sxs-lookup"><span data-stu-id="246c1-120">Bundling</span></span>

<span data-ttu-id="246c1-121">绑定是一种功能，可以轻松地合并或捆绑到单个文件的多个文件。</span><span class="sxs-lookup"><span data-stu-id="246c1-121">Bundling is a feature that makes it easy to combine or bundle multiple files into a single file.</span></span> <span data-ttu-id="246c1-122">由于绑定将多个文件合并到单个文件，它可请求数减少到检索和显示 web 资产，例如 web 页所需的服务器。</span><span class="sxs-lookup"><span data-stu-id="246c1-122">Because bundling combines multiple files into a single file, it reduces the number of requests to the server that are required to retrieve and display a web asset, such as a web page.</span></span> <span data-ttu-id="246c1-123">你可以创建 CSS、 JavaScript 和其他捆绑包。</span><span class="sxs-lookup"><span data-stu-id="246c1-123">You can create CSS, JavaScript and other bundles.</span></span> <span data-ttu-id="246c1-124">少选一些文件意味着少数几个 HTTP 请求从你的浏览器到服务器或提供你的应用程序的服务。</span><span class="sxs-lookup"><span data-stu-id="246c1-124">Fewer files means fewer HTTP requests from your browser to the server or from the service providing your application.</span></span> <span data-ttu-id="246c1-125">第一个页面加载性能改善中的此结果。</span><span class="sxs-lookup"><span data-stu-id="246c1-125">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="246c1-126">缩减</span><span class="sxs-lookup"><span data-stu-id="246c1-126">Minification</span></span>

<span data-ttu-id="246c1-127">缩减执行各种不同的代码优化，从而降低的请求资产 （如 CSS、 图像、 JavaScript 文件） 的大小。</span><span class="sxs-lookup"><span data-stu-id="246c1-127">Minification performs a variety of different code optimizations to reduce the size of requested assets (such as CSS, images, JavaScript files).</span></span> <span data-ttu-id="246c1-128">常见的结果的缩减包括删除不必要的空白区域和注释，并缩短为一个字符的变量名。</span><span class="sxs-lookup"><span data-stu-id="246c1-128">Common results of minification include removing unnecessary white space and comments, and shortening variable names to one character.</span></span>

<span data-ttu-id="246c1-129">请考虑下面的 JavaScript 函数：</span><span class="sxs-lookup"><span data-stu-id="246c1-129">Consider the following JavaScript function:</span></span>

```javascript
AddAltToImg = function (imageTagAndImageID, imageContext) {
  ///<signature>
  ///<summary> Adds an alt tab to the image
  // </summary>
  //<param name="imgElement" type="String">The image selector.</param>
  //<param name="ContextForImage" type="String">The image context.</param>
  ///</signature>
  var imageElement = $(imageTagAndImageID, imageContext);
  imageElement.attr('alt', imageElement.attr('id').replace(/ID/, ''));
}
```

<span data-ttu-id="246c1-130">后缩减，该函数将都会减少到以下：</span><span class="sxs-lookup"><span data-stu-id="246c1-130">After minification, the function is reduced to the following:</span></span>

```javascript
AddAltToImg=function(t,a){var r=$(t,a);r.attr("alt",r.attr("id").replace(/ID/,""))};
```

<span data-ttu-id="246c1-131">除了删除注释和多余的空格，以下参数和变量名已重命名 （缩短），如下所示：</span><span class="sxs-lookup"><span data-stu-id="246c1-131">In addition to removing the comments and unnecessary whitespace, the following parameters and variable names were renamed (shortened) as follows:</span></span>

<span data-ttu-id="246c1-132">原始</span><span class="sxs-lookup"><span data-stu-id="246c1-132">Original</span></span> | <span data-ttu-id="246c1-133">重命名</span><span class="sxs-lookup"><span data-stu-id="246c1-133">Renamed</span></span>
--- | :---:
<span data-ttu-id="246c1-134">imageTagAndImageID</span><span class="sxs-lookup"><span data-stu-id="246c1-134">imageTagAndImageID</span></span> | <span data-ttu-id="246c1-135">T</span><span class="sxs-lookup"><span data-stu-id="246c1-135">t</span></span>
<span data-ttu-id="246c1-136">imageContext</span><span class="sxs-lookup"><span data-stu-id="246c1-136">imageContext</span></span> | <span data-ttu-id="246c1-137">a</span><span class="sxs-lookup"><span data-stu-id="246c1-137">a</span></span>
<span data-ttu-id="246c1-138">imageElement</span><span class="sxs-lookup"><span data-stu-id="246c1-138">imageElement</span></span> | <span data-ttu-id="246c1-139">r</span><span class="sxs-lookup"><span data-stu-id="246c1-139">r</span></span>

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="246c1-140">绑定和缩减的影响</span><span class="sxs-lookup"><span data-stu-id="246c1-140">Impact of bundling and minification</span></span>

<span data-ttu-id="246c1-141">下表显示单独列出所有资产，然后在简单的网页上使用绑定和缩减的几个重要区别：</span><span class="sxs-lookup"><span data-stu-id="246c1-141">The following table shows several important differences between listing all the assets individually and using bundling and minification on a simple web page:</span></span>

<span data-ttu-id="246c1-142">操作</span><span class="sxs-lookup"><span data-stu-id="246c1-142">Action</span></span> | <span data-ttu-id="246c1-143">与 B/M</span><span class="sxs-lookup"><span data-stu-id="246c1-143">With B/M</span></span> | <span data-ttu-id="246c1-144">而无需 B/M</span><span class="sxs-lookup"><span data-stu-id="246c1-144">Without B/M</span></span> | <span data-ttu-id="246c1-145">更改</span><span class="sxs-lookup"><span data-stu-id="246c1-145">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="246c1-146">文件请求</span><span class="sxs-lookup"><span data-stu-id="246c1-146">File Requests</span></span> |<span data-ttu-id="246c1-147">7</span><span class="sxs-lookup"><span data-stu-id="246c1-147">7</span></span> | <span data-ttu-id="246c1-148">18</span><span class="sxs-lookup"><span data-stu-id="246c1-148">18</span></span> | <span data-ttu-id="246c1-149">157%</span><span class="sxs-lookup"><span data-stu-id="246c1-149">157%</span></span>
<span data-ttu-id="246c1-150">传输的 KB</span><span class="sxs-lookup"><span data-stu-id="246c1-150">KB Transferred</span></span> | <span data-ttu-id="246c1-151">156</span><span class="sxs-lookup"><span data-stu-id="246c1-151">156</span></span> | <span data-ttu-id="246c1-152">264.68</span><span class="sxs-lookup"><span data-stu-id="246c1-152">264.68</span></span> | <span data-ttu-id="246c1-153">70%</span><span class="sxs-lookup"><span data-stu-id="246c1-153">70%</span></span>
<span data-ttu-id="246c1-154">加载时间 (MS)</span><span class="sxs-lookup"><span data-stu-id="246c1-154">Load Time (MS)</span></span> | <span data-ttu-id="246c1-155">885</span><span class="sxs-lookup"><span data-stu-id="246c1-155">885</span></span> | <span data-ttu-id="246c1-156">2360</span><span class="sxs-lookup"><span data-stu-id="246c1-156">2360</span></span> | <span data-ttu-id="246c1-157">167%</span><span class="sxs-lookup"><span data-stu-id="246c1-157">167%</span></span>

<span data-ttu-id="246c1-158">发送的字节数必须是与绑定因为浏览器是与它们在请求应用的 HTTP 标头非常详细，大大减少。</span><span class="sxs-lookup"><span data-stu-id="246c1-158">The bytes sent had a significant reduction with bundling as browsers are fairly verbose with the HTTP headers that they apply on requests.</span></span> <span data-ttu-id="246c1-159">加载时间显示显著的改进，但在本地运行，此示例。</span><span class="sxs-lookup"><span data-stu-id="246c1-159">The load time shows a big improvement, however this example was run locally.</span></span> <span data-ttu-id="246c1-160">使用资产的绑定和缩减通过网络传输时，你将获得的性能更高的增益。</span><span class="sxs-lookup"><span data-stu-id="246c1-160">You will get greater gains in performance when using bundling and minification with assets transferred over a network.</span></span>

## <a name="using-bundling-and-minification-in-a-project"></a><span data-ttu-id="246c1-161">在项目中使用绑定和缩减</span><span class="sxs-lookup"><span data-stu-id="246c1-161">Using bundling and minification in a project</span></span>

<span data-ttu-id="246c1-162">MVC 项目模板提供`bundleconfig.json`配置文件用于定义每个捆绑包的选项。</span><span class="sxs-lookup"><span data-stu-id="246c1-162">The MVC project template provides a `bundleconfig.json` configuration file which defines the options for each bundle.</span></span> <span data-ttu-id="246c1-163">默认情况下，一个捆绑包配置定义的自定义 javascript (`wwwroot/js/site.js`) 和样式表 (`wwwroot/css/site.css`) 文件。</span><span class="sxs-lookup"><span data-stu-id="246c1-163">By default, a single bundle configuration is defined for the custom JavaScript (`wwwroot/js/site.js`) and Stylesheet (`wwwroot/css/site.css`) files.</span></span>

<span data-ttu-id="246c1-164">[!code-json[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/bundleconfig.json)]</span><span class="sxs-lookup"><span data-stu-id="246c1-164">[!code-json[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/bundleconfig.json)]</span></span>

<span data-ttu-id="246c1-165">捆绑选项包括：</span><span class="sxs-lookup"><span data-stu-id="246c1-165">Bundle options include:</span></span>

* <span data-ttu-id="246c1-166">outputFileName-要输出捆绑文件的名称。</span><span class="sxs-lookup"><span data-stu-id="246c1-166">outputFileName - name of the bundle file to output.</span></span> <span data-ttu-id="246c1-167">可以包含中的相对路径`bundleconfig.json`文件。</span><span class="sxs-lookup"><span data-stu-id="246c1-167">Can contain a relative path from the `bundleconfig.json` file.</span></span> <span data-ttu-id="246c1-168">**必填**</span><span class="sxs-lookup"><span data-stu-id="246c1-168">**required**</span></span>
* <span data-ttu-id="246c1-169">inputFiles-文件将捆绑在一起的数组。</span><span class="sxs-lookup"><span data-stu-id="246c1-169">inputFiles - array of files to bundle together.</span></span> <span data-ttu-id="246c1-170">这些是配置文件的相对路径。</span><span class="sxs-lookup"><span data-stu-id="246c1-170">These are relative paths to the configuration file.</span></span> <span data-ttu-id="246c1-171">**可选**，* 空值会在空的输出文件。</span><span class="sxs-lookup"><span data-stu-id="246c1-171">**optional**, *an empty value results in an empty output file.</span></span> <span data-ttu-id="246c1-172">[组合](http://www.tldp.org/LDP/abs/html/globbingref.html)支持模式。</span><span class="sxs-lookup"><span data-stu-id="246c1-172">[globbing](http://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="246c1-173">minify-输出的缩减选项键入。</span><span class="sxs-lookup"><span data-stu-id="246c1-173">minify - minification options for the output type.</span></span> <span data-ttu-id="246c1-174">**可选**，*默认值-`minify: { enabled: true }`*</span><span class="sxs-lookup"><span data-stu-id="246c1-174">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="246c1-175">每个输出文件类型有配置选项。</span><span class="sxs-lookup"><span data-stu-id="246c1-175">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="246c1-176">CSS Minifier</span><span class="sxs-lookup"><span data-stu-id="246c1-176">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="246c1-177">JavaScript Minifier</span><span class="sxs-lookup"><span data-stu-id="246c1-177">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
    * [<span data-ttu-id="246c1-178">HTML Minifier</span><span class="sxs-lookup"><span data-stu-id="246c1-178">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="246c1-179">includeInProject-将生成的文件添加到项目文件。</span><span class="sxs-lookup"><span data-stu-id="246c1-179">includeInProject - add generated files to project file.</span></span> <span data-ttu-id="246c1-180">**可选**，*默认-false*</span><span class="sxs-lookup"><span data-stu-id="246c1-180">**optional**, *default - false*</span></span>
* <span data-ttu-id="246c1-181">sourceMaps-生成捆绑的文件的源映射。</span><span class="sxs-lookup"><span data-stu-id="246c1-181">sourceMaps - generate source maps for the bundled file.</span></span> <span data-ttu-id="246c1-182">**可选**，*默认-false*</span><span class="sxs-lookup"><span data-stu-id="246c1-182">**optional**, *default - false*</span></span>

### <a name="visual-studio-2015--2017"></a><span data-ttu-id="246c1-183">Visual Studio 2015 / 2017年</span><span class="sxs-lookup"><span data-stu-id="246c1-183">Visual Studio 2015 / 2017</span></span>

<span data-ttu-id="246c1-184">打开`bundleconfig.json`Visual Studio 中，如果你的环境不具有安装; 扩展提示提供了几种建议一个无法帮助你与此文件类型。</span><span class="sxs-lookup"><span data-stu-id="246c1-184">Open `bundleconfig.json` in Visual Studio, if your environment does not have the extension installed; a prompt is presented suggesting that there is one that could assist with this file type.</span></span>

![BuildBundlerMinifier 扩展建议](../client-side/bundling-and-minification/_static/bundler-extension-suggestion.png)

<span data-ttu-id="246c1-186">选择查看扩展，并安装**捆绑包 （&) Minifier**扩展 （需要 Visual Studio 重启）。</span><span class="sxs-lookup"><span data-stu-id="246c1-186">Select View Extensions, and install the **Bundler & Minifier** extension (Requires Visual Studio restart).</span></span>

![BuildBundlerMinifier 扩展建议](../client-side/bundling-and-minification/_static/view-extension.png)

<span data-ttu-id="246c1-188">重新启动完成后，你需要配置要运行的进程贴图层，然后将捆绑的客户端资产的生成。</span><span class="sxs-lookup"><span data-stu-id="246c1-188">When the restart is complete, you need to configure the build to run the processes of minifying and bundling the client-side assets.</span></span> <span data-ttu-id="246c1-189">右键单击`bundleconfig.json`文件，然后选择*上生成的启用软件包...*.</span><span class="sxs-lookup"><span data-stu-id="246c1-189">Right-click the `bundleconfig.json` file and select *Enable bundle on build...*.</span></span>

<span data-ttu-id="246c1-190">生成项目时，与`bundleconfig.json`包含在生成过程以生成基于配置的输出文件中。</span><span class="sxs-lookup"><span data-stu-id="246c1-190">Build the project, and the `bundleconfig.json` is included in the build process to produce the output files based on the configuration.</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierExample, Configuration: Debug Any CPU ------
1>
1>Bundler: Begin processing bundleconfig.json
1>Bundler: Done processing bundleconfig.json
1>BuildBundlerMinifierExample -> C:\BuildBundlerMinifierExample\bin\Debug\netcoreapp1.1\BuildBundlerMinifierExample.dll
========== Build: 1 succeeded or up-to-date, 0 failed, 0 skipped ==========
```

### <a name="visual-studio-code-or-command-line"></a><span data-ttu-id="246c1-191">Visual Studio Code 或命令行</span><span class="sxs-lookup"><span data-stu-id="246c1-191">Visual Studio Code or Command Line</span></span>

<span data-ttu-id="246c1-192">Visual Studio 和扩展驱动器使用 GUI 手势; 的绑定和缩减进程但是，相同的功能可与`dotnet`CLI 和 BuildBundlerMinifier NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="246c1-192">Visual Studio and the extension drive the bundling and minification process using GUI gestures; however, the same capabilities are available with the `dotnet` CLI and BuildBundlerMinifier NuGet package.</span></span>

<span data-ttu-id="246c1-193">将 NuGet 包添加到你的项目：</span><span class="sxs-lookup"><span data-stu-id="246c1-193">Add the NuGet package to your project:</span></span>

```console
dotnet add package BuildBundlerMinifier
```

<span data-ttu-id="246c1-194">还原这些依赖项：</span><span class="sxs-lookup"><span data-stu-id="246c1-194">Restore the dependencies:</span></span>

```console
dotnet restore
```

<span data-ttu-id="246c1-195">生成应用程序：</span><span class="sxs-lookup"><span data-stu-id="246c1-195">Build the app:</span></span>

```console
dotnet build
```

<span data-ttu-id="246c1-196">生成命令的输出显示缩减和/或绑定根据配置的内容的结果。</span><span class="sxs-lookup"><span data-stu-id="246c1-196">The output from the build command shows the results of the minification and/or bundling according to what is configured.</span></span>

```console
Microsoft (R) Build Engine version 15.1.545.13942
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Begin processing bundleconfig.json
     Minified wwwroot/css/site.min.css
  Bundler: Done processing bundleconfig.json
  BuildBundlerMinifierExample -> /BuildBundlerMinifierExample/bin/Debug/netcoreapp1.0/BuildBundlerMinifierExample.dll
```

## <a name="adding-files"></a><span data-ttu-id="246c1-197">将文件添加</span><span class="sxs-lookup"><span data-stu-id="246c1-197">Adding files</span></span>

<span data-ttu-id="246c1-198">在此示例中，一个额外的 CSS 文件添加调用`custom.css`并且配置为绑定和缩减功能`site.css`，这会导致在单个`site.min.css`。</span><span class="sxs-lookup"><span data-stu-id="246c1-198">In this example, an additional CSS file is added called `custom.css` and configured for bundling and minification with `site.css`, resulting in a single `site.min.css`.</span></span>

<span data-ttu-id="246c1-199">custom.css</span><span class="sxs-lookup"><span data-stu-id="246c1-199">custom.css</span></span>

```css
.about, [role=main], [role=complementary]
{
    margin-top: 60px;
}

footer
{
    margin-top: 10px;
}
```

<span data-ttu-id="246c1-200">添加的相对路径`bundleconfig.json`。</span><span class="sxs-lookup"><span data-stu-id="246c1-200">Add the relative path to `bundleconfig.json`.</span></span>

<span data-ttu-id="246c1-201">[!code-json[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/bundleconfig2.json)]</span><span class="sxs-lookup"><span data-stu-id="246c1-201">[!code-json[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/bundleconfig2.json)]</span></span>

> [!NOTE]
> <span data-ttu-id="246c1-202">或者，可以使用组合模式-`"inputFiles": ["wwwroot/**/*(*.css|!(*.min.css)"]`后者将获取所有 CSS 文件，不包括缩减的文件模式。</span><span class="sxs-lookup"><span data-stu-id="246c1-202">Alternatively, the globbing pattern could be used - `"inputFiles": ["wwwroot/**/*(*.css|!(*.min.css)"]` which gets all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="246c1-203">生成应用程序和如果打开`site.min.css`，现在将注意到，内容`custom.css`已追加到文件末尾。</span><span class="sxs-lookup"><span data-stu-id="246c1-203">Build the application and if you open `site.min.css`, you'll now notice that contents of `custom.css` has been appended to the end of the file.</span></span>

## <a name="controlling-bundling-and-minification"></a><span data-ttu-id="246c1-204">控制绑定和缩减</span><span class="sxs-lookup"><span data-stu-id="246c1-204">Controlling bundling and minification</span></span>

<span data-ttu-id="246c1-205">一般情况下，你想要使用您的应用程序仅在生产环境中的捆绑和缩减型文件。</span><span class="sxs-lookup"><span data-stu-id="246c1-205">In general, you want to use the bundled and minified files of your app only in a production environment.</span></span> <span data-ttu-id="246c1-206">在开发期间，你想要使用原始文件，因此你的应用程序是更轻松地调试。</span><span class="sxs-lookup"><span data-stu-id="246c1-206">During development, you want to use your original files so your app is easier to debug.</span></span>

<span data-ttu-id="246c1-207">你可以指定哪些脚本和 CSS 文件，以包括页面布局页中使用环境标记帮助程序中 (请参阅[标记帮助程序](../mvc/views/tag-helpers/index.md))。</span><span class="sxs-lookup"><span data-stu-id="246c1-207">You can specify which scripts and CSS files to include in your pages using the environment tag helper in your layout pages (see [Tag Helpers](../mvc/views/tag-helpers/index.md)).</span></span> <span data-ttu-id="246c1-208">在特定环境中运行时，环境标记帮助器将仅呈现其内容。</span><span class="sxs-lookup"><span data-stu-id="246c1-208">The environment tag helper will only render its contents when running in specific environments.</span></span> <span data-ttu-id="246c1-209">请参阅[使用多个环境](../fundamentals/environments.md)有关指定当前的环境的详细信息。</span><span class="sxs-lookup"><span data-stu-id="246c1-209">See [Working with Multiple Environments](../fundamentals/environments.md) for details on specifying the current environment.</span></span>

<span data-ttu-id="246c1-210">在运行时，以下环境标记将呈现的未处理的 CSS 文件`Development`环境：</span><span class="sxs-lookup"><span data-stu-id="246c1-210">The following environment tag will render the unprocessed CSS files when running in the `Development` environment:</span></span>

<span data-ttu-id="246c1-211">[!code-html[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/Views/Shared/_Layout.cshtml?highlight=3&range=9-12)]</span><span class="sxs-lookup"><span data-stu-id="246c1-211">[!code-html[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/Views/Shared/_Layout.cshtml?highlight=3&range=9-12)]</span></span>

<span data-ttu-id="246c1-212">仅在运行时，此环境标记将呈现的捆绑和缩减型 CSS 文件`Production`或`Staging`:</span><span class="sxs-lookup"><span data-stu-id="246c1-212">This environment tag will render the bundled and minified CSS files only when running in `Production` or `Staging`:</span></span>

<span data-ttu-id="246c1-213">[!code-html[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/Views/Shared/_Layout.cshtml?highlight=5&range=13-18)]</span><span class="sxs-lookup"><span data-stu-id="246c1-213">[!code-html[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/Views/Shared/_Layout.cshtml?highlight=5&range=13-18)]</span></span>

## <a name="consuming-bundleconfigjson-from-gulp"></a><span data-ttu-id="246c1-214">使用从 Gulp bundleconfig.json</span><span class="sxs-lookup"><span data-stu-id="246c1-214">Consuming bundleconfig.json from Gulp</span></span>

<span data-ttu-id="246c1-215">如果你的应用绑定和缩减工作流需要其他进程，如图像处理、 缓存清除功能、 CDN assest 处理等，然后可以将 Gulp 转换的捆绑和 Minify 过程。</span><span class="sxs-lookup"><span data-stu-id="246c1-215">If your app bundling and minification workflow requires additional processes such as image processing, cache busting, CDN assest processing, etc., then you can convert the Bundle and Minify process to Gulp.</span></span>

> [!NOTE]
> <span data-ttu-id="246c1-216">转换选项仅在 Visual Studio 2015 和自 2017 年 1 中可用。</span><span class="sxs-lookup"><span data-stu-id="246c1-216">Conversion option only available in Visual Studio 2015 and 2017.</span></span>

<span data-ttu-id="246c1-217">右键单击`bundleconfig.json`和选择**将转换为 Gulp...**.这将生成`gulpfile.js`并安装必要的 npm 包。</span><span class="sxs-lookup"><span data-stu-id="246c1-217">Right-click the `bundleconfig.json` and select **Convert to Gulp...**. This will generate the `gulpfile.js` and install the necessary npm packages.</span></span>

![将转换为 Gulp](../client-side/bundling-and-minification/_static/convert-togulp.png)

<span data-ttu-id="246c1-219">`gulpfile.js`生成读取`bundleconfig.json`文件有关的配置，因此它可以继续用于进行输入/输出和设置。</span><span class="sxs-lookup"><span data-stu-id="246c1-219">The `gulpfile.js` produced reads the `bundleconfig.json` file for the configuration, therefore it can continue to be used for the inputs/outputs and settings.</span></span>

<span data-ttu-id="246c1-220">[!code-javascript[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/gulpfile.js)]</span><span class="sxs-lookup"><span data-stu-id="246c1-220">[!code-javascript[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/gulpfile.js)]</span></span>

<span data-ttu-id="246c1-221">若要在 Visual Studio 2017 生成项目时，请启用 Gulp，请向 *.csproj 文件中添加以下：</span><span class="sxs-lookup"><span data-stu-id="246c1-221">To enable Gulp when the project builds in Visual Studio 2017, add the following to the *.csproj file:</span></span>

```xml
<Target Name="MyPreCompileTarget" BeforeTargets="Build">
    <Exec Command="gulp min" />
</Target>
```

<span data-ttu-id="246c1-222">若要启用 Gulp，Visual Studio 2015 中生成项目时，将以下代码添加到`project.json`文件：</span><span class="sxs-lookup"><span data-stu-id="246c1-222">To enable Gulp when the project builds in Visual Studio 2015, add the following to the `project.json` file:</span></span>

```json
"scripts": {
    "precompile": "gulp min"
}
```

## <a name="additional-resources"></a><span data-ttu-id="246c1-223">其他资源</span><span class="sxs-lookup"><span data-stu-id="246c1-223">Additional resources</span></span>

* [<span data-ttu-id="246c1-224">使用 Gulp</span><span class="sxs-lookup"><span data-stu-id="246c1-224">Using Gulp</span></span>](using-gulp.md)
* [<span data-ttu-id="246c1-225">使用 Grunt</span><span class="sxs-lookup"><span data-stu-id="246c1-225">Using Grunt</span></span>](using-grunt.md)
* [<span data-ttu-id="246c1-226">使用多个环境</span><span class="sxs-lookup"><span data-stu-id="246c1-226">Working with Multiple Environments</span></span>](../fundamentals/environments.md)
* [<span data-ttu-id="246c1-227">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="246c1-227">Tag Helpers</span></span>](../mvc/views/tag-helpers/index.md)
