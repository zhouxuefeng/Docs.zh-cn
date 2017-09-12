---
title: "小于，Sass 和 ASP.NET Core 中出色的字体"
author: ardalis
description: "了解如何在 ASP.NET Core 应用程序中使用较少，Sass，和字体出色。"
keywords: "ASP.NET 核心，小于 Sass、 字体出色、 预处理器"
ms.author: tdykstra
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 94c988f9-95fd-425d-b37e-7f846598c6d4
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/less-sass-fa
ms.openlocfilehash: 128612eb2f7c6c8fdd0cc01f10b8e522df46dcf6
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="introduction-to-styling-applications-with-less-sass-and-font-awesome-in-aspnet-core"></a><span data-ttu-id="51006-104">简介事半功倍的样式应用程序、 Sass，和在 ASP.NET 核心中出色的字体</span><span class="sxs-lookup"><span data-stu-id="51006-104">Introduction to styling applications with Less, Sass, and Font Awesome in ASP.NET Core</span></span>

<span data-ttu-id="51006-105">通过[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="51006-105">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="51006-106">Web 应用程序的用户有越来越高的期望时设置的样式和总体体验。</span><span class="sxs-lookup"><span data-stu-id="51006-106">Users of web applications have increasingly high expectations when it comes to style and overall experience.</span></span> <span data-ttu-id="51006-107">现代 web 应用程序频繁地利用丰富的工具和框架用于定义和管理其外观和感觉以一致的方式。</span><span class="sxs-lookup"><span data-stu-id="51006-107">Modern web applications frequently leverage rich tools and frameworks for defining and managing their look and feel in a consistent manner.</span></span> <span data-ttu-id="51006-108">框架喜欢[Bootstrap](http://getbootstrap.com/)可以地长定义一组通用的样式和网站的布局选项。</span><span class="sxs-lookup"><span data-stu-id="51006-108">Frameworks like [Bootstrap](http://getbootstrap.com/) can go a long way toward defining a common set of styles and layout options for web sites.</span></span> <span data-ttu-id="51006-109">但是，大多数重要站点还受益于能够有效地定义和维护样式和级联样式表 (CSS) 文件，以及能够轻松访问帮助使站点的接口更直观的非图像图标。</span><span class="sxs-lookup"><span data-stu-id="51006-109">However, most non-trivial sites also benefit from being able to effectively define and maintain styles and cascading style sheet (CSS) files, as well as having easy access to non-image icons that help make the site's interface more intuitive.</span></span> <span data-ttu-id="51006-110">就是在此处的支持语言和工具[较少](http://lesscss.org/)和[Sass](http://sass-lang.com/)，库等，以及将[字体出色](http://fontawesome.io/)，进入。</span><span class="sxs-lookup"><span data-stu-id="51006-110">That's where languages and tools that support [Less](http://lesscss.org/) and [Sass](http://sass-lang.com/), and libraries like [Font Awesome](http://fontawesome.io/), come in.</span></span>

## <a name="css-preprocessor-languages"></a><span data-ttu-id="51006-111">CSS 预处理器语言</span><span class="sxs-lookup"><span data-stu-id="51006-111">CSS preprocessor languages</span></span>

<span data-ttu-id="51006-112">为了改进的体验的使用基础的语言中，编译成其它语言的语言被称为预处理器。</span><span class="sxs-lookup"><span data-stu-id="51006-112">Languages that are compiled into other languages, in order to improve the experience of working with the underlying language, are referred to as preprocessors.</span></span> <span data-ttu-id="51006-113">有两个常用的预处理器针对 CSS： 不太和 Sass。</span><span class="sxs-lookup"><span data-stu-id="51006-113">There are two popular preprocessors for CSS: Less and Sass.</span></span>  <span data-ttu-id="51006-114">这些预处理器将功能添加到 CSS，如对变量和嵌套的规则，以改进可维护性的大型、 复杂的样式表的支持。</span><span class="sxs-lookup"><span data-stu-id="51006-114">These preprocessors add features to CSS, such as support for variables and nested rules, which improve the maintainability of large, complex stylesheets.</span></span> <span data-ttu-id="51006-115">作为一种语言的 CSS 是非常基本，缺少那样简单变量，即使对于支持，这往往会重复和臃肿使 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="51006-115">CSS as a language is very basic, lacking support even for something as simple as variables, and this tends to make CSS files repetitive and bloated.</span></span> <span data-ttu-id="51006-116">添加通过预处理器的实际编程语言功能可以帮助减少重复，提供更好地组织样式规则。</span><span class="sxs-lookup"><span data-stu-id="51006-116">Adding real programming language features via preprocessors can help reduce duplication and provide better organization of styling rules.</span></span> <span data-ttu-id="51006-117">Visual Studio 提供这两个较低的内置支持和 Sass，以及可以进一步提高开发体验，使用这些语言时的扩展。</span><span class="sxs-lookup"><span data-stu-id="51006-117">Visual Studio provides built-in support for both Less and Sass, as well as extensions that can further improve the development experience when working with these languages.</span></span>

<span data-ttu-id="51006-118">作为预处理器可以如何改进可读性和可维护性的样式信息的快速示例，请考虑以下 CSS:</span><span class="sxs-lookup"><span data-stu-id="51006-118">As a quick example of how preprocessors can improve readability and maintainability of style information, consider this CSS:</span></span>

```css
.header {
    color: black;
    font-weight: bold;
    font-size: 18px;
    font-family: Helvetica, Arial, sans-serif;
}

.small-header {
    color: black;
    font-weight: bold;
    font-size: 14px;
    font-family: Helvetica, Arial, sans-serif;
}
```

<span data-ttu-id="51006-119">使用更少，这可以重写以消除所有重复，使用*mixin* (这样命名是因为它允许你"中混合使用"从一个类或到另一个规则集的属性):</span><span class="sxs-lookup"><span data-stu-id="51006-119">Using Less, this can be rewritten to eliminate all of the duplication, using a *mixin* (so named because it allows you to "mix in" properties from one class or rule-set into another):</span></span>

```less
.header {
    color: black;
    font-weight: bold;
    font-size: 18px;
    font-family: Helvetica, Arial, sans-serif;
}

.small-header {
    .header;
    font-size: 14px;
}
```

## <a name="less"></a><span data-ttu-id="51006-120">小于</span><span class="sxs-lookup"><span data-stu-id="51006-120">Less</span></span>

<span data-ttu-id="51006-121">使用 Node.js 不太 CSS 预处理器运行。</span><span class="sxs-lookup"><span data-stu-id="51006-121">The Less CSS preprocessor runs using Node.js.</span></span> <span data-ttu-id="51006-122">若要安装小于，使用 Node 程序包管理器 (npm) 从命令提示符 (-g 意味着"全局"):</span><span class="sxs-lookup"><span data-stu-id="51006-122">To install Less, use Node Package Manager (npm) from a command prompt (-g means "global"):</span></span>

```console
npm install -g less
```

<span data-ttu-id="51006-123">如果你使用 Visual Studio，你可以开始使用小于通过将一个或多个更少文件添加到你的项目，然后配置 Gulp （或 Grunt） 在编译时处理它们。</span><span class="sxs-lookup"><span data-stu-id="51006-123">If you're using Visual Studio, you can get started with Less by adding one or more Less files to your project, and then configuring Gulp (or Grunt) to process them at compile-time.</span></span> <span data-ttu-id="51006-124">添加*样式*到你的项目的文件夹，然后添加新的名为的文件小于*main.less*到此文件夹。</span><span class="sxs-lookup"><span data-stu-id="51006-124">Add a *Styles* folder to your project, and then add a new Less file named *main.less* to this folder.</span></span>

![添加较少的文件](less-sass-fa/_static/add-less-file.png)

<span data-ttu-id="51006-126">添加后，您的文件夹结构应如下所示：</span><span class="sxs-lookup"><span data-stu-id="51006-126">Once added, your folder structure should look something like this:</span></span>

![文件夹结构](less-sass-fa/_static/folder-structure.png)

<span data-ttu-id="51006-128">现在你可以添加到文件中，这将编译到 CSS 并部署到的 wwwroot 文件夹 Gulp 的一些基本的样式。</span><span class="sxs-lookup"><span data-stu-id="51006-128">Now you can add some basic styling to the file, which will be compiled into CSS and deployed to the wwwroot folder by Gulp.</span></span>

<span data-ttu-id="51006-129">修改*main.less*为包括以下内容，这将从一种基颜色创建一个简单的调色板。</span><span class="sxs-lookup"><span data-stu-id="51006-129">Modify *main.less* to include the following content, which creates a simple color palette from a single base color.</span></span>

```less
@base: #663333;
@background: spin(@base, 180);
@lighter: lighten(spin(@base, 5), 10%);
@lighter2: lighten(spin(@base, 10), 20%);
@darker: darken(spin(@base, -5), 10%);
@darker2: darken(spin(@base, -10), 20%);

body {
    background-color:@background;
}
.baseColor  {color:@base}
.bgLight    {color:@lighter}
.bgLight2   {color:@lighter2}
.bgDark     {color:@darker}
.bgDark2    {color:@darker2}
```

<span data-ttu-id="51006-130">`@base`和其他@-prefixed项是变量。</span><span class="sxs-lookup"><span data-stu-id="51006-130">`@base` and the other @-prefixed items are variables.</span></span> <span data-ttu-id="51006-131">每个表示一种颜色。</span><span class="sxs-lookup"><span data-stu-id="51006-131">Each of them represents a color.</span></span> <span data-ttu-id="51006-132">除`@base`，它们设置使用颜色函数： 加亮、 变暗，和旋转。</span><span class="sxs-lookup"><span data-stu-id="51006-132">Except for `@base`, they are set using color functions: lighten, darken, and spin.</span></span> <span data-ttu-id="51006-133">淡化和加深执行几乎你将预期;数值调节钮调整颜色色调的大量度 （围绕颜色盘中）。</span><span class="sxs-lookup"><span data-stu-id="51006-133">Lighten and darken do pretty much what you would expect; spin adjusts the hue of a color by a number of degrees (around the color wheel).</span></span> <span data-ttu-id="51006-134">较少处理器是足够智能，可忽略不使用的变量，因此若要展示了这些变量的工作原理，我们需要某个位置使用它们。</span><span class="sxs-lookup"><span data-stu-id="51006-134">The Less processor is smart enough to ignore variables that aren't used, so to demonstrate how these variables work, we need to use them somewhere.</span></span> <span data-ttu-id="51006-135">类`.baseColor`，等将演示每个生成的 CSS 文件中的变量的计算的值。</span><span class="sxs-lookup"><span data-stu-id="51006-135">The classes `.baseColor`, etc. will demonstrate the calculated values of each of the variables in the CSS file that is produced.</span></span>

### <a name="getting-started"></a><span data-ttu-id="51006-136">入门</span><span class="sxs-lookup"><span data-stu-id="51006-136">Getting started</span></span>

<span data-ttu-id="51006-137">创建**npm 配置文件**(*package.json*) 在你的项目文件夹并编辑它以引用`gulp`和`gulp-less`:</span><span class="sxs-lookup"><span data-stu-id="51006-137">Create an **npm Configuration File** (*package.json*) in your project folder and edit it to reference `gulp` and `gulp-less`:</span></span>

```json
{
  "version": "1.0.0",
  "name": "asp.net",
  "private": true,
  "devDependencies": {
    "gulp": "3.9.1",
    "gulp-less": "3.3.0"
  }
}
```

<span data-ttu-id="51006-138">在你的项目文件夹，或在 Visual Studio 中安装的依赖关系，请在命令提示符下**解决方案资源管理器**(**依赖项 > npm > 还原程序包**)。</span><span class="sxs-lookup"><span data-stu-id="51006-138">Install the dependencies either at a command prompt in your project folder, or in Visual Studio **Solution Explorer** (**Dependencies > npm > Restore packages**).</span></span>

```console
npm install
```

![VS 还原包](less-sass-fa/_static/restore-packages.png)

<span data-ttu-id="51006-140">在项目文件夹中，创建**Gulp 配置文件**(*gulpfile.js*) 以定义自动的过程。</span><span class="sxs-lookup"><span data-stu-id="51006-140">In the project folder, create a **Gulp Configuration File** (*gulpfile.js*) to define the automated process.</span></span>  <span data-ttu-id="51006-141">在更少，表示的文件及要运行更少的任务的顶部添加一个变量：</span><span class="sxs-lookup"><span data-stu-id="51006-141">Add a variable at the top of the file to represent Less, and a task to run Less:</span></span>

```javascript
var gulp = require("gulp"),
  fs = require("fs"),
  less = require("gulp-less");

gulp.task("less", function () {
  return gulp.src('Styles/main.less')
    .pipe(less())
    .pipe(gulp.dest('wwwroot/css'));
});
```

<span data-ttu-id="51006-142">打开**任务运行程序资源管理器**(**视图 > 其他 Windows > 任务运行程序资源管理器**)。</span><span class="sxs-lookup"><span data-stu-id="51006-142">Open the **Task Runner Explorer** (**View > Other Windows > Task Runner Explorer**).</span></span> <span data-ttu-id="51006-143">在任务之间，你应看到一个名为的新任务`less`。</span><span class="sxs-lookup"><span data-stu-id="51006-143">Among the tasks, you should see a new task named `less`.</span></span> <span data-ttu-id="51006-144">你可能必须刷新窗口。</span><span class="sxs-lookup"><span data-stu-id="51006-144">You might have to refresh the window.</span></span>

<span data-ttu-id="51006-145">运行`less`任务，并看到类似于此处所示的输出：</span><span class="sxs-lookup"><span data-stu-id="51006-145">Run the `less` task, and you see output similar to what is shown here:</span></span>

![较少的任务运行程序](less-sass-fa/_static/less-task-runner.png)

<span data-ttu-id="51006-147">*Wwwroot/css*文件夹现在包含一个新的文件， *main.css*:</span><span class="sxs-lookup"><span data-stu-id="51006-147">The *wwwroot/css* folder now contains a new file, *main.css*:</span></span>

![创建主 css](less-sass-fa/_static/main-css-created.png)

<span data-ttu-id="51006-149">打开*main.css* ，你将看到与下面类似：</span><span class="sxs-lookup"><span data-stu-id="51006-149">Open *main.css* and you see something like the following:</span></span>

```css
body {
    background-color: #336666;
}
.baseColor {
    color: #663333;
}
.bgLight {
    color: #884a44;
}
.bgLight2 {
    color: #aa6355;
}
.bgDark {
    color: #442225;
}
.bgDark2 {
    color: #221114;
}
```

<span data-ttu-id="51006-150">添加到一个简单的 HTML 页面*wwwroot*文件夹，然后引用*main.css*若要查看操作中的调色板。</span><span class="sxs-lookup"><span data-stu-id="51006-150">Add a simple HTML page to the *wwwroot* folder, and reference *main.css* to see the color palette in action.</span></span>

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <link href="css/main.css" rel="stylesheet" />
    <title></title>
</head>
<body>
    <div>
        <div class="baseColor">BaseColor</div>
        <div class="bgLight">Light</div>
        <div class="bgLight2">Light2</div>
        <div class="bgDark">Dark</div>
        <div class="bgDark2">Dark2</div>
    </div>
</body>
</html>
```

<span data-ttu-id="51006-151">你可以看到上旋转 180 度`@base`用于生成`@background`导致相反颜色的颜色盘`@base`:</span><span class="sxs-lookup"><span data-stu-id="51006-151">You can see that the 180 degree spin on `@base` used to produce `@background` resulted in the color wheel opposing color of `@base`:</span></span>

![不太测试示例](less-sass-fa/_static/less-test-screenshot.png)

<span data-ttu-id="51006-153">小于还提供对嵌套的规则以及嵌套的媒体查询的支持。</span><span class="sxs-lookup"><span data-stu-id="51006-153">Less also provides support for nested rules, as well as nested media queries.</span></span> <span data-ttu-id="51006-154">例如，像菜单可能会导致详细 CSS 规则的定义嵌套层次结构像这样:</span><span class="sxs-lookup"><span data-stu-id="51006-154">For example, defining nested hierarchies like menus can result in verbose CSS rules like these:</span></span>

```css
nav {
    height: 40px;
    width: 100%;
}
nav li {
    height: 38px;
    width: 100px;
}
nav li a:link {
    color: #000;
    text-decoration: none;
}
nav li a:visited {
    text-decoration: none;
    color: #CC3333;
}
nav li a:hover {
    text-decoration: underline;
    font-weight: bold;
}
nav li a:active {
    text-decoration: underline;
}
```

<span data-ttu-id="51006-155">理想情况下的所有相关的样式规则将被放在一起在 CSS 文件中，但在实践中没有任何强制实施此规则约定和可能块注释除外。</span><span class="sxs-lookup"><span data-stu-id="51006-155">Ideally all of the related style rules will be placed together within the CSS file, but in practice there is nothing enforcing this rule except convention and perhaps block comments.</span></span>

<span data-ttu-id="51006-156">定义使用小于这些相同的规则类似如下所示：</span><span class="sxs-lookup"><span data-stu-id="51006-156">Defining these same rules using Less looks like this:</span></span>

```less
nav {
    height: 40px;
    width: 100%;
    li {
        height: 38px;
        width: 100px;
        a {
            color: #000;
            &:link { text-decoration:none}
            &:visited { color: #CC3333; text-decoration:none}
            &:hover { text-decoration:underline; font-weight:bold}
            &:active {text-decoration:underline}
        }
    }
}
```

<span data-ttu-id="51006-157">请注意，在此情况下，所有的从属元素`nav`包含在其作用域内。</span><span class="sxs-lookup"><span data-stu-id="51006-157">Note that in this case, all of the subordinate elements of `nav` are contained within its scope.</span></span> <span data-ttu-id="51006-158">不再父元素的任何重复 (`nav`， `li`， `a`)，并且 （尽管其中一部分是将值放在第二个示例的同一行上的结果） 的总的行计数已以及删除。</span><span class="sxs-lookup"><span data-stu-id="51006-158">There is no longer any repetition of parent elements (`nav`, `li`, `a`), and the total line count has dropped as well (though some of that is a result of putting values on the same lines in the second example).</span></span> <span data-ttu-id="51006-159">它可以是非常有帮助，组织，若要查看的所有规则的给定的用户界面元素在显式限定范围内，在这种情况下设置从文件的其余部分由大括号。</span><span class="sxs-lookup"><span data-stu-id="51006-159">It can be very helpful, organizationally, to see all of the rules for a given UI element within an explicitly bounded scope, in this case set off from the rest of the file by curly braces.</span></span>

<span data-ttu-id="51006-160">`&`语法是较少的选择器功能，与 （&) 表示当前的选择器父级。</span><span class="sxs-lookup"><span data-stu-id="51006-160">The `&` syntax is a Less selector feature, with & representing the current selector parent.</span></span> <span data-ttu-id="51006-161">这样，在 {...}</span><span class="sxs-lookup"><span data-stu-id="51006-161">So, within the a {...}</span></span> <span data-ttu-id="51006-162">块中，`&`表示`a`标记，因此`&:link`等效于`a:link`。</span><span class="sxs-lookup"><span data-stu-id="51006-162">block, `&` represents an `a` tag, and thus `&:link` is equivalent to `a:link`.</span></span>

<span data-ttu-id="51006-163">创建响应式设计非常有用的媒体查询还可能影响很大程度重复和 CSS 中的复杂性。</span><span class="sxs-lookup"><span data-stu-id="51006-163">Media queries, extremely useful in creating responsive designs, can also contribute heavily to repetition and complexity in CSS.</span></span> <span data-ttu-id="51006-164">小于允许媒体查询嵌套在类，以便整个类定义不需要重复内不同顶级`@media`元素。</span><span class="sxs-lookup"><span data-stu-id="51006-164">Less allows media queries to be nested within classes, so that the entire class definition doesn't need to be repeated within different top-level `@media` elements.</span></span> <span data-ttu-id="51006-165">例如，下面是响应式菜单的 CSS:</span><span class="sxs-lookup"><span data-stu-id="51006-165">For example, here is CSS for a responsive menu:</span></span>

```css
.navigation {
    margin-top: 30%;
    width: 100%;
}
@media screen and (min-width: 40em) {
    .navigation {
        margin: 0;
    }
}
@media screen and (min-width: 62em) {
    .navigation {
        width: 960px;
        margin: 0;
    }
}
```

<span data-ttu-id="51006-166">这可以更好地定义以秒为：</span><span class="sxs-lookup"><span data-stu-id="51006-166">This can be better defined in Less as:</span></span>

```less
.navigation {
    margin-top: 30%;
    width: 100%;
    @media screen and (min-width: 40em) {
        margin: 0;
    }
    @media screen and (min-width: 62em) {
        width: 960px;
        margin: 0;
    }
}
```

<span data-ttu-id="51006-167">小于我们已经看到的另一个功能是其支持的数学运算，允许从预定义的变量构造的样式特性。</span><span class="sxs-lookup"><span data-stu-id="51006-167">Another feature of Less that we have already seen is its support for mathematical operations, allowing style attributes to be constructed from pre-defined variables.</span></span> <span data-ttu-id="51006-168">这使得更新相关的样式容易得多，因为可以修改基变量和所有依赖值自动更改。</span><span class="sxs-lookup"><span data-stu-id="51006-168">This makes updating related styles much easier, since the base variable can be modified and all dependent values change automatically.</span></span>

<span data-ttu-id="51006-169">CSS 文件，尤其是对于大型站点 （和尤其是如果正在使用媒体查询），就会变得很大随着时间推移，从而使用它们难以操作。</span><span class="sxs-lookup"><span data-stu-id="51006-169">CSS files, especially for large sites (and especially if media queries are being used), tend to get quite large over time, making working with them unwieldy.</span></span> <span data-ttu-id="51006-170">Less 文件可以单独定义，然后一起使用，取出`@import`指令。</span><span class="sxs-lookup"><span data-stu-id="51006-170">Less files can be defined separately, then pulled together using `@import` directives.</span></span> <span data-ttu-id="51006-171">小于还可导入单个 CSS 文件，同样，如果需要。</span><span class="sxs-lookup"><span data-stu-id="51006-171">Less can also be used to import individual CSS files, as well, if desired.</span></span>

<span data-ttu-id="51006-172">*Mixins*可以接受参数，并且小于 mixin 防护，提供声明性方式定义某些 mixins 生效的形式支持条件逻辑。</span><span class="sxs-lookup"><span data-stu-id="51006-172">*Mixins* can accept parameters, and Less supports conditional logic in the form of mixin guards, which provide a declarative way to define when certain mixins take effect.</span></span> <span data-ttu-id="51006-173">Mixin 临界条件的一个常见用途是调整颜色如何细基于或深色的源颜色是。</span><span class="sxs-lookup"><span data-stu-id="51006-173">A common use for mixin guards is to adjust colors based on how light or dark the source color is.</span></span> <span data-ttu-id="51006-174">Mixin 防护给定 mixin 接受颜色的参数，用于修改基于该颜色 mixin:</span><span class="sxs-lookup"><span data-stu-id="51006-174">Given a mixin that accepts a parameter for color, a mixin guard can be used to modify the mixin based on that color:</span></span>

```less
.box (@color) when (lightness(@color) >= 50%) {
    background-color: #000;
}
.box (@color) when (lightness(@color) < 50%) {
    background-color: #FFF;
}
.box (@color) {
    color: @color;
}

.feature {
    .box (@base);
}
```

<span data-ttu-id="51006-175">给定我们当前`@base`值`#663333`，小于此脚本将生成以下 CSS:</span><span class="sxs-lookup"><span data-stu-id="51006-175">Given our current `@base` value of `#663333`, this Less script will produce the following CSS:</span></span>

```css
.feature {
    background-color: #FFF;
    color: #663333;
}
```

<span data-ttu-id="51006-176">小于提供许多其他功能，但这应为你提供此强大的一些思路预处理语言。</span><span class="sxs-lookup"><span data-stu-id="51006-176">Less provides a number of additional features, but this should give you some idea of the power of this preprocessing language.</span></span>

## <a name="sass"></a><span data-ttu-id="51006-177">Sass</span><span class="sxs-lookup"><span data-stu-id="51006-177">Sass</span></span>

<span data-ttu-id="51006-178">Sass 类似于较少，是为许多相同功能，但使用略有不同的语法提供支持。</span><span class="sxs-lookup"><span data-stu-id="51006-178">Sass is similar to Less, providing support for many of the same features, but with slightly different syntax.</span></span> <span data-ttu-id="51006-179">它使用 Ruby，而不是 JavaScript 中，生成，因此不同的安装要求。</span><span class="sxs-lookup"><span data-stu-id="51006-179">It is built using Ruby, rather than JavaScript, and so has different setup requirements.</span></span> <span data-ttu-id="51006-180">原始 Sass 语言未使用大括号或分号，但改为定义使用空白和缩进的作用域。</span><span class="sxs-lookup"><span data-stu-id="51006-180">The original Sass language did not use curly braces or semicolons, but instead defined scope using white space and indentation.</span></span> <span data-ttu-id="51006-181">在 Sass 3 版本中，引入了新的语法， **SCSS** ("Sassy CSS")。</span><span class="sxs-lookup"><span data-stu-id="51006-181">In version 3 of Sass, a new syntax was introduced, **SCSS** ("Sassy CSS").</span></span> <span data-ttu-id="51006-182">SCSS 是类似于 CSS，可忽略缩进级别和空格，并改为使用分号和大括号。</span><span class="sxs-lookup"><span data-stu-id="51006-182">SCSS is similar to CSS in that it ignores indentation levels and whitespace, and instead uses semicolons and curly braces.</span></span>

<span data-ttu-id="51006-183">若要安装 Sass，通常你将首先安装 Ruby （预安装在 Mac 上），，然后运行：</span><span class="sxs-lookup"><span data-stu-id="51006-183">To install Sass, typically you would first install Ruby (pre-installed on Mac), and then run:</span></span>

```console
gem install sass
```

<span data-ttu-id="51006-184">但是，如果要运行 Visual Studio，你可以开始使用 Sass 在很大程度的相同方式就像对待小于。</span><span class="sxs-lookup"><span data-stu-id="51006-184">However, if you're running Visual Studio, you can get started with Sass in much the same way as you would with Less.</span></span> <span data-ttu-id="51006-185">打开*package.json*并添加到"gulp sass"包`devDependencies`:</span><span class="sxs-lookup"><span data-stu-id="51006-185">Open *package.json* and add the "gulp-sass" package to `devDependencies`:</span></span>

```json
"devDependencies": {
  "gulp": "3.9.1",
  "gulp-less": "3.3.0",
  "gulp-sass": "3.1.0"
}
```

<span data-ttu-id="51006-186">接下来，修改*gulpfile.js*添加 sass 变量和编译 Sass 文件并将结果放的 wwwroot 文件夹中的任务：</span><span class="sxs-lookup"><span data-stu-id="51006-186">Next, modify *gulpfile.js* to add a sass variable and a task to compile your Sass files and place the results in the wwwroot folder:</span></span>

```javascript
var gulp = require("gulp"),
  fs = require("fs"),
  less = require("gulp-less"),
  sass = require("gulp-sass");

// other content removed

gulp.task("sass", function () {
  return gulp.src('Styles/main2.scss')
    .pipe(sass())
    .pipe(gulp.dest('wwwroot/css'));
});
```

<span data-ttu-id="51006-187">现在，您可以将 Sass 文件*main2.scss*到*样式*项目的根目录中的文件夹：</span><span class="sxs-lookup"><span data-stu-id="51006-187">Now you can add the Sass file *main2.scss* to the *Styles* folder in the root of the project:</span></span>

![添加 scss 文件](less-sass-fa/_static/add-scss-file.png)

<span data-ttu-id="51006-189">打开*main2.scss* ，添加以下内容：</span><span class="sxs-lookup"><span data-stu-id="51006-189">Open *main2.scss* and add the following:</span></span>

```sass
$base: #CC0000;
body {
    background-color: $base;
}
```

<span data-ttu-id="51006-190">保存你所有文件。</span><span class="sxs-lookup"><span data-stu-id="51006-190">Save all of your files.</span></span> <span data-ttu-id="51006-191">现在刷新**任务运行程序资源管理器**，你看到`sass`任务。</span><span class="sxs-lookup"><span data-stu-id="51006-191">Now when you refresh **Task Runner Explorer**, you see a `sass` task.</span></span> <span data-ttu-id="51006-192">运行它，并查找*/wwwroot/css*文件夹。</span><span class="sxs-lookup"><span data-stu-id="51006-192">Run it, and look in the */wwwroot/css* folder.</span></span> <span data-ttu-id="51006-193">现在有了*main2.css*文件，使用以下内容：</span><span class="sxs-lookup"><span data-stu-id="51006-193">There is now a *main2.css* file, with these contents:</span></span>

```css
body {
    background-color: #CC0000;
}
```

<span data-ttu-id="51006-194">Sass 支持嵌套大体相同时，小于存在，提供类似的好处。</span><span class="sxs-lookup"><span data-stu-id="51006-194">Sass supports nesting in much the same was that Less does, providing similar benefits.</span></span> <span data-ttu-id="51006-195">文件可以由函数拆分和包含使用`@import`指令：</span><span class="sxs-lookup"><span data-stu-id="51006-195">Files can be split up by function and included using the `@import` directive:</span></span>

```sass
@import 'anotherfile';
```

<span data-ttu-id="51006-196">Sass 支持 mixins 同样，使用`@mixin`关键字定义它们和`@include`以便将它们，包含从如此示例所示[sass lang.com](http://sass-lang.com):</span><span class="sxs-lookup"><span data-stu-id="51006-196">Sass supports mixins as well, using the `@mixin` keyword to define them and `@include` to include them, as in this example from [sass-lang.com](http://sass-lang.com):</span></span>

```sass
@mixin border-radius($radius) {
    -webkit-border-radius: $radius;
    -moz-border-radius: $radius;
    -ms-border-radius: $radius;
    border-radius: $radius;
}

.box { @include border-radius(10px); }
```

<span data-ttu-id="51006-197">Sass 除了 mixins，它还支持继承，的概念允许一个类，以扩展另一个。</span><span class="sxs-lookup"><span data-stu-id="51006-197">In addition to mixins, Sass also supports the concept of inheritance, allowing one class to extend another.</span></span> <span data-ttu-id="51006-198">它是从概念上讲类似于 mixin，但在更少的 CSS 代码中的结果。</span><span class="sxs-lookup"><span data-stu-id="51006-198">It's conceptually similar to a mixin, but results in less CSS code.</span></span> <span data-ttu-id="51006-199">它通过`@extend`关键字。</span><span class="sxs-lookup"><span data-stu-id="51006-199">It's accomplished using the `@extend` keyword.</span></span> <span data-ttu-id="51006-200">若要尝试 mixins，将以下代码添加到你*main2.scss*文件：</span><span class="sxs-lookup"><span data-stu-id="51006-200">To try out mixins, add the following to your *main2.scss* file:</span></span>

```sass
@mixin alert {
    border: 1px solid black;
    padding: 5px;
    color: #333333;
}

.success {
    @include alert;
    border-color: green;
}

.error {
    @include alert;
    color: red;
    border-color: red;
    font-weight:bold;
}
```

<span data-ttu-id="51006-201">检查在输出*main2.css*运行之后`sass`任务中**任务运行程序资源管理器**:</span><span class="sxs-lookup"><span data-stu-id="51006-201">Examine the output in *main2.css* after running the `sass` task in **Task Runner Explorer**:</span></span>

```css
.success {
    border: 1px solid black;
    padding: 5px;
    color: #333333;
    border-color: green;
 }

.error {
    border: 1px solid black;
    padding: 5px;
    color: #333333;
    color: red;
    border-color: red;
    font-weight: bold;
}
```

<span data-ttu-id="51006-202">请注意所有警报 mixin 的常见属性在每个类中有重复。</span><span class="sxs-lookup"><span data-stu-id="51006-202">Notice that all of the common properties of the alert mixin are repeated in each class.</span></span> <span data-ttu-id="51006-203">Mixin 好地帮助消除重复在开发时，但它仍包含中的重复，从而导致大于必要 CSS 文件-一个潜在的性能问题很多要创建 CSS。</span><span class="sxs-lookup"><span data-stu-id="51006-203">The mixin did a good job of helping eliminate duplication at development time, but it's still creating CSS with a lot of duplication in it, resulting in larger than necessary CSS files - a potential performance issue.</span></span>

<span data-ttu-id="51006-204">现在将与警报 mixin`.alert`类，并更改`@include`到`@extend`(记住来扩展`.alert`，而不`alert`):</span><span class="sxs-lookup"><span data-stu-id="51006-204">Now replace the alert mixin with a `.alert` class, and change `@include` to `@extend` (remembering to extend `.alert`, not `alert`):</span></span>

```sass
.alert {
    border: 1px solid black;
    padding: 5px;
    color: #333333;
}

.success {
    @extend .alert;
    border-color: green;
}

.error {
    @extend .alert;
    color: red;
    border-color: red;
    font-weight:bold;
}
```

<span data-ttu-id="51006-205">运行 Sass 次，并检查生成的 CSS:</span><span class="sxs-lookup"><span data-stu-id="51006-205">Run Sass once more, and examine the resulting CSS:</span></span>

```css
.alert, .success, .error {
    border: 1px solid black;
    padding: 5px;
    color: #333333;
}

.success {
    border-color: green;
}

.error {
    color: red;
    border-color: red;
    font-weight: bold;
}
```

<span data-ttu-id="51006-206">现在仅为根据需要多次定义属性，并更好地生成 CSS。</span><span class="sxs-lookup"><span data-stu-id="51006-206">Now the properties are defined only as many times as needed, and better CSS is generated.</span></span>

<span data-ttu-id="51006-207">Sass 还包括函数和条件逻辑操作，类似于小于。</span><span class="sxs-lookup"><span data-stu-id="51006-207">Sass also includes functions and conditional logic operations, similar to Less.</span></span> <span data-ttu-id="51006-208">事实上，这两种语言的功能是非常相似。</span><span class="sxs-lookup"><span data-stu-id="51006-208">In fact, the two languages' capabilities are very similar.</span></span>

## <a name="less-or-sass"></a><span data-ttu-id="51006-209">较低或 Sass？</span><span class="sxs-lookup"><span data-stu-id="51006-209">Less or Sass?</span></span>

<span data-ttu-id="51006-210">仍没有关于是否通常更好的做法将占用更少或 Sass 没有共识 （或甚至是否首选原始 Sass 中的较新 SCSS 语法）。</span><span class="sxs-lookup"><span data-stu-id="51006-210">There is still no consensus as to whether it's generally better to use Less or Sass (or even whether to prefer the original Sass or the newer SCSS syntax within Sass).</span></span> <span data-ttu-id="51006-211">可能的最重要的决定是**使用这些工具之一**，而不是只使用手工编码 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="51006-211">Probably the most important decision is to **use one of these tools**, as opposed to just hand-coding your CSS files.</span></span> <span data-ttu-id="51006-212">一旦你所做的决策，这两个不太和 Sass 是不错的选择。</span><span class="sxs-lookup"><span data-stu-id="51006-212">Once you've made that decision, both Less and Sass are good choices.</span></span>

## <a name="font-awesome"></a><span data-ttu-id="51006-213">出色的字体</span><span class="sxs-lookup"><span data-stu-id="51006-213">Font Awesome</span></span>

<span data-ttu-id="51006-214">除了 CSS 预处理器样式现代 web 应用程序的另一个很好的办法是字体出色。</span><span class="sxs-lookup"><span data-stu-id="51006-214">In addition to CSS preprocessors, another great resource for styling modern web applications is Font Awesome.</span></span> <span data-ttu-id="51006-215">字体 Awesome 是一个工具包，提供 500 多个可以自由地使用 web 应用程序中的可缩放的向量图标。</span><span class="sxs-lookup"><span data-stu-id="51006-215">Font Awesome is a toolkit that provides over 500 scalable vector icons that can be freely used in your web applications.</span></span> <span data-ttu-id="51006-216">它最初设计成可使用 Bootstrap，但它并不依赖于该框架或在任何 JavaScript 库。</span><span class="sxs-lookup"><span data-stu-id="51006-216">It was originally designed to work with Bootstrap, but it has no dependency on that framework or on any JavaScript libraries.</span></span>

<span data-ttu-id="51006-217">若要开始使用字体出色的最简单方法是添加对它，使用其公用内容交付网络 (CDN) 位置的引用：</span><span class="sxs-lookup"><span data-stu-id="51006-217">The easiest way to get started with Font Awesome is to add a reference to it, using its public content delivery network (CDN) location:</span></span>

```html
<link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/font-awesome/4.3.0/css/font-awesome.min.css">
```

<span data-ttu-id="51006-218">你可以还将其添加到你的 Visual Studio 项目通过将它添加到"依赖关系"中*bower.json*:</span><span class="sxs-lookup"><span data-stu-id="51006-218">You can also add it to your Visual Studio project by adding it to the "dependencies" in *bower.json*:</span></span>

```json
{
  "name": "ASP.NET",
  "private": true,
  "dependencies": {
    "bootstrap": "3.0.0",
    "jquery": "1.10.2",
    "jquery-validation": "1.11.1",
    "jquery-validation-unobtrusive": "3.2.2",
    "hammer.js": "2.0.4",
    "bootstrap-touch-carousel": "0.8.0",
    "Font-Awesome": "4.3.0"
  }
}
```

<span data-ttu-id="51006-219">指向字体出色页上的引用后，你可以将图标添加到你的应用程序通过应用字体出色的类，通常前缀为"fa-"，为嵌入式 HTML 元素 (如`<span>`或`<i>`)。</span><span class="sxs-lookup"><span data-stu-id="51006-219">Once you have a reference to Font Awesome on a page, you can add icons to your application by applying Font Awesome classes, typically prefixed with "fa-", to your inline HTML elements (such as `<span>` or `<i>`).</span></span>  <span data-ttu-id="51006-220">例如，你可以将图标添加到简单列表和菜单使用如下代码：</span><span class="sxs-lookup"><span data-stu-id="51006-220">For example, you can add icons to simple lists and menus using code like this:</span></span>

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title></title>
    <link href="lib/font-awesome/css/font-awesome.css" rel="stylesheet" />
</head>
<body>
    <ul class="fa-ul">
        <li><i class="fa fa-li fa-home"></i> Home</li>
        <li><i class="fa fa-li fa-cog"></i> Settings</li>
    </ul>
</body>
</html>
```

<span data-ttu-id="51006-221">这将生成浏览器中的以下-请注意每个项旁边的图标：</span><span class="sxs-lookup"><span data-stu-id="51006-221">This produces the following in the browser - note the icon beside each item:</span></span>

![列表图标](less-sass-fa/_static/list-icons-screenshot.png)

<span data-ttu-id="51006-223">你可以查看可用的图标的完整列表：</span><span class="sxs-lookup"><span data-stu-id="51006-223">You can view a complete list of the available icons here:</span></span>

<span data-ttu-id="51006-224">http://fontawesome.io/icons/</span><span class="sxs-lookup"><span data-stu-id="51006-224">http://fontawesome.io/icons/</span></span>

## <a name="summary"></a><span data-ttu-id="51006-225">摘要</span><span class="sxs-lookup"><span data-stu-id="51006-225">Summary</span></span>

<span data-ttu-id="51006-226">现代 web 应用程序越来越要求清晰、 直观，编写和更易于使用的各种设备从的响应速度快、 流体设计。</span><span class="sxs-lookup"><span data-stu-id="51006-226">Modern web applications increasingly demand responsive, fluid designs that are clean, intuitive, and easy to use from a variety of devices.</span></span> <span data-ttu-id="51006-227">管理实现这些目标所需的 CSS 样式表的复杂性最好的做法是不太使用预处理器类似或 Sass。</span><span class="sxs-lookup"><span data-stu-id="51006-227">Managing the complexity of the CSS stylesheets required to achieve these goals is best done using a preprocessor like Less or Sass.</span></span> <span data-ttu-id="51006-228">此外，如字体出色的工具包快速提供的已知图标添加到文本导航菜单和按钮，提高整体用户体验的应用程序。</span><span class="sxs-lookup"><span data-stu-id="51006-228">In addition, toolkits like Font Awesome quickly provide well-known icons to textual navigation menus and buttons, improving the overall user experience of your application.</span></span>
