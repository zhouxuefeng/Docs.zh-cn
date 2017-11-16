<a name="cli"></a>
## <a name="add-scaffold-tooling-and-perform-initial-migration"></a><span data-ttu-id="15739-101">添加基架工具并执行初始迁移</span><span class="sxs-lookup"><span data-stu-id="15739-101">Add scaffold tooling and perform initial migration</span></span>

<span data-ttu-id="15739-102">从命令行运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="15739-102">From the command line, run the following .NET Core CLI commands:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet ef migrations add InitialCreate
dotnet ef database update
```

<span data-ttu-id="15739-103">`add package` 命令安装运行基架引擎所需的工具。</span><span class="sxs-lookup"><span data-stu-id="15739-103">The `add package` command installs the tooling required to run the scaffolding engine.</span></span>

<span data-ttu-id="15739-104">`ef migrations add InitialCreate` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="15739-104">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="15739-105">此架构以（Models/MovieContext.cs 文件中的）`DbContext` 中指定的模型为基础。</span><span class="sxs-lookup"><span data-stu-id="15739-105">The schema is based on the model specified in the `DbContext` (In the *Models/MovieContext.cs* file).</span></span> <span data-ttu-id="15739-106">`Initial` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="15739-106">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="15739-107">可以使用任意名称，但是按照惯例应选择描述迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="15739-107">You can use any name, but by convention you choose a name that describes the migration.</span></span> <span data-ttu-id="15739-108">有关详细信息，请参阅[迁移简介](xref:data/ef-mvc/migrations#introduction-to-migrations)。</span><span class="sxs-lookup"><span data-stu-id="15739-108">See [Introduction to migrations](xref:data/ef-mvc/migrations#introduction-to-migrations) for more information.</span></span>

<span data-ttu-id="15739-109">`ef database update` 命令在用于创建数据库的 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="15739-109">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>