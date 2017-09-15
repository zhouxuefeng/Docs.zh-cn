<span data-ttu-id="3a0ad-101">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]</span><span class="sxs-lookup"><span data-stu-id="3a0ad-101">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]</span></span>

<span data-ttu-id="3a0ad-102">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="3a0ad-102">The preceding code:</span></span>

* <span data-ttu-id="3a0ad-103">定义空控制器类。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-103">Defines an empty controller class.</span></span> <span data-ttu-id="3a0ad-104">在接下来的部分中，我们将添加方法来实现 API。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-104">In the next sections, we'll add methods to implement the API.</span></span>
* <span data-ttu-id="3a0ad-105">构造函数使用[依赖关系注入](xref:fundamentals/dependency-injection)将数据库上下文 (`TodoContext `) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-105">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`TodoContext `) into the controller.</span></span> <span data-ttu-id="3a0ad-106">数据库上下文将在控制器中的每个 [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-106">The database context is used in each of the [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="3a0ad-107">构造函数将一个项（如果不存在）添加到内存数据库。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-107">The constructor adds an item to the in-memory database if one doesn't exist.</span></span>

## <a name="getting-to-do-items"></a><span data-ttu-id="3a0ad-108">获取待办事项</span><span class="sxs-lookup"><span data-stu-id="3a0ad-108">Getting to-do items</span></span>

<span data-ttu-id="3a0ad-109">若要获取待办事项，请将下面的方法添加到 `TodoController` 类中。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-109">To get to-do items, add the following methods to the `TodoController` class.</span></span>

<span data-ttu-id="3a0ad-110">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]</span><span class="sxs-lookup"><span data-stu-id="3a0ad-110">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]</span></span>

<span data-ttu-id="3a0ad-111">这些方法实现两种 GET 方法：</span><span class="sxs-lookup"><span data-stu-id="3a0ad-111">These methods implement the two GET methods:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="3a0ad-112">以下是 `GetAll` 方法的 HTTP 响应示例：</span><span class="sxs-lookup"><span data-stu-id="3a0ad-112">Here is an example HTTP response for the `GetAll` method:</span></span>

```
HTTP/1.1 200 OK
   Content-Type: application/json; charset=utf-8
   Server: Microsoft-IIS/10.0
   Date: Thu, 18 Jun 2015 20:51:10 GMT
   Content-Length: 82

   [{"Key":"1", "Name":"Item1","IsComplete":false}]
   ```

<span data-ttu-id="3a0ad-113">稍后在本教程中将演示如何使用 [Postman](https://www.getpostman.com/) 或 [curl](https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man1/curl.1.html) 查看 HTTP 响应。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-113">Later in the tutorial I'll show how you can view the HTTP response using [Postman](https://www.getpostman.com/) or [curl](https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man1/curl.1.html).</span></span>

### <a name="routing-and-url-paths"></a><span data-ttu-id="3a0ad-114">路由和 URL 路径</span><span class="sxs-lookup"><span data-stu-id="3a0ad-114">Routing and URL paths</span></span>

<span data-ttu-id="3a0ad-115">`[HttpGet]` 特性指定 HTTP GET 方法。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-115">The `[HttpGet]` attribute specifies an HTTP GET method.</span></span> <span data-ttu-id="3a0ad-116">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="3a0ad-116">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="3a0ad-117">在控制器的路由特性中采用模板字符串：</span><span class="sxs-lookup"><span data-stu-id="3a0ad-117">Take the template string in the controller’s route attribute:</span></span>

<span data-ttu-id="3a0ad-118">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]</span><span class="sxs-lookup"><span data-stu-id="3a0ad-118">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]</span></span>

