<span data-ttu-id="b87cc-101">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="b87cc-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/MovieNoEF.cs?name=snippet_MovieNoEF)]

<span data-ttu-id="b87cc-102">数据库需要 `ID` 字段作为主键。</span><span class="sxs-lookup"><span data-stu-id="b87cc-102">The `ID` field is required by the database for the primary key.</span></span>

<a name="dc"></a>
### <a name="add-a-database-context-class"></a><span data-ttu-id="b87cc-103">添加数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="b87cc-103">Add a database context class</span></span>

<span data-ttu-id="b87cc-104">向“Models”文件夹添加名为 MovieContext.cs 的 `DbContext` 派生类。</span><span class="sxs-lookup"><span data-stu-id="b87cc-104">Add a `DbContext` derived class named *MovieContext.cs* to the *Models* folder.</span></span>

[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/MovieContext.cs?range=1-12,14-17,19-21)]

<span data-ttu-id="b87cc-105">前面的代码为实体集创建 `DbSet` 属性。</span><span class="sxs-lookup"><span data-stu-id="b87cc-105">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="b87cc-106">在实体框架术语中，实体集通常与数据库表相对应，实体与表中的行相对应。</span><span class="sxs-lookup"><span data-stu-id="b87cc-106">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="b87cc-107">`DbSet` 属性名称为 `Movies`。</span><span class="sxs-lookup"><span data-stu-id="b87cc-107">The `DbSet` property name is `Movies`.</span></span> <span data-ttu-id="b87cc-108">由于此数据库使用单数名称，此示例将替代 `OnModelCreating` 以对该表格名称使用单数形式 (`Movie`)。</span><span class="sxs-lookup"><span data-stu-id="b87cc-108">Since the database uses singular names, the sample overrides `OnModelCreating` to use the singular form (`Movie`) for the table name.</span></span>
