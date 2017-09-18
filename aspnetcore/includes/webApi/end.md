## <a name="implement-the-other-crud-operations"></a><span data-ttu-id="09ab6-101">实现其他的 CRUD 操作</span><span class="sxs-lookup"><span data-stu-id="09ab6-101">Implement the other CRUD operations</span></span>

<span data-ttu-id="09ab6-102">我们将向控制器添加 `Create`、`Update` 和 `Delete` 方法。</span><span class="sxs-lookup"><span data-stu-id="09ab6-102">We'll add `Create`, `Update`, and `Delete` methods to the controller.</span></span> <span data-ttu-id="09ab6-103">这些是主题的变体，因此在这里只提供代码并突出显示主要的差异。</span><span class="sxs-lookup"><span data-stu-id="09ab6-103">These are variations on a theme, so I'll just show the code and highlight the main differences.</span></span> <span data-ttu-id="09ab6-104">添加或更改代码后生成项目。</span><span class="sxs-lookup"><span data-stu-id="09ab6-104">Build the project after adding or changing code.</span></span>

### <a name="create"></a><span data-ttu-id="09ab6-105">创建</span><span class="sxs-lookup"><span data-stu-id="09ab6-105">Create</span></span>

<span data-ttu-id="09ab6-106">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]</span><span class="sxs-lookup"><span data-stu-id="09ab6-106">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]</span></span>

<span data-ttu-id="09ab6-107">这是 HTTP POST 方法，由 [`[HttpPost]`](https://docs.microsoft.com/aspnet/core/api) 特性指示的。</span><span class="sxs-lookup"><span data-stu-id="09ab6-107">This is an HTTP POST method, indicated by the [`[HttpPost]`](https://docs.microsoft.com/aspnet/core/api) attribute.</span></span> <span data-ttu-id="09ab6-108">[`[FromBody]`](https://docs.microsoft.com/aspnet/core/api) 特性告诉 MVC 从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="09ab6-108">The [`[FromBody]`](https://docs.microsoft.com/aspnet/core/api) attribute tells MVC to get the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="09ab6-109">`CreatedAtRoute` 方法返回 201 响应，这是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="09ab6-109">The `CreatedAtRoute` method returns a 201 response, which is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="09ab6-110">`CreatedAtRoute` 还会向响应添加位置标头。</span><span class="sxs-lookup"><span data-stu-id="09ab6-110">`CreatedAtRoute` also adds a Location header to the response.</span></span> <span data-ttu-id="09ab6-111">位置标头指定新建的待办事项的 URI。</span><span class="sxs-lookup"><span data-stu-id="09ab6-111">The Location header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="09ab6-112">请参阅 [10.2.2 201 已创建](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="09ab6-112">See [10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

### <a name="use-postman-to-send-a-create-request"></a><span data-ttu-id="09ab6-113">使用 Postman 发送创建请求</span><span class="sxs-lookup"><span data-stu-id="09ab6-113">Use Postman to send a Create request</span></span>

![Postman 控制台](../../tutorials/first-web-api/_static/pmc.png)

* <span data-ttu-id="09ab6-115">将 HTTP 方法设置为 `POST`</span><span class="sxs-lookup"><span data-stu-id="09ab6-115">Set the HTTP method to `POST`</span></span>
* <span data-ttu-id="09ab6-116">选择“正文”单选按钮</span><span class="sxs-lookup"><span data-stu-id="09ab6-116">Select the **Body** radio button</span></span>
* <span data-ttu-id="09ab6-117">选择“原始”单选按钮</span><span class="sxs-lookup"><span data-stu-id="09ab6-117">Select the **raw** radio button</span></span>
* <span data-ttu-id="09ab6-118">将类型设置为 JSON</span><span class="sxs-lookup"><span data-stu-id="09ab6-118">Set the type to JSON</span></span>
* <span data-ttu-id="09ab6-119">在键值编辑器中，输入一个待办事项，例如</span><span class="sxs-lookup"><span data-stu-id="09ab6-119">In the key-value editor, enter a Todo item such as</span></span> 

```json
{
    "name":"walk dog",
    "isComplete":true
}
```

* <span data-ttu-id="09ab6-120">选择“发送”</span><span class="sxs-lookup"><span data-stu-id="09ab6-120">Select **Send**</span></span>

* <span data-ttu-id="09ab6-121">选择下窗格中的“标头”选项卡，然后复制“位置”标头：</span><span class="sxs-lookup"><span data-stu-id="09ab6-121">Select the Headers tab in the lower pane and copy the **Location** header:</span></span>

![Postman 控制台的“标头”选项卡](../../tutorials/first-web-api/_static/pmget.png)

<span data-ttu-id="09ab6-123">可以使用此位置标头 URI 访问刚刚创建的资源。</span><span class="sxs-lookup"><span data-stu-id="09ab6-123">You can use the Location header URI to access the resource you just created.</span></span> <span data-ttu-id="09ab6-124">重新调用创建名为 `"GetTodo"` 路由时使用的 `GetById` 方法：</span><span class="sxs-lookup"><span data-stu-id="09ab6-124">Recall the `GetById` method created the `"GetTodo"` named route:</span></span>

```csharp
[HttpGet("{id}", Name = "GetTodo")]
public IActionResult GetById(long id)
```

### <a name="update"></a><span data-ttu-id="09ab6-125">更新</span><span class="sxs-lookup"><span data-stu-id="09ab6-125">Update</span></span>

<span data-ttu-id="09ab6-126">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]</span><span class="sxs-lookup"><span data-stu-id="09ab6-126">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]</span></span>

<span data-ttu-id="09ab6-127">`Update` 与 `Create` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="09ab6-127">`Update` is similar to `Create`, but uses HTTP PUT.</span></span> <span data-ttu-id="09ab6-128">响应是 [204（无内容）](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="09ab6-128">The response is [204 (No Content)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="09ab6-129">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是增量。</span><span class="sxs-lookup"><span data-stu-id="09ab6-129">According to the HTTP spec, a PUT request requires the client to send the entire updated entity, not just the deltas.</span></span> <span data-ttu-id="09ab6-130">若要支持部分更新，请使用 HTTP PATCH。</span><span class="sxs-lookup"><span data-stu-id="09ab6-130">To support partial updates, use HTTP PATCH.</span></span>

![显示 204（无内容）响应的 Postman 控制台](../../tutorials/first-web-api/_static/pmcput.png)

### <a name="delete"></a><span data-ttu-id="09ab6-132">删除</span><span class="sxs-lookup"><span data-stu-id="09ab6-132">Delete</span></span>

<span data-ttu-id="09ab6-133">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]</span><span class="sxs-lookup"><span data-stu-id="09ab6-133">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]</span></span>

<span data-ttu-id="09ab6-134">响应是 [204（无内容）](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="09ab6-134">The response is [204 (No Content)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

![显示 204（无内容）响应的 Postman 控制台](../../tutorials/first-web-api/_static/pmd.png)
