# <a name="adding-search-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="7d2e3-101">将搜索添加到 ASP.NET Core MVC 应用</span><span class="sxs-lookup"><span data-stu-id="7d2e3-101">Adding Search to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="7d2e3-102">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7d2e3-102">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7d2e3-103">在本部分中，将向 `Index` 操作方法添加搜索功能，以实现按“类型”或“名称”搜索电影。</span><span class="sxs-lookup"><span data-stu-id="7d2e3-103">In this section you add search capability to the `Index` action method that lets you search movies by *genre* or *name*.</span></span>

<span data-ttu-id="7d2e3-104">使用以下代码更新 `Index` 方法：</span><span class="sxs-lookup"><span data-stu-id="7d2e3-104">Update the `Index` method with the following code:</span></span>
<!--
[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/_Layout.cshtml?highlight=7,31)]
-->

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

<span data-ttu-id="7d2e3-105">`Index` 操作方法的第一行创建了 [LINQ](https://docs.microsoft.com/dotnet/standard/using-linq) 查询用于选择电影：</span><span class="sxs-lookup"><span data-stu-id="7d2e3-105">The first line of the `Index` action method creates a [LINQ](https://docs.microsoft.com/dotnet/standard/using-linq) query to select the movies:</span></span>

```csharp
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="7d2e3-106">此时仅对查询进行了定义，它还不会针对数据库运行。</span><span class="sxs-lookup"><span data-stu-id="7d2e3-106">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="7d2e3-107">如果 `searchString` 参数包含一个字符串，电影查询则会被修改为根据搜索字符串的值进行筛选：</span><span class="sxs-lookup"><span data-stu-id="7d2e3-107">If the `searchString` parameter contains a string, the movies query is modified to filter on the value of the search string:</span></span>

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull)]

<span data-ttu-id="7d2e3-108">上面的 `s => s.Title.Contains()` 代码是 [Lambda 表达式](https://docs.microsoft.com/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)。</span><span class="sxs-lookup"><span data-stu-id="7d2e3-108">The `s => s.Title.Contains()` code above is a [Lambda Expression](https://docs.microsoft.com/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="7d2e3-109">Lambda 在基于方法的 [LINQ](https://docs.microsoft.com/dotnet/standard/using-linq) 查询中用作标准查询运算符方法的参数，如 [Where](https://docs.microsoft.com//dotnet/api/system.linq.enumerable.where) 方法或 `Contains`（上述的代码中所使用的）。</span><span class="sxs-lookup"><span data-stu-id="7d2e3-109">Lambdas are used in method-based [LINQ](https://docs.microsoft.com/dotnet/standard/using-linq) queries as arguments to standard query operator methods such as the [Where](https://docs.microsoft.com//dotnet/api/system.linq.enumerable.where) method or `Contains` (used in the code above).</span></span> <span data-ttu-id="7d2e3-110">在对 LINQ 查询进行定义或通过调用方法（如  `Where`、`Contains` 或 `OrderBy`）进行修改后，此查询不会被执行。</span><span class="sxs-lookup"><span data-stu-id="7d2e3-110">LINQ queries are not executed when they are defined or when they are modified by calling a method such as `Where`, `Contains`  or `OrderBy`.</span></span> <span data-ttu-id="7d2e3-111">相反，会延迟执行查询。</span><span class="sxs-lookup"><span data-stu-id="7d2e3-111">Rather, query execution is deferred.</span></span>  <span data-ttu-id="7d2e3-112">这意味着表达式的计算会延迟，直到真正循环访问其实现的值或者调用 `ToListAsync` 方法为止。</span><span class="sxs-lookup"><span data-stu-id="7d2e3-112">That means that the evaluation of an expression is delayed until its realized value is actually iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="7d2e3-113">有关延迟执行查询的详细信息，请参阅[Query Execution](https://docs.microsoft.com/dotnet/framework/data/adonet/ef/language-reference/query-execution)（查询执行）。</span><span class="sxs-lookup"><span data-stu-id="7d2e3-113">For more information about deferred query execution, see [Query Execution](https://docs.microsoft.com/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span></span>

<span data-ttu-id="7d2e3-114">注意：[Contains](https://docs.microsoft.com//dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) 方法在数据库上运行，而不是在上面显示的 c# 代码中运行。</span><span class="sxs-lookup"><span data-stu-id="7d2e3-114">Note: The [Contains](https://docs.microsoft.com//dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the c# code shown above.</span></span> <span data-ttu-id="7d2e3-115">查询是否区分大小写取决于数据库和排序规则。</span><span class="sxs-lookup"><span data-stu-id="7d2e3-115">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="7d2e3-116">在 SQL Server 上，[Contains](https://docs.microsoft.com//dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) 映射到 [SQL LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql)，这是不区分大小写的。</span><span class="sxs-lookup"><span data-stu-id="7d2e3-116">On SQL Server, [Contains](https://docs.microsoft.com//dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) maps to [SQL LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="7d2e3-117">在 SQLlite 中，由于使用了默认排序规则，因此需要区分大小写。</span><span class="sxs-lookup"><span data-stu-id="7d2e3-117">In SQLlite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="7d2e3-118">导航到 `/Movies/Index`。</span><span class="sxs-lookup"><span data-stu-id="7d2e3-118">Navigate to `/Movies/Index`.</span></span> <span data-ttu-id="7d2e3-119">将查询字符串（如 `?searchString=Ghost`）追加到 URL。</span><span class="sxs-lookup"><span data-stu-id="7d2e3-119">Append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="7d2e3-120">筛选的电影将显示出来。</span><span class="sxs-lookup"><span data-stu-id="7d2e3-120">The filtered movies are displayed.</span></span>

![索引视图](../../tutorials/first-mvc-app/search/_static/ghost.png)

<span data-ttu-id="7d2e3-122">如果将 `Index` 方法的签名更改为具有名称为 `id` 的参数，则 `id` 参数将匹配 Startup.cs 中设置的默认路由的可选 `{id}` 占位符。</span><span class="sxs-lookup"><span data-stu-id="7d2e3-122">If you change the signature of the `Index` method to have a parameter named `id`, the `id` parameter will match the optional `{id}` placeholder for the default routes set in *Startup.cs*.</span></span>

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]
