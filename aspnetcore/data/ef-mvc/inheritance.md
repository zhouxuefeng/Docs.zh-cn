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
ms.openlocfilehash: 10bde121dac3bdbbf0e55f2d146d91dea0f0210f
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="inheritance---ef-core-with-aspnet-core-mvc-tutorial-9-of-10"></a><span data-ttu-id="d274e-104">继承的 EF 内核，它们有 ASP.NET 核心 MVC 教程 (9 的 10)</span><span class="sxs-lookup"><span data-stu-id="d274e-104">Inheritance - EF Core with ASP.NET Core MVC tutorial (9 of 10)</span></span>

<span data-ttu-id="d274e-105">通过[Tom Dykstra](https://github.com/tdykstra)和[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d274e-105">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d274e-106">Contoso 大学示例 web 应用程序演示如何创建使用实体框架核心和 Visual Studio 的 ASP.NET 核心 MVC web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="d274e-106">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="d274e-107">有关教程系列的信息，请参阅[序列中的第一个教程](intro.md)。</span><span class="sxs-lookup"><span data-stu-id="d274e-107">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="d274e-108">在前面的教程，你可以处理并发异常。</span><span class="sxs-lookup"><span data-stu-id="d274e-108">In the previous tutorial, you handled concurrency exceptions.</span></span> <span data-ttu-id="d274e-109">本教程将演示如何在数据模型中实现继承。</span><span class="sxs-lookup"><span data-stu-id="d274e-109">This tutorial will show you how to implement inheritance in the data model.</span></span>

<span data-ttu-id="d274e-110">在面向对象编程中，你可以使用继承便于代码重用。</span><span class="sxs-lookup"><span data-stu-id="d274e-110">In object-oriented programming, you can use inheritance to facilitate code reuse.</span></span> <span data-ttu-id="d274e-111">本教程中，你将更改`Instructor`和`Student`类，以使其从中派生`Person`基本类，其中包含属性，如`LastName`所共有的教师和学生。</span><span class="sxs-lookup"><span data-stu-id="d274e-111">In this tutorial, you'll change the `Instructor` and `Student` classes so that they derive from a `Person` base class which contains properties such as `LastName` that are common to both instructors and students.</span></span> <span data-ttu-id="d274e-112">不会添加或更改任何 web 页面，但你将更改某些代码并将在数据库中自动反映这些更改。</span><span class="sxs-lookup"><span data-stu-id="d274e-112">You won't add or change any web pages, but you'll change some of the code and those changes will be automatically reflected in the database.</span></span>

## <a name="options-for-mapping-inheritance-to-database-tables"></a><span data-ttu-id="d274e-113">将继承映射到数据库表的选项</span><span class="sxs-lookup"><span data-stu-id="d274e-113">Options for mapping inheritance to database tables</span></span>

<span data-ttu-id="d274e-114">`Instructor`和`Student`School 数据模型中的类具有相同的多个属性：</span><span class="sxs-lookup"><span data-stu-id="d274e-114">The `Instructor` and `Student` classes in the School data model have several properties that are identical:</span></span>

![学生和教师类](inheritance/_static/no-inheritance.png)

<span data-ttu-id="d274e-116">假设你想要消除冗余代码由共享的属性以`Instructor`和`Student`实体。</span><span class="sxs-lookup"><span data-stu-id="d274e-116">Suppose you want to eliminate the redundant code for the properties that are shared by the `Instructor` and `Student` entities.</span></span> <span data-ttu-id="d274e-117">或者，你想要编写可以设置名称格式而无需管理名称来自一个教师或学生的服务。</span><span class="sxs-lookup"><span data-stu-id="d274e-117">Or you want to write a service that can format names without caring whether the name came from an instructor or a student.</span></span> <span data-ttu-id="d274e-118">你可以创建`Person`基类仅包含那些共享属性，然后进行`Instructor`和`Student`类都继承自该基类，如下面的插图中所示：</span><span class="sxs-lookup"><span data-stu-id="d274e-118">You could create a `Person` base class that contains only those shared properties, then make the `Instructor` and `Student` classes inherit from that base class, as shown in the following illustration:</span></span>

![学生和教师从人员类派生的类](inheritance/_static/inheritance.png)

<span data-ttu-id="d274e-120">有几种方法无法在数据库中表示此继承结构。</span><span class="sxs-lookup"><span data-stu-id="d274e-120">There are several ways this inheritance structure could be represented in the database.</span></span> <span data-ttu-id="d274e-121">你可以包括有关学生和单个表中的教师信息 Person 表。</span><span class="sxs-lookup"><span data-stu-id="d274e-121">You could have a Person table that includes information about both students and instructors in a single table.</span></span> <span data-ttu-id="d274e-122">某些列可能仅适用于教师 （雇用日期) 中，某些仅向学生 (EnrollmentDate)，一些为这两个 (LastName、 FirstName)。</span><span class="sxs-lookup"><span data-stu-id="d274e-122">Some of the columns could apply only to instructors (HireDate), some only to students (EnrollmentDate), some to both (LastName, FirstName).</span></span> <span data-ttu-id="d274e-123">通常情况下，你必须以指示哪种类型的每一行代表一个鉴别器列。</span><span class="sxs-lookup"><span data-stu-id="d274e-123">Typically, you'd have a discriminator column to indicate which type each row represents.</span></span> <span data-ttu-id="d274e-124">例如，鉴别器列可能有"教师"教师和"学生"学生版。</span><span class="sxs-lookup"><span data-stu-id="d274e-124">For example, the discriminator column might have "Instructor" for instructors and "Student" for students.</span></span>

