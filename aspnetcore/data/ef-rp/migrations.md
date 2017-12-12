---
title: "与 EF 核心-迁移-4 的 8 razor 页"
author: rick-anderson
description: "在本教程中，你启动使用 EF 核心迁移功能来管理的 ASP.NET 核心 MVC 应用程序中的数据模型更改。"
keywords: "ASP.NET Core, Entity Framework Core, 迁移"
ms.author: riande
manager: wpickett
ms.date: 10/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-rp/migrations
ms.openlocfilehash: 8549fc522bcd05a5755a449cd6d4b655f00d998b
ms.sourcegitcommit: 6e46abd65973dea796d364a514de9ec2e3e1c1ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2017
---
# <a name="migrations---ef-core-with-razor-pages-tutorial-4-of-8"></a>迁移的 EF 核心 Razor 页教程 (8 的第 4)

通过[Tom Dykstra](https://github.com/tdykstra)， [Jon P Smith](https://twitter.com/thereformedprog)，和[Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[about the series](../../includes/RP-EF/intro.md)]

在本教程中，使用 EF 核心迁移功能用于管理数据模型更改。

如果你遇到无法解决的问题，请下载[对此阶段已完成应用程序](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part4-migrations)。

当开发新的应用程序时、 数据模型经常更改。 每次将模型更改，该模型获取与数据库不同步。 通过配置实体框架可以创建数据库，如果它不存在开始本教程。 每个数据模型更改的时间：

* 数据库已被删除。
* EF 创建一个与型号匹配的新。
* 应用程序设定种子使用测试数据的数据库。

使数据库保持与数据模型同步到此方法适用也直到应用程序部署到生产环境。 当在生产环境中运行应用程序时，它通常将需要维护的数据存储。 应用程序无法启动一个测试数据库每次更改 （如添加新列）。 EF 核心迁移功能启用 EF 核心以更新数据库架构，而不是创建新的 DB，从而解决了此问题。

而不是删除并重新创建数据库，在数据模型更改时，迁移架构更新，并保留现有数据。

## <a name="entity-framework-core-nuget-packages-for-migrations"></a>用于进行迁移的 Entity Framework Core NuGet 包

若要使用迁移，使用**程序包管理器控制台**(PMC) 或命令行界面 (CLI)。 这些教程介绍如何使用 CLI 命令。 有关 PMC 信息位于[本教程末尾](#pmc)。

中提供了命令行界面 (CLI) 的 EF 核心工具[Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet)。 若要安装此包，将其添加到`DotNetCliToolReference`中的集合*.csproj*文件，如下所示。 **注意：**必须编辑安装此程序包*.csproj*文件。 `install-package`命令或包管理器 GUI 不能用于安装此包。 编辑*.csproj*通过右键单击中的项目名称的文件**解决方案资源管理器**并选择**编辑 ContosoUniversity.csproj**。

以下标记显示已更新*.csproj*使用 EF 核心 CLI 工具突出显示的文件：

[!code-xml[](intro/samples/cu/ContosoUniversity.csproj?highlight=12)]
  
前面的示例中的版本号是当前在撰写本教程时。 为其他包中找到的 EF 核心 CLI 工具中使用相同版本。

## <a name="change-the-connection-string"></a>更改连接字符串

在*appsettings.json*文件中，将连接字符串中数据库的名称更改为 ContosoUniversity2。

[!code-json[Main](intro/samples/cu/appsettings2.json?range=1-4)]

更改连接字符串中的数据库名称，则会导致创建新数据库的初始迁移。 创建新数据库，因为具有该名称不存在。 更改连接字符串不是必需的迁移入门知识。

更改数据库名称的替代方法删除数据库。 使用**SQL Server 对象资源管理器**(SSOX) 或`database drop`CLI 命令：

 ```console
 dotnet ef database drop
 ```

以下部分说明如何运行 CLI 命令。

## <a name="create-an-initial-migration"></a>创建初始迁移

生成项目。

打开命令窗口并导航到项目文件夹。 项目文件夹包含*Startup.cs*文件。

在命令窗口中输入以下命令：

```console
dotnet ef migrations add InitialCreate
```

命令窗口显示类似于以下信息：

```console
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

如果迁移失败，出现消息"*无法访问文件...ContosoUniversity.dll 由于另一个进程正在使用。*" 显示：

* 停止 IIS Express。

   * 退出并重新启动 Visual Studio 中，或
   * 在 Windows 系统任务栏中找到的 IIS Express 图标。
   * 右键单击 IIS Express 图标，并依次**ContosoUniversity > 停止站点**。

如果错误消息"生成失败。" 将显示，重新运行该命令。 如果你收到此错误，将在本教程的底部保留注释。

### <a name="examine-the-up-and-down-methods"></a>检查向上和向下方法

EF 核心命令`migrations add`生成代码以创建从 DB。 此迁移代码位于*迁移\<时间戳 > _InitialCreate.cs*文件。 `Up`方法`InitialCreate`类创建到数据模型的实体集对应的数据库表。 `Down`方法删除它们，如下面的示例中所示：

[!code-csharp[Main](intro/samples/cu/Migrations/20171026010210_InitialCreate.cs?range=8-24,77-)]

迁移调用`Up`方法以实现迁移的数据模型更改。 当你输入命令回滚更新，迁移调用`Down`方法。

前面的代码适用于初始迁移。 该代码时，将创建`migrations add InitialCreate`命令已运行。 迁移 name 参数 (如在示例中的"InitialCreate") 用于文件名称。 该迁移的名称可以是任何有效的文件名称。 最好选择的单词或短语，总结了中迁移正在进行的内容。 例如，添加了一个部门表的迁移可能会调用"AddDepartmentTable。"

如果创建初始迁移和 DB 退出：

* 生成数据库创建代码。
* 数据库创建代码不需要运行，因为数据库已与匹配的数据模型。 如果运行的 DB 创建代码，它不执行任何更改，因为数据库已与数据模型相匹配。

应用程序部署到新的环境中，必须运行数据库创建代码以创建数据库。

以前的连接字符串已更改为使用数据库的新名称。 指定的数据库不存在，因此迁移创建数据库。

### <a name="examine-the-data-model-snapshot"></a>检查数据模型快照

迁移创建*快照*中的当前数据库架构的*Migrations/SchoolContextModelSnapshot.cs*:

[!code-csharp[Main](intro/samples/cu/Migrations/SchoolContextModelSnapshot1.cs?name=snippet_Truncate)]

以代码表示为当前的数据库架构，因为 EF 核心没有与创建迁移的数据库进行交互。 当添加迁移时，EF 核心确定通过比较快照文件的数据模型更改的内容。 仅当有更新数据库时，EF 核心与数据库交互。

快照文件必须与创建它的迁移同步。 无法通过删除名为的文件中删除迁移*\<时间戳 > _\<migrationname >.cs*。 如果删除该文件时，将剩余的迁移将与数据库快照文件不同步。 若要删除上次添加的迁移，使用[dotnet ef 迁移删除](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)命令。

## <a name="remove-ensurecreated"></a>删除 EnsureCreated

对于早期的开发，`EnsureCreated`命令未使用。 在本教程中，使用迁移。 `EnsureCreated`具有以下 limatitions:

* 绕过迁移并创建数据库和架构。
* 不会创建迁移表。
* 可以*不*迁移与一起使用。
* 专为测试或快速原型制作，其中数据库是删除并重新创建频繁。

删除以下行从`DbInitializer`:

```csharp
context.Database.EnsureCreated();
```

## <a name="apply-the-migration-to-the-db-in-development"></a>适用于在开发过程中数据库的迁移

在命令窗口中，输入以下命令以创建数据库和表。

```console
dotnet ef database update
```

注意： 如果`update`命令将返回"生成失败"。 错误：

* 再次运行该命令。
* 如果再次失败，退出 Visual Studio，然后运行`update`命令。
* 将保留在页面底部的消息。

该命令的输出是类似于`migrations add`命令输出。 在上述命令中，显示的 SQL 命令的设置数据库的日志。 在下面的示例输出中，大部分日志被省略：

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

若要减少的日志消息中的详细信息级别可以更改中的日志级别*appsettings。Development.json*文件。 有关详细信息，请参阅[简介日志记录](xref:fundamentals/logging/index)。

使用**SQL Server 对象资源管理器**检查数据库。 请注意添加`__EFMigrationsHistory`表。 `__EFMigrationsHistory`表将跟踪的哪些迁移已应用于数据库。 查看中的数据`__EFMigrationsHistory`表，它显示为第一次迁移一个行。 在 CLI 输出上例中的最后一个日志显示创建此行的 INSERT 语句。

运行应用程序并验证一切就绪。

## <a name="appling-migrations-in-production"></a>在生产环境中的 appling 迁移

我们建议生产应用程序应**不**调用[Database.Migrate](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_)在应用程序启动。 `Migrate`不应从服务器场中的应用程序调用。 例如，如果应用已部署，向外扩展 （应用程序的多个实例正在运行） 的云。

应作为的一部分，部署，且以可控方式完成数据库迁移。 生产数据库迁移方法包括：

* 使用迁移来创建 SQL 脚本，并在部署中使用的 SQL 脚本。
* 运行`dotnet ef database update`从受控的环境。

EF 核心使用`__MigrationsHistory`表以查看任何迁移需要运行。 如果数据库是最新，则运行无需迁移。

<a id="pmc"></a>
## <a name="command-line-interface-cli-vs-package-manager-console-pmc"></a>命令行界面 (CLI) vs。程序包管理器控制台 (PMC)

EF 核心工具，用于管理迁移是从可用：

* .NET 核心 CLI 命令。
* 在 Visual Studio 的 PowerShell cmdlet**程序包管理器控制台**(PMC) 窗口。

本教程演示如何使用 CLI，一些开发人员喜欢使用 PMC。

PMC 的 EF 核心命令处于[Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)包。 此包包含在[Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage，因此你不必安装它。

**重要说明：**这不的是通过编辑的 cli 安装与相同的包*.csproj*文件。 此名称以结尾`Tools`，与中结束的 CLI 包名称不同`Tools.DotNet`。

有关 CLI 命令的详细信息，请参阅[.NET 核心 CLI](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet)。

有关 PMC 命令的详细信息，请参阅[Package Manager Console (Visual Studio)](https://docs.microsoft.com/ef/core/miscellaneous/cli/powershell)。

## <a name="troubleshooting"></a>疑难解答

下载[对此阶段已完成应用程序](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part4-migrations)。

应用程序生成以下异常：

```text
`SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

解决方案： 运行`dotnet ef database update`

如果`update`命令将返回"生成失败"。 错误：

* 再次运行该命令。
* 将保留在页面底部的消息。

>[!div class="step-by-step"]
[上一页](xref:data/ef-rp/sort-filter-page)
[下一页](xref:data/ef-rp/complex-data-model)
