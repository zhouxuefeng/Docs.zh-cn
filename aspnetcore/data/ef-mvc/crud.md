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
ms.openlocfilehash: 3393bb90d170cfc572d2307ec18f1a8e25bdce59
ms.sourcegitcommit: 74a8ad9c1ba5c155d7c4303e67632a0922c38e86
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2017
---
# <a name="create-read-update-and-delete---ef-core-with-aspnet-core-mvc-tutorial-2-of-10"></a>创建、 读取、 更新和删除的 EF 内核，它们有 ASP.NET 核心 MVC 教程 (2 个 10)

通过[Tom Dykstra](https://github.com/tdykstra)和[Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso 大学示例 web 应用程序演示如何创建使用实体框架核心和 Visual Studio 的 ASP.NET 核心 MVC web 应用程序。 有关教程系列的信息，请参阅[序列中的第一个教程](intro.md)。

在前面的教程，你创建的 MVC 应用程序存储和显示数据使用实体框架和 SQL Server LocalDB。 在本教程中，你将查看和自定义 CRUD （创建、 读取、 更新、 删除） 的 MVC 基架自动为你创建在控制器和视图中的代码。

> [!NOTE] 
> 它是常见的做法，以便创建你的控制器和数据访问层之间的抽象层实现存储库模式。 若要使这些教程简单和关注教学如何使用实体框架本身，它们不使用存储库。 有关存储库和 EF 的信息，请参阅[本系列最后一个教程](advanced.md)。

在本教程中，您将可以使用以下网页：

![学生详细信息页](crud/_static/student-details.png)

![学生创建页](crud/_static/student-create.png)

![学生编辑页](crud/_static/student-edit.png)

![学生删除页](crud/_static/student-delete.png)

## <a name="customize-the-details-page"></a>自定义的详细信息页

学生索引页的基架的代码中省略`Enrollments`属性，因为该属性包含集合。 在**详细信息**页上，将集合的内容显示在 HTML 表。

在*Controllers/StudentsController.cs*，有关详细信息的操作方法查看使用`SingleOrDefaultAsync`方法来检索单个`Student`实体。 将调用的代码添加`Include`。 `ThenInclude`和`AsNoTracking`方法，如以下突出显示的代码中所示。

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Details&highlight=8-12)]

`Include`和`ThenInclude`方法导致要加载的上下文`Student.Enrollments`导航属性，并在每个注册`Enrollment.Course`导航属性。  你将了解有关这些方法中[读取相关的数据](read-related-data.md)教程。

`AsNoTracking`方法可以提高其中返回的实体将不会更新在当前上下文的生存期中的方案中的性能。 你将了解更多有关`AsNoTracking`在本教程末尾。

### <a name="route-data"></a>路线数据

传递到的密钥值`Details`方法来自*将数据路由*。 路由数据是模型联编程序的 URL 段中找到的数据。 例如，默认路由指定控制器、 操作和 id 段：

[!code-csharp[Main](intro/samples/cu/Startup.cs?name=snippet_Route&highlight=5)]

在下面的 URL，默认路由教师映射为控制器，作为相应的操作、 索引和 1，因为 id;这些是路由数据值。

```
http://localhost:1230/Instructor/Index/1?courseID=2021
```

URL 的最后部分 ("？ courseID = 2021年") 是一个查询字符串值。 模型联编程序还将传递到的 ID 值`Details`方法`id`如果将其作为查询字符串值传递的参数：

```
http://localhost:1230/Instructor/Index?id=1&CourseID=2021
```

在索引页中，通过在 Razor 视图中的标记帮助器语句创建超链接 Url。 在以下 Razor 代码中，`id`参数和默认路由匹配，因此`id`将添加到路由数据。

```html
<a asp-action="Edit" asp-route-id="@item.ID">Edit</a>
```

这将生成以下 HTML 时`item.ID`为 6:

```html
<a href="/Students/Edit/6">Edit</a>
```

在以下 Razor 代码中，`studentID`不匹配默认路由中的参数，因此，它将添加作为查询字符串。

```html
<a asp-action="Edit" asp-route-studentID="@item.ID">Edit</a>
```

这将生成以下 HTML 时`item.ID`为 6:

```html
<a href="/Students/Edit?studentID=6">Edit</a>
```

