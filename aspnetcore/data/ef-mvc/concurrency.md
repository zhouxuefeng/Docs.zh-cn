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
ms.openlocfilehash: fc6b218034183a9153c1ef22c99d920a942d2d09
ms.sourcegitcommit: 74a8ad9c1ba5c155d7c4303e67632a0922c38e86
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2017
---
# <a name="handling-concurrency-conflicts---ef-core-with-aspnet-core-mvc-tutorial-8-of-10"></a><span data-ttu-id="01980-104">处理并发冲突的 EF 内核，它们有 ASP.NET 核心 MVC 教程 (10 的第 8)</span><span class="sxs-lookup"><span data-stu-id="01980-104">Handling concurrency conflicts - EF Core with ASP.NET Core MVC tutorial (8 of 10)</span></span>

<span data-ttu-id="01980-105">通过[Tom Dykstra](https://github.com/tdykstra)和[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="01980-105">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="01980-106">Contoso 大学示例 web 应用程序演示如何创建使用实体框架核心和 Visual Studio 的 ASP.NET 核心 MVC web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="01980-106">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="01980-107">有关教程系列的信息，请参阅[序列中的第一个教程](intro.md)。</span><span class="sxs-lookup"><span data-stu-id="01980-107">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="01980-108">在前面教程中，您学习了如何更新数据。</span><span class="sxs-lookup"><span data-stu-id="01980-108">In earlier tutorials, you learned how to update data.</span></span> <span data-ttu-id="01980-109">本教程演示如何处理冲突，当多个用户在同一时间更新同一实体。</span><span class="sxs-lookup"><span data-stu-id="01980-109">This tutorial shows how to handle conflicts when multiple users update the same entity at the same time.</span></span>

<span data-ttu-id="01980-110">你将创建网页使用部门实体和处理并发错误。</span><span class="sxs-lookup"><span data-stu-id="01980-110">You'll create web pages that work with the Department entity and handle concurrency errors.</span></span> <span data-ttu-id="01980-111">下图显示的编辑和删除的页面，包括某些并发冲突发生时显示的消息。</span><span class="sxs-lookup"><span data-stu-id="01980-111">The following illustrations show the Edit and Delete pages, including some messages that are displayed if a concurrency conflict occurs.</span></span>

![部门编辑页](concurrency/_static/edit-error.png)

![部门删除页](concurrency/_static/delete-error.png)

## <a name="concurrency-conflicts"></a><span data-ttu-id="01980-114">并发冲突</span><span class="sxs-lookup"><span data-stu-id="01980-114">Concurrency conflicts</span></span>

<span data-ttu-id="01980-115">一个用户才能进行编辑，显示实体的数据，并在第一个用户的更改写入到数据库之前，另一个用户然后更新相同实体的数据时发生并发冲突。</span><span class="sxs-lookup"><span data-stu-id="01980-115">A concurrency conflict occurs when one user displays an entity's data in order to edit it, and then another user updates the same entity's data before the first user's change is written to the database.</span></span> <span data-ttu-id="01980-116">如果不启用此类冲突检测，任何人都更新数据库上次将覆盖其他用户的更改。</span><span class="sxs-lookup"><span data-stu-id="01980-116">If you don't enable the detection of such conflicts, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="01980-117">在许多应用程序，这种风险很可接受： 如果有很多用户或几个更新，或如果并不真正重要的一些更改将被覆盖，如果并发编程的开销可能超过带来的好处。</span><span class="sxs-lookup"><span data-stu-id="01980-117">In many applications, this risk is acceptable: if there are few users, or few updates, or if isn't really critical if some changes are overwritten, the cost of programming for concurrency might outweigh the benefit.</span></span> <span data-ttu-id="01980-118">在这种情况下，你不必配置此应用程序处理并发冲突。</span><span class="sxs-lookup"><span data-stu-id="01980-118">In that case, you don't have to configure the application to handle concurrency conflicts.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="01980-119">保守式并发 （锁定）</span><span class="sxs-lookup"><span data-stu-id="01980-119">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="01980-120">如果你的应用程序需要防止并发方案中的意外数据丢失，做到这一点的一种方法是使用数据库锁。</span><span class="sxs-lookup"><span data-stu-id="01980-120">If your application does need to prevent accidental data loss in concurrency scenarios, one way to do that is to use database locks.</span></span> <span data-ttu-id="01980-121">这称为保守式并发。</span><span class="sxs-lookup"><span data-stu-id="01980-121">This is called pessimistic concurrency.</span></span> <span data-ttu-id="01980-122">例如，从数据库读取行之前，你请求的锁只读的或更新访问权限。</span><span class="sxs-lookup"><span data-stu-id="01980-122">For example, before you read a row from a database, you request a lock for read-only or for update access.</span></span> <span data-ttu-id="01980-123">如果锁定的行更新访问权限时，不允许任何其他用户锁定该行为只读的或更新访问权限，因为它们将获取正在进行更改的数据的副本。</span><span class="sxs-lookup"><span data-stu-id="01980-123">If you lock a row for update access, no other users are allowed to lock the row either for read-only or update access, because they would get a copy of data that's in the process of being changed.</span></span> <span data-ttu-id="01980-124">如果锁定用于只读访问的行，其他人可以还将其锁定用于只读访问权限但不是用于更新。</span><span class="sxs-lookup"><span data-stu-id="01980-124">If you lock a row for read-only access, others can also lock it for read-only access but not for update.</span></span>

<span data-ttu-id="01980-125">管理锁也有缺点。</span><span class="sxs-lookup"><span data-stu-id="01980-125">Managing locks has disadvantages.</span></span> <span data-ttu-id="01980-126">它可以是复杂到程序。</span><span class="sxs-lookup"><span data-stu-id="01980-126">It can be complex to program.</span></span> <span data-ttu-id="01980-127">它需要大量的数据库管理资源，并且可能导致性能问题的应用程序的用户数会增加。</span><span class="sxs-lookup"><span data-stu-id="01980-127">It requires significant database management resources, and it can cause performance problems as the number of users of an application increases.</span></span> <span data-ttu-id="01980-128">出于这些原因，不是所有数据库管理系统都支持保守式并发。</span><span class="sxs-lookup"><span data-stu-id="01980-128">For these reasons, not all database management systems support pessimistic concurrency.</span></span> <span data-ttu-id="01980-129">本教程不向你展示如何实现它，并且实体框架核心提供，没有内置支持。</span><span class="sxs-lookup"><span data-stu-id="01980-129">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show you how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="01980-130">开放式并发</span><span class="sxs-lookup"><span data-stu-id="01980-130">Optimistic Concurrency</span></span>

<span data-ttu-id="01980-131">保守式并发替代方法是开放式并发。</span><span class="sxs-lookup"><span data-stu-id="01980-131">The alternative to pessimistic concurrency is optimistic concurrency.</span></span> <span data-ttu-id="01980-132">开放式并发意味着允许并发冲突发生，然后相应地反应，如果他们这样做。</span><span class="sxs-lookup"><span data-stu-id="01980-132">Optimistic concurrency means allowing concurrency conflicts to happen, and then reacting appropriately if they do.</span></span> <span data-ttu-id="01980-133">例如，Jane 访问部门编辑页，并从 $350,000.00 英语部门的预算金额变为 0.00 美元。</span><span class="sxs-lookup"><span data-stu-id="01980-133">For example, Jane visits the Department Edit page and changes the Budget amount for the English department from $350,000.00 to $0.00.</span></span>

![将预算更改为 0](concurrency/_static/change-budget.png)

<span data-ttu-id="01980-135">Jane 单击之前**保存**，John 访问同一页上，并从 2007 年 9 月 1 日的开始日期字段更改为 2013 年 9 月 1 日。</span><span class="sxs-lookup"><span data-stu-id="01980-135">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![更改为 2013年的开始日期](concurrency/_static/change-date.png)

<span data-ttu-id="01980-137">Jane 单击**保存**第一个，看到她更改时浏览器返回到索引页面。</span><span class="sxs-lookup"><span data-stu-id="01980-137">Jane clicks **Save** first and sees her change when the browser returns to the Index page.</span></span>

![更改为零的预算](concurrency/_static/budget-zero.png)

<span data-ttu-id="01980-139">然后 John 单击**保存**仍会显示 $350,000.00 预算将编辑页上。</span><span class="sxs-lookup"><span data-stu-id="01980-139">Then John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="01980-140">接下来如何操作取决于如何处理并发冲突。</span><span class="sxs-lookup"><span data-stu-id="01980-140">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="01980-141">某些选项包括：</span><span class="sxs-lookup"><span data-stu-id="01980-141">Some of the options include the following:</span></span>

* <span data-ttu-id="01980-142">你可以跟踪的用户进行了修改的属性，并更新仅在数据库中的相应列。</span><span class="sxs-lookup"><span data-stu-id="01980-142">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

     <span data-ttu-id="01980-143">在示例方案中，任何数据将不会丢失，因为不同的属性已更新由两个用户。</span><span class="sxs-lookup"><span data-stu-id="01980-143">In the example scenario, no data would be lost, because different properties were updated by the two users.</span></span> <span data-ttu-id="01980-144">下一次有人浏览英语部门，他们将看到 Jane 的和 John 的更改-2013 年 9 月 1 日开始日期和零美元的预算。</span><span class="sxs-lookup"><span data-stu-id="01980-144">The next time someone browses the English department, they'll see both Jane's and John's changes -- a start date of 9/1/2013 and a budget of zero dollars.</span></span> <span data-ttu-id="01980-145">这种更新方法可以减少可能导致数据丢失的冲突数，但它无法避免数据丢失，如果实体的同一属性进行竞争的更改。</span><span class="sxs-lookup"><span data-stu-id="01980-145">This method of updating can reduce the number of conflicts that could result in data loss, but it can't avoid data loss if competing changes are made to the same property of an entity.</span></span> <span data-ttu-id="01980-146">实体框架是否可以通过这种方式取决于如何实现你更新代码。</span><span class="sxs-lookup"><span data-stu-id="01980-146">Whether the Entity Framework works this way depends on how you implement your update code.</span></span> <span data-ttu-id="01980-147">它通常是状态的不现实中 web 应用程序，因为它可能需要维护大量，以便跟踪的实体的所有原始属性值以及新值。</span><span class="sxs-lookup"><span data-stu-id="01980-147">It's often not practical in a web application, because it can require that you maintain large amounts of state in order to keep track of all original property values for an entity as well as new values.</span></span> <span data-ttu-id="01980-148">维护大量的状态会影响应用程序性能，因为它需要服务器资源或必须包含在 web 页本身 （例如，在隐藏的字段） 或在一个 cookie。</span><span class="sxs-lookup"><span data-stu-id="01980-148">Maintaining large amounts of state can affect application performance because it either requires server resources or must be included in the web page itself (for example, in hidden fields) or in a cookie.</span></span>

* <span data-ttu-id="01980-149">你可以让 John 的更改覆盖 Jane 的更改。</span><span class="sxs-lookup"><span data-stu-id="01980-149">You can let John's change overwrite Jane's change.</span></span>

     <span data-ttu-id="01980-150">下一次有人浏览英语部门时，他们将看到 2013 年 9 月 1 日和还原的 $350,000.00 值。</span><span class="sxs-lookup"><span data-stu-id="01980-150">The next time someone browses the English department, they'll see 9/1/2013 and the restored $350,000.00 value.</span></span> <span data-ttu-id="01980-151">这称为*客户端优先*或*在 Wins 中最后一个*方案。</span><span class="sxs-lookup"><span data-stu-id="01980-151">This is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="01980-152">（从客户端的所有值都优先于什么是在数据存储。）中所述的简介本部分中，如果您不执行任何并发处理的编码，这将自动发生。</span><span class="sxs-lookup"><span data-stu-id="01980-152">(All values from the client take precedence over what's in the data store.) As noted in the introduction to this section, if you don't do any coding for concurrency handling, this will happen automatically.</span></span>

