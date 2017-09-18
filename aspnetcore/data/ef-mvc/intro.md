---
title: "ASP.NET 核心 MVC 与实体框架核心-10 的教程 1"
author: tdykstra
description: 
keywords: "ASP.NET 核心，实体框架核心，教程"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: b67c3d4a-f2bf-4132-a48b-4b0d599d7981
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/intro
ms.openlocfilehash: 6bde59ddbf153ada36034765b390892ec2ed5997
ms.sourcegitcommit: 98ecb0f1bae4886507b090c84ecd99ff1e5c46ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2017
---
# <a name="getting-started-with-aspnet-core-mvc-and-entity-framework-core-using-visual-studio-1-of-10"></a>ASP.NET 核心 MVC 和使用 Visual Studio (第 1 个 10) 的实体框架核心入门

通过[Tom Dykstra](https://github.com/tdykstra)和[Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso 大学示例 web 应用程序演示如何创建使用 Entity Framework (EF) 核心 2.0 和 Visual Studio 2017 ASP.NET 核心 2.0 MVC web 应用程序。

示例应用程序是一个用于 fictional Contoso 大学网站。 它包括诸如学生许可、 过程创建和教师分配等功能。 这是一系列教程说明如何生成从零开始的 Contoso 大学示例应用程序中的第一个。

[下载或查看已完成的应用程序。](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

EF 核心 2.0 是 EF 的最新版本，但还没有的 EF 的所有功能 6.x。 有关如何 EF 之间进行选择 6.x 和 EF 核心，请参阅[EF 核心 vs。EF6.x](https://docs.microsoft.com/ef/efcore-and-ef6/)。 如果你选择 EF 6.x 时，请参阅[本系列教程的上一个版本](https://docs.microsoft.com/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)。

> [!NOTE]
> * 本教程的 ASP.NET 核心 1.1 版本，请参阅[以 PDF 格式在本教程中的 VS 2017 Update 2 版本](https://github.com/aspnet/Docs/blob/master/aspnetcore/data/ef-mvc/intro/_static/efmvc1.1.pdf)。
> * 有关本教程的 Visual Studio 2015 版本，请参阅 [PDF 格式的 ASP.NET Core 文档的 VS 2015 版本](https://github.com/aspnet/Docs/blob/master/aspnetcore/common/_static/aspnet-core-project-json.pdf)。

## <a name="prerequisites"></a>先决条件

[!INCLUDE[install 2.0](../../includes/install2.0.md)]

## <a name="troubleshooting"></a>疑难解答

如果你遇到无法解决的问题，通常可以通过比较你的代码查找解决方案[已完成的项目](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)。 有关常见错误以及如何解决这些列表，请参阅[最后一个教程系列中的故障排除部分](advanced.md#common-errors)。 如果没有找到你那里需要你可以将问题发布到为 StackOverflow.com [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core)或[EF 核心](https://stackoverflow.com/questions/tagged/entity-framework-core)。

> [!TIP] 
> 这是一系列 10 教程，其中每个基于前面教程中完成。  请考虑在每个成功的教程完成后保存项目的副本。  以后如果遇到问题，你可以开始通过从以前的教程，而不是追溯到整个序列的开头。

## <a name="the-contoso-university-web-application"></a>Contoso 大学 web 应用程序

你将在这些教程中构建的应用程序是简单大学网站。

用户可以查看和更新学生、 课程中和教师信息。 以下是一些你将创建的屏幕。

![学生索引页](intro/_static/students-index.png)

![学生编辑页](intro/_static/student-edit.png)

此站点的用户界面样式而保留接近什么由运行内置的任何模板，以便在本教程主要关注如何使用实体框架。

## <a name="create-an-aspnet-core-mvc-web-application"></a>创建 ASP.NET 核心 MVC web 应用程序

打开 Visual Studio 并创建一个新 ASP.NET 核心 C# web 项目名为"ContosoUniversity"。

* 从**文件**菜单上，选择**新建 > 项目**。

* 从左窗格中，选择**已安装 > Visual C# > Web**。

* 选择**ASP.NET 核心 Web 应用程序**项目模板。

* 输入**ContosoUniversity**作为名称，然后单击**确定**。

  ![“新建项目”对话框](intro/_static/new-project.png)

* 等待**新 ASP.NET 核心 Web 应用程序 (.NET Core)**显示对话框

* 选择**ASP.NET 核心 2.0**和**Web 应用程序 （模型-视图-控制器）**模板。

  **注意：**本教程需要安装 ASP.NET 核心 2.0 和 EF 核心 2.0 或更高版本-请确保**ASP.NET 核心 1.1**未选中。

* 请确保**身份验证**设置为**无身份验证**。

* 单击“确定” 

  ![“新建 ASP.NET 项目”对话框](intro/_static/new-aspnet.png)

## <a name="set-up-the-site-style"></a>设置站点样式

几个简单的更改将设置站点菜单、 布局和主页。

打开*Views/Shared/_Layout.cshtml*并进行以下更改：

* 将每个匹配项的"ContosoUniversity"更改为"Contoso 大学"。 有三个匹配项。

* 添加菜单项**学生**，**课程**，**教师**，和**部门**，并删除**联系人**菜单项。

突出显示所做的更改。

[!code-html[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,30,36-39,48)]

在*Views/Home/Index.cshtml*，将文件的内容替换为以下代码以将有关 ASP.NET 和 MVC 的文本替换为有关此应用程序的文本：

[!code-html[](intro/samples/cu/Views/Home/Index.cshtml)]

按 CTRL + F5 来运行该项目或选择**调试 > 启动但不调试**从菜单。 你看到其中包含你将创建这些教程中的页的选项卡的主页。

![Contoso 大学主页](intro/_static/home-page.png)

## <a name="entity-framework-core-nuget-packages"></a>实体框架核心 NuGet 包

若要添加到项目的 EF 核心支持，请安装你想要针对的数据库提供程序。 本教程使用 SQL Server，并且提供程序包[Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。 此包包含在[Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage，因此你不必安装它。

此包和其依赖项 (`Microsoft.EntityFrameworkCore`和`Microsoft.EntityFrameworkCore.Relational`) 提供 EF 的运行时支持。 你将添加工具包更高版本，在[迁移](migrations.md)教程。 

有关可用于实体框架核心其他数据库提供程序的信息，请参阅[数据库提供程序](https://docs.microsoft.com/ef/core/providers/)。

## <a name="create-the-data-model"></a>创建数据模型

接下来你将创建 Contoso 大学应用程序的实体类。 你将开始以下三个实体。

![课程注册学生数据模型关系图](intro/_static/data-model-diagram.png)

之间的一对多关系`Student`和`Enrollment`实体，并且没有之间的一个对多关系`Course`和`Enrollment`实体。 换而言之，一名学生可以在任意数量的课程中, 注册，并且某一课程可以具有任意数量的学生在其中注册。

下列部分中，你将创建每个这些实体的类。

### <a name="the-student-entity"></a>学生实体

![学生实体关系图](intro/_static/student-entity.png)

在*模型*文件夹中，创建一个名为的类文件*Student.cs*和模板代码替换为以下代码。

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

`ID`属性将成为对应于此类数据库表的主键列。 默认情况下，实体框架将解释一个属性，名为`ID`或`classnameID`为主键。

`Enrollments`属性是导航属性。 导航属性将分别包含与此实体相关的其他实体。 在这种情况下，`Enrollments`属性`Student entity`将保留所有`Enrollment`与该订阅相关的实体`Student`实体。 换而言之，如果在数据库中给定的学生行有两个相关注册行 （包含该学生的主键值其 StudentID 外键列中的行），`Student`实体的`Enrollments`导航属性将包含那些两个`Enrollment`实体。

如果导航属性可以具有多个实体 （如多对多或一对多关系），其类型必须是的列表中的条目可以是添加、 删除和更新，如`ICollection<T>`。  你可以指定`ICollection<T>`或类型，如`List<T>`或`HashSet<T>`。 如果指定`ICollection<T>`，EF 创建`HashSet<T>`默认情况下的集合。

### <a name="the-enrollment-entity"></a>注册实体

![注册实体关系图](intro/_static/enrollment-entity.png)

在*模型*文件夹中，创建*Enrollment.cs*和替换为以下代码替换现有代码：

[!code-csharp[Main](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

`EnrollmentID`属性将是主键; 此实体使用`classnameID`模式而不是`ID`本身作为你在中看到`Student`实体。 通常，你将选择一个模式，并使用它在你的数据模型。 在这里，变体说明了你可以使用任一模式。 在[后面的教程](inheritance.md)，你将看到如何利用 ID 没有类名的情况下可使其更轻松地在数据模型中实现继承。

`Grade`属性是`enum`。 后问号`Grade`类型声明指示`Grade`属性可以为 null。 为 null 的等级不从零个年级不同--null 意味着一个等级而言未知的或未尚未分配。

`StudentID`属性是一个外键，且相应的导航属性为`Student`。 `Enrollment`实体是与一个相关联`Student`实体，因此该属性只包含单个`Student`实体 (与不同`Student.Enrollments`导航属性前面所看到的后者可以容纳多个`Enrollment`实体)。

`CourseID`属性是一个外键，且相应的导航属性为`Course`。 `Enrollment`实体是与一个相关联`Course`实体。

实体框架： 将属性解释为外键属性，如果它名为`<navigation property name><primary key property name>`(例如，`StudentID`为`Student`以来的导航属性`Student`实体的主键是`ID`)。 此外可以只需命名外键属性`<primary key property name>`(例如，`CourseID`由于`Course`实体的主键是`CourseID`)。

### <a name="the-course-entity"></a>课程实体

![课程实体关系图](intro/_static/course-entity.png)

在*模型*文件夹中，创建*Course.cs*和替换为以下代码替换现有代码：

[!code-csharp[Main](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

`Enrollments`属性是导航属性。 A`Course`可以与任意数量的相关实体`Enrollment`实体。

我们举例更多有关`DatabaseGenerated`属性中[后面的教程](complex-data-model.md)本系列。 基本上，此属性允许您输入的过程，而不是让生成它的数据库的主键。

## <a name="create-the-database-context"></a>创建的数据库上下文

协调为给定的数据模型的实体框架功能的主类是数据库上下文类。 将通过从 `Microsoft.EntityFrameworkCore.DbContext` 类派生的方式创建此类。 在代码中你指定数据模型中包含哪些实体。 你还可以自定义某些实体框架行为。 在此项目中类命名为`SchoolContext`。

在项目文件夹中，创建名为的文件夹*数据*。

在*数据*文件夹创建名为的新类文件*SchoolContext.cs*，并将模板代码替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

此代码将创建`DbSet`每个实体集的属性。 在实体框架术语中，实体集通常与数据库表相对应，实体与表中的行相对应。

可以省略`DbSet<Enrollment>`和`DbSet<Course>`语句和其效果相同。 实体框架会将其包含隐式因为`Student`实体引用`Enrollment`实体和`Enrollment`实体引用`Course`实体。

EF 创建数据库后，创建具有名称相同的表`DbSet`属性名称。 集合的属性名称通常是有关是否表名称应变为复数形式或不反对复数形式 （学生而不是学生），但开发人员。 对于这些教程将通过在 DbContext 中指定单数表名称来覆盖默认行为。 若要做到这一点，请在最后一个 DbSet 属性之后添加以下突出显示的代码。

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-context-with-dependency-injection"></a>上下文注册依赖关系注入

ASP.NET 核心实现[依赖关系注入](../../fundamentals/dependency-injection.md)默认情况下。 在应用程序启动过程的依赖关系注入注册服务 （例如 EF 数据库上下文中）。 需要这些服务 （如 MVC 控制器） 的组件提供了这些服务通过构造函数参数。 你将看到控制器构造函数代码，可在本教程后面获取上下文实例。

若要注册`SchoolContext`作为一种服务，打开*Startup.cs*，并添加到突出显示的行`ConfigureServices`方法。

[!code-csharp[Main](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=3-4)]

连接字符串的名称均在通过传递给上下文上调用的方法`DbContextOptionsBuilder`对象。 以进行本地开发， [ASP.NET 核心配置系统](../../fundamentals/configuration.md)读取中的连接字符串*appsettings.json*文件。

添加`using`语句`ContosoUniversity.Data`和`Microsoft.EntityFrameworkCore`命名空间，然后生成项目。

[!code-csharp[Main](intro/samples/cu/Startup.cs?name=snippet_Usings)]

打开*appsettings.json*文件并添加连接字符串，如下面的示例中所示。

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

连接字符串指定 SQL Server LocalDB 数据库。 LocalDB 是 SQL Server Express 数据库引擎的轻量级版本，用于应用程序开发，不生产环境中使用。 LocalDB 按需启动并在用户模式下运行，因此没有复杂的配置。 默认情况下，创建 LocalDB *.mdf*数据库中的文件`C:/Users/<user>`目录。

## <a name="add-code-to-initialize-the-database-with-test-data"></a>添加代码以初始化测试数据的数据库

实体框架将为你创建空数据库。  在本部分中，你编写以便填充测试数据创建数据库后调用的方法。

此处将使用`EnsureCreated`方法来自动创建数据库。 在[后面的教程](migrations.md)你将了解如何通过使用 Code First 迁移以更改而不是删除并重新创建数据库的数据库架构处理模型更改。

在*数据*文件夹中，创建名为的新类文件*DbInitializer.cs*和模板代码替换为以下代码，这会导致要在需要时创建的数据库和负载测试到新的数据数据库。

[!code-csharp[Main](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

代码检查是否有任何学生在数据库中，并且如果没有，它假定数据库是新，并且需要使用测试数据进行种子设定。  它将测试数据加载到数组而不是`List<T>`集合来优化性能。

在*Program.cs*，修改`Main`方法来执行以下操作，在应用程序启动：

* 从依赖关系注入容器中获取的数据库上下文实例。
* 调用 seed 方法，将上下文传递到它。
* Seed 方法完成此操作，请释放上下文。

[!code-csharp[Main](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

添加`using`语句：

[!code-csharp[Main](intro/samples/cu/Program.cs?name=snippet_Usings)]

在较旧教程中，你可能会看到中的类似代码`Configure`中的方法*Startup.cs*。 我们建议你使用`Configure`方法只是为了设置请求管道。 应用程序启动代码放入`Main`方法。

现在首次运行该应用程序，将数据库创建并使用测试数据设定种子。 每当你更改你的数据模型，你可以删除数据库、 更新你的 seed 方法，然后重新开始与新数据库相同的方式。 在更高版本的教程中，你将了解如何在数据模型更改，而无需删除并重新创建它时修改数据库。

## <a name="create-a-controller-and-views"></a>创建控制器和视图

接下来，将使用基架引擎在 Visual Studio 中添加一个 MVC 控制器和将使用 EF 来查询和保存数据的视图。

CRUD 操作方法和视图的自动创建被称为基架。 基架与不同从代码生成的基架的代码是一个起始点，您可以修改以满足自己需求，而你通常无需修改生成的代码。 当你需要进行自定义生成的代码时，你使用分部类或发生更改时重新生成代码。

* 右键单击**控制器**文件夹中的**解决方案资源管理器**和选择**添加 > 新建基架项**。

* 在“添加 MVC 依赖项”对话框中，选择“最小依赖项”，然后选择“添加”。

  ![添加依赖项](intro/_static/add-depend.png)

  Visual Studio 添加搭建基架控制器所需的依赖关系。 项目文件中的唯一更改是添加`Microsoft.VisualStudio.Web.CodeGeneration.Design`包。

  A *ScaffoldingReadMe.txt*这可以删除创建文件。

* 同样，右键单击**控制器**文件夹中的**解决方案资源管理器**和选择**添加 > 新建基架项**。

* 在**添加基架**对话框中：

  * 选择**使用实体框架的包含视图的 MVC 控制器**。

  * 单击 **“添加”**。

* 在**添加控制器**对话框中：

  * 在**模型类**选择**学生**。

  * 在**数据上下文类**选择**SchoolContext**。

  * 接受默认值**StudentsController**作为名称。

  * 单击 **“添加”**。

  ![基架学生](intro/_static/scaffold-student.png)

  当你单击**添加**，Visual Studio 基架引擎创建*StudentsController.cs*文件和一组视图 (*.cshtml*文件) 这样与控制器起作用。

(基架引擎还可以创建的数据库上下文为你如果你不在手动创建如你此前在本教程第一次。 你可以指定在一个新上下文类**添加控制器**通过单击右侧的加号框**数据上下文类**。  然后，visual Studio 将创建你`DbContext`以及控制器和视图类。)

你会注意到控制器采用`SchoolContext`作为构造函数参数。

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

ASP.NET 依赖关系注入将会负责处理传递的一个实例`SchoolContext`插入控制器。 配置，在*Startup.cs*前面文件。

控制器包含`Index`显示数据库中的所有学生的操作方法。 该方法获取的学生列表的学生实体集通过读取从`Students`数据库上下文实例属性：

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

你将在本教程后面了解此代码中的异步编程元素。

*Views/Students/Index.cshtml*视图在表中显示此列表：

[!code-html[](intro/samples/cu/Views/Students/Index1.cshtml)]

按 CTRL + F5 来运行该项目或选择**调试 > 启动但不调试**从菜单。

单击学生选项卡以查看测试的数据，`DbInitializer.Initialize`插入的方法。 具体取决于如何窄浏览器窗口，你将看到`Student`选项卡链接在顶部的页或你将需要单击右上角才能看到该链接中的导航图标。

![窄的 Contoso 大学主页](intro/_static/home-page-narrow.png)

![学生索引页](intro/_static/students-index.png)

## <a name="view-the-database"></a>查看数据库

当你启动了应用程序，`DbInitializer.Initialize`方法调用`EnsureCreated`。 EF 看到没有任何数据库，它创建一个，然后的其余部分`Initialize`方法代码填充数据的数据库。 你可以使用**SQL Server 对象资源管理器**(SSOX) 若要查看 Visual Studio 中的数据库。

关闭浏览器。

如果 SSOX 窗口尚未打开，请选择它从**视图**Visual Studio 中的菜单。

在 SSOX 中，单击**(localdb) \MSSQLLocalDB > 数据库**，然后单击数据库名称中的连接字符串中的条目你*appsettings.json*文件。

展开**表**节点以查看你的数据库中的表。

![在 SSOX 中的表](intro/_static/ssox-tables.png)

右键单击**学生**表，然后单击**查看数据**若要查看已创建的列和已插入到表的行。

![在 SSOX 中学生表](intro/_static/ssox-student-table.png)

*.Mdf*和*.ldf*数据库文件位于*C:\Users\<yourusername >*文件夹。

因为正在呼叫`EnsureCreated`的初始值设定项方法中，在启动应用程序上运行，你现在可以进行的更改`Student`类、 删除数据库、 运行一次，应用程序和数据库将自动重新创建，以匹配所做的更改。 例如，如果你添加`EmailAddress`属性`Student`类，你将看到管理新`EmailAddress`重新创建表中的列。

## <a name="conventions"></a>约定

你必须编写实体框架能够为你创建完整的数据库中的代码量很短，因为使用了约定，或者使实体框架的假设。

* 名称`DbSet`属性用作表名。 未被引用的实体`DbSet`属性，实体类名称用作表名称。

* 实体属性名称用于列名称。

* ID 或 classnameID 命名的实体属性被识别为主键属性。

* 如果它名为属性将被解释为外键属性* <navigation property name> <primary key property name> * (例如，`StudentID`为`Student`以来的导航属性`Student`实体的主键是`ID`). 此外可以只需命名外键属性* <primary key property name> * (例如，`EnrollmentID`由于`Enrollment`实体的主键是`EnrollmentID`)。

可以重写常规行为。 例如，你可以显式指定表名称，正如你此前在本教程中看到。 你可以设置列名称并将任何属性设置为 primary key 或外键，正如在看到[后面的教程](complex-data-model.md)本系列。

## <a name="asynchronous-code"></a>异步代码

异步编程是 ASP.NET Core 和 EF 核心的默认模式。

Web 服务器具有有限的数量的线程可用，并且在高负载情况下的所有可用线程可能正在使用。 在这种情况，服务器无法处理新请求，直到线程会被释放。 与同步代码，多个线程可能会占用时它们实际上不执行任何操作，因为它们正在等待 I/O 完成。 与异步代码时进程正在等待 I/O 完成，将其线程被释放服务器用于处理其他请求。 因此，异步代码启用更有效地使用服务器资源，并启用该服务器以处理更多流量不会延迟。

异步代码在运行时，会引入的少量开销，但潜在的性能改善的性能影响可以忽略不计，时针对的高流量情况低流量情况下，较高。

在下面的代码中，`async`关键字，`Task<T>`返回值，`await`关键字，和`ToListAsync`方法使代码异步执行。

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* `async`关键字告知编译器生成的方法主体的部件的回调并自动创建`Task<IActionResult>`返回的对象。

* 返回类型`Task<IActionResult>`表示正在进行的工作类型的结果`IActionResult`。

* `await`关键字会导致编译器拆分为两个部分的方法。 第一部分结尾以异步方式启动该操作。 第二部分被放入操作完成时调用的回调方法。

* `ToListAsync`是的异步版本`ToList`扩展方法。

请注意当你编写异步代码的使用实体框架的一些事项：

* 以异步方式执行导致查询或命令发送到数据库的语句。 这包括，例如， `ToListAsync`， `SingleOrDefaultAsync`，和`SaveChangesAsync`。  它不包括，例如，只需更改的语句`IQueryable`，如`var students = context.Students.Where(s => s.LastName == "Davolio")`。

* EF 上下文不是线程安全： 请勿尝试执行并行的多个操作。 当调用的任何异步 EF 方法时，始终使用`await`关键字。

* 如果你想要利用异步代码的性能优势，请确保任何库包，你仅使用 （例如，用于分页），还使用异步，如果它们调用任何导致查询发送到数据库的实体框架方法。

有关在.NET 中的异步编程的详细信息，请参阅[异步概述](https://docs.microsoft.com/dotnet/articles/standard/async)。

## <a name="summary"></a>摘要

你现在已创建的简单应用程序使用的实体框架核心和 SQL Server Express LocalDB 来存储和显示数据。 在以下教程中，你将学习如何执行基本的 CRUD （创建、 读取、 更新、 删除） 操作。

>[!div class="step-by-step"]
[下一篇](crud.md)  
