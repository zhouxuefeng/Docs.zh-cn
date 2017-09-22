---
title: "ASP.NET 核心 MVC 与 EF 核心-CRUD-2 的 10"
author: tdykstra
description: 
keywords: "ASP.NET 核心、 实体框架核心、 CRUD，创建、 读取、 更新、 删除"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 6e1cd570-40f1-4b24-8b6e-7d2d27758f18
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/crud
ms.openlocfilehash: 9fc2b4c126c4d109deb2125f0db70a355c04eb15
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="create-read-update-and-delete---ef-core-with-aspnet-core-mvc-tutorial-2-of-10"></a><span data-ttu-id="0beee-103">创建、 读取、 更新和删除的 EF 内核，它们有 ASP.NET 核心 MVC 教程 (2 个 10)</span><span class="sxs-lookup"><span data-stu-id="0beee-103">Create, Read, Update, and Delete - EF Core with ASP.NET Core MVC tutorial (2 of 10)</span></span>

<span data-ttu-id="0beee-104">通过[Tom Dykstra](https://github.com/tdykstra)和[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0beee-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0beee-105">Contoso 大学示例 web 应用程序演示如何创建使用实体框架核心和 Visual Studio 的 ASP.NET 核心 MVC web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="0beee-105">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="0beee-106">有关教程系列的信息，请参阅[序列中的第一个教程](intro.md)。</span><span class="sxs-lookup"><span data-stu-id="0beee-106">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="0beee-107">在前面的教程，你创建的 MVC 应用程序存储和显示数据使用实体框架和 SQL Server LocalDB。</span><span class="sxs-lookup"><span data-stu-id="0beee-107">In the previous tutorial, you created an MVC application that stores and displays data using the Entity Framework and SQL Server LocalDB.</span></span> <span data-ttu-id="0beee-108">在本教程中，你将查看和自定义 CRUD （创建、 读取、 更新、 删除） 的 MVC 基架自动为你创建在控制器和视图中的代码。</span><span class="sxs-lookup"><span data-stu-id="0beee-108">In this tutorial, you'll review and customize the CRUD (create, read, update, delete) code that the MVC scaffolding automatically creates for you in controllers and views.</span></span>

> [!NOTE] 
> <span data-ttu-id="0beee-109">它是常见的做法，以便创建你的控制器和数据访问层之间的抽象层实现存储库模式。</span><span class="sxs-lookup"><span data-stu-id="0beee-109">It's a common practice to implement the repository pattern in order to create an abstraction layer between your controller and the data access layer.</span></span> <span data-ttu-id="0beee-110">若要使这些教程简单和关注教学如何使用实体框架本身，它们不使用存储库。</span><span class="sxs-lookup"><span data-stu-id="0beee-110">To keep these tutorials simple and focused on teaching how to use the Entity Framework itself, they don't use repositories.</span></span> <span data-ttu-id="0beee-111">有关存储库和 EF 的信息，请参阅[本系列最后一个教程](advanced.md)。</span><span class="sxs-lookup"><span data-stu-id="0beee-111">For information about repositories with EF, see [the last tutorial in this series](advanced.md).</span></span>

<span data-ttu-id="0beee-112">在本教程中，您将可以使用以下网页：</span><span class="sxs-lookup"><span data-stu-id="0beee-112">In this tutorial, you'll work with the following web pages:</span></span>

![学生详细信息页](crud/_static/student-details.png)

![学生创建页](crud/_static/student-create.png)

![学生编辑页](crud/_static/student-edit.png)

![学生删除页](crud/_static/student-delete.png)

## <a name="customize-the-details-page"></a><span data-ttu-id="0beee-117">自定义的详细信息页</span><span class="sxs-lookup"><span data-stu-id="0beee-117">Customize the Details page</span></span>

<span data-ttu-id="0beee-118">学生索引页的基架的代码中省略`Enrollments`属性，因为该属性包含集合。</span><span class="sxs-lookup"><span data-stu-id="0beee-118">The scaffolded code for the Students Index page left out the `Enrollments` property, because that property holds a collection.</span></span> <span data-ttu-id="0beee-119">在**详细信息**页上，将集合的内容显示在 HTML 表。</span><span class="sxs-lookup"><span data-stu-id="0beee-119">In the **Details** page, you'll display the contents of the collection in an HTML table.</span></span>

<span data-ttu-id="0beee-120">在*Controllers/StudentsController.cs*，有关详细信息的操作方法查看使用`SingleOrDefaultAsync`方法来检索单个`Student`实体。</span><span class="sxs-lookup"><span data-stu-id="0beee-120">In *Controllers/StudentsController.cs*, the action method for the Details view uses the `SingleOrDefaultAsync` method to retrieve a single `Student` entity.</span></span> <span data-ttu-id="0beee-121">将调用的代码添加`Include`。</span><span class="sxs-lookup"><span data-stu-id="0beee-121">Add code that calls `Include`.</span></span> <span data-ttu-id="0beee-122">`ThenInclude`和`AsNoTracking`方法，如以下突出显示的代码中所示。</span><span class="sxs-lookup"><span data-stu-id="0beee-122">`ThenInclude`,  and `AsNoTracking` methods, as shown in the following highlighted code.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Details&highlight=8-12)]

<span data-ttu-id="0beee-123">`Include`和`ThenInclude`方法导致要加载的上下文`Student.Enrollments`导航属性，并在每个注册`Enrollment.Course`导航属性。</span><span class="sxs-lookup"><span data-stu-id="0beee-123">The `Include` and `ThenInclude` methods cause the context to load the `Student.Enrollments` navigation property, and within each enrollment the `Enrollment.Course` navigation property.</span></span>  <span data-ttu-id="0beee-124">你将了解有关这些方法中[读取相关的数据](read-related-data.md)教程。</span><span class="sxs-lookup"><span data-stu-id="0beee-124">You'll learn more about these methods in the [reading related data](read-related-data.md) tutorial.</span></span>

<span data-ttu-id="0beee-125">`AsNoTracking`方法可以提高其中返回的实体将不会更新在当前上下文的生存期中的方案中的性能。</span><span class="sxs-lookup"><span data-stu-id="0beee-125">The `AsNoTracking` method improves performance in scenarios where the entities returned will not be updated in the current context's lifetime.</span></span> <span data-ttu-id="0beee-126">你将了解更多有关`AsNoTracking`在本教程末尾。</span><span class="sxs-lookup"><span data-stu-id="0beee-126">You'll learn more about `AsNoTracking` at the end of this tutorial.</span></span>

### <a name="route-data"></a><span data-ttu-id="0beee-127">路线数据</span><span class="sxs-lookup"><span data-stu-id="0beee-127">Route data</span></span>

<span data-ttu-id="0beee-128">传递到的密钥值`Details`方法来自*将数据路由*。</span><span class="sxs-lookup"><span data-stu-id="0beee-128">The key value that is passed to the `Details` method comes from *route data*.</span></span> <span data-ttu-id="0beee-129">路由数据是模型联编程序的 URL 段中找到的数据。</span><span class="sxs-lookup"><span data-stu-id="0beee-129">Route data is data that the model binder found in a segment of the URL.</span></span> <span data-ttu-id="0beee-130">例如，默认路由指定控制器、 操作和 id 段：</span><span class="sxs-lookup"><span data-stu-id="0beee-130">For example, the default route specifies controller, action, and id segments:</span></span>

[!code-csharp[Main](intro/samples/cu/Startup.cs?name=snippet_Route&highlight=5)]

