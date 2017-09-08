---
title: "使用 AngularJS 单页面应用程序 (Spa)"
author: rick-anderson
description: "了解如何构建使用 AngularJS 的 SPA 样式 ASP.NET 应用程序"
keywords: "ASP.NET 核心，AngularJS SPA"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 4b30576b-2718-4c39-9253-a59966747893
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/angular
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 50d2e76c472e67c26238abee4f7b0ed64cd043ab
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="using-angularjs-for-single-page-applications-spas-with-aspnet-core"></a><span data-ttu-id="e26ef-104">使用 AngularJS 适用于 ASP.NET Core 的单页面应用程序 (Spa)</span><span class="sxs-lookup"><span data-stu-id="e26ef-104">Using AngularJS for Single Page Applications (SPAs) with ASP.NET Core</span></span>


<span data-ttu-id="e26ef-105">通过[Venkata Koppaka](http://blog.falafel.com/author/venkata-koppaka/)和[Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="e26ef-105">By [Venkata Koppaka](http://blog.falafel.com/author/venkata-koppaka/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="e26ef-106">在本文中，你将了解如何构建使用 AngularJS 的 SPA 样式 ASP.NET 应用程序。</span><span class="sxs-lookup"><span data-stu-id="e26ef-106">In this article, you will learn how to build a SPA-style ASP.NET application using AngularJS.</span></span>

[<span data-ttu-id="e26ef-107">查看或下载示例代码</span><span class="sxs-lookup"><span data-stu-id="e26ef-107">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample)

## <a name="what-is-angularjs"></a><span data-ttu-id="e26ef-108">AngularJS 是什么？</span><span class="sxs-lookup"><span data-stu-id="e26ef-108">What is AngularJS?</span></span>

<span data-ttu-id="e26ef-109">[AngularJS](http://angularjs.org/)是一个现代的 JavaScript 框架，将来自 Google 通常用于使用单页面应用程序 (Spa)。</span><span class="sxs-lookup"><span data-stu-id="e26ef-109">[AngularJS](http://angularjs.org/) is a modern JavaScript framework from Google commonly used to work with Single Page Applications (SPAs).</span></span> <span data-ttu-id="e26ef-110">AngularJS 处于打开状态来源依据 MIT 许可和 AngularJS 的开发进度适用于[其 GitHub 存储库](https://github.com/angular/angular.js)。</span><span class="sxs-lookup"><span data-stu-id="e26ef-110">AngularJS is open sourced under MIT license, and the development progress of AngularJS can be followed on [its GitHub repository](https://github.com/angular/angular.js).</span></span> <span data-ttu-id="e26ef-111">库称为角，因为 HTML 使用形角度方括号。</span><span class="sxs-lookup"><span data-stu-id="e26ef-111">The library is called Angular because HTML uses angular-shaped brackets.</span></span>

<span data-ttu-id="e26ef-112">AngularJS 不是如 jQuery，一个 DOM 操作库，但它使用 jQuery 调用 jQLite 的子集。</span><span class="sxs-lookup"><span data-stu-id="e26ef-112">AngularJS is not a DOM manipulation library like jQuery, but it uses a subset of jQuery called jQLite.</span></span> <span data-ttu-id="e26ef-113">AngularJS 主要取决于你可以将其添加到 HTML 标记的声明性 HTML 特性。</span><span class="sxs-lookup"><span data-stu-id="e26ef-113">AngularJS is primarily based on declarative HTML attributes that you can add to your HTML tags.</span></span> <span data-ttu-id="e26ef-114">你可以在你浏览器使用尝试 AngularJS[代码学校网站](https://www.codeschool.com/courses/shaping-up-with-angular-js)或[W3Schools 网站](https://www.w3schools.com/angular/)。</span><span class="sxs-lookup"><span data-stu-id="e26ef-114">You can try AngularJS in your browser using the [Code School website](https://www.codeschool.com/courses/shaping-up-with-angular-js) or  [W3Schools website](https://www.w3schools.com/angular/).</span></span>

<span data-ttu-id="e26ef-115">本文着重于 AngularJS 与在其中的角发展方向上的一些注意事项。</span><span class="sxs-lookup"><span data-stu-id="e26ef-115">This article focuses on AngularJS with some notes on where Angular is heading.</span></span>

## <a name="getting-started"></a><span data-ttu-id="e26ef-116">入门</span><span class="sxs-lookup"><span data-stu-id="e26ef-116">Getting started</span></span>

<span data-ttu-id="e26ef-117">若要开始在 ASP.NET 应用程序中使用 AngularJS，你必须安装属于你的项目，或从内容传送网络 (CDN) 中引用它。</span><span class="sxs-lookup"><span data-stu-id="e26ef-117">To start using AngularJS in your ASP.NET application, you must either install it as part of your project, or reference it from a content delivery network (CDN).</span></span>

### <a name="installation"></a><span data-ttu-id="e26ef-118">安装</span><span class="sxs-lookup"><span data-stu-id="e26ef-118">Installation</span></span>

<span data-ttu-id="e26ef-119">有多种方法来将 AngularJS 添加到你的应用程序。</span><span class="sxs-lookup"><span data-stu-id="e26ef-119">There are several ways to add AngularJS to your application.</span></span> <span data-ttu-id="e26ef-120">如果你在 Visual Studio 中开始新的 ASP.NET 核心 web 应用程序，则可以添加使用内置的 AngularJS [Bower](bower.md)支持。</span><span class="sxs-lookup"><span data-stu-id="e26ef-120">If you’re starting a new ASP.NET Core web application in Visual Studio, you can add AngularJS using the built-in [Bower](bower.md) support.</span></span> <span data-ttu-id="e26ef-121">打开*bower.json*，并添加到一个条目`dependencies`属性：</span><span class="sxs-lookup"><span data-stu-id="e26ef-121">Open *bower.json*, and add an entry to the `dependencies` property:</span></span>

<a name=angular-bower-json></a>

<span data-ttu-id="e26ef-122">[!code-json[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/bower.json?highlight=9)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-122">[!code-json[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/bower.json?highlight=9)]</span></span>

<span data-ttu-id="e26ef-123">保存后*bower.json*文件，角将安装在你的项目的*wwwroot/lib*文件夹。</span><span class="sxs-lookup"><span data-stu-id="e26ef-123">Upon saving the *bower.json* file, Angular will be installed in your project's *wwwroot/lib* folder.</span></span> <span data-ttu-id="e26ef-124">此外中, 会列出`Dependencies/Bower`文件夹。</span><span class="sxs-lookup"><span data-stu-id="e26ef-124">Additionally, it will be listed within the `Dependencies/Bower` folder.</span></span> <span data-ttu-id="e26ef-125">请参阅下面的屏幕截图。</span><span class="sxs-lookup"><span data-stu-id="e26ef-125">See the screenshot below.</span></span>

![使用 AngularJS 项目的解决方案资源管理器](angular/_static/angular-solution-explorer.png)

<span data-ttu-id="e26ef-127">接下来，添加`<script>`参考底部`<body>`的 HTML 页的部分或*_Layout.cshtml*文件，如下所示：</span><span class="sxs-lookup"><span data-stu-id="e26ef-127">Next, add a `<script>` reference to the bottom of the `<body>` section of your HTML page or *_Layout.cshtml* file, as shown here:</span></span>

<span data-ttu-id="e26ef-128">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Shared/_Layout.cshtml?highlight=4&range=48-52)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-128">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Shared/_Layout.cshtml?highlight=4&range=48-52)]</span></span>

<span data-ttu-id="e26ef-129">建议生产应用程序，如 AngularJS 的公共库利用 Cdn。</span><span class="sxs-lookup"><span data-stu-id="e26ef-129">It's recommended that production applications utilize CDNs for common libraries like AngularJS.</span></span> <span data-ttu-id="e26ef-130">你可以从多个 Cdn，例如这个之一来引用 AngularJS:</span><span class="sxs-lookup"><span data-stu-id="e26ef-130">You can reference AngularJS from one of several CDNs, such as this one:</span></span>

<span data-ttu-id="e26ef-131">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Shared/_Layout.cshtml?highlight=10&range=53-67)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-131">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Shared/_Layout.cshtml?highlight=10&range=53-67)]</span></span>

<span data-ttu-id="e26ef-132">一旦对引用*angular.js*你准备好开始使用 AngularJS 在网页中的脚本文件。</span><span class="sxs-lookup"><span data-stu-id="e26ef-132">Once you have a reference to the *angular.js* script file, you're ready to begin using AngularJS in your web pages.</span></span>

## <a name="key-components"></a><span data-ttu-id="e26ef-133">关键组件</span><span class="sxs-lookup"><span data-stu-id="e26ef-133">Key components</span></span>

<span data-ttu-id="e26ef-134">AngularJS 包括大量的主要组件，如*指令*，*模板*，*中继器*，*模块*， *控制器*，*组件*，*组件路由器*和的详细信息。</span><span class="sxs-lookup"><span data-stu-id="e26ef-134">AngularJS includes a number of major components, such as *directives*, *templates*, *repeaters*, *modules*, *controllers*, *components*, *component router* and more.</span></span> <span data-ttu-id="e26ef-135">让我们看一下这些组件如何协同工作以将行为添加到你的网页。</span><span class="sxs-lookup"><span data-stu-id="e26ef-135">Let's examine how these components work together to add behavior to your web pages.</span></span>

### <a name="directives"></a><span data-ttu-id="e26ef-136">指令</span><span class="sxs-lookup"><span data-stu-id="e26ef-136">Directives</span></span>

<span data-ttu-id="e26ef-137">使用 AngularJS[指令](https://docs.angularjs.org/guide/directive)来扩展自定义属性和元素的 HTML。</span><span class="sxs-lookup"><span data-stu-id="e26ef-137">AngularJS uses [directives](https://docs.angularjs.org/guide/directive) to extend HTML with custom attributes and elements.</span></span> <span data-ttu-id="e26ef-138">AngularJS 指令定义通过`data-ng-*`或`ng-*`前缀 (`ng`是短的角度)。</span><span class="sxs-lookup"><span data-stu-id="e26ef-138">AngularJS directives are defined via `data-ng-*` or `ng-*` prefixes (`ng` is short for angular).</span></span> <span data-ttu-id="e26ef-139">有两种类型的 AngularJS 指令：</span><span class="sxs-lookup"><span data-stu-id="e26ef-139">There are two types of AngularJS directives:</span></span>

   1. <span data-ttu-id="e26ef-140">**基元指令**： 这些预定义的由角度团队并且 AngularJS 框架的一部分。</span><span class="sxs-lookup"><span data-stu-id="e26ef-140">**Primitive Directives**: These are predefined by the Angular team and are part of the AngularJS framework.</span></span>

   2. <span data-ttu-id="e26ef-141">**自定义指令**： 这些是你可以定义的自定义指令。</span><span class="sxs-lookup"><span data-stu-id="e26ef-141">**Custom Directives**: These are custom directives that you can define.</span></span>

<span data-ttu-id="e26ef-142">在所有 AngularJS 应用程序中使用的基元指令之一是`ng-app`指令，启动 AngularJS 应用程序。</span><span class="sxs-lookup"><span data-stu-id="e26ef-142">One of the primitive directives used in all AngularJS applications is the `ng-app` directive, which bootstraps the AngularJS application.</span></span> <span data-ttu-id="e26ef-143">此指令可应用于`<body>`标记或正文的子元素。</span><span class="sxs-lookup"><span data-stu-id="e26ef-143">This directive can be applied to the `<body>` tag or to a child element of the body.</span></span> <span data-ttu-id="e26ef-144">我们来看操作部分中的示例。</span><span class="sxs-lookup"><span data-stu-id="e26ef-144">Let's see an example in action.</span></span> <span data-ttu-id="e26ef-145">假设你在 ASP.NET 项目中，可以添加到某一 HTML 文件`wwwroot`文件夹，或添加新的控制器操作和关联的视图。</span><span class="sxs-lookup"><span data-stu-id="e26ef-145">Assuming you're in an ASP.NET project, you can either add an HTML file to the `wwwroot` folder, or add a new controller action and an associated view.</span></span> <span data-ttu-id="e26ef-146">在这种情况下，我添加了一个新`Directives`到操作方法`HomeController.cs`。</span><span class="sxs-lookup"><span data-stu-id="e26ef-146">In this case, I've added a new `Directives` action method to `HomeController.cs`.</span></span> <span data-ttu-id="e26ef-147">关联的视图如下所示：</span><span class="sxs-lookup"><span data-stu-id="e26ef-147">The associated view is shown here:</span></span>

<span data-ttu-id="e26ef-148">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Directives.cshtml?highlight=5,7)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-148">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Directives.cshtml?highlight=5,7)]</span></span>

<span data-ttu-id="e26ef-149">若要使这些示例的相互独立，我现在没有使用共享的布局文件。</span><span class="sxs-lookup"><span data-stu-id="e26ef-149">To keep these samples independent of one another, I'm not using the shared layout file.</span></span> <span data-ttu-id="e26ef-150">你可以看到我们修饰将正文标记与`ng-app`指令来指示在此页是一个 AngularJS 应用程序。</span><span class="sxs-lookup"><span data-stu-id="e26ef-150">You can see that we decorated the body tag with the `ng-app` directive to indicate this page is an AngularJS application.</span></span> <span data-ttu-id="e26ef-151">`{{2+2}}`是你将了解有关在稍后的角度的数据绑定表达式。</span><span class="sxs-lookup"><span data-stu-id="e26ef-151">The `{{2+2}}` is an Angular data binding expression that you will learn more about in a moment.</span></span> <span data-ttu-id="e26ef-152">下面是结果，如果运行此应用程序：</span><span class="sxs-lookup"><span data-stu-id="e26ef-152">Here is the result if you run this application:</span></span>

![简单的角度指令](angular/_static/simple-directive.png)

<span data-ttu-id="e26ef-154">其他基元中 AngularJS 指令包括：</span><span class="sxs-lookup"><span data-stu-id="e26ef-154">Other primitive directives in AngularJS include:</span></span>

<span data-ttu-id="e26ef-155">`ng-controller`确定哪个 JavaScript 控制器绑定到的视图。</span><span class="sxs-lookup"><span data-stu-id="e26ef-155">`ng-controller` Determines which JavaScript controller is bound to which view.</span></span>

<span data-ttu-id="e26ef-156">`ng-model`确定 HTML 元素的属性的值绑定到的模型。</span><span class="sxs-lookup"><span data-stu-id="e26ef-156">`ng-model` Determines the model to which the values of an HTML element's properties are bound.</span></span>

<span data-ttu-id="e26ef-157">`ng-init`用于初始化当前作用域表达式形式的应用程序数据。</span><span class="sxs-lookup"><span data-stu-id="e26ef-157">`ng-init` Used to initialize the application data in the form of an expression for the current scope.</span></span>

<span data-ttu-id="e26ef-158">`ng-if`删除或重新创建基于提供的表达式 truthiness DOM 中给定的 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="e26ef-158">`ng-if` Removes or recreates the given HTML element in the DOM based on the truthiness of the expression provided.</span></span>

<span data-ttu-id="e26ef-159">`ng-repeat`重复访问的数据集的 HTML 给定的块。</span><span class="sxs-lookup"><span data-stu-id="e26ef-159">`ng-repeat` Repeats a given block of HTML over a set of data.</span></span>

<span data-ttu-id="e26ef-160">`ng-show`显示或隐藏基于提供的表达式的给定的 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="e26ef-160">`ng-show` Shows or hides the given HTML element based on the expression provided.</span></span>

<span data-ttu-id="e26ef-161">有关在 AngularJS 中受支持的所有基元指令的完整列表，请参阅[AngularJS 文档网站上的指令的文档部分](https://docs.angularjs.org/api/ng/directive)。</span><span class="sxs-lookup"><span data-stu-id="e26ef-161">For a full list of all primitive directives supported in AngularJS, please refer to the [directive documentation section on the AngularJS documentation website](https://docs.angularjs.org/api/ng/directive).</span></span>

### <a name="data-binding"></a><span data-ttu-id="e26ef-162">数据绑定</span><span class="sxs-lookup"><span data-stu-id="e26ef-162">Data binding</span></span>

<span data-ttu-id="e26ef-163">AngularJS 提供[数据绑定](https://docs.angularjs.org/guide/databinding)支持的-现成使用`ng-bind`指令或数据绑定表达式语法，如`{{expression}}`。</span><span class="sxs-lookup"><span data-stu-id="e26ef-163">AngularJS provides [data binding](https://docs.angularjs.org/guide/databinding) support out-of-the-box using either the `ng-bind` directive or a data binding expression syntax such as `{{expression}}`.</span></span> <span data-ttu-id="e26ef-164">AngularJS 支持双向数据绑定模型中的数据与视图模板在所有时间的同步的保存。</span><span class="sxs-lookup"><span data-stu-id="e26ef-164">AngularJS supports two-way data binding where data from a model is kept in synchronization with a view template at all times.</span></span> <span data-ttu-id="e26ef-165">对视图的任何更改自动反映在模型中。</span><span class="sxs-lookup"><span data-stu-id="e26ef-165">Any changes to the view are automatically reflected in the model.</span></span> <span data-ttu-id="e26ef-166">同样，在模型中的任何更改都会反映在视图中。</span><span class="sxs-lookup"><span data-stu-id="e26ef-166">Likewise, any changes in the model are reflected in the view.</span></span>

<span data-ttu-id="e26ef-167">使用名为的随附视图创建某一 HTML 文件或的控制器操作`Databinding`。</span><span class="sxs-lookup"><span data-stu-id="e26ef-167">Create either an HTML file or a controller action with an accompanying view named `Databinding`.</span></span> <span data-ttu-id="e26ef-168">该视图中包括以下内容：</span><span class="sxs-lookup"><span data-stu-id="e26ef-168">Include the following in the view:</span></span>

<span data-ttu-id="e26ef-169">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Databinding.cshtml?highlight=8,9,10)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-169">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Databinding.cshtml?highlight=8,9,10)]</span></span>

<span data-ttu-id="e26ef-170">请注意，你可以显示使用指令或数据绑定模型值 (`ng-bind`)。</span><span class="sxs-lookup"><span data-stu-id="e26ef-170">Notice that you can display model values using either directives or data binding (`ng-bind`).</span></span> <span data-ttu-id="e26ef-171">则结果页应如下所示：</span><span class="sxs-lookup"><span data-stu-id="e26ef-171">The resulting page should look like this:</span></span>

![简单数据绑定](angular/_static/simple-databinding.png)

### <a name="templates"></a><span data-ttu-id="e26ef-173">模板</span><span class="sxs-lookup"><span data-stu-id="e26ef-173">Templates</span></span>

<span data-ttu-id="e26ef-174">[模板](https://docs.angularjs.org/guide/templates)AngularJS 中是普通的 HTML 页使用了修饰 AngularJS 指令和项目。</span><span class="sxs-lookup"><span data-stu-id="e26ef-174">[Templates](https://docs.angularjs.org/guide/templates) in AngularJS are just plain HTML pages decorated with AngularJS directives and artifacts.</span></span> <span data-ttu-id="e26ef-175">AngularJS 中的模板是指令、 表达式、 筛选和组合使用的 HTML 以窗体视图的控件的混合。</span><span class="sxs-lookup"><span data-stu-id="e26ef-175">A template in AngularJS is a mixture of directives, expressions, filters, and controls that combine with HTML to form the view.</span></span>

<span data-ttu-id="e26ef-176">添加另一个视图，以演示模板，并向其中添加以下：</span><span class="sxs-lookup"><span data-stu-id="e26ef-176">Add another view to demonstrate templates, and add the following to it:</span></span>

<span data-ttu-id="e26ef-177">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Templates.cshtml?highlight=8,9,10)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-177">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Templates.cshtml?highlight=8,9,10)]</span></span>

<span data-ttu-id="e26ef-178">该模板具有类似的 AngularJS 指令`ng-app`， `ng-init`，`ng-model`和数据绑定表达式语法以绑定`personName`属性。</span><span class="sxs-lookup"><span data-stu-id="e26ef-178">The template has AngularJS directives like `ng-app`, `ng-init`, `ng-model` and data binding expression syntax to bind the `personName` property.</span></span> <span data-ttu-id="e26ef-179">视图在浏览器中运行，类似于下面的屏幕截图：</span><span class="sxs-lookup"><span data-stu-id="e26ef-179">Running in the browser, the view looks like the screenshot below:</span></span>

![简单的模板示例 1](angular/_static/simple-templates-1.png)

<span data-ttu-id="e26ef-181">如果通过在输入字段中键入更改名称，你将看到的文本输入字段旁边动态更新，显示角度双向数据绑定操作中。</span><span class="sxs-lookup"><span data-stu-id="e26ef-181">If you change the name by typing in the input field, you will see the text next to the input field dynamically update, showing Angular two-way data binding in action.</span></span>

![简单的模板示例 2](angular/_static/simple-templates-2.png)

### <a name="expressions"></a><span data-ttu-id="e26ef-183">表达式</span><span class="sxs-lookup"><span data-stu-id="e26ef-183">Expressions</span></span>

<span data-ttu-id="e26ef-184">[表达式](https://docs.angularjs.org/guide/expression)AngularJS 中是类似于 JavaScript 的代码段内写入`{{ expression }}`语法。</span><span class="sxs-lookup"><span data-stu-id="e26ef-184">[Expressions](https://docs.angularjs.org/guide/expression) in AngularJS are JavaScript-like code snippets that are written inside the `{{ expression }}` syntax.</span></span> <span data-ttu-id="e26ef-185">这些表达式中的数据绑定到 HTML 与相同的方式`ng-bind`指令。</span><span class="sxs-lookup"><span data-stu-id="e26ef-185">The data from these expressions is bound to HTML the same way as `ng-bind` directives.</span></span> <span data-ttu-id="e26ef-186">AngularJS 表达式和 JavaScript 的正则表达式之间的主要区别是表达式针对进行了评估该 AngularJS `$scope` AngularJS 中的对象。</span><span class="sxs-lookup"><span data-stu-id="e26ef-186">The main difference between AngularJS expressions and regular JavaScript expressions is that AngularJS expressions are evaluated against the `$scope` object in AngularJS.</span></span>

<span data-ttu-id="e26ef-187">绑定下面的示例中的 AngularJS 表达式`personName`和简单 JavaScript 计算表达式：</span><span class="sxs-lookup"><span data-stu-id="e26ef-187">The AngularJS expressions in the sample below bind `personName` and a simple JavaScript calculated expression:</span></span>

<span data-ttu-id="e26ef-188">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Expressions.cshtml?highlight=8,9,10)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-188">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Expressions.cshtml?highlight=8,9,10)]</span></span>

<span data-ttu-id="e26ef-189">在浏览器将显示运行该示例`personName`数据和计算的结果：</span><span class="sxs-lookup"><span data-stu-id="e26ef-189">The example running in the browser displays the `personName` data and the results of the calculation:</span></span>

![简单表达式](angular/_static/simple-expressions.png)

### <a name="repeaters"></a><span data-ttu-id="e26ef-191">中继器</span><span class="sxs-lookup"><span data-stu-id="e26ef-191">Repeaters</span></span>

<span data-ttu-id="e26ef-192">重复 AngularJS 中是通过调用基元指令`ng-repeat`。</span><span class="sxs-lookup"><span data-stu-id="e26ef-192">Repeating in AngularJS is done via a primitive directive called `ng-repeat`.</span></span> <span data-ttu-id="e26ef-193">`ng-repeat`指令重复通过重复的数据数组的长度在视图中给定的 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="e26ef-193">The `ng-repeat` directive repeats a given HTML element in a view over the length of a repeating data array.</span></span> <span data-ttu-id="e26ef-194">在 AngularJS 中继器可以重复对字符串或对象的数组。</span><span class="sxs-lookup"><span data-stu-id="e26ef-194">Repeaters in AngularJS can repeat over an array of strings or objects.</span></span> <span data-ttu-id="e26ef-195">下面是一个字符串数组通过重复的示例用法：</span><span class="sxs-lookup"><span data-stu-id="e26ef-195">Here is a sample usage of repeating over an array of strings:</span></span>

<span data-ttu-id="e26ef-196">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters.cshtml?highlight=8,10,11)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-196">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters.cshtml?highlight=8,10,11)]</span></span>

<span data-ttu-id="e26ef-197">[Repeat 指令](https://docs.angularjs.org/api/ng/directive/ngRepeat)输出一系列的列表项，在未排序的列表中，在此屏幕截图所示的开发人员工具中可以看到：</span><span class="sxs-lookup"><span data-stu-id="e26ef-197">The [repeat directive](https://docs.angularjs.org/api/ng/directive/ngRepeat) outputs a series of list items in an unordered list, as you can see in the developer tools shown in this screenshot:</span></span>

![转发器示例](angular/_static/repeater.png)

<span data-ttu-id="e26ef-199">下面是示例重复通过对象的数组。</span><span class="sxs-lookup"><span data-stu-id="e26ef-199">Here is an example that repeats over an array of objects.</span></span> <span data-ttu-id="e26ef-200">`ng-init`指令建立`names`数组，其中每个元素是一个包含第一次的对象的姓氏。</span><span class="sxs-lookup"><span data-stu-id="e26ef-200">The `ng-init` directive establishes a `names` array, where each element is an object containing first and last names.</span></span> <span data-ttu-id="e26ef-201">`ng-repeat`分配， `name in names`，输出每个数组元素的一个列表项。</span><span class="sxs-lookup"><span data-stu-id="e26ef-201">The `ng-repeat` assignment, `name in names`, outputs a list item for every array element.</span></span>

<span data-ttu-id="e26ef-202">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters2.cshtml?highlight=8,9,10,11,13,14)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-202">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters2.cshtml?highlight=8,9,10,11,13,14)]</span></span>

<span data-ttu-id="e26ef-203">在这种情况下，输出是与前面的示例中的相同。</span><span class="sxs-lookup"><span data-stu-id="e26ef-203">The output in this case is the same as in the previous example.</span></span>

<span data-ttu-id="e26ef-204">角提供了一些可以帮助提供基于其中循环是在其执行中的行为的其他指令。</span><span class="sxs-lookup"><span data-stu-id="e26ef-204">Angular provides some additional directives that can help provide behavior based on where the loop is in its execution.</span></span>

`$index`

<span data-ttu-id="e26ef-205">使用`$index`中`ng-repeat`循环，以确定哪个索引定位你循环当前位于。</span><span class="sxs-lookup"><span data-stu-id="e26ef-205">Use `$index` in the `ng-repeat` loop to determine which index position your loop currently is on.</span></span>

<span data-ttu-id="e26ef-206">`$even` 和 `$odd`</span><span class="sxs-lookup"><span data-stu-id="e26ef-206">`$even` and `$odd`</span></span>

<span data-ttu-id="e26ef-207">使用`$even`中`ng-repeat`循环，以确定你循环中的当前索引是否即使索引的行。</span><span class="sxs-lookup"><span data-stu-id="e26ef-207">Use `$even` in the `ng-repeat` loop to determine whether the current index in your loop is an even indexed row.</span></span> <span data-ttu-id="e26ef-208">同样，使用`$odd`以确定当前的索引是否为奇数的索引的行。</span><span class="sxs-lookup"><span data-stu-id="e26ef-208">Similarly, use `$odd` to determine if the current index is an odd indexed row.</span></span>

<span data-ttu-id="e26ef-209">`$first` 和 `$last`</span><span class="sxs-lookup"><span data-stu-id="e26ef-209">`$first` and `$last`</span></span>

<span data-ttu-id="e26ef-210">使用`$first`中`ng-repeat`循环，以判定在循环中的当前索引是否为第一个行。</span><span class="sxs-lookup"><span data-stu-id="e26ef-210">Use `$first` in the `ng-repeat` loop to determine whether the current index in your loop is the first row.</span></span> <span data-ttu-id="e26ef-211">同样，使用`$last`若要确定是否当前的索引的最后一行。</span><span class="sxs-lookup"><span data-stu-id="e26ef-211">Similarly, use `$last` to determine if the current index is the last row.</span></span>

<span data-ttu-id="e26ef-212">下面是一个示例，演示`$index`， `$even`， `$odd`， `$first`，和`$last`中操作：</span><span class="sxs-lookup"><span data-stu-id="e26ef-212">Below is a sample that shows `$index`, `$even`, `$odd`, `$first`, and `$last` in action:</span></span>

<span data-ttu-id="e26ef-213">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters3.cshtml?highlight=14,15,16,17,18)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-213">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters3.cshtml?highlight=14,15,16,17,18)]</span></span>

<span data-ttu-id="e26ef-214">下面是生成的输出：</span><span class="sxs-lookup"><span data-stu-id="e26ef-214">Here is the resulting output:</span></span>

![转发器示例 2](angular/_static/repeaters2.png)

### <a name="scope"></a><span data-ttu-id="e26ef-216">$scope</span><span class="sxs-lookup"><span data-stu-id="e26ef-216">$scope</span></span>

<span data-ttu-id="e26ef-217">`$scope`是充当粘附视图 （模板） 和 （如下所述） 的控制器之间的 JavaScript 对象。</span><span class="sxs-lookup"><span data-stu-id="e26ef-217">`$scope` is a JavaScript object that acts as glue between the view (template) and the controller (explained below).</span></span> <span data-ttu-id="e26ef-218">有关附加到的值仅知道 AngularJS 中的视图模板`$scope`控制器中的对象。</span><span class="sxs-lookup"><span data-stu-id="e26ef-218">A view template in AngularJS only knows about the values attached to the `$scope` object in the controller.</span></span>

> [!NOTE]
> <span data-ttu-id="e26ef-219">MVVM 世界`$scope`中 AngularJS 对象通常定义为视图模型。</span><span class="sxs-lookup"><span data-stu-id="e26ef-219">In the MVVM world, the `$scope` object in AngularJS is often defined as the ViewModel.</span></span> <span data-ttu-id="e26ef-220">AngularJS 团队指`$scope`对象作为数据模型。</span><span class="sxs-lookup"><span data-stu-id="e26ef-220">The AngularJS team refers to the `$scope` object as the Data-Model.</span></span> <span data-ttu-id="e26ef-221">[了解有关 AngularJS 中的作用域](https://docs.angularjs.org/guide/scope)。</span><span class="sxs-lookup"><span data-stu-id="e26ef-221">[Learn more about Scopes in AngularJS](https://docs.angularjs.org/guide/scope).</span></span>

<span data-ttu-id="e26ef-222">下面是一个简单的示例演示如何设置属性`$scope`内一个单独的 JavaScript 文件， *scope.js*:</span><span class="sxs-lookup"><span data-stu-id="e26ef-222">Below is a simple example showing how to set properties on `$scope` within a separate JavaScript file, *scope.js*:</span></span>

<span data-ttu-id="e26ef-223">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/scope.js?highlight=2,3)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-223">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/scope.js?highlight=2,3)]</span></span>

<span data-ttu-id="e26ef-224">观察`$scope`参数传递到控制器在第 2 行上。</span><span class="sxs-lookup"><span data-stu-id="e26ef-224">Observe the `$scope` parameter passed to the controller on line 2.</span></span> <span data-ttu-id="e26ef-225">此对象是视图的了解。</span><span class="sxs-lookup"><span data-stu-id="e26ef-225">This object is what the view knows about.</span></span> <span data-ttu-id="e26ef-226">在一行 3，我们设置一个名为"name"到"Mary Jane"属性。</span><span class="sxs-lookup"><span data-stu-id="e26ef-226">On line 3, we are setting a property called "name" to "Mary Jane".</span></span>

<span data-ttu-id="e26ef-227">由视图找不到的特定属性时，会发生什么情况？</span><span class="sxs-lookup"><span data-stu-id="e26ef-227">What happens when a particular property is not found by the view?</span></span> <span data-ttu-id="e26ef-228">下面定义的视图引用的是"name"和"age"属性：</span><span class="sxs-lookup"><span data-stu-id="e26ef-228">The view defined below refers to "name" and "age" properties:</span></span>

<span data-ttu-id="e26ef-229">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Scope.cshtml?highlight=9,10,14)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-229">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Scope.cshtml?highlight=9,10,14)]</span></span>

