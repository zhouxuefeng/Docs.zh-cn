---
title: "分部视图"
author: ardalis
description: "在 ASP.NET 核心 MVC 中使用分部视图"
keywords: "ASP.NET 核心，分部视图部分"
ms.author: riande
manager: wpickett
ms.date: 03/14/2017
ms.topic: article
ms.assetid: 4be1b12c-b74e-44ff-826b-99ce86e8d464
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/partial
ms.openlocfilehash: 8f890009f71d4c1f693ee06dcd448e9803164aca
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="partial-views"></a>分部视图

通过[Steve Smith](https://ardalis.com/)， [Maher JENDOUBI](https://twitter.com/maherjend)，和[Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET 核心 MVC 支持分部视图，当您想要不同的视图之间共享的网页的可重用部件时很有用。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/partial/sample)

## <a name="what-are-partial-views"></a>分部视图有哪些？

分部视图是另一个视图中呈现的视图。 通过执行分部视图生成的 HTML 输出呈现到调用 （或父） 视图。 分部视图和视图一样使用*.cshtml*文件扩展名。

## <a name="when-should-i-use-partial-views"></a>何时应使用分部视图？

分部视图是分解成较小组件的大视图的有效方法。 它们可以减少重复查看内容，并允许重复使用的视图元素。 常见的布局元素应指定在[_Layout.cshtml](layout.md)。 非布局可重用内容可以封装到分部视图。

如果你有几个逻辑部分组成的复杂页，它可以帮助使用其自己的分部视图为每个段。 页上的其余部分分开，可以查看每个页片段，因为它仅包含的整体页结构和调用来呈现分部视图为页本身的视图变得简单得多。

提示： 按照[不重复自己原则](http://deviq.com/don-t-repeat-yourself/)在您的视图。

## <a name="declaring-partial-views"></a>声明分部视图

分部视图创建与其他视图类似： 创建*.cshtml*文件内*视图*文件夹。 分部视图和常规视图之间没有语义差异-它们只是以不同的方式呈现。 你可以直接从控制器的返回的视图`ViewResult`，和可以为分部视图使用相同的视图。 如何呈现的视图和分部视图之间的主要区别是分部视图不要运行*_ViewStart.cshtml* (时视图-详细了解*_ViewStart.cshtml*中[布局](layout.md)).

## <a name="referencing-a-partial-view"></a>引用了分部视图

在视图页中，有几种方法可以在其中呈现了分部视图。 若要使用的是最简单`Html.Partial`，它将返回`IHtmlString`，并且可以通过将调用具有引用`@`:

[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Home/About.cshtml?range=9)]

`PartialAsync`方法对于分部视图包含异步代码 （尽管在视图中的代码通常不鼓励） 是可用：

[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Home/About.cshtml?range=8)]

你可以呈现具有的分部视图`RenderPartial`。 此方法不返回结果;流式处理直接响应呈现的输出。 因为它不返回结果，它必须调用在 Razor 代码块中 (你还可以调用`RenderPartialAsync`如有必要):

[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Home/About.cshtml?range=10-12)]

因为它直接，流式处理结果`RenderPartial`和`RenderPartialAsync`可能在某些情况下更好地执行。 但是，在大多数情况下，建议你使用`Partial`和`PartialAsync`。

> [!NOTE]
> 如果你的视图需要执行代码，建议的模式是使用[视图组件](view-components.md)而不是分部视图。

### <a name="partial-view-discovery"></a>分部视图发现

当引用了分部视图，您可以参考以下几种方式其位置：

```text
// Uses a view in current folder with this name
// If none is found, searches the Shared folder
@Html.Partial("ViewName")

// A view with this name must be in the same folder
@Html.Partial("ViewName.cshtml")

// Locate the view based on the application root
// Paths that start with "/" or "~/" refer to the application root
@Html.Partial("~/Views/Folder/ViewName.cshtml")
@Html.Partial("/Views/Folder/ViewName.cshtml")

// Locate the view using relative paths
@Html.Partial("../Account/LoginPartial.cshtml")
```

你可以具有相同名称的不同分部视图不同视图文件夹中。 当按名称 （不带文件扩展名） 引用视图，每个文件夹中的视图将与它们位于同一文件夹中使用分部视图。 你还可以指定默认的分部视图，若要使用，将其放在*共享*文件夹。 共享的分部视图将使用由任何没有其自己版本的分部视图的视图。 你可以有一个默认分部视图 (在*共享*)，该方法由具有与父视图相同的文件夹中的相同名称的部分视图重写。

可以是分部视图*链接*。 也就是说，分部视图可以调用另一个分部视图 （只要你不创建循环）。 在每个视图或分部视图中，相对路径始终是相对于该视图，而不是根或父视图。

> [!NOTE]
> 如果你声明[Razor](razor.md) `section`在分部视图中，它将看不到其父对象; 它将被限制为分部视图。

## <a name="accessing-data-from-partial-views"></a>从分部视图访问数据

当实例化了分部视图时，它将获取父视图的一份`ViewData`字典。 对分部视图内的数据所做的更新不会保留到父视图。 `ViewData`更改的分部视图在分部视图返回时丢失。

你可以将传递的实例`ViewDataDictionary`对分部视图：

```csharp
@Html.Partial("PartialName", customViewData)
   ```

此外可以将模型传递到了分部视图。 这可以是页面的视图模型某一部分或自定义对象。 你可以将模型传递到`Partial`，`PartialAsync`， `RenderPartial`，或`RenderPartialAsync`:

```csharp
@Html.Partial("PartialName", viewModel)
   ```

你可以将传递的实例`ViewDataDictionary`和分部视图到视图模型：

[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Articles/Read.cshtml?range=15-16)]

下面显示的标记*Views/Articles/Read.cshtml*视图，其中包含两个分部视图。 第二个分部视图将在模型中传递和`ViewData`对分部视图。 你可以将新传递`ViewData`同时保留现有的字典`ViewData`如果使用的构造函数重载`ViewDataDictionary`下面突出显示：

[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Articles/Read.cshtml)]

*视图/共享/AuthorPartial*:

[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Shared/AuthorPartial.cshtml)]

*ArticleSection*部分：

[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Articles/ArticleSection.cshtml)]

在运行时，它们将呈现到父视图中，呈现其本身内共享*_Layout.cshtml*

![分部视图输出](partial/_static/output.png)
