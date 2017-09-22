---
title: "ASP.NET 核心 MVC 与 EF 核心的排序、 筛选器、 分页-10 3"
author: tdykstra
description: "在本教程中，你将添加排序、 筛选和分页功能页上使用 ASP.NET Core 和实体框架核心。"
keywords: "ASP.NET 核心、 实体框架核心、 排序、 筛选器、 分页，分组"
ms.author: tdykstra
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: e6c1ff3c-5673-43bf-9c2d-077f6ada1f29
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: 59fff4dbf4736f0776aac4072f3f4e2d40119842
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="sorting-filtering-paging-and-grouping---ef-core-with-aspnet-core-mvc-tutorial-3-of-10"></a>排序、 筛选、 分页和分组-ASP.NET 核心 MVC 教程 (3 的 10) 的 EF 核心

通过[Tom Dykstra](https://github.com/tdykstra)和[Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso 大学示例 web 应用程序演示如何创建使用实体框架核心和 Visual Studio 的 ASP.NET 核心 MVC web 应用程序。 有关教程系列的信息，请参阅[序列中的第一个教程](intro.md)。

在前面的教程，你可以实现的学生实体的基本 CRUD 操作的网页的一组。 在本教程将向学生索引页添加排序、 筛选和分页功能。 你还将创建简单分组的页。

下图显示哪些页面将如下所示完成后。 列标题是用户可以单击以按该列排序的链接。 单击列标题反复将在升序和降序之间切换。

![学生索引页](sort-filter-page/_static/paging.png)

## <a name="add-column-sort-links-to-the-students-index-page"></a>将列排序链接添加到学生索引页

若要添加排序学生索引页，你将更改`Index`学生控制器方法并将代码添加到学生索引视图。

### <a name="add-sorting-functionality-to-the-index-method"></a>添加排序功能向 Index 方法

在*StudentsController.cs*，替换`Index`方法替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

此代码接收`sortOrder`从 URL 中的查询字符串参数。 ASP.NET 核心 MVC 提供查询字符串值作为参数传递给的操作方法。 参数将为"Name"日期"，可以选择后跟下划线和字符串"desc"指定降序顺序的字符串。 默认排序顺序为升序。

第一次请求索引页时，没有任何查询字符串。 学生按升序显示按姓氏排序，而是默认值，因为回退通过用例中建立`switch`语句。 当用户单击列标题超链接，相应`sortOrder`查询字符串中提供值。

这两个`ViewData`元素 （NameSortParm 和 DateSortParm） 供视图，用于将列标题超链接配置使用相应的查询字符串值。

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

这些是三元语句。 第一个指定，如果`sortOrder`参数为 null 或为空，NameSortParm 应设置为"name_desc"; 否则，它应设置为一个空字符串。 这两个语句启用要设置列标题的超链接，如下所示的视图：

|  当前的排序顺序  | 最后一个名称超链接 | 日期超链接 |
|:--------------------:|:-------------------:|:--------------:|
| 上次名称升序排列  | descending          | ascending      |
| 上次名称降序 | ascending           | ascending      |
| 升序的日期       | ascending           | descending     |
| 日期降序      | ascending           | ascending      |

该方法使用 LINQ to Entities 指定要作为排序依据的列。 该代码创建`IQueryable`switch 语句中之前, 的变量对其进行修改在 switch 语句，并调用`ToListAsync`方法之后`switch`语句。 当你创建和修改`IQueryable`变量，没有查询发送到数据库。 您将转换之前，不执行查询`IQueryable`到通过调用方法，如集合对象`ToListAsync`。 因此，此代码将导致直到不执行单个查询`return View`语句。

此代码可以获得详细具有大量列。 [本系列最后一个教程](advanced.md#dynamic-linq)演示如何编写代码，你可以将名称传递给`OrderBy`字符串变量中的列。

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a>将列标题的超链接添加到学生索引视图

中的代码替换*Views/Students/Index.cshtml*，替换为以下代码以添加列标题的超链接。 突出显示已更改的行。

[!code-html[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

此代码使用中的信息`ViewData`属性设置超链接与相应的查询字符串值。

运行应用程序中，选择**学生**卡，然后单击**姓氏**和**注册日期**列标题，以验证该排序工作原理。

![名称顺序中的学生索引页](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box-to-the-students-index-page"></a>将一个搜索框添加到学生索引页

若要添加到学生索引页过滤条件，将向视图添加一个文本框和提交按钮，并将中的相应更改`Index`方法。 文本框中，你将输入要在名字和姓氏字段中搜索的字符串。

### <a name="add-filtering-functionality-to-the-index-method"></a>将筛选功能添加到索引方法

在*StudentsController.cs*，替换`Index`方法替换为以下代码 （突出显示所做的更改）。

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

你已添加`searchString`参数`Index`方法。 从将添加到索引视图的文本框中接收到搜索字符串值。 你已还添加到 LINQ 语句 where 子句选择仅学生的名字或姓氏包含搜索字符串。 添加 where 语句子句执行只有在要搜索的值。

> [!NOTE]
> 此处调用`Where`方法`IQueryable`对象，并且筛选器将在服务器上处理。 在某些情况下你可能会调用`Where`作为扩展方法对内存中集合的方法。 (例如，假设你更改对引用`_context.Students`，因此的 EF`DbSet`它引用返回的存储库方法`IEnumerable`集合。)结果通常将相同，但在某些情况下可能会不同。
>
>例如，.NET Framework 实现的`Contains`方法执行区分大小写的比较，默认情况下，但 SQL Server 中这由 SQL Server 实例的排序规则设置。 该设置默认为不区分大小写。 您可以调用`ToUpper`来进行测试显式不区分大小写的方法：*其中 (s = > s.LastName.ToUpper()。Contains(searchString.ToUpper())*。 这将确保如果更改代码更高版本才能使用它返回的存储库，结果保持相同`IEnumerable`而不是集合`IQueryable`对象。 (当你调用`Contains`方法`IEnumerable`集合，你将获取.NET Framework 实现; 当上调用它`IQueryable`对象，则会出现的数据库提供程序实现。)但是，没有为此解决方案对性能产生负面影响。 `ToUpper`代码将放入 TSQL SELECT 语句的 WHERE 子句的函数。 将阻止优化器使用索引。 假设 SQL 主要安装为不区分大小写，最好是避免`ToUpper`代码之前你将迁移到区分大小写的数据存储区。

### <a name="add-a-search-box-to-the-student-index-view"></a>将一个搜索框添加到学生索引视图

在*Views/Student/Index.cshtml*，打开以便创建标题，文本框中，表标签之前立即添加突出显示的代码和**搜索**按钮。

[!code-html[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

此代码使用`<form>`[标记帮助器](xref:mvc/views/tag-helpers/intro)添加搜索文本框和按钮。 默认情况下，`<form>`标记帮助器提交 post，这意味着，参数作为进行传递 HTTP 消息正文中，不能在 URL 查询字符串的窗体数据。 指定 HTTP GET 时，窗体数据是在 URL 中作为查询字符串传递，这使得用户能够创建 URL 的书签。 操作未导致更新时，将收到 W3C 准则，则建议你应使用。

运行应用程序中，选择**学生**选项卡上，输入搜索字符串，然后单击搜索以验证筛选是否正常工作。

![使用筛选的学生索引页](sort-filter-page/_static/filtering.png)

请注意该 URL 包含搜索字符串。

```html
http://localhost:5813/Students?SearchString=an
```

如果此页加入书签，你将获得的筛选的列表，当你使用书签。 添加`method="get"`到`form`标记是什么导致要生成的查询字符串。

在此阶段，如果您单击的列标题排序链接你将丢失中输入的筛选器值**搜索**框。 你将在下一部分中修复此问题。

## <a name="add-paging-functionality-to-the-students-index-page"></a>将分页功能添加到学生索引页

将分页添加到学生索引页，你将创建`PaginatedList`类，该类使用`Skip`和`Take`语句而不是始终检索表的所有行的服务器上的数据进行筛选。 然后你将使中的其他更改`Index`方法和分页将按钮添加到`Index`视图。 下图显示了分页按钮。

![学生索引页，带有分页链接](sort-filter-page/_static/paging.png)

在项目文件夹中，创建`PaginatedList.cs`，然后将模板代码替换下面的代码。

[!code-csharp[Main](intro/samples/cu/PaginatedList.cs)]

`CreateAsync`在此代码中的方法采用页大小和页码，并应用相应`Skip`和`Take`向语句`IQueryable`。 当`ToListAsync`上调用`IQueryable`，它将返回包含请求的页的列表。 属性`HasPreviousPage`和`HasNextPage`可用来启用或禁用**上一步**和**下一步**分页按钮。

A`CreateAsync`方法用于而不是一个构造函数创建`PaginatedList<T>`对象，因为构造函数不能运行异步代码。

## <a name="add-paging-functionality-to-the-index-method"></a>将分页功能添加到索引方法

在*StudentsController.cs*，替换`Index`方法替换为以下代码。

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

此代码将页数字参数、 当前的排序顺序参数和当前的筛选器参数添加到方法签名中。

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? page)
```

第一次显示页面，或如果用户未单击分页或排序链接，则所有参数将都为 null。  单击分页链接时，如果页变量将包含要显示的页码。

`ViewData`名为 CurrentSort 元素提供了与当前的排序顺序中，视图，因为这必须包含在分页链接以保持排序顺序分页时相同。

`ViewData`元素名为当前筛选器提供了与当前的筛选器字符串的视图。 若要分页，过程维护的筛选器设置情况下，此值必须包括在分页链接，必须还原到文本框中时重新显示页。

如果分页期间更改搜索字符串，页将显示被重置为 1，因为新的筛选器可能会导致不同的数据，以显示。 在文本框中输入了值，按下提交按钮，则会更改搜索字符串。 在这种情况下，`searchString`参数不为 null。

```csharp
if (searchString != null)
{
    page = 1;
}
else
{
    searchString = currentFilter;
}
```

在结束`Index`方法，`PaginatedList.CreateAsync`方法将学生查询转换为支持分页的集合类型中的学生的单页。 学生的单个页面然后传递给视图。

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), page ?? 1, pageSize));
```

`PaginatedList.CreateAsync`方法采用页号。 两个问号表示 null 合并运算符。 Null 合并运算符定义可以为 null 的类型; 默认值表达式`(page ?? 1)`意味着返回的值`page`如果它具有一个值，或返回 1，如果`page`为 null。

## <a name="add-paging-links-to-the-student-index-view"></a>将分页链接添加到学生索引视图

在*Views/Students/Index.cshtml*，替换为以下代码替换现有代码。 突出显示所做的更改。

[!code-html[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

`@model`页顶部的语句指定视图现在获取`PaginatedList<T>`对象而不是`List<T>`对象。

列标题链接使用查询字符串将当前的搜索字符串传递到控制器，以便用户可以在筛选器的结果中进行排序：

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

分页按钮显示通过标记帮助程序：

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-page="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

运行应用并转到学生页。

![学生索引页，带有分页链接](sort-filter-page/_static/paging.png)

单击以确保分页工作原理的不同的排序顺序中的分页链接。 然后输入搜索字符串，然后重试以验证分页还适用正确使用排序和筛选的分页。

## <a name="create-an-about-page-that-shows-student-statistics"></a>创建一个显示学生统计信息的关于页面

为 Contoso 大学网站**有关**页上，将显示如何很多学生已注册的每个注册日期。 这要求在组上的分组和简单计算。 要完成此操作，将执行以下操作：

* 创建一个视图模型类，你需要将传递到该视图的数据。

* 修改对 Home 控制器中的关于方法。

* 修改关于视图。

### <a name="create-the-view-model"></a>创建视图模型

创建*SchoolViewModels*文件夹中的*模型*文件夹。

在新的文件夹中，将类文件添加*EnrollmentDateGroup.cs*和模板代码替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a>修改主控制器

在*HomeController.cs*，添加以下 using 语句在该文件的顶部：

[!code-csharp[Main](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

在类中，左大括号后立即添加的数据库上下文的类变量，并从 ASP.NET 核心 DI 获取上下文的实例：

[!code-csharp[Main](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

将 `About` 方法替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

LINQ 语句学生实体分组按注册日期，计算每个组中的实体数并将结果存储在集合中的`EnrollmentDateGroup`查看模型对象。
> [!NOTE] 
> 在实体框架核心 1.0 版本中，整个结果集返回到客户端，并在客户端上进行分组。 在某些情况下，这会导致性能问题。 请务必用生产增长的数据，测试性能，如有必要使用原始 SQL 执行对服务器进行分组。 有关如何使用原始的 SQL 的信息，请参阅[本系列最后一个教程](advanced.md)。

### <a name="modify-the-about-view"></a>修改有关视图

中的代码替换*Views/Home/About.cshtml*文件替换为以下代码：

[!code-html[](intro/samples/cu/Views/Home/About.cshtml)]

运行应用并转到关于页面。 每个注册日期的学生计数显示在表格中。

![有关页面](sort-filter-page/_static/about.png)

## <a name="summary"></a>摘要

在本教程中，你已了解如何执行排序、 筛选、 分页和分组。 在下一步的教程中，你将了解如何通过使用迁移来处理数据模型更改。

>[!div class="step-by-step"]
[上一页](crud.md)
[下一页](migrations.md)  
