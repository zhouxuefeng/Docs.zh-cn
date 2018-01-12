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
ms.openlocfilehash: 4c20ed37e1e54273929593dddc9fe1180f1492d6
ms.sourcegitcommit: 12e5194936b7e820efc5505a2d5d4f84e88eb5ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2018
---
# <a name="advanced-topics---ef-core-with-aspnet-core-mvc-tutorial-10-of-10"></a>高级的主题的 EF 内核，它们有 ASP.NET 核心 MVC 教程 (10/10)

通过[Tom Dykstra](https://github.com/tdykstra)和[Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso 大学示例 web 应用程序演示如何创建使用实体框架核心和 Visual Studio 的 ASP.NET 核心 MVC web 应用程序。 有关教程系列的信息，请参阅[序列中的第一个教程](intro.md)。

在前面的教程，你实现的每个层次结构一个表继承。 本教程介绍了可需要注意的时要考虑的因素的基础知识的开发使用实体框架核心的 ASP.NET 核心 web 应用程序的几个主题。

## <a name="raw-sql-queries"></a>原始的 SQL 查询

使用实体框架的优点之一是它可避免将你过于仔细存储数据的特定方法的代码。 此，它会生成 SQL 查询和命令，其中还使你无需自行编写。 但当你需要运行手动创建的特定 SQL 查询时有特殊情况。 对于这些情况下，实体框架代码的第一个 API 包括使您能够 SQL 命令将直接传递到数据库的方法。 在 EF 核心 1.0 中具有以下选项：

* 使用`DbSet.FromSql`返回实体类型的查询的方法。 返回的对象必须是期望的类型的`DbSet`对象，并且它们是否自动跟踪数据库上下文中的除非你[关闭跟踪](crud.md#no-tracking-queries)。

* 使用`Database.ExecuteSqlCommand`非查询命令。

如果你需要运行该查询将返回不是实体类型的你可以使用由 EF 提供的数据库连接中使用 ADO.NET。 返回的数据不跟踪数据库上下文中，即使你使用此方法来检索实体类型。

因为时，将始终 true web 应用程序中执行 SQL 命令，你必须采取预防措施来保护你的站点对 SQL 注入式攻击。 若要这样做的一种方法是使用参数化的查询来确保由网页上提交的字符串，不能解释为 SQL 命令。 在本教程中将用户输入集成到查询时，将使用参数化的查询。

## <a name="call-a-query-that-returns-entities"></a>调用返回实体的查询

`DbSet<TEntity>`类提供了可用于执行该查询将返回类型的实体的方法`TEntity`。 若要查看你原理将更改中的代码`Details`部门控制器方法。

在*DepartmentsController.cs*中`Details`方法，将检索一个部门有代码`FromSql`方法调用，如以下突出显示的代码中所示：

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10,13)]

若要验证新代码是否工作正常，请选择**部门**选项卡，然后**详细信息**部门之一。

![部门详细信息](advanced/_static/department-details.png)

## <a name="call-a-query-that-returns-other-types"></a>调用返回其他类型的查询

前面你创建的每个注册日期显示学生的数量关于页面的学生统计信息网格。 从学生实体集获取数据 (`_context.Students`) 和 LINQ 用于结果投影到的列表`EnrollmentDateGroup`查看模型对象。 假设你想要本身而不使用 LINQ 编写 SQL。 需要运行 SQL 查询中返回实体对象之外的内容。 在 EF 核心 1.0 中，执行该操作的一种方法是编写 ADO.NET 代码，并从 EF 获取数据库连接。

在*HomeController.cs*，替换`About`方法替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

添加 using 语句：

