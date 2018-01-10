---
title: "与 EF 核心-并发-8 的 8 razor 页"
author: rick-anderson
description: "本教程演示如何处理冲突，当多个用户在同一时间更新同一实体。"
keywords: "ASP.NET 核心，实体框架核心并发"
ms.author: riande
manager: wpickett
ms.date: 11/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-rp/concurrency
ms.openlocfilehash: 841c638b2cacaab7970f2b173fee488972957b63
ms.sourcegitcommit: 2d23ea501e0213bbacf65298acf1c8bd17209540
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2018
---
en-我们 /

# <a name="handling-concurrency-conflicts---ef-core-with-razor-pages-8-of-8"></a>处理并发冲突的 EF 内核，它们有 Razor 页 (8 的第 8)

通过[Rick Anderson](https://twitter.com/RickAndMSFT)， [Tom Dykstra](https://github.com/tdykstra)，和[Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE[about the series](../../includes/RP-EF/intro.md)]

本教程演示如何在多个用户并发 （在同一时间） 更新实体时处理冲突。 如果你遇到无法解决的问题，请下载[对此阶段已完成应用程序](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part8)。

## <a name="concurrency-conflicts"></a>并发冲突

发生并发冲突时：

* 用户导航到实体的编辑页。
* 在第一个用户的更改写入到数据库之前，另一个用户更新相同的实体。

如果未启用并发检测，当发生并发更新：

* 最后一个的更新 wins。 即，最后一个的更新值将保存到数据库。
* 当前的更新的第一个都将丢失。

### <a name="optimistic-concurrency"></a>开放式并发

开放式并发允许并发冲突发生，，然后反应适当时它们完成。 例如，Jane 访问部门编辑页，并从 $350,000.00 英语部门预算变为 0.00 美元。

![将预算更改为 0](concurrency/_static/change-budget.png)

Jane 单击之前**保存**，John 访问同一页上，并从 2007 年 9 月 1 日的开始日期字段更改为 2013 年 9 月 1 日。

![更改为 2013年的开始日期](concurrency/_static/change-date.png)

Jane 单击**保存**第一个，看到她更改时浏览器将显示索引页面。

![更改为零的预算](concurrency/_static/budget-zero.png)

John 单击**保存**仍会显示 $350,000.00 预算将编辑页上。 接下来如何操作取决于如何处理并发冲突。

开放式并发包括以下选项：

* 你可以跟踪的用户进行了修改的属性，并更新仅在数据库中的相应列。

 在方案中，将不会丢失任何数据。 不同的属性更新了两个用户。 下一次有人浏览英语部门，他们将看到 Jane 的和 John 的更改。 这种更新方法可以减少可能导致数据丢失的冲突数。 这种方法: * 无法避免数据丢失，如果同一属性进行竞争的更改。
        * 是在 web 应用程序通常不可行。 它需要维护以跟踪所有提取的值和新值的有效状态。 保留大量的状态可能会影响应用程序性能。
        * 可以增加相比实体上的并发检测的应用程序复杂性。

* 你可以让 John 的更改覆盖 Jane 的更改。

 下一次有人浏览英语部门时，他们将看到 2013 年 9 月 1 日和提取 $350,000.00 值。 这种方法称为*客户端优先*或*在 Wins 中最后一个*方案。 （从客户端的所有值都优先于什么是在数据存储。）如果你不执行任何并发处理的编码，客户端优先自动发生。

* 你可以防止 John 的更改正在更新数据库中。 通常情况下，应用程序将: * 显示一条错误消息。
        * 显示数据的当前状态。
        * 允许用户重新应用更改。

 这称为*存储 Wins*方案。 （数据存储值优先于提交的客户端的值。）在本教程中实现存储 Wins 方案。 此方法可确保任何更改都会收到通知用户的情况下被覆盖。

## <a name="handling-concurrency"></a>处理并发 

当属性配置为[并发标记](https://docs.microsoft.com/en-us/ef/core/modeling/concurrency):

* EF 核心验证已提取后属性没有已修改过。 就会执行检查时[SaveChanges](https://docs.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges)或[SaveChangesAsync](https://docs.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_)调用。
* 如果它已提取的后, 更改的属性[DbUpdateConcurrencyException](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0)引发。 

数据库和数据模型必须配置为支持引发`DbUpdateConcurrencyException`。

### <a name="detecting-concurrency-conflicts-on-a-property"></a>检测并发冲突的属性

可以在属性级别检测并发冲突[ConcurrencyCheck](https://docs.microsoft.com/en-us/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0)属性。 特性可以应用于模型的多个属性。 有关详细信息，请参阅[数据批注 ConcurrencyCheck](https://docs.microsoft.com/en-us/ef/core/modeling/concurrency#data-annotations)。

`[ConcurrencyCheck]`属性不在本教程中使用。

### <a name="detecting-concurrency-conflicts-on-a-row"></a>检测并发冲突行上

若要检测并发冲突[rowversion](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql)跟踪列添加到模型。  `rowversion` :

* 是特定的 SQL Server。 其他数据库可能无法提供相似的功能。
* 用于确定实体后从 DB 中提取了不被更改。 

数据库生成顺序`rowversion`递增每次行的数量会更新。 在`Update`或`Delete`命令，`Where`子句包括将获取的值的`rowversion`。 如果正在更新的行已更改：

 * `rowversion`提取的值不匹配。
 * `Update`或`Delete`命令未找到行，因为`Where`子句中包括提取`rowversion`。
 * A`DbUpdateConcurrencyException`引发。

在 EF 核，通过不更新任何行后`Update`或`Delete`命令时，会引发并发异常。

### <a name="add-a-tracking-property-to-the-department-entity"></a>将跟踪属性添加到部门实体

在*Models/Department.cs*，添加名为 RowVersion 的跟踪属性：

[!code-csharp[Main](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

[时间戳](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations.timestampattribute)属性指定此列包含在`Where`子句`Update`和`Delete`命令。 该属性称为`Timestamp`因为以前版本的 SQL Server 使用 SQL`timestamp`之前 SQL 数据类型`rowversion`类型替换它。

Fluent API 还可以指定跟踪属性：

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

下面的代码显示在更新部门名称时生成的 EF 核心 T-SQL 的一部分：

[!code-sql[](intro/samples/sql.txt?highlight=2-3)]

前面突出显示的代码所示`WHERE`子句包含`RowVersion`。 如果数据库`RowVersion`不等于`RowVersion`参数 (`@p2`)，会更新任何行。

下面突出显示的代码演示 T-SQL 验证恰好一个行已更新：

[!code-sql[](intro/samples/sql.txt?highlight=4-6)]

[@@ROWCOUNT ](https://docs.microsoft.com/en-us/sql/t-sql/functions/rowcount-transact-sql)返回的最后一个语句所影响的行数。 没有行均已更新，EF 核心引发`DbUpdateConcurrencyException`。

你可以看到在 Visual Studio 的输出窗口中生成的 T-SQL 的 EF 核心。

### <a name="update-the-db"></a>更新数据库

添加`RowVersion`属性更改 DB 模型，这需要迁移。

生成项目。 在命令窗口中输入以下命令：

```console
dotnet ef migrations add RowVersion
dotnet ef database update
```

前面的命令：

* 将添加*迁移 / {时间 stamp}_RowVersion.cs*迁移文件。
* 更新*Migrations/SchoolContextModelSnapshot.cs*文件。 该更新添加到以下突出显示的代码`BuildModel`方法：

[!code-csharp[Main](intro/samples/cu/Migrations/SchoolContextModelSnapshot2.cs?name=snippet&highlight=14-16)]

* 运行迁移来更新数据库。

<a name="scaffold"></a>
## <a name="scaffold-the-departments-model"></a>基架部门模型

* 退出 Visual Studio。
* 打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。
* 运行下面的命令：

 ```console
dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
 ```

前面的命令基架`Department`模型。 在 Visual Studio 中打开项目。

生成项目。 生成将生成如下所示的错误：

`1>Pages/Departments/Index.cshtml.cs(26,37,26,43): error CS1061: 'SchoolContext' does not
 contain a definition for 'Department' and no extension method 'Department' accepting a first
 argument of type 'SchoolContext' could be found (are you missing a using directive or
 an assembly reference?)`

 全局更改`_context.Department`到`_context.Departments`(即，将"s"添加到`Department`)。 7 匹配项的发现和更新。

### <a name="update-the-departments-index-page"></a>更新部门索引页

创建基架引擎`RowVersion`不应显示为索引页面中，但该字段的列。 在本教程中，最后一个字节的`RowVersion`显示以帮助了解并发。 最后一个字节不保证是唯一的。 实际的应用程序不会显示`RowVersion`或最后一个字节的`RowVersion`。

更新索引页：

* 将替换为部门的索引。
* 替换标记包含`RowVersion`与最后一个字节的`RowVersion`。
* 将替换为 FullName FirstMidName。

以下标记显示更新页上：

[!code-html[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a>更新编辑页模型

更新*pages\departments\edit.cshtml.cs*替换为以下代码：

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

若要检测的并发问题， [OriginalValue](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue)使用更新`rowVersion`提取的实体中的值。 EF 核心生成 SQL UPDATE 命令与 WHERE 子句包含原始`RowVersion`值。 如果更新命令会不影响任何行 (没有行具有原始`RowVersion`值)、 一个`DbUpdateConcurrencyException`引发异常。

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-)]

在前面的代码中，`Department.RowVersion`时提取了该实体是值。 `OriginalValue`是数据库中的值时`FirstOrDefaultAsync`调用此方法中。

以下代码将获取客户端值 （发布到此方法的值） 和 DB 值：

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

以下代码将添加自定义错误消息，为每个列具有 DB 值不同于什么发到`OnPostAsync`:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

以下突出显示代码集`RowVersion`从数据库检索为新值的值。 下次用户单击**保存**，仅将发生这种情况由于将捕获的最后一个显示编辑页的并发错误。

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

`ModelState.Remove`语句是必需的因为`ModelState`具有旧`RowVersion`值。 在 Razor 页中，`ModelState`字段将优先于模型属性值都存在时的值。

## <a name="update-the-edit-page"></a>更新编辑页

更新*Pages/Departments/Edit.cshtml*替换为以下标记：

[!code-html[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

前面的标记：

* 更新`page`指令从`@page`到`@page "{id:int}"`。
* 添加一个隐藏的行版本。 `RowVersion`必须添加，使回发将的值绑定。
* 显示的最后一个字节`RowVersion`以便进行调试。
* 替换`ViewData`使用强类型`InstructorNameSL`。

## <a name="test-concurrency-conflicts-with-the-edit-page"></a>测试与编辑页的并发冲突

打开两个浏览器上的实例编辑英语部门：

* 运行应用程序并选择部门。
* 右键单击**编辑**英语部门和选择的超链接**新选项卡中打开**。
* 在第一个选项卡上，单击**编辑**英语部门的超链接。

两个浏览器选项卡显示相同的信息。

更改第一个浏览器选项卡中的名称，然后单击**保存**。

![部门编辑更改后的第 1 页](concurrency/_static/edit-after-change-1.png)

浏览器将显示有更改的值和更新的 rowVersion 指示器的索引页。 请注意更新的 rowVersion 指示器中，它将显示在第二个回发的其他选项卡中。

更改第二个浏览器选项卡中的不同字段。

![部门编辑更改后的第 2 页](concurrency/_static/edit-after-change-2.png)

单击“保存” 。 你看到不匹配的 DB 值的所有字段的错误的消息：

![部门编辑页错误消息](concurrency/_static/edit-error.png)

此浏览器窗口不打算更改名称字段。 复制并粘贴到名称字段的当前值 （语言）。 选项卡上。客户端验证删除错误消息。

![部门编辑页错误消息](concurrency/_static/cv.png)

单击**保存**试。 保存在第二个浏览器选项卡中输入的值。 你看到索引页面中的已保存的值。

## <a name="update-the-delete-page"></a>更新删除页

替换为以下代码更新删除页模型：

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

在该实体已提取后发生更改时，删除页检测并发冲突。 `Department.RowVersion`提取了实体的行版本时。 当 EF 核心创建 SQL DELETE 命令时，它包括 WHERE 子句和`RowVersion`。 如果受影响的零行中的 SQL DELETE 命令结果：

* `RowVersion`中 SQL DELETE 命令不匹配`RowVersion`在数据库中。
* 会引发一个 DbUpdateConcurrencyException 异常。
* `OnGetAsync`使用调用`concurrencyError`。

### <a name="update-the-delete-page"></a>更新删除页

更新*Pages/Departments/Delete.cshtml*替换为以下代码：

[!code-html[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,36,51)]


前面的标记进行以下更改：

* 更新`page`指令从`@page`到`@page "{id:int}"`。
* 添加一条错误消息。
* 替换 FullName 中 FirstMidName**管理员**字段。
* 更改`RowVersion`要显示的最后一个字节。
* 添加一个隐藏的行版本。 `RowVersion`必须添加，使回发将的值绑定。

### <a name="test-concurrency-conflicts-with-the-delete-page"></a>使用删除页上的测试并发冲突

创建测试部门。

打开两个浏览器上的实例删除测试部门：

* 运行应用程序并选择部门。
* 右键单击**删除**超链接的测试部门和选择**新选项卡中打开**。
* 单击**编辑**测试部门的超链接。

两个浏览器选项卡显示相同的信息。

更改第一个浏览器选项卡中的预算并单击**保存**。

浏览器将显示有更改的值和更新的 rowVersion 指示器的索引页。 请注意更新的 rowVersion 指示器中，它将显示在第二个回发的其他选项卡中。

从第二个选项卡中删除测试部门。并发错误是显示来自数据库的当前值。 单击**删除**删除的实体，除非`RowVersion`已 updated.department 已被删除。

请参阅[继承](xref:data/ef-mvc/inheritance)如何继承数据模型。

### <a name="additional-resources"></a>其他资源

* [在 EF 核心中的并发标记](https://docs.microsoft.com/en-us/ef/core/modeling/concurrency)
* [在 EF 核心中处理并发](https://docs.microsoft.com/en-us/ef/core/saving/concurrency)

>[!div class="step-by-step"]
[上一篇](xref:data/ef-rp/update-related-data)
