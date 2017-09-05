---
title: "ASP.NET 核心 MVC 与 EF 核心-更新与相关的数据-7 个 10"
author: tdykstra
description: "在本教程中你将更新的更新外键字段和导航属性的相关的数据。"
keywords: "ASP.NET 核心，实体框架核心，相关数据，联接"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 67bd162b-bfb7-4750-9e7f-705228b5288c
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/update-related-data
ms.openlocfilehash: 85686fe4ebf95f95dc672fbc2d23cddd5bee85e5
ms.sourcegitcommit: 605dc99d241b6d955432bcd42c0178e6e6a212fd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2017
---
# <a name="updating-related-data---ef-core-with-aspnet-core-mvc-tutorial-7-of-10"></a>更新相关的数据的 EF 内核，它们有 ASP.NET 核心 MVC 教程 (7 个 10)

通过[Tom Dykstra](https://github.com/tdykstra)和[Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso 大学示例 web 应用程序演示如何创建使用实体框架核心和 Visual Studio 的 ASP.NET 核心 MVC web 应用程序。 有关教程系列的信息，请参阅[序列中的第一个教程](intro.md)。

在前面的教程中显示相关的数据;在本教程中你将更新的更新外键字段和导航属性的相关的数据。

下图显示一些您将使用的页。

![课程编辑页面](update-related-data/_static/course-edit.png)

![教师编辑页](update-related-data/_static/instructor-edit-courses.png)

## <a name="customize-the-create-and-edit-pages-for-courses"></a>课程中自定义创建和编辑页

创建新的课程实体时，它必须具有与现有部门的关系。 为了便于执行这种情况，基架的代码包括控制器方法以及创建和编辑视图中包括用于选择部门的下拉列表。 下拉列表设置`Course.DepartmentID`外键属性，而这正是实体框架需要以便加载`Department`与相应的部门实体的导航属性。 你将使用基架的代码，但将其更改略有为添加错误处理和下拉列表进行排序。

在*CoursesController.cs*、 删除的四种方式创建和编辑方法，并用它们替换下面的代码：

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

后`Edit`HttpPost 方法，创建一个新方法来加载下拉列表的部门信息。

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

`PopulateDepartmentsDropDownList`方法获取按名称排序的所有部门的列表，创建`SelectList`集合有关的下拉列表，并将集合传递到该视图`ViewBag`。 该方法接受可选`selectedDepartment`允许指定呈现下拉列表时将选定的项的调用代码的参数。 该视图会将"DepartmentID"的名称传递到`<select>`标记帮助器和帮助程序就会知道要查找的`ViewBag`对象`SelectList`名为"DepartmentID"。

HttpGet`Create`方法调用`PopulateDepartmentsDropDownList`而无需设置选定的项，因为新课程部门不尚未建立的方法：

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

HttpGet`Edit`方法设置选定的项，基于部门已分配给过程中正在编辑的 ID:

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

两个 HttpPost 方法`Create`和`Edit`还包括当它们在错误之后重新显示页时设置选定的项的代码。 这可确保当重新显示页以显示错误消息，选择任何部门保持所选。

### <a name="add-asnotracking-to-details-and-delete-methods"></a>添加。AsNoTracking 详细信息和删除方法

若要优化性能的过程详细信息和删除页，添加`AsNoTracking`调用`Details`和 HttpGet`Delete`方法。

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a>修改视图的课程视图

在*Views/Courses/Create.cshtml*，添加到"选择部门"选项**部门**下拉列表中，更改从标题**DepartmentID**到**部门**，并添加一条验证消息。

[!code-html[Main](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

在*Views/Courses/Edit.cshtml*，使您刚中的部门字段相同的更改*Create.cshtml*。

另外，请在*Views/Courses/Edit.cshtml*，添加课程数字字段之前**标题**字段。 因为课程号码是为主键，它会显示，但不能更改。

[!code-html[Main](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

已存在一个隐藏的字段 (`<input type="hidden">`) 编辑视图中的过程编号。 添加`<label>`标记帮助器不会消除隐藏字段的需要，因为它不会导致课程编号，以包括在已发布数据中，当用户单击**保存**上**编辑**页。

在*Views/Courses/Delete.cshtml*、 在顶部添加课程数字字段和部门 ID 更改为部门名称。

[!code-html[Main](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

在*Views/Course/Details.cshtml*，进行相同的更改您刚为*Delete.cshtml*。

### <a name="test-the-course-pages"></a>测试过程页面

运行**创建**页 (显示课程索引页，然后单击**新建**) 和新的课程中输入数据：

![课程创建页](update-related-data/_static/course-create.png)

单击 **“创建”**。 课程索引页会显示新添加到列表中的过程。 索引页列表中的部门名称来自于导航属性，显示已正确建立关系。

运行**编辑**页 (单击**编辑**课程索引页在过程上)。

![课程编辑页面](update-related-data/_static/course-edit.png)

更改页面上的数据，然后单击**保存**。 课程索引页会显示已更新的课程数据。

## <a name="add-an-edit-page-for-instructors"></a>添加讲师编辑页

编辑教师记录时，你想要能够更新教师的办公室分配。 Instructor 实体具有 OfficeAssignment 实体，这意味着你的代码必须处理以下情况下对零或一一个关系：

* 如果用户清除 office 分配，并且最初具有一个值，则删除 OfficeAssignment 实体。

* 如果用户输入 office 分配值，它最初为空，则创建一个新的 OfficeAssignment 实体。

* 如果用户更改一个办公室分配的值，更改现有 OfficeAssignment 实体中的值。

### <a name="update-the-instructors-controller"></a>更新教师控制器

在*InstructorsController.cs*，更改 HttpGet 中的代码`Edit`方法，以便它加载 Instructor 实体`OfficeAssignment`导航属性并调用`AsNoTracking`:

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=9,10&name=snippet_EditGetOA)]

替换 HttpPost`Edit`方法替换为以下代码来处理 office 分配更新：

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

该代码执行以下任务：

-  方法名称更改为`EditPost`因为签名现在为 HttpGet 相同`Edit`方法 (`ActionName`属性指定`/Edit/`URL 仍使用)。

-  获取从数据库使用的当前 Instructor 实体预先加载的`OfficeAssignment`导航属性。 这是与你在 HttpGet 未相同`Edit`方法。

-  将检索到的 Instructor 实体更新模型联编程序中的值。 `TryUpdateModel`重载使你到白名单你想要包括的属性。 这可以防止过度发布中所述[第二个教程](crud.md)。

    <!-- Snippets do not play well with <ul> [!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```
    
-   如果 office 位置为空，设置 Instructor.OfficeAssignment 属性为 null，以便将删除 OfficeAssignment 表中的相关的行。

    <!-- Snippets do not play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

- 将所做的更改保存到数据库。

### <a name="update-the-instructor-edit-view"></a>更新教师编辑视图

在*Views/Instructors/Edit.cshtml*，添加新字段以进行编辑的办公地点，在结束日期早于**保存**按钮：

[!code-html[Main](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

运行页面 (选择**教师**选项卡，然后单击**编辑**教师上)。 更改**办公地点**单击**保存**。

![教师编辑页](update-related-data/_static/instructor-edit-office.png)

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>将课程作业添加到教师编辑页

教师可能讲解任意数量的课程。 现在你将通过添加更改过程分配使用一组复选框，如下面的屏幕快照中所示的功能增强教师编辑页：

![课程教师编辑页](update-related-data/_static/instructor-edit-courses.png)

当然，Instructor 实体之间的关系是多对多。 若要添加和删除关系，你添加和删除实体到和从 CourseAssignments 联接实体集。

使你能够更改哪些课程 UI 教师是分配给是一组的复选框。 显示数据库中每个课程复选框，并选择 instructor 当前分配给那些。 用户可以选择或清除复选框来更改过程分配。 如果课程数大得多，你将可能想要使用不同的方法来在视图中，显示数据的但操作联接实体的相同方法将用于创建或删除关系。

### <a name="update-the-instructors-controller"></a>更新教师控制器

若要提供的复选框列表视图的数据，你将使用视图模型类。

创建*AssignedCourseData.cs*中*SchoolViewModels*文件夹和替换现有代码替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

在*InstructorsController.cs*，替换 HttpGet`Edit`方法替换为以下代码。 突出显示所做的更改。

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

该代码将添加为预先加载`Courses`导航属性并调用新`PopulateAssignedCourseData`方法以提供有关复选框的数组使用信息`AssignedCourseData`查看模型类。

中的代码`PopulateAssignedCourseData`方法读取通过所有课程实体才能加载课程使用视图模型类的列表。 对于每个课程中，代码检查过程中是否存在于教师`Courses`导航属性。 若要检查是否将某一课程分配给教师时，请创建高效的查找，分配给教师的课程已放入`HashSet`集合。 `Assigned`属性设置为 true 的课程教师分配给。 该视图将使用此属性来确定哪些复选框必须显示为选择。 最后，该列表传递给中的视图`ViewData`。

接下来，添加在用户单击时执行的代码与**保存**。 替换`EditPost`方法替换为以下代码，并添加一个新方法来更新`Courses`Instructor 实体导航属性。

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

方法签名现在是不同于 HttpGet`Edit`方法，以便从更改方法名称`EditPost`回`Edit`。

由于该视图不包含课程实体的集合，不能自动更新模型联编程序`CourseAssignments`导航属性。 而不是使用模型联编程序更新`CourseAssignments`导航属性，则执行该操作在新`UpdateInstructorCourses`方法。 因此你需要以排除`CourseAssignments`模型绑定中的属性。 这不需要对调用代码的任何更改`TryUpdateModel`因为您正在使用的白名单重载和`CourseAssignments`不在包括列表中。

如果没有复选框已选中中的代码`UpdateInstructorCourses`初始化`CourseAssignments`具有空集合，并返回导航属性：

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

该代码，然后循环访问数据库中的所有课程，并检查对当前分配给与在视图中选择的教师的每个课程。 为了便于高效的查找，后者的两个集合都存储在`HashSet`对象。

如果选择某一课程复选框，但过程不在`Instructor.CourseAssignments`导航属性，过程添加到集合中的导航属性。

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

如果未选择某一课程复选框，但过程处于`Instructor.CourseAssignments`导航属性，过程删除从导航属性。

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a>更新教师视图

在*Views/Instructors/Edit.cshtml*，添加**课程**字段与非数组的复选框，通过添加以下代码后立即`div`元素**Office**字段和之前`div`元素**保存**按钮。

<a id="notepad"></a>
> [!NOTE] 
> 当你将代码粘贴到 Visual Studio 时，分行符将更改在将中断代码的方式。  按 Ctrl + Z 一次撤消的自动格式设置。  这将修复分行符，使它们看起来像所示。 缩进不一定是完美的但`@</tr><tr>`， `@:<td>`， `@:</td>`，和`@:</tr>`行都必须在单独的行所示，或你将获取运行时错误。 与所选的新代码块，按 tab 键三次到了新代码与现有代码的行。

[!code-html[Main](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

此代码将创建一个 HTML 表，包含三列。 处于每个列组成课程编号及标题的标题后跟一个复选框。 所有对应的复选框具有相同的名称 ("selectedCourses")，通知它们将被视为一组模型联编程序。 每个复选框的值属性设置为的值`CourseID`。 当发页面时，模型联编程序会将数组传递给组成的控制器`CourseID`仅复选框进行选择的值。

当最初呈现复选框时，为分配给教师的课程的那些具有检查属性，后者选择它们 （它们检查的显示）。

运行教师索引页，并单击**编辑**上以查看一个教师**编辑**页。

![课程教师编辑页](update-related-data/_static/instructor-edit-courses.png)

更改某些课程作业并单击保存。 所做的更改会反映在索引页面。

> [!NOTE] 
> 用此处编辑教师课程数据的方法适用于没有有限的数量的课程。 对于则大得多的集合，会需要不同的 UI 以及不同的更新方法。

## <a name="update-the-delete-page"></a>更新删除页

在*InstructorsController.cs*，删除`DeleteConfirmed`方法并插入以下代码在其位置。

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

此代码进行以下更改：

* 执行预先加载的`CourseAssignments`导航属性。  您必须包含此或 EF 不会知道有关相关`CourseAssignment`实体并不会将其删除。  若要避免来阅读它们此处你可以配置级联删除数据库中。

* 如果要删除教师被指定为任何部门的管理员，移除这些部门教师分配。

## <a name="add-office-location-and-courses-to-the-create-page"></a>将 office 位置和课程添加到创建页

在*InstructorsController.cs*，删除的 HttpGet 和 HttpPost`Create`方法，然后在它们的位置添加以下代码：

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

此代码将类似于你所看到的针对`Edit`方法除外，它最初没有课程处于选中状态。 HttpGet`Create`方法调用`PopulateAssignedCourseData`方法不是因为可能存在但在选择的课程，以便提供为空集合`foreach`（否则查看代码将引发空引用异常） 的视图中的循环。

HttpPost`Create`方法将添加到每个所选的课程`CourseAssignments`检查验证错误和向数据库添加新教师前的导航属性。 即使有模型错误以便时模型错误 （例如，用户键控无效的日期），并且页面将重新显示并显示错误消息，将自动还原所做的任何课程选择添加课程。

请注意，若要添加到的课程`CourseAssignments`导航属性，你必须初始化为空集合属性：

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

作为执行此操作在控制器代码中的替代方法，你无法以执行此操作教师模型通过更改属性 getter 以自动创建集合如果不存在，如下面的示例中所示：

```csharp
private ICollection<CourseAssignment> _courseAssignments;
public ICollection<CourseAssignment> CourseAssignments
{
    get
    {
        return _courseAssignments ?? (_courseAssignments = new List<CourseAssignment>());
    }
    set
    {
        _courseAssignments = value;
    }
}
```

如果你修改`CourseAssignments`属性这种方式，可以删除在控制器中的显式属性初始化代码。

在*Views/Instructor/Create.cshtml*、 office 位置中添加文本框和复选框为提交按钮之前的课程。 在编辑页中，如[修复如果 Visual Studio 时将其粘贴代码重新格式化的格式设置](#notepad)。

[!code-html[Main](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

通过运行的测试**创建**页并添加一个教师。 

## <a name="handling-transactions"></a>处理事务

中所述[CRUD 教程](crud.md)，实体框架隐式实现事务。 有关方案，你需要更多的控制-例如，如果你想要包括在实体框架外部事务-中执行的操作，请参阅[事务](https://docs.microsoft.com/ef/core/saving/transactions)。

## <a name="summary"></a>摘要

你现在已经完成对使用相关的数据引入。 在下一步的教程中，你将看到如何处理并发冲突。

>[!div class="step-by-step"]
[上一页](read-related-data.md)
[下一页](concurrency.md)  
