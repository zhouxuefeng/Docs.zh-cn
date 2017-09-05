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
# <a name="updating-related-data---ef-core-with-aspnet-core-mvc-tutorial-7-of-10"></a><span data-ttu-id="f5c36-104">更新相关的数据的 EF 内核，它们有 ASP.NET 核心 MVC 教程 (7 个 10)</span><span class="sxs-lookup"><span data-stu-id="f5c36-104">Updating related data - EF Core with ASP.NET Core MVC tutorial (7 of 10)</span></span>

<span data-ttu-id="f5c36-105">通过[Tom Dykstra](https://github.com/tdykstra)和[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f5c36-105">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f5c36-106">Contoso 大学示例 web 应用程序演示如何创建使用实体框架核心和 Visual Studio 的 ASP.NET 核心 MVC web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="f5c36-106">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="f5c36-107">有关教程系列的信息，请参阅[序列中的第一个教程](intro.md)。</span><span class="sxs-lookup"><span data-stu-id="f5c36-107">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="f5c36-108">在前面的教程中显示相关的数据;在本教程中你将更新的更新外键字段和导航属性的相关的数据。</span><span class="sxs-lookup"><span data-stu-id="f5c36-108">In the previous tutorial you displayed related data; in this tutorial you'll update related data by updating foreign key fields and navigation properties.</span></span>

<span data-ttu-id="f5c36-109">下图显示一些您将使用的页。</span><span class="sxs-lookup"><span data-stu-id="f5c36-109">The following illustrations show some of the pages that you'll work with.</span></span>

![课程编辑页面](update-related-data/_static/course-edit.png)

![教师编辑页](update-related-data/_static/instructor-edit-courses.png)

## <a name="customize-the-create-and-edit-pages-for-courses"></a><span data-ttu-id="f5c36-112">课程中自定义创建和编辑页</span><span class="sxs-lookup"><span data-stu-id="f5c36-112">Customize the Create and Edit Pages for Courses</span></span>

<span data-ttu-id="f5c36-113">创建新的课程实体时，它必须具有与现有部门的关系。</span><span class="sxs-lookup"><span data-stu-id="f5c36-113">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="f5c36-114">为了便于执行这种情况，基架的代码包括控制器方法以及创建和编辑视图中包括用于选择部门的下拉列表。</span><span class="sxs-lookup"><span data-stu-id="f5c36-114">To facilitate this, the scaffolded code includes controller methods and Create and Edit views that include a drop-down list for selecting the department.</span></span> <span data-ttu-id="f5c36-115">下拉列表设置`Course.DepartmentID`外键属性，而这正是实体框架需要以便加载`Department`与相应的部门实体的导航属性。</span><span class="sxs-lookup"><span data-stu-id="f5c36-115">The drop-down list sets the `Course.DepartmentID` foreign key property, and that's all the Entity Framework needs in order to load the `Department` navigation property with the appropriate Department entity.</span></span> <span data-ttu-id="f5c36-116">你将使用基架的代码，但将其更改略有为添加错误处理和下拉列表进行排序。</span><span class="sxs-lookup"><span data-stu-id="f5c36-116">You'll use the scaffolded code, but change it slightly to add error handling and sort the drop-down list.</span></span>

<span data-ttu-id="f5c36-117">在*CoursesController.cs*、 删除的四种方式创建和编辑方法，并用它们替换下面的代码：</span><span class="sxs-lookup"><span data-stu-id="f5c36-117">In *CoursesController.cs*, delete the four Create and Edit methods and replace them with the following code:</span></span>

<span data-ttu-id="f5c36-118">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-118">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]</span></span>

<span data-ttu-id="f5c36-119">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-119">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]</span></span>

<span data-ttu-id="f5c36-120">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-120">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]</span></span>

<span data-ttu-id="f5c36-121">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-121">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]</span></span>

<span data-ttu-id="f5c36-122">后`Edit`HttpPost 方法，创建一个新方法来加载下拉列表的部门信息。</span><span class="sxs-lookup"><span data-stu-id="f5c36-122">After the `Edit` HttpPost method, create a new method that loads department info for the drop-down list.</span></span>

<span data-ttu-id="f5c36-123">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-123">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]</span></span>

<span data-ttu-id="f5c36-124">`PopulateDepartmentsDropDownList`方法获取按名称排序的所有部门的列表，创建`SelectList`集合有关的下拉列表，并将集合传递到该视图`ViewBag`。</span><span class="sxs-lookup"><span data-stu-id="f5c36-124">The `PopulateDepartmentsDropDownList` method gets a list of all departments sorted by name, creates a `SelectList` collection for a drop-down list, and passes the collection to the view in `ViewBag`.</span></span> <span data-ttu-id="f5c36-125">该方法接受可选`selectedDepartment`允许指定呈现下拉列表时将选定的项的调用代码的参数。</span><span class="sxs-lookup"><span data-stu-id="f5c36-125">The method accepts the optional `selectedDepartment` parameter that allows the calling code to specify the item that will be selected when the drop-down list is rendered.</span></span> <span data-ttu-id="f5c36-126">该视图会将"DepartmentID"的名称传递到`<select>`标记帮助器和帮助程序就会知道要查找的`ViewBag`对象`SelectList`名为"DepartmentID"。</span><span class="sxs-lookup"><span data-stu-id="f5c36-126">The view will pass the name "DepartmentID" to the `<select>` tag helper, and the helper then knows to look in the `ViewBag` object for a `SelectList` named "DepartmentID".</span></span>

<span data-ttu-id="f5c36-127">HttpGet`Create`方法调用`PopulateDepartmentsDropDownList`而无需设置选定的项，因为新课程部门不尚未建立的方法：</span><span class="sxs-lookup"><span data-stu-id="f5c36-127">The HttpGet `Create` method calls the `PopulateDepartmentsDropDownList` method without setting the selected item, because for a new course the department is not established yet:</span></span>

<span data-ttu-id="f5c36-128">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-128">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]</span></span>

<span data-ttu-id="f5c36-129">HttpGet`Edit`方法设置选定的项，基于部门已分配给过程中正在编辑的 ID:</span><span class="sxs-lookup"><span data-stu-id="f5c36-129">The HttpGet `Edit` method sets the selected item, based on the ID of the department that is already assigned to the course being edited:</span></span>

<span data-ttu-id="f5c36-130">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-130">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]</span></span>

<span data-ttu-id="f5c36-131">两个 HttpPost 方法`Create`和`Edit`还包括当它们在错误之后重新显示页时设置选定的项的代码。</span><span class="sxs-lookup"><span data-stu-id="f5c36-131">The HttpPost methods for both `Create` and `Edit` also include code that sets the selected item when they redisplay the page after an error.</span></span> <span data-ttu-id="f5c36-132">这可确保当重新显示页以显示错误消息，选择任何部门保持所选。</span><span class="sxs-lookup"><span data-stu-id="f5c36-132">This ensures that when the page is redisplayed to show the error message, whatever department was selected stays selected.</span></span>

### <a name="add-asnotracking-to-details-and-delete-methods"></a><span data-ttu-id="f5c36-133">添加。AsNoTracking 详细信息和删除方法</span><span class="sxs-lookup"><span data-stu-id="f5c36-133">Add .AsNoTracking to Details and Delete methods</span></span>

<span data-ttu-id="f5c36-134">若要优化性能的过程详细信息和删除页，添加`AsNoTracking`调用`Details`和 HttpGet`Delete`方法。</span><span class="sxs-lookup"><span data-stu-id="f5c36-134">To optimize performance of the Course Details and Delete pages, add `AsNoTracking` calls in the `Details` and HttpGet `Delete` methods.</span></span>

<span data-ttu-id="f5c36-135">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-135">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]</span></span>

<span data-ttu-id="f5c36-136">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-136">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]</span></span>

### <a name="modify-the-course-views"></a><span data-ttu-id="f5c36-137">修改视图的课程视图</span><span class="sxs-lookup"><span data-stu-id="f5c36-137">Modify the Course views</span></span>

<span data-ttu-id="f5c36-138">在*Views/Courses/Create.cshtml*，添加到"选择部门"选项**部门**下拉列表中，更改从标题**DepartmentID**到**部门**，并添加一条验证消息。</span><span class="sxs-lookup"><span data-stu-id="f5c36-138">In *Views/Courses/Create.cshtml*, add a "Select Department" option to the **Department** drop-down list, change the caption from **DepartmentID** to **Department**, and add a validation message.</span></span>

<span data-ttu-id="f5c36-139">[!code-html[Main](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-139">[!code-html[Main](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]</span></span>

<span data-ttu-id="f5c36-140">在*Views/Courses/Edit.cshtml*，使您刚中的部门字段相同的更改*Create.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="f5c36-140">In *Views/Courses/Edit.cshtml*, make the same change for the Department field that you just did in *Create.cshtml*.</span></span>

<span data-ttu-id="f5c36-141">另外，请在*Views/Courses/Edit.cshtml*，添加课程数字字段之前**标题**字段。</span><span class="sxs-lookup"><span data-stu-id="f5c36-141">Also in *Views/Courses/Edit.cshtml*, add a course number field before the **Title** field.</span></span> <span data-ttu-id="f5c36-142">因为课程号码是为主键，它会显示，但不能更改。</span><span class="sxs-lookup"><span data-stu-id="f5c36-142">Because the course number is the primary key, it's displayed, but it can't be changed.</span></span>

<span data-ttu-id="f5c36-143">[!code-html[Main](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-143">[!code-html[Main](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]</span></span>

<span data-ttu-id="f5c36-144">已存在一个隐藏的字段 (`<input type="hidden">`) 编辑视图中的过程编号。</span><span class="sxs-lookup"><span data-stu-id="f5c36-144">There's already a hidden field (`<input type="hidden">`) for the course number in the Edit view.</span></span> <span data-ttu-id="f5c36-145">添加`<label>`标记帮助器不会消除隐藏字段的需要，因为它不会导致课程编号，以包括在已发布数据中，当用户单击**保存**上**编辑**页。</span><span class="sxs-lookup"><span data-stu-id="f5c36-145">Adding a `<label>` tag helper doesn't eliminate the need for the hidden field because it doesn't cause the course number to be included in the posted data when the user clicks **Save** on the **Edit** page.</span></span>

<span data-ttu-id="f5c36-146">在*Views/Courses/Delete.cshtml*、 在顶部添加课程数字字段和部门 ID 更改为部门名称。</span><span class="sxs-lookup"><span data-stu-id="f5c36-146">In *Views/Courses/Delete.cshtml*, add a course number field at the top and change department ID to department name.</span></span>

<span data-ttu-id="f5c36-147">[!code-html[Main](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-147">[!code-html[Main](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]</span></span>

<span data-ttu-id="f5c36-148">在*Views/Course/Details.cshtml*，进行相同的更改您刚为*Delete.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="f5c36-148">In *Views/Course/Details.cshtml*, make the same change that you just did for *Delete.cshtml*.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="f5c36-149">测试过程页面</span><span class="sxs-lookup"><span data-stu-id="f5c36-149">Test the Course pages</span></span>

<span data-ttu-id="f5c36-150">运行**创建**页 (显示课程索引页，然后单击**新建**) 和新的课程中输入数据：</span><span class="sxs-lookup"><span data-stu-id="f5c36-150">Run the **Create** page (display the Course Index page and click **Create New**) and enter data for a new course:</span></span>

![课程创建页](update-related-data/_static/course-create.png)

<span data-ttu-id="f5c36-152">单击 **“创建”**。</span><span class="sxs-lookup"><span data-stu-id="f5c36-152">Click **Create**.</span></span> <span data-ttu-id="f5c36-153">课程索引页会显示新添加到列表中的过程。</span><span class="sxs-lookup"><span data-stu-id="f5c36-153">The Courses Index page is displayed with the new course added to the list.</span></span> <span data-ttu-id="f5c36-154">索引页列表中的部门名称来自于导航属性，显示已正确建立关系。</span><span class="sxs-lookup"><span data-stu-id="f5c36-154">The department name in the Index page list comes from the navigation property, showing that the relationship was established correctly.</span></span>

<span data-ttu-id="f5c36-155">运行**编辑**页 (单击**编辑**课程索引页在过程上)。</span><span class="sxs-lookup"><span data-stu-id="f5c36-155">Run the **Edit** page (click **Edit** on a course in the Course Index page ).</span></span>

![课程编辑页面](update-related-data/_static/course-edit.png)

<span data-ttu-id="f5c36-157">更改页面上的数据，然后单击**保存**。</span><span class="sxs-lookup"><span data-stu-id="f5c36-157">Change data on the page and click **Save**.</span></span> <span data-ttu-id="f5c36-158">课程索引页会显示已更新的课程数据。</span><span class="sxs-lookup"><span data-stu-id="f5c36-158">The Courses Index page is displayed with the updated course data.</span></span>

## <a name="add-an-edit-page-for-instructors"></a><span data-ttu-id="f5c36-159">添加讲师编辑页</span><span class="sxs-lookup"><span data-stu-id="f5c36-159">Add an Edit Page for Instructors</span></span>

<span data-ttu-id="f5c36-160">编辑教师记录时，你想要能够更新教师的办公室分配。</span><span class="sxs-lookup"><span data-stu-id="f5c36-160">When you edit an instructor record, you want to be able to update the instructor's office assignment.</span></span> <span data-ttu-id="f5c36-161">Instructor 实体具有 OfficeAssignment 实体，这意味着你的代码必须处理以下情况下对零或一一个关系：</span><span class="sxs-lookup"><span data-stu-id="f5c36-161">The Instructor entity has a one-to-zero-or-one relationship with the OfficeAssignment entity, which means your code has to handle the following situations:</span></span>

* <span data-ttu-id="f5c36-162">如果用户清除 office 分配，并且最初具有一个值，则删除 OfficeAssignment 实体。</span><span class="sxs-lookup"><span data-stu-id="f5c36-162">If the user clears the office assignment and it originally had a value, delete the OfficeAssignment entity.</span></span>

* <span data-ttu-id="f5c36-163">如果用户输入 office 分配值，它最初为空，则创建一个新的 OfficeAssignment 实体。</span><span class="sxs-lookup"><span data-stu-id="f5c36-163">If the user enters an office assignment value and it originally was empty, create a new OfficeAssignment entity.</span></span>

* <span data-ttu-id="f5c36-164">如果用户更改一个办公室分配的值，更改现有 OfficeAssignment 实体中的值。</span><span class="sxs-lookup"><span data-stu-id="f5c36-164">If the user changes the value of an office assignment, change the value in an existing OfficeAssignment entity.</span></span>

### <a name="update-the-instructors-controller"></a><span data-ttu-id="f5c36-165">更新教师控制器</span><span class="sxs-lookup"><span data-stu-id="f5c36-165">Update the Instructors controller</span></span>

<span data-ttu-id="f5c36-166">在*InstructorsController.cs*，更改 HttpGet 中的代码`Edit`方法，以便它加载 Instructor 实体`OfficeAssignment`导航属性并调用`AsNoTracking`:</span><span class="sxs-lookup"><span data-stu-id="f5c36-166">In *InstructorsController.cs*, change the code in the HttpGet `Edit` method so that it loads the Instructor entity's `OfficeAssignment` navigation property and calls `AsNoTracking`:</span></span>

<span data-ttu-id="f5c36-167">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=9,10&name=snippet_EditGetOA)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-167">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=9,10&name=snippet_EditGetOA)]</span></span>

<span data-ttu-id="f5c36-168">替换 HttpPost`Edit`方法替换为以下代码来处理 office 分配更新：</span><span class="sxs-lookup"><span data-stu-id="f5c36-168">Replace the HttpPost `Edit` method with the following code to handle office assignment updates:</span></span>

<span data-ttu-id="f5c36-169">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-169">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]</span></span>

<span data-ttu-id="f5c36-170">该代码执行以下任务：</span><span class="sxs-lookup"><span data-stu-id="f5c36-170">The code does the following:</span></span>

-  <span data-ttu-id="f5c36-171">方法名称更改为`EditPost`因为签名现在为 HttpGet 相同`Edit`方法 (`ActionName`属性指定`/Edit/`URL 仍使用)。</span><span class="sxs-lookup"><span data-stu-id="f5c36-171">Changes the method name to `EditPost` because the signature is now the same as the HttpGet `Edit` method (the `ActionName` attribute specifies that the `/Edit/` URL is still used).</span></span>

-  <span data-ttu-id="f5c36-172">获取从数据库使用的当前 Instructor 实体预先加载的`OfficeAssignment`导航属性。</span><span class="sxs-lookup"><span data-stu-id="f5c36-172">Gets the current Instructor entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span> <span data-ttu-id="f5c36-173">这是与你在 HttpGet 未相同`Edit`方法。</span><span class="sxs-lookup"><span data-stu-id="f5c36-173">This is the same as what you did in the HttpGet `Edit` method.</span></span>

-  <span data-ttu-id="f5c36-174">将检索到的 Instructor 实体更新模型联编程序中的值。</span><span class="sxs-lookup"><span data-stu-id="f5c36-174">Updates the retrieved Instructor entity with values from the model binder.</span></span> <span data-ttu-id="f5c36-175">`TryUpdateModel`重载使你到白名单你想要包括的属性。</span><span class="sxs-lookup"><span data-stu-id="f5c36-175">The `TryUpdateModel` overload enables you to whitelist the properties you want to include.</span></span> <span data-ttu-id="f5c36-176">这可以防止过度发布中所述[第二个教程](crud.md)。</span><span class="sxs-lookup"><span data-stu-id="f5c36-176">This prevents over-posting, as explained in the [second tutorial](crud.md).</span></span>

    <!-- Snippets do not play well with <ul> [!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```
    
-   <span data-ttu-id="f5c36-177">如果 office 位置为空，设置 Instructor.OfficeAssignment 属性为 null，以便将删除 OfficeAssignment 表中的相关的行。</span><span class="sxs-lookup"><span data-stu-id="f5c36-177">If the office location is blank, sets the Instructor.OfficeAssignment property to null so that the related row in the OfficeAssignment table will be deleted.</span></span>

    <!-- Snippets do not play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

- <span data-ttu-id="f5c36-178">将所做的更改保存到数据库。</span><span class="sxs-lookup"><span data-stu-id="f5c36-178">Saves the changes to the database.</span></span>

### <a name="update-the-instructor-edit-view"></a><span data-ttu-id="f5c36-179">更新教师编辑视图</span><span class="sxs-lookup"><span data-stu-id="f5c36-179">Update the Instructor Edit view</span></span>

<span data-ttu-id="f5c36-180">在*Views/Instructors/Edit.cshtml*，添加新字段以进行编辑的办公地点，在结束日期早于**保存**按钮：</span><span class="sxs-lookup"><span data-stu-id="f5c36-180">In *Views/Instructors/Edit.cshtml*, add a new field for editing the office location, at the end before the **Save** button:</span></span>

<span data-ttu-id="f5c36-181">[!code-html[Main](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-181">[!code-html[Main](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]</span></span>

<span data-ttu-id="f5c36-182">运行页面 (选择**教师**选项卡，然后单击**编辑**教师上)。</span><span class="sxs-lookup"><span data-stu-id="f5c36-182">Run the page (select the **Instructors** tab and then click **Edit** on an instructor).</span></span> <span data-ttu-id="f5c36-183">更改**办公地点**单击**保存**。</span><span class="sxs-lookup"><span data-stu-id="f5c36-183">Change the **Office Location** and click **Save**.</span></span>

![教师编辑页](update-related-data/_static/instructor-edit-office.png)

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="f5c36-185">将课程作业添加到教师编辑页</span><span class="sxs-lookup"><span data-stu-id="f5c36-185">Add Course assignments to the Instructor Edit page</span></span>

<span data-ttu-id="f5c36-186">教师可能讲解任意数量的课程。</span><span class="sxs-lookup"><span data-stu-id="f5c36-186">Instructors may teach any number of courses.</span></span> <span data-ttu-id="f5c36-187">现在你将通过添加更改过程分配使用一组复选框，如下面的屏幕快照中所示的功能增强教师编辑页：</span><span class="sxs-lookup"><span data-stu-id="f5c36-187">Now you'll enhance the Instructor Edit page by adding the ability to change course assignments using a group of check boxes, as shown in the following screen shot:</span></span>

![课程教师编辑页](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="f5c36-189">当然，Instructor 实体之间的关系是多对多。</span><span class="sxs-lookup"><span data-stu-id="f5c36-189">The relationship between the Course and Instructor entities is many-to-many.</span></span> <span data-ttu-id="f5c36-190">若要添加和删除关系，你添加和删除实体到和从 CourseAssignments 联接实体集。</span><span class="sxs-lookup"><span data-stu-id="f5c36-190">To add and remove relationships, you add and remove entities to and from the CourseAssignments join entity set.</span></span>

<span data-ttu-id="f5c36-191">使你能够更改哪些课程 UI 教师是分配给是一组的复选框。</span><span class="sxs-lookup"><span data-stu-id="f5c36-191">The UI that enables you to change which courses an instructor is assigned to is a group of check boxes.</span></span> <span data-ttu-id="f5c36-192">显示数据库中每个课程复选框，并选择 instructor 当前分配给那些。</span><span class="sxs-lookup"><span data-stu-id="f5c36-192">A check box for every course in the database is displayed, and the ones that the instructor is currently assigned to are selected.</span></span> <span data-ttu-id="f5c36-193">用户可以选择或清除复选框来更改过程分配。</span><span class="sxs-lookup"><span data-stu-id="f5c36-193">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="f5c36-194">如果课程数大得多，你将可能想要使用不同的方法来在视图中，显示数据的但操作联接实体的相同方法将用于创建或删除关系。</span><span class="sxs-lookup"><span data-stu-id="f5c36-194">If the number of courses were much greater, you would probably want to use a different method of presenting the data in the view, but you'd use the same method of manipulating a join entity to create or delete relationships.</span></span>

### <a name="update-the-instructors-controller"></a><span data-ttu-id="f5c36-195">更新教师控制器</span><span class="sxs-lookup"><span data-stu-id="f5c36-195">Update the Instructors controller</span></span>

<span data-ttu-id="f5c36-196">若要提供的复选框列表视图的数据，你将使用视图模型类。</span><span class="sxs-lookup"><span data-stu-id="f5c36-196">To provide data to the view for the list of check boxes, you'll use a view model class.</span></span>

<span data-ttu-id="f5c36-197">创建*AssignedCourseData.cs*中*SchoolViewModels*文件夹和替换现有代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="f5c36-197">Create *AssignedCourseData.cs* in the *SchoolViewModels* folder and replace the existing code with the following code:</span></span>

<span data-ttu-id="f5c36-198">[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-198">[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]</span></span>

<span data-ttu-id="f5c36-199">在*InstructorsController.cs*，替换 HttpGet`Edit`方法替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="f5c36-199">In *InstructorsController.cs*, replace the HttpGet `Edit` method with the following code.</span></span> <span data-ttu-id="f5c36-200">突出显示所做的更改。</span><span class="sxs-lookup"><span data-stu-id="f5c36-200">The changes are highlighted.</span></span>

<span data-ttu-id="f5c36-201">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-201">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]</span></span>

<span data-ttu-id="f5c36-202">该代码将添加为预先加载`Courses`导航属性并调用新`PopulateAssignedCourseData`方法以提供有关复选框的数组使用信息`AssignedCourseData`查看模型类。</span><span class="sxs-lookup"><span data-stu-id="f5c36-202">The code adds eager loading for the `Courses` navigation property and calls the new `PopulateAssignedCourseData` method to provide information for the check box array using the `AssignedCourseData` view model class.</span></span>

<span data-ttu-id="f5c36-203">中的代码`PopulateAssignedCourseData`方法读取通过所有课程实体才能加载课程使用视图模型类的列表。</span><span class="sxs-lookup"><span data-stu-id="f5c36-203">The code in the `PopulateAssignedCourseData` method reads through all Course entities in order to load a list of courses using the view model class.</span></span> <span data-ttu-id="f5c36-204">对于每个课程中，代码检查过程中是否存在于教师`Courses`导航属性。</span><span class="sxs-lookup"><span data-stu-id="f5c36-204">For each course, the code checks whether the course exists in the instructor's `Courses` navigation property.</span></span> <span data-ttu-id="f5c36-205">若要检查是否将某一课程分配给教师时，请创建高效的查找，分配给教师的课程已放入`HashSet`集合。</span><span class="sxs-lookup"><span data-stu-id="f5c36-205">To create efficient lookup when checking whether a course is assigned to the instructor, the courses assigned to the instructor are put into a `HashSet` collection.</span></span> <span data-ttu-id="f5c36-206">`Assigned`属性设置为 true 的课程教师分配给。</span><span class="sxs-lookup"><span data-stu-id="f5c36-206">The `Assigned` property  is set to true for courses the instructor is assigned to.</span></span> <span data-ttu-id="f5c36-207">该视图将使用此属性来确定哪些复选框必须显示为选择。</span><span class="sxs-lookup"><span data-stu-id="f5c36-207">The view will use this property to determine which check boxes must be displayed as selected.</span></span> <span data-ttu-id="f5c36-208">最后，该列表传递给中的视图`ViewData`。</span><span class="sxs-lookup"><span data-stu-id="f5c36-208">Finally, the list is passed to the view in `ViewData`.</span></span>

<span data-ttu-id="f5c36-209">接下来，添加在用户单击时执行的代码与**保存**。</span><span class="sxs-lookup"><span data-stu-id="f5c36-209">Next, add the code that's executed when the user clicks **Save**.</span></span> <span data-ttu-id="f5c36-210">替换`EditPost`方法替换为以下代码，并添加一个新方法来更新`Courses`Instructor 实体导航属性。</span><span class="sxs-lookup"><span data-stu-id="f5c36-210">Replace the `EditPost` method with the following code, and add a new method that updates the `Courses` navigation property of the Instructor entity.</span></span>

<span data-ttu-id="f5c36-211">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-211">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]</span></span>

<span data-ttu-id="f5c36-212">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-212">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]</span></span>

<span data-ttu-id="f5c36-213">方法签名现在是不同于 HttpGet`Edit`方法，以便从更改方法名称`EditPost`回`Edit`。</span><span class="sxs-lookup"><span data-stu-id="f5c36-213">The method signature is now different from the HttpGet `Edit` method, so the method name changes from `EditPost` back to `Edit`.</span></span>

<span data-ttu-id="f5c36-214">由于该视图不包含课程实体的集合，不能自动更新模型联编程序`CourseAssignments`导航属性。</span><span class="sxs-lookup"><span data-stu-id="f5c36-214">Since the view doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="f5c36-215">而不是使用模型联编程序更新`CourseAssignments`导航属性，则执行该操作在新`UpdateInstructorCourses`方法。</span><span class="sxs-lookup"><span data-stu-id="f5c36-215">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="f5c36-216">因此你需要以排除`CourseAssignments`模型绑定中的属性。</span><span class="sxs-lookup"><span data-stu-id="f5c36-216">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="f5c36-217">这不需要对调用代码的任何更改`TryUpdateModel`因为您正在使用的白名单重载和`CourseAssignments`不在包括列表中。</span><span class="sxs-lookup"><span data-stu-id="f5c36-217">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the whitelisting overload and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="f5c36-218">如果没有复选框已选中中的代码`UpdateInstructorCourses`初始化`CourseAssignments`具有空集合，并返回导航属性：</span><span class="sxs-lookup"><span data-stu-id="f5c36-218">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

<span data-ttu-id="f5c36-219">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-219">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]</span></span>

<span data-ttu-id="f5c36-220">该代码，然后循环访问数据库中的所有课程，并检查对当前分配给与在视图中选择的教师的每个课程。</span><span class="sxs-lookup"><span data-stu-id="f5c36-220">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the view.</span></span> <span data-ttu-id="f5c36-221">为了便于高效的查找，后者的两个集合都存储在`HashSet`对象。</span><span class="sxs-lookup"><span data-stu-id="f5c36-221">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="f5c36-222">如果选择某一课程复选框，但过程不在`Instructor.CourseAssignments`导航属性，过程添加到集合中的导航属性。</span><span class="sxs-lookup"><span data-stu-id="f5c36-222">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

<span data-ttu-id="f5c36-223">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-223">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]</span></span>

<span data-ttu-id="f5c36-224">如果未选择某一课程复选框，但过程处于`Instructor.CourseAssignments`导航属性，过程删除从导航属性。</span><span class="sxs-lookup"><span data-stu-id="f5c36-224">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

<span data-ttu-id="f5c36-225">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-225">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]</span></span>

### <a name="update-the-instructor-views"></a><span data-ttu-id="f5c36-226">更新教师视图</span><span class="sxs-lookup"><span data-stu-id="f5c36-226">Update the Instructor views</span></span>

<span data-ttu-id="f5c36-227">在*Views/Instructors/Edit.cshtml*，添加**课程**字段与非数组的复选框，通过添加以下代码后立即`div`元素**Office**字段和之前`div`元素**保存**按钮。</span><span class="sxs-lookup"><span data-stu-id="f5c36-227">In *Views/Instructors/Edit.cshtml*, add a **Courses** field with an array of check boxes by adding the following code immediately after the `div` elements for the **Office** field and before the `div` element for the **Save** button.</span></span>

<a id="notepad"></a>
> [!NOTE] 
> <span data-ttu-id="f5c36-228">当你将代码粘贴到 Visual Studio 时，分行符将更改在将中断代码的方式。</span><span class="sxs-lookup"><span data-stu-id="f5c36-228">When you paste the code in Visual Studio, line breaks will be changed in a way that breaks the code.</span></span>  <span data-ttu-id="f5c36-229">按 Ctrl + Z 一次撤消的自动格式设置。</span><span class="sxs-lookup"><span data-stu-id="f5c36-229">Press Ctrl+Z one time to undo the automatic formatting.</span></span>  <span data-ttu-id="f5c36-230">这将修复分行符，使它们看起来像所示。</span><span class="sxs-lookup"><span data-stu-id="f5c36-230">This will fix the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="f5c36-231">缩进不一定是完美的但`@</tr><tr>`， `@:<td>`， `@:</td>`，和`@:</tr>`行都必须在单独的行所示，或你将获取运行时错误。</span><span class="sxs-lookup"><span data-stu-id="f5c36-231">The indentation doesn't have to be perfect, but the `@</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown or you'll get a runtime error.</span></span> <span data-ttu-id="f5c36-232">与所选的新代码块，按 tab 键三次到了新代码与现有代码的行。</span><span class="sxs-lookup"><span data-stu-id="f5c36-232">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span>

<span data-ttu-id="f5c36-233">[!code-html[Main](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-233">[!code-html[Main](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]</span></span>

<span data-ttu-id="f5c36-234">此代码将创建一个 HTML 表，包含三列。</span><span class="sxs-lookup"><span data-stu-id="f5c36-234">This code creates an HTML table that has three columns.</span></span> <span data-ttu-id="f5c36-235">处于每个列组成课程编号及标题的标题后跟一个复选框。</span><span class="sxs-lookup"><span data-stu-id="f5c36-235">In each column is a check box followed by a caption that consists of the course number and title.</span></span> <span data-ttu-id="f5c36-236">所有对应的复选框具有相同的名称 ("selectedCourses")，通知它们将被视为一组模型联编程序。</span><span class="sxs-lookup"><span data-stu-id="f5c36-236">The check boxes all have the same name ("selectedCourses"), which informs the model binder that they are to be treated as a group.</span></span> <span data-ttu-id="f5c36-237">每个复选框的值属性设置为的值`CourseID`。</span><span class="sxs-lookup"><span data-stu-id="f5c36-237">The value attribute of each check box is set to the value of `CourseID`.</span></span> <span data-ttu-id="f5c36-238">当发页面时，模型联编程序会将数组传递给组成的控制器`CourseID`仅复选框进行选择的值。</span><span class="sxs-lookup"><span data-stu-id="f5c36-238">When the page is posted, the model binder passes an array to the controller that consists of the `CourseID` values for only the check boxes which are selected.</span></span>

<span data-ttu-id="f5c36-239">当最初呈现复选框时，为分配给教师的课程的那些具有检查属性，后者选择它们 （它们检查的显示）。</span><span class="sxs-lookup"><span data-stu-id="f5c36-239">When the check boxes are initially rendered, those that are for courses assigned to the instructor have checked attributes, which selects them (displays them checked).</span></span>

<span data-ttu-id="f5c36-240">运行教师索引页，并单击**编辑**上以查看一个教师**编辑**页。</span><span class="sxs-lookup"><span data-stu-id="f5c36-240">Run the Instructor Index page, and click **Edit** on an instructor to see the **Edit** page.</span></span>

![课程教师编辑页](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="f5c36-242">更改某些课程作业并单击保存。</span><span class="sxs-lookup"><span data-stu-id="f5c36-242">Change some course assignments and click Save.</span></span> <span data-ttu-id="f5c36-243">所做的更改会反映在索引页面。</span><span class="sxs-lookup"><span data-stu-id="f5c36-243">The changes you make are reflected on the Index page.</span></span>

> [!NOTE] 
> <span data-ttu-id="f5c36-244">用此处编辑教师课程数据的方法适用于没有有限的数量的课程。</span><span class="sxs-lookup"><span data-stu-id="f5c36-244">The approach taken here to edit instructor course data works well when there is a limited number of courses.</span></span> <span data-ttu-id="f5c36-245">对于则大得多的集合，会需要不同的 UI 以及不同的更新方法。</span><span class="sxs-lookup"><span data-stu-id="f5c36-245">For collections that are much larger, a different UI and a different updating method would be required.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="f5c36-246">更新删除页</span><span class="sxs-lookup"><span data-stu-id="f5c36-246">Update the Delete page</span></span>

<span data-ttu-id="f5c36-247">在*InstructorsController.cs*，删除`DeleteConfirmed`方法并插入以下代码在其位置。</span><span class="sxs-lookup"><span data-stu-id="f5c36-247">In *InstructorsController.cs*, delete the `DeleteConfirmed` method and insert the following code in its place.</span></span>

<span data-ttu-id="f5c36-248">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-248">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]</span></span>

<span data-ttu-id="f5c36-249">此代码进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="f5c36-249">This code makes the following changes:</span></span>

* <span data-ttu-id="f5c36-250">执行预先加载的`CourseAssignments`导航属性。</span><span class="sxs-lookup"><span data-stu-id="f5c36-250">Does eager loading for the `CourseAssignments` navigation property.</span></span>  <span data-ttu-id="f5c36-251">您必须包含此或 EF 不会知道有关相关`CourseAssignment`实体并不会将其删除。</span><span class="sxs-lookup"><span data-stu-id="f5c36-251">You have to include this or EF won't know about related `CourseAssignment` entities and won't delete them.</span></span>  <span data-ttu-id="f5c36-252">若要避免来阅读它们此处你可以配置级联删除数据库中。</span><span class="sxs-lookup"><span data-stu-id="f5c36-252">To avoid needing to read them here you could configure cascade delete in the database.</span></span>

* <span data-ttu-id="f5c36-253">如果要删除教师被指定为任何部门的管理员，移除这些部门教师分配。</span><span class="sxs-lookup"><span data-stu-id="f5c36-253">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="add-office-location-and-courses-to-the-create-page"></a><span data-ttu-id="f5c36-254">将 office 位置和课程添加到创建页</span><span class="sxs-lookup"><span data-stu-id="f5c36-254">Add office location and courses to the Create page</span></span>

<span data-ttu-id="f5c36-255">在*InstructorsController.cs*，删除的 HttpGet 和 HttpPost`Create`方法，然后在它们的位置添加以下代码：</span><span class="sxs-lookup"><span data-stu-id="f5c36-255">In *InstructorsController.cs*, delete the HttpGet and HttpPost `Create` methods, and then add the following code in their place:</span></span>

<span data-ttu-id="f5c36-256">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-256">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]</span></span>

<span data-ttu-id="f5c36-257">此代码将类似于你所看到的针对`Edit`方法除外，它最初没有课程处于选中状态。</span><span class="sxs-lookup"><span data-stu-id="f5c36-257">This code is similar to what you saw for the `Edit` methods except that initially no courses are selected.</span></span> <span data-ttu-id="f5c36-258">HttpGet`Create`方法调用`PopulateAssignedCourseData`方法不是因为可能存在但在选择的课程，以便提供为空集合`foreach`（否则查看代码将引发空引用异常） 的视图中的循环。</span><span class="sxs-lookup"><span data-stu-id="f5c36-258">The HttpGet `Create` method calls the `PopulateAssignedCourseData` method not because there might be courses selected but in order to provide an empty collection for the `foreach` loop in the view (otherwise the view code would throw a null reference exception).</span></span>

<span data-ttu-id="f5c36-259">HttpPost`Create`方法将添加到每个所选的课程`CourseAssignments`检查验证错误和向数据库添加新教师前的导航属性。</span><span class="sxs-lookup"><span data-stu-id="f5c36-259">The HttpPost `Create` method adds each selected course to the `CourseAssignments` navigation property before it checks for validation errors and adds the new instructor to the database.</span></span> <span data-ttu-id="f5c36-260">即使有模型错误以便时模型错误 （例如，用户键控无效的日期），并且页面将重新显示并显示错误消息，将自动还原所做的任何课程选择添加课程。</span><span class="sxs-lookup"><span data-stu-id="f5c36-260">Courses are added even if there are model errors so that when there are model errors (for an example, the user keyed an invalid date), and the page is redisplayed with an error message, any course selections that were made are automatically restored.</span></span>

<span data-ttu-id="f5c36-261">请注意，若要添加到的课程`CourseAssignments`导航属性，你必须初始化为空集合属性：</span><span class="sxs-lookup"><span data-stu-id="f5c36-261">Notice that in order to be able to add courses to the `CourseAssignments` navigation property you have to initialize the property as an empty collection:</span></span>

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

<span data-ttu-id="f5c36-262">作为执行此操作在控制器代码中的替代方法，你无法以执行此操作教师模型通过更改属性 getter 以自动创建集合如果不存在，如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="f5c36-262">As an alternative to doing this in controller code, you could do it in the Instructor model by changing the property getter to automatically create the collection if it doesn't exist, as shown in the following example:</span></span>

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

<span data-ttu-id="f5c36-263">如果你修改`CourseAssignments`属性这种方式，可以删除在控制器中的显式属性初始化代码。</span><span class="sxs-lookup"><span data-stu-id="f5c36-263">If you modify the `CourseAssignments` property in this way, you can remove the explicit property initialization code in the controller.</span></span>

<span data-ttu-id="f5c36-264">在*Views/Instructor/Create.cshtml*、 office 位置中添加文本框和复选框为提交按钮之前的课程。</span><span class="sxs-lookup"><span data-stu-id="f5c36-264">In *Views/Instructor/Create.cshtml*, add an office location text box and check boxes for courses before the Submit button.</span></span> <span data-ttu-id="f5c36-265">在编辑页中，如[修复如果 Visual Studio 时将其粘贴代码重新格式化的格式设置](#notepad)。</span><span class="sxs-lookup"><span data-stu-id="f5c36-265">As in the case of the Edit page, [fix the formatting if Visual Studio reformats the code when you paste it](#notepad).</span></span>

<span data-ttu-id="f5c36-266">[!code-html[Main](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]</span><span class="sxs-lookup"><span data-stu-id="f5c36-266">[!code-html[Main](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]</span></span>

<span data-ttu-id="f5c36-267">通过运行的测试**创建**页并添加一个教师。</span><span class="sxs-lookup"><span data-stu-id="f5c36-267">Test by running the **Create** page and adding an instructor.</span></span> 

## <a name="handling-transactions"></a><span data-ttu-id="f5c36-268">处理事务</span><span class="sxs-lookup"><span data-stu-id="f5c36-268">Handling Transactions</span></span>

<span data-ttu-id="f5c36-269">中所述[CRUD 教程](crud.md)，实体框架隐式实现事务。</span><span class="sxs-lookup"><span data-stu-id="f5c36-269">As explained in the [CRUD tutorial](crud.md), the Entity Framework implicitly implements transactions.</span></span> <span data-ttu-id="f5c36-270">有关方案，你需要更多的控制-例如，如果你想要包括在实体框架外部事务-中执行的操作，请参阅[事务](https://docs.microsoft.com/ef/core/saving/transactions)。</span><span class="sxs-lookup"><span data-stu-id="f5c36-270">For scenarios where you need more control -- for example, if you want to include operations done outside of Entity Framework in a transaction -- see [Transactions](https://docs.microsoft.com/ef/core/saving/transactions).</span></span>

## <a name="summary"></a><span data-ttu-id="f5c36-271">摘要</span><span class="sxs-lookup"><span data-stu-id="f5c36-271">Summary</span></span>

<span data-ttu-id="f5c36-272">你现在已经完成对使用相关的数据引入。</span><span class="sxs-lookup"><span data-stu-id="f5c36-272">You have now completed the introduction to working with related data.</span></span> <span data-ttu-id="f5c36-273">在下一步的教程中，你将看到如何处理并发冲突。</span><span class="sxs-lookup"><span data-stu-id="f5c36-273">In the next tutorial you'll see how to handle concurrency conflicts.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="f5c36-274">[上一页](read-related-data.md)
[下一页](concurrency.md)</span><span class="sxs-lookup"><span data-stu-id="f5c36-274">[Previous](read-related-data.md)
[Next](concurrency.md)</span></span>  