<span data-ttu-id="e26ef-230">请注意，在第 9 我们会要求角显示"name"属性使用表达式语法行。</span><span class="sxs-lookup"><span data-stu-id="e26ef-230">Notice on line 9 that we are asking Angular to show the "name" property using expression syntax.</span></span> <span data-ttu-id="e26ef-231">此行 10 然后引用"时间"，不存在的属性。</span><span class="sxs-lookup"><span data-stu-id="e26ef-231">Line 10 then refers to "age", a property that does not exist.</span></span> <span data-ttu-id="e26ef-232">运行示例显示期限设置为"Mary Jane"和执行任何操作的名称。</span><span class="sxs-lookup"><span data-stu-id="e26ef-232">The running example shows the name set to "Mary Jane" and nothing for age.</span></span> <span data-ttu-id="e26ef-233">将忽略缺少的属性。</span><span class="sxs-lookup"><span data-stu-id="e26ef-233">Missing properties are ignored.</span></span>

![作用域示例](angular/_static/scope.png)

### <a name="modules"></a><span data-ttu-id="e26ef-235">模块</span><span class="sxs-lookup"><span data-stu-id="e26ef-235">Modules</span></span>

<span data-ttu-id="e26ef-236">A[模块](https://docs.angularjs.org/guide/module)AngularJS 中是控制器、 服务、 指令、 等的集合。`angular.module()`函数调用用于创建、 注册和检索 AngularJS 中的模块。</span><span class="sxs-lookup"><span data-stu-id="e26ef-236">A [module](https://docs.angularjs.org/guide/module) in AngularJS is a collection of controllers, services, directives, etc. The `angular.module()` function call is used to create, register, and retrieve modules in AngularJS.</span></span> <span data-ttu-id="e26ef-237">应使用注册所有模块，包括那些由 AngularJS 团队和第三方库提供`angular.module()`函数。</span><span class="sxs-lookup"><span data-stu-id="e26ef-237">All modules, including those shipped by the AngularJS team and third party libraries, should be registered using the `angular.module()` function.</span></span>

<span data-ttu-id="e26ef-238">下面是代码的演示如何在 AngularJS 中创建新的模块段。</span><span class="sxs-lookup"><span data-stu-id="e26ef-238">Below is a snippet of code that shows how to create a new module in AngularJS.</span></span> <span data-ttu-id="e26ef-239">第一个参数是模块的名称。</span><span class="sxs-lookup"><span data-stu-id="e26ef-239">The first parameter is the name of the module.</span></span> <span data-ttu-id="e26ef-240">第二个参数定义上其他模块依赖项。</span><span class="sxs-lookup"><span data-stu-id="e26ef-240">The second parameter defines dependencies on other modules.</span></span> <span data-ttu-id="e26ef-241">更高版本在本文中，我们将演示如何将传递到这些依赖关系`angular.module()`方法调用。</span><span class="sxs-lookup"><span data-stu-id="e26ef-241">Later in this article, we will be showing how to pass these dependencies to an `angular.module()` method call.</span></span>

```javascript
var personApp = angular.module('personApp', []);
```

<span data-ttu-id="e26ef-242">使用`ng-app`指令来表示在页上的 AngularJS 模块。</span><span class="sxs-lookup"><span data-stu-id="e26ef-242">Use the `ng-app` directive to represent an AngularJS module on the page.</span></span> <span data-ttu-id="e26ef-243">若要使用模块，请将该模块，该名称分配`personApp`在此示例中，到`ng-app`指令在我们的模板。</span><span class="sxs-lookup"><span data-stu-id="e26ef-243">To use a module, assign the name of the module, `personApp` in this example, to the `ng-app` directive in our template.</span></span>

```html
<body ng-app="personApp">
```

### <a name="controllers"></a><span data-ttu-id="e26ef-244">控制器</span><span class="sxs-lookup"><span data-stu-id="e26ef-244">Controllers</span></span>

<span data-ttu-id="e26ef-245">[控制器](https://docs.angularjs.org/guide/controller)AngularJS 中是为你的代码的条目的第一个点。</span><span class="sxs-lookup"><span data-stu-id="e26ef-245">[Controllers](https://docs.angularjs.org/guide/controller) in AngularJS are the first point of entry for your code.</span></span> <span data-ttu-id="e26ef-246">`<module name>.controller()`函数调用用于创建和注册在 AngularJS 控制器。</span><span class="sxs-lookup"><span data-stu-id="e26ef-246">The `<module name>.controller()` function call is used to create and register controllers in AngularJS.</span></span> <span data-ttu-id="e26ef-247">`ng-controller`指令用于表示 HTML 页上的 AngularJS 控制器。</span><span class="sxs-lookup"><span data-stu-id="e26ef-247">The `ng-controller` directive is used to represent an AngularJS controller on the HTML page.</span></span> <span data-ttu-id="e26ef-248">角中的控制器角色是将设置状态和数据模型的行为 (`$scope`)。</span><span class="sxs-lookup"><span data-stu-id="e26ef-248">The role of the controller in Angular is to set state and behavior of the data model (`$scope`).</span></span> <span data-ttu-id="e26ef-249">控制器不应该用于直接处理的 DOM。</span><span class="sxs-lookup"><span data-stu-id="e26ef-249">Controllers should not be used to manipulate the DOM directly.</span></span>

<span data-ttu-id="e26ef-250">下面是代码的注册新的控制器段。</span><span class="sxs-lookup"><span data-stu-id="e26ef-250">Below is a snippet of code that registers a new controller.</span></span> <span data-ttu-id="e26ef-251">`personApp`代码段中的变量引用一个角度模块，请在第 2 行定义。</span><span class="sxs-lookup"><span data-stu-id="e26ef-251">The `personApp` variable in the snippet references an Angular module, which is defined on line 2.</span></span>

<span data-ttu-id="e26ef-252">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/controllers.js?highlight=2,5)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-252">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/controllers.js?highlight=2,5)]</span></span>

<span data-ttu-id="e26ef-253">视图使用`ng-controller`指令将分配的控制器名称：</span><span class="sxs-lookup"><span data-stu-id="e26ef-253">The view using the `ng-controller` directive assigns the controller name:</span></span>

<span data-ttu-id="e26ef-254">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Controllers.cshtml?highlight=8,14)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-254">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Controllers.cshtml?highlight=8,14)]</span></span>

