---
title: "ASP.NET 核心 MVC 与 EF 核心-读取相关的数据-10 6"
author: tdykstra
description: "在本教程中将读取并显示相关的数据-即，实体框架将加载到导航属性的数据。"
keywords: "ASP.NET 核心，实体框架核心，相关数据，联接"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 71fec30f-8ea7-4ca8-96e3-d2e26c5be44e
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: 37613d974fdf1766b187cdd05efc926ecc6a351b
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="reading-related-data---ef-core-with-aspnet-core-mvc-tutorial-6-of-10"></a>读取与相关的数据的 EF 内核，它们有 ASP.NET 核心 MVC 教程 (6 的 10)

通过[Tom Dykstra](https://github.com/tdykstra)和[Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso 大学示例 web 应用程序演示如何创建使用实体框架核心和 Visual Studio 的 ASP.NET 核心 MVC web 应用程序。 有关教程系列的信息，请参阅[序列中的第一个教程](intro.md)。

在前面的教程，你将完成 School 数据模型。 在本教程中，将读取并显示相关的数据-即，实体框架将加载到导航属性的数据。

下图显示了您将使用的页。

![课程索引页](read-related-data/_static/courses-index.png)

![教师索引页](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a>Eager、 显式，和延迟加载的相关数据

有以下几种对象关系映射 (ORM) 软件，就如实体框架可以将相关的数据加载到实体的导航属性：

* 预先加载。 实体中读取时，会随之检索相关的数据。 这通常会导致检索所有具有所需的数据的单一联接查询。 你通过使用实体框架核心中指定预先加载`Include`和`ThenInclude`方法。

  ![预先加载示例](read-related-data/_static/eager-loading.png)

  你可以检索一些单独的查询中的数据并 EF"修复"的导航属性。  也就是说，EF 会自动将它们所属的单独检索到的实体添加以前检索到实体的导航属性。 对于查询以检索相关的数据，你可以使用`Load`方法而不是返回的列表或对象，如方法`ToList`或`Single`。

  ![单独的查询示例](read-related-data/_static/separate-queries.png)

* 显式加载。 当第一次读取实体时，不检索相关的数据。 你将编写检索相关的数据的代码需要它。 如下所示预先加载使用单独的查询的情况下，显式加载在多个查询的结果发送到数据库。 不同之处在于，使用显式加载，该代码指定要加载的导航属性。 你可以使用实体框架核心 1.1`Load`方法执行显式加载。 例如: 

  ![显式加载示例](read-related-data/_static/explicit-loading.png)

* 延迟加载。 当第一次读取实体时，不检索相关的数据。 但是，第一次尝试访问的导航属性，将自动检索该导航属性所需的数据。 每次尝试首次从一个导航属性获取数据时，查询是发送到数据库。 实体框架核心 1.0 不支持延迟加载。

### <a name="performance-considerations"></a>性能注意事项

如果你知道需要相关的数据检索的每个实体，预先加载通常提供最佳性能，因为发送到数据库的单个查询通常比用于检索每个实体的单独查询效率更高。 例如，假设每个部门都有十个相关的课程。 预先加载了相关的所有数据将导致仅单 （联接） 查询和单个往返到数据库。 对于每个部门的课程单独的查询中，将导致到数据库的十一个往返过程。 与数据库额外的往返延迟较高时尤其是对性能造成不利影响。

另一方面，在某些情况下的单独查询会更加高效。 预先加载了一个查询中的所有相关数据可能会导致非常复杂的联接为其生成 SQL Server 无法有效地处理它。 或者，如果需要访问仅针对要处理的实体集的子集的实体的导航属性，当单独的查询因为预先加载了所有内容预先将检索比你需要更多的数据，可能更好地执行。 如果性能非常重要，则最好测试以便做出最佳选择这两种方式的性能。

## <a name="create-a-courses-page-that-displays-department-name"></a>创建显示部门名称的课程页

课程实体包括一个包含过程分配给部门的部门实体的导航属性。 若要显示分配的部门的名称的课程，列表中，你需要获取的 Name 属性中的部门实体从`Course.Department`导航属性。

创建名为课程的实体类型，使用的相同选项 CoursesController 的控制器**使用实体框架的包含视图的 MVC 控制器**你此前学生控制器中所示的基架下图：

![添加课程控制器](read-related-data/_static/add-courses-controller.png)

打开*CoursesController.cs*并检查`Index`方法。 自动的基架已指定为预先加载`Department`导航属性使用`Include`方法。

替换`Index`方法替换为以下代码使用更合适的名称为`IQueryable`返回课程实体 (`courses`而不是`schoolContext`):

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

打开*Views/Courses/Index.cshtml*和模板代码替换为以下代码。 突出显示所做的更改：

[!code-html[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

你已对基架代码进行以下更改：

* 从索引的标题更改为课程。

* 添加**数**显示的列`CourseID`属性值。 默认情况下，主键不基架，因为它们通常是向最终用户无意义。 但是，在这种情况下的主键有意义，以及你想要对其进行说明。

* 更改**部门**列以显示部门名称。 该代码将显示`Name`加载到的部门实体属性`Department`导航属性：

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

运行应用并选择**课程**选项卡以查看使用部门名称的列表。

![课程索引页](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a>创建显示 Courses，并将注册一个教师页面

在此部分中，可以创建一个控制器和视图 Instructor 实体为了显示教师页：

![教师索引页](read-related-data/_static/instructors-index.png)

此页读取，并通过以下方式显示相关的数据：

* 教师的列表显示来自 OfficeAssignment 实体的相关的数据。 教师和 OfficeAssignment 实体都将位于对零或一一个关系。 你将为 OfficeAssignment 实体使用预先加载。 如前面所述，预先加载是通常更高效，您需要为所有检索行的主表的相关的数据。 在这种情况下，你想要显示的所有显示教师 office 分配。

* 当用户选择一个教师时，将显示相关的课程实体。 教师和课程实体都将位于多对多关系。 你将使用预先加载的课程实体和其相关的部门实体。 在这种情况下，因为你只为所选教师需要课程，单独的查询可能会更有效。 但是，此示例演示如何使用预先加载用于本身是导航属性中的实体内的导航属性。

* 当用户选择某一课程时，显示来自注册实体集的相关的数据。 当然，注册实体都将位于一个对多关系。 对于注册实体和其相关的学生实体，你将使用单独的查询。

### <a name="create-a-view-model-for-the-instructor-index-view"></a>创建教师索引视图的视图模型

教师页显示三个不同的表中的数据。 因此，你将创建包括三个属性，每个保存的表中的一个数据视图模型。

在*SchoolViewModels*文件夹中，创建*InstructorIndexData.cs*和替换为以下代码替换现有代码：

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a>创建教师控制器和视图

创建一个教师控制器具有 EF 读/写操作，如下面的插图中所示：

![添加教师控制器](read-related-data/_static/add-instructors-controller.png)

打开*InstructorsController.cs*和添加 using 语句 Viewmodel 命名空间：

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

Index 方法替换为以下代码以执行预先加载了相关的数据并将其置于视图模型。

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

该方法将接受可选路由数据 (`id`) 和查询字符串参数 (`courseID`) 提供的所选的教师和所选的过程的 ID 值。 参数提供的**选择**网页超链接。

代码首先通过在创建视图模型的实例并在其中放置教师的列表。 该代码指定为预先加载`Instructor.OfficeAssignment`和`Instructor.CourseAssignments`导航属性。 在`CourseAssignments`属性，`Course`属性是在其中已加载，和，则`Enrollments`和`Department`属性是在每个已加载，和`Enrollment`实体`Student`加载属性。

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

由于视图始终要求 OfficeAssignment 实体，它会更加高效，若要在同一查询中提取的。 在 web 页中，选择一个教师，仅当与某一课程比选择而无需更多时候显示页面，因此单个查询多个查询更好时，过程实体是必需的。

代码重复`CourseAssignments`和`Course`因为你需要从两个属性`Course`。 第一个字符串`ThenInclude`调用获取`CourseAssignment.Course`， `Course.Enrollments`，和`Enrollment.Student`。

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

此时，在代码中，另一个`ThenInclude`将用于导航属性的`Student`，不需要的。 但调用`Include`通过开头`Instructor`属性，因此你必须再次执行链，此时间指定`Course.Department`而不是`Course.Enrollments`。

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

在选择了一个教师时，将执行下面的代码。 所选的教师检索从教师视图模型中的列表中。 视图模型`Courses`属性然后加载和课程实体从该教师`CourseAssignments`导航属性。

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

`Where`方法将返回一个集合，但条件在此情况下传递到方法导致仅单个 Instructor 实体返回。 `Single`方法将集合转换为单个 Instructor 实体，使你可以访问该实体的`CourseAssignments`属性。 `CourseAssignments`属性包含`CourseAssignment`从中您想仅相关的实体`Course`实体。

你使用`Single`时你知道该集合的集合上的方法将有只有一项。 如果传递给它的集合为空或没有多个项，单个方法将引发异常。 替代方法是`SingleOrDefault`，它返回默认值 (在此情况下 null) 如果该集合为空。 但是，在这种情况下，仍会生成异常 (从尝试查找`Courses`上的 null 引用的属性)，和异常消息将不太清楚地指示问题的原因。 当调用`Single`方法，你还可以传递在 Where 条件而不是调用`Where`方法单独：

```csharp
.Single(i => i.ID == id.Value)
```

而不是：

```csharp
.Where(I => i.ID == id.Value).Single()
```

接下来，如果选择某一课程，从列表中的课程，视图模型中检索所选的课程。 然后视图模型的`Enrollments`属性加载和注册实体从该过程`Enrollments`导航属性。

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a>修改教师索引视图

在*Views/Instructors/Index.cshtml*，模板代码替换为以下代码。 突出显示所做的更改。

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-64&highlight=1,3-7,15-19,24,26-31,41-54,56)]

你已对现有代码做出以下更改：

* 更改到模型类`InstructorIndexData`。

* 更改页标题从**索引**到**教师**。

* 添加**Office**显示的列`item.OfficeAssignment.Location`才`item.OfficeAssignment`不为 null。 （由于这是一个对零或一一个关系，因此可能不具备相关的 OfficeAssignment 实体。）

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* 添加**课程**通过每个教师讲授显示课程的列。 请参阅[显式行转换与`@:`](xref:mvc/views/razor#explicit-line-transition-with-label)有关此 razor 语法的详细信息。

* 添加动态添加的代码`class="success"`到`tr`的所选教师的元素。 此设置使用 Bootstrap 类所选行的背景色。

  ```html
  string selectedRow = "";
  if (item.ID == (int?)ViewData["InstructorID"])
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* 添加新的超链接标记为**选择**立即之前每个行中的其他链接，这将导致所选的教师 ID 发送到`Index`方法。

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

运行应用并选择**教师**选项卡。没有相关的 OfficeAssignment 实体时，该页将显示相关的 OfficeAssignment 实体以及的空表单元格的 Location 属性。

![未选择任何项教师索引页](read-related-data/_static/instructors-index-no-selection.png)

在*Views/Instructors/Index.cshtml*文件之后结束表元素 （在文件末尾），, 添加以下代码。 此代码将显示一个教师选中与一个教师相关的课程的列表。

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

此代码读取`Courses`要显示的课程列表的视图模型的属性。 它还提供了**选择**超链接，以便将发送到所选课程 ID`Index`操作方法。

刷新页面并选择一个教师。 此时你会看到一个网格，其中显示分配给所选教师的课程，对于每个课程中，你将看到分配部门的名称。

![选择教师索引页教师](read-related-data/_static/instructors-index-instructor-selected.png)

之后你刚添加的代码块，添加下面的代码。 当选中该过程时，此值显示课程中注册的学生的列表。

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

此代码读取为了显示学生课程中注册的列表视图模型的注册属性。

再次刷新页面并选择一个教师。 然后选择要查看的已注册的学生和其年级列表的课程。

![教师索引页教师和所选课程](read-related-data/_static/instructors-index.png)

## <a name="explicit-loading"></a>显式加载

检索的讲师列表时*InstructorsController.cs*，指定为预先加载`CourseAssignments`导航属性。

假设你预期用户只是偶尔想要查看所选的教师和课程中的注册。 在这种情况下，你可能想要加载的注册数据才会在被请求。 若要查看有关如何执行显式加载操作的示例，请替换`Index`方法替换为以下代码，这将移除注册为预先加载并显式加载该属性。 突出显示代码更改。

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

新代码将删除*ThenInclude*方法的代码中检索 instructor 实体调用注册数据。 如果选择教师和课程，突出显示的代码检索所选的课程，注册实体和每个注册的学生实体。

运行应用程序，请转到教师索引页现在和你将看到任何区别中的页上，显示的内容，不过你已更改如何检索的数据。

## <a name="summary"></a>摘要

你现在使用预先加载与一个查询和多个查询相关的数据读入导航属性。 在下一教程你将学习如何更新相关的数据。

>[!div class="step-by-step"]
>[上一页](complex-data-model.md)
>[下一页](update-related-data.md)  
