---
title: "ASP.NET Core 中已搭建基架的 Razor 页面"
author: rick-anderson
description: "介绍通过搭建基架生成的 Razor 页面。"
keywords: "ASP.NET Core, Razor 页面, Razor, MVC"
ms.author: riande
manager: wpickett
ms.date: 7/27/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages/page
ms.openlocfilehash: 1b7a41a0e1383ba287e4ddce53b33761a96a3129
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="scaffolded-razor-pages-in-aspnet-core"></a><span data-ttu-id="9ae0d-104">ASP.NET Core 中已搭建基架的 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="9ae0d-104">Scaffolded Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="9ae0d-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9ae0d-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9ae0d-106">本教程介绍在[上一教程](xref:tutorials/razor-pages/page)中通过搭建基架创建的 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-106">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/page).</span></span> 

<span data-ttu-id="9ae0d-107">[查看或下载](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie)示例。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-107">[View or download](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="9ae0d-108">“创建”、“删除”、“详细信息”和“编辑”页。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-108">The Create, Delete, Details, and Edit pages.</span></span>

<span data-ttu-id="9ae0d-109">检查 Pages/Movies/Index.cshtml.cs 代码隐藏文件：[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Index.cshtml.cs)]</span><span class="sxs-lookup"><span data-stu-id="9ae0d-109">Examine the *Pages/Movies/Index.cshtml.cs* code-behind file: [!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Index.cshtml.cs)]</span></span>

<span data-ttu-id="9ae0d-110">Razor 页面派生自 `PageModel`。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-110">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="9ae0d-111">按照约定，`PageModel` 派生的类称为 `<PageName>Model`。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-111">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="9ae0d-112">此构造函数使用[依赖关系注入](xref:fundamentals/dependency-injection)将 `MovieContext` 添加到页。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-112">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `MovieContext` to the page.</span></span> <span data-ttu-id="9ae0d-113">所有已搭建基架的页面都遵循此模式。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-113">All the scaffolded pages follow this pattern.</span></span>

<span data-ttu-id="9ae0d-114">对页面发出请求时，`OnGetAsync` 方法向 Razor 页面返回影片列表。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-114">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="9ae0d-115">在 Razor 页面上调用 `OnGetAsync` 或 `OnGet` 以初始化页面状态。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-115">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="9ae0d-116">在这种情况下，`OnGetAsync` 将获得要显示的影片列表。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-116">In this case, `OnGetAsync` gets a list of movies to display.</span></span>

<span data-ttu-id="9ae0d-117">检查 Pages/Movies/Index.cshtml Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="9ae0d-117">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

<span data-ttu-id="9ae0d-118">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Index.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="9ae0d-118">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Index.cshtml)]</span></span>

<span data-ttu-id="9ae0d-119">Razor 可以从 HTML 转换为 C# 或 Razor 特定标记。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-119">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="9ae0d-120">当 `@` 符号后跟 [Razor 保留关键字](xref:mvc/views/razor#razor-reserved-keywords)时，它会转换为 Razor 特定标记，否则会转换为 C#。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-120">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="9ae0d-121">`@page` Razor 指令将文件转换为一个 MVC 操作 &mdash;，这意味着它可以处理请求。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-121">The `@page` Razor directive makes the file into an MVC action &mdash; which means that it can handle requests.</span></span> <span data-ttu-id="9ae0d-122">`@page` 必须是页面上的第一个 Razor 指令。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-122">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="9ae0d-123">`@page` 是转换到 Razor 特定标记的一个示例。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-123">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="9ae0d-124">有关详细信息，请参阅 [Razor 语法](xref:mvc/views/razor#razor-syntax)。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-124">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="9ae0d-125">检查以下 HTML 帮助程序中使用的 Lambda 表达式：</span><span class="sxs-lookup"><span data-stu-id="9ae0d-125">Examine the lambda expression used in the following HTML Helper:</span></span>

`@Html.DisplayNameFor(model => model.Movie[0].Title))`

