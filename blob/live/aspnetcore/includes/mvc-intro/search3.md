<!--
[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/_Layout.cshtml?highlight=7,31)]


[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull)]

![Index view](../../tutorials/first-mvc-app/search/_static/ghost.png)


[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

--> 

<span data-ttu-id="1e61e-101">现在提交搜索后，URL 将包含搜索查询字符串。</span><span class="sxs-lookup"><span data-stu-id="1e61e-101">Now when you submit a search, the URL contains the search query string.</span></span> <span data-ttu-id="1e61e-102">即使具备 `HttpPost Index` 方法，搜索也将转到 `HttpGet Index` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="1e61e-102">Searching will also go to the `HttpGet Index` action method, even if you have a `HttpPost Index` method.</span></span>

![URL 中显示 searchString=ghost 且返回了 Ghostbusters 和 Ghostbusters 2 的浏览器窗口包含 ghost 一词](../../tutorials/first-mvc-app/search/_static/search_get.png)

<span data-ttu-id="1e61e-104">以下标记显示对 `form` 标记的更改：</span><span class="sxs-lookup"><span data-stu-id="1e61e-104">The following markup shows the change to the `form` tag:</span></span>

```html
<form asp-controller="Movies" asp-action="Index" method="get">
   ```

## <a name="adding-search-by-genre"></a><span data-ttu-id="1e61e-105">添加“按流派搜索”</span><span class="sxs-lookup"><span data-stu-id="1e61e-105">Adding Search by genre</span></span>

<span data-ttu-id="1e61e-106">将以下 `MovieGenreViewModel` 类添加到“模型”文件夹：</span><span class="sxs-lookup"><span data-stu-id="1e61e-106">Add the following `MovieGenreViewModel` class to the *Models* folder:</span></span>

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

<span data-ttu-id="1e61e-107">“电影流派”视图模型将包含：</span><span class="sxs-lookup"><span data-stu-id="1e61e-107">The movie-genre view model will contain:</span></span>

   * <span data-ttu-id="1e61e-108">电影列表。</span><span class="sxs-lookup"><span data-stu-id="1e61e-108">A list of movies.</span></span>
   * <span data-ttu-id="1e61e-109">包含流派列表的 `SelectList`。</span><span class="sxs-lookup"><span data-stu-id="1e61e-109">A `SelectList` containing the list of genres.</span></span> <span data-ttu-id="1e61e-110">用户可通过它从列表中选择一种流派。</span><span class="sxs-lookup"><span data-stu-id="1e61e-110">This will allow the user to select a genre from the list.</span></span>
   * <span data-ttu-id="1e61e-111">包含所选流派的 `movieGenre`。</span><span class="sxs-lookup"><span data-stu-id="1e61e-111">`movieGenre`, which contains the selected genre.</span></span>

<span data-ttu-id="1e61e-112">将 `MoviesController.cs` 中的 `Index` 方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="1e61e-112">Replace the `Index` method in `MoviesController.cs` with the following code:</span></span>

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

<span data-ttu-id="1e61e-113">以下代码是一种 `LINQ` 查询，可从数据库中检索所有流派。</span><span class="sxs-lookup"><span data-stu-id="1e61e-113">The following code is a `LINQ` query that retrieves all the genres from the database.</span></span>

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_LINQ)]

<span data-ttu-id="1e61e-114">通过投影不同的流派创建 `SelectList`（我们不希望选择列表中的流派重复）。</span><span class="sxs-lookup"><span data-stu-id="1e61e-114">The `SelectList` of genres is created by projecting the distinct genres (we don't want our select list to have duplicate genres).</span></span>

```csharp
movieGenreVM.genres = new SelectList(await genreQuery.Distinct().ToListAsync())
   ```

## <a name="adding-search-by-genre-to-the-index-view"></a><span data-ttu-id="1e61e-115">向索引视图添加“按流派搜索”</span><span class="sxs-lookup"><span data-stu-id="1e61e-115">Adding search by genre to the Index view</span></span>

<span data-ttu-id="1e61e-116">按如下更新 `Index.cshtml`：</span><span class="sxs-lookup"><span data-stu-id="1e61e-116">Update `Index.cshtml` as follows:</span></span>

[!code-HTML[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,28,31,34,37,43)]

<span data-ttu-id="1e61e-117">检查以下 HTML 帮助程序中使用的 Lambda 表达式：</span><span class="sxs-lookup"><span data-stu-id="1e61e-117">Examine the lambda expression used in the following HTML Helper:</span></span>

`@Html.DisplayNameFor(model => model.movies[0].Title)`
 
<span data-ttu-id="1e61e-118">在上述代码中，`DisplayNameFor` HTML 帮助程序检查 Lambda 表达式中引用的 `Title` 属性来确定显示名称。</span><span class="sxs-lookup"><span data-stu-id="1e61e-118">In the preceding code, the `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="1e61e-119">由于只检查但未计算 Lambda 表达式，因此当 `model`、`model.movies[0]` 或 `model.movies` 为 `null` 或空时，你不会收到访问冲突。</span><span class="sxs-lookup"><span data-stu-id="1e61e-119">Since the lambda expression is inspected rather than evaluated, you don't receive an access violation when `model`, `model.movies`, or `model.movies[0]` are `null` or empty.</span></span> <span data-ttu-id="1e61e-120">对 Lambda 表达式求值时（例如，`@Html.DisplayFor(modelItem => item.Title)`），将求得该模型的属性值。</span><span class="sxs-lookup"><span data-stu-id="1e61e-120">When the lambda expression is evaluated (for example, `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<span data-ttu-id="1e61e-121">通过按流派或/和电影标题搜索来测试应用。</span><span class="sxs-lookup"><span data-stu-id="1e61e-121">Test the app by searching by genre, by movie title, and by both.</span></span>