<span data-ttu-id="0beee-131">在下面的 URL，默认路由教师映射为控制器，作为相应的操作、 索引和 1，因为 id;这些是路由数据值。</span><span class="sxs-lookup"><span data-stu-id="0beee-131">In the following URL, the default route maps Instructor as the controller, Index as the action, and 1 as the id; these are route data values.</span></span>

```
http://localhost:1230/Instructor/Index/1?courseID=2021
```

<span data-ttu-id="0beee-132">URL 的最后部分 ("？ courseID = 2021年") 是一个查询字符串值。</span><span class="sxs-lookup"><span data-stu-id="0beee-132">The last part of the URL ("?courseID=2021") is a query string value.</span></span> <span data-ttu-id="0beee-133">模型联编程序还将传递到的 ID 值`Details`方法`id`如果将其作为查询字符串值传递的参数：</span><span class="sxs-lookup"><span data-stu-id="0beee-133">The model binder will also pass the ID value to the `Details` method `id` parameter if you pass it as a query string value:</span></span>

```
http://localhost:1230/Instructor/Index?id=1&CourseID=2021
```

<span data-ttu-id="0beee-134">在索引页中，通过在 Razor 视图中的标记帮助器语句创建超链接 Url。</span><span class="sxs-lookup"><span data-stu-id="0beee-134">In the Index page, hyperlink URLs are created by tag helper statements in the Razor view.</span></span> <span data-ttu-id="0beee-135">在以下 Razor 代码中，`id`参数和默认路由匹配，因此`id`将添加到路由数据。</span><span class="sxs-lookup"><span data-stu-id="0beee-135">In the following Razor code, the `id` parameter matches the default route, so `id` is added to the route data.</span></span>

```html
<a asp-action="Edit" asp-route-id="@item.ID">Edit</a>
```

<span data-ttu-id="0beee-136">这将生成以下 HTML 时`item.ID`为 6:</span><span class="sxs-lookup"><span data-stu-id="0beee-136">This generates the following HTML when `item.ID` is 6:</span></span>

```html
<a href="/Students/Edit/6">Edit</a>
```

<span data-ttu-id="0beee-137">在以下 Razor 代码中，`studentID`不匹配默认路由中的参数，因此，它将添加作为查询字符串。</span><span class="sxs-lookup"><span data-stu-id="0beee-137">In the following Razor code, `studentID` doesn't match a parameter in the default route, so it's added as a query string.</span></span>

```html
<a asp-action="Edit" asp-route-studentID="@item.ID">Edit</a>
```

<span data-ttu-id="0beee-138">这将生成以下 HTML 时`item.ID`为 6:</span><span class="sxs-lookup"><span data-stu-id="0beee-138">This generates the following HTML when `item.ID` is 6:</span></span>

```html
<a href="/Students/Edit?studentID=6">Edit</a>
```

<span data-ttu-id="0beee-139">有关标记帮助器的详细信息，请参阅[中 ASP.NET Core 标记帮助程序](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="0beee-139">For more information about tag helpers, see [Tag helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

### <a name="add-enrollments-to-the-details-view"></a><span data-ttu-id="0beee-140">将注册添加到详细信息视图</span><span class="sxs-lookup"><span data-stu-id="0beee-140">Add enrollments to the Details view</span></span>

<span data-ttu-id="0beee-141">打开*Views/Students/Details.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="0beee-141">Open *Views/Students/Details.cshtml*.</span></span> <span data-ttu-id="0beee-142">使用显示每个字段`DisplayNameFor`和`DisplayFor`帮助器，如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="0beee-142">Each field is displayed using `DisplayNameFor` and `DisplayFor` helpers, as shown in the following example:</span></span>

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=13-18&highlight=2,5)]

<span data-ttu-id="0beee-143">最后一个字段后，立即在关闭前`</dl>`标记中，添加以下代码以显示注册的列表：</span><span class="sxs-lookup"><span data-stu-id="0beee-143">After the last field and immediately before the closing `</dl>` tag, add the following code to display a list of enrollments:</span></span>

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=31-52)]

<span data-ttu-id="0beee-144">如果后粘贴代码，代码缩进有误，，按 CTRL-K-D 更正它。</span><span class="sxs-lookup"><span data-stu-id="0beee-144">If code indentation is wrong after you paste the code, press CTRL-K-D to correct it.</span></span>

<span data-ttu-id="0beee-145">此代码循环访问中的实体`Enrollments`导航属性。</span><span class="sxs-lookup"><span data-stu-id="0beee-145">This code loops through the entities in the `Enrollments` navigation property.</span></span> <span data-ttu-id="0beee-146">对于每个注册，它显示课程标题和评分。</span><span class="sxs-lookup"><span data-stu-id="0beee-146">For each enrollment, it displays the course title and the grade.</span></span> <span data-ttu-id="0beee-147">正在从存储中的课程实体检索课程标题`Course`注册实体导航属性。</span><span class="sxs-lookup"><span data-stu-id="0beee-147">The course title is retrieved from the Course entity that's stored in the `Course` navigation property of the Enrollments entity.</span></span>

<span data-ttu-id="0beee-148">运行应用程序中，选择**学生**卡，然后单击**详细信息**一名学生的链接。</span><span class="sxs-lookup"><span data-stu-id="0beee-148">Run the app, select the **Students** tab, and click the **Details** link for a student.</span></span> <span data-ttu-id="0beee-149">为所选学生查看课程和年级的列表：</span><span class="sxs-lookup"><span data-stu-id="0beee-149">You see the list of courses and grades for the selected student:</span></span>

![学生详细信息页](crud/_static/student-details.png)

## <a name="update-the-create-page"></a><span data-ttu-id="0beee-151">更新创建页</span><span class="sxs-lookup"><span data-stu-id="0beee-151">Update the Create page</span></span>

<span data-ttu-id="0beee-152">在*StudentsController.cs*，修改 HttpPost`Create`方法通过添加 try catch 块和删除从 ID`Bind`属性。</span><span class="sxs-lookup"><span data-stu-id="0beee-152">In *StudentsController.cs*, modify the HttpPost `Create` method by adding a try-catch block and removing ID from the `Bind` attribute.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=4,6-7,14-21)]

<span data-ttu-id="0beee-153">此代码添加到学生实体的 ASP.NET MVC 模型联编程序通过创建学生实体设置，然后将所做的更改保存到数据库。</span><span class="sxs-lookup"><span data-stu-id="0beee-153">This code adds the Student entity created by the ASP.NET MVC model binder to the Students entity set and then saves the changes to the database.</span></span> <span data-ttu-id="0beee-154">（模型联编程序指 ASP.NET MVC 功能，让你更轻松地处理提交的窗体的数据; 模型联编程序将已发布的窗体值转换为 CLR 类型，并将其传递给参数中的操作方法。</span><span class="sxs-lookup"><span data-stu-id="0beee-154">(Model binder refers to the ASP.NET MVC functionality that makes it easier for you to work with data submitted by a form; a model binder converts posted form values to CLR types and passes them to the action method in parameters.</span></span> <span data-ttu-id="0beee-155">在此情况下，模型联编程序实例化学生实体为你使用从窗体集合的属性值。）</span><span class="sxs-lookup"><span data-stu-id="0beee-155">In this case, the model binder instantiates a Student entity for you using property values from the Form collection.)</span></span>

<span data-ttu-id="0beee-156">你删除`ID`从`Bind`特性，因为 ID 是 SQL Server 将插入行时自动设置的主键值。</span><span class="sxs-lookup"><span data-stu-id="0beee-156">You removed `ID` from the `Bind` attribute because ID is the primary key value which SQL Server will set automatically when the row is inserted.</span></span> <span data-ttu-id="0beee-157">来自用户的输入不设置的 ID 值。</span><span class="sxs-lookup"><span data-stu-id="0beee-157">Input from the user does not set the ID value.</span></span>

