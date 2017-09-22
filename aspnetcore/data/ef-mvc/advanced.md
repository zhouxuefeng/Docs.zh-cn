---
title: "ASP.NET 核心 MVC 与 EF 核心-高级-10/10"
author: tdykstra
description: "本教程介绍了可需要注意的时要考虑的因素的基础知识的开发使用实体框架核心的 ASP.NET web 应用程序的几个主题。"
keywords: "ASP.NET 核心、 从实体框架核心，原始的 sql，检查 sql、 存储库模式、 单元的工作模式，自动更改检测现有数据库"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 92a2986a-d005-4ff6-9559-6657fd466bb7
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/advanced
ms.openlocfilehash: d63502a32e38eb192b40f21f5cd57d20048154e3
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="advanced-topics---ef-core-with-aspnet-core-mvc-tutorial-10-of-10"></a><span data-ttu-id="d074a-104">高级的主题的 EF 内核，它们有 ASP.NET 核心 MVC 教程 (10/10)</span><span class="sxs-lookup"><span data-stu-id="d074a-104">Advanced topics - EF Core with ASP.NET Core MVC tutorial (10 of 10)</span></span>

<span data-ttu-id="d074a-105">通过[Tom Dykstra](https://github.com/tdykstra)和[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d074a-105">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d074a-106">Contoso 大学示例 web 应用程序演示如何创建使用实体框架核心和 Visual Studio 的 ASP.NET 核心 MVC web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="d074a-106">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="d074a-107">有关教程系列的信息，请参阅[序列中的第一个教程](intro.md)。</span><span class="sxs-lookup"><span data-stu-id="d074a-107">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="d074a-108">在前面的教程，你实现的每个层次结构一个表继承。</span><span class="sxs-lookup"><span data-stu-id="d074a-108">In the previous tutorial, you implemented table-per-hierarchy inheritance.</span></span> <span data-ttu-id="d074a-109">本教程介绍了可需要注意的时要考虑的因素的基础知识的开发使用实体框架核心的 ASP.NET 核心 web 应用程序的几个主题。</span><span class="sxs-lookup"><span data-stu-id="d074a-109">This tutorial introduces several topics that are useful to be aware of when you go beyond the basics of developing ASP.NET Core web applications that use Entity Framework Core.</span></span>

## <a name="raw-sql-queries"></a><span data-ttu-id="d074a-110">原始的 SQL 查询</span><span class="sxs-lookup"><span data-stu-id="d074a-110">Raw SQL Queries</span></span>

<span data-ttu-id="d074a-111">使用实体框架的优点之一是它可避免将你过于仔细存储数据的特定方法的代码。</span><span class="sxs-lookup"><span data-stu-id="d074a-111">One of the advantages of using the Entity Framework is that it avoids tying your code too closely to a particular method of storing data.</span></span> <span data-ttu-id="d074a-112">此，它会生成 SQL 查询和命令，其中还使你无需自行编写。</span><span class="sxs-lookup"><span data-stu-id="d074a-112">It does this by generating SQL queries and commands for you, which also frees you from having to write them yourself.</span></span> <span data-ttu-id="d074a-113">但当你需要运行手动创建的特定 SQL 查询时有特殊情况。</span><span class="sxs-lookup"><span data-stu-id="d074a-113">But there are exceptional scenarios when you need to run specific SQL queries that you have manually created.</span></span> <span data-ttu-id="d074a-114">对于这些情况下，实体框架代码的第一个 API 包括使您能够 SQL 命令将直接传递到数据库的方法。</span><span class="sxs-lookup"><span data-stu-id="d074a-114">For these scenarios, the Entity Framework Code First API includes methods that enable you to pass SQL commands directly to the database.</span></span> <span data-ttu-id="d074a-115">在 EF 核心 1.0 中具有以下选项：</span><span class="sxs-lookup"><span data-stu-id="d074a-115">You have the following options in EF Core 1.0:</span></span>

* <span data-ttu-id="d074a-116">使用`DbSet.FromSql`返回实体类型的查询的方法。</span><span class="sxs-lookup"><span data-stu-id="d074a-116">Use the `DbSet.FromSql` method for queries that return entity types.</span></span> <span data-ttu-id="d074a-117">返回的对象必须是期望的类型的`DbSet`对象，并且它们是否自动跟踪数据库上下文中的除非你[关闭跟踪](crud.md#no-tracking-queries)。</span><span class="sxs-lookup"><span data-stu-id="d074a-117">The returned objects must be of the type expected by the `DbSet` object, and they are automatically tracked by the database context unless you [turn tracking off](crud.md#no-tracking-queries).</span></span>

* <span data-ttu-id="d074a-118">使用`Database.ExecuteSqlCommand`非查询命令。</span><span class="sxs-lookup"><span data-stu-id="d074a-118">Use the `Database.ExecuteSqlCommand` for non-query commands.</span></span>

<span data-ttu-id="d074a-119">如果你需要运行该查询将返回不是实体类型的你可以使用由 EF 提供的数据库连接中使用 ADO.NET。</span><span class="sxs-lookup"><span data-stu-id="d074a-119">If you need to run a query that returns types that aren't entities, you can use ADO.NET with the database connection provided by EF.</span></span> <span data-ttu-id="d074a-120">返回的数据不跟踪数据库上下文中，即使你使用此方法来检索实体类型。</span><span class="sxs-lookup"><span data-stu-id="d074a-120">The returned data isn't tracked by the database context, even if you use this method to retrieve entity types.</span></span>

<span data-ttu-id="d074a-121">因为时，将始终 true web 应用程序中执行 SQL 命令，你必须采取预防措施来保护你的站点对 SQL 注入式攻击。</span><span class="sxs-lookup"><span data-stu-id="d074a-121">As is always true when you execute SQL commands in a web application, you must take precautions to protect your site against SQL injection attacks.</span></span> <span data-ttu-id="d074a-122">若要这样做的一种方法是使用参数化的查询来确保由网页上提交的字符串，不能解释为 SQL 命令。</span><span class="sxs-lookup"><span data-stu-id="d074a-122">One way to do that is to use parameterized queries to make sure that strings submitted by a web page can't be interpreted as SQL commands.</span></span> <span data-ttu-id="d074a-123">在本教程中将用户输入集成到查询时，将使用参数化的查询。</span><span class="sxs-lookup"><span data-stu-id="d074a-123">In this tutorial you'll use parameterized queries when integrating user input into a query.</span></span>

## <a name="call-a-query-that-returns-entities"></a><span data-ttu-id="d074a-124">调用返回实体的查询</span><span class="sxs-lookup"><span data-stu-id="d074a-124">Call a query that returns entities</span></span>

<span data-ttu-id="d074a-125">`DbSet<TEntity>`类提供了可用于执行该查询将返回类型的实体的方法`TEntity`。</span><span class="sxs-lookup"><span data-stu-id="d074a-125">The `DbSet<TEntity>` class provides a method that you can use to execute a query that returns an entity of type `TEntity`.</span></span> <span data-ttu-id="d074a-126">若要查看你原理将更改中的代码`Details`部门控制器方法。</span><span class="sxs-lookup"><span data-stu-id="d074a-126">To see how this works you'll change the code in the `Details` method of the Department controller.</span></span>

<span data-ttu-id="d074a-127">在*DepartmentsController.cs*中`Details`方法，将检索一个部门有代码`FromSql`方法调用，如以下突出显示的代码中所示：</span><span class="sxs-lookup"><span data-stu-id="d074a-127">In *DepartmentsController.cs*, in the `Details` method, replace the code that retrieves a department with a `FromSql` method call, as shown in the following highlighted code:</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10,13)]

