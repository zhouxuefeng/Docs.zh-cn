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
ms.openlocfilehash: 810d89798330aeb1c387ec85eb05b1f4efde167a
ms.sourcegitcommit: 275a5381b6172b4f0b5fcd1d252aff03d3dae166
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2017
---
# <a name="bundling-and-minification-in-aspnet-core"></a>绑定和缩减中 ASP.NET 核心

绑定和缩减是两种技术可以在 ASP.NET 中使用，来提高 web 应用程序的页面加载性能。 绑定将多个文件合并到单个文件。 缩减执行各种不同的代码优化到脚本和 CSS，这会导致较小的负载。 一起使用时，绑定和缩减可以提高负载时间性能通过减少到服务器的请求数和减少的请求资产 （如 CSS 和 JavaScript 文件） 的大小。

此文章介绍了使用绑定和缩减，包括如何与 ASP.NET 核心应用程序使用这些功能的好处。

## <a name="overview"></a>概述

在 ASP.NET Core 应用中，有多个绑定和贴图层客户端资源选项。 MVC 的核心模板提供现成可用解决方案使用配置文件和 BuildBundlerMinifier NuGet 包。 第三方工具，如[Gulp](using-gulp.md)和[Grunt](using-grunt.md)也已可完成相同任务应你的过程中需要的其他工作流或复杂性。 通过使用设计时绑定和缩减，在应用程序的部署之前创建缩减的文件。 绑定和在部署前贴图层提供减少的服务器负载的优点。 但是，务必要识别该设计时绑定，缩减会增加生成复杂性，并且仅适用于静态文件。

绑定和缩减主要提高第一个页面请求加载时间。 一旦已请求网页上，浏览器缓存的资产 （JavaScript、 CSS 和图像） 因此绑定和缩减在请求同一页面时，不会提供任何性能提升，或在同一站点请求相同的资产。 如果未设置过期标头在你的资产上正常和不使用绑定和缩减、 浏览器的新鲜度试探方法会将标记为资产陈旧在几天后和浏览器将需要为每个资产的验证请求。 在这种情况下，绑定和缩减性能提高了性能即使在第一个页面请求之后。

### <a name="bundling"></a>绑定

绑定是一种功能，可以轻松地合并或捆绑到单个文件的多个文件。 由于绑定将多个文件合并到单个文件，它可请求数减少到检索和显示 web 资产，例如 web 页所需的服务器。 你可以创建 CSS、 JavaScript 和其他捆绑包。 少选一些文件意味着少数几个 HTTP 请求从你的浏览器到服务器或提供你的应用程序的服务。 第一个页面加载性能改善中的此结果。

### <a name="minification"></a>缩减

缩减执行各种不同的代码优化，从而降低的请求资产 （如 CSS、 图像、 JavaScript 文件） 的大小。 常见的结果的缩减包括删除不必要的空白区域和注释，并缩短为一个字符的变量名。

请考虑下面的 JavaScript 函数：

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

后缩减，该函数将都会减少到以下：

```javascript
AddAltToImg=function(t,a){var r=$(t,a);r.attr("alt",r.attr("id").replace(/ID/,""))};
```

除了删除注释和多余的空格，以下参数和变量名已重命名 （缩短），如下所示：

原始 | 重命名
--- | :---:
imageTagAndImageID | T
imageContext | a
imageElement | r

## <a name="impact-of-bundling-and-minification"></a>绑定和缩减的影响

下表显示单独列出所有资产，然后在简单的网页上使用绑定和缩减的几个重要区别：

操作 | 与 B/M | 而无需 B/M | 更改
--- | :---: | :---: | :---:
文件请求 |7 | 18 | 157%
传输的 KB | 156 | 264.68 | 70%
加载时间 (MS) | 885 | 2360 | 167%

发送的字节数必须是与绑定因为浏览器是与它们在请求应用的 HTTP 标头非常详细，大大减少。 加载时间显示显著的改进，但在本地运行，此示例。 使用资产的绑定和缩减通过网络传输时，你将获得的性能更高的增益。

## <a name="using-bundling-and-minification-in-a-project"></a>在项目中使用绑定和缩减

MVC 项目模板提供`bundleconfig.json`配置文件用于定义每个捆绑包的选项。 默认情况下，一个捆绑包配置定义的自定义 javascript (`wwwroot/js/site.js`) 和样式表 (`wwwroot/css/site.css`) 文件。

[!code-json[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/bundleconfig.json)]

捆绑选项包括：