<span data-ttu-id="0beee-158">除`Bind`属性，try catch 块将仅对基架的代码所做的更改。</span><span class="sxs-lookup"><span data-stu-id="0beee-158">Other than the `Bind` attribute, the try-catch block is the only change you've made to the scaffolded code.</span></span> <span data-ttu-id="0beee-159">如果异常派生自`DbUpdateException`是捕捉到正在保存所做的更改时，将显示一般错误消息。</span><span class="sxs-lookup"><span data-stu-id="0beee-159">If an exception that derives from `DbUpdateException` is caught while the changes are being saved, a generic error message is displayed.</span></span> <span data-ttu-id="0beee-160">`DbUpdateException`由外部的应用程序，而不是编程错误，有时会导致异常，因此建议用户以重试。</span><span class="sxs-lookup"><span data-stu-id="0beee-160">`DbUpdateException` exceptions are sometimes caused by something external to the application rather than a programming error, so the user is advised to try again.</span></span> <span data-ttu-id="0beee-161">尽管在此示例中未实现，但生产质量应用程序会将异常记录。</span><span class="sxs-lookup"><span data-stu-id="0beee-161">Although not implemented in this sample, a production quality application would log the exception.</span></span> <span data-ttu-id="0beee-162">有关详细信息，请参阅**要深入探索日志**主题中[监视和遥测 （构建真实世界云应用程序与 Azure）](https://docs.microsoft.com/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry)。</span><span class="sxs-lookup"><span data-stu-id="0beee-162">For more information, see the **Log for insight** section in [Monitoring and Telemetry (Building Real-World Cloud Apps with Azure)](https://docs.microsoft.com/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).</span></span>

<span data-ttu-id="0beee-163">`ValidateAntiForgeryToken`属性有助于防止跨站点请求伪造 (CSRF) 攻击。</span><span class="sxs-lookup"><span data-stu-id="0beee-163">The `ValidateAntiForgeryToken` attribute helps prevent cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="0beee-164">令牌自动注入到按查看[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper)和由用户提交表单时，将包括。</span><span class="sxs-lookup"><span data-stu-id="0beee-164">The token is automatically injected into the view by the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) and is included when the form is submitted by the user.</span></span> <span data-ttu-id="0beee-165">验证的令牌`ValidateAntiForgeryToken`属性。</span><span class="sxs-lookup"><span data-stu-id="0beee-165">The token is validated by the `ValidateAntiForgeryToken` attribute.</span></span> <span data-ttu-id="0beee-166">有关 CSRF 的详细信息，请参阅[反请求伪造](../../security/anti-request-forgery.md)。</span><span class="sxs-lookup"><span data-stu-id="0beee-166">For more information about CSRF, see [Anti-Request Forgery](../../security/anti-request-forgery.md).</span></span>

<a id="overpost"></a>
### <a name="security-note-about-overposting"></a><span data-ttu-id="0beee-167">有关 overposting 安全说明</span><span class="sxs-lookup"><span data-stu-id="0beee-167">Security note about overposting</span></span>

<span data-ttu-id="0beee-168">`Bind`包括的基架的代码的属性`Create`方法是一种方法，以防止 overposting 中创建的方案。</span><span class="sxs-lookup"><span data-stu-id="0beee-168">The `Bind` attribute that the scaffolded code includes on the `Create` method is one way to protect against overposting in create scenarios.</span></span> <span data-ttu-id="0beee-169">例如，假设学生实体包含`Secret`你不希望此网页可设置的属性。</span><span class="sxs-lookup"><span data-stu-id="0beee-169">For example, suppose the Student entity includes a `Secret` property that you don't want this web page to set.</span></span>

```csharp
public class Student
{
    public int ID { get; set; }
    public string LastName { get; set; }
    public string FirstMidName { get; set; }
    public DateTime EnrollmentDate { get; set; }
    public string Secret { get; set; }
}
```

<span data-ttu-id="0beee-170">即使你没有`Secret`在网页上，黑客字段无法使用 Fiddler，之类的工具或编写一些 JavaScript，发布`Secret`形成的值。</span><span class="sxs-lookup"><span data-stu-id="0beee-170">Even if you don't have a `Secret` field on the web page, a hacker could use a tool such as Fiddler, or write some JavaScript, to post a `Secret` form value.</span></span> <span data-ttu-id="0beee-171">而无需`Bind`属性限制时它将创建学生实例，模型联编程序模型联编程序使用的字段将选取的`Secret`窗体值并使用它来创建学生实体实例。</span><span class="sxs-lookup"><span data-stu-id="0beee-171">Without the `Bind` attribute limiting the fields that the model binder uses when it creates a Student instance, the model binder would pick up that `Secret` form value and use it to create the Student entity instance.</span></span> <span data-ttu-id="0beee-172">然后任何值为指定黑客`Secret`窗体字段将在你的数据库中更新。</span><span class="sxs-lookup"><span data-stu-id="0beee-172">Then whatever value the hacker specified for the `Secret` form field would be updated in your database.</span></span> <span data-ttu-id="0beee-173">下图显示 Fiddler 工具添加`Secret`（具有值"OverPost"） 到已发布的窗体值的字段。</span><span class="sxs-lookup"><span data-stu-id="0beee-173">The following image shows the Fiddler tool adding the `Secret` field (with the value "OverPost") to the posted form values.</span></span>

![Fiddler 添加机密字段](crud/_static/fiddler.png)

<span data-ttu-id="0beee-175">随后将可"OverPost"成功添加到的值`Secret`属性插入行，尽管你永远不会预期网页能够设置该属性。</span><span class="sxs-lookup"><span data-stu-id="0beee-175">The value "OverPost" would then be successfully added to the `Secret` property of the inserted row, although you never intended that the web page be able to set that property.</span></span>

<span data-ttu-id="0beee-176">你可以通过先从数据库读取实体，然后再调用防止在编辑方案中的 overposting `TryUpdateModel`，并传递显式允许的属性列表中。</span><span class="sxs-lookup"><span data-stu-id="0beee-176">You can prevent overposting in edit scenarios by reading the entity from the database first and then calling `TryUpdateModel`, passing in an explicit allowed properties list.</span></span> <span data-ttu-id="0beee-177">这是这些教程中使用的方法。</span><span class="sxs-lookup"><span data-stu-id="0beee-177">That is the method used in these tutorials.</span></span>

<span data-ttu-id="0beee-178">防止首选许多开发人员的 overposting 一种备用方法是使用视图模型，而不是实体类与模型绑定。</span><span class="sxs-lookup"><span data-stu-id="0beee-178">An alternative way to prevent overposting that is preferred by many developers is to use view models rather than entity classes with model binding.</span></span> <span data-ttu-id="0beee-179">包括你想要在视图模型中更新的属性。</span><span class="sxs-lookup"><span data-stu-id="0beee-179">Include only the properties you want to update in the view model.</span></span> <span data-ttu-id="0beee-180">完成 MVC 模型联编程序后，将复制到实体实例，根据需要使用 AutoMapper 等工具的查看模型属性。</span><span class="sxs-lookup"><span data-stu-id="0beee-180">Once the MVC model binder has finished, copy the view model properties to the entity instance, optionally using a tool such as AutoMapper.</span></span> <span data-ttu-id="0beee-181">使用`_context.Entry`对实体实例，其状态设置为`Unchanged`，然后设置`Property("PropertyName").IsModified`为 true 的每个视图模型中包含的实体属性。</span><span class="sxs-lookup"><span data-stu-id="0beee-181">Use `_context.Entry` on the entity instance to set its state to `Unchanged`, and then set `Property("PropertyName").IsModified` to true on each entity property that is included in the view model.</span></span> <span data-ttu-id="0beee-182">此方法适用于同时编辑和创建方案。</span><span class="sxs-lookup"><span data-stu-id="0beee-182">This method works in both edit and create scenarios.</span></span>

