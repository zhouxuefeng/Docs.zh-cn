---
title: "在 ASP.NET 核心中的标记帮助程序"
author: rick-anderson
description: "了解标记帮助程序是什么以及如何在 ASP.NET 核心中使用它们。"
keywords: "ASP.NET 核心，标记帮助程序"
ms.author: riande
manager: wpickett
ms.date: 7/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/tag-helpers/intro
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 10471210075dc8a5366b7d5170d6594c2e66ce94
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="introduction-to-tag-helpers-in-aspnet-core"></a><span data-ttu-id="8bbf0-104">在 ASP.NET 核心中的标记帮助器简介</span><span class="sxs-lookup"><span data-stu-id="8bbf0-104">Introduction to Tag Helpers in ASP.NET Core</span></span> 

<span data-ttu-id="8bbf0-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8bbf0-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="what-are-tag-helpers"></a><span data-ttu-id="8bbf0-106">标记帮助器有哪些？</span><span class="sxs-lookup"><span data-stu-id="8bbf0-106">What are Tag Helpers?</span></span>

<span data-ttu-id="8bbf0-107">标记帮助程序启用服务器端代码，以参与创建和呈现 Razor 文件中的 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-107">Tag Helpers enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="8bbf0-108">例如，内置`ImageTagHelper`可以追加到映像名称的版本号。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-108">For example, the built-in `ImageTagHelper` can append a version number to the image name.</span></span> <span data-ttu-id="8bbf0-109">当映像发生更改时，服务器将生成的映像的新唯一版本，因此，保证客户端以获取当前映像 （而不是过时缓存的映像）。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-109">Whenever the image changes, the server generates a new unique version for the image, so clients are guaranteed to get the current image (instead of a stale cached image).</span></span> <span data-ttu-id="8bbf0-110">有许多内置的标记帮助器常见任务-例如，创建窗体、 链接、 加载资产和详细的和甚至更多的可用在公共 GitHub 存储库并作为 NuGet 程序包。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-110">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="8bbf0-111">在使用 C# 中，创作标记帮助程序和它们目标基于元素名称、 属性名称或父标记的 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-111">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="8bbf0-112">例如，内置`LabelTagHelper`可以面向 HTML`<label>`元素时`LabelTagHelper`属性均适用。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-112">For example, the built-in `LabelTagHelper` can target the HTML `<label>` element when the `LabelTagHelper` attributes are applied.</span></span> <span data-ttu-id="8bbf0-113">如果你熟悉[HTML 帮助器](http://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers)，标记帮助程序减少之间 HTML 和 C# 在 Razor 视图中的显式转换。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-113">If you're familiar with [HTML Helpers](http://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), Tag Helpers reduce the explicit transitions between HTML and C# in Razor views.</span></span> <span data-ttu-id="8bbf0-114">在许多情况下，HTML 帮助器向特定的标记帮助器，提供的备用方法，但务必要识别标记帮助程序不替换 HTML 帮助器并不是每个 HTML 帮助器标记帮助器。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-114">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers do not replace HTML Helpers and there is not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="8bbf0-115">[标记帮助程序与 HTML 帮助器相比](#tag-helpers-compared-to-html-helpers)介绍更多详细信息中的差异。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-115">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span>

## <a name="what-tag-helpers-provide"></a><span data-ttu-id="8bbf0-116">标记帮助程序提供的内容</span><span class="sxs-lookup"><span data-stu-id="8bbf0-116">What Tag Helpers provide</span></span>

<span data-ttu-id="8bbf0-117">**HTML 友好开发体验**对于在多数情况下，使用标记帮助程序 Razor 标记看起来像标准 HTML。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-117">**An HTML-friendly development experience** For the most part, Razor markup using Tag Helpers looks like standard HTML.</span></span> <span data-ttu-id="8bbf0-118">与 HTML/CSS/JavaScript conversant 前端设计器可以编辑 Razor，而无需学习 C# Razor 语法。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-118">Front-end designers conversant with HTML/CSS/JavaScript can edit Razor without learning C# Razor syntax.</span></span>