<span data-ttu-id="e26ef-255">该页面显示"Mary"和"Jane"对应于`firstName`和`lastName`属性附加到`$scope`对象：</span><span class="sxs-lookup"><span data-stu-id="e26ef-255">The page shows "Mary" and "Jane" that correspond to the `firstName` and `lastName` properties attached to the `$scope` object:</span></span>

![控制器示例](angular/_static/controllers.png)

### <a name="components"></a><span data-ttu-id="e26ef-257">组件数</span><span class="sxs-lookup"><span data-stu-id="e26ef-257">Components</span></span>

<span data-ttu-id="e26ef-258">[组件](https://docs.angularjs.org/guide/component)在角 1.5.x 允许封装和创建单独的 HTML 元素的功能。</span><span class="sxs-lookup"><span data-stu-id="e26ef-258">[Components](https://docs.angularjs.org/guide/component) in Angular 1.5.x allow for the encapsulation and capability of creating individual HTML elements.</span></span> <span data-ttu-id="e26ef-259">角度 1.4.x 中无法实现使用.directive() 方法的相同功能。</span><span class="sxs-lookup"><span data-stu-id="e26ef-259">In Angular 1.4.x you could achieve the same feature using the .directive() method.</span></span>

<span data-ttu-id="e26ef-260">通过使用.component() 方法，开发简化获得指令和控制器的功能。</span><span class="sxs-lookup"><span data-stu-id="e26ef-260">By using the .component() method, development is simplified gaining the functionality of the directive and the controller.</span></span> <span data-ttu-id="e26ef-261">其他好处包括：作用域隔离，最佳做法是固有的并迁移到角度 2 变得更轻松的任务。</span><span class="sxs-lookup"><span data-stu-id="e26ef-261">Other benefits include; scope isolation, best practices are inherent, and migration to Angular 2 becomes an easier task.</span></span> <span data-ttu-id="e26ef-262">`<module name>.component()`函数调用用于创建和在 AngularJS 中注册组件。</span><span class="sxs-lookup"><span data-stu-id="e26ef-262">The `<module name>.component()` function call is used to create and register components in AngularJS.</span></span>

<span data-ttu-id="e26ef-263">下面是代码的注册新的组件段。</span><span class="sxs-lookup"><span data-stu-id="e26ef-263">Below is a snippet of code that registers a new component.</span></span> <span data-ttu-id="e26ef-264">`personApp`代码段中的变量引用一个角度模块，请在第 2 行定义。</span><span class="sxs-lookup"><span data-stu-id="e26ef-264">The `personApp` variable in the snippet references an Angular module, which is defined on line 2.</span></span>

<span data-ttu-id="e26ef-265">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/components.js?highlight=2,5,13)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-265">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/components.js?highlight=2,5,13)]</span></span>