有关标记帮助器的详细信息，请参阅[中 ASP.NET Core 标记帮助程序](xref:mvc/views/tag-helpers/intro)。

### <a name="add-enrollments-to-the-details-view"></a>将注册添加到详细信息视图

打开*Views/Students/Details.cshtml*。 使用显示每个字段`DisplayNameFor`和`DisplayFor`帮助器，如下面的示例中所示：

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=13-18&highlight=2,5)]

最后一个字段后，立即在关闭前`</dl>`标记中，添加以下代码以显示注册的列表：

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=31-52)]

如果后粘贴代码，代码缩进有误，，按 CTRL-K-D 更正它。

此代码循环访问中的实体`Enrollments`导航属性。 对于每个注册，它显示课程标题和评分。 正在从存储中的课程实体检索课程标题`Course`注册实体导航属性。

运行应用程序中，选择**学生**卡，然后单击**详细信息**一名学生的链接。 为所选学生查看课程和年级的列表：

![学生详细信息页](crud/_static/student-details.png)

## <a name="update-the-create-page"></a>更新创建页

在*StudentsController.cs*，修改 HttpPost`Create`方法通过添加 try catch 块和删除从 ID`Bind`属性。

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=4,6-7,14-21)]

此代码添加到学生实体的 ASP.NET MVC 模型联编程序通过创建学生实体设置，然后将所做的更改保存到数据库。 （模型联编程序指 ASP.NET MVC 功能，让你更轻松地处理提交的窗体的数据; 模型联编程序将已发布的窗体值转换为 CLR 类型，并将其传递给参数中的操作方法。 在此情况下，模型联编程序实例化学生实体为你使用从窗体集合的属性值。）

你删除`ID`从`Bind`特性，因为 ID 是 SQL Server 将插入行时自动设置的主键值。 来自用户的输入不设置的 ID 值。

除`Bind`属性，try catch 块将仅对基架的代码所做的更改。 如果异常派生自`DbUpdateException`是捕捉到正在保存所做的更改时，将显示一般错误消息。 `DbUpdateException`由外部的应用程序，而不是编程错误，有时会导致异常，因此建议用户以重试。 尽管在此示例中未实现，但生产质量应用程序会将异常记录。 有关详细信息，请参阅**要深入探索日志**主题中[监视和遥测 （构建真实世界云应用程序与 Azure）](https://docs.microsoft.com/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry)。

`ValidateAntiForgeryToken`属性有助于防止跨站点请求伪造 (CSRF) 攻击。 令牌自动注入到按查看[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper)和由用户提交表单时，将包括。 验证的令牌`ValidateAntiForgeryToken`属性。 有关 CSRF 的详细信息，请参阅[反请求伪造](../../security/anti-request-forgery.md)。

<a id="overpost"></a>
### <a name="security-note-about-overposting"></a>有关 overposting 安全说明

`Bind`包括的基架的代码的属性`Create`方法是一种方法，以防止 overposting 中创建的方案。 例如，假设学生实体包含`Secret`你不希望此网页可设置的属性。

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

即使你没有`Secret`在网页上，黑客字段无法使用 Fiddler，之类的工具或编写一些 JavaScript，发布`Secret`形成的值。 而无需`Bind`属性限制时它将创建学生实例，模型联编程序模型联编程序使用的字段将选取的`Secret`窗体值并使用它来创建学生实体实例。 然后任何值为指定黑客`Secret`窗体字段将在你的数据库中更新。 下图显示 Fiddler 工具添加`Secret`（具有值"OverPost"） 到已发布的窗体值的字段。

![Fiddler 添加机密字段](crud/_static/fiddler.png)

随后将可"OverPost"成功添加到的值`Secret`属性插入行，尽管你永远不会预期网页能够设置该属性。

你可以通过先从数据库读取实体，然后再调用防止在编辑方案中的 overposting `TryUpdateModel`，并传递显式允许的属性列表中。 这是这些教程中使用的方法。

防止首选许多开发人员的 overposting 一种备用方法是使用视图模型，而不是实体类与模型绑定。 包括你想要在视图模型中更新的属性。 完成 MVC 模型联编程序后，将复制到实体实例，根据需要使用 AutoMapper 等工具的查看模型属性。 使用`_context.Entry`对实体实例，其状态设置为`Unchanged`，然后设置`Property("PropertyName").IsModified`为 true 的每个视图模型中包含的实体属性。 此方法适用于同时编辑和创建方案。

### <a name="test-the-create-page"></a>测试创建页

中的代码*Views/Students/Create.cshtml*使用`label`， `input`，和`span`（用于验证消息） 标记的每个字段的帮助程序。

运行应用程序中，选择**学生**卡，然后单击**新建**。

输入名称和日期。 请尝试输入无效的日期，如果你的浏览器，可以做到这一点。 （某些浏览器强制你要使用日期选取器。）然后单击**创建**可查看错误消息。

![日期验证错误](crud/_static/date-error.png)

这是默认情况下; 获取的服务器端验证在更高版本的教程中，你将看到如何添加还将生成代码以便进行客户端验证的属性。 以下突出显示的代码演示模型验证检查在`Create`方法。

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=8)]