### <a name="test-the-create-page"></a><span data-ttu-id="0beee-183">测试创建页</span><span class="sxs-lookup"><span data-stu-id="0beee-183">Test the Create page</span></span>

<span data-ttu-id="0beee-184">中的代码*Views/Students/Create.cshtml*使用`label`， `input`，和`span`（用于验证消息） 标记的每个字段的帮助程序。</span><span class="sxs-lookup"><span data-stu-id="0beee-184">The code in *Views/Students/Create.cshtml* uses `label`, `input`, and `span` (for validation messages) tag helpers for each field.</span></span>

<span data-ttu-id="0beee-185">运行应用程序中，选择**学生**卡，然后单击**新建**。</span><span class="sxs-lookup"><span data-stu-id="0beee-185">Run the app, select the **Students** tab, and click **Create New**.</span></span>

<span data-ttu-id="0beee-186">输入名称和日期。</span><span class="sxs-lookup"><span data-stu-id="0beee-186">Enter names and a date.</span></span> <span data-ttu-id="0beee-187">请尝试输入无效的日期，如果你的浏览器，可以做到这一点。</span><span class="sxs-lookup"><span data-stu-id="0beee-187">Try entering an invalid date if your browser lets you do that.</span></span> <span data-ttu-id="0beee-188">（某些浏览器强制你要使用日期选取器。）然后单击**创建**可查看错误消息。</span><span class="sxs-lookup"><span data-stu-id="0beee-188">(Some browsers force you to use a date picker.) Then click **Create** to see the error message.</span></span>

![日期验证错误](crud/_static/date-error.png)

<span data-ttu-id="0beee-190">这是默认情况下; 获取的服务器端验证在更高版本的教程中，你将看到如何添加还将生成代码以便进行客户端验证的属性。</span><span class="sxs-lookup"><span data-stu-id="0beee-190">This is server-side validation that you get by default; in a later tutorial you'll see how to add attributes that will generate code for client-side validation also.</span></span> <span data-ttu-id="0beee-191">以下突出显示的代码演示模型验证检查在`Create`方法。</span><span class="sxs-lookup"><span data-stu-id="0beee-191">The following highlighted code shows the model validation check in the `Create` method.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=8)]

<span data-ttu-id="0beee-192">将日期更改为有效的值并单击**创建**若要查看显示在新学生**索引**页。</span><span class="sxs-lookup"><span data-stu-id="0beee-192">Change the date to a valid value and click **Create** to see the new student appear in the **Index** page.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="0beee-193">更新编辑页</span><span class="sxs-lookup"><span data-stu-id="0beee-193">Update the Edit page</span></span>

<span data-ttu-id="0beee-194">在*StudentController.cs*，HttpGet`Edit`方法 (而不是`HttpPost`属性) 使用`SingleOrDefaultAsync`方法来检索所选的学生实体，正如你在看到`Details`方法。</span><span class="sxs-lookup"><span data-stu-id="0beee-194">In *StudentController.cs*, the HttpGet `Edit` method (the one without the `HttpPost` attribute) uses the `SingleOrDefaultAsync` method to retrieve the selected Student entity, as you saw in the `Details` method.</span></span> <span data-ttu-id="0beee-195">你不需要更改此方法。</span><span class="sxs-lookup"><span data-stu-id="0beee-195">You don't need to change this method.</span></span>

### <a name="recommended-httppost-edit-code-read-and-update"></a><span data-ttu-id="0beee-196">建议 HttpPost 编辑代码： 读取和更新</span><span class="sxs-lookup"><span data-stu-id="0beee-196">Recommended HttpPost Edit code: Read and update</span></span>

<span data-ttu-id="0beee-197">HttpPost 编辑操作方法替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="0beee-197">Replace the HttpPost Edit action method with the following code.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ReadFirst)]

<span data-ttu-id="0beee-198">这些更改实现安全的最佳做法，以防止 overposting。</span><span class="sxs-lookup"><span data-stu-id="0beee-198">These changes implement a security best practice to prevent overposting.</span></span> <span data-ttu-id="0beee-199">生成基架`Bind`属性，并添加到的实体集与模型联编程序所创建的实体`Modified`标志。</span><span class="sxs-lookup"><span data-stu-id="0beee-199">The scaffolder generated a `Bind` attribute and added the entity created by the model binder to the entity set with a `Modified` flag.</span></span> <span data-ttu-id="0beee-200">代码不建议用于许多方案，因为`Bind`属性将清除任何预先存在的数据中未列出的字段中`Include`参数。</span><span class="sxs-lookup"><span data-stu-id="0beee-200">That code is not recommended for many scenarios because the `Bind` attribute clears out any pre-existing data in fields not listed in the `Include` parameter.</span></span>

<span data-ttu-id="0beee-201">新的代码读取的现有实体和调用`TryUpdateModel`更新中检索到的实体的字段[基于已发布的窗体数据中的用户输入](xref:mvc/models/model-binding#how-model-binding-works)。</span><span class="sxs-lookup"><span data-stu-id="0beee-201">The new code reads the existing entity and calls `TryUpdateModel` to update fields in the retrieved entity [based on user input in the posted form data](xref:mvc/models/model-binding#how-model-binding-works).</span></span> <span data-ttu-id="0beee-202">实体框架自动更改跟踪设置`Modified`窗体输入，更改字段上的标志。</span><span class="sxs-lookup"><span data-stu-id="0beee-202">The Entity Framework's automatic change tracking sets the `Modified` flag on the fields that are changed by form input.</span></span> <span data-ttu-id="0beee-203">当`SaveChanges`方法被调用时，实体框架创建 SQL 语句更新数据库行。</span><span class="sxs-lookup"><span data-stu-id="0beee-203">When the `SaveChanges` method is called, the Entity Framework creates SQL statements to update the database row.</span></span> <span data-ttu-id="0beee-204">并发冲突将被忽略，并更新了用户的表格列更新数据库中。</span><span class="sxs-lookup"><span data-stu-id="0beee-204">Concurrency conflicts are ignored, and only the table columns that were updated by the user are updated in the database.</span></span> <span data-ttu-id="0beee-205">（后面的教程演示如何处理并发冲突。）</span><span class="sxs-lookup"><span data-stu-id="0beee-205">(A later tutorial shows how to handle concurrency conflicts.)</span></span>

<span data-ttu-id="0beee-206">作为最佳做法以防止过多发布，你想要通过可更新的字段**编辑**页是白名单中的`TryUpdateModel`参数。</span><span class="sxs-lookup"><span data-stu-id="0beee-206">As a best practice to prevent overposting, the fields that you want to be updateable by the **Edit** page are whitelisted in the `TryUpdateModel` parameters.</span></span> <span data-ttu-id="0beee-207">（前面的参数列表中的字段的列表为空字符串是为要使用窗体字段名称的前缀。）当前没有额外字段，你要保护的但列出你想要绑定的模型联编程序的字段可确保，如果在将来将字段添加到数据模型，它们是否自动受到保护之前你显式将其添加此处。</span><span class="sxs-lookup"><span data-stu-id="0beee-207">(The empty string preceding the list of fields in the parameter list is for a prefix to use with the form fields names.) Currently there are no extra fields that you're protecting, but listing the fields that you want the model binder to bind ensures that if you add fields to the data model in the future, they're automatically protected until you explicitly add them here.</span></span>

<span data-ttu-id="0beee-208">由于这些更改，HttpPost 方法签名`Edit`方法等同于 HttpGet`Edit`方法; 因此已重命名方法`EditPost`。</span><span class="sxs-lookup"><span data-stu-id="0beee-208">As a result of these changes, the method signature of the HttpPost `Edit` method is the same as the HttpGet `Edit` method; therefore you've renamed the method `EditPost`.</span></span>

### <a name="alternative-httppost-edit-code-create-and-attach"></a><span data-ttu-id="0beee-209">替代 HttpPost 编辑代码： 创建和附加</span><span class="sxs-lookup"><span data-stu-id="0beee-209">Alternative HttpPost Edit code: Create and attach</span></span>

<span data-ttu-id="0beee-210">建议的 HttpPost 编辑代码可确保仅已更改的列会更新，并保留你不希望包括模型绑定的属性中的数据。</span><span class="sxs-lookup"><span data-stu-id="0beee-210">The recommended HttpPost edit code ensures that only changed columns get updated and preserves data in properties that you don't want included for model binding.</span></span> <span data-ttu-id="0beee-211">但是，读取为先的方法需要的额外数据库读取，并且可能导致更复杂的代码，用于处理并发冲突。</span><span class="sxs-lookup"><span data-stu-id="0beee-211">However, the read-first approach requires an extra database read, and can result in more complex code for handling concurrency conflicts.</span></span> <span data-ttu-id="0beee-212">一种替代方法是将由 EF 上下文模型联编程序创建实体并将其标记为已修改。</span><span class="sxs-lookup"><span data-stu-id="0beee-212">An alternative is to attach an entity created by the model binder to the EF context and mark it as modified.</span></span> <span data-ttu-id="0beee-213">(未更新你的项目替换为以下代码，它只表明来演示一种可选方法。)</span><span class="sxs-lookup"><span data-stu-id="0beee-213">(Don't update your project with this code, it's only shown to illustrate an optional approach.)</span></span> 

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_CreateAndAttach)]