<span data-ttu-id="e26ef-266">我们将在其中显示的自定义的 HTML 元素的视图。</span><span class="sxs-lookup"><span data-stu-id="e26ef-266">The view where we are displaying the custom HTML element.</span></span>

<span data-ttu-id="e26ef-267">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Components.cshtml?highlight=8)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-267">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Components.cshtml?highlight=8)]</span></span>

<span data-ttu-id="e26ef-268">使用组件的关联的模板：</span><span class="sxs-lookup"><span data-stu-id="e26ef-268">The associated template used by component:</span></span>

<span data-ttu-id="e26ef-269">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/partials/personcomponent.html?highlight=2,3)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-269">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/partials/personcomponent.html?highlight=2,3)]</span></span>

<span data-ttu-id="e26ef-270">该页面显示"Aftab"和"Ansari"对应于`firstName`和`lastName`属性附加到`vm`对象：</span><span class="sxs-lookup"><span data-stu-id="e26ef-270">The page shows "Aftab" and "Ansari" that correspond to the `firstName` and `lastName` properties attached to the `vm` object:</span></span>

![组件的示例](angular/_static/components.png)

### <a name="services"></a><span data-ttu-id="e26ef-272">服务</span><span class="sxs-lookup"><span data-stu-id="e26ef-272">Services</span></span>

