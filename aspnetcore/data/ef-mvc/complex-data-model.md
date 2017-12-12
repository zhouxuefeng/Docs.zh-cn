---
title: "ASP.NET 核心 MVC 与 EF 核心-数据模型的 10 5"
author: tdykstra
description: "在本教程中添加更多实体和关系，并通过指定格式设置、 验证和数据库的映射规则自定义数据模型。"
keywords: "ASP.NET 核心，实体框架核心数据批注"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 0dd63913-a041-48b6-96a4-3aeaedbdf5d0
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: cacb23441e5f5ab06c6be27f3068276f21ff4ed9
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="creating-a-complex-data-model---ef-core-with-aspnet-core-mvc-tutorial-5-of-10"></a>创建复杂的数据模型的 EF 内核，它们有 ASP.NET 核心 MVC 教程 (5 的 10)

通过[Tom Dykstra](https://github.com/tdykstra)和[Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso 大学示例 web 应用程序演示如何创建使用实体框架核心和 Visual Studio 的 ASP.NET 核心 MVC web 应用程序。 有关教程系列的信息，请参阅[序列中的第一个教程](intro.md)。

在前面的教程，你使用过简单的数据模型的三个实体组成。 在本教程中，你将添加多个实体和关系并将通过指定格式设置、 验证和数据库的映射规则自定义数据模型。

当完成时，实体类将组成以下图所示的已完成的数据模型：

![实体关系图](complex-data-model/_static/diagram.png)

## <a name="customize-the-data-model-by-using-attributes"></a>通过使用属性自定义数据模型

在本部分中，你将了解如何通过使用指定格式设置，验证和数据库的映射规则的属性来自定义数据模型。 然后在其中你将创建以下各节的几加上完成的 School 数据模型属性类到你已创建，并在模型中创建的剩余的实体类型的新类。

### <a name="the-datatype-attribute"></a>数据类型属性

对于学生注册日期，网页的所有当前显示时间和日期，虽然所有关心为此字段是日期。 通过使用数据 annotation 特性，你可以使一个代码将在每个视图中显示的数据中修复的显示格式的更改。 若要查看如何执行操作，你将添加到属性的示例`EnrollmentDate`中的属性`Student`类。

在*Models/Student.cs*，添加`using`语句`System.ComponentModel.DataAnnotations`命名空间并添加`DataType`和`DisplayFormat`特性以`EnrollmentDate`属性，如下面的示例中所示：

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

`DataType` 特性用于指定比数据库内部类型更具体的数据类型。 在这种情况下，我们只想跟踪的日期，不的日期和时间。 `DataType`枚举提供了许多数据类型，例如日期、 时间、 电话号码、 货币、 电子邮件地址，和的详细信息。 应用程序还可通过 `DataType` 特性自动提供类型特定的功能。 例如，可以为 `DataType.EmailAddress` 创建 `mailto:` 链接，并且可以在支持 HTML5 的浏览器中为 `DataType.Date` 提供日期选择器。 `DataType`属性发出 HTML 5 `data-` HTML 5 浏览器可以理解的 (读作的数据 dash) 属性。 `DataType`属性不提供任何验证。

`DataType.Date` 不指定显示日期的格式。 默认情况下，根据基于服务器的 CultureInfo 的默认格式显示数据字段。

`DisplayFormat` 特性用于显式指定日期格式：

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`ApplyFormatInEditMode` 设置指定在文本框中显示值以进行编辑时也应用格式。 （你可能不想，对于某些域-例如，货币值，你可能不想在文本框中的货币符号以进行编辑。）

你可以使用`DisplayFormat`属性通过本身，但它通常是使用一个好办法`DataType`还属性。 `DataType`属性传达数据而不是如何在屏幕上呈现其的语义，并提供就不会使用了以下好处`DisplayFormat`:

* 浏览器可以启用 HTML5 功能 （例如以显示一个日历控件、 区域设置相对应的货币符号、 电子邮件链接，某些客户端输入验证，等等。）。

* 默认情况下，浏览器将根据区域设置采用正确的格式呈现数据。

有关详细信息，请参阅[\<输入 > 标记帮助器文档](../../mvc/views/working-with-forms.md#the-input-tag-helper)。

运行应用程序，转到学生索引页，请注意，注册日期不再显示时间。 相同将为真，使用学生模型的任何视图。

![显示日期而无需时间的学生索引页](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>StringLength 属性

你还可以指定数据验证规则和使用属性的验证错误消息。 `StringLength`属性数据库中设置的最大长度，并提供客户端和服务器端 ASP.NET MVC 的验证。 你还可以通过以下方式指定最小字符串长度中此特性，但最小值对数据库架构没有任何影响。

假设你想要确保，用户不超过 50 个字符输入名称。 若要添加此限制，添加`StringLength`特性以`LastName`和`FirstMidName`属性，如下面的示例中所示：

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

`StringLength`属性不会阻止的用户的空白区域输入一个名称。 你可以使用`RegularExpression`要将限制应用到的输入属性。 例如，下面的代码要求的第一个字符是大写且其余的字符是按字母顺序排列：

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z''-'\s]*$")]
```

`MaxLength`属性提供的功能类似于`StringLength`属性但不提供客户端验证。

现在，数据库模型已更改需要在数据库架构更改的方式。 你将使用迁移来更新的架构，而不会丢失你在使用应用程序 UI 已添加到数据库的任何数据。

保存所做的更改并生成项目。 然后在项目文件夹中打开命令窗口并输入以下命令：

```console
dotnet ef migrations add MaxLengthOnNames
```

```console
dotnet ef database update
```

`migrations add`命令会发出警告，可能会发生数据丢失，因为更改使短为两个列的最大长度。  迁移创建名为的文件*\<时间戳 > _MaxLengthOnNames.cs*。 此文件包含中的代码`Up`将更新数据库，以匹配当前数据模型的方法。 `database update`命令运行该代码。

实体框架使用的迁移文件名称的前缀的时间戳以整理迁移。 你可以在运行 update-database 命令中之前, 创建多个迁移，然后所有迁移应用中已创建的顺序。

运行应用程序中，选择**学生**选项卡上，单击**新建**，并输入任一名称超过 50 个字符。 当你单击**创建**，客户端验证显示一条错误消息。

![学生索引页显示的字符串长度错误](complex-data-model/_static/string-length-errors.png)

### <a name="the-column-attribute"></a>列属性

特性还可用于控制如何类和属性映射到数据库。 假设你已使用名称`FirstMidName`的第一个名称字段，因为该字段还可能包含中间名。 但你想要将名为的数据库列`FirstName`，因为将编写针对数据库的即席查询的用户习惯于该名称。 若要使此映射，你可以使用`Column`属性。

`Column`属性指定当创建数据库时，列`Student`映射到表`FirstMidName`属性将被命名为`FirstName`。 换而言之，你的代码引用到`Student.FirstMidName`，数据将来自或在中更新`FirstName`列`Student`表。 如果未指定列名称，系统会提供与属性名相同的名称。

在*Student.cs*文件中，添加`using`语句`System.ComponentModel.DataAnnotations.Schema`并添加到的列名称属性`FirstMidName`属性，如以下突出显示的代码中所示：

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

添加`Column`属性将更改模型后备`SchoolContext`，因此它不会与数据库相匹配。

保存所做的更改并生成项目。 然后在项目文件夹中打开命令窗口并输入以下命令以创建另一个迁移：

```console
dotnet ef migrations add ColumnFirstName
```

```console
dotnet ef database update
```

在**SQL Server 对象资源管理器**，通过双击打开学生表设计器**学生**表。

![在 SSOX 中后迁移的学生表](complex-data-model/_static/ssox-after-migration.png)

应用前两个迁移之前，名称列中的 nvarchar (max) 类型。 它们现在是 nvarchar(50) 和列名称已从 FirstMidName 更改为名字。

> [!Note]
> 如果您尝试编译完成下列部分中创建的所有实体类之前，你可能会收到编译器错误。

## <a name="final-changes-to-the-student-entity"></a>最终学生实体更改

![学生实体](complex-data-model/_static/student-entity.png)

在*Models/Student.cs*，更早版本将你添加的代码替换为下面的代码。 突出显示所做的更改。

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>必需的特性

`Required`特性使名称属性必填的字段。 `Required`属性不需要不可为 null 的类型，如值类型 (DateTime、 int、 double、 float 等。)。 不能为 null 的类型是自动被视为必填字段。

无法删除`Required`属性并将其替换的最小长度参数`StringLength`属性：

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>显示属性

`Display`属性指定文本框的标题应在每个实例 （其除以单词没有空间） 中为"名字"、"姓氏"、"全名"和"注册日期"而不是属性名称。

### <a name="the-fullname-calculated-property"></a>计算的 FullName 属性

`FullName`是返回一个值，通过串联两个其他属性创建一个计算的属性。 因此它具有仅一个 get 访问器，但没有`FullName`列将生成数据库中。

## <a name="create-the-instructor-entity"></a>创建 Instructor 实体

![Instructor 实体](complex-data-model/_static/instructor-entity.png)

创建*Models/Instructor.cs*，模板代码替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

请注意的几个属性都是相同的学生和 Instructor 实体中。 在[实现继承](inheritance.md)本系列教程更高版本，你将重构此代码以消除冗余。

可以将多个属性放在一行上，这样你可以编写`HireDate`属性，如下所示：

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>CourseAssignments 和 OfficeAssignment 导航属性

`CourseAssignments`和`OfficeAssignment`属性是导航属性。

一个教师可以教授任意数量的课程，因此`CourseAssignments`定义为集合。

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

如果导航属性可以具有多个实体，其类型必须是在其中条目可以添加、 删除和更新的列表。  你可以指定`ICollection<T>`或类型，如`List<T>`或`HashSet<T>`。 如果指定`ICollection<T>`，EF 创建`HashSet<T>`默认情况下的集合。

为何它们仅的原因`CourseAssignment`实体进行了解释下面有关多对多关系的部分。

Contoso 大学业务规则状态教师只能有一个 office 最多，因此`OfficeAssignment`属性包含单个 OfficeAssignment 实体 （它可能是不分配任何 office 的情况下为 null）。

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a>创建 OfficeAssignment 实体

![OfficeAssignment 实体](complex-data-model/_static/officeassignment-entity.png)

创建*Models/OfficeAssignment.cs*替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>键属性

没有教师和 OfficeAssignment 实体之间的对零或一一个关系。 相对于其分配给，教师仅存在一个办公室分配，因此其主键也是其外的键与 Instructor 实体。 但是，实体框架无法自动识别 InstructorID 作为此实体的主键因为其名称未遵循 ID 或 classnameID 命名约定。 因此，`Key`属性用于标识作为键：

```csharp
[Key]
public int InstructorID { get; set; }
```

你还可以使用`Key`属性如果实体具有其自己的主键，但你想要提供的名称属性是除 classnameID 或 id。

默认情况下，EF 将密钥视为非数据库生成因为列是标识关系。

### <a name="the-instructor-navigation-property"></a>教师导航属性

Instructor 实体具有为 null`OfficeAssignment`导航属性 （因为教师可能没有一个办公室分配），并且 OfficeAssignment 实体具有非 null`Instructor`导航属性 （因为一个办公室分配无法存在，而一个教师-`InstructorID`是不可为 null)。 当 Instructor 实体具有相关的 OfficeAssignment 实体时，每个实体将具有对另一个在其导航属性的引用。

您可以将`[Required]`属性教师导航属性以指定必须有相关的教师，但不需要这样做是因为`InstructorID`外键 （它也是此表的关键） 是不可为 null。

## <a name="modify-the-course-entity"></a>修改过程实体

![课程实体](complex-data-model/_static/course-entity.png)

在*Models/Course.cs*，更早版本将你添加的代码替换为下面的代码。 突出显示所做的更改。

[!code-csharp[Main](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

课程实体具有外键属性`DepartmentID`它指向相关的部门实体和它具有`Department`导航属性。

实体框架不要求你将外键属性添加到你的数据模型，当你具有相关实体的导航属性时。  EF 自动在数据库中创建外键，在需要的位置和创建[隐藏属性](https://docs.microsoft.com/ef/core/modeling/shadow-properties)它们。 但是，数据模型中具有外键可以使更新更简单、 更高效。 例如，当你提取要编辑的课程实体，部门实体为 null 时，如果你不加载它，当你更新课程实体中，你将需要来首次读取部门实体。 当外键属性`DepartmentID`包含在数据模型中，你无需更新之前提取部门实体。

### <a name="the-databasegenerated-attribute"></a>DatabaseGenerated 属性

`DatabaseGenerated`特性与`None`参数`CourseID`属性指定主键值是由用户而不是由数据库生成。

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

默认情况下，实体框架将假定主键值都由数据库。 在大多数情况下要执行的操作。 但是，对于课程实体，将一个部门，另一个部门，一 2000年系列使用用户指定过程号例如 1000年系列，依此类推。

`DatabaseGenerated`属性还可以用于生成默认值，如在数据库列用于记录的日期的情况下创建或更新行。  有关详细信息，请参阅[生成属性](https://docs.microsoft.com/ef/core/modeling/generated-properties)。

### <a name="foreign-key-and-navigation-properties"></a>外键和导航属性

外键属性和课程实体中的导航属性反映了以下关系：

课程都将分配到一个部门，因此没有`DepartmentID`外键和一个`Department`上面提到的原因的导航属性。

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

课程可以有任意数量的学生注册，因此`Enrollments`导航属性是集合：

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

可能由多个教师讲授课程因此`CourseAssignments`导航属性是集合 (类型`CourseAssignment`解释了[更高版本](#many-to-many-relationships)):

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-the-department-entity"></a>创建部门实体

![部门实体](complex-data-model/_static/department-entity.png)


创建*Models/Department.cs*替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>列属性

前面使用`Column`特性更改列名称映射。 在代码中的部门实体，`Column`属性用于更改 SQL 数据类型映射，以便将将列定义的数据库中使用 SQL Server money 类型：

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

列映射通常不是必需的因为实体框架选择基于你定义的属性的 CLR 类型的相应 SQL Server 数据类型。 CLR`decimal`类型映射到 SQL Server`decimal`类型。 但在这种情况下你知道保存在列中的货币金额和 money 数据类型是更适合。

### <a name="foreign-key-and-navigation-properties"></a>外键和导航属性

外键和导航属性反映了以下关系：

部门可能或可能没有管理员，并且管理员始终是一个教师。 因此`InstructorID`作为外键与 Instructor 实体中，还包括的属性和之后添加一个问号`int`键入标记用于将标记为可为 null 的属性。 导航属性名为`Administrator`但保留 Instructor 实体：

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

部门可能会产生许多课程，因此课程导航属性：

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> 按照约定，实体框架使级联删除对于不可为 null 的外键以及多对多关系。 这可能导致循环的级联删除规则，当你尝试添加迁移时，将导致异常。 例如，如果你未定义 Department.InstructorID 属性为可为 null，EF 会配置一个级联删除规则，以删除部门，这不是你希望能够发生时删除教师。 如果您的业务规则需要`InstructorID`属性不可为 null，你必须使用以下 fluent API 语句若要禁用的关系上的级联删除：
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-the-enrollment-entity"></a>修改注册实体

![注册实体](complex-data-model/_static/enrollment-entity.png)

在*Models/Enrollment.cs*，你添加的代码更早版本替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>外键和导航属性

外键属性和导航属性反映了以下关系：

注册记录已经是单个的课程，因此`CourseID`外键属性和`Course`导航属性：

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

因此，注册记录将适用于单个学生，`StudentID`外键属性和`Student`导航属性：

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>多对多关系

学生和课程，则为实体之间没有多对多关系和注册实体函数为多对多联接表*具有负载*数据库中。 "负载"意味着注册表包含除 （在这种情况下，主键和年级属性） 的联接的表的外键以外的其他数据。

下图显示这些关系中的实体关系图的外观。 (此关系图生成使用实体框架 Power Tools for EF 6.x; 创建关系图不属于本教程，只需使用此处为说明。)

![学生课程多对多关系](complex-data-model/_static/student-course.png)

每个关系行已在其他，，指示一个对多关系一端和星号 （*） 1。

如果注册表没有包括年级信息，它将只需包含两个的外键 CourseID 和 StudentID。 在这种情况下，它将在数据库中是没有负载的多对多联接表 （或纯联接表）。 教师和课程实体具有这种多对多关系，并且你下一步是创建一个实体类能够充当没有负载的联接表。

（隐式联接表的多对多关系，但 EF 核心不 EF 6.x 支持。 有关详细信息，请参阅[EF 核心 GitHub 存储库中的讨论](https://github.com/aspnet/EntityFramework/issues/1368)。) 

## <a name="the-courseassignment-entity"></a>CourseAssignment 实体

![CourseAssignment 实体](complex-data-model/_static/courseassignment-entity.png)

创建*Models/CourseAssignment.cs*替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a>加入实体名称

联接表需要在数据库中为教师课程多对多关系，并且它必须由一个实体集。 很普遍命名联接实体`EntityName1EntityName2`，这样在这种情况下具有`CourseInstructor`。 但是，我们建议你选择的名称。 描述的关系。 数据模型按简单启动和扩展，有无负载联接频繁收到的负载。 如果启动具有描述性的实体名称时，不需要以后更改此名称。 理想情况下，联接实体将业务域中具有其自己自然的 （可能是单个单词） 名称。 例如，丛书客户无法链接通过分级。 为此关系，`CourseAssignment`是更好的选择比`CourseInstructor`。

### <a name="composite-key"></a>复合密钥

由于外键不是可以为 null 并且它们一起唯一地标识表的每一行，则无需单独的主键。 *InstructorID*和*CourseID*属性应充当复合主键。 标识到 EF 的复合主键的唯一方法是使用*fluent API* （它不能通过使用属性来完成）。 你将看到如何在下一节中配置的复合主键。

复合密钥可确保该虽然都有一个过程中，以及一个教师的多个行的多个行，不能具有多个行的同一教师和过程。 `Enrollment`联接实体定义其自己的主键，因此可能会出现这种重复项。 若要防止此类重复项，你无法上的外键字段，添加一个唯一索引，或配置`Enrollment`带有复合主键类似于`CourseAssignment`。 有关详细信息，请参阅[索引](https://docs.microsoft.com/ef/core/modeling/indexes)。

## <a name="update-the-database-context"></a>更新数据库上下文

添加以下突出显示的代码*Data/SchoolContext.cs*文件：

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

此代码将添加新的实体，并配置 CourseAssignment 实体的复合主键。

## <a name="fluent-api-alternative-to-attributes"></a>属性 fluent API 替代方法

中的代码`OnModelCreating`方法`DbContext`类使用*fluent API*配置 EF 行为。 API 称为"fluent"，因为它通常用于通过连接到单个语句中，从如此示例所示的一系列方法调用一起[EF 核心文档](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration):

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

在本教程中，仅为不能具有属性的数据库映射使用 fluent API。 但是，你可以使用 fluent API 指定的格式、 验证和映射规则，你可以通过使用特性执行大多数。 某些属性，如`MinimumLength`不能使用 fluent API 应用。 如前所述，`MinimumLength`不会更改架构，它仅适用于客户端和服务器端验证规则。

一些开发人员更愿意使用 fluent API 以独占方式，以便它们能够获得它们的实体类"清理"。 如果你想，并且有几个仅可由使用 fluent API 中完成的自定义项，可以混合属性和 fluent API，但建议的做法通常是选择这两种方法之一，并使用该一致地尽可能多地。 不要使用这两者时，请注意，每当存在冲突，则 Fluent API 将覆盖属性。

有关与 fluent API 的特性的详细信息，请参阅[方法配置](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration)。

## <a name="entity-diagram-showing-relationships"></a>关系图显示关系的实体

下图显示为已完成的 School 模型的实体框架 Power 工具创建的关系图。

![实体关系图](complex-data-model/_static/diagram.png)

除了一个对多关系线 (1 到\*)，你可以在此处看到教师和 OfficeAssignment 实体和零-或--一对多关系行之间的对零或一一个关系行 (1 对 0..1) (0..1 对 *) 之间教师和部门实体。

## <a name="seed-the-database-with-test-data"></a>种子使用测试数据的数据库

中的代码替换*Data/DbInitializer.cs*文件替换为以下代码，以便为你已创建的新实体提供种子数据。

[!code-csharp[Main](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

正如你看到的第一个教程中，大多数的此代码将只需创建新的实体对象，并将示例数据加载到所需的测试的属性。 请注意如何处理的多对多关系： 该代码通过创建中的实体创建关系`Enrollments`和`CourseAssignment`加入实体集。

## <a name="add-a-migration"></a>添加迁移

保存所做的更改并生成项目。 然后在项目文件夹中打开命令窗口并输入`migrations add`命令 （不执行操作的更新 database 命令尚未）：

```console
dotnet ef migrations add ComplexDataModel
```

获取有关可能造成数据丢失的警告。

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

如果你尝试运行`database update`此时命令 （不执行此操作尚未），则会遇到以下错误：

> ALTER TABLE 语句与外键约束"FK_dbo 冲突。Course_dbo。Department_DepartmentID"。 冲突发生于数据库"ContosoUniversity"表"dbo。部门"，列 DepartmentID。

有时时执行现有数据的迁移，你需要将存根 （stub） 数据插入数据库以满足外键约束。 生成的代码`Up`方法将不可为 null 的 DepartmentID 外键添加到过程表。 如果已有行课程表中的代码运行时，`AddColumn`操作失败，因为 SQL Server 并不知道要放入中不能为 null 的列的值。 对于本教程将运行迁移，在新的数据库，但在生产应用程序将需要进行迁移处理现有数据，因此下面的说明展示一个如何执行该操作的示例。

若要使这种迁移处理你需要更改代码以提供默认值的新列的现有数据并创建一个存根 （stub） 部门名为"Temp"使其作为默认部门。 因此，现有的课程行将所有与"Temp"部门后`Up`方法运行。

* 打开*{timestamp}_ComplexDataModel.cs*文件。 

* 注释掉的将 DepartmentID 列添加到过程表的代码行。

  [!code-csharp[Main](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* 创建将 Department 表的代码后面添加以下突出显示的代码：

  [!code-csharp[Main](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

在生产应用程序，你将编写代码或脚本添加部门行并与新的部门行相关课程行。 您然后不再需要"Temp"部门或 Course.DepartmentID 列上的默认值。

保存所做的更改并生成项目。

## <a name="change-the-connection-string-and-update-the-database"></a>更改连接字符串，并更新数据库

你现在有新的代码`DbInitializer`将新实体的种子数据添加到空数据库的类。 若要使 EF 创建新的空数据库，更改中的连接字符串中的数据库的名称*appsettings.json*到 ContosoUniversity3 或尚未在所使用的计算机使用的一些其他名称。

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

保存到你更改*appsettings.json*。

> [!NOTE]
> 作为对不断变化的数据库名称的替代方法，您可以删除数据库。 使用**SQL Server 对象资源管理器**(SSOX) 或`database drop`CLI 命令：
> ```console
> dotnet ef database drop
> ```

已更改的数据库名称或删除了数据库后，运行`database update`命令在命令窗口中执行迁移。

```console
dotnet ef database update
```

运行应用，以导致`DbInitializer.Initialize`方法运行并填充新数据库。

更早版本，就像在 SSOX 中打开数据库并展开**表**节点以查看是否已创建的所有表。 (如果仍有 SSOX 打开从较早的时间，请单击**刷新**按钮。)

![在 SSOX 中的表](complex-data-model/_static/ssox-tables.png)

运行应用，以触发设定种子数据库初始值设定项代码。

右键单击**CourseAssignment**表，然后选择**查看数据**以验证它中有数据。

![在 SSOX 中 CourseAssignment 数据](complex-data-model/_static/ssox-ci-data.png)

## <a name="summary"></a>摘要

现在将具有更复杂的数据模型和相应的数据库。 在以下教程中，你将了解有关如何访问相关的数据的详细信息。

>[!div class="step-by-step"]
[上一页](migrations.md)
[下一页](read-related-data.md)  
