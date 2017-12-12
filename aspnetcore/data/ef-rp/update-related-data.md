---
title: "与 EF 核心-razor 页更新相关的数据-7 个 8"
author: rick-anderson
description: "在本教程中你将更新的更新外键字段和导航属性的相关的数据。"
keywords: "ASP.NET 核心，实体框架核心，相关数据，联接"
ms.author: riande
manager: wpickett
ms.date: 11/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-rp/update-related-data
ms.openlocfilehash: f07a33c19ba1be623fae14228f8fbc909d766816
ms.sourcegitcommit: 6e46abd65973dea796d364a514de9ec2e3e1c1ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2017
---
# <a name="updating-related-data---ef-core-razor-pages-7-of-8"></a>更新相关的数据的 EF 核心 Razor 页 (7 个 8)

通过[Tom Dykstra](https://github.com/tdykstra)，和[Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[about the series](../../includes/RP-EF/intro.md)]

本教程演示了更新相关的数据。 如果你遇到无法解决的问题，请下载[对此阶段已完成应用程序](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part7)。

下图显示了一些已完成的页。

![课程编辑页面](update-related-data/_static/course-edit.png)
![教师编辑页](update-related-data/_static/instructor-edit-courses.png)

检查并测试创建和编辑课程页面。 创建新的课程。 由其主键 （整数），而不是其名称选择部门。 编辑新的过程。 完成测试后，删除新的过程。

## <a name="create-a-base-class-to-share-common-code"></a>创建一个基本的类，以共享通用代码

课程/创建和课程/编辑页每个需要部门名称的列表。 创建*Pages/Courses/DepartmentNamePageModel.cshtml.cs*基类的创建和编辑页：

[!code-csharp[Main](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

前面的代码创建[此时](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0)用于包含部门名称的列表。 如果`selectedDepartment`指定，在中选择该部门`SelectList`。

创建和编辑页模型类都派生自`DepartmentNamePageModel`。

## <a name="customize-the-courses-pages"></a>自定义课程页

创建新的课程实体时，它必须具有与现有部门的关系。 若要创建某一课程时添加一个部门，创建和编辑的基类包含下拉列表选择部门。 下拉列表设置`Course.DepartmentID`外键 (FK) 属性。 EF 核心使用`Course.DepartmentID`FK 加载`Department`导航属性。

![创建过程](update-related-data/_static/ddl.png)

替换为以下代码更新创建页模型：

[!code-csharp[Main](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-)]

前面的代码：

* 派生自 `DepartmentNamePageModel`。
* 使用`TryUpdateModelAsync`以防止[过多发布](xref:data/ef-rp/crud#overposting)。
* 替换`ViewData["DepartmentID"]`与`DepartmentNameSL`（从基类）。

`ViewData["DepartmentID"]`替换为使用强类型化`DepartmentNameSL`。 强类型化的模型被优先于弱化类型使用。 有关详细信息，请参阅[弱类型化数据 （ViewData 和 ViewBag）](xref:mvc/views/overview#VD_VB)。

### <a name="update-the-courses-create-page"></a>更新课程创建页

更新*Pages/Courses/Create.cshtml*替换为以下标记：

[!code-cshtml[Main](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

前面的标记进行以下更改：

* 更改从标题**DepartmentID**到**部门**。
* 替换`"ViewBag.DepartmentID"`与`DepartmentNameSL`（从基类）。
* 将添加"选择部门"选项。 此更改而不是第一个部门呈现"选择部门"。
* 如果未选择部门，请添加一条验证消息。

使用 Razor 页[选择标记帮助器](xref:mvc/views/working-with-forms#the-select-tag-helper):

[!code-cshtml[Main](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

创建页进行测试。 创建页显示部门名称，而不是部门 id。

### <a name="update-the-courses-edit-page"></a>更新课程编辑页。

更新编辑页模型替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-)]

在类似于那些在创建页模型中所做更改。 在前面的代码中，`PopulateDepartmentsDropDownList`部门 ID，其中选择下拉列表中指定的部门的传递。

更新*Pages/Courses/Edit.cshtml*替换为以下标记：

[!code-cshtml[Main](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

前面的标记进行以下更改：

* 显示过程 id。 通常在实体的主密钥 (PK) 不会显示。 Pk 将向用户通常毫无意义。 在这种情况下，在 PK 是过程数。
* 更改从标题**DepartmentID**到**部门**。
* 替换`"ViewBag.DepartmentID"`与`DepartmentNameSL`（从基类）。
* 将添加"选择部门"选项。 此更改而不是第一个部门呈现"选择部门"。
* 如果未选择部门，请添加一条验证消息。

页包含隐藏的字段 (`<input type="hidden">`) 过程数。 添加`<label>`标记帮助器与`asp-for="Course.CourseID"`不消除隐藏字段的需要。 `<input type="hidden">`需要的课程编号，以包括在已发布数据中，当用户单击**保存**。

测试更新的代码。 创建、 编辑和删除过程。

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a>将 AsNoTracking 添加到详细信息和删除页模型

[AsNoTracking](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__)跟踪不需要时可以提高性能。 添加`AsNoTracking`到删除和详细信息页模型。 下面的代码演示更新的删除页模型：

[!code-csharp[Main](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

更新`OnGetAsync`中的方法*Pages/Courses/Details.cshtml.cs*文件：

[!code-csharp[Main](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a>修改删除和详细信息页

使用以下标记更新删除 Razor 页：

[!code-cshtml[Main](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

到详细信息页中进行相同的更改。

### <a name="test-the-course-pages"></a>测试过程页面

测试创建、 编辑，详细信息，和删除。

## <a name="update-the-instructor-pages"></a>更新教师页

下列各节更新教师页。

### <a name="add-office-location"></a>添加 office 位置

在编辑教师记录时，你可能想要更新教师的办公室分配。 `Instructor`实体具有对零或一一个关系`OfficeAssignment`实体。 教师代码必须处理：

* 如果用户清除 office 分配，删除`OfficeAssignment`实体。
* 如果用户输入一个办公室分配，且为空，创建一个新`OfficeAssignment`实体。
* 如果用户更改 office 分配，更新`OfficeAssignment`实体。

替换为以下代码更新教师编辑页模型：

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-)]

前面的代码：

- 获取当前`Instructor`中使用的预先加载的数据库实体`OfficeAssignment`导航属性。
- 更新检索`Instructor`模型联编程序中的值的实体。 `TryUpdateModel`阻止[过多发布](xref:data/ef-rp/crud#overposting)。
- 如果 office 位置为空，设置`Instructor.OfficeAssignment`为 null。 当`Instructor.OfficeAssignment`是 null 中的相关的行`OfficeAssignment`删除表。

### <a name="update-the-instructor-edit-page"></a>更新教师编辑页

更新*Pages/Instructors/Edit.cshtml*与办公地点：

[!code-cshtml[Main](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

验证可以更改教师办公地点。

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>将课程作业添加到教师编辑页

教师可能讲解任意数量的课程。 在本部分中，你将添加能够更改课程作业。 下图显示更新的教师编辑页：

![课程教师编辑页](update-related-data/_static/instructor-edit-courses.png)

`Course`和`Instructor`具有多对多关系。 若要添加和删除关系，你可以添加和删除实体从`CourseAssignments`加入实体集。

复选框启用对一个教师分配给的课程的更改。 在数据库中每个课程显示一个复选框。 检查教师分配给的课程。 用户可以选择或清除复选框来更改过程分配。 如果课程数大得多：

* 你可能需要使用不同的用户界面以显示课程。
* 操作创建或删除关系的联接实体的方法不会更改。

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a>添加类以支持创建和编辑教师页面

创建*SchoolViewModels/AssignedCourseData.cs*替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

`AssignedCourseData`类包含数据以创建一个教师通过分配课程对应的复选框。

创建*Pages/Instructors/InstructorCoursesPageModel.cshtml.cs*基类：

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

`InstructorCoursesPageModel`是将用于编辑和创建页模型的基类。 `PopulateAssignedCourseData`读取所有`Course`实体来填充`AssignedCourseDataList`。 对于每个课程中，该代码将设置`CourseID`，标题，并决定是否教师分配给过程。 A [HashSet](https://docs.microsoft.com/dotnet/api/system.collections.generic.hashset-1)用于创建高效的查找。

### <a name="instructors-edit-page-model"></a>教师编辑页模型

替换为以下代码更新教师编辑页模型：

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-)]

前面的代码处理 office 分配更改。

更新教师 Razor 视图：

[!code-cshtml[Main](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> 当你将代码粘贴到 Visual Studio 时，分行符都转换将中断代码的方式。 按 Ctrl + Z 一次撤消的自动格式设置。 Ctrl + Z，以便它们看起来像你在此处看到修复分行符。 缩进不一定是完美的但`@</tr><tr>`， `@:<td>`， `@:</td>`，和`@:</tr>`行都必须在单独的行所示。 与所选的新代码块，按 tab 键三次到了新代码与现有代码的行。 对投票或查看此 bug 的状态[与此链接](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html)。

前面的代码创建一个 HTML 表，包含三列。 每列均具有一个复选框和包含课程编号及标题的标题。 所有对应的复选框具有相同的名称 ("selectedCourses")。 使用相同的名称会通知模型联编程序来将它们视为一个组。 每个复选框的值属性设置为`CourseID`。 模型联编程序当发页面时，将传递一个数组，其中包含`CourseID`仅复选框选择的值。

当最初呈现复选框时，分配给教师课程签入属性。

运行应用和测试更新的教师编辑页。 更改某些过程分配。 所做的更改会反映在索引页面。

注意： 采取此处编辑教师课程数据的做法适用于没有有限的数量的课程。 对于要大得多的集合，不同的 UI 和其他更新方法为更可用且更高效。

### <a name="update-the-instructors-create-page"></a>更新教师创建页

替换为以下代码更新教师创建页模型：

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

前面的代码将类似于*Pages/Instructors/Edit.cshtml.cs*代码。

使用以下标记更新教师创建 Razor 页：

[!code-cshtml[Main](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

教师创建页进行测试。

## <a name="update-the-delete-page"></a>更新删除页

替换为以下代码更新删除页模型：

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-)]

前面的代码进行以下更改：

* 使用预先加载的`CourseAssignments`导航属性。 `CourseAssignments`必须包含或删除教师时不删除它们。 若要避免无需阅读这些条款，配置数据库中的级联删除。

* 如果要删除教师被指定为任何部门的管理员，移除这些部门教师分配。

>[!div class="step-by-step"]
[上一页](xref:data/ef-rp/read-related-data)
[下一页](xref:data/ef-rp/concurrency)