<!--
[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/_Layout.cshtml?highlight=7,31)]


[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull)]

![Index view](../../tutorials/first-mvc-app/search/_static/ghost.png)


[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

--> 

现在提交搜索后，URL 将包含搜索查询字符串。 即使具备 `HttpPost Index` 方法，搜索也将转到 `HttpGet Index` 操作方法。

![URL 中显示 searchString=ghost 且返回了 Ghostbusters 和 Ghostbusters 2 的浏览器窗口包含 ghost 一词](../../tutorials/first-mvc-app/search/_static/search_get.png)

以下标记显示对 `form` 标记的更改：

```html
<form asp-controller="Movies" asp-action="Index" method="get">
   ```

## <a name="adding-search-by-genre"></a>添加“按流派搜索”

将以下 `MovieGenreViewModel` 类添加到“模型”文件夹：

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

“电影流派”视图模型将包含：

   * 电影列表。
   * 包含流派列表的 `SelectList`。 用户可通过它从列表中选择一种流派。
   * 包含所选流派的 `movieGenre`。

将 `MoviesController.cs` 中的 `Index` 方法替换为以下代码：

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

以下代码是一种 `LINQ` 查询，可从数据库中检索所有流派。

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_LINQ)]

通过投影不同的流派创建 `SelectList`（我们不希望选择列表中的流派重复）。

```csharp
movieGenreVM.genres = new SelectList(await genreQuery.Distinct().ToListAsync())
   ```

## <a name="adding-search-by-genre-to-the-index-view"></a>向索引视图添加“按流派搜索”

按如下更新 `Index.cshtml`：

[!code-HTML[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,28,31,34,37,43)]

检查以下 HTML 帮助程序中使用的 Lambda 表达式：

`@Html.DisplayNameFor(model => model.movies[0].Title)`
 
在上述代码中，`DisplayNameFor` HTML 帮助程序检查 Lambda 表达式中引用的 `Title` 属性来确定显示名称。 由于只检查但未计算 Lambda 表达式，因此当 `model`、`model.movies[0]` 或 `model.movies` 为 `null` 或空时，你不会收到访问冲突。 对 Lambda 表达式求值时（例如，`@Html.DisplayFor(modelItem => item.Title)`），将求得该模型的属性值。

通过按流派或/和电影标题搜索来测试应用。
