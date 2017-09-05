---
title: "ASP.NET 核心 MVC 与 EF 核心-继承-9 的 10"
author: tdykstra
description: "本教程将演示如何在数据模型中，ASP.NET Core 应用程序中使用实体框架核心实现继承。"
keywords: "ASP.NET 核心，实体框架核心继承"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 41dc0db7-6f17-453e-aba6-633430609c74
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/inheritance
ms.openlocfilehash: 3c86dea145d2d4dec10c77e008f511cfe67975f9
ms.sourcegitcommit: 4e84d8bf5f404bb77f3d41665cf7e7374fc39142
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2017
---
# <a name="inheritance---ef-core-with-aspnet-core-mvc-tutorial-9-of-10"></a>继承的 EF 内核，它们有 ASP.NET 核心 MVC 教程 (9 的 10)

通过[Tom Dykstra](https://github.com/tdykstra)和[Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso 大学示例 web 应用程序演示如何创建使用实体框架核心和 Visual Studio 的 ASP.NET 核心 MVC web 应用程序。 有关教程系列的信息，请参阅[序列中的第一个教程](intro.md)。

在前面的教程，你可以处理并发异常。 本教程将演示如何在数据模型中实现继承。

在面向对象编程中，你可以使用继承便于代码重用。 本教程中，你将更改`Instructor`和`Student`类，以使其从中派生`Person`基本类，其中包含属性，如`LastName`所共有的教师和学生。 不会添加或更改任何 web 页面，但你将更改某些代码并将在数据库中自动反映这些更改。

## <a name="options-for-mapping-inheritance-to-database-tables"></a>将继承映射到数据库表的选项

`Instructor`和`Student`School 数据模型中的类具有相同的多个属性：

![学生和教师类](inheritance/_static/no-inheritance.png)

假设你想要消除冗余代码由共享的属性以`Instructor`和`Student`实体。 或者，你想要编写可以设置名称格式而无需管理名称来自一个教师或学生的服务。 你可以创建`Person`基类仅包含那些共享属性，然后进行`Instructor`和`Student`类都继承自该基类，如下面的插图中所示：

![学生和教师从人员类派生的类](inheritance/_static/inheritance.png)

有几种方法无法在数据库中表示此继承结构。 你可以包括有关学生和单个表中的教师信息 Person 表。 某些列可能仅适用于教师 （雇用日期) 中，某些仅向学生 (EnrollmentDate)，一些为这两个 (LastName、 FirstName)。 通常情况下，你必须以指示哪种类型的每一行代表一个鉴别器列。 例如，鉴别器列可能有"教师"教师和"学生"学生版。

![表每个层次结构示例](inheritance/_static/tph.png)

从单个数据库表生成的实体继承结构的此模式称为表每个层次结构 (TPH) 继承。

一种替代方法是使看上去更像继承结构数据库。 例如，你可以只有 Person 表中的名称字段，并拥有单独教师和学生表，其中的日期字段。

![每种类型一个表继承](inheritance/_static/tpt.png)

使数据库表，以便每个实体类的此模式称为每类型 (TPT) 继承的表。

还有另一种方法是将所有的非抽象类型映射到单个表。 类，包括继承的属性的所有属性将都映射到相应的表的列。 此模式称为表每个具体类 (TPC) 继承。 如果你实施的人员、 学生和教师类的 TPC 继承，如前面所示，Student 与教师表如下实现继承与以前后没有什么不同。

TPC 和 TPH 继承模式通常提供更好的性能比 TPT 继承模式，因为 TPT 模式可能会导致复杂的联接查询。

本教程演示如何实现 TPH 继承。 TPH 是实体框架核心支持一个唯一的继承模式。  你将执行的操作是创建`Person`类中，更改`Instructor`和`Student`类派生自`Person`，添加到新的类`DbContext`，并创建迁移。

> [!TIP] 
> 请考虑进行以下更改之前保存项目的副本。  然后你会遇到问题，也需要从头开始，如果它将更轻松地从已保存的项目，而不是反转完成本教程中的步骤或正在启动回整个序列的开头。