* <span data-ttu-id="01980-153">你可以防止 John 的更改正在更新数据库中。</span><span class="sxs-lookup"><span data-stu-id="01980-153">You can prevent John's change from being updated in the database.</span></span>

     <span data-ttu-id="01980-154">通常情况下，将显示一条错误消息、 显示他的数据的当前状态和允许他将他仍想要使它们的情况下重新应用他的更改。</span><span class="sxs-lookup"><span data-stu-id="01980-154">Typically, you would display an error message, show him the current state of the data, and allow him to reapply his changes if he still wants to make them.</span></span> <span data-ttu-id="01980-155">这称为*存储 Wins*方案。</span><span class="sxs-lookup"><span data-stu-id="01980-155">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="01980-156">（数据存储值优先于提交的客户端的值。）在本教程中，你将实现存储 Wins 方案。</span><span class="sxs-lookup"><span data-stu-id="01980-156">(The data-store values take precedence over the values submitted by the client.) You'll implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="01980-157">此方法可确保到发生了什么情况收到通知用户的情况下，任何更改都会被覆盖。</span><span class="sxs-lookup"><span data-stu-id="01980-157">This method ensures that no changes are overwritten without a user being alerted to what's happening.</span></span>

### <a name="detecting-concurrency-conflicts"></a><span data-ttu-id="01980-158">检测并发冲突</span><span class="sxs-lookup"><span data-stu-id="01980-158">Detecting concurrency conflicts</span></span>

