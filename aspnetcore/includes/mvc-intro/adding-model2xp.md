<a name="cli"></a>
## <a name="perform-initial-migration"></a><span data-ttu-id="35852-101">添加初始迁移</span><span class="sxs-lookup"><span data-stu-id="35852-101">Perform initial migration</span></span>

<span data-ttu-id="35852-102">从命令行运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="35852-102">From the command line, run the following .NET Core CLI commands:</span></span>

```console
dotnet ef migrations add InitialCreate
dotnet ef database update
```

<span data-ttu-id="35852-103">`dotnet ef migrations add InitialCreate` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="35852-103">The `dotnet ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="35852-104">此架构以（Models/MovieContext.cs 文件中的）`DbContext` 中指定的模型为基础。</span><span class="sxs-lookup"><span data-stu-id="35852-104">The schema is based on the model specified in the `DbContext` (In the *Models/MovieContext.cs* file).</span></span> <span data-ttu-id="35852-105">`Initial` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="35852-105">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="35852-106">可以使用任意名称，但是按照惯例要选择可以描述迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="35852-106">You can use any name, but by convention you choose a name that describes the migration.</span></span> <span data-ttu-id="35852-107">请参阅[迁移介绍](xref:data/ef-mvc/migrations#introduction-to-migrations)了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="35852-107">See [Introduction to migrations](xref:data/ef-mvc/migrations#introduction-to-migrations) for more information.</span></span>

<span data-ttu-id="35852-108">`dotnet ef database update` 命令在用于创建数据库的 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="35852-108">The `dotnet ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>