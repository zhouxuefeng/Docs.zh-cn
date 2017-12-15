---
title: "与 EF 核心-CRUD-2 的 8 razor 页"
author: rick-anderson
description: "演示如何创建、 读取、 更新和删除与 EF 核心"
keywords: "ASP.NET 核心、 实体框架核心、 CRUD，创建、 读取、 更新、 删除"
ms.author: riande
manager: wpickett
ms.date: 10/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-rp/crud
ms.openlocfilehash: b4b24c155c29a0ef8ffffda752253f56097e50ed
ms.sourcegitcommit: 198fb0488e961048bfa376cf58cb853ef1d1cb91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2017
---
# <a name="create-read-update-and-delete---ef-core-with-razor-pages-2-of-8"></a>创建、 读取、 更新和删除的 EF 内核，它们有 Razor 页 (2 的 8)

通过[Tom Dykstra](https://github.com/tdykstra)， [Jon P Smith](https://twitter.com/thereformedprog)，和[Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[about the series](../../includes/RP-EF/intro.md)]

在本教程中，基架 CRUD （创建、 读取、 更新、 删除） 查看和自定义代码。

注意： 若要最小化复杂性并保留侧重于 EF 核心这些教程，请 EF 核心使用代码在 Razor 页代码隐藏文件中。 一些开发人员使用一种服务层或存储库模式中的创建 UI （Razor 页） 和数据访问层之间的抽象层。

在本教程、 创建、 编辑、 删除、 和中详细说明 Razor 页*学生*文件夹进行修改。

用于创建、 编辑和删除页面，该基架的代码使用以下模式：

* 获取并使用 HTTP GET 方法显示所请求的数据`OnGetAsync`。
* 使用 HTTP POST 方法将更改保存到数据`OnPostAsync`。

索引和详细信息页获取并使用 HTTP GET 方法显示所请求的数据`OnGetAsync`

## <a name="replace-singleordefaultasync-with-firstordefaultasync"></a>将替换为 FirstOrDefaultAsync SingleOrDefaultAsync

生成的代码使用[SingleOrDefaultAsync](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_)提取请求的实体。 [FirstOrDefaultAsync](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_)是，能够有效地提取一个实体：

* 除非代码需要进行验证，否则是不从查询返回的多个实体。 
* `SingleOrDefaultAsync`提取更多的数据，并执行不必要的工作。
* `SingleOrDefaultAsync`如果没有适合的筛选器部分的多个实体，则引发异常。
*  `FirstOrDefaultAsync`不会引发，如果没有适合的筛选器部分的多个实体。

全局替换`SingleOrDefaultAsync`与`FirstOrDefaultAsync`。 `SingleOrDefaultAsync`位置也使用 5:

* `OnGetAsync`中的详细信息页中。
* `OnGetAsync`和`OnPostAsync`在页中编辑和删除。

<a name="FindAsync"></a>
### <a name="findasync"></a>FindAsync

在很大程度的基架代码[FindAsync](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___)可代替了`FirstOrDefaultAsync`或`SingleOrDefaultAsync`。 

`FindAsync`：

* 使用主键 (PK) 来查找实体。 如果具有在 PK 的实体正在由上下文跟踪时，它将发出的请求返回到的数据库。
* 是简单和简洁。
* 经过优化，可查找单个实体。
* 可以在某些情况下，有性能的好处，但它们很少派上用场的正常 web 方案。
* 隐式使用[FirstAsync](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_)而不是[SingleAsync](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_)。
但如果你想要包括其他实体，然后查找不再适当。 这意味着，你可能需要以放弃查找并应用随着你转到查询。

## <a name="customize-the-details-page"></a>自定义的详细信息页

浏览到`Pages/Students`页。 **编辑**，**详细信息**，和**删除**链接都由[定位点标记帮助器](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)中*页/学生 /Index.cshtml*文件。

[!code-cshtml[Main](intro/samples/cu/Pages/Students/Index1.cshtml?range=40-44)]

选择的详细信息链接。 URL 的格式为`http://localhost:5000/Students/Details?id=2`。 使用查询字符串传递学生 ID (`?id=2`)。

更新用于编辑、 详细信息，并删除 Razor 页面`"{id:int}"`路由模板。 将上述每个页面的页面指令从 `@page` 更改为 `@page "{id:int}"`。

对"{id: int}"路由模板执行的页的请求**不**包括整数路由值返回 HTTP 404 （找不到） 错误。 例如，`http://localhost:5000/Students/Details`返回 404 错误。 若要使 ID 可选，请将 `?` 追加到路由约束：

 ```cshtml
@page "{id:int?}"
```

运行应用程序，单击详细信息链接，并验证 URL 将 ID 传递作为路由数据 (`http://localhost:5000/Students/Details/2`)。

不要全局更改`@page`到`@page "{id:int}"`、 执行会中断主页的链接和创建页面。

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a>添加相关的数据

学生索引页的基架的代码不包括`Enrollments`属性。 本节的内容中`Enrollments`集合显示在详细信息页。

`OnGetAsync`方法*Pages/Students/Details.cshtml.cs*使用`FirstOrDefaultAsync`方法来检索单个`Student`实体。 添加以下突出显示的代码：

[!code-csharp[Main](intro/samples/cu/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

`Include`和`ThenInclude`方法导致要加载的上下文`Student.Enrollments`导航属性，并在每个注册`Enrollment.Course`导航属性。 这些方法在读取相关数据教程中是 examinied 在详细信息。

`AsNoTracking`方法可以提高方案中的性能时不会更新在当前上下文中返回的实体。 `AsNoTracking`本教程中稍后将讨论。

### <a name="display-related-enrollments-on-the-details-page"></a>详细信息页上显示相关的注册

打开*Pages/Students/Details.cshtml*。 添加以下突出显示的代码，以显示注册的列表：

 <!--2do ricka. if doesn't change, remove dup -->
[!code-cshtml[Main](intro/samples/cu/Pages/Students/Details1.cshtml?highlight=32-53)]

如果后粘贴代码，代码缩进有误，，按 CTRL-K-D 更正它。

前面的代码循环访问中的实体`Enrollments`导航属性。 对于每个注册，它显示课程标题和评分。 正在从存储中的课程实体检索课程标题`Course`注册实体导航属性。

运行应用程序中，选择**学生**卡，然后单击**详细信息**一名学生的链接。 将显示 courses，并将所选学生的年级的列表。

## <a name="update-the-create-page"></a>更新创建页

更新`OnPostAsync`中的方法*Pages/Students/Create.cshtml.cs*替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>
### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

检查[TryUpdateModelAsync](https://docs.microsoft.com/ dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_)代码：

[!code-csharp[Main](intro/samples/cu/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

在前面的代码中，`TryUpdateModelAsync<Student>`尝试更新`emptyStudent`对象使用的已发布的窗体值[PageContext](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext)中的属性[PageModel](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel?view=aspnetcore-2.0)。 `TryUpdateModelAsync`仅更新列出的属性 (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`)。

在前面的示例：

* 第二个参数 (` "student", // Prefix`) 是前缀使用来查找值。 不区分大小写。
* 已发布的窗体值转换为中的类型`Student`模型使用[模型绑定](xref:mvc/models/model-binding#how-model-binding-works)。

<a id="overpost"></a>
### <a name="overposting"></a>过多发布

使用`TryUpdateModel`更新具有发送的值字段先是最佳安全方案，因为它阻止 overposting。 例如，假设学生实体包含`Secret`此 web 页面不应更新或添加的属性：

[!code-csharp[Main](intro/samples/cu/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

即使此应用程序没有`Secret`上创建/更新 Razor 页上，黑客字段可以设置`Secret`值过多发布。 黑客无法使用 Fiddler，之类的工具或编写一些 JavaScript，发布`Secret`形成的值。 原始代码不限制时它将创建学生实例模型联编程序使用的字段。

任何值为指定黑客`Secret`在数据库中更新窗体字段。 下图显示 Fiddler 工具添加`Secret`（具有值"OverPost"） 到已发布的窗体值的字段。

![Fiddler 添加机密字段](../ef-mvc/crud/_static/fiddler.png)

"OverPost"已成功添加到的值`Secret`所插入行的属性。 预期之外的应用程序设计器`Secret`属性与创建页设置。

<a name="vm"></a>
### <a name="view-model"></a>视图模型

视图模型通常包含应用程序使用的模型中包括的属性的子集。 应用程序模型通常称为域模型。 域模型通常包含在数据库中的对应实体所需的所有属性。 视图模型仅包含属性所需的 UI 层 （例如，创建页）。 除视图模型中，某些应用程序，用于绑定模型或输入的模型 Razor 页代码隐藏类和浏览器之间传递数据。 请考虑以下`Student`视图模型：

[!code-csharp[Main](intro/samples/cu/Models/StudentVM.cs)]

查看模型提供一种替代方式，以防止 overposting。 视图模型包含仅查看 （显示），或更新的属性。

下面的代码使用`StudentVM`视图模型来创建新的学生：

[!code-csharp[Main](intro/samples/cu/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

[SetValues](https://docs.microsoft.com/ dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_)方法通过从另一个读取值来设置此对象的值的[PropertyValues](https://docs.microsoft.com/ dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues)对象。 `SetValues`使用属性名称匹配。 视图模型类型不需要与模型类型相关，它只需要具有匹配的属性。

使用`StudentVM`需要[CreateVM.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Students/CreateVM.cshtml)将更新为使用`StudentVM`而非`Student`。

在 Razor 页中，`PageModel`派生的类是视图模型。 

## <a name="update-the-edit-page"></a>更新编辑页

更新编辑页代码隐藏文件：

[!code-csharp[Main](intro/samples/cu/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

代码更改是类似于创建页面，但有少数例外：

* `OnPostAsync`具有可选`id`参数。
* 当前的学生提取从 DB 中，而不是创建空的学生。
* `FirstOrDefaultAsync`已替换为[FindAsync](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync?view=efcore-2.0)。 `FindAsync`选择从为主键的实体时，是一个不错的选择。 请参阅[FindAsync](#FindAsync)有关详细信息。

### <a name="test-the-edit-and-create-pages"></a>测试编辑和创建页

创建和编辑几个学生实体。

## <a name="entity-states"></a>实体状态

在内存中的实体是否与数据库中其相应行同步，将跟踪的数据库上下文。 DB 上下文同步信息确定会发生什么情况时`SaveChanges`调用。 例如，新实体传递到`Add`方法，该实体的状态设置为方法`Added`。 当`SaveChanges`调用时，数据库上下文发出 SQL INSERT 命令。

实体可能处于以下状态之一：

* `Added`: 实体的 DB 中尚不存在。 `SaveChanges`方法发出 INSERT 语句。

* `Unchanged`： 不需要此实体一起保存任何更改。 从数据库中读取时，实体具有此状态。

* `Modified`： 某些或所有实体的属性值已都更改。 `SaveChanges`方法发出 UPDATE 语句。

* `Deleted`: 实体已标记为删除。 `SaveChanges`方法发出的 DELETE 语句。

* `Detached`: 实体不跟踪的数据库上下文。

在桌面应用中，通常会自动设置状态更改。 读取实体，进行更改，和要自动更改为的实体状态`Modified`。 调用`SaveChanges`生成 SQL UPDATE 语句，都会更新仅已更改的属性。

在 web 应用中，`DbContext`读取实体，并显示在呈现页面后释放数据。 当一页面`OnPostAsync`方法被调用时，新的 web 请求进行的新实例的与`DbContext`。 重新读取该新的上下文中的实体模拟桌面处理。

## <a name="update-the-delete-page"></a>更新删除页

在此部分中，不会添加代码以实现自定义错误消息时对调用`SaveChanges`失败。 添加要包含 possile 错误消息的字符串：

[!code-csharp[Main](intro/samples/cu/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

将 `OnGetAsync` 方法替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

前面的代码中包含的可选参数`saveChangesError`。 `saveChangesError`指示方法是否在无法删除学生对象之后调用。 删除操作由于暂时性网络问题可能会失败。 在云中很有可能暂时性网络错误。 `saveChangesError`为 false 时删除页`OnGetAsync`从 UI 调用。 当`OnGetAsync`由调用`OnPostAsync`（因为删除操作失败），则`saveChangesError`参数为 true。

### <a name="the-delete-pages-onpostasync-method"></a>Delete 页 OnPostAsync 方法

替换`OnPostAsync`替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

前面的代码检索所选的实体，然后调用`Remove`方法将实体的状态设置为`Deleted`。 当`SaveChanges`称为 SQL DELETE 命令生成的。 如果`Remove`失败：

* DB 异常捕获。
* 删除页`OnGetAsync`方法调用与`saveChangesError=true`。

### <a name="update-the-delete-razor-page"></a>更新删除 Razor 页

将以下突出显示的错误消息添加到删除 Razor 页。

[!code-cshtml[Main](intro/samples/cu/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

测试删除。

## <a name="common-errors"></a>常见错误

学生/主页或其他链接不起作用：

验证是否 Razor 页包含正确`@page`指令。 例如，学生/Razor 主页应**不**包含路由模板：

```cshtml
@page "{id:int}"
```

每个 Razor 页必须包括`@page`指令。

>[!div class="step-by-step"]
[上一页](xref:data/ef-rp/intro)
[下一页](xref:data/ef-rp/sort-filter-page)