* <span data-ttu-id="3a0ad-119">将“[Controller]”替换为控制器的名称，即在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-119">Replace "[Controller]" with the name of the controller, which is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="3a0ad-120">对于此示例，控制器类名称为“Todo”控制器，根名称为“todo”。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-120">For this sample, the controller class name is **Todo**Controller and the root name is "todo".</span></span> <span data-ttu-id="3a0ad-121">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-121">ASP.NET Core [routing](xref:mvc/controllers/routing) is not case sensitive.</span></span>
* <span data-ttu-id="3a0ad-122">如果 `[HttpGet]` 特性具有路由模板（如 `[HttpGet("/products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-122">If the `[HttpGet]` attribute has a route template (such as `[HttpGet("/products")]`, append that to the path.</span></span> <span data-ttu-id="3a0ad-123">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-123">This sample doesn't use a template.</span></span> <span data-ttu-id="3a0ad-124">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-124">See [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes) for more information.</span></span>

<span data-ttu-id="3a0ad-125">在 `GetById` 方法中：</span><span class="sxs-lookup"><span data-stu-id="3a0ad-125">In the `GetById` method:</span></span>

```csharp
[HttpGet("{id}", Name = "GetTodo")]
public IActionResult GetById(long id)
```

<span data-ttu-id="3a0ad-126">`"{id}"` 是 `todo` 项 的 ID 的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-126">`"{id}"` is a placeholder variable for the ID of the `todo` item.</span></span> <span data-ttu-id="3a0ad-127">调用 `GetById` 时，它会将 URL 中“{id}”的值分配给方法的 `id` 参数。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-127">When `GetById` is invoked, it assigns the value of "{id}" in the URL to the method's `id` parameter.</span></span>

<span data-ttu-id="3a0ad-128">`Name = "GetTodo"` 创建一个命名的路由，使你能够 HTTP 响应中链接到此路由。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-128">`Name = "GetTodo"` creates a named route and allows you to link to this route in an HTTP Response.</span></span> <span data-ttu-id="3a0ad-129">稍后将使用示例进行解释。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-129">I'll explain it with an example later.</span></span> <span data-ttu-id="3a0ad-130">有关详细信息，请参阅[路由到控制器操作](xref:mvc/controllers/routing)。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-130">See [Routing to Controller Actions](xref:mvc/controllers/routing) for detailed information.</span></span>

### <a name="return-values"></a><span data-ttu-id="3a0ad-131">返回值</span><span class="sxs-lookup"><span data-stu-id="3a0ad-131">Return values</span></span>

<span data-ttu-id="3a0ad-132">`GetAll` 方法返回 `IEnumerable`。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-132">The `GetAll` method returns an `IEnumerable`.</span></span> <span data-ttu-id="3a0ad-133">MVC 自动将对象序列化为 [JSON](http://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-133">MVC automatically serializes the object to [JSON](http://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="3a0ad-134">在假设没有未经处理的异常的情况下，此方法的响应代码为 200。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-134">The response code for this method is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="3a0ad-135">（未经处理的异常将转换为 5xx 错误。）</span><span class="sxs-lookup"><span data-stu-id="3a0ad-135">(Unhandled exceptions are translated into 5xx errors.)</span></span>

<span data-ttu-id="3a0ad-136">相反，`GetById` 方法返回多个常规的 `IActionResult` 类型，它表示一系列返回类型。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-136">In contrast, the `GetById` method returns the more general `IActionResult` type, which represents a wide range of return types.</span></span> <span data-ttu-id="3a0ad-137">`GetById` 具有两个不同的返回类型：</span><span class="sxs-lookup"><span data-stu-id="3a0ad-137">`GetById` has two different return types:</span></span>

* <span data-ttu-id="3a0ad-138">如果没有任何项与请求的 ID 匹配，此方法将返回 404 错误。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-138">If no item matches the requested ID, the method returns a 404 error.</span></span>  <span data-ttu-id="3a0ad-139">此通过返回 `NotFound` 来实现。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-139">This is done by returning `NotFound`.</span></span>

* <span data-ttu-id="3a0ad-140">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="3a0ad-140">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="3a0ad-141">此通过返回 `ObjectResult` 来完成</span><span class="sxs-lookup"><span data-stu-id="3a0ad-141">This is done by returning an `ObjectResult`</span></span>