<span data-ttu-id="d074a-128">若要验证新代码是否工作正常，请选择**部门**选项卡，然后**详细信息**部门之一。</span><span class="sxs-lookup"><span data-stu-id="d074a-128">To verify that the new code works correctly, select the **Departments** tab and then **Details** for one of the departments.</span></span>

![部门详细信息](advanced/_static/department-details.png)

## <a name="call-a-query-that-returns-other-types"></a><span data-ttu-id="d074a-130">调用返回其他类型的查询</span><span class="sxs-lookup"><span data-stu-id="d074a-130">Call a query that returns other types</span></span>

<span data-ttu-id="d074a-131">前面你创建的每个注册日期显示学生的数量关于页面的学生统计信息网格。</span><span class="sxs-lookup"><span data-stu-id="d074a-131">Earlier you created a student statistics grid for the About page that showed the number of students for each enrollment date.</span></span> <span data-ttu-id="d074a-132">从学生实体集获取数据 (`_context.Students`) 和 LINQ 用于结果投影到的列表`EnrollmentDateGroup`查看模型对象。</span><span class="sxs-lookup"><span data-stu-id="d074a-132">You got the data from the Students entity set (`_context.Students`) and used LINQ to project the results into a list of `EnrollmentDateGroup` view model objects.</span></span> <span data-ttu-id="d074a-133">假设你想要本身而不使用 LINQ 编写 SQL。</span><span class="sxs-lookup"><span data-stu-id="d074a-133">Suppose you want to write the SQL itself rather than using LINQ.</span></span> <span data-ttu-id="d074a-134">需要运行 SQL 查询中返回实体对象之外的内容。</span><span class="sxs-lookup"><span data-stu-id="d074a-134">To do that you need to run a SQL query that returns something other than entity objects.</span></span> <span data-ttu-id="d074a-135">在 EF 核心 1.0 中，执行该操作的一种方法是编写 ADO.NET 代码，并从 EF 获取数据库连接。</span><span class="sxs-lookup"><span data-stu-id="d074a-135">In EF Core 1.0, one way to do that is write ADO.NET code and get the database connection from EF.</span></span>

<span data-ttu-id="d074a-136">在*HomeController.cs*，替换`About`方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="d074a-136">In *HomeController.cs*, replace the `About` method with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

<span data-ttu-id="d074a-137">添加 using 语句：</span><span class="sxs-lookup"><span data-stu-id="d074a-137">Add a using statement:</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

<span data-ttu-id="d074a-138">运行应用并转到关于页面。</span><span class="sxs-lookup"><span data-stu-id="d074a-138">Run the app and go to the About page.</span></span> <span data-ttu-id="d074a-139">它显示它以前的相同数据。</span><span class="sxs-lookup"><span data-stu-id="d074a-139">It displays the same data it did before.</span></span>

![有关页面](advanced/_static/about.png)

## <a name="call-an-update-query"></a><span data-ttu-id="d074a-141">调用更新查询</span><span class="sxs-lookup"><span data-stu-id="d074a-141">Call an update query</span></span>

