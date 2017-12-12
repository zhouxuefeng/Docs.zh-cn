---
title: "Razor 页与 EF 核心的排序、 筛选器、 分页-8 3"
author: rick-anderson
description: "在本教程中，你将添加排序、 筛选和分页功能页上使用 ASP.NET Core 和实体框架核心。"
keywords: "ASP.NET 核心、 实体框架核心、 排序、 筛选器、 分页，分组"
ms.author: riande
ms.date: 10/22/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-rp/sort-filter-page
ms.openlocfilehash: 5e17663b88a622101245228e9372db55e4e874be
ms.sourcegitcommit: 703593d5fd14076e79be2ba75a5b8da12a60ab15
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2017
---
# <a name="sorting-filtering-paging-and-grouping---ef-core-with-razor-pages-3-of-8"></a>排序、 筛选、 分页和分组-带有 Razor 页 (8 的 3) 的 EF 核心

通过[Tom Dykstra](https://github.com/tdykstra)， [Rick Anderson](https://twitter.com/RickAndMSFT)，和[Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE[about the series](../../includes/RP-EF/intro.md)]

在此教程、 排序、 筛选、 分组和分页，将添加功能。

下图显示已完成的页。 列标题是可单击的链接来对列进行排序。 单击列标题反复将升序和降序之间切换。

![学生索引页](sort-filter-page/_static/paging.png)

如果你遇到无法解决的问题，请下载[对此阶段已完成应用程序](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting)。

## <a name="add-sorting-to-the-index-page"></a>添加排序索引页面

添加字符串转换为*Students/Index.cshtml.cs* `PageModel`以包含排序的参数：

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet1&highlight=10-13)]


更新*Students/Index.cshtml.cs* `OnGetAsync`替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly)]

前面的代码接收`sortOrder`从 URL 中的查询字符串参数。 由生成的 URL （包括查询字符串）[定位点标记帮助器](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper
)

`sortOrder`参数为"Name"日期"。 `sortOrder`参数可以选择后跟"_desc"指定降序顺序。 默认排序顺序为升序。

在索引页面请求从**学生**链接，没有任何查询字符串。 学生按姓氏升序顺序显示。 按姓氏升序顺序中是默认值 （位于通过用例）`switch`语句。 当用户单击列标题链接，相应`sortOrder`在查询字符串值中提供值。

`NameSort`和`DateSort`Razor 页用于配置使用相应的查询字符串值的列标题超链接：

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=3-4)]

下面的代码包含 C# [？: 运算符](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/conditional-operator):

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_Ternary)]

第一行指定当`sortOrder`为 null 或为空，`NameSort`设置为"name_desc。" 如果`sortOrder`是**不**null 或为空，`NameSort`设置为空字符串。

`?: operator`是也称为的三元运算符。

这两个语句启用要设置列标题的超链接，如下所示的视图：

| 当前的排序顺序 | 最后一个名称超链接 | 日期超链接 |
|:--------------------:|:-------------------:|:--------------:|
| 上次名称升序排列 | descending        | ascending      |
| 上次名称降序 | ascending           | ascending      |
| 升序的日期       | ascending           | descending     |
| 日期降序      | ascending           | ascending      |

