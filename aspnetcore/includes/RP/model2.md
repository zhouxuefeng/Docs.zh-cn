<span data-ttu-id="5f16d-101">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="5f16d-101">Add the following properties to the `Movie` class:</span></span>

<span data-ttu-id="5f16d-102">[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/MovieNoEF.cs?name=snippet_MovieNoEF)]</span><span class="sxs-lookup"><span data-stu-id="5f16d-102">[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/MovieNoEF.cs?name=snippet_MovieNoEF)]</span></span>

<span data-ttu-id="5f16d-103">数据库需要 `ID` 字段作为主键。</span><span class="sxs-lookup"><span data-stu-id="5f16d-103">The `ID` field is required by the database for the primary key.</span></span>

<a name="dc"></a>
### <a name="add-a-database-context-class"></a><span data-ttu-id="5f16d-104">添加数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="5f16d-104">Add a database context class</span></span>

<span data-ttu-id="5f16d-105">向“Models”文件夹添加名为 MovieContext.cs 的 `DbContext` 派生类。</span><span class="sxs-lookup"><span data-stu-id="5f16d-105">Add a `DbContext` derived class named *MovieContext.cs* to the *Models* folder.</span></span>

<span data-ttu-id="5f16d-106">[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/MovieContext.cs)]</span><span class="sxs-lookup"><span data-stu-id="5f16d-106">[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/MovieContext.cs)]</span></span>

<span data-ttu-id="5f16d-107">前面的代码为实体集创建 `DbSet` 属性。</span><span class="sxs-lookup"><span data-stu-id="5f16d-107">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="5f16d-108">在实体框架术语中，实体集通常与数据库表相对应，实体与表中的行相对应。</span><span class="sxs-lookup"><span data-stu-id="5f16d-108">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>