![表每个层次结构示例](inheritance/_static/tph.png)

<span data-ttu-id="d274e-126">从单个数据库表生成的实体继承结构的此模式称为表每个层次结构 (TPH) 继承。</span><span class="sxs-lookup"><span data-stu-id="d274e-126">This pattern of generating an entity inheritance structure from a single database table is called table-per-hierarchy (TPH) inheritance.</span></span>

<span data-ttu-id="d274e-127">一种替代方法是使看上去更像继承结构数据库。</span><span class="sxs-lookup"><span data-stu-id="d274e-127">An alternative is to make the database look more like the inheritance structure.</span></span> <span data-ttu-id="d274e-128">例如，你可以只有 Person 表中的名称字段，并拥有单独教师和学生表，其中的日期字段。</span><span class="sxs-lookup"><span data-stu-id="d274e-128">For example, you could have only the name fields in the Person table and have separate Instructor and Student tables with the date fields.</span></span>

![每种类型一个表继承](inheritance/_static/tpt.png)

<span data-ttu-id="d274e-130">使数据库表，以便每个实体类的此模式称为每类型 (TPT) 继承的表。</span><span class="sxs-lookup"><span data-stu-id="d274e-130">This pattern of making a database table for each entity class is called table per type (TPT) inheritance.</span></span>

<span data-ttu-id="d274e-131">还有另一种方法是将所有的非抽象类型映射到单个表。</span><span class="sxs-lookup"><span data-stu-id="d274e-131">Yet another option is to map all non-abstract types to individual tables.</span></span> <span data-ttu-id="d274e-132">类，包括继承的属性的所有属性将都映射到相应的表的列。</span><span class="sxs-lookup"><span data-stu-id="d274e-132">All properties of a class, including inherited properties, map to columns of the corresponding table.</span></span> <span data-ttu-id="d274e-133">此模式称为表每个具体类 (TPC) 继承。</span><span class="sxs-lookup"><span data-stu-id="d274e-133">This pattern is called Table-per-Concrete Class (TPC) inheritance.</span></span> <span data-ttu-id="d274e-134">如果你实施的人员、 学生和教师类的 TPC 继承，如前面所示，Student 与教师表如下实现继承与以前后没有什么不同。</span><span class="sxs-lookup"><span data-stu-id="d274e-134">If you implemented TPC inheritance for the Person, Student, and Instructor classes as shown earlier, the Student and Instructor tables would look no different after implementing inheritance than they did before.</span></span>

<span data-ttu-id="d274e-135">TPC 和 TPH 继承模式通常提供更好的性能比 TPT 继承模式，因为 TPT 模式可能会导致复杂的联接查询。</span><span class="sxs-lookup"><span data-stu-id="d274e-135">TPC and TPH inheritance patterns generally deliver better performance than TPT inheritance patterns, because TPT patterns can result in complex join queries.</span></span>

