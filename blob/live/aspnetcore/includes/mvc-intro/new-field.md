# <a name="adding-a-new-field"></a><span data-ttu-id="cbe95-101">添加新字段</span><span class="sxs-lookup"><span data-stu-id="cbe95-101">Adding a new field</span></span>

<span data-ttu-id="cbe95-102">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cbe95-102">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cbe95-103">本教程将向 `Movies` 表添加一个新字段。</span><span class="sxs-lookup"><span data-stu-id="cbe95-103">This tutorial will add a new field to the `Movies` table.</span></span> <span data-ttu-id="cbe95-104">当更改架构（添加一个新的字段）时，我们将丢弃数据库并创建一个新的数据库。</span><span class="sxs-lookup"><span data-stu-id="cbe95-104">We'll drop the database and create a new one when we change the schema (add a new field).</span></span> <span data-ttu-id="cbe95-105">此工作流适用于在没有要保留的任何生产数据的早期开发阶段。</span><span class="sxs-lookup"><span data-stu-id="cbe95-105">This workflow works well early in development when we don't have any production data to perserve.</span></span>

<span data-ttu-id="cbe95-106">在部署了应用并且具有要保留的数据后，在需要更改架构时则不能丢弃数据库。</span><span class="sxs-lookup"><span data-stu-id="cbe95-106">Once your app is deployed and you have data that you need to perserve, you can't drop your DB when you need to change the schema.</span></span> <span data-ttu-id="cbe95-107">Entity Framework [Code First 迁移](https://docs.microsoft.com/ef/core/get-started/aspnetcore/new-db)使你能够更新架构并迁移数据库，而不会导致数据丢失。</span><span class="sxs-lookup"><span data-stu-id="cbe95-107">Entity Framework [Code First Migrations](https://docs.microsoft.com/ef/core/get-started/aspnetcore/new-db) allows you to update your schema and migrate the database without losing data.</span></span> <span data-ttu-id="cbe95-108">迁移是使用 SQL Server 时的一个常用的功能，但 SQLite 不支持许多迁移架构操作，因此只可能进行非常简单的迁移。</span><span class="sxs-lookup"><span data-stu-id="cbe95-108">Migrations is a popular feature when using SQL Server, but SQLlite does not support many migration schema operations, so only very simply migrations are possible.</span></span> <span data-ttu-id="cbe95-109">有关详细信息，请参阅 [SQLite 限制](https://docs.microsoft.com/ef/core/providers/sqlite/limitations)。</span><span class="sxs-lookup"><span data-stu-id="cbe95-109">See [SQLite Limitations](https://docs.microsoft.com/ef/core/providers/sqlite/limitations) for more information.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="cbe95-110">向电影模型添加分级属性</span><span class="sxs-lookup"><span data-stu-id="cbe95-110">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="cbe95-111">打开 Models/Movie.cs 文件，并添加 `Rating` 属性：</span><span class="sxs-lookup"><span data-stu-id="cbe95-111">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateRating.cs?highlight=11&range=7-18)]

<span data-ttu-id="cbe95-112">因为已经添加新字段到 `Movie` 类，所以还需要更新绑定允许名单，将此新属性纳入其中。</span><span class="sxs-lookup"><span data-stu-id="cbe95-112">Because you've added a new field to the `Movie` class, you also need to update the binding whitelist so this new property will be included.</span></span> <span data-ttu-id="cbe95-113">在 MoviesController.cs 中，更新 `Create` 和 `Edit` 操作方法的 `[Bind]` 属性，以包括 `Rating` 属性：</span><span class="sxs-lookup"><span data-stu-id="cbe95-113">In *MoviesController.cs*, update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("ID,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="cbe95-114">还需要更新视图模板以在浏览器视图中显示、创建和编辑新的 `Rating` 属性。</span><span class="sxs-lookup"><span data-stu-id="cbe95-114">You also need to update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="cbe95-115">编辑 /Views/Movies/Index.cshtml 文件并添加 `Rating` 字段：</span><span class="sxs-lookup"><span data-stu-id="cbe95-115">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

