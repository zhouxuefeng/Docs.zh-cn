---
title: "ASP.NET 核心 MVC 与 EF 核心-迁移-4 的 10"
author: tdykstra
description: "在本教程中，你开始使用 EF 核心迁移功能用于管理 ASP.NET 核心 MVC 应用程序中的数据模型更改。"
keywords: "ASP.NET 核心，实体框架核心，迁移"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 81f6c9c2-a819-4f3a-97a4-4b0503b56c26
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/migrations
ms.openlocfilehash: 4d81099d1ab97a8a49d96657153a54aa96dd6bf8
ms.sourcegitcommit: 74e22e08e3b08cb576e5184d16f4af5656c13c0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2017
---
# <a name="migrations---ef-core-with-aspnet-core-mvc-tutorial-4-of-10"></a><span data-ttu-id="265a8-104">迁移的 EF 内核，它们有 ASP.NET 核心 MVC 教程 (10 的第 4)</span><span class="sxs-lookup"><span data-stu-id="265a8-104">Migrations - EF Core with ASP.NET Core MVC tutorial (4 of 10)</span></span>

<span data-ttu-id="265a8-105">通过[Tom Dykstra](https://github.com/tdykstra)和[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="265a8-105">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="265a8-106">Contoso 大学示例 web 应用程序演示如何创建使用实体框架核心和 Visual Studio 的 ASP.NET 核心 MVC web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="265a8-106">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="265a8-107">有关教程系列的信息，请参阅[序列中的第一个教程](intro.md)。</span><span class="sxs-lookup"><span data-stu-id="265a8-107">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="265a8-108">在本教程中，你启动使用 EF 核心迁移功能来管理数据模型更改。</span><span class="sxs-lookup"><span data-stu-id="265a8-108">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="265a8-109">在更高版本的教程中，你将添加多个迁移的数据模型更改时。</span><span class="sxs-lookup"><span data-stu-id="265a8-109">In later tutorials, you'll add more migrations as you change the data model.</span></span>

## <a name="introduction-to-migrations"></a><span data-ttu-id="265a8-110">迁移简介</span><span class="sxs-lookup"><span data-stu-id="265a8-110">Introduction to migrations</span></span>

<span data-ttu-id="265a8-111">当你开发新的应用程序时，你的数据模型更改通常情况下，和每次将模型更改，它将获取与数据库不同步。</span><span class="sxs-lookup"><span data-stu-id="265a8-111">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="265a8-112">通过配置实体框架可以创建数据库，如果它不存在启动这些教程。</span><span class="sxs-lookup"><span data-stu-id="265a8-112">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="265a8-113">然后每次更改数据模型-添加、 删除，或更改实体类或更改您的 DbContext 类-可以删除数据库，然后 EF 创建一个新的匹配模型，并设定其种子使用测试数据。</span><span class="sxs-lookup"><span data-stu-id="265a8-113">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="265a8-114">保持数据库与数据模型同步此方法适用很好地部署到生产应用程序之前。</span><span class="sxs-lookup"><span data-stu-id="265a8-114">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="265a8-115">在生产它通常存储数据，你想要保留，并不想丢失的所有内容每次运行应用程序时进行了更改，例如添加新列。</span><span class="sxs-lookup"><span data-stu-id="265a8-115">When the application is running in production it is usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="265a8-116">EF 核心迁移功能启用 EF 更新数据库架构，而不是创建新的数据库，从而解决了此问题。</span><span class="sxs-lookup"><span data-stu-id="265a8-116">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

## <a name="entity-framework-core-nuget-packages-for-migrations"></a><span data-ttu-id="265a8-117">迁移的实体框架核心 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="265a8-117">Entity Framework Core NuGet packages for migrations</span></span>

<span data-ttu-id="265a8-118">若要使用迁移，你可以使用**程序包管理器控制台**(PMC) 或命令行界面 (CLI)。</span><span class="sxs-lookup"><span data-stu-id="265a8-118">To work with migrations, you can use the **Package Manager Console** (PMC) or the command-line interface (CLI).</span></span>  <span data-ttu-id="265a8-119">这些教程介绍如何使用 CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="265a8-119">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="265a8-120">有关 PMC 信息位于[本教程末尾](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="265a8-120">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

<span data-ttu-id="265a8-121">中提供了命令行界面 (CLI) 的 EF 工具[Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet)。</span><span class="sxs-lookup"><span data-stu-id="265a8-121">The EF tools for the command-line interface (CLI) are provided in [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet).</span></span> <span data-ttu-id="265a8-122">若要安装此包，将其添加到`DotNetCliToolReference`中的集合*.csproj*文件，如下所示。</span><span class="sxs-lookup"><span data-stu-id="265a8-122">To install this package, add it to the `DotNetCliToolReference` collection in the *.csproj* file, as shown.</span></span> <span data-ttu-id="265a8-123">**注意：**必须安装此包通过编辑*.csproj*文件; 不能使用`install-package`命令或包管理器 GUI。</span><span class="sxs-lookup"><span data-stu-id="265a8-123">**Note:** You have to install this package by editing the *.csproj* file; you can't use the `install-package` command or the package manager GUI.</span></span> <span data-ttu-id="265a8-124">你可以编辑*.csproj*通过右键单击中的项目名称的文件**解决方案资源管理器**并选择**编辑 ContosoUniversity.csproj**。</span><span class="sxs-lookup"><span data-stu-id="265a8-124">You can edit the *.csproj* file by right-clicking the project name in **Solution Explorer** and selecting **Edit ContosoUniversity.csproj**.</span></span>

[!code-xml[](intro/samples/cu/ContosoUniversity.csproj?range=12-15&highlight=2)]
  
<span data-ttu-id="265a8-125">（在此示例中的版本号为当前在撰写本教程时。）</span><span class="sxs-lookup"><span data-stu-id="265a8-125">(The version numbers in this example were current when the tutorial was written.)</span></span> 

## <a name="change-the-connection-string"></a><span data-ttu-id="265a8-126">更改连接字符串</span><span class="sxs-lookup"><span data-stu-id="265a8-126">Change the connection string</span></span>

<span data-ttu-id="265a8-127">在*appsettings.json*文件，将连接字符串中数据库的名称更改为 ContosoUniversity2 或尚未在所使用的计算机使用的一些其他名称。</span><span class="sxs-lookup"><span data-stu-id="265a8-127">In the *appsettings.json* file, change the name of the database in the connection string to ContosoUniversity2 or some other name that you haven't used on the computer you're using.</span></span>

<span data-ttu-id="265a8-128">[!code-json[Main](intro/samples/cu/appsettings2.json?range=1-4)]</span><span class="sxs-lookup"><span data-stu-id="265a8-128">[!code-json[Main](intro/samples/cu/appsettings2.json?range=1-4)]</span></span>

<span data-ttu-id="265a8-129">此更改将项目设置，以便第一次迁移将创建一个新的数据库。</span><span class="sxs-lookup"><span data-stu-id="265a8-129">This change sets up the project so that the first migration will create a new database.</span></span> <span data-ttu-id="265a8-130">这不是必需的入门知识迁移，但你将看到更高版本的原因是一个不错的主意。</span><span class="sxs-lookup"><span data-stu-id="265a8-130">This isn't required for getting started with migrations, but you'll see later why it's a good idea.</span></span>

> [!NOTE]
> <span data-ttu-id="265a8-131">作为对不断变化的数据库名称的替代方法，您可以删除数据库。</span><span class="sxs-lookup"><span data-stu-id="265a8-131">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="265a8-132">使用**SQL Server 对象资源管理器**(SSOX) 或`database drop`CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="265a8-132">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
> ```console
> dotnet ef database drop
> ```
> <span data-ttu-id="265a8-133">以下部分说明如何运行 CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="265a8-133">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="265a8-134">创建初始迁移</span><span class="sxs-lookup"><span data-stu-id="265a8-134">Create an initial migration</span></span>

<span data-ttu-id="265a8-135">保存所做的更改并生成项目。</span><span class="sxs-lookup"><span data-stu-id="265a8-135">Save your changes and build the project.</span></span> <span data-ttu-id="265a8-136">然后打开命令窗口并导航到项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="265a8-136">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="265a8-137">下面是快速办法做到这一点：</span><span class="sxs-lookup"><span data-stu-id="265a8-137">Here's a quick way to do that:</span></span>

* <span data-ttu-id="265a8-138">在**解决方案资源管理器**，右键单击项目，然后选择**在文件资源管理器中打开**从上下文菜单。</span><span class="sxs-lookup"><span data-stu-id="265a8-138">In **Solution Explorer**, right-click the project and choose **Open in File Explorer** from the context menu.</span></span>

  ![在文件资源管理器菜单项中打开](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="265a8-140">在地址栏中输入"cmd"，然后按 Enter。</span><span class="sxs-lookup"><span data-stu-id="265a8-140">Enter "cmd" in the address bar and press Enter.</span></span>

  ![打开命令窗口](migrations/_static/open-command-window.png)

<span data-ttu-id="265a8-142">在命令窗口中输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="265a8-142">Enter the following command in the command window:</span></span>

```console
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="265a8-143">你看到类似命令窗口中的以下输出：</span><span class="sxs-lookup"><span data-stu-id="265a8-143">You see output like the following in the command window:</span></span>

```console
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> <span data-ttu-id="265a8-144">如果你看到一条错误消息*任何可执行文件找到匹配命令 dotnet 的 ef*，请参阅[这篇博客文章](http://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/)以帮助进行故障排除。</span><span class="sxs-lookup"><span data-stu-id="265a8-144">If you see an error message *No executable found matching command "dotnet-ef"*, see [this blog post](http://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) for help troubleshooting.</span></span>

<span data-ttu-id="265a8-145">如果你看到一条错误消息"*无法访问文件...ContosoUniversity.dll 由于另一个进程正在使用。*"，在 Windows 系统任务栏中，查找 IIS Express 图标并右键单击它，然后单击**ContosoUniversity > 停止站点**。</span><span class="sxs-lookup"><span data-stu-id="265a8-145">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="265a8-146">检查向上和向下方法</span><span class="sxs-lookup"><span data-stu-id="265a8-146">Examine the Up and Down methods</span></span>

<span data-ttu-id="265a8-147">当执行`migrations add`命令时，EF 生成将从头开始创建数据库的代码。</span><span class="sxs-lookup"><span data-stu-id="265a8-147">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="265a8-148">此代码位于*迁移*文件夹中，在名为的文件*\<时间戳 > _InitialCreate.cs*。</span><span class="sxs-lookup"><span data-stu-id="265a8-148">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="265a8-149">`Up`方法`InitialCreate`类创建对应的数据模型实体集，将数据库表和`Down`方法删除它们，如下面的示例中所示。</span><span class="sxs-lookup"><span data-stu-id="265a8-149">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

<span data-ttu-id="265a8-150">[!code-csharp[Main](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]</span><span class="sxs-lookup"><span data-stu-id="265a8-150">[!code-csharp[Main](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]</span></span>

<span data-ttu-id="265a8-151">迁移调用`Up`方法以实现迁移的数据模型更改。</span><span class="sxs-lookup"><span data-stu-id="265a8-151">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="265a8-152">当你输入命令回滚更新，迁移调用`Down`方法。</span><span class="sxs-lookup"><span data-stu-id="265a8-152">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="265a8-153">此代码适用于你输入时创建的初始迁移`migrations add InitialCreate`命令。</span><span class="sxs-lookup"><span data-stu-id="265a8-153">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="265a8-154">迁移 name 参数 (如在示例中的"InitialCreate") 使用的文件名称，并可以是任何所需内容。</span><span class="sxs-lookup"><span data-stu-id="265a8-154">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="265a8-155">最好选择的单词或短语，总结了中迁移正在进行的内容。</span><span class="sxs-lookup"><span data-stu-id="265a8-155">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="265a8-156">例如，你可能会将更高版本的迁移"AddDepartmentTable"。</span><span class="sxs-lookup"><span data-stu-id="265a8-156">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="265a8-157">如果数据库已存在时创建初始迁移，则生成的数据库创建代码，但它无需运行，因为数据库已与匹配的数据模型。</span><span class="sxs-lookup"><span data-stu-id="265a8-157">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="265a8-158">时应用部署到另一个环境，其中数据库不存在，请运行此代码将创建数据库时，因此它会先测试一个好办法。</span><span class="sxs-lookup"><span data-stu-id="265a8-158">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="265a8-159">这就是为什么以便迁移可以创建一个从零开始的新更改连接字符串前面-中数据库的名称。</span><span class="sxs-lookup"><span data-stu-id="265a8-159">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="examine-the-data-model-snapshot"></a><span data-ttu-id="265a8-160">检查数据模型快照</span><span class="sxs-lookup"><span data-stu-id="265a8-160">Examine the data model snapshot</span></span>

<span data-ttu-id="265a8-161">迁移还会创建*快照*中的当前数据库架构*Migrations/SchoolContextModelSnapshot.cs*。</span><span class="sxs-lookup"><span data-stu-id="265a8-161">Migrations also creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="265a8-162">下面是该代码如下所示：</span><span class="sxs-lookup"><span data-stu-id="265a8-162">Here's what that code looks like:</span></span>

<span data-ttu-id="265a8-163">[!code-csharp[Main](intro/samples/cu/Migrations/SchoolContextModelSnapshot1.cs?name=snippet_Truncate)]</span><span class="sxs-lookup"><span data-stu-id="265a8-163">[!code-csharp[Main](intro/samples/cu/Migrations/SchoolContextModelSnapshot1.cs?name=snippet_Truncate)]</span></span>

<span data-ttu-id="265a8-164">以代码表示为当前的数据库架构，因为 EF 核心没有与要创建迁移的数据库进行交互。</span><span class="sxs-lookup"><span data-stu-id="265a8-164">Because the current database schema is represented in code, EF Core doesn't have to interact with the database to create migrations.</span></span> <span data-ttu-id="265a8-165">当添加迁移时，EF 确定通过比较快照文件的数据模型更改的内容。</span><span class="sxs-lookup"><span data-stu-id="265a8-165">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span> <span data-ttu-id="265a8-166">仅当有更新数据库时，EF 与数据库交互。</span><span class="sxs-lookup"><span data-stu-id="265a8-166">EF interacts with the database only when it has to update the database.</span></span> 

<span data-ttu-id="265a8-167">快照文件必须保持与创建它，因此不能只需通过删除名为的文件中删除迁移的迁移同步*\<时间戳 > _\<migrationname >.cs*。</span><span class="sxs-lookup"><span data-stu-id="265a8-167">The snapshot file has to be kept in sync with the migrations that create it, so you can't remove a migration just by deleting the file named  *\<timestamp>_\<migrationname>.cs*.</span></span> <span data-ttu-id="265a8-168">如果你删除该文件，将剩余的迁移将与数据库快照文件不同步。</span><span class="sxs-lookup"><span data-stu-id="265a8-168">If you delete that file, the remaining migrations will be out of sync with the database snapshot file.</span></span> <span data-ttu-id="265a8-169">若要删除你添加的最后一个迁移，使用[dotnet ef 迁移删除](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)命令。</span><span class="sxs-lookup"><span data-stu-id="265a8-169">To delete the last migration that you added, use the [dotnet ef migrations remove](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command.</span></span>

## <a name="apply-the-migration-to-the-database"></a><span data-ttu-id="265a8-170">适用于数据库的迁移</span><span class="sxs-lookup"><span data-stu-id="265a8-170">Apply the migration to the database</span></span>

<span data-ttu-id="265a8-171">在命令窗口中，输入以下命令以在其中创建数据库和表。</span><span class="sxs-lookup"><span data-stu-id="265a8-171">In the command window, enter the following command to create the database and tables in it.</span></span>

```console
dotnet ef database update
```

<span data-ttu-id="265a8-172">该命令的输出是类似于`migrations add`命令时，只不过你请参阅日志，以将数据库设置的 SQL 命令。</span><span class="sxs-lookup"><span data-stu-id="265a8-172">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="265a8-173">在下面的示例输出中，大部分日志被省略。</span><span class="sxs-lookup"><span data-stu-id="265a8-173">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="265a8-174">如果你不希望查看此级别的日志消息中的详细信息，你可以更改中的日志级别*appsettings。Development.json*文件。</span><span class="sxs-lookup"><span data-stu-id="265a8-174">If you prefer not to see this level of detail in log messages, you can change the log levels in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="265a8-175">有关详细信息，请参阅[简介日志记录](xref:fundamentals/logging)。</span><span class="sxs-lookup"><span data-stu-id="265a8-175">For more information, see [Introduction to logging](xref:fundamentals/logging).</span></span>

```text
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (467ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (20ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20170816151242_InitialCreate', N'2.0.0-rtm-26452');
Done.
```

<span data-ttu-id="265a8-176">使用**SQL Server 对象资源管理器**检查数据库，就像在第一个教程。</span><span class="sxs-lookup"><span data-stu-id="265a8-176">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="265a8-177">你会注意到 __EFMigrationsHistory 表，用于跟踪的哪些迁移已应用于数据库的添加。</span><span class="sxs-lookup"><span data-stu-id="265a8-177">You'll notice the addition of an __EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="265a8-178">查看该表中的数据，你将看到第一次迁移一个行。</span><span class="sxs-lookup"><span data-stu-id="265a8-178">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="265a8-179">（前面的 CLI 输出示例中的最后一个日志演示创建此行的 INSERT 语句。）</span><span class="sxs-lookup"><span data-stu-id="265a8-179">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="265a8-180">运行应用程序以验证所有内容仍适用之前相同。</span><span class="sxs-lookup"><span data-stu-id="265a8-180">Run the application to verify that everything still works the same as before.</span></span>

![学生索引页](migrations/_static/students-index.png)

<a id="pmc"></a>
## <a name="command-line-interface-cli-vs-package-manager-console-pmc"></a><span data-ttu-id="265a8-182">命令行界面 (CLI) vs。程序包管理器控制台 (PMC)</span><span class="sxs-lookup"><span data-stu-id="265a8-182">Command-line interface (CLI) vs. Package Manager Console (PMC)</span></span>

<span data-ttu-id="265a8-183">EF 工具，可从.NET 核心 CLI 命令或从 Visual Studio 中的 PowerShell cmdlet 管理迁移为**程序包管理器控制台**(PMC) 窗口。</span><span class="sxs-lookup"><span data-stu-id="265a8-183">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="265a8-184">本教程演示如何使用 CLI，但如果你愿意，可以使用 PMC。</span><span class="sxs-lookup"><span data-stu-id="265a8-184">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="265a8-185">PMC 命令的 EF 命令处于[Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)包。</span><span class="sxs-lookup"><span data-stu-id="265a8-185">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="265a8-186">此包已包含在[Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage，因此你不必安装它。</span><span class="sxs-lookup"><span data-stu-id="265a8-186">This package is already included in the [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage, so you don't have to install it.</span></span>

<span data-ttu-id="265a8-187">**重要说明：**这不的是通过编辑的 cli 安装与相同的包*.csproj*文件。</span><span class="sxs-lookup"><span data-stu-id="265a8-187">**Important:** This is not the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="265a8-188">此名称以结尾`Tools`，与中结束的 CLI 包名称不同`Tools.DotNet`。</span><span class="sxs-lookup"><span data-stu-id="265a8-188">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="265a8-189">有关 CLI 命令的详细信息，请参阅[.NET 核心 CLI](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="265a8-189">For more information about the CLI commands, see [.NET Core CLI](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet).</span></span> 

<span data-ttu-id="265a8-190">有关 PMC 命令的详细信息，请参阅[Package Manager Console (Visual Studio)](https://docs.microsoft.com/ef/core/miscellaneous/cli/powershell)。</span><span class="sxs-lookup"><span data-stu-id="265a8-190">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](https://docs.microsoft.com/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="summary"></a><span data-ttu-id="265a8-191">摘要</span><span class="sxs-lookup"><span data-stu-id="265a8-191">Summary</span></span>

<span data-ttu-id="265a8-192">在本教程中，你已了解如何创建并应用你第一次迁移。</span><span class="sxs-lookup"><span data-stu-id="265a8-192">In this tutorial, you've seen how to create and apply your first migration.</span></span> <span data-ttu-id="265a8-193">在下一步的教程中，将首先通过展开数据模型来查看更高级的主题。</span><span class="sxs-lookup"><span data-stu-id="265a8-193">In the next tutorial, you'll begin looking at more advanced topics by expanding the data model.</span></span> <span data-ttu-id="265a8-194">此过程将创建并应用将附加迁移。</span><span class="sxs-lookup"><span data-stu-id="265a8-194">Along the way you'll create and apply additional migrations.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="265a8-195">[上一页](sort-filter-page.md)
[下一页](complex-data-model.md)</span><span class="sxs-lookup"><span data-stu-id="265a8-195">[Previous](sort-filter-page.md)
[Next](complex-data-model.md)</span></span>  