将日期更改为有效的值并单击**创建**若要查看显示在新学生**索引**页。

## <a name="update-the-edit-page"></a>更新编辑页

在*StudentController.cs*，HttpGet`Edit`方法 (而不是`HttpPost`属性) 使用`SingleOrDefaultAsync`方法来检索所选的学生实体，正如你在看到`Details`方法。 你不需要更改此方法。

### <a name="recommended-httppost-edit-code-read-and-update"></a>建议 HttpPost 编辑代码： 读取和更新

HttpPost 编辑操作方法替换为以下代码。

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ReadFirst)]

这些更改实现安全的最佳做法，以防止 overposting。 生成基架`Bind`属性，并添加到的实体集与模型联编程序所创建的实体`Modified`标志。 代码不建议用于许多方案，因为`Bind`属性将清除任何预先存在的数据中未列出的字段中`Include`参数。

新的代码读取的现有实体和调用`TryUpdateModel`更新中检索到的实体的字段[基于已发布的窗体数据中的用户输入](xref:mvc/models/model-binding#how-model-binding-works)。 实体框架自动更改跟踪设置`Modified`窗体输入，更改字段上的标志。 当`SaveChanges`方法被调用时，实体框架创建 SQL 语句更新数据库行。 并发冲突将被忽略，并更新了用户的表格列更新数据库中。 （后面的教程演示如何处理并发冲突。）

作为最佳做法以防止过多发布，你想要通过可更新的字段**编辑**页是白名单中的`TryUpdateModel`参数。 （前面的参数列表中的字段的列表为空字符串是为要使用窗体字段名称的前缀。）当前没有额外字段，你要保护的但列出你想要绑定的模型联编程序的字段可确保，如果在将来将字段添加到数据模型，它们是否自动受到保护之前你显式将其添加此处。

由于这些更改，HttpPost 方法签名`Edit`方法等同于 HttpGet`Edit`方法; 因此已重命名方法`EditPost`。

### <a name="alternative-httppost-edit-code-create-and-attach"></a>替代 HttpPost 编辑代码： 创建和附加

建议的 HttpPost 编辑代码可确保仅已更改的列会更新，并保留你不希望包括模型绑定的属性中的数据。 但是，读取为先的方法需要的额外数据库读取，并且可能导致更复杂的代码，用于处理并发冲突。 一种替代方法是将由 EF 上下文模型联编程序创建实体并将其标记为已修改。 (未更新你的项目替换为以下代码，它只表明来演示一种可选方法。) 

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_CreateAndAttach)]

在实体中包含的所有字段和可以更新其中的任何 web 页 UI 时，可以使用此方法。

基架的代码使用的创建和附加方法，但仅捕获`DbUpdateConcurrencyException`异常并返回 404 错误代码。  显示的示例将捕获所有数据库更新异常并显示一条错误消息。

### <a name="entity-states"></a>实体状态

是否在内存中的实体是在数据库中，其对应行与同步，此信息确定在调用时，会发生什么情况，将跟踪的数据库上下文`SaveChanges`方法。 例如，当传递到新实体`Add`方法，该实体的状态设置为方法`Added`。 然后调用`SaveChanges`方法，数据库上下文发出 SQL INSERT 命令。

实体可能处于以下状态之一：

