---
title: "包含实体框架核心技术-8 的教程 1 razor 页面"
author: rick-anderson
description: "演示如何创建使用实体框架核心的 Razor 页面应用程序"
keywords: "ASP.NET 核心，实体框架核心，教程"
ms.author: riande
manager: wpickett
ms.date: 11/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-rp/intro
ms.openlocfilehash: 571d683636244565b184cfec49061ec656377f11
ms.sourcegitcommit: 198fb0488e961048bfa376cf58cb853ef1d1cb91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2017
---
# <a name="getting-started-with-razor-pages-and-entity-framework-core-using-visual-studio-1-of-8"></a>开始使用 Razor 页和使用 Visual Studio (第 1 个 8) 的实体框架核心

通过[Tom Dykstra](https://github.com/tdykstra)和[Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso 大学示例 web 应用程序演示如何创建使用 Entity Framework (EF) 核心 2.0 和 Visual Studio 2017 ASP.NET 核心 2.0 MVC web 应用程序。

该示例应用是一个用于 fictional Contoso 大学网站。 它包括诸如学生许可、 过程创建和教师分配等功能。 此页是一系列教程说明如何生成 Contoso 大学示例应用程序中的第一个。

[下载或查看已完成的应用程序。](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [下载说明](xref:tutorials/index#how-to-download-a-sample)。

## <a name="prerequisites"></a>先决条件

[!INCLUDE[install 2.0](../../includes/install2.0.md)]

## <a name="troubleshooting"></a>疑难解答

如果你遇到无法解决的问题，通常可以通过比较你的代码查找解决方案[完成阶段](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots)或[已完成的项目](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu-final)。 有关常见错误以及如何解决这些列表，请参阅[最后一个教程系列中的故障排除部分](xref:data/ef-mvc/advanced#common-errors)。 如果没有找到你那里需要你可以将问题发布到为 StackOverflow.com [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core)或[EF 核心](https://stackoverflow.com/questions/tagged/entity-framework-core)。

> [!TIP]
> 这一系列教程的基础上早期教程中执行哪些操作。 请考虑在每个成功的教程完成后保存项目的副本。 如果遇到问题时，便可以通过从以前的教程，而不是追溯到开头。 或者，你可以下载[完成阶段](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots)并重新使用已完成的阶段开始。

## <a name="the-contoso-university-web-app"></a>Contoso 大学 web 应用

这些教程中生成该应用是基本大学网站。

用户可以查看和更新学生、 课程中和教师信息。 以下是几个在本教程创建的屏幕。

![学生索引页](intro/_static/students-index.png)

![学生编辑页](intro/_static/student-edit.png)

什么由内置模板生成即将此站点的用户界面样式。 教程的重点是 EF 核心具有 Razor 页面，ui 部分除外。

## <a name="create-a-razor-pages-web-app"></a>创建 Razor 页的 web 应用

* 从 Visual Studio“文件”菜单中选择“新建” > “项目”。
* 创建新的 ASP.NET Core Web 应用程序。 将项目**ContosoUniversity**。 务必要将项目*ContosoUniversity*因此命名空间与匹配时代码复制/粘贴。
 ![新建 ASP.NET Core Web 应用程序](intro/_static/np.png)
* 在下拉列表中选择“ASP.NET Core 2.0”，然后选择“Web 应用程序”。
 ![Web 应用程序（Razor 页面）](../../mvc/razor-pages/index/_static/np2.png)

按 F5 在调试模式下运行应用，或按 Ctrl-F5 在运行（不附加调试器）

## <a name="set-up-the-site-style"></a>设置站点样式

少量的更改将设置为站点菜单、 布局和主页。

打开*Pages/_Layout.cshtml*并进行以下更改：

* 将每个匹配项的"ContosoUniversity"更改为"Contoso 大学。" 有三个匹配项。

* 添加菜单项**学生**，**课程**，**教师**，和**部门**，并删除**联系人**菜单项。

突出显示所做的更改。

[!code-html[](intro/samples/cu/Pages/_Layout.cshtml?highlight=6,29,35-38,47&range=1-50)]

在*Pages/Index.cshtml*，将文件的内容替换为以下代码以将有关 ASP.NET 和 MVC 的文本替换为此应用相关的文本：

[!code-html[](intro/samples/cu/Pages/Index.cshtml)]

按 Ctrl+F5 运行项目。 在以下教程中创建的选项卡显示主页：

![Contoso 大学主页](intro/_static/home-page.png)

## <a name="create-the-data-model"></a>创建数据模型

创建 Contoso 大学应用的实体类。 使用以下三个实体启动：

![课程注册学生数据模型关系图](intro/_static/data-model-diagram.png)

之间的一对多关系`Student`和`Enrollment`实体。 之间的一对多关系`Course`和`Enrollment`实体。 一名学生可以在任意数量的课程中注册。 课程可以有任意数量的学生在其中注册。

在以下部分中，创建为这些实体的每个类。

### <a name="the-student-entity"></a>学生实体

![学生实体关系图](intro/_static/student-entity.png)

创建*模型*文件夹。 在*模型*文件夹中，创建一个名为的类文件*Student.cs*替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

`ID`属性变为对应于此类数据库 (DB) 表的主键列。 默认情况下，EF 核心将解释一个属性，名为`ID`或`classnameID`为主键。

`Enrollments`属性是导航属性。 导航属性链接到与此实体相关的其他实体。 在这种情况下，`Enrollments`属性`Student entity`包含的所有`Enrollment`与该订阅相关的实体`Student`。 例如，如果数据库中的学生行有两个相关注册行`Enrollments`导航属性包含这两个`Enrollment`实体。 相关`Enrollment`行是包含该学生的主键值中的行`StudentID`列。 例如，假设学生 id = 1 有两个行`Enrollment`表。 `Enrollment`表包含两个行`StudentID`= 1。 `StudentID`是中的外键`Enrollment`指定中的学生的表`Student`表。

如果导航属性可以具有多个实体，导航属性必须是列表类型，如`ICollection<T>`。 `ICollection<T>`可以指定，或如类型`List<T>`或`HashSet<T>`。 当`ICollection<T>`是使用，EF 核心创建`HashSet<T>`默认情况下的集合。 包含多个实体的导航属性来自于多对多和一个对多关系。

### <a name="the-enrollment-entity"></a>注册实体

![注册实体关系图](intro/_static/enrollment-entity.png)

在*模型*文件夹中，创建*Enrollment.cs*替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

`EnrollmentID`属性是为主键。 此实体使用`classnameID`模式而不是`ID`如`Student`实体。 通常，开发人员选择一个模式并使用整个数据模型。 在更高版本的教程中，没有类名的情况下使用 ID 将显示以便更加轻松地在数据模型中实现继承。

`Grade`属性是`enum`。 后问号`Grade`类型声明指示`Grade`属性可以为 null。 为 null 的等级不从零个年级不同--null 意味着一个等级而言未知的或未尚未分配。

`StudentID`属性是一个外键，且相应的导航属性为`Student`。 `Enrollment`实体是与一个相关联`Student`实体，因此该属性包含单个`Student`实体。 `Student`实体区别`Student.Enrollments`导航属性，其中包含多个`Enrollment`实体。

`CourseID`属性是一个外键，且相应的导航属性为`Course`。 `Enrollment`实体是与一个相关联`Course`实体。

EF 核心作为外键解释属性，如果它名为`<navigation property name><primary key property name>`。 例如，`StudentID`为`Student`导航属性，因为`Student`实体的主键是`ID`。 外键属性还可以进行命名`<primary key property name>`。 例如，`CourseID`由于`Course`实体的主键是`CourseID`。

### <a name="the-course-entity"></a>课程实体

![课程实体关系图](intro/_static/course-entity.png)

在*模型*文件夹中，创建*Course.cs*替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

`Enrollments`属性是导航属性。 A`Course`可以与任意数量的相关实体`Enrollment`实体。

`DatabaseGenerated`属性允许应用程序以指定为主键，而不是具有数据库生成它。

## <a name="create-the-schoolcontext-db-context"></a>创建 SchoolContext 数据库上下文

协调 EF 核心功能为给定的数据模型的主类是 DB 上下文类。 数据上下文派生自`Microsoft.EntityFrameworkCore.DbContext`。 数据上下文指定数据模型中包含哪些实体。 在此项目中类命名为`SchoolContext`。

在项目文件夹中，创建名为的文件夹*数据*。

在*数据*文件夹创建*SchoolContext.cs*替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

此代码将创建`DbSet`每个实体集的属性。 在 EF 核心术语：

* 实体集通常对应于数据库表。
* 实体对应于表中的行。

`DbSet<Enrollment>`和`DbSet<Course>`可以省略。 EF 核心或希伯来语它们隐式`Student`实体引用`Enrollment`实体，与`Enrollment`实体引用`Course`实体。 对于本教程中，保留`DbSet<Enrollment>`和`DbSet<Course>`中`SchoolContext`。

EF 核心创建数据库后，创建具有名称相同的表`DbSet`属性名称。 为集合的属性名将采用通常复数形式 （学生而不是学生）。 开发人员不同意有关表名称是否应采用复数形式。 有关这些教程中，覆盖默认行为是在 DbContext 中指定单数表名称。 若要指定单数表名称，添加以下突出显示的代码：

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-context-with-dependency-injection"></a>上下文注册依赖关系注入

ASP.NET 核心包括[依赖关系注入](xref:fundamentals/dependency-injection)。 在应用程序启动过程的依赖关系注入注册服务 （例如 EF 核心 DB 上下文中）。 需要这些服务 （如 Razor 页） 的组件提供了这些服务通过构造函数参数。 在教程后面部分显示了获取的数据库上下文实例的构造函数代码。

若要注册`SchoolContext`作为一种服务，打开*Startup.cs*，并添加到突出显示的行`ConfigureServices`方法。

[!code-csharp[Main](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=3-4)]

连接字符串的名称均在通过传递给上下文上调用的方法`DbContextOptionsBuilder`对象。 以进行本地开发， [ASP.NET 核心配置系统](xref:fundamentals/configuration/index)读取中的连接字符串*appsettings.json*文件。

添加`using`语句`ContosoUniversity.Data`和`Microsoft.EntityFrameworkCore`命名空间。 生成项目。

[!code-csharp[Main](intro/samples/cu/Startup.cs?name=snippet_Usings)]

打开*appsettings.json*文件并添加连接字符串，如下面的代码中所示：

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

前面提到的连接字符串使用`ConnectRetryCount=0`以防止[SQLClient](https://docs.microsoft.com/dotnet/framework/data/adonet/ef/sqlclient-for-the-entity-framework)从挂起。

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

连接字符串指定 SQL Server LocalDB 数据库。 LocalDB 是 SQL Server Express 数据库引擎的轻量级版本，用于应用程序开发，不生产环境中使用。 LocalDB 按需启动并在用户模式下运行，因此没有复杂的配置。 默认情况下，创建 LocalDB *.mdf* DB 文件中`C:/Users/<user>`目录。

## <a name="add-code-to-initialize-the-db-with-test-data"></a>添加代码以初始化使用测试数据的数据库

EF 核心创建空数据库。 在本部分中，*种子*方法编写要测试数据填充它。

在*数据*文件夹中，创建名为的新类文件*DbInitializer.cs*并添加以下代码：

[!code-csharp[Main](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

代码将检查数据库中是否存在任何学生。 如果在数据库中不有任何学生，数据库设置种子值使用测试数据。 它将测试数据加载到数组而不是`List<T>`集合来优化性能。

`EnsureCreated`方法自动创建的数据库上下文 DB。 如果 DB 存在，`EnsureCreated`返回而不修改数据库。

在*Program.cs*，修改`Main`方法来执行以下操作：

* 从依赖关系注入容器获取 DB 上下文实例。
* 调用 seed 方法，将上下文传递到它。
* Seed 方法完成时释放上下文。

下面的代码演示更新*Program.cs*文件。

[!code-csharp[Main](intro/samples/cu/ProgramOriginal.cs?name=snippet)]

第一次运行该应用程序，数据库是创建并使用测试数据设定种子。 更新数据模型时：
* 删除数据库。
* 更新的 seed 方法。
* 运行应用并创建新的植入的 DB。 

在更高版本的教程中，在数据模型更改，而无需删除并重新创建数据库时，被更新数据库。

<a name="pmc"></a>
## <a name="add-scaffold-tooling"></a>添加基架工具

在本部分中，包管理器控制台 (PMC) 用于添加的 Visual Studio web 代码生成包。 必须添加此包才能运行基架引擎。

从“工具”菜单中，选择“NuGet 包管理器” > “包管理器控制台”。

在包管理器控制台 (PMC) 中，输入以下命令：

```powershell
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Utils
```

前一个命令将 NuGet 包添加到 *.csproj 文件中：

[!code-csharp[Main](intro/samples/cu/ContosoUniversity1_csproj.txt?highlight=7-8)]

<a name="scaffold"></a>
## <a name="scaffold-the-model"></a>基架模型

* 打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。
* 运行以下命令：


 ```console
dotnet restore
dotnet aspnet-codegenerator razorpage -m Student -dc SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
 ```
 
如果生成以下错误：

```text
Unhandled Exception: System.IO.FileNotFoundException: 
Could not load file or assembly 
'Microsoft.VisualStudio.Web.CodeGeneration.Utils, 
Version=2.0.0.0, Culture=neutral, PublicKeyToken=adb9793829ddae60'.
The system cannot find the file specified.
```

再次运行该命令并保留在页面底部的注释。

如果收到错误：
  ```
No executable found matching command "dotnet-aspnet-codegenerator"
  ```

打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。


生成项目。 生成将生成如下所示的错误：

 `1>Pages\Students\Index.cshtml.cs(26,38,26,45): error CS1061: 'SchoolContext' does not contain a definition for 'Student'`

 全局更改`_context.Student`到`_context.Students`(即，将"s"添加到`Student`)。 7 匹配项的发现和更新。 我们希望修复[此 bug](https://github.com/aspnet/Scaffolding/issues/633)下一个版本中。

[!INCLUDE[model4tbl](../../includes/RP/model4tbl.md)]

 <a name="test"></a>
### <a name="test-the-app"></a>测试应用

运行应用并选择**学生**链接。 具体取决于浏览器宽度，**学生**链接将显示在页面顶部。 如果**学生**链接不可见，请单击右上角中的导航图标。

![窄的 Contoso 大学主页](intro/_static/home-page-narrow.png)

测试**创建**，**编辑**，和**详细信息**链接。

## <a name="view-the-db"></a>查看数据库

当启动应用程序时，`DbInitializer.Initialize`调用`EnsureCreated`。 `EnsureCreated`检测到如果 DB 存在，并且如有必要将创建一个。 如果 DB 中, 不有任何学生`Initialize`方法将添加学生。

打开**SQL Server 对象资源管理器**(SSOX) 从**视图**Visual Studio 中的菜单。
在 SSOX 中，单击**(localdb) \MSSQLLocalDB > 数据库 > ContosoUniversity1**。

展开**表**节点。

右键单击**学生**表，然后单击**查看数据**以查看创建的列和插入到表的行。

*.Mdf*和*.ldf* DB 文件位于*C:\Users\\ <yourusername>* 文件夹。

`EnsureCreated`称为应用程序启动，允许以下工作流：

* 删除数据库。
* 更改数据库架构 (例如，添加`EmailAddress`字段)。
* 运行应用。

`EnsureCreated`创建与 DB`EmailAddress`列。

## <a name="conventions"></a>约定

为了使 EF 核心以创建完整数据库编写量是代码的最小因为使用的约定，或者使 EF 核心的假设。

* 名称`DbSet`属性用作表名。 未被引用的实体`DbSet`属性，实体类名称用作表名称。

* 实体属性名称用于列名称。

* ID 或 classnameID 命名的实体属性被识别为主键属性。

* 如果它名为属性将被解释为外键属性 *<navigation property name> <primary key property name>*  (例如，`StudentID`为`Student`以来的导航属性`Student`实体的主键是`ID`). 可以命名外键属性 *<primary key property name>*  (例如，`EnrollmentID`由于`Enrollment`实体的主键是`EnrollmentID`)。

可以重写常规行为。 例如，表名称可以显式指定，本教程中前面所示。 可以显式设置的列名称。 可以显式设置主键和外键。

## <a name="asynchronous-code"></a>异步代码

异步编程是 ASP.NET Core 和 EF 核心的默认模式。

Web 服务器具有有限的数量的线程可用，并且在高负载情况下的所有可用线程可能正在使用。 在这种情况，服务器无法处理新请求，直到线程会被释放。 与同步代码，多个线程可能会占用时它们实际上不执行任何操作，因为它们正在等待 I/O 完成。 与异步代码时进程正在等待 I/O 完成，将其线程被释放服务器用于处理其他请求。 因此，异步代码启用更有效地使用服务器资源，并启用该服务器以处理更多流量不会延迟。

异步代码会在运行时引入的少量开销。 低流量情况下，性能命中可以忽略不计，时的高流量情况下，此潜在的性能改进非常大量。

在下面的代码中，`async`关键字，`Task<T>`返回值，`await`关键字，和`ToListAsync`方法使代码异步执行。

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* `async`关键字告知编译器对：

  * 生成方法体的部分的回调。
  * 自动创建[任务](https://docs.microsoft.com/dotnet/api/system.threading.tasks.task?view=netframework-4.7)返回的对象。 有关详细信息，请参阅[任务返回类型](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)。

* 隐式返回类型`Task`表示正在进行的工作。

* `await`关键字会导致编译器拆分为两个部分的方法。 第一部分结尾以异步方式启动该操作。 第二部分被放入操作完成时调用的回调方法。

* `ToListAsync`是的异步版本`ToList`扩展方法。

编写异步代码，使用 EF 核心时考虑的一些事项：

* 以异步方式执行导致查询或命令发送到数据库的语句。 这包括`ToListAsync`， `SingleOrDefaultAsync`， `FirstOrDefaultAsync`，和`SaveChangesAsync`。 它不包括只需更改的语句`IQueryable`，如`var students = context.Students.Where(s => s.LastName == "Davolio")`。

* EF 核心上下文不线程安全： 请勿尝试执行并行的多个操作。 

* 若要充分利用异步代码的性能优势，请验证库包 （如分页），是否它们调用将查询发送到数据库的 EF 核心方法中使用异步。

有关在.NET 中的异步编程的详细信息，请参阅[异步概述](https://docs.microsoft.com/dotnet/articles/standard/async)。

在下一步的教程中，基本的 CRUD （创建、 读取、 更新、 删除） 操作进行检查。

>[!div class="step-by-step"]
[下一篇](xref:data/ef-rp/crud)
