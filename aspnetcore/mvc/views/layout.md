---
title: "布局"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 29f12d1f-9734-48bd-bf1a-cee53a8ab700
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/layout
ms.openlocfilehash: 25aa5fc730d9076fdcf9d29cb5d9dfa75a246a1a
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="layout"></a>布局

通过[Steve Smith](https://ardalis.com/)

视图频繁共享 visual 和编程元素。 在本文中，你将了解如何使用通用的布局、 共享指令，以及在 ASP.NET 应用程序运行之前呈现视图的常见代码。

## <a name="what-is-a-layout"></a>什么是一种布局

大多数 web 应用都具有它们导航页之间为用户提供一致的体验的常用布局。 布局通常包括常见的用户界面元素，如应用程序标题、 导航或菜单元素和页脚。

![页面布局示例](layout/_static/page-layout.png)

在应用内的很多页也经常使用常见的 HTML 结构，如脚本和样式表。 在所有这些共享元素可定义*布局*文件，然后在应用程序中使用任何视图引用。 布局减少重复的代码在视图中，帮助他们按照[不重复自己 （模拟） 原则](http://deviq.com/don-t-repeat-yourself/)。

按照约定，名为 ASP.NET 应用程序的默认布局`_Layout.cshtml`。 Visual Studio ASP.NET 核心 MVC 项目模板包含在此布局文件`Views/Shared`文件夹：

![在解决方案资源管理器的视图文件夹](layout/_static/web-project-views.png)

此布局在应用程序定义视图的顶级模板。 应用不需要一种布局，并应用可以定义多个布局，与指定不同布局的不同视图。

示例`_Layout.cshtml`:

[!code-html[Main](../../common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=42,66)]

## <a name="specifying-a-layout"></a>指定布局

具有 razor 视图`Layout`属性。 单个视图，通过设置此属性指定布局：

[!code-html[Main](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

指定的布局可以使用完整路径 (示例： `/Views/Shared/_Layout.cshtml`) 或部分名称 (示例： `_Layout`)。 如果提供部分名称，Razor 视图引擎将搜索使用其标准的发现进程的布局文件。 控制器关联文件夹搜索第一次后, 跟`Shared`文件夹。 此发现过程等同于用来发现[分部视图](partial.md)。

默认情况下，必须调用每个布局`RenderBody`。 任何位置调用`RenderBody`是放置，就将呈现的视图的内容。

<a name=layout-sections-label></a>

### <a name="sections"></a>部分

布局 （可选） 可以引用一个或多个*部分*，通过调用`RenderSection`。 各节提供了一种方法来组织放置某些页元素。 每次调用`RenderSection`可以指定该部分为必需或可选。 如果未找到所需的部分，将引发异常。 单个视图指定要在中部分使用呈现的内容`@section`Razor 语法。 如果视图可用于定义一个部分，必须呈现 （或将发生错误）。

示例`@section`视图中的定义：

```html
@section Scripts {
     <script type="text/javascript" src="/scripts/main.js"></script>
   }
   ```

在上面的代码中，验证脚本添加到`scripts`包含一个窗体的视图上的部分。 同一个应用程序中的其他视图可能不需要任何其他脚本，并因此不需要定义脚本部分。

在视图中定义的部分是仅在其立即进行布局页中可用。 不能从它们、 将视图组件或查看系统的其他部分引用。

### <a name="ignoring-sections"></a>忽略部分

默认情况下，正文和内容页中的所有部分必须所有来呈现布局页。 Razor 视图引擎将强制实施此通过跟踪是否在呈现的正文和每个部分。

若要让视图引擎以忽略正文或部分，请调用`IgnoreBody`和`IgnoreSection`方法。

正文和 Razor 页中的每个部分必须呈现或忽略。

<a name=viewimports></a>

## <a name="importing-shared-directives"></a>导入共享的指令

视图可以使用 Razor 指令来执行很多东西，例如导入命名空间或执行[依赖关系注入](dependency-injection.md)。 可能在一组公共中指定指令由许多视图共享`_ViewImports.cshtml`文件。 `_ViewImports`文件支持以下指令：

* `@addTagHelper`

* `@removeTagHelper`

* `@tagHelperPrefix`

* `@using`

* `@model`

* `@inherits`

* `@inject`

该文件不支持其他 Razor 功能，如函数和部分定义。

示例`_ViewImports.cshtml`文件：

[!code-html[Main](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

`_ViewImports.cshtml`文件的 ASP.NET 核心 MVC 应用程序通常置于`Views`文件夹。 A`_ViewImports.cshtml`可以将文件放在任何文件夹中，它将仅应用于的情况下该文件夹及其子文件夹内的视图。 `_ViewImports`根级别中，开始处理文件，然后对截止到每个文件夹的位置的视图本身，以便设置指定的根级别可能重写在文件夹级别。

例如，如果根级别`_ViewImports.cshtml`文件指定`@model`和`@addTagHelper`，以及另一个`_ViewImports.cshtml`控制器相关视图的文件夹中的文件指定一个不同`@model`和添加另一个`@addTagHelper`，视图将有权访问这两个标记帮助程序，并将使用后者`@model`。

如果选择多个`_ViewImports.cshtml`文件是否为视图运行、 组合中包含的指令的行为`ViewImports.cshtml`文件将如下所示：

* `@addTagHelper``@removeTagHelper`： 所有运行，按顺序

* `@tagHelperPrefix`： 到视图最近的一个替代的任何其他

* `@model`： 到视图最近的一个替代的任何其他

* `@inherits`： 到视图最近的一个替代的任何其他

* `@using`： 所有都包含;将忽略重复项

* `@inject`： 对于每个属性，则最近到视图将覆盖具有相同的属性名称的任何其他

<a name=viewstart></a>

## <a name="running-code-before-each-view"></a>运行代码之前每个视图

如果你的代码需要在每个视图之前运行，这应置于`_ViewStart.cshtml`文件。 按照约定，`_ViewStart.cshtml`文件位于`Views`文件夹。 中列出的语句`_ViewStart.cshtml`在每个完整的视图 （不布局和非分部视图） 之前运行。 如[ViewImports.cshtml](xref:mvc/views/layout#viewimports)，`_ViewStart.cshtml`是分层。 如果`_ViewStart.cshtml`控制器关联的视图文件夹中定义文件，它将运行后的根目录中定义的一个`Views`文件夹 （如果有）。

示例`_ViewStart.cshtml`文件：

[!code-html[Main](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

上述文件指定所有视图将都使用`_Layout.cshtml`布局。

> [!NOTE]
> 既不`_ViewStart.cshtml`也不`_ViewImports.cshtml`通常置于`/Views/Shared`文件夹。 应直接在放置这些文件的应用程序级版本`/Views`文件夹。