* outputFileName-要输出捆绑文件的名称。 可以包含中的相对路径`bundleconfig.json`文件。 **必填**
* inputFiles-文件将捆绑在一起的数组。 这些是配置文件的相对路径。 **可选**，* 空值会在空的输出文件。 [组合](http://www.tldp.org/LDP/abs/html/globbingref.html)支持模式。
* minify-输出的缩减选项键入。 **可选**，*默认值-`minify: { enabled: true }`*
  * 每个输出文件类型有配置选项。
    * [CSS Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [JavaScript Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/jsminifier)
    * [HTML Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/htmlminifier)
* includeInProject-将生成的文件添加到项目文件。 **可选**，*默认-false*
* sourceMaps-生成捆绑的文件的源映射。 **可选**，*默认-false*

### <a name="visual-studio-2015--2017"></a>Visual Studio 2015 / 2017年

打开`bundleconfig.json`Visual Studio 中，如果你的环境不具有安装; 扩展提示提供了几种建议一个无法帮助你与此文件类型。

![BuildBundlerMinifier 扩展建议](../client-side/bundling-and-minification/_static/bundler-extension-suggestion.png)

选择查看扩展，并安装**捆绑包 （&) Minifier**扩展 （需要 Visual Studio 重启）。

![BuildBundlerMinifier 扩展建议](../client-side/bundling-and-minification/_static/view-extension.png)

重新启动完成后，你需要配置要运行的进程贴图层，然后将捆绑的客户端资产的生成。 右键单击`bundleconfig.json`文件，然后选择*上生成的启用软件包...*.

生成项目时，与`bundleconfig.json`包含在生成过程以生成基于配置的输出文件中。

```console
1>------ Build started: Project: BuildBundlerMinifierExample, Configuration: Debug Any CPU ------
1>
1>Bundler: Begin processing bundleconfig.json
1>Bundler: Done processing bundleconfig.json
1>BuildBundlerMinifierExample -> C:\BuildBundlerMinifierExample\bin\Debug\netcoreapp1.1\BuildBundlerMinifierExample.dll
========== Build: 1 succeeded or up-to-date, 0 failed, 0 skipped ==========
```

### <a name="visual-studio-code-or-command-line"></a>Visual Studio Code 或命令行

Visual Studio 和扩展驱动器使用 GUI 手势; 的绑定和缩减进程但是，相同的功能可与`dotnet`CLI 和 BuildBundlerMinifier NuGet 包。

将 NuGet 包添加到你的项目：

```console
dotnet add package BuildBundlerMinifier
```

还原这些依赖项：

```console
dotnet restore
```

生成应用程序：

```console
dotnet build
```

生成命令的输出显示缩减和/或绑定根据配置的内容的结果。

```console
Microsoft (R) Build Engine version 15.1.545.13942
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Begin processing bundleconfig.json
     Minified wwwroot/css/site.min.css
  Bundler: Done processing bundleconfig.json
  BuildBundlerMinifierExample -> /BuildBundlerMinifierExample/bin/Debug/netcoreapp1.0/BuildBundlerMinifierExample.dll
```

## <a name="adding-files"></a>将文件添加

在此示例中，一个额外的 CSS 文件添加调用`custom.css`并且配置为绑定和缩减功能`site.css`，这会导致在单个`site.min.css`。

custom.css

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

添加的相对路径`bundleconfig.json`。

[!code-json[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/bundleconfig2.json)]

> [!NOTE]
> 或者，可以使用组合模式-`"inputFiles": ["wwwroot/**/*(*.css|!(*.min.css)"]`后者将获取所有 CSS 文件，不包括缩减的文件模式。

生成应用程序和如果打开`site.min.css`，现在将注意到，内容`custom.css`已追加到文件末尾。

## <a name="controlling-bundling-and-minification"></a>控制绑定和缩减

一般情况下，你想要使用您的应用程序仅在生产环境中的捆绑和缩减型文件。 在开发期间，你想要使用原始文件，因此你的应用程序是更轻松地调试。

你可以指定哪些脚本和 CSS 文件，以包括页面布局页中使用环境标记帮助程序中 (请参阅[标记帮助程序](../mvc/views/tag-helpers/index.md))。 在特定环境中运行时，环境标记帮助器将仅呈现其内容。 请参阅[使用多个环境](../fundamentals/environments.md)有关指定当前的环境的详细信息。

在运行时，以下环境标记将呈现的未处理的 CSS 文件`Development`环境：

[!code-html[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/Views/Shared/_Layout.cshtml?highlight=3&range=9-12)]

仅在运行时，此环境标记将呈现的捆绑和缩减型 CSS 文件`Production`或`Staging`:

[!code-html[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/Views/Shared/_Layout.cshtml?highlight=5&range=13-18)]

## <a name="consuming-bundleconfigjson-from-gulp"></a>使用从 Gulp bundleconfig.json

如果你的应用绑定和缩减工作流需要其他进程，如图像处理、 缓存清除功能、 CDN assest 处理等，然后可以将 Gulp 转换的捆绑和 Minify 过程。

> [!NOTE]
> 转换选项仅在 Visual Studio 2015 和自 2017 年 1 中可用。

右键单击`bundleconfig.json`和选择**将转换为 Gulp...**.这将生成`gulpfile.js`并安装必要的 npm 包。

![将转换为 Gulp](../client-side/bundling-and-minification/_static/convert-togulp.png)

`gulpfile.js`生成读取`bundleconfig.json`文件有关的配置，因此它可以继续用于进行输入/输出和设置。

[!code-javascript[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/gulpfile.js)]

若要在 Visual Studio 2017 生成项目时，请启用 Gulp，请向 *.csproj 文件中添加以下：

```xml
<Target Name="MyPreCompileTarget" BeforeTargets="Build">
    <Exec Command="gulp min" />
</Target>
```

若要启用 Gulp，Visual Studio 2015 中生成项目时，将以下代码添加到`project.json`文件：

```json
"scripts": {
    "precompile": "gulp min"
}
```

## <a name="additional-resources"></a>其他资源

* [使用 Gulp](using-gulp.md)
* [使用 Grunt](using-grunt.md)
* [使用多个环境](../fundamentals/environments.md)
* [标记帮助程序](../mvc/views/tag-helpers/index.md)