[!code-HTML[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexGenreRating.cshtml?highlight=17,39&range=24-64)]

<span data-ttu-id="cbe95-116">使用 `Rating` 字段更新 /Views/Movies/Create.cshtml。</span><span class="sxs-lookup"><span data-stu-id="cbe95-116">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

<span data-ttu-id="cbe95-117">在将 DB 更新为包括新字段之前，应用不会正常工作。</span><span class="sxs-lookup"><span data-stu-id="cbe95-117">The app won't work until we update the DB to include the new field.</span></span> <span data-ttu-id="cbe95-118">如果现在运行，会出现以下 `SqliteException`：</span><span class="sxs-lookup"><span data-stu-id="cbe95-118">If you run it now, you'll get the following `SqliteException`:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: m.Rating'.
```

<span data-ttu-id="cbe95-119">看到此错误是因为更新的 Movie 模型类与现有数据库的 Movie 表架构不同。</span><span class="sxs-lookup"><span data-stu-id="cbe95-119">You're seeing this error because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="cbe95-120">（数据库表中没有 `Rating` 列。）</span><span class="sxs-lookup"><span data-stu-id="cbe95-120">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="cbe95-121">可通过几种方法解决此错误：</span><span class="sxs-lookup"><span data-stu-id="cbe95-121">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="cbe95-122">丢弃数据库，让 Entity Framework 基于新的模型类架构自动重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="cbe95-122">Drop the database and have the Entity Framework automatically re-create the database based on the new model class schema.</span></span> <span data-ttu-id="cbe95-123">如果使用此方法，则会丢失数据库中的现有数据，因此不能对生产数据库使用此方法！</span><span class="sxs-lookup"><span data-stu-id="cbe95-123">With this approach, you lose existing data in the database — so you can't do this with a production database!</span></span> <span data-ttu-id="cbe95-124">使用初始值设定项，以使用测试数据自动设定数据库种子，这通常是开发应用的有效方式。</span><span class="sxs-lookup"><span data-stu-id="cbe95-124">Using an initializer to automatically seed a database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="cbe95-125">手动修改现有数据库的架构，使它与模型类匹配。</span><span class="sxs-lookup"><span data-stu-id="cbe95-125">Manually modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="cbe95-126">此方法的优点是可以保留数据。</span><span class="sxs-lookup"><span data-stu-id="cbe95-126">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="cbe95-127">可以手动或通过创建数据库更改脚本进行此更改。</span><span class="sxs-lookup"><span data-stu-id="cbe95-127">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="cbe95-128">使用 Code First 迁移更新数据库架构。</span><span class="sxs-lookup"><span data-stu-id="cbe95-128">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="cbe95-129">在本教程中，我们将在架构更改时丢弃并重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="cbe95-129">For this tutorial, we'll drop and re-create the database when the schema changes.</span></span> <span data-ttu-id="cbe95-130">从终端运行以下命令丢弃 db：</span><span class="sxs-lookup"><span data-stu-id="cbe95-130">Run the following command from a terminal to drop the db:</span></span>

`dotnet ef database drop`

<span data-ttu-id="cbe95-131">更新 `SeedData` 类，使它提供新列的值。</span><span class="sxs-lookup"><span data-stu-id="cbe95-131">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="cbe95-132">示例更改如下所示，但可能需要对每个 `new Movie` 做出此更改。</span><span class="sxs-lookup"><span data-stu-id="cbe95-132">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="cbe95-133">将 `Rating` 字段添加到 `Edit`、`Details` 和 `Delete` 视图。</span><span class="sxs-lookup"><span data-stu-id="cbe95-133">Add the `Rating` field to the `Edit`, `Details`, and `Delete` view.</span></span>

<span data-ttu-id="cbe95-134">运行应用，并验证是否可以创建/编辑/显示具有 `Rating` 字段的电影。</span><span class="sxs-lookup"><span data-stu-id="cbe95-134">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="cbe95-135">模板。</span><span class="sxs-lookup"><span data-stu-id="cbe95-135">templates.</span></span>