* `Added`。 实体在数据库中尚不存在。 `SaveChanges`方法发出 INSERT 语句。

* `Unchanged`。 无需使用通过此实体完成`SaveChanges`方法。 当从数据库读取实体时，该实体开始时具有此状态。

* `Modified`。 某些或所有实体的属性值已都更改。 `SaveChanges`方法发出 UPDATE 语句。

* `Deleted`。 实体已标记为删除。 `SaveChanges`方法发出的 DELETE 语句。

* `Detached`。 实体不跟踪的数据库上下文。

在桌面应用中，通常会自动设置状态更改。 读取实体并对它的一些属性值进行更改。 这将导致其实体状态自动更改为`Modified`。 然后调用`SaveChanges`，实体框架生成更新仅你更改的实际属性 SQL UPDATE 语句。

在 web 应用中，`DbContext`最初读取某个实体以及显示要编辑其数据已释放之后呈现一个页面。 当 HttpPost`Edit`操作方法调用、 新的 web 请求进行并且您具有的新实例`DbContext`。 如果你重新读取该新的上下文中的实体，则来模拟桌面处理。

但如果不希望执行的额外读取操作，你必须使用由模型联编程序创建的实体对象。  执行此操作的最简单方法是将实体状态设置为已修改，前面所示的备用 HttpPost 编辑代码中的做法一样。 然后调用`SaveChanges`，实体框架更新的数据库行中，所有列，因为上下文具有无法知道您更改了哪些属性。

如果你想要避免读取为先的方法，但你还想要更新仅用户实际更改的字段的更新 SQL 语句，则代码会更复杂。 你必须以某种方式保存的原始值 (如通过使用隐藏的字段)，以便它们可用时 HttpPost`Edit`调用方法。 然后你可以创建使用原始值，调用的学生实体`Attach`实体，该原始版本的方法的新值更新实体的值，然后调用`SaveChanges`。

### <a name="test-the-edit-page"></a>测试的编辑页

运行应用程序中，选择**学生**选项卡，然后单击**编辑**超链接。

![学生编辑页](crud/_static/student-edit.png)

更改某些数据，再单击**保存**。 **索引**页将打开并查看更改的数据。

## <a name="update-the-delete-page"></a>更新删除页

在*StudentController.cs*，HttpGet 的模板代码`Delete`方法使用`SingleOrDefaultAsync`方法来检索所选的学生实体，正如你看到在详细信息视图和编辑方法。 但是，若要实现自定义错误消息时对调用`SaveChanges`失败，需要将某些功能添加到此方法，其相应的视图。

当你看到的针对更新，并创建操作，删除操作需要两个操作方法。 调用以响应 GET 请求的方法显示为用户提供了机会批准或取消删除操作的视图。 如果用户批准它，则创建 POST 请求。 当发生这种情况，HttpPost`Delete`调用方法，该方法然后实际执行删除操作。

将将 try catch 块添加到 HttpPost`Delete`方法以处理更新数据库时可能出现的任何错误。 如果发生错误，HttpPost Delete 方法调用 HttpGet Delete 方法，将其传递参数，该值指示发生错误。 HttpGet Delete 方法然后重新显示确认页以及错误消息，向用户提供机会取消或重试。

替换 HttpGet`Delete`替换为以下代码，它管理错误报告的操作方法。

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteGet&highlight=1,9,16-21)]

此代码将接受可选参数，该值指示是否在出现故障，若要保存更改后调用该方法。 时，此参数为 false HttpGet`Delete`不上一次失败的情况下调用方法。 当由 HttpPost`Delete`方法中对数据库更新错误响应，该参数是 true，并且一条错误消息传递给视图。

### <a name="the-read-first-approach-to-httppost-delete"></a>HttpPost 删除读取为先的方法

替换 HttpPost`Delete`操作方法 (名为`DeleteConfirmed`) 替换为以下代码，该执行实际的删除操作并捕获任何数据库更新错误。

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithReadFirst&highlight=6,8-11,13-14,18-23)]

此代码检索所选的实体，然后调用`Remove`方法将实体的状态设置为`Deleted`。 当`SaveChanges`称为 SQL DELETE 命令生成的。

### <a name="the-create-and-attach-approach-to-httppost-delete"></a>HttpPost 删除创建和附加方法