<span data-ttu-id="8bbf0-119">**用于创建 HTML 和 Razor 标记的丰富智能感知环境**这是到 HTML 帮助器，在 Razor 视图中的标记的服务器端创建与前一方法尖锐相反。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-119">**A rich IntelliSense environment for creating HTML and Razor markup** This is in sharp contrast to HTML Helpers, the previous approach to server-side creation of markup in Razor views.</span></span> <span data-ttu-id="8bbf0-120">[标记帮助程序与 HTML 帮助器相比](#tag-helpers-compared-to-html-helpers)介绍更多详细信息中的差异。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-120">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span> <span data-ttu-id="8bbf0-121">[标记帮助器的 IntelliSense 支持](#intellisense-support-for-tag-helpers)还是解释了 IntelliSense 环境。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-121">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) explains the IntelliSense environment.</span></span> <span data-ttu-id="8bbf0-122">具有 Razor C# 语法的经验的甚至开发人员提高工作效率比编写 C# Razor 标记中使用标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-122">Even developers experienced with Razor C# syntax are more productive using Tag Helpers than writing C# Razor markup.</span></span>

<span data-ttu-id="8bbf0-123">**方法使你更高效且能够生成更为可靠、 可靠和可维护的代码使用在服务器上的信息只能**例如，从历史上看上更新映像箴言是在更改时更改映像的名称图像。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-123">**A way to make you more productive and able to produce more robust, reliable, and maintainable code using information only available on the server** For example, historically the mantra on updating images was to change the name of the image when you change the image.</span></span> <span data-ttu-id="8bbf0-124">映像应激进缓存出于性能原因，并更改映像的名称，除非你存在的风险获取陈旧副本的客户端。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-124">Images should be aggressively cached for performance reasons, and unless you change the name of an image, you risk clients getting a stale copy.</span></span> <span data-ttu-id="8bbf0-125">从历史上看，映像中进行了编辑后，名称必须更改并且 web 应用中的图像的每次引用所需更新。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-125">Historically, after an image was edited, the name had to be changed and each reference to the image in the web app needed to be updated.</span></span> <span data-ttu-id="8bbf0-126">这不只是非常人工密集型的它也是 （你可能缺少引用、 意外输入错误字符串，等等），而且容易出错内置`ImageTagHelper`可以自动执行此操作为你。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-126">Not only is this very labor intensive, it's also error prone (you could miss a reference, accidentally enter the wrong string, etc.) The built-in `ImageTagHelper` can do this for you automatically.</span></span> <span data-ttu-id="8bbf0-127">`ImageTagHelper`可以追加版本数字到映像名称，因此当该图像发生更改时，服务器自动生成的映像的新唯一版本。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-127">The `ImageTagHelper` can append a version number to the image name, so whenever the image changes, the server automatically generates a new unique version for the image.</span></span> <span data-ttu-id="8bbf0-128">保证客户端都获得当前的图像。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-128">Clients are guaranteed to get the current image.</span></span> <span data-ttu-id="8bbf0-129">通过使用实质上是免费的可靠性和人工节约附送`ImageTagHelper`。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-129">This robustness and labor savings comes essentially free by using the `ImageTagHelper`.</span></span>

<span data-ttu-id="8bbf0-130">大部分内置标记帮助器目标现有 HTML 元素，而为的元素提供服务器端属性。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-130">Most of the built-in Tag Helpers target existing HTML elements and provide server-side attributes for the element.</span></span> <span data-ttu-id="8bbf0-131">例如，`<input>`元素中的视图中的许多使用*视图/帐户*文件夹包含`asp-for`属性，它将指定的模型属性的名称提取到呈现的 HTML。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-131">For example, the `<input>` element used in many of the views in the *Views/Account* folder contains the `asp-for` attribute, which extracts the name of the specified model property into the rendered HTML.</span></span> <span data-ttu-id="8bbf0-132">以下 Razor 标记：</span><span class="sxs-lookup"><span data-stu-id="8bbf0-132">The following Razor markup:</span></span>

```html
<label asp-for="Email"></label>
```

<span data-ttu-id="8bbf0-133">生成的以下 HTML:</span><span class="sxs-lookup"><span data-stu-id="8bbf0-133">Generates the following HTML:</span></span>

```html
<label for="Email">Email</label>
```

<span data-ttu-id="8bbf0-134">`asp-for`属性可通过`For`中的属性`LabelTagHelper`。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-134">The `asp-for` attribute is made available by the `For` property in the `LabelTagHelper`.</span></span> <span data-ttu-id="8bbf0-135">请参阅[创作标记帮助程序](authoring.md)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-135">See [Authoring Tag Helpers](authoring.md) for more information.</span></span>

## <a name="managing-tag-helper-scope"></a><span data-ttu-id="8bbf0-136">管理标记帮助器作用域</span><span class="sxs-lookup"><span data-stu-id="8bbf0-136">Managing Tag Helper scope</span></span>

<span data-ttu-id="8bbf0-137">标记帮助器作用域控制的组合来`@addTagHelper`， `@removeTagHelper`，与"！"选择退出字符。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-137">Tag Helpers scope is controlled by a combination of `@addTagHelper`, `@removeTagHelper`, and the "!" opt-out character.</span></span>

<a name=add-helper-label></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a><span data-ttu-id="8bbf0-138">`@addTagHelper`提供标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="8bbf0-138">`@addTagHelper` makes Tag Helpers available</span></span>

<span data-ttu-id="8bbf0-139">如果你创建新的 ASP.NET 核心 web 应用名为*AuthoringTagHelpers* （不带身份验证），以下*Views/_ViewImports.cshtml*文件将添加到你的项目：</span><span class="sxs-lookup"><span data-stu-id="8bbf0-139">If you create a new ASP.NET Core web app named *AuthoringTagHelpers* (with no authentication), the following *Views/_ViewImports.cshtml* file will be added to your project:</span></span>

[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

<span data-ttu-id="8bbf0-140">`@addTagHelper`指令使标记帮助程序可供查看。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-140">The `@addTagHelper` directive makes Tag Helpers available to the view.</span></span> <span data-ttu-id="8bbf0-141">在此示例中，视图文件仅*Views/_ViewImports.cshtml*，默认情况下由继承中的所有视图文件*视图*文件夹和子目录; 提供标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-141">In this case, the view file is *Views/_ViewImports.cshtml*, which by default is inherited by all view files in the *Views* folder and sub-directories; making Tag Helpers available.</span></span> <span data-ttu-id="8bbf0-142">上面的代码中使用通配符语法 ("\*") 来指定在指定的程序集中的所有标记帮助程序 (*Microsoft.AspNetCore.Mvc.TagHelpers*) 将可供在每个视图文件*视图*目录或子目录。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-142">The code above uses the wildcard syntax ("\*") to specify that all Tag Helpers in the specified assembly (*Microsoft.AspNetCore.Mvc.TagHelpers*) will be available to every view file in the *Views* directory or sub-directory.</span></span> <span data-ttu-id="8bbf0-143">之后的第一个参数`@addTagHelper`指定标记帮助程序，以加载 (我们将使用"\*"的所有标记帮助程序)，和第二个参数"Microsoft.AspNetCore.Mvc.TagHelpers"指定包含标记帮助程序的程序集。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-143">The first parameter after `@addTagHelper` specifies the Tag Helpers to load (we are using "\*" for all Tag Helpers), and the second parameter "Microsoft.AspNetCore.Mvc.TagHelpers" specifies the assembly containing the Tag Helpers.</span></span> <span data-ttu-id="8bbf0-144">*Microsoft.AspNetCore.Mvc.TagHelpers*是内置的 ASP.NET 核心标记帮助程序的程序集。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-144">*Microsoft.AspNetCore.Mvc.TagHelpers* is the assembly for the built-in ASP.NET Core Tag Helpers.</span></span>

<span data-ttu-id="8bbf0-145">公开所有在此项目中的标记帮助程序 (这将创建名为程序集*AuthoringTagHelpers*)，则将使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="8bbf0-145">To expose all of the Tag Helpers in this project (which creates an assembly named *AuthoringTagHelpers*), you would use the following:</span></span>

[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

<span data-ttu-id="8bbf0-146">如果你的项目包含`EmailTagHelper`具有默认命名空间 (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`)，你可以提供标记帮助器的完全限定的名称 (FQN):</span><span class="sxs-lookup"><span data-stu-id="8bbf0-146">If your project contains an `EmailTagHelper` with the default namespace (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), you can provide the fully qualified name (FQN) of the Tag Helper:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [3]}} -->

```html
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<span data-ttu-id="8bbf0-147">若要将标记帮助器添加到使用 FQN 的视图，你首先添加 FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`)，，然后是程序集名称 (*AuthoringTagHelpers*)。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-147">To add a Tag Helper to a view using an FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the assembly name (*AuthoringTagHelpers*).</span></span> <span data-ttu-id="8bbf0-148">大多数开发人员更愿意使用"\*"通配符语法。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-148">Most developers prefer to use the  "\*" wildcard syntax.</span></span> <span data-ttu-id="8bbf0-149">通配符语法，可插入通配符"\*"FQN 中的后缀。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-149">The wildcard syntax allows you to insert the wildcard character "\*" as the suffix in an FQN.</span></span> <span data-ttu-id="8bbf0-150">例如，任何以下指令将`EmailTagHelper`:</span><span class="sxs-lookup"><span data-stu-id="8bbf0-150">For example, any of the following directives will bring in the `EmailTagHelper`:</span></span>

```csharp
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

<span data-ttu-id="8bbf0-151">如前所述，添加`@addTagHelper`指令至*Views/_ViewImports.cshtml*文件使标记帮助器可用于中的所有视图文件*视图*目录及其子目录。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-151">As mentioned previously, adding the `@addTagHelper` directive to the *Views/_ViewImports.cshtml* file makes the Tag Helper available to all view files in the *Views* directory and sub-directories.</span></span> <span data-ttu-id="8bbf0-152">你可以使用`@addTagHelper`指令特定视图文件，如果你想要参加公开标记帮助器到仅这些视图中。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-152">You can use the `@addTagHelper` directive in specific view files if you want to opt-in to exposing the Tag Helper to only those views.</span></span>

<a name=remove-razor-directives-label></a>

### <a name="removetaghelper-removes-tag-helpers"></a><span data-ttu-id="8bbf0-153">`@removeTagHelper`删除标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="8bbf0-153">`@removeTagHelper` removes Tag Helpers</span></span>

<span data-ttu-id="8bbf0-154">`@removeTagHelper`具有相同的两个参数为`@addTagHelper`，它会删除以前添加标记帮助器。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-154">The `@removeTagHelper` has the same two parameters as `@addTagHelper`, and it removes a Tag Helper that was previously added.</span></span> <span data-ttu-id="8bbf0-155">例如，`@removeTagHelper`应用于特定视图中删除指定的标记帮助器从视图。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-155">For example, `@removeTagHelper` applied to a specific view removes the specified Tag Helper from the view.</span></span> <span data-ttu-id="8bbf0-156">使用`@removeTagHelper`中*Views/Folder/_ViewImports.cshtml*从视图中的所有文件中都移除的指定的标记帮助器*文件夹*。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-156">Using `@removeTagHelper` in a *Views/Folder/_ViewImports.cshtml* file removes the specified Tag Helper from all of the views in *Folder*.</span></span>

### <a name="controlling-tag-helper-scope-with-the-viewimportscshtml-file"></a><span data-ttu-id="8bbf0-157">控制具有标记帮助器作用域*_ViewImports.cshtml*文件</span><span class="sxs-lookup"><span data-stu-id="8bbf0-157">Controlling Tag Helper scope with the *_ViewImports.cshtml* file</span></span>

<span data-ttu-id="8bbf0-158">你可以添加*_ViewImports.cshtml*任何视图文件夹中，和视图引擎应用到指令从这两个文件和*Views/_ViewImports.cshtml*文件。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-158">You can add a *_ViewImports.cshtml* to any view folder, and the view engine applies the directives from both that file and the *Views/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="8bbf0-159">如果你添加一个空*Views/Home/_ViewImports.cshtml*文件以获取*主页*视图，则不存在任何更改因为*_ViewImports.cshtml*文件是累加性。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-159">If you added an empty *Views/Home/_ViewImports.cshtml* file for the *Home* views, there would be no change because the *_ViewImports.cshtml* file is additive.</span></span> <span data-ttu-id="8bbf0-160">任何`@addTagHelper`指令将添加到*Views/Home/_ViewImports.cshtml*文件 (位于不在默认*Views/_ViewImports.cshtml*文件) 将公开到视图这些标记帮助器仅在*主页*文件夹。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-160">Any `@addTagHelper` directives you add to the *Views/Home/_ViewImports.cshtml* file (that are not in the default *Views/_ViewImports.cshtml* file) would expose those Tag Helpers to views only in the *Home* folder.</span></span>

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a><span data-ttu-id="8bbf0-161">选择退出各个元素</span><span class="sxs-lookup"><span data-stu-id="8bbf0-161">Opting out of individual elements</span></span>

<span data-ttu-id="8bbf0-162">你可以禁用与标记帮助器选择退出字符元素级别的标记帮助器 ("！")。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-162">You can disable a Tag Helper at the element level with the Tag Helper opt-out character ("!").</span></span> <span data-ttu-id="8bbf0-163">例如，`Email`中禁用验证`<span>`标记帮助器选择退出字符：</span><span class="sxs-lookup"><span data-stu-id="8bbf0-163">For example, `Email` validation is disabled in the `<span>` with the Tag Helper opt-out character:</span></span>

```csharp
<!span asp-validation-for="Email" class="text-danger"></!span>
```

<span data-ttu-id="8bbf0-164">必须将标记帮助器选择退出字符应用于的开始和结束标记。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-164">You must apply the Tag Helper opt-out character to the opening and closing tag.</span></span> <span data-ttu-id="8bbf0-165">（Visual Studio 编辑器会自动向退出字符结束标记时你添加到的开始标记）。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-165">(The Visual Studio editor automatically adds the opt-out character to the closing tag when you add one to the opening tag).</span></span> <span data-ttu-id="8bbf0-166">添加退出字符后和标记帮助器属性的元素将不再显示在不同的字体。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-166">After you add the opt-out character, the element and Tag Helper attributes are no longer displayed in a distinctive font.</span></span>

<a name=prefix-razor-directives-label></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a><span data-ttu-id="8bbf0-167">使用`@tagHelperPrefix`以便显式标记帮助程序使用情况</span><span class="sxs-lookup"><span data-stu-id="8bbf0-167">Using `@tagHelperPrefix` to make Tag Helper usage explicit</span></span>

<span data-ttu-id="8bbf0-168">`@tagHelperPrefix`指令允许你指定的标记前缀字符串来启用标记帮助器支持和明确标记帮助程序使用情况。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-168">The `@tagHelperPrefix` directive allows you to specify a tag prefix string to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> <span data-ttu-id="8bbf0-169">例如，可以添加到以下标记*Views/_ViewImports.cshtml*文件：</span><span class="sxs-lookup"><span data-stu-id="8bbf0-169">For example, you could add the following markup to the *Views/_ViewImports.cshtml* file:</span></span>

```html
@tagHelperPrefix th:
```
<span data-ttu-id="8bbf0-170">在下面的代码图中，标记帮助器前缀设置为`th:`，因此只有这些元素使用前缀`th:`支持标记帮助程序 （已启用标记帮助器的元素具有不同的字体）。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-170">In the code image below, the Tag Helper prefix is set to `th:`, so only those elements using the prefix `th:` support Tag Helpers (Tag Helper-enabled elements have a distinctive font).</span></span> <span data-ttu-id="8bbf0-171">`<label>`和`<input>`元素具有标记帮助器前缀和标记帮助器支持，而是`<span>`元素不一样。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-171">The `<label>` and `<input>` elements have the Tag Helper prefix and are Tag Helper-enabled, while the `<span>` element does not.</span></span>

![图像](intro/_static/thp.png)

<span data-ttu-id="8bbf0-173">相同的层次结构规则适用于`@addTagHelper`也适用于`@tagHelperPrefix`。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-173">The same hierarchy rules that apply to `@addTagHelper` also apply to `@tagHelperPrefix`.</span></span>

## <a name="intellisense-support-for-tag-helpers"></a><span data-ttu-id="8bbf0-174">标记帮助器的 IntelliSense 支持</span><span class="sxs-lookup"><span data-stu-id="8bbf0-174">IntelliSense support for Tag Helpers</span></span>

<span data-ttu-id="8bbf0-175">Visual Studio 中创建新的 ASP.NET web 应用程序，它会添加 NuGet 包"Microsoft.AspNetCore.Razor.Tools"。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-175">When you create a new ASP.NET web app in Visual Studio, it adds the NuGet package "Microsoft.AspNetCore.Razor.Tools".</span></span> <span data-ttu-id="8bbf0-176">这是添加标记帮助程序工具的包。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-176">This is the package that adds Tag Helper tooling.</span></span>

<span data-ttu-id="8bbf0-177">请考虑编写 HTML`<label>`元素。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-177">Consider writing an HTML `<label>` element.</span></span> <span data-ttu-id="8bbf0-178">你输入时，就会立即`<l`在 Visual Studio 编辑器中，IntelliSense 将显示匹配的元素：</span><span class="sxs-lookup"><span data-stu-id="8bbf0-178">As soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![图像](intro/_static/label.png)

<span data-ttu-id="8bbf0-180">你不仅获取 HTML 帮助，但图标 ("@"符号与在其下的"<>")。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-180">Not only do you get HTML help, but the icon (the "@" symbol with "<>" under it).</span></span>

![图像](intro/_static/tagSym.png)

<span data-ttu-id="8bbf0-182">为为目标的标记帮助程序，请标识此元素。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-182">identifies the element as targeted by Tag Helpers.</span></span> <span data-ttu-id="8bbf0-183">纯 HTML 元素 (如`fieldset`) 显示"<>"图标。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-183">Pure HTML elements (such as the `fieldset`) display the "<>" icon.</span></span>

<span data-ttu-id="8bbf0-184">纯 HTML`<label>`标记棕色的进度的字体，以红色的属性中显示 （与默认 Visual Studio 颜色主题） 的 HTML 标记，并以蓝色的属性值。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-184">A pure HTML `<label>` tag displays the HTML tag (with the default Visual Studio color theme) in a brown font, the attributes in red, and the attribute values in blue.</span></span>

![图像](intro/_static/LableHtmlTag.png)

<span data-ttu-id="8bbf0-186">输入后`<label`，IntelliSense 将列出可用的 HTML/CSS 属性和标记帮助程序目标属性：</span><span class="sxs-lookup"><span data-stu-id="8bbf0-186">After you enter `<label`, IntelliSense lists the available HTML/CSS attributes and the Tag Helper-targeted attributes:</span></span>

![图像](intro/_static/labelattr.png)

<span data-ttu-id="8bbf0-188">IntelliSense 语句结束允许您输入 tab 键完成语句的选定值：</span><span class="sxs-lookup"><span data-stu-id="8bbf0-188">IntelliSense statement completion allows you to enter the tab key to complete the statement with the selected value:</span></span>

![图像](intro/_static/stmtcomplete.png)

<span data-ttu-id="8bbf0-190">只要输入标记帮助器属性，标记和特性字体更改。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-190">As soon as a Tag Helper attribute is entered, the tag and attribute fonts change.</span></span> <span data-ttu-id="8bbf0-191">使用默认 Visual Studio"蓝色"或"Light"颜色主题，此字体是粗体紫色。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-191">Using the default Visual Studio "Blue" or "Light" color theme, the font is bold purple.</span></span> <span data-ttu-id="8bbf0-192">如果你使用的"深色"主题此字体是粗体蓝绿色。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-192">If you're using the "Dark" theme the font is bold teal.</span></span> <span data-ttu-id="8bbf0-193">本文档中的映像已使用默认主题执行。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-193">The images in this document were taken using the default theme.</span></span>

![图像](intro/_static/labelaspfor2.png)

<span data-ttu-id="8bbf0-195">你可以输入 Visual Studio *CompleteWord*快捷方式 (Ctrl + 空格键是[默认](https://docs.microsoft.com/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio)在双引号 ("")，，您现在在 C# 中，就像你将 C# 类中一样。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-195">You can enter the Visual Studio *CompleteWord* shortcut (Ctrl +spacebar is the [default](https://docs.microsoft.com/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) inside the double quotes (""), and you are now in C#, just like you would be in a C# class.</span></span> <span data-ttu-id="8bbf0-196">IntelliSense 显示页面模型上的所有方法和属性。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-196">IntelliSense displays all the methods and properties on the page model.</span></span> <span data-ttu-id="8bbf0-197">方法和属性有该属性类型是因为`ModelExpression`。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-197">The methods and properties are available because the property type is `ModelExpression`.</span></span> <span data-ttu-id="8bbf0-198">在下图中，我将编辑`Register`视图中，因此`RegisterViewModel`可用。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-198">In the image below, I'm editing the `Register` view, so the `RegisterViewModel` is available.</span></span>

![图像](intro/_static/intellemail.png)

<span data-ttu-id="8bbf0-200">IntelliSense 将列出的属性和方法可用于在页上的模型。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-200">IntelliSense lists the properties and methods available to the model on the page.</span></span> <span data-ttu-id="8bbf0-201">丰富智能感知环境可帮助你选择的 CSS 类：</span><span class="sxs-lookup"><span data-stu-id="8bbf0-201">The rich IntelliSense environment helps you select the CSS class:</span></span>

![图像](intro/_static/iclass.png)

![图像](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a><span data-ttu-id="8bbf0-204">标记帮助器相比 HTML 帮助器</span><span class="sxs-lookup"><span data-stu-id="8bbf0-204">Tag Helpers compared to HTML Helpers</span></span>

<span data-ttu-id="8bbf0-205">标记帮助程序将附加到在 Razor 视图中的 HTML 元素时[HTML 帮助器](http://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers)如方法穿插 HTML 在 Razor 视图中调用。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-205">Tag Helpers attach to HTML elements in Razor views, while [HTML Helpers](http://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) are invoked as methods interspersed with HTML in Razor views.</span></span> <span data-ttu-id="8bbf0-206">请考虑使用 CSS 类"标题"创建一个 HTML 标签的以下 Razor 标记：</span><span class="sxs-lookup"><span data-stu-id="8bbf0-206">Consider the following Razor markup, which creates an HTML label with the CSS class "caption":</span></span>

```html
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

<span data-ttu-id="8bbf0-207">在 (`@`) 符号通知 Razor 这是代码的开始位置。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-207">The at (`@`) symbol tells Razor this is the start of code.</span></span> <span data-ttu-id="8bbf0-208">下面的两个参数 ("FirstName"和"第一个名称:") 都是字符串，因此[IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense)无法提供帮助。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-208">The next two parameters ("FirstName" and "First Name:") are strings, so [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense) can't help.</span></span> <span data-ttu-id="8bbf0-209">最后一个自变量：</span><span class="sxs-lookup"><span data-stu-id="8bbf0-209">The last argument:</span></span>

```html
new {@class="caption"}
```

<span data-ttu-id="8bbf0-210">用于表示属性的匿名对象。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-210">Is an anonymous object used to represent attributes.</span></span> <span data-ttu-id="8bbf0-211">因为**类**是保留的关键字在 C# 中，你使用`@`符号以强制 C#，以解释"@class="为符号 （属性名称）。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-211">Because **class** is a reserved keyword in C#, you use the `@` symbol to force C# to interpret "@class=" as a symbol (property name).</span></span> <span data-ttu-id="8bbf0-212">到前端的设计器 (人熟悉 HTML/CSS/JavaScript 和其他客户端技术，但不是熟悉 C# 和 Razor)，行的大多数是外部角色。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-212">To a front-end designer (someone familiar with HTML/CSS/JavaScript and other client technologies but not familiar with C# and Razor), most of the line is foreign.</span></span> <span data-ttu-id="8bbf0-213">从 IntelliSense 没有帮助，必须编写的整个行。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-213">The entire line must be authored with no help from IntelliSense.</span></span>

<span data-ttu-id="8bbf0-214">使用`LabelTagHelper`，相同的标记可以编写为：</span><span class="sxs-lookup"><span data-stu-id="8bbf0-214">Using the `LabelTagHelper`, the same markup can be written as:</span></span>

![图像](intro/_static/label2.png)

<span data-ttu-id="8bbf0-216">使用标记帮助器版本，你输入时，就会立即`<l`在 Visual Studio 编辑器中，IntelliSense 将显示匹配的元素：</span><span class="sxs-lookup"><span data-stu-id="8bbf0-216">With the Tag Helper version, as soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![图像](intro/_static/label.png)

<span data-ttu-id="8bbf0-218">IntelliSense 可帮助您编写的整个行。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-218">IntelliSense helps you write the entire line.</span></span> <span data-ttu-id="8bbf0-219">`LabelTagHelper`也默认为设置的内容`asp-for`到"名字"; 属性值 ("FirstName")它将转换为句子组成与每个新的大小写字母的出现位置空间的属性名称混合使用大小写的属性。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-219">The `LabelTagHelper` also defaults to setting the content of the `asp-for` attribute value ("FirstName") to "First Name"; It converts camel-cased properties to a sentence composed of the property name with a space where each new upper-case letter occurs.</span></span> <span data-ttu-id="8bbf0-220">在下列标记中：</span><span class="sxs-lookup"><span data-stu-id="8bbf0-220">In the following markup:</span></span>

![图像](intro/_static/label2.png)

<span data-ttu-id="8bbf0-222">生成：</span><span class="sxs-lookup"><span data-stu-id="8bbf0-222">generates:</span></span>

```html
<label class="caption" for="FirstName">First Name</label>
```

<span data-ttu-id="8bbf0-223">如果你将内容添加到未使用 camel 大小写形式句子大小写形式内容`<label>`。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-223">The camel-cased to sentence-cased content is not used if you add content to the `<label>`.</span></span> <span data-ttu-id="8bbf0-224">例如: </span><span class="sxs-lookup"><span data-stu-id="8bbf0-224">For example:</span></span>

![图像](intro/_static/1stName.png)

<span data-ttu-id="8bbf0-226">生成：</span><span class="sxs-lookup"><span data-stu-id="8bbf0-226">generates:</span></span>

```html
<label class="caption" for="FirstName">Name First</label>
```

<span data-ttu-id="8bbf0-227">下面的代码图显示的窗体部分*Views/Account/Register.cshtml* Razor 视图从 Visual Studio 2015 中包含旧 ASP.NET 4.5.x MVC 模板生成。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-227">The following code image shows the Form portion of the *Views/Account/Register.cshtml* Razor view generated from the legacy ASP.NET 4.5.x MVC template included with Visual Studio 2015.</span></span>

![图像](intro/_static/regCS.png)

<span data-ttu-id="8bbf0-229">Visual Studio 编辑器会显示包含一个灰色背景的 C# 代码。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-229">The Visual Studio editor displays C# code with a grey background.</span></span> <span data-ttu-id="8bbf0-230">例如，`AntiForgeryToken`的 HTML 帮助器：</span><span class="sxs-lookup"><span data-stu-id="8bbf0-230">For example, the `AntiForgeryToken` HTML Helper:</span></span>

```html
@Html.AntiForgeryToken()
```

<span data-ttu-id="8bbf0-231">将显示灰色背景。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-231">is displayed with a grey background.</span></span> <span data-ttu-id="8bbf0-232">注册视图中的大多数是标记的 C#。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-232">Most of the markup in the Register view is C#.</span></span> <span data-ttu-id="8bbf0-233">与等效的方法使用标记帮助程序进行的比较：</span><span class="sxs-lookup"><span data-stu-id="8bbf0-233">Compare that to the equivalent approach using Tag Helpers:</span></span>

![图像](intro/_static/regTH.png)

<span data-ttu-id="8bbf0-235">标记是得更简洁且更轻松地读取、 编辑和维护的 HTML 帮助器方法。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-235">The markup is much cleaner and easier to read, edit, and maintain than the HTML Helpers approach.</span></span> <span data-ttu-id="8bbf0-236">C# 代码都会减少到所需要了解的有关服务器的最低要求。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-236">The C# code is reduced to the minimum that the server needs to know about.</span></span> <span data-ttu-id="8bbf0-237">Visual Studio 编辑器会显示不同的字体中标记帮助程序目标的标记。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-237">The Visual Studio editor displays markup targeted by a Tag Helper in a distinctive font.</span></span>

<span data-ttu-id="8bbf0-238">请考虑*电子邮件*组：</span><span class="sxs-lookup"><span data-stu-id="8bbf0-238">Consider the *Email* group:</span></span>

[!code-csharp[Main](intro/sample/Register.cshtml?range=12-18)]

<span data-ttu-id="8bbf0-239">每个"asp-"属性具有值为"电子邮件"，但是"Email"不是字符串。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-239">Each of the "asp-" attributes has a value of "Email", but "Email" is not a string.</span></span> <span data-ttu-id="8bbf0-240">在此上下文中，"Email"为 C# 模型表达式属性`RegisterViewModel`。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-240">In this context, "Email" is the C# model expression property for the `RegisterViewModel`.</span></span>

<span data-ttu-id="8bbf0-241">Visual Studio 编辑器可帮助您编写**所有**的寄存器窗体，而 Visual Studio 中的 HTML 帮助器方法的代码的大部分提供没有帮助的标记帮助程序方法中的标记。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-241">The Visual Studio editor helps you write **all** of the markup in the Tag Helper approach of the register form, while Visual Studio provides no help for most of the code in the HTML Helpers approach.</span></span> <span data-ttu-id="8bbf0-242">[标记帮助器的 IntelliSense 支持](#intellisense-support-for-tag-helpers)进入使用在 Visual Studio 编辑器中的标记帮助器的详细信息。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-242">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) goes into detail on working with Tag Helpers in the Visual Studio editor.</span></span>

## <a name="tag-helpers-compared-to-web-server-controls"></a><span data-ttu-id="8bbf0-243">与 Web 服务器控件的标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="8bbf0-243">Tag Helpers compared to Web Server Controls</span></span>

* <span data-ttu-id="8bbf0-244">标记帮助程序不属于他们正在与; 关联的元素他们只需参与的元素和内容的呈现。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-244">Tag Helpers don't own the element they're associated with; they simply participate in the rendering of the element and content.</span></span> <span data-ttu-id="8bbf0-245">ASP.NET[控件 Web 服务器控件](https://msdn.microsoft.com/library/7698y1f0.aspx)是声明并调用在页面上。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-245">ASP.NET [Web Server controls](https://msdn.microsoft.com/library/7698y1f0.aspx) are declared and invoked on a page.</span></span>

* <span data-ttu-id="8bbf0-246">[Web 服务器控件](https://msdn.microsoft.com/library/zsyt68f1.aspx)具有重要的生命周期，难以在开发和调试。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-246">[Web Server controls](https://msdn.microsoft.com/library/zsyt68f1.aspx) have a non-trivial lifecycle that can make developing and debugging difficult.</span></span>

* <span data-ttu-id="8bbf0-247">Web 服务器控件，可以使用的客户端控件将功能添加到客户端文档对象模型 (DOM) 元素。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-247">Web Server controls allow you to add functionality to the client Document Object Model (DOM) elements by using a client control.</span></span> <span data-ttu-id="8bbf0-248">标记帮助程序有任何 dom。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-248">Tag Helpers have no DOM.</span></span>

* <span data-ttu-id="8bbf0-249">Web 服务器控件包括浏览器自动检测。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-249">Web Server controls include automatic browser detection.</span></span> <span data-ttu-id="8bbf0-250">标记帮助程序具有浏览器没有的知识。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-250">Tag Helpers have no knowledge of the browser.</span></span>

* <span data-ttu-id="8bbf0-251">多个标记帮助程序可以在同一元素上执行操作 (请参阅[避免标记帮助器冲突](https://docs.microsoft.com/aspnet/core/mvc/views/tag-helpers/authoring#avoiding-tag-helper-conflicts)) 时通常不能撰写 Web 服务器控件。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-251">Multiple Tag Helpers can act on the same element (see [Avoiding Tag Helper conflicts](https://docs.microsoft.com/aspnet/core/mvc/views/tag-helpers/authoring#avoiding-tag-helper-conflicts) ) while you typically can't compose Web Server controls.</span></span>

* <span data-ttu-id="8bbf0-252">标记帮助程序可以修改的标记和它们在作用于的 HTML 元素的内容，但不要直接修改任何其他页上。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-252">Tag Helpers can modify the tag and content of HTML elements that they're scoped to, but don't directly modify anything else on a page.</span></span> <span data-ttu-id="8bbf0-253">Web 服务器控件具有更具体的作用域，并且可以执行影响你页; 的其他部件的操作启用意外的副作用。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-253">Web Server controls have a less specific scope and can perform actions that affect other parts of your page; enabling unintended side effects.</span></span>

* <span data-ttu-id="8bbf0-254">Web 服务器控件使用的类型转换器将字符串转换为对象。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-254">Web Server controls use type converters to convert strings into objects.</span></span> <span data-ttu-id="8bbf0-255">通过标记帮助程序，您可以以本机方式在 C# 中，因此无需进行类型转换。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-255">With Tag Helpers, you work natively in C#, so you don't need to do type conversion.</span></span>

* <span data-ttu-id="8bbf0-256">Web 服务器控件使用[System.ComponentModel](https://docs.microsoft.com/dotnet/api/system.componentmodel)实现组件和控件的运行时和设计时行为。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-256">Web Server controls use [System.ComponentModel](https://docs.microsoft.com/dotnet/api/system.componentmodel) to implement the run-time and design-time behavior of components and controls.</span></span> <span data-ttu-id="8bbf0-257">`System.ComponentModel`包括基类和实现属性和类型转换器、 绑定到数据源，以及授权组件的接口。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-257">`System.ComponentModel` includes the base classes and interfaces for implementing attributes and type converters, binding to data sources, and licensing components.</span></span> <span data-ttu-id="8bbf0-258">相比之下，到标记帮助程序，通常派生自`TagHelper`，和`TagHelper`基类公开只有两种方法，`Process`和`ProcessAsync`。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-258">Contrast that to Tag Helpers, which typically derive from `TagHelper`, and the `TagHelper` base class exposes only two methods, `Process` and `ProcessAsync`.</span></span>

## <a name="customizing-the-tag-helper-element-font"></a><span data-ttu-id="8bbf0-259">自定义标记帮助器元素字体</span><span class="sxs-lookup"><span data-stu-id="8bbf0-259">Customizing the Tag Helper element font</span></span>

<span data-ttu-id="8bbf0-260">你可以自定义的字体和着色从**工具** > **选项** > **环境** > **字体和颜色**:</span><span class="sxs-lookup"><span data-stu-id="8bbf0-260">You can customize the font and colorization from **Tools** > **Options** > **Environment** > **Fonts and Colors**:</span></span>

![图像](intro/_static/fontoptions2.png)

## <a name="additional-resources"></a><span data-ttu-id="8bbf0-262">其他资源</span><span class="sxs-lookup"><span data-stu-id="8bbf0-262">Additional Resources</span></span>

* [<span data-ttu-id="8bbf0-263">创作标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="8bbf0-263">Authoring Tag Helpers</span></span>](authoring.md)
* [<span data-ttu-id="8bbf0-264">使用窗体</span><span class="sxs-lookup"><span data-stu-id="8bbf0-264">Working with Forms </span></span>](../working-with-forms.md)
* <span data-ttu-id="8bbf0-265">[在 GitHub 上的 TagHelperSamples](https://github.com/dpaquette/TagHelperSamples)包含用于处理的标记帮助程序示例[Bootstrap](http://getbootstrap.com/)。</span><span class="sxs-lookup"><span data-stu-id="8bbf0-265">[TagHelperSamples on GitHub](https://github.com/dpaquette/TagHelperSamples) contains Tag Helper samples for working with [Bootstrap](http://getbootstrap.com/).</span></span>

<!--
* [Working with Forms ](xref:mvc/views/working-with-forms)
-->
