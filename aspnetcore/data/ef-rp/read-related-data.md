---
title: "与 EF 核心-razor 页读取相关的数据-8 6"
author: rick-anderson
description: "在本教程中你已阅读并显示相关的数据-即，实体框架将加载到导航属性的数据。"
keywords: "ASP.NET 核心，实体框架核心，相关数据，联接"
ms.author: riande
manager: wpickett
ms.date: 11/05/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-rp/read-related-data
ms.openlocfilehash: ba9b17ecdcb605d39117d03230b1db37e8e4d0dd
ms.sourcegitcommit: 05e798c9bac7b9e9983599afb227ef393905d023
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2017
---
# <a name="reading-related-data---ef-core-with-razor-pages-6-of-8"></a>读取与相关的数据的 EF 内核，它们有 Razor 页 (6 的 8)

通过[Tom Dykstra](https://github.com/tdykstra)， [Jon P Smith](https://twitter.com/thereformedprog)，和[Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[about the series](../../includes/RP-EF/intro.md)]

在本教程中，是读取和显示相关的数据。 相关的数据是 EF 核心将加载到导航属性的数据。

如果你遇到无法解决的问题，请下载[对此阶段已完成应用程序](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part6-related)。

下图显示了本教程已完成的页：

![课程索引页](read-related-data/_static/courses-index.png)

![教师索引页](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a>Eager、 显式，和延迟加载的相关数据

有多种 EF 核心可以加载到实体的导航属性的相关的数据：

* [预先加载](https://docs.microsoft.com/ef/core/querying/related-data#eager-loading)。 预先加载是实体的一种类型的查询还加载相关的实体时。 实体中读取时，会检索其相关的数据。 这通常会导致检索所有具有所需的数据的单一联接查询。 EF 核心将颁发某些类型的预先加载多个的查询。 发出多个查询可以是效率更高，比 ef6 更高版本中的某些查询这种情况其中没有单个查询。 使用指定预先加载`Include`和`ThenInclude`方法。

 ![预先加载示例](read-related-data/_static/eager-loading.png)
 
 包含集合 nvavigation 时，预先加载将发送多个查询：

 * 一个查询主查询 
 * 每个集合"边缘"负载树中的的一个查询。

* 单独的查询与`Load`： 可以在单独的查询中检索数据并 EF 核心"修复"的导航属性。 "向上修复"意味着 EF 核心自动填充的导航属性。 单独的查询与`Load`很像 explict 加载比预先加载。

 ![单独的查询示例](read-related-data/_static/separate-queries.png)

 注意： EF 核心与以前已加载到上下文实例的任何其他实体的导航属性将自动修复。 即使一个导航属性的数据已*不*显式包含，可能仍填充属性，如果某些或所有相关实体先前已加载。

* [显式加载](https://docs.microsoft.com/ef/core/querying/related-data#explicit-loading)。 当第一次读取实体时，不检索相关的数据。 必须编写代码，当需要时检索相关的数据。 显式加载与单独的查询会导致发送到的数据库的多个查询。 显式加载，该代码指定要加载的导航属性。 使用`Load`方法执行显式加载。 例如: 

 ![显式加载示例](read-related-data/_static/explicit-loading.png)

* [延迟加载](https://docs.microsoft.com/ef/core/querying/related-data#lazy-loading)。 [EF 核心当前不支持延迟加载](https://github.com/aspnet/EntityFrameworkCore/issues/3797)。 当第一次读取实体时，不检索相关的数据。 第一次访问导航属性时，将自动检索该导航属性所需的数据。 查询发送到每个时间导航属性访问第一次数据库。

* `Select`运算符加载仅所需的相关的数据。

## <a name="create-a-courses-page-that-displays-department-name"></a>创建显示部门名称的课程页

课程实体包括一个导航属性，包含`Department`实体。 `Department`实体包含过程分配给的部门。

若要在课程的列表中显示分配的部门的名称：

* 获取`Name`属性从`Department`实体。
* `Department`实体来自于`Course.Department`导航属性。

![课程。部门](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>
### <a name="scaffold-the-course-model"></a>基架过程模型

* 退出 Visual Studio。
* 打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。
* 运行下面的命令：

 ```console
dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
 ```

前面的命令基架`Course`模型。 在 Visual Studio 中打开项目。

生成项目。 生成将生成如下所示的错误：

`1>Pages/Courses/Index.cshtml.cs(26,37,26,43): error CS1061: 'SchoolContext' does not
 contain a definition for 'Course' and no extension method 'Course' accepting a first
 argument of type 'SchoolContext' could be found (are you missing a using directive or
 an assembly reference?)`

 全局更改`_context.Course`到`_context.Courses`(即，将"s"添加到`Course`)。 7 匹配项的发现和更新。

打开*Pages/Courses/Index.cshtml.cs*并检查`OnGetAsync`方法。 基架引擎指定为预先加载`Department`导航属性。 `Include`方法指定预先加载。

运行应用并选择**课程**链接。 部门列显示`DepartmentID`，这是毫无意义。

使用以下代码更新 `OnGetAsync` 方法：

[!code-csharp[Main](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

前面的代码将添加`AsNoTracking`。 `AsNoTracking`因为返回的实体不会跟踪，可以提高性能。 因为它们不会更新在当前上下文中，不会跟踪实体。

更新*Views/Courses/Index.cshtml*替换为以下突出显示的标记：

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

基架的代码进行了以下更改：

* 从索引的标题更改为课程。
* 添加**数**显示的列`CourseID`属性值。 默认情况下，主键不基架，因为它们通常是向最终用户无意义。 但是，在这种情况下的主键是有意义。
* 更改**部门**列以显示部门名称。 该代码将显示`Name`属性`Department`实体加载到`Department`导航属性：

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

运行应用并选择**课程**选项卡以查看使用部门名称的列表。

![课程索引页](read-related-data/_static/courses-index.png)

<a name="select"></a>
### <a name="loading-related-data-with-select"></a>正在加载相关与选择的数据

`OnGetAsync`方法将加载相关的数据与`Include`方法：

[!code-csharp[Main](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

`Select`运算符加载仅所需的相关的数据。 为单个项目，如`Department.Name`它使用 SQL INNER JOIN。 对于集合，它使用与另一个数据库访问，但也会更激烈。`Include` 集合的运算符。

下面的代码加载与相关的数据`Select`方法：

[!code-csharp[Main](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

`CourseViewModel`:

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

请参阅[IndexSelect.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml)和[IndexSelect.cshtml.cs](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs)有关完整示例。

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a>创建显示 Courses，并将注册一个教师页面

在此部分中，创建教师页面。

<a name="IP"></a>
![教师索引页](read-related-data/_static/instructors-index.png)

此页读取，并通过以下方式显示相关的数据：

* 教师的列表显示来自的相关的数据`OfficeAssignment`实体 (上图中的 Office)。 `Instructor`和`OfficeAssignment`实体位于对零或一一个关系。 用于预先加载`OfficeAssignment`实体。 预先加载需要显示相关的数据时通常效率更高。 在这种情况下，将显示讲师办公室分配。
* 当用户选择教师 (上图中的 Harui) 相关`Course`显示实体。 `Instructor`和`Course`实体位于多对多关系。 预先加载的`Course`实体和其相关`Department`使用实体。 在这种情况下，因为需要仅为所选教师的课程，单独的查询可能会更有效。 此示例演示如何使用预先加载用于导航属性中的实体中的导航属性。
* 当用户选择课程 （上图中的化学） 与相关的数据从`Enrollments`显示实体。 在前面的图中，显示学生名称，并将评分。 `Course`和`Enrollment`实体位于一个对多关系。

### <a name="create-a-view-model-for-the-instructor-index-view"></a>创建教师索引视图的视图模型

教师页显示三个不同的表中的数据。 创建视图模型，包括表示三个表的三个实体。

在*SchoolViewModels*文件夹中，创建*InstructorIndexData.cs*替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a>基架教师模型

* 退出 Visual Studio。
* 打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。
* 运行下面的命令：

 ```console
dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
 ```

前面的命令基架`Instructor`模型。 在 Visual Studio 中打开项目。

生成项目。 生成生成错误。

全局更改`_context.Instructor`到`_context.Instructors`(即，将"s"添加到`Instructor`)。 7 匹配项的发现和更新。

运行应用并导航到教师页。

替换*Pages/Instructors/Index.cshtml.cs*替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,20-)]

`OnGetAsync`方法用于所选教师的 ID 将接受可选路线数据。

在检查查询*Pages/Instructors/Index.cshtml*页：

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

查询具有两个包括：

* `OfficeAssignment`： 显示在[教师视图](#IP)。
* `CourseAssignments`： 它可提供讲授的课程中。


### <a name="update-the-instructors-index-page"></a>更新教师索引页

更新*Pages/Instructors/Index.cshtml*替换为以下标记：

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

前面的标记进行以下更改：

* 更新`page`指令从`@page`到`@page "{id:int?}"`。 `"{id:int?}"`是一个路由模板。 路由模板更改为路由数据整数在 URL 中的查询字符串。 例如，单击**选择**教师当页面指令产生类似于下面的 URL 的链接：

    `http://localhost:1234/Instructors?id=2`

    Page 指令时`@page "{id:int?}"`，以前的 URL 是：

    `http://localhost:1234/Instructors/2`

* 页标题**教师**。
* 添加**Office**显示的列`item.OfficeAssignment.Location`才`item.OfficeAssignment`不为 null。 这是一个对零或一一个关系，因为不可能的相关的 OfficeAssignment 实体。

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* 添加**课程**通过每个教师讲授显示课程的列。 请参阅[显式行转换与`@:`](xref:mvc/views/razor#explicit-line-transition-with-)有关此 razor 语法的详细信息。

* 添加动态添加的代码`class="success"`到`tr`的所选教师的元素。 此设置使用 Bootstrap 类所选行的背景色。

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* 添加新的超链接标记为**选择**。 此链接将发送到所选的教师 ID`Index`方法，并设置背景色。

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

运行应用并选择**教师**选项卡。该页面将显示`Location`(office) 从相关`OfficeAssignment`实体。 如果 OfficeAssignment' 是 null，则将显示空表单元格。

![未选择任何项教师索引页](read-related-data/_static/instructors-index-no-selection.png)

单击**选择**链接。 行样式更改。

### <a name="add-courses-taught-by-selected-instructor"></a>添加由选教师讲授的课程

更新`OnGetAsync`中的方法*Pages/Instructors/Index.cshtml.cs*替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-)]

检查更新的查询：

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

前面的查询添加`Department`实体。

下面的代码执行时选择一个教师 (`id != null`)。 所选的教师检索从教师视图模型中的列表中。 视图模型`Courses`属性加载与`Course`从该 instructor 实体`CourseAssignments`导航属性。

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

`Where`方法返回的集合。 在前面`Where`方法，只有一个`Instructor`返回实体。 `Single`方法将集合转换为单个`Instructor`实体。 `Instructor`实体提供访问`CourseAssignments`属性。 `CourseAssignments`提供对相关访问`Course`实体。

![教师课程多对多](complex-data-model/_static/courseassignment.png)

`Single`在集合具有只有一个项目时，使用方法的集合。 `Single`方法引发异常，如果该集合为空或没有多个项。 替代方法是`SingleOrDefault`，它返回默认值 (在此情况下 null) 如果该集合为空。 使用`SingleOrDefault`对空集合：

* 将引发异常 (从尝试查找`Courses`上的 null 引用的属性)。
* 异常消息将不太清楚地指示问题的原因。

下面的代码填充视图模型`Enrollments`属性选择某一课程时：

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

将以下标记添加到末尾*Pages/Courses/Index.cshtml* Razor 页：

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-)]

前面的标记显示选中一个教师与一个教师相关的课程的列表。

测试应用程序。 单击**选择**教师页上的链接。

![选择教师索引页教师](read-related-data/_static/instructors-index-instructor-selected.png)

### <a name="show-student-data"></a>显示学生的数据

在此部分中，应用更新以显示所选过程的学生数据。

更新中的查询`OnGetAsync`中的方法*Pages/Instructors/Index.cshtml.cs*替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

更新*Pages/Instructors/Index.cshtml*。 将以下标记添加到文件末尾：

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

前面的标记显示所选课程中注册的学生的列表。

刷新页面并选择一个教师。 选择要查看的已注册的学生和其年级列表课程。

![教师索引页教师和所选课程](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a>使用单个

`Single`方法可以传入`Where`条件而不是调用`Where`方法单独：

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21,28-29)]

前面`Single`方法通过使用未提供任何好处`Where`。 一些开发人员更喜欢`Single`接近样式。

## <a name="explicit-loading"></a>显式加载

当前的代码指定为预先加载`Enrollments`和`Students`:

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

假设用户很少会想要查看在课程中的注册。 在这种情况下，是一种优化仅加载注册数据，如果它请求。 在本部分中，`OnGetAsync`更新为使用显式加载`Enrollments`和`Students`。

更新`OnGetAsync`替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

前面的代码将删除*ThenInclude*方法调用注册和学生数据。 如果选择了某一课程，突出显示的代码检索：

* `Enrollment`所选课程的实体。
* `Student`每个实体`Enrollment`。

请注意上述代码注释掉`.AsNoTracking()`。 只能为导航属性显式加载的跟踪的实体。

测试应用程序。 从用户角度看，应用程序的行为会与以前的版本相同。

下一教程演示如何更新相关的数据。

>[!div class="step-by-step"]
>[上一页](xref:data/ef-rp/complex-data-model)
>[下一页](xref:data/ef-rp/update-related-data)