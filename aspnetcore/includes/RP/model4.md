<span data-ttu-id="a1053-101">下表详细说明了 ASP.NET Core 代码生成器的参数：</span><span class="sxs-lookup"><span data-stu-id="a1053-101">The following table details the ASP.NET Core code generators\` parameters:</span></span>

| <span data-ttu-id="a1053-102">参数</span><span class="sxs-lookup"><span data-stu-id="a1053-102">Parameter</span></span>               | <span data-ttu-id="a1053-103">描述</span><span class="sxs-lookup"><span data-stu-id="a1053-103">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="a1053-104">-m</span><span class="sxs-lookup"><span data-stu-id="a1053-104">-m</span></span>  | <span data-ttu-id="a1053-105">模型的名称。</span><span class="sxs-lookup"><span data-stu-id="a1053-105">The name of the model.</span></span> |
| <span data-ttu-id="a1053-106">-dc</span><span class="sxs-lookup"><span data-stu-id="a1053-106">-dc</span></span>  | <span data-ttu-id="a1053-107">数据上下文。</span><span class="sxs-lookup"><span data-stu-id="a1053-107">The data context.</span></span> |
| <span data-ttu-id="a1053-108">-udl</span><span class="sxs-lookup"><span data-stu-id="a1053-108">-udl</span></span> | <span data-ttu-id="a1053-109">使用默认布局。</span><span class="sxs-lookup"><span data-stu-id="a1053-109">Use the default layout.</span></span> |
| <span data-ttu-id="a1053-110">-outDir</span><span class="sxs-lookup"><span data-stu-id="a1053-110">-outDir</span></span> | <span data-ttu-id="a1053-111">用于创建视图的相对输出文件夹路径。</span><span class="sxs-lookup"><span data-stu-id="a1053-111">The relative output folder path to create the views.</span></span> |
| <span data-ttu-id="a1053-112">--referenceScriptLibraries</span><span class="sxs-lookup"><span data-stu-id="a1053-112">--referenceScriptLibraries</span></span> | <span data-ttu-id="a1053-113">向“编辑”和“创建”页面添加 `_ValidationScriptsPartial`</span><span class="sxs-lookup"><span data-stu-id="a1053-113">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="a1053-114">使用 `h` 开关获取 `aspnet-codegenerator razorpage` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="a1053-114">Use the `h` switch to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```console
dotnet aspnet-codegenerator razorpage -h
```
<a name="test"></a>
### <a name="test-the-app"></a><span data-ttu-id="a1053-115">测试应用</span><span class="sxs-lookup"><span data-stu-id="a1053-115">Test the app</span></span>

* <span data-ttu-id="a1053-116">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="a1053-116">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>
* <span data-ttu-id="a1053-117">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="a1053-117">Test the **Create** link.</span></span>

 ![创建页面](../../tutorials/razor-pages/model/_static/conan.png)

<a name="scaffold"></a>

* <span data-ttu-id="a1053-119">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="a1053-119">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="a1053-120">如果收到以下错误，则验证是否已运行迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="a1053-120">If you get the following error, verify you have run migrations and updated the database:</span></span>

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)
```