<span data-ttu-id="01980-159">您可以通过处理中解决冲突`DbConcurrencyException`实体框架引发的异常。</span><span class="sxs-lookup"><span data-stu-id="01980-159">You can resolve conflicts by handling `DbConcurrencyException` exceptions that the Entity Framework throws.</span></span> <span data-ttu-id="01980-160">为了知道何时引发这些异常，实体框架必须能够检测到冲突。</span><span class="sxs-lookup"><span data-stu-id="01980-160">In order to know when to throw these exceptions, the Entity Framework must be able to detect conflicts.</span></span> <span data-ttu-id="01980-161">因此，你必须从数据库和数据模型正确配置。</span><span class="sxs-lookup"><span data-stu-id="01980-161">Therefore, you must configure the database and the data model appropriately.</span></span> <span data-ttu-id="01980-162">有关启用冲突检测某些选项包括：</span><span class="sxs-lookup"><span data-stu-id="01980-162">Some options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="01980-163">在数据库表中，包括可以用于确定当行已更改的跟踪列。</span><span class="sxs-lookup"><span data-stu-id="01980-163">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span> <span data-ttu-id="01980-164">然后，你可以配置实体框架可以包括该列在 Where 子句的 SQL 更新或删除命令。</span><span class="sxs-lookup"><span data-stu-id="01980-164">You can then configure the Entity Framework to include that column in the Where clause of SQL Update or Delete commands.</span></span>

     <span data-ttu-id="01980-165">跟踪列的数据类型通常是`rowversion`。</span><span class="sxs-lookup"><span data-stu-id="01980-165">The data type of the tracking column is typically `rowversion`.</span></span> <span data-ttu-id="01980-166">`rowversion`值是每次更新行时递增序列号。</span><span class="sxs-lookup"><span data-stu-id="01980-166">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="01980-167">在 Update 或 Delete 命令中，Where 子句中包括跟踪列 （原始行版本） 的原始值。</span><span class="sxs-lookup"><span data-stu-id="01980-167">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version) .</span></span> <span data-ttu-id="01980-168">如果正在更新的行已由其他用户时中的值更改`rowversion`列是不同于原始值，因此 Update 或 Delete 语句找不到要更新，因为 Where 出现的行子句。</span><span class="sxs-lookup"><span data-stu-id="01980-168">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value, so the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="01980-169">当实体框架会找到任何行，已更新的 Update 或 Delete 命令 （即，在受影响的行数为零） 时，它会，解释为并发冲突。</span><span class="sxs-lookup"><span data-stu-id="01980-169">When the Entity Framework finds that no rows have been updated by the Update or Delete command (that is, when the number of affected rows is zero), it interprets that as a concurrency conflict.</span></span>