如果提高大容量应用程序中的性能是优先级，则无法通过实例化使用只有主要的学生实体来避免不必要的 SQL 查询密钥值，然后将实体状态设置为`Deleted`。 这就是所有实体框架需要以便删除该实体。 （不要将此代码放在你的项目; 就在这里仅用于阐释一种替代方法。）

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithoutReadFirst&highlight=7-8)]

如果实体有相关的还应删除的数据，请确保数据库中配置该级联删除。 使用此方法实体被删除时，EF 可能不知道有要删除的相关的实体。

### <a name="update-the-delete-view"></a>更新删除视图

在*Views/Student/Delete.cshtml*，添加之间 h2 标题和 h3 标题中，一条错误消息，如下面的示例中所示：

[!code-html[](intro/samples/cu/Views/Students/Delete.cshtml?range=7-9&highlight=2)]

运行应用程序中，选择**学生**卡，然后单击**删除**超链接：

![删除确认页面](crud/_static/student-delete.png)

单击**删除**。 索引页面显示没有已删除的学生。 （你将看到举例说明的错误处理中并发教程中的操作代码）。

## <a name="closing-database-connections"></a>关闭数据库连接

要释放数据库连接包含的资源，上下文实例必须释放尽可能快地完成此操作使用它。 ASP.NET 核心内置[依赖关系注入](../../fundamentals/dependency-injection.md)将为你负责该任务。

在*Startup.cs*，你调用[AddDbContext 扩展方法](https://github.com/aspnet/EntityFrameworkCore/blob/03bcb5122e3f577a84498545fcf130ba79a3d987/src/Microsoft.EntityFrameworkCore/EntityFrameworkServiceCollectionExtensions.cs)设置`DbContext`ASP.NET DI 容器中的类。 方法将服务生存期设置为`Scoped`默认情况下。 `Scoped`表示与 web 请求生存时间，一致上下文对象生存期和`Dispose`将 web 请求结束时自动调用方法。

## <a name="handling-transactions"></a>处理事务

默认情况下实体框架隐式实现事务。 在方案中，对多个行或表进行更改，然后调用`SaveChanges`，实体框架自动可确保所有所做的更改成功或它们都失败。 如果先完成一些更改，然后，将发生错误，则这些更改可能是自动回滚。 有关方案，你需要更多的控制-例如，如果你想要包括在实体框架外部事务-中执行的操作，请参阅[事务](https://docs.microsoft.com/ef/core/saving/transactions)。

## <a name="no-tracking-queries"></a>否跟踪查询

当数据库上下文检索表行，并创建表示它们的实体对象时，默认情况下它将跟踪的内存中的实体是否同步，与数据库中。 内存中的数据充当缓存，并更新实体时使用。 此缓存通常是不必要的 web 应用中由于上下文实例是否通常生存期较短 （新功能之一是创建和释放为每个请求） 和上下文读取再次使用该实体通常释放实体。

可以通过调用来禁用对实体对象在内存中的跟踪`AsNoTracking`方法。 在其中你可能想要执行此操作的典型方案包括：

* 在上下文生存期内无需更新任何实体，并且您不必到 EF[自动加载与单独的查询所检索的实体的导航属性](read-related-data.md)。 频繁的控制器 HttpGet 操作方法中满足这些条件。

* 正在运行的查询检索大量数据，并将更新仅返回的数据的一小部分。 它可能会更有效，为大型查询中，关闭跟踪并运行更高版本的很少需要更新的实体的查询。

* 你想要将实体附加以便其进行更新，但出于其他目的检索相同的实体的更早版本。 因为该实体已跟踪数据库上下文时，不能将附加你想要更改的实体。 处理这种情况的一种方法是调用`AsNoTracking`上前面的查询。

有关详细信息，请参阅[跟踪 vs。否跟踪](https://docs.microsoft.com/ef/core/querying/tracking)。

## <a name="summary"></a>摘要

你现在具有一组完整的执行学生实体的简单 CRUD 操作的页。 在下一步的教程，你将扩展的功能**索引**页上通过添加排序、 筛选和分页。

>[!div class="step-by-step"]
[上一页](intro.md)
[下一页](sort-filter-page.md)  