<span data-ttu-id="9ae0d-126">`DisplayNameFor` HTML 帮助程序检查 Lambda 表达式中引用的 `Title` 属性来确定显示名称。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-126">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="9ae0d-127">检查 Lambda 表达式（而非求值）。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-127">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="9ae0d-128">这意味着当 `model`、`model.Movies` 或 `model.Movies[0]` 为 `null` 或为空时，不会存在任何访问冲突。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-128">That means there is no access violation when `model`, `model.Movies`, or `model.Movies[0]` are `null` or empty.</span></span> <span data-ttu-id="9ae0d-129">对 Lambda 表达式求值时（例如，使用 `@Html.DisplayFor(modelItem => item.Title)`），将求得该模型的属性值。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-129">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>
### <a name="the-model-directive"></a><span data-ttu-id="9ae0d-130">@model 指令</span><span class="sxs-lookup"><span data-stu-id="9ae0d-130">The @model directive</span></span>

<span data-ttu-id="9ae0d-131">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Index.cshtml?range=1-2&highlight=2)]</span><span class="sxs-lookup"><span data-stu-id="9ae0d-131">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Index.cshtml?range=1-2&highlight=2)]</span></span>

<span data-ttu-id="9ae0d-132">`@model` 指令指定传递给 Razor 页面的模型类型。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-132">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="9ae0d-133">在前面的示例中，`@model` 行使 `PageModel` 派生的类可用于 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-133">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="9ae0d-134">在页面上的 `@Html.DisplayNameFor` 和 `@Html.DisplayName` [HTML 帮助程序](https://docs.microsoft.com/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)中使用该模型。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-134">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayName` [HTML Helpers](https://docs.microsoft.com/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

<span data-ttu-id="9ae0d-135"><!-- why don't xref links work?
[HTML Helpers 2](xref:aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs)
-->

<a name="vd"></a>
### ViewData 和布局</span><span class="sxs-lookup"><span data-stu-id="9ae0d-135"><!-- why don't xref links work?
[HTML Helpers 2](xref:aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs)
-->

<a name="vd"></a>
### ViewData and layout</span></span>

<span data-ttu-id="9ae0d-136">考虑下列代码：</span><span class="sxs-lookup"><span data-stu-id="9ae0d-136">Consider the following code:</span></span>

<span data-ttu-id="9ae0d-137">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Index.cshtml?range=1-6&highlight=4-)]</span><span class="sxs-lookup"><span data-stu-id="9ae0d-137">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Index.cshtml?range=1-6&highlight=4-)]</span></span>

<span data-ttu-id="9ae0d-138">前面突出显示的代码是 Razor 转换为 C# 的一个示例。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-138">The preceding highligted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="9ae0d-139">`{` 和 `}` 字符括住 C# 代码块。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-139">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="9ae0d-140">`Controller` 基类具有 `ViewData` 字典属性，可用于添加要传递到某个视图的数据。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-140">The `Controller` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="9ae0d-141">可以使用键/值模式将对象添加到 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-141">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="9ae0d-142">在前面的示例中，“Title”属性被添加到 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-142">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span> <span data-ttu-id="9ae0d-143">“Title”属性用于 Pages/_Layout.cshtml 文件。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-143">The "Title" property is used in the *Pages/_Layout.cshtml* file.</span></span> <span data-ttu-id="9ae0d-144">以下标记显示 Pages/_Layout.cshtml 文件的前几行。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-144">The following markup shows the first few lines of the *Pages/_Layout.cshtml* file.</span></span>

<span data-ttu-id="9ae0d-145">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout1.cshtml?highlight=6-)]</span><span class="sxs-lookup"><span data-stu-id="9ae0d-145">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout1.cshtml?highlight=6-)]</span></span>

<span data-ttu-id="9ae0d-146">行 `@*Markup removed for brevity.*@` 为 Razor 注释。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-146">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="9ae0d-147">与 HTML 注释不同 (`<!-- -->`)，Razor 注释不会发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-147">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

<span data-ttu-id="9ae0d-148">运行应用并测试项目中的链接（“主页”、“关于”、“联系人”、“创建”、“编辑”和“删除”）。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-148">Run the app and test the links in the project (**Home**, **About**, **Contact**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="9ae0d-149">每个页面都设置有标题，可以在浏览器选项卡中看到标题。将某个页面加入书签时，标题用于该书签。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-149">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span> <span data-ttu-id="9ae0d-150">Pages/Index.cshtml 和 Pages/Movies/Index.cshtml 当前具有相同的标题，但可以修改它们以具有不同的值。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-150">*Pages/Index.cshtml* and *Pages/Movies/Index.cshtml* currently have the same title, but you can modify them to have different values.</span></span>