<span data-ttu-id="0beee-214">在实体中包含的所有字段和可以更新其中的任何 web 页 UI 时，可以使用此方法。</span><span class="sxs-lookup"><span data-stu-id="0beee-214">You can use this approach when the web page UI includes all of the fields in the entity and can update any of them.</span></span>

<span data-ttu-id="0beee-215">基架的代码使用的创建和附加方法，但仅捕获`DbUpdateConcurrencyException`异常并返回 404 错误代码。</span><span class="sxs-lookup"><span data-stu-id="0beee-215">The scaffolded code uses the create-and-attach approach but only catches `DbUpdateConcurrencyException` exceptions and returns 404 error codes.</span></span>  <span data-ttu-id="0beee-216">显示的示例将捕获所有数据库更新异常并显示一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="0beee-216">The example shown catches any database update exception and displays an error message.</span></span>

### <a name="entity-states"></a><span data-ttu-id="0beee-217">实体状态</span><span class="sxs-lookup"><span data-stu-id="0beee-217">Entity States</span></span>

<span data-ttu-id="0beee-218">是否在内存中的实体是在数据库中，其对应行与同步，此信息确定在调用时，会发生什么情况，将跟踪的数据库上下文`SaveChanges`方法。</span><span class="sxs-lookup"><span data-stu-id="0beee-218">The database context keeps track of whether entities in memory are in sync with their corresponding rows in the database, and this information determines what happens when you call the `SaveChanges` method.</span></span> <span data-ttu-id="0beee-219">例如，当传递到新实体`Add`方法，该实体的状态设置为方法`Added`。</span><span class="sxs-lookup"><span data-stu-id="0beee-219">For example, when you pass a new entity to the `Add` method, that entity's state is set to `Added`.</span></span> <span data-ttu-id="0beee-220">然后调用`SaveChanges`方法，数据库上下文发出 SQL INSERT 命令。</span><span class="sxs-lookup"><span data-stu-id="0beee-220">Then when you call the `SaveChanges` method, the database context issues a SQL INSERT command.</span></span>

<span data-ttu-id="0beee-221">实体可能处于以下状态之一：</span><span class="sxs-lookup"><span data-stu-id="0beee-221">An entity may be in one of the following states:</span></span>

* <span data-ttu-id="0beee-222">`Added`。</span><span class="sxs-lookup"><span data-stu-id="0beee-222">`Added`.</span></span> <span data-ttu-id="0beee-223">实体在数据库中尚不存在。</span><span class="sxs-lookup"><span data-stu-id="0beee-223">The entity does not yet exist in the database.</span></span> <span data-ttu-id="0beee-224">`SaveChanges`方法发出 INSERT 语句。</span><span class="sxs-lookup"><span data-stu-id="0beee-224">The `SaveChanges` method issues an INSERT statement.</span></span>

* <span data-ttu-id="0beee-225">`Unchanged`。</span><span class="sxs-lookup"><span data-stu-id="0beee-225">`Unchanged`.</span></span> <span data-ttu-id="0beee-226">无需使用通过此实体完成`SaveChanges`方法。</span><span class="sxs-lookup"><span data-stu-id="0beee-226">Nothing needs to be done with this entity by the `SaveChanges` method.</span></span> <span data-ttu-id="0beee-227">当从数据库读取实体时，该实体开始时具有此状态。</span><span class="sxs-lookup"><span data-stu-id="0beee-227">When you read an entity from the database, the entity starts out with this status.</span></span>

* <span data-ttu-id="0beee-228">`Modified`。</span><span class="sxs-lookup"><span data-stu-id="0beee-228">`Modified`.</span></span> <span data-ttu-id="0beee-229">某些或所有实体的属性值已都更改。</span><span class="sxs-lookup"><span data-stu-id="0beee-229">Some or all of the entity's property values have been modified.</span></span> <span data-ttu-id="0beee-230">`SaveChanges`方法发出 UPDATE 语句。</span><span class="sxs-lookup"><span data-stu-id="0beee-230">The `SaveChanges` method issues an UPDATE statement.</span></span>

* <span data-ttu-id="0beee-231">`Deleted`。</span><span class="sxs-lookup"><span data-stu-id="0beee-231">`Deleted`.</span></span> <span data-ttu-id="0beee-232">实体已标记为删除。</span><span class="sxs-lookup"><span data-stu-id="0beee-232">The entity has been marked for deletion.</span></span> <span data-ttu-id="0beee-233">`SaveChanges`方法发出的 DELETE 语句。</span><span class="sxs-lookup"><span data-stu-id="0beee-233">The `SaveChanges` method issues a DELETE statement.</span></span>

* <span data-ttu-id="0beee-234">`Detached`。</span><span class="sxs-lookup"><span data-stu-id="0beee-234">`Detached`.</span></span> <span data-ttu-id="0beee-235">实体不跟踪的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="0beee-235">The entity isn't being tracked by the database context.</span></span>

