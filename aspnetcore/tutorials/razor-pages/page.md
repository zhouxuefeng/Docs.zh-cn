---
title: "ASP.NET Core 中已搭建基架的 Razor 页面"
author: rick-anderson
description: "介绍通过搭建基架生成的 Razor 页面。"
keywords: "ASP.NET Core, Razor 页面, Razor, MVC"
ms.author: riande
manager: wpickett
ms.date: 09/27/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages/page
ms.openlocfilehash: 7ae83b9bdadf5ebf8846b0c09c585da406708d12
ms.sourcegitcommit: 94b7e0f95b92c98b182a93d2b3dc0287e5f97976
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2017
---
# <a name="scaffolded-razor-pages-in-aspnet-core"></a>ASP.NET Core 中已搭建基架的 Razor 页面

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

本教程介绍在上一教程主题[添加模型](xref:tutorials/razor-pages/model#scaffold-the-movie-model)中通过搭建基架创建的 Razor 页面。 

[查看或下载](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie)示例。

## <a name="the-create-delete-details-and-edit-pages"></a>“创建”、“删除”、“详细信息”和“编辑”页。

检查 Pages/Movies/Index.cshtml.cs 代码隐藏文件：[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

Razor 页面派生自 `PageModel`。 按照约定，`PageModel` 派生的类称为 `<PageName>Model`。 此构造函数使用[依赖关系注入](xref:fundamentals/dependency-injection)将 `MovieContext` 添加到页。 所有已搭建基架的页面都遵循此模式。

对页面发出请求时，`OnGetAsync` 方法向 Razor 页面返回影片列表。 在 Razor 页面上调用 `OnGetAsync` 或 `OnGet` 以初始化页面状态。 在这种情况下，`OnGetAsync` 将获得要显示的影片列表。

检查 Pages/Movies/Index.cshtml Razor 页面：

[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

Razor 可以从 HTML 转换为 C# 或 Razor 特定标记。 当 `@` 符号后跟 [Razor 保留关键字](xref:mvc/views/razor#razor-reserved-keywords)时，它会转换为 Razor 特定标记，否则会转换为 C#。

`@page` Razor 指令将文件转换为一个 MVC 操作 &mdash;，这意味着它可以处理请求。 `@page` 必须是页面上的第一个 Razor 指令。 `@page` 是转换到 Razor 特定标记的一个示例。 有关详细信息，请参阅 [Razor 语法](xref:mvc/views/razor#razor-syntax)。

检查以下 HTML 帮助程序中使用的 Lambda 表达式：

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title))
```

`DisplayNameFor` HTML 帮助程序检查 Lambda 表达式中引用的 `Title` 属性来确定显示名称。 检查 Lambda 表达式（而非求值）。 这意味着当 `model`、`model.Movie` 或 `model.Movie[0]` 为 `null` 或为空时，不会存在任何访问冲突。 对 Lambda 表达式求值时（例如，使用 `@Html.DisplayFor(modelItem => item.Title)`），将求得该模型的属性值。

<a name="md"></a>
### <a name="the-model-directive"></a>@model 指令

[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

`@model` 指令指定传递给 Razor 页面的模型类型。 在前面的示例中，`@model` 行使 `PageModel` 派生的类可用于 Razor 页面。 在页面上的 `@Html.DisplayNameFor` 和 `@Html.DisplayName` [HTML 帮助程序](https://docs.microsoft.com/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)中使用该模型。

<!-- why don't xref links work?
[HTML Helpers 2](xref:aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs)
-->

<a name="vd"></a>
### ViewData 和布局

考虑下列代码：

[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-)]

前面突出显示的代码是 Razor 转换为 C# 的一个示例。 `{` 和 `}` 字符括住 C# 代码块。

`PageModel` 基类具有 `ViewData` 字典属性，可用于添加要传递到某个视图的数据。 可以使用键/值模式将对象添加到 `ViewData` 字典。 在前面的示例中，“Title”属性被添加到 `ViewData` 字典。 “Title”属性用于 Pages/_Layout.cshtml 文件。 以下标记显示 Pages/_Layout.cshtml 文件的前几行。

[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout1.cshtml?highlight=6-)]

行 `@*Markup removed for brevity.*@` 为 Razor 注释。 与 HTML 注释不同 (`<!-- -->`)，Razor 注释不会发送到客户端。

运行应用并测试项目中的链接（“主页”、“关于”、“联系人”、“创建”、“编辑”和“删除”）。 每个页面都设置有标题，可以在浏览器选项卡中看到标题。将某个页面加入书签时，标题用于该书签。 Pages/Index.cshtml 和 Pages/Movies/Index.cshtml 当前具有相同的标题，但可以修改它们以具有不同的值。

在 Pages/_ViewStart.cshtml 文件中设置 `Layout` 属性：

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/_ViewStart.cshtml)]

前面的标记针对所有 Razor 文件将布局文件设置为 Pages 文件夹下的 Pages/_Layout.cshtml。 请参阅[布局](xref:mvc/razor-pages/index#layout)了解详细信息。

### <a name="update-the-layout"></a>更新布局

更改 Pages/_Layout.cshtml 文件中的 `<title>` 元素以使用较短的字符串。

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/_Layout.cshtml?range=1-6&highlight=6)]

查找 Pages/_Layout.cshtml 文件中的以下定位点元素。

```cshtml
<a asp-page="/Index" class="navbar-brand">RazorPagesMovie</a>
```
将前面的元素替换为以下标记。

```cshtml
<a asp-page="/Movies/Index" class="navbar-brand">RpMovie</a>
```

前面的定位点元素是一个[标记帮助程序](xref:mvc/views/tag-helpers/intro)。 此处它是[定位点标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)。 `asp-page="/Movies/Index"` 标记帮助程序属性和值可以创建指向 `/Movies/Index` Razor 页面的链接。

保存所做的更改，并通过单击“RpMovie”链接测试应用。 请参阅 GitHub 中的 [_Layout.cshtml](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/_Layout.cshtml) 文件。

### <a name="the-create-code-behind-page"></a>创建代码隐藏页面

检查 Pages/Movies/Create.cshtml.cs 代码隐藏文件：

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

`OnGet` 方法初始化页面所需的任何状态。 “创建”页没有任何要初始化的状态。 `Page` 方法创建用于呈现 Create.cshtml 页的 `PageResult` 对象。

`Movie` 属性使用 `[BindProperty]` 特性来选择加入[模型绑定](xref:mvc/models/model-binding)。 当“创建”表单发布表单值时，ASP.NET Core 运行时将发布的值绑定到 `Movie` 模型。

当页面发布表单数据时，运行 `OnPostAsync` 方法：

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

如果不存在任何模型错误，将重新显示表单，以及发布的任何表单数据。 在发布表单前，可以在客户端捕获到大部分模型错误。 模型错误的一个示例是，发布的日期字段值无法转换为日期。 我们将在本教程后面的内容中讨论有关客户端验证和模型验证的更多信息。

如果不存在模型错误，将保存数据，并且浏览器会重定向到索引页。

### <a name="the-create-razor-page"></a>创建 Razor 页面

检查 Pages/Movies/Create.cshtml Razor 页面文件：

[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

Visual Studio 显示用于标记帮助程序的特殊字体的 `<form method="post">` 标记。 `<form method="post">` 元素是一个[表单标记帮助程序](xref:mvc/views/working-with-forms#the-form-tag-helper)。 表单标记帮助程序会自动包含[防伪令牌](xref:security/anti-request-forgery)。

![Create.cshtml 页的 VS17 视图](page/_static/th.png)

基架引擎在模型中为每个字段（ID 除外）创建 Razor 标记，如下所示：

[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

[验证标记帮助程序](xref:mvc/views/working-with-forms#the-validation-tag-helpers)（`<div asp-validation-summary` 和 ` <span asp-validation-for`）显示验证错误。 本系列后面的部分将更详细地讨论有关验证的信息。

[标签标记帮助程序](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) 生成标签描述和 `Title` 属性的 `for` 特性。

[输入标记帮助程序](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control" />`) 使用 [DataAnnotations](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 属性并在客户端生成 jQuery 验证所需的 HTML 属性。

下一教程将介绍 SQL Server LocalDB 和数据库的种子设定。

>[!div class="step-by-step"]
[上一篇：添加模型](xref:tutorials/razor-pages/model)
[下一篇：SQL Server LocalDB](xref:tutorials/razor-pages/sql)
