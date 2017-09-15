# <a name="adding-search-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="bfdc4-101">将搜索添加到 ASP.NET Core MVC 应用</span><span class="sxs-lookup"><span data-stu-id="bfdc4-101">Adding Search to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="bfdc4-102">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bfdc4-102">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bfdc4-103">在本部分中，将向 `Index` 操作方法添加搜索功能，以实现按“类型”或“名称”搜索电影。</span><span class="sxs-lookup"><span data-stu-id="bfdc4-103">In this section you add search capability to the `Index` action method that lets you search movies by *genre* or *name*.</span></span>

<span data-ttu-id="bfdc4-104">使用以下代码更新 `Index` 方法：</span><span class="sxs-lookup"><span data-stu-id="bfdc4-104">Update the `Index` method with the following code:</span></span>
<!--
[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/_Layout.cshtml?highlight=7,31)]
-->

<span data-ttu-id="bfdc4-105">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]</span><span class="sxs-lookup"><span data-stu-id="bfdc4-105">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]</span></span>

<span data-ttu-id="bfdc4-106">`Index` 操作方法的第一行创建了 [LINQ](http://msdn.microsoft.com/library/bb397926.aspx) 查询用于选择电影：</span><span class="sxs-lookup"><span data-stu-id="bfdc4-106">The first line of the `Index` action method creates a [LINQ](http://msdn.microsoft.com/library/bb397926.aspx) query to select the movies:</span></span>

```csharp
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="bfdc4-107">此时仅对查询进行了定义，它还不会针对数据库运行。</span><span class="sxs-lookup"><span data-stu-id="bfdc4-107">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="bfdc4-108">如果 `searchString` 参数包含一个字符串，电影查询则会被修改为根据搜索字符串的值进行筛选：</span><span class="sxs-lookup"><span data-stu-id="bfdc4-108">If the `searchString` parameter contains a string, the movies query is modified to filter on the value of the search string:</span></span>

<span data-ttu-id="bfdc4-109">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull)]</span><span class="sxs-lookup"><span data-stu-id="bfdc4-109">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull)]</span></span>

<span data-ttu-id="bfdc4-110">上面的 `s => s.Title.Contains()` 代码是 [Lambda 表达式](http://msdn.microsoft.com/library/bb397687.aspx)。</span><span class="sxs-lookup"><span data-stu-id="bfdc4-110">The `s => s.Title.Contains()` code above is a [Lambda Expression](http://msdn.microsoft.com/library/bb397687.aspx).</span></span> <span data-ttu-id="bfdc4-111">Lambda 在基于方法的 [LINQ](http://msdn.microsoft.com/library/bb397926.aspx) 查询中用作标准查询运算符方法的参数，如 [Where](http://msdn.microsoft.com/library/system.linq.enumerable.where.aspx) 方法或 `Contains`（上述的代码中所使用的）。</span><span class="sxs-lookup"><span data-stu-id="bfdc4-111">Lambdas are used in method-based [LINQ](http://msdn.microsoft.com/library/bb397926.aspx) queries as arguments to standard query operator methods such as the [Where](http://msdn.microsoft.com/library/system.linq.enumerable.where.aspx) method or `Contains` (used in the code above).</span></span> <span data-ttu-id="bfdc4-112">在对 LINQ 查询进行定义或通过调用方法（如  `Where`、`Contains` 或 `OrderBy`）进行修改后，此查询不会被执行。</span><span class="sxs-lookup"><span data-stu-id="bfdc4-112">LINQ queries are not executed when they are defined or when they are modified by calling a method such as `Where`, `Contains`  or `OrderBy`.</span></span> <span data-ttu-id="bfdc4-113">相反，会延迟执行查询。</span><span class="sxs-lookup"><span data-stu-id="bfdc4-113">Rather, query execution is deferred.</span></span>  <span data-ttu-id="bfdc4-114">这意味着表达式的计算会延迟，直到真正循环访问其实现的值或者调用 `ToListAsync` 方法为止。</span><span class="sxs-lookup"><span data-stu-id="bfdc4-114">That means that the evaluation of an expression is delayed until its realized value is actually iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="bfdc4-115">有关延迟执行查询的详细信息，请参阅[Query Execution](http://msdn.microsoft.com/library/bb738633.aspx)（查询执行）。</span><span class="sxs-lookup"><span data-stu-id="bfdc4-115">For more information about deferred query execution, see [Query Execution](http://msdn.microsoft.com/library/bb738633.aspx).</span></span>

<span data-ttu-id="bfdc4-116">注意：[Contains](http://msdn.microsoft.com/library/bb155125.aspx) 方法在数据库上运行，而不是在上面显示的 c# 代码中运行。</span><span class="sxs-lookup"><span data-stu-id="bfdc4-116">Note: The [Contains](http://msdn.microsoft.com/library/bb155125.aspx) method is run on the database, not in the c# code shown above.</span></span> <span data-ttu-id="bfdc4-117">查询是否区分大小写取决于数据库和排序规则。</span><span class="sxs-lookup"><span data-stu-id="bfdc4-117">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="bfdc4-118">在 SQL Server 上，[Contains](http://msdn.microsoft.com/library/bb155125.aspx) 映射到 [SQL LIKE](http://msdn.microsoft.com/library/ms179859.aspx)，这是不区分大小写的。</span><span class="sxs-lookup"><span data-stu-id="bfdc4-118">On SQL Server, [Contains](http://msdn.microsoft.com/library/bb155125.aspx) maps to [SQL LIKE](http://msdn.microsoft.com/library/ms179859.aspx), which is case insensitive.</span></span> <span data-ttu-id="bfdc4-119">在 SQLlite 中，由于使用了默认排序规则，因此需要区分大小写。</span><span class="sxs-lookup"><span data-stu-id="bfdc4-119">In SQLlite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="bfdc4-120">导航到 `/Movies/Index`。</span><span class="sxs-lookup"><span data-stu-id="bfdc4-120">Navigate to `/Movies/Index`.</span></span> <span data-ttu-id="bfdc4-121">将查询字符串（如 `?searchString=Ghost`）追加到 URL。</span><span class="sxs-lookup"><span data-stu-id="bfdc4-121">Append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="bfdc4-122">筛选的电影将显示出来。</span><span class="sxs-lookup"><span data-stu-id="bfdc4-122">The filtered movies are displayed.</span></span>

![索引视图](../../tutorials/first-mvc-app/search/_static/ghost.png)

<span data-ttu-id="bfdc4-124">如果将 `Index` 方法的签名更改为具有名称为 `id` 的参数，则 `id` 参数将匹配 Startup.cs 中设置的默认路由的可选 `{id}` 占位符。</span><span class="sxs-lookup"><span data-stu-id="bfdc4-124">If you change the signature of the `Index` method to have a parameter named `id`, the `id` parameter will match the optional `{id}` placeholder for the default routes set in *Startup.cs*.</span></span>

<span data-ttu-id="bfdc4-125">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]</span><span class="sxs-lookup"><span data-stu-id="bfdc4-125">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]</span></span>