<span data-ttu-id="e26ef-273">[服务](https://docs.angularjs.org/guide/services)中 AngularJS 通常用于为一个可在整个角度的应用程序的生存期的文件抽象出来的共享代码。</span><span class="sxs-lookup"><span data-stu-id="e26ef-273">[Services](https://docs.angularjs.org/guide/services) in AngularJS are commonly used for shared code that is abstracted away into a file which can be used throughout the lifetime of an Angular application.</span></span> <span data-ttu-id="e26ef-274">服务惰式实例化，这意味着，不服务的实例，除非获取使用依赖于服务的组件。</span><span class="sxs-lookup"><span data-stu-id="e26ef-274">Services are lazily instantiated, meaning that there will not be an instance of a service unless a component that depends on the service gets used.</span></span> <span data-ttu-id="e26ef-275">工厂是服务的使用 AngularJS 应用程序中的一个示例。</span><span class="sxs-lookup"><span data-stu-id="e26ef-275">Factories are an example of a service used in AngularJS applications.</span></span> <span data-ttu-id="e26ef-276">使用创建工厂`myApp.factory()`函数调用，其中`myApp`是模块。</span><span class="sxs-lookup"><span data-stu-id="e26ef-276">Factories are created using the `myApp.factory()` function call, where `myApp` is the module.</span></span>

<span data-ttu-id="e26ef-277">下面是一个示例，演示如何使用中 AngularJS 工厂：</span><span class="sxs-lookup"><span data-stu-id="e26ef-277">Below is an example that shows how to use factories in AngularJS:</span></span>

<span data-ttu-id="e26ef-278">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/simpleFactory.js?highlight=1)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-278">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/simpleFactory.js?highlight=1)]</span></span>