<span data-ttu-id="9ae0d-151">在 Pages/_ViewStart.cshtml 文件中设置 `Layout` 属性：</span><span class="sxs-lookup"><span data-stu-id="9ae0d-151">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

<span data-ttu-id="9ae0d-152">[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/_ViewStart.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="9ae0d-152">[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/_ViewStart.cshtml)]</span></span>

<span data-ttu-id="9ae0d-153">前面的标记针对所有 Razor 文件将布局文件设置为 Pages 文件夹下的 Pages/_Layout.cshtml。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-153">The preceding markup sets the layout file to *Pages/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="9ae0d-154">请参阅[布局](xref:mvc/razor-pages/index#layout)了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-154">See [Layout](xref:mvc/razor-pages/index#layout) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="9ae0d-155">更新布局</span><span class="sxs-lookup"><span data-stu-id="9ae0d-155">Update the layout</span></span>

<span data-ttu-id="9ae0d-156">更改 Pages/_Layout.cshtml 文件中的 `<title>` 元素以使用较短的字符串。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-156">Change the `<title>` element in the *Pages/_Layout.cshtml* file to use a shorter string.</span></span>

<span data-ttu-id="9ae0d-157">[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/_Layout.cshtml?range=1-6&highlight=6-)]</span><span class="sxs-lookup"><span data-stu-id="9ae0d-157">[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/_Layout.cshtml?range=1-6&highlight=6-)]</span></span>

<span data-ttu-id="9ae0d-158">查找 Pages/_Layout.cshtml 文件中的以下定位点元素。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-158">Find the following anchor element in the *Pages/_Layout.cshtml* file.</span></span>

```cshtml
<a asp-page="/Index" class="navbar-brand">RazorPagesMovie</a>
```
<span data-ttu-id="9ae0d-159">将前面的元素替换为以下标记。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-159">Replace the preceding element with the following markup.</span></span>

```cshtml
<a asp-page="/Movies/Index" class="navbar-brand">RpMovie</a>
```

<span data-ttu-id="9ae0d-160">前面的定位点元素是一个[标记帮助程序](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-160">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="9ae0d-161">此处它是[定位点标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper)。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-161">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper).</span></span> <span data-ttu-id="9ae0d-162">`asp-page="/Movies/Index"` 标记帮助程序属性和值可以创建指向 `/Movies/Index` Razor 页面的链接。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-162">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span>

<span data-ttu-id="9ae0d-163">保存所做的更改，并通过单击“RpMovie”链接测试应用。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-163">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="9ae0d-164">请参阅 GitHub 中的 [_Layout.cshtml](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/_Layout.cshtml) 文件。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-164">See the [_Layout.cshtml](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/_Layout.cshtml) file in GitHub.</span></span>

### <a name="the-create-code-behind-page"></a><span data-ttu-id="9ae0d-165">创建代码隐藏页面</span><span class="sxs-lookup"><span data-stu-id="9ae0d-165">The Create code-behind page</span></span>

<span data-ttu-id="9ae0d-166">检查 Pages/Movies/Create.cshtml.cs 代码隐藏文件：</span><span class="sxs-lookup"><span data-stu-id="9ae0d-166">Examine the *Pages/Movies/Create.cshtml.cs* code-behind file:</span></span>

<span data-ttu-id="9ae0d-167">[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Create.cshtml.cs?name=snippetALL)]</span><span class="sxs-lookup"><span data-stu-id="9ae0d-167">[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Create.cshtml.cs?name=snippetALL)]</span></span>