## <a name="create-the-person-class"></a>创建 Person 类

在 Models 文件夹中，创建 Person.cs 和模板代码替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Models/Person.cs)]

## <a name="make-student-and-instructor-classes-inherit-from-person"></a>将 Student 与教师从人员继承的类

在*Instructor.cs*、 从 Person 类派生教师类和删除密钥和名称字段。 代码将类似于以下示例：

[!code-csharp[Main](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

进行中的相同更改*Student.cs*。

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-the-person-entity-type-to-the-data-model"></a>将 Person 实体类型添加到数据模型

添加到 Person 实体类型*SchoolContext.cs*。 突出显示新行。

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

这是所有实体框架所需配置每个层次结构一个表继承。 当你将看到更新数据库时，它将具有代替 Student 与教师表 Person 表。

## <a name="create-and-customize-migration-code"></a>创建和自定义迁移代码

保存所做的更改并生成项目。 然后在项目文件夹中打开命令窗口并输入以下命令：

```console
dotnet ef migrations add Inheritance
```

不运行`database update`尚未命令。 该命令将导致丢失数据，因为它将删除教师表并将学生表重命名为 Person。 你需要提供自定义代码来保留现有数据。

打开*迁移 /\<时间戳 > _Inheritance.cs*和替换`Up`方法替换为以下代码：

[!code-csharp[Main](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

此代码将负责以下数据库更新任务：

* 删除外键约束和指向学生表的索引。

* 重命名为人员教师表，并使它来存储学生数据所需的更改：

* 将可以为 null EnrollmentDate 添加学生版。

* 添加鉴别器列来指示行是否适用于一名学生或一个教师。

* 使雇用日期可以为 null，因为学生行不会产生雇佣日期。

* 添加临时的字段，将用于更新指向学生的外键。 当将学生复制到 Person 表他们将获得新的主键值。

* 将数据从 Person 表到学生表。 这将导致学生以分配新的主键值。

* 修复了指向学生的外键值。

* 重新创建外键约束和索引，现在将它们指向 Person 表。

（如果你在作为主键类型使用而不是整数的 GUID，学生主键值不需要更改，并且这些步骤的几个可能已被省略。）

运行`database update`命令：

```console
dotnet ef database update
```

(在生产系统中会使对相应更改`Down`在你曾必须使用，以返回到以前的数据库版本的情况下的方法。 本教程中你将不使用`Down`方法。)

> [!NOTE] 
> 就可以在具有现有数据的数据库中进行架构更改时，发生其他错误。 如果您无法解析的迁移错误，你可以更改连接字符串中的数据库名称，或删除数据库。 与新数据库，若要迁移，没有数据和更新数据库命令是更有可能完成且未发生错误。 若要删除此数据库，使用 SSOX 或运行`database drop`CLI 命令。

## <a name="test-with-inheritance-implemented"></a>使用继承实现进行测试

运行站点并尝试各种页面。 一切相同像以前一样。

在**SQL Server 对象资源管理器**，展开**数据连接/SchoolContext**然后**表**，并查看已被取代学生和教师表Person 表。 打开 Person 表设计器，您看到它具有所有用于将 Student 与教师表中的列。

![在 SSOX 中 person 表](inheritance/_static/ssox-person-table.png)

Person 表中，右键单击，然后单击**显示表数据**才能看到鉴别器列。

![在 SSOX-表数据中的 person 表](inheritance/_static/ssox-person-data.png)

## <a name="summary"></a>摘要

你已实现的每个层次结构一个表继承`Person`， `Student`，和`Instructor`类。 有关在实体框架核心中继承的详细信息，请参阅[继承](https://docs.microsoft.com/ef/core/modeling/inheritance)。 在下一步的教程中，你将看到如何处理各种相对较高级的实体框架方案。

>[!div class="step-by-step"]
[上一页](concurrency.md)
[下一页](advanced.md)  