<span data-ttu-id="e26ef-279">若要从控制器中调用此工厂，传入`personFactory`作为参数传递给`controller`函数：</span><span class="sxs-lookup"><span data-stu-id="e26ef-279">To call this factory from the controller, pass `personFactory` as a parameter to the `controller` function:</span></span>

```javascript
personApp.controller('personController', function($scope,personFactory) {
  $scope.name = personFactory.getName();
});
```

### <a name="using-services-to-talk-to-a-rest-endpoint"></a><span data-ttu-id="e26ef-280">使用服务进行对话的 REST 终结点</span><span class="sxs-lookup"><span data-stu-id="e26ef-280">Using services to talk to a REST endpoint</span></span>

<span data-ttu-id="e26ef-281">下面是一个端到端示例使用服务在 AngularJS 与 ASP.NET 核心 Web API 终结点进行交互。</span><span class="sxs-lookup"><span data-stu-id="e26ef-281">Below is an end-to-end example using services in AngularJS to interact with an ASP.NET Core Web API endpoint.</span></span> <span data-ttu-id="e26ef-282">该示例从 Web API 获取数据，并显示一个视图模板中的数据。</span><span class="sxs-lookup"><span data-stu-id="e26ef-282">The example gets data from the Web API and displays the data in a view template.</span></span> <span data-ttu-id="e26ef-283">让我们首先启动与视图：</span><span class="sxs-lookup"><span data-stu-id="e26ef-283">Let's start with the view first:</span></span>

<span data-ttu-id="e26ef-284">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Index.cshtml?highlight=5,8,10,17,18,19)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-284">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Index.cshtml?highlight=5,8,10,17,18,19)]</span></span>

<span data-ttu-id="e26ef-285">在此视图中，我们具有调用的角度模块`PersonsApp`和控制器调用`personController`。</span><span class="sxs-lookup"><span data-stu-id="e26ef-285">In this view, we have an Angular module called `PersonsApp` and a controller called `personController`.</span></span> <span data-ttu-id="e26ef-286">我们将使用`ng-repeat`以循环访问的人员列表。</span><span class="sxs-lookup"><span data-stu-id="e26ef-286">We are using `ng-repeat` to iterate over the list of persons.</span></span> <span data-ttu-id="e26ef-287">我们正在引用三个行 17-19 的自定义 JavaScript 文件。</span><span class="sxs-lookup"><span data-stu-id="e26ef-287">We are referencing three custom JavaScript files on lines 17-19.</span></span>

<span data-ttu-id="e26ef-288">*PersonApp.js*文件用于注册`PersonsApp`模块中; 以及的语法是类似于前面的示例。</span><span class="sxs-lookup"><span data-stu-id="e26ef-288">The *personApp.js* file is used to register the `PersonsApp` module; and, the syntax is similar to previous examples.</span></span> <span data-ttu-id="e26ef-289">我们将使用`angular.module`函数来创建我们将使用的模块的新实例。</span><span class="sxs-lookup"><span data-stu-id="e26ef-289">We are using the `angular.module` function to create a new instance of the module that we will be working with.</span></span>

<span data-ttu-id="e26ef-290">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personApp.js?highlight=3)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-290">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personApp.js?highlight=3)]</span></span>

<span data-ttu-id="e26ef-291">让我们看一看*personFactory.js*下面。</span><span class="sxs-lookup"><span data-stu-id="e26ef-291">Let's take a look at *personFactory.js*, below.</span></span> <span data-ttu-id="e26ef-292">我们正在呼叫模块的`factory`方法来创建一个工厂。</span><span class="sxs-lookup"><span data-stu-id="e26ef-292">We are calling the module’s `factory` method to create a factory.</span></span> <span data-ttu-id="e26ef-293">第 12 行显示内置角`$http`服务从 web 服务检索用户信息。</span><span class="sxs-lookup"><span data-stu-id="e26ef-293">Line 12 shows the built-in Angular `$http` service retrieving people information from a web service.</span></span>

<span data-ttu-id="e26ef-294">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personFactory.js?highlight=6,7,12)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-294">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personFactory.js?highlight=6,7,12)]</span></span>

