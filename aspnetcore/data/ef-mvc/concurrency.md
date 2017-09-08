---
title: "ASP.NET 核心 MVC 与 EF 核心-并发-8 的 10"
author: tdykstra
description: "本教程演示如何处理冲突，当多个用户在同一时间更新同一实体。"
keywords: "ASP.NET 核心，实体框架核心并发"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 15e79e15-bda5-441d-80c7-8032a2628605
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/concurrency
ms.openlocfilehash: f44a4f842180b4001eb1428316c24fd9cacc39db
ms.sourcegitcommit: 5355c96a1768e5a1d5698a98c190e7addcc4ded5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2017
---
# <a name="handling-concurrency-conflicts---ef-core-with-aspnet-core-mvc-tutorial-8-of-10"></a>处理并发冲突的 EF 内核，它们有 ASP.NET 核心 MVC 教程 (10 的第 8)

通过[Tom Dykstra](https://github.com/tdykstra)和[Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso 大学示例 web 应用程序演示如何创建使用实体框架核心和 Visual Studio 的 ASP.NET 核心 MVC web 应用程序。 有关教程系列的信息，请参阅[序列中的第一个教程](intro.md)。

在前面教程中，您学习了如何更新数据。 本教程演示如何处理冲突，当多个用户在同一时间更新同一实体。

你将创建网页使用部门实体和处理并发错误。 下图显示的编辑和删除的页面，包括某些并发冲突发生时显示的消息。

![部门编辑页](concurrency/_static/edit-error.png)

![部门删除页](concurrency/_static/delete-error.png)

## <a name="concurrency-conflicts"></a>并发冲突

一个用户才能进行编辑，显示实体的数据，并在第一个用户的更改写入到数据库之前，另一个用户然后更新相同实体的数据时发生并发冲突。 如果不启用此类冲突检测，任何人都更新数据库上次将覆盖其他用户的更改。 在许多应用程序，这种风险很可接受： 如果有很多用户或几个更新，或如果并不真正重要的一些更改将被覆盖，如果并发编程的开销可能超过带来的好处。 在这种情况下，你不必配置此应用程序处理并发冲突。

### <a name="pessimistic-concurrency-locking"></a>保守式并发 （锁定）

如果你的应用程序需要防止并发方案中的意外数据丢失，做到这一点的一种方法是使用数据库锁。 这称为保守式并发。 例如，从数据库读取行之前，你请求的锁只读的或更新访问权限。 如果锁定的行更新访问权限时，不允许任何其他用户锁定该行为只读的或更新访问权限，因为它们将获取正在进行更改的数据的副本。 如果锁定用于只读访问的行，其他人可以还将其锁定用于只读访问权限但不是用于更新。

管理锁也有缺点。 它可以是复杂到程序。 它需要大量的数据库管理资源，并且可能导致性能问题的应用程序的用户数会增加。 出于这些原因，不是所有数据库管理系统都支持保守式并发。 本教程不向你展示如何实现它，并且实体框架核心提供，没有内置支持。

### <a name="optimistic-concurrency"></a>开放式并发

保守式并发替代方法是开放式并发。 开放式并发意味着允许并发冲突发生，然后相应地反应，如果他们这样做。 例如，Jane 访问部门编辑页，并从 $350,000.00 英语部门的预算金额变为 0.00 美元。

![将预算更改为 0](concurrency/_static/change-budget.png)

Jane 单击之前**保存**，John 访问同一页上，并从 2007 年 9 月 1 日的开始日期字段更改为 2013 年 9 月 1 日。

![更改为 2013年的开始日期](concurrency/_static/change-date.png)

Jane 单击**保存**第一个，看到她更改时浏览器返回到索引页面。

![更改为零的预算](concurrency/_static/budget-zero.png)

然后 John 单击**保存**仍会显示 $350,000.00 预算将编辑页上。 接下来如何操作取决于如何处理并发冲突。

某些选项包括：

* 你可以跟踪的用户进行了修改的属性，并更新仅在数据库中的相应列。

     在示例方案中，任何数据将不会丢失，因为不同的属性已更新由两个用户。 下一次有人浏览英语部门，他们将看到 Jane 的和 John 的更改-2013 年 9 月 1 日开始日期和零美元的预算。 这种更新方法可以减少可能导致数据丢失的冲突数，但它无法避免数据丢失，如果实体的同一属性进行竞争的更改。 实体框架是否可以通过这种方式取决于如何实现你更新代码。 它通常是状态的不现实中 web 应用程序，因为它可能需要维护大量，以便跟踪的实体的所有原始属性值以及新值。 维护大量的状态会影响应用程序性能，因为它需要服务器资源或必须包含在 web 页本身 （例如，在隐藏的字段） 或在一个 cookie。

* 你可以让 John 的更改覆盖 Jane 的更改。

     下一次有人浏览英语部门时，他们将看到 2013 年 9 月 1 日和还原的 $350,000.00 值。 这称为*客户端优先*或*在 Wins 中最后一个*方案。 （从客户端的所有值都优先于什么是在数据存储。）中所述的简介本部分中，如果您不执行任何并发处理的编码，这将自动发生。

* 你可以防止 John 的更改正在更新数据库中。

     通常情况下，将显示一条错误消息、 显示他的数据的当前状态和允许他将他仍想要使它们的情况下重新应用他的更改。 这称为*存储 Wins*方案。 （数据存储值优先于提交的客户端的值。）在本教程中，你将实现存储 Wins 方案。 此方法可确保到发生了什么情况收到通知用户的情况下，任何更改都会被覆盖。

### <a name="detecting-concurrency-conflicts"></a>检测并发冲突

您可以通过处理中解决冲突`DbConcurrencyException`实体框架引发的异常。 为了知道何时引发这些异常，实体框架必须能够检测到冲突。 因此，你必须从数据库和数据模型正确配置。 有关启用冲突检测某些选项包括：

* 在数据库表中，包括可以用于确定当行已更改的跟踪列。 然后，你可以配置实体框架可以包括该列在 Where 子句的 SQL 更新或删除命令。

     跟踪列的数据类型通常是`rowversion`。 `rowversion`值是每次更新行时递增序列号。 在 Update 或 Delete 命令中，Where 子句中包括跟踪列 （原始行版本） 的原始值。 如果正在更新的行已由其他用户时中的值更改`rowversion`列是不同于原始值，因此 Update 或 Delete 语句找不到要更新，因为 Where 出现的行子句。 当实体框架会找到任何行，已更新的 Update 或 Delete 命令 （即，在受影响的行数为零） 时，它会，解释为并发冲突。

* 配置实体框架可以包括每个列的原始值的表中 Where 子句的 Update 和 Delete 命令。

     如下所示的第一个选项，如果自第一次读取一行，行中的任何内容已更改 Where 子句不会返回的行，若要更新，该实体框架会将解释为并发冲突。 对于包含许多列的数据库表，此方法会导致非常大的 Where 子句，并可能需要维护大量的状态。 如前文所述，则保留大量的状态可能会影响应用程序性能。 因此此方法通常不建议，并且它不在本教程中使用的方法。

     如果您想要实现这种并发的方法，则必须将你想要通过添加跟踪的并发性实体中的所有非主键属性标记`ConcurrencyCheck`属性设为它们。 所做的更改使实体框架能够在 Update 和 Delete 语句的 SQL Where 子句中包括所有列。

本教程的其余部分中将添加`rowversion`跟踪对部门实体的属性，创建一个控制器和视图，并进行测试以验证一切是否正常工作。

## <a name="add-a-tracking-property-to-the-department-entity"></a>将跟踪属性添加到部门实体

在*Models/Department.cs*，添加名为 RowVersion 的跟踪属性：

[!code-csharp[Main](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

`Timestamp`属性指定此列将包含在 Where 子句的 Update 和 Delete 命令发送到数据库。 该属性称为`Timestamp`因为以前版本的 SQL Server 使用 SQL`timestamp`之前 SQL 数据类型`rowversion`替换它。 .NET 类型`rowversion`为字节数组。

如果想要使用 fluent API，则可以使用`IsConcurrencyToken`方法 (在*Data/SchoolContext.cs*) 来指定跟踪属性，在下面的示例所示：

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

通过添加一个属性，更改数据库模型，因此你需要执行另一个迁移。

保存所做的更改和生成项目，然后在命令窗口中输入以下命令：

```console
dotnet ef migrations add RowVersion
```

```console
dotnet ef database update
```

## <a name="create-a-departments-controller-and-views"></a>创建部门控制器和视图

如你此前为学生、 课程和教师搭建部门控制器和视图。

![基架部门](concurrency/_static/add-departments-controller.png)

在*DepartmentsController.cs*文件中，将所有四个出现的"FirstMidName"更改为"FullName"，以便部门管理员下拉列表将包含教师的完整名称而不是只是最后一个名称。

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-the-departments-index-view"></a>更新部门索引视图

基架引擎创建 RowVersion 列在索引视图中，但不应显示该字段。

中的代码替换*Views/Departments/Index.cshtml*替换为以下代码。

[!code-html[Main](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

这将更改发往"部门"的、 删除 RowVersion 列中，然后显示管理员而不是第一个名称的完整名称。

## <a name="update-the-edit-methods-in-the-departments-controller"></a>更新部门控制器中的编辑方法

在这两个 HttpGet`Edit`方法和`Details`方法，添加`AsNoTracking`。 在 HttpGet`Edit`方法，添加为管理员预先加载。

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading&highlight=2,3)]

将现有代码为 HttpPost`Edit`方法替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

代码首先尝试读取要更新的部门。 如果`SingleOrDefaultAsync`方法将返回 null，部门已由另一个用户被删除。 在这种情况下代码使用已发布的窗体值创建部门实体，以便编辑页可以重新显示并显示错误消息。 作为替代方法，你不需要重新创建部门实体，如果不重新显示部门字段的情况下显示仅错误消息。

该视图将存储原始`RowVersion`值中隐藏的字段，因此该方法接收中的该值`rowVersion`参数。 在调用之前`SaveChanges`，你必须将它放原始`RowVersion`中的属性值`OriginalValues`的实体的集合。

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

然后在实体框架创建 SQL UPDATE 命令，该命令将包含一个 WHERE 子句，查找具有行的原始`RowVersion`值。 如果更新命令会不影响任何行 (没有行具有原始`RowVersion`值)，实体框架将引发`DbUpdateConcurrencyException`异常。

该异常的 catch 块中的代码获取具有从更新后的值的受影响的部门实体`Entries`异常对象上的属性。

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

`Entries`集合将有仅仅一个`EntityEntry`对象。  该对象可用于获取用户输入的新值和当前的数据库值。

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

该代码将添加具有不同的数据库值从用户所输入的编辑页上的每个列的自定义错误消息 （只有一个字段如下所示为简洁起见）。

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

最后，代码设置`RowVersion`值`departmentToUpdate`从数据库检索到的新值。 此新`RowVersion`值将存储在隐藏的字段，将重新显示页，编辑和下一次用户单击时**保存**，因为将捕获的编辑页重新显示发生这种情况的并发错误。

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

`ModelState.Remove`语句是必需的因为`ModelState`具有旧`RowVersion`值。 在视图中，`ModelState`字段将优先于模型属性值都存在时的值。

## <a name="update-the-department-edit-view"></a>更新部门编辑视图

在*Views/Departments/Edit.cshtml*，进行以下更改：

* 添加隐藏的字段以保存`RowVersion`立即之后的隐藏的字段的属性值`DepartmentID`属性。

* 将"选择管理员"选项添加到下拉列表中。

[!code-html[Main](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts-in-the-edit-page"></a>在编辑页中测试并发冲突

运行该站点，然后单击以转到部门索引页的部门。

右键单击**编辑**英语部门和选择的超链接**新选项卡中打开**，然后单击**编辑**英语部门的超链接。 两个浏览器选项卡现在显示的相同信息。

更改第一个浏览器选项卡中的字段，然后单击**保存**。

![部门编辑更改后的第 1 页](concurrency/_static/edit-after-change-1.png)

浏览器将显示更改后的值的索引页。

更改第二个浏览器选项卡中的字段。

![部门编辑更改后的第 2 页](concurrency/_static/edit-after-change-2.png)

单击“保存” 。 你看到一条错误消息：

![部门编辑页错误消息](concurrency/_static/edit-error.png)

单击**保存**试。 保存在第二个浏览器选项卡中输入的值。 索引页面显示时，将显示保存的值。

## <a name="update-the-delete-page"></a>更新删除页

对于删除页中，实体框架检测并发冲突某人其他类似的方式编辑部门引起。 当 HttpGet`Delete`方法会显示确认视图，则视图包括原始`RowVersion`隐藏字段中的值。 然后，该值将供 HttpPost`Delete`用户确认删除时调用的方法。 当实体框架创建 SQL DELETE 命令时，它包括 WHERE 子句与原始`RowVersion`值。 如果零行中的命令结果的影响 （含义后显示删除确认页，已更改行），则会引发一个并发异常，和 HttpGet`Delete`使用错误标志设置为 true 以重新显示调用方法确认页，并一条错误消息。 还有可能因为行由其他用户时，删除，因此在这种情况下不显示任何错误消息影响了零行。

### <a name="update-the-delete-methods-in-the-departments-controller"></a>更新部门控制器中的删除方法

在*DepartmentsController.cs*，替换 HttpGet`Delete`方法替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

该方法将接受可选参数，该值指示是否页正在后将重新显示并发错误。 如果此标志为 true 且不再指定部门存在，它是由另一个用户中删除。 在这种情况下，代码将重定向到索引页面。  如果此标志为 true 且部门确实存在，则它已由另一个用户。 在这种情况下，代码将一条错误消息发送到视图使用`ViewData`。  

HttpPost 中的代码替换`Delete`方法 (名为`DeleteConfirmed`) 替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

在您刚更换的基架代码，此方法接受仅记录 ID:


```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

你已更改为创建的模型联编程序的部门实体实例的此参数。 这样，EF 访问为除了的记录密钥 RowVersion 的属性值。

```csharp
public async Task<IActionResult> Delete(Department department)
```

您还已更改的操作方法名称`DeleteConfirmed`到`Delete`。 基架的代码使用名称`DeleteConfirmed`为 HttpPost 方法提供一个唯一的签名。 （CLR 需要具有不同的方法参数的重载的方法。）现在，签名是唯一的可以停在使用 MVC 约定，并使用 HttpPost 和 HttpGet 删除方法相同的名称。

如果该部门已删除，`AnyAsync`方法返回 false，应用程序只需将返回到 Index 方法。

如果捕获到并发错误，则该代码将显示删除确认页，并提供一个标志，指示它应显示并发错误消息。

### <a name="update-the-delete-view"></a>更新删除视图

在*Views/Departments/Delete.cshtml*，基架的代码替换为下面的代码用于添加一个错误消息字段和 DepartmentID 和 RowVersion 属性的隐藏的字段。 突出显示所做的更改。

[!code-html[Main](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

这将做出以下更改：

* 添加一条错误消息之间`h2`和`h3`标题。

* 替换 FullName 中 FirstMidName**管理员**字段。

* 删除 RowVersion 字段。

* 添加的隐藏的字段`RowVersion`属性。

运行部门索引页。 右键单击**删除**英语部门和选择的超链接**新选项卡中打开**，然后在第一个选项卡中单击**编辑**英语部门的超链接。

在第一个窗口中，更改一个值，然后单击**保存**:

![在删除之前的更改后的部门编辑页](concurrency/_static/edit-after-change-for-delete.png)

在第二个选项卡上，单击**删除**。 请参阅并发错误消息，并使用什么是当前数据库中刷新的部门值。

![出现并发错误的部门删除确认页](concurrency/_static/delete-error.png)

如果你单击**删除**同样，在重定向到显示部门已被删除的索引页。

## <a name="update-details-and-create-views"></a>更新详细信息，并创建视图

（可选） 可以清理详细信息中的基架代码并创建视图。

中的代码替换*Views/Departments/Details.cshtml*删除 RowVersion 列和显示管理员的完整名称。

[!code-html[Main](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

中的代码替换*Views/Departments/Create.cshtml*将选择的选项添加到下拉列表。

[!code-html[Main](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="summary"></a>摘要

这将完成简介处理并发冲突。 有关如何处理在 EF 核心中的并发的详细信息，请参阅[并发冲突](https://docs.microsoft.com/ef/core/saving/concurrency)。 下一教程演示如何实现教师和学生实体的每个层次结构一个表继承。

>[!div class="step-by-step"]
[上一页](update-related-data.md)
[下一页](inheritance.md)  
