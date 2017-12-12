---
title: "与 EF 核心-数据模型-5 8 razor 页"
author: rick-anderson
description: "在本教程中添加更多实体和关系，并通过指定格式设置、 验证和数据库的映射规则自定义数据模型。"
keywords: "ASP.NET 核心，实体框架核心数据批注"
ms.author: riande
manager: wpickett
ms.date: 10/25/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: c2761f79fa4836d29541526782969bb0fd47778b
ms.sourcegitcommit: 6e46abd65973dea796d364a514de9ec2e3e1c1ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2017
---
# <a name="creating-a-complex-data-model---ef-core-with-razor-pages-tutorial-5-of-8"></a>创建复杂的数据模型的 EF 核心 Razor 页教程 (5 的 8)

通过[Tom Dykstra](https://github.com/tdykstra)和[Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[about the series](../../includes/RP-EF/intro.md)]

与基本数据模型的三个实体组成合作，前面的教程。 在本教程中：

* 添加更多实体和关系。
* 通过指定格式设置、 验证和数据库的映射规则自定义数据模型。

已完成的数据模型的实体类是在下图中所示：

![实体关系图](complex-data-model/_static/diagram.png)

如果你遇到无法解决的问题，请下载[对此阶段已完成应用程序](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part5-complex)。

## <a name="customize-the-data-model-with-attributes"></a>自定义数据模型具有属性

在此部分中，数据模型是使用自定义属性。

### <a name="the-datatype-attribute"></a>数据类型属性

学生页当前显示的注册日期的时间。 通常情况下，日期字段显示仅显示日期而非时间。

更新*Models/Student.cs*替换为以下突出显示的代码：

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

[DataType](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1)属性指定比数据库内部类型更具体的数据类型。 在此情况下应显示仅显示日期，不的日期和时间。 [DataType 枚举](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1)提供对于许多数据类型，例如日期、 时间、 电话号码、 货币、 电子邮件地址，等等。`DataType`属性还可以启用该应用程序自动提供特定类型的功能。 例如: 

* `mailto:`为自动创建链接`DataType.EmailAddress`。
* 为提供的日期选择器`DataType.Date`大多数浏览器中。

`DataType`属性发出 HTML 5 `data-` HTML 5 浏览器使用的 (读作的数据 dash) 属性。 `DataType`属性是否提供验证。

`DataType.Date` 不指定显示日期的格式。 默认情况下，日期字段显示根据基于服务器的默认格式[CultureInfo](https://docs.microsoft.com/aspnet/core/fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)。

`DisplayFormat` 特性用于显式指定日期格式：

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`ApplyFormatInEditMode`设置指定的格式设置也应该将应用到编辑 UI。 某些字段不应使用`ApplyFormatInEditMode`。 例如，货币符号应通常不会显示在编辑文本框中。

`DisplayFormat`属性可由本身。 它通常是使用一个好办法`DataType`特性与`DisplayFormat`属性。 `DataType`属性传达数据而不是如何在屏幕上呈现其的语义。 `DataType`属性提供中均不提供了以下好处`DisplayFormat`:

* 浏览器可以启用 HTML5 功能。 例如，显示一个日历控件、 区域设置相对应的货币符号、 电子邮件链接，客户端进行输入的验证，等等。
* 默认情况下，浏览器呈现使用基于的区域设置的正确格式的数据。

有关详细信息，请参阅[\<输入 > 标记帮助器文档](xref:mvc/views/working-with-forms#the-input-tag-helper)。

运行应用。 导航到学生索引页。 不再显示时间。 每个视图中使用`Student`模型显示无时间的日期。

![显示日期而无需时间的学生索引页](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>StringLength 属性

具有属性，可以指定数据验证规则和验证错误消息。 [StringLength](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1)属性指定数据字段中允许的字符的最小和最大长度。 `StringLength`特性还提供对客户端和服务器端验证。 最小值对数据库架构没有任何影响。

更新`Student`模型替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

前面的代码限制为不超过 50 个字符的名称。 `StringLength`属性不会阻止用户从空白区域输入一个名称。 [正则表达式](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1)特性用于将限制应用到的输入。 例如，下面的代码要求的第一个字符是大写且其余的字符是按字母顺序排列：

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z''-'\s]*$")]
```

运行应用程序：

* 导航到学生页。
* 选择**新建**，并输入的名称超过 50 个字符。
* 选择**创建**，客户端验证显示一条错误消息。

![学生索引页显示的字符串长度错误](complex-data-model/_static/string-length-errors.png)

在**SQL Server 对象资源管理器**(SSOX)，通过双击打开学生表设计器**学生**表。

![在 SSOX 中迁移前的学生表](complex-data-model/_static/ssox-before-migration.png)

上图显示的架构`Student`表。 名称字段具有类型`nvarchar(MAX)`由于尚未在数据库上运行迁移。 在本教程中稍后运行迁移时，名称字段将成为`nvarchar(50)`。

### <a name="the-column-attribute"></a>列属性

属性可以控制如何类和属性映射到数据库。 在本部分中，`Column`属性用于将映射的名称`FirstMidName`属性设置为"FirstName"在数据库中。

创建数据库后，将在模型上的属性名称用于列名称 (时除外`Column`属性，则使用)。

`Student`模型使用`FirstMidName`的第一个名称字段，因为该字段还可能包含中间名。

更新*Student.cs*替换为以下突出显示代码文件：

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

进行上述更改后，`Student.FirstMidName`在应用程序映射到`FirstName`列`Student`表。

添加`Column`属性将更改模型后备`SchoolContext`。 模型后备`SchoolContext`不再与数据库相匹配。 如果在应用迁移之前运行该应用，则会生成以下异常：

```SQL
SqlException: Invalid column name 'FirstName'.
```
更新数据库：

* 生成项目。
* 在项目文件夹中打开命令窗口。 输入以下命令以创建新的迁移和更新数据库：

    ```console
    dotnet ef migrations add ColumnFirstName
    dotnet ef database update
    ```

`dotnet ef migrations add ColumnFirstName`命令将生成以下警告消息：

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

会生成警告，因为名称字段现在是限制为 50 个字符。 如果在数据库中的名称必须超过 50 个字符，到最后一个字符 51 将会丢失。

* 测试应用程序。

在 SSOX 中打开学生表：

![在 SSOX 中后迁移的学生表](complex-data-model/_static/ssox-after-migration.png)

类型的迁移已应用之前，已名称列[nvarchar (max)](https://docs.microsoft.com/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)。 名称列现`nvarchar(50)`。 列名称已更改，不再`FirstMidName`到`FirstName`。

> [!Note]
> 在以下部分中，某些阶段时生成的应用程序会生成编译器错误。 说明中指定何时生成该应用。

## <a name="student-entity-update"></a>学生实体更新

![学生实体](complex-data-model/_static/student-entity.png)

更新*Models/Student.cs*替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>必需的特性

`Required`特性使名称属性必填的字段。 `Required`属性不需要不可为 null 的类型，如值类型 (`DateTime`， `int`，`double`等。)。 不能为 null 的类型是自动被视为必填字段。

`Required`属性无法替换中的最小长度参数`StringLength`属性：

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>显示属性

`Display`属性指定文本框的标题应为"名字"、"姓氏"、"全名"和"注册日期"。 默认标题不包含空格除以词，例如"Lastname。"

### <a name="the-fullname-calculated-property"></a>计算的 FullName 属性

`FullName`是返回一个值，通过串联两个其他属性创建一个计算的属性。 `FullName`不能设置，它具有仅一个 get 访问器。 不`FullName`在数据库中创建列。

## <a name="create-the-instructor-entity"></a>创建 Instructor 实体

![Instructor 实体](complex-data-model/_static/instructor-entity.png)

创建*Models/Instructor.cs*替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

请注意的几个属性都在相同`Student`和`Instructor`实体。 在更高版本在本系列中的实现继承教程中，此代码将重构以消除冗余。

多个属性可在一行上。 `HireDate`属性可以进行编写，如下所示：

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>CourseAssignments 和 OfficeAssignment 导航属性

`CourseAssignments`和`OfficeAssignment`属性是导航属性。

一个教师可以教授任意数量的课程，因此`CourseAssignments`定义为集合。

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

如果导航属性包含多个实体：

* 它必须是列表类型，可以添加、 删除和更新条目。

导航属性类型包括：

* `ICollection<T>`
*  `List<T>`
*  `HashSet<T>`

如果`ICollection<T>`EF 核心创建的指定`HashSet<T>`默认情况下的集合。

`CourseAssignment`实体部分所述对多对多关系。

Contoso 大学业务规则，一个教师可以有最多一个 office 的状态。 `OfficeAssignment`属性包含单个`OfficeAssignment`实体。 `OfficeAssignment`如果没有 office 分配，为 null。

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a>创建 OfficeAssignment 实体

![OfficeAssignment 实体](complex-data-model/_static/officeassignment-entity.png)

创建*Models/OfficeAssignment.cs*替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>键属性

`[Key]`属性用于标识属性作为的主键 (PK) 时的属性名称而非 classnameID 或 id。

没有之间的对零或一一个关系`Instructor`和`OfficeAssignment`实体。 相对于分配给教师仅存在一个办公室分配。 `OfficeAssignment` PK 是还其外键 (FK) 到`Instructor`实体。 无法自动识别 EF 核心`InstructorID`作为的 PK`OfficeAssignment`因为：

* `InstructorID`未遵循 ID 或 classnameID 命名约定。

因此，`Key`属性用于标识`InstructorID`PK 作为：

```csharp
[Key]
public int InstructorID { get; set; }
```

默认情况下，EF 核心将密钥视为非数据库生成因为列是标识关系。

### <a name="the-instructor-navigation-property"></a>教师导航属性

`OfficeAssignment`导航属性进行`Instructor`实体是可以为 null 因为：

* 引用类型 （如类都可以为 null）。
* 一个教师可能没有一个办公室分配。


`OfficeAssignment`实体具有非 null`Instructor`导航属性因为：

* `InstructorID`是不可为 null。
* 一个办公室分配不存在一个教师不存在。

当`Instructor`实体具有相关`OfficeAssignment`实体，每个实体都有对另一个在其导航属性的引用。

`[Required]`特性可以应用到`Instructor`导航属性：

```csharp
[Required]
public Instructor Instructor { get; set; }
```

前面的代码指定必须是相关的教师。 前面的代码是不必要因为`InstructorID`外键 （它也是在 PK） 是不可为 null。

## <a name="modify-the-course-entity"></a>修改过程实体

![课程实体](complex-data-model/_static/course-entity.png)

更新*Models/Course.cs*替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

`Course`实体具有外键 (FK) 属性`DepartmentID`。 `DepartmentID`指向相关`Department`实体。 `Course`实体具有`Department`导航属性。

该模型具有相关实体的导航属性时，EF 核心不会为数据模型需要 FK 属性。

EF 核心会在需要时，会自动将 FKs 创建数据库中。 EF 核心创建[隐藏属性](https://docs.microsoft.com/ef/core/modeling/shadow-properties)的自动创建 FKs。 更简单、 更高效，FK 采用数据模型可以进行更新。 例如，考虑一个模型其中 FK 属性`DepartmentID`是*不*包含。 当过程实体被提取编辑：

* `Department`实体是未显式加载的情况下为 null。
* 若要更新过程实体，`Department`必须先提取实体。

当 FK 属性`DepartmentID`包含在数据模型中，没有必要提取`Department`之前更新的实体。

### <a name="the-databasegenerated-attribute"></a>DatabaseGenerated 属性

`[DatabaseGenerated(DatabaseGeneratedOption.None)]`属性指定在 PK 是应用程序提供而不是由数据库生成。

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

默认情况下，EF 核心假定 PK 值都由数据库。 DB 生成 PK 值通常是最佳的方法。 有关`Course`实体，用户指定 PK. 例如，如数学部门的 1000年系列值，则将英语部门 2000年系列课程数。

`DatabaseGenerated`属性还可以用于生成默认值。 例如，数据库可以自动生成要记录的创建或更新行的日期的日期字段。 有关详细信息，请参阅[生成属性](https://docs.microsoft.com/ef/core/modeling/generated-properties)。

### <a name="foreign-key-and-navigation-properties"></a>外键和导航属性

外键 (FK) 属性和中的导航属性`Course`实体反映了以下关系：

课程都将分配到一个部门，因此没有`DepartmentID`FK 和`Department`导航属性。

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

课程可以有任意数量的学生注册，因此`Enrollments`导航属性是集合：

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

可能由多个教师讲授课程因此`CourseAssignments`导航属性是集合：

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment`解释了[更高版本](#many-to-many-relationships)。

## <a name="create-the-department-entity"></a>创建部门实体

![部门实体](complex-data-model/_static/department-entity.png)

创建*Models/Department.cs*替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>列属性

以前`Column`属性用于更改列名称映射。 中的代码`Department`实体，`Column`属性用于更改 SQL 数据类型映射。 `Budget` DB 中使用 SQL Server money 类型定义列：

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

列映射则通常不需要。 EF 核心通常选择基于属性的 CLR 类型的相应 SQL Server 数据类型。 CLR`decimal`类型映射到 SQL Server`decimal`类型。 `Budget`为货币，是更适合于货币 money 数据类型。

### <a name="foreign-key-and-navigation-properties"></a>外键和导航属性

FK 和导航属性反映了以下关系：

* 部门可能或可能没有管理员。
* 管理员始终是一个教师。 因此`InstructorID`属性是作为到 FK 包含`Instructor`实体。

导航属性名为`Administrator`但保留`Instructor`实体：

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

在前面的代码问号 （？） 指定属性可以为 null。

部门可能会产生许多课程，因此课程导航属性：

```csharp
public ICollection<Course> Courses { get; set; }
```

注意： 按照约定，EF 核心使级联删除对于不可为 null FKs 以及多对多关系。 级联 delete 可能会导致循环的级联删除规则。 循环的级联删除规则原因添加迁移时，此异常。

例如，如果`Department.InstructorID`属性未定义为可以为 null:

* EF 核心配置一个级联删除规则，以删除部门时删除教师。
* 删除教师删除部门时并不是预期的行为。

如果业务规则需要`InstructorID`属性为不可为 null，请使用以下 fluent API 语句：

 ```csharp
 modelBuilder.Entity<Department>()
    .HasOne(d => d.Administrator)
    .WithMany()
    .OnDelete(DeleteBehavior.Restrict)
 ```

前面的代码中禁用部门教师关系上的级联删除。

## <a name="update-the-enrollment-entity"></a>更新注册实体

注册记录适用于一个执行的一名学生课程。

![注册实体](complex-data-model/_static/enrollment-entity.png)

更新*Models/Enrollment.cs*替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>外键和导航属性

FK 属性和导航属性反映了以下关系：

注册记录为一个过程中，因此`CourseID`FK 属性和`Course`导航属性：

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

注册记录是一名学生的因此`StudentID`FK 属性和`Student`导航属性：

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>多对多关系

没有之间的多对多关系`Student`和`Course`实体。 `Enrollment`实体充当多对多联接表*具有负载*数据库中。 "使用负载"意味着`Enrollment`表包含更多数据除了 FKs 联接的表 (在此情况下，在 PK 和`Grade`)。

下图显示这些关系中的实体关系图的外观。 (此关系图生成使用 EF Power Tools for EF 6.x。 创建关系图并不在本教程的一部分。）

![学生课程多对多关系](complex-data-model/_static/student-course.png)

每个关系行已在其他，，指示一个对多关系一端和星号 （*） 1。

如果`Enrollment`表没有包括年级信息，它只需包含两个 FKs (`CourseID`和`StudentID`)。 没有负载的多对多联接表有时称为纯联接表 (PJT)。

`Instructor`和`Course`实体具有使用纯联接表的多对多关系。

注意： EF 6.x 支持隐式联接表的多对多关系，但 EF 核心却没有。 有关详细信息，请参阅[多对多关系在 EF 核心 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)。

## <a name="the-courseassignment-entity"></a>CourseAssignment 实体

![CourseAssignment 实体](complex-data-model/_static/courseassignment-entity.png)

创建*Models/CourseAssignment.cs*替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a>教师课程

![教师课程多对多](complex-data-model/_static/courseassignment.png)

教师课程多对多关系：

* 要求必须由一个实体集表示一个联接表。
* 是纯联接表 （表而无需负载）。

很普遍命名联接实体`EntityName1EntityName2`。 例如，使用此模式对教师课程联接表是`CourseInstructor`。 但是，我们建议使用描述的关系的名称。

数据模型按简单启动和增长。 否负载联接 (PJTs) 经常发展以包括负载。 通过启动具有描述性的实体名称，名称不需要联接表发生更改时更改。 理想情况下，联接实体将业务域中具有其自己自然的 （可能是单个单词） 名称。 例如，无法与联接实体调用分级链接丛书和客户。 对于教师课程多对多关系，`CourseAssignment`是优于`CourseInstructor`。

### <a name="composite-key"></a>复合密钥

FKs 不可以为 null。 在两个 FKs `CourseAssignment` (`InstructorID`和`CourseID`) 一起唯一标识的每一行`CourseAssignment`表。 `CourseAssignment`不需要专用的 PK. `InstructorID`和`CourseID`属性用作复合 PK. 若要指定到 EF 核心的复合 Pk 的唯一方法是使用*fluent API*。 下一节演示如何配置复合 PK.

复合密钥可确保：

* 一个课程允许多个行。
* 一个教师允许多个行。
* 不允许为相同的教师和过程的多个行。

`Enrollment`联接实体定义其自己的主键，因此可能会出现这种重复项。 若要防止此类重复：

* 上的 FK 字段中，添加一个唯一索引或
* 配置`Enrollment`带有复合主键类似于`CourseAssignment`。 有关详细信息，请参阅[索引](https://docs.microsoft.com/ef/core/modeling/indexes)。

## <a name="update-the-db-context"></a>更新数据库上下文

添加以下突出显示的代码*Data/SchoolContext.cs*:

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

前面的代码中添加新的实体，并配置`CourseAssignment`实体的复合 PK.

## <a name="fluent-api-alternative-to-attributes"></a>属性 fluent API 替代方法

`OnModelCreating`方法在前面的代码使用*fluent API*配置 EF 核心行为。 API 称为"fluent"，因为它通常用于通过连接一系列组合在一起成为单个语句的方法调用。 [下面的代码](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration)是 fluent API 的示例：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

在本教程中，仅为不能具有属性完成的 DB 映射使用 fluent API。 但是，fluent API 可以指定的格式、 验证和可通过属性的映射规则的大多数。

某些属性，如`MinimumLength`不能使用 fluent API 应用。 `MinimumLength`不会更改架构，它仅适用于最小长度验证规则。

一些开发人员更愿意使用 fluent API 以独占方式，以便它们能够获得它们的实体类"清理"。 可以混合属性和 fluent API。 有一些可仅可通过 fluent API （在指定组合主键） 的配置。 有一些只能具有属性的配置 (`MinimumLength`)。 使用 fluent API 或属性的建议的做法：

* 选择这两种方法之一。
* 使用一致地尽可能多选的方法。

某些特性在此教程适用于：

* 仅验证 (例如， `MinimumLength`)。
* EF 核心配置 (例如， `HasKey`)。
* 验证和 EF 核心配置 (例如， `[StringLength(50)]`)。

有关与 fluent API 的特性的详细信息，请参阅[方法配置](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration)。

## <a name="entity-diagram-showing-relationships"></a>关系图显示关系的实体

下图显示关系图用于 EF Power Tools 创建的已完成的 School 模型。

![实体关系图](complex-data-model/_static/diagram.png)

上图中显示：

* 一个对多关系的多个行 (1 到\*)。
* 对零或一一个关系行 (1 对 0..1) 之间`Instructor`和`OfficeAssignment`实体。
* 零-或--一对多关系行 (0..1 对 *) 之间`Instructor`和`Department`实体。

## <a name="seed-the-db-with-test-data"></a>种子使用测试数据的数据库

更新中的代码*Data/DbInitializer.cs*:

[!code-csharp[Main](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

前面的代码中为新实体提供了种子数据。 大多数的此代码创建新的实体对象，并加载示例数据。 示例数据用于测试。 前面的代码将创建以下多对多关系：

* `Enrollments`
* `CourseAssignment`

注意： [EF 核心 2.1](https://github.com/aspnet/EntityFrameworkCore/wiki/Roadmap)将支持[数据种子设定](https://github.com/aspnet/EntityFrameworkCore/issues/629)。

## <a name="add-a-migration"></a>添加迁移

生成项目。 在项目文件夹中打开命令窗口并输入以下命令：

```console
dotnet ef migrations add ComplexDataModel
```

前一个命令显示有关可能造成数据丢失的警告。

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

如果`database update`运行命令，则会生成以下错误：

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

迁移运行时与现有数据，可能有不能满足与正在退出的数据的 FK 约束。 对于本教程中，创建新数据库，因此没有任何 FK 约束冲突。 请参阅[修复与旧的数据的外键约束](#fk)有关如何解决 FK 冲突在当前数据库上的说明。

## <a name="change-the-connection-string-and-update-the-db"></a>更改连接字符串并更新数据库

中已更新的代码`DbInitializer`添加新的实体的种子数据。 若要强制 EF 核心以创建新的空数据库：

* 更改中的数据库连接字符串名称*appsettings.json*到 ContosoUniversity3。 新名称必须是未在计算机已使用的名称。

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
      },
    ```

* 或者，删除数据库使用：

    * **SQL Server 对象资源管理器**(SSOX)。
    * `database drop` CLI 命令：

   ```console
   dotnet ef database drop
   ```

运行`database update`命令窗口中：

```console
dotnet ef database update
```

前一个命令运行所有迁移。

运行应用。 运行应用程序运行`DbInitializer.Initialize`方法。 `DbInitializer.Initialize`填充新数据库。

在 SSOX 中打开数据库：

* 展开**表**节点。 显示创建的表。
* 如果 SSOX 以前已打开，请单击**刷新**按钮。

![在 SSOX 中的表](complex-data-model/_static/ssox-tables.png)

检查**CourseAssignment**表：

* 右键单击**CourseAssignment**表，然后选择**查看数据**。
* 验证**CourseAssignment**表包含数据。

![在 SSOX 中 CourseAssignment 数据](complex-data-model/_static/ssox-ci-data.png)

<a name="fk"></a>

## <a name="fixing-foreign-key-constraints-with-legacy-data"></a>与旧数据修复外键约束

本部分是可选的。

迁移运行时与现有数据，可能有不能满足与正在退出的数据的 FK 约束。 使用生产数据，必须采取步骤来将现有数据迁移。 本部分提供修复 FK 约束冲突的一个示例。 不进行不备份这些代码更改。 不进行这些代码更改，如果在完成上一节，并更新数据库。

*{Timestamp}_ComplexDataModel.cs*文件包含以下代码：

[!code-csharp[Main](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

前面的代码将添加不可为 null`DepartmentID`到 FK`Course`表。 从以前的教程，DB 包含中的行`Course`，因此无法迁移通过更新该表。

若要使`ComplexDataModel`使用现有数据迁移工作：

* 更改代码以提供新的列 (`DepartmentID`) 默认值。
* 创建名为"Temp"使其作为默认部门的假部门。

### <a name="fix-the-foreign-key-constraints"></a>修复外键约束

更新`ComplexDataModel`类`Up`方法：

* 打开*{timestamp}_ComplexDataModel.cs*文件。
* 注释掉的添加的代码行`DepartmentID`列`Course`表。

[!code-csharp[Main](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

添加以下突出显示的代码。 新的代码会进入后`.CreateTable( name: "Department"`块：[!code-csharp[Main](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

与前面的更改，现有`Course`将与"Temp"部门后相关行`ComplexDataModel``Up`方法运行。

生产应用，则应该：

* 包括代码或脚本添加`Department`行以及相关`Course`对新的行`Department`行。
* 不会使用"Temp"部门或的默认值为`Course.DepartmentID `。

下一步教程介绍如何相关的数据。

>[!div class="step-by-step"]
[上一页](xref:data/ef-rp/migrations)
[下一页](xref:data/ef-rp/read-related-data)