<span data-ttu-id="d274e-136">本教程演示如何实现 TPH 继承。</span><span class="sxs-lookup"><span data-stu-id="d274e-136">This tutorial demonstrates how to implement TPH inheritance.</span></span> <span data-ttu-id="d274e-137">TPH 是实体框架核心支持一个唯一的继承模式。</span><span class="sxs-lookup"><span data-stu-id="d274e-137">TPH is the only inheritance pattern that the Entity Framework Core supports.</span></span>  <span data-ttu-id="d274e-138">你将执行的操作是创建`Person`类中，更改`Instructor`和`Student`类派生自`Person`，添加到新的类`DbContext`，并创建迁移。</span><span class="sxs-lookup"><span data-stu-id="d274e-138">What you'll do is create a `Person` class, change the `Instructor` and `Student` classes to derive from `Person`, add the new class to the `DbContext`, and create a migration.</span></span>

> [!TIP] 
> <span data-ttu-id="d274e-139">请考虑进行以下更改之前保存项目的副本。</span><span class="sxs-lookup"><span data-stu-id="d274e-139">Consider saving a copy of the project before making the following changes.</span></span>  <span data-ttu-id="d274e-140">然后你会遇到问题，也需要从头开始，如果它将更轻松地从已保存的项目，而不是反转完成本教程中的步骤或正在启动回整个序列的开头。</span><span class="sxs-lookup"><span data-stu-id="d274e-140">Then if you run into problems and need to start over, it will be easier to start from the saved project instead of reversing steps done for this tutorial or going back to the beginning of the whole series.</span></span>

## <a name="create-the-person-class"></a><span data-ttu-id="d274e-141">创建 Person 类</span><span class="sxs-lookup"><span data-stu-id="d274e-141">Create the Person class</span></span>

<span data-ttu-id="d274e-142">在 Models 文件夹中，创建 Person.cs 和模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="d274e-142">In the Models folder, create Person.cs and replace the template code with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu/Models/Person.cs)]

## <a name="make-student-and-instructor-classes-inherit-from-person"></a><span data-ttu-id="d274e-143">将 Student 与教师从人员继承的类</span><span class="sxs-lookup"><span data-stu-id="d274e-143">Make Student and Instructor classes inherit from Person</span></span>

<span data-ttu-id="d274e-144">在*Instructor.cs*、 从 Person 类派生教师类和删除密钥和名称字段。</span><span class="sxs-lookup"><span data-stu-id="d274e-144">In *Instructor.cs*, derive the Instructor class from the Person class and remove the key and name fields.</span></span> <span data-ttu-id="d274e-145">代码将类似于以下示例：</span><span class="sxs-lookup"><span data-stu-id="d274e-145">The code will look like the following example:</span></span>