* <span data-ttu-id="01980-170">配置实体框架可以包括每个列的原始值的表中 Where 子句的 Update 和 Delete 命令。</span><span class="sxs-lookup"><span data-stu-id="01980-170">Configure the Entity Framework to include the original values of every column in the table in the Where clause of Update and Delete commands.</span></span>

     <span data-ttu-id="01980-171">如下所示的第一个选项，如果自第一次读取一行，行中的任何内容已更改 Where 子句不会返回的行，若要更新，该实体框架会将解释为并发冲突。</span><span class="sxs-lookup"><span data-stu-id="01980-171">As in the first option, if anything in the row has changed since the row was first read, the Where clause won't return a row to update, which the Entity Framework interprets as a concurrency conflict.</span></span> <span data-ttu-id="01980-172">对于包含许多列的数据库表，此方法会导致非常大的 Where 子句，并可能需要维护大量的状态。</span><span class="sxs-lookup"><span data-stu-id="01980-172">For database tables that have many columns, this approach can result in very large Where clauses, and can require that you maintain large amounts of state.</span></span> <span data-ttu-id="01980-173">如前文所述，则保留大量的状态可能会影响应用程序性能。</span><span class="sxs-lookup"><span data-stu-id="01980-173">As noted earlier, maintaining large amounts of state can affect application performance.</span></span> <span data-ttu-id="01980-174">因此此方法通常不建议，并且它不在本教程中使用的方法。</span><span class="sxs-lookup"><span data-stu-id="01980-174">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

     <span data-ttu-id="01980-175">如果您想要实现这种并发的方法，则必须将你想要通过添加跟踪的并发性实体中的所有非主键属性标记`ConcurrencyCheck`属性设为它们。</span><span class="sxs-lookup"><span data-stu-id="01980-175">If you do want to implement this approach to concurrency, you have to mark all non-primary-key properties in the entity you want to track concurrency for by adding the `ConcurrencyCheck` attribute to them.</span></span> <span data-ttu-id="01980-176">所做的更改使实体框架能够在 Update 和 Delete 语句的 SQL Where 子句中包括所有列。</span><span class="sxs-lookup"><span data-stu-id="01980-176">That change enables the Entity Framework to include all columns in the SQL Where clause of Update and Delete statements.</span></span>

<span data-ttu-id="01980-177">本教程的其余部分中将添加`rowversion`跟踪对部门实体的属性，创建一个控制器和视图，并进行测试以验证一切是否正常工作。</span><span class="sxs-lookup"><span data-stu-id="01980-177">In the remainder of this tutorial you'll add a `rowversion` tracking property to the Department entity, create a controller and views, and test to verify that everything works correctly.</span></span>

## <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="01980-178">将跟踪属性添加到部门实体</span><span class="sxs-lookup"><span data-stu-id="01980-178">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="01980-179">在*Models/Department.cs*，添加名为 RowVersion 的跟踪属性：</span><span class="sxs-lookup"><span data-stu-id="01980-179">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[Main](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="01980-180">`Timestamp`属性指定此列将包含在 Where 子句的 Update 和 Delete 命令发送到数据库。</span><span class="sxs-lookup"><span data-stu-id="01980-180">The `Timestamp` attribute specifies that this column will be included in the Where clause of Update and Delete commands sent to the database.</span></span> <span data-ttu-id="01980-181">该属性称为`Timestamp`因为以前版本的 SQL Server 使用 SQL`timestamp`之前 SQL 数据类型`rowversion`替换它。</span><span class="sxs-lookup"><span data-stu-id="01980-181">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` replaced it.</span></span> <span data-ttu-id="01980-182">.NET 类型`rowversion`为字节数组。</span><span class="sxs-lookup"><span data-stu-id="01980-182">The .NET type for `rowversion` is a byte array.</span></span>

<span data-ttu-id="01980-183">如果想要使用 fluent API，则可以使用`IsConcurrencyToken`方法 (在*Data/SchoolContext.cs*) 来指定跟踪属性，在下面的示例所示：</span><span class="sxs-lookup"><span data-stu-id="01980-183">If you prefer to use the fluent API, you can use the `IsConcurrencyToken` method (in *Data/SchoolContext.cs*) to specify the tracking property, as shown in the following example:</span></span>

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

<span data-ttu-id="01980-184">通过添加一个属性，更改数据库模型，因此你需要执行另一个迁移。</span><span class="sxs-lookup"><span data-stu-id="01980-184">By adding a property you changed the database model, so you need to do another migration.</span></span>

<span data-ttu-id="01980-185">保存所做的更改和生成项目，然后在命令窗口中输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="01980-185">Save your changes and build the project, and then enter the following commands in the command window:</span></span>

```console
dotnet ef migrations add RowVersion
```

```console
dotnet ef database update
```

## <a name="create-a-departments-controller-and-views"></a><span data-ttu-id="01980-186">创建部门控制器和视图</span><span class="sxs-lookup"><span data-stu-id="01980-186">Create a Departments controller and views</span></span>

<span data-ttu-id="01980-187">如你此前为学生、 课程和教师搭建部门控制器和视图。</span><span class="sxs-lookup"><span data-stu-id="01980-187">Scaffold a Departments controller and views as you did earlier for Students, Courses, and Instructors.</span></span>

![基架部门](concurrency/_static/add-departments-controller.png)

<span data-ttu-id="01980-189">在*DepartmentsController.cs*文件中，将所有四个出现的"FirstMidName"更改为"FullName"，以便部门管理员下拉列表将包含教师的完整名称而不是只是最后一个名称。</span><span class="sxs-lookup"><span data-stu-id="01980-189">In the *DepartmentsController.cs* file, change all four occurrences of "FirstMidName" to "FullName" so that the department administrator drop-down lists will contain the full name of the instructor rather than just the last name.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-the-departments-index-view"></a><span data-ttu-id="01980-190">更新部门索引视图</span><span class="sxs-lookup"><span data-stu-id="01980-190">Update the Departments Index view</span></span>

<span data-ttu-id="01980-191">基架引擎创建 RowVersion 列在索引视图中，但不应显示该字段。</span><span class="sxs-lookup"><span data-stu-id="01980-191">The scaffolding engine created a RowVersion column in the Index view, but that field shouldn't be displayed.</span></span>

