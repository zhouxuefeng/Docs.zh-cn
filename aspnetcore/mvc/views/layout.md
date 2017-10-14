---
title: "布局"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 29f12d1f-9734-48bd-bf1a-cee53a8ab700
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/layout
ms.openlocfilehash: 064621d8756b007c5b8859111bf3a03a0d7dda81
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="layout"></a><span data-ttu-id="17224-103">布局</span><span class="sxs-lookup"><span data-stu-id="17224-103">Layout</span></span>

<span data-ttu-id="17224-104">通过[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="17224-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="17224-105">视图频繁共享 visual 和编程元素。</span><span class="sxs-lookup"><span data-stu-id="17224-105">Views frequently share visual and programmatic elements.</span></span> <span data-ttu-id="17224-106">在本文中，你将了解如何使用通用的布局、 共享指令，以及在 ASP.NET 应用程序运行之前呈现视图的常见代码。</span><span class="sxs-lookup"><span data-stu-id="17224-106">In this article, you'll learn how to use common layouts, share directives, and run common code before rendering views in your ASP.NET app.</span></span>

## <a name="what-is-a-layout"></a><span data-ttu-id="17224-107">什么是一种布局</span><span class="sxs-lookup"><span data-stu-id="17224-107">What is a Layout</span></span>

<span data-ttu-id="17224-108">大多数 web 应用都具有它们导航页之间为用户提供一致的体验的常用布局。</span><span class="sxs-lookup"><span data-stu-id="17224-108">Most web apps have a common layout that provides the user with a consistent experience as they navigate from page to page.</span></span> <span data-ttu-id="17224-109">布局通常包括常见的用户界面元素，如应用程序标题、 导航或菜单元素和页脚。</span><span class="sxs-lookup"><span data-stu-id="17224-109">The layout typically includes common user interface elements such as the app header, navigation or menu elements, and footer.</span></span>

![页面布局示例](layout/_static/page-layout.png)

<span data-ttu-id="17224-111">在应用内的很多页也经常使用常见的 HTML 结构，如脚本和样式表。</span><span class="sxs-lookup"><span data-stu-id="17224-111">Common HTML structures such as scripts and stylesheets are also frequently used by many pages within an app.</span></span> <span data-ttu-id="17224-112">在所有这些共享元素可定义*布局*文件，然后在应用程序中使用任何视图引用。</span><span class="sxs-lookup"><span data-stu-id="17224-112">All of these shared elements may be defined in a *layout* file, which can then be referenced by any view used within the app.</span></span> <span data-ttu-id="17224-113">布局减少重复的代码在视图中，帮助他们按照[不重复自己 （模拟） 原则](http://deviq.com/don-t-repeat-yourself/)。</span><span class="sxs-lookup"><span data-stu-id="17224-113">Layouts reduce duplicate code in views, helping them follow the [Don't Repeat Yourself (DRY) principle](http://deviq.com/don-t-repeat-yourself/).</span></span>

<span data-ttu-id="17224-114">按照约定，名为 ASP.NET 应用程序的默认布局`_Layout.cshtml`。</span><span class="sxs-lookup"><span data-stu-id="17224-114">By convention, the default layout for an ASP.NET app is named `_Layout.cshtml`.</span></span> <span data-ttu-id="17224-115">Visual Studio ASP.NET 核心 MVC 项目模板包含在此布局文件`Views/Shared`文件夹：</span><span class="sxs-lookup"><span data-stu-id="17224-115">The Visual Studio ASP.NET Core MVC project template includes this layout file in the `Views/Shared` folder:</span></span>

![在解决方案资源管理器的视图文件夹](layout/_static/web-project-views.png)

<span data-ttu-id="17224-117">此布局在应用程序定义视图的顶级模板。</span><span class="sxs-lookup"><span data-stu-id="17224-117">This layout defines a top level template for views in the app.</span></span> <span data-ttu-id="17224-118">应用不需要一种布局，并应用可以定义多个布局，与指定不同布局的不同视图。</span><span class="sxs-lookup"><span data-stu-id="17224-118">Apps do not require a layout, and apps can define more than one layout, with different views specifying different layouts.</span></span>

<span data-ttu-id="17224-119">示例`_Layout.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="17224-119">An example `_Layout.cshtml`:</span></span>

[!code-html[Main](../../common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=42,66)]

## <a name="specifying-a-layout"></a><span data-ttu-id="17224-120">指定布局</span><span class="sxs-lookup"><span data-stu-id="17224-120">Specifying a Layout</span></span>

<span data-ttu-id="17224-121">具有 razor 视图`Layout`属性。</span><span class="sxs-lookup"><span data-stu-id="17224-121">Razor views have a `Layout` property.</span></span> <span data-ttu-id="17224-122">单个视图，通过设置此属性指定布局：</span><span class="sxs-lookup"><span data-stu-id="17224-122">Individual views specify a layout by setting this property:</span></span>

[!code-html[Main](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

<span data-ttu-id="17224-123">指定的布局可以使用完整路径 (示例： `/Views/Shared/_Layout.cshtml`) 或部分名称 (示例： `_Layout`)。</span><span class="sxs-lookup"><span data-stu-id="17224-123">The layout specified can use a full path (example: `/Views/Shared/_Layout.cshtml`) or a partial name (example: `_Layout`).</span></span> <span data-ttu-id="17224-124">如果提供部分名称，Razor 视图引擎将搜索使用其标准的发现进程的布局文件。</span><span class="sxs-lookup"><span data-stu-id="17224-124">When a partial name is provided, the Razor view engine will search for the layout file using its standard discovery process.</span></span> <span data-ttu-id="17224-125">控制器关联文件夹搜索第一次后, 跟`Shared`文件夹。</span><span class="sxs-lookup"><span data-stu-id="17224-125">The controller-associated folder is searched first, followed by the `Shared` folder.</span></span> <span data-ttu-id="17224-126">此发现过程等同于用来发现[分部视图](partial.md)。</span><span class="sxs-lookup"><span data-stu-id="17224-126">This discovery process is identical to the one used to discover [partial views](partial.md).</span></span>

<span data-ttu-id="17224-127">默认情况下，必须调用每个布局`RenderBody`。</span><span class="sxs-lookup"><span data-stu-id="17224-127">By default, every layout must call `RenderBody`.</span></span> <span data-ttu-id="17224-128">任何位置调用`RenderBody`是放置，就将呈现的视图的内容。</span><span class="sxs-lookup"><span data-stu-id="17224-128">Wherever the call to `RenderBody` is placed, the contents of the view will be rendered.</span></span>

<a name="layout-sections-label"></a>

### <a name="sections"></a><span data-ttu-id="17224-129">部分</span><span class="sxs-lookup"><span data-stu-id="17224-129">Sections</span></span>

<span data-ttu-id="17224-130">布局 （可选） 可以引用一个或多个*部分*，通过调用`RenderSection`。</span><span class="sxs-lookup"><span data-stu-id="17224-130">A layout can optionally reference one or more *sections*, by calling `RenderSection`.</span></span> <span data-ttu-id="17224-131">各节提供了一种方法来组织放置某些页元素。</span><span class="sxs-lookup"><span data-stu-id="17224-131">Sections provide a way to organize where certain page elements should be placed.</span></span> <span data-ttu-id="17224-132">每次调用`RenderSection`可以指定该部分为必需或可选。</span><span class="sxs-lookup"><span data-stu-id="17224-132">Each call to `RenderSection` can specify whether that section is required or optional.</span></span> <span data-ttu-id="17224-133">如果未找到所需的部分，将引发异常。</span><span class="sxs-lookup"><span data-stu-id="17224-133">If a required section is not found, an exception will be thrown.</span></span> <span data-ttu-id="17224-134">单个视图指定要在中部分使用呈现的内容`@section`Razor 语法。</span><span class="sxs-lookup"><span data-stu-id="17224-134">Individual views specify the content to be rendered within a section using the `@section` Razor syntax.</span></span> <span data-ttu-id="17224-135">如果视图可用于定义一个部分，必须呈现 （或将发生错误）。</span><span class="sxs-lookup"><span data-stu-id="17224-135">If a view defines a section, it must be rendered (or an error will occur).</span></span>

<span data-ttu-id="17224-136">示例`@section`视图中的定义：</span><span class="sxs-lookup"><span data-stu-id="17224-136">An example `@section` definition in a view:</span></span>

```html
@section Scripts {
     <script type="text/javascript" src="/scripts/main.js"></script>
   }
   ```

<span data-ttu-id="17224-137">在上面的代码中，验证脚本添加到`scripts`包含一个窗体的视图上的部分。</span><span class="sxs-lookup"><span data-stu-id="17224-137">In the code above, validation scripts are added to the `scripts` section on a view that includes a form.</span></span> <span data-ttu-id="17224-138">同一个应用程序中的其他视图可能不需要任何其他脚本，并因此不需要定义脚本部分。</span><span class="sxs-lookup"><span data-stu-id="17224-138">Other views in the same application might not require any additional scripts, and so wouldn't need to define a scripts section.</span></span>

<span data-ttu-id="17224-139">在视图中定义的部分是仅在其立即进行布局页中可用。</span><span class="sxs-lookup"><span data-stu-id="17224-139">Sections defined in a view are available only in its immediate layout page.</span></span> <span data-ttu-id="17224-140">不能从它们、 将视图组件或查看系统的其他部分引用。</span><span class="sxs-lookup"><span data-stu-id="17224-140">They cannot be referenced from partials, view components, or other parts of the view system.</span></span>

### <a name="ignoring-sections"></a><span data-ttu-id="17224-141">忽略部分</span><span class="sxs-lookup"><span data-stu-id="17224-141">Ignoring sections</span></span>

<span data-ttu-id="17224-142">默认情况下，正文和内容页中的所有部分必须所有来呈现布局页。</span><span class="sxs-lookup"><span data-stu-id="17224-142">By default, the body and all sections in a content page must all be rendered by the layout page.</span></span> <span data-ttu-id="17224-143">Razor 视图引擎将强制实施此通过跟踪是否在呈现的正文和每个部分。</span><span class="sxs-lookup"><span data-stu-id="17224-143">The Razor view engine enforces this by tracking whether the body and each section have been rendered.</span></span>

<span data-ttu-id="17224-144">若要让视图引擎以忽略正文或部分，请调用`IgnoreBody`和`IgnoreSection`方法。</span><span class="sxs-lookup"><span data-stu-id="17224-144">To instruct the view engine to ignore the body or sections, call the `IgnoreBody` and `IgnoreSection` methods.</span></span>

<span data-ttu-id="17224-145">正文和 Razor 页中的每个部分必须呈现或忽略。</span><span class="sxs-lookup"><span data-stu-id="17224-145">The body and every section in a Razor page must be either rendered or ignored.</span></span>

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a><span data-ttu-id="17224-146">导入共享的指令</span><span class="sxs-lookup"><span data-stu-id="17224-146">Importing Shared Directives</span></span>

<span data-ttu-id="17224-147">视图可以使用 Razor 指令来执行很多东西，例如导入命名空间或执行[依赖关系注入](dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="17224-147">Views can use Razor directives to do many things, such as importing namespaces or performing [dependency injection](dependency-injection.md).</span></span> <span data-ttu-id="17224-148">可能在一组公共中指定指令由许多视图共享`_ViewImports.cshtml`文件。</span><span class="sxs-lookup"><span data-stu-id="17224-148">Directives shared by many views may be specified in a common `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="17224-149">`_ViewImports`文件支持以下指令：</span><span class="sxs-lookup"><span data-stu-id="17224-149">The `_ViewImports` file supports the following directives:</span></span>

* `@addTagHelper`

* `@removeTagHelper`

* `@tagHelperPrefix`

* `@using`

* `@model`

* `@inherits`

* `@inject`

<span data-ttu-id="17224-150">该文件不支持其他 Razor 功能，如函数和部分定义。</span><span class="sxs-lookup"><span data-stu-id="17224-150">The file does not support other Razor features, such as functions and section definitions.</span></span>

<span data-ttu-id="17224-151">示例`_ViewImports.cshtml`文件：</span><span class="sxs-lookup"><span data-stu-id="17224-151">A sample `_ViewImports.cshtml` file:</span></span>

[!code-html[Main](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

<span data-ttu-id="17224-152">`_ViewImports.cshtml`文件的 ASP.NET 核心 MVC 应用程序通常置于`Views`文件夹。</span><span class="sxs-lookup"><span data-stu-id="17224-152">The `_ViewImports.cshtml` file for an ASP.NET Core MVC app is typically placed in the `Views` folder.</span></span> <span data-ttu-id="17224-153">A`_ViewImports.cshtml`可以将文件放在任何文件夹中，它将仅应用于的情况下该文件夹及其子文件夹内的视图。</span><span class="sxs-lookup"><span data-stu-id="17224-153">A `_ViewImports.cshtml` file can be placed within any folder, in which case it will only be applied to views within that folder and its subfolders.</span></span> <span data-ttu-id="17224-154">`_ViewImports`根级别中，开始处理文件，然后对截止到每个文件夹的位置的视图本身，以便设置指定的根级别可能重写在文件夹级别。</span><span class="sxs-lookup"><span data-stu-id="17224-154">`_ViewImports` files are processed starting at the root level, and then for each folder leading up to the location of the view itself, so settings specified at the root level may be overridden at the folder level.</span></span>

<span data-ttu-id="17224-155">例如，如果根级别`_ViewImports.cshtml`文件指定`@model`和`@addTagHelper`，以及另一个`_ViewImports.cshtml`控制器相关视图的文件夹中的文件指定一个不同`@model`和添加另一个`@addTagHelper`，视图将有权访问这两个标记帮助程序，并将使用后者`@model`。</span><span class="sxs-lookup"><span data-stu-id="17224-155">For example, if a root level `_ViewImports.cshtml` file specifies `@model` and `@addTagHelper`, and another `_ViewImports.cshtml` file in the controller-associated folder of the view specifies a different `@model` and adds another `@addTagHelper`, the view will have access to both tag helpers and will use the latter `@model`.</span></span>

<span data-ttu-id="17224-156">如果选择多个`_ViewImports.cshtml`文件是否为视图运行、 组合中包含的指令的行为`ViewImports.cshtml`文件将如下所示：</span><span class="sxs-lookup"><span data-stu-id="17224-156">If multiple `_ViewImports.cshtml` files are run for a view, combined behavior of the directives included in the `ViewImports.cshtml` files will be as follows:</span></span>

* <span data-ttu-id="17224-157">`@addTagHelper``@removeTagHelper`： 所有运行，按顺序</span><span class="sxs-lookup"><span data-stu-id="17224-157">`@addTagHelper`, `@removeTagHelper`: all run, in order</span></span>

* <span data-ttu-id="17224-158">`@tagHelperPrefix`： 到视图最近的一个替代的任何其他</span><span class="sxs-lookup"><span data-stu-id="17224-158">`@tagHelperPrefix`: the closest one to the view overrides any others</span></span>

* <span data-ttu-id="17224-159">`@model`： 到视图最近的一个替代的任何其他</span><span class="sxs-lookup"><span data-stu-id="17224-159">`@model`: the closest one to the view overrides any others</span></span>

* <span data-ttu-id="17224-160">`@inherits`： 到视图最近的一个替代的任何其他</span><span class="sxs-lookup"><span data-stu-id="17224-160">`@inherits`: the closest one to the view overrides any others</span></span>

* <span data-ttu-id="17224-161">`@using`： 所有都包含;将忽略重复项</span><span class="sxs-lookup"><span data-stu-id="17224-161">`@using`: all are included; duplicates are ignored</span></span>

* <span data-ttu-id="17224-162">`@inject`： 对于每个属性，则最近到视图将覆盖具有相同的属性名称的任何其他</span><span class="sxs-lookup"><span data-stu-id="17224-162">`@inject`: for each property, the closest one to the view overrides any others with the same property name</span></span>

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a><span data-ttu-id="17224-163">运行代码之前每个视图</span><span class="sxs-lookup"><span data-stu-id="17224-163">Running Code Before Each View</span></span>

<span data-ttu-id="17224-164">如果你的代码需要在每个视图之前运行，这应置于`_ViewStart.cshtml`文件。</span><span class="sxs-lookup"><span data-stu-id="17224-164">If you have code you need to run before every view, this should be placed in the `_ViewStart.cshtml` file.</span></span> <span data-ttu-id="17224-165">按照约定，`_ViewStart.cshtml`文件位于`Views`文件夹。</span><span class="sxs-lookup"><span data-stu-id="17224-165">By convention, the `_ViewStart.cshtml` file is located in the `Views` folder.</span></span> <span data-ttu-id="17224-166">中列出的语句`_ViewStart.cshtml`在每个完整的视图 （不布局和非分部视图） 之前运行。</span><span class="sxs-lookup"><span data-stu-id="17224-166">The statements listed in `_ViewStart.cshtml` are run before every full view (not layouts, and not partial views).</span></span> <span data-ttu-id="17224-167">如[ViewImports.cshtml](xref:mvc/views/layout#viewimports)，`_ViewStart.cshtml`是分层。</span><span class="sxs-lookup"><span data-stu-id="17224-167">Like [ViewImports.cshtml](xref:mvc/views/layout#viewimports), `_ViewStart.cshtml` is hierarchical.</span></span> <span data-ttu-id="17224-168">如果`_ViewStart.cshtml`控制器关联的视图文件夹中定义文件，它将运行后的根目录中定义的一个`Views`文件夹 （如果有）。</span><span class="sxs-lookup"><span data-stu-id="17224-168">If a `_ViewStart.cshtml` file is defined in the controller-associated view folder, it will be run after the one defined in the root of the `Views` folder (if any).</span></span>

<span data-ttu-id="17224-169">示例`_ViewStart.cshtml`文件：</span><span class="sxs-lookup"><span data-stu-id="17224-169">A sample `_ViewStart.cshtml` file:</span></span>

[!code-html[Main](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

<span data-ttu-id="17224-170">上述文件指定所有视图将都使用`_Layout.cshtml`布局。</span><span class="sxs-lookup"><span data-stu-id="17224-170">The file above specifies that all views will use the `_Layout.cshtml` layout.</span></span>

> [!NOTE]
> <span data-ttu-id="17224-171">既不`_ViewStart.cshtml`也不`_ViewImports.cshtml`通常置于`/Views/Shared`文件夹。</span><span class="sxs-lookup"><span data-stu-id="17224-171">Neither `_ViewStart.cshtml` nor `_ViewImports.cshtml` are typically placed in the `/Views/Shared` folder.</span></span> <span data-ttu-id="17224-172">应直接在放置这些文件的应用程序级版本`/Views`文件夹。</span><span class="sxs-lookup"><span data-stu-id="17224-172">The app-level versions of these files should be placed directly in the `/Views` folder.</span></span>