[!code-csharp[Main](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

<span data-ttu-id="d274e-146">进行中的相同更改*Student.cs*。</span><span class="sxs-lookup"><span data-stu-id="d274e-146">Make the same changes in *Student.cs*.</span></span>

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-the-person-entity-type-to-the-data-model"></a><span data-ttu-id="d274e-147">将 Person 实体类型添加到数据模型</span><span class="sxs-lookup"><span data-stu-id="d274e-147">Add the Person entity type to the data model</span></span>

<span data-ttu-id="d274e-148">添加到 Person 实体类型*SchoolContext.cs*。</span><span class="sxs-lookup"><span data-stu-id="d274e-148">Add the Person entity type to *SchoolContext.cs*.</span></span> <span data-ttu-id="d274e-149">突出显示新行。</span><span class="sxs-lookup"><span data-stu-id="d274e-149">The new lines are highlighted.</span></span>

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

<span data-ttu-id="d274e-150">这是所有实体框架所需配置每个层次结构一个表继承。</span><span class="sxs-lookup"><span data-stu-id="d274e-150">This is all that the Entity Framework needs in order to configure table-per-hierarchy inheritance.</span></span> <span data-ttu-id="d274e-151">当你将看到更新数据库时，它将具有代替 Student 与教师表 Person 表。</span><span class="sxs-lookup"><span data-stu-id="d274e-151">As you'll see, when the database is updated, it will have a Person table in place of the Student and Instructor tables.</span></span>

## <a name="create-and-customize-migration-code"></a><span data-ttu-id="d274e-152">创建和自定义迁移代码</span><span class="sxs-lookup"><span data-stu-id="d274e-152">Create and customize migration code</span></span>

<span data-ttu-id="d274e-153">保存所做的更改并生成项目。</span><span class="sxs-lookup"><span data-stu-id="d274e-153">Save your changes and build the project.</span></span> <span data-ttu-id="d274e-154">然后在项目文件夹中打开命令窗口并输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="d274e-154">Then open the command window in the project folder and enter the following command:</span></span>

```console
dotnet ef migrations add Inheritance
```

<span data-ttu-id="d274e-155">不运行`database update`尚未命令。</span><span class="sxs-lookup"><span data-stu-id="d274e-155">Don't run the `database update` command yet.</span></span> <span data-ttu-id="d274e-156">该命令将导致丢失数据，因为它将删除教师表并将学生表重命名为 Person。</span><span class="sxs-lookup"><span data-stu-id="d274e-156">That command will result in lost data because it will drop the Instructor table and rename the Student table to Person.</span></span> <span data-ttu-id="d274e-157">你需要提供自定义代码来保留现有数据。</span><span class="sxs-lookup"><span data-stu-id="d274e-157">You need to provide custom code to preserve existing data.</span></span>

<span data-ttu-id="d274e-158">打开*迁移 /\<时间戳 > _Inheritance.cs*和替换`Up`方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="d274e-158">Open *Migrations/\<timestamp>_Inheritance.cs* and replace the `Up` method with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

<span data-ttu-id="d274e-159">此代码将负责以下数据库更新任务：</span><span class="sxs-lookup"><span data-stu-id="d274e-159">This code takes care of the following database update tasks:</span></span>

* <span data-ttu-id="d274e-160">删除外键约束和指向学生表的索引。</span><span class="sxs-lookup"><span data-stu-id="d274e-160">Removes foreign key constraints and indexes that point to the Student table.</span></span>

* <span data-ttu-id="d274e-161">重命名为人员教师表，并使它来存储学生数据所需的更改：</span><span class="sxs-lookup"><span data-stu-id="d274e-161">Renames the Instructor table as Person and makes changes needed for it to store Student data:</span></span>

* <span data-ttu-id="d274e-162">将可以为 null EnrollmentDate 添加学生版。</span><span class="sxs-lookup"><span data-stu-id="d274e-162">Adds nullable EnrollmentDate for students.</span></span>

* <span data-ttu-id="d274e-163">添加鉴别器列来指示行是否适用于一名学生或一个教师。</span><span class="sxs-lookup"><span data-stu-id="d274e-163">Adds Discriminator column to indicate whether a row is for a student or an instructor.</span></span>

* <span data-ttu-id="d274e-164">使雇用日期可以为 null，因为学生行不会产生雇佣日期。</span><span class="sxs-lookup"><span data-stu-id="d274e-164">Makes HireDate nullable since student rows won't have hire dates.</span></span>

* <span data-ttu-id="d274e-165">添加临时的字段，将用于更新指向学生的外键。</span><span class="sxs-lookup"><span data-stu-id="d274e-165">Adds a temporary field that will be used to update foreign keys that point to students.</span></span> <span data-ttu-id="d274e-166">当将学生复制到 Person 表他们将获得新的主键值。</span><span class="sxs-lookup"><span data-stu-id="d274e-166">When you copy students into the Person table they'll get new primary key values.</span></span>

* <span data-ttu-id="d274e-167">将数据从 Person 表到学生表。</span><span class="sxs-lookup"><span data-stu-id="d274e-167">Copies data from the Student table into the Person table.</span></span> <span data-ttu-id="d274e-168">这将导致学生以分配新的主键值。</span><span class="sxs-lookup"><span data-stu-id="d274e-168">This causes students to get assigned new primary key values.</span></span>

* <span data-ttu-id="d274e-169">修复了指向学生的外键值。</span><span class="sxs-lookup"><span data-stu-id="d274e-169">Fixes foreign key values that point to students.</span></span>

* <span data-ttu-id="d274e-170">重新创建外键约束和索引，现在将它们指向 Person 表。</span><span class="sxs-lookup"><span data-stu-id="d274e-170">Re-creates foreign key constraints and indexes, now pointing them to the Person table.</span></span>

<span data-ttu-id="d274e-171">（如果你在作为主键类型使用而不是整数的 GUID，学生主键值不需要更改，并且这些步骤的几个可能已被省略。）</span><span class="sxs-lookup"><span data-stu-id="d274e-171">(If you had used GUID instead of integer as the primary key type, the student primary key values wouldn't have to change, and several of these steps could have been omitted.)</span></span>

<span data-ttu-id="d274e-172">运行`database update`命令：</span><span class="sxs-lookup"><span data-stu-id="d274e-172">Run the `database update` command:</span></span>

```console
dotnet ef database update
```

<span data-ttu-id="d274e-173">(在生产系统中会使对相应更改`Down`在你曾必须使用，以返回到以前的数据库版本的情况下的方法。</span><span class="sxs-lookup"><span data-stu-id="d274e-173">(In a production system you would make corresponding changes to the `Down` method in case you ever had to use that to go back to the previous database version.</span></span> <span data-ttu-id="d274e-174">本教程中你将不使用`Down`方法。)</span><span class="sxs-lookup"><span data-stu-id="d274e-174">For this tutorial you won't be using the `Down` method.)</span></span>

> [!NOTE] 
> <span data-ttu-id="d274e-175">就可以在具有现有数据的数据库中进行架构更改时，发生其他错误。</span><span class="sxs-lookup"><span data-stu-id="d274e-175">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="d274e-176">如果您无法解析的迁移错误，你可以更改连接字符串中的数据库名称，或删除数据库。</span><span class="sxs-lookup"><span data-stu-id="d274e-176">If you get migration errors that you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="d274e-177">与新数据库，若要迁移，没有数据和更新数据库命令是更有可能完成且未发生错误。</span><span class="sxs-lookup"><span data-stu-id="d274e-177">With a new database, there is no data to migrate, and the update-database command is more likely to complete without errors.</span></span> <span data-ttu-id="d274e-178">若要删除此数据库，使用 SSOX 或运行`database drop`CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="d274e-178">To delete the database, use SSOX or run the `database drop` CLI command.</span></span>

## <a name="test-with-inheritance-implemented"></a><span data-ttu-id="d274e-179">使用继承实现进行测试</span><span class="sxs-lookup"><span data-stu-id="d274e-179">Test with inheritance implemented</span></span>

<span data-ttu-id="d274e-180">运行应用程序并尝试各种页面。</span><span class="sxs-lookup"><span data-stu-id="d274e-180">Run the app and try various pages.</span></span> <span data-ttu-id="d274e-181">一切相同像以前一样。</span><span class="sxs-lookup"><span data-stu-id="d274e-181">Everything works the same as it did before.</span></span>

<span data-ttu-id="d274e-182">在**SQL Server 对象资源管理器**，展开**数据连接/SchoolContext**然后**表**，并查看已被取代学生和教师表Person 表。</span><span class="sxs-lookup"><span data-stu-id="d274e-182">In **SQL Server Object Explorer**, expand **Data Connections/SchoolContext** and then **Tables**, and you see that the Student and Instructor tables have been replaced by a Person table.</span></span> <span data-ttu-id="d274e-183">打开 Person 表设计器，您看到它具有所有用于将 Student 与教师表中的列。</span><span class="sxs-lookup"><span data-stu-id="d274e-183">Open the Person table designer and you see that it has all of the columns that used to be in the Student and Instructor tables.</span></span>

![在 SSOX 中 person 表](inheritance/_static/ssox-person-table.png)

<span data-ttu-id="d274e-185">Person 表中，右键单击，然后单击**显示表数据**才能看到鉴别器列。</span><span class="sxs-lookup"><span data-stu-id="d274e-185">Right-click the Person table, and then click **Show Table Data** to see the discriminator column.</span></span>

![在 SSOX-表数据中的 person 表](inheritance/_static/ssox-person-data.png)

## <a name="summary"></a><span data-ttu-id="d274e-187">摘要</span><span class="sxs-lookup"><span data-stu-id="d274e-187">Summary</span></span>

<span data-ttu-id="d274e-188">你已实现的每个层次结构一个表继承`Person`， `Student`，和`Instructor`类。</span><span class="sxs-lookup"><span data-stu-id="d274e-188">You've implemented table-per-hierarchy inheritance for the `Person`, `Student`, and `Instructor` classes.</span></span> <span data-ttu-id="d274e-189">有关在实体框架核心中继承的详细信息，请参阅[继承](https://docs.microsoft.com/ef/core/modeling/inheritance)。</span><span class="sxs-lookup"><span data-stu-id="d274e-189">For more information about inheritance in Entity Framework Core, see [Inheritance](https://docs.microsoft.com/ef/core/modeling/inheritance).</span></span> <span data-ttu-id="d274e-190">在下一步的教程中，你将看到如何处理各种相对较高级的实体框架方案。</span><span class="sxs-lookup"><span data-stu-id="d274e-190">In the next tutorial you'll see how to handle a variety of relatively advanced Entity Framework scenarios.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="d274e-191">[上一页](concurrency.md)
[下一页](advanced.md)</span><span class="sxs-lookup"><span data-stu-id="d274e-191">[Previous](concurrency.md)
[Next](advanced.md)</span></span>  