<span data-ttu-id="01980-192">中的代码替换*Views/Departments/Index.cshtml*替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="01980-192">Replace the code in *Views/Departments/Index.cshtml* with the following code.</span></span>

[!code-html[Main](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

<span data-ttu-id="01980-193">这将更改发往"部门"的、 删除 RowVersion 列中，然后显示管理员而不是第一个名称的完整名称。</span><span class="sxs-lookup"><span data-stu-id="01980-193">This changes the heading to "Departments", deletes the RowVersion column, and shows full name instead of first name for the administrator.</span></span>

## <a name="update-the-edit-methods-in-the-departments-controller"></a><span data-ttu-id="01980-194">更新部门控制器中的编辑方法</span><span class="sxs-lookup"><span data-stu-id="01980-194">Update the Edit methods in the Departments controller</span></span>

<span data-ttu-id="01980-195">在这两个 HttpGet`Edit`方法和`Details`方法，添加`AsNoTracking`。</span><span class="sxs-lookup"><span data-stu-id="01980-195">In both the HttpGet `Edit` method and the `Details` method, add `AsNoTracking`.</span></span> <span data-ttu-id="01980-196">在 HttpGet`Edit`方法，添加为管理员预先加载。</span><span class="sxs-lookup"><span data-stu-id="01980-196">In the HttpGet `Edit` method, add eager loading for the Administrator.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading&highlight=2,3)]

<span data-ttu-id="01980-197">将现有代码为 HttpPost`Edit`方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="01980-197">Replace the existing code for the HttpPost `Edit` method with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

<span data-ttu-id="01980-198">代码首先尝试读取要更新的部门。</span><span class="sxs-lookup"><span data-stu-id="01980-198">The code begins by trying to read the department to be updated.</span></span> <span data-ttu-id="01980-199">如果`SingleOrDefaultAsync`方法将返回 null，部门已由另一个用户被删除。</span><span class="sxs-lookup"><span data-stu-id="01980-199">If the `SingleOrDefaultAsync` method returns null, the department was deleted by another user.</span></span> <span data-ttu-id="01980-200">在这种情况下代码使用已发布的窗体值创建部门实体，以便编辑页可以重新显示并显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="01980-200">In that case the code uses the posted form values to create a department entity so that the Edit page can be redisplayed with an error message.</span></span> <span data-ttu-id="01980-201">作为替代方法，你不需要重新创建部门实体，如果不重新显示部门字段的情况下显示仅错误消息。</span><span class="sxs-lookup"><span data-stu-id="01980-201">As an alternative, you wouldn't have to re-create the department entity if you display only an error message without redisplaying the department fields.</span></span>

<span data-ttu-id="01980-202">该视图将存储原始`RowVersion`值中隐藏的字段，因此该方法接收中的该值`rowVersion`参数。</span><span class="sxs-lookup"><span data-stu-id="01980-202">The view stores the original `RowVersion` value in a hidden field, and this method receives that value in the `rowVersion` parameter.</span></span> <span data-ttu-id="01980-203">在调用之前`SaveChanges`，你必须将它放原始`RowVersion`中的属性值`OriginalValues`的实体的集合。</span><span class="sxs-lookup"><span data-stu-id="01980-203">Before you call `SaveChanges`, you have to put that original `RowVersion` property value in the `OriginalValues` collection for the entity.</span></span>

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

<span data-ttu-id="01980-204">然后在实体框架创建 SQL UPDATE 命令，该命令将包含一个 WHERE 子句，查找具有行的原始`RowVersion`值。</span><span class="sxs-lookup"><span data-stu-id="01980-204">Then when the Entity Framework creates a SQL UPDATE command, that command will include a WHERE clause that looks for a row that has the original `RowVersion` value.</span></span> <span data-ttu-id="01980-205">如果更新命令会不影响任何行 (没有行具有原始`RowVersion`值)，实体框架将引发`DbUpdateConcurrencyException`异常。</span><span class="sxs-lookup"><span data-stu-id="01980-205">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value),  the Entity Framework throws a `DbUpdateConcurrencyException` exception.</span></span>

<span data-ttu-id="01980-206">该异常的 catch 块中的代码获取具有从更新后的值的受影响的部门实体`Entries`异常对象上的属性。</span><span class="sxs-lookup"><span data-stu-id="01980-206">The code in the catch block for that exception gets the affected Department entity that has the updated values from the `Entries` property on the exception object.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

<span data-ttu-id="01980-207">`Entries`集合将有仅仅一个`EntityEntry`对象。</span><span class="sxs-lookup"><span data-stu-id="01980-207">The `Entries` collection will have just one `EntityEntry` object.</span></span>  <span data-ttu-id="01980-208">该对象可用于获取用户输入的新值和当前的数据库值。</span><span class="sxs-lookup"><span data-stu-id="01980-208">You can use that object to get the new values entered by the user and the current database values.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

<span data-ttu-id="01980-209">该代码将添加具有不同的数据库值从用户所输入的编辑页上的每个列的自定义错误消息 （只有一个字段如下所示为简洁起见）。</span><span class="sxs-lookup"><span data-stu-id="01980-209">The code adds a custom error message for each column that has database values different from what the user entered on the Edit page (only one field is shown here for brevity).</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