<span data-ttu-id="e26ef-295">在*personController.js*，我们正在呼叫模块的`controller`方法可创建控制器。</span><span class="sxs-lookup"><span data-stu-id="e26ef-295">In *personController.js*, we are calling the module’s `controller` method to create the controller.</span></span> <span data-ttu-id="e26ef-296">`$scope`对象的`people`属性分配从 personFactory （行 13） 返回的数据。</span><span class="sxs-lookup"><span data-stu-id="e26ef-296">The `$scope` object's `people` property is assigned the data returned from the personFactory (line 13).</span></span>

<span data-ttu-id="e26ef-297">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personController.js?highlight=6,7,13)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-297">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personController.js?highlight=6,7,13)]</span></span>

<span data-ttu-id="e26ef-298">让我们快速了解一下 Web API 和其后面的模型。</span><span class="sxs-lookup"><span data-stu-id="e26ef-298">Let's take a quick look at the Web API and the model behind it.</span></span> <span data-ttu-id="e26ef-299">`Person`模型是使用 POCO （普通旧 CLR 对象） `Id`， `FirstName`，和`LastName`属性：</span><span class="sxs-lookup"><span data-stu-id="e26ef-299">The `Person` model is a POCO (Plain Old CLR Object) with `Id`, `FirstName`, and `LastName` properties:</span></span>

<span data-ttu-id="e26ef-300">[!code-csharp[Main](angular/sample/AngularJSSample/src/AngularJSSample/Models/Person.cs)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-300">[!code-csharp[Main](angular/sample/AngularJSSample/src/AngularJSSample/Models/Person.cs)]</span></span>

<span data-ttu-id="e26ef-301">`Person`控制器返回 JSON 格式的列表`Person`对象：</span><span class="sxs-lookup"><span data-stu-id="e26ef-301">The `Person` controller returns a JSON-formatted list of `Person` objects:</span></span>

<span data-ttu-id="e26ef-302">[!code-csharp[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Controllers/Api/PersonController.cs?highlight=9,10,19)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-302">[!code-csharp[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Controllers/Api/PersonController.cs?highlight=9,10,19)]</span></span>

<span data-ttu-id="e26ef-303">让我们查看在操作中的应用程序：</span><span class="sxs-lookup"><span data-stu-id="e26ef-303">Let's see the application in action:</span></span>

![控制器显示 REST 结果](angular/_static/rest-bound.png)

<span data-ttu-id="e26ef-305">你可以[在 GitHub 上查看应用程序的结构](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample)。</span><span class="sxs-lookup"><span data-stu-id="e26ef-305">You can [view the application's structure on GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample).</span></span>

> [!NOTE]
> <span data-ttu-id="e26ef-306">有关构建 AngularJS 应用程序的详细信息，请参阅[John 爸爸角度样式指南](https://github.com/johnpapa/angular-styleguide)</span><span class="sxs-lookup"><span data-stu-id="e26ef-306">For more on structuring AngularJS applications, see [John Papa's Angular Style Guide](https://github.com/johnpapa/angular-styleguide)</span></span>

&nbsp;

> [!NOTE]
> <span data-ttu-id="e26ef-307">若要创建 AngularJS 模块、 控制器、 工厂，指令和视图文件轻松，一定要签出 Sayed Hashimi [SideWaffle 模板适用于 Visual Studio 的包](http://sidewaffle.com/)。</span><span class="sxs-lookup"><span data-stu-id="e26ef-307">To create AngularJS module, controller, factory, directive and view files easily, be sure to check out Sayed Hashimi's [SideWaffle template pack for Visual Studio](http://sidewaffle.com/).</span></span> <span data-ttu-id="e26ef-308">Sayed Hashimi 是在 Microsoft 的 Visual Studio Web 团队高级项目经理和 SideWaffle 模板均视为黄金标准。</span><span class="sxs-lookup"><span data-stu-id="e26ef-308">Sayed Hashimi is a Senior Program Manager on the Visual Studio Web Team at Microsoft and SideWaffle templates are considered the gold standard.</span></span> <span data-ttu-id="e26ef-309">在撰写本文时，SideWaffle 是可用于 Visual Studio 2012、 2013年和 2015年。</span><span class="sxs-lookup"><span data-stu-id="e26ef-309">At the time of this writing, SideWaffle is available for Visual Studio 2012, 2013, and 2015.</span></span>

### <a name="routing-and-multiple-views"></a><span data-ttu-id="e26ef-310">路由和多个视图</span><span class="sxs-lookup"><span data-stu-id="e26ef-310">Routing and multiple views</span></span>

<span data-ttu-id="e26ef-311">AngularJS 有内置的路由提供程序来处理基于 SPA （单页面应用程序） 导航。</span><span class="sxs-lookup"><span data-stu-id="e26ef-311">AngularJS has a built-in route provider to handle SPA (Single Page Application) based navigation.</span></span> <span data-ttu-id="e26ef-312">若要使用 AngularJS 中的路由，必须添加`angular-route`使用 Bower 库。</span><span class="sxs-lookup"><span data-stu-id="e26ef-312">To work with routing in AngularJS, you must add the `angular-route` library using Bower.</span></span> <span data-ttu-id="e26ef-313">你可在看到[bower.json](#angular-bower-json)在开始时，我们已在引用它在我们的项目这篇文章中引用的文件。</span><span class="sxs-lookup"><span data-stu-id="e26ef-313">You can see in the [bower.json](#angular-bower-json) file referenced at the start of this article that we are already referencing it in our project.</span></span>

<span data-ttu-id="e26ef-314">安装包后，添加脚本引用 (*角度 route.js*) 向你的视图。</span><span class="sxs-lookup"><span data-stu-id="e26ef-314">After you install the package, add the script reference (*angular-route.js*) to your view.</span></span>

<span data-ttu-id="e26ef-315">现在让我们举个人员应用我们已生成并向其中添加导航。</span><span class="sxs-lookup"><span data-stu-id="e26ef-315">Now let's take the Person App we have been building and add navigation to it.</span></span> <span data-ttu-id="e26ef-316">首先，我们将使通过创建新的应用程序一份`PeopleController`操作调用`Spa`和相应`Spa.cshtml`通过复制中的 Index.cshtml 视图的视图`People`文件夹。</span><span class="sxs-lookup"><span data-stu-id="e26ef-316">First, we will make a copy of the app by creating a new `PeopleController` action called `Spa` and a corresponding `Spa.cshtml` view by copying the Index.cshtml view in the `People` folder.</span></span> <span data-ttu-id="e26ef-317">添加对的脚本引用`angular-route`（请参阅第 11 行）。</span><span class="sxs-lookup"><span data-stu-id="e26ef-317">Add a script reference to `angular-route` (see line 11).</span></span> <span data-ttu-id="e26ef-318">此外添加`div`标有`ng-view`指令 （请参阅第 6 行） 作为一个占位符，以将视图中的。</span><span class="sxs-lookup"><span data-stu-id="e26ef-318">Also add a `div` marked with the `ng-view` directive (see line 6) as a placeholder to place views in.</span></span> <span data-ttu-id="e26ef-319">我们将使用其他几个*.js*行 13-16 引用的文件。</span><span class="sxs-lookup"><span data-stu-id="e26ef-319">We are going to be using several additional *.js* files which are referenced on lines 13-16.</span></span>

<span data-ttu-id="e26ef-320">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Spa.cshtml?highlight=6,11,12,13,14,15,16)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-320">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Spa.cshtml?highlight=6,11,12,13,14,15,16)]</span></span>

<span data-ttu-id="e26ef-321">让我们看一看*personModule.js*文件以查看如何我们实例化具有路由的模块。</span><span class="sxs-lookup"><span data-stu-id="e26ef-321">Let's take a look at *personModule.js* file to see how we are instantiating the module with routing.</span></span> <span data-ttu-id="e26ef-322">我们传递`ngRoute`为到该模块库。</span><span class="sxs-lookup"><span data-stu-id="e26ef-322">We are passing `ngRoute` as a library into the module.</span></span> <span data-ttu-id="e26ef-323">此模块处理我们的应用程序中的路由。</span><span class="sxs-lookup"><span data-stu-id="e26ef-323">This module handles routing in our application.</span></span>

<span data-ttu-id="e26ef-324">[!code-javascript[Main](angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personModule.js)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-324">[!code-javascript[Main](angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personModule.js)]</span></span>

<span data-ttu-id="e26ef-325">*PersonRoutes.js*文件，下面，定义基于路由提供程序的路由。</span><span class="sxs-lookup"><span data-stu-id="e26ef-325">The *personRoutes.js* file, below, defines routes based on the route provider.</span></span> <span data-ttu-id="e26ef-326">行 4-7 通过有效地说，当与 URL 定义导航`/persons`是请求，使用一个称为模板`partials/personlist`通过一起`personListController`。</span><span class="sxs-lookup"><span data-stu-id="e26ef-326">Lines 4-7 define navigation by effectively saying, when a URL with `/persons` is requested, use a template called `partials/personlist` by working through `personListController`.</span></span> <span data-ttu-id="e26ef-327">第 8 11 行指示的路由参数的详细信息页`personId`。</span><span class="sxs-lookup"><span data-stu-id="e26ef-327">Lines 8-11 indicate a detail page with a route parameter of `personId`.</span></span> <span data-ttu-id="e26ef-328">如果 URL 不匹配的模式，角默认为`/persons`视图。</span><span class="sxs-lookup"><span data-stu-id="e26ef-328">If the URL doesn't match one of the patterns, Angular defaults to the `/persons` view.</span></span>

<span data-ttu-id="e26ef-329">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personRoutes.js?highlight=4,5,6,7,8,9,10,11,13)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-329">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personRoutes.js?highlight=4,5,6,7,8,9,10,11,13)]</span></span>

<span data-ttu-id="e26ef-330">`personlist.html`文件是包含仅显示用户列表所需的 HTML 了分部视图。</span><span class="sxs-lookup"><span data-stu-id="e26ef-330">The `personlist.html` file is a partial view containing only the HTML needed to display person list.</span></span>

<span data-ttu-id="e26ef-331">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/partials/personlist.html?highlight=3)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-331">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/partials/personlist.html?highlight=3)]</span></span>

<span data-ttu-id="e26ef-332">通过使用模块的定义控制器`controller`函数中*personListController.js*。</span><span class="sxs-lookup"><span data-stu-id="e26ef-332">The controller is defined by using the module's `controller` function in *personListController.js*.</span></span>

<span data-ttu-id="e26ef-333">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personListController.js?highlight=1)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-333">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personListController.js?highlight=1)]</span></span>

<span data-ttu-id="e26ef-334">如果我们运行此应用程序并导航到`people/spa#/persons`URL，我们将看到：</span><span class="sxs-lookup"><span data-stu-id="e26ef-334">If we run this application and navigate to the `people/spa#/persons` URL, we will see:</span></span>

![人员列表视图](angular/_static/spa-persons.png)

<span data-ttu-id="e26ef-336">如果我们导航到详细信息页中，例如`people/spa#/persons/2`，我们将要看到的详细信息部分视图：</span><span class="sxs-lookup"><span data-stu-id="e26ef-336">If we navigate to a detail page, for example `people/spa#/persons/2`, we will see the detail partial view:</span></span>

![人员详细信息视图](angular/_static/spa-persons-2.png)

<span data-ttu-id="e26ef-338">你可以查看完整的源和上不显示这篇文章中的任何文件[GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample)。</span><span class="sxs-lookup"><span data-stu-id="e26ef-338">You can view the full source and any files not shown in this article on [GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample).</span></span>

### <a name="event-handlers"></a><span data-ttu-id="e26ef-339">事件处理程序</span><span class="sxs-lookup"><span data-stu-id="e26ef-339">Event Handlers</span></span>

<span data-ttu-id="e26ef-340">有大量中将事件处理功能添加到你的 HTML dom。 中的输入元素的 AngularJS 指令</span><span class="sxs-lookup"><span data-stu-id="e26ef-340">There are a number of directives in AngularJS that add event-handling capabilities to the input elements in your HTML DOM.</span></span> <span data-ttu-id="e26ef-341">下面是内置于 AngularJS 的事件的列表。</span><span class="sxs-lookup"><span data-stu-id="e26ef-341">Below is a list of the events that are built into AngularJS.</span></span>

   * `ng-click`

   * `ng-dbl-click`

   * `ng-mousedown`

   * `ng-mouseup`

   * `ng-mouseenter`

   * `ng-mouseleave`

   * `ng-mousemove`

   * `ng-keydown`

   * `ng-keyup`

   * `ng-keypress`

   * `ng-change`

> [!NOTE]
> <span data-ttu-id="e26ef-342">你可以添加自己的事件处理程序使用[AngularJS 中功能的自定义指令](https://docs.angularjs.org/guide/directive)。</span><span class="sxs-lookup"><span data-stu-id="e26ef-342">You can add your own event handlers using the [custom directives feature in AngularJS](https://docs.angularjs.org/guide/directive).</span></span>

<span data-ttu-id="e26ef-343">让我们看一下如何`ng-click`向上有线事件。</span><span class="sxs-lookup"><span data-stu-id="e26ef-343">Let's look at how the `ng-click` event is wired up.</span></span> <span data-ttu-id="e26ef-344">创建一个名为的新 JavaScript 文件*eventHandlerController.js*，并向其中添加以下：</span><span class="sxs-lookup"><span data-stu-id="e26ef-344">Create a new JavaScript file named *eventHandlerController.js*, and add the following to it:</span></span>

<span data-ttu-id="e26ef-345">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/eventHandlerController.js?highlight=5,6,7)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-345">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/eventHandlerController.js?highlight=5,6,7)]</span></span>

<span data-ttu-id="e26ef-346">请注意新`sayName`函数中`eventHandlerController`在行 5 上面。</span><span class="sxs-lookup"><span data-stu-id="e26ef-346">Notice the new `sayName` function in `eventHandlerController` on line 5 above.</span></span> <span data-ttu-id="e26ef-347">所有方法执行的都操作，为现在通过一条欢迎消息向用户显示 JavaScript 警报。</span><span class="sxs-lookup"><span data-stu-id="e26ef-347">All the method is doing for now is showing a JavaScript alert to the user with a welcome message.</span></span>

<span data-ttu-id="e26ef-348">下面的视图将控制器函数绑定到 AngularJS 事件。</span><span class="sxs-lookup"><span data-stu-id="e26ef-348">The view below binds a controller function to an AngularJS event.</span></span> <span data-ttu-id="e26ef-349">第 9 行在其上具有按钮`ng-click`已应用角度指令。</span><span class="sxs-lookup"><span data-stu-id="e26ef-349">Line 9 has a button on which the `ng-click` Angular directive has been applied.</span></span> <span data-ttu-id="e26ef-350">它调用我们`sayName`函数，附加到`$scope`对象传递给此视图。</span><span class="sxs-lookup"><span data-stu-id="e26ef-350">It calls our `sayName` function, which is attached to the `$scope` object passed to this view.</span></span>

<span data-ttu-id="e26ef-351">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Events.cshtml?highlight=9)]</span><span class="sxs-lookup"><span data-stu-id="e26ef-351">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Events.cshtml?highlight=9)]</span></span>