<span data-ttu-id="9ae0d-168">`OnGet` 方法初始化页面所需的任何状态。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-168">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="9ae0d-169">“创建”页没有任何要初始化的状态。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-169">The Create page doesn't have any state to initialize.</span></span> <span data-ttu-id="9ae0d-170">`Page` 方法创建用于呈现 Create.cshtml 页的 `PageResult` 对象。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-170">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="9ae0d-171">`Movie` 属性使用 `[BindProperty]` 特性来选择加入[模型绑定](xref:mvc/models/model-binding)。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-171">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="9ae0d-172">当“创建”表单发布表单值时，ASP.NET Core 运行时将发布的值绑定到 `Movie` 模型。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-172">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="9ae0d-173">当页面发布表单数据时，运行 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="9ae0d-173">The `OnPostAsync` method is run when the page posts form data:</span></span>

<span data-ttu-id="9ae0d-174">[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Create.cshtml.cs?name=snippetPost)]</span><span class="sxs-lookup"><span data-stu-id="9ae0d-174">[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Create.cshtml.cs?name=snippetPost)]</span></span>

<span data-ttu-id="9ae0d-175">如果不存在任何模型错误，将重新显示表单，以及发布的任何表单数据。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-175">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="9ae0d-176">在发布表单前，可以在客户端捕获到大部分模型错误。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-176">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="9ae0d-177">模型错误的一个示例是，发布的日期字段值无法转换为日期。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-177">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="9ae0d-178">我们将在本教程后面的内容中讨论有关客户端验证和模型验证的更多信息。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-178">We'll talk more about client-side validation and model validation later in the tutorial.</span></span>

<span data-ttu-id="9ae0d-179">如果不存在模型错误，将保存数据，并且浏览器会重定向到索引页。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-179">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="9ae0d-180">创建 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="9ae0d-180">The Create Razor Page</span></span>

<span data-ttu-id="9ae0d-181">检查 Pages/Movies/Create.cshtml Razor 页面文件：</span><span class="sxs-lookup"><span data-stu-id="9ae0d-181">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

<span data-ttu-id="9ae0d-182">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Create.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="9ae0d-182">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Create.cshtml)]</span></span>

<span data-ttu-id="9ae0d-183">Visual Studio 显示用于标记帮助程序的特殊字体的 `<form method="post">` 标记。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-183">Visual Studio displays the `<form method="post">` tag in a distinctive font used for Tag Helpers.</span></span> <span data-ttu-id="9ae0d-184">`<form method="post">` 元素是一个[表单标记帮助程序](xref:mvc/views/working-with-forms#the-form-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-184">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="9ae0d-185">表单标记帮助程序会自动包含[防伪令牌](xref:security/anti-request-forgery)。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-185">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

![Create.cshtml 页的 VS17 视图](page/_static/th.png)

<span data-ttu-id="9ae0d-187">基架引擎在模型中为每个字段（ID 除外）创建 Razor 标记，如下所示：</span><span class="sxs-lookup"><span data-stu-id="9ae0d-187">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

<span data-ttu-id="9ae0d-188">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Create.cshtml?range=15-20)]</span><span class="sxs-lookup"><span data-stu-id="9ae0d-188">[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Create.cshtml?range=15-20)]</span></span>

<span data-ttu-id="9ae0d-189">[验证标记帮助程序](xref:mvc/views/working-with-forms#the-validation-tag-helpers)（`<div asp-validation-summary` 和 ` <span asp-validation-for`）显示验证错误。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-189">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and ` <span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="9ae0d-190">本系列后面的部分将更详细地讨论有关验证的信息。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-190">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="9ae0d-191">[标签标记帮助程序](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) 生成标签描述和 `Title` 属性的 `for` 特性。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-191">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="9ae0d-192">[输入标记帮助程序](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control" />`) 使用 [DataAnnotations](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 属性并在客户端生成 jQuery 验证所需的 HTML 属性。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-192">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control" />`) uses the [DataAnnotations](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="9ae0d-193">下一教程将介绍 SQL Server LocalDB 和数据库的种子设定。</span><span class="sxs-lookup"><span data-stu-id="9ae0d-193">The next tutorial explains SQL Server LocalDB and seeding the database.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="9ae0d-194">[上一篇：添加模型](xref:tutorials/razor-pages/modelz)
[下一篇：SQL Server LocalDB](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="9ae0d-194">[Previous: Adding a model](xref:tutorials/razor-pages/modelz)
[Next: SQL Server LocalDB](xref:tutorials/razor-pages/sql)</span></span>