<span data-ttu-id="01980-210">最后，代码设置`RowVersion`值`departmentToUpdate`从数据库检索到的新值。</span><span class="sxs-lookup"><span data-stu-id="01980-210">Finally, the code sets the `RowVersion` value of the `departmentToUpdate` to the new value retrieved from the database.</span></span> <span data-ttu-id="01980-211">此新`RowVersion`值将存储在隐藏的字段，将重新显示页，编辑和下一次用户单击时**保存**，因为将捕获的编辑页重新显示发生这种情况的并发错误。</span><span class="sxs-lookup"><span data-stu-id="01980-211">This new `RowVersion` value will be stored in the hidden field when the Edit page is redisplayed, and the next time the user clicks **Save**, only concurrency errors that happen since the redisplay of the Edit page will be caught.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

<span data-ttu-id="01980-212">`ModelState.Remove`语句是必需的因为`ModelState`具有旧`RowVersion`值。</span><span class="sxs-lookup"><span data-stu-id="01980-212">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="01980-213">在视图中，`ModelState`字段将优先于模型属性值都存在时的值。</span><span class="sxs-lookup"><span data-stu-id="01980-213">In the view, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-department-edit-view"></a><span data-ttu-id="01980-214">更新部门编辑视图</span><span class="sxs-lookup"><span data-stu-id="01980-214">Update the Department Edit view</span></span>

<span data-ttu-id="01980-215">在*Views/Departments/Edit.cshtml*，进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="01980-215">In *Views/Departments/Edit.cshtml*, make the following changes:</span></span>

* <span data-ttu-id="01980-216">添加隐藏的字段以保存`RowVersion`立即之后的隐藏的字段的属性值`DepartmentID`属性。</span><span class="sxs-lookup"><span data-stu-id="01980-216">Add a hidden field to save the `RowVersion` property value, immediately following the hidden field for the `DepartmentID` property.</span></span>

* <span data-ttu-id="01980-217">将"选择管理员"选项添加到下拉列表中。</span><span class="sxs-lookup"><span data-stu-id="01980-217">Add a "Select Administrator" option to the drop-down list.</span></span>

