## <a name="add-initial-migration-and-update-the-database"></a>添加初始迁移并更新数据库

* 打开命令提示符并导航到项目目录。 (目录包含*Startup.cs*文件)。

* 在命令提示符中运行以下命令：

  ```console
  dotnet restore
  dotnet ef migrations add Initial
  dotnet ef database update
  ```
  
  [.NET 核心](https://docs.microsoft.com/dotnet/core/tools/index)是.NET 的跨平台实现。 下面是这些命令执行的操作：

  * `dotnet restore`： 将下载 NuGet 包中指定*.csproj*文件。
  * `dotnet ef migrations add Initial`运行实体框架.NET 核心 CLI 迁移命令并创建初始迁移。 在"添加"后的参数是你将分配给迁移的名称。 此处你正在命名迁移"初始"因为它是初始数据库迁移。 此操作将创建*数据/迁移/\<日期时间 > _Initial.cs*文件包含要添加的迁移命令*电影*到数据库的表。
  * `dotnet ef database update`使用我们刚刚创建的迁移更新数据库。

你将在下一教程中了解有关数据库和连接字符串。 你将了解有关中的数据模型更改[添加字段](xref:tutorials/first-mvc-app/new-field)教程。