[!code-csharp[Main](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

运行应用并转到关于页面。 它显示它以前的相同数据。

![有关页面](advanced/_static/about.png)

## <a name="call-an-update-query"></a>调用更新查询

假设 Contoso 大学管理员想要在数据库中，如更改的每个课程的信用额度数执行全局更改。 如果该大学具有大量的课程，这会降低效率要检索其所有项作为实体并单独更改。 在本部分中，你将实施一个网页，使用户能够指定要更改用于所有课程的信用额度次数因子和则将通过执行 SQL UPDATE 语句进行的更改。 网页外观类似于下图：

![更新过程信用额度页面](advanced/_static/update-credits.png)

在*CoursesContoller.cs*，为 HttpGet 和 HttpPost 添加 UpdateCourseCredits 方法：

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

当控制器处理 HttpGet 请求时，执行任何操作中返回`ViewData["RowsAffected"]`，并查看显示的空文本框和提交按钮，如在上图中所示。

当**更新**单击按钮时，将调用 HttpPost 方法，且乘数已在文本框中输入的值。 然后执行的代码更新课程，并返回受影响的行数中的视图的 SQL `ViewData`。 当视图获取`RowsAffected`值，它将显示更新的行数。

在**解决方案资源管理器**，右键单击*视图/课程*文件夹，，然后单击**添加 > 新项**。

在**添加新项**对话框中，单击**ASP.NET**下**已安装**在左窗格中，单击**MVC 视图页**，并命名新视图*UpdateCourseCredits.cshtml*。

在*Views/Courses/UpdateCourseCredits.cshtml*，模板代码替换为以下代码：

[!code-html[Main](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

运行`UpdateCourseCredits`方法通过选择**课程**选项卡上，然后添加"/ UpdateCourseCredits"到末尾的浏览器的地址栏中的 URL (例如： `http://localhost:5813/Courses/UpdateCourseCredits`)。 在文本框中输入数字：

![更新过程信用额度页面](advanced/_static/update-credits.png)

单击“更新” 。 你看到受影响的行数：

![更新过程信用额度页上的受影响的行](advanced/_static/update-credits-rows-affected.png)

单击**回列表**若要查看的课程替换信用额度的修订号的列表。

请注意生产代码将确保始终更新导致有效的数据。 此处所示的简化的代码可以将信用额度足够导致数字大于 5 数相乘。 (`Credits`属性具有`[Range(0, 5)]`属性。)更新查询将起作用，但在假设的信用额度数为 5 或更少的系统其他部分，无效的数据可能会导致意外的结果。

有关原始的 SQL 查询的详细信息，请参阅[原始的 SQL 查询](https://docs.microsoft.com/ef/core/querying/raw-sql)。

## <a name="examine-sql-sent-to-the-database"></a>检查发送到数据库的 SQL

有时很有用，能够以查看发送到数据库的实际 SQL 查询。 EF 核心自动使用 ASP.NET Core 的内置日志记录功能来编写日志包含的 SQL 查询和更新。 在本部分中，你将看到 SQL 日志记录的一些示例。

打开*StudentsController.cs*并在`Details`方法上设置断点`if (student == null)`语句。

在调试模式下运行应用，并请转到详细信息页上的一名学生。

转到**输出**显示调试窗口输出，并查看的查询：

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

你会注意到一些操作可能惊讶你： SQL 选择最多为 2 的行 (`TOP(2)`) 从 Person 表。 `SingleOrDefaultAsync`方法未解析为服务器上的 1 行。 原因是：

* 如果查询将返回多个行，该方法返回 null。
* 若要确定查询是否将返回多个行，EF 必须检查是否它返回至少为 2。

请注意，你不必使用调试模式，并获取日志记录输出中的断点处停止**输出**窗口。 它是只是停止时候你想要查看输出日志记录的简便方法。 如果你不这样做，日志记录仍然存在，并且必须能够向后滚动查找你感兴趣的部分。

## <a name="repository-and-unit-of-work-patterns"></a>存储库和单元的工作模式

许多开发人员编写代码以作为使用实体框架代码的周围的包装器实现的存储库和单元的工作模式。 这些模式用于创建数据访问层和应用程序的业务逻辑层之间的抽象层。 实现这些模式可帮助防止你的应用程序数据存储区中的更改，而且很容易自动的单元测试驱动开发 (TDD)。 但是，编写附加代码以实现这些模式并不总是使用 EF，有几个原因的应用程序的最佳选择：

* EF 上下文类本身使应用商店数据特有的代码中的代码。

* EF 上下文类可以充当数据库的工作单位类更新你不要使用 EF。

* EF 包括用于实现 TDD 而无需编写存储库代码的功能。

有关如何实现的存储库和单元的工作模式的信息，请参阅[本系列教程的实体框架 5 版本](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application)。

实体框架核心实现可用于测试的内存中数据库提供程序。 有关详细信息，请参阅[测试以及 InMemory](https://docs.microsoft.com/ef/core/miscellaneous/testing/in-memory)。

## <a name="automatic-change-detection"></a>自动更改检测

实体框架通过比较的实体的当前值与原始值确定更改实体的方式 （并因此需要发送到数据库的更新）。 查询或附加该实体时，存储的原始值。 某些会导致自动更改检测的方法如下所示：

* DbContext.SaveChanges

* DbContext.Entry

* ChangeTracker.Entries

如果您跟踪的大量实体，并且你调用这些方法之一多次在循环中，你可能会显著的性能改进，通过暂时关闭自动更改检测使用`ChangeTracker.AutoDetectChangesEnabled`属性。 例如:

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="entity-framework-core-source-code-and-development-plans"></a>实体框架核心源代码和开发计划

实体框架核心的源代码位于[https://github.com/aspnet/EntityFrameworkCore](https://github.com/aspnet/EntityFrameworkCore)。 除了源代码，可以获取每夜生成、 问题跟踪、 功能规范、 设计会议备忘录[在将来的开发路线图](https://github.com/aspnet/EntityFrameworkCore/wiki/Roadmap)，和的详细信息。 你可以报告 bug，并可以提供你自己的 EF 源代码的增强功能。

尽管源代码处于打开状态，实体框架核心完全支持的 Microsoft 产品。 Microsoft 实体框架团队将控制哪些接受的贡献保持和测试所有代码更改，以确保每个版本的质量。

## <a name="reverse-engineer-from-existing-database"></a>从现有数据库反向工程

若要反向工程数据模型，包括从现有数据库的实体类，使用[基架 dbcontext](https://docs.microsoft.com/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext)命令。 请参阅[入门教程](https://docs.microsoft.com/ef/core/get-started/aspnetcore/existing-db)。

<a id="dynamic-linq"></a>
## <a name="use-dynamic-linq-to-simplify-sort-selection-code"></a>使用动态 LINQ 来简化排序所选内容的代码

[本系列第三个教程](sort-filter-page.md)演示如何通过硬编码中的列名称来编写 LINQ 代码`switch`语句。 具有可供选择的两列，这可正常使用，但如果你有多列的代码无法获得详细。 若要解决该问题，可以使用`EF.Property`方法，以字符串形式指定属性的名称。 若要尝试这种方法，替换`Index`中的方法`StudentsController`替换为以下代码。

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="next-steps"></a>后续步骤

这将完成这一系列的 ASP.NET MVC 应用程序中使用实体框架核心的教程。

有关 EF 核心的详细信息，请参阅[实体框架的核心文档](https://docs.microsoft.com/ef/core)。 一本书中也有：[中操作的实体框架内核](https://www.manning.com/books/entity-framework-core-in-action)。

有关如何部署 web 应用的信息，请参阅[主机并将其部署](xref:host-and-deploy/index)。

有关其他身份验证和授权，如与 ASP.NET 核心 MVC 相关的主题信息请参阅[ASP.NET 核心文档](https://docs.microsoft.com/aspnet/core/)。

## <a name="acknowledgments"></a>确认

Tom Dykstra 和 Rick Anderson (twitter @RickAndMSFT) 编写本教程。 Rowan Miller、 迪 Vega 和实体框架团队其他成员的辅助与代码评审和帮助调试问题所引起时我们已为教程编写代码。

## <a name="common-errors"></a>常见错误  

### <a name="contosouniversitydll-used-by-another-process"></a>另一个进程使用的 ContosoUniversity.dll

错误消息：

> 无法打开...bin\Debug\netcoreapp1.0\ContosoUniversity.dll 进行写入-进程无法访问文件 '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll 因为另一个进程正在使用。

解决方案：

停止 IIS Express 中的站点。 请转到 Windows 系统任务栏中，查找 IIS Express 并右键单击图标将其、 选择 Contoso 大学站点中，，然后单击**停止站点**。

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a>使用向上和向下方法中的任何代码基架的迁移

可能的原因：

EF CLI 命令不会自动关闭并保存代码文件。 如果在运行时，你有未保存更改`migrations add`命令时，EF 将找不到所做的更改。

解决方案：

运行`migrations remove`命令，保存你的代码更改并重新运行`migrations add`命令。

### <a name="errors-while-running-database-update"></a>正在运行的数据库更新时出错

就可以在具有现有数据的数据库中进行架构更改时，发生其他错误。 如果您无法解析的迁移错误，你可以更改连接字符串中的数据库名称，或删除数据库。 与新数据库，若要迁移，没有数据并更新数据库命令更有望完成且未发生错误。

最简单方法是在数据库重命名*appsettings.json*。 下次运行`database update`，将创建一个新数据库。

若要删除的数据库在 SSOX 中，右键单击数据库，单击**删除**，然后在**删除数据库**对话框框中，选择**关闭现有连接**单击**确定**。

若要使用 CLI 删除数据库，运行`database drop`CLI 命令：

```console
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a>错误找到 SQL Server 实例

错误消息：

> 建立与 SQL Server 的连接时发生与网络相关或特定于实例的错误。 未找到或无法访问服务器。 请验证实例名称是否正确，SQL Server 是否已配置为允许远程连接。 (提供程序： SQL 网络接口，错误： 26-错误查找服务器/实例指定)

解决方案：

请检查连接字符串。 如果你已手动删除数据库文件，更改要从头开始使用新的数据库的构造字符串中数据库的名称。

>[!div class="step-by-step"]
[上一篇](inheritance.md)