<span data-ttu-id="0beee-236">在桌面应用中，通常会自动设置状态更改。</span><span class="sxs-lookup"><span data-stu-id="0beee-236">In a desktop application, state changes are typically set automatically.</span></span> <span data-ttu-id="0beee-237">读取实体并对它的一些属性值进行更改。</span><span class="sxs-lookup"><span data-stu-id="0beee-237">You read an entity and make changes to some of its property values.</span></span> <span data-ttu-id="0beee-238">这将导致其实体状态自动更改为`Modified`。</span><span class="sxs-lookup"><span data-stu-id="0beee-238">This causes its entity state to automatically be changed to `Modified`.</span></span> <span data-ttu-id="0beee-239">然后调用`SaveChanges`，实体框架生成更新仅你更改的实际属性 SQL UPDATE 语句。</span><span class="sxs-lookup"><span data-stu-id="0beee-239">Then when you call `SaveChanges`, the Entity Framework generates a SQL UPDATE statement that updates only the actual properties that you changed.</span></span>

<span data-ttu-id="0beee-240">在 web 应用中，`DbContext`最初读取某个实体以及显示要编辑其数据已释放之后呈现一个页面。</span><span class="sxs-lookup"><span data-stu-id="0beee-240">In a web app, the `DbContext` that initially reads an entity and displays its data to be edited is disposed after a page is rendered.</span></span> <span data-ttu-id="0beee-241">当 HttpPost`Edit`操作方法调用、 新的 web 请求进行并且您具有的新实例`DbContext`。</span><span class="sxs-lookup"><span data-stu-id="0beee-241">When the HttpPost `Edit` action method is called,  a new web request is made and you have a new instance of the `DbContext`.</span></span> <span data-ttu-id="0beee-242">如果你重新读取该新的上下文中的实体，则来模拟桌面处理。</span><span class="sxs-lookup"><span data-stu-id="0beee-242">If you re-read the entity in that new context, you simulate desktop processing.</span></span>

<span data-ttu-id="0beee-243">但如果不希望执行的额外读取操作，你必须使用由模型联编程序创建的实体对象。</span><span class="sxs-lookup"><span data-stu-id="0beee-243">But if you don't want to do the extra read operation, you have to use the entity object created by the model binder.</span></span>  <span data-ttu-id="0beee-244">执行此操作的最简单方法是将实体状态设置为已修改，前面所示的备用 HttpPost 编辑代码中的做法一样。</span><span class="sxs-lookup"><span data-stu-id="0beee-244">The simplest way to do this is to set the entity state to Modified as is done in the alternative HttpPost Edit code shown earlier.</span></span> <span data-ttu-id="0beee-245">然后调用`SaveChanges`，实体框架更新的数据库行中，所有列，因为上下文具有无法知道您更改了哪些属性。</span><span class="sxs-lookup"><span data-stu-id="0beee-245">Then when you call `SaveChanges`, the Entity Framework updates all columns of the database row, because the context has no way to know which properties you changed.</span></span>

<span data-ttu-id="0beee-246">如果你想要避免读取为先的方法，但你还想要更新仅用户实际更改的字段的更新 SQL 语句，则代码会更复杂。</span><span class="sxs-lookup"><span data-stu-id="0beee-246">If you want to avoid the read-first approach, but you also want the SQL UPDATE statement to update only the fields that the user actually changed, the code is more complex.</span></span> <span data-ttu-id="0beee-247">你必须以某种方式保存的原始值 (如通过使用隐藏的字段)，以便它们可用时 HttpPost`Edit`调用方法。</span><span class="sxs-lookup"><span data-stu-id="0beee-247">You have to save the original values in some way (such as by using hidden fields) so that they are available when the HttpPost `Edit` method is called.</span></span> <span data-ttu-id="0beee-248">然后你可以创建使用原始值，调用的学生实体`Attach`实体，该原始版本的方法的新值更新实体的值，然后调用`SaveChanges`。</span><span class="sxs-lookup"><span data-stu-id="0beee-248">Then you can create a Student entity using the original values, call the `Attach` method with that original version of the entity, update the entity's values to the new values, and then call `SaveChanges`.</span></span>

### <a name="test-the-edit-page"></a><span data-ttu-id="0beee-249">测试的编辑页</span><span class="sxs-lookup"><span data-stu-id="0beee-249">Test the Edit page</span></span>

<span data-ttu-id="0beee-250">运行应用程序中，选择**学生**选项卡，然后单击**编辑**超链接。</span><span class="sxs-lookup"><span data-stu-id="0beee-250">Run the app, select the **Students** tab, then click an **Edit** hyperlink.</span></span>

![学生编辑页](crud/_static/student-edit.png)

<span data-ttu-id="0beee-252">更改某些数据，再单击**保存**。</span><span class="sxs-lookup"><span data-stu-id="0beee-252">Change some of the data and click **Save**.</span></span> <span data-ttu-id="0beee-253">**索引**页将打开并查看更改的数据。</span><span class="sxs-lookup"><span data-stu-id="0beee-253">The **Index** page opens and you see the changed data.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="0beee-254">更新删除页</span><span class="sxs-lookup"><span data-stu-id="0beee-254">Update the Delete page</span></span>

<span data-ttu-id="0beee-255">在*StudentController.cs*，HttpGet 的模板代码`Delete`方法使用`SingleOrDefaultAsync`方法来检索所选的学生实体，正如你看到在详细信息视图和编辑方法。</span><span class="sxs-lookup"><span data-stu-id="0beee-255">In *StudentController.cs*, the template code for the HttpGet `Delete` method uses the `SingleOrDefaultAsync` method to retrieve the selected Student entity, as you saw in the Details and Edit methods.</span></span> <span data-ttu-id="0beee-256">但是，若要实现自定义错误消息时对调用`SaveChanges`失败，需要将某些功能添加到此方法，其相应的视图。</span><span class="sxs-lookup"><span data-stu-id="0beee-256">However, to implement a custom error message when the call to `SaveChanges` fails, you'll add some functionality to this method and its corresponding view.</span></span>

<span data-ttu-id="0beee-257">当你看到的针对更新，并创建操作，删除操作需要两个操作方法。</span><span class="sxs-lookup"><span data-stu-id="0beee-257">As you saw for update and create operations, delete operations require two action methods.</span></span> <span data-ttu-id="0beee-258">调用以响应 GET 请求的方法显示为用户提供了机会批准或取消删除操作的视图。</span><span class="sxs-lookup"><span data-stu-id="0beee-258">The method that is called in response to a GET request displays a view that gives the user a chance to approve or cancel the delete operation.</span></span> <span data-ttu-id="0beee-259">如果用户批准它，则创建 POST 请求。</span><span class="sxs-lookup"><span data-stu-id="0beee-259">If the user approves it, a POST request is created.</span></span> <span data-ttu-id="0beee-260">当发生这种情况，HttpPost`Delete`调用方法，该方法然后实际执行删除操作。</span><span class="sxs-lookup"><span data-stu-id="0beee-260">When that happens, the HttpPost `Delete` method is called and then that method actually performs the delete operation.</span></span>

<span data-ttu-id="0beee-261">将将 try catch 块添加到 HttpPost`Delete`方法以处理更新数据库时可能出现的任何错误。</span><span class="sxs-lookup"><span data-stu-id="0beee-261">You'll add a try-catch block to the HttpPost `Delete` method to handle any errors that might occur when the database is updated.</span></span> <span data-ttu-id="0beee-262">如果发生错误，HttpPost Delete 方法调用 HttpGet Delete 方法，将其传递参数，该值指示发生错误。</span><span class="sxs-lookup"><span data-stu-id="0beee-262">If an error occurs, the HttpPost Delete method calls the HttpGet Delete method, passing it a parameter that indicates that an error has occurred.</span></span> <span data-ttu-id="0beee-263">HttpGet Delete 方法然后重新显示确认页以及错误消息，向用户提供机会取消或重试。</span><span class="sxs-lookup"><span data-stu-id="0beee-263">The HttpGet Delete method then redisplays the confirmation page along with the error message, giving the user an opportunity to cancel or try again.</span></span>

