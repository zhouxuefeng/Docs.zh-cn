---
title: "ASP.NET 核心 MVC 与 EF 核心-迁移-4 的 10"
author: tdykstra
description: "在本教程中，你开始使用 EF 核心迁移功能用于管理 ASP.NET 核心 MVC 应用程序中的数据模型更改。"
keywords: "ASP.NET Core, Entity Framework Core, 迁移"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 81f6c9c2-a819-4f3a-97a4-4b0503b56c26
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/migrations
ms.openlocfilehash: 638bef0cda14f53a326c66c6a5da3f3c1bb762c6
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="migrations---ef-core-with-aspnet-core-mvc-tutorial-4-of-10"></a>迁移的 EF 内核，它们有 ASP.NET 核心 MVC 教程 (10 的第 4)

通过[Tom Dykstra](https://github.com/tdykstra)和[Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso 大学示例 web 应用程序演示如何创建使用实体框架核心和 Visual Studio 的 ASP.NET 核心 MVC web 应用程序。 有关教程系列的信息，请参阅[序列中的第一个教程](intro.md)。

在本教程中，你启动使用 EF 核心迁移功能来管理数据模型更改。 在更高版本的教程中，你将添加多个迁移的数据模型更改时。

## <a name="introduction-to-migrations"></a>迁移简介

当你开发新的应用程序时，你的数据模型更改通常情况下，和每次将模型更改，它将获取与数据库不同步。 通过配置实体框架可以创建数据库，如果它不存在启动这些教程。 然后每次更改数据模型-添加、 删除，或更改实体类或更改您的 DbContext 类-可以删除数据库，然后 EF 创建一个新的匹配模型，并设定其种子使用测试数据。

保持数据库与数据模型同步此方法适用很好地部署到生产应用程序之前。 在生产它通常存储数据，你想要保留，并不想丢失的所有内容每次运行应用程序时进行了更改，例如添加新列。 EF 核心迁移功能启用 EF 更新数据库架构，而不是创建新的数据库，从而解决了此问题。

## <a name="entity-framework-core-nuget-packages-for-migrations"></a>用于进行迁移的 Entity Framework Core NuGet 包

若要使用迁移，你可以使用**程序包管理器控制台**(PMC) 或命令行界面 (CLI)。  这些教程介绍如何使用 CLI 命令。 有关 PMC 信息位于[本教程末尾](#pmc)。

[Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet) 中提供了适用于命令行接口 (CLI) 的 EF 工具。 若要安装此包，将其添加到`DotNetCliToolReference`中的集合*.csproj*文件，如下所示。 注意：必须通过编辑 .csproj 文件来安装此包；不能使用 `install-package` 命令或程序包管理器 GUI。 你可以编辑*.csproj*通过右键单击中的项目名称的文件**解决方案资源管理器**并选择**编辑 ContosoUniversity.csproj**。

[!code-xml[](intro/samples/cu/ContosoUniversity.csproj?range=12-15&highlight=2)]
  
（在此示例中的版本号为当前在撰写本教程时。） 

## <a name="change-the-connection-string"></a>更改连接字符串

在*appsettings.json*文件，将连接字符串中数据库的名称更改为 ContosoUniversity2 或尚未在所使用的计算机使用的一些其他名称。

[!code-json[Main](intro/samples/cu/appsettings2.json?range=1-4)]

此更改将项目设置，以便第一次迁移将创建一个新的数据库。 这不是必需的入门知识迁移，但你将看到更高版本的原因是一个不错的主意。

> [!NOTE]
> 作为对不断变化的数据库名称的替代方法，您可以删除数据库。 使用**SQL Server 对象资源管理器**(SSOX) 或`database drop`CLI 命令：
> ```console
> dotnet ef database drop
> ```
> 以下部分说明如何运行 CLI 命令。

## <a name="create-an-initial-migration"></a>创建初始迁移

保存所做的更改并生成项目。 然后打开命令窗口并导航到项目文件夹。 下面是快速办法做到这一点：

* 在**解决方案资源管理器**，右键单击项目，然后选择**在文件资源管理器中打开**从上下文菜单。

  ![在文件资源管理器菜单项中打开](migrations/_static/open-in-file-explorer.png)

* 在地址栏中输入"cmd"，然后按 Enter。

  ![打开命令窗口](migrations/_static/open-command-window.png)

在命令窗口中输入以下命令：

```console
dotnet ef migrations add InitialCreate
```

你看到类似命令窗口中的以下输出：

```console
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> 如果你看到一条错误消息*任何可执行文件找到匹配命令 dotnet 的 ef*，请参阅[这篇博客文章](http://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/)以帮助进行故障排除。

如果你看到一条错误消息"*无法访问文件...ContosoUniversity.dll 由于另一个进程正在使用。*"，在 Windows 系统任务栏中，查找 IIS Express 图标并右键单击它，然后单击**ContosoUniversity > 停止站点**。

## <a name="examine-the-up-and-down-methods"></a>检查向上和向下方法

当执行`migrations add`命令时，EF 生成将从头开始创建数据库的代码。 此代码位于*迁移*文件夹中，在名为的文件*\<时间戳 > _InitialCreate.cs*。 `Up`方法`InitialCreate`类创建对应的数据模型实体集，将数据库表和`Down`方法删除它们，如下面的示例中所示。

[!code-csharp[Main](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

迁移调用`Up`方法以实现迁移的数据模型更改。 当你输入命令回滚更新，迁移调用`Down`方法。

此代码适用于你输入时创建的初始迁移`migrations add InitialCreate`命令。 迁移 name 参数 (如在示例中的"InitialCreate") 使用的文件名称，并可以是任何所需内容。 最好选择的单词或短语，总结了中迁移正在进行的内容。 例如，你可能会将更高版本的迁移"AddDepartmentTable"。

如果数据库已存在时创建初始迁移，则生成的数据库创建代码，但它无需运行，因为数据库已与匹配的数据模型。 时应用部署到另一个环境，其中数据库不存在，请运行此代码将创建数据库时，因此它会先测试一个好办法。 这就是为什么以便迁移可以创建一个从零开始的新更改连接字符串前面-中数据库的名称。

## <a name="examine-the-data-model-snapshot"></a>检查数据模型快照

迁移还会创建*快照*中的当前数据库架构*Migrations/SchoolContextModelSnapshot.cs*。 下面是该代码如下所示：

[!code-csharp[Main](intro/samples/cu/Migrations/SchoolContextModelSnapshot1.cs?name=snippet_Truncate)]

以代码表示为当前的数据库架构，因为 EF 核心没有与要创建迁移的数据库进行交互。 当添加迁移时，EF 确定通过比较快照文件的数据模型更改的内容。 仅当有更新数据库时，EF 与数据库交互。 

快照文件必须保持与创建它，因此不能只需通过删除名为的文件中删除迁移的迁移同步*\<时间戳 > _\<migrationname >.cs*。 如果你删除该文件，将剩余的迁移将与数据库快照文件不同步。 若要删除你添加的最后一个迁移，使用[dotnet ef 迁移删除](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)命令。

## <a name="apply-the-migration-to-the-database"></a>适用于数据库的迁移

在命令窗口中，输入以下命令以在其中创建数据库和表。

```console
dotnet ef database update
```

该命令的输出是类似于`migrations add`命令时，只不过你请参阅日志，以将数据库设置的 SQL 命令。 在下面的示例输出中，大部分日志被省略。 如果你不希望查看此级别的日志消息中的详细信息，你可以更改中的日志级别*appsettings。Development.json*文件。 有关详细信息，请参阅[简介日志记录](xref:fundamentals/logging)。

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

使用**SQL Server 对象资源管理器**检查数据库，就像在第一个教程。  你会注意到 __EFMigrationsHistory 表，用于跟踪的哪些迁移已应用于数据库的添加。 查看该表中的数据，你将看到第一次迁移一个行。 （前面的 CLI 输出示例中的最后一个日志演示创建此行的 INSERT 语句。）

运行应用程序以验证所有内容仍适用之前相同。

![学生索引页](migrations/_static/students-index.png)

<a id="pmc"></a>
## <a name="command-line-interface-cli-vs-package-manager-console-pmc"></a>命令行界面 (CLI) vs。程序包管理器控制台 (PMC)

EF 工具，可从.NET 核心 CLI 命令或从 Visual Studio 中的 PowerShell cmdlet 管理迁移为**程序包管理器控制台**(PMC) 窗口。 本教程演示如何使用 CLI，但如果你愿意，可以使用 PMC。

PMC 命令的 EF 命令处于[Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)包。 此包已包含在[Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage，因此你不必安装它。

**重要说明：**这不的是通过编辑的 cli 安装与相同的包*.csproj*文件。 此名称以结尾`Tools`，与中结束的 CLI 包名称不同`Tools.DotNet`。

有关 CLI 命令的详细信息，请参阅[.NET 核心 CLI](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet)。 

有关 PMC 命令的详细信息，请参阅[Package Manager Console (Visual Studio)](https://docs.microsoft.com/ef/core/miscellaneous/cli/powershell)。

## <a name="summary"></a>摘要

在本教程中，你已了解如何创建并应用你第一次迁移。 在下一步的教程中，将首先通过展开数据模型来查看更高级的主题。 此过程将创建并应用将附加迁移。

>[!div class="step-by-step"]
[上一页](sort-filter-page.md)
[下一页](complex-data-model.md)  