<span data-ttu-id="d074a-142">假设 Contoso 大学管理员想要在数据库中，如更改的每个课程的信用额度数执行全局更改。</span><span class="sxs-lookup"><span data-stu-id="d074a-142">Suppose Contoso University administrators want to perform global changes in the database, such as changing the number of credits for every course.</span></span> <span data-ttu-id="d074a-143">如果该大学具有大量的课程，这会降低效率要检索其所有项作为实体并单独更改。</span><span class="sxs-lookup"><span data-stu-id="d074a-143">If the university has a large number of courses, it would be inefficient to retrieve them all as entities and change them individually.</span></span> <span data-ttu-id="d074a-144">在本部分中，你将实施一个网页，使用户能够指定要更改用于所有课程的信用额度次数因子和则将通过执行 SQL UPDATE 语句进行的更改。</span><span class="sxs-lookup"><span data-stu-id="d074a-144">In this section you'll implement a web page that enables the user to specify a factor by which to change the number of credits for all courses, and you'll make the change by executing a SQL UPDATE statement.</span></span> <span data-ttu-id="d074a-145">网页外观类似于下图：</span><span class="sxs-lookup"><span data-stu-id="d074a-145">The web page will look like the following illustration:</span></span>

![更新过程信用额度页面](advanced/_static/update-credits.png)

<span data-ttu-id="d074a-147">在*CoursesContoller.cs*，为 HttpGet 和 HttpPost 添加 UpdateCourseCredits 方法：</span><span class="sxs-lookup"><span data-stu-id="d074a-147">In *CoursesContoller.cs*, add UpdateCourseCredits methods for HttpGet and HttpPost:</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

<span data-ttu-id="d074a-148">当控制器处理 HttpGet 请求时，执行任何操作中返回`ViewData["RowsAffected"]`，并查看显示的空文本框和提交按钮，如在上图中所示。</span><span class="sxs-lookup"><span data-stu-id="d074a-148">When the controller processes an HttpGet request, nothing is returned in `ViewData["RowsAffected"]`, and the view displays an empty text box and a submit button, as shown in the preceding illustration.</span></span>

<span data-ttu-id="d074a-149">当**更新**单击按钮时，将调用 HttpPost 方法，且乘数已在文本框中输入的值。</span><span class="sxs-lookup"><span data-stu-id="d074a-149">When the **Update** button is clicked, the HttpPost method is called, and multiplier has the value entered in the text box.</span></span> <span data-ttu-id="d074a-150">然后执行的代码更新课程，并返回受影响的行数中的视图的 SQL `ViewData`。</span><span class="sxs-lookup"><span data-stu-id="d074a-150">The code then executes the SQL that updates courses and returns the number of affected rows to the view in `ViewData`.</span></span> <span data-ttu-id="d074a-151">当视图获取`RowsAffected`值，它将显示更新的行数。</span><span class="sxs-lookup"><span data-stu-id="d074a-151">When the view gets a `RowsAffected` value, it displays the number of rows updated.</span></span>

<span data-ttu-id="d074a-152">在**解决方案资源管理器**，右键单击*视图/课程*文件夹，，然后单击**添加 > 新项**。</span><span class="sxs-lookup"><span data-stu-id="d074a-152">In **Solution Explorer**, right-click the *Views/Courses* folder, and then click **Add > New Item**.</span></span>

<span data-ttu-id="d074a-153">在**添加新项**对话框中，单击**ASP.NET**下**已安装**在左窗格中，单击**MVC 视图页**，并命名新视图*UpdateCourseCredits.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="d074a-153">In the **Add New Item** dialog, click **ASP.NET** under **Installed** in the left pane, click **MVC View Page**, and name the new view *UpdateCourseCredits.cshtml*.</span></span>

<span data-ttu-id="d074a-154">在*Views/Courses/UpdateCourseCredits.cshtml*，模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="d074a-154">In *Views/Courses/UpdateCourseCredits.cshtml*, replace the template code with the following code:</span></span>