<span data-ttu-id="0beee-264">替换 HttpGet`Delete`替换为以下代码，它管理错误报告的操作方法。</span><span class="sxs-lookup"><span data-stu-id="0beee-264">Replace the HttpGet `Delete` action method with the following code, which manages error reporting.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteGet&highlight=1,9,16-21)]

<span data-ttu-id="0beee-265">此代码将接受可选参数，该值指示是否在出现故障，若要保存更改后调用该方法。</span><span class="sxs-lookup"><span data-stu-id="0beee-265">This code accepts an optional parameter that indicates whether the method was called after a failure to save changes.</span></span> <span data-ttu-id="0beee-266">时，此参数为 false HttpGet`Delete`不上一次失败的情况下调用方法。</span><span class="sxs-lookup"><span data-stu-id="0beee-266">This parameter is false when the HttpGet `Delete` method is called without a previous failure.</span></span> <span data-ttu-id="0beee-267">当由 HttpPost`Delete`方法中对数据库更新错误响应，该参数是 true，并且一条错误消息传递给视图。</span><span class="sxs-lookup"><span data-stu-id="0beee-267">When it is called by the HttpPost `Delete` method in response to a database update error, the parameter is true and an error message is passed to the view.</span></span>

### <a name="the-read-first-approach-to-httppost-delete"></a><span data-ttu-id="0beee-268">HttpPost 删除读取为先的方法</span><span class="sxs-lookup"><span data-stu-id="0beee-268">The read-first approach to HttpPost Delete</span></span>

<span data-ttu-id="0beee-269">替换 HttpPost`Delete`操作方法 (名为`DeleteConfirmed`) 替换为以下代码，该执行实际的删除操作并捕获任何数据库更新错误。</span><span class="sxs-lookup"><span data-stu-id="0beee-269">Replace the HttpPost `Delete` action method (named `DeleteConfirmed`) with the following code, which performs the actual delete operation and catches any database update errors.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithReadFirst&highlight=6,8-11,13-14,18-23)]

<span data-ttu-id="0beee-270">此代码检索所选的实体，然后调用`Remove`方法将实体的状态设置为`Deleted`。</span><span class="sxs-lookup"><span data-stu-id="0beee-270">This code retrieves the selected entity, then calls the `Remove` method to set the entity's status to `Deleted`.</span></span> <span data-ttu-id="0beee-271">当`SaveChanges`称为 SQL DELETE 命令生成的。</span><span class="sxs-lookup"><span data-stu-id="0beee-271">When `SaveChanges` is called, a SQL DELETE command is generated.</span></span>

### <a name="the-create-and-attach-approach-to-httppost-delete"></a><span data-ttu-id="0beee-272">HttpPost 删除创建和附加方法</span><span class="sxs-lookup"><span data-stu-id="0beee-272">The create-and-attach approach to HttpPost Delete</span></span>

<span data-ttu-id="0beee-273">如果提高大容量应用程序中的性能是优先级，则无法通过实例化使用只有主要的学生实体来避免不必要的 SQL 查询密钥值，然后将实体状态设置为`Deleted`。</span><span class="sxs-lookup"><span data-stu-id="0beee-273">If improving performance in a high-volume application is a priority, you could avoid an unnecessary SQL query by instantiating a Student entity using only the primary key value and then setting the entity state to `Deleted`.</span></span> <span data-ttu-id="0beee-274">这就是所有实体框架需要以便删除该实体。</span><span class="sxs-lookup"><span data-stu-id="0beee-274">That's all that the Entity Framework needs in order to delete the entity.</span></span> <span data-ttu-id="0beee-275">（不要将此代码放在你的项目; 就在这里仅用于阐释一种替代方法。）</span><span class="sxs-lookup"><span data-stu-id="0beee-275">(Don't put this code in your project; it's here just to illustrate an alternative.)</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithoutReadFirst&highlight=7-8)]

<span data-ttu-id="0beee-276">如果实体有相关的还应删除的数据，请确保数据库中配置该级联删除。</span><span class="sxs-lookup"><span data-stu-id="0beee-276">If the entity has related data that should also be deleted, make sure that cascade delete is configured in the database.</span></span> <span data-ttu-id="0beee-277">使用此方法实体被删除时，EF 可能不知道有要删除的相关的实体。</span><span class="sxs-lookup"><span data-stu-id="0beee-277">With this approach to entity deletion, EF might not realize there are related entities to be deleted.</span></span>

### <a name="update-the-delete-view"></a><span data-ttu-id="0beee-278">更新删除视图</span><span class="sxs-lookup"><span data-stu-id="0beee-278">Update the Delete view</span></span>

<span data-ttu-id="0beee-279">在*Views/Student/Delete.cshtml*，添加之间 h2 标题和 h3 标题中，一条错误消息，如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="0beee-279">In *Views/Student/Delete.cshtml*, add an error message between the h2 heading and the h3 heading, as shown in the following example:</span></span>

[!code-html[](intro/samples/cu/Views/Students/Delete.cshtml?range=7-9&highlight=2)]

<span data-ttu-id="0beee-280">运行应用程序中，选择**学生**卡，然后单击**删除**超链接：</span><span class="sxs-lookup"><span data-stu-id="0beee-280">Run the app, select the **Students** tab, and click a **Delete** hyperlink:</span></span>

![删除确认页面](crud/_static/student-delete.png)

