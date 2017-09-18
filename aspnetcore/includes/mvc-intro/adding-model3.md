
## <a name="test-the-app"></a><span data-ttu-id="70a8d-101">测试应用</span><span class="sxs-lookup"><span data-stu-id="70a8d-101">Test the app</span></span>

* <span data-ttu-id="70a8d-102">运行应用并点击“Mvc Movie”链接。</span><span class="sxs-lookup"><span data-stu-id="70a8d-102">Run the app and tap the **Mvc Movie** link.</span></span>
* <span data-ttu-id="70a8d-103">点击“新建”链接，创建电影。</span><span class="sxs-lookup"><span data-stu-id="70a8d-103">Tap the **Create New** link and create a movie.</span></span>

  ![创建包含“流派”、“价格”、“上映时间”和“标题”字段的视图](../../tutorials/first-mvc-app/adding-model/_static/movies.png)

* <span data-ttu-id="70a8d-105">可能无法在 `Price` 字段中输入小数点或逗号。</span><span class="sxs-lookup"><span data-stu-id="70a8d-105">You may not be able to enter decimal points or commas in the `Price` field.</span></span> <span data-ttu-id="70a8d-106">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点及使用非美国英语日期格式的非英语区域设置，必须执行使应用全球化的步骤。</span><span class="sxs-lookup"><span data-stu-id="70a8d-106">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="70a8d-107">请参阅 https://github.com/aspnet/Docs/issues/4076 和[其他资源](#additional-resources)了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="70a8d-107">See https://github.com/aspnet/Docs/issues/4076 and [Additional resources](#additional-resources) for more information.</span></span> <span data-ttu-id="70a8d-108">目前，只能输入整数，例如 10。</span><span class="sxs-lookup"><span data-stu-id="70a8d-108">For now, just enter whole numbers like 10.</span></span>

<a name="displayformatdatelocal"></a>

* <span data-ttu-id="70a8d-109">在一些区域设置中，需要指定日期格式。</span><span class="sxs-lookup"><span data-stu-id="70a8d-109">In some locales you need to specify the date format.</span></span> <span data-ttu-id="70a8d-110">请参阅下方突出显示的代码。</span><span class="sxs-lookup"><span data-stu-id="70a8d-110">See the highlighted code below.</span></span>

<span data-ttu-id="70a8d-111">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateFormat.cs?name=snippet_1&highlight=2,10)]</span><span class="sxs-lookup"><span data-stu-id="70a8d-111">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateFormat.cs?name=snippet_1&highlight=2,10)]</span></span>

<span data-ttu-id="70a8d-112">我们将在本教程的后续部分中探讨 `DataAnnotations`。</span><span class="sxs-lookup"><span data-stu-id="70a8d-112">We'll talk about `DataAnnotations` later in the tutorial.</span></span>

<span data-ttu-id="70a8d-113">点击“创建”后，窗体会发布到服务器，其中电影信息会保存在数据库中。</span><span class="sxs-lookup"><span data-stu-id="70a8d-113">Tapping **Create** causes the form to be posted to the server, where the movie information is saved in a database.</span></span> <span data-ttu-id="70a8d-114">应用重定向到 /Movies URL，其中会显示新创建的电影信息。</span><span class="sxs-lookup"><span data-stu-id="70a8d-114">The app redirects to the */Movies* URL, where the newly created movie information is displayed.</span></span>

![显示新创建的电影列表的电影视图](../../tutorials/first-mvc-app/adding-model/_static/h.png)

<span data-ttu-id="70a8d-116">再创建几个其他的电影条目。</span><span class="sxs-lookup"><span data-stu-id="70a8d-116">Create a couple more movie entries.</span></span> <span data-ttu-id="70a8d-117">试用“编辑”、“详细信息”和“删除”链接，它们均可正常工作。</span><span class="sxs-lookup"><span data-stu-id="70a8d-117">Try the **Edit**, **Details**, and **Delete** links, which are all functional.</span></span>