[!code-html[Main](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts-in-the-edit-page"></a><span data-ttu-id="01980-218">在编辑页中测试并发冲突</span><span class="sxs-lookup"><span data-stu-id="01980-218">Test concurrency conflicts in the Edit page</span></span>

<span data-ttu-id="01980-219">运行应用并转到部门索引页。</span><span class="sxs-lookup"><span data-stu-id="01980-219">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="01980-220">右键单击**编辑**英语部门和选择的超链接**新选项卡中打开**，然后单击**编辑**英语部门的超链接。</span><span class="sxs-lookup"><span data-stu-id="01980-220">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**, then click the **Edit** hyperlink for the English department.</span></span> <span data-ttu-id="01980-221">两个浏览器选项卡现在显示的相同信息。</span><span class="sxs-lookup"><span data-stu-id="01980-221">The two browser tabs now display the same information.</span></span>

<span data-ttu-id="01980-222">更改第一个浏览器选项卡中的字段，然后单击**保存**。</span><span class="sxs-lookup"><span data-stu-id="01980-222">Change a field in the first browser tab and click **Save**.</span></span>

![部门编辑更改后的第 1 页](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="01980-224">浏览器将显示更改后的值的索引页。</span><span class="sxs-lookup"><span data-stu-id="01980-224">The browser shows the Index page with the changed value.</span></span>

<span data-ttu-id="01980-225">更改第二个浏览器选项卡中的字段。</span><span class="sxs-lookup"><span data-stu-id="01980-225">Change a field in the second browser tab.</span></span>

![部门编辑更改后的第 2 页](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="01980-227">单击“保存” 。</span><span class="sxs-lookup"><span data-stu-id="01980-227">Click **Save**.</span></span> <span data-ttu-id="01980-228">你看到一条错误消息：</span><span class="sxs-lookup"><span data-stu-id="01980-228">You see an error message:</span></span>

![部门编辑页错误消息](concurrency/_static/edit-error.png)

<span data-ttu-id="01980-230">单击**保存**试。</span><span class="sxs-lookup"><span data-stu-id="01980-230">Click **Save** again.</span></span> <span data-ttu-id="01980-231">保存在第二个浏览器选项卡中输入的值。</span><span class="sxs-lookup"><span data-stu-id="01980-231">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="01980-232">索引页面显示时，将显示保存的值。</span><span class="sxs-lookup"><span data-stu-id="01980-232">You see the saved values when the Index page appears.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="01980-233">更新删除页</span><span class="sxs-lookup"><span data-stu-id="01980-233">Update the Delete page</span></span>

<span data-ttu-id="01980-234">对于删除页中，实体框架检测并发冲突某人其他类似的方式编辑部门引起。</span><span class="sxs-lookup"><span data-stu-id="01980-234">For the Delete page, the Entity Framework detects concurrency conflicts caused by someone else editing the department in a similar manner.</span></span> <span data-ttu-id="01980-235">当 HttpGet`Delete`方法会显示确认视图，则视图包括原始`RowVersion`隐藏字段中的值。</span><span class="sxs-lookup"><span data-stu-id="01980-235">When the HttpGet `Delete` method displays the confirmation view, the view includes the original `RowVersion` value in a hidden field.</span></span> <span data-ttu-id="01980-236">然后，该值将供 HttpPost`Delete`用户确认删除时调用的方法。</span><span class="sxs-lookup"><span data-stu-id="01980-236">That value is then available to the HttpPost `Delete` method that's called when the user confirms the deletion.</span></span> <span data-ttu-id="01980-237">当实体框架创建 SQL DELETE 命令时，它包括 WHERE 子句与原始`RowVersion`值。</span><span class="sxs-lookup"><span data-stu-id="01980-237">When the Entity Framework creates the SQL DELETE command, it includes a WHERE clause with the original `RowVersion` value.</span></span> <span data-ttu-id="01980-238">如果零行中的命令结果的影响 （含义后显示删除确认页，已更改行），则会引发一个并发异常，和 HttpGet`Delete`使用错误标志设置为 true 以重新显示调用方法确认页，并一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="01980-238">If the command results in zero rows affected (meaning the row was changed after the Delete confirmation page was displayed), a concurrency exception is thrown, and the HttpGet `Delete` method is called with an error flag set to true in order to redisplay the confirmation page with an error message.</span></span> <span data-ttu-id="01980-239">还有可能因为行由其他用户时，删除，因此在这种情况下不显示任何错误消息影响了零行。</span><span class="sxs-lookup"><span data-stu-id="01980-239">It's also possible that zero rows were affected because the row was deleted by another user, so in that case no error message is displayed.</span></span>

### <a name="update-the-delete-methods-in-the-departments-controller"></a><span data-ttu-id="01980-240">更新部门控制器中的删除方法</span><span class="sxs-lookup"><span data-stu-id="01980-240">Update the Delete methods in the Departments controller</span></span>

<span data-ttu-id="01980-241">在*DepartmentsController.cs*，替换 HttpGet`Delete`方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="01980-241">In *DepartmentsController.cs*, replace the HttpGet `Delete` method with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

<span data-ttu-id="01980-242">该方法将接受可选参数，该值指示是否页正在后将重新显示并发错误。</span><span class="sxs-lookup"><span data-stu-id="01980-242">The method accepts an optional parameter that indicates whether the page is being redisplayed after a concurrency error.</span></span> <span data-ttu-id="01980-243">如果此标志为 true 且不再指定部门存在，它是由另一个用户中删除。</span><span class="sxs-lookup"><span data-stu-id="01980-243">If this flag is true and the department specified no longer exists, it was deleted by another user.</span></span> <span data-ttu-id="01980-244">在这种情况下，代码将重定向到索引页面。</span><span class="sxs-lookup"><span data-stu-id="01980-244">In that case, the code redirects to the Index page.</span></span>  <span data-ttu-id="01980-245">如果此标志为 true 且部门确实存在，则它已由另一个用户。</span><span class="sxs-lookup"><span data-stu-id="01980-245">If this flag is true and the Department does exist, it was changed by another user.</span></span> <span data-ttu-id="01980-246">在这种情况下，代码将一条错误消息发送到视图使用`ViewData`。</span><span class="sxs-lookup"><span data-stu-id="01980-246">In that case, the code sends an error message to the view using `ViewData`.</span></span>  

<span data-ttu-id="01980-247">HttpPost 中的代码替换`Delete`方法 (名为`DeleteConfirmed`) 替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="01980-247">Replace the code in the HttpPost `Delete` method (named `DeleteConfirmed`) with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

<span data-ttu-id="01980-248">在您刚更换的基架代码，此方法接受仅记录 ID:</span><span class="sxs-lookup"><span data-stu-id="01980-248">In the scaffolded code that you just replaced, this method accepted only a record ID:</span></span>


```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

<span data-ttu-id="01980-249">你已更改为创建的模型联编程序的部门实体实例的此参数。</span><span class="sxs-lookup"><span data-stu-id="01980-249">You've changed this parameter to a Department entity instance created by the model binder.</span></span> <span data-ttu-id="01980-250">这样，EF 访问为除了的记录密钥 RowVersion 的属性值。</span><span class="sxs-lookup"><span data-stu-id="01980-250">This gives EF access to the RowVersion property value in addition to the record key.</span></span>

```csharp
public async Task<IActionResult> Delete(Department department)
```

<span data-ttu-id="01980-251">您还已更改的操作方法名称`DeleteConfirmed`到`Delete`。</span><span class="sxs-lookup"><span data-stu-id="01980-251">You have also changed the action method name from `DeleteConfirmed` to `Delete`.</span></span> <span data-ttu-id="01980-252">基架的代码使用名称`DeleteConfirmed`为 HttpPost 方法提供一个唯一的签名。</span><span class="sxs-lookup"><span data-stu-id="01980-252">The scaffolded code used the name `DeleteConfirmed` to give the HttpPost method a unique signature.</span></span> <span data-ttu-id="01980-253">（CLR 需要具有不同的方法参数的重载的方法。）现在，签名是唯一的可以停在使用 MVC 约定，并使用 HttpPost 和 HttpGet 删除方法相同的名称。</span><span class="sxs-lookup"><span data-stu-id="01980-253">(The CLR requires overloaded methods to have different method parameters.) Now that the signatures are unique, you can stick with the MVC convention and use the same name for the HttpPost and HttpGet delete methods.</span></span>

<span data-ttu-id="01980-254">如果该部门已删除，`AnyAsync`方法返回 false，应用程序只需将返回到 Index 方法。</span><span class="sxs-lookup"><span data-stu-id="01980-254">If the department is already deleted, the `AnyAsync` method returns false and the application just goes back to the Index method.</span></span>

<span data-ttu-id="01980-255">如果捕获到并发错误，则该代码将显示删除确认页，并提供一个标志，指示它应显示并发错误消息。</span><span class="sxs-lookup"><span data-stu-id="01980-255">If a concurrency error is caught, the code redisplays the Delete confirmation page and provides a flag that indicates it should display a concurrency error message.</span></span>

### <a name="update-the-delete-view"></a><span data-ttu-id="01980-256">更新删除视图</span><span class="sxs-lookup"><span data-stu-id="01980-256">Update the Delete view</span></span>

<span data-ttu-id="01980-257">在*Views/Departments/Delete.cshtml*，基架的代码替换为下面的代码用于添加一个错误消息字段和 DepartmentID 和 RowVersion 属性的隐藏的字段。</span><span class="sxs-lookup"><span data-stu-id="01980-257">In *Views/Departments/Delete.cshtml*, replace the scaffolded code with the following code that adds an error message field and hidden fields for the DepartmentID and RowVersion properties.</span></span> <span data-ttu-id="01980-258">突出显示所做的更改。</span><span class="sxs-lookup"><span data-stu-id="01980-258">The changes are highlighted.</span></span>

[!code-html[Main](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

<span data-ttu-id="01980-259">这将做出以下更改：</span><span class="sxs-lookup"><span data-stu-id="01980-259">This makes the following changes:</span></span>

* <span data-ttu-id="01980-260">添加一条错误消息之间`h2`和`h3`标题。</span><span class="sxs-lookup"><span data-stu-id="01980-260">Adds an error message between the `h2` and `h3` headings.</span></span>

* <span data-ttu-id="01980-261">替换 FullName 中 FirstMidName**管理员**字段。</span><span class="sxs-lookup"><span data-stu-id="01980-261">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>

* <span data-ttu-id="01980-262">删除 RowVersion 字段。</span><span class="sxs-lookup"><span data-stu-id="01980-262">Removes the RowVersion field.</span></span>

* <span data-ttu-id="01980-263">添加的隐藏的字段`RowVersion`属性。</span><span class="sxs-lookup"><span data-stu-id="01980-263">Adds a hidden field for the `RowVersion` property.</span></span>

<span data-ttu-id="01980-264">运行应用并转到部门索引页。</span><span class="sxs-lookup"><span data-stu-id="01980-264">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="01980-265">右键单击**删除**英语部门和选择的超链接**新选项卡中打开**，然后在第一个选项卡中单击**编辑**英语部门的超链接。</span><span class="sxs-lookup"><span data-stu-id="01980-265">Right-click the **Delete** hyperlink for the English department and select **Open in new tab**, then in the first tab click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="01980-266">在第一个窗口中，更改一个值，然后单击**保存**:</span><span class="sxs-lookup"><span data-stu-id="01980-266">In the first window, change one of the values, and click **Save**:</span></span>

![在删除之前的更改后的部门编辑页](concurrency/_static/edit-after-change-for-delete.png)

<span data-ttu-id="01980-268">在第二个选项卡上，单击**删除**。</span><span class="sxs-lookup"><span data-stu-id="01980-268">In the second tab, click **Delete**.</span></span> <span data-ttu-id="01980-269">请参阅并发错误消息，并使用什么是当前数据库中刷新的部门值。</span><span class="sxs-lookup"><span data-stu-id="01980-269">You see the concurrency error message, and the Department values are refreshed with what's currently in the database.</span></span>

![出现并发错误的部门删除确认页](concurrency/_static/delete-error.png)

<span data-ttu-id="01980-271">如果你单击**删除**同样，在重定向到显示部门已被删除的索引页。</span><span class="sxs-lookup"><span data-stu-id="01980-271">If you click **Delete** again, you're redirected to the Index page, which shows that the department has been deleted.</span></span>

## <a name="update-details-and-create-views"></a><span data-ttu-id="01980-272">更新详细信息，并创建视图</span><span class="sxs-lookup"><span data-stu-id="01980-272">Update Details and Create views</span></span>

<span data-ttu-id="01980-273">（可选） 可以清理详细信息中的基架代码并创建视图。</span><span class="sxs-lookup"><span data-stu-id="01980-273">You can optionally clean up scaffolded code in the Details and Create views.</span></span>

<span data-ttu-id="01980-274">中的代码替换*Views/Departments/Details.cshtml*删除 RowVersion 列和显示管理员的完整名称。</span><span class="sxs-lookup"><span data-stu-id="01980-274">Replace the code in *Views/Departments/Details.cshtml* to delete the RowVersion column and show the full name of the Administrator.</span></span>

[!code-html[Main](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

<span data-ttu-id="01980-275">中的代码替换*Views/Departments/Create.cshtml*将选择的选项添加到下拉列表。</span><span class="sxs-lookup"><span data-stu-id="01980-275">Replace the code in *Views/Departments/Create.cshtml* to add a Select option to the drop-down list.</span></span>

[!code-html[Main](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="summary"></a><span data-ttu-id="01980-276">摘要</span><span class="sxs-lookup"><span data-stu-id="01980-276">Summary</span></span>

<span data-ttu-id="01980-277">这将完成简介处理并发冲突。</span><span class="sxs-lookup"><span data-stu-id="01980-277">This completes the introduction to handling concurrency conflicts.</span></span> <span data-ttu-id="01980-278">有关如何处理在 EF 核心中的并发的详细信息，请参阅[并发冲突](https://docs.microsoft.com/ef/core/saving/concurrency)。</span><span class="sxs-lookup"><span data-stu-id="01980-278">For more information about how to handle concurrency in EF Core, see [Concurrency conflicts](https://docs.microsoft.com/ef/core/saving/concurrency).</span></span> <span data-ttu-id="01980-279">下一教程演示如何实现教师和学生实体的每个层次结构一个表继承。</span><span class="sxs-lookup"><span data-stu-id="01980-279">The next tutorial shows how to implement table-per-hierarchy inheritance for the Instructor and Student entities.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="01980-280">[上一页](update-related-data.md)
[下一页](inheritance.md)</span><span class="sxs-lookup"><span data-stu-id="01980-280">[Previous](update-related-data.md)
[Next](inheritance.md)</span></span>  