该方法使用 LINQ to Entities 指定要作为排序依据的列。 此代码初始化`IQueryable<Student> `之前 switch 语句中，并在 switch 语句中对其进行修改：

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=6-)]

 当`IQueryable`创建或修改，没有查询发送到数据库。 查询未执行直到`IQueryable`对象转换为集合。 `IQueryable`将转换为集合通过调用方法，如`ToListAsync`。 因此，`IQueryable`代码将不会执行以下语句之前的单个查询中的结果：

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync`无法获取详细具有大量列。

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a>将列标题的超链接添加到学生索引视图

中的代码替换*Students/Index.cshtml*，替换为以下突出显示的代码：

[!code-html[](intro/samples/cu/Pages/Students/Index2.cshtml?highlight=17-19,25-27)]

前面的代码：

* 将超链接到添加`LastName`和`EnrollmentDate`列标题。
* 使用中的信息`NameSort`和`DateSort`设置与当前的排序顺序值的超链接。

若要确认排序在有效运行：

* 运行应用并选择**学生**选项卡。
* 单击**姓氏**。
* 单击**注册日期**。

若要获取更好地了解代码：

* 在*Student/Index.cshtml.cs*上, 设置断点`switch (sortOrder)`。
* 为添加监视`NameSort`和`DateSort`。
* 在*Student/Index.cshtml*上, 设置断点`@Html.DisplayNameFor(model => model.Student[0].LastName)`。

单步执行调试程序。

## <a name="add-a-search-box-to-the-students-index-page"></a>将一个搜索框添加到学生索引页

若要添加到学生索引页筛选：

* 一个文本框和提交按钮添加到 Razor 页。 文本框中提供了一个搜索字符串的第一个或最后一个名称。
* 代码隐藏文件更新为使用第文本框的值。

### <a name="add-filtering-functionality-to-the-index-method"></a>将筛选功能添加到索引方法

更新*Students/Index.cshtml.cs* `OnGetAsync`替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

前面的代码：

* 将添加`searchString`参数`OnGetAsync`方法。 从下一节中添加的文本框中接收到搜索字符串值。
* 添加到 LINQ 语句`Where`子句。 `Where`子句选择仅学生的名字或姓氏包含搜索字符串。 只有在要搜索的值执行 LINQ 语句。

注意： 在前面的代码调用`Where`方法`IQueryable`在服务器上处理对象和筛选器。 在某些情况下，可能会调用 tha 应用`Where`作为扩展方法对内存中集合的方法。 例如，假设`_context.Students`从 EF 核心更改`DbSet`返回的存储库方法`IEnumerable`集合。 结果通常将相同，但在某些情况下可能会不同。

例如，.NET Framework 实现的`Contains`将默认执行区分大小写的比较。 在 SQL Server，`Contains`区分大小写由 SQL Server 实例的排序规则设置。 SQL Serve 默认为不区分大小写。 `ToUpper`无法调用来进行测试显式不区分大小写：

`Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`

前面的代码将确保结果不区分大小写如果代码更改来使用`IEnumerable`。 当`Contains`上调用`IEnumerable`集合，用于实现.NET 核心。 当`Contains`上调用`IQueryable`对象，则使用数据库实现。 返回`IEnumerable`从存储库可以具有显著的性能罚金：

1. 从 DB 服务器返回所有行。
1. 筛选器应用于应用程序中的所有返回的行。

没有对性能产生负面影响为调用`ToUpper`。 `ToUpper`代码 TSQL SELECT 语句的 WHERE 子句中添加了一个函数。 添加的函数会阻止优化器使用索引。 假设 SQL 安装为不区分大小写，最好是避免`ToUpper`不需要时调用。

### <a name="add-a-search-box-to-the-student-index-view"></a>将一个搜索框添加到学生索引视图

在*Views/Student/Index.cshtml*，添加以下突出显示的代码，以创建**搜索**按钮和各种的 chrome。

[!code-html[](intro/samples/cu/Pages/Students/Index3.cshtml?highlight=14-23&range=1-25)]

前面的代码使用`<form>`[标记帮助器](xref:mvc/views/tag-helpers/intro)添加搜索文本框和按钮。 默认情况下，`<form>`标记帮助器提交 post 的窗体数据。 使用 POST，参数进行传递 HTTP 消息正文中，不能在 URL。 当使用 HTTP GET 时，窗体数据是在 URL 中传递作为查询字符串。 传递具有查询字符串的数据使用户能够创建 URL 的书签。 [W3C 准则](https://www.w3.org/2001/tag/doc/whenToUseGet.html)建议的操作未导致更新时，应使用 GET。

测试应用程序：

* 选择**学生**选项卡并输入搜索字符串。
* 选择**搜索**。

请注意该 URL 包含搜索字符串。

```html
http://localhost:5000/Students?SearchString=an
```

如果页面标有书签，书签将包含到页面的 URL 和`SearchString`查询字符串。 `method="get"`中`form`标记是什么导致要生成的查询字符串。

目前，当选择列标题排序链接时，筛选器值从**搜索**框将丢失。 在下一部分中被固定的丢失的筛选器值。

## <a name="add-paging-functionality-to-the-students-index-page"></a>将分页功能添加到学生索引页

在本部分中，`PaginatedList`创建类，用以支持分页。 `PaginatedList`类使用`Skip`和`Take`语句而不是检索表的所有行的服务器上的数据进行筛选。 下图显示了分页按钮。

![学生索引页，带有分页链接](sort-filter-page/_static/paging.png)

在项目文件夹中，创建`PaginatedList.cs`替换为以下代码：

[!code-csharp[Main](intro/samples/cu/PaginatedList.cs)]

`CreateAsync`在前面的代码的方法采用页大小和页码，并应用相应`Skip`和`Take`向语句`IQueryable`。 当`ToListAsync`上调用`IQueryable`，它将返回包含请求的页的列表。 属性`HasPreviousPage`和`HasNextPage`用于启用或禁用**上一步**和**下一步**分页按钮。

`CreateAsync`方法用于创建`PaginatedList<T>`。 构造函数不能创建`PaginatedList<T>`对象，构造函数不能运行异步代码。

## <a name="add-paging-functionality-to-the-index-method"></a>将分页功能添加到索引方法

在*Students/Index.cshtml.cs*，更新的类型`Student`从`IList<Student>`到`PaginatedList<Student>`:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPageType)]

更新*Students/Index.cshtml.cs* `OnGetAsync`替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage&highlight=1-4,7-14,41-)]

前面的代码将添加的页索引当前`sortOrder`，和`currentFilter`向方法签名。

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage2)]

所有参数都为 null 时：

* 从调用页**学生**链接。
* 用户未单击分页或排序链接。

单击分页链接时，页索引变量将包含要显示的页码。

`CurrentSort`Razor 页上提供了当前的排序顺序。 当前的排序顺序必须包含在分页链接，以保持在分页时的排序顺序。

`CurrentFilter`Razor 页上提供了当前的筛选器字符串。 `CurrentFilter`值：

* 必须以维护期间分页的筛选器设置包含在分页链接。
* 必须将还原到文本框中时重新显示页。

如果更改搜索字符串，则在分页时，页面将重置为 1。 页面必须重置为 1，因为新的筛选器可能会导致不同的数据，以显示。 在输入搜索值时和**提交**选择：

* 更改搜索字符串。
* `searchString`参数不为 null。

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage3)]

`PaginatedList.CreateAsync`方法将学生查询转换为支持分页的集合类型中的学生的单页。 学生的单个页面传递到 Razor 页。

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage4)]

在两个问号`PaginatedList.CreateAsync`表示[null 合并运算符](https://docs.microsoft.com/ dotnet/csharp/language-reference/operators/null-conditional-operator)。 Null 合并运算符定义可以为 null 的类型的默认值。 表达式`(pageIndex ?? 1)`意味着返回的值`pageIndex`如果它的值。 如果`pageIndex`不包含一个值，则返回 1。

## <a name="add-paging-links-to-the-student-razor-page"></a>将分页链接添加到学生 Razor 页

更新中的标记*Students/Index.cshtml*。 突出显示所做的更改：

[!code-html[](intro/samples/cu/Pages/Students/Index.cshtml?highlight=28-31,37-40,68-)]

列标题链接使用查询字符串将传递到当前的搜索字符串`OnGetAsync`方法，以便用户可以在筛选器的结果中进行排序：

[!code-html[](intro/samples/cu/Pages/Students/Index.cshtml?range=28-31)]

分页按钮显示通过标记帮助程序：

[!code-html[](intro/samples/cu/Pages/Students/Index.cshtml?range=72-)]

运行应用并导航到学生页。

* 若要使确保分页工作原理，请单击不同的排序顺序中的分页链接。
* 若要确认分页适用于排序和筛选，请输入搜索字符串，然后重分页。

![学生索引页，带有分页链接](sort-filter-page/_static/paging.png)

若要获取更好地了解代码：

* 在*Student/Index.cshtml.cs*上, 设置断点`switch (sortOrder)`。
* 为添加监视`NameSort`， `DateSort`， `CurrentSort`，和`Model.Student.PageIndex`。
* 在*Student/Index.cshtml*上, 设置断点`@Html.DisplayNameFor(model => model.Student[0].LastName)`。

单步执行调试程序。

## <a name="update-the-about-page-to-show-student-statistics"></a>更新有关页以显示学生统计信息

在此步骤中， *Pages/About.cshtml*更新以显示多少学生已注册的每个注册日期。 更新使用分组，并包括以下步骤：

* 创建使用的数据的视图模型类**有关**页。
* 修改有关 Razor 页和代码隐藏文件。

### <a name="create-the-view-model"></a>创建视图模型

创建*SchoolViewModels*文件夹中的*模型*文件夹。

在*SchoolViewModels*文件夹中，添加*EnrollmentDateGroup.cs*替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="update-the-about-code-behind-page"></a>更新有关代码隐藏页

更新*Pages/About.cshtml.cs*文件替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Pages/About.cshtml.cs)]

LINQ 语句学生实体分组按注册日期，计算每个组中的实体数并将结果存储在集合中的`EnrollmentDateGroup`查看模型对象。

注意： LINQ`group`命令当前不支持通过 EF 核心。 在前面的代码中，从 SQL Server 返回所有学生记录。 `group` Razor 页应用，不是在 SQL Server 中应用命令。 EF 核心 2.1 将支持此 LINQ`group`运算符，并且分组发生在 SQL Server 上。 请参阅[关系： 支持转换到 SQL groupby （）](https://github.com/aspnet/EntityFrameworkCore/issues/2341)。 [EF 核心 2.1](https://github.com/aspnet/EntityFrameworkCore/wiki/roadmap)将与.NET 核心 2.1 一起发布。 有关详细信息，请参阅[.NET 核心路线图](https://github.com/dotnet/core/blob/master/roadmap.md)。

### <a name="modify-the-about-razor-page"></a>修改有关 Razor 页面

中的代码替换*Views/Home/About.cshtml*文件替换为以下代码：

[!code-html[](intro/samples/cu/Pages/About.cshtml)]

运行应用程序并导航到关于页面。 每个注册日期的学生计数显示在表格中。

如果你遇到无法解决的问题，请下载[对此阶段已完成应用程序](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting)。

![有关页面](sort-filter-page/_static/about.png)

## <a name="additional-resources"></a>其他资源

* [调试 ASP.NET Core 2.x 源](https://github.com/aspnet/Docs/issues/4155)

在下一步的教程中，该应用使用迁移来更新数据模型。

>[!div class="step-by-step"]
[上一页](xref:data/ef-rp/crud)
[下一页](xref:data/ef-rp/migrations)