[!code-html[Main](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

<span data-ttu-id="d074a-155">运行`UpdateCourseCredits`方法通过选择**课程**选项卡上，然后添加"/ UpdateCourseCredits"到末尾的浏览器的地址栏中的 URL (例如： `http://localhost:5813/Courses/UpdateCourseCredits`)。</span><span class="sxs-lookup"><span data-stu-id="d074a-155">Run the `UpdateCourseCredits` method by selecting the **Courses** tab, then adding "/UpdateCourseCredits" to the end of the URL in the browser's address bar (for example: `http://localhost:5813/Courses/UpdateCourseCredits`).</span></span> <span data-ttu-id="d074a-156">在文本框中输入数字：</span><span class="sxs-lookup"><span data-stu-id="d074a-156">Enter a number in the text box:</span></span>

![更新过程信用额度页面](advanced/_static/update-credits.png)

<span data-ttu-id="d074a-158">单击“更新” 。</span><span class="sxs-lookup"><span data-stu-id="d074a-158">Click **Update**.</span></span> <span data-ttu-id="d074a-159">你看到受影响的行数：</span><span class="sxs-lookup"><span data-stu-id="d074a-159">You see the number of rows affected:</span></span>

![更新过程信用额度页上的受影响的行](advanced/_static/update-credits-rows-affected.png)

<span data-ttu-id="d074a-161">单击**回列表**若要查看的课程替换信用额度的修订号的列表。</span><span class="sxs-lookup"><span data-stu-id="d074a-161">Click **Back to List** to see the list of courses with the revised number of credits.</span></span>

<span data-ttu-id="d074a-162">请注意生产代码将确保始终更新导致有效的数据。</span><span class="sxs-lookup"><span data-stu-id="d074a-162">Note that production code would ensure that updates always result in valid data.</span></span> <span data-ttu-id="d074a-163">此处所示的简化的代码可以将信用额度足够导致数字大于 5 数相乘。</span><span class="sxs-lookup"><span data-stu-id="d074a-163">The simplified code shown here could multiply the number of credits enough to result in numbers greater than 5.</span></span> <span data-ttu-id="d074a-164">(`Credits`属性具有`[Range(0, 5)]`属性。)更新查询将起作用，但在假设的信用额度数为 5 或更少的系统其他部分，无效的数据可能会导致意外的结果。</span><span class="sxs-lookup"><span data-stu-id="d074a-164">(The `Credits` property has a `[Range(0, 5)]` attribute.) The update query would work but the invalid data could cause unexpected results in other parts of the system that assume the number of credits is 5 or less.</span></span>

<span data-ttu-id="d074a-165">有关原始的 SQL 查询的详细信息，请参阅[原始的 SQL 查询](https://docs.microsoft.com/ef/core/querying/raw-sql)。</span><span class="sxs-lookup"><span data-stu-id="d074a-165">For more information about raw SQL queries, see [Raw SQL Queries](https://docs.microsoft.com/ef/core/querying/raw-sql).</span></span>

## <a name="examine-sql-sent-to-the-database"></a><span data-ttu-id="d074a-166">检查发送到数据库的 SQL</span><span class="sxs-lookup"><span data-stu-id="d074a-166">Examine SQL sent to the database</span></span>

<span data-ttu-id="d074a-167">有时很有用，能够以查看发送到数据库的实际 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="d074a-167">Sometimes it's helpful to be able to see the actual SQL queries that are sent to the database.</span></span> <span data-ttu-id="d074a-168">EF 核心自动使用 ASP.NET Core 的内置日志记录功能来编写日志包含的 SQL 查询和更新。</span><span class="sxs-lookup"><span data-stu-id="d074a-168">Built-in logging functionality for ASP.NET Core is automatically used by EF Core to write logs that contain the SQL for queries and updates.</span></span> <span data-ttu-id="d074a-169">在本部分中，你将看到 SQL 日志记录的一些示例。</span><span class="sxs-lookup"><span data-stu-id="d074a-169">In this section you'll see some examples of SQL logging.</span></span>

<span data-ttu-id="d074a-170">打开*StudentsController.cs*并在`Details`方法上设置断点`if (student == null)`语句。</span><span class="sxs-lookup"><span data-stu-id="d074a-170">Open *StudentsController.cs* and in the `Details` method set a breakpoint on the `if (student == null)` statement.</span></span>

<span data-ttu-id="d074a-171">在调试模式下运行应用，并请转到详细信息页上的一名学生。</span><span class="sxs-lookup"><span data-stu-id="d074a-171">Run the app in debug mode, and go to the Details page for a student.</span></span>

<span data-ttu-id="d074a-172">转到**输出**显示调试窗口输出，并查看的查询：</span><span class="sxs-lookup"><span data-stu-id="d074a-172">Go to the **Output** window showing debug output, and you see the query:</span></span>

```
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (56ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT TOP(2) [s].[ID], [s].[Discriminator], [s].[FirstName], [s].[LastName], [s].[EnrollmentDate]
FROM [Person] AS [s]
WHERE ([s].[Discriminator] = N'Student') AND ([s].[ID] = @__id_0)
ORDER BY [s].[ID]
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (122ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT [s.Enrollments].[EnrollmentID], [s.Enrollments].[CourseID], [s.Enrollments].[Grade], [s.Enrollments].[StudentID], [e.Course].[CourseID], [e.Course].[Credits], [e.Course].[DepartmentID], [e.Course].[Title]
FROM [Enrollment] AS [s.Enrollments]
INNER JOIN [Course] AS [e.Course] ON [s.Enrollments].[CourseID] = [e.Course].[CourseID]
INNER JOIN (
    SELECT TOP(1) [s0].[ID]
    FROM [Person] AS [s0]
    WHERE ([s0].[Discriminator] = N'Student') AND ([s0].[ID] = @__id_0)
    ORDER BY [s0].[ID]
) AS [t] ON [s.Enrollments].[StudentID] = [t].[ID]
ORDER BY [t].[ID]
```

<span data-ttu-id="d074a-173">你会注意到一些操作可能惊讶你： SQL 选择最多为 2 的行 (`TOP(2)`) 从 Person 表。</span><span class="sxs-lookup"><span data-stu-id="d074a-173">You'll notice something here that might surprise you: the SQL selects up to 2 rows (`TOP(2)`) from the Person table.</span></span> <span data-ttu-id="d074a-174">`SingleOrDefaultAsync`方法未解析为服务器上的 1 行。</span><span class="sxs-lookup"><span data-stu-id="d074a-174">The `SingleOrDefaultAsync` method doesn't resolve to 1 row on the server.</span></span> <span data-ttu-id="d074a-175">原因是：</span><span class="sxs-lookup"><span data-stu-id="d074a-175">Here's why:</span></span>

* <span data-ttu-id="d074a-176">如果查询将返回多个行，该方法返回 null。</span><span class="sxs-lookup"><span data-stu-id="d074a-176">If the query would return multiple rows, the method returns null.</span></span>
* <span data-ttu-id="d074a-177">若要确定查询是否将返回多个行，EF 必须检查是否它返回至少为 2。</span><span class="sxs-lookup"><span data-stu-id="d074a-177">To determine whether the query would return multiple rows, EF has to check if it returns at least 2.</span></span>

<span data-ttu-id="d074a-178">请注意，你不必使用调试模式，并获取日志记录输出中的断点处停止**输出**窗口。</span><span class="sxs-lookup"><span data-stu-id="d074a-178">Note that you don't have to use debug mode and stop at a breakpoint to get logging output in the **Output** window.</span></span> <span data-ttu-id="d074a-179">它是只是停止时候你想要查看输出日志记录的简便方法。</span><span class="sxs-lookup"><span data-stu-id="d074a-179">It's just a convenient way to stop the logging at the point you want to look at the output.</span></span> <span data-ttu-id="d074a-180">如果你不这样做，日志记录仍然存在，并且必须能够向后滚动查找你感兴趣的部分。</span><span class="sxs-lookup"><span data-stu-id="d074a-180">If you don't do that, logging continues and you have to scroll back to find the parts you're interested in.</span></span>

## <a name="repository-and-unit-of-work-patterns"></a><span data-ttu-id="d074a-181">存储库和单元的工作模式</span><span class="sxs-lookup"><span data-stu-id="d074a-181">Repository and unit of work patterns</span></span>

<span data-ttu-id="d074a-182">许多开发人员编写代码以作为使用实体框架代码的周围的包装器实现的存储库和单元的工作模式。</span><span class="sxs-lookup"><span data-stu-id="d074a-182">Many developers write code to implement the repository and unit of work patterns as a wrapper around code that works with the Entity Framework.</span></span> <span data-ttu-id="d074a-183">这些模式用于创建数据访问层和应用程序的业务逻辑层之间的抽象层。</span><span class="sxs-lookup"><span data-stu-id="d074a-183">These patterns are intended to create an abstraction layer between the data access layer and the business logic layer of an application.</span></span> <span data-ttu-id="d074a-184">实现这些模式可帮助防止你的应用程序数据存储区中的更改，而且很容易自动的单元测试驱动开发 (TDD)。</span><span class="sxs-lookup"><span data-stu-id="d074a-184">Implementing these patterns can help insulate your application from changes in the data store and can facilitate automated unit testing or test-driven development (TDD).</span></span> <span data-ttu-id="d074a-185">但是，编写附加代码以实现这些模式并不总是使用 EF，有几个原因的应用程序的最佳选择：</span><span class="sxs-lookup"><span data-stu-id="d074a-185">However, writing additional code to implement these patterns is not always the best choice for applications that use EF, for several reasons:</span></span>

* <span data-ttu-id="d074a-186">EF 上下文类本身使应用商店数据特有的代码中的代码。</span><span class="sxs-lookup"><span data-stu-id="d074a-186">The EF context class itself insulates your code from data-store-specific code.</span></span>

* <span data-ttu-id="d074a-187">EF 上下文类可以充当数据库的工作单位类更新你不要使用 EF。</span><span class="sxs-lookup"><span data-stu-id="d074a-187">The EF context class can act as a unit-of-work class for database updates that you do using EF.</span></span>

* <span data-ttu-id="d074a-188">EF 包括用于实现 TDD 而无需编写存储库代码的功能。</span><span class="sxs-lookup"><span data-stu-id="d074a-188">EF includes features for implementing TDD without writing repository code.</span></span>

<span data-ttu-id="d074a-189">有关如何实现的存储库和单元的工作模式的信息，请参阅[本系列教程的实体框架 5 版本](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application)。</span><span class="sxs-lookup"><span data-stu-id="d074a-189">For information about how to implement the repository and unit of work patterns, see [the Entity Framework 5 version of this tutorial series](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span></span>

<span data-ttu-id="d074a-190">实体框架核心实现可用于测试的内存中数据库提供程序。</span><span class="sxs-lookup"><span data-stu-id="d074a-190">Entity Framework Core implements an in-memory database provider that can be used for testing.</span></span> <span data-ttu-id="d074a-191">有关详细信息，请参阅[测试以及 InMemory](https://docs.microsoft.com/ef/core/miscellaneous/testing/in-memory)。</span><span class="sxs-lookup"><span data-stu-id="d074a-191">For more information, see [Testing with InMemory](https://docs.microsoft.com/ef/core/miscellaneous/testing/in-memory).</span></span>

## <a name="automatic-change-detection"></a><span data-ttu-id="d074a-192">自动更改检测</span><span class="sxs-lookup"><span data-stu-id="d074a-192">Automatic change detection</span></span>

<span data-ttu-id="d074a-193">实体框架通过比较的实体的当前值与原始值确定更改实体的方式 （并因此需要发送到数据库的更新）。</span><span class="sxs-lookup"><span data-stu-id="d074a-193">The Entity Framework determines how an entity has changed (and therefore which updates need to be sent to the database) by comparing the current values of an entity with the original values.</span></span> <span data-ttu-id="d074a-194">查询或附加该实体时，存储的原始值。</span><span class="sxs-lookup"><span data-stu-id="d074a-194">The original values are stored when the entity is queried or attached.</span></span> <span data-ttu-id="d074a-195">某些会导致自动更改检测的方法如下所示：</span><span class="sxs-lookup"><span data-stu-id="d074a-195">Some of the methods that cause automatic change detection are the following:</span></span>

* <span data-ttu-id="d074a-196">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="d074a-196">DbContext.SaveChanges</span></span>

* <span data-ttu-id="d074a-197">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="d074a-197">DbContext.Entry</span></span>

* <span data-ttu-id="d074a-198">ChangeTracker.Entries</span><span class="sxs-lookup"><span data-stu-id="d074a-198">ChangeTracker.Entries</span></span>

<span data-ttu-id="d074a-199">如果您跟踪的大量实体，并且你调用这些方法之一多次在循环中，你可能会显著的性能改进，通过暂时关闭自动更改检测使用`ChangeTracker.AutoDetectChangesEnabled`属性。</span><span class="sxs-lookup"><span data-stu-id="d074a-199">If you're tracking a large number of entities and you call one of these methods many times in a loop, you might get significant performance improvements by temporarily turning off automatic change detection using the `ChangeTracker.AutoDetectChangesEnabled` property.</span></span> <span data-ttu-id="d074a-200">例如: </span><span class="sxs-lookup"><span data-stu-id="d074a-200">For example:</span></span>

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="entity-framework-core-source-code-and-development-plans"></a><span data-ttu-id="d074a-201">实体框架核心源代码和开发计划</span><span class="sxs-lookup"><span data-stu-id="d074a-201">Entity Framework Core source code and development plans</span></span>

<span data-ttu-id="d074a-202">实体框架核心的源代码位于[https://github.com/aspnet/EntityFrameworkCore](https://github.com/aspnet/EntityFrameworkCore)。</span><span class="sxs-lookup"><span data-stu-id="d074a-202">The source code for Entity Framework Core is available at [https://github.com/aspnet/EntityFrameworkCore](https://github.com/aspnet/EntityFrameworkCore).</span></span> <span data-ttu-id="d074a-203">除了源代码，可以获取每夜生成、 问题跟踪、 功能规范、 设计会议备忘录[在将来的开发路线图](https://github.com/aspnet/EntityFrameworkCore/wiki/Roadmap)，和的详细信息。</span><span class="sxs-lookup"><span data-stu-id="d074a-203">Besides source code, you can get nightly builds, issue tracking, feature specs, design meeting notes, [the roadmap for future development](https://github.com/aspnet/EntityFrameworkCore/wiki/Roadmap), and more.</span></span> <span data-ttu-id="d074a-204">你可以报告 bug，并可以提供你自己的 EF 源代码的增强功能。</span><span class="sxs-lookup"><span data-stu-id="d074a-204">You can file bugs, and you can contribute your own enhancements to the EF source code.</span></span>

<span data-ttu-id="d074a-205">尽管源代码处于打开状态，实体框架核心完全支持的 Microsoft 产品。</span><span class="sxs-lookup"><span data-stu-id="d074a-205">Although the source code is open, Entity Framework Core is fully supported as a Microsoft product.</span></span> <span data-ttu-id="d074a-206">Microsoft 实体框架团队将控制哪些接受的贡献保持和测试所有代码更改，以确保每个版本的质量。</span><span class="sxs-lookup"><span data-stu-id="d074a-206">The Microsoft Entity Framework team keeps control over which contributions are accepted and tests all code changes to ensure the quality of each release.</span></span>

## <a name="reverse-engineer-from-existing-database"></a><span data-ttu-id="d074a-207">从现有数据库反向工程</span><span class="sxs-lookup"><span data-stu-id="d074a-207">Reverse engineer from existing database</span></span>

<span data-ttu-id="d074a-208">若要反向工程数据模型，包括从现有数据库的实体类，使用[基架 dbcontext](https://docs.microsoft.com/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext)命令。</span><span class="sxs-lookup"><span data-stu-id="d074a-208">To reverse engineer a data model including entity classes from an existing database, use the [scaffold-dbcontext](https://docs.microsoft.com/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) command.</span></span> <span data-ttu-id="d074a-209">请参阅[入门教程](https://docs.microsoft.com/ef/core/get-started/aspnetcore/existing-db)。</span><span class="sxs-lookup"><span data-stu-id="d074a-209">See the [getting-started tutorial](https://docs.microsoft.com/ef/core/get-started/aspnetcore/existing-db).</span></span>

<a id="dynamic-linq"></a>
## <a name="use-dynamic-linq-to-simplify-sort-selection-code"></a><span data-ttu-id="d074a-210">使用动态 LINQ 来简化排序所选内容的代码</span><span class="sxs-lookup"><span data-stu-id="d074a-210">Use dynamic LINQ to simplify sort selection code</span></span>

<span data-ttu-id="d074a-211">[本系列第三个教程](sort-filter-page.md)演示如何通过硬编码中的列名称来编写 LINQ 代码`switch`语句。</span><span class="sxs-lookup"><span data-stu-id="d074a-211">The [third tutorial in this series](sort-filter-page.md) shows how to write LINQ code by hard-coding column names in a `switch` statement.</span></span> <span data-ttu-id="d074a-212">具有可供选择的两列，这可正常使用，但如果你有多列的代码无法获得详细。</span><span class="sxs-lookup"><span data-stu-id="d074a-212">With two columns to choose from, this works fine, but if you have many columns the code could get verbose.</span></span> <span data-ttu-id="d074a-213">若要解决该问题，可以使用`EF.Property`方法，以字符串形式指定属性的名称。</span><span class="sxs-lookup"><span data-stu-id="d074a-213">To solve that problem, you can use the `EF.Property` method to specify the name of the property as a string.</span></span> <span data-ttu-id="d074a-214">若要尝试这种方法，替换`Index`中的方法`StudentsController`替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="d074a-214">To try out this approach, replace the `Index` method in the `StudentsController` with the following code.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="next-steps"></a><span data-ttu-id="d074a-215">后续步骤</span><span class="sxs-lookup"><span data-stu-id="d074a-215">Next steps</span></span>

<span data-ttu-id="d074a-216">这将完成这一系列的 ASP.NET MVC 应用程序中使用实体框架核心的教程。</span><span class="sxs-lookup"><span data-stu-id="d074a-216">This completes this series of tutorials on using the Entity Framework Core in an ASP.NET MVC application.</span></span>

<span data-ttu-id="d074a-217">有关 EF 核心的详细信息，请参阅[实体框架的核心文档](https://docs.microsoft.com/ef/core)。</span><span class="sxs-lookup"><span data-stu-id="d074a-217">For more information about EF Core, see the [Entity Framework Core documentation](https://docs.microsoft.com/ef/core).</span></span> <span data-ttu-id="d074a-218">一本书中也有：[中操作的实体框架内核](https://www.manning.com/books/entity-framework-core-in-action)。</span><span class="sxs-lookup"><span data-stu-id="d074a-218">A book is also available: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action).</span></span>

<span data-ttu-id="d074a-219">有关如何部署 web 应用程序，你已生成后的信息，请参阅[发布和部署](../../publishing/index.md)。</span><span class="sxs-lookup"><span data-stu-id="d074a-219">For information about how to deploy your web application after you've built it, see [Publishing and deployment](../../publishing/index.md).</span></span>

<span data-ttu-id="d074a-220">有关其他身份验证和授权，如与 ASP.NET 核心 MVC 相关的主题信息请参阅[ASP.NET 核心文档](https://docs.microsoft.com/aspnet/core/)。</span><span class="sxs-lookup"><span data-stu-id="d074a-220">For information about other topics related to ASP.NET Core MVC, such as authentication and authorization, see the [ASP.NET Core documentation](https://docs.microsoft.com/aspnet/core/).</span></span>

## <a name="acknowledgments"></a><span data-ttu-id="d074a-221">确认</span><span class="sxs-lookup"><span data-stu-id="d074a-221">Acknowledgments</span></span>

<span data-ttu-id="d074a-222">Tom Dykstra 和 Rick Anderson (twitter @RickAndMSFT) 编写本教程。</span><span class="sxs-lookup"><span data-stu-id="d074a-222">Tom Dykstra and Rick Anderson (twitter @RickAndMSFT) wrote this tutorial.</span></span> <span data-ttu-id="d074a-223">Rowan Miller、 迪 Vega 和实体框架团队其他成员的辅助与代码评审和帮助调试问题所引起时我们已为教程编写代码。</span><span class="sxs-lookup"><span data-stu-id="d074a-223">Rowan Miller, Diego Vega, and other members of the Entity Framework team assisted with code reviews and helped debug issues that arose while we were writing code for the tutorials.</span></span>

## <a name="common-errors"></a><span data-ttu-id="d074a-224">常见错误</span><span class="sxs-lookup"><span data-stu-id="d074a-224">Common errors</span></span>  

### <a name="contosouniversitydll-used-by-another-process"></a><span data-ttu-id="d074a-225">另一个进程使用的 ContosoUniversity.dll</span><span class="sxs-lookup"><span data-stu-id="d074a-225">ContosoUniversity.dll used by another process</span></span>

<span data-ttu-id="d074a-226">错误消息：</span><span class="sxs-lookup"><span data-stu-id="d074a-226">Error message:</span></span>

> <span data-ttu-id="d074a-227">无法打开...bin\Debug\netcoreapp1.0\ContosoUniversity.dll 进行写入-进程无法访问文件 '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll 因为另一个进程正在使用。</span><span class="sxs-lookup"><span data-stu-id="d074a-227">Cannot open '...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' for writing -- 'The process cannot access the file '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' because it is being used by another process.</span></span>

<span data-ttu-id="d074a-228">解决方案：</span><span class="sxs-lookup"><span data-stu-id="d074a-228">Solution:</span></span>

<span data-ttu-id="d074a-229">停止 IIS Express 中的站点。</span><span class="sxs-lookup"><span data-stu-id="d074a-229">Stop the site in IIS Express.</span></span> <span data-ttu-id="d074a-230">请转到 Windows 系统任务栏中，查找 IIS Express 并右键单击图标将其、 选择 Contoso 大学站点中，，然后单击**停止站点**。</span><span class="sxs-lookup"><span data-stu-id="d074a-230">Go to the Windows System Tray, find IIS Express and right-click its icon, select the Contoso University site, and then click **Stop Site**.</span></span>

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a><span data-ttu-id="d074a-231">使用向上和向下方法中的任何代码基架的迁移</span><span class="sxs-lookup"><span data-stu-id="d074a-231">Migration scaffolded with no code in Up and Down methods</span></span>

<span data-ttu-id="d074a-232">可能的原因：</span><span class="sxs-lookup"><span data-stu-id="d074a-232">Possible cause:</span></span>

<span data-ttu-id="d074a-233">EF CLI 命令不会自动关闭并保存代码文件。</span><span class="sxs-lookup"><span data-stu-id="d074a-233">The EF CLI commands don't automatically close and save code files.</span></span> <span data-ttu-id="d074a-234">如果在运行时，你有未保存更改`migrations add`命令时，EF 将找不到所做的更改。</span><span class="sxs-lookup"><span data-stu-id="d074a-234">If you have unsaved changes when you run the `migrations add` command, EF won't find your changes.</span></span>

<span data-ttu-id="d074a-235">解决方案：</span><span class="sxs-lookup"><span data-stu-id="d074a-235">Solution:</span></span>

<span data-ttu-id="d074a-236">运行`migrations remove`命令，保存你的代码更改并重新运行`migrations add`命令。</span><span class="sxs-lookup"><span data-stu-id="d074a-236">Run the `migrations remove` command, save your code changes and rerun the `migrations add` command.</span></span>

### <a name="errors-while-running-database-update"></a><span data-ttu-id="d074a-237">正在运行的数据库更新时出错</span><span class="sxs-lookup"><span data-stu-id="d074a-237">Errors while running database update</span></span>

<span data-ttu-id="d074a-238">就可以在具有现有数据的数据库中进行架构更改时，发生其他错误。</span><span class="sxs-lookup"><span data-stu-id="d074a-238">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="d074a-239">如果您无法解析的迁移错误，你可以更改连接字符串中的数据库名称，或删除数据库。</span><span class="sxs-lookup"><span data-stu-id="d074a-239">If you get migration errors you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="d074a-240">与新数据库，若要迁移，没有数据并更新数据库命令更有望完成且未发生错误。</span><span class="sxs-lookup"><span data-stu-id="d074a-240">With a new database, there is no data to migrate, and the update-database command is much more likely to complete without errors.</span></span>

<span data-ttu-id="d074a-241">最简单方法是在数据库重命名*appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="d074a-241">The simplest approach is to rename the database in *appsettings.json*.</span></span> <span data-ttu-id="d074a-242">下次运行`database update`，将创建一个新数据库。</span><span class="sxs-lookup"><span data-stu-id="d074a-242">The next time you run `database update`, a new database will be created.</span></span>

<span data-ttu-id="d074a-243">若要删除的数据库在 SSOX 中，右键单击数据库，单击**删除**，然后在**删除数据库**对话框框中，选择**关闭现有连接**单击**确定**。</span><span class="sxs-lookup"><span data-stu-id="d074a-243">To delete a database in SSOX, right-click the database, click **Delete**, and then in the **Delete Database** dialog box select **Close existing connections** and click **OK**.</span></span>

<span data-ttu-id="d074a-244">若要使用 CLI 删除数据库，运行`database drop`CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="d074a-244">To delete a database by using the CLI, run the `database drop` CLI command:</span></span>

```console
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a><span data-ttu-id="d074a-245">错误找到 SQL Server 实例</span><span class="sxs-lookup"><span data-stu-id="d074a-245">Error locating SQL Server instance</span></span>

<span data-ttu-id="d074a-246">错误消息：</span><span class="sxs-lookup"><span data-stu-id="d074a-246">Error Message:</span></span>

> <span data-ttu-id="d074a-247">建立与 SQL Server 的连接时发生与网络相关或特定于实例的错误。</span><span class="sxs-lookup"><span data-stu-id="d074a-247">A network-related or instance-specific error occurred while establishing a connection to SQL Server.</span></span> <span data-ttu-id="d074a-248">未找到或无法访问服务器。</span><span class="sxs-lookup"><span data-stu-id="d074a-248">The server was not found or was not accessible.</span></span> <span data-ttu-id="d074a-249">请验证实例名称是否正确，SQL Server 是否已配置为允许远程连接。</span><span class="sxs-lookup"><span data-stu-id="d074a-249">Verify that the instance name is correct and that SQL Server is configured to allow remote connections.</span></span> <span data-ttu-id="d074a-250">(提供程序： SQL 网络接口，错误： 26-错误查找服务器/实例指定)</span><span class="sxs-lookup"><span data-stu-id="d074a-250">(provider: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)</span></span>

<span data-ttu-id="d074a-251">解决方案：</span><span class="sxs-lookup"><span data-stu-id="d074a-251">Solution:</span></span>

<span data-ttu-id="d074a-252">请检查连接字符串。</span><span class="sxs-lookup"><span data-stu-id="d074a-252">Check the connection string.</span></span> <span data-ttu-id="d074a-253">如果你已手动删除数据库文件，更改要从头开始使用新的数据库的构造字符串中数据库的名称。</span><span class="sxs-lookup"><span data-stu-id="d074a-253">If you have manually deleted the database file, change the name of the database in the construction string to start over with a new database.</span></span>

>[!div class="step-by-step"]
[<span data-ttu-id="d074a-254">上一篇</span><span class="sxs-lookup"><span data-stu-id="d074a-254">Previous</span></span>](inheritance.md)
