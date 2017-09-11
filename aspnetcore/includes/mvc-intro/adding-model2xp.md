<a name="cli"></a>
## <a name="perform-initial-migration"></a>添加初始迁移

从命令行运行以下 .NET Core CLI 命令：

```console
dotnet ef migrations add InitialCreate
dotnet ef database update
```

`dotnet ef migrations add InitialCreate` 命令生成用于创建初始数据库架构的代码。 此架构以（Models/MovieContext.cs 文件中的）`DbContext` 中指定的模型为基础。 `Initial` 参数用于为迁移命名。 可以使用任意名称，但是按照惯例要选择可以描述迁移的名称。 请参阅[迁移介绍](xref:data/ef-mvc/migrations#introduction-to-migrations)了解详细信息。

`dotnet ef database update` 命令在用于创建数据库的 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。