<span data-ttu-id="e26ef-352">运行示例演示控制器的`sayName`时单击该按钮时将自动调用函数。</span><span class="sxs-lookup"><span data-stu-id="e26ef-352">The running example demonstrates that the controller's `sayName` function is called automatically when the button is clicked.</span></span>

![单击事件](angular/_static/events.png)

<span data-ttu-id="e26ef-354">有关 AngularJS 内置的事件处理程序指令的详细信息，请务必开头到[文档网站](https://docs.angularjs.org/api/ng/directive/ngClick)AngularJS。</span><span class="sxs-lookup"><span data-stu-id="e26ef-354">For more detail on AngularJS built-in event handler directives, be sure to head to the [documentation website](https://docs.angularjs.org/api/ng/directive/ngClick) of AngularJS.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e26ef-355">其他资源</span><span class="sxs-lookup"><span data-stu-id="e26ef-355">Additional resources</span></span>

* [<span data-ttu-id="e26ef-356">角度文档</span><span class="sxs-lookup"><span data-stu-id="e26ef-356">Angular Docs</span></span>](https://docs.angularjs.org)

* [<span data-ttu-id="e26ef-357">角度 2 信息</span><span class="sxs-lookup"><span data-stu-id="e26ef-357">Angular 2 Info</span></span>](http://angular.io)