<span data-ttu-id="0beee-282">单击**删除**。</span><span class="sxs-lookup"><span data-stu-id="0beee-282">Click **Delete**.</span></span> <span data-ttu-id="0beee-283">索引页面显示没有已删除的学生。</span><span class="sxs-lookup"><span data-stu-id="0beee-283">The Index page is displayed without the deleted student.</span></span> <span data-ttu-id="0beee-284">（你将看到举例说明的错误处理中并发教程中的操作代码）。</span><span class="sxs-lookup"><span data-stu-id="0beee-284">(You'll see an example of the error handling code in action in the concurrency tutorial.)</span></span>

## <a name="closing-database-connections"></a><span data-ttu-id="0beee-285">关闭数据库连接</span><span class="sxs-lookup"><span data-stu-id="0beee-285">Closing database connections</span></span>

<span data-ttu-id="0beee-286">要释放数据库连接包含的资源，上下文实例必须释放尽可能快地完成此操作使用它。</span><span class="sxs-lookup"><span data-stu-id="0beee-286">To free up the resources that a database connection holds, the context instance must be disposed as soon as possible when you are done with it.</span></span> <span data-ttu-id="0beee-287">ASP.NET 核心内置[依赖关系注入](../../fundamentals/dependency-injection.md)将为你负责该任务。</span><span class="sxs-lookup"><span data-stu-id="0beee-287">The ASP.NET Core built-in [dependency injection](../../fundamentals/dependency-injection.md) takes care of that task for you.</span></span>

<span data-ttu-id="0beee-288">在*Startup.cs*，你调用[AddDbContext 扩展方法](https://github.com/aspnet/EntityFrameworkCore/blob/03bcb5122e3f577a84498545fcf130ba79a3d987/src/Microsoft.EntityFrameworkCore/EntityFrameworkServiceCollectionExtensions.cs)设置`DbContext`ASP.NET DI 容器中的类。</span><span class="sxs-lookup"><span data-stu-id="0beee-288">In *Startup.cs*, you call the [AddDbContext extension method](https://github.com/aspnet/EntityFrameworkCore/blob/03bcb5122e3f577a84498545fcf130ba79a3d987/src/Microsoft.EntityFrameworkCore/EntityFrameworkServiceCollectionExtensions.cs) to provision the `DbContext` class in the ASP.NET DI container.</span></span> <span data-ttu-id="0beee-289">方法将服务生存期设置为`Scoped`默认情况下。</span><span class="sxs-lookup"><span data-stu-id="0beee-289">That method sets the service lifetime to `Scoped` by default.</span></span> <span data-ttu-id="0beee-290">`Scoped`表示与 web 请求生存时间，一致上下文对象生存期和`Dispose`将 web 请求结束时自动调用方法。</span><span class="sxs-lookup"><span data-stu-id="0beee-290">`Scoped` means the context object lifetime coincides with the web request life time, and the `Dispose` method will be called automatically at the end of the web request.</span></span>

## <a name="handling-transactions"></a><span data-ttu-id="0beee-291">处理事务</span><span class="sxs-lookup"><span data-stu-id="0beee-291">Handling Transactions</span></span>

<span data-ttu-id="0beee-292">默认情况下实体框架隐式实现事务。</span><span class="sxs-lookup"><span data-stu-id="0beee-292">By default the Entity Framework implicitly implements transactions.</span></span> <span data-ttu-id="0beee-293">在方案中，对多个行或表进行更改，然后调用`SaveChanges`，实体框架自动可确保所有所做的更改成功或它们都失败。</span><span class="sxs-lookup"><span data-stu-id="0beee-293">In scenarios where you make changes to multiple rows or tables and then call `SaveChanges`, the Entity Framework automatically makes sure that either all of your changes succeed or they all fail.</span></span> <span data-ttu-id="0beee-294">如果先完成一些更改，然后，将发生错误，则这些更改可能是自动回滚。</span><span class="sxs-lookup"><span data-stu-id="0beee-294">If some changes are done first and then an error happens, those changes are automatically rolled back.</span></span> <span data-ttu-id="0beee-295">有关方案，你需要更多的控制-例如，如果你想要包括在实体框架外部事务-中执行的操作，请参阅[事务](https://docs.microsoft.com/ef/core/saving/transactions)。</span><span class="sxs-lookup"><span data-stu-id="0beee-295">For scenarios where you need more control -- for example, if you want to include operations done outside of Entity Framework in a transaction -- see [Transactions](https://docs.microsoft.com/ef/core/saving/transactions).</span></span>

## <a name="no-tracking-queries"></a><span data-ttu-id="0beee-296">否跟踪查询</span><span class="sxs-lookup"><span data-stu-id="0beee-296">No-tracking queries</span></span>

<span data-ttu-id="0beee-297">当数据库上下文检索表行，并创建表示它们的实体对象时，默认情况下它将跟踪的内存中的实体是否同步，与数据库中。</span><span class="sxs-lookup"><span data-stu-id="0beee-297">When a database context retrieves table rows and creates entity objects that represent them, by default it keeps track of whether the entities in memory are in sync with what's in the database.</span></span> <span data-ttu-id="0beee-298">内存中的数据充当缓存，并更新实体时使用。</span><span class="sxs-lookup"><span data-stu-id="0beee-298">The data in memory acts as a cache and is used when you update an entity.</span></span> <span data-ttu-id="0beee-299">此缓存通常是不必要的 web 应用中由于上下文实例是否通常生存期较短 （新功能之一是创建和释放为每个请求） 和上下文读取再次使用该实体通常释放实体。</span><span class="sxs-lookup"><span data-stu-id="0beee-299">This caching is often unnecessary in a web application because context instances are typically short-lived (a new one is created and disposed for each request) and the context that reads an entity is typically disposed before that entity is used again.</span></span>

<span data-ttu-id="0beee-300">可以通过调用来禁用对实体对象在内存中的跟踪`AsNoTracking`方法。</span><span class="sxs-lookup"><span data-stu-id="0beee-300">You can disable tracking of entity objects in memory by calling the `AsNoTracking` method.</span></span> <span data-ttu-id="0beee-301">在其中你可能想要执行此操作的典型方案包括：</span><span class="sxs-lookup"><span data-stu-id="0beee-301">Typical scenarios in which you might want to do that include the following:</span></span>

* <span data-ttu-id="0beee-302">在上下文生存期内无需更新任何实体，并且您不必到 EF[自动加载与单独的查询所检索的实体的导航属性](read-related-data.md)。</span><span class="sxs-lookup"><span data-stu-id="0beee-302">During the context lifetime you don't need to update any entities, and you don't need EF to [automatically load navigation properties with  entities retrieved by separate queries](read-related-data.md).</span></span> <span data-ttu-id="0beee-303">频繁的控制器 HttpGet 操作方法中满足这些条件。</span><span class="sxs-lookup"><span data-stu-id="0beee-303">Frequently these conditions are met in a controller's HttpGet action methods.</span></span>

* <span data-ttu-id="0beee-304">正在运行的查询检索大量数据，并将更新仅返回的数据的一小部分。</span><span class="sxs-lookup"><span data-stu-id="0beee-304">You are running a query that retrieves a large volume of data, and only a small portion of the returned data will be updated.</span></span> <span data-ttu-id="0beee-305">它可能会更有效，为大型查询中，关闭跟踪并运行更高版本的很少需要更新的实体的查询。</span><span class="sxs-lookup"><span data-stu-id="0beee-305">It may be more efficient to turn off tracking for the large query, and run a query later for the few entities that need to be updated.</span></span>

* <span data-ttu-id="0beee-306">你想要将实体附加以便其进行更新，但出于其他目的检索相同的实体的更早版本。</span><span class="sxs-lookup"><span data-stu-id="0beee-306">You want to attach an entity in order to update it, but earlier you retrieved the same entity for a different purpose.</span></span> <span data-ttu-id="0beee-307">因为该实体已跟踪数据库上下文时，不能将附加你想要更改的实体。</span><span class="sxs-lookup"><span data-stu-id="0beee-307">Because the entity is already being tracked by the database context, you can't attach the entity that you want to change.</span></span> <span data-ttu-id="0beee-308">处理这种情况的一种方法是调用`AsNoTracking`上前面的查询。</span><span class="sxs-lookup"><span data-stu-id="0beee-308">One way to handle this situation is to call `AsNoTracking` on the earlier query.</span></span>

<span data-ttu-id="0beee-309">有关详细信息，请参阅[跟踪 vs。否跟踪](https://docs.microsoft.com/ef/core/querying/tracking)。</span><span class="sxs-lookup"><span data-stu-id="0beee-309">For more information, see [Tracking vs. No-Tracking](https://docs.microsoft.com/ef/core/querying/tracking).</span></span>

## <a name="summary"></a><span data-ttu-id="0beee-310">摘要</span><span class="sxs-lookup"><span data-stu-id="0beee-310">Summary</span></span>

<span data-ttu-id="0beee-311">你现在具有一组完整的执行学生实体的简单 CRUD 操作的页。</span><span class="sxs-lookup"><span data-stu-id="0beee-311">You now have a complete set of pages that perform simple CRUD operations for Student entities.</span></span> <span data-ttu-id="0beee-312">在下一步的教程，你将扩展的功能**索引**页上通过添加排序、 筛选和分页。</span><span class="sxs-lookup"><span data-stu-id="0beee-312">In the next tutorial you'll expand the functionality of the **Index** page by adding sorting, filtering, and paging.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="0beee-313">[上一页](intro.md)
[下一页](sort-filter-page.md)</span><span class="sxs-lookup"><span data-stu-id="0beee-313">[Previous](intro.md)
[Next](sort-filter-page.md)</span></span>  
